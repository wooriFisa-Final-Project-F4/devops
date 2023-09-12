# devops

이 저장소는 배포 관련 파일을 포함하고 있습니다. 여기에는 docker-compose.yml 파일들과 EC2 인스턴스 생성 시 도커 설치 명령어 등이 저장되어 있습니다.

## Infra Architecture

<img width="1767" alt="Architect (2)" src="https://github.com/wooriFisa-Final-Project-F4/.github/assets/109801772/31fe93f0-4a6f-452f-b823-2a9e351fcaf6">

## Overview

### Deploy MicroServices

마이크로 서비스들은 서비스별로 EC2인스턴스(m5.large)에 도커를 설치한후, 컨테이너로 배포됩니다. 각 컨테이너는 Eureka Server에 등록된 다른 서비스들과 연결되기 위해, 컨테이너 ID가 아닌 Host IP를 사용하여 자신의 위치를 알립니다.

### Deploy Middleware(Redis, Confluent Kafka)

Redis와 Confluent Kafka는 각각의 인스턴스에 도커 위 컨테이너로 배포됩니다.

### CI/CD (with.Deploy Jenkins)

마이크로 서비스들의 CI/CD 작업을 위해 또 하나의 EC2인스턴스에 Jenkins를 배포했으며, Jenkins는 각 서비스별로 동작하여 CI/CD를 진행합니다. 이러한 진행상황은 슬랙을 통해 전달받습니다.

### AWS(Amazon Web Service)

#### ALB(Application Load Balancer)

Spring Cloud Project엔 [Gateway 서버](https://github.com/wooriFisa-Final-Project-F4/api-gateway)가 있고, 이 서버에 요청을 보내기 위해, ALB(ELB)를 사용하여 사용자의 요청을 받습니다. Route53에서 도메인을 구매하여 SSL/TLS 인증서를 발급받았습니다. 이 인증서를 이용하여 ALB에 등록하고, HTTPS의 요청을 허용할 수 있게 되었습니다.

#### AutoScaling Group

서버의 안정성을 위해, 과부하가 예상이 되는 서비스가 있는 EC2 인스턴스의 AMI(Amazon Machine Image)를 생성하고, AutoScaling설정을 이용해 대상그룹으로 지정하였습니다.

#### RDS (Amazon Relational Database Service)

데이터베이스로는 RDS를 사용했습니다. 버전관리, 자동복구, 백업 등을 지원하는 서비스이기 때문입니다. 또한, RDS 인스턴스의 크기 변경이나. 단순한 조작으로 확장성을 보장할 수 있다는 점을 인지하고, RDS for MySQL을 사용했습니다. 그리고, 마이크로 서비스들과의 서브넷을 구분지어 서비스들은 퍼블릭 서브넷, RDS는 프라이빗 서브넷에 배포함으로서 보안성을 높이려 했습니다.
