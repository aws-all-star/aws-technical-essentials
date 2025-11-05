# 실습. AWS 관리 콘솔에서 AWS EC2 인스턴스 시작 :
</br>

1. **Step 1: 키 쌍 만들기**:
   To securely connect to an EC2 instance, you need a key pair.
   
   ```bash
   aws ec2 create-key-pair --key-name MyKeyPair --query 'KeyMaterial' --output text > MyKeyPair.pem
   ```

   **Note**: `.pem` 파일은 개인 키이며, 안전하게 유지되어야 합니다.

<br/>

2. **EC2 인스턴스 실행**:
  Amazon Linux 2 AMI와 방금 생성한 키 쌍을 사용하여 EC2 인스턴스를 실행합니다.
   
   ```bash
   aws ec2 run-instances \
   --image-id ami-0cf1ead55e8259a57 \
   --instance-type t2.micro \
   --key-name MyKeyPair \
   --security-group-ids <sg-016083c1c7587afb6> \
   --subnet-id <subnet-0dce4429e653b1c32> \
   --count 1 \
   --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=MyFirstEC2Instance}]'
   ```

<br/>

   - **image-id**: 실행하려는 운영 체제의 AMI ID입니다.
   - **instance-type**: 인스턴스 유형 (e.g., `t2.micro` for the free tier).
   - **key-name**: 생성한 키 쌍의 이름입니다.
   - **security-group-ids**: SSH/HTTP 트래픽을 허용하는 보안 그룹입니다.
   - **subnet-id**: 인스턴스가 실행될 서브넷입니다.
   - **count**: 시작할 인스턴스 수입니다.
   - **tag-specifications**: 인스턴스에 적용된 태그.

<br/>

4. **EC2 인스턴스 확인**:
   실행 중인 모든 EC2 인스턴스 나열:
   
   ```bash
   aws ec2 describe-instances \
   --query "Reservations[*].Instances[*].{PublicIP:PublicIpAddress,Type:InstanceType,Name:Tags[?Key=='Name']|[0].Value,Status:State.Name}"  \
   --filters "Name=instance-state-name,Values=running" "Name=tag:Name,Values='*'"  \
   --output table
   ```

<br/>


이제 AWS EC2 인스턴스가 시작되어 사용할 준비가 되었습니다.!
