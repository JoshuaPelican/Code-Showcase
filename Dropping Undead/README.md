# Dropping Undead
Dropping Undead is a third-person endless survival game featuring zombies and guns! Blast away unending hordes of increasing zombies with several gun types at your disposal. Dropping Undead is inspired by Call of Duty Zombies gameplay but with a third-person twist. I chose a stylized art direction and sharp color palette to emphasize the core gameplay elements such as health and ammo. The endless round system and limited weapon pickups keep the game interesting over longer play sessions.

View the entire repository for this project here: https://github.com/JoshuaPelican/Dropping-Undead

<details>
  <summary> 
    <h3>
      Stylized Functional Graphics
    </h3>
  </summary>

I didn't have much time to make many 3D assets and textures for this project, so I found a creative solution using unlit shaders! Everything from the characters to the particles use unlit shading, with an emphasis on contrasting colors.

![image](https://github.com/JoshuaPelican/Code-Showcase/assets/65318134/459f69f9-e728-4462-b3a7-e7c1dfbb0717)

The color gradients from green to red communicate important gameplay information without the need for a HUD. Guns, for example, are bright green when full of ammo and red when out of ammo. Enemies and the player change from green to red as their health runs out.

![image](https://github.com/JoshuaPelican/Code-Showcase/assets/65318134/208e58a1-a238-41a9-844d-4ecd8b8bc18a)

I needed some way to give the 3D environment a sense of depth without proper lighting. I discovered that the Unity fog system still interacts with unlit shaders! I tweaked it to work with my shader setup and matched it with the game's palette so the environment slowly fades into the background and the subtle shapes of the geometry stand out.
</details>
