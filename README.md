# 지하철 노선도 미션
[ATDD 강의](https://edu.nextstep.camp/c/R89PYi5H) 실습을 위한 지하철 노선도 애플리케이션

# 실습 - 문서화
## 실습 - 테스트를 통한 문서화
- `경로 찾기` 기능을 문서화하기 위한 **테스트를 작성**하세요.
- 테스트 작성 후 **Spring Rest Docs 적용**을 통해 문서에 기재할 정보를 설정하세요.

### 실습 방법
- PathDocumentation 의 path 메서드를 완성시키세요.
- PathDocumentation 의 테스트를 수행시켜 snippet 을 생성하세요.
- gradle 로 asciidoctor task 를 수행시켜 문서 파일을 생성하세요
- build > asciidoc > html5 > index.html 을 브라우저로 열고 캡쳐한 이미지를 PR에 포함시켜 주세요.
- PathSteps 클래스를 활용하여 PathDocumentation 내 코드 중복을 제거해주세요

# 1단계 - 경로 조회 타입 추가
## 요구사항
### 기능 요구사항 - 최소 시간 경로 타입 추가
- 경로 조회 시 최소 시간 기준으로 조회할 수 있도록 기능을 추가하세요.
- 노선추가 & 구간 추가 시 `거리`와 함께 `소요시간` 정보도 추가하세요.
- 인수 테스트 (수정) -> 문서화 -> 기능 구현 순으로 진행하세요.
- 개발 흐름을 파악할 수 있도록 커밋을 작은 단위로 나누어 구현해보세요.

### 프로그래밍 요구사항
- **인수 테스트 주도 개발 프로세스**에 맞춰서 기능을 구현하세요.
  - `요구사항 설명`을 참고하여 인수 조건을 정의
  - 인수 조건을 검증하는 인수 테스트 작성
  - 인수 테스트를 충족하는 기능 구현
- 인수 조건은 인수 테스트 메서드 상단에 주석으로 작성하세요.
  - 뼈대 코드의 인수 테스트를 참고
- `인수 테스트 이후 기능 구현은 TDD로 진행하세요.`
  - 도메인 레이어 테스트는 필수
  - 서비스 레이어 테스트는 선택

## 요구사항 설명
### 인수 조건
```text
Feature: 지하철 경로 검색

  Scenario: 두 역의 최소 시간 경로를 조회
    Given 지하철역이 등록되어있음
    And 지하철 노선이 등록되어있음
    And 지하철 노선에 지하철역이 등록되어있음
    When 출발역에서 도착역까지의 최소 시간 기준으로 경로 조회를 요청
    Then 최소 시간 기준 경로를 응답
    And 총 거리와 소요 시간을 함께 응답함
```
### 요청 api 수정
- source: 출발역 id
- target: 도착역 id
- type: 거리 or 시간
```http request
HTTP/1.1 200 
Request method:	GET
Request URI:	http://localhost:55494/paths?source=1&target=3&type=DURATION
Headers: 	Accept=application/json
		Content-Type=application/json; charset=UTF-8
```

### 소요 시간 추가
- 경로 조회 시 총 소요 시간을 조회하기 위해서는 노선과 구간을 생성할 때 소요 시간 정보를 함께 보내야 합니다.
```java
public class LineRequest {
    private String name;
    private String color;
    private Long upStationId;
    private Long downStationId;
    private int distance;
    private int duration;
    
    ...
```

### 요구사항별 시나리오
- 두 역의 최단 시간 경로를 조회 인수 테스트 시나리오
  - 교대역 - 강남역 (2호선, 10km, 4min) 이 포함된 노선을 등록한다.
  - 강남역 - 양재역 (신분당선, 10km, 3min) 이 포함된 노선을 등록한다.
  - 교대역 - 남부터미널역 (3호선, 2km, 4min) 이 포함된 노선을 등록한다.
  - 남부터미널역 - 양재역 (3호선, 3km, 4min) 이 포함된 노선을 등록한다.
  - 교대역에서 양재역까지의 최단 시간 경로를 조회한다.
    - 최단 시간 경로로 역은 교대역 -> 강남역 -> 양재역 순이다.
    - 각 역의 이동 경로의 총 시간은 7min 이다.

# 2단계 - 요금 조회
## 요구사항
### 기능 요구사항 - 경로 조회 시 요금 정보 포함하기
- 경로 조회 결과에 요금 정보를 포함하세요.
- 인수 테스트 (수정) -> 문서화 -> 기능 구현 순으로 진행하세요.
- 개발 흐름을 파악할 수 있도록 커밋을 작은 단위로 나누어 구현해보세요.

### 프로그래밍 요구사항
- 인수 테스트 주도 개발 프로세스에 맞춰서 기능을 구현하세요.
  - `요구사항 설명`을 참고하여 인수 조건을 정의
  - 인수 조건을 검증하는 인수 테스트 작성
  - 인수 테스트를 충족하는 기능 구현
- 인수 조건은 인수 테스트 메서드 상단에 주석으로 작성하세요.
  - 뼈대 코드의 인수 테스트를 참고
- `인수 테스트 이후 기능 구현은 TDD로 진행하세요.`
  - 도메인 레이어 테스트는 필수
  - 서비스 레이어 테스트는 선택

## 요구사항 설명
### 인수 조건
```text
Feature: 지하철 경로 검색

  Scenario: 두 역의 최단 거리 경로를 조회
    Given 지하철역이 등록되어있음
    And 지하철 노선이 등록되어있음
    And 지하철 노선에 지하철역이 등록되어있음
    When 출발역에서 도착역까지의 최단 거리 경로 조회를 요청
    Then 최단 거리 경로를 응답
    And 총 거리와 소요 시간을 함께 응답함
    And 지하철 이용 요금도 함께 응답함
```

### 요금 계산 방법
- 기본운임(10㎞ 이내) : 기본운임 1,250원
- 이용 거리초과 시 추가운임 부과
  - 10km 초과 ∼ 50km 까지(5km 마다 100원)
  - 50km 초과 시 (8km 마다 100원)
```text
9km = 1250원
12km = 10km + 2km = 1350원
16km = 10km + 6km = 1450원
```
> 지하철 운임은 거리비례제로 책정됩니다. (실제 경로가 아닌 최단거리 기준)

### 요구사항별 시나리오
- 두 역의 최단 시간, 거리 경로에 따른 총 요금 조회 인수 테스트 시나리오
  - 교대역 - 강남역 (2호선, 10km, 4min) 이 포함된 노선을 등록한다.
  - 강남역 - 양재역 (신분당선, 10km, 3min) 이 포함된 노선을 등록한다.
  - 교대역 - 남부터미널역 (3호선, 2km, 4min) 이 포함된 노선을 등록한다.
  - 남부터미널역 - 양재역 (3호선, 3km, 4min) 이 포함된 노선을 등록한다.
  - 교대역에서 양재역까지의 최단 시간 경로를 조회한다.
    - 최단 시간 경로로 역은 교대역 -> 강남역 -> 양재역 순이다.
    - 각 역의 이동 경로의 총 거리는 20km 이다.
    - 따라서 총 요금은 1450 원이 부과된다.
  - 교대역에서 양재역까지의 최단 거리 경로를 조회한다.
    - 최단 거리 경로로 역은 교대역 -> 남부터미널역 -> 양재역 순이다.
    - 각 역의 이동 경로의 총 거리는 5km 이다.
    - 따라서 총 요금은 1250 원이 부과된다.

- FareType 단위 테스트
  - input (distance) 에 맞는 요금을 계산할 수 있다.
  - distance <= 0 인 경우
    - 요금 계산을 할 수 없다.
  - **기본 운임** : 0 < distance <= 10km 인 경우 
    - distance = 9km 인 경우 (경계값)
      - 기본 요금인 1250 원이 부과된다.
    - distance = 10km 인 경우 (경계값)
      - 기본 요금인 1250 원이 부과된다.
  - **이용거리 초과에 따른 추가 운임** : distance > 10km 인 경우
    - distance = 11km 인 경우 (경계값)
      - 1250 + 100 = 1350 원이 부과된다.
    - distance = 14km 인 경우 (경계값)
      - 1250 + 100 = 1350 원이 부과된다.
    - distance = 15km 인 경우 (경계값)
      - 1250 + 100 = 1350 원이 부과된다.
    - distance = 16km 인 경우 (경계값)
      - 1250 + 200 = 1450 원이 부과된다.
    - distance = 19km 인 경우 (경계값)
      - 1250 + 200 = 1450 원이 부과된다.
    - distance = 20km 인 경우 (경계값)
      - 1250 + 200 = 1450 원이 부과된다.
    - distance = 21km 인 경우 (경계값)
      - 1250 + 300 = 1550 원이 부과된다.
    - distance = 49km 인 경우 (경계값)
      - 1250 + 800 = 2050 원이 부과된다.
    - distance = 50km 인 경우 (경계값)
      - 1250 + 800 = 2050 원이 부과된다.
  - **이용거리 초과에 따른 추가 운임** : distance > 50km 인 경우
    - distance = 51km 인 경우 (경계값) 
      - 2050 + 100 = 2150 원이 부과된다.
    - distance = 58km 인 경우 (경계값)
      - 2050 + 100 = 2150 원이 부과된다.
    - distance = 59km 인 경우 (경계값)
      - 2050 + 200 = 2250 원이 부과된다.
    - distance = 66km 인 경우 (경계값)
      - 2050 + 200 = 2250 원이 부과된다.
    - distance = 67km 인 경우 (경계값)
      - 2050 + 200 = 2350 원이 부과된다.

# 3단계 - 요금 정책 추가
## 요구사항
### 기능 요구사항 - 스펙 추가하기
- 추가된 요금 정책을 반영하세요.
- 인수 테스트 변경 -> 문서화 변경 -> 기능 구현 순으로 진행하세요.
- 개발 흐름을 파악할 수 있도록 커밋을 작은 단위로 나누어 구현해보세요.

### 프로그래밍 요구사항
- **인수 테스트 주도 개발 프로세스**에 맞춰서 기능을 구현하세요.
  - `요구사항 설명`을 참고하여 인수 조건을 정의
  - 인수 조건을 검증하는 인수 테스트 작성
  - 인수 테스트를 충족하는 기능 구현
- 인수 조건은 인수 테스트 메서드 상단에 주석으로 작성하세요.
  - 뼈대 코드의 인수 테스트를 참고
- `인수 테스트 이후 기능 구현은 TDD로 진행하세요.`
  - 도메인 레이어 테스트는 필수
  - 서비스 레이어 테스트는 선택

## 요구사항 설명
### 추가된 요금 정책
#### 노선별 추가 요금
- 추가 요금이 있는 노선을 이용 할 경우 측정된 요금에 추가
  - ex) 900원 추가 요금이 있는 노선 8km 이용 시 1,250원 -> 2,150원
  - ex) 900원 추가 요금이 있는 노선 12km 이용 시 1,350원 -> 2,250원
- 경로 중 추가요금이 있는 노선을 환승 하여 이용 할 경우 가장 높은 금액의 추가 요금만 적용
  - ex) 0원, 500원, 900원의 추가 요금이 있는 노선들을 경유하여 8km 이용 시 1,250원 -> 2,150원

#### 로그인 사용자의 경우 연령별 요금으로 계산
- 청소년: 운임에서 350원을 공제한 금액의 20%할인
- 어린이: 운임에서 350원을 공제한 금액의 50%할인
```text
- 청소년: 13세 이상~19세 미만
- 어린이: 6세 이상~ 13세 미만
```

### 요구사항별 시나리오
```
 교대역    --- *2호선*  ---  강남역
   |                        |
 *3호선*                   *신분당선*
   |                        |
 남부터미널역  --- *3호선* ---  양재
```
- 요금 정책에 따른 요금조회 기능을 위한 초기 데이터 셋팅
  - 교대역 - 강남역 (2호선, 10km, 4min) 이 포함된 노선을 등록한다.
  - 강남역 - 양재역 (신분당선, 10km, 3min) 이 포함된 노선을 등록한다.
  - 교대역 - 남부터미널역 (3호선, 2km, 4min, 추가요금 300원) 이 포함된 노선을 등록한다.
  - 남부터미널역 - 양재역 (3호선, 3km, 4min, 추가요금 300원) 이 포함된 노선을 등록한다.

- 추가 요금이 있는 노선에 대한 경로 조회 & 연령별 할인 된 총 요금 조회 인수 테스트 시나리오
  - 교대-양재역에 대한 최단 경로 조회를 요청한다. (거리 기준)
    - 최단 거리 경로로 역은 교대역 -> 남부터미널역 -> 양재역 순이다.
    - 각 역의 이동 경로의 총 거리는 5km 이다.
    - 3호선의 경우, 추가요금 300원이 부과된다.
      - 여기까지 총 요금은 1550 원이다.
    - 어린이일 경우 (1550 - 350) * (1 - 0.5) = 600 원이 부과된다.
    - 청소년일 경우 (1550 - 350) * (1 - 0.2) = 960 원이 부과된다.
    - 성인일 경우 1550 원이 부과된다.

- 연령별 할인 요금 계산 단위 테스트
  - 거리 & 노선에 부과된 최종 요금이 x 원 일 때
  - 유아일 경우, 요금은 0원이다.
  - 어린이일 경우, 요금은 (x-350)*0.5 원이다.
  - 청소년일 경우, 요금은 (x-350)*0.8 원이다.
  - 성인일 경우, 요금은 x 원이다.
  - 노인일 경우, 요금은 0원이다.

```
고속터미널역  --- *7호선* ---  강남구청역
   |
 *3호선*
   |
 교대역    --- *2호선*  ---  강남역
                            |
                        *신분당선*
                            |
                          양재역
```
- 노선에 추가 요금계산 단위 테스트
  - 2호선, 신분당선(추가 요금 300원), 3호선, 7호선(추가 요금 100원)을 추가한다.
  - 경로 조회 결과로 위 노선을 모두 탔다면 최종 경로에서 계산될 노선의 추가 요금은 가장 추가 요금이 많은 신분당선의 300원이 책정된다. 

- 거리 & 연령 & 노선 정책이 모두 포함된 상태의 최종 요금 조회 단위 테스트
  - 2호선, 신분당선(추가 요금 300원), 7호선(추가 요금 100원)을 추가한다.
  - 양재-강남(10km), 강남-교대(10km), 교대-고속(1km), 고속-강남구청(3km) => 총 24km 를 이동함.
  - 계산된 거리 & 노선의 추가요금을 합해서 1550원 + 300원 = 1850 원의 추가 요금이 부과됨
  - 어린이일 경우, 요금은 (1850 - 350) * 0.5 = 750 원이다.
  - 청소년일 경우, 요금은 (1850 - 350) * 0.8 = 1200 원이다.
  - 성인일 경우, 요금은 1850 원이다.
