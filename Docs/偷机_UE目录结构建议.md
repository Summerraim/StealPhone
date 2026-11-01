# 《偷机》UE 程序目录结构建议

> 当前工程：`E:\Unreal\StealPhone`  
> 使用模板：First Person Blueprint + Survival Horror 变体  
> 原则：模板资源只作为参考；自己的资源全部放进 `Content/StealPhone/`。

## 01｜先处理模板资源

- `Content/FirstPerson/`、`Content/Variant_Horror/`、`Content/Characters/`、`Content/Input/` 是模板/共享内容，不要直接改名或覆盖。
- 需要改角色、GameMode、PlayerController 时，复制到 `Content/StealPhone/` 后再改。
- `BP_HorrorCharacter` 里已有第一人称相机和手电；保留手电，删除或禁用冲刺和体力 UI。
- 当前默认地图还是 `Lvl_FirstPerson`，正式开发时改为自己的关卡和 GameMode。

## 02｜推荐目录结构

```text
E:\Unreal\StealPhone\
├─ Config\
├─ Docs\
│  ├─ 偷机_程序执行版策划案.md
│  ├─ 偷机_流程线.md
│  └─ 偷机_UE目录结构建议.md
├─ Content\
│  ├─ FirstPerson\                 # 模板，不改
│  ├─ Variant_Horror\              # 模板，不改
│  ├─ Characters\                  # 模板共享资源
│  ├─ Input\                       # 模板输入资源
│  ├─ LevelPrototyping\            # 灰盒资源
│  └─ StealPhone\                  # 本项目自己的资源
│     ├─ Core\
│     │  ├─ BP_SP_GameMode
│     │  ├─ BP_SP_PlayerController
│     │  ├─ BP_SP_RunManager
│     │  └─ BP_SP_FatherDirector
│     ├─ Player\
│     │  ├─ BP_SP_PlayerCharacter
│     │  ├─ BPC_SP_Interaction
│     │  ├─ BPC_SP_Equipment
│     │  └─ BPC_SP_Footstep
│     ├─ Interaction\
│     │  ├─ BPI_SP_Interactable
│     │  ├─ BP_SP_InteractableBase
│     │  ├─ BP_SP_Phone
│     │  ├─ BP_SP_Cup
│     │  ├─ BP_SP_Door
│     │  ├─ BP_SP_LightSwitch
│     │  ├─ BP_SP_Curtain
│     │  ├─ BP_SP_Socks
│     │  ├─ BP_SP_Bed
│     │  └─ BP_SP_HidingSpot
│     ├─ Props\
│     │  ├─ BP_SP_CollisionProp
│     │  └─ BPC_SP_ImpactFeedback
│     ├─ Data\
│     │  ├─ Enums\
│     │  │  ├─ E_SP_MaterialType
│     │  │  ├─ E_SP_EquipSlot
│     │  │  ├─ E_SP_RunState
│     │  │  └─ E_SP_SnoreStage
│     │  ├─ Structs\
│     │  │  ├─ ST_SP_MaterialConfig
│     │  │  └─ ST_SP_EquipmentConfig
│     │  ├─ DT_SP_Materials
│     │  ├─ DT_SP_Equipment
│     │  └─ DA_SP_Tuning
│     ├─ Levels\
│     │  ├─ L_SP_Blockout             # 灰盒测试
│     │  ├─ L_SP_AudioTest            # 音频/材质测试
│     │  └─ L_SP_Main                 # 正式流程
│     ├─ UI\
│     │  ├─ WBP_SP_InteractIcon       # 仅有图标，无文字
│     │  ├─ WBP_SP_Pause
│     │  └─ WBP_SP_Fade
│     ├─ Audio\
│     │  ├─ Footsteps\
│     │  ├─ Impacts\
│     │  ├─ Snore\
│     │  ├─ Voice\
│     │  └─ UI\
│     ├─ Art\
│     │  ├─ Meshes\
│     │  ├─ Materials\
│     │  ├─ Textures\
│     │  └─ Animations\
│     └─ Dev\
│        └─ Debug\
└─ Source\                            # 现在不需要；以后加 C++ 再出现
```

## 03｜命名规范

| 前缀 | 用途 | 示例 |
|---|---|---|
| `BP_SP_` | 蓝图 Actor / 游戏对象 | `BP_SP_Phone` |
| `BPC_SP_` | Actor 组件 | `BPC_SP_Footstep` |
| `BPI_SP_` | 蓝图接口 | `BPI_SP_Interactable` |
| `DT_SP_` | DataTable | `DT_SP_Materials` |
| `DA_SP_` | DataAsset | `DA_SP_Tuning` |
| `E_SP_` | 枚举 | `E_SP_MaterialType` |
| `ST_SP_` | 结构体 | `ST_SP_MaterialConfig` |
| `WBP_SP_` | Widget Blueprint | `WBP_SP_InteractIcon` |
| `L_SP_` | 关卡 | `L_SP_Blockout` |
| `M_SP_` / `MI_SP_` | 材质 / 材质实例 | `MI_SP_Wood` |
| `SM_SP_` | 静态网格 | `SM_SP_Cup` |
| `T_SP_` | 纹理 | `T_SP_Wood` |
| `S_SP_` | 声音资源 | `S_SP_Cup_Ceramic` |

不要在蓝图里创建同名资源；不同目录可以同名，但查找时容易混淆，建议全部带 `SP_`。

## 04｜核心数据配置

### `DT_SP_Materials`

一行代表一种材质：

| 字段 | 类型 | 说明 |
|---|---|---|
| `Material` | `E_SP_MaterialType` | 材质类型 |
| `DisplayName` | Text | 仅编辑器调试用，不显示给玩家 |
| `PlayerMultiplier` | float | 作为玩家接触材质时的 C_player，没有则填 0 |
| `ObjectMultiplier` | float | 作为对象材质时的 C_object，没有则填 0 |
| `CollisionSound` | SoundBase | 该材质的通用碰撞音效 |
| `Priority` | enum/int | P0/P1/P2 |

材质行至少包含：

```text
Skin, Cotton, Wool, Cloth, Rubber, Leather, HardPlastic,
Fabric, Paper, Wood, Plastic, Ceramic, Metal, WoodFloor, Tile
```

不要把“玩具车”“垃圾桶”当成独立材质；它们都映射到 `Plastic`。

### `DT_SP_Equipment`

| 字段 | 说明 |
|---|---|
| `EquipmentID` | 装备编号 |
| `Slot` | Hand / FootInner / FootOuter |
| `Material` | 对应的材质类型 |
| `Mesh` | 可选，P0 可以先不做 |
| `Icon` | 无文字交互图标 |
| `Priority` | P0/P1 |

P0 只需要棉袜；棉手套、软布鞋可以等主流程跑通后再加。

### `DA_SP_Tuning`

建议集中放置：

- H 最大值：100
- 恢复等待：2 秒
- 恢复速度：6 点/秒
- 碰撞合并窗口：0.25 秒
- 再次触发冷却：0.50 秒
- S1/S2/S3 阈值：67 / 34 / 1
- 父亲完整往返：23–26 秒
- 稳定鼾声：3 秒
- 碰杯教学是否扣 H：否
- 第一次取机是否固定触发夜起：是
- 距离模式：直线距离 / 寻路距离（二选一，全项目统一）

## 05｜蓝图之间的职责

```text
PlayerCharacter
  ├─ 移动、视角、手电
  ├─ BPC_Interaction：从相机发射射线，确认实际接触
  ├─ BPC_Equipment：当前手部/足部材质
  └─ BPC_Footstep：落脚时读取地面材质并发事件

Interactable Actor
  ├─ 实现 BPI_SP_Interactable
  ├─ 本身有主要材质
  └─ 实际接触时调用 RunManager 的风险入口

RunManager
  ├─ 接收玩家材质 + 对象材质 + 声源位置
  ├─ 计算 D、L、H
  ├─ 处理去重、冷却、恢复
  └─ 广播 OnRiskApplied / OnSnoreStageChanged

FatherDirector
  ├─ 只负责父亲固定时间线
  ├─ 通知音频和检查点
  └─ 不直接修改 H
```

**不要在每个道具蓝图里各写一份扣 H 逻辑。** 所有碰撞最终进入同一个函数，例如：

```text
RunManager.ApplyCollisionRisk(PlayerMaterial, ObjectMaterial, SourceLocation, SourceActor, TargetActor)
```

RunManager 内部负责：

- 判断这次事件是否有效；
- 生成 EventID；
- 查距、扣 H、判断阶段；
- 记录 Debug 日志；
- 触发声音和阶段变化事件。

## 06｜碰撞检测建议

### 脚步

- 用移动距离或动画通知触发“有效落脚”，不要用 Tick 每帧发脚步。
- 落脚时向下做短射线，读取地面材质（木地板/瓷砖）。
- 读取当前足部最外层材质：有鞋读鞋，无鞋读袜子，再否则皮肤。
- 一次落脚只提交一个脚步事件。

### 手部/道具

- 交互用相机射线或近距离球形检测，确认玩家确实接触/指向物体。
- 拿取、放回、推门只在实际接触确认点提交一次。
- 持续贴住物体不每帧提交；用 0.25 秒合并、0.50 秒冷却。
- 复杂连锁碰撞 P0 不做；需要物体反应时用 Timeline/简单动画。

### 物体反馈

给核心道具加 `BPC_SP_ImpactFeedback`：

- 播放碰撞音效；
- 播放轻微晃动/位移/倾斜 Timeline；
- 可选调用玩家手部动作或非常轻微的镜头反馈；
- 不修改 H，不增加第四个风险系数。

## 07｜关卡与项目设置

1. 复制模板角色和 GameMode 到 `Content/StealPhone/`，不要直接改模板。
2. 创建 `L_SP_Blockout`，在 World Settings 里覆盖 GameMode。
3. 项目设置 > Maps & Modes：
   - Editor Startup Map 改为自己的关卡；
   - Game Default Map 改为自己的关卡；
   - 默认 GameMode 改为 `BP_SP_GameMode`。
4. 先只放一个卧室 C 和一个过道，完成“走动→碰物→扣 H→鼾声变化”的闭环。
5. 再加入书房、衣帽间、父亲时序和第二次取机。

## 08｜以后要加 C++ 时

现在不要为了“作品集”强行把蓝图工程改成 C++。等主流程稳定后，可以只把纯逻辑抽成 C++：

```text
Source\StealPhone\
├─ StealPhone.Build.cs
├─ StealPhone.h
├─ StealPhone.cpp
└─ Collision\
   ├─ SPRiskSubsystem.h
   └─ SPRiskSubsystem.cpp
```

适合放 C++ 的部分：

- 风险计算与 H 状态机；
- 事件去重和冷却；
- 数据表读取；
- Debug 日志。

关卡、UI、动画、交互表现继续留在蓝图。这样既有工程深度，也不会拖慢 Jam。
