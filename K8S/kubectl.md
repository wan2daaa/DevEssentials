```shell
kubectl apply -f gw-back-pod.yaml 

kubectl get po

kubectl get po -A

%% 지속적으로 로그형식으로 계속 나옴 %%
kubectl get po --watch

%% 어떤 명령어들을 이런식으로도 사용 가능 %%
kubectl run curl --image=curlimages/curl -- sleep infinite 

```
