- 모든 노드에서 실행되어야 하는 백그라운드 작업을 위한 리소스
- 모든 노드에 **하나의 [[K8S/Pod|Pod]]** 를 유지하며, 노드가 추가되거나 제거될 때 자동으로 조정된다. 

- e.x. 로깅, 모니터링, 네트워크 에이전트 등과 같은 백그라운드 서비스에 유용

**Ref**
https://kubernetes.io/ko/docs/concepts/workloads/controllers/daemonset/