# 실습: MySQL DB 인스턴스 생성 및 해당 인스턴스에 연결
이 실습은 AWS MySQL RDS 인스턴스를 시작하고 Amazon Linux EC2 인스턴스에 연결하는 과정을 안내합니다. 이 실습이 끝나면 EC2 인스턴스에서 액세스할 수 있는 MySQL RDS 인스턴스를 갖게 됩니다.

![image](https://github.com/user-attachments/assets/a9833175-5dae-4aa7-b935-ad69b556d073)
</br>


## **1단계: MySQL RDS 인스턴스 실행**

1. **AWS 관리 콘솔에 로그인:**
   - [AWS 콘솔](https://aws.amazon.com/console/)로 이동하여 **RDS Console**을 엽니다.

2. **새 RDS 인스턴스 생성:**
   - **RDS Console**에서 왼쪽 메뉴에서 **Databases**를 클릭합니다.
   - **Create database**를 클릭하세요.

3. **데이터베이스 생성 방법 선택:**
   - **Database creation method**에서 **Standard Create**을 선택합니다.

4. **데이터베이스 엔진 선택:**
   - **Engine options**에서 **MySQL**을 선택하세요.

5. **데이터베이스 설정:**
   - **Version** 선택: 기본 버전을 사용하거나 선호하는 버전을 선택하십시오(예: MySQL 8.x)
   - **Templates**: 추가 요금을 피하기 위해 해당하는 경우 **Free Tier**을 선택하십시오.
   - **DB Instance Identifier**: 데이터베이스 이름을 지정합니다(예: `my-rds-mysql`)
   - **Master Username**: 사용자 이름을 입력합니다(예: `admin`)
   - **Master Password**: 보안 비밀번호를 입력하세요

6. **인스턴스 사양:**
   - **DB Instance Class**: 인스턴스 유형을 선택하십시오(예: 무료 계층의 경우 `db.t3.micro`).
   - **Allocated Storage**: 기본 저장 공간을 설정합니다(예: 20GB).

7. **저장 설정:**
   - **Auto Scaling**을 활성화하지 않는 한 기본 설정을 그대로 두십시오.

8. **연결성:**
   - **VPC**: 기본 VPC를 선택하세요.
   - **Subnet Group**: 기본 서브넷을 선택합니다.
   - ****공개 액세스 가능****: 외부 액세스를 허용하려면 **YES**로 설정하십시오(이 랩의 경우).
   - ****VPC 보안 그룹****: 기본 보안 그룹을 사용하거나 새 보안 그룹을 만들 수 있습니다.

9. **데이터베이스 인증 및 암호화:**
   - **Database Authentication**(암호 인증)에 대한 기본 설정을 유지하십시오.
   - 이 실습에서는 암호화를 비활성화한 상태로 유지하십시오.

10. **백업 및 모니터링:**
   - **Backup Retention Period**을 설정합니다(예: 7일).
   - 원하는 대로 모니터링을 활성화하거나 비활성화하세요.

11. **데이터베이스 생성:**
   - 모든 설정을 검토하고 **Create Database**를 클릭하세요.

</br></br></br>



## **2단계: Amazon Linux EC2 인스턴스 실행**

1.**EC2 관리 콘솔 열기:**
   - [EC2 콘솔](https://console.aws.amazon.com/ec2/)로 이동합니다.

2. **Launch Instance:**
   - **Launch Instance**를 클릭합니다.

3. **AMI 선택:**
   - **Amazon Linux 2023** AMI(또는 선호하는 Amazon Linux  버전)를 선택하세요.

4. **인스턴스 유형 선택:**
   - `t2.micro`를 선택하세요 (무료 등급 적용 가능)

5. **인스턴스 세부 사항 구성:**
   - EC2 인스턴스가 연결을 위한 RDS 인스턴스와 동일한 **VPC** 및 **Subney**에서 실행되었는지 확인합니다.

6. **저장 공간 추가:**
   - 기본 저장 공간 설정을 사용하거나 필요에 따라 사용자 지정하십시오.

7. **보안 그룹 구성:**
   - 다음 규칙으로 새 보안 그룹을 만듭니다.
      - **Inbound Rule**: 귀하의 IP에서 **SSH**(포트 22)를 허용합니다(인스턴스에 연결하기 위해).
      - **Inbound Rule**: EC2 인스턴스에 연결된 보안 그룹에서 **MySQL/Aurora** (포트 3306)를 허용합니다.

8. **Key Pair:**
   - 기존 키 쌍을 선택하거나 새 키 쌍을 생성하여 EC2 인스턴스에 액세스합니다.

9. **Launch the EC2 Instance:**
   - **Launch**를 클릭하고 EC2 인스턴스를 사용할 수 있을 때까지 기다립니다.
   - 
</br></br></br>


## **3단계: RDS 및 EC2용 보안 그룹 구성**

1. **RDS 보안 그룹:**
   - **EC2 콘솔**으로 이동하여 **네트워크 및 보안** 아래의 **보안 그룹**을 클릭하십시오.
   - RDS 인스턴스에 연결된 보안 그룹을 찾아 인바운드 규칙을 추가합니다.
      - **Type**: MySQL/Aurora
      - **Port**: 3306
      - **Source**: EC2 인스턴스의 보안 그룹(드롭다운에서 선택).

2. **EC2 보안 그룹:**
   - EC2 인스턴스에 연결된 보안 그룹에 모든 트래픽을 허용하는 아웃바운드 규칙이 있는지 확인하십시오(기본 설정).

</br></br>


## **4단계: EC2 인스턴스에 연결**

1. **EC2 인스턴스에 연결:**

- EC2 콘솔에서 인스턴스를 선택하고 **연결**을 클릭합니다.
- 터미널에서 SSH 클라이언트를 사용하여 인스턴스에 연결하십시오:
     ```bash
     ssh -i /path/to/your-key.pem ec2-user@your-ec2-public-ip
     ```
   - `/path/to/your-key.pem`을 키 파일의 경로로 바꾸고 `your-ec2-public-ip`을 EC2 인스턴스의 공용 IP로 대체해야 합니다.

</br></br>


## **5단계: EC2 인스턴스에 MySQL 클라이언트 설치**

1. **MySQL 클라이언트 설치:**

- EC2 인스턴스에 연결한 후 다음 명령을 실행하여 MySQL 클라이언트를 설치합니다.
     ```bash
     sudo yum update -y
     sudo yum install mysql -y
     ```

2. **MySQL 설치 확인:**

- 다음 명령을 실행하여 MySQL 클라이언트가 설치되어 있는지 확인하십시오.

     ```bash
     mysql --version
     ```

</br></br>


## **6단계: EC2에서 MySQL RDS에 연결**

1. **RDS 엔드포인트 받기:**
   - RDS 콘솔에서 MySQL 인스턴스로 이동합니다.
   - **연결성 및 보안** 탭에서 **엔드포인트**를 복사합니다(예: `my-rds-mysql.xxxxxxx.us-east-1.rds.amazonaws.com`).

2. **RDS MySQL에 연결:**
   - EC2 인스턴스 터미널에서 다음 명령을 사용하여 RDS 인스턴스에 연결합니다.
     ```bash
     mysql -h your-rds-endpoint -u admin -p
     ```

   - `your-rds-endpoint`를 RDS 인스턴스의 실제 엔드포인트로 바꾸고 `admin`을 마스터 사용자 이름으로 바꾸세요.
   - 메시지가 표시되면 비밀번호를 입력하세요.


4. **연결 테스트:**
   - 로그인하면 다음 명령을 실행하여 데이터베이스를 표시할 수 있습니다.
     ```sql
     SHOW DATABASES;
     ```

   - `information_schema`, `mysql` 등과 같은 기본 데이터베이스를 볼 수 있습니다.

</br></br>


### **7단계: 샘플 데이터베이스 및 테이블 만들기**

1. **새 데이터베이스 만들기:**

   ```sql
   CREATE DATABASE mydb;
   ```

2. **새 데이터베이스로 전환:**
   ```sql
   USE mydb;
   ```

3. **테이블 만들기:**
   ```sql
   CREATE TABLE employees (
       id INT AUTO_INCREMENT PRIMARY KEY,
       name VARCHAR(100),
       position VARCHAR(100),
       salary DECIMAL(10,2)
   );
   ```

4. **데이터 삽입:**
   ```sql
   INSERT INTO employees (name, position, salary) VALUES 
   ('John Doe', 'Developer', 75000),
   ('Jane Smith', 'Manager', 85000);
   ```

5. **데이터 삽입 확인:**
   ```sql
   SELECT * FROM employees;
   ```

</br></br>

## **8단계: 정리(선택 사항)**

1. **RDS 인스턴스 삭제:**
   - **RDS 콘솔**으로 이동하여 데이터베이스를 선택하고 **작업** -> **삭제**를 클릭하십시오.
   - 삭제하기 전에 최종 스냅샷을 생성할지 여부를 선택할 수 있습니다.

2. **EC2 인스턴스 종료:**
   - **EC2 콘솔**에서 인스턴스를 선택하고 **작업**을 클릭한 다음 **종료**를 선택합니다.

</br></br>



## **결론**
이 실습에서는 Amazon RDS MySQL 인스턴스를 성공적으로 시작하고 Amazon Linux EC2 인스턴스를 생성하여 연결했습니다. 보안 액세스를 위해 보안 그룹을 설정하고 EC2 인스턴스를 통해 데이터베이스 작업을 수행하는 방법을 배웠습니다.


Refrence: https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.MySQL.html
