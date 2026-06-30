# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 常用命令

| 命令                 | 说明                                                             |
| -------------------- | ---------------------------------------------------------------- |
| `npm run dev`        | 启动开发服务器                                                   |
| `npm run build`      | 生产构建                                                         |
| `npm run start`      | 运行生产构建产物 (`react-router-serve`)                          |
| `npm run typecheck`  | 生成类型定义 + TypeScript 类型检查                               |
| `npm run lint`       | 全量 ESLint 检查                                                 |
| `npm run lint:app`   | 仅检查 `./app` 目录                                              |
| `npm run format`     | 全量 Prettier 格式化                                             |
| `npm run format:app` | 仅格式化 `./app` 目录                                            |
| `npm run docker`     | 使用 `.env.development` 启动 Docker Compose (PostgreSQL + Redis) |
| `npm run commit`     | 交互式提交 (czg)                                                 |

## 技术栈

- **框架**: React Router v8 (SSR 模式，`react-router.config.ts` 中 `ssr: true`)
- **构建**: Vite 8 + `@react-router/dev`
- **语言**: TypeScript (strict 模式，target ES2022)
- **样式**: Tailwind CSS v4 + `next-themes`（亮/暗主题切换），设计 token 使用 oklch 颜色空间
- **代码质量**: ESLint 10 + Prettier 3 + Husky (pre-commit 自动格式化、commit-msg 校验)
- **提交规范**: Conventional Commits (通过 commitlint + czg)
- **基础设施**: PostgreSQL 16 + Redis 7 (Docker Compose)，含初始化脚本 (`docker/postgres.sh`, `docker/redis.sh`)

## 项目架构

```
app/
├── routes.ts          # 路由配置（React Router v8 flat routes）
├── root.tsx           # 根布局：ThemeProvider + ErrorBoundary + Meta/Links/Scripts
├── app.css            # Tailwind 入口 + CSS 变量设计 token (亮/暗主题)
├── routes/            # 路由模块（每个文件导出 meta、default 组件等）
│   └── home.tsx       # 首页 (`/`)
└── libs/
    └── utils.ts       # `cn()` 工具函数 (clsx + tailwind-merge)
```

- **路径别名**: `~/` 映射到 `./app/`（在 `tsconfig.json` 和 `vite.config.ts` 中配置）
- **类型生成**: `.react-router/types/+routes.ts` 由 `react-router typegen` 自动生成，提供路由参数和模块的类型安全
- **路由模块类型**: 使用 `./+types/home`、`./+types/root` 等（`Route.*` 类型由 React Router 自动生成）

## 关键约定

- `printWidth: 100`，无分号（`semi: false`），双引号（`singleQuote: false`），ES5 尾逗号
- 提交信息遵循 Conventional Commits，type 枚举见 `commitlint.config.cjs`
- npm registry 使用 npmmirror 镜像源（`.npmrc`）
- 环境变量：`.env`（服务端专用，JWT 配置）、`.env.development` / `.env.production`（PostgreSQL/Redis 连接信息）
