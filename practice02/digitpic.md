alias 지정해 kubectl 대신 k 로 사용 가능  
다른 명령어들도 alias 지정하면 편하게 사용 가능

---

### krew

kubectl 플러그인 매니저  
kubectl 에서 사용 가능한 플러그인 설치, 삭제, 조회 기능 제공

list 로 설치된 플러그인 현황 확인  
search 로 플러그인 검색  
install 로 플러그인 설치

`kubectl krew search`

---

## kube-ctx

쿠버네티스 context 를 선택하는 플러그인  
여러 클러스터 중 원하는 클러스터를 선택할 수 있는 플러그인  
`kubectl krew install ctx` 를 통해 설치

`kubectl ctx` 를 통해 로컬에 등록된 클러스터 목록 확인  
`kubectl ctx 클리스터이름` 으로 클러스터 변경

---

## kube-ns

`kubectl krew install ns` 를 통해 설치  
`kubectl ns` 를 통해 현재 클러스터 전체 네임스페이스 현황 출력  
`kubectl ns 네임스페이스이름` 으로 네임스페이스 변경

---

## kube-ps1

현재 클러스터와 네임스페이스를 프롬프트에 표시하는 플러그인  
`git clone https://github.com/jonmosco/kube-ps1.git`  
`chmod +x ./kube-ps1/kube-ps1.sh`  
`vi ~/.bashrc`

[##_Image|kage@OJCDS/btsLAAWDw0i/vL9l03G4bqy6LSnuA5Bfn0/img.png|CDM|1.3|{"originWidth":273,"originHeight":77,"style":"alignLeft"}_##]

  
`source ~/.bashrc`

위 과정을 통해 적용

