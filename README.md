## 카카오페이 뿌리기 기능 구현하기(사전과제)

### 목적

- 뿌리기, 받기, 조회 기능을 수행하는 간소화된 REST API를 구현  

  
### 개발환경

- SpringBoot 2.4.0 with Maven
- JPA with H2 Database(in memory)

    H2 데이터베이스는 in memory 방식으로 사용(테스트용도로 간단하게 사용하기 위함)하여 데이터 영속성이 주어지지 않았으며 휘발성임. 


### 핵심문제해결전략

1. 토큰생성
    - 3자리 문자열, 예측불가능 해야함 → random 함수를 사용하여 알파벳 대소문자로 구성된 3자리 문자열을 생성
    - 중복된 토큰이 있을경우를 대비 → recursive 함수를 사용하여 중복된 토큰이 있을경우 재생성(중복이 발생하지 않을떄까지) 하도록 구성

2. 뿌릴금액 분배
    - 금액을 일괄적으로 분배하지 않고 랜덤하게 분배하도록 구성. → 받기 또한 가능한 건을 랜덤으로 받아가도록 함으로써 재미요소를 가미
    - 분배된 금액의 총합은 뿌릴금액과 동일하여야 하므로 분배될 인원수만큼 분배될때까지 매번 잔액범위 내에서 랜덤한 금액 생성.

3. 뿌리기, 조회, 받기시 조건만족 검증
    - 특별히 검증 및 결과값을 사용자에게 알려줄만한 조건들은 각 조건을 데이터와 검증하고 결과값 리턴
    - 불필요한 쿼리생성을 피하고 간단히 구성하기 위해 쿼리메소드 방식을 사용

4. 다수의 서버, 다수의 인스턴스로 동작
... 구현중


### API 요청 및 응답 예제

1. **뿌리기**

    요청

    ```
    POST /sprinkle/create/{뿌릴금액}/{인원수}
    // /create/120000/500
    ```

    결과

    ```
    {
        "code": "S000",
        "message": "정상적으로 처리되었습니다.",
        "data": "MVF"   // 토큰값 리턴
    }
    ```

2. **받기**

    요청

    ```java
    PUT /sprinkle/pickup/{토큰값}
    // /sprinkle/pickup/MVF
    ```

    **결과**

    ```java
    {
        "code": "S000",
        "message": "정상적으로 처리되었습니다.",
        "data": "8457"
    }
    ```

3. **조회**

    요청

    ```java
    GET /sprinkle/{토큰값}
    // /sprinkle/MVF
    ```

    **결과**

    ```java
    {
        "code": "S000",
        "message": "정상적으로 처리되었습니다.",
        "data": "{\"regDate\":\"2020-11-21 15:33:30\",\"sprinkleAmt\":120000,\"receivedAmt\":8457,\"distrbtList\":[{\"rcvAmt\":8457,\"rcvUserId\":\"123469\"}]}"
    }
    ```
