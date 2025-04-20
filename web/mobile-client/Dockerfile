# ─── Stage 1: Build Vue App ───────────────────────────────────
FROM node:18-alpine AS builder
WORKDIR /app

# 1) 先复制 package 文件，加速依赖安装缓存
COPY package.json yarn.lock ./

# 2) 安装依赖
RUN yarn install --frozen-lockfile

# 3) 复制源码并打包
COPY . .
RUN yarn build

# ─── Stage 2: Serve with Nginx ────────────────────────────────
FROM nginx:1.23.3-alpine
WORKDIR /app

# 1) 移除默认配置，准备自定义
RUN rm /etc/nginx/conf.d/default.conf \
    && rm /etc/nginx/nginx.conf \
    && mkdir -p /etc/nginx/logs \
    && touch /etc/nginx/logs/error.log

# 2) 拷贝你的自定义 nginx.conf
COPY nginx.conf /etc/nginx/

# 3) 将 Vue 打包输出（默认为 /app/dist）复制到 Nginx 静态目录
COPY --from=builder /app/dist /usr/share/nginx/html/mobile

EXPOSE 9020

# 4) 前台启动 Nginx
CMD ["nginx", "-g", "daemon off;"]
