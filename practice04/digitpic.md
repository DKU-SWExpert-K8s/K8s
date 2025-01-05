복잡한 옵션들을 사용해야 하는 경우, kubectl 명령어만을 통해 관리하기는 어렵습니다.

복잡한 옵션들을 코드로 구현해두면 kubectl 명령어보다 가독성이 높아지며, 재사용성도 증가하게 됩니다.

쿠버네티스 코드를 작성하는 데에는 보통 yaml 파일을 사용합니다.

모든 쿠버네티스 리소스와 옵션들은 yaml 파일로 구현할 수 있습니다.

예제는 아래와 같습니다.

특정 노드에서만 파드가 실행되도록 하지 않고, 파드를 여러 노드에 분산해 배치하는 예제입니다.

이를 anti-affinity 라고 합니다.

```yaml
affinity:
  podAntiAffinity:
    preferredDuringSchedulingIngnoredDuringExecution:
    - podAffinityTerm:
        labelSelector:
          matchExpressions:
          - key: app
            operator: In
            values:
            - nginx
        topologyKey: kubernetes.io/hostname
      weight: 100
```

대부분의 쿠버네티스 작업은 위와 같이 코드를 작성하는 작업입니다.

아래와 같은 쿠버네티스 오브젝트들은 복잡한 옵션을 사용해야 하는 경우가 많습니다.

파드를 연결하는 방식을 정의하는 service

개별 애플리케이션의 환경변수 설정을 정의하는 ConfigMap

자원을 많이 사용해서 동일한 노드를 사용하는 다른 파드에 영향을 끼치지 않도록 하는 리소스 limit, requests

코드 형태로 한 번 구현이 되면, 복사해 다시 사용하면서 재사용성이 높아지게 됩니다.

kubectl 명령어에 -o yaml 옵션을 주면 쿠버네티스 리소스를 yaml 파일 형태로 export 할 수 있습니다.

하지만 불필요한 status 정보도 포함되어 있어 보기가 불편합니다.

kube-neat 을 사용하면, 이 불필요한 정보를 제거해 보여줍니다.

---

### 1절. yaml 파일 익스포트 플러그인 kube-neat 설치

`kubectl krew install neat` 를 통해 설치할 수 있습니다.

\-o yaml 옵션에 kube-neat 명령어를 추가하면 간결한 yaml 파일을 export 할 수 있습니다.

`kubectl get pod busybod -o yaml | kubectl neat` 와 같이 사용하면 됩니다.

---

### 2절. yaml 파일을 이용한 파드 배포

`spec.containers.command` 안에

```
- "/bin/sh"
- "-c"
- "sleep inf"
```

위 내용을 적어 줌으로, 시간 제한 없이 sleep 명령어가 작동하게 합니다.

`kubectl apply -f example.yaml` 를 적어 줌으로 yaml 파일로부터 파드를 생성합니다.

쿠버네티스에서는 yaml 파일 문법을 들여쓰기, 배열 정의, 주석 처리라는 세 가지만 알고 있어도 됩니다.

들여쓰기를 통해 계층을 나누기에 정확한 들여쓰기가 필요합니다.

들여쓰기는 2칸 혹은 4칸을 사용하지만, 일반적으로 2칸을 사용합니다.

배열은 '-' 을 통해 표현하고 위에서부터 순서대로 항목을 나열합니다.

주석은 '#' 기호를 사용하면 됩니다.

vi 를 통해 yaml 을 수정하기는 쉽지 않으니 각자가 선호하는 text editor 를 사용해도 됩니다.

```yaml
resources:
  limits:
    memory: 512Mi
  requests:
    memory: 128Mi
```

위와 같이 작성하면

해당 파드의 최대 메모리 사용량을 512Mi, 최소 사용량을 128Mi 로 보장할 수 있게 됩니다.

---

### 3절. 쿠버네티스 yaml 템플릿 파일 검색 및 네이밍 규칙을 적용해 파일 저장하기

yaml 파일은 [쿠버네티스 공식 홈페이지](https://kubernetes.io/ko/docs/home/ "쿠버네티스 공식 홈페이지")에서 거의 모든 오브젝트에 대한 템플릿을 제공하기에 처음부터 작성하지 않아도 됩니다.

다른 사람들이 작성한 블로그를 보기보다, 공식 홈페이지를 보는 게 불편하지만 결국은 더 빠른 길이기에 익숙해져야 합니다.

거의 모든 쿠버네티스 오브젝트 yaml 파일은 AKMS(apiVersion, kind, metadata, spec) 필드를 포함하고, 해당 필드는 들여쓰기상 첫 번째에 위치합니다.

apiVersion 은 오브젝트를 생성하기 위해 사용하고 있는 쿠버네티스 API 의 버전 정보입니다.

kubectl api-versions 명령어로 현재 지원하는 전체 API 목록을 확인할 수 있습니다.

kind 에서 생성할 오브젝트의 종류를 나타냅니다.

metadata 에서 이름, UID, 네임스페이스를 포함해 각 오브젝트를 식별할 수 있는 데이터 등을 지정합니다.

spec 에서 오브젝트에 대한 상세한 상태를 지정합니다.

yaml 파일명은 네이밍 규칙을 정해두면 다음에 재사용할 때 쉽게 찾을 수 있습니다.

네이밍 규칙 예시

[앱 이름]-[옵션]-[오브젝트 이름].yml

nginx-clusterIP-svc.yml

