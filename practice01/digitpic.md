## 1장. 쿠버네티스 개요와 클러스터 설치

### 1절. 쿠버네티스란?
> 쿠버네티스의 소개와 특징

- 쿠버네티스
    - 대규모 클러스터 환경에서 컨테이너화된 애플리케이션 자동 배포
    - 확장, 관리를 위한 여러 요소 자동화
    - 오픈소스 플랫폼
    - 사용자 부하에 따라 자동으로 애플리케이션과 서버 규모 확장 가능

- 특징
    - 소스코드 기반 클러스터 운영
        - 명령어가 아닌 소스코드 기반
        - 적용하기 전에 코드로 구현해 검토 가능
        - 공통의 도구로써 효율적인 의사소통에 도움

    - desired state 를 통한 클러스터 관리
        - Go 언어의 watch 모듈을 통해 의도한 상태와 현재 실행 중인 상태를 끊임없이 비교
        - 의도한 상태에서 벗어나는 경우 의도한 상태로 다시 변경 (self-healing)
        - 예기치 않은 종료 시 자동으로 재시작
        - 운영자가 개입하지 않아도 되기에 생산성 향상

    - rolling update 를 통해 무중단 배포 가능

### 2절. Kubespray 를 이용해 3개의 노드로 구성된 클러스터 구축
> Kubespray, K3s 를 이용해 쿠버네티스를 설치하고 단일 지점 로컬호스트에서 여러 원격 클러스터를 관리하는 방법 소개

- 쿠버네티스 설치
    - 온프레미스 환경
        - kubeadm, Kubespray 등으로 설치

    - 퍼블릭 클라우드 환경
        - managed kubernetes service 이용
    
- 실습
    - Kubespray 를 이용한 쿠버네티스 클러스터 설치
        - Ansible 기반 쿠버네티스 설치 자동화 도구

sudo swapoff -a
sudo mount -o remount,rw /opt/orbstack-guest/
sudo chmod 755 /opt/orbstack-guest/etc
sudo vi /etc/resolv.conf 이후 nameserver 8.8.8.8 추가

## 3절. K3s를 이용해 단일 노드로 구성된 클러스터 구축
> 단일 노드 쿠버네티스 설치

- 단일 노드 쿠버네티스를 위해 K3s, minikube, kind 이용 가능
- 노드 간 워크로드 분배, 노드 failover 등 클러스터 관리 업무 수행 불가능

### 설치 방법
```bash
curl -sfL https://get.k3s.io | sh -
```

## 4절. 로컬호스트에서 원격 쿠버네티스 관리하기

### kubectl 설치
```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```

```bash
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

또는 
```bash
chmod +x kubectl
```

```bash
sudo mv kubectl /usr/local/bin/
```

kubeconfig 에서 API 서버 통신에 필요한 인증 정보를 관리
~/.kube/config 에 존재
kubectl config view 를 통해 확인 가능

원격 클러스터의 kubeconfig 파일을 로컬호스트로 복사한 뒤
서버 ip, 클러스터, 사용자, context 이름을 변경

