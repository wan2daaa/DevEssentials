쿠버네티스에서 생성하고 관리할 수 있는 배포 가능한 가장 작은 컴퓨팅 단위

- Pod은 하나 이상의 컨테이너로 구성되어있다 
- Pod 단위로 스케줄링이 된다.
- Pod 내부 컨테이너들은 스토리지와 네트워크를 공유한다.
- 클러스터 내에서 유일한 IP를 가진다. (logical Host , 논리적 주소)
- Pod 내의 컨테이너간 네트워크 통신도 Logical Host를 이용한다. 

**Ref**
https://kubernetes.io/ko/docs/concepts/workloads/pods/

