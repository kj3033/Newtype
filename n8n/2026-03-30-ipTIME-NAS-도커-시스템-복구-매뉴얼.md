### Context
NAS의 펌웨어 업데이트나 비정상적인 재부팅 이후, ipTIME 환경에서 발생하는 도커(Docker)의 내부 파일 시스템 손상 및 네트워크 오류를 우회하여 안정적으로 서비스를 복구하고 재설치하기 위한 기술적 대응 매뉴얼이다.

### Core
도커 데몬이 오작동하거나 이미지 빌드/실행 시 발생하는 주요 오류에 대한 복구 로직이다.

* **필수 디렉토리 재생성 및 권한 설정**
```bash
# Docker의 필수 내부 구조 복구
sudo mkdir -p /mnt/HDD1/docker/docker/tmp
sudo mkdir -p /mnt/HDD1/docker/docker/containers
sudo mkdir -p /mnt/HDD1/docker/docker/overlay2/l
sudo mkdir -p /mnt/HDD1/docker/docker/image/overlay2/imagedb/content/sha256
sudo mkdir -p /mnt/HDD1/docker/docker/network/files

# 권한 설정 (Permissive)
sudo chmod 777 /mnt/HDD1/docker/docker/tmp /mnt/HDD1/docker/docker/containers /mnt/HDD1/docker/docker/overlay2/l /mnt/HDD1/docker/docker/image/overlay2/imagedb/content/sha256 /mnt/HDD1/docker/docker/network/files
```

* **네트워크 오류 우회 및 데이터베이스 초기화**
```bash
# 빌드 시 호스트 네트워크 강제 사용
sudo docker build --network host --no-cache -t 봇이름 .

# 도커 네트워크 DB 초기화 (상단 필수 디렉토리 생성 후 수행)
sudo systemctl stop docker
sudo rm -f /mnt/HDD1/docker/docker/network/files/local-kv.db
sudo systemctl start docker
```

* **좀비 이미지 클렌징**
```bash
# 불필요한 이미지 및 볼륨 제거
sudo docker system prune -a --volumes -f
```

### Insight
ipTIME NAS 환경에서 발생하는 도커 관련 오류는 주로 스토리지의 마운트 해제 시점과 도커 데몬의 `overlay2` 스토리지 드라이버가 파일 시스템 무결성을 유지하지 못할 때 발생한다. 특히 `invalid tar header` 에러는 네트워크 연결 불안정으로 인한 이미지 풀(Pull) 도중 데이터 오염이 원인이다. 이를 방지하기 위해서는 직접적인 `docker pull`을 지양하고, 사전 정의된 쉘 스크립트를 통한 이미지 수동 로드나 `network host` 옵션을 활용한 외부 통신 간섭 차단이 효과적이다. 모든 복구 작업은 파일 시스템의 좀비 마운트(Zombie Mount)를 피하기 위해 재부팅 직후 수행하는 것을 권장한다.

**출처:** 
* [Docker Storage Drivers - overlay2](https://docs.docker.com/storage/storagedriver/overlayfs-driver/)
* [Docker Prune Command Reference](https://docs.docker.com/reference/cli/docker/system/prune/)
* [Docker Networking Deep Dive](https://docs.docker.com/network/)