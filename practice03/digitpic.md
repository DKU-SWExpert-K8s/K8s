### 1절. nginx 파드 실행과 배시 실행

-   run, create
    -   pod, deployment 생성

-   get, exec
    -   pod 현황 조회, pod 내 명령 전송

-   scale, delete
    -   pod 수량 증가, 감소, 삭제

-   create namespace
    -   namespace 생성

쿠버네티스의 모든 오브젝트는 API 서버로 생성

  
사용자는 쿠버네티스 명령어 입력  
쿠버네티스는 해당 명령어의 API 호출해 오브젝트 실행

복잡한 옵션의 오브젝트의 경우, 명령어 대신 YAML 파일 사용

-   파드
    -   쿠버네티스 환경에서 컨테이너 애플리케이션을 실행하는 기본 단위
    -   일반적으로 하나의 파드 안에 하나의 컨테이너만 실행
    -   하나의 파드 안에 2개 이상의 컨테이너도 실행 가능
    -   IT 업계에서 흔히 사용하는 용어로는 컴퓨팅, 네트워크, 스토리지를 모듈 형태로 묶어 시스템 확장 시 사용하는 기본 단위를 의미

`kubectl run nginx --image=nginx`  
nginx 이미지로 nginx 파드 생성

`kubectl get pod`  
실행 중인 파드 목록 조회

`-o wide` 적으면 ip 주소 포함 더 자세한 정보 파악 가능

모든 파드는 서로 다른 ip 를 가짐  
각 파드는 각각 고유의 볼륨 사용

  
볼륨은 호스트 노드의 /var/lib/containers/파드이름 디렉토리 사용  
파드별 고유한 네트워크와 스토리지를 가짐

`kubectl exec -it nginx -- bash` 를 통해 bash 접속 가능  
쿠버네티스에서는 파드 접속이 파드에 bash 를 실행하는 것과 동일

컨테이너는 가상 머신과 다르게 특정 프로세스만 실행 중  
이미지 크기가 작고 실행 속도 매우 빠름

---

### 2절. 디플로이먼트와 파드 개수 변경과 삭제

> 파드의 개수를 변경, 삭제하는 작업을 수행

파드의 개수를 변경하려면 오브젝트 타입을 파드가 아닌 디플로이먼트로 실행

-   deployment
    -   pod 가 배포되는 방법을 정의하는 오브젝트
        -   pod 의 개수
        -   pod 의 이미지
        -   pod 의 배포 방법
        -   위 3가지를 정의

`kubectl create deployment` 를 통해 생성

  
`kubectl scale deployment httpd --replicas 10` 으로 파드 개수 증가

`kubectl delete pod httpd-12...231` 해도 pod 이 다시 살아남

  
desired state 가 "pod 개수 = 1" 이기 때문

---

### 3절. 네임스페이스 생성

> 네임스페이스는 클러스터를 구분하는 가상 클러스터 단위

같은 네임스페이스 내 같은 이름 오브젝트 생성 불가  
다른 네임스페이스 내 같은 이름 오브젝트 생성 가능

`kubectl create ns default01` 로 default01 이라는 namespace생성

현업에서는 애플리케이션을 구분하는 단위로 네임스페이스를 사용

웹(nginx), 웹 애플리케이션 서버(tomcat), 데이터베이스(mysql), 레디스(redis) 등 각 애플리케이션마다 네임스페이스를 별도로 지정

개발 담당자 별 권한 제한을 위해 사용하기도 함

클러스터를 논리적으로 분리하기에 물리적으로 분리하지는 못함  
그렇기에, 서로 다른 네임스페이스에 존재하는 파드끼리 통신이 가능함

