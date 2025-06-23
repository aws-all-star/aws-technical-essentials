# 실습: AWS MySQL RDS를 시작하고 Amazon Linux EC2 인스턴스와 연결
이 실습은 AWS MySQL RDS 인스턴스를 시작하고 Amazon Linux EC2 인스턴스에 연결하는 과정을 안내합니다. 이 실습이 끝나면 EC2 인스턴스에서 액세스할 수 있는 MySQL RDS 인스턴스를 갖게 됩니다.

![image](https://github.com/user-attachments/assets/826fba50-42b1-4031-9d9a-227778bf401f)

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



### **Step 2: Launch an Amazon Linux EC2 Instance**

1. **Open EC2 Management Console:**
   - Navigate to the [EC2 Console](https://console.aws.amazon.com/ec2/).

2. **Launch Instance:**
   - Click **Launch Instance**.
   
3. **Choose AMI:**
   - Select **Amazon Linux 2023** AMI (or any preferred Amazon Linux version).

4. **Choose Instance Type:**
   - Select `t2.micro` (free tier eligible).

5. **Configure Instance Details:**
   - Ensure the EC2 instance is launched in the same **VPC** and **subnet** as the RDS instance for connectivity.

6. **Add Storage:**
   - Use the default storage settings or customize as needed.

7. **Configure Security Group:**
   - Create a new security group with the following rules:
     - **Inbound Rule**: Allow **SSH** (Port 22) from your IP (to connect to the instance).
     - **Inbound Rule**: Allow **MySQL/Aurora** (Port 3306) from the security group attached to your EC2 instance.

8. **Key Pair:**
   - Choose an existing key pair or create a new one to access the EC2 instance.

9. **Launch the EC2 Instance:**
   - Click **Launch** and wait for the EC2 instance to be available.


### **3단계: RDS 및 EC2용 보안 그룹 구성**

1. **RDS 보안 그룹:**

- **EC2 콘솔**으로 이동하여 **네트워크 및 보안** 아래의 **보안 그룹**을 클릭하십시오.

- RDS 인스턴스에 연결된 보안 그룹을 찾아 인바운드 규칙을 추가합니다.

- **유형**: MySQL/오로라

- **포트**: 3306

- **출처**: EC2 인스턴스의 보안 그룹(드롭다운에서 선택).

2. **EC2 보안 그룹:**

- EC2 인스턴스에 연결된 보안 그룹에 모든 트래픽을 허용하는 아웃바운드 규칙이 있는지 확인하십시오(기본 설정).

---

### **4단계: EC2 인스턴스에 연결**

1. **EC2 인스턴스에 연결:**

- EC2 콘솔에서 인스턴스를 선택하고 **연결**을 클릭합니다.

- 터미널에서 SSH 클라이언트를 사용하여 인스턴스에 연결하십시오:
     ```bash
     ssh -i /path/to/your-key.pem ec2-user@your-ec2-public-ip
     ```
   - Make sure to replace `/path/to/your-key.pem` with the path to your key file and `your-ec2-public-ip` with the EC2 instance's public IP.

---

### **Step 5: Install MySQL Client on EC2 Instance**

1. **Install MySQL Client:**
   - After connecting to the EC2 instance, run the following commands to install the MySQL client:
     ```bash
     sudo yum update -y
     sudo yum install mysql -y
     ```

2. **Verify MySQL Installation:**
   - Run the following command to ensure the MySQL client is installed:
     ```bash
     mysql --version
     ```

---

### **Step 6: Connect to the MySQL RDS from EC2**

1. **Get RDS Endpoint:**
   - In the RDS console, navigate to your MySQL instance.
   - Copy the **Endpoint** from the **Connectivity & security** tab (e.g., `my-rds-mysql.xxxxxxx.us-east-1.rds.amazonaws.com`).

2. **Connect to RDS MySQL:**
   - From the EC2 instance terminal, use the following command to connect to the RDS instance:
     ```bash
     mysql -h your-rds-endpoint -u admin -p
     ```
   - Replace `your-rds-endpoint` with the actual endpoint of your RDS instance, and `admin` with your master username.
   - Enter the password when prompted.

3. **Test the Connection:**
   - Once logged in, you can run the following command to show the databases:
     ```sql
     SHOW DATABASES;
     ```
   - You should see the default databases like `information_schema`, `mysql`, etc.

---

### **Step 7: Create a Sample Database and Table**

1. **Create a New Database:**
   ```sql
   CREATE DATABASE mydb;
   ```

2. **Switch to the New Database:**
   ```sql
   USE mydb;
   ```

3. **Create a Table:**
   ```sql
   CREATE TABLE employees (
       id INT AUTO_INCREMENT PRIMARY KEY,
       name VARCHAR(100),
       position VARCHAR(100),
       salary DECIMAL(10,2)
   );
   ```

4. **Insert Data:**
   ```sql
   INSERT INTO employees (name, position, salary) VALUES 
   ('John Doe', 'Developer', 75000),
   ('Jane Smith', 'Manager', 85000);
   ```

5. **Verify Data Insertion:**
   ```sql
   SELECT * FROM employees;
   ```

---

### **Step 8: Cleanup (Optional)**

1. **Delete RDS Instance:**
   - Go to the **RDS Console**, select your database, and click **Actions** -> **Delete**.
   - You can choose whether or not to create a final snapshot before deletion.

2. **Terminate EC2 Instance:**
   - In the **EC2 Console**, select your instance, click **Actions**, and choose **Terminate**.

---

### **Conclusion**

In this lab, you successfully launched an Amazon RDS MySQL instance, created an Amazon Linux EC2 instance, and connected them. You learned how to set up security groups for secure access and performed database operations through the EC2 instance.


Refrence: https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.MySQL.html
