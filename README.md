# GAS文档

<font color=#999AAA >
本文档是针对[原英文文档](./README.md)的中文译作，目的是以中文还原、学习和传播作者针对GAS的独到理解。翻译的过程会结合[虚幻官方文档](https://docs.unrealengine.com/4.27/zh-CN/InteractiveExperiences/GameplayAbilitySystem/) 中使用的术语标准，但是针对一些通用的概念，核心类，以及一些从业人员需要掌握的基础英文词汇，译者在做一些简单的解释后，还是会偏向于直接使用英文原单词 </font> 

这是我的一个简单多人案例项目，其中蕴含了我对虚幻引擎中Gameplay技能系统（Gameplay Ability System，简称GAS）插件的理解。首先需要说明的是，这并不是一份官方文档，这个项目以及我本身都并不归属于Epic Games。出于这点考虑，我并不会保障这篇文档中涉及到的信息的准确性。

这篇文档的目的是去解释GAS中的核心概念和类，并且从我对GAS的经验为出发点提一些额外的论述。关于GAS，用户社区已经积攒了大量的tribal knowledge（译者注：直译作部落知识，指的是那些一些无法言传的技能或者知识，是书本中获取不到的东西，也是通过工作者在大量实践和探索所得的心得和体会），而我在这里就是想分享一些属于我的理解。

案例项目和文档所使用的引擎版本为4.27。当然对于更旧一点的版本也有相应的分支，只是我目前已经停止相关的更新和支持，其中也自然会有一些bug和过期的信息等等。

[GASShooter](https://github.com/tranek/GASShooter)项目本项目的孪生项目，其中展示了GAS高级技术在多人FPS/TPS游戏中的使用。

当然，最佳的文档永远还是插件源码本身。

<a name="table-of-contents"></a>

## Table of Contents

> 1. [Intro to the GameplayAbilitySystem Plugin](#intro)
> 1. [Sample Project](#sp)
> 1. [Setting Up a Project Using GAS](#setup)
> 1. [Concepts](#concepts)  
>    4.1 [Ability System Component](#concepts-asc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.1.1 [Replication Mode](#concepts-asc-rm)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.1.2 [Setup and Initialization](#concepts-asc-setup)  
>    4.2 [Gameplay Tags](#concepts-gt)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.2.1 [Responding to Changes in Gameplay Tags](#concepts-gt-change)  
>    4.3 [Attributes](#concepts-a)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.1 [Attribute Definition](#concepts-a-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.2 [BaseValue vs CurrentValue](#concepts-a-value)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.3 [Meta Attributes](#concepts-a-meta)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.4 [Responding to Attribute Changes](#concepts-a-changes)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.5 [Derived Attributes](#concepts-a-derived)  
>    4.4 [Attribute Set](#concepts-as)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.1 [Attribute Set Definition](#concepts-as-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2 [Attribute Set Design](#concepts-as-design)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.1 [Subcomponents with Individual Attributes](#concepts-as-design-subcomponents)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.2 [Adding and Removing AttributeSets at Runtime](#concepts-as-design-addremoveruntime)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3 [Item Attributes (Weapon Ammo)](#concepts-as-design-itemattributes)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3.1 [Plain Floats on the Item](#concepts-as-design-itemattributes-plainfloats)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3.2 [`AttributeSet` on the Item](#concepts-as-design-itemattributes-attributeset)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3.3 [`ASC` on the Item](#concepts-as-design-itemattributes-asc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.3 [Defining Attributes](#concepts-as-attributes)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.4 [Initializing Attributes](#concepts-as-init)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.5 [PreAttributeChange()](#concepts-as-preattributechange)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.6 [PostGameplayEffectExecute()](#concepts-as-postgameplayeffectexecute)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.7 [OnAttributeAggregatorCreated()](#concepts-as-onattributeaggregatorcreated)  
>    4.5 [Gameplay Effects](#concepts-ge)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.1 [Gameplay Effect Definition](#concepts-ge-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.2 [Applying Gameplay Effects](#concepts-ge-applying)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.3 [Removing Gameplay Effects](#concepts-ga-removing)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.4 [Gameplay Effect Modifiers](#concepts-ge-mods)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.4.1 [Multiply and Divide Modifiers](#concepts-ge-mods-multiplydivide)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.4.2 [Gameplay Tags on Modifiers](#concepts-ge-mods-gameplaytags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.5 [Stacking Gameplay Effects](#concepts-ge-stacking)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.6 [Granted Abilities](#concepts-ge-ga)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.7 [Gameplay Effect Tags](#concepts-ge-tags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.8 [Immunity](#concepts-ge-immunity)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.9 [Gameplay Effect Spec](#concepts-ge-spec)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.9.1 [SetByCallers](#concepts-ge-spec-setbycaller)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.10 [Gameplay Effect Context](#concepts-ge-context)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.11 [Modifier Magnitude Calculation](#concepts-ge-mmc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12 [Gameplay Effect Execution Calculation](#concepts-ge-ec)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1 [Sending Data to Execution Calculations](#concepts-ge-ec-senddata)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.1 [SetByCaller](#concepts-ge-ec-senddata-setbycaller)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.2 [Backing Data Attribute Calculation Modifier](#concepts-ge-ec-senddata-backingdataattribute)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.3 [Backing Data Temporary Variable Calculation Modifier](#concepts-ge-ec-senddata-backingdatatempvariable)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.4 [Gameplay Effect Context](#concepts-ge-ec-senddata-effectcontext)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.13 [Custom Application Requirement](#concepts-ge-car)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.14 [Cost Gameplay Effect](#concepts-ge-cost)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15 [Cooldown Gameplay Effect](#concepts-ge-cooldown)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15.1 [Get the Cooldown Gameplay Effect's Remaining Time](#concepts-ge-cooldown-tr)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15.2 [Listening for Cooldown Begin and End](#concepts-ge-cooldown-listen)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15.3 [Predicting Cooldowns](#concepts-ge-cooldown-prediction)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.16 [Changing Active Gameplay Effect Duration](#concepts-ge-duration)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.17 [Creating Dynamic Gameplay Effects at Runtime](#concepts-ge-dynamic)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.18 [Gameplay Effect Containers](#concepts-ge-containers)  
>    4.6 [Gameplay Abilities](#concepts-ga)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1 [Gameplay Ability Definition](#concepts-ga-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1.1 [Replication Policy](#concepts-ga-definition-reppolicy)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1.2 [Server Respects Remote Ability Cancellation](#concepts-ga-definition-remotecancel)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1.3 [Replicate Input Directly](#concepts-ga-definition-repinputdirectly)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.2 [Binding Input to the ASC](#concepts-ga-input)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.2.1 [Binding to Input without Activating Abilities](#concepts-ga-input-noactivate)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.3 [Granting Abilities](#concepts-ga-granting)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.4 [Activating Abilities](#concepts-ga-activating)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.4.1 [Passive Abilities](#concepts-ga-activating-passive)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.5 [Canceling Abilities](#concepts-ga-cancelabilities)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.6 [Getting Active Abilities](#concepts-ga-definition-activeability)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.7 [Instancing Policy](#concepts-ga-instancing)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.8 [Net Execution Policy](#concepts-ga-net)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.9 [Ability Tags](#concepts-ga-tags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.10 [Gameplay Ability Spec](#concepts-ga-spec)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.11 [Passing Data to Abilities](#concepts-ga-data)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.12 [Ability Cost and Cooldown](#concepts-ga-commit)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.13 [Leveling Up Abilities](#concepts-ga-leveling)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.14 [Ability Sets](#concepts-ga-sets)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.15 [Ability Batching](#concepts-ga-batching)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.16 [Net Security Policy](#concepts-ga-netsecuritypolicy)  
>    4.7 [Ability Tasks](#concepts-at)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.1 [Ability Task Definition](#concepts-at-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.2 [Custom Ability Tasks](#concepts-at-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.3 [Using Ability Tasks](#concepts-at-using)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.4 [Root Motion Source Ability Tasks](#concepts-at-rms)  
>    4.8 [Gameplay Cues](#concepts-gc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.1 [Gameplay Cue Definition](#concepts-gc-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.2 [Triggering Gameplay Cues](#concepts-gc-trigger)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.3 [Local Gameplay Cues](#concepts-gc-local)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.4 [Gameplay Cue Parameters](#concepts-gc-parameters)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.5 [Gameplay Cue Manager](#concepts-gc-manager)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.6 [Prevent Gameplay Cues from Firing](#concepts-gc-prevention)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.7 [Gameplay Cue Batching](#concepts-gc-batching)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.7.1 [Manual RPC](#concepts-gc-batching-manualrpc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.7.2 [Multiple GCs on one GE](#concepts-gc-batching-gcsonge)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.8 [Gameplay Cue Events](#concepts-gc-events)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.9 [Gameplay Cue Reliability](#concepts-gc-reliability)  
>    4.9 [Ability System Globals](#concepts-asg)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.9.1 [InitGlobalData()](#concepts-asg-initglobaldata)  
>    4.10 [Prediction](#concepts-p)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.1 [Prediction Key](#concepts-p-key)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.2 [Creating New Prediction Windows in Abilities](#concepts-p-windows)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.3 [Predictively Spawning Actors](#concepts-p-spawn)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.4 [Future of Prediction in GAS](#concepts-p-future)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.5 [Network Prediction Plugin](#concepts-p-npp)  
>    4.11 [Targeting](#concepts-targeting)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.1 [Target Data](#concepts-targeting-data)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.2 [Target Actors](#concepts-targeting-actors)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.3 [Target Data Filters](#concepts-target-data-filters)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.4 [Gameplay Ability World Reticles](#concepts-targeting-reticles)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.5 [Gameplay Effect Containers Targeting](#concepts-targeting-containers)  
> 1. [Commonly Implemented Abilities and Effects](#cae)  
>    5.1 [Stun](#cae-stun)  
>    5.2 [Sprint](#cae-sprint)  
>    5.3 [Aim Down Sights](#cae-ads)  
>    5.4 [Lifesteal](#cae-ls)  
>    5.5 [Generating a Random Number on Client and Server](#cae-random)  
>    5.6 [Critical Hits](#cae-crit)  
>    5.7 [Non-Stacking Gameplay Effects but Only the Greatest Magnitude Actually Affects the Target](#cae-nonstackingge)  
>    5.8 [Generate Target Data While Game is Paused](#cae-paused)  
>    5.9 [One Button Interaction System](#cae-onebuttoninteractionsystem)  
> 1. [Debugging GAS](#debugging)  
>    6.1 [showdebug abilitysystem](#debugging-sd)  
>    6.2 [Gameplay Debugger](#debugging-gd)  
>    6.3 [GAS Logging](#debugging-log)  
> 1. [Optimizations](#optimizations)  
>    7.1 [Ability Batching](#optimizations-abilitybatching)  
>    7.2 [Gameplay Cue Batching](#optimizations-gameplaycuebatching)  
>    7.3 [AbilitySystemComponent Replication Mode](#optimizations-ascreplicationmode)  
>    7.4 [Attribute Proxy Replication](#optimizations-attributeproxyreplication)  
>    7.5 [ASC Lazy Loading](#optimizations-asclazyloading)  
> 1. [Quality of Life Suggestions](#qol)  
>    8.1 [Gameplay Effect Containers](#qol-gameplayeffectcontainers)  
>    8.2 [Blueprint AsyncTasks to Bind to ASC Delegates](#qol-asynctasksascdelegates)  
> 1. [Troubleshooting](#troubleshooting)  
>    9.1 [`LogAbilitySystem: Warning: Can't activate LocalOnly or LocalPredicted ability %s when not local!`](#troubleshooting-notlocal)  
>    9.2 [`ScriptStructCache` errors](#troubleshooting-scriptstructcache)  
>    9.3 [Animation Montages are not replicating to clients](#troubleshooting-replicatinganimmontages)  
>    9.4 [Duplicating Blueprint Actors is setting AttributeSets to nullptr](#troubleshooting-duplicatingblueprintactors)  
> 1. [Common GAS Acronyms](#acronyms)
> 1. [Other Resources](#resources)  
>    11.1 [Q&A With Epic Game's Dave Ratti](#resources-daveratti)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;11.1.1 [Community Questions 1](#resources-daveratti-community1)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;11.1.2 [Community Questions 2](#resources-daveratti-community2)  
> 1. [GAS Changelog](#changelog)  
>    * [4.27](#changelog-4.27)  
>    * [4.26](#changelog-4.26)  
>    * [4.25.1](#changelog-4.25.1)  
>    * [4.25](#changelog-4.25)  
>    * [4.24](#changelog-4.24)

<a name="intro"></a>
## 1. Gameplay技能系统（GAS）插件介绍

这里先引用[虚幻官方文档](https://docs.unrealengine.com/en-US/Gameplay/GameplayAbilitySystem/index.html)中的一段话：

>Gameplay技能系统是一个高度灵活的框架，可用于构建你可能会在RPG或MOBA游戏中看到的技能和属性类型。你可以构建可供游戏中的角色使用的动作或被动技能，使这些动作导致各种属性累积或损耗的状态效果，实现约束这些动作使用的"冷却"计时器或资源消耗，更改技能等级及每个技能等级的技能效果，激活粒子或音效，等等。简单来说，此系统可帮助你在任何现代RPG或MOBA游戏中设计、实现及高效关联各种游戏中的技能，既包括跳跃等简单技能，也包括你喜欢的角色的复杂技能集。

GAS插件是由Epic Games开发，并随着Unreal Engine 4一并推出的。其用法已经在3A级商业游戏（比如Paragon和Fortnite等）中进行了测试和验证。

这个插件针对单人及多人游戏提供了一套开箱即用的方案，其内容主要包括：

- 实现了基于等级的角色能力或技能，并可以配置相应的消耗和冷却时间（参见[GameplayAbilities](#concepts-ga)）

* 操作actor的 `Attributes` （属性，参见[Attributes](#concepts-a)）
* 为actor施加状态效果（参见[GameplayEffects](#concepts-ge)）
* 为actor标记`GameplayTags`（Gameplay标签，参见[GameplayTags](#concepts-gt)）
* 生成视觉或是声音效果（参见[GameplayCues](#concepts-gc)）
* 对上述所有内容的复制

在多人游戏中，GAS针对客户端预测（[client-side prediction](#concepts-p) ）也做了一些方面的支持：

- 技能的激活
- 播放动画蒙太奇
- 修改`Attributes`

* 应用`GameplayTags`
* 生成`GameplayCues`
* 通过连接到 `CharacterMovementComponent`的`RootMotionSource`函数进行运动

**GAS必须使用C++来进行设置**，但是`GameplayAbilities`和`GameplayEffects`可以由设计师通过蓝图来进行创建。

GAS目前存在的一些问题：

* `GameplayEffect`延迟调节（不能预测技能冷却时间而导致高延迟的玩家与低延迟的玩家相比，其低冷却的技能频率是更低的）
* 无法预测`GameplayEffect`的移除操作。但是，我们可以预测添加GameplayEffects的操作，从而预测其反向效果，从而高效得对效果进行清除。但这也并不总总是是合理或可行的，所以这一块目前仍然是一个问题。
* 缺少规范性的模板，多人游戏的示例，以及相应的文档。希望本文能够有助于解决这个问题。

**[⬆回到目录](#table-of-contents)**

<a name="sp"></a>

## 2. 示例项目

本文档中还包含一个多人第三人称射击的示例项目，可以作为读者去熟悉GAS插件的途径，但是不推荐还没有熟悉Unreal Engine 4的读者直接上手。我希望文档的读者用户已经了解了C++、蓝图、UMG、网络复制以及其他一些的有关UE4的课题内容。这个项目提供了一个示例，旨在说明如何设置一个基本的多人第三人称射击游戏项目，包括为玩家或是AI控制的英雄角色配置位于`PlayerState`类上的`AbilitySystemComponent` （`ASC`） ，以及为AI控制的小兵角色配置位于`Character`类上的`ASC`。

我的目标是保持项目尽量简单的同时，展示GAS的基础内容，并且演示一些常用的技能及其代码。出于为初学者服务的目标，这个项目并不会展示一些其他的高级特性（比如[子弹预测](#concepts-p-spawn)）。

演示的概念主要有：

* `PlayerState`上的`ASC`  vs `Character`上的`ASC` 
* `Attributes`的网络复制
* 动画蒙太奇的网络复制
* `GameplayTags`
* 在`GameplayAbilities`的内部或外部应用和删除`GameplayEffects` 
* 应用盔甲削弱的伤害来改变角色的生命值
* `GameplayEffectExecutionCalculations`
* 眩晕效果
* 死亡和重生
* 在服务器上由技能生成actor（如子弹）
* 预测性地改变本地玩家在瞄准和冲刺时速度
* 不断消耗体力进行冲刺
* 消耗魔法来使用技能
* 被动技能
* 堆叠 `GameplayEffects`
* 瞄准actor
* 由蓝图创建的`GameplayAbilities`
* 由C++创建的`GameplayAbilities`
* 实例化的`GameplayAbilities`
* 非实例化的`GameplayAbilities`（如跳跃）
* 静态`GameplayCues` （火枪子弹的冲击粒子特效）
* Actor `GameplayCues` (冲刺和眩晕的粒子特效)

英雄类有如下这些能力/技能：

| 技能                 | 输入绑定           | 是否可预测 | C++ / Blueprint | 描述                                                         |
| -------------------- | ------------------ | ---------- | --------------- | ------------------------------------------------------------ |
| Jump                 | Space Bar          | Yes        | C++             | 英雄跳跃。                                                   |
| Gun                  | Left Mouse Button  | No         | C++             | 从英雄的枪里射出子弹。动画可以预测但是子弹不行。             |
| Aim Down Sights      | Right Mouse Button | Yes        | Blueprint       | 当按钮持续按下时，英雄走得会更加缓慢，相机会放大，从而得以更精确地进行射击。 |
| Sprint               | Left Shift         | Yes        | Blueprint       | 当按钮持续按下时，英雄可以跑得更快，同时消耗体力。           |
| Forward Dash         | Q                  | Yes        | Blueprint       | 英雄以一定的体力为代价向前冲刺。                             |
| Passive Armor Stacks | Passive            | No         | Blueprint       | 每过4秒英雄会获得一层护甲的叠加，最多叠加4层。受到伤害时会损失一层护甲。 |
| Meteor               | R                  | No         | Blueprint       | 玩家指定一个位置来向敌人发射流星，造成伤害并且会施加眩晕。目标指定是可以预测的而生成流星则不是。 |

`GameplayAbilities`是用C++创建还是由蓝图创建其实并不重要。这里使用了两种方式的混合，从而举例说明如何分别在两种语言中做到这一点。

小兵不具备任何预定的`GameplayAbilities`。红色小兵有更强的生命值恢复能力，而蓝色小兵有更高的初始生命值。

对`GameplayAbility`的命名，我使用后缀`_BP`来表示`GameplayAbility`的逻辑部分是由蓝图进行创建的。而如果没有相应后缀的话，说明对应逻辑部分是在C++中创建的。

**蓝图资产命名前缀**

| 前缀 | 资产类型        |
| ---- | --------------- |
| GA_  | GameplayAbility |
| GC_  | GameplayCue     |
| GE_  | GameplayEffect  |

**[⬆回到目录](#table-of-contents)**

<a name="setup"></a>

## 3. GAS项目设置

当需要使用GAS时，需要对项目进行基本的设置：

1. 在编辑器中激活Gameplay Ability System插件
1. 编辑`YourProjectName.Build.cs`脚本，添加`"GameplayAbilities", "GameplayTags", "GameplayTasks"`到`PrivateDependencyModuleNames`
1. 重新生成你的Visual Studio项目文件
1. 从4.24版本开始，引擎强制要求调用`UAbilitySystemGlobals::Get().InitGlobalData()`来使用[`TargetData`](#concepts-targeting-data)。示例项目中是在`UAssetManager::StartInitialLoading()`中进行的调用。更多信息参考[`InitGlobalData()`](#concepts-asg-initglobaldata)（位于后续Ability System Global章节）。

完成上述步骤就激活了GAS相关功能。现在，可以添加[`ASC`](#concepts-asc)和[`AttributeSet`](#concepts-as)到你的`Character`或是`PlayerState`上，然后开始制作你的[`GameplayAbilities`](#concepts-ga)和[`GameplayEffects`](#concepts-ge)!

**[⬆回到目录](#table-of-contents)**

<a name="concepts"></a>

## 4. 核心概念 - Concepts

#### Sections

> 4.1 [Ability System Component](#concepts-asc)  
> 4.2 [Gameplay Tags](#concepts-gt)  
> 4.3 [Attributes](#concepts-a)  
> 4.4 [Attribute Set](#concepts-as)  
> 4.5 [Gameplay Effects](#concepts-ge)  
> 4.6 [Gameplay Abilities](#concepts-ga)  
> 4.7 [Ability Tasks](#concepts-at)  
> 4.8 [Gameplay Cues](#concepts-gc)  
> 4.9 [Ability System Globals](#concepts-asg)  
> 4.10 [Prediction](#concepts-p)

<a name="concepts-asc"></a>
### 4.1 技能系统组件 - Ability System Component
`AbilitySystemComponent` （`ASC`）是整个GAS系统中的核心。它本质上是一个`UActorComponent` （[`UAbilitySystemComponent`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UAbilitySystemComponent/index.html)），负责处理技能系统中涉及到的所有交互。任意`Actor`，只要它想要使用技能[`GameplayAbilities`](#concepts-ga)，拥有着属性[`Attributes`](#concepts-a)，或者接收效果[`GameplayEffects`](#concepts-ge)，都必须附着一个`ASC`组件。所有这些对象都存在于、被管理于以及被复制于`ASC`（其中`Attributes`是个特例，[`AttributeSet`](#concepts-as)负责其复制）。开发者们可以自行拓展`ASC`，当然你并不一定非得这样做。

拥有`ASC`的`Actor`也被称为是`ASC`的`OwnerActor`。 `ASC`实际上作用的`Actor`被称为是`AvatarActor`。`OwnerActor`和`AvatarActor`可以是同一个`Actor`，比如MOBA游戏中的一个简单的AI小兵。它们也可以是不同的`Actor`，比如MOBA游戏中玩家控制的英雄角色，其中`OwnerActor`是`PlayerState`而`TargetActor`则是`Character`类。大部分的`Actors`都会把`ASC`放在他们自己身上。但是某些情况下比如你的`Actor`需要使用重生机制，并且在重生后仍然保留死亡之前的`Attributes`或者是`GameplayEffects`（例如MOBA游戏中的英雄），那么`ASC`的理想位置就是在`PlayerState`上。

**注意：**如果你的`ASC`在`PlayerState`上，那么你可能会需要去增加`PlayerState`的`NetUpdateFrequency`。原本`PlayerState`里该值在默认情况下是很低的，可能会导致`Attributes`或者`GameplayTags`在客户端上的延迟。如果是这样的话，确保激活[`Adaptive Network Update Frequency`](https://docs.unrealengine.com/en-US/Gameplay/Networking/Actors/Properties/index.html#adaptivenetworkupdatefrequency)，这也是Fortnite（堡垒之夜）中的解决办法。

如果`OwnerActor`和`AvatarActor`是不同的`Actor`，那么两者都应该去实现接口`IAbilitySystemInterface`。这个接口只有一个需要重写的方法`UAbilitySystemComponent* GetAbilitySystemComponent() const`，会返回一个指针指向它的`ASC`组件。在系统内部，`ASC`互相之间就是通过寻找这个接口函数来进行相互之间的交互。

`ASC`存有当前处于激活状态的`GameplayEffects`，具体就位于`FActiveGameplayEffectsContainer ActiveGameplayEffects`。

`ASC`存有它所赋予的`Gameplay Abilities`，具体就位于`FGameplayAbilitySpecContainer ActivatableAbilities`。无论何时，当你想要遍历`ActivatableAbilities.Items`，请一定在你的循环之前添加`ABILITYLIST_SCOPE_LOCK();`语句，以锁定其中的内容以防对其中内容的修改（意外删除某项技能）。`ABILITYLIST_SCOPE_LOCK();`本质上是在作用范围内增加`AbilityScopeLockCount`然后当离开作用范围时相应的减少。不要尝试在`ABILITYLIST_SCOPE_LOCK();`的作用范围内移除某项技能（清除技能的函数会在内部检查`AbilityScopeLockCount`，从而防止在内容被锁定的情况下移除技能）。

<a name="concepts-asc-rm"></a>
#### 4.1.1 复制模式 - Replication Mode
`ASC`定义了三种不同的复制模式用以复制`GameplayEffects`、`GameplayTags`以及`GameplayCues`，分别是`Full`、`Mixed`以及`Minimal`。`Attributes`是由他们所在的`AttributeSet`来进行复制的。

| 复制模式   | 使用情景                             | 描述                                                                                                                    |
| ------------------ | --------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| `Full`             | 单人                           | `GameplayEffect`会被复制到所有客户端                                                                          |
| `Mixed`            | 多人和玩家控制的`Actors` | `GameplayEffects`仅被复制到拥有者客户端。只有`GameplayTags`和`GameplayCues`会被复制到所有客户端 |
| `Minimal`          | 多人和AI控制的`Actors`     | `GameplayEffects`不会复制到任何客户端。只有`GameplayTags`和`GameplayCues`会被复制到所有客户端           |

**注意：**`Mixed`复制模式要求`OwnerActor`的`Owner`必须是`Controller`。`PlayerState`的默认`Owner`是`Controller`，但是`Character`不是。如果使用`Mixed`复制模式时其`OwnerActor`不是`PlayerState`，那么你需要调用`OwnerActor`上的`SetOwner()`并传递一个有效的`Controller`进去。

从4.24版本开始，`PossessedBy()`会将`Pawn`的拥有者设置为新的`Controller`。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-asc-setup"></a>
#### 4.1.2 设置和初始化 - Setup and Initialization
`ASC`通常是在`OwnerActor`的构造器中进行构造，并且显式得标记为可复制（replicated）。**这一步必须在C++内完成**。

```c++
AGDPlayerState::AGDPlayerState()
{
	// Create ability system component, and set it to be explicitly replicated
	AbilitySystemComponent = CreateDefaultSubobject<UGDAbilitySystemComponent>(TEXT("AbilitySystemComponent"));
	AbilitySystemComponent->SetIsReplicated(true);
	//...
}
```

`ASC`需要在服务器和客户端都完成初始化，其中两个重要的初始化参数为`OwnerActor`和`AvatarActor`。通常时机是在`Pawn`的`Controller`设置之后（在possession之后）。单人游戏只需要关心服务器路径。

对于玩家控制的角色（ASC存在于Pawn之上），我通常是在`Pawn`的`PossessedBy()`方法中完成`ASC`在服务器的初始化，在`PlayerController`的`AcknowledgePossession()`方法中完成`ASC`在客户端的初始化。

```c++
void APACharacterBase::PossessedBy(AController * NewController)
{
	Super::PossessedBy(NewController);

	if (AbilitySystemComponent)
	{
		AbilitySystemComponent->InitAbilityActorInfo(this, this);
	}

	// ASC MixedMode replication requires that the ASC Owner's Owner be the Controller.
	SetOwner(NewController);
}
```

```c++
void APAPlayerControllerBase::AcknowledgePossession(APawn* P)
{
	Super::AcknowledgePossession(P);

	APACharacterBase* CharacterBase = Cast<APACharacterBase>(P);
	if (CharacterBase)
	{
		CharacterBase->GetAbilitySystemComponent()->InitAbilityActorInfo(CharacterBase, CharacterBase);
	}

	//...
}
```

对于玩家控制的角色（`ASC`存在于`PlayerState`上），我通常是在`Pawn`的`PossessedBy()`方法中完成`ASC`在服务器的初始化，在`Pawn`的`OnRep_PlayerState()`方法中完成`ASC`在客户端的初始化。这确保了`PlayerState`在客户端上已经存在。

```c++
// Server only
void AGDHeroCharacter::PossessedBy(AController * NewController)
{
	Super::PossessedBy(NewController);

	AGDPlayerState* PS = GetPlayerState<AGDPlayerState>();
	if (PS)
	{
		// Set the ASC on the Server. Clients do this in OnRep_PlayerState()
		AbilitySystemComponent = Cast<UGDAbilitySystemComponent>(PS->GetAbilitySystemComponent());

		// AI won't have PlayerControllers so we can init again here just to be sure. No harm in initing twice for heroes that have PlayerControllers.
		PS->GetAbilitySystemComponent()->InitAbilityActorInfo(PS, this);
	}

	//...
}
```

```c++
// Client only
void AGDHeroCharacter::OnRep_PlayerState()
{
	Super::OnRep_PlayerState();

	AGDPlayerState* PS = GetPlayerState<AGDPlayerState>();
	if (PS)
	{
		// Set the ASC for clients. Server does this in PossessedBy.
		AbilitySystemComponent = Cast<UGDAbilitySystemComponent>(PS->GetAbilitySystemComponent());

		// Init ASC Actor Info for clients. Server will init its ASC when it possesses a new Actor.
		AbilitySystemComponent->InitAbilityActorInfo(PS, this);
	}

	// ...
}
```

如果你得到如下的错误反馈`LogAbilitySystem: Warning: Can't activate LocalOnly or LocalPredicted ability %s when not local!`，那说明你并没有在客户端上初始化你的`ASC`。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gt"></a>
### 4.2 游戏标签 - Gameplay Tags
[`FGameplayTags`](https://docs.unrealengine.com/en-US/API/Runtime/GameplayTags/FGameplayTag/index.html)是通过`GameplayTagManager`进行注册的具备一系列层级的标签名字，如`Parent.Child.Grandchild...`。这些标签在分类和描述一个对象的状态时格外有用。例如，如果一个角色被眩晕了，我们可以在眩晕的持续时间之内给它一个`State.Debuff.Stun`的`GameplayTag`。

你会发现其实你是用`GameplayTags`替代了之前用布尔或者枚举来处理的内容，并且通过判断某个对象是否具备特定的`GameplayTags`来进行一些布尔逻辑运算。

当为某个对象赋予标签时，我们通常会将标签添加到对象上的`ASC`，这样GAS就能和他们产生相应的交互。`UAbilitySystemComponent`实现了接口`IGameplayTagAssetInterface`中的给定方法，来访问它所有拥有的`GameplayTags`。

多个`GameplayTags`可以存储在一个`FGameplayTagContainer`里。这里通常我们更偏向于使用`GameplayTagContainer`而不是`TArray<FGameplayTag>`，因为`GameplayTagContainers`中有一些高效的工具。因为标签本质上就是标准的`FName`，如果项目设置中的`Fast Replication`处于激活状态的话，他们可以被高效得打包在一起到`FGameplayTagContainers`中，以方便网络复制的使用。`Fast Replication`要求服务器和客户端们有着相同的`GameplayTags`列表。这通常并不会有什么问题，所以你尽管激活这个选项就好了。`GameplayTagContainers`在遍历时也可以一返回到一个`TArray<FGameplayTag>`数组。

存储在`FGameplayTagCountContainer`的`GameplayTags`有着一个`TagMap`，其内存储着相应`GameplayTag`的实例的数量。`FGameplayTagCountContainer`可能还存在某些`GameplayTag`，其对应的`TagMapCount`实际上已经是0了。当调试的时候可能就会遇到这种情况。`HasTag()`或者`HasMatchingTag()`或者相类似的函数都会去检查`TagMapCount`，并且在`GameplayTag`不存在或者相应的`TagMapCount`为0的情况下直接返回false。

`GameplayTags`必须在`DefaultGameplayTags.ini`中提早进行定义。虚幻编辑器在项目设置中提供了一个编辑界面来让开发者管理`GameplayTags`，而无需手动编辑`DefaultGameplayTags.ini`文件。`GameplayTag`的编辑器内可以进行创建、改名、查找引用以及删除操作。

![GameplayTag Editor in Project Settings](https://github.com/tranek/GASDocumentation/raw/master/Images/gameplaytageditor.png)

查找`GameplayTag`的引用将会在编辑器内打开一个类似`Reference Viewer`的图形界面，其中展示了所有引用`GameplayTag`的资产。当然，不包括任何C++类。

重命名`GameplayTag`实际上是创建了一个重定向，与原`GameplayTag`有引用关系的资产可以通过这个重定向找到新的`GameplayTag`。我偏向于创建一个新的`GameplayTag`，然后手动更新所有的引用，然后删除掉旧的`GameplayTag`，从而避免创建一个重定向。

除了`Fast Replication`，`GameplayTag`编辑器中有一个选项可以选择常用的网络复制的`GameplayTag`，从而进一步实现优化。

如果`GameplayTag`是由`GameplayEffect`添加的话，那他们就会被复制。`ASC`可以让你添加不会被复制并且必须手动管理的`LooseGameplayTags`。示例项目使用`LooseGameplayTag`来作为`State.Dead`，这样所属客户端在他们的hp降为0后就能够立即响应。重生的时候需要手动得将`TagMapCount`设置回0。仅在使用`LooseGameplayTags`时才会用到手动调整`TagMapCount`。我偏向于使用`UAbilitySystemComponent::AddLooseGameplayTag()`和`UAbilitySystemComponent::RemoveLooseGameplayTag()` 这两个函数，而不是直接手动调整`TagMapCount`。

在C++中去获取`GameplayTag`的引用：
```c++
FGameplayTag::RequestGameplayTag(FName("Your.GameplayTag.Name"))
```

至于高级的`GameplayTag`的相关操作如获取父级或者子级的`GameplayTag`，参考`GameplayTagManager`中提供的一些方法。要访问`GameplayTagManager`，首先要包含`GameplayTagManager.h`头文件，然后调用`UGameplayTagManager::Get().FunctionName`函数即可。实际上`GameplayTagManager`将`GameplayTag`存储为关系节点（父，子，等等），所以处理速度要快于字符串的操作和比较。

`GameplayTags`和`GameplayTagContainers`可以通过`UPROPERTY`中的说明符`Meta = (Categories = "GameplayCue")`来过滤蓝图中的标签，从而仅显示父级标签为`GameplayCue`的那些`GameplayTags`。当你知道那些仅用于`GameplayCues`的`GameplayTag`或者`GameplayTagContainer`变量时这样的操作就非常有用处了。

此外，还有一个单独的结构体`FGameplayCueTag`封装了`FGameplayTag`，这样也可以实现上面的那种过滤的效果。

如果你想在一个函数中过滤`GameplayTag`参数时，可以使用`UFUNCTION`中的说明符`Meta = (GameplayTagFilter = "GameplayCue")`。而函数中的`GameplayTagContainer`参数是不可以被过滤的（译者注：现在已经支持过滤的特性，但是后续翻译时仍然保留了相关描述，目的是让大家了解相关特性是如何通过元数据进行支持的）。如果你想要让你的引擎支持这个特性，参考`Engine\Plugins\Editor\GameplayTagsEditor\Source\GameplayTagsEditor\Private\SGameplayTagGraphPin.cpp`中的函数`SGameplayTagGraphPin::ParseDefaultValueData()`是怎样调用`FilterString = UGameplayTagsManager::Get().GetCategoriesMetaFromField(PinStructType);`并且在函数`SGameplayTagGraphPin::GetListContent()`中传递`FilterString`到`SGameplayTagWidget`。`Engine\Plugins\Editor\GameplayTagsEditor\Source\GameplayTagsEditor\Private\SGameplayTagContainerGraphPin.cpp`中的`GameplayTagContainer`相关函数并没有检查元字段属性并代入过滤器。

示例项目广泛使用了`GameplayTags`。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gt-change"></a>
#### 4.2.1 响应游戏标签的变化 - Responding to Changes in Gameplay Tags
`ASC`为`GameplayTags`的添加和删除提供了相应的委托。需要给定一个`EGameplayTagEventType`来表明事件类型，即是`GameplayTags`的增加亦或是删除，还是`GameplayTag`的 `TagMapCount`发生了变化之类的。

```c++
AbilitySystemComponent->RegisterGameplayTagEvent(FGameplayTag::RequestGameplayTag(FName("State.Debuff.Stun")), EGameplayTagEventType::NewOrRemoved).AddUObject(this, &AGDPlayerState::StunTagChanged);
```

回调函数中有两个参数，分别是`GameplayTag`和新的`TagCount`。
```c++
virtual void StunTagChanged(const FGameplayTag CallbackTag, int32 NewCount);
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-a"></a>
### 4.3 属性 - Attributes

<a name="concepts-a-definition"></a>
#### 4.3.1 属性的定义 - Attribute Definition
`Attribute`是由结构体[`FGameplayAttributeData`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FGameplayAttributeData/index.html)定义的一系列浮点值。它们能够表示角色拥有的生命值、角色等级以及药水的充能数等等。只要是从属于`Actor`的游玩相关的数值，就可以考虑将其设为一项`Attribute`。`Attribute`通常应该由[`GameplayEffect`](#concepts-ge)来负责修改，这样`ASC`就能够[predict](#concepts-p)预测相应的变化。

`Attributes`是由[`AttributeSet`](#concepts-as)来进行定义的并保存在其中。`AttributeSet`负责处理那些被标记要进行复制的`Attributes`。参考[`AttributeSets`](#concepts-as)的相关小节，来获取更多关于如何定义`Attributes`的内容。

**小贴士：**如果你不想要某个`Attribute`显示在编辑器的`Attributes`列表中，可以使用`Meta = (HideInDetailsView)`这样的属性说明符（`Property Specifier`）。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-a-value"></a>
#### 4.3.2 基本值和当前值 - BaseValue vs CurrentValue
每个属性`Attribute`都由两个值组成——基本值`BaseValue`和当前值`CurrentValue`。`BaseValue`是`Attribute`的一个恒定值，而`CurrentValue`则是`BaseValue`再叠加上来自`GameplayEffects`的临时修改后的结果。例如，你的`Character`可能会有一个移动速度`Attribute`，其`BaseValue`为600u/s（译者注：单位虚幻距离每秒）。此时还没有施加任何的影响移动速度相关的`GameplayEffects`，`CurrentValue`也就还是600u/s。如果角色被施加了一个50u/s的移速buff，`BaseValue`还仍然是600u/s，而`CurrentValue`此时则是600 + 50 = 650u/s。当移速的buff消失后吗，`CurrentValue`会恢复到`BaseValue`的值，也就是600u/s。

GAS的新手经常会把`BaseValue`和`Attribute`的最大值搞混，把两者当作同一个东西。这种认知并不正确。`Attributes`的最大值也会发生改变，它会和技能或者UI相关联，应该作为一个单独的`Attributes`来处理。对于硬编码的最大值和最小值，有一种方式可以通过`FAttributeMetaData`的`DataTable`来定义（其中有关于最大值和最小值设置的内容），但是Epic对于这个结构体注释道：work in progress，也就是该功能目前还没有稳定下来，可能还会进行修改。详细内容请参阅`AttributeSet.h`。为了防止混淆，我建议是将那些和技能或者UI关联的最大值作为一个单独的`Attributes`来对待——硬编码的最大值和最小值仅用于限定`AttributeSet`中的`Attributes`的上下限的限定。`Attributes`的上下限的限制的讨论后面还会继续进行，具体是在`GameplayEffects`为属性施加影响时，比如[PreAttributeChange()](#concepts-as-preattributechange)中对`CurrentValue`可以发生的变化的限制，又比如[PostGameplayEffectExecute()](#concepts-as-postgameplayeffectexecute)中对`BaseValue`可以发生的变化的限制。

即刻生效`Instant`的`GameplayEffects`会对`BaseValue`产生永久性的影响，而持续一段时间`Duration`的和无限持续`Infinite`的`GameplayEffects`改变的是`CurrentValue`。周期性`Periodic`的`GameplayEffects`和`instant`类型的`GameplayEffects`是类似的，都改变的是`BaseValue`。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-a-meta"></a>
#### 4.3.3 元属性 - Meta Attributes
一些`Attributes`会作为和其他`Attributes`作交互的临时值的占位数据，这一类的属性被称为是元属性`Meta Attributes`。例如，我们通常会去将伤害值定义为`Meta Attribute`。我们使用伤害值的`Meta Attribute`作为占位数据，而不是使用`GameplayEffect`直接改变我们的生命值的`Attribute`。这样，伤害值就可以通过 [`GameplayEffectExecutionCalculation`](#concepts-ge-ec)中的buff和debuff等进行修改，也可以在`AttributeSet`中作进一步处理，例如让伤害值减去当前的护甲的`Attribute`，然后再让生命值的`Attribute`减去前面的结果。伤害值的`Meta Attribute`在多个`GameplayEffects`之间并不是恒定的，可以被任意一个覆盖重写。`Meta Attributes`通常不会被复制。

像我们经常会说：“我造成了多少的伤害”，“这个伤害值怎么处理”之类的，`Meta Attributes`为此（伤害和治疗这类的属性）提供了一个良好的逻辑分离。这里的逻辑分离意思是我们的`Gameplay Effects`和`Execution Calculations`并不需要知道目标是如何处理这个伤害值的。继续我们关于伤害的话题，`Gameplay Effect`决定了伤害值的多少，然后`AttributeSet`去具体处理这个值。并不是所有的角色都有着相同的`Attributes`，特别是当你拓展`AttributeSets`的子类时。基类`AttributeSet`可能仅有生命值一个`Attribute`，其子类可能添加了一个护盾的`Attribute`。那自然的，基类和子类在处理这个伤害值的时候就不同了。

即便`Meta Attributes`是一个良好的设计模式，但是这并不意味着非得用它不可。如果你仅有一个`Execution Calculation`用来处理所有的伤害值，且所有角色共享着同一个`Attribute Set`，那么你就可以直接在`Execution Calculation`中来作伤害值、生命值和护盾值的计算和修改。这样做的代价自然就是牺牲掉一定的灵活性，这中间的权衡全在于你。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-a-changes"></a>
#### 4.3.4 响应属性的变化 - Responding to Attribute Changes
要监听某个`Attribute`的变化从而更新UI或者其他游玩部分，可以使用`UAbilitySystemComponent::GetGameplayAttributeValueChangeDelegate(FGameplayAttribute Attribute)`。这个方法返回一个委托，你可以自由绑定相应的回调，当对应的`Attribute`发生变化时就会自动执行这个回调。这个委托提供了一个`FOnAttributeChangeData`参数，有`NewValue`，`OldValue`以及`FGameplayEffectModCallbackData`。**注意：**`FGameplayEffectModCallbackData`只能够在服务器进行设置。

```c++
AbilitySystemComponent->GetGameplayAttributeValueChangeDelegate(AttributeSetBase->GetHealthAttribute()).AddUObject(this, &AGDPlayerState::HealthChanged);
```

```c++
virtual void HealthChanged(const FOnAttributeChangeData& Data);
```

示例项目将`Attribute`值变化的委托绑定到`GDPlayerState`中的一个方法来更新HUD并响应玩家的死亡（生命值归零）的情况。

在示例项目中还包含一个使用异步任务`ASyncTask`将所有这些封装起来的自定义蓝图节点。它被用在名为`UI_HUD`的`UMG Widget`用来更新生命值，魔法值以及体力值。这个`AsyncTask`会一直存在直到手动调用了`EndTask()`，我们一般会在`UMG Widget`的`Destruct`事件中去调用。参阅`AsyncTaskAttributeChanged.h/cpp`获取更多内容。

![Listen for Attribute Change BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/attributechange.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-a-derived"></a>
#### 4.3.5 衍生属性 - Derived Attributes
要令某个`Attribute`的值是从其他某个或者某些`Attributes`的值衍生过来，需要使用`Infinite`类型的`GameplayEffect`，以及一个或多个`Attribute Based`或者[`MMC`](#concepts-ge-mmc)的[`Modifiers`](#concepts-ge-mods)。`Derived Attribute`将会自动根据其依赖的`Attribute`的更新而进行更新。

`Derived Attribute`上的所有`Modifiers`的最终公式与`Modifier Aggregators`的公式是同一个。如果你需要依照一定的顺序进行计算，需要在`MMC`内完成所有的操作。

```
((CurrentValue + Additive) * Multiplicitive) / Division
```

**注意：**如果在PIE中运行多个客户端时，你需要在编辑器偏好界面中禁用`Run Under One Process`，否则处第一个客户端以外的其他客户端将不会更新`Derived Attributes`。

这里我们举个例子，我们有一个`Infinite`类型的`GameplayEffect`，其会根据`TestAttrB`和`TestAttrC`的值来推导`TestAttrA`的值。公式具体为`TestAttrA = (TestAttrA + TestAttrB) * ( 2 * TestAttrC)`。无论何时，当`TestAttrB`和`TestAttrC`中的任意一个属性更新时，那么`TestAttrA`将会自动根据上面的公式进行计算。

![Derived Attribute Example](https://github.com/tranek/GASDocumentation/raw/master/Images/derivedattribute.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as"></a>
### 4.4 属性集 - Attribute Set

<a name="concepts-as-definition"></a>
#### 4.4.1 属性集的定义 - Attribute Set Definition
`AttributeSet`会负责`Attributes`的定义、保存和管理。开发者可以从[`UAttributeSet`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UAttributeSet/index.html)继承并拓展。在`OwnerActor`的构造函数中创建`AttributeSet`会将他自动注册到`OwnerActor`的`ASC`中。**这一步必须在C++中完成**。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-design"></a>
#### 4.4.2 属性集的设计 - Attribute Set Design
一个`ASC`可以拥有一个或者许多个的`AttributeSets`。`AttributeSets`的内存消耗总体上是微不足道的，所以要使用具体多少是完全由开发者来决定和安排。

在你的游戏中可以让所有的`Actor`都共享同一个大而全的`AttributeSet`，具体每个`Actor`可以只关系自己需要的属性忽略那些和自己无关的。

或者，你也选择可以创建多个`AttributeSet`表示多个不同的`Attributes`组，这样你就可以根据`Actors`的需要进行有选择的添加。例如，你可以有一个包含所有和生命值相关的`Attributes`的`AttributeSet`；还可以有一个包含所有和法力值相关的`Attributes`的`AttributeSet`；等等。在MOBA游戏中，英雄可能会用到法力值属性，而小兵往往不需要。因此可以给英雄们一个法力值的`AttributeSet`，而小兵就不需要了。

此外，`AttributeSets`也可以继承使用，这是另一种为`Actor`有选择得赋予`Attributes`的方式。在内部`Attributes`是以`AttributeSetClassName.AttributeName`的方式来进行引用。当你继承了`AttributeSet`，所有父类中继承过来的`Attributes`也不许通过父类的类名作为前缀来进行引用，即`ParentClassName.AttributeName`。

虽然你可以使用多个`AttributeSet`，但是源自同一类的`AttributeSet`最多只能有一个（比如某个`AttributeSet`和某个继承自该`AttributeSet`的`AttributeSet`就算是同一个，因为他们源自同一个类）。如果你有多个源自同一个类的`AttributeSet`，系统就不知道你要调用的是哪一个`AttributeSet`而去随机（译者注：属于不可预测的行为，C++代码的原则是要规避不可预测的编码）的选择一个。

<a name="concepts-as-design-subcomponents"></a>
##### 4.4.2.1 具备独立属性的子组件 - Subcomponents with Individual Attributes
设想这样一种情形：某个`Pawn`上面有很多个负责抵御伤害的组件（比如说多个独立的可被破坏的护甲），如果你知道`Pawn`可拥有的护甲的最大数量，那么可以在该`Pawn`上做一个`AttributeSet`，其中包含着许多的生命值`Attributes`，比如说DamageableCompHealth0，DamageableCompHealth1等等，来表示这些可以抵御伤害的组件的逻辑上的插槽（即建立护甲和生命值属性的逻辑关联）。在你的表示护甲的类的实例上，令表示对应的生命值的插槽`Attribute`可以由`GameplayAbilities`进行读取或者由[`Executions`](#concepts-ge-ec)来读取，从而可以知道某个护甲遭到伤害时，伤害应该结算到具体哪一个`Attribute`上。即使某些`Pawns`拥有的护甲数量比`AttributeSet`预先设定的数量小也没有关系，因为可以不去使用对应的`Attribute`，而这部分**额外**的内存消耗是微乎其微的。

如果你的子组件每个上面都需要很多个的`Attributes`，或者这个数量是未知的，亦或者子组件会被从现有个体上卸载然后被其他玩家使用（比如说你的角色死亡后掉落的武器被别人拾取），总之不管什么原因前面提到的方法无法完全解决你的问题，我的建议是直接使用老办法，即不用`Attributes`系统来做而改用老办法，即单独创建一些float类型的值之类的（译者注：因为此时情况的复杂性再使用`Attributes`来强行拓展已经是弊大于利了）。参阅[Item Attributes](#concepts-as-design-itemattributes)。

<a name="concepts-as-design-addremoveruntime"></a>
##### 4.4.2.2 运行时添加和删除属性集 - Adding and Removing AttributeSets at Runtime
可以在运行时从`ASC`中添加和删除`AttributeSets`，当然移除掉某些`AttributeSets`的行为可能是很危险的。例如，如果某个`AttributeSet`的移除在客户端上早于在服务器端，而恰巧此时有一个`Attribute`值被复制到客户端，这样的话`Attribute`就找不对对应的`AttributeSet`从而导致游戏奔溃。

装备武器时：
```c++
AbilitySystemComponent->GetSpawnedAttributes_Mutable().AddUnique(WeaponAttributeSetPointer);
AbilitySystemComponent->ForceReplication();
```

卸载武器时：
```c++
AbilitySystemComponent->GetSpawnedAttributes_Mutable().Remove(WeaponAttributeSetPointer);
AbilitySystemComponent->ForceReplication();
```
<a name="concepts-as-design-itemattributes"></a>
##### 4.4.2.3 物品的属性（武器弹药） - Item Attributes (Weapon Ammo)
有多种方式可以实现带有`Attributes`的可装备物品（武器弹药，防具耐久等）。所有这些东西都是把值直接存在物品上。这对于能够被多个玩家装备和使用的那些物品来说是必须的。

> 1. 在物品上完全都用float来处理 (**推荐**)
> 2. 为物品分别分配独立的`AttributeSet`
> 3. 为物品分别分配独立的`ASC`

<a name="concepts-as-design-itemattributes-plainfloats"></a>
###### 4.4.2.3.1 在物品上完全都用浮点数来处理 - Plain Floats on the Item
即直接在物品类实例上使用浮点值而不是`Attributes`。Fortnite和[GASShooter](https://github.com/tranek/GASShooter)都是用这种方式来处理弹药的。对于枪械，具体就是存储最大的弹夹数量，当前弹夹中的弹药数量，后备弹药等等，把这些都以支持复制的浮点数（`COND_OwnerOnly`）形式存在枪械实例上。如果武器可以共享后备弹药，也就是说所有的武器都是使用的同一种弹药，那么你可以为`Character`添加一个代表后备弹药的`Attribute`及其相应的`AttributeSet`（重新加载能力时可以使用一个`Cost GE`来从后备弹药中抽取然后装填到枪械的弹夹中）。因为你的当前弹夹弹药并没有用`Attributes`来表示，你可能需要重写`UGameplayAbility`中的某些函数来检查和修改枪械上对应的float类型值。在授予技能时，令枪械作为[`GameplayAbilitySpec`](https://github.com/tranek/GASDocumentation#concepts-ga-spec)中的`SourceObject`，这样你才可以在技能中去访问枪械的相应数据。

为了防止枪械在快速自动开火中由于弹药数量的复制而搞乱本地的弹药数量，需要当玩家在`PreReplication()`中有`IsFiring`的`GameplayTag`时禁用掉复制功能。你也可以在这里实现你本地的预测。

```c++
void AGSWeapon::PreReplication(IRepChangedPropertyTracker& ChangedPropertyTracker)
{
	Super::PreReplication(ChangedPropertyTracker);

	DOREPLIFETIME_ACTIVE_OVERRIDE(AGSWeapon, PrimaryClipAmmo, (IsValid(AbilitySystemComponent) && !AbilitySystemComponent->HasMatchingGameplayTag(WeaponIsFiringTag)));
	DOREPLIFETIME_ACTIVE_OVERRIDE(AGSWeapon, SecondaryClipAmmo, (IsValid(AbilitySystemComponent) && !AbilitySystemComponent->HasMatchingGameplayTag(WeaponIsFiringTag)));
}
```

优势：
1. 避免了使用`AttributeSets`的局限性（后面会有详细内容）

限制：
1. 无法使用现有的`GameplayEffect`的工作流（比如以`Cost GEs`来处理弹药的使用，等等）
2. 需要进一步拓展`UGameplayAbility`（重写其中一些函数），来检查和处理弹药的消耗（从而应对float类型而非`Attribute`）

<a name="concepts-as-design-itemattributes-attributeset"></a>
###### 4.4.2.3.2 为物品分别分配独立的属性集 - AttributeSet on the Item
在物品上使用独立的`AttributeSet`，当玩家[装备物品时将物品上的`AttributeSet`添加到玩家的`ASC`上](#concepts-as-design-addremoveruntime)也是可以的，但是这样的做法也会相应的带来一些问题。我在[GASShooter](https://github.com/tranek/GASShooter)的早期版本中就是用这种方式来处理弹药的。武器将一些`Attributes`，比如最大弹夹数，当前弹夹中的弹药，后备弹药等等，存储到一个`AttributeSet`放到武器类上。如果武器间共享后备弹药，你可以将后备弹药这个`Attribute`移动到角色身上，用一个共享弹药的`AttributeSet`来保管。当在服务器上玩家装备武器时，武器的`AttributeSet`将会添加到玩家的`ASC::SpawnedAttributes`中。服务器将这个复制到客户端。如果武器被卸载掉，再从`ASC::SpawnedAttributes`中移除掉`AttributeSet`。

当`AttributeSet`保存在非`OwnerActor`的什么东西上（比如说武器），你会发现在`AttributeSet`上发现一些编译错误。解决方案是在`BeginPlay()`中构造`AttributeSet`，而非在构造函数中，然后还要在武器上实现实现`IAbilitySystemInterface`接口（当你装备武器时设置一个指向`ASC`的指针，就和之前在Character或者PlayerState上实现的这个接口类似）。

```c++
void AGSWeapon::BeginPlay()
{
	if (!AttributeSet)
	{
		AttributeSet = NewObject<UGSWeaponAttributeSet>(this);
	}
	//...
}
```

参见 [older version of GASShooter](https://github.com/tranek/GASShooter/tree/df5949d0dd992bd3d76d4a728f370f2e2c827735).

优势：
1. 可以使用现有`GameplayAbility`和`GameplayEffect`的工作流 workflow（比如以`Cost GEs`来处理弹药的使用，等等）
2. 在物品不多时比较容易设置

限制：
1. 对于每种武器类型都需要去定制一个新的`AttributeSet`类。`ASCs`只能够保存一个`AttributeSet`类的实例，因为对某个`Attribute`的修改会去在`ASC`的`SpawnedAttributes`数组中查找他们`AttributeSet`类的第一个实例。额外的同一个或者同源的`AttributeSet`类会被忽略掉。
2. 出于上面的原因，那么同种类型的装备你就只能装备一把了。
3. 移除掉某个`AttributeSet`是危险的行为。比如说在GASShooter里，如果玩家用火箭筒杀掉自己，玩家会立即卸载掉火箭筒这件装备（并从`ASC`中卸载`AttributeSet`）。当服务器去复制火箭筒弹药这个`Attribute`的变化时，客户端上的`ASC`上已经没有`AttributeSet`，这样游戏就奔溃了。

<a name="concepts-as-design-itemattributes-asc"></a>
###### 4.4.2.3.3 为物品分别分配独立的ASC - ASC on the Item
为每个物品都添加一个完整的`AbilitySystemComponent`是一个极端的方法。这个我既没有自己实现过，更没见过。要实现这个方案需要大量额外的工作。

> Is it viable to have several AbilitySystemComponents which have the same owner but different avatars (e.g. on pawn and weapon/items/projectiles with Owner set to PlayerState)?
>
> The first problem I see there would be implementing the IGameplayTagAssetInterface and IAbilitySystemInterface on the owning actor. The former may be possible: just aggregate the tags from all all ASCs (but watch out -HasAllMatchingGameplayTags may be met only via cross ASC aggregation. It wouldn't be enough to just forward that calls to each ASC and OR the results together). But the later is even trickier: which ASC is the authoritative one? If someone wants to apply a GE -which one should receive it? Maybe you can work these out but this side of the problem will be the hardest: owners will multiple ASCs beneath them.
>
> Separate ASCs on the pawn and the weapon can make sense on its own though. E.g, distinguishing between tags the describe the weapon vs those that describe the owning pawn. Maybe it does make sense that tags granted to the weapon also “apply” to the owner and nothing else (E.g, attributes and GEs are independent but the owner will aggregate the owned tags like I describe above). This could work out, I am sure. But having multiple ASCs with the same owner may get dicey.


*Dave Ratti from Epic's answer to [community questions #6](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)*

优势：
1. 可以使用现有的`GameplayAbility`和`GameplayEffect`的工作流（比如以`Cost GEs`来处理弹药的使用，等等）
2. 可以重用`AttributeSet`类（在每个武器的ASC上重复使用）

限制：
1. 为止的工作量
2. 可行性

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-attributes"></a>
#### 4.4.3 定义属性 - Defining Attributes
**`Attributes`只能通过C++来定义** 在`AttributeSet`的头文件中。我建议是将这一段宏代码块儿添加到每个`AttributeSet`的头文件里。它会帮我们自动生成对应`Attributes`的访问器（getter方法）和修改器（setter方法）。

```c++
// Uses macros from AttributeSet.h
#define ATTRIBUTE_ACCESSORS(ClassName, PropertyName) \
	GAMEPLAYATTRIBUTE_PROPERTY_GETTER(ClassName, PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_GETTER(PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_SETTER(PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_INITTER(PropertyName)
```

一个支持复制的生命值`Attribute`可以像这样来定义：

```c++
UPROPERTY(BlueprintReadOnly, Category = "Health", ReplicatedUsing = OnRep_Health)
FGameplayAttributeData Health;
ATTRIBUTE_ACCESSORS(UGDAttributeSetBase, Health)
```

别忘记在头文件中定义`OnRep`函数：
```c++
UFUNCTION()
virtual void OnRep_Health(const FGameplayAttributeData& OldHealth);
```

`AttributeSet`的.cpp文件中应该在相应的`OnRep`方法中添加`GAMEPLAYATTRIBUTE_REPNOTIFY`宏，从而支持预测系统的一些内容：
```c++
void UGDAttributeSetBase::OnRep_Health(const FGameplayAttributeData& OldHealth)
{
	GAMEPLAYATTRIBUTE_REPNOTIFY(UGDAttributeSetBase, Health, OldHealth);
}
```

最后，需要把`Attribute`添加到`GetLifetimeReplicatedProps`:
```c++
void UGDAttributeSetBase::GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& OutLifetimeProps) const
{
	Super::GetLifetimeReplicatedProps(OutLifetimeProps);

	DOREPLIFETIME_CONDITION_NOTIFY(UGDAttributeSetBase, Health, COND_None, REPNOTIFY_Always);
}
```

`REPNOTIFY_Always`是告诉`OnRep`函数当本地值和服务器下发的值相同的时候也去进行相应的触发。默认情况下（即这里不用`REPNOTIFY_Always`的情况下）这两个值一样的时候是不会触发`OnRep`函数的。

如果某个`Attribute`不需要复制，类似`Meta Attribute`，那么`OnRep`和`GetLifetimeReplicatedProps`这两步的设置是可以跳过的。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-init"></a>
#### 4.4.4 初始化属性 - Initializing Attributes
实际上存在很多种方法来去初始化`Attributes`（设置`BaseValue`以及`CurrentValue`的初始值）。Epic推荐使用一个`Instant`类型的`GameplayEffect`来完成这一步初始化（译者注：即通过应用应用一个`GameplayEffect`，这个`GameplayEffect`的施加效果就是对`Attributes`进行初始化）。这也是示例项目中使用的方法。

参考示例项目中的`GE_HeroAttributes`蓝图，其中有关于如何使用一个`Instant`类型的`GameplayEffect`来去初始化`Attributes`。这个`GameplayEffect`的实际应用是在C++的。

如果你在定义`Attributes`时使用了宏`ATTRIBUTE_ACCESSORS`，它会帮助你自动为`AttributeSet`里的每个`Attribute`都生成一个初始化方法，在C++里可以放心大胆的使用。

```c++
// InitHealth(float InitialValue) is an automatically generated function for an Attribute 'Health' defined with the `ATTRIBUTE_ACCESSORS` macro
AttributeSet->InitHealth(100.0f);
```

更多的`Attributes`的初始化方法可以进一步参阅`AttributeSet.h`。

**注意：** 在版本4.42之前，`FAttributeSetInitterDiscreteLevels`是和`FGameplayAttributeData`无法协调的。它是在`Attributes`还是原始浮点数时创建，并且嫌弃`FGameplayAttributeData`不是`Plain Old Data`（`POD`）。在4.24版本之后这个问题就被修复掉了 https://issues.unrealengine.com/issue/UE-76557。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-preattributechange"></a>
#### 4.4.5 PreAttributeChange()
`PreAttributeChange(const FGameplayAttribute& Attribute, float& NewValue)`是`AttributeSet`中重要的函数方法之一，主要是响应`Attribute`中的`CurrentValue`的修改发生之前的调用。这里最好就是去做一些对输入的限制和调整，利用`NewValue`来将可能会被应用到`CurrentValue`上的修改限制到某个合理的区间范围。

比如示例项目中将移动速度的修改器的限制如下：
```c++
if (Attribute == GetMoveSpeedAttribute())
{
	// Cannot slow less than 150 units/s and cannot boost more than 1000 units/s
	NewValue = FMath::Clamp<float>(NewValue, 150, 1000);
}
```
其中`GetMoveSpeedAttribute()`函数是前面我们提到的宏代码块生成的函数之一（[Defining Attributes](#concepts-as-attributes)）。

任何对`Attributes`的修改都会先调用这个方法，无论是使用`Attribute`的设置器（setters）（[Defining Attributes](#concepts-as-attributes)）亦或是使用[`GameplayEffects`](#concepts-ge)。

**注意：**此处的截取操作并没有永久的修改`ASC`的修改器。它改变的实际上只是通过对修改器的查询而返回的值。这意味着任何修改器（比如[`GameplayEffectExecutionCalculations`](#concepts-ge-ec)和[`ModifierMagnitudeCalculations`](#concepts-ge-mmc)）在重计算`CurrentValue`时都需要再实现截取的操作。

**注意**Epic的对`PreAttributeChange()`的注释提到，不要去使用它来处理游玩相关的事件，而只是把它用作数值的修正和处理。监听`Attribute`的变化而产生的和游玩相关的事件（译者注：比如说生命值、弹药数等属性的UI响应事件）的推荐的处理方案是使用`UAbilitySystemComponent::GetGameplayAttributeValueChangeDelegate(FGameplayAttribute Attribute)` （[Responding to Attribute Changes](#concepts-a-changes)）。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-postgameplayeffectexecute"></a>
#### 4.4.6 PostGameplayEffectExecute()
`PostGameplayEffectExecute(const FGameplayEffectModCallbackData & Data)`只是在由`Instant`类型的[`GameplayEffect`](#concepts-ge)对某个`Attribute`的`BaseValue`修改之后才会触发。这里可以进一步做一些`Attribute`相关的操作。

例如，在示例项目中我们令生命值的`Attribute`减去最终伤害值的`Meta Attribute`。如果有护盾的`Attribute`的话，我们可以在这里先让护盾值减去伤害值，然后再把剩余伤害（如果还有的话）应用到生命值上。示例项目也在这个位置来应用受击动画，显示伤害飘字，并且为击杀者赋予经验和金币奖励。从设计上说，伤害值的`Meta Attribute`将始终通过`Instant`类型的`GameplayEffect`来设置，并且永远不需要通过`Attribute`的设置器（setter）来设置。

其他一些仅由`Instant`类型的`GameplayEffect`来改变其`BaseValue`的`Attributes`，比如法力值和体力值，也可以在这里通过其最大值对应的`Attributes`来进行截取操作。

**注意：**当调用`PostGameplayEffectExecute()`，对`Attribute`的修改就已经生效了，但是还没有复制回客户端，所以在此处进行截取操作的话实际上不会进行两次值的复制。客户端仅收到截取过后的结果（最终值）。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-as-onattributeaggregatorcreated"></a>
#### 4.4.7 OnAttributeAggregatorCreated()
`OnAttributeAggregatorCreated(const FGameplayAttribute& Attribute, FAggregator* NewAggregator)`会在`AttributeSet`中的某个`Attribute`的聚合器`Aggregator`创建时进行触发。这里可以自定义设置[`FAggregatorEvaluateMetaData`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FAggregatorEvaluateMetaData/index.html)。`Aggregator`使用`AggregatorEvaluateMetaData`，基于所有应用到当前`Attribute`的[`Modifiers`](#concepts-ge-mods)来计算该`Attribute`的的`CurrentValue`。默认为情况下，`Aggregator`使用`AggregatorEvaluateMetaData`来确定哪些`Modifiers`符合`MostNegativeMod_AllPositiveMods`的要求，而`MostNegativeMod_AllPositiveMods`允许所有的正面的`Modifiers`后仅仅最负面的`Modifiers`。Paragon（虚幻争霸）中使用的就是这种方式，对于负面的减速效果的话在某个具体的时间点不管施加多少个只应用最负面的那个，而所有的正面的加速效果则全盘应用。没有通过要求的`Modifiers`仍然会存在于`ASC`上，只是不会进一步汇总到`CurrentValue`里。当某些情况发生变化后，他们可能又有可能性来通过验证，比如说之前影响最大的减速效果结束了，那么就会从还没超时的`Modifier`（如果还有的话）中挑一个减速效果最强的应用。

上述例子中的AggregatorEvaluateMetaData的使用：

```c++
virtual void OnAttributeAggregatorCreated(const FGameplayAttribute& Attribute, FAggregator* NewAggregator) const override;
```

```c++
void UGSAttributeSetBase::OnAttributeAggregatorCreated(const FGameplayAttribute& Attribute, FAggregator* NewAggregator) const
{
	Super::OnAttributeAggregatorCreated(Attribute, NewAggregator);

	if (!NewAggregator)
	{
		return;
	}

	if (Attribute == GetMoveSpeedAttribute())
	{
		NewAggregator->EvaluationMetaData = &FAggregatorEvaluateMetaDataLibrary::MostNegativeMod_AllPositiveMods;
	}
}
```

自定义的`AggregatorEvaluateMetaData`限定符应该以静态变量的形式添加到`FAggregatorEvaluateMetaDataLibrary`。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge"></a>
### 4.5 游戏效果 - Gameplay Effects

<a name="concepts-ge-definition"></a>

#### 4.5.1 游戏效果的定义 - Gameplay Effect Definition
[`GameplayEffects`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayEffect/index.html) （`GE`）是技能对自身和他者的[`Attributes`](#concepts-a)和[`GameplayTags`](#concepts-gt)产生影响的容器。具体来讲，他们可以产生一些瞬间的`Attribute`的改变效果，比如说伤害或者治疗，以及一些长期的属性buff/debuff效果，比如加速或者眩晕之类的。`UGameplayEffect`是一个定义单一游戏效果的**数据**类，这意味着`GameplayEffects`里面不应该添加任何其他的逻辑。通常设计师们只需要创建`UGameplayEffect`的派生类就够了。

`GameplayEffects`是通过[`Modifiers`](#concepts-ge-mods)和[`Executions` (`GameplayEffectExecutionCalculation`)](#concepts-ge-ec)来对`Attributes`进行修改和调整的。

`GameplayEffects`可以按生效时间分为三类：即刻生效`Instant`，持续一段时间`Duration`，以及无限持续时间`Infinite`。

此外，`GameplayEffects`也可以添加和执行[`GameplayCues`](#concepts-gc)。`Instant`类型的`GameplayEffect`将调用`GameplayCue`里的`Execute`，而`Duration`或者`Infinite`类型的`GameplayEffect`将会调用`GameplayCue`上的`Add`和`Remove`。

| 持续类型 | GameplayCue事件 | 使用时机                                                                                                                                                                                                                                |
| ------------- | ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `Instant`     | Execute           | 用于永久性的、立即的对`Attribute`的`BaseValue`的修改。`GameplayTags`将不会被应用，即便是一帧都没有。                                                                                                                    |
| `Duration`    | Add & Remove      | 用于临时的对`Attribute`的`CurrentValue`的修改并且应用`GameplayTags`，该`GameplayTags`会随着`GameplayEffect`的到期而被移除（或者自行手动删除）。具体的持续时间可以在`UGameplayEffect`类/Blueprint中进行指定。       |
| `Infinite`    | Add & Remove      | 用于临时的对`Attribute`的`CurrentValue`的修改并且应用`GameplayTags`，该`GameplayTags`会随着`GameplayEffect`被移除时一起移除。他们永远不会过期，所以必须通过技能或者`ASC`手动移除掉。 |

`Duration`和`Infinite`类型的`GameplayEffects`中可以通过一个选项来应用周期性的效果`Periodic Effects`，在它里面可以通过定义`Period`来周期性得每`X`秒就调用一次它的`Modifiers`和`Executions`。`Periodic Effects`可以作为`Instant`类型的`GameplayEffects`来对待，即它会修改`Attribute`的`BaseValue`并且执行`GameplayCues`。这对于实现DOT伤害（持续性伤害）非常有效果。**注意：** 对`Periodic Effects`无法进行[预测](#concepts-p)。

可以依据`Duration`和`Infinite`类型的`GameplayEffects`的`Ongoing Tag Requirements`选项是否符合[Gameplay Effect Tags](#concepts-ge-tags)来临时对该`GameplayEffects`进行关闭或开启。关闭一个`GameplayEffect`将会移除掉它的`Modifiers`的效果并且应用`GameplayTags`，但是并不会移除掉该`GameplayEffect`。将`GameplayEffect`再开启会再应用它的`Modifiers`和`GameplayTags`。

如果你需要手动重新计算`Duration`和`Infinite`类型的`GameplayEffects`的`Modifiers`（假设你有一个`MMC`，而其使用的数据并不是从`Attributes`里来的），你可以去调用`UAbilitySystemComponent::ActiveGameplayEffects.SetActiveGameplayEffectLevel(FActiveGameplayEffectHandle ActiveHandle, int32 NewLevel)`，其中的NewLevel参数可以通过`UAbilitySystemComponent::ActiveGameplayEffects.GetActiveGameplayEffect(ActiveHandle).Spec.GetLevel()`来得到。基于自动`Attributes`的`Modifiers`会随着`Attributes`更新而进行更新。用来更新`Modifier`的`SetActiveGameplayEffectLevel()`的内部的关键函数有：

```C++
MarkItemDirty(Effect);
Effect.Spec.CalculateModifierMagnitudes();
// Private function otherwise we'd call these three functions without needing to set the level to what it already is
UpdateAllAggregatorModMagnitudes(Effect);
```

`GameplayEffects`通常并没有实例化。当某个技能或者`ASC`想要去应用一个`GameplayEffect`时，它会利用`GameplayEffect`的`ClassDefaultObject`创建一个[`GameplayEffectSpec`](#concepts-ge-spec)。成功得应用`GameplayEffectSpecs`后它会被添加到一个`FActiveGameplayEffect`的结构体，这也就是`ASC`的`ActiveGameplayEffects`。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-applying"></a>

#### 4.5.2 应用游戏效果 - Applying Gameplay Effects
在[`GameplayAbilities`](#concepts-ga)和`ASC`里有很多函数可以用来应用某个`GameplayEffects`，这些函数名字里通常里面都会带有`ApplyGameplayEffectTo`。不同的函数其本质都是一样的，最终都会落到在`Target`上去调用其相应的`UAbilitySystemComponent::ApplyGameplayEffectSpecToSelf()`。

为了在`GameplayAbility`之外应用`GameplayEffects`，比如子弹的发射，你需要去获取`Target`的`ASC`，然后调用其`ApplyGameplayEffectToSelf`。

你可以监听`Duration`或者`Infinite`类型的`GameplayEffects`被应用到某个`ASC`上的事件，通过在相应的委托上绑定回调：
```c++
AbilitySystemComponent->OnActiveGameplayEffectAddedDelegateToSelf.AddUObject(this, &APACharacterBase::OnActiveGameplayEffectAddedCallback);
```
The callback function:
```c++
virtual void OnActiveGameplayEffectAddedCallback(UAbilitySystemComponent* Target, const FGameplayEffectSpec& SpecApplied, FActiveGameplayEffectHandle ActiveHandle);
```

无论何种复制模式下服务器都会去调用这个函数。当复制模式为`Full`和`Mixed`，自主代理会为复制的`GameplayEffects`调用此方法。只有当[replication mode](#concepts-asc-rm)为`Full`时，模拟代理才会调用这个方法。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-removing"></a>

#### 4.5.3 移除游戏效果 - Removing Gameplay Effects
在[`GameplayAbilities`](#concepts-ga)和`ASC`里有很多函数可以用来移除某个`GameplayEffects`，这些函数名字里通常里面都会带有`RemoveActiveGameplayEffect`。不同的函数其本质都是一样的，最终都会落到在`Target`上去调用其相应的`FActiveGameplayEffectsContainer::RemoveActiveEffects()`。

为了在`GameplayAbility`之外应用`GameplayEffects`，比如子弹的发射，你需要去获取`Target`的`ASC`，然后调用其`RemoveActiveGameplayEffect`。

你可以监听`Duration`或者`Infinite`类型的`GameplayEffects`被从某个`ASC`上移除的事件，通过在相应的委托上绑定回调：
```c++
AbilitySystemComponent->OnAnyGameplayEffectRemovedDelegate().AddUObject(this, &APACharacterBase::OnRemoveGameplayEffectCallback);
```
对应的回调函数：
```c++
virtual void OnRemoveGameplayEffectCallback(const FActiveGameplayEffect& EffectRemoved);
```

无论何种复制模式下服务器都会去调用这个函数。当复制模式为`Full`和`Mixed`，自主代理会为复制的`GameplayEffects`调用此方法。只有当[replication mode](#concepts-asc-rm)为`Full`时，模拟代理才会调用这个方法。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-mods"></a>
#### 4.5.4 游戏效果的修改器 - Gameplay Effect Modifiers
修改器`Modifiers`可以去修改某个`Attribute`，并且也是唯一的对`Attribute`的修改进行[预测](#concepts-p)的方式。某个`GameplayEffect`可以没有也可以有多个`Modifiers`。每个`Modifier`只能通过特定的操作对一项`Attribute`进行修改。

| 操作  | 描述                                                                                                         |
| ---------- | ------------------------------------------------------------------------------------------------------------------- |
| `Add`      | 将结果加到`Modifier`的指定的`Attribute`上，减法就是使用相应的负值即可。                    |
| `Multiply` | 将结果乘以`Modifier`的指定的`Attribute`。                                                    |
| `Divide`   | 将结果除以`Modifier`的指定的`Attribute`。                                                |
| `Override` | 将结果直接替换掉`Modifier`的指定的`Attribute`。                                                   |

`Attribute`的`CurrentValue`是将其所有的`Modifiers`添加到其`BaseValue`的一个汇总的结果。对`Modifiers`如何进行汇总的公式是在`GameplayEffectAggregator.cpp`的`FAggregatorModChannel::EvaluateWithBase`中进行定义的：

```c++
((InlineBaseValue + Additive) * Multiplicitive) / Division
```

任意的`Override`类型的`Modifiers`都将会优先使用最后应用的`Modifier`来覆盖最终的值。

**注意：**百分比式的修改要使用`Multiply`操作，以确保其是在`Add`之后执行。

**注意：**对百分比式的修改的[预测](#concepts-p)是有一些问题存在的。

共有四种类型的`Modifiers`：`Scalable Float`，`Attribute Based`，`Custom Calculation Class`，以及`Set By Caller`。他们都会生成一些浮点值，然后基于`Modifier`的操作类型利用值对`Attribute`进行修改。。

| `Modifier`类型            | 描述                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Scalable Float`           | `FScalableFloats`是一种能够指向Data Table的结构，其中Data Table是将变量作为行、将等级作为列。`Scalable Floats`将会自动根据技能的当前等级（或者是在[`GameplayEffectSpec`](#concepts-ge-spec)中重写的等级）读取指定行的值。这个值可以进一步用一个系数相乘。如果没有指定Data Table/行，该值会被当做是1，从而使用一个单独的硬编码的值作为所有等级的值。![ScalableFloat](https://github.com/tranek/GASDocumentation/raw/master/Images/scalablefloats.png)                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| `Attribute Based`          | `Attribute Based`类型的`Modifiers`会获取`Source`（`GameplayEffectSpec`的创建者）或者`Target`（`GameplayEffectSpec`的接收者）上的`Attribute`的`CurrentValue`或者`BaseValue`，然后进一步对其使用系数以及一些前/后处理来进行修改。快照`Snapshotting`意味着会在`GameplayEffectSpec`被创建时对`Attribute`进行捕捉，而`no snapshotting`则意味着在`GameplayEffectSpec`应用时来对`Attribute`进行捕捉。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| `Custom Calculation Class` | `Custom Calculation Class`为复杂`Modifiers`提供了最大的灵活性。这类`Modifier`需要一个[`ModifierMagnitudeCalculation`](#concepts-ge-mmc)类，然后可以通过系数和以及一些前/后处理来修改结果值。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `Set By Caller`            | `SetByCaller`类型的`Modifiers`由技能在运行时在`GameplayEffect`之外设置或者由`GameplayEffectSpec`的创建者进行设置。例如，如果你希望根据玩家按压按钮来对技能进行充能的时间来设置伤害值，那么你就可以使用`SetByCaller`。`SetByCallers`本质上是一个`TMap<FGameplayTag, float>`，存在于`GameplayEffectSpec`。`Modifier`只是告诉`Aggregator`去通过提供的`GameplayTag`来查找`SetByCaller`值。`Modifiers`使用的`SetByCallers`只能使用`GameplayTag`而不能使用`FName`。如果`Modifier`被设置为`SetByCaller`，但是相应`GameplayTag`的`SetByCaller`并不存在于`GameplayEffectSpec`里的话，游戏就会抛出一个运行时的错误并返回0。这样如果是`Divide`运算的话就会出问题了。参阅[`SetByCallers`](#concepts-ge-spec-setbycaller)获取更多关于如何使用`SetByCallers`的信息。 |

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-mods-multiplydivide"></a>
##### 4.5.4.1 乘法和除法类型的修改器 - Multiply and Divide Modifiers
默认情况下，所有的`Multiply`和`Divide`类型的`Modifiers`会在将他们应用`Attribute`的`BaseValue`之前先被加到一起。

```c++
float FAggregatorModChannel::EvaluateWithBase(float InlineBaseValue, const FAggregatorEvaluateParameters& Parameters) const
{
	...
	float Additive = SumMods(Mods[EGameplayModOp::Additive], GameplayEffectUtilities::GetModifierBiasByModifierOp(EGameplayModOp::Additive), Parameters);
	float Multiplicitive = SumMods(Mods[EGameplayModOp::Multiplicitive], GameplayEffectUtilities::GetModifierBiasByModifierOp(EGameplayModOp::Multiplicitive), Parameters);
	float Division = SumMods(Mods[EGameplayModOp::Division], GameplayEffectUtilities::GetModifierBiasByModifierOp(EGameplayModOp::Division), Parameters);
	...
	return ((InlineBaseValue + Additive) * Multiplicitive) / Division;
	...
}
```

```c++
float FAggregatorModChannel::SumMods(const TArray<FAggregatorMod>& InMods, float Bias, const FAggregatorEvaluateParameters& Parameters)
{
	float Sum = Bias;

	for (const FAggregatorMod& Mod : InMods)
	{
		if (Mod.Qualifies())
		{
			Sum += (Mod.EvaluatedMagnitude - Bias);
		}
	}

	return Sum;
}
```
*from `GameplayEffectAggregator.cpp`*

`Multiply`和`Divide`类型的`Modifiers`中都有一个等于1的偏差值`Bias`（`Addition`的`Bias`为`0`）。所以其公式实际上是这样的：

```
1 + (Mod1.Magnitude - 1) + (Mod2.Magnitude - 1) + ...
```

这样的公式会导致一些无法预料的结果。首先，这个公式会在将其应用到`BaseValue`之前把所有的`Modifiers`加到一起。大部分人的想法是将他们一起进行乘或者除。例如如果你有两个为`1.5`的`Multiply`，大部分人对`BaseValue`的计算方式是`1.5 x 1.5 = 2.25`。而实际上的计算是`BaseValue`乘以`2`（`BaseValue`具有50%的增长 + `BaseValue`具有50%的增长 = 100%的增长）。这也是`GameplayPrediction.h`中使用的方式，`500`的基础速度外加`10%`速度buff就得到`550`的速度。再施加另一个`10%`的速度buff则是`600`（500 + 50 x 10% + 50 x 10%）。

其次，这个公式对值如何来使用有一些没有记录在文档的规则，而在Paragon中又确确实实使用了这些规则。

`Multiply`和`Divide`运算的加法公式：
* `(小于1的值不超过1个) 和 ([1, 2)范围内的值可以有任意个)`
* `或者 (有一个值 >= 2)`

公式中的`Bias`基本上是减去`[1, 2)`范围内的整数。第一个`Modifier`的`Bias`从起始的`Sum`值（设置为循环前的`Bias`）中减去，这就是为什么任何值本身都有效，为什么一个`<1`的值与范围`[1,2)`内的数字有效的原因。

`Multiply`的相关例子：  
乘数：`0.5`  
`1 + (0.5 - 1) = 0.5`，正确

乘数：`0.5, 0.5`  
`1 + (0.5 - 1) + (0.5 - 1) = 0`，而不是预期的`1`（`0.5 + 0.5`）。小于`1`的值有多个，这时直接将乘数相加并没有意义。Paragon设计上只使用[最大的负值用作`Multiply`计算类型的`Modifiers`](#cae-nonstackingge)，因此最多只有一个小于`1`乘以`BaseValue`。

乘数：`1.1, 0.5`  
`1 + (0.5 - 1) + (1.1 - 1) = 0.6`，正确

乘数：`5, 5`  
`1 + (5 - 1) + (5 - 1) = 9`，而不是预期的`10`（`5 + 5`）。结果总是`sum of the Modifiers - number of Modifiers + 1`。

许多游戏会想要他们的`Multiply`和`Divide`类型的`Modifiers`在应用到`BaseValue`之前共同去乘以及除。为了实现这个目标，你可能需要**修改引擎源码**，具体的话在`FAggregatorModChannel::EvaluateWithBase()`。

```c++
float FAggregatorModChannel::EvaluateWithBase(float InlineBaseValue, const FAggregatorEvaluateParameters& Parameters) const
{
	...
	float Multiplicitive = MultiplyMods(Mods[EGameplayModOp::Multiplicitive], Parameters);
	...

	return ((InlineBaseValue + Additive) * Multiplicitive) / Division;
}
```

```c++
float FAggregatorModChannel::MultiplyMods(const TArray<FAggregatorMod>& InMods, const FAggregatorEvaluateParameters& Parameters)
{
	float Multiplier = 1.0f;

	for (const FAggregatorMod& Mod : InMods)
	{
		if (Mod.Qualifies())
		{
			Multiplier *= Mod.EvaluatedMagnitude;
		}
	}

	return Multiplier;
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-mods-gameplaytags"></a>
##### 4.5.4.2 修改器上的游戏标签 - Gameplay Tags on Modifiers

每个[Modifier](#concepts-ge-mods)都可以设置`SourceTags`和`TargetTags`。他们的工作原理与`GameplayEffect`的[`Application Tag requirements`](#concepts-ge-tags)是一样的。因此标签只有在效果应用时才会被考虑。即，当有一个周期性的无限持续时间的效果时，他们只在第一次效果应用时考虑而*不是*在每个执行周期都被重新考虑。

`Attribute Based`类型的`Modifiers`也可以设置`SourceTagFilter`和`TargetTagFilter`。当确定作为`Attribute Based`的`Modifier`的来源的属性的具体大小时，这些过滤器会用于排除该属性的某些`Modifier`。那些`source`或者`target`没有所有过滤器标签的`Modifiers`将会被排除在外。

更详细来讲：作为`source`和`ASC`和作为`target`的`ASC`的标签会被`GameplayEffects`捕捉。作为`source`和`ASC`的标签会在`GameplayEffectSpec`创建时被捕捉， 作为`target`的`ASC`的标签则是在效果执行时被捕捉。当确定无限持续时间的或者持续一定时间的效果的`Modifier`是否合格（即其`Aggregator`聚合器符合要求），并且设置这些过滤器时，被捕获的标签将会与过滤器进行比较。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-stacking"></a>
#### 4.5.5 堆叠游戏效果 - Stacking Gameplay Effects
默认情况下，`GameplayEffects`会无视已经存在的`GameplayEffectSpec`的实例，在应用新的`GameplayEffectSpec`时会直接创建新的实例。`GameplayEffects`也可以设置为叠加，这是就不是添加新的`GameplayEffectSpec`实例，而是修改当前已存在的`GameplayEffectSpec`的堆叠数。堆叠只能用于`Duration`和`Infinite`类型的`GameplayEffects`。

共有两种类型的堆叠：源聚合和目标聚合。

| 堆叠类型       | 描述                                                                                                                          |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| 源聚合 | 目标上每一个不同源的`ASC`都有一个自己的单独的栈实例。每个源都能够应用X数目个栈。                     |
| 目标聚合 | 无论有多少源，目标上仅有一个栈实例。每一个源能够应用栈的上限不能超过共享栈限制。 |

堆叠对于超时、持续时间刷新以及周期性重置都有相应对的处理办法。在`GameplayEffect`的蓝图里在对应条目上悬停都有相应的提示。

示例项目中包括了一个自定义的蓝图节点用来监听`GameplayEffect`栈的变化。HUD使用它来更新玩家的被动护甲叠加数。这一异步任务`AsyncTask`将会一直持续，知道手动调用`EndTask()`，这一步我们会在UMG Widget的`Destruct`事件中来做。参阅`AsyncTaskEffectStackChanged.h/cpp`。

![Listen for GameplayEffect Stack Change BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/gestackchange.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-ga"></a>
#### 4.5.6 赋予技能 - Granted Abilities
`GameplayEffects`能够为`ASCs`赋予新的[`GameplayAbilities`](#concepts-ga)。只有`Duration`和`Infinite`类型的`GameplayEffects`可以进行赋予技能的操作。

一个常见的用法就是当你想要强制玩家做某些事情，比如将他们击退或者拉近，你就可以给他们一个`GameplayEffect`来赋予他们一些自动激活的技能（参阅[Passive Abilities](#concepts-ga-activating-passive)获得更多关于如何在赋予技能后进度进行激活的内容），令他们能够做我们想让他们做的事情。。

设计师们可以选择用`GameplayEffect`设置具体赋予哪些技能，其具体的技能等级，对应的[绑定输入](#concepts-ga-input)是什么，以及赋予技能后其移除机制又是怎样的.

| 移除机制             | 描述                                                                                                                                                                     |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Cancel Ability Immediately 立即取消技能 | 当`GameplayEffect`被从目标上移除时，立即取消并移除相应被赋予的技能。                                                   |
| Remove Ability on End 结束后移除技能      | 被赋予的技能可以自然执行直到结束，然后从目标上移除。                                                                                                   |
| Do Nothing 什么都不做                 | 从目标上移除`GameplayEffect`并不会影响相应被赋予的技能。目标可以一直拥有对应的能力直到手动移除。 |

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-tags"></a>
#### 4.5.7 游戏效果标签 - Gameplay Effect Tags
`GameplayEffects`带有多个[`GameplayTagContainers`](#concepts-gt)。设计师可以为每个类别编辑相应的`Added`和`Removed`的`GameplayTagContainers`，对应的结果会在编译后展现在`Combined`的 `GameplayTagContainer`中。 `Added`的标签是指这个`GameplayEffect`新添加的父类之前所没有的标签。`Removed`的标签则是父类拥有而子类没有的。

| 种类                          | 描述                                                                                                                                                                                                                                                                                                                                                                        |
| --------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Gameplay Effect Asset Tags        | `GameplayEffect`所具有的标签。他们本身并不执行任何函数，仅用于描述`GameplayEffect`.                                                                                                                                                                                                                                        |
| Granted Tags                      | 存在于`GameplayEffect`上的标签，但也会给到`GameplayEffect`应用到的目标的`ASC`上。当`GameplayEffect`移除时他们也会被一并从`ASC`上移除。仅用于`Duration`和`Infinite`类型的`GameplayEffects`。                                                                                                                              |
| Ongoing Tag Requirements          | 一旦应用，这些标签将决定`GameplayEffect`是开还是关。`GameplayEffect`可以被应用时仍然时关闭状态的。如果`GameplayEffect`没有满足Ongoing Tag Requirements的就会被关闭，当条件满足时，它又会被再次打开并重新应用它的修改器。仅用于`Duration`和`Infinite`类型的`GameplayEffects。 |
| Application Tag Requirements      | 指那些在目标上的标签，它们会决定`GameplayEffect`是否可以被应用到目标上。如果相应的要求没有满足，那么`GameplayEffect`则不会应用。                                                                                                                                                                                                                      |
| Remove Gameplay Effects with Tags | 目标上的`GameplayEffects`如果在`Asset Tags`或`Granted Tags`里有任何这种类型的标签的话，将会被从目标上移除。                                                                                                                                                                                            |

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-immunity"></a>
#### 4.5.8 免疫效果 - Immunity
`GameplayEffects`能够赋予免疫的能力，即基于[`GameplayTags`](#concepts-gt)高效得阻止其他`GameplayEffects`的应用。虽然免疫的效果也可以通过其他方式实现，比如前面提到的`Application Tag Requirements`，但是这里介绍的方法会提供一个委托`UAbilitySystemComponent::OnImmunityBlockGameplayEffectDelegate`，从而监听`GameplayEffects`被免疫掉的这一事件。

`GrantedApplicationImmunityTags`会检查源`ASC`（包括源上技能的`AbilityTags`中的那些标签）是否有某些特殊的标签。这是方式是基于标签通过某些角色或者源上的`GameplayEffects`来提供免疫的效果。

`Granted Application Immunity Query`会检查`GameplayEffectSpec`判断其是否匹配从而决定是阻止还是放行。

在`GameplayEffect`的蓝图里悬停到`Queries`上查看更多相应的提示。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-spec"></a>
#### 4.5.9 Gameplay Effect Spec
The [`GameplayEffectSpec`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FGameplayEffectSpec/index.html) (`GESpec`)可以认为是`GameplayEffects`的实例化。他会保存一个指向他们所表示的`GameplayEffect`类的引用，创建它时给定的等级，以及是谁创建了它。这些内容都可以在应用前运行时进行自由得创建和修改，这一点和`GameplayEffects`不一样（`GameplayEffects`需要在允许之前由设计师先行创建配置好。在应用一个`GameplayEffect`时，会先从`GameplayEffect`中创建一个`GameplayEffectSpec`出来，然后实际上是把`GameplayEffectSpec`应用给目标。

从`GameplayEffects`创建`GameplayEffectSpecs`会用到`UAbilitySystemComponent::MakeOutgoingSpec()`（`BlueprintCallable`）。`GameplayEffectSpecs`不是必须立即应用。通常是将`GameplayEffectSpec`传递给由技能创建的子弹，然后当子弹击中目标时将具体的技能效果应用给目标。当`GameplayEffectSpecs`成功被应用后，它会返回一个新的结构体`FActiveGameplayEffect`。

`GameplayEffectSpec`中比较重要的内容有：
* `GameplayEffectSpec`创建所依据的`GameplayEffect`类。
* `GameplayEffectSpec`的等级。通常和创建这个`GameplayEffectSpec`的技能的等级相同，当然也可以不同。
* `GameplayEffectSpec`的持续时间。默认是`GameplayEffect`的持续时间，当然也可以不同。
* 当`GameplayEffectSpec`用于周期效果时，其周期。默认是`GameplayEffect`的周期，当然也可以不同。
* `GameplayEffectSpec`的当前的堆叠数量。具体的堆叠限制在`GameplayEffect`上。
* [`GameplayEffectContextHandle`](#concepts-ge-context)告诉我们是由谁创建的这个`GameplayEffectSpec`。
* `GameplayEffectSpec`创建时所对`Attributes`进行的快照。
* `GameplayEffectSpec`赋予目标的`DynamicGrantedTags`，这个是在`GameplayEffect`赋予的`GameplayTags`之外的部分。
* `GameplayEffectSpec`赋予目标的`DynamicAssetTags`，这个是在`GameplayEffect`赋予的`AssetTags`之外的部分。
* `SetByCaller``TMaps`.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-spec-setbycaller"></a>
##### 4.5.9.1 SetByCallers
`SetByCallers`允许`GameplayEffectSpec`去带一个和`GameplayTag`或`FName`关联的浮点值。他们分别存储在各自对应的`TMaps`里：在`GameplayEffectSpec`上的`TMap<FGameplayTag, float>`和`TMap<FName, float>`里。他们可以像是`GameplayEffect`上的`Modifiers`那样来用或者更加普遍的就是运送浮点值。常常会利用`SetByCallers`技能中生成的数值传递给[`GameplayEffectExecutionCalculations`](#concepts-ge-ec)或者[`ModifierMagnitudeCalculations`](#concepts-ge-mmc)。

| `SetByCaller`的使用 | 注意                                                                                                                                                                                                                                                                                                                                                                                                                               |
| ----------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Modifiers`       | 必须在`GameplayEffect`类中提前定义。只能使用`GameplayTag`（译者注：对应着不能使用FName）。如果在`GameplayEffect`类里定义了，但是`GameplayEffectSpec`并没有对应的标签和浮点值对，游戏将会报一个运行时的错误并返回0。`Divide`运算可能会有一些潜在的问题。参阅[`Modifiers`](#concepts-ge-mods)。 |
| 其他地方         | 不需要提前进行定义。去读取`GameplayEffectSpec`上不存在的`SetByCaller`会返回一个开发者定义的默认值以及可以配置的警告内容。                                                                                                                                                                                                                                      |

要在蓝图中使用`SetByCaller`的值，可以使用相应的蓝图节点（`GameplayTag`或者是`FName`）：

![Assigning SetByCaller](https://github.com/tranek/GASDocumentation/raw/master/Images/setbycaller.png)

要在蓝图中读取`SetByCaller`的值，你需要在`Blueprint Library`中实现自定义的节点。

要在C++中设置`SetByCaller`的值，可以使用相应的函数（`GameplayTag`或者是`FName`）：

```c++
void FGameplayEffectSpec::SetSetByCallerMagnitude(FName DataName, float Magnitude);
```
```c++
void FGameplayEffectSpec::SetSetByCallerMagnitude(FGameplayTag DataTag, float Magnitude);
```

要在C++中读取`SetByCaller`的值，可以使用相应的函数（`GameplayTag`或者是`FName`）：

```c++
float GetSetByCallerMagnitude(FName DataName, bool WarnIfNotFound = true, float DefaultIfNotFound = 0.f) const;
```
```c++
float GetSetByCallerMagnitude(FGameplayTag DataTag, bool WarnIfNotFound = true, float DefaultIfNotFound = 0.f) const;
```

我建议是选用`GameplayTag`版本的不管函数也好蓝图节点也好，而不是`FName`版本。这可以在蓝图中防止我们出现拼写错误，另外一个点是`GameplayEffectSpec`在进行网络复制时，`GameplayTags`是比`FNames`更加高效的。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-context"></a>
#### 4.5.10 游戏效果的上下文 - Gameplay Effect Context
[`GameplayEffectContext`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FGameplayEffectContext/index.html)这个结构体中保存了`GameplayEffectSpec`的发起者和[`TargetData`](#concepts-targeting-data)的一些信息。这个结构也可以稍作拓展用来在[`ModifierMagnitudeCalculations`](#concepts-ge-mmc) / [`GameplayEffectExecutionCalculations`](#concepts-ge-ec)，[`AttributeSets`](#concepts-as)以及[`GameplayCues`](#concepts-gc)之间传递数据。

派生`GameplayEffectContext`的过程：

1. 实现`FGameplayEffectContext`的派生结构
2. 重写`FGameplayEffectContext::GetScriptStruct()`
3. 重写`FGameplayEffectContext::Duplicate()`
4. 重写`FGameplayEffectContext::NetSerialize()`，如果你有一些新的数据需要复制的话
5. 仿照父类结构`FGameplayEffectContext`实现派生类中的`TStructOpsTypeTraits`
6. 在你的[`AbilitySystemGlobals`](#concepts-asg)类中重写`AllocGameplayEffectContext()`来返回你所建的派生类对象。

[GASShooter](https://github.com/tranek/GASShooter)项目中使用了`GameplayEffectContext`的派生类来添加`TargetData`，从而可以在`GameplayCues`中对其进行访问，这一点特别为霰弹枪设计，因为它可以击中不止一个目标。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-mmc"></a>
#### 4.5.11 修改器的幅值计算 - Modifier Magnitude Calculation
[`ModifierMagnitudeCalculations`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayModMagnitudeCalculation/index.html)（简称`ModMagCalc`或`MMC`）是一个功能非常强大的类，使用起来就像是`GameplayEffects`中的[`Modifiers`](#concepts-ge-mods)。他们的功能类似于[`GameplayEffectExecutionCalculations`](#concepts-ge-ec)，甚至于没有`GameplayEffectExecutionCalculations`那么功能繁多，但是最重要的是，他们是可以被[预测](#concepts-p)的。`MMC`唯一的目的就是通过`CalculateBaseMagnitude_Implementation()`返回一个浮点值。你可以通过蓝图或者C++派生以及重写这个方法。

`MMC`可以用于任何持续时间的`GameplayEffects` - `Instant`，`Duration`，`Infinite`亦或是`Periodic`。

`MMC`的优势在于捕捉`GameplayEffect`的`Source`或者`Target`上的任意数量的`Attributes`的值的能力并且能够完整得访问`GameplayEffectSpec`以读取`GameplayTags`以及`SetByCallers`。`Attributes`可以是快照也可以不是。快照的`Attributes`是在`GameplayEffectSpec`被创建时进行捕捉的，而非快照的`Attributes`则是在`GameplayEffectSpec`应用时进行捕捉的，并且会根据`Infinite`和`Duration`类型的`GameplayEffects`对`Attribute`进行修改而进行更新。通过已经存在于`ASC`的修改捕捉`Attributes`然后重新计算他们的`CurrentValue`。这里的重计算**不会**运行`AbilitySet`上的[`PreAttributeChange()`](#concepts-as-preattributechange)，所以之前提到的那些对数值的预处理操作（截取）必须在这里再做一遍。

| 快照 | Source 或是 Target | 在`GameplayEffectSpec`上被捕捉的时机 | 当`Attribute`被`Infinite`或`Duration`类型的`GE`修改时自动更新 |
| -------- | ---------------- | -------------------------------- | -------------------------------------------------------------------------------- |
| Yes      | Source           | Creation                         | No                                                                               |
| Yes      | Target           | Application                      | No                                                                               |
| No       | Source           | Application                      | Yes                                                                              |
| No       | Target           | Application                      | Yes                                                                              |

`MMC`的结果浮点值可以进一步在`GameplayEffect`的`Modifier`通过系数、预系数加法或后系数加法等方式进行修改。

下面是一个`MMC`的示例，会捕捉`Target`的魔法值`Attribute`从而用一个中毒效果来对其进行减少，其中减少的数量会依据`Target`拥有的魔法值和`Target`可能拥有的标签来决定：
```c++
UPAMMC_PoisonMana::UPAMMC_PoisonMana()
{

	//ManaDef defined in header FGameplayEffectAttributeCaptureDefinition ManaDef;
	ManaDef.AttributeToCapture = UPAAttributeSetBase::GetManaAttribute();
	ManaDef.AttributeSource = EGameplayEffectAttributeCaptureSource::Target;
	ManaDef.bSnapshot = false;

	//MaxManaDef defined in header FGameplayEffectAttributeCaptureDefinition MaxManaDef;
	MaxManaDef.AttributeToCapture = UPAAttributeSetBase::GetMaxManaAttribute();
	MaxManaDef.AttributeSource = EGameplayEffectAttributeCaptureSource::Target;
	MaxManaDef.bSnapshot = false;

	RelevantAttributesToCapture.Add(ManaDef);
	RelevantAttributesToCapture.Add(MaxManaDef);
}

float UPAMMC_PoisonMana::CalculateBaseMagnitude_Implementation(const FGameplayEffectSpec & Spec) const
{
	// Gather the tags from the source and target as that can affect which buffs should be used
	const FGameplayTagContainer* SourceTags = Spec.CapturedSourceTags.GetAggregatedTags();
	const FGameplayTagContainer* TargetTags = Spec.CapturedTargetTags.GetAggregatedTags();

	FAggregatorEvaluateParameters EvaluationParameters;
	EvaluationParameters.SourceTags = SourceTags;
	EvaluationParameters.TargetTags = TargetTags;

	float Mana = 0.f;
	GetCapturedAttributeMagnitude(ManaDef, Spec, EvaluationParameters, Mana);
	Mana = FMath::Max<float>(Mana, 0.0f);

	float MaxMana = 0.f;
	GetCapturedAttributeMagnitude(MaxManaDef, Spec, EvaluationParameters, MaxMana);
	MaxMana = FMath::Max<float>(MaxMana, 1.0f); // Avoid divide by zero

	float Reduction = -20.0f;
	if (Mana / MaxMana > 0.5f)
	{
		// Double the effect if the target has more than half their mana
		Reduction *= 2;
	}

	if (TargetTags->HasTagExact(FGameplayTag::RequestGameplayTag(FName("Status.WeakToPoisonMana"))))
	{
		// Double the effect if the target is weak to PoisonMana
		Reduction *= 2;
	}

	return Reduction;
}
```

如果你没有在`MMC`的构建方法中将`FGameplayEffectAttributeCaptureDefinition`添加到`RelevantAttributesToCapture`中，在尝试去捕捉`Attributes`时你会在得到一个missing Spec相关的错误信息。如果你不需要捕捉`Attributes`，那么就不需要上面提到的那一步操作。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-ec"></a>
#### 4.5.12 游戏效果执行的计算 - Gameplay Effect Execution Calculation
[`GameplayEffectExecutionCalculations`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayEffectExecutionCalculat-/index.html) （`ExecutionCalculation`，`Execution`（在插件源码中你会经常看到这个的术语），亦或是 `ExecCalc`），是`GameplayEffects`修改`ASC`的最强有力的一种方式。与[`ModifierMagnitudeCalculations`](#concepts-ge-mmc)类似，`GameplayEffectExecutionCalculations`可以捕捉`Attributes`并且可以对属性们进行快照。而与`MMCs`不同的是，他们可以改版不止一个`Attribute`，并且高效得执行编程者想要的任何事。当然强大和灵活也伴随着代价，`GameplayEffectExecutionCalculations`的代价就是其不支持[预测](#concepts-p)，并且他们也必须在C++中进行实现。

`ExecutionCalculations`只能搭配`Instant`和`Periodic`类型的`GameplayEffects`来使用。通常任何带有'Execute'一词的内容基本都是指向这两种类型的`GameplayEffects`。

快照会在创建`GameplayEffectSpec`时捕捉`Attribute`，而非快照会在`GameplayEffectSpec`应用时对`Attribute`进行捕捉。捕捉`Attributes`会去根据`ASC`上存在的修改器而重新计算他们的`CurrentValue`。这个重计算**不会**执行`AbilitySet`里的[`PreAttributeChange()`](#concepts-as-preattributechange) ，所以这里需要再做一次数值的处理（截取）。

| Snapshot | Source or Target | Captured on `GameplayEffectSpec` |
| -------- | ---------------- | -------------------------------- |
| Yes      | Source           | Creation                         |
| Yes      | Target           | Application                      |
| No       | Source           | Application                      |
| No       | Target           | Application                      |

为了配置`Attribute`的获取，我们可以参考Epic的ActionRPG示例项目中设置好的模板，具体就是定义了一个结构体来保存并且定义我们是如何捕捉`Attributes`，并且再结构体的构造函数中创建一份它的拷贝。对每个`ExecCalc`你都要有一个类似这样的结构体。**注意：**每个结构体的名称不应该重复，因为他们是在同一命名空间之下的。使用重名结构体会导致在捕捉`Attributes`时出现不正确的行为（没有捕捉到预想的那个`Attributes`的值）。

对于`Local Predicted`，`Server Only`以及`Server Initiated`的[`GameplayAbilities`](#concepts-ga)，`ExecCalc`仅在服务器上进行调用。

`ExecCalc`最常见的应用案例就是基于一个复杂的公式，从`Source`和`Target`上读取多个`Attributes`的值，然后计算出伤害。示例项目中有一个简单的`ExecCalc`，从`GameplayEffectSpec`的[`SetByCaller`](#concepts-ge-spec-setbycaller)中读取伤害值，在通过在`Target`上捕获到的护甲`Attribute`，计算出最终的受到削减过后的伤害值。参阅`GDDamageExecCalculation.cpp/.h`。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-ec-senddata"></a>
##### 4.5.12.1 发送数据到Execution Calculations
除了捕捉`Attributes`之外，还有一些其他的方式去发送数据到`ExecutionCalculation`中。

<a name="concepts-ge-ec-senddata-setbycaller"></a>
###### 4.5.12.1.1 SetByCaller
任何[在`GameplayEffectSpec`上设置的`SetByCallers`](#concepts-ge-spec-setbycaller)都能够直接在`ExecutionCalculation`里被读取。

```c++
const FGameplayEffectSpec& Spec = ExecutionParams.GetOwningSpec();
float Damage = FMath::Max<float>(Spec.GetSetByCallerMagnitude(FGameplayTag::RequestGameplayTag(FName("Data.Damage")), false, -1.0f), 0.0f);
```

<a name="concepts-ge-ec-senddata-backingdataattribute"></a>
###### 4.5.12.1.2 后备数据的属性计算修改器 - Backing Data Attribute Calculation Modifier
如果你想要将值硬编码到`GameplayEffect`中，你可以使用一个`CalculationModifier`将他们传入，这个`CalculationModifier`会使用捕获到的其中一个`Attributes`作为后备数据。

在截图所示的例子中，我们添加了50到捕获的伤害`Attribute`上。你也可以设置其为`Override`，以直接用硬编码的值进行覆盖。

![Backing Data Attribute Calculation Modifier](https://github.com/tranek/GASDocumentation/raw/master/Images/calculationmodifierbackingdataattribute.png)

`ExecutionCalculation`会在捕捉`Attribute`时对值进行读取。

```c++
float Damage = 0.0f;
// Capture optional damage value set on the damage GE as a CalculationModifier under the ExecutionCalculation
ExecutionParams.AttemptCalculateCapturedAttributeMagnitude(DamageStatics().DamageDef, EvaluationParameters, Damage);
```

<a name="concepts-ge-ec-senddata-backingdatatempvariable"></a>
###### 4.5.12.1.3 后备数据临时变量计算修改器 - Backing Data Temporary Variable Calculation Modifier
如果你想要将值硬编码到`GameplayEffect`中，你可以使用一个`CalculationModifier`将他们传入，这个`CalculationModifier`会使用一个`Temporary Variable`或者`Transient Aggregator`，就像在C++中调用的那样。`Temporary Variable`是和`GameplayTag`相关联的。

在截屏所示的例子中，我们使用`Data.Damage`的`GameplayTag`向`Temporary Variable`添加了50。

![Backing Data Temporary Variable Calculation Modifier](https://github.com/tranek/GASDocumentation/raw/master/Images/calculationmodifierbackingdatatempvariable.png)

添加后备的`Temporary Variables`到`ExecutionCalculation`的构造函数中：

```c++
ValidTransientAggregatorIdentifiers.AddTag(FGameplayTag::RequestGameplayTag("Data.Damage"));
```

`ExecutionCalculation`使用特殊的捕捉函数（类似`Attribute`的捕捉函数）来读取这个值。

```c++
float Damage = 0.0f;
ExecutionParams.AttemptCalculateTransientAggregatorMagnitude(FGameplayTag::RequestGameplayTag("Data.Damage"), EvaluationParameters, Damage);
```

<a name="concepts-ge-ec-senddata-effectcontext"></a>
###### 4.5.12.1.4 游戏效果上下文 - Gameplay Effect Context
你可以通过一个自定义的[`GameplayEffectSpec`上的`GameplayEffectContext`](#concepts-ge-context)发送数据到`ExecutionCalculation`。

在`ExecutionCalculation`中你可以从`FGameplayEffectCustomExecutionParameters`访问`EffectContext`。

```c++
const FGameplayEffectSpec& Spec = ExecutionParams.GetOwningSpec();
FGSGameplayEffectContext* ContextHandle = static_cast<FGSGameplayEffectContext*>(Spec.GetContext().Get());
```

如果你需要修改`GameplayEffectSpec`或者`EffectContext`上的什么东西的话：

```c++
FGameplayEffectSpec* MutableSpec = ExecutionParams.GetOwningSpecForPreExecuteMod();
FGSGameplayEffectContext* ContextHandle = static_cast<FGSGameplayEffectContext*>(MutableSpec->GetContext().Get());
```

如果修改`ExecutionCalculation`中的`GameplayEffectSpec`的话，请一定小心。参阅`GetOwningSpecForPreExecuteMod()`的注释部分。

```c++
/** Non const access. Be careful with this, especially when modifying a spec after attribute capture. */
FGameplayEffectSpec* GetOwningSpecForPreExecuteMod() const;
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-car"></a>
#### 4.5.13 自定义应用的要求 - Custom Application Requirement
[`CustomApplicationRequirement`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayEffectCustomApplication-/index.html)（`CAR`）类为设计者提供了是否去应用某个的`GameplayEffect`的高级控制，这和简单的在`GameplayEffect`上进行`GameplayTag`检查是不同的。这是可以通过在蓝图中重写`CanApplyGameplayEffect()`函数，或者在C++中重写`CanApplyGameplayEffect_Implementation()`来实现的。

使用`CARs`的情形可以有:
* `Target`需要有一定数量的`Attribute`
* `Target`需要`GameplayEffect`堆叠到一定数目

`CARs`也可以实现更高级的事，比如检查某个`GameplayEffect`的实例是否已经应用到`Target`上，并且在已有其他的同类实例存在的情况下不去做实例的替换而是[改变已有实例的持续时间](#concepts-ge-duration)（`CanApplyGameplayEffect()`返回false）。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-cost"></a>
#### 4.5.14 消耗的游戏效果 - Cost Gameplay Effect
[`GameplayAbilities`](#concepts-ga)中有一种`GameplayEffect`专门设计用来处理技能的消耗。`Costs`就是`ASC`激活某个`GameplayAbility`所需要的某个`Attribute`的多少。如果某个`GA`无法负担对应的`Cost GE`，那么它就无法被激活使用。这个`Cost GE`需要是一个`Instant`类型的`GameplayEffect`，具备一个或者多个`Modifiers`，用于对`Attributes`进行消耗。默认情况下，`Cost GEs`是支持预测的，建议是不要使用`ExecutionCalculations`（译者注：上面提到过，`ExecutionCalculations`不支持预测）。所以最好是只使用`MMCs`来进行对应的消耗计算。

刚开始时，你可能会为每个有消耗的`GA`来配备一个单独的`Cost GE`。更高级一点的做法是为多个`GAs`重用一个`Cost GE`，只要根据`GA`的指定数据修改从`Cost GE`创建的`GameplayEffectSpec`（消耗值一般定义在`GA`上）。**这只能用于`Instanced`的技能。**

两种重用`Cost GE`的技术:

1. **使用`MMC`。**这是最简单的方法。创建一个[`MMC`](#concepts-ge-mmc)，从`GameplayAbility`示例中读取消耗值（具体是从`GameplayEffectSpec`得到）。

```c++
float UPGMMC_HeroAbilityCost::CalculateBaseMagnitude_Implementation(const FGameplayEffectSpec & Spec) const
{
	const UPGGameplayAbility* Ability = Cast<UPGGameplayAbility>(Spec.GetContext().GetAbilityInstance_NotReplicated());

	if (!Ability)
	{
		return 0.0f;
	}

	return Ability->Cost.GetValueAtLevel(Ability->GetAbilityLevel());
}
```

本例中，消耗值是我添加到`GameplayAbility`子类上的`FScalableFloat`类型。
```c++
UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cost")
FScalableFloat Cost;
```

![Cost GE With MMC](https://github.com/tranek/GASDocumentation/raw/master/Images/costmmc.png)

2. **重写`UGameplayAbility::GetCostGameplayEffect()`。**重写该函数，并且[在运行时创建`GameplayEffect`](#concepts-ge-dynamic)，从而读取`GameplayAbility`的消耗值。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-cooldown"></a>
#### 4.5.15 冷却的游戏效果 - Cooldown Gameplay Effect
[`GameplayAbilities`](#concepts-ga)中可以有一种专门设计用来处理技能的冷却的`GameplayEffect`。冷却指的就是某个技能被施放后直到可以再次施放所需的时间。如果某个`GA`仍然出于冷却过程中的话，即意味着它无法被激活。这个`Cooldown GE`应是一个`Duration`类型的`GameplayEffect`，无`Modifiers`，并且在`GameplayEffect`的`GrantedTags`（`Cooldown Tag`）中配置代表每个`GameplayAbility`或每个技能插槽（如果你的游戏具有分配给共享冷却时间的插槽的可互换技能）的唯一的一个`GameplayTag`。实际上`GA`会检查`Cooldown Tag`是否存在，而不是`Cooldown GE`的存在。默认情况下，`Cooldown GEs`是支持预测的，故而在冷却计算时最好不去使用`ExecutionCalculations`（译者注：上面提到过，`ExecutionCalculations`不支持预测）。所以最好是只使用`MMCs`来进行对应的冷却计算。

刚开始时，你可能会为每个拥有冷却的`GA`来配备一个单独的`Cooldown GE`。更高级一点的做法是为多个`GAs`重用一个`Cooldown GE`，只要根据`GA`的指定数据修改从`Cooldown GE`创建的`GameplayEffectSpec`（冷却的持续时间和`Cooldown Tag`是定义在`GA`上）。**这只能用于`Instanced`的技能。**

两种重用`Cooldown GE`的技术:

1. **使用[`SetByCaller`](#concepts-ge-spec-setbycaller)。**这是最简单快捷的方法。通过带有`GameplayTag`的`SetByCaller`设置`Cooldown GE`的持续时间。可以在你的`GameplayAbility`的子类中，定义一个float/`FScalableFloat`作为持续时间，定义一个`FGameplayTagContainer`作为唯一的`Cooldown Tag`，定义一个临时`FGameplayTagContainer`用来返回`Cooldown Tag`和`Cooldown GE`的标签集合。
```c++
UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FScalableFloat CooldownDuration;

UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FGameplayTagContainer CooldownTags;

// Temp container that we will return the pointer to in GetCooldownTags().
// This will be a union of our CooldownTags and the Cooldown GE's cooldown tags.
UPROPERTY(Transient)
FGameplayTagContainer TempCooldownTags;
```

然后重写`UGameplayAbility::GetCooldownTags()`，返回`Cooldown Tags`和`Cooldown GE`标签。
```c++
const FGameplayTagContainer * UPGGameplayAbility::GetCooldownTags() const
{
	FGameplayTagContainer* MutableTags = const_cast<FGameplayTagContainer*>(&TempCooldownTags);
	MutableTags->Reset(); // MutableTags writes to the TempCooldownTags on the CDO so clear it in case the ability cooldown tags change (moved to a different slot)
	const FGameplayTagContainer* ParentTags = Super::GetCooldownTags();
	if (ParentTags)
	{
		MutableTags->AppendTags(*ParentTags);
	}
	MutableTags->AppendTags(CooldownTags);
	return MutableTags;
}
```

最后，重写`UGameplayAbility::ApplyCooldown()`注入`Cooldown Tags`并且添加`SetByCaller`到冷却的`GameplayEffectSpec`。
```c++
void UPGGameplayAbility::ApplyCooldown(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo * ActorInfo, const FGameplayAbilityActivationInfo ActivationInfo) const
{
	UGameplayEffect* CooldownGE = GetCooldownGameplayEffect();
	if (CooldownGE)
	{
		FGameplayEffectSpecHandle SpecHandle = MakeOutgoingGameplayEffectSpec(CooldownGE->GetClass(), GetAbilityLevel());
		SpecHandle.Data.Get()->DynamicGrantedTags.AppendTags(CooldownTags);
		SpecHandle.Data.Get()->SetSetByCallerMagnitude(FGameplayTag::RequestGameplayTag(FName(  OurSetByCallerTag  )), CooldownDuration.GetValueAtLevel(GetAbilityLevel()));
		ApplyGameplayEffectSpecToOwner(Handle, ActorInfo, ActivationInfo, SpecHandle);
	}
}
```

在下图中，冷却持续时间的`Modifier`是由`SetByCaller`通过一个`Data.Cooldown`的`Data Tag`来进行设置。`Data.Cooldown`即是上面代码中的`OurSetByCallerTag`。

![Cooldown GE with SetByCaller](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownsbc.png)

2. **使用[`MMC`](#concepts-ge-mmc)。**这基本上与上面的设置类似，除了在`ApplyCooldown`中设置`SetByCaller`作为`Cooldown GE`上的冷却持续时间，相对的，而是设置`Custom Calculation Class`并且指向我们创建的新的`MMC`。
```c++
UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FScalableFloat CooldownDuration;

UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FGameplayTagContainer CooldownTags;

// Temp container that we will return the pointer to in GetCooldownTags().
// This will be a union of our CooldownTags and the Cooldown GE's cooldown tags.
UPROPERTY(Transient)
FGameplayTagContainer TempCooldownTags;
```

然后重写`UGameplayAbility::GetCooldownTags()`，返回`Cooldown Tags`和`Cooldown GE`标签。
```c++
const FGameplayTagContainer * UPGGameplayAbility::GetCooldownTags() const
{
	FGameplayTagContainer* MutableTags = const_cast<FGameplayTagContainer*>(&TempCooldownTags);
	MutableTags->Reset(); // MutableTags writes to the TempCooldownTags on the CDO so clear it in case the ability cooldown tags change (moved to a different slot)
	const FGameplayTagContainer* ParentTags = Super::GetCooldownTags();
	if (ParentTags)
	{
		MutableTags->AppendTags(*ParentTags);
	}
	MutableTags->AppendTags(CooldownTags);
	return MutableTags;
}
```

最后，重写`UGameplayAbility::ApplyCooldown()`注入`Cooldown Tags`并且添加`SetByCaller`到冷却的`GameplayEffectSpec`。
```c++
void UPGGameplayAbility::ApplyCooldown(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo * ActorInfo, const FGameplayAbilityActivationInfo ActivationInfo) const
{
	UGameplayEffect* CooldownGE = GetCooldownGameplayEffect();
	if (CooldownGE)
	{
		FGameplayEffectSpecHandle SpecHandle = MakeOutgoingGameplayEffectSpec(CooldownGE->GetClass(), GetAbilityLevel());
		SpecHandle.Data.Get()->DynamicGrantedTags.AppendTags(CooldownTags);
		ApplyGameplayEffectSpecToOwner(Handle, ActorInfo, ActivationInfo, SpecHandle);
	}
}
```

```c++
float UPGMMC_HeroAbilityCooldown::CalculateBaseMagnitude_Implementation(const FGameplayEffectSpec & Spec) const
{
	const UPGGameplayAbility* Ability = Cast<UPGGameplayAbility>(Spec.GetContext().GetAbilityInstance_NotReplicated());

	if (!Ability)
	{
		return 0.0f;
	}

	return Ability->CooldownDuration.GetValueAtLevel(Ability->GetAbilityLevel());
}
```

![Cooldown GE with MMC](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownmmc.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-cooldown-tr"></a>
##### 4.5.15.1 获取冷却的游戏效果的剩余时间 - Get the Cooldown Gameplay Effect's Remaining Time
```c++
bool APGPlayerState::GetCooldownRemainingForTag(FGameplayTagContainer CooldownTags, float & TimeRemaining, float & CooldownDuration)
{
	if (AbilitySystemComponent && CooldownTags.Num() > 0)
	{
		TimeRemaining = 0.f;
		CooldownDuration = 0.f;

		FGameplayEffectQuery const Query = FGameplayEffectQuery::MakeQuery_MatchAnyOwningTags(CooldownTags);
		TArray< TPair<float, float> > DurationAndTimeRemaining = AbilitySystemComponent->GetActiveEffectsTimeRemainingAndDuration(Query);
		if (DurationAndTimeRemaining.Num() > 0)
		{
			int32 BestIdx = 0;
			float LongestTime = DurationAndTimeRemaining[0].Key;
			for (int32 Idx = 1; Idx < DurationAndTimeRemaining.Num(); ++Idx)
			{
				if (DurationAndTimeRemaining[Idx].Key > LongestTime)
				{
					LongestTime = DurationAndTimeRemaining[Idx].Key;
					BestIdx = Idx;
				}
			}

			TimeRemaining = DurationAndTimeRemaining[BestIdx].Key;
			CooldownDuration = DurationAndTimeRemaining[BestIdx].Value;

			return true;
		}
	}

	return false;
}
```

**注意：**在客户端上查询剩余冷却时间是需要客户端能够接收到复制的`GameplayEffects`，这也依赖于他们`ASC`的[replication mode](#concepts-asc-rm)。

<a name="concepts-ge-cooldown-listen"></a>
##### 4.5.15.2 监听冷却的开始和结束 - Listening for Cooldown Begin and End
要监听冷却的开始，你既可以绑定`AbilitySystemComponent->OnActiveGameplayEffectAddedDelegateToSelf`，从而响应`Cooldown GE`的应用；或者，可以绑定`AbilitySystemComponent->RegisterGameplayTagEvent(CooldownTag, EGameplayTagEventType::NewOrRemoved)`，从而响应`Cooldown Tag`的新增。我的建议是使用前者，因为此时你也可以访问应用到其上的`GameplayEffectSpec`。这样，你可以判断`Cooldown GE`是本地预测的那个还是服务器矫正过的那个。

要监听冷却的结束，你既可以绑定`AbilitySystemComponent->OnAnyGameplayEffectRemovedDelegate()`，从而响应`Cooldown GE`的移除；或者，可以绑定`AbilitySystemComponent->RegisterGameplayTagEvent(CooldownTag, EGameplayTagEventType::NewOrRemoved)`，从而响应`Cooldown Tag`的移除。我的建议是使用后者，因为当服务器矫正的`Cooldown GE`到达时，它会移除我们本地预测的那个，从而触发`OnAnyGameplayEffectRemovedDelegate()`，即便我们此时仍然出于冷却过程中。在预测的`Cooldown GE`的移除过程和服务器矫正的`Cooldown GE`的应用过程中，`Cooldown Tag`也不会发生变化。

**注意：**监听`GameplayEffect`在客户端上的添加和移除要求，客户端们可以接收复制的`GameplayEffects`。这也依赖于他们`ASC`的[replication mode](#concepts-asc-rm)。

示例项目中包含了一个自定义的蓝图节点，用来监听冷却的开始和结束。HUD UMG Widget使用它依照Meteor技能的冷却来更新剩余时间。这个`AsyncTask`会一直持续，直到手动调用`EndTask()`，这具体也是在UMG Widget的`Destruct`时间中调用的。参阅[`AsyncTaskCooldownChanged.h/cpp`](Source/GASDocumentation/Private/Characters/Abilities/AsyncTaskCooldownChanged.cpp)。

![Listen for Cooldown Change BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownchange.png)

<a name="concepts-ge-cooldown-prediction"></a>
##### 4.5.15.3 冷却效果的预测 - Predicting Cooldowns
目前冷却并不能够真正得被预测。我们可以在本地预测的`Cooldown GE`应用时开始UI上的冷却计数器，但是`GameplayAbility`的实际冷却却与服务器的剩余冷却时间挂钩。由于玩家可能会存在延迟，本地的预测冷却可能已经结束，但是在服务器上`GameplayAbility`却依然出于冷却，这就会去阻止技能的施放直到服务器端的冷却结束。

示例项目解决这个问题是通过在本地预测的冷却开始时将Meteor技能的图标置灰，然后在服务器矫正的`Cooldown GE`到达时开启冷却计时器。

这样游戏游玩时，与低延迟的玩家相比，高延迟的玩家在短冷却技能上的开火率较低，从而导致出于劣势。Fortnite则是通过在武器上使用自定义的统计而不是冷却的`GameplayEffects`从而规避了这个问题。

而真正的可预测的冷却（玩家可以在本地冷却结束而服务器依然出于冷却时激活）会由Epic在[Gas未来的迭代计划中](#concepts-p-future)实现.

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-duration"></a>
#### 4.5.16 改变激活的游戏效果的持续时间 - Changing Active Gameplay Effect Duration
为了改变某个`Cooldown GE`或者任意`Duration`类型的`GameplayEffect`的剩余时间，我们需要改变`GameplayEffectSpec`的`Duration`，更新它的`StartServerWorldTime`、`CachedStartServerWorldTime`、`StartWorldTime`，并且使用`CheckDuration()`重新检查持续时间。在服务器上执行上面的步骤，并且将`FActiveGameplayEffect`标记为`dirty`将会把变化复制到客户端。
**注意：**这里会涉及到一个`const_cast`的使用，值得一说的是，这并不是Epic官方预想的修改持续时间的方式，但是目前为止使用它并无不可。

```c++
bool UPAAbilitySystemComponent::SetGameplayEffectDurationHandle(FActiveGameplayEffectHandle Handle, float NewDuration)
{
	if (!Handle.IsValid())
	{
		return false;
	}

	const FActiveGameplayEffect* ActiveGameplayEffect = GetActiveGameplayEffect(Handle);
	if (!ActiveGameplayEffect)
	{
		return false;
	}

	FActiveGameplayEffect* AGE = const_cast<FActiveGameplayEffect*>(ActiveGameplayEffect);
	if (NewDuration > 0)
	{
		AGE->Spec.Duration = NewDuration;
	}
	else
	{
		AGE->Spec.Duration = 0.01f;
	}

	AGE->StartServerWorldTime = ActiveGameplayEffects.GetServerWorldTime();
	AGE->CachedStartServerWorldTime = AGE->StartServerWorldTime;
	AGE->StartWorldTime = ActiveGameplayEffects.GetWorldTime();
	ActiveGameplayEffects.MarkItemDirty(*AGE);
	ActiveGameplayEffects.CheckDuration(Handle);

	AGE->EventSet.OnTimeChanged.Broadcast(AGE->Handle, AGE->StartWorldTime, AGE->GetDuration());
	OnGameplayEffectDurationChange(*AGE);

	return true;
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-dynamic"></a>
#### 4.5.17 运行时创建动态游戏效果 - Creating Dynamic Gameplay Effects at Runtime
运行时创建动态的`GameplayEffects`是一项高级主题，切忌滥用。

在运行时，只有`Instant`类型的`GameplayEffects`可以用C++创建。`Duration`和`Infinite`类型的`GameplayEffects`无法在运行时动态创建，因为当他们复制时他们会寻找不存在的`GameplayEffect`类的定义。为了实现该功能，你应该像通常在编辑器中那样创建一个原型`GameplayEffect`类。然后在运行时自定义`GameplayEffectSpec`实例。

运行时创建的`Instant`类型的`GameplayEffects`也可以由[本地预测](#concepts-p)的`GameplayAbility`内进行调用。但是，目前还不清楚动态的创建是否会有一些副作用。

##### 示例

示例项目中动态创建了一个`GameplayEffect`，来当角色受到击杀时（由`AttributeSet`中处理），发送金币和经验点数到击杀者头上。

```c++
// Create a dynamic instant Gameplay Effect to give the bounties
UGameplayEffect* GEBounty = NewObject<UGameplayEffect>(GetTransientPackage(), FName(TEXT("Bounty")));
GEBounty->DurationPolicy = EGameplayEffectDurationType::Instant;

int32 Idx = GEBounty->Modifiers.Num();
GEBounty->Modifiers.SetNum(Idx + 2);

FGameplayModifierInfo& InfoXP = GEBounty->Modifiers[Idx];
InfoXP.ModifierMagnitude = FScalableFloat(GetXPBounty());
InfoXP.ModifierOp = EGameplayModOp::Additive;
InfoXP.Attribute = UGDAttributeSetBase::GetXPAttribute();

FGameplayModifierInfo& InfoGold = GEBounty->Modifiers[Idx + 1];
InfoGold.ModifierMagnitude = FScalableFloat(GetGoldBounty());
InfoGold.ModifierOp = EGameplayModOp::Additive;
InfoGold.Attribute = UGDAttributeSetBase::GetGoldAttribute();

Source->ApplyGameplayEffectToSelf(GEBounty, 1.0f, Source->MakeEffectContext());
```

第二个例子中展示了运行时在一个本地预测的`GameplayAbility`中运行时创建`GameplayEffect`。使用的话需要自行承担其风险（参见代码中的注释）！

```c++
UGameplayAbilityRuntimeGE::UGameplayAbilityRuntimeGE()
{
	NetExecutionPolicy = EGameplayAbilityNetExecutionPolicy::LocalPredicted;
}

void UGameplayAbilityRuntimeGE::ActivateAbility(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo* ActorInfo, const FGameplayAbilityActivationInfo ActivationInfo, const FGameplayEventData* TriggerEventData)
{
	if (HasAuthorityOrPredictionKey(ActorInfo, &ActivationInfo))
	{
		if (!CommitAbility(Handle, ActorInfo, ActivationInfo))
		{
			EndAbility(Handle, ActorInfo, ActivationInfo, true, true);
		}

		// Create the GE at runtime.
		UGameplayEffect* GameplayEffect = NewObject<UGameplayEffect>(GetTransientPackage(), TEXT("RuntimeInstantGE"));
		GameplayEffect->DurationPolicy = EGameplayEffectDurationType::Instant; // Only instant works with runtime GE.

		// Add a simple scalable float modifier, which overrides MyAttribute with 42.
		// In real world applications, consume information passed via TriggerEventData.
		const int32 Idx = GameplayEffect->Modifiers.Num();
		GameplayEffect->Modifiers.SetNum(Idx + 1);
		FGameplayModifierInfo& ModifierInfo = GameplayEffect->Modifiers[Idx];
		ModifierInfo.Attribute.SetUProperty(UMyAttributeSet::GetMyModifiedAttribute());
		ModifierInfo.ModifierMagnitude = FScalableFloat(42.f);
		ModifierInfo.ModifierOp = EGameplayModOp::Override;

		// Apply the GE.

		// Create the GESpec here to avoid the behavior of ASC to create GESpecs from the GE class default object.
		// Since we have a dynamic GE here, this would create a GESpec with the base GameplayEffect class, so we
		// would lose our modifiers. Attention: It is unknown, if this "hack" done here can have drawbacks!
		// The spec prevents the GE object being collected by the GarbageCollector, since the GE is a UPROPERTY on the spec.
		FGameplayEffectSpec* GESpec = new FGameplayEffectSpec(GameplayEffect, {}, 0.f); // "new", since lifetime is managed by a shared ptr within the handle
		ApplyGameplayEffectSpecToOwner(Handle, ActorInfo, ActivationInfo, FGameplayEffectSpecHandle(GESpec));
	}
	EndAbility(Handle, ActorInfo, ActivationInfo, false, false);
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ge-containers"></a>
#### 4.5.18 游戏效果容器 - Gameplay Effect Containers
Epic的[Action RPG Sample Project](https://www.unrealengine.com/marketplace/en-US/product/action-rpg)项目实现了一个名为`FGameplayEffectContainer`的结构。他们并不存在于默认GAS框架内，但是对于存储`GameplayEffects`和[`TargetData`](#concepts-targeting-data)是极为好用的。它为一些效果实现了自动化，比如从`GameplayEffects`创建`GameplayEffectSpecs`，并在它的`GameplayEffectContext`中设置其默认值。在`GameplayAbility`中构建`GameplayEffectContainer`，并且将其传递给生成的子弹，这一些列操作是非常简单直接的。我并没有在示例项目中选择去实现`GameplayEffectContainers`，这也失去了为你展示如何将寻常GAS项目进行拓展，但是我还是强烈建议在你的项目中去使用这个。

要访问`GameplayEffectContainers`中的`GESpecs`，做类似添加`SetByCallers`的操作，要展开`FGameplayEffectContainer`并且通过`GESpecs`数组的索引访问其内具体的`GESpec`引用。这就需要你提前知晓你想要访问的`GESpec`的索引。

![SetByCaller with a GameplayEffectContainer](https://github.com/tranek/GASDocumentation/raw/master/Images/gecontainersetbycaller.png)

`GameplayEffectContainers`也包含了一个选项可以高效的[目标选取方法](#concepts-targeting-containers)。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga"></a>
### 4.6 游戏技能 - Gameplay Abilities

<a name="concepts-ga-definition"></a>

#### 4.6.1 游戏技能的定义 - Gameplay Ability Definition
[`GameplayAbilities`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/UGameplayAbility/index.html)（常简称`GA`），是游戏中`Actor`可以完成的任意的动作或者技能。在同一时间，可以同时存在且激活的`GameplayAbility`的数量并没有限制，比如说冲刺能力和射击的能力就可以同时存在。这些`GameplayAbilities`在蓝图或者C++中都可以实现。

适合使用`GameplayAbilities`来实现的动作举例：
* Jumping - 跳跃
* Sprinting - 冲刺
* Shooting a gun - 持枪射击
* Passively blocking an attack every X number of seconds - 每X秒被动阻挡一次攻击
* Using a potion - 使用药水
* Opening a door - 开门（机关）
* Collecting a resource - 收集资源
* Constructing a building - 构建建筑

不适合使用`GameplayAbilities`来实现的动作：
* Basic movement input - 基本移动输入
* Some interactions with UIs - 一些UI相关的交互，建议不要使用`GameplayAbility`来实现商店购买相关的功能。

这些并不是规则，而只是我的建议。你的设计和实现可以根据具体情况和玩法去灵活变通。

`GameplayAbilities`默认就带有一项功能，即在对属性进行修改时会根据等级具体决定修改的数值的多少，甚至于根据等级去改变`GameplayAbility`的功能也是有可能的。

`GameplayAbilities`会在所属客户端上运行，而在服务端则会根据[`Net Execution Policy`](#concepts-ga-net)（而不是模拟代理节点）来决定是否也运行。`Net Execution Policy`决定了`GameplayAbility`是否进行本地的[预测](#concepts-p)。对[optional cost and cooldown `GameplayEffects`](#concepts-ga-commit)他们会包含一些默认的行为。`GameplayAbilities`使用[`AbilityTasks`](#concepts-at)来处理那些会持续一段时间的动作，比如等待某个事件，等待某个属性变化，等待玩家选择某个目标，或者通过`Root Motion Source`来移动某个`Character`。**模拟的客户端将不会运行`GameplayAbilities`**。相对应的，当服务器运行技能时，任何需要在模拟代理上可视化呈现的部分（如播放动画蒙太奇）都将通过`AbilityTasks`或者[`GameplayCues`](#concepts-gc)（负责声音和粒子部分）来复制或者远程过程调用。

所有的`GameplayAbilities`都需要重写`ActivateAbility()`以实现你自己的游玩逻辑。当`GameplayAbility`结束或者取消时，还可以在`EndAbility()`添加一些额外的运行逻辑。

简单的`GameplayAbility`流程图：
![Simple GameplayAbility Flowchart](https://github.com/tranek/GASDocumentation/raw/master/Images/abilityflowchartsimple.png)

稍微复杂一些的`GameplayAbility`流程图：
![Complex GameplayAbility Flowchart](https://github.com/tranek/GASDocumentation/raw/master/Images/abilityflowchartcomplex.png)

复杂的技能也可以使用多个互相之间交互（激活、取消等）的`GameplayAbilities`来实现。

<a name="concepts-ga-definition-reppolicy"></a>

##### 4.6.1.1 复制策略 - Replication Policy
不要使用这个选项。本身这个名字存在一定的误导性，你要知道其实你并不需要关心这个。默认情况下[`GameplayAbilitySpecs`](#concepts-ga-spec)就会被从服务端复制到所属服务器。上面也提到过，**`GameplayAbilities`不会在模拟代理上运行**。他们使用`AbilityTasks`和`GameplayCues`来复制或者远程过程调用可视化的变化到模拟代理。Epic的Dave Ratti也表明他希望能够[在未来删除这个选项](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89).

<a name="concepts-ga-definition-remotecancel"></a>
##### 4.6.1.2 服务器端远程技能取消 - Server Respects Remote Ability Cancellation
这个选项常常会引发一些麻烦。即，如果客户端的`GameplayAbility`因为取消或者自然完成而结束，它会强制服务器也去结束（无论在服务器是否也完成）。这个问题很重要，特别是针对使用本地预测的`GameplayAbilities`高延迟的玩家来说。通常你最好禁用这个选项。

<a name="concepts-ga-definition-repinputdirectly"></a>
##### 4.6.1.3 直接对输入的复制 - Replicate Input Directly
启用这个选项将会一直把输入的按下和释放事件复制给服务器。Epic并不建议这样使用，取而代之的，最好使用内置到已存在的输入相关的[`AbilityTasks`](#concepts-at)的`Generic Replicated Events`，前提是你已经将你的[输入绑定到`ASC`](#concepts-ga-input)。

Epic留下的注释：
```c++
/** Direct Input state replication. These will be called if bReplicateInputDirectly is true on the ability and is generally not a good thing to use. (Instead, prefer to use Generic Replicated Events). */
UAbilitySystemComponent::ServerSetInputPressed()
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-input"></a>
#### 4.6.2 绑定输入到技能系统组件 - Binding Input to the ASC
`ASC`允许直接将输入绑定到它上面，并且当你赋予`GameplayAbilities`时可以指定相应的输入。指定给`GameplayAbilities`的输入动作会在输入触发后且`GameplayTag`满足要求的情况下自动激活这些`GameplayAbilities`。要使用内置的响应输入的`AbilityTasks`就需要做好IA的分配。

除了指定输入动作从而激活`GameplayAbilities`外，`ASC`还可以接受通用的`Confirm`和`Cancel`输入。这些特殊的输入由`AbilityTasks`来使用，从而进行一些操作的确认以及取消，比如[`Target Actors`](#concepts-targeting-actors)，即目标的选取和取消选取。

要绑定输入到`ASC`，你必须首先创建一个枚举，将输入动作的名称转换为字节。枚举名称必须与项目设置中的输入动作名称相匹配。`DisplayName`则无所谓。

实例项目中的代码：
```c++
UENUM(BlueprintType)
enum class EGDAbilityInputID : uint8
{
	// 0 None
	None			UMETA(DisplayName = "None"),
	// 1 Confirm
	Confirm			UMETA(DisplayName = "Confirm"),
	// 2 Cancel
	Cancel			UMETA(DisplayName = "Cancel"),
	// 3 LMB
	Ability1		UMETA(DisplayName = "Ability1"),
	// 4 RMB
	Ability2		UMETA(DisplayName = "Ability2"),
	// 5 Q
	Ability3		UMETA(DisplayName = "Ability3"),
	// 6 E
	Ability4		UMETA(DisplayName = "Ability4"),
	// 7 R
	Ability5		UMETA(DisplayName = "Ability5"),
	// 8 Sprint
	Sprint			UMETA(DisplayName = "Sprint"),
	// 9 Jump
	Jump			UMETA(DisplayName = "Jump")
};
```

如果你的`ASC`位于`Character`上，那么在`SetupPlayerInputComponent()`可以进行这个绑定过程：
```c++
// Bind to AbilitySystemComponent
AbilitySystemComponent->BindAbilityActivationToInputComponent(PlayerInputComponent, FGameplayAbilityInputBinds(FString("ConfirmTarget"), FString("CancelTarget"), FString("EGDAbilityInputID"), static_cast<int32>(EGDAbilityInputID::Confirm), static_cast<int32>(EGDAbilityInputID::Cancel)));
```

如果你的`ASC`位于`PlayerState`上，那么在`SetupPlayerInputComponent()`里可能会存在潜在的竞争情况，`PlayerState`可能还没有被复制到客户端。因此，我建议可以尝试在`SetupPlayerInputComponent()`和`OnRep_PlayerState()`里都去做输入的绑定。只在`OnRep_PlayerState()`里做绑定的话也并不够，因为某些情况下`PlayerState`被复制到客户端时，`Actor`的`InputComponent`可能会为空（比如说`PlayerController`通知客户端调用`ClientRestart()`，这一步会进行`InputComponent`的创建，而这一步可能是晚于`OnRep_PlayerState()`，那么此时就没有`InputComponent`可以用来去绑定了）。实例项目演示了如何在两处进行绑定，并且通过一个布尔字段进行控制，从而令绑定操作实际上只执行一次。

**注意：**示例项目中枚举的`Confirm`和`Cancel`并没有和项目配置里的输入动作的名称匹配（`ConfirmTarget` and `CancelTarget`），但是我们在`BindAbilityActivationToInputComponent()`进行了他们之间映射的构建。这里我们只是针对他们利用映射构建做了一下特殊处理，所以他们才不需要名称相同，当然他们也可以去进行依据名称的匹配。枚举中的其他输入都必须与项目设置中的输入动作名称相匹配。

对于只会被通过一个输入来激活的`GameplayAbilities`（比如MOBA游戏中，技能始终都在一个固定的技能槽中），我偏向于在`UGameplayAbility`的子类里添加一个变量，利用它来定义输入。然后我可以在赋予技能时从`ClassDefaultObject`里读取这个变量。

<a name="concepts-ga-input-noactivate"></a>
##### 4.6.2.1 在不激活技能的情况下绑定到输入 - Binding to Input without Activating Abilities
如果你不希望你的`GameplayAbilities`在输入被按下时自动激活，但是仍然想要把输入绑定到对应技能并使用`AbilityTasks`，你可以在你的`UGameplayAbility`子类中添加一个新的布尔变量，`bActivateOnInput`，默认设置为`true`，并且重载`UAbilitySystemComponent::AbilityLocalInputPressed()`：

```c++
void UGSAbilitySystemComponent::AbilityLocalInputPressed(int32 InputID)
{
	// Consume the input if this InputID is overloaded with GenericConfirm/Cancel and the GenericConfim/Cancel callback is bound
	if (IsGenericConfirmInputBound(InputID))
	{
		LocalInputConfirm();
		return;
	}

	if (IsGenericCancelInputBound(InputID))
	{
		LocalInputCancel();
		return;
	}

	// ---------------------------------------------------------

	ABILITYLIST_SCOPE_LOCK();
	for (FGameplayAbilitySpec& Spec : ActivatableAbilities.Items)
	{
		if (Spec.InputID == InputID)
		{
			if (Spec.Ability)
			{
				Spec.InputPressed = true;
				if (Spec.IsActive())
				{
					if (Spec.Ability->bReplicateInputDirectly && IsOwnerActorAuthoritative() == false)
					{
						ServerSetInputPressed(Spec.Handle);
					}

					AbilitySpecInputPressed(Spec);

					// Invoke the InputPressed event. This is not replicated here. If someone is listening, they may replicate the InputPressed event to the server.
					InvokeReplicatedEvent(EAbilityGenericReplicatedEvent::InputPressed, Spec.Handle, Spec.ActivationInfo.GetActivationPredictionKey());
				}
				else
				{
					UGSGameplayAbility* GA = Cast<UGSGameplayAbility>(Spec.Ability);
					if (GA && GA->bActivateOnInput)
					{
						// Ability is not active, so try to activate it
						TryActivateAbility(Spec.Handle);
					}
				}
			}
		}
	}
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-granting"></a>
#### 4.6.3 赋予技能 - Granting Abilities
赋予`GameplayAbility`到某个`ASC`，是将其添加到`ASC`的`ActivatableAbilities`列表之中，并在满足[`GameplayTag` requirements](#concepts-ga-tags)时根据意愿允许其激活。

我们在服务器赋予`GameplayAbilities`，然后会自动将[`GameplayAbilitySpec`](#concepts-ga-spec)复制到所属客户端。其他的客户端/模拟代理并不会接收`GameplayAbilitySpec`。

示例项目在`Character`类里存储了一个`TArray<TSubclassOf<UGDGameplayAbility>>`，当游戏开始时，读取这些技能并将它们赋予给角色：
```c++
void AGDCharacterBase::AddCharacterAbilities()
{
	// Grant abilities, but only on the server
	if (Role != ROLE_Authority || !AbilitySystemComponent.IsValid() || AbilitySystemComponent->CharacterAbilitiesGiven)
	{
		return;
	}

	for (TSubclassOf<UGDGameplayAbility>& StartupAbility : CharacterAbilities)
	{
		AbilitySystemComponent->GiveAbility(
			FGameplayAbilitySpec(StartupAbility, GetAbilityLevel(StartupAbility.GetDefaultObject()->AbilityID), static_cast<int32>(StartupAbility.GetDefaultObject()->AbilityInputID), this));
	}

	AbilitySystemComponent->CharacterAbilitiesGiven = true;
}
```

当赋予这些`GameplayAbilities`是，我们根据`UGameplayAbility`类、技能等级、绑定输入以及`SourceObject`/将`GameplayAbility`给到`ASC`的相关信息去创建`GameplayAbilitySpecs`。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-activating"></a>
#### 4.6.4 激活技能 - Activating Abilities
如果某个`GameplayAbility`指定了输入动作，它将会在输入按下且满足`GameplayTag`的要求后自动激活。当然，这种激活方式不一定能够满足所有的需求。`ASC`提供了四种其他的方法来激活`GameplayAbilities`：通过`GameplayTag`激活，通过`GameplayAbility`类来激活，通过`GameplayAbilitySpec`句柄来激活，以及通过事件进行激活。通过事件激活`GameplayAbility`可以让你[随事件传入一定量的数据](#concepts-ga-data)。

```c++
UFUNCTION(BlueprintCallable, Category = "Abilities")
bool TryActivateAbilitiesByTag(const FGameplayTagContainer& GameplayTagContainer, bool bAllowRemoteActivation = true);

UFUNCTION(BlueprintCallable, Category = "Abilities")
bool TryActivateAbilityByClass(TSubclassOf<UGameplayAbility> InAbilityToActivate, bool bAllowRemoteActivation = true);

bool TryActivateAbility(FGameplayAbilitySpecHandle AbilityToActivate, bool bAllowRemoteActivation = true);

bool TriggerAbilityFromGameplayEvent(FGameplayAbilitySpecHandle AbilityToTrigger, FGameplayAbilityActorInfo* ActorInfo, FGameplayTag Tag, const FGameplayEventData* Payload, UAbilitySystemComponent& Component);

FGameplayAbilitySpecHandle GiveAbilityAndActivateOnce(const FGameplayAbilitySpec& AbilitySpec);
```
要想通过事件激活一个`GameplayAbility`，必须在`GameplayAbility`里对其`Triggers`进行配置，并指定一个`GameplayTag`和选择一个`GameplayEvent`。为了将事件发送出去，可以使用函数`UAbilitySystemBlueprintLibrary::SendGameplayEventToActor(AActor* Actor, FGameplayTag EventTag, FGameplayEventData Payload)`。通过事件激活一个`GameplayAbility`允许你传入一定量的数据。

`GameplayAbility`的`Triggers`可以允许你在添加或者移除`GameplayTag`时对`GameplayAbility`进行激活。

**注意：** 当在蓝图中通过事件激活某个`GameplayAbility`，你必须使用`ActivateAbilityFromEvent`节点，同时标准`ActivateAbility`节点**必须不能**在你的蓝图中出现。如果`ActivateAbility`节点存在的话，则会忽略`ActivateAbilityFromEvent`节点。

**注意：** 当`GameplayAbility`应该结束时不要忘记调用`EndAbility()`，除非该`GameplayAbility`是作为被动技能存在。

**本地预测**的 `GameplayAbilities`的激活步骤：
1. **所属客户端** 调用`TryActivateAbility()`
2. 调用`InternalTryActivateAbility()`
3. 调用`CanActivateAbility()`，返回值是去检查`GameplayTag`的要求是否满足，`ASC`是否能够承受消耗，`GameplayAbility`是否出于冷却状态，以及是否当前有其他实例出于激活状态
4. 调用`CallServerTryActivateAbility()`，并且传递生成好的`Prediction Key`
5. 调用`CallActivateAbility()`
6. 调用`PreActivate()`，Epic将这个称为例行公事
7. 调用`ActivateAbility()`，即最终激活这个技能

**服务器**接收`CallServerTryActivateAbility()`
1. 调用`ServerTryActivateAbility()`
2. 调用`InternalServerTryActivateAbility()`
3. 调用`InternalTryActivateAbility()`
4. 调用`CanActivateAbility()`，并且返回：`GameplayTag`的要求是否得到满足，`ASC`是否能够承受消耗，`GameplayAbility`是否出于冷却状态，以及是否当前有其他实例出于激活状态
5. 调用`ClientActivateAbilitySucceed()`，如果成功的话，更新其`ActivationInfo`，表明其激活行为已被服务器确认，并且广播`OnConfirmDelegate`委托。这和输入的确认是不同的两回事。
6. 调用`CallActivateAbility()`
7. 调用`PreActivate()`，Epic将这个称为例行公事
8. 调用`ActivateAbility()`，即最终激活这个技能

无论任何时候服务器激活失败，它会去调用`ClientActivateAbilityFailed()`，立即结束客户端的`GameplayAbility`并且撤销任何可以预测的变化。

<a name="concepts-ga-activating-passive"></a>
##### 4.6.4.1 被动技能 - Passive Abilities
要实现一个被动技能`GameplayAbilities`，其自动激活且可以持续允许，需要重载`UGameplayAbility::OnAvatarSet()`（它在`GameplayAbility`被赋予且`AvatarActor`被设置时会自动调用），并且调用`TryActivateAbility()`。

我建议在你自定义的`UGameplayAbility`类里添加一个`bool`量，用来指明在`GameplayAbility`被赋予时是否应该去激活。示例项目中实际利用这个来实现了被动护甲堆叠的技能。

被动的`GameplayAbilities`通常会将[`Net Execution Policy`](#concepts-ga-net)设置为`Server Only`。

```c++
void UGDGameplayAbility::OnAvatarSet(const FGameplayAbilityActorInfo * ActorInfo, const FGameplayAbilitySpec & Spec)
{
	Super::OnAvatarSet(ActorInfo, Spec);

	if (ActivateAbilityOnGranted)
	{
		bool ActivatedAbility = ActorInfo->AbilitySystemComponent->TryActivateAbility(Spec.Handle, false);
	}
}
```

Epic对该函数作了如下描述：这是去初始化被动技能并且干类似`BeginPlay`这种类型的事情的位置。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-cancelabilities"></a>
#### 4.6.5 取消技能 - Canceling Abilities
要从内部取消一个`GameplayAbility`，你可以调用`CancelAbility()`。这将会调用`EndAbility()`并且将其中的`WasCancelled`参数设置为真。

要从外部取消一个`GameplayAbility`的话，`ASC`提供了一些相关的方法：

```c++
/** Cancels the specified ability CDO. */
void CancelAbility(UGameplayAbility* Ability);

/** Cancels the ability indicated by passed in spec handle. If handle is not found among reactivated abilities nothing happens. */
void CancelAbilityHandle(const FGameplayAbilitySpecHandle& AbilityHandle);

/** Cancel all abilities with the specified tags. Will not cancel the Ignore instance */
void CancelAbilities(const FGameplayTagContainer* WithTags=nullptr, const FGameplayTagContainer* WithoutTags=nullptr, UGameplayAbility* Ignore=nullptr);

/** Cancels all abilities regardless of tags. Will not cancel the ignore instance */
void CancelAllAbilities(UGameplayAbility* Ignore=nullptr);

/** Cancels all abilities and kills any remaining instanced abilities */
virtual void DestroyActiveState();
```

**注意：** 我发现如果你有一个 `Non-Instanced`的`GameplayAbilities`时，`CancelAllAbilities`似乎并无法正常起作用。它似乎是在遇到`Non-Instanced`的`GameplayAbility`时会取消掉。`CancelAbilities`在处理`Non-Instanced`的 `GameplayAbilities`时候表现更加良好，示例项目中也是用的这种处理方式（跳跃就是用的`Non-Instanced`的 `GameplayAbilities`来做的）。当然这方面你的做法可以是不同的。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-definition-activeability"></a>
#### 4.6.6 获取处于激活状态的技能 - Getting Active Abilities
新手经常会提一些类似“我怎么获取到激活的技能”这样类似的问题，希望可能去操作其上的变量或者是去取消掉这个技能。某一个事件点上可以同时有多个`GameplayAbility`处于激活状态，所以并不会有某个所谓的“active ability”让你去获取。取而代之的，你必须在`ASC`的名为`ActivatableAbilities`的列表（`ASC`上存储赋予的`GameplayAbilities`的位置）中去查询，去尝试寻找匹配你所寻找的[`Asset`或者`Granted`的`GameplayTag`](#concepts-ga-tags)的技能。

`UAbilitySystemComponent::GetActivatableAbilities()`函数会返回一个`TArray<FGameplayAbilitySpec>`，你可以在这个基础上进行迭代。

`ASC`也提供了另一个帮助函数，可以传入一个`GameplayTagContainer`参数来进行查找，这比上面直接在`GameplayAbilitySpecs`列表上进行迭代更加方便。其中的`bOnlyAbilitiesThatSatisfyTagRequirements`参数只会返回匹配`GameplayTag`要求且当前能够被激活的`GameplayAbilitySpecs`。例如，你可以有两个基本的攻击`GameplayAbilities`，其中一个是使用武器的，另一个使用的是拳头。根据是否装备武器设定相应的`GameplayTag`，从而激活我们想要的那个。参考Epic对这个函数的注释以获取更多信息。
```c++
UAbilitySystemComponent::GetActivatableGameplayAbilitySpecsByAllMatchingTags(const FGameplayTagContainer& GameplayTagContainer, TArray < struct FGameplayAbilitySpec* >& MatchingGameplayAbilities, bool bOnlyAbilitiesThatSatisfyTagRequirements = true)
```

一旦你获取到你想要找的`FGameplayAbilitySpec`，你可以调用技能上的`IsActive()`函数来判断其是否处于激活状态中。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-instancing"></a>
#### 4.6.7 实例化的策略 - Instancing Policy
`GameplayAbility`的`Instancing Policy`决定了当`GameplayAbility`激活时是否去进行实例化以及如何实例化的问题。

| `Instancing Policy`     | 描述                                                                                      | 何时使用举例                                                                                                                                                                                                                                                                                                                                                                                             |
| ----------------------- | ------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Instanced Per Actor     | 每个`ASC`只有一个`GameplayAbility`的实例，在技能的重复激活时进行复用。    | 这可能是你使用的最多的`Instancing Policy`。你可以在任何技能上使用它，并且在技能的重复激活期间提供持久性。设计者需要负责在激活时去手动重置需要的变量。                                                                                                                                                               |
| Instanced Per Execution | 每次激活一个`GameplayAbility`，就会去创建一个新的`GameplayAbility`的实例。 | 这种做法的好处是你每次激活`GameplayAbilities`时都会去重置所有变量（不需要手动）。相对于上面的`Instanced Per Actor`这种做法开销非常大，因为每当激活一个新的`GameplayAbilities`都需要进行一次实例化。示例项目种并没有使用这个。                                                                                                                                 |
| Non-Instanced           | `GameplayAbility`在它的`ClassDefaultObject`上进行操作。不会去创建任何的实例。            | 在三个做法之中这种做法开销最小，但是其使用起来颇为严苛，且限制颇多。`Non-Instanced`的`GameplayAbilities`不能存储状态，意味着不能有动态变量且不能绑定`AbilityTask`的委托。这个的最佳用途是频繁使用的简单技能，例如MOBA或者RTS游戏种小兵的普通攻击。示例项目中跳跃的`GameplayAbility`就是`Non-Instanced`。 |

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-net"></a>
#### 4.6.8 网络执行策略 - Net Execution Policy
`GameplayAbility`的`Net Execution Policy`决定了谁来运行`GameplayAbility`以及以什么样的顺序来运行。

| `Net Execution Policy` | 描述                                                                                                                                                                                                         |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Local Only`           | `GameplayAbility`只运行在所属客户端上。这个对于那些只会有本地的视觉等装饰性的变化的技能来说是非常好用的。单人游戏应该使用`Server Only`。                                     |
| `Local Predicted`      | `Local Predicted`的`GameplayAbilities`首先在所属客户端上激活，然后才是服务器。服务器那边将会修正客户端预测的不正确的部分。参考[Prediction](#concepts-p)。 |
| `Server Only`          | `GameplayAbility`只在服务器上运行。被动的`GameplayAbilities`通常是`Server Only`。。                                                                  |
| `Server Initiated`     | `Server Initiated`的`GameplayAbilities`首先在服务器上进行激活，然后才是所属客户端。我个人不太使用这个。。                                                                     |

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-tags"></a>
#### 4.6.9 技能标签 - Ability Tags
`GameplayAbilities`附带着`GameplayTagContainers`，其又有内置的逻辑。这些`GameplayTags`都是不去复制的。

| `GameplayTag Container`     | 描述                                                                                                                                                                                   |
| --------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Ability Tags`              | `GameplayAbility`所拥有的`GameplayTags`。这些只是用来描述`GameplayAbility`的`GameplayTags`。                                                                              |
| `Cancel Abilities with Tag` | 当这个`GameplayAbility`激活时，如果还有其他`GameplayAbilities`的`Ability Tags`也有这种`GameplayTags`的话，那么这些其他的技能就会被取消掉。                                                   |
| `Block Abilities with Tag`  | 当这个`GameplayAbility`激活时，如果还有其他`GameplayAbilities`的`Ability Tags`也有这种`GameplayTags`的话，那么就会阻止其他的这些技能的激活。                                          |
| `Activation Owned Tags`     | 在`GameplayAbility`激活时，这些`GameplayTags`会被给到`GameplayAbility`的所有者。再次强调这些不会被进行复制。                                                    |
| `Activation Required Tags`  | 仅当所有者拥有**所有**这些`GameplayTags`时，`GameplayAbility`才能够被激活。                                                                                                |
| `Activation Blocked Tags`   | 如果所有者有这些`GameplayTags`中的任意一些，那么`GameplayAbility`就不能够被激活。                                                                                                  |
| `Source Required Tags`      | 仅当`Source`拥有**所有**这些`GameplayTags`时，这个`GameplayAbility`才能够被激活。`Source`的`GameplayTags`仅在 事件触发`GameplayAbility`时进行设置。 |
| `Source Blocked Tags`       | 如果`Source`拥有这些`GameplayTags`中的任意一些，那么`GameplayAbility`就不能够被激活。`Source`的`GameplayTags`仅在 事件触发`GameplayAbility`时进行设置。   |
| `Target Required Tags`      | 仅当`Target`拥有**所有**这些`GameplayTags`时，这个`GameplayAbility`才能够被激活。`Target`的`GameplayTags`仅在 事件触发`GameplayAbility`时进行设置。 |
| `Target Blocked Tags`       | 如果`Target`拥有这些`GameplayTags`中的任意一些，那么`GameplayAbility`就不能够被激活。`Target`的`GameplayTags`仅在 事件触发`GameplayAbility`时进行设置。  |

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-spec"></a>
#### 4.6.10 Gameplay Ability Spec
在技能被赋予后，`GameplayAbilitySpec`就会存在于`ASC`，其定义了处于可被激活状态的`GameplayAbility` —— 根据`GameplayAbility`类，等级，输入绑定，以及运行时状态。

当`GameplayAbility`在服务区上被赋予之后，服务器会复制`GameplayAbilitySpec`到所属客户端，然后才能够被进一步激活。

激活一个`GameplayAbilitySpec`将会依照其`Instancing Policy`创建一个`GameplayAbility`的实例（如果是`Non-Instanced`的`GameplayAbilities`则并不会创建相应的实例）。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-data"></a>
#### 4.6.11 传递数据到技能 - Passing Data to Abilities
`GameplayAbilities`的一般的使用流程是`Activate->Generate Data->Apply->End`。有些时候你需要在已有数据上做一些操作。GAS为将外部数据传入到`GameplayAbilities`内部这样的操作提供了一些可选项：

| 方法                                      | 描述                                                         |
| ----------------------------------------- | ------------------------------------------------------------ |
| 通过事件激活`GameplayAbility`             | 可以在通过事件对`GameplayAbility`进行激活时包含一定量的数据。对于本地预测的`GameplayAbilities`，事件的数据们将会从客户端复制到服务端。如果有些数据无法利用已存在的变量去处理，这时可以使用`Optional Object`或者[`TargetData`](#concepts-targeting-data)两种变量。这样做的不便之处是就无法利用输入绑定来激活技能了。要通过事件激活`GameplayAbility`，`GameplayAbility`本身必须配置好`Triggers`，指定`GameplayTag`并且选定`GameplayEvent`的选项。要发送事件，可以使用函数`UAbilitySystemBlueprintLibrary::SendGameplayEventToActor(AActor* Actor, FGameplayTag EventTag, FGameplayEventData Payload)`。 |
| 使用`WaitGameplayEvent`的`AbilityTask`    | 在`GameplayAbility`激活之后，使用`WaitGameplayEvent`这个`AbilityTask`去通知`GameplayAbility`监听带有数据的事件。这个事件和发送过程和通过事件去激活`GameplayAbilities`是一样的。这样做的不便之处是事件并不是由`AbilityTask`来进行复制，只能用于`Local Only`和`Server Only`的`GameplayAbilities`。你可以编写自己的`AbilityTask`，来支持复制带数据的事件。 |
| 使用`TargetData`                          | 使用一个自定义的`TargetData`结构是一个在客户端和服务端之间传递数据的好办法。 |
| 将数据存储到`OwnerActor`或是`AvatarActor` | 使用那些存储在`OwnerActor`，`AvatarActor`或者任何其他你可以引用得到的对象的复制变量。这个方法是最灵活，且能够支持通过输入绑定来激活`GameplayAbilities`。但是，这个方式并不能保证在使用时复制来的数据一定的同步的。 你必须保证提前性——即如果你设置一个复制变量，然后立即激活`GameplayAbility`，那么由于可能的潜在的丢包问题就无法保证接收者上面的顺序。 |

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-commit"></a>

#### 4.6.12 技能的消耗和冷却 - Ability Cost and Cooldown
`GameplayAbilities`会带有可选的消耗和冷却的功能。技能消耗是为了激活由`Instant`类型的 `GameplayEffect`（[` Cost GE`](#concepts-ge-cost)）实现的`GameplayAbility`，所预定义的所需某些`Attributes`的数量。技能冷却则是为了控制`GameplayAbility`的重新激活所设定的计时器，其实现是通过一个`Duration`类型的`GameplayEffect` （[`Cooldown GE`](#concepts-ge-cooldown)）。

在`GameplayAbility`调用`UGameplayAbility::Activate()`之前，他会调用首先调用`UGameplayAbility::CanActivateAbility()`。这个函数会去检查所属的`ASC`是否能够承担技能的消耗（`UGameplayAbility::CheckCost()`）并且`GameplayAbility`并没有处于冷却回转期间（`UGameplayAbility::CheckCooldown()`）。

在`GameplayAbility`调用`Activate()`之后，可选地，他可以使用`UGameplayAbility::CommitAbility()`在任意时间点提交消耗和冷却，其内部实现实际上是去分别调用`UGameplayAbility::CommitCost()`和`UGameplayAbility::CommitCooldown()`。设计者可能会根据实际需求去选择单独调用`CommitCost()`或是`CommitCooldown()`。提交消耗和冷却会去再一次调用`CheckCost()`以及`CheckCooldown()`，这也是`GameplayAbility`去根据自身信息检查是否能够激活的最后一道保险。所属的`ASC`的`Attributes`可能在`GameplayAbility`激活之后就会发生变化，从而在技能提交时无法满足消耗。技能和冷却的提交可以是[locally predicted](#concepts-p)，前提是[prediction key](#concepts-p-key)在提交时是合法的。

参阅[`CostGE`](#concepts-ge-cost)和[`CooldownGE`](#concepts-ge-cooldown)获取更多实现细节。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-leveling"></a>

#### 4.6.13 升级技能 - Leveling Up Abilities
对于提升技能等级这件事，有两种通常的做法：

| 技能等级提升的方法                 | 描述                                                         |
| ---------------------------------- | ------------------------------------------------------------ |
| 根据新的等级，剥离然后重新赋予技能 | 从`ASC`中剥离（删除）掉`GameplayAbility`，然后在服务器上以新的等级重新进行赋予。这种做法下，如果当时技能正处于激活状态，那么他就会立即被结束掉。 |
| 提升`GameplayAbilitySpec`的等级    | 在服务器上，查找到`GameplayAbilitySpec`，增加其等级，然后将其标记为dirty，这样就可以将其复制到所属的客户端了。这种做法之下，如果当时技能正处于激活状态下，是不会将其打断或者结束的。 |

上面两种方法的主要不同之处在于，技能升级的当口会不会把技能给取消掉。这一点你可以根据你的`GameplayAbilities`的实际需求来灵活选择。我建议是在你的`UGameplayAbility`子类中用一个bool变量去具体控制到底是使用哪一种方法。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-sets"></a>

#### 4.6.14 技能组 - Ability Sets
`GameplayAbilitySets`是一系列便捷的`UDataAsset`类，可以用来存储输入绑定以及角色的初始的`GameplayAbilities`的列表。可以继承它然后再添加一些额外的逻辑和属性。Paragon项目中为每个英雄准备了一个`GameplayAbilitySet`，其中包含了所有的赋予到其身上的`GameplayAbilities`.

就目前来看，我发现这个类并不是非常常用。实例项目在`GDCharacterBase`及其子类中就完成了和`GameplayAbilitySets`类似的功能。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-batching"></a>
#### 4.6.15 技能批处理 - Ability Batching
传统的`Gameplay Ability`的声明周期包含了至少两到三次的从客户端到服务端的RPC，即：

1. `CallServerTryActivateAbility()`
1. `ServerSetReplicatedTargetData()`（不是必须的）
1. `ServerEndAbility()`

如果`GameplayAbility`在一帧中的一个原子组内执行所有这些操作的话，我们可以将这两个到三个的RPC打包成一个RPC进而优化操作。`GAS`中将这种针对RPC的优化称为是`Ability Batching`，即技能的批处理。`Ability Batching`常见的一个使用情况就是扫射的枪械。枪械激活，执行一个射线检测，发送[`TargetData`](#concepts-targeting-data)到服务器，然后在一帧的一个原子组中结束技能。[GASShooter](https://github.com/tranek/GASShooter)示例工程中演示了这项技术的使用。

半自动枪械就是最好的案例，可以将`CallServerTryActivateAbility()`，`ServerSetReplicatedTargetData()`（子弹撞击结果），以及`ServerEndAbility()`打包到一个RPC而不是三个单独的RPC。

全自动/爆破枪械可以将第一发子弹的`CallServerTryActivateAbility()`和`ServerSetReplicatedTargetData()`打包到一个RPC里而不是单独的两个RPC。后续的每发子弹则是它自己的`ServerSetReplicatedTargetData()`的RPC。最后，`ServerEndAbility()`则是作为一个单独的RPC，在枪械停火后发送。这种情况并不十分美好，我们仅仅在第一发子弹上节省了一个RPC。相对的，针对这种情况还有另外一种做法，即通过[`Gameplay Event`](#concepts-ga-data)来进行技能的激活，从而将子弹的`TargetData`放在`EventPayload`里从客户端发送到服务端。后面这种方法的不便之处就是`TargetData`其实是在技能之外生成的，而批处理的方法则是在技能里进行的生成过程。

`Ability Batching`默认在[`ASC`](#concepts-asc)上是关闭的。想要激活`Ability Batching`，需要重载`ShouldDoServerAbilityRPCBatch()`并返回true：

```c++
virtual bool ShouldDoServerAbilityRPCBatch() const override { return true; }
```

现在`Ability Batching`已经被激活了，在激活你希望批处理的技能之前，你必须预先创建一个`FScopedServerAbilityRPCBatcher`。这个特殊的结构体将会试着去打包在其作用域内的任何技能。一旦`FScopedServerAbilityRPCBatcher`超出范围，其他任何技能都不会打包进去。`FScopedServerAbilityRPCBatcher`的工作原理是在每个可批处理的函数中都有特殊的代码，这些特殊代码可拦截发送RPC的调用，并将消息打包为批处理结构。当`FScopedServerAbilityRPCBatcher`超出作用域，它会自动 在`UAbilitySystemComponent::EndServerAbilityRPCBatch()`中将这个批结构发送到服务器。服务器会在`UAbilitySystemComponent::ServerAbilityRPCBatch_Internal(FServerAbilityRPCBatch& BatchInfo)`中接收这个批RPC。`BatchInfo`参数包含了一些标签：技能是否应该结束，输入是否在激活时已经按下，是否包含`TargetData`。如果你想想调试你的批处理是否正常工作，这里是个打断点的好地方。另外，可以使用控制台程序输入`AbilitySystem.ServerRPCBatching.Log 1`来激活特定的技能批处理的日志。

这一机制只能使用C++实现，并且只能通过`FGameplayAbilitySpecHandle`来激活技能。

```c++
bool UGSAbilitySystemComponent::BatchRPCTryActivateAbility(FGameplayAbilitySpecHandle InAbilityHandle, bool EndAbilityImmediately)
{
	bool AbilityActivated = false;
	if (InAbilityHandle.IsValid())
	{
		FScopedServerAbilityRPCBatcher GSAbilityRPCBatcher(this, InAbilityHandle);
		AbilityActivated = TryActivateAbility(InAbilityHandle, true);

		if (EndAbilityImmediately)
		{
			FGameplayAbilitySpec* AbilitySpec = FindAbilitySpecFromHandle(InAbilityHandle);
			if (AbilitySpec)
			{
				UGSGameplayAbility* GSAbility = Cast<UGSGameplayAbility>(AbilitySpec->GetPrimaryInstance());
				GSAbility->ExternalEndAbility();
			}
		}

		return AbilityActivated;
	}

	return AbilityActivated;
}
```

GASShooter项目对半自动枪械和全自动枪械都是用了相同的批处理`GameplayAbility`，其并不是会直接调用`EndAbility()`来进行技能的结束（它是由技能外部的另外一个本地技能来管理，具体就是根据当前的开火模式来管理玩家的输入以及技能的批处理调用）。因为所有的RPC必须被在`FScopedServerAbilityRPCBatcher`的作用域内调用，我提供了一个`EndAbilityImmediately`参数，从而令本地的控制/管理能够指出这个技能是否应该打包 `EndAbility()`调用（半自动），亦或是不打包`EndAbility()`调用（全自动），这样它可以在后面的某个时间用自己的RPC来发送`EndAbility()`。

GASShooter项目中暴露了一个蓝图节点，用以在本地执行的技能中来出发技能批处理。

![Activate Batched Ability](https://github.com/tranek/GASDocumentation/raw/master/Images/batchabilityactivate.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-ga-netsecuritypolicy"></a>
#### 4.6.16 网络安全策略 - Net Security Policy
`GameplayAbility`的`NetSecurityPolicy`决定了技能具体是在网络上的哪部分去执行。这可以防止客户端去尝试执行受限的技能。

| `NetSecurityPolicy`     | 描述                                                         |
| ----------------------- | ------------------------------------------------------------ |
| `ClientOrServer`        | 没有安全要求。客户端和服务端可以自由得执行和结束技能。       |
| `ServerOnlyExecution`   | 服务端会忽略客户端发起的技能执行的请求。客户端仍然可以发起请求，令服务端取消或者结束这个技能。 |
| `ServerOnlyTermination` | 服务端会忽略客户端发起的技能的取消和结束请求。客户端仍然可以发起技能执行的请求。 |
| `ServerOnly`            | 服务端控制技能的执行和结束。发起请求的客户端会被忽略。       |

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-at"></a>
### 4.7 技能任务 - Ability Tasks

<a name="concepts-at-definition"></a>
#### 4.7.1 技能任务的定义 - Ability Task Definition
`GameplayAbilities`只能在某一帧执行，这样的机制导致其并没有很大的灵活性。为了实现那些随时间变化的行为，或是在一定时间后响应委托的行为，我们可以使用延迟行为，也即`AbilityTasks`。

GAS自己带了一些可以直接使用的`AbilityTasks`：
* 基于`RootMotionSource`的用于角色移动的Task
* 播放动画蒙太奇的Task
* 响应`Attribute`的变化的Task
* 响应`GameplayEffect`的变化的Task
* 响应玩家输入的Task
* 等等

`UAbilityTask`的构造函数中硬编码了同一时间最多能够运行1000个并行的`AbilityTasks`。请谨记，当为游戏设计`GameplayAbilities`时，像RTS这种游戏可是在某个时间点会同时有上百名角色。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-at-definition"></a>
#### 4.7.2 自定义技能任务 - Custom Ability Tasks
你可能会需要创建一些自定义的`AbilityTasks`（在C++中）。示例项目中建立了两个自定义的`AbilityTasks`：
1. `PlayMontageAndWaitForEvent`是将默认的`PlayMontageAndWait`和`WaitGameplayEvent`两种`AbilityTasks`进行了结合。这可以使用动画蒙太奇利用`AnimNotifies`给播放他们的`GameplayAbility`发送事件。使用这种方式在动画蒙太奇播放过程中的特定时间点来触发指定的行为。
1. `WaitReceiveDamage`会监听`OwnerActor`接收伤害的事件。被动护甲的`GameplayAbility`在英雄接收到伤害时移除一层护甲。

`AbilityTasks`的实现需要有：

* 一个静态函数创建这个`AbilityTask`的实例
* 一些委托，绑定到`AbilityTask`实现其目标
* `Activate()`函数，以开始其核心任务，绑定外部委托等等
* `OnDestroy()`函数，用来进行清理，包括一些绑定的外部委托
* 绑定的外部委托的回调函数
* 成员变量和内联的辅助函数

**注意：**`AbilityTasks`只能声明一种类型的委托，你的所有的输出委托都必须是这个类型，无论对应的参数是否使用。未使用的委托参数会传递默认值。

`AbilityTasks`只运行在运行所属`GameplayAbility`的服务器或者客户端上；但是，`AbilityTasks`可以通过在构造函数中设置`bSimulatedTask = true;`，重载`virtual void InitSimulatedTask(UGameplayTasksComponent& InGameplayTasksComponent);`，并且设定成员变量未复制，从而运行在模拟客户端上。这只用在很少的情况，如模拟运动的`AbilityTasks`，其中你并不想复制所有的运动变化，而是模拟整个运动的`AbilityTask`。所有的`RootMotionSource`的 `AbilityTasks`都是在做这件事。参阅`AbilityTask_MoveToLocation.h/.cpp`。

如果你在构造函数中设置`bTickingTask = true;`并且重写`virtual void TickTask(float DeltaTime);`的话，`AbilityTasks`是可以执行`Tick`类似的工作的。如果你希望去逐帧插值的话，这就非常有用了。参见`AbilityTask_MoveToLocation.h/.cpp`。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-at-using"></a>
#### 4.7.3 使用技能任务 - Using Ability Tasks
为了在C++中创建和激活`AbilityTask`（`GDGA_FireGun.cpp`），需要做：
```c++
UGDAT_PlayMontageAndWaitForEvent* Task = UGDAT_PlayMontageAndWaitForEvent::PlayMontageAndWaitForEvent(this, NAME_None, MontageToPlay, FGameplayTagContainer(), 1.0f, NAME_None, false, 1.0f);
Task->OnBlendOut.AddDynamic(this, &UGDGA_FireGun::OnCompleted);
Task->OnCompleted.AddDynamic(this, &UGDGA_FireGun::OnCompleted);
Task->OnInterrupted.AddDynamic(this, &UGDGA_FireGun::OnCancelled);
Task->OnCancelled.AddDynamic(this, &UGDGA_FireGun::OnCancelled);
Task->EventReceived.AddDynamic(this, &UGDGA_FireGun::EventReceived);
Task->ReadyForActivation();
```

在蓝图中，我们仅需要使用为`AbilityTask`而构建的蓝图节点即可。而且不需要去调用`ReadyForActivate()`。它会由`Engine/Source/Editor/GameplayTasksEditor/Private/K2Node_LatentGameplayTaskCall.cpp`自动调用。如果在你的`AbilityTask`类中有`BeginSpawningActor()`和`FinishSpawningActor()`的话，`K2Node_LatentGameplayTaskCall`也将其自动调用（参见`AbilityTask_WaitTargetData`）。这里再强调一遍，`K2Node_LatentGameplayTaskCall`仅仅针对蓝图做了这些神奇的操作。在C++中，我们还是需要手动调用`ReadyForActivation()`，`BeginSpawningActor()`以及`FinishSpawningActor()`。

![Blueprint WaitTargetData AbilityTask](https://github.com/tranek/GASDocumentation/raw/master/Images/abilitytask.png)

若需要取消某个`AbilityTask`，只要在蓝图或C++中的`AbilityTask`对象上（即`Async Task Proxy`）调用`EndTask()`即可。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-at-rms"></a>
#### 4.7.4 Root Motion Source Ability Tasks
GAS带有一些能够处理角色随时间移动的`AbilityTasks`，比如角色的击退，复杂的跳跃，拉，冲刺，这些都可以使用`Root Motion Sources`以及响应的`CharacterMovementComponent`里的对应功能来实现。

**注意：** 带预测的`RootMotionSource`的`AbilityTasks`在版本4.19和4.25之后的版本可以正常运行，而在4.20-4.24之间的版本是有问题的；但是，`AbilityTasks`仍然会在多玩家下利用镜像网络矫正来执行其功能，且在单人玩家环境下运行良好。如果要强行使用，建议参考[prediction fix](https://github.com/EpicGames/UnrealEngine/commit/94107438dd9f490e7b743f8e13da46927051bf33#diff-65f6196f9f28f560f95bd578e07e290c)。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc"></a>
### 4.8 游戏反馈 - Gameplay Cues

<a name="concepts-gc-definition"></a>
#### 4.8.1 游戏反馈的定义 - Gameplay Cue Definition
`GameplayCues`（`GC`）负责执行与游玩无关的事情的处理，比如说声音效果，粒子效果，相机抖动之类。`GameplayCues`通常是会复制（除非在外部进行`Executed`，`Added`，或者本地进行`Removed`）和预测的。

我们可以通过利用`ASC`发送与某个`GameplayCue`的合法的父名称对应的`GameplayTag`以及来发送某个事件类型（`Execute`，`Add`或者`Remove`）到`GameplayCueManager`来触发`GameplayCues`。`GameplayCueNotify`对象，以及其他实现了`IGameplayCueInterface`接口的`Actors`可以注册到这些基于`GameplayCue`的`GameplayTag`（`GameplayCueTag`）的事件。

**注意：**需要重申一下，`GameplayCue`的`GameplayTags`需要以`GameplayCue`这个`GameplayTag`作为起始。比如，一个合法的`GameplayCue`的`GameplayTag`可以是`GameplayCue.A.B.C`。

有两类的`GameplayCueNotifies`：`Static`和`Actor`。他们响应不同的事件，并且不同类型的`GameplayEffects`可以去对他们进行触发。 你可以用你自己的逻辑对响应事件的内容进行重写。

| `GameplayCue` Class                                          | Event             | `GameplayEffect` 类型    | 描述                                                         |
| ------------------------------------------------------------ | ----------------- | ------------------------ | ------------------------------------------------------------ |
| [`GameplayCueNotify_Static`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayCueNotify_Static/index.html) | `Execute`         | `Instant`或者`Periodic`  | 静态`GameplayCueNotifies`是在`ClassDefaultObject`（即没有对应的实例）上进行操作，这非常适合是实现那些一次性的效果，比如说碰撞冲击这一类的。 |
| [`GameplayCueNotify_Actor`](https://docs.unrealengine.com/en-US/BlueprintAPI/GameplayCueNotify/index.html) | `Add`或者`Remove` | `Duration`或者`Infinite` | Actor类型的`GameplayCueNotifies`会在`Added`的时候生成一个新的实例。因为这些都是实例化出来的，他们可以执行某些操作，一直到他们被`Removed`掉。他们比较适合来做那些循环的声效和粒子效果，在响应的`Duration`或者`Infinite`类型的`GameplayEffect`被移除掉或者手动调用移除指令时进行中断并移除。他们也提供了一些选项来管理在同一时间允许被`Added`的数量，这样在不同的程序想要开始某段声音或者粒子时，就不会去重复叠加多个同样的效果。 |

`GameplayCueNotifies`从技术层面讲可以响应任意的事件，但是上面这些是我们更加普遍的使用方式。

**注意：**当使用`GameplayCueNotify_Actor`时，要勾选`Auto Destroy on Remove`，否则在随后`Add`那个`GameplayCueTag`就无法正常生效了。

当`ASC`的[Replication Mode](#concepts-asc-rm)不是`Full`时，服务器玩家（监听服务器）的`Add`和`Remove` `GC`的事件将会触发两次——一次是应用`GE`，另一次是通过`NetMultiCast`广播给客户端们。但是`WhileActive`事件讲仅会触发一次。所有事件在客户端仅触发一次。

示例项目中包含一个`GameplayCueNotify_Actor`来处理眩晕和冲刺效果。此外还有一个`GameplayCueNotify_Static`来处理枪械的子弹命中效果。这些`GC`可以通过[triggering them locally](#concepts-gc-local)来做进一步的优化，这样就不用通过`GE`来对他们进行复制。我在示例项目中选择以简单的初学的方法来对他们进行使用展示。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-trigger"></a>
#### 4.8.2 触发游戏反馈 - Triggering Gameplay Cues

当`GameplayEffect`被成功应用时，在相应`GameplayTags`下的所有的`GameplayCues`都会被进行触发。

![GameplayCue Triggered from a GameplayEffect](https://github.com/tranek/GASDocumentation/raw/master/Images/gcfromge.png)

`UGameplayAbility`提供了一些蓝图节点来`Execute`，`Add`或者`Remove` `GameplayCues`。

![GameplayCue Triggered from a GameplayAbility](https://github.com/tranek/GASDocumentation/raw/master/Images/gcfromga.png)

在C++里，你可以直接调用`ASC`上的函数（或者是在你的`ASC`类中将其暴露给蓝图）：

```c++
/** GameplayCues can also come on their own. These take an optional effect context to pass through hit result, etc */
void ExecuteGameplayCue(const FGameplayTag GameplayCueTag, FGameplayEffectContextHandle EffectContext = FGameplayEffectContextHandle());
void ExecuteGameplayCue(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

/** Add a persistent gameplay cue */
void AddGameplayCue(const FGameplayTag GameplayCueTag, FGameplayEffectContextHandle EffectContext = FGameplayEffectContextHandle());
void AddGameplayCue(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

/** Remove a persistent gameplay cue */
void RemoveGameplayCue(const FGameplayTag GameplayCueTag);

/** Removes any GameplayCue added on its own, i.e. not as part of a GameplayEffect. */
void RemoveAllGameplayCues();
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-local"></a>
#### 4.8.3 本地游戏反馈 - Local Gameplay Cues
从`GameplayAbilities`和`ASC`暴露出来的用于触发`GameplayCues`的函数在默认情况下是会被复制的。每个`GameplayCue`事件都是一个多播的RPC。浙江导致大量的RPC。GAS也强制限制每次网络更新至多有两个同样的`GameplayCue`的RPC。我们可以使用本地`GameplayCues`来解决这个问题。本地`GameplayCues`只会在每个独立的客户端上进行`Execute`，`Add`或者`Remove`。

本地`GameplayCues`的使用情景：
* 子弹冲击效果
* 近战撞击效果
* 从动画蒙太奇触发的`GameplayCues`

本地`GameplayCue`的函数可以添加到你自定义的`ASC`子类中：

```c++
UFUNCTION(BlueprintCallable, Category = "GameplayCue", Meta = (AutoCreateRefTerm = "GameplayCueParameters", GameplayTagFilter = "GameplayCue"))
void ExecuteGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

UFUNCTION(BlueprintCallable, Category = "GameplayCue", Meta = (AutoCreateRefTerm = "GameplayCueParameters", GameplayTagFilter = "GameplayCue"))
void AddGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

UFUNCTION(BlueprintCallable, Category = "GameplayCue", Meta = (AutoCreateRefTerm = "GameplayCueParameters", GameplayTagFilter = "GameplayCue"))
void RemoveGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);
```

```c++
void UPAAbilitySystemComponent::ExecuteGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters & GameplayCueParameters)
{
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::Executed, GameplayCueParameters);
}

void UPAAbilitySystemComponent::AddGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters & GameplayCueParameters)
{
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::OnActive, GameplayCueParameters);
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::WhileActive, GameplayCueParameters);
}

void UPAAbilitySystemComponent::RemoveGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters & GameplayCueParameters)
{
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::Removed, GameplayCueParameters);
}
```

如果某个`GameplayCue`是本地`Added`，它也相应的应该本地`Removed`。如果它是通过复制`Added`，则它相应的也应该通过复制进行 `Removed`。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-parameters"></a>
#### 4.8.4 游戏反馈的参数 - Gameplay Cue Parameters
`GameplayCues`接收一个`FGameplayCueParameters`结构体作为参数，其中包含了关于`GameplayCue`的一些额外的信息。如果你手动利用`GameplayAbility`或者`ASC`上的函数来触发`GameplayCue`，那么你必须手动构建传入到`GameplayCue`的`GameplayCueParameters`结构体。如果`GameplayCue`是由`GameplayEffect`来进行触发的，那么`GameplayCueParameters`结构体的下列参数将会自动填充：

* AggregatedSourceTags
* AggregatedTargetTags
* GameplayEffectLevel
* AbilityLevel
* [EffectContext](#concepts-ge-context)
* Magnitude （如果`GameplayEffect`选择了某项`Attribute`，并且会有对应的`Modifier`对其产生影响。）

`GameplayCueParameters`里的`SourceObject`变量是一个高度自定义的数据位置，你可以利用它在手动触发`GameplayCue`时，传入任意的数据。

**注意：**在参数结构体中的一些变量，比如`Instigator`，可能已经存在于`EffectContext`。`EffectContext`也可以包含一个`FHitResult`，用来确定在世界中的哪一位置来生成`GameplayCue`。继承`EffectContext`来进行拓展可能是一个不错的方式来向`GameplayCues`传入更多的数据，特别是对于那些由`GameplayEffect`进行触发的`GameplayCues`来说更是如此。

参阅[`UAbilitySystemGlobals`](#concepts-asg)中的三个函数，他们是负责为`GameplayCueParameters`填入数据的。他们是虚函数，所以你可以对他们进行重写以便自动填入更多的信息。

```c++
/** Initialize GameplayCue Parameters */
virtual void InitGameplayCueParameters(FGameplayCueParameters& CueParameters, const FGameplayEffectSpecForRPC &Spec);
virtual void InitGameplayCueParameters_GESpec(FGameplayCueParameters& CueParameters, const FGameplayEffectSpec &Spec);
virtual void InitGameplayCueParameters(FGameplayCueParameters& CueParameters, const FGameplayEffectContextHandle& EffectContext);
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-manager"></a>
#### 4.8.5 游戏反馈管理器 - Gameplay Cue Manager
默认情况下，`GameplayCueManager`将会在整个游戏目录中搜索`GameplayCueNotifies`，并且在游戏时讲他们加载到内存中。我们可以通过在`DefaultGame.ini`中设置`GameplayCueManager`进行扫描的路径。

```
[/Script/GameplayAbilities.AbilitySystemGlobals]
GameplayCueNotifyPaths="/Game/GASDocumentation/Characters"
```

我们希望`GameplayCueManager`扫描并找到所有的`GameplayCueNotifies`；但是，我们并不希望它在游戏时异步加载所有的`GameplayCueNotifies`。因为这样做的话，每一个`GameplayCueNotify`及其相关的声音，粒子都会被加载到内存中，不论其到底是否在关卡中进行使用。在大型游戏中，比如Paragon，这可能会是好几百兆的内存消耗，并且可能会让游戏在开启时陷入加载缓慢的境况。

另一个方案是在开启游戏时异步加载真正在关卡中起作用或可能被触发的`GameplayCues`。这可以一定程度上缓解无用内存的消耗，以及可能出现的游戏卡死，当然代价就是在游玩中每当有某个`GameplayCue`是第一次被触发时，可能会有一定的延迟效果。在SSD上并不会出现这样的延迟。我还没有在HDD上测试过。如果在UE编辑器内选用这个选项，就可能会在`GameplayCue`第一次被触发加载时出现轻微的卡顿或甚至卡死，尤其是针对粒子系统（编辑器可能会需要编译粒子系统）。在打包后这就不成问题了，因为粒子系统就已经是编译好的了。

首先我们必须继承`UGameplayCueManager`并且告诉`AbilitySystemGlobals`类，去使用我们拓展的`UGameplayCueManager`的子类，具体是在`DefaultGame.ini`中。

```
[/Script/GameplayAbilities.AbilitySystemGlobals]
GlobalGameplayCueManagerClass="/Script/ParagonAssets.PBGameplayCueManager"
```

在我们的`UGameplayCueManager`子类中，重写`ShouldAsyncLoadRuntimeObjectLibraries()`。

```c++
virtual bool ShouldAsyncLoadRuntimeObjectLibraries() const override
{
	return false;
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-prevention"></a>
#### 4.8.6 阻止游戏反馈触发 - Prevent Gameplay Cues from Firing
有些时候我们并不希望去触发某些`GameplayCue`。例如，如果我们阻止某项攻击，我们可能并不希望播放附着在伤害`GameplayEffect`上的碰撞效果For example if we block an attack, we may not want to play the hit impact attached to the damage `GameplayEffect`，亦或是想要换另外一个效果。我们可以在[`GameplayEffectExecutionCalculations`](#concepts-ge-ec)内调用`OutExecutionOutput.MarkGameplayCuesHandledManually()`，并且手动发送`GameplayCue`事件到`Target`或者`Source`的 `ASC`。

如果你永远不想在特定的`ASC`上触发任何`GameplayCues`，你可以设置`AbilitySystemComponent->bSuppressGameplayCues = true;`。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-batching"></a>

#### 4.8.7 游戏反馈的批处理 - Gameplay Cue Batching
每个触发的`GameplayCue`都是一个不可靠的NetMulticast的RPC。在某些情况下，我们可能需要同一时间触发多个`GC`，对应着有着一些优化的处理方法，来将他们合并到一个RPC中，亦或是发送相对更少量的数据从而节省带宽。

<a name="concepts-gc-batching-manualrpc"></a>
##### 4.8.7.1 手动远程过程调用 - Manual RPC
假设你有一把能射八颗子弹的猎枪，这就会有8个射线检测和以及轨迹效果的`GameplayCues`。[GASShooter](https://github.com/tranek/GASShooter)中采用了一种偷懒的方法，它将所有的轨迹信息打包到一块儿以 [`TargetData`](#concepts-targeting-data)的格式存储到[`EffectContext`](#concepts-ge-ec) 。虽然这种方法将8个RPC减到了1个，但是这1个里直接包含了原先8个的信息，包含了大量的数据（约500b），仍然需要占用很多网络资源。针对这种情况，还有一种更好的处理方法，可以在要发送的RPC中用一个自定义的结构体，其中你可以高效编码命中位置的数据，或者放一个随机数种子，从而在接收端能够重建/拟合出冲击位置的数据信息。然后客户端就可以进行数据解包并将解析出来的数据刷到[本地执行的`GameplayCues`](#concepts-gc-local)。

具体操作步骤：
1. 声明一个`FScopedGameplayCueSendContext`。它会自动阻止 `UGameplayCueManager::FlushPendingCues()`的执行，直到超出其作用域。这意味着其作用域内的所有的`GameplayCues`将会排成一个队列以供使用。
1. 重写`UGameplayCueManager::FlushPendingCues()`，依据`GameplayTag`来合并`GameplayCues`到自定义的结构体，然后通过RPC发送到客户端。
1. 客户端接收自定义结构体然后将其解包到本地执行的`GameplayCues`中。

这个方法也还有其他的适用情况，比如说你需要一些特定的参数，但是这些参数与`GameplayCueParameters`所提供的并不匹配，而且你也并不希望将其添加到`EffectContext`中，比如说伤害飘字，暴击提示，破盾提示，致命一击的提示等等。

https://forums.unrealengine.com/development-discussion/c-gameplay-programming/1711546-fscopedgameplaycuesendcontext-gameplaycuemanager

<a name="concepts-gc-batching-gcsonge"></a>
##### 4.8.7.2 一个游戏效果上带有多个游戏表现 - Multiple GCs on one GE
一个`GameplayEffect`的全部`GameplayCues`都在一个RPC中发送。默认情况下，`UGameplayCueManager::InvokeGameplayCueAddedAndWhileActive_FromSpec()`将会通过不可靠的NetMulticast的RPC来发送整个`GameplayEffectSpec`（但会转换成`FGameplayEffectSpecForRPC`），这一点不会受到`ASC`的`Replication Mode`影响。依据`GameplayEffectSpec`中的内容的不同所占据的带宽可能会非常之不同（有可能会非常占用资源）。我们可以在控制台设置`AbilitySystem.AlwaysConvertGESpecToGCParams 1`来尝试进行优化。这会将`GameplayEffectSpecs`转换为`FGameplayCueParameter`结构，这样就不用发送整个 `FGameplayEffectSpecForRPC`。这样可能会节省一些带宽，但也会相对的少一些信息，具体取决于`GESpec`到`GameplayCueParameters`的转换方法以及具体你的`GCs`需要哪些信息。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-events"></a>

#### 4.8.8 游戏反馈事件 - Gameplay Cue Events
`GameplayCues`会去响应特定的`EGameplayCueEvents`：

| `EGameplayCueEvent` | 描述                                                         |
| ------------------- | ------------------------------------------------------------ |
| `OnActive`          | 在`GameplayCue`激活（添加）时调用。                          |
| `WhileActive`       | 当`GameplayCue`处于激活状态时调用，即使它当前并没有应用。注意，这并不是`Tick`！它只会被调用一次，即当`GameplayCueNotify_Actor`被添加或者被引用。如果你需要用到`Tick()`，可以使用`GameplayCueNotify_Actor`的`Tick()`。其本质上是一个`AActor`。 |
| `Removed`           | 当`GameplayCue`被移除时调用。蓝图中对应的函数事件是`OnRemove`。 |
| `Executed`          | 当`GameplayCue`被执行时调用：瞬间的效果亦或是持续一定事件的`Tick()`。蓝图中对应的函数事件是`OnExecute`。 |

对于那些在`GameplayCue`开始时所发生的内容，都可以将其写在`OnActive`，当然，晚加入者会错过相关的东西。这无妨。如果你希望晚加入者也要能够看到相应的内容的话，可以使用`WhileActive`。例如，你在MOBA类游戏中有一个炮塔的爆炸要处理，你可以将一开始的爆炸声音和粒子效果放在`OnActive`中，然后把后续火焰粒子以及声音放在`WhileActive`。在这种情形下，晚加入者是不需要在连接上之后再去为其播放一遍初始的爆炸效果，相对的，你需要为其播放后续的炮塔燃烧的火焰效果和声音。`OnRemove`应该要去清理任何通过`OnActive` 和`WhileActive`添加的东西。`WhileActive`是每当某个`Actor`进入到`GameplayCueNotify_Actor`的关联范围里时调用。`OnRemove`则是每当有某个`Actor`离开`GameplayCueNotify_Actor`的关联范围进行触发。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-gc-reliability"></a>

#### 4.8.9 游戏反馈的可靠性 - Gameplay Cue Reliability

`GameplayCues`通常被认为是不可靠的，因此不适合用来做那些会直接影响游玩的效果。

**已执行的`GameplayCues`：**这些`GameplayCues`是通过不可靠的多播而被应用的，所以全部是不可靠的。

**从`GameplayEffects`应用的`GameplayCues`：**

* 主控端可靠得接收到`OnActive`，`WhileActive`，以及`OnRemove`  
`FActiveGameplayEffectsContainer::NetDeltaSerialize()`调用`UAbilitySystemComponent::HandleDeferredGameplayCues()`来进行`OnActive`以及`WhileActive`的调用。 `FActiveGameplayEffectsContainer::RemoveActiveGameplayEffectGrantedTagsAndModifiers()` 则负责`OnRemoved`的调用。
* 模拟端可靠得介绍到`WhileActive`和`OnRemove`  
`UAbilitySystemComponent::MinimalReplicationGameplayCues`的复制调用`WhileActive`以及`OnRemove`。`OnActive`事件则是通过不可靠的多播来进行调用的。

**`GameplayEffect`之外进行的`GameplayCues`的应用：**

* 主控端可靠得接收到`OnRemove`  
`OnActive`以及`WhileActive`事件是通过一个不可靠得多播来进行调用的。
* 模拟端可靠得接收到`WhileActive`以及`OnRemove`  
`UAbilitySystemComponent::MinimalReplicationGameplayCues`的复制调用`WhileActive`以及`OnRemove`。`OnActive`事件是由一个不可靠的多播进行调用的。

如果你需要`GameplayCue`里的某样东西是可靠的，那么就利用`GameplayEffect`进行应用，并且使用`WhileActive`来添加特效，使用`OnRemove`来进行特效的移除。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-asg"></a>
### 4.9 GAS的全局信息管理者 - Ability System Globals
[`AbilitySystemGlobals`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UAbilitySystemGlobals/index.html)类保存这GAS的全局信息。其中大多数的变量可以在`DefaultGame.ini`中设置。通常你是不会用到这个类的，但是你需要知道它的存在。如果你需要去继承[`GameplayCueManager`](#concepts-gc-manager)或者[`GameplayEffectContext`](#concepts-ge-context)，你就的要在`AbilitySystemGlobals`上做一些文章。

为了继承`AbilitySystemGlobals`，要在`DefaultGame.ini`中设置类名：
```
[/Script/GameplayAbilities.AbilitySystemGlobals]
AbilitySystemGlobalsClassName="/Script/ParagonAssets.PAAbilitySystemGlobals"
```

<a name="concepts-asg-initglobaldata"></a>
#### 4.9.1 InitGlobalData()
从UE 4.24开始，就必须要调用`UAbilitySystemGlobals::Get().InitGlobalData()`来使用[`TargetData`](#concepts-targeting-data)，否则你会有`ScriptStructCache`相关的报错，并且客户端将从服务器断开。这个函数在项目中只需要调用一次。堡垒之夜是在`UAssetManager::StartInitialLoading()`中进行调用的，Paragon则是在`UEngine::Init()`。我发现将其放在`UAssetManager::StartInitialLoading()`是比较合适的位置，可以参看示例项目。这是很好的样板代码，如果我是你我就乖乖得将其拷贝到自己得项目中，以防出现`TargetData`相关的问题。

如果你在使用`AbilitySystemGlobals` 的`GlobalAttributeSetDefaultsTableNames`时遇到崩溃，你可以需要延后对`UAbilitySystemGlobals::Get().InitGlobalData()`的调用，就将堡垒之夜那样在`AssetManager`或者在`GameInstance`中。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p"></a>
### 4.10 预测 - Prediction
GAS带有开箱即用的客户端预测的支持；但是，这并不意味着它能够完美预测所有的事情。GAS中的客户端预测意味着客户端不需要去等待服务器的许可就可以去激活`GameplayAbility`并且应用`GameplayEffects`。它也可以“预测”服务器给与它的许可并且预测它所应用`GameplayEffects`的目标。 在客户端激活后，服务器会在网络延迟的时间后运行`GameplayAbility`并且告诉客户端它所作的预测是否正确。如果客户端的预测中的某一项是错误的，它会对错误的预测进行回滚直到与服务器匹配为止。

GAS相关的预测内容是在插件源码中的`GameplayPrediction.h`里。

Epic的思路是只去预测你需要应付的内容。例如，Paragon和Fortnite就不会去预测伤害的相关内容。最有可能的方案是，他们都使用了[`ExecutionCalculations`](#concepts-ge-ec)来进行伤害的处理，这也是无法进行预测的。当然，这并不是说你不可以去预测伤害或者跟其类似的东西，只是需要稍微进行一定的开发工作。

> ... we are also not all in on a "predict everything: seamlessly and automatically" solution. We still feel player prediction is best kept to a minimum (meaning: predict the minimum amount of stuff you can get away with).
>
> 我们也并不是希望搞出一个“完美、自动的预测”的解决方案。我们依旧认为，玩家的预测最好保持在一个最低的限度，这意思是，只去预测你需要应付的那些内容，越少越好。

*摘自Dave Ratti关于新的[Network Prediction Plugin](#concepts-p-npp)网络预测插件的叙述*

**什么是可以预测的：**

> * 技能激活
> *	触发事件
> *	GameplayEffect的应用：
>    * `Attribute`的修改（例外：`Executions`当前并不能够预测，只有`attribute modifiers`可以）
>    * `GameplayTag`的修改
> * `Gameplay Cue`事件（从可预测的GE里发出的以及从其自身发出的）
> * 动画蒙太奇
> * 移动（UE4内置的`UCharacterMovement`）

**什么是不可以预测的：**

> * `GameplayEffect`的移除 removal
> * `GameplayEffect`的周期性效果（比如dot效果）

*摘自`GameplayPrediction.h`*

虽然我们可以预测`GameplayEffect`的应用，我们却不能够预测`GameplayEffect`的移除。这个限制也有对应的解决之道，就是去预测我们希望移除的`GameplayEffect`的反效果。假设我们现在在预测一个速度减缓40%的效果。我们可以通过应用一个加速40%的速度buff来作为替代，最后再将两个效果同时移除。当然这并不是一劳永逸的完美的解决方法，我们还是需要有一个专门的针对`GameplayEffect`的移除的解决方案。Epic的Dave Ratti在[future iteration of GAS](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)后续的迭代中能够逐步支持这个。

因为我们不能预测`GameplayEffects`的移除，所以我们无法完美得预测`GameplayAbility`的冷却，因为并没有与之对应的相反效果的`GameplayEffect`。服务器复制的`Cooldown GE`将会存在于客户端，并且任何尝试去绕过这个（比如说使用`Minimal`复制模式）都会被服务器拒绝。这意味着高延迟的客户端需要花费更长的时间来告诉服务器需要去走冷却并接收服务器的`Cooldown GE`的移除。高延迟的玩家相较于低延迟的玩家，会有相对更低的技能发射频率，从而失去对战优势。Fortnite使用了自定义的方案来代替`Cooldown GEs`。

就预测伤害而言，我个人并不推荐，尽管大多数人一拿到GAS就首先去做的事情就是这个。我更不推荐去预测死亡。虽然你可以预测伤害，但是这可能会带来很多麻烦。如果你错误得预测了伤害，玩家就会看到敌人的生命值在来回来去得跳，如果这个错误发生在死亡的预测上的话，那可就更奇怪了。假设你错误得预测了`Character`的死亡，你所看到的表现是：敌人开始播放死亡动画（比如开始布娃娃系统的物理模拟），然后服务器将错误进行了纠正，敌人则要停止前面的模拟然后继续向你开火射击（译者注：在大部分情况下这都不是一种好的体验，在竞技型游戏中更是如此，想象一下你用枪击杀了对手，放下警戒，然后对手突然爬起来向你射击）。

**注意：** `Instant`类型的`GameplayEffects`（如`Cost GEs`），对于这类可以改变的你自己的`Attributes`的效果，是可以无缝得进行预测；而预测其他角色`Instant`类型的 `Attribute`的变化则可能表现出短暂的异常。对 `Instant`类型的`GameplayEffects`的预测被和`Infinite`类型的`GameplayEffects`归为一类进行处理的，对这类的预测错误，则可以进行回滚。当服务器的`GameplayEffect`应用时，可能会存在有两个相同的`GameplayEffect`，会导致在短短一个瞬间，`Modifier`被重复应用两次，亦或是完全不被应用。最终服务器都会将问题进行修正，但是对于玩家来说，如果这个瞬间被注意到了，那么就会对游玩体验造成影响。

GAS的预测解决方案试图去解决的一些问题
> 1. "Can I do this?" Basic protocol for prediction.
> 2. "Undo" How to undo side effects when a prediction fails.
> 3. "Redo" How to avoid replaying side effects that we predicted locally but that also get replicated from the server.
> 4. "Completeness" How to be sure we /really/ predicted all side effects.
> 5. "Dependencies" How to manage dependent prediction and chains of predicted events.
> 6. "Override" How to override state predictively that is otherwise replicated/owned by the server.
>
> 1. 我可以这样做吗？——预测的基本协议
> 2. 撤销——当预测失败时如何撤销副作用
> 3. 重播——如果避免重播我们本地预测和从服务器复制而来副作用
> 4. 完整性——如果确保我们确实预测了所有的副作用
> 5. 依赖性——如果管理依赖性预测和预测事件链条
> 6. 覆盖——如果预测性得覆盖服务器原本已复制/拥有得状态

*摘自`GameplayPrediction.h`*

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p-key"></a>

#### 4.10.1 预测键 - Prediction Key
GAS的预测的进行是基于一个名为`Prediction Key`预测键的概念，具体来说它是一个在客户端激活技能时所生成的一个整型标识符。

* 客户端激活一个`GameplayAbility`时生成一个预测键，这就是`Activation Prediction Key`。
* 客户端利用`CallServerTryActivateAbility()`发送这个预测键到服务器。
* 当预测键有效时，客户端将这个预测键添加给所有它应用的`GameplayEffects`。
* 客户端的预测键超出作用范围，在同一个`GameplayAbility`中进一步预测效果则需要一个新的[Scoped Prediction Window](#concepts-p-windows).


* 服务器从客户端接收预测键。
* 服务器将这个预测键添加给所有它应用的`GameplayEffects`。
* 服务器将预测键复制回客户端。


* 客户端从服务器接收复制的`GameplayEffects`，如果复制回来得到的`GameplayEffects`与客户端应用的`GameplayEffects`有着相同的预测键，那这意味着预测正确。在这个瞬时的时间点会同时有着两份`GameplayEffect`的拷贝，直到客户端将它预测的那个删除掉。
* 客户端从服务器接收预测键。即`Replicated Prediction Key`。这个预测键现在被标记为旧的。
* 客户端移除**所有的**标有旧的预测键的`GameplayEffects`。而由服务器复制得来的`GameplayEffects`将会被保留。任何客户端添加的但是却没有收到服务器返回版本的`GameplayEffects`都意味着预测的失败。

在`Activation Prediction Key`中以`Activation`开头的`GameplayAbilities`中的一个原子指令组“window”期间，预测键保准有效。对于这句话你可以直接理解成仅在一帧内有效。任何后续`AbilityTasks`中的回调都不会有有效的预测键，除非`AbilityTask`中包含有内置的`Synch Point`同步点，其会生成一个新的[Scoped Prediction Window](#concepts-p-windows)。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p-windows"></a>
#### 4.10.2 在技能中创建新的预测窗口 - Creating New Prediction Windows in Abilities
要预测`AbilityTasks`的回调中的更多的行为，我们需要用一个新的预测键创建一个新的范围预测窗口。这个有时候也称为是客户端和服务器之间的同步点。一些`AbilityTasks`，比如说所有的输入相关的那些，他们都内置了创建新的范围控制窗口的功能，意味着`AbilityTasks`的回调中的那些原子代码可以使用一个有效的预测键。其他的一些任务，如`WaitDelay`这种任务并没有内置的代码来为他的回调创建新的范围预测窗口。如果你希望去预测这样类型的`AbilityTask`，就必须手动调用`WaitNetSync`的`AbilityTask`，并选择`OnlyServerWait`。当客户端遇到带有`OnlyServerWait`的`WaitNetSync`时，它会基于`GameplayAbility`的激活预测键来生成一个新的范围预测键，利用RPC将其发送到服务器，再将其添加给它所应用的新的`GameplayEffects`。当服务器遇到带有`OnlyServerWait`的`WaitNetSync`，它将等待直到它从客户端接收到新的范围预测键才会继续。这个fa那位预测键的行为和激活预测键的行为基本一致 —— 应用到`GameplayEffects`并且复制回客户端，并标记为旧的。范围预测键在超出作用域时会失效，随即范围预测窗口关闭。所以在此强调，仅仅那些非延迟的原子操作可以使用新的范围预测键。

可以根据你的需求随意去创建范围预测窗口，不用担心数量。

如果你为你定义的`AbilityTasks`添加同步点的功能，可以参考输入的那些技能任务是如何将`WaitNetSync`的`AbilityTask`嵌入其中的。

**注意：**当使用`WaitNetSync`时，它会阻塞服务器上`GameplayAbility`的执行，直到接收到客户端的消息。这一点可能会被恶意的玩家利用，他们会黑掉游戏，延迟发送新的范围预测键。Epic就较少使用`WaitNetSync`，如果你需要使用它，Epic的建议是你自己构建一个新的`AbilityTask`，能够在一定的延迟后自动继续而不一定非得等待客户端的消息而造成阻塞。

示例项目在冲刺的`GameplayAbility`中使用了`WaitNetSync`为每次应用体力消减效果都创建了一个新的范围预测窗口，这样就可以针对这一点进行预测。理想状况下，在应用消耗和冷却时我们都想要一个有效的预测键。

如果你由一个预测的`GameplayEffect`在其所属客户端上播放了两次，你的预测键被标记为旧的，并且遭遇了“redo”重播的问题。你通常可以在应用`GameplayEffect`之前使用一个带有`OnlyServerWait`的`WaitNetSync来创建一个新的范围预测键。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p-spawn"></a>
#### 4.10.3 预测性得生成Actors - Predictively Spawning Actors
在客户端预测性得生成 `Actors`是一个进阶的议题。GAS并没有提供现成的功能来解决这个（`SpawnActor`的`AbilityTask`只是在服务端生成`Actor` ）。这里的核心问题是要在客户端和服务器都生成一个复制的`Actor`。

如果`Actor`只是装饰性的，不带有任何的游玩逻辑，那么最简单的解决方案就是重写`Actor`的`IsNetRelevantFor()`函数限制从服务器到所属客户端的复制。所属客户端仅需要其本地生成的版本，而其他客户端和服务器则是使用服务器的复制版本。
```c++
bool APAReplicatedActorExceptOwner::IsNetRelevantFor(const AActor * RealViewer, const AActor * ViewTarget, const FVector & SrcLocation) const
{
	return !IsOwnedBy(ViewTarget);
}
```

如果生成的`Actor`会影响游玩，比如说会产生伤害的子弹，那么你需要你就需要更加高阶的技巧，但是这并不包含在本文档内。可以在Epic Games的Github上查找UnrealTournament项目，其中实现了可预测得生成子弹。他是只在所属客户端上生成一个虚拟子弹，该虚拟子弹与服务器的复制子弹同步。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p-future"></a>

#### 4.10.4 技能系统中关于预测机制的未来规划 - Future of Prediction in GAS
`GameplayPrediction.h`中提到在未来，他们可能还会加入预测`GameplayEffect`的移除和预测周期性 `GameplayEffects`的功能。

Epic的Dave Ratti from Epic提过[expressed interest](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)，以解决预测冷却的`latency reconciliation`问题，从而让低延迟玩家比高延迟玩家更具备优势的问题得以解决。

Epic开发的新的[`Network Prediction` plugin](#concepts-p-npp)插件将会完美与GAS协调，就像`CharacterMovementComponent` 那样。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-p-npp"></a>

#### 4.10.5 网络预测插件 - Network Prediction Plugin
最近Epic发起了一项新的计划，即用新的`Network Prediction`替换掉`CharacterMovementComponent`。这个插件仍然处于早期的开发阶段，但是关于其的讨论在Unreal Engine GitHubs上已经非常热烈。现在还不太好讲未来在哪个版本我们会正式体验到。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-targeting"></a>
### 4.11 目标 - Targeting

<a name="concepts-targeting-data"></a>
#### 4.11.1 目标数据 - Target Data
[`FGameplayAbilityTargetData`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/FGameplayAbilityTargetData/index.html)是一个通用的结构体，专为那些可以在网络上传递的目标数据使用。`TargetData`通常会保存`AActor`/`UObject`的引用，`FHitResults`以及其他一些通用的位置/方向/原点的信息。当然，你也可以对他进行派生，把你想要的任何东西塞到它里面，这是一种简单的通过`GameplayAbilities`来在客户端和服务器之间传递数据的方式。`FGameplayAbilityTargetData`结构体需要去派生出子类来进行使用，不要直接去进行使用。`GameplayAbilityTargetTypes.h`中罗列了一些为`GAS`准备的开箱即用的的`FGameplayAbilityTargetData`子类结构体。

`TargetData`通常是由[`Target Actors`](#concepts-targeting-actors)产生，或是手动创建，并且由[`AbilityTasks`](#concepts-at)和[`GameplayEffects`](#concepts-ge)通过[`EffectContext`](#concepts-ge-context)来进行使用。当`TargetData`作为`EffectContext`的结果时，[`Executions`](#concepts-ge-ec)， [`MMCs`](#concepts-ge-mmc)，[`GameplayCues`](#concepts-gc)以及[`AttributeSet`](#concepts-as)的一些默认方法都可以对其进行访问。

通常我们不会去直接传递`FGameplayAbilityTargetData`，相对的，我们会去利用[`FGameplayAbilityTargetDataHandle`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/FGameplayAbilityTargetDataHandle/index.html)，其内部存有一个`FGameplayAbilityTargetData`的数组指针。这个中间结构体会为`TargetData`的多态性提供支持。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-targeting-actors"></a>
#### 4.11.2 目标Actor - Target Actors
`GameplayAbilities`是利用`WaitTargetData`的`AbilityTask`来生成[`TargetActors`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/AGameplayAbilityTargetActor/index.html)，以显示和捕捉世界中的目标信息。`TargetActors`可以使用[`GameplayAbilityWorldReticles`](#concepts-targeting-reticles)来显示当前的目标。在目标确认后，目标的信息会以[`TargetData`](#concepts-targeting-data)的形式返回，然后可以进一步传递给`GameplayEffects`。

`TargetActors`是派生自`AActor`，因此他们可以拥有任意类型的渲染组件（比如static mesh静态网格体或者decal贴花），来表示他们的位置以及他们如何进行目标选择。静态网格体可以用来显示你的角色所构建的物体。贴花可以用来显示地面上的相应作用区域。示例工程中使用了一个带有地面贴花的[`AGameplayAbilityTargetActor_GroundTrace`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/AGameplayAbilityTargetActor_Grou-/index.html)，用以表示Meteor流星技能的伤害效果区域。`TargetActors`也可以不去显示任何东西。例如，[GASShooter](https://github.com/tranek/GASShooter)项目中的枪械弹药需要使用射线检测目标，但是这些一瞬间的检测并不需要显示任何东西。

`TargetActors`会利用基本的射线或者碰撞检测来捕获目标信息，并且可以根据`TargetActor`的具体实现将结果从`FHitResults`或者`AActor`数组转换为`TargetData`。`WaitTargetData`的`AbilityTask`可以根据`TEnumAsByte<EGameplayTargetingConfirmation::Type> ConfirmationType`的参数来决定目标在什么时候确定。当**不是**`TEnumAsByte<EGameplayTargetingConfirmation::Type::Instant`时，`TargetActor`通常会在`Tick()`中去作射线碰撞检测，并且根据其具体实现去将其位置更新到`FHitResult`中。尽管这是一个在`Tick()`中执行的射线碰撞检测，但是实际上你并不太需要为此担心性能问题，意味它并不需要进行网络复制，而且你通常也同一时间也不会有超过一个的`TargetActor`（这说的是更通常的情况，当然系统并没有限制同一时间的数量）。但是这件事你还是需要知道的，有些时候一些复杂的`TargetActors`可能会在`Tick()`中做非常多的操作，比如GASShooter项目中火箭发射器的第二个技能。尽管在客户端`Tick()`是非常灵敏的（即其执行次数高于服务端），但是当其开始对性能产生较大影响时你可能需要考虑降低`TargetActor`的`Tick()`频率。在`TEnumAsByte<EGameplayTargetingConfirmation::Type::Instant`时，`TargetActor`会瞬间生成，产生`TargetData`数据，然后销毁。这样，就永远不会涉及到`Tick()`的调用。

| `EGameplayTargetingConfirmation::Type` | 目标确认的时机                                               |
| -------------------------------------- | ------------------------------------------------------------ |
| `Instant`                              | 没有特殊逻辑或者用户输入决定何时去“开火”的情况下，目标的选取会立即发生。 |
| `UserConfirmed`                        | 在用户通过技能绑定到`Confirm`的输入进而确认了目标亦或是通过调用`UAbilitySystemComponent::TargetConfirm()`表明目标的确认时，目标的选取就发生了。`TargetActor`同样也会去相应`Cancel`的输入或者`UAbilitySystemComponent::TargetCancel()`的调用从而取消目标的选择。 |
| `Custom`                               | 技能中通过调用`UGameplayAbility::ConfirmTaskByInstanceName()`来决定具体的`targeting data`准备好的时机。同样，`TargetActor`也会响应`UGameplayAbility::CancelTaskByInstanceName()`来取消目标的选择。 |
| `CustomMulti`                          | 同上，其不同之处在于数据生成时不会去结束掉这个`AbilityTask`。 |

并不是每个`TargetActor`都支持所有的`EGameplayTargetingConfirmation::Type`。例如，`AGameplayAbilityTargetActor_GroundTrace`就不支持`Instant`类型的确认。

`WaitTargetData`的`AbilityTask`中有一个`AGameplayAbilityTargetActor`类作为参数，其会在每次这个技能任务激活时生成一个`TargetActor`的实例，而在`AbilityTask`结束时销毁这个实例。`WaitTargetDataUsingActor`的`AbilityTask`可以传入一个已经生成好的`TargetActor`，同样会在技能任务结束时销毁该实例。这两种`AbilityTasks`都是低效的，在每次使用时都需要生成或者需要一个生成好的`TargetActor`。对于开发游戏原型来说他们是可以开箱即用的，但是正式的开发可能就需要进行进一步优化，比如某些情况下你需要不间断得生成`TargetData`，像那种自动来复枪。GASShooter项目中有一个自定义的[`AGameplayAbilityTargetActor`](https://github.com/tranek/GASShooter/blob/master/Source/GASShooter/Public/Characters/Abilities/GSGATA_Trace.h)的派生类，以及一个全新的[`WaitTargetDataWithReusableActor`](https://github.com/tranek/GASShooter/blob/master/Source/GASShooter/Public/Characters/Abilities/AbilityTasks/GSAT_WaitTargetDataUsingActor.h) 的`AbilityTask`，在这个任务中你可以重复使用`TargetActor`而无需一直去处理销毁的问题。

`TargetActors`在默认情况下是不进行复制的。但是，如果你的游戏中有需要向其他玩家公开你所选目标的这样的需求的话，就需要用到`TargetActors`的复制了，当然这完全是可以实现的。`WaitTargetData`的`AbilityTask`中也包含了利用RPC和服务器进行通信的默认功能。如果`TargetActor`的`ShouldProduceTargetDataOnServer`属性设置为`false`，那么客户端在确定目标时会通过`UAbilityTask_WaitTargetData::OnTargetDataReadyCallback()`中的`CallServerSetReplicatedTargetData()`来将`TargetData`利用RPC发送给服务器。如果`ShouldProduceTargetDataOnServer`是`true`，客户端将发送一个通用的确认事件，即`EAbilityGenericReplicatedEvent::GenericConfirm`，在`UAbilityTask_WaitTargetData::OnTargetDataReadyCallback()`利用RPC方法传递给服务器，然后服务器将会基于接收到的RPC来做射线和碰撞检测，进而在服务器上生成数据。如果客户端取消了目标选择，它会发出一个通用的取消事件，即`EAbilityGenericReplicatedEvent::GenericCancel`，在`UAbilityTask_WaitTargetData::OnTargetDataCancelledCallback`中利用RPC的方法传递给客户端。如你所见，`TargetActor`和`WaitTargetData`的`AbilityTask`上都存在着非常多的委托。`TargetActor`响应输入并且广播`TargetData`准备就绪、确认或者是取消的委托。`WaitTargetData`监听`TargetActor`的`TargetData`准备就绪、确认以及取消的委托，并且将这些信息返回给`GameplayAbility`和服务器。如果你发送`TargetData`给服务器，你可能希望在服务器上进行确认操作，以便确认`TargetData`是否是合理的（防止作弊的发生）。直接在服务器上生成`TargetData`的话会完全避免这个问题，但是可能会导致所属客户端的预测的失败。

根据你所使用的`AGameplayAbilityTargetActor`的特定派生类的不同，`WaitTargetData`的`AbilityTask`节点上也会相应的暴露不同的`ExposeOnSpawn`参数。其中共享的部分有：

| 共享的 `TargetActor` 上的参数 | 定义                                                         |
| ----------------------------- | ------------------------------------------------------------ |
| Debug                         | 如果为`true`时，那么在非发行版的版本中它会去帮我们绘制所有的射线和碰撞检测的信息。请记住，非`Instant`的`TargetActors`将会在`Tick()`中去做检测，那么自然，这些绘制内容也是在`Tick()`中完成的（译者注：相对来说射线检测的消耗还可以接收，但是其绘制的消耗可能会非常爆炸）。 |
| Filter                        | [可选] 一个特定的结构体，用来在射线检测时对`Actors`进行过滤。通常的用法是去过滤玩家的`Pawn`，只允许某个特定类型的目标。参考[Target Data Filters](#concepts-target-data-filters)小节获取更多相关内容。 |
| Reticle Class                 | [可选] `TargetActor`所要创建的`AGameplayAbilityWorldReticle`的派生类。 |
| Reticle Parameters            | [可选] 配置光标。参考[Reticles](#concepts-targeting-reticles)的小结。 |
| Start Location                | 一个特定的结构体，其中包含了射线检测的开始位置的信息。通常会是玩家的视口、武器枪口，亦或是`Pawn`的位置。 |

默认的`TargetActor`类中，`Actors`只有在被射线检测到或者碰撞检测到才是有效的目标。如果他们离开射线和碰撞检测的范围（他们移动了或者你的移开了视线），那么他们就不再有效。如果你希望`TargetActor`记住之前的有效目标（们），那么你需要去自己实现自定义的`TargetActor`类。我称其为永久目标，因为他们的存在会一直持续到`TargetActor`接收到目标的确认和取消事件，或者是`TargetActor`找到了一个新的有效目标，还有或者是目标不再有效（即其被销毁了）。GASShooter项目中将这种方式应用到火箭发射器的第二个技能上。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-target-data-filters"></a>
#### 4.11.3 目标数据过滤器 - Target Data Filters
通过使用`Make GameplayTargetDataFilter`和`Make Filter Handle`节点，你可以过滤玩家的`Pawn`或者只去选择某个特定的类。如果你需要更多高级的过滤效果，你可以在`FGameplayTargetDataFilter`基础上进一步派生，并且重写其中的`FilterPassesForActor`函数。
```c++
USTRUCT(BlueprintType)
struct GASDOCUMENTATION_API FGDNameTargetDataFilter : public FGameplayTargetDataFilter
{
	GENERATED_BODY()

	/** Returns true if the actor passes the filter and will be targeted */
	virtual bool FilterPassesForActor(const AActor* ActorToBeFiltered) const override;
};
```

但是，这并不会直接令其在`Wait Target Data`节点上生效，因为它还需要一个`FGameplayTargetDataFilterHandle`。还需要再构建一个新的自定义的`Make Filter Handle`来收扩展出来的派生类：
```c++
FGameplayTargetDataFilterHandle UGDTargetDataFilterBlueprintLibrary::MakeGDNameFilterHandle(FGDNameTargetDataFilter Filter, AActor* FilterActor)
{
	FGameplayTargetDataFilter* NewFilter = new FGDNameTargetDataFilter(Filter);
	NewFilter->InitializeFilterContext(FilterActor);

	FGameplayTargetDataFilterHandle FilterHandle;
	FilterHandle.Filter = TSharedPtr<FGameplayTargetDataFilter>(NewFilter);
	return FilterHandle;
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-targeting-reticles"></a>
#### 4.11.4 游戏技能的世界标线 - Gameplay Ability World Reticles
[`AGameplayAbilityWorldReticles`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/AGameplayAbilityWorldReticle/index.html)，即`Reticles`，当你利用非`Instant`确定出[`TargetActors`](#concepts-targeting-actors)，它会帮你从视觉表现方面显示你当前正在选择的目标。`TargetActors`会负责`Reticles`从生成到销毁的整个生命周期。`Reticles`本质上是`AActors`，因此他们可以使用任意类型的可视化组件。[GASShooter](https://github.com/tranek/GASShooter)实现了一个它的常见的用法，即使用`WidgetComponent`来在屏幕空间中显示一个UMG Widget类型的控件（总是朝向玩家摄像机）。`Reticles`并不知道他们的目标是具体哪一个`AActor`，但是你可以在自定义的`TargetActor`进一步实现这个功能。`TargetActors`通常会在每个`Tick()`内去更新`Reticle`的位置到目标的位置（译者注：目标位置的世界控空间位置转到屏幕空间的二维空间位置）。

GASShooter中使用`Reticles`来显示被火箭发射器的第二个技能锁定的目标。敌人身上的红色的指示图标就是`Reticle`。而相同样式的白色的那个则是火箭发射器的准星。
![Reticles in GASShooter](https://github.com/tranek/GASDocumentation/raw/master/Images/gameplayabilityworldreticle.png)

`Reticles`提供了一些对设计者可能有用的`BlueprintImplementableEvents`（他们就是被设计来在蓝图中进行拓展开发的）：

```c++
/** Called whenever bIsTargetValid changes value. */
UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void OnValidTargetChanged(bool bNewValue);

/** Called whenever bIsTargetAnActor changes value. */
UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void OnTargetingAnActor(bool bNewValue);

UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void OnParametersInitialized();

UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void SetReticleMaterialParamFloat(FName ParamName, float value);

UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void SetReticleMaterialParamVector(FName ParamName, FVector value);
```

`Reticles`也可以使用由`TargetActor`提供的[`FWorldReticleParameters`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/FWorldReticleParameters/index.html)来进行配置。默认的结构体只提供了一个变量`FVector AOEScale`。尽管你可以去派生这个结构体，但是`TargetActor`只会去接收默认的基类结构体。从设计层面看，默认的`TargetActor`在`FWorldReticleParameters`上的限制可能看起来显得有些目光短浅。但是，如果你自定义了自己的`TargetActor`，你也可以提供你自己自定义的标线参数结构体，然后在生成他们的时候手动将其传递给你的`AGameplayAbilityWorldReticles`的派生类中。

`Reticles`默认情况下不会被复制，但是如果你的游戏有类似的需求，比如给其他玩家显示本地玩家锁定的敌人是你的游戏的设计之一的话，也可以将复制的选项打开。

默认的`TargetActors`中，`Reticles`只会在当前有效的目标上显示。例如，如果你使用了一个`AGameplayAbilityTargetActor_SingleLineTrace`来检测一个目标，那么`Reticle`将只会在目标直接暴露在检测路径上时出现。如果你看向别的什么地方，那么此时敌人就不再是一个有效的目标，那么`Reticle`就自动消失了。如果你希望`Reticle`停留在最后一个有效目标身上，你就需要自定义你的`TargetActor`，让其记住最后一个有效目标并保留`Reticle`到目标身上。我建议是将他们作为永久目标，因为他们会一直存在直到`TargetActor`接收到确认或者取消的指令，或者`TargetActor`通过检测找到了一个新的有效目标，或者目标不再有效（被销毁了）。GASShooter项目中就应用这个方案到火箭发射器的第二个技能上了。

**[⬆ Back to Top](#table-of-contents)**

<a name="concepts-targeting-containers"></a>
#### 4.11.5 游戏效果容器的目标 - Gameplay Effect Containers Targeting
[`GameplayEffectContainers`](#concepts-ge-containers)中提供了一个可选的，高效的生成[`TargetData`](#concepts-targeting-data)的方法。在客户端和服务器上应用`EffectContainer`时就会立马进行目标的选择。这个方法要远比[`TargetActors`](#concepts-targeting-actors)的方式更加高效，因为它是直接运行在目标选择的对象的CDO（译者注：Class Default Object，可以理解为类的一个默认单例对象）上的，相对于需要去生成和销毁的`Actors`对象来说当然更加高效。但是它会缺少玩家的输入，没有确认取消的过程，也不能够从客户端往服务器发送数据（因为它在两端都会执行）。对于立即触发的射线或者碰撞检测来说这非常有效。Epic的[Action RPG Sample Project](https://www.unrealengine.com/marketplace/en-US/product/action-rpg)项目中，在其容器中包含了两种类型的目标选择——选择技能的释放者，以及从某个事件中获取到`TargetData`。它也在蓝图中实现了一个去做球体的轨迹检测。你可以派生`URPGTargetType`来制作你自己的目标类型。

**[⬆ Back to Top](#table-of-contents)**

<a name="cae"></a>
## 5. 常见的技能和效果 - Commonly Implemented Abilities and Effects

<a name="cae-stun"></a>

### 5.1 眩晕 - Stun

通常在眩晕效果下，我们希望取消`Character`所有的处于激活状态的`GameplayAbilities`，同时阻止新的`GameplayAbility`的激活甚至是移动。示例项目中的流星`GameplayAbility`就会给其命中的目标施加一个眩晕的效果。

要取消目标身上激活的`GameplayAbilities`，我们可以在[相应`GameplayTag`被添加时](#concepts-gt-change)时调用`AbilitySystemComponent->CancelAbilities()`。

要实现阻止新的`GameplayAbilities`的激活的效果，可以在其他技能上把眩晕的`GameplayTag`设置到他们的[`Activation Blocked Tags`的`GameplayTagContainer`上](#concepts-ga-tags)。

要阻止角色的移动，我们可以重写`CharacterMovementComponent`的`GetMaxSpeed()`函数，将其当所有者拥有眩晕的`GameplayTag`时令这个函数返回0。

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-sprint"></a>

### 5.2 冲刺 - Sprint

示例项目中还提供了一个关于冲刺的例子，即当`Left Shift`按下时角色可以跑得更快一些。

快速移动可以由`CharacterMovementComponent`负责预测处理，具体就是通过网络发送一个标记到服务器。参考`GDCharacterMovementComponent.h/cpp`。

`GA`会处理`Left Shift`对应的输入响应，通知角色移动组件去开启或者停止冲刺，并且预测性得消耗体力值。参考`GA_Sprint_BP`。

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-ads"></a>

### 5.3 瞄准 - Aim Down Sights

示例项目中，瞄准的做法和冲刺的做法基本一致，只是把加速换成了减速。

具体参阅`GDCharacterMovementComponent.h/cpp`中关于减速的内容。

参阅`GA_AimDownSight_BP`中关于输入的处理的内容。当然，瞄准并不会消耗体力。

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-ls"></a>
### 5.4 生命偷取 - Lifesteal
我将生命偷取内置在伤害计算的[`ExecutionCalculation`](#concepts-ge-ec)内。`GameplayEffect`会有一个专门的`GameplayTag`，比如`Effect.CanLifesteal`这样的。`ExecutionCalculation`会去检查`GameplayEffectSpec`是否有这样的一个`GameplayTag`。如果这个`GameplayTag`存在，那么`ExecutionCalculation` [会去创建一个动态的`Instant`类型的`GameplayEffect`](#concepts-ge-dynamic)，并且给它一个增加生命值的`modifier`，然后把他应用到`Source`的`ASC`。

```c++
if (SpecAssetTags.HasTag(FGameplayTag::RequestGameplayTag(FName("Effect.Damage.CanLifesteal"))))
{
	float Lifesteal = Damage * LifestealPercent;

	UGameplayEffect* GELifesteal = NewObject<UGameplayEffect>(GetTransientPackage(), FName(TEXT("Lifesteal")));
	GELifesteal->DurationPolicy = EGameplayEffectDurationType::Instant;

	int32 Idx = GELifesteal->Modifiers.Num();
	GELifesteal->Modifiers.SetNum(Idx + 1);
	FGameplayModifierInfo& Info = GELifesteal->Modifiers[Idx];
	Info.ModifierMagnitude = FScalableFloat(Lifesteal);
	Info.ModifierOp = EGameplayModOp::Additive;
	Info.Attribute = UPAAttributeSetBase::GetHealthAttribute();

	SourceAbilitySystemComponent->ApplyGameplayEffectToSelf(GELifesteal, 1.0f, SourceAbilitySystemComponent->MakeEffectContext());
}
```

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-random"></a>
### 5.5 在服务端和客户端上生成随机数字 - Generating a Random Number on Client and Server
有些时候你需要在`GameplayAbility`里生成随机数，比如子弹后坐力和扩散等。客户端和服务器当然需要同样的随机数。为了实现这个效果，我们必须在`GameplayAbility`激活的时候设置相同的`random seed`。每次去激活`GameplayAbility`的时候你都需要设置`random seed`，防止客户端预测激活失败并且随机数序列和服务器不同步。

| 随机数种子设置方法                                 | 描述                                                         |
| -------------------------------------------------- | ------------------------------------------------------------ |
| 使用激活预测键                                     | The `GameplayAbility` activation prediction key is an int16 guaranteed to be synchronized and available in both the client and server in the `Activation()`. You can set this as the `random seed` on both the client and the server. The downside to this method is that the prediction key always starts at zero each time the game starts and consistently increments the value to use between generating keys. This means each match will have the exact same random number sequence. This may or may not be random enough for your needs. |
| 当你激活 `GameplayAbility`时，通过事件负载发送种子 | Activate your `GameplayAbility` by event and send the randomly generated seed from the client to the server via the replicated event payload. This allows for more randomness but the client could easily hack their game to only send the same seed value every time. Also activating `GameplayAbilities` by event will prevent them from activating from the input bind. |

If your random deviation is small, most players won't notice that the sequence is the same every game and using the activation prediction key as the `random seed` should work for you. If you're doing something more complex that needs to be hacker proof, perhaps using a `Server Initiated` `GameplayAbility` would work better where the server can create the prediction key or generate the `random seed` to send via an event payload.

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-crit"></a>
### 5.6 暴击 - Critical Hits
我将暴击效果的实现内置在了[`ExecutionCalculation`](#concepts-ge-ec)中。`GameplayEffect`会有一个专门的`GameplayTag`，比如说像`Effect.CanCrit`。`ExecutionCalculation`会去检查`GameplayEffectSpec`是否拥有这个`GameplayTag`。如果`GameplayTag`存在的话，`ExecutionCalculation`会去生成一个随机的数字对应着暴击几率（从`Source`中取到的对应的`Attribute`值），然后加到暴击伤害（另外一个从`Source`中取到的对应的`Attribute`值）中。因为我并没有去预测伤害，我并不需要去担心随机数生成的同步问题，因为`ExecutionCalculation`只会在服务器上运行。如果你希望预测性得去使用`MMC`来处理伤害计算，那么你就需要从`GameplayEffectSpec->GameplayEffectContext->GameplayAbilityInstance`中拿到`random seed`的引用。

可以参考[GASShooter](https://github.com/tranek/GASShooter)项目中是如何实现爆头的。其本质就是本小节的内容，当然它并没有暴击率的设计，而是去检查`FHitResult`中命中的骨头的名称。

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-nonstackingge"></a>
### 5.7 非可叠加游戏效果（仅取对目标影响最大者的游戏效果） - Non-Stacking Gameplay Effects but Only the Greatest Magnitude Actually Affects the Target
Paragon里的减速效果并不会堆叠。每一个减速实例正常应用并且正常追踪其生命周期，但是只有值最大的那一个才会在真正影响`Character`。GAS利用`AggregatorEvaluateMetaData`将这个效果做得开箱即用。可以参考[`AggregatorEvaluateMetaData()`](#concepts-as-onattributeaggregatorcreated)中的具体实现。

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-paused"></a>

### 5.8 游戏暂停时生成目标数据 - Generate Target Data While Game is Paused
如果你需要为你的玩家在利用`WaitTargetData` `AbilityTask`来生成[`TargetData`](#concepts-targeting-data)时暂停游戏，我建议不去暂停游戏，而是使用`slomo 0`。

**[⬆ Back to Top](#table-of-contents)**

<a name="cae-onebuttoninteractionsystem"></a>
### 5.9 单按键交互系统 - One Button Interaction System
[GASShooter](https://github.com/tranek/GASShooter)实现了一个一键交互系统，其中玩家可以通过按下"E"来与可交互物进行交互，比如复活玩家，打开武器箱，以及打开或者关闭滑动门。

**[⬆ Back to Top](#table-of-contents)**

<a name="debugging"></a>
## 6. 游戏技能系统的调试 - Debugging GAS
在调试GAS相关的问题时，你经常回想知道：
> * "我的属性们的值是多少？"
> * "我身上有哪些gameplay tags？"
> * "当前我有哪些gameplay effects？"
> * "我赋予了哪些技能，其中哪些还在运行，而哪些被阻止激活？"

对于这些问题，GAS带有两项运行时的技术来应对——[`showdebug abilitysystem`](#debugging-sd)以及 [`GameplayDebugger`](#debugging-gd)。

**提示：**UE4会去优化C++代码，这会使某些函数难以进行调试。当你深入追踪你的代码时你就有概率会遇到这种情况。如果设置Visual Studio解决方案的配置为`DebugGame Editor`仍然无法让你尽情对代码进行调试，你可以通过`PRAGMA_DISABLE_OPTIMIZATION_ACTUAL`和`PRAGMA_ENABLE_OPTIMIZATION_ACTUAL`宏来对特定方法的优化进行限制。这个方式不能对插件代码使用，除非你从源代码对插件进行构建。对于内联函数可能会也可能不会起作用，具体取决于其实际的功能和位置。无比在调试完成后移除这些宏代码。

```c++
PRAGMA_DISABLE_OPTIMIZATION_ACTUAL
void MyClass::MyFunction(int32 MyIntParameter)
{
	// My code
}
PRAGMA_ENABLE_OPTIMIZATION_ACTUAL
```

**[⬆ Back to Top](#table-of-contents)**

<a name="debugging-sd"></a>
### 6.1 showdebug abilitysystem
在游戏控制台键入`showdebug abilitysystem`即可实现。这项特性一共分为了3页。每页上都会显示你当前拥有的`GameplayTags`。键入`AbilitySystem.Debug.NextCategory`可以按顺序查看3页的内容。

第一页显示你的`Attributes`的`CurrentValue`：
![First Page of showdebug abilitysystem](https://github.com/tranek/GASDocumentation/raw/master/Images/showdebugpage1.png)

第二页显示了你身上所有的`Duration`和`Infinite`的`GameplayEffects`，其具体的叠加的数量，他们赋予了哪些`GameplayTags`，以及他们赋予了哪些`Modifiers`。
![Second Page of showdebug abilitysystem](https://github.com/tranek/GASDocumentation/raw/master/Images/showdebugpage2.png)

第三页显示了所有的赋予给你的`GameplayAbilities`，他们当前的运行状态，他们是否被阻止进行激活，以及当前运行的`AbilityTasks`的状态。
![Third Page of showdebug abilitysystem](https://github.com/tranek/GASDocumentation/raw/master/Images/showdebugpage3.png)

当使用`PageUp`和`PageDown`进行目标间的切换时，当前页面只会显示本地控制的`Character`的`ASC`的数据。而shi用`AbilitySystem.Debug.NextTarget`和`AbilitySystem.Debug.PrevTarget`将会切换显示到对应目标的`ASCs`的数据，但是可惜的是，绿色的长方体选中框不会随着调试信息的变化而改变，所以可能在定位到底是哪一个`ASC`时会有些麻烦。这个bug也已经提交https://issues.unrealengine.com/issue/UE-90437。

**注意：**为使`showdebug abilitysystem`正常工作，需要在`GameMode`里选择一个真正的HUD。负责命令会丢失，并且返回”Unknown Command“。

**[⬆ Back to Top](#table-of-contents)**

<a name="debugging-gd"></a>
### 6.2 游戏调试器 - Gameplay Debugger
GAS为Gameplay Debugger也提供了一些功能选项。可以通过单引号（'）访问`Gameplay Debugger`。按数字键盘上的3可以激活`Abilities category`。这个分类根据你的插件可能也会有所不同。如果你的键盘是那种没有小键盘的笔记本电脑，那么你可以在项目设置了修改响应的按键绑定。

当你希望看到别的`Characters`身上的`GameplayTags`，`GameplayEffects`，以及`GameplayAbilities`式，就可以使用`Gameplay Debugger`。不过可惜的是，它并不会显示目标的`Attributes`的`CurrentValue`。它会以你屏幕正中的`Character`为目标。有两种方式可以更改选定的目标：在编辑器的`World Outliner`中选定你的目标，或者看向其他的`Character`然后按下单引号（‘）。当前选中的目标角色会在其上方有一个大大的红色圆圈。

![Gameplay Debugger](https://github.com/tranek/GASDocumentation/raw/master/Images/gameplaydebugger.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="debugging-log"></a>
### 6.3 游戏技能系统的日志 - GAS Logging
GAS的源码包含很多的日志语句，分别应对各种级别（错误，警告，日志）。你应该会经常在`ABILITY_LOG()`中见到这些语句。默认的日志级别是`Display`。其他高于这个级别的日志则在默认情况下不会在控制台进行显示。

要修改显示的日志等级，可以在你的控制台中键入：

```
log [category] [verbosity]
```

例如，为了激活`ABILITY_LOG()`语句，你可以在控制台键入：
```
log LogAbilitySystem VeryVerbose
```

重置回默认，则键入：
```
log LogAbilitySystem Display
```

要显示所有级别的日志，键入：
```
log list
```

GAS相关的日志类别：

| 日志类别                  | 默认显示等级 |
| ------------------------- | ------------ |
| LogAbilitySystem          | Display      |
| LogAbilitySystemComponent | Log          |
| LogGameplayCueDetails     | Log          |
| LogGameplayCueTranslator  | Display      |
| LogGameplayEffectDetails  | Log          |
| LogGameplayEffects        | Display      |
| LogGameplayTags           | Log          |
| LogGameplayTasks          | Log          |
| VLogAbilitySystem         | Display      |

更多信息参见[Wiki on Logging](https://www.ue4community.wiki/Legacy/Logs,_Printing_Messages_To_Yourself_During_Runtime)。

**[⬆ Back to Top](#table-of-contents)**

<a name="optimizations"></a>
## 7. 优化 - Optimizations

<a name="optimizations-abilitybatching"></a>
### 7.1 技能批处理 - Ability Batching
[`GameplayAbilities`](#concepts-ga) that activate, optionally send `TargetData` to the server, and end all in one frame can be [batched to condense two-three RPCs into one RPC](#concepts-ga-batching). These types of abilities are commonly used for hitscan guns.

<a name="optimizations-gameplaycuebatching"></a>
### 7.2 游戏反馈批处理 - Gameplay Cue Batching
If you're sending many [`GameplayCues`](#concepts-gc) at the same time, consider [batching them into one RPC](#concepts-gc-batching). The goal is to reduce the number of RPCs (`GameplayCues` are unreliable NetMulticasts) and send as little data as possible.

<a name="optimizations-ascreplicationmode"></a>
### 7.3 技能系统组件的复制模式 - AbilitySystemComponent Replication Mode
By default, the [`ASC`](#concepts-asc) is in [`Full Replication Mode`](#concepts-asc-rm). This will replicate all [`GameplayEffects`](#concepts-ge) to every client (which is fine for a single player game). In a multiplayer game, set the player owned `ASCs` to `Mixed Replication Mode` and AI controlled characters to `Minimal Replication Mode`. This will replicate `GEs` applied on a player character to only replicate to the owner of that character and `GEs` applied on AI controlled characters will never replicate `GEs` to clients. [`GameplayTags`](#concepts-gt) will still replicate and [`GameplayCues`](#concepts-gc) will still unreliable NetMulticast to all clients, regardless of the `Replication Mode`. This will cut down on network data from `GEs` being replicated when all clients don't need to see them.

<a name="optimizations-attributeproxyreplication"></a>
### 7.4 属性代理的复制 - Attribute Proxy Replication
In large games with many players like Fortnite Battle Royale (FNBR), there will be a lot of [`ASCs`](#concepts-asc) living on always-relevant `PlayerStates` replicating a lot of [`Attributes`](#concepts-a). To optimize this bottleneck, Fortnite disables the `ASC` and its [`AttributeSets`](#concepts-as) from replicating altogether on **simulated player-controlled proxies** in the `PlayerState::ReplicateSubobjects()`. Autonomous proxies and AI controlled `Pawns` still fully replicate according to their [`Replication Mode`](#concepts-asc-rm). Instead of replicating `Attributes` on the `ASC` on the always-relevant `PlayerStates`, FNBR uses a replicated proxy structure on the player's `Pawn`. When `Attributes` change on the server's `ASC`, they are changed on the proxy struct too. The client receives the replicated `Attributes` from the proxy struct and pushes the changes back into its local `ASC`. This allows `Attribute` replication to use the `Pawn`'s relevancy and `NetUpdateFrequency`. This proxy struct also replicates a small white-listed set of `GameplayTags` in a bitmask. This optimization reduces the amount of data over the network and allows us to take advantage of pawn relevancy. AI controlled `Pawns` have their `ASC` on the `Pawn` which already uses its relevancy so this optimization is not needed for them.

> I’m not sure if it is still necessary with other server side optimizations that have been done since then (Replication Graph, etc) and it is not the most maintainable pattern.

*Dave Ratti from Epic's answer to [community questions #3](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)*

<a name="optimizations-asclazyloading"></a>
### 7.5 技能系统组件的延迟加载 - ASC Lazy Loading
Fortnite Battle Royale (FNBR) has a lot of damageable `AActors` (trees, buildings, etc) in the world, each with an [`ASC`](#concepts-asc). This can add up in memory cost. FNBR optimizes this by lazily loading `ASCs` only when they're needed (when they first take damage by a player). This reduces overall memory usage since some `AActors` may never be damaged in a match.

**[⬆ Back to Top](#table-of-contents)**

<a name="qol"></a>
## 8. 建议 - Quality of Life Suggestions

<a name="qol-gameplayeffectcontainers"></a>
### 8.1 游戏效果容器 - Gameplay Effect Containers
[GameplayEffectContainers](#concepts-ge-containers) combine [`GameplayEffectSpecs`](#concepts-ge-spec), [`TargetData`](#concepts-targeting-data), [simple targeting](#concepts-targeting-containers), and related functionality into easy to use structures. These are great for transfering `GameplayEffectSpecs` to projectiles spawned from an ability that will then apply them on collision at a later time.

<a name="qol-asynctasksascdelegates"></a>
### 8.2 蓝图异步任务绑定到技能系统组件的委托 - Blueprint AsyncTasks to Bind to ASC Delegates
To increase designer-friendly iteration times, especially when designing UMG Widgets for UI, create Blueprint AsyncTasks (in C++) to bind to the common change delegates on the `ASC` directly from your UMG Blueprint graphs. The only caveat is that they must be manually destroyed (like when the widget is destroyed) otherwise they will live in memory forever. The Sample Project includes three Blueprint AsyncTasks.

Listen for `Attribute` changes:

![Listen for Attributes Changes BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/attributeschange.png)

Listen for cooldown changes:

![Listen for Cooldown Change BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownchange.png)

Listen for `GE` stack changes:

![Listen for GameplayEffect Stack Change BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/gestackchange.png)

**[⬆ Back to Top](#table-of-contents)**

<a name="troubleshooting"></a>
## 9. 答疑 - Troubleshooting

<a name="troubleshooting-notlocal"></a>
### 9.1 `LogAbilitySystem: Warning: Can't activate LocalOnly or LocalPredicted ability %s when not local!`
You need to [initialize the `ASC` on the client](#concepts-asc-setup).

**[⬆ Back to Top](#table-of-contents)**

<a name="troubleshooting-scriptstructcache"></a>
### 9.2 `ScriptStructCache` errors
You need to call [`UAbilitySystemGlobals::InitGlobalData()`](#concepts-asg-initglobaldata).

**[⬆ Back to Top](#table-of-contents)**

<a name="troubleshooting-replicatinganimmontages"></a>
### 9.3 Animation Montages are not replicating to clients
Make sure that you're using the `PlayMontageAndWait` Blueprint node instead of `PlayMontage` in your [GameplayAbilities](#concepts-ga). This [AbilityTask](#concepts-at) replicates the montage through the `ASC` automatically whereas the `PlayMontage` node does not.

**[⬆ Back to Top](#table-of-contents)**

<a name="troubleshooting-duplicatingblueprintactors"></a>
### 9.4 Duplicating Blueprint Actors is setting AttributeSets to nullptr
There is a [bug in Unreal Engine](https://issues.unrealengine.com/issue/UE-81109) that will set `AttributeSet` pointers on your classes to nullptr for Blueprint Actor classes that are duplicated from existing Blueprint Actor classes. There are a few workarounds for this. I've had success not creating bespoke `AttributeSet` pointers on my classes (no pointer in the .h, not calling `CreateDefaultSubobject` in the constructor) and instead just directly adding `AttributeSets` to the `ASC` in `PostInitializeComponents()` (not shown in the Sample Project). The replicated `AttributeSets` will still live in the `ASC's` `SpawnedAttributes` array. It would look something like this:

```c++
void AGDPlayerState::PostInitializeComponents()
{
	Super::PostInitializeComponents();

	if (AbilitySystemComponent)
	{
		AbilitySystemComponent->AddSet<UGDAttributeSetBase>();
		// ... any other AttributeSets that you may have
	}
}
```

In this scenario, you would read and set the values in the `AttributeSet` using the functions on the `ASC` instead of [calling functions on the `AttributeSet` made from the macros](#concepts-as-attributes).

```c++
/** Returns current (final) value of an attribute */
float GetNumericAttribute(const FGameplayAttribute &Attribute) const;

/** Sets the base value of an attribute. Existing active modifiers are NOT cleared and will act upon the new base value. */
void SetNumericAttributeBase(const FGameplayAttribute &Attribute, float NewBaseValue);
```

So the `GetHealth()` would look something like:

```c++
float AGDPlayerState::GetHealth() const
{
	if (AbilitySystemComponent)
	{
		return AbilitySystemComponent->GetNumericAttribute(UGDAttributeSetBase::GetHealthAttribute());
	}

	return 0.0f;
}
```

Setting (initializing) the health `Attribute` would look something like:

```c++
const float NewHealth = 100.0f;
if (AbilitySystemComponent)
{
	AbilitySystemComponent->SetNumericAttributeBase(UGDAttributeSetBase::GetHealthAttribute(), NewHealth);
}
```

As a reminder, the `ASC` only ever expects at most one `AttributeSet` object per `AttributeSet` class.

**[⬆ Back to Top](#table-of-contents)**

<a name="acronyms"></a>
## 10. 游戏技能系统中常用的缩写 - Common GAS Acronyms

| Name                                                                                                   | Acronyms            |
|------------------------------------------------------------------------------------------------------- | ------------------- |
| AbilitySystemComponent                                                                                 | ASC                 |
| AbilityTask                                                                                            | AT                  |
| [Action RPG Sample Project by Epic](https://www.unrealengine.com/marketplace/en-US/product/action-rpg) | ARPG, ARPG Sample   |
| CharacterMovementComponent                                                                             | CMC                 |
| GameplayAbility                                                                                        | GA                  |
| GameplayAbilitySystem                                                                                  | GAS                 |
| GameplayCue                                                                                            | GC                  |
| GameplayEffect                                                                                         | GE                  |
| GameplayEffectExecutionCalculation                                                                     | ExecCalc, Execution |
| GameplayTag                                                                                            | Tag, GT             |
| ModifierMagnitudeCalculation                                                                           | ModMagCalc, MMC     |

**[⬆ Back to Top](#table-of-contents)**

<a name="resources"></a>
## 11. 资源 - Other Resources
* [Official Documentation](https://docs.unrealengine.com/en-US/Gameplay/GameplayAbilitySystem/index.html)
* Source Code!
   * Especially `GameplayPrediction.h`
* [Action RPG Sample Project by Epic](https://www.unrealengine.com/marketplace/en-US/product/action-rpg)
* [Unreal Slackers Discord](https://unrealslackers.org/) has a text channel dedicated to GAS `#gameplay-ability-system`
   * Check pinned messages
* [GitHub repository of resources by Dan 'Pan'](https://github.com/Pantong51/GASContent)
* [YouTube Videos by SabreDartStudios](https://www.youtube.com/channel/UCCFUhQ6xQyjXDZ_d6X_H_-A)

<a name="resources-daveratti"></a>
### 11.1 Q&A With Epic Game's Dave Ratti

<a name="resources-daveratti-community1"></a>
#### 11.1.1 Community Questions 1
[Dave Ratti responses to the Unreal Slackers Discord Server community questions about GAS](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89):

1. How can we create scoped prediction windows on demand outside or irrespective of GameplayAbilities? For example, how can a fire and forget projectile locally predict a damage GameplayEffectwhen it hits an enemy?

> The PredictionKey system is not really meant to do this. Fundamentally this systems works by a client initiating a predictive action, telling the server about it with a key, and then both client and server running the same thing and associating predictive side effects with the given prediction key. For example, “I am predictively activating an ability” or “I have produced target data and am going to predictively run the part of the ability graph after the WaitTargetData task”.
>
> With this pattern, the PredictionKey “bounces” off the server and comes back to the client via UAbilitySystemComponent::ReplicatedPredictionKeyMap (replicated property). Once the key is replicated back from the server, the client is able to undo all of the locally predictive side effects (GameplayCues, GameplayEffects): the replicated versions *will be there* and if they aren’t then it was a misprediction. Knowing exactly when to undo the predictive side effects is crucial here: if you are too early you will see gaps, if you are too late you will have “double”. (Note this is referring to stateful prediction, like a looping GameplayCue of a duration based Gameplay Effect. “Burst” GameplayCues and instant Gameplay Effects are never “undone” or rolled back. They are just skipped on the client if there is a prediction key associated with them).
>
> To further hit home the point: it’s crucial that predictive action is something the server does not do on their own, but only does so when the client tells them too. So having a generic “Create a key on demand and tell the server so I can run something” does not work unless that “something” is something the server will only do once told to by the client.
>
> Backing up to the original question: something like a fire and forget projectile. Both Paragon and Fornite have projectile actor classes that use GameplayCues. However we do not use the Prediction Key system to do these. Instead we have a concept on Non-Replicated GameplayCues. GameplayCues that just fire off locally and are skipped by the server completely.  Essentially all these are, are direct calls to UGameplayCueManager::HandleGameplayCue. They do not route through the UAbilitySystemComponent so no prediction key checks / early returns are made.
>
> The downside with non replicated GameplayCues is that, well, they are not replicated. So its up to the projectile class/blueprint to make sure the code paths that call these functions are running on everyone. We have for cues startup (called in BeginPlay), explosion, hit wall/character, etc.
>
> These type ofevents are already generated client side, so calling into a non replicated gameplay cue was no big deal. Complicated blueprints can be tricky, and are up to the author to make sure they understand what is running where.


2. When using a WaitNetSyncAbilityTaskwith OnlyServerWaitto create a scoped prediction window in a locally predicted GameplayAbility, could players potentially cheat by delaying their packets to the Server to control GameplayAbilitytiming since the Server is waiting for their RPC withtheir prediction key? Was this ever an issue in Paragon or Fortnite, and if so, what did Epic do to remedy it?

> Yes, this is a valid concern. Any ability blueprint running on the server that is waiting for a client “signal” is potentially vulnerable to lag switch type exploits.
>
> Paragon had a custom targeting task similar to UAbilityTask_WaitTargetData. In this task we had timeouts, or a “max delay” that we would wait on the client for instantaneous targeting modes. If the targeting mode was waiting for user confirmation (button press) then it would be ignored since the user is allowed to take his time. But for abilities that instantly confirmed targeting we would only wait a certain amount of time before either A) generating the target data server side of B) canceling the ability.
>
> We never had such mechanisms for WaitNetSync, which we used pretty sparingly.
>
> I don’t believe Fortnite makes use of anything like this though. The weapon abilities in Fortnite are special cased batched to a single fortnite-specific RPC: one RPC to activate the ability, provide target data, and end the ability. So weapon abilities are intrinsically not vulnerable to this in Battle Royale.
>
> My take is that this is something that could probably be solved system wide but I don’t see us making the change ourselves anytime soon. Spot fixing WaitNetSync to include a max delay for the case you mention is probably a reasonable task, but again - unlikely we will do this on our end in the immediate future.


3. Which EGameplayEffectReplicationModedid Paragon and Fortnite use and what are Epic’s recommendations for when to use each?

> Both games essentially use Mixed mode for their player controlled characters and Minimal for AI controlled (AI minions, jungle creeps, AIHusks, etc). This is what I would recommend most people using the system in a multiplayer game. The sooner into your project you set these, the better.
>
> Fortnite goes a few steps further with its optimizations. It actually does not replicate the UAbilitySystemComponent at all for simulated proxies. The component and attribute subobjects are skipped inside ::ReplicateSubobjects() on the owning fortnite player state class. We do push the bare minimum replicated data from the ability system component to a structure on the pawn itself (basically, a subset of attribute values and a white list subset of tags that we replicate down in a bitmask). We call this a “proxy”. On the receiving side we take the proxy data, replicated on the pawn, and push it back into ability system component on the player state. So you do have an ASC for each player in FNBR, it just doesn’t directly replicate: instead it replicates data via a minimal proxy struct on the pawn and then routes back to the ASC on receiving side. This is advantage since its A) a more minimal set of data B) takes advantage of pawn relevancy.
>
> I’m not sure if it is still necessary with other server side optimizations that have been done since then (Replication Graph, etc) and it is not the most maintainable pattern.


4. Since we cannot predict the removal of GameplayEffectsas per GameplayPrediction.h, are there any strategies for mitigating the effects of latency on removing GameplayEffects? For example, when removing a movement speed slow, we currently have to wait for the Server to replicate the GameplayEffectremoval resulting in a snap of the player’s character position.

> This is a tough one and I don’t have a good answer. We generally skirted around these problems with tolerances and smoothing. I totally agree that ability system and precise synchronization with the character movement system is not in a good place and something we do want to fix.
>
> I had a shelf of allowing predictive removal of GEs but could never work out all edge cases before having to move on. This doesn’t solve everything though since character movement still has an internal saved move buffer that does not know anything about the ability system and possible movement speed modifiers, etc. It is still possible to get into correction feedback loops even outside of not being able to predict the removal of GEs.
>
> If you think you have a case that is truly desperate, you are able to predictively add a GE that would inhibit your movement speed GEs. I’ve never done this myself but have theorized about it before. It may be able to help with a certain class of problem.


5. We know that the AbilitySystemComponentlives on the PlayerStatein Paragon and Fortnite and on the Characterin the Action RPG Sample. What are Epic’s internal rules, guidelines, or recommendations for where the AbilitySystemComponentshould live --what should its Ownerbe?

> In general I would say anything that does not need to respawn should have the Owner andAvatar actor be the same thing. Anything like AI enemies, buildings, world props, etc.
>
> Anything that does respawn should have the Owner and Avatar be different so that the Ability System Component does not need to be saved off / recreated / restored after a respawn.. PlayerState is the logical choice it is replicated to all clients (where as PlayerController is not). The downside is PlayerStates are always relevant so you can run into problems in 100 player games. (See notes on what FN did in question #3).


6. Is it viable to have several AbilitySystemComponentswhich have the same owner but different avatars (e.g. on pawn and weapon/items/projectiles with Ownerset to PlayerState)?

> The first problem I see there would be implementing the IGameplayTagAssetInterface and IAbilitySystemInterface on the owning actor. The former may be possible: just aggregate the tags from all all ASCs (but watch out - HasAlMatchingGameplayTags may be met only via cross ASC aggregation. It wouldn't be enough to just forward that calls to each ASC and OR the results together). But the later is even trickier: which ASC is the authoritative one? If someone wants to apply a GE - which one should receive it? Maybe you can work these out but this side of the problem will be the hardest: owners will multiple ASCs beneath them.
>
> Separate ASCs on the pawn and the weapon can make sense on its own though. E.g, distinguishing between tags the describe the weapon vs those that describe the owning pawn. Maybe it does make sense that tags granted to the weapon also “apply” to the owner and nothing else (E.g, attributes and GEs are independent but the owner will aggregate the owned tags like I describe above). This could work out, I am sure. But having multiple ASCs with the same owner may get dicey.


7. Is there a way to stop the Server from overwriting the cooldown duration of locally predicted abilities on the Owning Client? In scenarios of high latency, this would let theOwning Client "try" to activate the ability again when its local cooldown expires but it is still on cooldown on the Server. By the time the Owning Client's activation request reaches the Server over the network, the Server may be off cooldown or the Server might be able to queue the activation request for the remaining milliseconds that it has left. Otherwise as is, clients with higher latency have a longer delay before when they can reactivate an ability versus those with less latency. This is most apparent with very low cooldown abilities like a basic attack that can be less than one second of cooldown. If there isn't a way to stop the Server from overwriting the cooldown duration of locally predicted abilities, what is Epic's strategy for mitigating theeffects of high latency on reactivating abilities? To word it another example-based way, how did Epic design Paragon's basic attacks and other abilities so that high latency players could attack or activate at the same speed as low latency players with local prediction?

> The short answer there is not a way to prevent this and Paragon definitely had the problem. Higher latency connections would have a lower ROF with basic attacks.
>
> I attempted to fix this by adding “GE reconciliation” where latency was taken into account when calculating GE duration. Essentially allowing the server to eat some of the total GE time so that the effective time of the GE client side would be 100% consistent with any amount of latency (though fluctuations could still cause issues). However I never got this working in a state that could ship and the project moved fast and we just never fully addressed it.
>
> Fortnite does its own bookkeeping for weapon firing rates: it does not use GEs for cooldowns on weapons. I would recommend this if this is a critical problem for your game.


8. What is Epic’s roadmap for the GameplayAbilitySystem plugin? Which features does Epic plan to add in 2019 and beyond?

> We feel that overall the system is pretty stable at this point and we don’t have anyone working on major new features. Bug fixes and small improvements occasionally are made for Fortnite or from UDN/pull requests, but that is it right now.
>
> Longer term, I think we will eventually do a “V2” or some big changes. We learned a lot from writing this system and feel we got a lot right and a lot wrong. I would love a chance to correct those mistakes and improve some of the fatal flaws that were pointed out above.
>
> If a V2 was to ever come, providing an upgrade path would be of utmost importance. We would never make a V2 and leave Fortnite on V1 forever: there would be some path or procedures that would automatically migrate as much as possible, though there would still almost certainly be some manual remaking required.
>
> The high priority fixes would be:
> * Better interoperability with the character movement system. Unifying client prediction.
> * GE removal prediction (question #4)
> * GE latency reconciliation (question #8)
> * Generalized network optimizations such as batching RPCs and proxy structures. Mostly the stuff that we’ve done for Fortnite but find ways to break it down into more generalized form, at least so that games can write their own game specific optimizations more easily.
>
> The more general refactor type of changes I would consider making:
> * I would like to look at fundamentally moving away from having GEs reference spreadsheet values directly, instead they would be able to emit parameters and those parameters could be filled by some higher level object that is bound to spreadsheet values. The problem with the current model is that GEs become unsharable due to their tight coupling with the curve table rows. I think a generalized system for parameterization could be written and be the underpinning of a V2 system.
> * Reduce number of “policies” on UGameplayAbility. I would remove ReplicationPolicy InstancingPolicy. Replication is, imo, almost never actually needed and causes confusion. InstancingPolicy should be replaced instead by making FGameplayAbilitySpec a UObject that can be subclassed. This should have been the “non instantiated ability object” that has events and is blueprintable. The UGameplayAbility should be the “instanced per execution” object. It could be optional if you need to actually instantiate: instead “non instanced” abilities would be implemented via the new UGameplayAbilitySpec object.
> * The system should provide more “middle level” constructs such as “filtered GE application container” (data drive what GEs to apply to which actors with higher level gameplay logic), “Overlapping volume support” (apply the “Filtered GE application container” based on collision primitive overlap events), etc.These are building blocks that every project ends up implementing in their own way. Getting them right is non trivial so I think we should do a better job providing some basic implementations.
> * In general, reducing boilerplate needed to get your project up and running. Possibly a separate module “Ex library” or whatever that could provide things like passive abilities or basichitscan weapons out of the box. This module would be optional but would get you up and running quickly.
> * I would like to move GameplayCues to a separate module that is not coupled with the ability system. I think there are a lot of improvements that could be made here.


> This is only my personal opinion and not a commitment from anyone. I think the most realistic course of action will be as new engine tech initiatives come through, the ability system will need to be updated and that will be a time to do this sort of thing. These initiatives could be related to scripting, networking, or physics/character movement. This is all very far looking ahead though so I cannot give commitments or estimates on timelines.

**[⬆ Back to Top](#table-of-contents)**

<a name="resources-daveratti-community2"></a>
#### 11.1.2 Community Questions 2
Community member [iniside](https://github.com/iniside)'s Q&A with Dave Ratti:

1. Is the support for decoupled fixed ticking planned ? I'd like to
have Game Thread be fixed (like 30/60fps) and let the rendering thread
run wild. I ask if this is something we should expect in future or
not, to make some assumptions about how gameplay should work.
I ask mainly because there is now a fixed async tick for physics and
this poses a question how the rest of the system might work in the
future. I do not hide that having the ability to have fixed tick game
thread without also fixing tick rate of the rest of the engine would
be beyond awesome.

> There are no plans to decouple rendering frame rate and game thread tick frame rate. I think the ship has sailed on this ever happening due to the complexity of these systems and the requirement to preserve backwards compatibility with previous engine versions.
>
> Instead, the direction we've gone is to have an asynchronous "Physics Thread" which runs at a fixed tick rate, independent of the game thread. Things that need to run at a fixed rate can run here and the game thread / rendering can operate how they always have.
>
> It's worth clarifying that Network Prediction supports what it calls Independent Ticking and Fixed Ticking modes. My long term plan is to keep Independent Ticking roughly how it is today in Network Prediction where it runs on the game thread at variable frame rate and there is no "group/world" prediction, it's just the classic "clients predict their own pawn and owned actors" model. And Fixed Ticking would be what uses the async physics stuff and allows you to predict non client controlled/owned actors like physics objects and other clients/pawns/vehicles/etc.


2. Is there any plan on how the integration of Network Prediction will
look with the Ability System ? Like for example, fixed frame ability
activation (so the server gets frames in which abilities were
activated and tasks executed instead of prediction keys) ?

> Yes, the plan is to rewrite/remove the Ability System's prediction keys and replace them with Network Prediction constructs. The MockAbility examples in NetworkPredictionExtras show how this might work but they are more "hard coded" than what GAS will require.
>
> The main idea would be that we remove the explicit client->server Prediction Key exchange in the ASC's RPCs. There would no longer be prediction windows or scoped prediction keys. Instead everything would be anchored around NetworkPrediction frames. The important thing is that client and server agree on when things happen. Examples would be:
>
> * When abilities were activated/ended/cancelled
> * When Gameplay Effects were applied/removed
> * Attribute values (what an attributes value was at frame X)
>
> I think this could be done generically at the ability system level. But actually making the user-defined logic inside a UGameplayAbility completely rollback-able would still take more work. We may end up having a subclass of UGameplayAbility that is fully rollbackable and has access to a more limited set of functionality or only Ability Tasks that are marked as rollback-friendly. Something like that. There are also many implications to animation events and root motion and how those are processed.
>
> Wish I had a more clear answer but it's really important we get the foundation right before touching GAS again. Movement and physics have to be solid before the higher level systems can be changed.


3. Is there a plan to move Network Prediction development toward the
main branch ? Not gonna lie, I'd really like to check the latest code.
Regardless of it's state.

> We are working towards it. The system work is still all being done in NetworkPrediction (see NetworkPhysics.h) and the underlying async physics stuff should be all available (RewindData.h etc). But we also have use cases in Fortnite that we have been focused on that obviously can't be made public. We are working through bugs, performance optimizations, etc.
>
> For more context: when working on the early versions of this system, we were very focused on the "front end" of things - how state and simulations were defined and written. We learned a lot there. But as the async physics stuff has come online, we've been much more focused on just getting something real to work in this system, at the expense of throwing out some of our early abstractions. The goal here is to circle back when the real thing is working and reunifying things. E.g, get back to the "front end" and make the final version of that on top of the core pieces of tech we are working on now.


4. For some time on Main there was a plugin for sending Gameplay
Messages (Looked like Event/Message Bus), but it was removed. Any
plans to restore it ? With the Game Features/Modular Gameplay plugins,
having a generic Event Bus Dispatcher would be extremely useful.

> I think you are referring to the GameplayMessages plugin. This will probably come back at some point - the API isn't really finalized yet and the author didn't mean for it to be public yet. I agree it should be useful for modular gameplay design. But it's not really my area so I don't have much more information.


5. I've been playing recently with async fixed physics and the results
are promising, though if there is going to be NP update in the future
I will probably just play around and wait, since to get it working I
still need to get entire engine into fixed tick and on the other hand
I try to keep physics at 33ms. Which does not make for a good
experience if everything is at 30 fps (:.

I have noticed there was some work on Async
CharacterMovementComponent, but not sure if this will be using Network
Prediction, or it is a separate effort ?

Since I noticed it, I also went ahead and tried to implement my custom
async movement at fixed tick rate, which worked okay, but on top of it
I also needed to add a separate update for interpolation. The setup
was to run simulation tick on separate worker threads at fixed 33ms
update, do calculations, save result, and interpolate it at the game
thread to match current frame rate. Not perfect, but it got the job
done.

My question is, if this is something that might be easier to set up in
the future, as there is just quite a bit of boilerplate code to write,
(the interpolation part) and it's not particularly efficient to
interpolate each moving object individually.

The async stuff is really interesting, because it would allow you to
really run game simulation at fixed update rate (which would make
fixed thread unneeded) and have more predictable results. Is this
something that is intended going forward, or more of a benefit to
select systems ? As far as I remember actor transforms are not updated
async and blueprints are not entirely thread safe. In other words is
it something that is planned to be supported at more of a framework
level or something that each game has to solve on it's own ?

> Async CharacterMovementComponent
>
> This is basically an early prototype/experiment of porting CMC as it is to the physics thread. I don't view it as the future of CMC yet, but it could evolve into that. Right now there is no networking support so it's not something I would really follow. The people doing it are mostly concerned with measuring input latency that this system would add and how that could be mitigated.
>
> I still need to get entire engine into fixed tick and on the other hand I try to keep physics at 33ms. Which does not make for a good experience if everything is at 30 fps (:.
>
> The async stuff is really interesting, because it would allow you to really run game simulation at fixed update rate (which would make fixed thread unneeded)
>
> Yes. The goal here is that with async physics enabled, you can run the engine at variable tick rate while the physics and "core" gameplay simulations can run at the fixed rate (such as character movement, vehicles, GAS, etc).
>
> These are the cvars that need to be set to enable this now: (I think you've figured this out)  
> `p.DefaultAsyncDt=0.03333`  
> `p.RewindCaptureNumFrames=64`
>
> Chaos does provide interpolation for the physics state (E.g, the transforms that get pushed back to the UPrimitiveComponent and are visible to the game code). There is a cvar now, `p.AsyncInterpolationMultiplier`, which controls that if you want to look at it. You should see smooth continuous motion of physics bodies without having to write any extra code.
>
> If you want to interpolate non physics state, it is still up to you to do that right now. The example would be like a cool-down that you want to update (tick) on the async physics thread but see smooth continuous interpolation on the game thread so that every render frame the cool down visualization is updated. We will get to this eventually but don't have examples yet.
>
> there is just quite a bit of boilerplate code to write,
>
> Yeah, so that has been a big general problem with the system up until now. We want to provide an interface that experienced programmers can use to maximize performance and safety (the ability to write gameplay code that "just works" predictively without tons of hazards and things you could-do-but-better-not). So something like CharacterMoverment might do a bunch of custom stuff to maximize its performance - e.g, writing templated code and doing batch updating, going wide, breaking the update loop into distinct phases etc. We want to provide a good "low level" interface into the async thread and rollback systems for this use case. And in this case too - it's still reasonable that the character movement system itself is extendable in its own way. For example providing a way to blueprint a custom movement mode and providing a blueprint API that is thread safe.
>
> But we recognize this is not acceptable for simpler gameplay objects that don't really need their own "system". Something more inline with Unreal is what is needed. E.g, using the reflection system, having general blueprint support, etc. There are examples of blueprints being used on other threads (see BlueprintThreadSafe keyword and what the animation system has been working towards). So I think there will be some form of this one day. But again, we aren't there yet.
>
> I realize you were just asking about interpolation but that is the general answer: right now we have you do everything manually like NetSerialize, ShouldReconcile, Interpolate, etc but eventually we'll have a way that is like "if you want to just use the reflection system, you don't have to manually write this stuff". We just don't want to *force* everyone to use the reflection system since that imposes other limitations that we think we don't want to take on the lowest levels of the system.
>
> And then just to tie this back to what I said earlier - right now we are really focused on getting a few very specific examples working and performant and then we will turn attention back to the front end and making things friendly to use and iterate on, reducing boilerplate, etc for everybody else to use.

**[⬆ Back to Top](#table-of-contents)**

<a name="changelog"></a>
## 12. 游戏技能系统版本日志 - GAS Changelog

This is a list of notable changes (fixes, changes, and new features) to GAS compiled from the official Unreal Engine upgrade changelog and from undocumented changes that I've encountered. If you've found something that isn't listed here, please make an issue or pull request.

<a name="changelog-4.27"></a>
### 4.27
* Crash Fix: Fixed a root motion source issue where a networked client could crash when an Actor finishes executing an ability that uses a constant force root motion task with a strength-over-time modifier.
* Bug Fix: Fixed a regression in Editor loading time when using GameplayCues.
* Bug Fix: GameplayEffectsContainer's `SetActiveGameplayEffectLevel` method will no longer dirty FastArray if setting the same EffectLevel.
* Bug Fix: Fixed an edge case in GameplayEffect mixed replication mode where Actors not explicitly owned by the net connection but who utilize that connection from `GetNetConnection` will not received mixed replication updates.
* Bug Fix: Fixed an endless recursion occuring in GameplayAbility's class method `EndAbility` which was called by calling `EndAbility` again from `K2_OnEndAbility`.
* Bug Fix: GameplayTags Blueprint pins will no longer be silently cleared if they are loaded before tags are registered. They now work the same as GameplayTag variables, and the behavior for both can be changed with the ClearInvalidTags option in the Project Settings.
* Bug Fix: Improved thread safety of GameplayTag operations.
* New: Exposed SourceObject to GameplayAbility's `K2_CanActivateAbility` method.
* New: Native GameplayTags. Introducing a new `FNativeGameplayTag`, these make it possible to do one off native tags that are correctly registered and unregistered when the module is loaded and unloaded.
* New: Added new method `GrantAndActivateAbilityOnSelfWithParams` which allows Designers to pass in FGameplayEventData when granting and then activating an ability from Blueprint.
* New: Improved ScalableFloats in the GameplayAbilities plugin to support dynamic lookup of curve tables from the new Data Registry System. Added a ScalableFloat header for easier reuse of the generic struct outside the abilities plugin.
* New: Added code support for using the GameplayTag UI in other Editor customizations via GameplayTagsEditorModule.
* New: Modified UGameplayAbility's PreActivate method to optionally take in trigger event data.
* New: Added more support to filter GameplayTags in the Editor using a project-specific filter. `OnFilterGameplayTag` supplies the referencing property and the tag source, so you can filter tags based on what asset is requesting the tag.
* New: Added option to preserve the original captured SourceTags when GameplayEffectSpec's class method `SetContext` is called after initialization.
* New: Improved UI for registering GameplayTags from specific plugins. The new tag UI now lets you select a plugin location on disk for newly added GameplayTag sources.
* New: A new track has been added to Sequencer to allow for triggering notify states on Actors built using the GameplayAbiltiySystem. Like notifies, the GameplayCueTrack can utilize range-based events or trigger-based events.
* Change: Changed the GameplayCueInterface to pass GameplayCueParameters struct by reference.
* Optimization: Made several performance improvements to loading and regenerating the GameplayTag table were implemented so that this option would be optimized.

https://docs.unrealengine.com/en-US/WhatsNew/Builds/ReleaseNotes/4_27/](https://docs.unrealengine.com/en-US/WhatsNew/Builds/ReleaseNotes/4_27/

<a name="changelog-4.26"></a>
### 4.26
* GAS plugin is no longer flagged as beta.
* Crash Fix: Fixed a crash when adding a gameplay tag without a valid tag source selection.
* Crash Fix: Added the path string arg to a message to fix a crash in UGameplayCueManager::VerifyNotifyAssetIsInValidPath.
* Crash Fix: Fixed an access violation crash in AbilitySystemComponent_Abilities when using a ptr without checking it.
* Bug Fix: Fixed a bug where stacking GEs that did not reset the duration on additional instances of the effect being applied.
* Bug Fix: Fixed an issue that caused CancelAllAbilities to only cancel non-instanced abilities.
* New: Added optional tag parameters to gameplay ability commit functions.
* New: Added StartTimeSeconds to PlayMontageAndWait ability task and improved comments.
* New: Added tag container "DynamicAbilityTags" to FGameplayAbilitySpec. These are optional ability tags that are replicated with the spec. They are also captured as source tags by applied gameplay effects.
* New: GameplayAbility IsLocallyControlled and HasAuthority functions are now callable from Blueprint.
* New: Visual logger will now only collect and store info about instant GEs if we're currently recording visual logging data.
* New: Added support for redirectors on gameplay attribute pins in blueprint nodes.
* New: Added new functionality for when root motion movement related ability tasks end they will return the movement component's movement mode to the movement mode it was in before the task started.

https://docs.unrealengine.com/en-US/WhatsNew/Builds/ReleaseNotes/4_26/

<a name="changelog-4.25.1"></a>
### 4.25.1
* Fixed! UE-92787 Crash saving blueprint with a Get Float Attribute node and the attribute pin is set inline
* Fixed! UE-92810 Crash spawning actor with instance editable gameplay tag property that was changed inline

<a name="changelog-4.25"></a>
### 4.25
* Fixed prediction of `RootMotionSource` `AbilityTasks`
* [`GAMEPLAYATTRIBUTE_REPNOTIFY()`](#concepts-as-attributes) now additionally takes in the old `Attribute` value. We must supply that as the optional parameter to our `OnRep` functions. Previously, it was reading the attribute value to try to get the old value. However, if called from a replication function, the old value had already been discarded before reaching SetBaseAttributeValueFromReplication so we'd get the new value instead.
* Added [`NetSecurityPolicy`](#concepts-ga-netsecuritypolicy) to `UGameplayAbility`.
* Crash Fix: Fixed a crash when adding a gameplay tag without a valid tag source selection.
* Crash Fix: Removed a few ways for attackers to crash a server through the ability system.
* Crash Fix: We now make sure we have a GameplayEffect definition before checking tag requirements.
* Bug Fix: Fixed an issue with gameplay tag categories not applying to function parameters in Blueprints if they were part of a function terminator node.
* Bug Fix: Fixed an issue with gameplay effects' tags not being replicated with multiple viewports.
* Bug Fix: Fixed a bug where a gameplay ability spec could be invalidated by the InternalTryActivateAbility function while looping through triggered abilities.
* Bug Fix: Changed how we handle updating gameplay tags inside of tag count containers. When deferring the update of parent tags while removing gameplay tags, we will now call the change-related delegates after the parent tags have updated. This ensures that the tag table is in a consistent state when the delegates broadcast.
* Bug Fix: We now make a copy of the spawned target actor array before iterating over it inside when confirming targets because some callbacks may modify the array.
* Bug Fix: Fixed a bug where stacking GameplayEffects that did not reset the duration on additional instances of the effect being applied and with set by caller durations would only have the duration correctly set for the first instance on the stack. All other GE specs in the stack would have a duration of 1 second. Added automation tests to detect this case.
* Bug Fix: Fixed a bug that could occur if handling gameplay event delegates modified the list of gameplay event delegates.
* Bug Fix: Fixed a bug causing GiveAbilityAndActivateOnce to behave inconsistently.
* Bug Fix: Reordered some operations inside FGameplayEffectSpec::Initialize to deal with a potential ordering dependency.
* New: UGameplayAbility now has an OnRemoveAbility function. It follows the same pattern as OnGiveAbility and is only called on the primary instance of the ability or the class default object.
* New: When displaying blocked ability tags, the debug text now includes the total number of blocked tags.
* New: Renamed UAbilitySystemComponent::InternalServerTryActiveAbility to UAbilitySystemComponent::InternalServerTryActivateAbility.Code that was calling InternalServerTryActiveAbility should now call InternalServerTryActivateAbility.
* New: Continue to use the filter text for displaying gameplay tags when a tag is added or deleted. The previous behavior cleared the filter.
* New: Don't reset the tag source when we add a new tag in the editor.
* New: Added the ability to query an ability system component for all active gameplay effects that have a specified set of tags. The new function is called GetActiveEffectsWithAllTags and can be accessed through code or blueprints.
* New: When root motion movement related ability tasks end they now return the movement component's movement mode to the movement mode it was in before the task started.
* New: Made SpawnedAttributes transient so it won't save data that can become stale and incorrect. Added null checks to prevent any currently saved stale data from propagating. This prevents problems related to bad data getting stored in SpawnedAttributes.
* API Change: AddDefaultSubobjectSet has been deprecated. AddAttributeSetSubobject should be used instead.
* New: Gameplay Abilities can now specify the Anim Instance on which to play a montage.

https://docs.unrealengine.com/en-US/WhatsNew/Builds/ReleaseNotes/4_25/

<a name="changelog-4.24"></a>
### 4.24
* Fixed blueprint node `Attribute` variables resetting to `None` on compile.
* Need to call [`UAbilitySystemGlobals::InitGlobalData()`](#concepts-asg-initglobaldata) to use [`TargetData`](#concepts-targeting-data) otherwise you will get `ScriptStructCache` errors and clients will be disconnected from the server. My advice is to always call this in every project now whereas before 4.24 it was optional.
* Fixed crash when copying a `GameplayTag` setter to a blueprint that didn't have the variable previously defined.
* `UGameplayAbility::MontageStop()` function now properly uses the `OverrideBlendOutTime` parameter.
* Fixed `GameplayTag` query variables on components not being modified when edited.
* Added the ability for `GameplayEffectExecutionCalculations` to support scoped modifiers against "temporary variables" that aren't required to be backed by an attribute capture.
   * Implementation basically enables `GameplayTag`-identified aggregators to be created as a means for an execution to expose a temporary value to be manipulated with scoped modifiers; you can now build formulas that want manipulatable values that don't need to be captured from a source or target.
   * To use, an execution has to add a tag to the new member variable `ValidTransientAggregatorIdentifiers`; those tags will show up in the calculation modifier array of scoped mods at the bottom, marked as temporary variables—with updated details customizations accordingly to support feature
* Added restricted tag quality-of-life improvements. Removed the default option for restricted `GameplayTag` source. We no longer reset the source when adding restricted tags to make it easier to add several in a row.
* `APawn::PossessedBy()` now sets the owner of the `Pawn` to the new `Controller`. Useful because [Mixed Replication Mode](#concepts-asc-rm) expects the owner of the `Pawn` to be the `Controller` if the `ASC` lives on the `Pawn`.
* Fixed bug with POD (Plain Old Data) in `FAttributeSetInitterDiscreteLevels`.

https://docs.unrealengine.com/en-US/WhatsNew/Builds/ReleaseNotes/4_24/

**[⬆ Back to Top](#table-of-contents)**