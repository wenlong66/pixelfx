<p align="center">
  <img src="docs/readme/montage.gif" width="800" alt="同时播放的像素特效：篝火、燃烧的骷髅、瀑布、等离子球、传送门、龙卷风等。">
</p>

# pixelfx.

[English](README.md) · 简体中文

**244 个像素艺术特效和动画，每一个都是一小段代码。** 火焰、爆炸、魔法、天气、界面、拾取物、小动物等应有尽有。你可以实时预览、更改配色和镜像，将它们放进浏览器游戏，或者导出精灵表用于任何引擎。项目还提供一个 Claude Code 技能，以挑剔的像素画师的方式制作新特效：起稿、渲染、观察、改进。

每个涉及角色的特效都有**配套动作**：角色蓄力、出招、在命中瞬间停顿并收招，目标也会受到打击。另有 12 个完整招式将特效串联成从蓄力到收招的攻击。

**[打开特效画廊](https://patch-gg.github.io/pixelfx/)** · [完整目录](docs/CATALOG.md) · [角色动作](#角色动作) · [技能](#技能) · [制作自己的特效](#制作自己的特效) · [工作原理](#工作原理)

这种方法来自 [Hugo Duprez](https://x.com/HugoDuprez) 的文章 [How to make (good) pixel art effects with Claude](https://www.spritefusion.com/blog/how-to-make-good-pixel-art-effects-with-claude)：把代码当作可塑的材料，相信自己的眼睛，并提供参考素材。他在 Sprite Fusion 发布的[开源特效](https://www.spritefusion.com/pixel-art-vfx)树立了品质标杆；`src/core/sim.js` 和文件开头注明了来源的特效改编自他的代码。本仓库将这一思路扩展为一个特效库，并提供技能来持续创作。

## 包含什么

| 类别 | 数量 | 示例 |
|---|---|---|
| **招式**（moves） | 12 | 射箭、冲刺斩、地震践踏、投掷火球、砸地、神圣打击…… |
| **火焰**（fire） | 12 | 火盆、篝火、蜡烛、火焰吐息、火环、火焰龙卷…… |
| **爆炸**（explosions） | 12 | 木桶爆炸、大爆炸、卡通爆炸、泥土爆炸、烟花绽放、烟花火箭…… |
| **水**（water） | 12 | 气泡、水滴、喷泉、间歇泉、雨滴飞溅、涟漪…… |
| **冰霜**（ice） | 10 | 暴雪漩涡、冻结、冰霜吐息、冰霜新星、冰晶、冰枪…… |
| **闪电**（lightning） | 11 | 球状闪电、闪电链、蓄能、触电、落雷、等离子球…… |
| **科幻**（sci-fi） | 12 | 电磁脉冲、能量核心、力场、故障、全息影像、喷气背包…… |
| **近战**（melee） | 12 | 爪击、匕首连击、双重斩、重锤猛击、重劈、镰刀横扫…… |
| **冲击**（impacts） | 11 | 钝击、子弹撞击、暴击、砸地、重落地、命中闪光…… |
| **投射物**（projectiles） | 13 | 箭矢、弹道曳光、炮弹、能量盘、信号弹、追踪导弹…… |
| **生成与消亡**（spawn & death） | 10 | 金币掉落、瓦解、燃烧消散、破壳、嘭的一声消失、重生光束…… |
| **奥术**（arcane） | 12 | 奥术光束、奥术飞弹、奥术球、闪现进入、闪现离开、施放魔法阵…… |
| **神圣**（holy） | 10 | 祝福、神圣护盾、羽毛迸发、光环、治疗、治疗环…… |
| **暗影**（shadow） | 12 | 黑洞、诅咒、暗影爆炸、日蚀、抓取之手、咒印…… |
| **状态**（status） | 13 | 流血、燃烧、魅惑、混乱、诅咒、冻结…… |
| **自然**（nature） | 13 | 酸液飞溅、巨石、地刺、治愈之花、落叶风暴、毒云…… |
| **天气**（weather） | 12 | 极光、暴雪、樱花、云、落叶、萤火虫…… |
| **界面**（ui） | 12 | 成就、收集金币、连击计数、彩纸、倒计时、暴击数字…… |
| **拾取物**（pickups） | 11 | 开宝箱、旋转金币、拾取迸发、水晶碎片、旋转宝石、拾取爱心…… |
| **转场**（transitions） | 10 | 百叶窗、燃烧消失、CRT 关机、菱形擦除、故障擦除、光圈…… |
| **小动物**（critters） | 12 | 蝙蝠、鸟、蝴蝶、眼球、鱼群、飞行骷髅…… |

每个特效都是 `src/fx/<category>/<name>.js` 中的一个文件。[完整目录](docs/CATALOG.md)列出了所有特效。

## 使用特效

**在浏览器游戏中**（canvas、Phaser、PixiJS、Kaplay、three.js），直接使用代码，无需依赖。

```js
import { createFrame, clearFrame, createFx, composite } from "pixelfx";
import * as frostNova from "pixelfx/fx/ice/frost-nova";

const frame = createFrame(320, 180);                   // 游戏的像素分辨率
const img = new ImageData(320, 180);
const fx = createFx(frostNova, { x: 160, y: 140 });   // 还可设置 flip、angle、seed、palette、light 等

function tick(dt) {
  fx.update(dt);
  clearFrame(frame);
  fx.draw(frame, "back");                              // 绘制在角色后方
  drawCharacters();
  fx.draw(frame, "front");
  composite(frame, img.data, backgroundColor);         // 先绘制颜色，再叠加光照
  ctx.putImageData(img, 0, 0);                         // 使用 image-rendering: pixelated 放大
}
```

通过 `npm i github:patch-gg/pixelfx` 从 GitHub 安装，或者复制 `src/core` 和需要的特效。

**在任何引擎中**（Godot、Unity、GameMaker、Defold、LÖVE、Bevy），可以使用精灵表：

```bash
node tools/render.mjs ice/frost-nova --sheet
```

这会将所有帧排列成一张透明 PNG，并生成 Aseprite 格式的 JSON（包含帧矩形、持续时间，以及记录 fps、循环、列数和枢轴点的 `meta.pixelfx`）。Phaser 的图集加载器和 Godot、Unity 的 Aseprite 导入器都能读取它。运行 `node tools/sheets.mjs` 可以将整个特效库导出到 `sheets/`。

所有特效都接受 `palette`（由深到浅排列的十六进制颜色），因此无需修改代码就能让火焰变蓝或让治疗效果发出粉光：

```bash
node tools/render.mjs fire/torch --gif --opts '{"palette": ["#102","#215","#43a","#8cf","#cff","#fff"]}'
```

## 角色动作

<p align="center">
  <img src="docs/readme/acts.gif" width="800" alt="灰色人偶攻击稻草靶：三连剑击、重锤猛击、砸地、神圣打击、冰霜新星和带残影的奔跑，每个动作都有蓄力、命中停顿和收招。">
</p>

单独的特效看起来像贴纸。真正让打击有说服力的是配合它的身体动作：预示攻击的蓄力、命中时的停顿、目标被击退，以及收招。因此，61 个涉及角色的特效和 12 个招式都描述了身体何时做什么：

```js
export const act = {
  target: { x: 58 },
  poses: [
    [-0.55, GUARD],            // 角色先于特效开始动作（t = 0 是特效的第一帧）
    [-0.3, WIND_UP],
    [0, CUT, "snap"],          // "snap" 保持上一姿势，直到关键时刻才切换
    [0.06, FOLLOW_THROUGH, "snap"],
    [0.86, GUARD],
  ],
  hits: [{ t: 0.06, stop: 0.14, shake: 0.18, amp: 2, push: 2.8, straw: 18 }],   // 命中时停顿 0.14 秒
};
```

将 `createFx` 换成 `createAct`，即可**播放完整动作**，包括角色和目标：

```js
import { createFrame, clearFrame, createAct, composite } from "pixelfx";
import * as slash from "pixelfx/fx/melee/sword-slash";

const act = createAct(slash, { x: 80, y: 140 });

function tick(dt) {
  act.update(dt);                                  // 命中停顿期间会自动静止
  clearFrame(frame);
  act.draw(frame);                                 // 用 { body: false } 或 { target: false } 隐藏对应对象
  composite(frame, img.data, backgroundColor);
  const [sx, sy] = act.shake;                      // 按此偏移量震动画面
  ctx.putImageData(img, sx, sy);
}
```

**已经有自己的角色？** 特效 ID 没有改变，精灵表仍然只包含特效，因此现有游戏可以继续使用。（绘制在身体上的特效会绘制到默认人偶上；如果要发布这些特效，请重新生成。）时序以普通数据表示：读取 `slash.act`，或者精灵表 JSON 中的 `meta.pixelfx.act`（`windup`、`recovery`、包含 `t` 和 `freeze` 的 `hits`、`poses`）。在特效开始前提前相应时间播放角色的蓄力动画，并在每次命中时停止更新 `freeze` 秒。

绘制在身体上的特效（冰霜、毒素、扫描线）以站立角色为基准，并跟随角色移动：姿势中的每个像素都知道自己来自站立身体的哪个像素。`node tools/act.mjs <id> --sheet` 可将包含角色的完整动作烘焙成精灵表。

你也可以使用身体本身：`skeleton(pose)` 提供姿势中每个关节的 3D 坐标，方便附加自己的内容；`posedParts(pose)` 则分别绘制各个身体部位。

## 技能

`pixelfx` 技能让 Claude Code 能够查找、使用、导出和制作特效。将其作为插件安装：

```
/plugin marketplace add patch-gg/pixelfx
/plugin install pixelfx@pixelfx
```

或者将 `skills/pixelfx` 复制到 `~/.claude/skills/`。然后直接提出需求，例如：

- “给我的法师大招加一个冰霜新星。”
- “制作五种拾取金币时的闪光效果，展示其中最好的两种。”
- “将火焰和爆炸类别导出为 Godot 可用的精灵表。”
- “这个命中特效感觉太轻飘了，让它更有冲击力。”
- “给这个法术加上蓄力动作，并让敌人做出反应。”

## 制作自己的特效

```bash
node tools/new.mjs arcane/my-spell       # 创建可运行的起点（添加 --loop 可创建循环特效）
node tools/render.mjs arcane/my-spell    # 查看 records/arcane/my-spell/contact.png
node tools/check.mjs arcane/my-spell     # 检查画面溢出、循环接缝、未设种子的随机数和耗时过长的帧
```

接着反复迭代：查看接触表，修复最明显的问题，再次渲染。`--gif` 和 `--mp4` 可以展示运动，`--all` 可以展示每一帧。`node tools/act.mjs arcane/my-spell` 以连续的 12 fps 步进展示动作，因为抽样帧无法显示所有运动问题。[docs/AUTHORING.md](docs/AUTHORING.md) 介绍了特效规范、绘图 API 和质量标准。运行 `node tools/serve.mjs` 可以在本地启动包含你的特效的画廊。

## 工作原理

- **两种特效，不使用图片。** 纯函数特效导出 `draw(p, t, o)`，仅根据时间 `t` 生成对应帧，因此循环时没有接缝。模拟特效导出 `create(o)`，以 120 Hz 更新实际的粒子、碎片和光照，因此火花弹跳和烟雾翻滚的效果不受帧率影响。
- **两个缓冲区。** `color` 存放不透明、抖动或真正半透明的像素；`light` 存放叠加在上方的加法光照。光照缓冲区让炽热的物体看起来发亮。
- **一切随机都有种子。** 每个随机数都来自特效的种子，因此第 40 帧每次都相同。
- **局部坐标。** 特效围绕自己的原点绘制；绘制器负责定位、镜像和旋转。有方向的特效朝右。一个 24×38 的默认角色用于展示状态、光环和命中特效。
- **真正的骨骼，逐像素绘制。** 灰色人偶刻意保持中性，让目光集中于动作，也让各种特效颜色清晰可见。它使用从骨盆到脊椎、肩膀的固定长度 3D 骨骼，以少量平涂色和一道干净的描边渲染，类似 Dead Cells 的精灵。它可以扭转、弯曲和真正转身，由深度决定前后关系。姿势以 12 fps 步进保持，类似手绘精灵，而粒子以完整速率运行。
- **与 Sprite Fusion 的 VFX 使用相同的帧结构**（`{ w, h, ox, oy, color, light }`），因此两个库可以绘制到同一帧中。

## 许可证

代码和特效均采用 MIT 许可证，可用于任何用途，包括商业用途。部分内容改编自 Hugo Duprez 的 MIT 特效和 Hollow Crown 的 MIT 源码；`gallery/fonts` 中的 Patch Pixel 字体属于 Patch 自有字体，不在此许可证范围内。详见 [NOTICE.md](NOTICE.md)。

由 [Patch](https://patch.gg) 制作。
