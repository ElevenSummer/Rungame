# Rungame — Pico VR 金币跑酷（期末大作业）

三人协作的 Unity + PICO VR 课程期末项目：第三人称自动跑酷，用手柄四向操作躲避障碍、收集金币，以**跑动距离（米）**作为成绩，后端提供账号注册登录与按已注册用户最佳距离排序的排行榜。

## 项目范围

**要做**

- 固定第三人称跟随；手柄左右变道、上跳、下滑（A/X、B/Y 为备选键）
- 1 个主题关卡；场景内只有金币（可收集）和障碍（危险元素）
- 成绩 = 本局跑动距离（米，向下取整）；排行榜按已注册用户最佳距离降序
- 前端用户名/密码注册登录，未登录不可进入游戏
- 前后端联调、断网成绩缓存与补传、PICO 真机打包演示

**明确不做**

- 新手教学流程（大厅只保留静态按键说明）
- 多主题关卡、主题切换、按周更新
- 磁铁/护盾/能量块/冲刺等任何道具（场景中除金币外无可拾取物）
- 角色外观装配与解锁
- 内购、充值、付费活动、广告变现
- 第一人称模式、游客模式

详细方案见课程文档《Pico VR金币跑酷游戏_期末大作业最终方案.md》（V2.0）。

## 开发环境

| 项 | 当前值 |
|----|--------|
| Unity | **2022.3.62f3c1**（建议用 Unity Hub 安装同版本） |
| PICO SDK | PICO Unity Integration SDK `release_2.5.0` |
| UI | UGUI + TextMeshPro 3.0.7 |
| 输入 | 当前为 XR 工程骨架，手柄输入抽象待按 Input System 接入 |
| 渲染 | 当前工程未启用 URP，如需按方案使用 URP 请后续自行接入 |

> ⚠️ **重要**：`Packages/manifest.json` 目前用**本机绝对路径**引用了三个本地包：
>
> - `com.coplaydev.unity-mcp` → `file:D:/download/unity-mcp-beta/unity-mcp-beta/MCPForUnity`
> - `com.unity.pico.livepreview` → `file:D:/download/Unity Live Preview Plugin-1.0.5-20250211`
> - `com.unity.xr.picoxr` → `file:D:/download/PICO-Unity-Integration-SDK-release_2.5.0/PICO-Unity-Integration-SDK-release_2.5.0`
>
> 队友克隆后这些路径无效，Unity 会报包加载失败。处理方式三选一：
>
> 1. 在各自电脑下载相同 SDK，并把包放到相同路径（最省事，但路径需一致）；
> 2. 把这三个包复制进仓库的 `Packages/` 目录，并把 manifest 改成 `file:包名` 形式（推荐，人人开箱即用，但仓库会变大）；
> 3. 去掉 MCP / LivePreview 这两个开发辅助包，仅保留 PICO SDK，后续按 Unity Package Manager 官方方式安装。

## 目录结构

```text
Assets/
  Resources/    PICO SDK 配置资产
  Scenes/       SampleScene.unity（示例场景，后续替换为游戏主场景）
  XR/           Loader 与 XR Settings
  XRI/          XR Interaction 配置
Packages/
  manifest.json 包依赖声明
  packages-lock.json
ProjectSettings/
  工程设置（含 XR、输入、画质、构建场景等）
.gitignore      Unity 生成物忽略规则
```

**注意**：`.meta` 文件必须一起提交，丢失会导致 Unity 资源 GUID 失效、引用丢失。`Library/`、`Temp/`、`Logs/`、`UserSettings/`、`*.csproj`、`*.sln` 已忽略，不要强制添加。

## 克隆与打开

```powershell
git clone https://github.com/ElevenSummer/Rungame.git
cd Rungame
```

1. 用 Unity Hub 打开该目录，选择 **Unity 2022.3.62f3c1**。
2. 按上面「重要」一节处理 `manifest.json` 的本地包路径，等 Unity 导入完成。
3. 打开 `Assets/Scenes/SampleScene.unity`，确认 Editor 内可进入 Play 模式。

## PICO 真机打包（简要）

1. `File → Build Settings`，切换平台到 **Android**，确认 `SampleScene`（后续替换为游戏场景）在构建列表中。
2. `Edit → Project Settings → Player`，设置包名（如 `com.example.coinrushvr`）、最低 API 与横竖屏。
3. 按 PICO SDK 文档完成 Android Manifest / 权限配置，装入小度或 PICO 开发者模式设备。
4. `Build And Run` 生成 APK 并安装；真机验证手柄四向操作、结算、排行榜与断网补传。

## 协作约定

- **分支**：`main` 为可演示主线，不直接提交；每人从 `main` 切 `feature/模块名` 或 `dev/姓名`，完成后开 Pull Request 合并。
- **提交前**：先 `git pull --rebase origin main`，再提交自己的改动；提交信息用 `模块: 做了什么`，例如 `UI: 完成结算面板` 或 `Backend: 实现排行榜接口`。
- **提交内容**：只提交 `Assets/`、`ProjectSettings/`、`Packages/` 和文档；不要提交 `Library/`、`Temp/`、构建产物、大体积临时文件。
- **冲突处理**：`.unity` 场景和 `.meta` 文件冲突时不要随便选一边，先和对方确认，再在 Unity Editor 里重新保存。
- **推送**：`git push -u origin <分支名>`；第一次推送新分支会设置跟踪关系。

## 建议分工（对应方案的 8 个实施步骤）

| 成员 | 主要负责 | 对应步骤 |
|------|----------|----------|
| A | 手柄输入、第三人称控制、单主题关卡与金币/障碍生成 | 步骤 2–3 |
| B | 游戏流程、UI（大厅/HUD/暂停/结算/排行榜）、反馈与性能 | 步骤 4–5 |
| C | 后端账号/成绩/排行榜 API、前后端联调、打包与真机测试 | 步骤 6–8 |

公共任务：步骤 1 环境初始化三人一起确认，步骤 8 的答辩演示共同验收。

## 当前状态

当前仓库是 Unity 工程骨架：PICO SDK 配置、示例场景、XR 设置已就位；玩法脚本、UI、后端服务尚未提交。按上方「建议分工」和最终方案第九章的 8 个步骤推进即可。
