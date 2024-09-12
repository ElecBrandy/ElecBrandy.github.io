+++
title = '[자료구조] Stack'
date = 2024-09-01
featured_image = "https://image.fnnews.com/resource/media/image/2012/11/04/201211041607219413_l.jpg"
tags = ['C++', 'dataStructure', 'CS101']
+++

draft = true

**📂 : 자료구조**
____
> <a href="https://elecbrandy.github.io/tags/dataStructure/list"> **[0]** List </a>  

> <a href="https://elecbrandy.github.io/tags/dataStructure/stack"> **[1]** Stack </a>  

> <a href="https://elecbrandy.github.io/tags/dataStructure/queue"> **[2]** Queue </a>  

> <a href="https://elecbrandy.github.io/tags/dataStructure/heap"> **[3]** Heap </a>  

> <a href="https://elecbrandy.github.io/tags/dataStructure/tree"> **[4]** Tree </a>  

> <a href="https://elecbrandy.github.io/tags/dataStructure/graph"> **[5]** Graph </a>  

> <a href="https://elecbrandy.github.io/tags/dataStructure/hashtable"> **[6]** HashTable </a>  


<br>
<br>

# Stack
____
<img src="https://media.geeksforgeeks.org/wp-content/uploads/20240606180735/Stack-representation-in-Data-Structures-(1).webp" width="700">

Stack은 **LIFO(Last in, First Out)** 방식으로 작동하는 선형 자료구조이다. 즉, 가장 마지막에 삽입된 데이터가 가장 먼저 삭제되는 구조인 것! Stack의 입/출력은 오로지 Stack의 꼭대기에서만 이루어진다. 갑자기 중간에 있는 데이터를 삭제하거나 끼워넣을 수 없으며, 맨 아래 데이터에 접근하기 위해서는 그 위에 있는 데이터를 모두 걷어내야한다.

<br>
<br>

# Stack의 연산
____
Stack의 주요 기능은 **삽입(push)**과 **제거(pop)**가 존재한다. 나머지는 두 연산을 위한 보조 연산이라고 생각하면 된다.

<br>
<br>

# Stack의 구현
____
여러 환경에서 이미 Stack은 구현되어있는 자료구조이지만, 공부를 위해서 직접 구현해보자. 일단은 Array로 구현해보고, `C++`의 STL에 속한 Stack 자료구조 사용법을 익혀보자.

## Array로 구현하는 Stack

### 기본구조

``` C
// Stack의 노드 하나
typedef struct s_node {
	int data;
} t_node;

// 스택 자료구조
typedef struct s_stack {
	int capacity;   // 해당 Stack이 얼마만큼의 노드를 가질 수 있는지 알기 위해
	int top;		// 삽입, 제거 연산 시 필요한 최상위 노드의 위치
	t_node *nodes;	// 노드 배열
} t_stack;
```

Stack을 배열로 구현할 경우 용량, 최상위 노드의 위치, 노드 배열 이렇게 세 가지 정보를 가지고 있어야 한다. 이제 이어서 Stack을 할당하고 해제하는 함수를 만들어보자.

<br>

### 할당과 해제
``` C
void create_stack(t_stack **stack, int capacity) {
	(*stack) = (t_stack *a)malloc(sizeof(t_stack));				// 1. Stack을 할당하고
	(*stack)->nodes = (node *)malloc(sizeof(node) * capacity) 	// 2. size 만큼의 노드를 추가
	(*stack)->capacity = capacity;								// 3. capacity 초기화
	(*stack)->top = -1;											// 4. top 초기화
}

void free_stack(t_stack *stack) {
	free(stack->nodes);
	free(stack);
}
```

이때 컴퓨터 배열 인덱스 상 첫번쨰 요소를 0으로 사용하므로, 비어있는 Stack의 최상위는 이보다 작아야한다. 따라서 `top`을 `-1`로 초기화한다. 이어서 삽입과 제거 연산도 만들어보자.

<br>

### 각종 연산
``` C
void do_push(t_stack *stack, int insert_data) {
	stack->top++;									// 1. 최상위 인덱스 업데이트 (증가)
	stack->nodes[stack->top].data = insert_data;	// 2. 업데이트 된 최상위 인덱스의 노드에 data 입력
}

int do_pop(t_stack *stack) {
	int tmp = stack->top--; 						// 1. 최상위 인덱스 업데이트 (감소)
	return stack->nodes[tmp].data; 					// 2. pop, 즉 Stack에서 제거한 노드의 data를 호출자에게 반환해주자.
}
```

이 외에도 Stack이 비어있는지 검사하거나, 최상위 노드 데이터만 반환하는 함수 등을 만들어 사용할 수 있다.

<br>
<br>

## STL::STACK
--> 여기서부터 

# Reference
____
- https://www.geeksforgeeks.org/stack-data-structure/

<br>
{{<alert>}}
<a href="https://elecbrandy.github.io/tags/dataStructure"> 자료구조 </a>
{{</alert>}}
<br>
