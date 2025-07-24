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