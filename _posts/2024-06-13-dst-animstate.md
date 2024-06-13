---
title: DST AnimState
description: AnimState 浅析
author: eattuce
date: 2024-06-13 15:10:00 +0800
categories: [Don't Starve Together, Tutorial]
tags: [dst, tips]
render_with_liquid: false
last_modified_at: 2024-06-13 15:20:00 +0800
---


> Spriter创建和打开的动画文件.scml其实也是一个类似 .xml .yaml的标记语言。
{: .prompt-tip }


Getters
```lua
SetBank
SetBuild
SetScale
SetFinalOffset
SetOrientation
SetLayer
SetPercent
SetTime
SetDeltaTimeMultiplier
SetBloomEffectHandle
SetDefaultEffectHandle
SetMultColour
SetAddColour
SetSymbolExchange
SetSortOrder
SetClientSideBuildOverrideFlag
SetDepthBias
SetDepthTestEnabled
SetSortWorldOffset
SetHaunted
SetOceanBlendParams
SetBankAndPlayAnimation
SetRayTestOnBB
SetWorldSpaceAmbientLightPos
SetManualBB
SetHighlightColour
SetUILightParams
SetErosionParams
SetClientsideBuildOverride
SetFloatParams
SetMultiSymbolExchange
SetDepthWriteEnabled
SetInheritsSortKey
SetSkin
SetLightOverride(.5)                        --  即使在黑暗中也能看见 0.5
SetSymbolBloom("symbol")                    --  给symbol加上 SetBloomEffectHandle("shaders/anim.ksh") 的效果 ClearSymbolBloom取消
SetSymbolLightOverride("symbol", 0.5)       --  给symbol加上 LightOverride(0.5) 的效果
```


Setters
```lua
GetSortOrder
GetCurrentAnimationTime
GetCurrentAnimationLength
GetCurrentAnimationFrame
GetMultColour
GetAddColour
GetInheritsSortKey
GetSkinBuild
GetCurrentFacing
GetSymbolOverride
GetBuild
GetSymbolPosition
```


Other
```lua
Show
Hide
HideSymbol
ShowSymbol
PlayAnimation
PushAnimation
Pause
Resume
OverrideSymbol
OverrideMultColour
IsCurrentAnimation
AnimDone
BuildHasSymbol
IsSymbolOverridden
OverrideShade
CompareSymbolBuilds
OverrideSkinSymbol
AssignItemSkins
OverrideItemSkinSymbol
UsePointFiltering
UseColourCube
FastForward
AddOverrideBuild
ClearOverrideSymbol
ClearAllOverrideSymbols
ClearBloomEffectHandle
ClearDefaultEffectHandle
ClearOverrideBuild
ClearSymbolExchanges
```


---

| Function Name                  | Parameters                       | Remarks                                                      |
| ------------------------------ | -------------------------------- | ------------------------------------------------------------ |
| Show                           | (layername)                      | 显示图层(Layer)                                              |
| Hide                           |                                  | 隐藏 symbol 或 组?                                           |
| HideSymbol                     |                                  |                                                              |
| ShowSymbol                     |                                  |                                                              |
| SetBank                        | (bankname)                       | spriter里动画的父级节点的名字                                |
| SetBuild                       | (buildname)                      | buildname就是scml文件的名字                                  |
| PlayAnimation                  | (animname, loop)                 | 播放动画，animname：动画名，loop：是否循环播放，默认是false  |
| PushAnimation                  | (animname, loop)                 | 播放动画，animname：动画名，loop：是否循环播放，默认是true   |
| SetScale                       | (x,y,z)                          | 贴图缩放,值范围：(0-1]                                       |
| SetFinalOffset                 | (number)                         | 设置z优先级 越高越靠近相机                                   |
| SetOrientation                 | [0, face)                        | Set<Four>Faced                                               |
| GetSortOrder                   |                                  |                                                              |
| SetLayer                       |                                  |                                                              |
| GetCurrentAnimationTime        |                                  |                                                              |
| SetPercent                     | ("name", 1)                      | 动画播放百分比，固定帧，不会动（动画名，百分比）             |
| GetCurrentAnimationLength      |                                  | 动画总长度，单位：秒，1 帧是 1/30 秒，0.33333 秒             |
| SetTime                        | (time)                           | 设置当前动画从第几秒开始播放（秒）                           |
| Pause                          |                                  |                                                              |
| Resume                         |                                  |                                                              |
| GetCurrentAnimationFrame       |                                  |                                                              |
| SetDeltaTimeMultiplier         | (multiplier)                     | 动画播放速度（速度倍数）                                     |
| OverrideSymbol                 | (oldsymbol, newbuild, newsymbol) | 覆盖旧通道                                                   |
| ClearOverrideSymbol            |                                  | 清除覆盖的通道                                               |
| ClearAllOverrideSymbols        |                                  | 清除所有覆盖通道                                             |
| SetBloomEffectHandle           | ("shaders/anim.ksh")             |                                                              |
| ClearBloomEffectHandle         |                                  |                                                              |
| SetDefaultEffectHandle         |                                  |                                                              |
| ClearDefaultEffectHandle       |                                  |                                                              |
| SetMultColour                  | (r,g,b,a)                        | 颜色叠乘，受原图颜色影响，参数0-1                            |
| GetMultColour                  |                                  |                                                              |
| SetAddColour                   | (r,g,b,a)                        | 颜色叠加，几乎不受原图颜色影响，参数0-1 (0,0,0,1)正常 (1,1,1,1)全白 |
| GetAddColour                   |                                  |                                                              |
| OverrideMultColour             | (r,g,b,a)                        | r = g = b = x, a = y                                         |
|                                |                                  | x = 1, y = 1 正常                                            |
|                                |                                  | x = 1, y = 0 白色透明                                        |
|                                |                                  | x = 0, y = 1 全黑                                            |
|                                |                                  | x = 0, y = 0 消失                                            |
| IsCurrentAnimation             | (name)                           | 当前动画名是否是name                                         |
| AnimDone                       |                                  |                                                              |
| BuildHasSymbol                 | (build, symbol)                  | 是否有该build下的通道                                        |
| IsSymbolOverridden             |                                  |                                                              |
| OverrideShade                  |                                  |                                                              |
| SetSymbolExchange              |                                  |                                                              |
| GetInheritsSortKey             |                                  |                                                              |
| SetSortOrder                   |                                  |                                                              |
| SetClientSideBuildOverrideFlag |                                  |                                                              |
| GetSkinBuild                   |                                  |                                                              |
| SetDepthBias                   |                                  |                                                              |
| SetDepthTestEnabled            |                                  |                                                              |
| CompareSymbolBuilds            |                                  |                                                              |
| ClearOverrideBuild             |                                  |                                                              |
| OverrideSkinSymbol             |                                  |                                                              |
| AssignItemSkins                |                                  |                                                              |
| SetSortWorldOffset             |                                  |                                                              |
| OverrideItemSkinSymbol         |                                  |                                                              |
| SetHaunted                     |                                  |                                                              |
| SetOceanBlendParams            |                                  |                                                              |
| UsePointFiltering              |                                  |                                                              |
| GetCurrentFacing               |                                  |                                                              |
| SetBankAndPlayAnimation        |                                  |                                                              |
| SetRayTestOnBB                 |                                  |                                                              |
| SetWorldSpaceAmbientLightPos   |                                  |                                                              |
| SetManualBB                    |                                  |                                                              |
| SetHighlightColour             |                                  |                                                              |
| GetSymbolOverride              |                                  | 获取覆盖通道名                                               |
| SetUILightParams               |                                  |                                                              |
| SetErosionParams               |                                  |                                                              |
| ClearSymbolExchanges           |                                  |                                                              |
| UseColourCube                  |                                  |                                                              |
| SetClientsideBuildOverride     |                                  |                                                              |
|                                |                                  |                                                              |
| FastForward                    |                                  |                                                              |
| SetFloatParams                 |                                  |                                                              |
| SetMultiSymbolExchange         |                                  |                                                              |
| GetBuild                       |                                  |                                                              |
| GetSymbolPosition              |                                  |                                                              |
| SetDepthWriteEnabled           |                                  |                                                              |
| AddOverrideBuild               |                                  |                                                              |
| SetInheritsSortKey             |                                  |                                                              |
| SetLightOverride               |                                  |                                                              |
| SetSkin                        |                                  |                                                              |
| SetSymbolBloom                 |                                  |                                                              |
| SetSymbolLightOverride         |                                  |                                                              |

