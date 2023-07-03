## **CI/CD**

- 도입 이유
  - 애플리케이션이 마이크로서비스 아키텍처 방식으로 도메인을 분리하여 개발하기 시작했고, 적게는 수십 개에서 많게는 수백 개 이상으로 쪼개졌다. -> 빌드 - 테스트 - 배포에 대한 부담감 증가
  - 더 자주 빌드하고 테스트하고 배포할 수 있는 환경을 만듦으로써 시스템 품질의 향상을 가져옴.

![](https://github.com/Duhui-Kim/BlogProject/assets/118238663/6d980c50-4f1a-48b5-8eb1-158d0a429ca7)

### **CI (Continuous Integration)**

- 빌드와 테스트를 자동화
- 여러 명이 동시에 작업하는 경우 충돌을 방지하고 모니터링 할 수 있음

### **CD (Continuous Delivery & Continuous Deployment)**

- Continuous Delivery
  - 애플리케이션에 적용한 코드의 빌드와 테스트를 성공적으로 진행했을 때, 깃허브와 같은 코드 저장소에 자동으로 업로드
- Continuous Deployment
  - 성공적으로 병합한 코드 내용을 AWS와 같은 배포 환경으로 보내는 것 (== Release)

### **CI/CD Flow**

### **Workflow**

- on 속성을 이용해 해당 Workflow가 언제 실행될지 정의

  ```yaml
  # 1. main, develop branch에 commit이 push되거나
  # 2. pull_request가 만들어지고 동기화될 때 실행
  
  on:
    push:
      branches:
        - main
        - develop
    pull_request:
  ```

### **Job**

- 하나의 워크플로에 여러 개의 Job 정의 가능

- 필수적으로 정의해야할 속성 : runs-on, steps

  ```yaml
  jobs:
    echo-hello-world:
      runs-on: ubuntu-latest # Job을 실행할 러너(환경) 정의
      steps: # 실행 스텝 지정
        - uses: actions/checkout@v3 # 지정한 Repository를 확인하고 코드에 대한 작업 실행
  
        - uses: actions/setup-java@v3 # step의 이름 지정
          with:
              distribution: 'corretto'
              java-version: '17'
  
        - name: Grant execute permission for gradlew
          run: chmod +x gradlew # 명령어 실행
  
        - name : Build with Gradle
          run: ./gradlew clean build!
  ```

- Job을 병렬적으로 구성하여 진행할 수도 있음.

  ![](https://github.com/Duhui-Kim/BlogProject/assets/118238663/527b70f7-f6f0-4552-aeca-4d2dfe124b77)

  - 병렬적 구성으로 속도 향상
  - 여러 관심사를 한 번에 체크할 수 있음 (정적 분석이 실패하더라도 유닛 테스트의 성공 여부를 알 수 있음)

## **CI/CD 파이프라인 종류 및 특징**

### **Jenkins**

- 무료
- 다양한 플러그인, IDE 지원
- 많은 사용자와 많은 문서
- 규모가 작은 프로젝트의 경우 설정하는데 리소스 낭비가 발생할 수 있다.
- 지라와 연동이 불편하거나 완벽하지 않을 수 있다.

### **Travis**

- Github와 연동
- 빌드 과정을 한 눈에 이해하기 쉽다.
- 초기 설정이 Jenkins에 비해 간편(YML 파일을 통한 설정)
- 별도의 서버가 필요 없다. Travis에서 알아서 VM으로 호스팅해줌.

### **Github Actions**

- 복잡한 과정 없이 바로 Github에서 사용할 수 있다.
- 빌드 과정을 눈으로 확인하기 쉽다.
- Github의 모든 이벤트에 대한 작업을 제공하고 다양한 언어와 프레임워크를 지원한다.
- Jenkins보다 빠르다.
- Public은 무료, Private 저장소의 경우 매월 3000분 무료
- 문서가 비교적 부족하다.
- UI에서 개별 워크플로우 실행을 삭제할 수 없다.
- 워크플로우에서 단일 작업만 다시 실행할 수 없다.



## Github Actions에서 Secret-Key 관리

- Github Actions를 사용하면서 application.yml에 JWT Secret이나 Google API key 같은 중요 정보들이 있어서 .gitignore을 이용해서 업로드되지 않도록 막았다. 그런데 그렇게 하니 Git에 upload 시 Build를 진행하는데 이 때 설정파일이 존재하지 않아 Test가 실패했다.

  ![캡처4](https://github.com/Duhui-Kim/BlogProject/assets/118238663/c57fe7fc-2f00-427f-93f0-6f28f9e1a6df) 

  

- 자료를 찾아보니 이를 해결하기 위한 여러가지 방법이 있지만 가장 간편하고 외부 노출을 막기 위한 방법이 있어서 시도해봤다.

1. 필요한 환경변수 설정 (Settings → Secrets and variables → Actions → New repository secret)

​				![캡처1](https://github.com/Duhui-Kim/BlogProject/assets/118238663/43e4cf36-e8b8-43d1-b1ae-c9848cd8c28d)

2. 설정값을 사용할 application.yml 파일에 지정

​				![캡처2](https://github.com/Duhui-Kim/BlogProject/assets/118238663/59b0dd6f-c810-4abf-b832-14dac8a01dfa)

3. 성공!!

   ![캡처3](https://github.com/Duhui-Kim/BlogProject/assets/118238663/4049f576-1322-4971-9ac2-1e692428a3a8)