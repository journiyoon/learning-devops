# Kubernetes 아키텍처

## 📌 개요
Kubernetes(k8s)는 컨테이너화된 애플리케이션의 자동 배포, 스케일링, 관리를 위한 오픈소스 플랫폼입니다. 이 문서에서는 k8s의 전반적인 아키텍처와 각 컴포넌트의 역할을 설명합니다.

## 🔍 컨트롤 플레인 (Control Plane)
마스터 노드에서 실행되며, 클러스터의 전반적인 결정과 이벤트 감지 및 응답을 담당합니다.

### API 서버 (kube-apiserver)
- 쿠버네티스 API를 노출하는 컨트롤 플레인의 프론트엔드
- 모든 내/외부 통신의 게이트웨이 역할
- REST 작업을 수행하며 클러스터의 공유 상태를 업데이트
- 수평적 확장 가능 (여러 API 서버 인스턴스 실행 가능)

### etcd
- 클러스터의 모든 데이터를 저장하는 분산 키-값 저장소
- 고가용성을 위해 여러 노드에 분산 저장 가능
- 클러스터 구성의 일관성과 신뢰성 보장
- 백업이 필수적인 중요 컴포넌트

### 스케줄러 (kube-scheduler)
- 새로 생성된 Pod을 실행할 노드 선택
- 하드웨어/소프트웨어/정책 제약 조건, 데이터 지역성, 워크로드 간섭 등 고려
- 리소스 요구사항, 노드 상태, 정책 제약을 기반으로 최적의 노드 선택

### 컨트롤러 매니저 (kube-controller-manager)
다양한 컨트롤러 프로세스를 실행:
- Node Controller: 노드 다운 시 대응
- Replication Controller: Pod 개수 유지
- Endpoints Controller: 서비스와 Pod 연결
- Service Account & Token Controllers: 계정 및 API 토큰 관리

## 🔧 노드 컴포넌트 (Node Components)
워커 노드에서 실행되며, 실제 워크로드를 처리합니다.

### Kubelet
- 각 노드에서 실행되는 에이전트
- Pod 스펙(PodSpec)에 따라 컨테이너 실행 보장
- 컨테이너 상태 모니터링 및 보고
- 노드의 건강 상태 체크

### Kube-proxy
- 네트워크 규칙 관리
- Pod 간 네트워크 통신 가능하게 함
- 클러스터 내/외부 네트워크 세션 관리
- iptables/IPVS 규칙을 통한 네트워크 트래픽 라우팅

### 컨테이너 런타임
- 컨테이너 실행을 담당 (Docker, containerd, CRI-O 등)
- 이미지 다운로드 및 압축 해제
- 컨테이너 프로세스 실행 및 격리

## 📊 작동 방식
1. 사용자가 kubectl 또는 API를 통해 요청 전송
2. API 서버가 요청을 인증/인가하고 etcd에서 현재 상태 확인
3. 컨트롤러가 현재 상태와 원하는 상태의 차이를 감지하고 조정
4. 스케줄러가 새로운 Pod을 적절한 노드에 할당
5. Kubelet이 할당된 Pod의 컨테이너를 실행
6. Kube-proxy가 필요한 네트워크 구성 수행

## 🔐 보안 아키텍처
- TLS 인증서를 통한 컴포넌트 간 암호화된 통신
- RBAC을 통한 접근 제어
- Service Account를 통한 Pod 인증
- 네트워크 정책을 통한 Pod 간 통신 제어

## 🌐 네트워킹 아키텍처
- 모든 Pod은 고유한 IP 주소 할당
- Pod 간 NAT 없이 직접 통신 가능
- Node-Pod 간 NAT 없이 통신 가능
- CNI (Container Network Interface) 플러그인을 통한 네트워크 구현

## 📈 스케일링 아키텍처
- Control Plane 컴포넌트의 수평적 확장
- etcd의 분산 구성
- 노드 자동 스케일링
- Pod 자동 스케일링 (HPA/VPA)

## ⚠️ 고가용성 고려사항
- Control Plane 컴포넌트 다중화
- etcd 클러스터 구성
- 여러 가용 영역에 노드 분산
- Pod 중복 실행을 통한 워크로드 가용성 확보

## 🛠 모니터링 포인트
- 컴포넌트 상태 모니터링
- 리소스 사용량 모니터링
- 로그 수집 및 분석
- 알림 설정

## 📚 참고 자료
- [Kubernetes 공식 문서 - 클러스터 아키텍처](https://kubernetes.io/docs/concepts/architecture/)
- [Kubernetes 컴포넌트](https://kubernetes.io/docs/concepts/overview/components/)
- [클러스터 네트워킹](https://kubernetes.io/docs/concepts/cluster-administration/networking/)