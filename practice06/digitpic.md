# 헬름 기반으로 애플리케이션 설치하기

파드의 노출을 담당하는 Service
애플리케이션 설정에 관련된 ConfigMap
기밀 정보를 다루는 Secret

- Helm
다양한 리소스를 각각 관리하지 않고 하나의 패키지로 관리하는 도구

리눅스에서 apt, yum 으로 애플리케이션에 필요한 라이브러리를 패키지 형태로 제공하는 것과 유사

많은 업체에서 쿠버네티스 환경에서 애플리케이션을 설치할 수 있도록 헬름 파일을 제공

## 헬름의 주요 구성 요소: charts, repository, templates

### helm charts
애플리케이션 설치에 사용되는 네트워크, 스토리지, 보안과 관련된 여러 쿠버네티스 리소스를 묶어놓은 패키지
보통 차트는 그래프를 의미하지만 헬름에서 차트는 여러 리소스의 묶음을 의미

#### 헬름 차트 디렉터리 구조
- Chart.yaml
	- charts 에 대한 정보가 담긴 yaml 파일
- LICENSE
	- charts 의 라이센스 정보가 담긴 텍스트 파일
- README.md
	- 해당 charts 에 대한 설명을 포함한 README 파일
- values.yaml
	- charts 의 기본 템플릿 변수 파일
- charts/
	- charts 에 종속된 charts 들을 포함하는 디렉터리
- crds/
	- 커스텀 자원 정의
- templates/
	- values 파일과 같이, 유효한 쿠버네티스 매니페스트 파일을 생성하는 템플릿
- templates/NOTES.txt
	- charts 사용법을 설명하는 텍스트 파일

### helm repository
다양한 helm charts 를 저장하고 공유하는 저장소

### helm templates
설치와 관련된 파일을 관리
일정한 형식, 포맷을 의미
사용자는 이름, 날짜 등 특정 변수만 수정해 파일 사용 가능
여러 템플릿 파일에서 공통으로 사용하는 변수를 단일 values.yaml 에서 관리
해당 파일만 수정하면 전체 템플릿 파일에서 사용하는 변수를 한번에 수정 가능

## 헬름 차트를 이용한 Nginx 웹서버 설치

### 헬름을 이용한 애플리케이션 라이프사이클 관리
- helm repo add
	- repository 를 로컬 환경에 추가
- helm pull
	- charts 파일을 로컬에 다운
- helm install {helm_name} -f values.yaml .
	- 수정한 파일을 템플릿 옵션 파일로 지정해 원하는 애플리케이션을 설치
- helm ls
	- 설치된 charts 목록 확인
- helm get manifest
	- 실행 중인 charts 의 전체 리소스 목록 yaml 확인
- helm upgrade
	- 기존 charts 의 변수 등을 수정해 재배포
- helm delete
	- charts  삭제

### 헬름 템플릿 변수 파일 사용하기

{{- if .Values.resources }}
if 를 사용해 resources 변수가 정의돼 있으면 해당 변수를 사용하도록 설정

{{- toYaml .Values.resources | nindent 12 }}
toYaml 함수를 사용해 해당 변수의 내용을 들여쓰기 12칸 형식의 yaml 형식으로 전환
 
### 리소스 Requests/Limits 이해
charts 로 설정한 변수 중 리소스 Request/Limits 가 존재
특정 파드가 자원을 많이 사용하면 다른 파드의 성능에 영향을 끼칠 수 있기에
파드가 사용 가능한 자원을 제한할 수 있도록 함

limits 는 해당 자원 이상 사용하지 못하도록 제한
넘어가면 OOMKilled 상태가 됨 (Out Of Memory)

requests 는 노드에 파드를 할당할 때,
해당 요청량 이상의 여유 자원이 있는 노드에 파드를 할당하게 하는 옵션
파드가 보장받을 수 있는 자원 요청량
파드가 요청해서 사용 가능한 자원 사용량

