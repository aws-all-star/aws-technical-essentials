# 실습 : Amazon Linux EC2 인스턴스에 Apache Web서버 구성

### **1단계: Amazon Linux EC2 인스턴스 실행**

1. **AWS 관리 콘솔**에 로그인하고 **EC2 대시보드**로 이동합니다.
2. **Amazon Linux AMI를 사용하여 EC2 인스턴스를 실행**합니다. 앞서 언급한 단계에 따라 인스턴스를 설정합니다.
3. 인스턴스가 가동되고 실행되면, SSH를 통해 **인스턴스에 연결**하세요.

연결을 위한 예시 명령:
```bash
ssh -i /path/to/your-key.pem ec2-user@your-instance-public-dns
```

### **2단계: 패키지 저장소 업데이트**

아파치를 설치하기 전에, 사용 가능한 모든 패키지가 최신 상태인지 확인하기 위해 패키지 저장소를 업데이트하는 것이 좋습니다.

1. 다음 명령을 실행합니다.
```bash
sudo yum update -y
```

### **3단계: 아파치 설치(httpd)**

1. 다음 명령을 실행하여 아파치를 설치하세요:
```bash
sudo yum install httpd -y
```

### **4단계: 아파치 웹 서버 시작**

1. 아파치 서비스 시작:
```bash
sudo systemctl start httpd
```

2. 부팅 시 시작하도록 아파치를 활성화하세요:
```bash
sudo systemctl enable httpd
```

### **5단계: 보안 그룹 구성**

EC2 인스턴스와 연결된 보안 그룹이 **HTTP** 트래픽을 허용하는지 확인하십시오.

1. **EC2 대시보드**로 이동하여 인스턴스를 선택하고 **보안 그룹**을 클릭하십시오.
2. 포트 **80**에서 **HTTP** 트래픽을 허용하도록 인바운드 규칙을 편집하세요:
   - **Type**: HTTP
   - **Port**: 80
   - **Source**: 0.0.0.0/0 (글로벌 액세스의 경우) 또는 IP 범위를 지정합니다.

### **6단계: 아파치 설치 테스트**

1. 웹 브라우저를 열고 EC2 인스턴스의 공용 IP 주소 또는 공용 DNS로 이동합니다.
```http
http://your-ec2-public-ip
```

2. 기본 아파치 테스트 페이지가 표시되어야 하며, 이는 아파치가 올바르게 설치되고 실행되고 있음을 확인합니다.

### **7단계: 선택적으로 간단한 웹 페이지 구성**

1. 기본 Apache 웹 페이지를 사용자 지정 콘텐츠로 바꿉니다.

- 아파치의 기본 문서 루트는 `/var/www/html/`이다.
- 여기에서 새로운 HTML 파일을 업로드하거나 만들 수 있습니다.

2. 예를 들어, 간단한 index.html 페이지를 만듭니다.
```bash
echo "<html><h1>Apache Web Server on Amazon Linux</h1></html>" | sudo tee /var/www/html/index.html
```

3. 업데이트된 콘텐츠를 보려면 브라우저에서 페이지를 다시 로드하세요.

### **8단계: 아파치 서비스 상태 확인**

1. 아파치 서비스의 상태를 확인하려면 다음을 실행하십시오.
```bash
sudo systemctl status httpd
```

### Troubleshooting:

경고는 **Amazon Linux**의 최신 버전을 사용할 수 있으며, 인스턴스를 최신 버전으로 업그레이드할 수 있는 옵션이 있음을 나타냅니다.

### Amazon Linux를 최신 버전으로 업그레이드하는 단계:

1. **SSH를 통해 EC2 인스턴스에 연결**.

2. **최신 버전으로 업그레이드하려면 경고 메시지에 제공된 업그레이드 명령**을 실행하세요:
```bash
sudo dnf update
```

3. **업그레이드 후 EC2 인스턴스를 재부팅**하세요:
```bash
sudo reboot
```

4. **재부팅 후 Amazon Linux의 버전을 확인하여 업그레이드**를 확인하십시오:
```bash
cat /etc/os-release
```


이 시스템은 이제 Amazon Linux 2023의 최신 버전을 실행해야 합니다.
