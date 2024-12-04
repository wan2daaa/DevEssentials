조금 더 편하게 쿠버네티스 구성하기 

- k8s 클러스터를 쉽게 생성하고 관리하기 위한 도구 


**aws eks 와 eksctl 연동**
```shell
aws eks --region ap-northeast-2 update-kubeconfig --name terraform-cluster
```


```shell
kubectl get node
kubectl get po -A
```


 envsubst < Dockerfile.template | docker build -t ${MODULE}:${VERSION} -f - .