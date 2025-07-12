Java 8 이상에서는 Stream API 활용해 데이터를 선언적으로 처리할 수 있는 방법 제공

요구사항 예시
1. 숫자 리스트 1,2,3,4,5 에서
2. 2보다 큰 값만 필터링하고
3. 그 값을 두 배로 반환
4. 리스트로 수집

**명령형**(어떻게 할지)
```java
List<Integer> numbers = Arrays.asList(1,2,3,4,5);
List<Integer> result = new ArrayList<>();
for(int n : numbers){
	if(n > 2){
		result.add(n*2);
	}
}
```
**선언형**(무엇을 할지에 집중)
```java
List<Integer> numbers = Arrays.asList(1,2,3,4,5);
List<Integer> result = numbers.stream()
	.filter(n -> n>2)
	.map(n -> n*2)
	.collect(Collectors.toList());
```