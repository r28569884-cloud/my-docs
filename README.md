# 实验二：GitHub Actions 自动部署 MkDocs 到 GitHub Pages

本项目对应课程实验二。源码保存在 `main` 分支，GitHub Actions 将 MkDocs 构建出的 `site/` 内容提交到 `gh-pages` 分支，GitHub Pages 从该分支发布网站。

计划仓库：<https://github.com/r28569884-cloud/my-docs>

计划网站：<https://r28569884-cloud.github.io/my-docs/>

这两个地址仅表示预期发布位置；本地文件准备完成并不代表远程仓库或网站已经存在。

## 文件用途

- `docs/index.md`：网站首页，包含其余三个页面的链接。
- `docs/about.md`：关于页面。
- `docs/guide.md`：使用指南。
- `docs/api.md`：API 参考页面。
- `mkdocs.yml`：网站名称、主题、导航及网站地址。
- `.github/workflows/deploy.yml`：自动构建和部署工作流。
- `.gitignore`：避免将本地构建产物、虚拟环境和缓存提交到源码分支。

## 本地检查

在项目根目录的 Git Bash 中执行：

```bash
python -m pip install mkdocs-material
python -m mkdocs build
```

需要预览时执行 `python -m mkdocs serve`，打开输出的网址；按 Ctrl+C 停止预览。

课程工作流指定 Python 3.9，因此云端保留该版本；本地检查可使用已经安装的 Python 3.12.7。依赖安装可能需要网络访问。

## 创建公开仓库并推送

在 GitHub 创建公开空仓库 `my-docs`，不要在网页初始化 README、.gitignore 或 License。

在项目根目录的 Git Bash 执行：

```bash
git init
git branch -M main
git config user.name "Ray"
git config user.email "r28569884@gmail.com"
git add .
git commit -m "Add MkDocs documentation and Pages deployment"
git remote add origin git@github.com:r28569884-cloud/my-docs.git
git remote -v
git push -u origin main
```

电脑已有实验一配置的 SSH 密钥时可继续使用，无须重新生成。上述命令适用于尚未初始化、尚未设置 origin 的新目录；如果已操作过，应先用 `git status` 和 `git remote -v` 核对状态。

## 配置和验证 Pages

1. 仓库 Settings → Actions → General → Workflow permissions，按课程要求选择 Read and write permissions 并保存。工作流也已写明 `permissions: contents: write`。课程要求勾选 Allow GitHub Actions to create and approve pull requests；该部署本身不创建 PR。
2. Actions 中等待 Deploy MkDocs to GitHub Pages 成功。部署动作会在 `gh-pages` 不存在时自动创建它，无须从源码分支手动复制一个同名分支。
3. Settings → Pages → Build and deployment：Source 选择 Deploy from a branch；Branch 选择 gh-pages；目录选择 /(root)；保存。
4. 等待 Pages 发布完成，访问计划网站并检查首页、关于、指南及 API 链接。
5. 后续更改 docs/、mkdocs.yml 或 deploy.yml 后推送到 main，会重新触发部署。仅修改 README.md 不会满足 push 的路径过滤条件；可以在 Actions 中用 Run workflow 手动运行。

注意：本实验使用将构建结果写入 gh-pages 的部署方式，因此 Pages 发布源不要选 GitHub Actions，也不要选 main/docs。

## 头歌评测环境

点击头歌的“实验环境”→“启动环境”，等待出现终端提示符，在这个远程终端执行课程要求的命令：

```bash
pip install --upgrade requests urllib3 chardet
pip install pyyaml
```

上述命令用于头歌的评测脚本。若提示 pip 不存在，先确认 `python3 -m pip --version`；如果可用，用 `python3 -m pip` 替代 `pip`。若评测仍报依赖错误，保留完整报错以确认评测实际使用的解释器，不要反复安装不同版本。

`PyYAML` 是安装包名称，而 Python 导入名称是 `yaml`。实验一曾出现的 RequestsDependencyWarning 属于评测环境；本关明确提供了修复依赖的终端步骤。

## 头歌输入与提交

保留代码文件中的注释及三个 `#Begin/#END` 区域，分别填写 GitHub 用户名、新的短期 PAT 和仓库链接：

```text
r28569884-cloud
```

PAT 仅填入课程指定的令牌字段，不要写进 GitHub 仓库、工作流、README 或提交截图。需要 Classic PAT 时可按课程使用短有效期，勾选 repo、workflow；若平台明确提供评测专用环境变量，应遵循平台要求。

```text
https://github.com/r28569884-cloud/my-docs.git
```

确认网站可访问、最新部署工作流成功、头歌依赖安装完成后点击“评测”。反馈失败信息时遮住 PAT；实验结束后撤销临时 PAT。

## 排错

- `Permission denied (publickey)`：电脑的 SSH 认证未成功。先运行 `ssh -T git@github.com` 核对。
- `remote origin already exists`：先运行 `git remote -v`，确认地址；需要修正时使用 `git remote set-url origin ...`。
- 工作流 403 / write access not granted：检查工作流的 contents: write 及仓库 Actions 权限。
- Actions 不触发：确认推送到 main，并且修改的文件满足 paths 过滤；也可以手动 Run workflow。
- Pages 404：检查 Pages 是否选 gh-pages 和 /(root)，部署产物根目录是否有 index.html，再等待发布完成。
- `No module named yaml`：在头歌评测所用 Python 环境安装 PyYAML。
- 两边输出不匹配：先查看实际输出和原始输出，确认业务检查、错误栈及环境警告；不要将警告文字硬编码成输出。
