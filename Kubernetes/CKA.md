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
