---
created: 2025-04-11
---
#docker #resouce-constraint

> https://docs.docker.com/engine/containers/resource_constraints
> https://www.baeldung.com/ops/docker-memory-limit

## **컨테이너 리소스 제약 기능**

기본적으로 컨테이너는 자원 제약이 없으며 호스트의 커널 스케줄러가 허용하는만큼 주어진 리소스를 사용할 수 있다. 

하지만 도커는 컨테이너가 쓸수 있는 CPU, memory 자원을 컨트롤하는 런타임 설정을 `docker run` 커멘드에서 제공한다. 

이 제약기능은 커널이 Linux capabilities를 지원해야 사용할 수 있다. 커널이 해당 기능을 지원하는지 확인하려면 `docker info`명령어를 사용할 수 있다. 만약 커널에서 어떤 capabilitiy가 비활성화 되있다면 다음과 같은 경고가 발생한다.
```console
WARNING: No swap limit support
```
---

## **예시**

```bash
docker run -d \
  --memory=512m \
  --cpus=1 \
  your-image
```

또는 `docker-compose.yml`에서:

```yaml
deploy:
  resources:
    limits:
      memory: 512M
      cpus: '1.0'
```

---

## **존재 이유(자원 제한 기능이 어떻게 쓰이는지)**
> ChatGPA Answer

Resource constraints 기능이 존재하는 이유는 **컨테이너가 시스템 자원을 과도하게 사용하는 걸 방지하고, 예측 가능한 성능을 유지**하기 위해서.

### **1. 자원 고갈 방지**

컨테이너는 기본적으로 **호스트 자원을 제한 없이 쓸 수 있음**.  
즉, 하나의 컨테이너가 CPU나 메모리를 너무 많이 쓰면 **다른 컨테이너나 호스트 전체에 영향을 줄 수 있다**.

> 예시: 메모리 제한 없이 돌리다가 Java 앱 OOM 터지면, 전체 시스템이 다운될 수도 있음 😱


### **2. 서비스 간 자원 격리 (Multi-tenancy)**

하나의 서버에서 여러 서비스(혹은 팀)가 컨테이너로 나눠서 쓰는 경우,

- 서비스 A는 CPU 1개
    
- 서비스 B는 CPU 0.5개만
    
- 서비스 C는 최대 메모리 512MB만


이런 식으로 제한을 걸어줘야 **공정하게 자원을 나누고 독립성**을 유지.

### **3. 성능 테스트 / 용량 계획 시뮬레이션**

테스트 환경에서 자원을 일부러 제한해서,

- "이 서비스는 최소 CPU 0.5개, 메모리 256MB만 있어도 잘 도는지?"
    
- "트래픽이 몰릴 때 어떤 서비스가 병목이 되는지?"
    

를 확인하고 **현실적인 배포 계획을 세울 수있다.**


### **4. Kubernetes 등 오케스트레이션 환경 연동**

Kubernetes에서도 Pod에 `resources.requests`와 `resources.limits`를 명시하게 되어 있다.  
이 값들을 기반으로 **스케줄링, 오토스케일링, QoS 보장, 우선순위 설정**까지 가능함.

```yaml
resources:
  requests:
    memory: "256Mi"
    cpu: "250m"
  limits:
    memory: "512Mi"
    cpu: "500m"
```

이렇게 제한을 설정하지 않으면, 클러스터에 자원 낭비나 비정상 동작이 생길 수 있음.

### **5. 안정성 + 보안**

리소스 제한을 걸면,

- 특정 컨테이너가 **의도치 않게 폭주하는 상황**에서도 다른 서비스는 안전하게 보호
    
- **DoS 공격 시** 시스템 전체가 뻗는 걸 막을 수 있음
    
- 메모리 제한(OOM) 시 해당 컨테이너만 죽고 로그도 확인 가능
    



**Resource constraints는 분산 환경에서 안정성, 성능 예측, 자원 분배를 위한 핵심적인 제어 수단**

---
## **Memory**
### **리눅스에서 Docker 컨테이너가 시스템 메모리를 과도하게 사용하는 것을 방지해야 하는 이유**
- 리눅스 호스트에서는 중요한 시스템 기능을 수행할 메모리가 부족하면 커널이 OOME(Out Of Memory Exception) 을 발생시키고, 메모리를 확보하기 위해 프로세스를 강제 종료한다.
- 이때 Docker나 다른 중요한 시스템 프로세스도 종료 대상이 될 수 있어, 시스템 전체가 불안정해지거나 다운될 수 있음.
### **Docker가 OOME 리스크를 완화시키는 기본 매커니즘(컨테이너보다 데몬)**
- Docker는 **Docker 데몬의 OOM 우선순위**를 낮게 설정하여, 다른 프로세스보다 **죽지 않도록 보호**.
- 그러나 **개별 컨테이너의 OOM 우선순위는 조정되지 않아서**, 문제가 생기면 **컨테이너가 먼저 종료**되는 쪽으로 설계되어 있음.
- 이 보호 기능을 무시하고 `--oom-score-adj` 값을 극단적으로 낮게(extreme negative number) 설정하거나, `--oom-kill-disable` 옵션을 사용해서 컨테이너의 종료를 방지하려고 하는 것은 **권장되지 않습니다**.

### **다음과 같은 방식으로 OOME 리스크를 완화**
- 애플리케이션의 메모리 요구량을 사전에 테스트해서 파악.
- 애플리케이션은 충분한 리소스를 가진 호스트에서만 실행.
-  컨테이너가 사용할 수 있는 메모리를 제한 (--memory 옵션 등).
- Docker 호스트에서 스왑(swap) 설정에 주의. 스왑은 메모리보다 느리지만, 시스템 메모리가 고갈되는 것을 대비해 버퍼를 제공.
- 컨테이너를 서비스로 전환하고, 서비스 제약조건 및 노드 라벨(node labels) 을 사용하여, 메모리가 충분한 호스트에서만 실행되도록 설정.
	- 여기서 '서비스'는 도커 스웜에서의 서비스를 말한다. 즉, 분산 실행 하라는 것.

### **컨테이너의 메모리 접근 제한**
도커는 두가지로 제한이 가능함. **hard** or **soft** memory limits.
- Hard limits: the container **use no more than a fixed amount of memory**.
- Soft limits: the container **use as much memory as it needs unless certain conditions are met**, such as when the kernel detects low memory or contention on the host machine.

**메모리 제약 설정하는 옵션들은 아래 링크 참조**
> https://docs.docker.com/engine/containers/resource_constraints/#limit-a-containers-access-to-memory


## **CPU**
호스트 머신의 CPU 사이클에 대한 지정된 컨테이너의 액세스를 제한하기 위해 다양한 제약 조건을 설정할 수 있다. 대부분의 유저는 기본  [default CFS scheduler](https://docs.docker.com/engine/containers/resource_constraints/#configure-the-default-cfs-scheduler)을 씀. [real-time scheduler](https://docs.docker.com/engine/containers/resource_constraints/#configure-the-real-time-scheduler)를 설정할 수도 있다.

CFS는 일반적인 리눅스 커널의 CPU 스케줄러임. 몇몇 runtime flag들을 사용시 컨테이너가 가지고 있는 CPU 리소스에 대한 액세스량을 설정 할 수 있다. 이런 설정을 사용하면 도커는 호스트 시스템의 컨테이너의 cgroup 세팅을 수정한다.

**CPU 제약 설정하는 옵션들은 아래 링크 참조**
> https://docs.docker.com/engine/containers/resource_constraints/#cpu