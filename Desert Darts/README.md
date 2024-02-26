# Desert Darts
Desert Darts is an endless arcade game and my first mobile project. Using your phone's gyroscope, control a target with the goal of landing precice hits in rapid succession. Desert Darts features an adaptive difficulty system and complex dart pattern generation for engaging and replayable gameplay, and was created with an accessable, icon-only interface.

View the entire repository for this project here: https://github.com/JoshuaPelican/Desert-Darts-Mobile-Game


<details>
  <summary> 
    <h3>
      Dart Pattern Generation
    </h3>
  </summary>
  
  I wanted to create gameplay that was interesting enough to be replayable without being completely random. I created a dart pattern generation system to choose from unique dart patterns over the course of the game.

  Patterns are defined as scriptable objects so that they can easily be created, tweaked, and shuffled around. These parameters give info to the dart generator for how to spawn the darts for this pattern.
  
  ```C#
  public class SpinePattern : ScriptableObject
  {
      [Header("Intensity Weight")]
      public Vector2 intensityRange = new Vector2(0, 1);
  
      [Header("Pattern Settings")]
      [Min(1)] public int burstCount = 10;
      public float startDelay = 1f;
      [Space()]
      public bool angled;
  
      [Header("Spawn Settings")]
      public int spinesPerBurst = 1;
      public float spineDelay = 0.5f;
  }
  ```

These patterns are fed to the generator and are picked from using the "Intensity Weight" parameter. This determines how difficult the pattern is to catch all the darts it produces. The generator is able to pick dart patterns that correspond to a global intnesity value that is calculated from factors such as game time and current score multiplier.

```C#
// Intensity Parameters
float intensity = 0;
float intensityStrength = 2;
float intensityGainMultiplier = 1;
float intensityTimeFactor = 0.125f;

float gravityIntensityMultiplier = 0.75f;
float startDelayIntensityMultiplier = 1.5f;
float spineDelayIntensityMultiplier = 1.5f;
```

```C#
private SpinePattern RandomWeightedSpinePattern()
{
    SpinePattern randPattern = patternDatabase[Random.Range(0, patternDatabase.Length)];
    bool inRange = intensity >= randPattern.intensityRange.x && intensity <= randPattern.intensityRange.y;
    int i = 0;

    while (!inRange && i < maxIterations)
    {
        i++;
        randPattern = patternDatabase[Random.Range(0, patternDatabase.Length)];
        inRange = intensity > randPattern.intensityRange.x && intensity < randPattern.intensityRange.y;
    }

    if (i >= maxIterations)
        randPattern = patternDatabase[0];

    return randPattern;
}
```

If I were to rewrite this system, I would simply clean up the logic for how patterns are picked and the global intensity is calculated. The system itself works well and it would be easy enough to go in and add new patterns well after the game's conception.

</details>

