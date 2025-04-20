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
WORKDIR /app

# 1) 移除默认配置，准备自定义
RUN rm /etc/nginx/conf.d/default.conf \
    && rm /etc/nginx/nginx.conf \
    && mkdir -p /etc/nginx/logs \
    && touch /etc/nginx/logs/error.log

# 2) 拷贝你自己写好的 nginx.conf
COPY nginx.conf /etc/nginx/

# 3) 将打包产物（默认 dist 目录）复制到 Nginx 下运行
COPY --from=builder /app/dist /usr/share/nginx/html/admin

# 4) 暴露 9030 端口
EXPOSE 9030

# 5) 前台启动 Nginx
CMD ["nginx", "-g", "daemon off;"]
