+++
title = '[42cursus] pipex'
date = 2024-02-28
featured_image = "https://i.imgur.com/TqbaZ79.png"
tags = ['C', '42cursus']
+++

> Cristina: "Go dance salsa somewhere :)"

<br>

# 01 소개
42서울 본과정 입과 후 다섯번째로 수행한 과제로, UNIX 동작 원리를 프로그래밍을 통해 상세히 파헤쳐보는 과제이다.
Shell에서의 파이프를 구현하는 과제이며 Redriection, File discriptor, Process, Pipe, Fork 등을 공부할 수 있다.

<br>

# 02 `PIPEX` 명세서
## Mandatory part
- `pipex`는 `./pipex file1 cmd1 cmd2 file2` 형태로 실행된다.
- file1과 file2는 파일 이름이다.
- cmd1과 cmd2는 매개 변수가 존재하는 쉘 명령어이다.
- 프로그램은 `< file1 cmd1 | cmd2 > file2` 명령과 동일하게 작동해야 한다.
- 즉, file1을 input으로 받아 cmd1를 실행하고, 그 결과를 파이프를 통해 cmd2로 넘긴 후 output인 file2로 내보낸다.
## Bonus part
- **Bonus part**에서는 다중 파이프와 here_doc를 구현해야 한다.
- 다중 파이프
- pipex : `./pipex file1 cmd1 cmd2 cmd3 ... cmdn file2`
- bash : `< file1 cmd1 | cmd2 | cmd3 ... | cmdn > file2`
- here_doc
- pipex : `./pipex here_doc LIMITER cmd cmd1 file`
- bash : `cmd << LIMITER | cmd1 >> file`

<br>

# 03 Mandatory

## Redirection
redirection은 shell에서 입력과 출력의 흐름을 재지정하는 프로세스이다. 기본적으로 커맨드는 표준 입력(stdin), 표준 출력(stdout), 그리고 표준 에러(stderr)라는 세 가지 주요 통신 채널을 사용한다. 리디렉션을 사용하면 이러한 통신 채널을 파일이나 다른 프로그램으로 전환할 수 있다. 유닉스 및 리눅스 기반 시스템에서 매우 강력한 도구로 시스템 관리, 스크립팅 및 데이터 관리에 유용하다.

- 표준 입력 : 커맨드에 데이터를 입력하기 위한 주요 수단으로, 기본적으로 키보드에서 입력 (stdin)
- 표준 출력 : 커맨드의 결과를 출력하는 채널로, 기본적으로 터미널 화면에 결과를 표시 (stdout)
- 표준 에러 : 에러 메시지나 진단 정보를 출력하는 데 사용되며, 이것 역시 기본적으로 터미널 화면에 표시 (stderr)

``` bash
# ls 커맨드의 출력을 filelist.txt 파일로 Redirection
ls > filelist.txt

# filelist.txt 파일의 내용을 sort 커맨드의 입력으로 Redirection
sort < filelist.txt

# 에러 메시지를 errorlog.txt로 Redirection
rm somefile.txt > errorlog.txt
```

<br>

## Here Documents
Here Document(here_doc)는 커맨드에 여러 줄의 입력을 직접 제공할 수 있는 Redirection 방법이다.
``` bash
cat << EOF
여러 줄의 텍스트
EOF
```

<br>

## Pipeline
Shell에서의 파이프라인은 커맨드 간의 데이터 스트림을 연결하는 데 사용된다.
이를 통해 한 커맨드의 출력을 다른 커맨드의 입력으로 직접 전달할 수 있으며, 복잡한 데이터 처리 작업을 효율적으로 수행할 수 있다.
기본적으로 여러 커맨드를 연결하여 첫 번째 커맨드의 출력을 두 번째 커맨드의 입력으로 전달하는 메커니즘이다.

파이프라인을 통해 연결된 각 프로세스는 별도의 프로세스로 실행되며, 이들 사이에서 데이터는 파이프라인을 통해 흐른다.
각 프로세스는 데이터를 비동기적으로 읽고 쓰며, 이는 서로의 실행을 차단하고, 병렬적이다.

**병렬실행** 이란 각 커맨드가 앞 또는 뒤의 프로세스의 종료를 기다리지 않고, 독립적으로 실행되는 것을 의미한다.
각 커맨드는 실행과 동시에 파이프에 읽기와 쓰기를 진행하며, 정보는 실시간으로 이동한다.

<br>

### EOF와 SIGPIPE
#### EOF (End of File)
한 프로세스가 파이프를 통해 데이터를 보내고 모든 데이터를 전송한 후 출력 스트림을 닫으면, 다음 프로세스는 더 이상 읽을 데이터가 없다는 것을 인지할 때까지 데이터를 계속 읽는다. 마지막 데이터를 읽고 나서 다시 읽기 시도를 할 때, 시스템은 더 이상 데이터가 없다는 신호인 **EOF**를 반환한다.

**EOF**는 오류나 예외 상황이 아니고, 단지 데이터 스트림의 끝을 나타낸다. **EOF**에 도달한 프로세스는 일반적으로 데이터 처리를 마치고 종료한다.

#### SIGPIPE
한 프로세스가 더 이상 데이터를 받지 않는 파이프에 데이터를 쓰려고 할 때 발생한다. 예를 들어, 한 프로세스가 종료되거나 파이프의 읽기 측면을 닫았는데, 다른 프로세스가 여전히 그 파이프에 데이터를 쓰려고 시도하면 SIGPIPE 시그널이 발생한다. 기본적으로 SIGPIPE 시그널은 해당 프로세스를 종료시키는데, 이는 데이터를 읽을 프로세스가 없는데도 데이터를 쓰려고 시도하는 것이 무의미하기 때문이다.

#### EOF와 SIGPIPE의 차이점
- 읽기 vs 쓰기
  - EOF는 데이터를 읽는 동작과 관련이 있다. 모든 데이터를 읽고 나서 더 이상 읽을 데이터가 없을 때 발생한다.
  - 반면, SIGPIPE는 데이터를 쓰는 동작과 관련이 있다. 읽을 대상이 없는데도 데이터를 계속 쓰려고 할 때 발생한다.
- 정상 종료 vs 비정상 종료
  - EOF는 정상적인 데이터 스트림의 종료를 나타내며 오류가 아니다.
  - SIGPIPE는 예상치 못한 상황에서 발생하는 오류 상태를 나타내며 기본적으로 프로세스를 종료시킬 수 있다.

<br>

### Pipeline 예시
``` bash
cat a.txt | grep A | sort
```
1. **cat a.txt** : **a.txt** 파일의 내용을 읽어서 첫번째 파이프라인으로 출력
2. **grep A** : 첫 번째 파이프에서 데이터를 읽어들이며, 'A'가 포함된 줄만을 선택하여 두 번째 파이프라인으로 전송
3. **sort** : 두 번째 파이프에서 데이터를 읽어들여 정렬 후 터미널에 출력

각 커맨드는 실행되자마자 자신의 역할을 수행한다. `cat`은 a.txt를 읽고 파이프에 쓰고, `grep`은 파이프를 읽으며 'A'가 포함된 줄을 선택한다.
이떄 각 커맨드는 타 커맨드와 무관하게 실행되고 있으며, 설정된 파이프에서 읽어오는 행위와 쓰는 행위를 지속한다.

`cat`이 실행을 마치고 출력을 모두 첫 번째 파이프에 쓴 후, 마지막으로 **EOF**를 전달한다. 이는 `cat` 프로세스의 종료를 의미한다.
`grep`은 **EOF**를 전달받고, 더 이상 첫번째 파이프에서 읽어올 데이터가 없음을 인지한다. 이는 `grep` 프로세스의 종료를 의미한다.
`sort` 또한, 파이프에서 **EOF** 신호를 받음을 통해 두번째 파이프에서 읽어올 데이터가 없음을 인지한다. 이는 `sort` 프로세스의 종료를 의미한다.



#### SIGPIPE의 행동
- 기본적으로 `SIGPIPE` 시그널은 프로세스를 종료시키도록 설계되어 있습니다. 그러나 많은 프로그램, 특히 쉘 스크립트에서는 이 시그널을 무시하거나 적절하게 처리하여 프로세스가 갑작스럽게 종료되는 것을 방지합니다.
- `grep A`는 통상적으로 SIGPIPE 시그널을 받고 자연스럽게 종료됩니다. 이후, `sort` 명령은 두 번째 파이프로부터 정상적으로 데이터를 받아 계속해서 처리를 진행합니다.


#### 4. 시그널 처리
- `SIGPIPE`와 같은 주요 시그널의 설명
- 시그널이 발생하는 조건과 프로세스에 미치는 영향
- 실제 예시 (`yes | head -n 1` 사용 예)


이 개요는 독자들이 UNIX/Linux 시스템의 깊은 이해를 얻는 데 도움이 될 것입니다. 게시글 작성에 성공하시길 바랍니다!

`cat a.txt | grep A | sort` 명령어는 a.txt의 내용을 가져와서 A가 존재하는 행만 추출하고, 그 행들을 정렬해서 shell에 출력하는 기능을 한다.
이때 bash shell은 파이프로 구분된 각각의 명령어를 실행하기 위해 fork를 실행한다. (명령어 실행을 위한 자식 프로세스를 생성)
fork된 각각의 자식 프로세스는 **병렬 실행** 된다.

즉, `grep A`는 `cat a.txt`의 종료를 기다리지 않는다. 그저 첫번째 파이프에서 읽기를 계속 시도할 뿐이다.
cat a.txt 가 첫번째 파이프에 결과를 쓰는 것을 기다리지 않고 grep A는 프로세스가 시작하자마자 그냥 읽기만을 계속 시도할 뿐이다.
그러다가 cat a.txt가 다 쓰고 파이프 쓰기 쪽을 닫으면, SIGPIPE 시그널이 전달되면서 grep A는 파이프 한쪽이 막힌 것을 알게된다.
이것은 곧 grep 입장에서 앞 프로세스의 종료가 된 것이다. 그럼 그 파이프는 SIG를 통해 닫히게 된다.

파이프라인을 통해 연결된 프로세스 간의 데이터 흐름과 관련된 시그널 처리, 특히 `SIGPIPE`에 대한 이해를 더 깊이 있게 다루겠습니다. 여기서는 `cat a.txt | grep A | sort` 명령을 계속해서 분석해보겠습니다.


`cat a.txt | grep A | sort` 에서 cat a.txt가 무사히 끝나면, eof가 전달되고 grep A가 종료되는건가? 혹은 SIGPIPE가 발생하는건가?



### 결론

파이프라인에서는 각 커맨드가 독립적으로 데이터를 처리하면서도 서로의 출력과 입력을 연결하여 복잡한 작업을 효율적으로 수행할 수 있습니다.
`SIGPIPE`와 같은 시그널 처리는 프로세스가 데이터의 끝에 도달했을 때 안정적으로 종료될 수 있도록 돕습니다.
이 모든 과정은 리소스의 효율적 사용과 빠른 데이터 처리를 가능하게 합니다, 특히 대용량 데이터를 다룰 때

 매우 유용합니다.




<br>

## Mandatory 로직

### 1. 사전 최적화
결국 숫자를 순서대로 정렬하는 것이고, 3진수로 변환해야하기 때문에 인자의 크기는 작을수록 좋다.
인자의 크기가 커질수록 3진수로 변환된 길이는 길어질 것이고, 그만큼 비교-정렬 과정이 길어질 것이다.
그렇기 때문에 어떤 정수의 집합이 들어와도, 그것을 0부터 시작하는 집합으로 인덱싱 과정이 필요하다.

만약 `123, 38, 234, -231, 0, 23, 11` 이 인자로 들어왔다면, `5, 4, 6, 0, 1, 3, 2` 로 바꾸어주자!

<br>

### 2. Hard sorting
인자가 5개 이하일 경우 과제 요구사항을 충족하기 위해 나름의 하드 코딩을 진행했다. 우선 n-1 개를 정렬하고, 나머지 하나를 추가하는 방식으로 정렬했다.

<br>

### 3. 기수정렬

<img src="https://i.imgur.com/yiREh79.png" width="700">

#### step 01
기수 0인 인자는 스**택 B**의 상위로, 기수가 1인 인자는 **스택 A**의 하위로, 기수가 2인 인자는 **스택 B**의 하위로 보내준다.

#### step 02
0, 1, 2 기수에 따라 분류가 완료되었다면, 이제 다음 자리수의 기수 비교를 위해 나눠진 인자들을 다시 한 공간에 모아야 한다.
흩어진 인자를 **스택 A**에 다시 모아주자. 이때 순서는 기수 0, 기수 1, 기수 2 순으로 유지한다. **스택 B**의 상위에 모아두었던 기수 0 모음을 **스택 A**의 상위로 옮겨주자.

#### step 03
그렇다면 현재 **스택 A**에 기수 0 모음, 기수 1 모음 순으로 정렬되어 있을 것이다. 이제 나머지 기수 2 모음을 **스택 A**의 하위로 옮겨주자.
최종적으로 **스택 A**에 기수 순으로 정렬이 완료 되었다. 이제 다음 자릿수 비교를 반복하자. 다시 step 1로!

<br>

## Mandatory 구현
### hard sort
``` c
void	ps_hardsort(t_head *head, int len)
{
	if (len == 2)
		ps_hardsort_2(head);
	else if (len == 3)
		ps_hardsort_3(head);
	else if (len == 4)
		ps_hardsort_4(head);
	else if (len == 5)
		ps_hardsort_5(head);
}
```

<br>

### 기수정렬
``` c
void	ps_radixsort(t_head *head, int len, int trlen)
{
	int		i;
	int		j;
	int		cnt_0;
	int		cnt_2;
	t_node	*a_cur;

	if (!(head->a) || !(head->b))
		return ;
	j = trlen - 1;
	while (j >= 0)
	{
		a_cur = head->a->next;
		cnt_0 = 0;
		cnt_2 = 0;
		i = 0;
		while (i < len && a_cur && j >= 0)
		{
			ps_div_radix(a_cur->num_tr[j], head, &cnt_0, &cnt_2);
			a_cur = head->a->next;
			i++;
		}
		ps_merge_0(head, cnt_0);
		ps_merge_2(head, cnt_2);
		j--;
	}
}
```

기수에 따라 나누고 (`ps_div_radix`), 다시 합치는 (`ps_merge_n`) 과정에서 명령어 구성은 사실상 변하지 않는다.
이때 0그룹과 1그룹의 위치를 어디에 놓아야 명령어 갯수가 최적화 되는지 다양하게 살펴보자. 0부터 1000까지 정수를 3진수로 변환했을때
각각 기수 0의 갯수, 1의 갯수, 2의 갯수를 카운트하는 것도 도움이 될 수 있다.

<br>

### push_swap
``` c
int	main(int ac, char **av)
{
	t_input	*input;
	t_head	*head;

	if (ac == 0 || ac == 1)
		return (0);
	head = init_head();
	input = init_input();
	if (!head || !input)
	{
		ps_free_all(&input, &head);
		return (0);
	}
	input->arr = ps_set_input((const char **)av, input);
	if (!(input->arr))
	{
		ps_free_all(&input, &head);
		return (0);
	}
	ps_set_stack(&(head->a), input);
	if (2 <= input->len && input->len <= 5)
		ps_hardsort(head, input->len);
	else
		ps_radixsort(head, input->len, input->trlen);
	ps_free_all(&input, &head);
	return (0);
}
```

<br>

# 05 Reference
- [wiki] https://buly.kr/6tZknhQ
- [tistory] https://buly.kr/CshW9Qu
- [velog] https://velog.io/@blank_/PushSwap-Radix-Sort

<br>
{{<alert>}}
<a href="https://elecbrandy.github.io/tags/42cursus/"> 42cursus</a>
{{</alert>}}
<br>
