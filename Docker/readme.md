# 목차 (Table of Contents)

1. [📁Docker란 무엇인가?](#docker란-무엇인가)
2. [📁Image vs Container](#image-vs-container)
3. [📁Dockerfile (Build Process)](#dockerfile-build-process)
4. [📁Docker Compose (Orchestration)](#docker-compose-orchestration)
5. [📁데이터 영속성 (Volumes)](#데이터-영속성-volumes)
6. [📁VM vs Docker (Container)](#vm-vs-docker-container)
---

## 📁Docker란 무엇인가?

> 애플리케이션을 **컨테이너**라는 격리된 환경에서 실행하기 위한 플랫폼.
> **"It works on my machine"** (내 컴퓨터에선 되는데?) 문제를 해결하는 것이 핵심 목적.

* **핵심 가치**:
    * **환경 격리**: 라이브러리, 의존성 충돌 방지.
    * **이식성**: 개발, 테스트, 운영 환경을 `Image` 단위로 통일하여 어디서든 동일하게 실행 보장.

---

## 📁Image vs Container

> **핵심 기본 개념** (OOP의 Class와 Object 관계와 유사)

1. **Docker Image (Class)**:
    * 애플리케이션 실행에 필요한 **모든 것(코드, 런타임, 라이브러리, 환경 설정 등)을 포함한 불변(Immutable) 파일**.
    * **Layer** 구조로 되어 있어, 변경된 부분만 다운로드하거나 빌드할 수 있어 효율적이다.
    
2. **Docker Container (Object)**:
    * 이미지를 실행하여 메모리에 올라간 **프로세스**.
    * 이미지에 **쓰기 가능한 레이어(Writable Layer)**가 추가된 상태.
    * 컨테이너가 삭제되면 저장된 데이터도 함께 사라짐 (따라서 Volume 사용 필수).

---

## 📁Dockerfile (Build Process)

> Docker Image를 만들기 위한 **설명서(Recipe)**

* **주요 명령어**:
    * `FROM`: 베이스 이미지 지정 (OS 또는 런타임).
    * `COPY` / `ADD`: 소스 코드를 컨테이너 안으로 복사.
    * `RUN`: 명령어를 실행하여 의존성 설치 (이미지 빌드 시 실행).
    * `CMD` / `ENTRYPOINT`: 컨테이너가 시작될 때 실행할 명령어 (실행 시점).

* **Layer Caching (중요 포인트)**:
    * Dockerfile의 각 라인은 하나의 레이어가 된다.
    * 빌드 시 **변경된 내용이 없는 레이어는 캐시를 재사용**한다.
    * **Tip**: 자주 변경되는 파일(소스코드) `COPY`는 최대한 아래쪽에 배치하고, 잘 안 바뀌는 `RUN pip install` 등을 위쪽에 배치해야 빌드 속도가 빠르다.

---

## 📁Docker Compose (Orchestration)

> 여러 개의 컨테이너(Multi-Container)를 **하나의 YAML 파일**로 정의하고 실행하는 도구.

* **왜 쓰는가?**:
    * 웹 서버(Django), DB(PostgreSQL), 캐시(Redis), Nginx 등을 각각 `docker run`으로 띄우면 관리가 헬(Hell)이다.
    * `docker-compose.yml` 파일 하나로 의존성 순서, 네트워크, 볼륨 등을 한 번에 관리 (`up`, `down`) 할 수 있다.

---

## 📁데이터 영속성 (Volumes)

> 컨테이너는 삭제되면 내부 데이터가 다 날아간다. **DB 데이터나 로그 파일**은 따로 저장해야 한다.

1. **Docker Volume**:
    * Docker가 관리하는 호스트 파일시스템의 특정 영역에 저장. (`/var/lib/docker/volumes/...`)
    * **가장 권장되는 방식**. 백업 및 마이그레이션이 쉽다.

2. **Bind Mount**:
    * 호스트의 특정 경로(예: 내 바탕화면 폴더)를 컨테이너 경로와 직접 연결.
    * **개발 환경**에서 소스 코드를 실시간으로 수정하고 반영할 때 주로 사용.

---

## 📁VM vs Docker (Container)

| 비교 항목 | Virtual Machine (VM) | Docker Container |
| --- | --- | --- |
| **격리 수준** | **하드웨어 레벨** 가상화 (Guest OS 필요) | **OS 레벨** 가상화 (Host OS 커널 공유) |
| **무게/성능** | 무거움 (수 GB), 부팅 느림 (수 분) | **매우 가벼움** (수 MB), 실행 빠름 (ms 단위) |
| **보안** | 완벽한 격리로 보안성 높음 | 커널을 공유하므로 VM보다는 보안 취약 가능성 있음 |
| **확장성** | 느림 | **매우 빠름** (Scale-out 용이) |

> **결론**: **MSA(Microservices Architecture)** 및 **CI/CD** 환경에서는 빠르고 가벼운 Docker가 표준이다.
