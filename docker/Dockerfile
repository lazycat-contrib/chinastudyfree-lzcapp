# 聪聪学堂 ChinaStudyFree LPK 构建镜像。
# 上游 docker/Dockerfile 原样适配（多阶段构建：node:20-slim 构建 Next.js 静态导出 + Express AI 后端）。
# 构建上下文 = ./upstream（build.sh 拉取的固定 commit 源码），COPY 均相对 upstream/。
# 上游 Dockerfile 的 COPY 路径不带前缀，此处统一加 upstream/ 前缀，其余逻辑不变。

FROM node:20-slim AS build
WORKDIR /app

# 安装全部 workspace 依赖（含 web / server / core；忽略 tauri/capacitor 的 postinstall 下载，容器内用不到）
COPY upstream/ .
RUN npm install --ignore-scripts

# 生成数据并构建前端静态导出 + AI 后端
RUN npm run build:data || true
RUN npm run sync:textbooks || true
RUN npm run build:web
RUN npm run build:server

# ---- 运行时 ----
FROM node:20-slim AS runtime
WORKDIR /app
ENV NODE_ENV=production
ENV PORT=8787
ENV STATIC_DIR=/app/apps/web/out
ENV HOST=0.0.0.0

# 仅复制运行所需产物。
# server/dist/index.js 是 esbuild 单文件产物（express/dotenv 已内联），
# 因此运行时**不需要 node_modules**，也无需再跑 npm install。
COPY --from=build /app/apps/server/dist ./apps/server/dist
COPY --from=build /app/apps/web/out ./apps/web/out
COPY --from=build /app/apps/web/public/data ./apps/web/public/data

EXPOSE 8787
HEALTHCHECK --interval=30s --timeout=5s --start-period=20s --retries=3 \
  CMD node -e "fetch('http://localhost:'+(process.env.PORT||8787)+'/api/health').then(r=>process.exit(r.ok?0:1)).catch(()=>process.exit(1))"

CMD ["node", "apps/server/dist/index.js"]
