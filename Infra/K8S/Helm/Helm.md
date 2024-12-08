### 특징 

- **복잡성 관리**
	- 복잡한 YAML 매니페스트를 **템플릿화**하고, 값 파일을 통해 구성을 관리 

- **쉬운 업데이트**
	- 새로운 버전의 차트를 설치하거나, 현재 차트의 값 파일을 업데이트 용이

- **간편한 공유**
	- 공개 Helm 차트 리포지토리가 있어 다른 사용자와 차트를 공유하고 다운로드 할 수 있음
	- 사용자 정의 Helm 차트를 개인 또는 조직내에서 공유

- 롤백
	- 롤백 기능을 제공하여 애플리케이션 업데이트 중 문제가 발생할 경우, 이전으로 복구


### 실행 방법

- Helm Chart(k8s 리소스 자원들이 기술) + values.yaml(앱 구동에 필요한 설정이 담긴 자원) 필요
- K8S 클러스터 내에 별도의 패키지를 설치할 필요 없음 

![[assets/스크린샷 2024-12-06 오전 11.39.52.png]]

``` shell
helm create gw-back-chart

helm template dev-ge-back . -f values-dev.yaml

helm install --namespace dev --create-namespace dev-gw-back . -f values-dev.yaml 

%% -n 은 namespace %%
helm uninstall dev-gw-back -n dev
```



 kubectl create namespace argocd

초기 argocd 비밀번호는 k8s secrets 에 있다. k9s에서 x 누르면 값이 나옴 









