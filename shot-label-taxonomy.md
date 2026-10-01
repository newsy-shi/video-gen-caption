# 视频镜头标签体系

这套体系用于给视频中的单个镜头、分镜稿或生成提示词做结构化标注。它把“镜头是什么样”“观众从哪里看”“摄影机如何运动”“画面里发生什么”分成彼此独立的维度。这样既能组合出完整镜头描述，也能避免把 `近景`、`俯拍`、`推镜头`、`双人镜头` 等不同种类的词误当成同一层级的选项。

## 1. 设计原则

### 多轴分类，不做一棵混杂的大树

一个镜头可以同时是“中近景 + 低机位 + 侧面视角 + 双人镜头 + 缓慢推轨 + 浅景深”。这些标签回答不同问题，彼此可以同时成立。因此，体系采用多个**正交标签轴**；每个轴内部再设层级和可选值。

### 标签只回答一个问题

| 标签轴 | 回答的问题 | 典型标签 |
|---|---|---|
| 景别 | 画面展示主体多少？ | 特写、中景、全景 |
| 主体配置 | 画面中有几个主要主体？ | 单人、双人、群像 |
| 机位角度 | 摄影机从什么高度和方向观察？ | 平视、俯拍、仰拍 |
| 视点关系 | 画面属于谁的观看位置？ | 客观视点、人物主观视点 |
| 镜头功能 | 这个镜头在叙事或剪辑里做什么？ | 建立镜头、反应镜头、插入镜头 |
| 摄影机运动 | 摄影机如何改变位置或朝向？ | 推轨、横移、摇摄、环绕 |
| 主体运动 | 人物或物体如何行动？ | 静止、走近、转身、奔跑 |
| 光学与焦点 | 镜头怎样成像、清晰点在哪里？ | 广角感、浅景深、焦点转换 |
| 时间与剪辑 | 镜头持续多久，如何进入/离开？ | 连续长镜头、慢动作、淡出 |

光线、色彩、风格、声音、画幅和交付规格会影响成片，但不属于摄影机镜头标签本体；体系最后单独列为关联字段。

### 受控标签与自由描述并用

稳定、可复用的概念使用受控标签；复杂路线、细节和叙事意图用自然语言补充。不要为了让标签“看起来全面”而把所有自由文本硬塞进枚举值。

## 2. 总体层级

```text
作品 / 项目
└── 场景 Scene                 地点、时间和连续动作的叙事单元
    └── 镜头 Shot              两次剪辑之间连续呈现的一段影像
        ├── A. 镜头功能与覆盖角色
        ├── B. 景别与取景范围
        ├── C. 主体配置
        ├── D. 机位与拍摄角度
        ├── E. 视点与观众关系
        ├── F. 摄影机运动
        ├── G. 主体运动与场面调度
        ├── H. 镜头、焦点与景深
        ├── I. 时间、镜头长度与剪辑衔接
        └── J. 意图与情绪（解释字段，不是技术分类）
```

一个场景可以由一个或多个镜头组成。**长镜头**描述镜头持续时间和连续性，**全景**描述景别；一个镜头可以同时是长镜头和全景。**主观镜头**描述观看视点，**特写**描述景别；它们也可以组合。

## 3. A 类：镜头功能与覆盖角色 `role`

这组标签描述镜头在分镜或剪辑中承担的任务，不说明镜头大小、角度或运动。

| 标签 | 定义 | 易混概念的区分 |
|---|---|---|
| `establishing` 建立镜头 | 建立新场景的位置、时间、环境或空间关系 | 可以是固定全景，也可以是运动镜头；“全景”本身不保证有建立功能 |
| `master` 主镜头 | 覆盖整个场景或主要表演段落，常作为剪辑覆盖基础 | 它可以是中景、全景或运动长镜头；“长镜头”强调连续时间，不等于主镜头 |
| `coverage` 覆盖镜头 | 为场景提供可剪辑的补充视角或表演覆盖 | 这是制作/剪辑功能，不是景别或机位类型 |
| `reaction` 反应镜头 | 以人物对事件的反应为主要信息 | 反应镜头常为近景，但也能以远景呈现群体反应 |
| `insert` 插入镜头 | 插入关键物件、手部或文字等细节信息 | 更偏叙事功能；常用特写，但不等同于特写 |
| `cutaway` 插入/旁切镜头 | 暂时离开当前主要动作，显示相关物件、环境或旁观者 | 可用于交代信息、转场或延缓动作，不固定景别 |
| `transition` 转场镜头 | 主要用于连接时间、地点、段落或状态变化 | 标签记录剪辑意图；具体剪辑手法另标 `edit.transition` |
| `pick_up` 动作补拍 | 从已开始的动作中途接入，补充动作覆盖 | 是制作覆盖类型，不是镜头运动类型 |

一个镜头可以有一个主要 `role` 和零至两个次要功能。除非有明确理由，避免把 `establishing`、`reaction`、`insert` 等所有可能功能全贴上去。

## 4. B 类：景别与取景范围 `framing.scale`

景别回答“主体在画面中有多大、展示了多少”。建议以**人物身体覆盖范围**为默认参照；对象或环境镜头用 `subject_reference` 说明参照物。

| 层级 | 规范标签 | 常见别名 / 英文 | 定义 |
|---|---|---|---|
| 1 | `extreme_wide` 极远景 | EWS / extreme long shot | 环境占绝大部分，人物很小或不可辨 |
| 2 | `wide` 远景/全景 | WS / long shot | 展示完整人物及周边环境，环境仍占重要比例 |
| 3 | `full` 全身景 | FS / full shot | 以人物全身为主要画面单位，头脚基本完整 |
| 4 | `medium_wide` 中远景 | MWS / medium long shot / cowboy shot（部分语境） | 人物大约膝部以上，兼顾动作和环境 |
| 5 | `medium` 中景 | MS / medium shot | 人物大约腰部以上，适合呈现交流和手势 |
| 6 | `medium_close` 中近景 | MCU / medium close-up | 人物大约胸部或肩部以上，突出表情并保留部分动作 |
| 7 | `close` 近景 | CU / close-up | 人脸或物件占画面主要面积 |
| 8 | `extreme_close` 特写/大特写 | ECU / extreme close-up | 仅展示面部局部、手指、物件细节等 |
| 9 | `macro` 微距 | macro | 以极近距离呈现微小纹理或细节；强调镜头/拍摄尺度，不必然等同于人物 ECU |

### 景别标注规则

- 多人画面按**主要叙事主体**判定尺度，并在 `subject_reference` 写清人物/对象。例如 `medium + subject_reference: two_people`。
- 人物头脚是否完整属于取景边界；人物在画面里大小属于景别。必要时另加 `framing.crop: head_cut / feet_cut / full_body`。
- “远景/全景/大全景”在不同团队和地区叫法并不完全统一。项目应固定一套词表，并在样例中定义，不要混用别名。
- `shot_scale` 不标主体数量；“双人镜头”请标在 `subject.configuration`。

## 5. C 类：主体配置 `subject.configuration`

| 标签 | 含义 |
|---|---|
| `single` | 一个主要主体 |
| `two` | 两个主要主体 |
| `small_group` | 小群体，建议 3–5 个主要主体 |
| `large_group` | 较大群体、队伍或人群 |
| `crowd` | 人群作为整体或环境组成部分，而非逐个主要主体 |
| `object_only` | 主要主体是一个物件/产品 |
| `multiple_objects` | 多个物件共同构成主要主体 |
| `environment_only` | 无明确人物主体，主要呈现环境/空间 |

另加可选字段：`primary_subject_id`、`secondary_subject_ids`、`subject_count_visible`、`subject_count_implied`。如果画面里人物很多但只有一人是焦点，配置标 `large_group`，同时指定主主体 ID，不能只靠 `single` 表达“主角突出”。

## 6. D 类：机位与拍摄角度 `camera.position`

机位至少分为**垂直角度**、**水平朝向**和**摄影机高度**。这几个值不能相互替代。

### D1. 垂直角度 `vertical_angle`

| 标签 | 定义 |
|---|---|
| `eye_level` 平视 | 镜头大致与主体眼睛同高 |
| `low_angle` 仰角 | 镜头低于主体，朝上拍 |
| `high_angle` 俯角 | 镜头高于主体，朝下拍 |
| `overhead` 顶视 | 近乎垂直向下，观察主体上方平面 |
| `undershot` 极低仰视 | 摄影机贴近地面或主体下方，强烈向上看 |
如角度需要精确，可写 `vertical_angle_degrees`，例如 `-25` 表示轻微仰视；必须在项目内统一正负号约定。

### D2. 水平朝向 `horizontal_view`

| 标签 | 定义 |
|---|---|
| `front` 正面 | 主体正面朝向摄影机 |
| `three_quarter_front` 前侧/四分之三正面 | 摄影机看到主体正面与一侧 |
| `profile` 侧面 | 主要呈现主体侧面轮廓 |
| `three_quarter_rear` 后侧/四分之三背面 | 摄影机看到主体背面与一侧 |
| `rear` 背面 | 摄影机位于主体后方 |
| `overhead_directional` 斜顶视 | 从上方斜向观察；若接近垂直向下用 `overhead` |

这里描述**静态视角**。摄影机在镜头中从正面绕到侧面时，应在 `camera.motion.path_description` 写弧线/环绕，而不是把多个水平角度当作同一个静态标签。

### D3. 摄影机高度 `camera_height`

| 标签 | 定义 |
|---|---|
| `ground_level` 地面高度 | 靠近地面或主体底部 |
| `knee_level` 膝部高度 | 机位明显低于腰部 |
| `waist_level` 腰部高度 | 机位约与主体腰部同高 |
| `chest_level` 胸部高度 | 机位约与主体胸部同高 |
| `eye_level` 眼平高度 | 机位约与主体眼睛同高 |
| `overhead_height` 高位 | 机位在主体上方，但不一定垂直向下 |
| `aerial_height` 空中高位 | 无人机、直升机或高空机位 |

`high_angle` 是向下看的角度；`aerial_height` 是机位所在高度。无人机可以用平视方向横飞，也可以顶视向下拍，两者需分开标注。

### D4. 画框水平状态 `roll_orientation`

- `level` 水平
- `dutch_left` 地平线向左倾斜
- `dutch_right` 地平线向右倾斜

这是镜头的静态画框方向；如果镜头中画框持续旋转，应另标 `camera.motion.type: roll_clockwise / roll_counterclockwise`。

## 7. E 类：视点与观众关系 `viewpoint`

| 标签 | 定义 | 易混概念 |
|---|---|---|
| `objective` 客观视点 | 画面不明确代表某个角色的眼睛 | 不是“绝对中立”；摄影机仍可带有明显风格 |
| `subjective_pov` 主观视点 | 画面模拟指定角色当下所见 | POV 可与手持、移动和任意景别组合 |
| `observer` 观察者视点 | 远距离或旁观位置观察事件 | 不等于长焦镜头；观察感也由景别和空间关系形成 |
| `surveillance` 监控视点 | 固定、隐蔽、受限或高位的监视式镜头 | 不是仅凭高角度即可判定；需要叙事意图或设备视角依据 |
| `direct_address` 直视观众 | 主体明确看向镜头/观众 | 和 POV 不同：人物看镜头不代表镜头属于人物视点 |

推荐把人物 POV 绑定到角色：`viewpoint: subjective_pov`、`viewpoint_subject_id: character_A`。

## 8. F 类：摄影机运动 `camera.motion`

将运动分为**空间平移、机位旋转、镜头焦距变化、复合路径**；稳定方式另列。`tracking` 是跟随主体的关系，也应与实际路径分开。

### G1. 运动类别与规范标签 `type`

| 运动类别 | 规范标签 | 定义 |
|---|---|---|
| 静止 | `locked_off` | 摄影机位置和朝向固定 |
| 轴向平移 | `dolly_in` / `dolly_out` | 摄影机沿主体视线方向靠近/远离 |
| 横向平移 | `truck_left` / `truck_right` | 摄影机在空间中向左/右移动 |
| 垂直平移 | `pedestal_up` / `pedestal_down` | 摄影机整体垂直升/降 |
| 水平旋转 | `pan_left` / `pan_right` | 机位大致不变，向左/右摇摄 |
| 垂直旋转 | `tilt_up` / `tilt_down` | 机位大致不变，向上/下俯仰 |
| 光轴旋转 | `roll_clockwise` / `roll_counterclockwise` | 摄影机围绕镜头轴滚转 |
| 焦距变化 | `zoom_in` / `zoom_out` | 机位基本不变，焦距变化 |
| 弧线路径 | `arc_left` / `arc_right` | 摄影机沿主体周边部分圆弧移动 |
| 环绕路径 | `orbit_left` / `orbit_right` | 摄影机绕主体运动；角度需另写 |
| 垂直弧线 | `crane_up` / `crane_down` | 摄影机沿较大垂直/弧形轨迹升降 |
| 快速摇摄 | `whip_pan_left` / `whip_pan_right` | 快速水平摇摄，常伴运动模糊 |
| 复合变焦 | `dolly_zoom_in` / `dolly_zoom_out` | 推轨与反向变焦同时发生 |
| 不规则运动 | `handheld_motion` | 机位带有人为手持的不规则修正和晃动 |

方向字段应统一采用“摄影机自身方向”，并给团队明确坐标约定。例如 `truck_left` 表示镜头从摄影机视角向左横移，不代表主体在屏幕上向左移动。

### G2. 跟随关系 `tracking_relation`

- `none` 不跟随主体
- `follow_from_front` 正面倒退跟随
- `follow_from_rear` 后方跟随
- `follow_profile` 侧面平行跟随
- `lead` 摄影机在前方引导主体
- `reframe_to_subject` 主体在动，摄影机调整画框或朝向以保持主体可见
- `independent` 摄影机按自身路线移动，不依主体运动

跟拍方向不是 `dolly` 的同义词。正面跟拍通常是摄影机倒退，侧面跟拍通常是 truck，背后跟拍可以用 dolly、稳定器或车辆拍摄；如需表达几何路径，把它们分别标注。

### G3. 运动参数 `parameters`

- `speed`: `very_slow / slow / medium / fast / very_fast`
- `acceleration`: `constant / ease_in / ease_out / accelerate / decelerate / abrupt_stop`
- `amplitude`: `subtle / small / medium / large / full_360`
- `stability`: `locked / smooth / stabilized / lightly_handheld / shaky / chaotic`
- `duration_seconds`: 运动实际持续时间
- `start_state` / `end_state`: 起止机位或景别
- `path_description`: 路径自然语言补充

若一个镜头有多个阶段，使用有序 `motion_beats`，不要把互相冲突的速度和方向同时堆在一个值里。

### G4. 支撑方式 `camera_support`

支撑方式是设备/质感标签，不是路径：`tripod`、`dolly_rig`、`steadicam`、`gimbal`、`handheld`、`jib_crane`、`drone`、`vehicle_rig`、`body_mount`、`motion_control_rig`。例如“gimbal + forward tracking”是稳定方式与移动路径组合；“handheld”也可能原地持机，并非一定跟拍。

## 9. G 类：主体运动与场面调度 `subject.action`

这一轴只描述画面中人物、动物和物件的变化，不描述摄影机如何移动。

### H1. 主体动作 `action_type`

- `stationary` 静止/保持姿势
- `gesture` 手势、面部微动作
- `body_action` 坐、站、转身、弯腰、跳跃等
- `locomotion` 行走、奔跑、爬行、飞行、车辆移动
- `interaction` 人物/物件之间的交互
- `transformation` 形态/状态变化
- `environmental_motion` 风、雨、烟、火、水流等环境运动

### H2. 路径、方向和节拍

为主要主体写 `direction`、`path`、`speed`、`start_state`、`end_state`，并可拆成 `beats`。示例：`A 从画面左侧走入 → 在桌边停下 → 拿起杯子 → 看向窗外`。屏幕方向（画面左/右）要和空间方向（朝门口/远离摄影机）区分。

### H3. 场面调度 `blocking`

记录主体在场景中的位置、相互距离、视线和遮挡关系：`screen_position`、`depth_plane`、`eyeline_target`、`facing_direction`、`relative_position_to`、`enters_frame`、`exits_frame`。场面调度描述人如何在空间里移动；摄影机路径仍写在 `camera.motion`。

## 10. H 类：镜头、焦点与景深 `optics`

### I1. 焦距观感 `focal_length_look`

以观感标签优先，具体毫米数作为可选参数：`ultra_wide`、`wide`、`normal`、`long_lens`、`telephoto`、`macro`。广角/长焦描述视角和透视观感；`close_up` 描述景别，不能互相替代。写“85mm”时需说明传感器/画幅或只把它当作大致风格参考。

### I2. 对焦状态 `focus.mode`

- `single_subject_focus` 单一主体清晰
- `selective_focus` 选择性浅焦
- `deep_focus` 前中后景均清晰
- `soft_focus` 柔焦/整体不锐
- `rack_focus` 焦点在镜头内从一个对象/距离转到另一个
- `focus_pull_to_subject` 焦点拉到指定主体
- `focus_hold` 焦点保持不变
- `focus_breathing_visible` 可见呼吸效应（只在明确需要时）

另设 `focus_start_target`、`focus_end_target`、`depth_of_field: shallow / medium / deep`。rack focus 是**清晰面切换**，不是摄影机转向或景别变化。

### I3. 光学/成像特征 `image_character`

可选标签：`bokeh`、`lens_flare`、`motion_blur`、`distortion`、`vignette`、`chromatic_aberration`、`anamorphic_streak`、`film_grain`、`shutter_smear`。这是镜头呈现特征，不属于摄影机运动；同时要区别后期视觉效果与物理镜头效果。

## 11. I 类：时间、镜头长度与剪辑衔接 `temporal_edit`

### J1. 镜头连续性与长度

- `single_take` 单次连续拍摄/生成
- `long_take` 相对较长、保持连续的镜头（项目需自定时长门槛）
- `short_insert` 短插入镜头
- `duration_seconds` 明确时长
- `real_time` 实时
- `slow_motion` 慢动作
- `fast_motion` 快动作
- `time_lapse` 延时
- `freeze_frame` 定格

“长镜头”没有跨作品统一的秒数界限，标注库应规定本项目阈值；“单镜头生成”也不一定等于长镜头。

### J2. 镜头内部时间组织 `beat_timing`

用时间点或比例描述运动/事件发生位置，例如 `0–2s hold; 2–5s dolly_in; 5–6s settle`。时间节拍属于镜头内部组织；如果发生了剪切，应开始一个新 `shot_id`。

### J3. 剪辑衔接 `edit.transition`

这些是镜头间的编辑关系，不是单个镜头的景别或运动：`hard_cut`、`match_cut`、`action_match`、`eyeline_match`、`cross_cut`、`jump_cut`、`whip_pan_transition`、`dissolve`、`fade_in`、`fade_out`、`wipe`。在数据中写入 `incoming_transition` / `outgoing_transition`，说明衔接发生在哪一侧。

## 12. J 类：意图与情绪 `intent`（解释层）

情绪和叙事意图是对镜头效果的解释，不是摄影机技术属性。建议限制为可选标签，并保留触发事件和证据：

- `intimacy` 亲密
- `isolation` 孤立
- `tension` 紧张
- `reveal` 揭示
- `disorientation` 失衡/眩晕
- `dominance` 支配/权力
- `vulnerability` 脆弱
- `immersion` 沉浸
- `scale` 规模感
- `comic_emphasis` 喜剧重音

至少区分 `intended_effect`（导演/提示词意图）和 `observed_effect`（观看者实际读到的效果）。同一镜头可以有多个合理解释；情绪标签不应替代镜头动作描述。

## 13. K 类：关联的画面与声音字段

这些属性常出现在视频提示词中，但不属于镜头技术树。单独归类，避免混淆：

| 字段 | 示例值 |
|---|---|
| `lighting` 光线 | `soft_window_light`、`backlight`、`hard_key`、`low_key` |
| `color_palette` 色彩 | `warm_amber`、`cool_blue`、`desaturated` |
| `visual_style` 风格 | `live_action`、`documentary`、`anime`、`stop_motion`、`film_noir` |
| `sound.dialogue` 对白 | 说话者 ID、台词、时机 |
| `sound.ambience` 环境声 | 雨声、室内底噪、街道人声 |
| `sound.sfx` 音效 | 关门、脚步、碰撞 |
| `sound.music` 音乐 | 类型、节奏、进入/退出点 |
| `delivery.aspect_ratio` 输出画幅 | `16:9`、`9:16`、`1:1` |
| `delivery.resolution` 输出分辨率 | 模型或项目支持的规格 |

风格词、情绪词和摄影机标签可以并用，但在标注数据中要放进不同字段。

## 14. 可复用的单镜头标签结构

下面是建议的 YAML 结构。按任务只填写必要字段；未知项用 `unknown` 或留空，不要猜测。

```yaml
shot_id: S01_SH03
scope:
  scene_id: SC01
  sequence_id: SEQ01
role:
  primary: reaction
framing:
  scale: medium_close
  crop: shoulders_up
subject:
  configuration: single
  primary_subject_id: person_A
  action_type: gesture
  action_beats:
    - looks_down_at_letter
    - hand_stops_moving
camera:
  position:
    height: eye_level
    vertical_angle: eye_level
    horizontal_view: three_quarter_front
  viewpoint:
    type: objective
  motion:
    type: dolly_in
    direction: forward
    speed: slow
    acceleration: ease_in
    amplitude: small
    tracking_relation: independent
    stability: smooth
    duration_seconds: 3
    end_state: face_closeup
optics:
  focal_length_look: normal
  depth_of_field: shallow
  focus:
    mode: rack_focus
    start_target: letter
    end_target: person_A_eyes
temporal_edit:
  duration_seconds: 5
  continuity: single_take
  beat_timing: "0-1s hold; 1-4s dolly_in with focus shift; 4-5s settle"
intent:
  intended_effect: realization
  trigger: person_A recognizes the date on the letter
```

## 15. 三个完整标注示例

### 示例 A：人物读信后的情绪反应

```text
role: reaction
framing.scale: medium_close
subject.configuration: single
camera.position: eye_level, three_quarter_front
viewpoint: objective
camera.motion: slow_dolly_in, small_amplitude, smooth
subject.action: eyes move from letter to off-screen doorway; hand stops
optics.focus: rack_focus(letter -> eyes)
temporal: single_take, 6s
intent: tension, realization
```

### 示例 B：两人对话中的权力关系

```text
role: coverage
framing.scale: medium_wide
subject.configuration: two
camera.position: low_angle toward standing_subject
viewpoint: objective
camera.motion: locked_off
subject.blocking: standing_subject remains still; seated_subject looks up
optics.focus: deep_focus
temporal: real_time, 8s
intent: dominance, vulnerability
```

### 示例 C：从空间建立到主角出现

```text
role: establishing
framing.scale: extreme_wide
subject.configuration: single
subject.visibility: enters_frame_at_2s
camera.position: aerial_height, high_angle
camera.motion_beats:
  - 0-2s: locked_off on empty station platform
  - 2-5s: crane_down and tilt toward the far entrance
  - 5-7s: slow dolly_in as one traveler enters frame
subject.action: traveler walks into frame and stops beneath the station clock
temporal: single_take, 7s
intent: reveal, anticipation
```

## 16. 组合规则与常见错标

| 错误 | 为什么不清楚 | 建议改法 |
|---|---|---|
| 把“全景、俯拍、推镜头、紧张”都放进一个 `shot_type` | 分别是景别、角度、运动、意图 | 拆到 `framing.scale`、`camera.position`、`camera.motion`、`intent` |
| 把“双人镜头”当成景别 | 它表示主体数量，不表示取景范围 | 用 `subject.configuration: two`，另选景别 |
| 把“POV”当成景别或运动 | POV 是观看关系，可搭配任意景别和运动 | 记录在 `viewpoint` |
| 把“跟拍”当成唯一运动类型 | 跟拍描述摄影机与主体的关系，实际路径可能是 dolly、truck 或手持 | 分别标 `tracking_relation` 和 `camera.motion.type` |
| 把“推镜头”与“变焦”当同义词 | 推轨改变机位和透视；变焦改变焦距 | 选择 `dolly_in/out` 或 `zoom_in/out`；需要时组合标 `dolly_zoom` |
| 把“向上摇”和“升机”合成一个标签 | tilt 是绕机位转动，pedestal/crane 是机位移动 | 分别记录旋转与空间平移，可同时出现 |
| 把 rack focus 写成镜头运动 | 它改变清晰焦点，不一定改变机位 | 放在 `optics.focus.mode` |
| 把静态倾斜画框写成动态 roll | 静态倾斜描述机位/画框状态；roll 描述镜头在时间中的旋转 | 前者标 `camera.position.roll_orientation`，后者标 `camera.motion.type: roll_clockwise / roll_counterclockwise` |
| 把慢动作写成慢速运镜 | 慢动作改变影像时间；慢速运镜改变摄影机移动速度 | 分别标 `temporal.playback_speed` 和 `camera.motion.speed` |
| 把“建立镜头”默认成远景 | 建立是叙事功能，景别可以不同 | `role: establishing` 与 `framing.scale` 独立标注 |
| 把镜头间转场记为镜头运动 | 剪切/叠化发生在镜头边界，不是镜头内的摄影机轨迹 | 在 `temporal_edit.incoming/outgoing_transition` 标注 |

## 17. 标注一致性检查

- 每个标签是否只回答一个明确问题？
- 一个字段里是否混进不同分类轴？
- 静态视角和镜头内的运动是否区分？
- 摄影机运动和主体运动是否分别记录？
- 运动的方向是摄影机方向、画面方向，还是主体路径？是否写清？
- “全景”“跟拍”“主观镜头”等多义词是否按本项目定义使用？
- 观察标签还是提示词意图？是否区分 `observed` 与 `intended`？
- 画面里看不到或无法确定的标签，是否标为 `unknown` 而不是推断？
- 连续视频中景别、视角或摄影机状态发生显著变化时，是否需要拆成多个 `shot_id`？

## 18. 参考资料

- [Runway — Camera Terms, Prompts, & Examples](https://help.runwayml.com/hc/en-us/articles/47313504791059-Camera-Terms-Prompts-Examples)：提供景别、摄影机角度、运动和焦点技术的术语与视频生成示例。
- [Google DeepMind — How to Create Effective Prompts with Veo](https://deepmind.google/models/veo/prompt-guide/)：把取景/运动、风格、光线、人物、场景、动作和对白作为分开的提示要素。
- [Google AI for Developers — Veo Prompt Guide](https://ai.google.dev/gemini-api/docs/veo)：分别列出主体、动作、风格、摄影机位置/运动、焦点/镜头效果和环境氛围。
- [Berklee Online — Digital Cinematography Fundamentals](https://online.berklee.edu/courses/digital-cinematography-fundamentals)：课程将镜头尺寸、机位高度、主观/客观视点、摄影机运动、光线和剪辑连续性分别教学。
- [CICLIC — Film Analysis Vocabulary](https://vocabulaire.ciclic.fr/en)：电影分析词汇资源，可对照景别、角度等视觉语言术语。
- [MiniMax H3 — Official Video Prompt Writing Guide](https://huggingface.co/MiniMaxAI/MiniMax-H3/blob/main/docs/VIDEO_PROMPT_WRITING_GUIDE_base_en.md)：将镜头、机位运动、幅度、速度和时间段放入独立的提示描述中。

---

**用途说明：**本体系是一套适合视频制作与生成提示词的工作词表，不是唯一的电影术语标准。团队可保留各维度和边界，再按项目需要增删受控值，并在正式标注前用一组共同样例校准定义。
