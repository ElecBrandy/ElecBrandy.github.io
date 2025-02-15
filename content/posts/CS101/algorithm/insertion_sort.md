+++
title = '[알고리즘] Insertion Sort'
date = 2024-09-21
featured_image = "https://upload.wikimedia.org/wikipedia/commons/e/ef/Sorting_shaker_sort_anim.gif"
tags = ['algorithm', 'sorting']
+++

{{<series title="📚 /algorithm" series="algorithm">}}

<br>

## 1. Insertion sort
____

<img src="https://i.imgur.com/sW8twRl.png" width="700">

`Insertion sort`(삽입 정렬)는 말 그대로 정렬된 배열에 새로운 요소를 삽입하여 정렬을 확장해 나가는 알고리즘이다. 배열을 처리할 때, 전체 배열 중 왼쪽 부분은 이미 정렬된 상태이고, 오른쪽 부분은 아직 정렬되지 않은 상태로 남아 있다. 삽입 정렬은 이 정렬되지 않은 부분에서 하나씩 요소를 가져와서, 정렬된 배열의 어느 위치에 삽입해야 할지 결정하는 방식으로 동작한다.

이 알고리즘에서 두 개의 주요 루프가 존재한다.  
- **첫 번째 루프** 는 정렬되지 않은 부분에서 인덱스를 하나씩 선택하는 루프로, 인덱스 1부터 배열의 끝(n-1)까지 반복된다.
- **두 번째 루프** 는 선택된 인덱스를 정렬된 부분에서 어디에 삽입할지를 결정하는 과정이다. 이 과정에서 선택된 값과 정렬된 배열의 값들을 비교하여 적절한 위치를 찾아 삽입하게 된다.  

<br>
<br>

## 2. 구현 분석
____

``` C++
void insertionSort(int *arr, int N) {
	for (int i = 1; i < N; i++) { // 루프_1
		int key = arr[i];
		int j = i - 1;
		while (j >= 0 && arr[j] > key) { // 루프_2
			arr[j + 1] = arr[j];
			j--;
		}
		arr[j + 1] = key;
	}
}
```

<br>

### 2-1. 루프 [1]

``` C++
for (int i = 1; i < N; i++)
```

이 부분은 정렬되지 않은 배열의 인덱스를 하나씩 선택하는 루프이다. 정렬되지 않은 부분에서 하나씩 가져와서 처리할 것이기 때문에, 이 루프는 결국 인덱스 `1` 부터 `N - 1` 까지 모든 인덱스를 순차적으로 처리하게 된다. 선택한 인덱스 `int key` 에 저장한다. 여기서 선택된 `int key`를 가지고 다음으로 넘어가 `while` 루프에서 지금 선택한 인덱스가 정렬된 부분에서 어느 위치에 들어가야 정렬 상태가 유지될지 결정한다.  

이때, 왜 인덱스를 `1`부터 시작하는지 궁금할 수 있다. 사실, `0`번 인덱스부터 시작해도 문제는 없다. 하지만 처음 상황에서, 아직 정렬을 시작하지 않은 상태에서는 굳이 `0`번 인덱스부터 처리할 필요가 없다. `0`번 인덱스는 이미 하나의 요소로만 이루어졌기 때문에, 크기가 1인 배열은 정렬된 상태라고 가정할 수 있다. 따라서, 굳이 `0`번부터 시작하지 않고, `1`번 인덱스부터 정렬을 시작하면 된다. 

<br>

### 2-2. 루프 [2]

``` C++
	while (j >= 0 && arr[j] > key) {
		arr[j + 1] = arr[j];
		j--;
	}
	arr[j + 1] = key;
```

이 부분은 선택된 요소가 정렬된 배열의 어느 위치에 삽입될지 결정하는 루프이다. 여기서 정렬된 부분을 탐색하는 인덱스는 `j`로, `i - 1`에서 시작한다. 즉, `j`는 현재 정렬해야 할 요소(`i`)의 바로 왼쪽에 있는 값부터 비교를 시작하는데, 이 말은 `j`가 `i - 1`에서 0까지 거꾸로 탐색하며, 정렬된 배열에서 적절한 위치를 찾아가게 된다는 의미이다. 코드를 더 자세히 분석해보자.

- **`j >= 0`**:
`j`가 0보다 작아지면 배열의 범위를 벗어나게 되므로, 배열을 넘어가는 것을 방지하기 위해 이 조건이 필요하다.  

- **`arr[j] > key`**:
이 조건은 현재 비교 중인 요소 `arr[j]`가 `key`보다 큰지 확인한다. 만약 크다면, `key`가 `arr[j]`보다 왼쪽에 위치해야 하므로, `arr[j]`를 오른쪽으로 이동시킨다. 이 과정이 반복되면서 `key`의 올바른 위치가 결정된다.  

- **`arr[j + 1] = arr[j];`**:
이 줄은 `arr[j]`를 오른쪽으로 한 칸 이동시키는 역할을 한다. 즉, `arr[j]` 값을 `arr[j + 1]`에 복사하여, `arr[j]`가 있던 자리는 비워지게 된다. 이 자리가 나중에 `key`가 들어갈 자리가 될 수 있다.  

- **`j--;`**:
`j` 값을 1 줄여 왼쪽으로 이동하면서 계속해서 `key`보다 큰 값들을 오른쪽으로 밀어낸다. 이 과정을 반복해 `key`가 들어갈 적절한 위치를 찾는다.  

- **`arr[j + 1] = key;`**:
`while` 루프가 끝난 후에는 `key`가 들어갈 자리가 정해진다. 이때 `arr[j + 1]` 위치가 `key`가 삽입될 정확한 위치이다. `while` 루프가 종료되는 시점에서는 `arr[j]`가 `key`보다 작거나 같거나, `j`가 배열을 벗어난 상태이므로, `key`는 그 다음 위치인 `j + 1`에 삽입된다.  

<br>
<br>

## 3. 정확성 분석
____
`Insertion sort`은 결국 리스트를 순차적으로 하나씩 살펴보며, 현재 요소를 이미 정렬된 부분에 알맞은 위치에 삽입하여 전체 리스트를 정렬하는 알고리즘이다. 예를 들어, 카드 게임에서 손에 카드를 한 장씩 추가하면서 이미 정렬된 손에 새로운 카드를 올바른 위치에 끼워 넣는 과정과 같다. 이제 귀납법을 통해 `Insertion sort` 알고리즘이 항상 입력된 리스트를 올바르게 정렬하는지 그 **정확성** 을 증명해보자.

<br>

### 3-1. 명제 설정

**명제 P(i)**: _반복문이 i번째 반복 후에 리스트의 앞부분 A[0...i−1]은 정렬되어 있다._  

증명을 위해 위와 같은 명제를 설정하자. 즉, 알고리즘이 `i`번째 요소까지 처리했을 때, 처음부터 `i`번째 요소까지의 부분 리스트가 정렬되어있음을 나타낸다.

<br>

### 3-2. 귀납적 증명의 구성 요소

귀납법은 다음 네 가지 단계로 이루어진다.

1. **기저 사례(Base Case):** P(0)이 참임을 보인다.
2. **귀납 가정(Inductive Hypothesis):** 임의의 자연수 k에 대해 P(k)가 참이라고 가정한다.
3. **귀납 단계(Inductive Step):** P(k)가 참일 때 P(k+1)도 참임을 보인다.
4. **종료성(Termination):** 알고리즘이 유한한 단계 내에 종료함을 보인다.

<br>

### 3-3. 귀납적 증명

#### 기저 사례

- _P(0)이 참임을 보이자._  

반복문이 시작하기 전에 리스트의 앞부분 `A[0...-1]`은 정렬되어 있다. `A[0...-1]`은 요소가 없는 빈 리스트이므로, 빈 리스트는 이미 정렬된 상태이다. 따라서 P(0)은 참이다.

<br>

#### 귀납 가정

- _P(k)가 참이라고 가정하자_

임의의 자연수 k에 대해 P(k)가 참이다. 즉, 리스트의 앞부분 `A[0...k-1]`은 정렬되어 있다.

<br>

#### 귀납 단계

- _P(k)가 참일 때, P(k+1)도 참임을 보이자._

P(k)가 참일 때, P(k+1)도 참이라는 것은 곧 `A[0...k]`이 정렬되어 있음을 보여야 하는 것! 알고리즘은 k번째 요소 `A[k]`를 고려해보자. 이미 정렬된 리스트 `A[0...k-1]`에서 `A[k]`의 적절한 위치를 찾는다. `A[k]`를 해당 위치에 삽입한다. 즉, 정렬된 리스트에 새로운 요소를 올바른 위치에 삽입하면, 리스트는 여전히 정렬된 상태를 유지한다. 따라서 `A[0...k]`은 정렬되어 있으며, P(k+1)은 참이다.

<br>

#### 종료성

- _알고리즘이 유한한 단계 내에 종료함을 보이자._

인덱스 `i`가 리스트의 길이 n보다 작을 때까지 반복한다. 각 반복마다 `i`는 1씩 증가한다. `i`가 n에 도달하면 반복문이 종료된다. 알고리즘은 유한한 단계 내에 반드시 종료한다.

<br>

#### 결론

- P(0)이 참이고, P(k) ⇒ P(k+1)이 참이므로, 수학적 귀납법에 의해 모든 자연수 n에 대해 P(n)이 참이다.
- 따라서 반복문이 종료된 후 리스트의 전체 부분 `A[0...n-1]`은 정렬되어 있다.
- 또한 알고리즘이 유한한 단계 내에 종료하므로, 알고리즘은 정확하게 동작한다.

<br>
<br>

## 4. 효율성 분석
____

### 4-1. Best Case
선택정렬에서 Best Case는 결국 이미 정렬이 된 배열이 Input으로 들어왔을 경우이다.
해당 경우의 시간복잡도를 자세하게 살펴보자.

``` C++
void insertionSort(int *arr, int N) {
	for (int i = 1; i < N; i++) {			// 1. (N - 1) Times
		int key = arr[i];					// 2. (N - 1) Times
		int j = i - 1;						// 3. (N - 1) Times
		while (j >= 0 && arr[j] > key) {	// 4. 0 Times
			arr[j + 1] = arr[j];
			j--;
		}
		arr[j + 1] = key;					// 5. (N - 1) Times
	}
}
```

1. `for (int i = 1; i < N; i++)`
	- **(N - 1)** 번 실행된다.  

2. `int key = arr[i];`
	- **(N - 1)** 번 실행된다.  

3. `int j = i - 1;`
	- **(N - 1)** 번 실행된다.  

4. `while (j >= 0 && arr[j] > key)`
	- 배열이 이미 정렬된 경우 `j`의 초기값이 -1 이므로 한번도 실행되지 않는다.  

5. `arr[j + 1] = key;`
	- **(N - 1)** 번 실행된다.  

따라서 선택정렬의 Best Case의 경우, 위 코드의 시간복잡도는 아래와 같다. 

$$
\Omega(4n - 4) ≈ O(N)
$$

<br>

### 4-2. Worst Case
선택정렬에서 Worst Case는 역순으로 정렬된 배열이 Input으로 들어왔을 경우이다.
해당 경우의 시간복잡도를 자세하게 살펴보자.

``` C++
void insertionSort(int *arr, int N) {
	for (int i = 1; i < N; i++) {			// 1. (N - 1) Times
		int key = arr[i];					// 2. (N - 1) Times
		int j = i - 1;						// 3. (N - 1) Times
		while (j >= 0 && arr[j] > key) {	// 4. 1/2 * (N - 1)N Times
			arr[j + 1] = arr[j];
			j--;
		}
		arr[j + 1] = key;					// 5. (N - 1) Times
	}
}
```

1. `for (int i = 1; i < N; i++)`
	- 여전히 **(N - 1)** 번 실행된다.  

2. `int key = arr[i];`
	- **(N - 1)** 번 실행된다.  

3. `int j = i - 1;`
	- **(N - 1)** 번 실행된다.  
	
4. `while (j >= 0 && arr[j] > key)`
	- `i`가 1 일때 `j`는 0 이고, 총 **1** 번 실행된다.
	- `i`가 2 일때 `j`는 1 이고, 총 **2** 번 실행된다.
	- `i`가 (N - 1) 일때 `j`는 (N - 2)이고, 총 **(N - 1)** 번 실행된다.
	- 결국 총 `1 + 2 + ... + (N - 1)` 의 합 만큼 실행된다.
	- 즉, $\frac{N (N - 1)}{2}$ 번 실행된다.

5. `arr[j + 1] = key;`
	- **(N - 1)** 번 실행된다.   

따라서 선택정렬의 Best Case의 경우, 위 코드의 시간복잡도는 아래와 같다. 

$$
O(\frac{1}{2}N^2 + \frac{7}{2}N - 4) ≈ O(N^2)
$$

<br>
<br>

## 5. Reference
____
- https://www.geeksforgeeks.org/insertion-sort-algorithm/
- https://medium.com/humanscape-tech/코드의-시간-복잡도-계산하기-b67dd8625966
- https://elecbrandy.github.io/posts/cs101/algorithm/bigo/

<br>
<br>