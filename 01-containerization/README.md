### Overview
Session 1: Containerization Setup
Thực hành chính:
 - Tạo Dockerfile cho Next.js 15
 - Multi-stage build optimization
   - size và cache (thời gian build image)
   - docker build -t <image_name> --build-arg NODE_ENV=development . (phải có dấu .)
   - docker run -p <PORT_EX>:<PORT_IN> {image_name}
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

- Docker Compose setup:
    - expose port
    - network
    - health-check

```yml
version: '3.3'
services:
  coffee-trace-frontend:
    container_name: cms-thietbi-frontend
    image: <image_name>:<tag>
    logging:
      driver: json-file
      options:
        max-file: '10'
        max-size: 200k
    ports:
      - published: 5005
        target: 3000
    restart: unless-stopped
    # networks:
    #   - cms-thietbi-network
    # environment:
    #   - REACT_APP_API_URL=http://backend-api:8000
    #   - REACT_APP_ENV=production
    healthcheck:
      test: wget -q --spider http://127.0.0.1:3000/api/health || exit 1
      interval: 15s
      timeout: 10s
      retries: 3
      start_period: 15s
# networks:
#   cms-thietbi-network:
#     external: true
#     name: cms-thietbi-network
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