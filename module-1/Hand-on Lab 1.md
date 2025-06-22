# **실습 : 다른 운영 체제에 AWS CLI를 설치하는 방법 익히기**
AWS 명령줄 인터페이스(CLI)를 사용하면 명령줄에서 AWS 서비스와 상호 작용할 수 있습니다. Linux, macOS 및 Windows와 같은 다양한 운영 체제에 AWS CLI를 설치하고 구성할 수 있습니다.
</br>

## **1. 다른 운영 체제에 AWS CLI 설치**
### **1.1 리눅스에 AWS CLI 설치하기**

1. **AWS CLI 설치 프로그램 다운로드**:
   
   다음 명령을 사용하여 AWS CLI 버전 2 설치 파일을 다운로드하십시오.
   ```bash
   curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
   ```

2. **설치 패키지 압축을 풉니다**:
   ```bash
   unzip awscliv2.zip
   ```

3. **설치 프로그램 실행**:
   ```bash
   sudo ./aws/install
   ```

4. **설치 확인**:
   AWS CLI가 올바르게 설치되었는지 확인하려면:
   ```bash
   aws --version
   ```

---

#### **1.2 macOS에 AWS CLI 설치하기**

1. **AWS CLI 설치 프로그램 다운로드**:
   
  다음 명령을 사용하여 macOS용 AWS CLI를 다운로드하십시오.
   ```bash
   curl "https://awscli.amazonaws.com/AWSCLIV2.pkg" -o "AWSCLIV2.pkg"
   ```

2. **설치 프로그램 실행**:
   Use the built-in macOS installer:
   ```bash
   sudo installer -pkg ./AWSCLIV2.pkg -target /
   ```

3. **설치 확인**:
   ```bash
   aws --version
   ```

---

#### **1.3 Windows에서 AWS CLI 설치**

1. **설치 프로그램 다운로드**:
   Windows용 AWS CLI MSI 설치 프로그램을 다운로드하십시오. [here](https://awscli.amazonaws.com/AWSCLIV2.msi).

2. **설치 프로그램 실행**:
   다운로드한 `.msi` 파일을 실행하고 화면의 지침을 따르십시오.

3. **설치 확인**:
   **명령 프롬프트** 또는 **PowerShell**을 열고 다음을 실행합니다.
   ```bash
   aws --version
   ```

---
</br></br>


## **2. AWS CLI를 구성하는 방법**

AWS CLI를 설치한 후 **AWS Credentials** 및 **Region**으로 구성해야 합니다.

1. **터미널 또는 명령 프롬프트**를 열고 다음 명령을 실행하세요:
   ```bash
   aws configure
   ```

2. **다음 세부 사항을 입력** 메시지가 표시되면:

   - **AWS Access Key ID**: Enter your access key.
   - **AWS Secret Access Key**: Enter your secret access key.
   - **Default Region Name**: Enter your region (e.g., `us-west-2` or `ap-south-2`).
   - **Default Output Format**: Choose the output format (`json`, `text`, or `table`).

   Example:
   ```bash
   AWS Access Key ID [None]: AKIAIOSFODNN7EXAMPLE
   AWS Secret Access Key [None]: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
   Default region name [None]: us-west-2
   Default output format [None]: json
   ```

**참고**: **IAM** 아래의 AWS 관리 콘솔에서 액세스 키를 생성할 수 있습니다.
