# 1. Core Concepts

## PODs
```
# pod 생성
kubectl run pod --image=nginx

# pod 생성에 사용된 image, container 확인
kubectl describe pod newpod

# pod가 배치된 node 확인
kubectl get pods -o wide

# 야물 이용한 pod 생성
kubectl run redis --image=redis123 --dry-run=client -o yaml > redis.yaml
kubectl create -f redis.yaml 

# pod 생성에 사용한 야물 편집
kubectl edit pod redis
kubectl apply -f redis-definition.yaml 
```

## ReplicaSets
```
# 설명 보기 
kubectl explain rs
kubectl explain rs | grep VERSION

# scaling
kubectl scale rs new-replica-set --replicas=5
```

## Deployments
```
# 명령어 도움말 보기
kubectl create deployment --help
```

## Namespaces
```
# 특정 네임스페이스 갖는 pod 수 확인
kubectl get pod -n research

# 특정 네임스페이스 갖는 pod 생성
kubectl run redis --image=redis -n=finance

# 특정 네임스페이스 갖는 pod 보기
kubectl get pod --all-namespaces | grep blue
```

## Services
```
# svc type : LoadBalancer / NodePort / ClusterIP/ External

# svc의 labels, targetPort, Endpoint 확인 
kubectl describe svc kubernetes
```

## Imperative Commands
```
# 특정 labels 갖는 pod 생성
kubectl run redis -l tier=db --image=redis:alpine

# expose pod from svc 
kubectl expose pod redis --port=6379 --name=redis-service

# svc와 같은 이름의 pod 생성, 
kubectl run httpd --image=httpd:alpine --port=80 --expose=true
kubectl run --help
```

# 2. Scheduling

## Manual Scheduling
```
# pod 삭제 후 다시 생성
kubectl replace --force -f nginx.yaml

```

## Labels & Selectors
```
# use selectors to filter 
kubectl get pods --selector env=dev

# 특정 selector의 모든 object 보기
kubectl get all --selector env=prod

# 2개 이상의 labels 갖는 object 보기
kubectl get all --selector env=prod,bu=finance,tier=frontend
```

##  Taints & Tolerations
```
# 특정 node에 Taints 확인
kubectl describe node node01 | grep -i taints
kubectl describe node node01 | grep Taints

```

## Node Affinity
```
# node에 특정 pod 할당
kubectl edit deployment blue

# 복사한 코드 줄 맞추기
# shift + v : 한줄 선택 -> 화살표 아래로 블록 선택
# shift + . : 자동 줄맞춤
```

## Resource Limits
```
# 파드가 실행되고 있는 경우 리소스 수정x
# 수정된 파일은 다른 파일로 저장됨
kubectl replace --force -f /tmp/kubectl-edit-213647675.yaml

```

## DaemonSets
```
# 데몬셋은 노드 개수만큼 생성됨
# 노드 개수만큼 pod가 유지됨

kubectl get ds --all-namespaces

# Deploy a DaemonSet
kubectl create deployment elasticsearch --image=k8s.gcr.io/fluentd-elasticsearch -n kube-system --dry-run=client -o yaml > fluentd.yaml
# remove the replicas, strategy and status 
# change the kind from Deployment -> DaemonSet
kubectl create -f fluentd.yaml
```

## Static PODs
```
# staticPod config
cat /var/lib/kubelet/config.yaml

# staticPodPath 찾기
ls /etc/kubernetes/manifests 

# staticPod 생성
kubectl run --restart=Never --image=busybox static-busybox --dry-run=client -o yaml --command -- sleep 1000 > /etc/kubernetes/manifests/static-busybox.yaml

```

## Multiple Schedulers
```
# 

```

# 3. Logging & Monitoring


