# ─── Stage 1: 构建 Vue 应用 ───────────────────────────────────
FROM node:18-alpine AS builder
WORKDIR /app

# 1) 复制 package 文件，加速依赖安装缓存
COPY package.json yarn.lock ./

# 2) 安装依赖
RUN yarn install --frozen-lockfile

# 3) 复制源码并打包
COPY . .
RUN yarn build

# ─── Stage 2: Nginx 静态服务器 ────────────────────────────────
FROM nginx:1.23.3-alpine

WORKDIR /web
COPY . .

# 清理默认配置并创建日志目录
RUN rm -f /etc/nginx/conf.d/default.conf \
    && rm -f /etc/nginx/nginx.conf \
    && mkdir -p /etc/nginx/logs \
    && touch /etc/nginx/logs/error.log

# 把主机的 nginx.conf 文件和 admin 静态文件照旧复制
COPY ./nginx.conf /etc/nginx/
COPY ./admin /usr/share/nginx/html/admin

# 简单的健康检查，保证 Nginx 正常响应
HEALTHCHECK --interval=30s --timeout=5s --start-period=5s \
  CMD wget --quiet --tries=1 --spider http://localhost:9030/admin || exit 1

EXPOSE 9030

CMD ["nginx", "-g", "daemon off;"]

