### Overview
Session 1: Containerization Setup
Thực hành chính:
 - Tạo Dockerfile cho Next.js 15
 - Multi-stage build optimization
   - size và cache (thời gian build image)
   - docker build -t <image_name> --build-arg NODE_ENV=development . (phải có dấu .)
   - docker run -p <PORT_EX>:<PORT_IN> {image_name}
 - Docker Compose setup:
    - expose port
    - network
    - health-check
  ```yml
# Chọn hình ảnh Node.js chính thức từ Docker Hub (v20)
FROM node:22-alpine AS build

# Tạo thư mục làm việc
WORKDIR /app

# Sao chép package.json và yarn.lock
COPY package.json yarn.lock ./

# Cài đặt các phụ thuộc của ứng dụng
RUN yarn --frozen-lockfile

# Thiết lập biến môi trường
ARG NEXT_ENVIRONMENT
ENV NODE_ENV $NEXT_ENVIRONMENT
ENV NEXT_PUBLIC_ENV $NEXT_ENVIRONMENT

# Sao chép toàn bộ mã nguồn và file .env phù hợp
COPY . .
COPY .env.${NEXT_ENVIRONMENT} .env.production

# Xây dựng ứng dụng Next.js
RUN yarn build

# Runtime stage với Node.js v20
FROM node:22-alpine AS runtime

# Tạo thư mục làm việc
WORKDIR /app

ENV HOST 0.0.0.0

ARG NEXT_ENVIRONMENT
ENV NODE_ENV $NEXT_ENVIRONMENT

# Sao chép các tập tin đã xây dựng từ giai đoạn build
COPY --from=build /app/.next/standalone ./
COPY --from=build /app/.next/static ./.next/static

# Mở cổng mà ứng dụng sẽ chạy
EXPOSE 3000
ENV PORT 3000

# Chạy ứng dụng
CMD ["node", "server.js"]
  ```
  - Tối ưu image size:
    - bổ sung thêm .dockerignore
    - thay đổi nextconfig sang `output: "standalone"`
    - sử dung: `FROM node:22-alpine`
    - Dockerfile sau khi sửa:
  
```yaml
  # Chọn hình ảnh Node.js chính thức từ Docker Hub (v20)
FROM node:22-alpine AS build

# Tạo thư mục làm việc
WORKDIR /app

# Sao chép package.json và yarn.lock
COPY package.json yarn.lock ./

# Cài đặt các phụ thuộc của ứng dụng
RUN yarn --frozen-lockfile

# Thiết lập biến môi trường
ARG NEXT_ENVIRONMENT
ENV NODE_ENV $NEXT_ENVIRONMENT
ENV NEXT_PUBLIC_ENV $NEXT_ENVIRONMENT

# Sao chép toàn bộ mã nguồn và file .env phù hợp
COPY . .
COPY .env.${NEXT_ENVIRONMENT} .env.production

# Xây dựng ứng dụng Next.js
RUN yarn build

# Runtime stage với Node.js v20
FROM node:22-alpine AS runtime

# Tạo thư mục làm việc
WORKDIR /app

# Sao chép các tập tin đã xây dựng từ giai đoạn build
COPY --from=build /app/.next/standalone ./
COPY --from=build /app/.next/static ./.next/static

# Mở cổng mà ứng dụng sẽ chạy
EXPOSE 3000
ENV PORT 3000

# Chạy ứng dụng
CMD ["node", "server.js"]
```

 - Local development với Docker
 - Environment variables handling
 - Development environment setup
 - Staging environment deployment
 - Production deployment strategy
 - Environment-specific Docker configs
 - Secret management


Session 2: GitHub Actions Foundation
Thực hành chính:

- Tạo workflow cơ bản (.github/workflows)
- Build automation
- Testing integration
- Docker image build & push
- Environment-specific configs
- Rollback strategies
- Health checks
- Basic monitoring setup