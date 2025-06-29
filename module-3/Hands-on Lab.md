# 실습: VPC 연결을 위한 AWS AWS Transit Gateway  설정
이 실습에서는 AWS Transit Gateway를 설정하여 여러 VPC 간의 연결을 설정하는 방법을 시연할 것입니다. 우리의 목표는 세 개의 VPC를 연결하고 Transit Gateway를 사용하여 통신을 가능하게 하는 동시에 필요한 경로 테이블과 보안 그룹을 구성하여 VPC 간에 트래픽이 흐를 수 있도록 하는 것입니다.
</br></br>

## 실습 개요:
다음과 같은 아키텍처를 목표로 인프라를 구성합니다.:
1. 개인 서브넷이 있는 세 개의 VPC(VPC A, B 및 C)
2. VPC 간의 통신을 가능하게 하는 Transit Gateway
3. 세 개의 VPC 모두에서 개인 EC2 인스턴스에 대한 SSH 액세스를 위한 VPC A의 공용 서브넷의 점프 호스트
4. Transit Gateway 를 통해 트래픽을 라우팅하기 위한 VPC 및 서브넷에 대한 적절한 경로 테이블(Route Table) 구성
</br>

## 전제 조건:
이 실습을 시작하기 전에 다음과 같이 준비가 되어 있는지 확인하십시오.

- VPC, 서브넷, EC2 인스턴스 및 AWS Transit Gateway 를 생성하는 데 필요한 권한이 있는 AWS 계정 액세스.
- 경로 테이블, 서브넷 및 EC2 인스턴스와 같은 기본 VPC 개념에 익숙합니다.
![ 이미지](https://github.com/user-attachments/assets/587c1733-088a-4a8b-9759-eb3f11ebe610)
</br>

## 단계별 실습 지침:
### 1단계: VPC 및 서브넷 생성

- **VPC A 만들기**: CIDR 블록 `10.0.0.0/16`을 사용하세요.
- 두 개의 서브넷을 만듭니다: 하나는 **Public Subnet**(점프 호스트용)과 하나는 **Private Subnet**입니다.

- **VPC B 만들기**: CIDR 블록 `10.1.0.0/16`을 사용하세요.
- 이 VPC에서 **Private Subnet**을 만듭니다.

- **VPC C 만들기**: CIDR 블록 `10.2.0.0/16`을 사용하세요.
- 이 VPC에서 **Private Subnet**을 만듭니다.
</br>

### 2단계: EC2 인스턴스 실행
- **VPC A**에서:
- **Public Subnet**(Ubuntu 또는 Amazon Linux)에서 점프 호스트를 실행합니다.
- **Private Subnet**에서 EC2 인스턴스를 실행합니다.
- **VPC B와 C**에서:
- 각 VPC의 **Private Subnet**에서 EC2 인스턴스를 실행합니다.
이러한 EC2 인스턴스의 보안 그룹에서 **SSH 및 ICMP(ping)** 트래픽을 활성화해야 합니다.
</br>

### 3단계: AWS Transit Gateway 생성
1. **VPC 콘솔** → **Transit Gateways** → **Transit Gateway**로 이동합니다.
2. 이름을 제공하고(예: `VPC_A_B_C_TGW`), **ASN**을 기본값으로 남겨두고 **DNS 지원**을 활성화한 다음 **만들기**을 클릭하십시오.
Transit Gateway가 생성되는 데 1~2분이 걸릴 수 있습니다.
</br>

### 4단계: Transit Gateway 첨부 파일 생성하기
1. Transit Gateway를 생성한 후 **첨부 파일 만들기**를 클릭합니다.
2. 각 VPC(A, B, C)에 대해:
- 적절한 **Transit Gateway**를 선택하세요.
- **VPC 첨부**를 선택합니다.
- VPC(예: VPC A)를 선택하고 EC2 인스턴스가 있는 **개인 서브넷**을 선택합니다.
- 첨부 파일을 만듭니다.
VPC B 및 VPC C에 대한 프로세스를 반복합니다.
</br>

### 5단계: VPC 경로 테이블 구성
1. 각 VPC의 **Private Subnet** 경로 테이블에 대해:
- VPC 콘솔의 **Route Table** 섹션으로 이동합니다.
- Private Subnet의 Route Table을 선택하고 **경로 편집**을 클릭하십시오.
- 교통이 대중 교통 게이트웨이를 통과할 수 있도록 경로를 추가합니다.
- 목적지: `10.0.0.0/8` (세 가지 VPC CIDR 범위를 모두 포함).
- 대상: **Transit Gateway**를 선택합니다.
- 변경 사항을 저장합니다.
VPC B와 C의 **Private Subnet 경로 테이블**에 대해 이 단계를 반복합니다.
</br></br>


### 6단계: 연결 테스트하기
1. VPC A의 Public Subnet에 있는 **점프 호스트**로 SSH.
2. 점프 호스트에서 VPC A의 Private Subnet에 있는 EC2 인스턴스로 SSH합니다.
3. VPC A의 EC2 인스턴스에서 VPC B와 C의 EC2 인스턴스의 개인 IP 주소를 **ping**하여 연결을 확인합니다.
예를 들면:
- `ping <Private_IP_of_VPC_B_EC2>`
- `ping <Private_IP_of_VPC_C_EC2>`
응답을 성공적으로 받으면 Transit Gateway가 올바르게 설정되었으며 VPC는 이제 Transit Gateway를 통해 통신할 수 있습니다.
</br></br>



### 7단계: 정리
- 실습을 완료한 후 더 이상 필요하지 않은 경우 Transit Gateway 첨부 파일, VPC, EC2 인스턴스 및 서브넷을 삭제하여 리소스를 정리하도록 선택할 수 있습니다.
</br></br>



## 결론:
이 실습에서는 여러 VPC를 연결하기 위한 AWS Transit Gateway를 성공적으로 생성했습니다. 우리는 Transit Gateway 첨부 파일을 구성하고, Route Table을 업데이트하고, 다른 VPC에서 EC2 인스턴스 간의 통신을 테스트하는 과정을 살펴보았습니다. 이 설정은 AWS 네트워크 설계를 단순화하기 위해 더 크고 복잡한 아키텍처로 확장될 수 있습니다.
</br>
