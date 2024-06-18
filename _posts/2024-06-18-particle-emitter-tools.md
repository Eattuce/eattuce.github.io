---
title: DST Particle Emitter 工具
description: Particle 工具自用
author: eattuce
date: 2024-06-18 13:10:00 +0800
categories: [Don't Starve Together, Tutorial]
tags: [dst, tips]
render_with_liquid: false
last_modified_at: 2024-06-18 15:00:00 +0800
---

## Emitters
- 在x和z平面上的环形指定角度范围
```lua
local function CreateRingEmitterWithAngle( radius, angle_start, angle_end )
    local cos = math.cos
    local sin = math.sin
    local rad = math.rad
    local random = math.random

    return function()
        local angle = rad(angle_start) + random() * rad(angle_end - angle_start)
        local x = radius * cos(angle)
        local y = radius * sin(angle)
        return x, y
    end
end
```

- 在Y轴平面的环形指定角度范围，形成的圆环始终朝向相机
```lua
local function CreateCircleEmitterOnYAxisProjection(radius, angle_start, angle_end)
    local rand = math.random
    local sin = math.sin
    local cos = math.cos
    local rad = math.rad

    return function()
        local cameraAngle = (TheCamera:GetHeadingTarget() - 90) * DEGREES
        -- Generate a random angle for the circle
        local t = 2.0 * PI * rand()
        if angle_start and angle_end then
            t = rad(angle_start) + rand() * rad(angle_end - angle_start)
        end
        -- Compute the coordinates on the circle in the y-z plane
        local y = radius * cos(t)
        local z = radius * sin(t)
        -- Rotate the point based on the camera angle
        local rotatedX = z * cos(cameraAngle)
        local rotatedZ = z * sin(cameraAngle)
        -- Return the coordinates on the projected circle
        return rotatedX, y, rotatedZ
    end
end
```

## Tools
- 把raw目录下的png调整到64x64并拼接到宽

```python
from PIL import Image
import os

def resize_and_pad_image(image_path, target_size=(64, 64)):
    img = Image.open(image_path).convert("RGBA")
    bbox = img.getbbox()
    if bbox:
        img = img.crop(bbox)
    img.thumbnail(target_size, Image.LANCZOS)
    
    new_img = Image.new("RGBA", target_size, (0, 0, 0, 0))
    img_w, img_h = img.size
    new_img.paste(img, ((target_size[0] - img_w) // 2, (target_size[1] - img_h) // 2))
    
    return new_img

def concatenate_images(image_list, output_path):
    width = 64 * len(image_list)
    height = 64
    new_img = Image.new("RGBA", (width, height), (0, 0, 0, 0))
    
    for i, img in enumerate(image_list):
        new_img.paste(img, (i * 64, 0))
    
    new_img.save(output_path)

def process_images(image_paths, output_path):
    resized_images = [resize_and_pad_image(img_path) for img_path in image_paths]
    concatenate_images(resized_images, output_path)

if __name__ == "__main__":
    input_folder = "./raw"
    output_image_path = "./image.png"
    
    image_paths = [os.path.join(input_folder, fname) for fname in os.listdir(input_folder) if fname.endswith('.png')]
    process_images(image_paths, output_image_path)
```


```powershell
@echo off
set PYTHON_EXECUTABLE="D:\path\to\python.exe"

set PYTHON_SCRIPT_PATH="script.py"

%PYTHON_EXECUTABLE% %PYTHON_SCRIPT_PATH%
pause
```