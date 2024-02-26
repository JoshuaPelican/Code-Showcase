# Krafts
Krafts is an arts and crafts making experience made in 2 days for a game jam with the theme of "Joined Together". I was inspired by glue and wanted to make a therapeutic experience reminiscent of kindergarten arts and crafts. Arrange many different objects, glue them together, and even draw on them! After you finish, shake off any un-glued objects and save a screenshot of your creation to your device. During the jam I engaged with the community by collecting their favorite screenshots and put them on the front page.

See the entire repository for this project here: https://github.com/JoshuaPelican/Krafts

<details>
  <summary> 
    <h3>
      Sprite Layering System
    </h3>
  </summary>

In Krafts, a huge issue I ran into right away was making sure the sprites were being layered as someone would expect them to be. If a player grabs an object it should be on top of everthing else, and any newly created objects would be on top of that. My quick, yet effective, solution for the game jam was to use the Z-axis to layer the sprites.

I initially looked into LayerMasks, but with the amount of layering I wanted to do it did not seem like a scalable solution.

https://github.com/JoshuaPelican/Code-Showcase/assets/65318134/db7a0a09-1dae-49c9-9d74-a3063a336e20

```C#
public class ToolManager : MonoBehaviour
{
    public float currentZ = 0;
    
    public void NextZ()
    {
        currentZ -= .01f;
    }
}
    
```
            
A manager script keeps track of the global Z value, and decrements it (brings it closer to the camera) every time an object is moved to the front.

```C#
{
    if (ToolManager.instance.SelectedTool == ToolManager.Tool.Manipulate)
    {
        ToolManager.instance.NextZ();
        SetActive(true);
    }
}
    
if (active)
{
    transform.position = new Vector3(InputUtility.MousePosition.x, InputUtility.MousePosition.y, ToolManager.instance.currentZ);
}
```
        
When an object is grabbed, its Z coordinate is set to the new value and bring it to the front immediately.

If I was to redo this system, given more time, I would like to keep a similar Z coordinate system, but clean it up. There were alot of issues related to local vs world space coordinates. Sometimes grabbing an object would not bring it to the front because previously glued objects had stacked Z values and surpassed the current global Z value.
</details>
