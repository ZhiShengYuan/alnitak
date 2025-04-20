# ─── Stage 1: Build ─────────────────────────────────────────────
FROM node:18-alpine AS builder
WORKDIR /app

# 1) 先复制 package.json & yarn.lock 利用缓存
COPY package.json yarn.lock ./

# 2) 安装 curl 用于后续的 registry 可达性检测
RUN apk update && apk add --no-cache curl

# 3) 检测默认镜像源，可达则用它，否者切换到官方 registry，再安装依赖
RUN set -eux; \
    if curl -s --head --fail --max-time 1 https://registry.npmmirror.com/; then \
      echo "✅ npmmirror.com reachable, using it"; \
    else \
      echo "⚠️  npmmirror.com unreachable, switching to official registry"; \
      yarn config set registry https://registry.npmjs.org; \
    fi; \
    yarn install --frozen-lockfile

# 4) 复制剩余项目文件并打包
COPY . .
RUN yarn build

# ─── Stage 2: Production ────────────────────────────────────────
FROM node:18-alpine
WORKDIR /app

# 只把 builder 里打包好的产物拷贝过来
COPY --from=builder /app/.output /app/.output

# 设置环境变量
ENV NITRO_PORT=9010

EXPOSE 9010

ENV ENV=docker
CMD ["node", "./.output/server/index.mjs"]