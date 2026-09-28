# Enterprise AI · GitHub 学生接入演示

这个项目用于亲自验证：网页绑定自己的 GitHub → push → 自动评测 → 查看报告。
初始代码是零售场景的 TypeScript 只读客户查询模板，包含本地测试和业务材料，**不是完整业务任务的答案**。

## 第一步：在网页填写

打开 <https://agentist.org/lab/enterprise-ai>，进入「提交 Agent」，填写：

| 字段 | 内容 |
|---|---|
| 仓库 | `https://github.com/mrvgao/enterprise-ai-github-demo` |
| 分支 | `main` |
| 场景 | 零售服务 / `retail_plus` |
| 语言 | TypeScript |
| 每次 push 评测 | `t1` 接入检查 |

保存绑定并下载平台生成的接入包。**仅安装包内的 `.github/workflows/hyper-lab.yml`，不要覆盖自己的 `agent/`。** 必须使用网页为本仓库生成的绑定 ID，不要复制其他项目的工作流。

本仓库初次上传时没有评测工作流，因为你还没有完成网页绑定；此时没有 Actions 评测记录是正常的。

## 第二步：首次提交评测

需要 Node.js 24+、Python 3 和 GitHub CLI。项目不依赖额外 npm 包。

```bash
cd /Users/mgao/Documents/ChatGPT/Agentist-Teaching-Materials/enterprise-ai-github-demo
npm test

# 网页绑定后，把下载的文件放到 .github/workflows/hyper-lab.yml
git add .github/workflows/hyper-lab.yml
git commit -m "chore: connect Enterprise AI evaluation"

# 若 GitHub CLI 尚未登录，先运行 gh auth login
source scripts/git-evaluate.sh
git push
```

已存在 git alias/function 时，脚本不会覆盖它；改用 `python3 scripts/git-evaluate.py push` 即可。

开启脚本后，push 会先上传 commit，再等待与其对应的 Actions 运行并输出结果。普通 `git push` 不自动等待评测；每个新终端需要重新 source。这里只等待 GitHub 已触发的任务，不会额外提交第二轮。

## 第三步：核对结果

检查终端、GitHub Actions、平台「运行与反馈」的 Job ID 和结果是否一致。正式的历史入口为「我的学习 → 实验管理 → 我的实验测评记录」。

`t1` 只验证接入，不调用付费模型；通过不代表完整业务场景通过。评测执行器平时休眠，提交后自动唤醒，完成后空闲自动停止；无需每次找教师开临时窗口。仍需有效课程权限，管理员可以暂停服务。

再次触发时，修改 `agent/` 下代码并提交。只改 README 或重复 push 同一 commit 不一定触发工作流。Ctrl-C 只停止本地等待，不取消已提交的远程任务。

## 项目内容

- `agent/`：远程评测只提交这里的候选代码。
- `tests/`：本地公开测试，`npm test` 不提交远程。
- `materials/`：业务材料、当前 OpenAPI 与合成开发数据。
- `AGENT-CONTRACT.md`、`PROJECT.md`：接口约定和扩展方式。
- `scripts/git-evaluate.sh`、`scripts/git-evaluate.py`：终端等待 GitHub 结果。
- `scripts/evaluate.mjs`：可选的本地直接提交入口。

本地直接提交需从权限中心领取自己的「统一评测配置」，保存于仓库外：

```bash
export HYPER_LAB_CONFIG="/绝对路径/Enterprise-AI.json"
npm run evaluate
```

GitHub 自动评测使用平台生成的 OIDC 工作流，不需要把个人 JSON 或平台 token 放到 GitHub Secrets。不要上传 API Key、个人凭证或真实客户数据。

教学模板与材料沿用原项目许可，见 `HYPER-TAU-LICENSE`。本仓库没有沿用旧演示项目的绑定信息或测试站地址。
