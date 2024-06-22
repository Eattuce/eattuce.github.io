---
title: DST Particle Emitter 工具
description: Particle 工具自用
author: eattuce
date: 2024-06-18 13:10:00 +0800
categories: [Don't Starve Together, Tutorial]
tags: [dst, tips]
render_with_liquid: false
last_modified_at: 2024-06-22 17:00:00 +0800
---

## Emitters
### xz平面环形指定角度范围
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

### Y平面环形指定角度范围 圆环始终朝向相机
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
### 生成可用UV offset 的 TEXTURE

> 导入自己的TEXTURE时需要resolvefilepath("fx/my_texture.tex")
> 加载素材Asset("IMAGE", TEXTURE) 不需要xml文件
{: .prompt-warning }

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

# input_folder下的png调整到64x64并拼接到宽
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


---


## Samples

### 从图片生成粒子坐标


> [Sample Image](https://th.bing.com/th/id/R.7822a59f6b645ba26680623e250f88d3?rik=X7QX6QG1A9fT4Q&riu=http%3a%2f%2fimg3.redocn.com%2ftupian%2f20141212%2fheibaishouhuitoutuan_3685428.jpg&ehk=ux5OcCo1E45kn3CsSYqnVuOjbjLx5ul7HB1P3PJ9cAw%3d&risl=&pid=ImgRaw&r=0)


```lua
--------------------------------------------------------------------------

local TEXTURE = "fx/wintersnow_cane.tex"
local SHADER = "shaders/vfx_particle.ksh"

local COLOUR_ENVELOPE_NAME = "testparticle_colourenvelope"
local SCALE_ENVELOPE_NAME = "testparticle_scaleenvelope"

local skull = require("imggriddata").skull

local assets =
{
    Asset("IMAGE", TEXTURE),
    Asset("SHADER", SHADER),
}

local function CreateImageEmitter(grid_data, grid_width, grid_size_each)
    return function ()
        local index = grid_data[math.random(#grid_data)]
        local y = math.floor(index / grid_width)
        local x = index % grid_width
        return (x - grid_width / 2) * grid_size_each, (y - grid_width / 2) * grid_size_each
    end
end

--------------------------------------------------------------------------
local function IntColour(r, g, b, a)
    return { r / 255, g / 255, b / 255, a / 255 }
end

local function InitEnvelope()

    EnvelopeManager:AddColourEnvelope(COLOUR_ENVELOPE_NAME,
        {
            { 0, IntColour(255, 255, 255, 255) },
            { 0.5, IntColour(255, 255, 255, 255) },
            { 1, IntColour(255, 255, 255, 0) },
        }
    )

    local max_scale = 1
    local end_scale = 1

    local envs = {
        { 0, { max_scale, max_scale } },
        { 1, { max_scale * end_scale, max_scale * end_scale } }
    }

    EnvelopeManager:AddVector2Envelope( SCALE_ENVELOPE_NAME, envs )

    InitEnvelope = nil
    IntColour = nil
end
--------------------------------------------------------------------------
local MAX_LIFETIME = 1

local function emit_fn(effect, i, emitter)
    local vx, vy, vz = 0, 0, 0
    local lifetime = MAX_LIFETIME * (.6 + math.random() * .4)
    local px, py, pz = emitter()
    if pz == nil then
        pz = py
        py = 0
    end

    local uv_offset = math.random(0, 7) * .125

    effect:AddParticleUV(
        i,
        lifetime,           -- lifetime
        px, py, pz,         -- position
        vx, vy, vz,         -- velocity
        uv_offset, 0        -- uv offset
    )
end

local function fn()
    local inst = CreateEntity()

    inst.entity:AddTransform()
    inst.entity:AddNetwork()

    inst:AddTag("FX")

    inst.entity:SetPristine()

    inst.persists = false

    --Dedicated server does not need to spawn local particle fx
    if TheNet:IsDedicated() then
        return inst
    elseif InitEnvelope ~= nil then
        InitEnvelope()
    end

    local effect = inst.entity:AddVFXEffect()
    effect:InitEmitters(1)

    effect:SetRenderResources(0, TEXTURE, SHADER)
    -- Rotation Status must match `effect:Add[Rotating]ParticleUV`
    -- effect:SetRotationStatus(0, true)
    effect:SetUVFrameSize(0, .125, 1)
    effect:SetMaxNumParticles(0, 1024)
    effect:SetMaxLifetime(0, MAX_LIFETIME)
    effect:SetColourEnvelope(0, COLOUR_ENVELOPE_NAME)
    effect:SetScaleEnvelope(0, SCALE_ENVELOPE_NAME)
    effect:SetBlendMode(0, BLENDMODE.Premultiplied)
    effect:EnableBloomPass(0, true)
    effect:SetSortOrder(0, 0)
    effect:SetSortOffset(0, 1)

    -----------------------------------------------------
    local tick_time = TheSim:GetTickTime()

    local desired_pps = 500
    local particles_per_tick = desired_pps * tick_time
    local num_particles_to_emit = 0

    local emitter = CreateImageEmitter(skull, 128, .05)

    EmitterManager:AddEmitter(inst, nil, function()

        while num_particles_to_emit > 1 do
            emit_fn(effect, 0, emitter)
            num_particles_to_emit = num_particles_to_emit - 1
        end
        num_particles_to_emit = num_particles_to_emit + particles_per_tick * math.random()
    end)

    return inst
end

return Prefab("testparticle", fn, assets)
```
{: file='testparticle.lua'}


```python
from PIL import Image

def resize_image_with_padding(input_image_path, output_image_path, max_size=256):
    # Open an image file
    with Image.open(input_image_path) as image:
        width, height = image.size
        
        # Calculate the new dimensions preserving the aspect ratio
        if width > height:
            new_width = max_size
            new_height = int((max_size / width) * height)
        else:
            new_height = max_size
            new_width = int((max_size / height) * width)
        
        # Resize the image
        resized_image = image.resize((new_width, new_height), Image.LANCZOS)
        
        # Create a new image with white background
        new_image = Image.new("RGB", (max_size, max_size), (255, 255, 255))
        
        # Calculate the position to paste the resized image on the white background
        upper_left_x = (max_size - new_width) // 2
        upper_left_y = (max_size - new_height) // 2
        
        # Paste the resized image onto the white background
        new_image.paste(resized_image, (upper_left_x, upper_left_y))
        
        # Save the new image
        new_image.save(output_image_path)
        print(f"Image has been resized, padded, and saved as {output_image_path}")

input_image_path = "./skull.jpg"
output_image_path = "skull_resized_padded.jpg"
resize_image_with_padding(input_image_path, output_image_path, 128)
```
{: file='resize.py'}


```python
from PIL import Image
import numpy as np

def process_image(input_image_path, output_image_path, lua_file_path):
    # 读取图片并转换为灰度图像
    image = Image.open(input_image_path).convert('L')
    
    # 二值化处理，找到所有黑色像素点
    threshold = 128
    image = image.point(lambda p: p > threshold and 255 or 0)
    
    # 保存二值化后的图片
    image.save(output_image_path)
    
    # 将图片转换为NumPy数组以便处理
    image_array = np.array(image)
    
    # 找到所有黑色像素点（即值为0的点）
    black_points = np.transpose(np.nonzero(image_array == 0))
    
    # 获取图片宽度
    width = image_array.shape[1]
    
    # 计算偏移量（转换为从0开始的序号）
    offsets = [point[0] * width + point[1] for point in black_points]
    
    # 将偏移量保存到Lua表中
    with open(lua_file_path, 'w') as lua_file:
        lua_file.write('black_pixel_indices = {\n')
        for offset in offsets:
            lua_file.write(f"    {offset},\n")
        lua_file.write('}\n')

    print(f"Binary image saved to {output_image_path}, offsets saved to {lua_file_path}")

# 使用示例
input_image_path = 'skull_resized_padded.jpg'
output_image_path = 'output_binary.jpg'
lua_file_path = 'pixels.lua'
process_image(input_image_path, output_image_path, lua_file_path)
```
{: file='togrid.py'}


1. `resize.py` 将图片进行二值化和缩放处理
2. `togrid.py` 保存图像有效像素点下标
3. `testparticle.lua` 读取像素点下标并解析得到坐标
4. `testparticle.lua` 选择随机坐标生成粒子
