데이터 흐름 파이프라인 개념을 기반으로 동작

이 파이프라인은 3가지 주요 요소로 구성
- 데이터 소스
	- Stream 데이터가 시작되는 곳
	- List, Array 등 다양한 형태의 데이터에서 Stream으로 시작 가능
- 중간 연산
	- 데이터 변환 및 필터링 같은 작업 수행할 지 정의
	- 하지만 여기서는 작업이 정의만 되고, 실제 수행 안함
	- 중간 연산은 단독으로도 사용 될 수 있고, 여러 개 체이닝도 가능
	- 핵심은 작업을 정의만 하고, 아직 수행되지 않는다는 점
	- `filter()`, `map()`등
- 종결 연산
	- 이게 호출 되어야만 파이프라인이 실행됨
	- 종결 연산 호출되어야, 중간 연산에서 정의한 작업들이 수행됨
	- 종결 연산 이후는 더이상Stream 못 씀
	- `findFirst()`, `anyMatch()` 같은 일부 종결 연산은 필요한 만큼의 데이터만 처리하고 즉시 종료하는 단축 평가(short-circuiting)방식 동작 할 수 있음
	- `collect(), foreach(), reduce()등`

## 세탁기 비유
1. 데이터 소스: 더러운 옷 준비
2. 중간 연산: 세탁 코스 설정
	- 세탁 15분, 헹굼 3회, 탈수 5분
	- 아직 세탁기 미작동
3. 종결 연산: 시작 버튼 누르기

## 예시 코드
```java
@Slf4j
public class StreamBasicTest {

    @Test
    void testStream() {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

        Stream<Integer> numberStream = numbers.stream()
                .filter(num -> {
                    // 로깅1: 각 숫자가 필터를 통과할 때 로그를 남깁니다
                    log.info("필터링: " + num);  // 로깅1
                    return num % 2 == 1;  // 홀수만 필터링
                })
                .map(num -> {
                    // 로깅2: 각 숫자가 필터를 통과할 때 로그를 남깁니다
                    log.info("매핑: " + num);  // 로깅2
                    return num * 2;  // 2를 곱하여 변환
                });

        // 아직 아무 연산도 실행되지 않았음을 보여주기 위한 로그
        log.info("[계획 수립 완료] 아직 실제 실행은 되지 않았습니다!");

        // 종결 연산을 호출하여 실제 실행
        log.info("[실행 시작] collect() 호출!");
        List<Integer> result = numberStream.collect(Collectors.toList());
        log.info("[실행 완료] 결과: " + result);
    }
}
```
결과
```
[계획 수립 완료] 아직 실제 실행은 되지 않았습니다!
[실행 시작] collect() 호출!
필터링: 1
매핑: 1
필터링: 2
필터링: 3
매핑: 3
필터링: 4
필터링: 5
매핑: 5
[실행 완료] 결과: [2, 6, 10]

```
![[SCR-20250622-nuxc.png]]

이러한 종결 연산이 호출되기 전까지는 중간 연산들이 실제로 수행되지 않는, Stream 의 특성을 ==지연 평가 특성==이라고합니다.