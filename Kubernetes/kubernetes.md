## 목차
- [쿠버테니스 정의](#쿠버테니스-정의)
- [쿠버테니스 리소스](#쿠버테니스-리소스)
  - [쿠버네티스 클러스터와 노드](#쿠버네티스-클러스터와-노드)
  - [Pod](#pod)
  - [ReplicaSet](#replicaset)
  - [Deployment](#deployment)
  - [Service](#service)
  - [Ingress](#ingress)
  - [pv와 pvc](#pv-pvc)
  - [StatefulSet](#statefulset)
  - [Job](#job)
  - [Cronjob](#cronjob)
  - [Secret](#secret)



## 쿠버테니스 정의
- 컨테이너 운영을 자동화하기 위한 '컨테이너 오케스트레이션 도구'
- 구글의 주도로 개발
- 많은 컨테이너를 잘 연동시키기 위한 통합 시스템
- 다양한 리소스 조합해 유연하게 애플리케이션 구축 및 배포, 운영 관리 업무 자동화 가능
- API 및 쿠버테니스를 다루기 위한 명령행 도구인 'kubectl'이 제공됨

## 쿠버테니스 리소스
- 리소스 = 애플리케이션 구성하는 부품

|리소스 | 설명           |
--------|-------------------------------------
노드 Node | 컨테이너가 배치되는 서버
파드 Pod | 컨테이너의 집합 중 '가장 작은 단위', 컨테이너 실행 방법 정의
리플리카세트 RS | 같은 스펙의 여러 파드 생성 및 관리
디플로이먼트 deploy | 레플리카 세트의 리비전 관리
서비스 svc | 파드 집합에 접근하기 위한 경로 정의
인그레스 ingress | 클러스터 외부로 서비스 노출
컨피그맵 cm | 설정 정보 정의하고 파드에 전달
퍼시스턴트볼륨 pv | 파드 사용할 스토리지 크기 및 종류 정의
퍼시스트볼륨클레임 pvc | 퍼시스트볼륨 동적으로 확보
스테이트풀세트 statefulset | 같은 스펙의 동일 파드 여러개 생성 관리
잡 job | 파드 여러개 생성 후 정상적 종료 보장. 일회성
크론잡 cronjob | 크론 문번으로 스케줄링되는 잡
시크릿 secret | 인증 정보 같은 기밀 데이터 정의


## 쿠버네티스 클러스터와 노드
- 쿠버테니스 클러스터 = 쿠버네티스의 여러 리소스를 관리하기 위한 집합체
- 리소스 중 가장 큰 개념은 '노드'
- 클러스터는 적어도 하나 이상의 'control plane'과 'worker node'로 구성
- 'control plane'은 클러스터 전체를 관리

### Control Plane 구성
<img src="https://user-images.githubusercontent.com/57473228/161087623-f75f506e-f9c7-4976-aeb4-f4b74f6c6a2e.svg" width="800"> 

관리 컴포넌트 | 역할
--------|-------------------------------------
API Server | 쿠버네티스 API를 노출시하는 컴포넌트 kubectl로부터 리소스를 조직하라는 지시 받음
Controller Manager | 리소스 제어하는 컨트롤러 실행
Scheduler | 노드 모니터링, 컨테이너 배치할 노드 선택
etcd | 분산 키-값 스토어, 고가용성, 백킹 스토어

## Pod
파드 (pod) = 컨테이너 집합체의 단위
- 적어도 하나 이상의 컨테이너
- 한 파드 안의 컨테이너는 모두 같은 노드에 배채해야
- 파드에는 고유의 가상 IP주소가 할당됨
  - 이 IP주소는 파드에 속하는 모든 컨테이너가 공유 
  - 같은 파드의 컨테이너 간 통신 가능
  - 다른 파드와 통신 가능

pod.yaml
```
apiVersion: v1
kind: Pod   # 리소스 유형
metadata:
  name: nginx   # 리소스 이름 = 파드명
  labels:
    app: echo
spec:
  containers:
  - name: nginx
    image: nginx:1.14.2
    ports:
    - containerPort: 80   # 노출시킬 포트 지정
```
- 실행 : kubectl apply -f pod.yaml
- kubectl get pod
- 컨테이너에 접근 : kubectl exec -it nginx bash
- 삭제 : kubectl delete pod nginx

### 쿠버네티스 오브젝트에 대한 yaml 파일의 필드
- apiVersion : 이 오브젝트를 생성하기 위해 사용하고 있는 쿠버네티스 API 버전이 어떤 것인지
- kind : 어떤 종류의 오브젝트(리소스)를 생성하고자 하는지
- metadata: 오브젝트를 유일하게 구분지어 줄 데이터 (이름 문자열, UID, 그리고 선택적인 네임스페이스를 포함) 
- spce : 오브젝트에 대해 어떤 상태를 의도하는지

## ReplicaSet
레플리카세트 = 같은 정의 파드 여러개 생성 관리 리소스
- 지정된 수의 파드 레플리카가 항상 실행되도록 보장
- 레플리카셋을 정의하는 필드는 
  - 획득 가능한 파드를 식별하는 방법이 명시된 셀렉터
  - 유지해야 하는 파드 개수를 명시하는 레플리카의 개수
  - 레플리카 수 유지를 위해 생성하는 신규 파드에 대한 데이터를 명시하는 파드 템플릿을 포함

```
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: frontend
  labels:
    app: echo
spec:
  replicas: 3   # 파드의 복제본 수
  selector:     # 레이블 셀렉터
    matchLabels:
      app: echo
  template:
    metadata:
      labels:echo    # 레이블 셀렉터와 일치해야
    spec:
      containers:
      - name: nginx
        image: nginx:latest
```
- kubectl get rs

### 파드 셀렉터
.spec.selector 필드는 레이블 셀렉터
- 이 레이블은 소유될 가능성이 있는 파드를 식별하는데 사용
- 레플리카셋의 셀렉터와 일치하는 레이블(app: echo)을 새로운 파드 생성 시 사용한다면, 레플리카 수 요구 사항을 충족시키기 위해 파드 수를 조절


## Deployment

디플로이먼트 = 애플리케이션 배포(deploy)의 기본 단위가 되는 리소스
- 레플리카세트를 관리 리소스
- 레플리카세트 보다 상위 리소스
- 쿠버네티스는 '디플로이먼트 단위'로 애플리케이션 배포
- 디플로이먼트가 관리하는 레플리카세트는 지정된 개수(replicas)만큼 파드 확보, 교체, 이전버전으로 롤백
- 디플로이먼트 수정(컨테이너 정의 수정 등) 시 레플리카세트 새로 생성됨
- replicas 값만 변경해서는 레플리카세트 교체 x

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80

```
- kubectl get deploy

## Service

서비스 = 쿠버네티스 클러스터 안에서 파드의 집합에 대한 경로나 서비스 디스커버리 제공하는 리소스
- 서비스 디스커버리 = API가 동적으로 바뀌는 경우에도 클라이언트가 접속 대상 바꾸지 않고, 하나의 이름으로 접근할 수 있는 기능
- 서비스의 대상이 되는 파드는 서비스에서 정의하는 '레이블 셀렉터'로 정의 
- svc 사용 이유
  - 파드는 비영구적 리소스
  - 각 파드는 고유한 IP 주소를 갖지만, 디플로이먼트에서는 한 시점에 실행되는 파드 집합이 잠시 후 실행되는 해당 파드 집합과 다를 수 있음

```
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: MyApp
  ports:
    - protocol: TCP
      port: 80
      targetPort: 9376
```
- spec.selector 속성값으로 서비스 대상으로 삼을 파드의 레이블값을 설정
- 파드의 레이블이 서비스에 정의된 셀렉터 값과 일치하면, 해당 파드는 그 서비스의 대상이 되므로, 서비스를 경유해 트래픽이 들어옴

### 서비스 종류
|서비스  |설명   |
------|------------------------
ClusterIP | 기본값, 클러스터 내부IP주소에 서비스 공개, 외부로부터 접근x
NodePort | 클러스터 밖 외부IP에서 접근o, ClusterIP 만듦, L4 레벨에서 노출, 고정 포트 (NodePort)로 각 노드의 IP에 서비스를 노출
LoadBalancer | 클라우드 공급자의 로드 밸런서(GLB, ELB)를 사용하여 서비스를 외부에 노출. 외부 로드 밸런서가 라우팅되는 NodePort와 ClusterIP 서비스가 자동으로 생성
ExternalName | 값과 함께 CNAME 레코드를 리턴하여, 서비스를 externalName 필드의 콘텐츠 (예:foo.bar.example.com)에 매핑, 외부 호스트를 네임레졸루션하기 위한 별명 제공, 포트 정의x

```
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: NodePort
  selector:
    app: MyApp
  ports:
      # 기본적으로 그리고 편의상 `targetPort` 는 `port` 필드와 동일한 값으로 설정된다.
    - port: 80
      targetPort: 80
      # 선택적 필드
      # 기본적으로 그리고 편의상 쿠버네티스 컨트롤 플레인은 포트 범위에서 할당한다(기본값: 30000-32767)
      nodePort: 30007
```

#### 서비서의 네임 레졸루션
- 쿠버네티스 클러스터의 DNS는 서비스를 '서비스명.네임스페이스.svc.local'로 연결해줌
- svc.local은 생략 가능
- 다른 ns의 svc를 참조하는 이름은 '서비스명.네임스페이스'
- 같은 ns는 '서비스명'

### 셀렉터가 없는 서비스
- 파드 셀렉터 없이 서비스를 정의해서 다른 종류의 백엔드를 추상화
- 아래의 서비스에는 셀렉터가 없으므로, '엔드포인트 endpoint 오브젝트'가 자동 생성 x
- 엔드포인트 수동 추가하여, 서비스를 실행 중인 네트워크 주소 및 포트에 수동으로 매핑 

```
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  ports:
    - protocol: TCP
      port: 80
      targetPort: 9376
---
apiVersion: v1
kind: Endpoints
metadata:
  name: my-service
subsets:
  - addresses:
      - ip: 192.0.2.42
    ports:
      - port: 9376
```

### 헤드리스 Headless 서비스
- 로드-밸런싱과 단일 서비스 IP는 필요치 않는 경우 사용 
- 명시적으로 클러스터 IP (.spec.clusterIP)에 "None"을 지정


## Ingress
인그레스 = 클러스터 외부에서 클러스터 내부 서비스로 HTTP와 HTTPS 경로를 노출
- 클러스터 내의 서비스에 대한 외부 접근을 관리하는 API 오브젝트이며, 일반적으로 HTTP를 관리
- HTTP(S)와 같은 경로 기반으로 서비스를 전환하는 L7 레벨의 제어를 위한 리소스
- 인그레스 리소스가 작동하려면, 클러스터는 실행 중인 '인그레스 컨트롤러'가 반드시 필요

### TLS
- TLS 개인 키 및 인증서가 포함된 시크릿(Secret)을 지정해서 인그레스를 보호 가능
- TLS secret에는 tls.crt 와 tls.key 라는 이름의 키가 있어야 하고, 여기에는 TLS에 사용할 인증서와 개인 키가 있음

```
apiVersion: v1
kind: Secret
metadata:
  name: testsecret-tls
  namespace: default
data:
  tls.crt: base64 encoded cert
  tls.key: base64 encoded key
type: kubernetes.io/tls
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: tls-example-ingress
spec:
  tls:
  - hosts:
      - https-example.foo.com
    secretName: testsecret-tls
  rules:
  - host: https-example.foo.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: service1
            port:
              number: 80
```
- kubectl get ingress


## PV PVC



## Statefulset



## Job 


## Cronjob



## Secret


## 참고
- [도커/쿠버네티스를 활용한 컨테이너 개발실전 입문](http://www.yes24.com/Product/Goods/70893433)
- [컨테이너 인프라 환경 구축을 위한 쿠버테니스/도커](http://www.yes24.com/Product/Goods/102099414)
- [쿠버네티스 인 액션](http://www.yes24.com/Product/Goods/89607047)
- [kubernetes.io](https://kubernetes.io/docs/concepts/)
