---
tags: 
created: 2025-09-03
---
## Lower Bound
**target** **이상**의 배열에서 **처음** 나타나는 인덱스 반환

즉, arr\[index] >= target 을 만족하는 가장 작은 index 찾는다.

```java
public static int lowerBound(int[] arr, int target){
	int low = 0;
	int high = arr.length; // high를 배열 길이로 설정하는 것이 핵심

    // low와 high가 같아질 때까지 반복
    while (low < high) {
        int mid = low + (high - low) / 2;

        // arr[mid]가 target보다 크거나 같으면,
        // mid는 잠재적인 답이 될 수 있으므로 high를 mid로 좁힘
        if (arr[mid] >= target) {
            high = mid;
        } 
        // arr[mid]가 target보다 작으면,
        // mid는 답이 될 수 없으므로 low를 mid + 1로 좁힘
        else {
            low = mid + 1;
        }
    }
    return low; // 또는 high를 반환해도 동일
}
```

## Upper Bound
**target** **초과**하는 값이 배열에서 **처음** 나타나는 인덱스 반환

즉, arr\[index] > target 을 만족하는 가장 작은 index 찾는다.

```java
    // arr에서 target을 초과하는 값이 처음 나타나는 인덱스를 반환 (upper_bound)
    // target 값이 없다면, target이 삽입될 위치를 반환
    public static int upperBound(int[] arr, int target) {
        int low = 0;
        int high = arr.length; // high를 배열 길이로 설정하는 것이 핵심

        // low와 high가 같아질 때까지 반복
        while (low < high) {
            int mid = low + (high - low) / 2;

            // arr[mid]가 target보다 크면,
            // mid는 잠재적인 답이 될 수 있으므로 high를 mid로 좁힘
            if (arr[mid] > target) {
                high = mid;
            } 
            // arr[mid]가 target보다 작거나 같으면,
            // mid는 답이 될 수 없으므로 low를 mid + 1로 좁힘
            else {
                low = mid + 1;
            }
        }
        return low; // 또는 high를 반환해도 동일
    }

```
