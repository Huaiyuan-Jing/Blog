---
title: How to render 3D to 2D pixel art in Godot
tags:
  - Godot
  - Algorithm
abbrlink: a9781501
index_img: https://github.com/user-attachments/assets/e4f921de-f066-4740-8694-8cffc5367ef7
banner_img: https://github.com/user-attachments/assets/e4f921de-f066-4740-8694-8cffc5367ef7
math:
date: 2025-09-05 16:33:06
author: HackThink
---

## What is 3D to 2D Rendering?

3D to 2D rendering is the process of automatically converting three-dimensional models into two-dimensional images using a computer. It's a fundamental technique in computer graphics that allows artists and developers to leverage the flexibility of 3D modeling for creating traditional 2D art.

## Why Use 3D to 2D Rendering?

The primary advantage of this method lies in its efficiency and scalability, especially for character animation. Consider a game like *Dead Cells*, which features detailed, fluid pixel art animations. 

![](https://github.com/user-attachments/assets/b96bf188-603b-42e7-8853-23c270cb09e9)

![](https://github.com/user-attachments/assets/51719172-1810-49bd-a07c-ea344f2cd852)

Creating these animations directly by hand is an incredibly labor-intensive process. A single second of motion might require a dozen or more individual frames, meaning hundreds or even thousands of unique images are needed to animate just one character. This workload increases exponentially with more characters, weapons, or actions.

A more efficient approach is to build a single 3D model of the character. Once the model is complete, animators can apply motions and animations to it in a 3D environment. The software then automatically renders these animated 3D sequences into 2D images or sprite sheets. This technique, which Dead Cells uses, significantly reduces the manual workload, allowing for a much higher volume of high-quality, fluid animations with a fraction of the effort required for traditional frame-by-frame drawing. It offers the best of both worlds: the visual style of 2D art with the production efficiency of 3D technology.

## How to Implement 3D to 2D Rendering in Godot

+ ### Arrange a 3d scene and model as you like
  + ![](https://github.com/user-attachments/assets/52a35dbb-d3f8-4793-9d92-9d7810c208df) 
    
    First, put your 3d model in a separate scene and setup everything you need to display the model (like reflection, animator, light and so on). 

    P.S.: If you want your item looks better after rendering to 2d, you can choose the "Diffusion Mode" and "Specular Mode" as "Toon", which is the godot inherit shading method compatiable with 3D to 2D rendering

    ![](https://github.com/user-attachments/assets/8b31bf2b-15ea-4805-abd1-fb29562402b9)

+ ### Add SubViewPort Node in 3D Scene
  + ![](https://github.com/user-attachments/assets/5e0d0ea8-9066-4dae-8a08-13f4b8063557)
    
    Create a SubViewPoint Node and Put all the 3D-related nodes under the SubViewPoint node.
    
    ![](https://github.com/user-attachments/assets/d7468004-e558-4dbf-b453-cb2bdaf0d476)
    
    Then create a Sprite2D node under root, choose "NewViewPoint Texture" in texture feature in this node and select the SubViewPoint we just created.
    
    Now we can see the view of camera in 3D is projected on the 2D scene like the following:
    
    ![](https://github.com/user-attachments/assets/86361bd3-a71d-4f7f-accf-39ddc327d03c)

    P.S.: Remember to select "Transparent BG" in SubViewPoint Node if you don't want to see the background in 3D view.

+ ### Create a shader for 2D sprite image
  + ![](https://github.com/user-attachments/assets/e55023d4-bd70-4f42-a30c-3a306e70423d)

    In the material of Sprite2D node, we choose to create new metariel and new shader for this image.

    After creation, we can see the shader editor and initial shader code at the bottom of screen like this:

    ![](https://github.com/user-attachments/assets/a0e1c91f-572f-49bc-bb0a-a49462016ce4)

    Now we start to code the shader, first we create 3 uniform global values:

    ```gdshader
      uniform vec2 pixel_count = vec2(64);  // Number of pixel after rendering the image
      uniform float outline_width = 1.0;
      uniform vec4 outline_color : source_color = vec4(0,0,0,1);
    ```
    Then we add the following two functions:

    ```gdshader
      vec2 pixelate(vec2 uv, vec2 count) {  // return uv
          uv *= count;
          uv = floor(uv);
          uv /= count;
          return uv;
      }
    ```
    This function is intended to pixelate the image

    ```gdshader
      vec4 add_outline(vec2 uv, vec4 color, sampler2D obj_texture, vec2 obj_pixel_size) {  // return color
        vec4 outline =  texture(obj_texture, uv + vec2(0, obj_pixel_size.y) * outline_width) +
                texture(obj_texture, uv + vec2(0, -obj_pixel_size.y) * outline_width) +
                texture(obj_texture, uv + vec2(obj_pixel_size.x, 0) * outline_width) +
                texture(obj_texture, uv + vec2(-obj_pixel_size.x, 0) * outline_width) +
                texture(obj_texture, uv + vec2(obj_pixel_size.x, obj_pixel_size.y) * outline_width * (sqrt(2) / 2.0)) +
                texture(obj_texture, uv + vec2(obj_pixel_size.x, -obj_pixel_size.y) * outline_width * (sqrt(2) / 2.0)) +
                texture(obj_texture, uv + vec2(-obj_pixel_size.x, obj_pixel_size.y) * outline_width * (sqrt(2) / 2.0)) +
                texture(obj_texture, uv + vec2(-obj_pixel_size.x, -obj_pixel_size.y) * outline_width * (sqrt(2) / 2.0));
        outline.rgb = color.rgb;
        outline.a = min(outline.a, 1);
        vec4 origin = texture(obj_texture, uv);
        return mix(outline, origin, origin.a);
      }
    ```

    This function is intended to add a border for the object.

    Finally, we adding the following line to the fragment function:

    ```gdshader
      void fragment() {
        // Called for every pixel the material is visible on.
        COLOR = add_outline(pixelate(UV, pixel_count), outline_color, TEXTURE, TEXTURE_PIXEL_SIZE);
      }
    ```

    Now save the code and we can see our picture in 2D scene changed to this:

    ![](https://github.com/user-attachments/assets/bd352c4b-d8d3-4b50-a0ab-8896f0ba1116)

+ ### Link it to the main scene
  + Go to the main scene of your project and link the object into the main scene, add a simple background for it, now we sucessfully convert our 3D model to 2D pixel image:

    ![](https://github.com/user-attachments/assets/58744a30-6c91-474e-9928-7d73af0f55fe)

    If we add an animation to the 3D object, the 2D picture will move as well:

    ![](https://github.com/user-attachments/assets/e2dd1d91-8037-4cca-b20b-b5ffc41f113f)

## Conclusion

Here are all the steps for basic 3D model to 2D pixel image rendering. You can explore by yourself for more features like adding HDR, setting the fps of pixel animation, create better outline to represent shadow and so on. 