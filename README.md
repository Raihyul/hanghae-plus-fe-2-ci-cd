# 프론트엔드 CI/CD 파이프라인

## 개요

![Blank diagram](https://github.com/user-attachments/assets/a0f35e27-8a88-455c-873e-6610c80557a2)


이 프로젝트는 GitHub Actions를 사용하여 자동화된 CI/CD 파이프라인을 구축하고, AWS 서비스를 활용하여 정적 웹사이트를 호스팅합니다. GitHub Actions를 통해 코드 변경사항에 따라 자동으로 웹사이트를 AWS S3에 배포하고 CloudFront를 통해 제공하는 CI/CD 파이프라인을 구현합니다.

## 기술 스택
- GitHub Actions
- AWS S3
- AWS CloudFront
- Node.js

## 주요 링크

- S3 버킷 웹사이트 엔드포인트: http://hanghae-plus-fe-2-ci-cd.s3-website-us-east-1.amazonaws.com/
- CloudFrount 배포 도메인 이름: https://d18d5ynnr4vqh7.cloudfront.net

## 배포 프로세스 상세 설명

배포 프로세스는 `.github/workflows/deployment.yml` 파일에 정의되어 있습니다. 이 워크플로우는 다음과 같은 주요 단계로 구성됩니다:

1. **트리거 조건**:
    - main 브랜치에 push가 발생
    - 수동으로 워크플로우를 실행할 때 (workflow_dispatch) 트리거

2. **환경 설정**:
   - GitHub Secrets에서 설정된 값 사용
     - AWS Region
     - Access key id
     - Secret access key
     - S3 버킷 이름
     - CloudFront 배포 ID 

3. **작업 환경**:
    - Ubuntu 최신 버전에서 실행

4. **단계별 프로세스**:

   a. 코드 체크아웃: actions/checkout@v4를 사용하여 최신 코드를 가져옵니다.

   b. AWS 자격 증명 구성: aws-actions/configure-aws-credentials@v4을 사용하여 AWS 접근 키와 비밀 키를 설정합니다.

   c. Node.js 설정: actions/setup-node@v4를 사용하여 Node.js 18 버전을 설정합니다.

   d. 의존성 설치: `npm ci` 명령을 실행하여 프로젝트 의존성을 설치합니다.

   e. 프로젝트 빌드: `npm run build` 명령으로 프로젝트를 빌드합니다.

   f. S3 배포: `aws s3 sync` 명령을 사용하여 빌드된 파일을 S3 버킷에 업로드합니다. `--delete` 옵션을 사용하여 버킷의 기존 파일 중 로컬에 없는 파일은 삭제합니다.

   g. CloudFront 캐시 무효화: `aws cloudfront create-invalidation` 명령을 사용하여 모든 경로("/*")에 대한 캐시를 무효화합니다.

## 주요 개념

- **GitHub Actions과 CI/CD 도구**: GitHub에서 제공하는 CI/CD 도구로, 코드 변경사항에 따라 자동으로 빌드, 테스트, 배포 과정을 수행합니다. 이를 통해 개발자는 코드 변경사항을 쉽게 통합하고 지속적으로 배포할 수 있습니다.

- **S3와 스토리지**: AWS S3(Simple Storage Service)는 확장성이 뛰어난 객체 스토리지 서비스입니다. 이 프로젝트에서는 정적 웹사이트 호스팅에 사용되며, 빌드된 프론트엔드 파일들을 저장하고 제공합니다.

- **CloudFront와 CDN**: AWS CloudFront는 콘텐츠 전송 네트워크(CDN) 서비스입니다. S3에 저장된 정적 파일을 전 세계 엣지 로케이션에 캐싱하여 빠른 콘텐츠 전달을 가능하게 합니다. 이를 통해 웹사이트의 로딩 속도를 개선하고 전 세계 사용자에게 일관된 경험을 제공합니다.

- **캐시 무효화(Cache Invalidation)**: CloudFront의 엣지 로케이션에 저장된 캐시를 새로운 콘텐츠로 업데이트하는 프로세스입니다. 이 프로젝트에서는 S3 업로드 직후 자동으로 수행되어, 사용자가 항상 최신 버전의 웹사이트를 볼 수 있도록 보장합니다.

- **Repository secret과 환경변수**: GitHub Secrets는 AWS 접근 키, S3 버킷 이름, CloudFront 배포 ID 등 민감한 정보를 안전하게 저장하고 사용할 수 있게 해주는 기능입니다. 이를 통해 중요한 정보를 코드에 직접 노출시키지 않고 안전하게 CI/CD 프로세스에서 사용할 수 있습니다.

## 향후 개선 사항

프로젝트의 규모, 복잡성, 그리고 특정 요구사항에 따라 유용할 수 있는 추가적인 기능들을 개발하여 프로젝트를 개선해 볼 수 있습니다:

1. **테스트 자동화**: 워크플로우에 자동화된 테스트 단계를 추가. 단위 테스트, 통합 테스트, E2E 테스트 등을 포함하여 배포 전 코드 품질을 보장
   
2. **스테이징 환경**: production 환경 외에 스테이징 환경을 구성. 이를 통해 실제 배포 전 최종 테스트를 수행할 수 있는 단계 마련

3. **성능 최적화**: CloudFront 설정을 세부적으로 조정하여 캐싱 전략을 최적화하고 웹사이트 로딩 속도를 개선

4. **모니터링 및 알림**: AWS CloudWatch와 연동하여 배포 성공/실패 알림을 받고, 웹사이트 성능 지표를 모니터링하는 시스템 구축.

## 결론

이 CI/CD 파이프라인은 GitHub Actions와 AWS 서비스를 효과적으로 활용하여 안정적이고 자동화된 배포 프로세스를 구현하고 있습니다. 코드 변경사항이 main 브랜치에 병합되면 자동으로 빌드, 배포, 캐시 무효화 과정이 수행되어 항상 최신 버전의 웹사이트를 사용자에게 제공할 수 있습니다.
