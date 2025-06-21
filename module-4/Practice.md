## 실습. AWS 관리 콘솔에서 AWS EC2 인스턴스 시작 :
</br>

### **Step 1: AWS 관리 콘솔에 로그인**
1. AWS 사이트[AWS Management Console](https://aws.amazon.com/console/) 이동하세요.
2. AWS 계정 자격 증명을 사용하여 로그인하세요.
   - 강사에 의해 제공되는 계정을 통해 로그인을 해야만 합니다.
</br>

### **Step 2: EC2 대시보드로 이동**
1. AWS 관리 콘솔에서 페이지 상단의 **"Services"** 드롭다운 메뉴를 찾습니다.
2. **"Compute"** 섹션에서 **"EC2"** 를 클릭하여 EC2 대시보드를 엽니다.
</br>

### **Step 3: 인스턴스 실행**
1. EC2 대시보드에서 **"Launch Instance"** 버튼을 클릭합니다.
2. **"Name and tages"** 공란에 사용자는 `My EC2 Server` 이름을 사용합니다.
</br>

### **Step 4: Amazon Machine Image(AMI) 선택**
1. **Amazon Machine Image (AMI)**, 운영 체제 및 소프트웨어 구성이 포함된 템플릿을 선택합니다.
2. 다음 중에서 선택할 수 있습니다.:
   - **Quick Start**: Amazon Linux, Ubuntu, Windows Server 등과 같이 AWS에서 제공하는 사전 구성된 이미지.
   - **My AMIs**: 생성했거나 공유한 AMI.
   - **AWS Marketplace**: 타사 공급업체에서 제공하는 맞춤형 AMI.
3. 사용자는 **"Quick Start"** 버튼을 클릭하여, `Amazon Linux 2 64-bit (x86)` 원하는 AMI를 선택하십시오.
</br>

### **Step 5: 인스턴스 유형 선택**
1. 원하는 CPU, 메모리 및 네트워킹 용량에 따라 **Instance Type**을 선택하십시오.
2. 보통 인기 있는 옵션으로는 **t2.micro**(무료 등급에 적합), **m5.large** 등이 있습니다. 사용자는 `t2.micro` 선택해야만 합니다.
3. **"Next: Configure Instance Details"** 을 클릭합니다.
</br>

### **Step 6: 인스턴스 세부 사항 구성**
1. 필요에 따라 인스턴스를 구성합니다. 몇 가지 주요 옵션은 다음과 같습니다.:
   - **Number of Instances**: 실행하려는 인스턴스 수를 지정합니다.
   - **Network**: 인스턴스가 위치할 VPC(가상 사설 클라우드)를 선택합니다.
   - **Subnet**: VPC 내에서 서브넷을 선택합니다.
   - **Auto-assign Public IP**: 인스턴스에 공용 IP 주소를 갖도록 하려면 활성화하십시오.
   - **IAM Role**: 다른 AWS 서비스에 액세스하기 위해 필요한 경우 IAM 역할을 첨부합니다.

2. 확실하지 않은 경우 기본 설정을 남겨두고 **"Next: Add Storage""** 를 클릭하십시오.
</br>

### **Step 7: 저장소 추가**
1. 인스턴스에 대한 스토리지를 구성합니다. 기본적으로 AWS는 **EBS(Elastic Block Store)** 볼륨을 할당합니다.
2. 크기, 유형(예: 범용 SSD, 프로비저닝된 IOPS SSD) 및 기타 설정을 조정할 수 있습니다.
3. 필요한 경우 추가 볼륨을 추가한 다음 **"Next: Add Tags"** 를 클릭합니다.
</br>

### **Step 8: 태그 추가*
1. 태그는 리소스를 구성하고 식별하는 데 도움이 됩니다. 각 태그는 키-값 쌍입니다.
2. **"Add Tag"** 를 클릭하고 **Key**(예: "Name")와 **Value**(예: "MyEC2Instance")을 제공하십시오.
3. **"Next: Configure Security Group"** 을 클릭합니다.
</br>

### **Step 9: 보안 그룹 구성**
1. **Security Group**은 인바운드 및 아웃바운드 트래픽을 제어하는 인스턴스의 가상 방화벽 역할을 합니다.
2. 새 보안 그룹을 생성하거나 기존 그룹을 선택합니다.
   - **Rule**: Linux 인스턴스의 경우 SSH(포트 22) 또는 Windows 인스턴스의 경우 RDP(포트 3389) 허용과 같은 규칙을 정의합니다.
   - **Source**: 인스턴스에 액세스할 수 있는 IP 범위를 지정하십시오(예: 0.0.0.0/0은 모든 IP에서 액세스를 허용합니다).

3. **"Review and Launch"** 을 클릭합니다.
</br>

### **Step 10: 인스턴스 실행 검토**
1. 모든 설정과 구성을 검토합니다.
2. 모든 것이 좋아 보인다면, **"Launch"** 를 클릭하세요.
</br>

### **Step 11: 키 쌍 선택 또는 생성**
1. 인스턴스에 안전하게 연결하려면 **키 쌍**(공개 및 개인 키 세트)이 필요합니다.
2. 기존 키 쌍을 선택하거나 새 키 쌍을 만듭니다.:
   - **If creating a new key pair**: 개인 키 파일(.pem)을 다운로드하고 안전하게 보관하십시오. 인스턴스에 SSH를 입력하려면 필요합니다.
3. 선택한 개인 키에 대한 액세스 권한이 있음을 확인한 다음 **"Launch Instances"** 를 클릭하십시오.*.
</br>

### **Step 12: 인스턴스 보기 및 연결**
1. **"인View Instances"** 를 클릭하여 EC2 대시보드에서 인스턴스를 확인하십시오.
2. 인스턴스 **State**가 **"running"** 을 표시하고 **Status Checks** 이 통과될 때까지 기다립니다.
3. 인스턴스를 선택한 다음 **"Connect"** 을 클릭하여 SSH(Linux용) 또는 RDP(Windows용)를 통해 연결 지침을 가져옵니다.
</br>

### **Step 13: 인스턴스에 연결**
1. **For Linux Instances**:
   - **"Connect"** 탭에 제공된 명령을 사용하여 인스턴스에 SSH를 입력하세요. 명령은 일반적으로:
     ```bash
     ssh -i /path/to/your-key.pem ec2-user@your-instance-public-dns
     ```
2. **For Windows Instances**:
   - **RDP** 클라이언트를 사용하여 연결합니다. 제공된 원격 데스크톱 파일을 다운로드하고 개인 키를 사용하여 관리자 암호를 해독하십시오.
</br>

이제 AWS EC2 인스턴스가 시작되어 사용할 준비가 되었습니다.!
