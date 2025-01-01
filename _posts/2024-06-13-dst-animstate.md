---
title: DST AnimState
description: AnimState 浅析
author: eattuce
date: 2024-06-13 15:10:00 +0800
categories: [Don't Starve Together, Tutorial]
tags: [dst, tips]
render_with_liquid: false
last_modified_at: 2024-06-22 17:20:00 +0800
---


> Spriter创建和打开的动画文件.scml其实也是一个类似 .xml .yaml的标记语言。
{: .prompt-tip }


Setters
```lua
-- .scml
SetBank("bankName")
-- Entity in .scml
SetBuild("buildName")
SetScale(x, y, z)
SetFinalOffset(1)
SetOrientation
SetLayer
SetPercent(.1)
SetTime
SetDeltaTimeMultiplier(2)
SetBloomEffectHandle("shaders/anim.ksh")
SetDefaultEffectHandle
SetMultColour(r, g, b, a)
SetAddColour(r, g, b, a)
SetSymbolMultColour(r, g, b, a)
SetSymbolExchange
SetSortOrder(1)
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
-- 1st: [0.0-1.0] 消失部分百分比 数值越大消失得越多
-- 2nd: [0.0-1.0] 残留部分的透明度 数值越大越透明
-- 3rd: [0.0-1.0] 消失部分透明度 数值越大越透明 (1,0,[0-1])仅改变这一项可以调整透明度
SetErosionParams(.5, 0.1, 1.0)
SetClientsideBuildOverride
SetFloatParams
SetMultiSymbolExchange
SetDepthWriteEnabled
SetInheritsSortKey
SetSkin
--  穿透黑暗的高亮0.5
SetLightOverride(.5)
--  symbol单独SetBloomEffectHandle("shaders/anim.ksh")
SetSymbolBloom("symbol")
--  symbol单独LightOverride(.5)
SetSymbolLightOverride("symbol", .5)
```


Getters
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
GetBuild()
GetSymbolPosition
```


Other
```lua
Show()
Hide()
HideSymbol("symbol")
ShowSymbol("symbol")
PlayAnimation("anim"[, false])
PushAnimation("anim"[, true])
Pause()
Resume()

-- overrides and clears
OverrideSymbol("originalSymbolName", "buildName", "newSymbolName")
OverrideMultColour(r, g, b, a)
OverrideShade
OverrideSkinSymbol
OverrideItemSkinSymbol

ClearOverrideSymbol("originalSymbolName")
ClearAllOverrideSymbols()
ClearBloomEffectHandle()
-- 反SetSymbolBloom
ClearSymbolBloom("symbolName")
ClearDefaultEffectHandle
-- 反AddOverrideBuild
ClearOverrideBuild("bankName")
ClearSymbolExchanges

-- 把名为bankName动画包中的动画
-- 添加到当前实体可播放的动画列表中
-- 比如要增加角色的动作
-- 同名动画覆盖
AddOverrideBuild("bankName")

IsSymbolOverridden
IsCurrentAnimation("animName")
AnimDone()
BuildHasSymbol
CompareSymbolBuilds
AssignItemSkins
UsePointFiltering
UseColourCube
FastForward
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

