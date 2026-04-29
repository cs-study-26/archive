# 🧑🏻‍💻 Docker

---

- [✅ Docker에 대해서](#-docker에-대해서)
- [✅ Docker Container란](#-docker-container란)
- [✅ Docker 이미지란](#-docker-이미지란)
- [✅ Docker Image vs Docker Container](#-docker-image-vs-docker-container)
- [✅ Docker 컨테이너와 가상 머신(VM)의 차이는 무엇인가?](#-docker-컨테이너와-가상-머신vm의-차이는-무엇인가)
- [✅ Docker Container에서 데이터를 영구적으로 저장하기 위한 방법 - Volume](#-docker-container에서-데이터를-영구적으로-저장하기-위한-방법---volume)
- [✅ Dockerfile이란?](#-dockerfile이란)
- [✅ Docker Compose란?](#-docker-compose란)
- [✅ Docker 컨테이너를 확장(scale)하는 방법](#-docker-컨테이너를-확장scale하는-방법)
- [✅ Docker Swarm vs Kubernetes](#-docker-swarm-vs-kubernetes)

## ✅ Docker에 대해서

> [!NOTE]
> Docker는 컨테이너화 기술을 사용하여 애플리케이션을 개발, 배포 및 실행할 수 있게 해주는 오픈 소스 플랫폼이다.  
> 컨테이너화란 애플리케이션과 그 종속성을 컨테이너라는 격리된 환경에 패키징하는 과정을 말한다.  
> 이를 통해 애플리케이션이 실행되는 환경에 구애받지 않고 일관된 동작을 보장할 수 있다.
> 
> 한 번 빌드한 Docker 이미지는 어느 환경에서나 동일하게 실행할 수 있다.  
> 이는 개발에서부터 테스트, 스테이징, 프로덕션 환경에 이르기까지 애플리케이션의 일관성과 호환성 문제를 대폭 줄여준다.

<br>

## ✅ Docker Container란

---

> [!NOTE]
> - 사용자가 기본 시스템에서 애플리케이션을 분리할 수 있는 가상화된 런타임 환경이다.
> - 중요 기능: 컨테이너 내부에서 실행되는 컴퓨팅 환경의 표준화  
>   ➡️ 응용 프로그램이 동일한 환경에서 작동하도록 하고, 다른 사람과의 공유도 단순화한다.
> - 격리(strong isolation): 자율적이어서 서로 방해하지 않는다.


<br>

## ✅ Docker 이미지란

---

> [!TIP]
> 닌텐도에 여러가지 칩을 꽂아서 다양한 게임을 즐길 수 있는데, Docker에서 닌텐도의 칩과 같은 역할을 하는 것이 이미지(Image)다.
> - Node.js 기반의 Express.js 서버 프로젝트를 이미지로 만들었다고 가정해보자.
>   - 이 이미지를 Docker로 실행시키면 Express.js 서버 프로젝트가 컨테이너(Container) 환경에서 실행된다.
> - MySQL 서버를 이미지로 만들었다면, 이 이미지를 Docker로 실행시키는 순간 MySQL 서버가 컨테이너 환경에서 실행된다.
>   - MySQL을 일일이 설치할 필요 없이 MySQL 데이터베이스를 사용할 수 있게 된다.

<br>


> [!NOTE]
> - 파일로 애플리케이션 실행에 필요한 독립적인 환경을 포함하는 런타임 환경을 위한 일종의 템플릿이다.
> - 소스코드, 라이브러리, 종속성, 도구 및 응용 프로그램을 실행하는 데 필요한 기타 파일을 모두 포함하는 불변 파일이다.
> - 스냅샷이라고도 하고, 특정 시점의 애플리케이션과 가상 환경을 나타낸다.
> - 일관성
>   - Docker의 큰 특징 중 하나다.
>   - 개발자가 안정적이고 균일한 조건에서 소프트웨어를 테스트하고 실험할 수 있도록 한다.


<br>


## ✅ Docker Image vs Docker Container

---
> - [!NOTE]
> - Container는 Image가 있어야 존재할 수 있지만, Image는 Container가 없어도 존재할 수 있다.
>   - 즉, Container는 Image에 종속되어 런타임 환경을 구성하고 애플리케이션을 실행하는 데 사용된다.
> - Docker Image는 Docker Container에서 코드를 실행한다.
>   - 실행 중인 Container를 만들려면 Docker Image에 핵심 기능의 쓰기 가능 계층을 추가한다.(Writable Container)
>   - 즉, Image를 바로 실행하는 것이 아니라 Image 레이어에 쓰기 레이어를 합쳐야 Container가 되는 것이다.
> - Docker Container는 실행 중인 Image Container로 간주한다.  
>   ➡️ Image가 설계도/칩이라면, Container는 실제로 동작하는 인스턴스다.
>   - 동일한 Image에서 그것을 기반으로 한 여러 개의 Container를 만들 수 있다.
>   - 다시 한 번 강조하자면, Docker Container의 채택 이유는 개발, 운영 및 테스트의 표준화 및 단순화다.

![docker_container_structure.png](res/docker_container_structure.png)

<br>

## ✅ Docker 컨테이너와 가상 머신(VM)의 차이는 무엇인가?

---

> [!NOTE]
> Docker 컨테이너는 호스트 OS의 커널을 공유하며, 필요한 애플리케이션과 그 종속성만을 포함하여 가볍고 빠르다.  
> 반면, 가상 머신은 전체 게스트 운영체제를 포함하여 더 많은 리소스를 소비하고 무겁다.  
> 컨테이너는 시작 시간이 짧고 리소스 사용이 효율적인 반면, 가상 머신은 더 강력한 격리와 보안을 제공한다.

![docker_container_virtual_machine.png](res/docker_container_virtual_machine.png)

<br>

## ✅ Docker Container에서 데이터를 영구적으로 저장하기 위한 방법 - Volume

---

> [!NOTE]
> Docker에서는 볼륨(Volume)과 바인드 마운트(Bind Mount)를 사용하여 컨테이너 외부에 데이터를 영구적으로 저장할 수 있다.  
> - Volume
>   - Docker가 관리하는 파일 시스템에 저장됨
>   - Host 머신의 저장 위치를 알 수 없음
>   - Docker Area 내부에서 관리됨
>   - Linux와는 달리, Mac/Windows는 Docker가 내부적으로 Linux VM을 띄워서 실행
>   - 운영 환경에 적합
> - Bind Mount
>   - Host 머신의 특정 경로와 직접 연결
>   - 저장 위칠르 정확히 알 수 있다.
>   - Docker Area 외부의 파일 시스템과 연결
>   - 로컬 개발, 소스코드 실시간 반영에 적합

<br>

## ✅ Dockerfile이란?

---

> [!NOTE]
> Dockerfile은 Docker Image를 빌드하기 위한 명세서다.  
> 이 파일 안에는 Image를 생성하는 데 필요한 명령어들이 순서대로 기술되어 있다.  
> `docker build` 명령어를 사용하여 Dockerfile로부터 Image를 생성할 수 있고, 이 과정에서 각 명령어가 순차적으로 실행된다.

<br>

## ✅ Docker Compose란?

---

> [!NOTE]
> Docker Compose는 여러 Docker Container를 정의하고 실행하기 위한 도구다.  
> YAML 파일을 사용하여 애플리케이션의 서비스, 네트워크, 볼륨 등을 구성한다.  
> Docker Compose를 사용하면 복잡한 애플리케이션을 간단한 명령어로 관리할 수 있으며, 개발, 테스트, 프로덕션 환경에서의 일관성을 보장할 수 있다.

<br>

## ✅ Docker 컨테이너를 확장(scale)하는 방법

---

> [!NOTE]
> Docker Container를 확장하기 위해서는 Docker Compose 파일 내에서 scale 명령어를 사용하거나, Docker Swarm 또는 Kubernetes 같은 오케스트레이션 도구를 사용하여 자동화된 방식으로 컨테이너의 인스턴스 수를 조정할 수 있다.  
> 이러한 도구들은 부하에 따라 자동으로 Container를 추가하거나 제거하여 애플리케이션의 확장성과 가용성을 보장한다.

<br>

## ✅ Docker Swarm vs Kubernetes

---

> [!NOTE]
> Docker Swarm과 Kubernetes는 모두 Container 오케스트레이션 도구다.  
> - Docker Swarm
>   - Docker에서 공식적으로 지원하는 도구로, 사용이 간단하고 Docker와의 통합이 잘 되어있다.
>   - 설정과 관리가 단순하다는 장점이 있다.
> - Kubernetes
>   - Kubernetes는 대규모 시스템을 위한 더 복잡한 배포, 스케일링, 관리 기능을 지원한다.
>   - 클러스터 관리, 자동화된 롤아웃과 롤백, 로드 밸런싱 등을 제공한다.
>   - 고급 기능과 더 높은 확장성이 필요한 환경에 적합하다.




<br>

**출처**
- [[면접] 기술 면접 - Docker](https://jaehyuuk.tistory.com/215?category=1170346)
- [비전공자도 이해할 수 있는 Docker 입문/실전](https://www.inflearn.com/course/%EB%B9%84%EC%A0%84%EA%B3%B5%EC%9E%90-docker-%EC%9E%85%EB%AC%B8-%EC%8B%A4%EC%A0%84/dashboard?cid=334085)
- [[Docker] 도커 바인드 마운트란 무엇일까❓: 데이터 저장과 실시간 공유](https://pixx.tistory.com/647)