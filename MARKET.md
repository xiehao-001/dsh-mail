# 邮件助手（@eduwork/dsh-mail）

> 标准 IMAP/SMTP 邮件助手，读取与发送分离，发送与删除等动作走审批门控。
>
> Standards-based IMAP/SMTP assistant with explicit read/send separation and approval-gated delivery.

## 功能

支持多账号、文件夹浏览、搜索、读取正文与附件、草拟与发送；发送、回复、删除等副作用动作需要用户明确批准。凭据存放于 DSH 凭据服务（可搭配原生凭据插件走系统保险库）。

## 安装

```bash
# 在 DSH 中把本目录作为本地 bundle 安装（绝对路径）
```

安装后由 DSH 插件系统加载，条目 id 为 `dsh-mail`。

## 依赖

- `@deepseek-ai/cordis`
- `@deepseek-ai/dsh-api-remotes`
- `@deepseek-ai/dsh-client-ui-renderer`
- `@deepseek-ai/dsh-client-ui-settings`
- `@deepseek-ai/dsh-credentials`
- `@deepseek-ai/dsh-permission-presets`
- `@deepseek-ai/dsh-tools`
- `react`
- `@deepseek-ai/dsh-client-locale`

## 兼容性

- `engines.dsh`: `>=0.2.1 <0.3.0`
- 与 EduWork 发行版同源，来源于 ECNU/EduWork 仓库。

## 注意

源码包未包含构建产物 lib/，发布前需在包目录执行 pnpm install && pnpm build（详见 BUILD.md）。

## 许可

MulanPSL-2.0 AND MIT（与上游 EduWork 一致）。
