- <https://github.com/team-hlab/nestjs-cicd-sample>

# AWS 세팅
# 권한
## IAM
### 1. ECR Policy 생성
- Authorization Token 획득
- ECR repository push
```json
{
    "Version": "2012-10-12",
    "Statement": [
        {
            "Sid": "Authorization",
            "Effect": "Allow",
            "Action": [
                "ecr:GetAuthorizationToken"
            ],
            "Resource": [
                "*"
            ]
        },
        {
            "Sid": "ECRusage",
            "Effect": "Allow",
            "Action": [
                "ecr:BatchCheckLayerAvailability",
                "ecr:BatchGetImage",
                "ecr:PutImage",
                "ecr:UploadLayerPart",
                "ecr:CompleteLayerUpload",
                "ecr:InitiateLayerUpload",
                "ecr:GetDownloadUrlForLayer"
            ],
            "Resource": [
                "arn:aws:ecr:ap-northeast-2:200073956152:repository/hhplus-dev",
                "arn:aws:ecr:ap-northeast-2:200073956152:repository/hhplus-prod"
            ]
        }
    ]
}
```
### 2. CI/CD 자동화에 사용할 Credentilals (IAM) 생성
- 다음 정책들을 추가
    - 위 만들어둔 Policy를 추가
    - ECS 배포 자동화를 위해 필요한 권한 추가
        - AmazonECS_FullAccess
        - CloudWatchLogFullAccess

### 3. Third-Party Service AccessKey 를 생성
- 해당 IAM으로 GithubActions이 사용할 AccessKey
    - 발급받은 Access key를 Github Repository secrets에 등록

# 이미지 저장소
## ECR
- ECR 생성
    - private
- 버저닝 ECR 배포
    - prod
    - dev

# 네트워크 환경
## VPC
- 네트워크 구성
### VPC 생성 옵션
![create_vpc](./img/create_vpc.png)
- Resources to create
    - VPC and more
- Name tag auto-generation
    - check Auto-generate 
    - 앱 이미지명 기입
- IPv4 CIDR block
    - 10.0.0.0/16
- IPv6 CIDR block
    - check No IPv6 CIDR block
- Tenancy
    - choose default
- Number of Availablility Zones(AZs)
    - 2

![vpc_result](./img/vpc_result.png)

## ALB
### 1. 보안 그룹 Security Group 생성
- Inbound / Outbound 트래픽 제어

![create_security_group](./img/create_security_group.png)

### 2. 타겟 그룹 Target Group 생성
- ALB가 요청을 분배하는 기준

![create_target_group](./img/create_target_group.png)
- Health check에 애플리케이션의 health check endpoint를 입력

![select_network](./img/select_network.png)

### 3. ALB 생성

![create_alb1](./img/create_alb1.png)

- 로드밸런스 이름과 설정

![create_alb2](./img/create_alb2.png)

- 사용할 vpc

![create_alb3](./img/create_alb3.png)

- 사용할 시큐리티 그룹과 라우팅 대상

![create_alb4](./img/create_alb4.png)

# 컨테이너 환경
## ECS 클러스터
### 1. fargate 클러스터 생성

![create_cluster](./img/create_cluster.png)

### 2. IAM에 클러스터에 대한 권한 제공
- AmazonECSTaskExecutionRolePolicy
    - 배포 자동화를 위한 Role
    - IAM > Role

### 3. Task Definition 생성
- 테스크 정의 생성

![create_td1](./img/create_td1.png)
- Fargate 선택

![create_td2](./img/create_td2.png)
- 이미지 ECR 주소 입력

![create_td3](./img/create_td3.png)
- CloudWatch 설정

### 4. Task 실행

![create_task1](./img/create_task1.png)
![create_task2](./img/create_task2.png)
![create_task3](./img/create_task3.png)

![create_task4](./img/create_task4.png)
- VPC, Security group 적용

![create_task5](./img/create_task5.png)
- alb 적용

### 5. 결과 확인
![ecs_result1](./img/ecs_result1.png)
- 태스크 생성 확인

![ecs_result2](./img/ecs_result2.png)
- 로그 확인

![ecs_result3](./img/ecs_result3.png)
![ecs_result4](./img/ecs_result4.png)
- 주소 확인

# 배포 자동화
- Github Actions를 활용

## env
```
env:
    # 본인의 Region 에 맞게 설정
    AWS_REGION: ap-northeast-2
    # 본인의 ECR URI 를 지정
    ECR_REGISTRY: 200073956152.dkr.ecr.ap-northeast-2.amazonaws.com/hhplus-prod
    # 본인의 ECR Repository 명을 지정
    ECR_REPOSITORY: hhplus-prod
```

## task-definition.json
- Task Definition 의 Json 을 복사해 프로젝트에 task-definition.json 파일을 생성

```json
{
    "family": "{Task Definition 이름}",
    "containerDefinitions": [
        {
            "name": "{컨테이너 명}",
            "image": "{실행할 ECR 이미지 주소 (스크립트를 통한 자동 변경)}",
            "cpu": 0,
            "portMappings": [
                {
                    "containerPort": 3000,
                    "hostPort": 3000,
                    "protocol": "tcp"
                }
            ],
            "essential": true,
            "environment": [],
            "environmentFiles": [],
            "mountPoints": [],
            "volumesFrom": [],
            "ulimits": [],
            "logConfiguration": {
                "logDriver": "awslogs",
                "options": {
                    "awslogs-create-group": "true",
                    "awslogs-group": "/ecs/task-hhplus-nest-prod",
                    "awslogs-region": "ap-northeast-2",
                    "awslogs-stream-prefix": "ecs"
                },
                "secretOptions": []
            }
        }
    ],
    "taskRoleArn": "arn:aws:iam::{iam식별자}:role/role-ecs-tasks",
    "executionRoleArn": "arn:aws:iam::{iam식별자}:role/role-ecs-tasks",
    "networkMode": "awsvpc",
    "requiresCompatibilities": [
        "FARGATE"
    ],
    "cpu": "512",
    "memory": "1024",
    "runtimePlatform": {
        "cpuArchitecture": "X86_64",
        "operatingSystemFamily": "LINUX"
    }
}
```

## work flow
- ecs 태스크 정의 생성과 태스크 배포
```yaml
- name: Render ECS task-definition
  id: render-task-definition
  uses: aws-actions/amazon-ecs-render-task-definition@v1
  with:
    task-definition: { task definition 경로 }
    container-name: { task definition 컨테이너 명칭 }
    image: ${{ steps.build-image.outputs.image }}

- name: Deploy Amazon ECS task-definition
  uses: aws-actions/amazon-ecs-deploy-task-definition@v1
  with:
    task-definition: ${{ steps.render-task-definition.outputs.task-definition }}
    cluster: { ECS 클러스터 명 }
    service: { ECS 서비스 명 }
    wait-for-service-stability: true
```

