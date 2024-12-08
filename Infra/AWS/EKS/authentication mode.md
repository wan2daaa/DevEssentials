Amazon Elastic Kubernetes Service(Amazon EKS)에서는 클러스터에 대한 인증 모드를 설정하여 IAM 보안 주체의 Kubernetes API 서버**에 대한 접근 방식을 관리할 수 있습니다. EKS는 다음 세 가지 인증 모드를 지원합니다:

1. **ConfigMap 모드 (**CONFIG_MAP**)**: 기존 방식으로, aws-auth ConfigMap을 통해 IAM 사용자 및 역할을 Kubernetes 사용자 및 그룹에 매핑합니다. 이를 통해 클러스터에 대한 접근 권한을 부여합니다.
2. **EKS API 및 ConfigMap 모드 (**API_AND_CONFIG_MAP**)**: EKS API와 aws-auth ConfigMap을 모두 사용하여 IAM 보안 주체의 클러스터 접근을 관리합니다. 이 모드에서는 두 방법을 병행하여 사용할 수 있습니다.
3. **EKS API 모드 (**API**)**: 최신 방식으로, EKS API를 통해 IAM 보안 주체의 클러스터 접근을 관리합니다. 이 모드에서는 aws-auth ConfigMap을 사용하지 않고, EKS API를 통해 액세스 항목을 생성하여 IAM 사용자 및 역할에 대한 접근 권한을 부여합니다.


---

Amazon EKS에서 언급한 **인증 모드(authentication mode)**는 **Kubernetes API 서버**에 대한 접근 방식을 관리하는 설정을 의미합니다. 즉, 클러스터의 **API 서버**에 누가, 어떤 방식으로 접근할 수 있는지를 결정하는 것입니다.

**Kubernetes API 서버**는 클러스터의 중심 컴포넌트로, 사용자, 클러스터 내부 구성 요소, 외부 시스템 등이 클러스터와 상호작용할 때 사용하는 HTTP API를 제공합니다. 이를 통해 클러스터의 상태를 조회하거나 변경할 수 있습니다.

따라서, EKS의 인증 모드는 **IAM 사용자나 역할이 Kubernetes API 서버에 접근할 때 어떤 인증 방식을 사용할지**를 설정하는 것입니다. 예를 들어, CONFIG_MAP 모드에서는 aws-auth ConfigMap을 통해 이러한 접근을 제어하며, API 모드에서는 EKS API를 통해 직접 접근 권한을 관리합니다.

이러한 설정을 통해 클러스터의 보안을 강화하고, 적절한 사용자나 역할만이 API 서버에 접근하여 클러스터를 제어할 수 있도록 합니다.