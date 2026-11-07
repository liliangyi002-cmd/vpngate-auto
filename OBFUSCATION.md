# 混淆发布说明

本目录是从 `/workspace/deploy/gate` 构建的独立发布目录，不包含原始 `.git` 历史、测试源码或 `vpngate.py` 明文源码。运行入口是 `bin/gate-checker`，它是由 Nuitka 编译的真实 Linux ELF 二进制，不是 base64、压缩包或字符串编码伪装。

## 固定构建工具

- Nuitka `2.8.8`
- 构建解释器：Python `3.12.3`
- 构建平台：Ubuntu 24.04 x86_64，GCC 13，glibc 动态链接
- 构建模式：`--onefile --follow-imports`，并嵌入 `web/index.html`
- PyArmor `9.1.9` 已实际尝试，但试用许可证对本项目大脚本返回 `out of license`，因此没有把失败的 PyArmor 运行时或假产物放入发布目录。

## 运行与兼容性

`bin/gate-checker` 可在兼容的 Linux x86_64、glibc 环境运行，不需要 Python 或 requests。它不是跨平台/跨架构产物：不能据此保证 macOS、Windows、ARM、musl/Alpine 或不同 ABI 环境可运行。二进制由 Python 3.12 构建，Nuitka 会把 Python 运行时编入文件，因此通常不要求目标机安装同一 Python 版本，但这不是跨环境保证。

GitHub Actions 工作流固定在 `ubuntu-latest`，直接执行该二进制；工作流没有在线构建或上传行为之外的额外密钥。若 GitHub runner 的架构/系统 ABI 变化导致二进制不可执行，需要在相同 runner 上重新构建并替换 `bin/gate-checker`。本目录没有保留重新编译所需的明文源码；重新构建必须在独立、未发布的源工作目录执行。

运行时仍需通过 Actions Secrets 提供 `DOMAIN` 和 `CHECK_TOKEN`。本目录和二进制均未嵌入任何密钥。
