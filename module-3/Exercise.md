# **실습 1. Networing in AWS**
**(1) Virtual Private Cloud(VPC): 나의 전용 가상 데이터센터**
<br/>
VPC는 AWS 네트워킹의 중심이 되는 서비스로, 사용자 전용의 가상 네트워크 공간을 제공합니다.쉽게 말해, AWS 상에서 자신만의 네트워크를 구축할 수 있는 “가상의 데이터센터”입니다. VPC 안에서는 IP 주소 범위를 직접 정의하고, 그 범위 안에서 여러 개의 Subnet(서브넷)을 생성해 서버(EC2), 데이터베이스(RDS), 컨테이너(EKS) 등을 논리적으로 구분할 수 있습니다.
VPC(Virtual Private Cloud) 는 AWS 네트워킹의 출발점입니다. CIDR로 IP 대역(예: 10.0.0.0/16)을 잡고, 그 범위를 Subnet으로 쪼개 워크로드를 배치합니다.
일반적으로 Public Subnet(인터넷과 직접 오갈 인스턴스)과 Private Subnet(외부 공개가 불필요한 시스템)으로 나눕니다.
서브넷은 반드시 하나의 AZ에 속합니다. 고가용성이 필요하면 동일한 역할의 서브넷/인스턴스를 2개 이상의 AZ에 분산하십시오.
<br/>

예를 들어,
Public Subnet: 인터넷과 직접 통신해야 하는 웹서버를 배치
Private Subnet: 내부 전용 DB서버나 백엔드 시스템을 배치
작게 시작할 때의 예:
- 10.0.0.0/16 VPC
- AZ-a: Public 10.0.1.0/24, Private 10.0.11.0/24
- AZ-b: Public 10.0.2.0/24, Private 10.0.12.0/24

여기서 “/24”는 256개 IP(실사용 약 251개)를 의미합니다. 너무 촘촘하게 잡으면 나중에 확장이 어렵고, 너무 넓게 잡으면 관리가 난해합니다. 초기에 성장 여지를 고려해 설계하는 습관이 중요합니다.
이처럼 VPC는 네트워크 구성을 안전하게 분리하고 세밀하게 제어할 수 있게 해줍니다.

<br/><br/>

예시) VPC 생성
```sh
$ aws ec2 create-vpc \
   --cidr-block 10.10.0.0/16 \
   --tag-specifications "ResourceType=vpc,Tags=[{Key=Name,Value=MyVPC}]"
```
<br/>

출력 예시

```sh
{
    "Vpc": {
        "OwnerId": "533267279970",
        "InstanceTenancy": "default",
        "Ipv6CidrBlockAssociationSet": [],
        "CidrBlockAssociationSet": [
            {
                "AssociationId": "vpc-cidr-assoc-07055e7058763ef47",
                "CidrBlock": "10.10.0.0/16",
                "CidrBlockState": {
                    "State": "associated"
                }
            }
```
<br/>

**(2) 서브넷(Subnet)과 라우팅 테이블(Routing Table): 패킷의 길 안내서**
<br/>
AWS Routing Table(라우팅 테이블)은 AWS 네트워크에서 패킷이 이동할 경로를 결정하는 지도와 같은 역할을 합니다.
즉, “이 트래픽은 어디로 보내야 하는가?” 를 정의하는 규칙 집합입니다.

AWS에서 EC2 인스턴스나 서비스가 데이터를 전송하면, 그 트래픽은 항상 서브넷(subnet) 을 통해 나갑니다.
각 서브넷은 반드시 하나의 라우팅 테이블(Route Table) 과 연결되어 있습니다.
이 테이블에 적힌 규칙(Route)이 패킷이 어떤 게이트웨이 또는 대상(IGW, NAT, TGW 등)으로 향할지를 결정합니다.
서브넷을 만들었다면, 이제 Route Table(라우팅 테이블) 로 트래픽의 나갈 길을 정합니다.
- Public Subnet의 라우트: 0.0.0.0/0 → Internet Gateway(IGW)
- Private Subnet의 라우트: 0.0.0.0/0 → NAT Gateway (아웃바운드 전용 인터넷 접근)
- 서비스 간 내부 통신: 대상 Prefix가 같은 VPC CIDR이면 로컬 라우트가 자동 존재
<br/>

예시) 퍼블릭 서브넷 생성

```sh
$ aws ec2 create-subnet \
   --vpc-id vpc-<VpcId> \
   --cidr-block 10.10.1.0/24 \
   --availability-zone ap-northeast-2a \
   --tag-specifications "ResourceType=subnet,Tags=[{Key=Name,Value=MyPublicSubnet}]"
```
<br/>

출력 예시

```sh
{
    "Vpc": {
        "VpcId": "vpc-0a1b2c3d4e5f6g7h",
        "State": "pending",
        "CidrBlock": "10.10.0.0/16",
        "IsDefault": false
    }
}
```
<br/>

💡 중요 포인트<br/>
서브넷은 라우트 테이블을 ‘연결(associate)’ 해야 유효합니다.
한 라우트 테이블을 여러 서브넷이 공유할 수 있지만, 역할(퍼블릭/프라이빗)에 따라 나누면 관리가 쉬워집니다.
- 퍼블릭 서브넷: 0.0.0.0/0 → Internet Gateway(IGW)
- 프라이빗 서브넷(IPv4): 0.0.0.0/0 → NAT Gateway (아웃바운드 전용)
- VPC 내부 통신: 같은 VPC CIDR은 로컬 라우트가 자동 존재
- IPv6 사용 시: 외부로 나갈 때 Egress-only Internet Gateway 를 고려
<br/>

🔑 모범 사례: NAT Gateway는 각 AZ에 1개씩 두고, 해당 AZ의 프라이빗 서브넷은 같은 AZ의 NAT 로 라우팅합니다(장애 격리, 데이터 경로 최적화).

<br/><br/>


**(3) 보안의 두 축: Security Group과 NACL**
<br/>
AWS 네트워크 보안의 기본은 보안 그룹(SG) 과 네트워크 ACL(NACL) 입니다.
Security Group(SG) 은 ENI/인스턴스 단위 가상 방화벽, 상태기반(Stateful). 인바운드 443을 허용하면 응답 아웃바운드는 자동 허용됩니다. “누가(소스) 어떤 포트로 들어올 수 있나/나갈 수 있나”를 정의합니다. 
<br/>
SG끼리 참조(출처를 다른 SG로 지정) 하면 동적으로 스케일되는 환경에서 관리가 매우 편합니다.
Network ACL(NACL) 은 서브넷 단위, 무상태(Stateless). 인바운드·아웃바운드를 각각 허용/거부 룰로 정의합니다. 세밀한 거부(Deny) 룰이나 기본 차단선으로 사용합니다.
<br/><br/>

예시) 보안 그룹 생성
```sh
$ aws ec2 create-security-group \
   --group-name MyWebServerSG \
   --description "Security Group for Web Server" \
   --vpc-id vpc-0cbbce3dab3abdb53
```

<br/>
출력 예시
<br/>

```sh
{
    "GroupId": "sg-016083c1c7587afb6",
    "SecurityGroupArn": "arn:aws:ec2:ap-northeast-2:533267279970:security-group/sg-016083c1c7587afb6"
}
```
<br/>

예시) 인터넷 출입문: IGW vs NAT Gateway
<br/>
Internet Gateway은 외부에서 들어오고(인바운드), 내부에서 나갈(아웃바운드) 수 있는 ‘정문’입니다. 퍼블릭 서브넷에 놓인 EC2가 퍼블릭 IP/Elastic IP 를 가지고 있고, 라우트가 IGW를 향하면 인터넷과 직접 통신합니다.
NAT Gateway은 Private Subnet에 있는 인스턴스가 밖으로 나가기만 하게 해 줍니다(예: OS 업데이트, 패키지 다운로드). 외부에서 직접 들어올 수는 없습니다. 고가용성을 위해 AZ마다 배치하고, 해당 AZ의 프라이빗 서브넷은 같은 AZ의 NAT GW 를 사용하도록 라우팅하는 것이 모범 사례입니다.

<br/>
예시) Internet Gateway 생성

```sh
$ aws ec2 create-internet-gateway \
  --tag-specifications 'ResourceType=internet-gateway,Tags=[{Key=Name,Value=MyInternetGateway}]'
```

<br/>
출력 예시
<br/>

```sh
{
    "InternetGateway": {
        "InternetGatewayId": "igw-0a1b2c3d4e5f6g7h",
        "Attachments": []
    }
}
```
<br/>

예시) 경로 테이블(route table)을 생성
```sh
aws ec2 create-route-table --vpc-id <vpc-03ac1f56988402f4c> --output text --query 'RouteTable.RouteTableId' --tag-specifications 'ResourceType=route-table,Tags=[{Key=Name,Value=MyRouteTable}]'
```
<br/>

출력 예시
<br/>

```sh
rtb-0ab4a61ef63cd5556
```
<br/>

예시) Public subnet 에서 Public IP 자동 할당
```sh
aws ec2 attach-internet-gateway --internet-gateway-id <igw-04f06e17e9bbd041d> --vpc-id <vpc-03ac1f56988402f4c>

```
<br/>

예시) 인터넷 게이트웨이에 추가

```sh
aws ec2 create-route --route-table-id rtb-0ab4a61ef63cd5556 --destination-cidr-block 0.0.0.0/0 --gateway-id igw-04f06e17e9bbd041d
```
<br/>

예시) Public subnet을 Public 경로 테이블(route table)과 연결
```sh
aws ec2 associate-route-table --subnet-id subnet-0dce4429e653b1c32 --route-table-id rtb-0ab4a61ef63cd5556
```
<br/>

출력 예시
<br/>
```sh
{
    "AssociationId": "rtbassoc-04518f2934655113a",
    "AssociationState": {
        "State": "associated"
    }
```
<br/>

예시) 외부에서 접속되도록 22번 포트 오픈
```sh
aws ec2 authorize-security-group-ingress --group-id $SG_ID --protocol tcp --port 22 --cidr 0.0.0.0/0
```
<br/>

```sh
{
    "Return": true,
    "SecurityGroupRules": [
        {
            "SecurityGroupRuleId": "sgr-0eb655ef934a820ed",
            "GroupId": "sg-016083c1c7587afb6",
            "GroupOwnerId": "533267279970",
            "IsEgress": false,
            "IpProtocol": "tcp",
            "FromPort": 22,
            "ToPort": 22,
            "CidrIpv4": "0.0.0.0/0",
            "SecurityGroupRuleArn": "arn:aws:ec2:ap-northeast-2:533267279970:security-group-rule/sgr-0eb655ef934a820ed"
        }
    ]
}
```

<br/>

```sh
aws ec2 authorize-security-group-ingress --group-id $SG_ID --protocol icmp --port -1 --source-group $SG_ID
```
<br/>


🎯 비용·가용성 관점
- NAT GW는 시간·트래픽 요금이 있으므로, S3/DynamoDB 등은 VPC Endpoint(아래 참조) 로 우회해 NAT 비용을 줄일 수 있습니다.
- IGW는 비용이 없습니다(데이터 전송료는 별도)

<br/><br/>

**(5) 사설로 AWS 서비스에 접속: VPC Endpoint & PrivateLink**
<br/>
VPC Endpoint는 AWS 내부 트래픽을 인터넷을 거치지 않고 AWS 네트워크 내부에서 바로 통신하게 해주는 기능입니다. 즉, Private Subnet의 리소스가 인터넷 게이트웨이(IGW)나 NAT 게이트웨이 없이도 AWS 서비스(S3, DynamoDB 등)에 접근할 수 있도록 해주는 “사설 연결 통로”입니다. Gateway형 VPC Endpoint 은 S3, DynamoDB 전용. 라우트 테이블에 대상 프리픽스를 추가해 트래픽을 엔드포인트로 보냅니다. NAT 비용 절감에 효과적입니다
<img width="1205" height="528" alt="virtual-private-gateway" src="https://github.com/user-attachments/assets/e918da4c-e785-41ea-8db8-811c5e52b98d" />

Interface형 VPC Endpoint(= PrivateLink)은 ENI 형태 엔드포인트를 서브넷에 생성. SSM, ECR, CloudWatch 등 다양한 서비스와, 타 계정 서비스(엔드포인트 서비스) 에 사설로 접속할 때 사용할 수 있습니다.

즉, 인터넷 회선 경유 없음, NAT 회피, 데이터 경로 단축할수 있어 보안·비용 이점이 있습니다.
<br/><br/>


**(6) VPC 간 및 하이브리드 연결**
<br/>
AWS 네트워킹의 핵심은 **“모든 네트워크를 안전하고 효율적으로 연결하는 것”**입니다. 하나의 AWS 계정·VPC만으로는 단일 서비스 수준에 그치지만, 기업 환경에서는 일반적으로 여러 개의 VPC(운영·개발·보안·로그 등) 와 온프레미스(사내 IDC, 본사망) 를 함께 사용합니다.
<br/>
<img width="1204" height="529" alt="aws-direct-connect" src="https://github.com/user-attachments/assets/ff4d05e7-9459-4192-af3d-ced6d7dc50d2" />

이때 필요한 것이 바로 VPC 간 연결 (VPC Peering / Transit Gateway) 과 하이브리드 연결 (VPN / Direct Connect) 입니다.
- VPC Peering: 두 VPC를 1:1로 직접 연결. 간단하지만 트랜짓(중계) 라우팅 불가라서 연결 수가 많아지면 관리가 복잡해집니다.
- Transit Gateway(TGW): 허브-앤-스포크 구조로 여러 VPC/온프레미스를 중앙집중 연결. 대규모 멀티계정·멀티VPC 환경의 표준.
- Site-to-Site VPN: 인터넷 위 암호화 터널. 구축이 빠르지만 인터넷 품질에 따라 지연/가용성 변화.
- AWS Direct Connect: 전용선으로 AWS와 데이터센터를 직결. 낮은 지연·안정성이 필요하거나 대역폭이 큰 워크로드에 적합.
<br/><br/>

참고문헌 :
- https://aws.amazon.com/ko/vpc/faqs/#topic-0
- https://intellipaat.com/blog/tutorial/amazon-web-services-aws-tutorial/networking/
- https://www.techtarget.com/searchcloudcomputing/feature/Boost-cloud-connectivity-with-these-Amazon-networking-services
- https://www.knowledgehut.com/tutorials/cloud-computing/aws/aws-nacl
- https://k21academy.com/amazon-web-services/networking-fundamental/
