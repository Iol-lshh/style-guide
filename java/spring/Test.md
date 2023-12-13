# 테스트
1. deploy the complete application and test
    - jar/war 파일을 빌드하고 어딘가 배포해서 테스트
    - 시스템 테스트 System Testing, 통합 테스트 Integration Testing
2. Test specific units of application code independently
    - 애플리케이션 코드의 특정 단위를 독립적으로 테스트
        - 특정 메서드, 메서드 그룹
    - 단위 테스트 Unit Testing
    - Java 인기 테스트 프레임워크
        - JUnit 단위 테스트 프레임워크
            - JUnit 5 Jupiter test
        - Mockito 모킹 프레임워크

# JUnit
- failure가 없으면 success한다.
    - 여러 조건 Assert를 검사하고, 하나의 검사라도 실패하면 유닛테스트에 실패한다.
    

