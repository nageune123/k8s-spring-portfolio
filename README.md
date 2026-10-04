# Kubernetes Spring Boot PortfolioO

Spring Boot 애플리케이션과 MySQL을 로컬 Kubernetes(kind) 환경에 배포하고,
Kubernetes의 배포, 서비스 디스커버리, 설정 관리, 데이터 영속성 및 장애 복구를 실습한 프로젝트입니다.

## Architecture

Local Client
    |
    | port-forward
    v
spring-service (ClusterIP :8080)
    |
    v
Spring Boot Deployment
    |
    +-- Pod x2
    +-- ConfigMap
    +-- Secret
    +-- Readiness Probe
    +-- Liveness Probe
    +-- Resource Requests / Limits
    |
    v
mysql-service (ClusterIP :3306)
    |
    v
MySQL Deployment
    |
    +-- MySQL Pod
    +-- Secret
    |
    v
PVC (1Gi)
    |
    v
PV

## Kubernetes Resources

- Deployment
  - Spring Boot replicas: 2
  - MySQL replicas: 1
- Service
  - spring-service
  - mysql-service
- ConfigMap
  - DB_URL
  - DB_USERNAME
- Secret
  - DB_PASSWORD
  - MYSQL_ROOT_PASSWORD
- PersistentVolumeClaim
  - MySQL 데이터 영속성
- Readiness / Liveness Probe
- CPU / Memory Requests & Limits

## What I Tested

### Deployment Self-Healing
Spring Boot Pod와 MySQL Pod를 직접 삭제하여
Deployment가 새로운 Pod를 자동 생성하는 것을 확인했습니다.

### Service & EndpointSlice
Service의 Label Selector와 EndpointSlice를 확인하여
Service가 정상적인 Pod를 대상으로 트래픽을 전달하는 구조를 확인했습니다.

### Readiness Probe
잘못된 Health Check 경로를 설정하여
Running 상태의 Pod라도 Ready 상태가 아니면 Service 트래픽 대상에서 제외되는 것을 확인했습니다.

### Liveness Probe
잘못된 Health Check 경로를 설정하여
Liveness Probe 실패 시 컨테이너가 자동으로 재시작되는 것을 확인했습니다.

### Persistent Storage
MySQL에 테스트 데이터를 저장한 후 MySQL Pod를 삭제하고 재생성했습니다.
새로운 Pod에서도 기존 데이터가 유지되는 것을 확인하여 PVC/PV의 데이터 영속성을 검증했습니다.

### ConfigMap & Secret
DB 접속 주소와 사용자명은 ConfigMap으로,
비밀번호는 Kubernetes Secret으로 분리하여 관리했습니다.

## Environment

- Kubernetes
- kind
- kubectl
- Docker
- Spring Boot
- MySQL 8
- Linux / WSL

## Security

실제 비밀번호가 포함된 Secret YAML 파일은 Git에서 제외했습니다.

GitHub에는 실제 Secret 대신 `mysql-secret.example.yaml`만 포함합니다.
