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

FROM nginx:1.23.3-alpine

WORKDIR /web

# 移除nginx容器的default.conf文件、nginx配置文件
RUN rm /etc/nginx/conf.d/default.conf \
    && rm /etc/nginx/nginx.conf \
    && mkdir /etc/nginx/logs \
    && touch /etc/nginx/logs/error.log

# 把主机的nginx.conf文件复制到nginx容器的/etc/nginx文件夹下
COPY ./nginx.conf /etc/nginx/

# 拷贝前端vue项目打包后生成的文件到nginx下运行
COPY ./admin /usr/share/nginx/html/admin

# 暴露端口
EXPOSE 9030

# 使用daemon off的方式将nginx运行在前台保证镜像不至于退出
CMD ["nginx", "-g", "daemon off;"]