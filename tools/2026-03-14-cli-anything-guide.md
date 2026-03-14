# CLI-Anything 使用指南

**项目地址**：https://github.com/HKUDS/CLI-Anything  
**Star 数**：12,708⭐  
**核心理念**：让任何软件都能被 AI Agent 原生调用

---

## 📖 什么是 CLI-Anything？

CLI-Anything 是一个自动生成 CLI 工具的框架，可以为任何有源代码的软件（GIMP、Blender、LibreOffice 等）生成 Agent 友好的命令行接口。

### 为什么 CLI 是 Agent 的最佳接口？

- ✅ **结构化 & 可组合**：文本命令匹配 LLM 格式，可链式调用
- ✅ **轻量 & 通用**：零依赖，跨平台
- ✅ **自描述**：`--help` 提供自动文档
- ✅ **Agent 优先设计**：结构化 JSON 输出，无需解析
- ✅ **确定性 & 可靠**：一致的结果，可预测的行为

---

## 🚀 安装 CLI-Anything

### 前置要求

1. **Python 3.10+**
2. **Claude Code**（用于生成 CLI）
3. **目标软件已安装**（如 GIMP、Blender 等）

### 安装步骤

#### 方法 1：通过 Claude Code 插件（推荐）

```bash
# 在 Claude Code 中运行
/cli-anything <software-repo-path>
```

#### 方法 2：手动安装插件

```bash
# 克隆仓库
git clone https://github.com/HKUDS/CLI-Anything.git
cd CLI-Anything

# 安装插件
cd cli-anything-plugin
pip install -e .

# 验证安装
cli-anything --version
```

---

## 🎯 使用 CLI-Anything

### 1. 生成 CLI（通过 Claude Code）

#### 在 Claude Code 中运行：

```bash
/cli-anything /path/to/software/repo
```

**7 阶段生成流程**：
1. **Discover**：分析源代码，识别核心功能
2. **Design**：设计 CLI 命令结构
3. **Implement**：生成 Python CLI 代码
4. **Test**：编写单元测试和 E2E 测试
5. **Validate**：运行测试验证
6. **Document**：生成文档
7. **Package**：打包为可安装的 Python 包

#### 生成后的目录结构：

```
<software>/
└── agent-harness/
    ├── cli_anything/
    │   └── <software>/
    │       ├── __init__.py
    │       ├── cli.py          # CLI 入口
    │       ├── commands/       # 命令实现
    │       └── tests/          # 测试
    ├── setup.py
    ├── README.md
    └── <software>.md           # 架构文档
```

### 2. 安装生成的 CLI

```bash
cd <software>/agent-harness
pip install -e .

# 验证安装
which cli-anything-<software>
cli-anything-<software> --version
```

### 3. 使用 CLI

#### 交互式 REPL 模式：

```bash
cli-anything-<software>
```

#### 命令行模式：

```bash
# 查看帮助
cli-anything-<software> --help

# 执行命令（人类可读输出）
cli-anything-<software> <command> [args]

# JSON 输出（Agent 友好）
cli-anything-<software> --json <command> [args]
```

#### 示例（假设是 GIMP）：

```bash
# 调整图片大小
cli-anything-gimp resize input.png 800x600 output.png

# JSON 输出（Agent 使用）
cli-anything-gimp --json resize input.png 800x600 output.png
# 输出：{"status": "success", "output": "output.png", "width": 800, "height": 600}
```

### 4. 运行测试

```bash
cd <software>/agent-harness

# 运行所有测试
python3 -m pytest cli_anything/<software>/tests/ -v

# 强制安装模式（推荐）
CLI_ANYTHING_FORCE_INSTALLED=1 python3 -m pytest cli_anything/<software>/tests/ -v -s
```

---

## 🔧 在 OpenClaw 中使用 CLI-Anything

### 场景 1：让 OpenClaw Agent 生成 CLI

```python
# 通过 sessions_spawn 调用 Claude Code
sessions_spawn(
    runtime="acp",
    agentId="claude-code",
    task="为 /path/to/software 生成 CLI-Anything 接口",
    mode="run"
)
```

### 场景 2：OpenClaw Agent 使用已生成的 CLI

```bash
# 通过 exec 工具调用
exec(command="cli-anything-gimp --json resize input.png 800x600 output.png")

# 解析 JSON 输出
import json
result = json.loads(output)
if result["status"] == "success":
    print(f"图片已调整为 {result['width']}x{result['height']}")
```

---

## 📦 已支持的软件（11 个）

| 软件 | 类型 | 测试数 | 说明 |
|------|------|--------|------|
| GIMP | 图像编辑 | 1,508 | 完整测试覆盖 |
| Blender | 3D 建模 | - | 需要无头模式 |
| LibreOffice | 办公套件 | - | 文档处理 |
| Inkscape | 矢量图形 | - | SVG 编辑 |
| Audacity | 音频编辑 | - | 音频处理 |
| FFmpeg | 视频处理 | - | 格式转换 |
| ImageMagick | 图像处理 | - | 批量操作 |
| Pandoc | 文档转换 | - | 格式转换 |
| SQLite | 数据库 | - | 数据查询 |
| Git | 版本控制 | - | 代码管理 |
| Vim | 文本编辑 | - | 文本处理 |

---

## ⚙️ 配置要点

### 1. 目标软件必须已安装

```bash
# 检查软件是否安装（以 GIMP 为例）
which gimp
# 或
ls /Applications/GIMP.app  # macOS
```

### 2. 配置软件路径（如果需要）

某些 CLI 可能需要配置文件：

```json
// ~/.config/cli-anything-<software>/config.json
{
  "software_path": "/Applications/GIMP.app/Contents/MacOS/gimp",
  "working_dir": "~/projects",
  "default_options": {
    "format": "png",
    "quality": 90
  }
}
```

### 3. 无头模式（GUI 软件）

对于 GUI 软件（如 Blender、GIMP），可能需要无头模式：

```bash
# Blender
blender --background --python script.py

# GIMP
gimp -i -b '(script-fu-command)' -b '(gimp-quit 0)'
```

生成的 CLI 会自动处理这些细节。

### 4. 权限配置

- **macOS**：首次运行可能需要授权
- **文件访问**：确保 CLI 有读写项目文件的权限

---

## ⚠️ 限制与注意事项

### 1. 需要强大的模型

- CLI-Anything 依赖前沿模型（Claude Opus 4.6、GPT-5.4）
- 弱模型可能生成不完整或错误的 CLI

### 2. 需要源代码

- 只能分析开源软件
- 闭源二进制效果差，需要反编译

### 3. 可能需要迭代

- 一次生成可能不完美
- 运行 `/refine` 优化 CLI 质量

### 4. 资源消耗

- 某些软件（如 Blender）渲染很吃资源
- 需要考虑 CPU/GPU/内存限制

---

## 🔄 迭代优化

如果生成的 CLI 不完美，可以运行：

```bash
# 在 Claude Code 中
/refine <software>/agent-harness

# 或者手动优化
cd <software>/agent-harness
# 编辑 cli_anything/<software>/commands/*.py
# 添加新命令或修复 bug
```

---

## 📚 文档参考

| 文档 | 说明 |
|------|------|
| [HARNESS.md](https://github.com/HKUDS/CLI-Anything/blob/main/cli-anything-plugin/HARNESS.md) | 方法论 SOP |
| [README.md](https://github.com/HKUDS/CLI-Anything/blob/main/cli-anything-plugin/README.md) | 插件文档 |
| [QUICKSTART.md](https://github.com/HKUDS/CLI-Anything/blob/main/cli-anything-plugin/QUICKSTART.md) | 5 分钟快速开始 |
| [PUBLISHING.md](https://github.com/HKUDS/CLI-Anything/blob/main/cli-anything-plugin/PUBLISHING.md) | 发布指南 |

---

## 🎯 实战流程总结

### 完整工作流（以 GIMP 为例）

#### 第一步：生成 CLI

```bash
# 在 Claude Code 中
/cli-anything /path/to/gimp/source
```

#### 第二步：安装 CLI

```bash
cd gimp/agent-harness
pip install -e .
which cli-anything-gimp  # 验证
```

#### 第三步：测试 CLI

```bash
# 查看帮助
cli-anything-gimp --help

# 测试基本功能
cli-anything-gimp --json version

# 测试实际操作
cli-anything-gimp --json resize test.png 800x600 output.png
```

#### 第四步：在 OpenClaw 中使用

```python
# 在 OpenClaw Agent 中
exec(command='cli-anything-gimp --json resize input.png 800x600 output.png')
# 解析 JSON 输出并处理
```

---

## 💡 最佳实践

### 1. 优先使用 JSON 输出

```bash
# ✅ 推荐（Agent 友好）
cli-anything-<software> --json <command>

# ❌ 不推荐（需要解析文本）
cli-anything-<software> <command>
```

### 2. 错误处理

```python
import json
import subprocess

result = subprocess.run(
    ['cli-anything-gimp', '--json', 'resize', 'input.png', '800x600', 'output.png'],
    capture_output=True,
    text=True
)

if result.returncode == 0:
    data = json.loads(result.stdout)
    if data['status'] == 'success':
        print(f"成功：{data['output']}")
    else:
        print(f"失败：{data['error']}")
else:
    print(f"CLI 调用失败：{result.stderr}")
```

### 3. 批量操作

```bash
# 批量处理图片
for img in *.png; do
  cli-anything-gimp --json resize "$img" 800x600 "resized_$img"
done
```

### 4. 配置默认参数

```json
// ~/.config/cli-anything-gimp/config.json
{
  "default_format": "png",
  "default_quality": 90,
  "working_dir": "~/projects/images"
}
```

---

## 🚀 未来计划

- 支持更多软件类别（CAD、DAW、IDE、EDA、科学工具）
- Agent 任务完成率基准测试
- 社区贡献的 CLI harness
- 支持闭源软件和 Web 服务
- **生成 SKILL.md**（OpenClaw 技能发现）

---

## 📝 总结

**CLI-Anything 的价值**：
- ✅ 让传统软件 Agent 化
- ✅ 自动生成 CLI + 测试 + 文档
- ✅ JSON 输出，Agent 友好
- ✅ 可靠性高（1,508 个测试）

**适用场景**：
- 图像/视频处理自动化
- 文档转换批量操作
- 3D 建模脚本化
- 数据库操作自动化

**OpenClaw 集成**：
- 通过 `sessions_spawn` 调用 Claude Code 生成 CLI
- 通过 `exec` 工具调用生成的 CLI
- 解析 JSON 输出完成任务

---

**项目地址**：https://github.com/HKUDS/CLI-Anything  
**许可证**：MIT License  
**维护者**：HKUDS  
**更新日期**：2026-03-14
