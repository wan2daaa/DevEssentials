쿠버네티스 클러스터 내에서 실행중인 [[Infra/K8S/Pod|Pod]](컨테이너 그룹)에 대한 네트워크 접근성을 제공하는 역할을 수행 

[[Infra/K8S/Pod|Pod]]는 수시로 생성 및 삭제되며, IP 주소가 변경되므로 -> Service를 통해 [[Infra/K8S/Pod|Pod]]에 접근 
-> L4 로드밸런서 역할 

Service는 **논리적 서비스 단위**로 **파드 그룹에 대한 네트워크 주소와 포트를 노출**


[[Infra/K8S/Pod 관리 방식/Deployment|Deployment]]와 Service의 관계는 ? 
- Pod들은 Deployment가 관리하는 Pod 이고, 
- Service는 Deployment가 관리하는 Pod 들을 라우팅 해준다. 
- Service는 Pod의 label 기준으로 관리한다. 

![[assets/스크린샷 2024-12-05 오전 10.18.13.png]]


