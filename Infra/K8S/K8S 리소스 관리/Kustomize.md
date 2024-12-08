- Kubernetes 리소스 구성 관리 및 배포를 간편화하는 도구 
- 다양한 환경 및 설정에 맞게 Kubernetes 리소스를 커스터마이징하고 배포할 수 있도록 지원 
- `kubectl` 에서 **kustomization file 지원**
	- `$ kubectl apply -k kustomizeFile`

### 특징 
 - **단순성** : 복잡한 템플릿 파일(Helm)을 작성하지 않고도 리소스를 커스터마이징 하고 배포 
 - **일관성** : 다양한 환경(dev, stage, qa, prod)에서 동일한 어플리케이션을 배포하기 위한 일관성을 제공
 - **Layered 방식** : 레이어 구조를 사용하여 리소스 정의를 계층화, 공통 기본 구성과 환경 특정 구성을 분리 가능 


![[assets/Pasted image 20241205141629.png]]

**HPA HorizontalPodAutoscaler** 
- 이걸로 오토 스케일링을 할 수 있음 


![[assets/스크린샷 2024-12-05 오후 2.18.33.png]]



### kustomization file 형식 

- `namespace` : 모든 자원에 namespace 추가 
- `namePrefix` : 모든 자원에 prefix 추가 
- `nameSuffix` : 모든 자원에 suffix 추가 
- `commonLabels` : 모든 리소스와 selectors에 레이블을 추가 
- `commonAnnotations` : 모든 리소스에 annotation 추가
 
![[assets/스크린샷 2024-12-06 오전 11.11.25.png]]


- `configMapGenerator` : ConfigMap 리소스 생성 (파일 또는 dotenv로 가능)

ㄴㄷ









