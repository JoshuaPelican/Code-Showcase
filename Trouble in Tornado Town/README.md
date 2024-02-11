# Trouble In Tornado Town
Trouble in Tornado Town is an physics-based destruction game featuring a giant tornado! Trouble in Tornado Town was produced by a <a href="https://abductedmonkeys.com">studio of 10 Drexel students</a> outside of class time, to learn how to ship a game. I was the lead programmer and created the wacky gameplay systems that make it so fun to play! I also optimized the physics and rendering pipelines to work for the thousands of object we wanted on-screen. It is now fully released on Steam following 2 major post-launch updates.

<details>
  <summary> 
    <h3>
      Tornado Destruction Calculations
    </h3>
  </summary>

In Trouble in Tornado Town, we want every object to be destructable. This reqired some sort of procedural approach, as manually adjusting each object to be destroyed at the right tornado size would be very time consuming.

To solve this issue, each object determines its own size programatically by calulcating the area of the mesh and the area of the bounds and using some blend of the two. This size value can be compared to the size of the tornado to determine if it should be destroyed when the tornado reaches it.

This is also calculated and serialized in the editor to avoid any runtime overhead.

This method works for most objects, and an additional scale factor is applied manually to objects that don't feel right when playtesting.

![image](https://github.com/JoshuaPelican/Code-Showcase/assets/65318134/c21f09d8-fb4c-40c7-84de-4a125be9d65e)

  ```C#
  public float CalculateSize()
    {
        mesh = GetComponent<MeshFilter>().sharedMesh;

        float SignedVolumeOfTriangle(Vector3 p1, Vector3 p2, Vector3 p3)
        {
            float v321 = p3.x * p2.y * p1.z;
            float v231 = p2.x * p3.y * p1.z;
            float v312 = p3.x * p1.y * p2.z;
            float v132 = p1.x * p3.y * p2.z;
            float v213 = p2.x * p1.y * p3.z;
            float v123 = p1.x * p2.y * p3.z;
            return (1.0f / 6.0f) * (-v321 + v231 + v312 - v132 - v213 + v123);
        }

        float VolumeOfMesh(Mesh mesh)
        {
            float volume = 0;
            Vector3[] vertices = mesh.vertices;
            int[] triangles = mesh.triangles;
            for (int i = 0; i < mesh.triangles.Length; i += 3)
            {
                Vector3 p1 = vertices[triangles[i + 0]];
                Vector3 p2 = vertices[triangles[i + 1]];
                Vector3 p3 = vertices[triangles[i + 2]];
                volume += SignedVolumeOfTriangle(p1, p2, p3);
            }
            return Mathf.Abs(volume);
        }

        float volume = VolumeOfMesh(mesh);
        float area = mesh.bounds.size.x * mesh.bounds.size.y * mesh.bounds.size.z;

        return Mathf.Pow(Mathf.Sqrt(Mathf.Sqrt(Mathf.Lerp(volume, area, 0.55f))), 1.6f);
    }
  ```  
</details>

<details>
  <summary> 
    <h3>
      Unity Physics Optimizations
    </h3>
  </summary>

Having 3000+ dynamic physics objects on the screen at any given time makes the CPU want to scream in pain. I was able to configure Unity to reduce the accuracy, frequency, and complexity of these collisions to improve performance.
- Each physics object is reduced to one or two box colliders, as accurate collisions are not needed, especially at large scales.
- Objects starts sleeping when no longer moving. Sleeping objects do not calculate physics until they wake up again.
- Messed with the Unity collision settings to use their less performant collision detection algorithim.
- Lowered the physics rate on lower-end devices. This adds visible but largely un-noticable lag for physics objects and saves alot on performance.

![image](https://github.com/JoshuaPelican/Code-Showcase/assets/65318134/e4d84907-3475-42c6-9318-f1b758288136)

Culling is another huge performance improvement. I created my own custom culling system to remove objects that are too small to be seen or noticed by the player. When the Tornado reaches a certain size threshold, any objects that are already destroyed, have stopped moving, and are under the size threshold are removed. If it is not destroyed, it simply turns off shadows for the object to squeeze out every drop of performance.


```C#
    void OnThresholdReached(int threshold)
    {
        if (TornadoScaling.CurrentThreshold >= 10 * Mathf.Sqrt(Size))
        {
            isCulled = true;

            renderer.shadowCastingMode = UnityEngine.Rendering.ShadowCastingMode.Off;

            if (isSleeping)
                Destroy(gameObject);
        }
    }
```
  
</details>
