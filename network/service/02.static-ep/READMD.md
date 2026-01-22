# Static Endpoint Service

외부에 존재하는 데이터베이스 및 스토리지 서비스들을 Kubernetes 클러스터 내부에서 Service로 접근할 수 있도록 Static Endpoint를 구성합니다.

## 구성 요소

### 1. Service (`01-svc-db-server-proxy.yaml`)
외부 서비스들에 대한 ClusterIP Service 정의
- **Name**: `db-server`
- **Namespace**: `yewon`

### 2. EndpointSlice (`02-eps-db-server-proxy.yaml`)
외부 IP 주소와 포트 매핑 (Kubernetes v1.21+ 권장)
- 외부 IP: `10.144.145.51`

### 3. Endpoints (`03-ep-db-server-proxy.yaml`)
레거시 Endpoints 리소스 (하위 호환성)

## 포트 매핑

| 서비스 | 포트 | 설명 |
|--------|------|------|
| **Qdrant** | 6333 | gRPC API |
| **Qdrant** | 6334 | HTTP API |
| **MinIO** | 9000 | S3 API |
| **MinIO** | 9001 | Console |
| **Redis** | 6379 | Redis Protocol |
| **PostgreSQL** | 5432 | PostgreSQL Protocol |

## 사용 방법

### 배포
```bash
kubectl apply -f 01-svc-db-server-proxy.yaml
kubectl apply -f 02-eps-db-server-proxy.yaml
kubectl apply -f 03-ep-db-server-proxy.yaml
```

### 클러스터 내부에서 접근
```bash
# Qdrant
db-server.yewon.svc.cluster.local:6333

# MinIO
db-server.yewon.svc.cluster.local:9000

# Redis
db-server.yewon.svc.cluster.local:6379

# PostgreSQL
db-server.yewon.svc.cluster.local:5432
```

## 주의사항
- 외부 IP 주소(`10.144.145.51`)가 변경되면 EndpointSlice와 Endpoints를 모두 업데이트해야 합니다
- 외부 서비스의 가용성은 Kubernetes가 관리하지 않으므로 별도 모니터링이 필요합니다
