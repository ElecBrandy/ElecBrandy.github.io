+++
title = 'File descriptor'
date = 2023-12-24
featured_image = "http://db.kookje.co.kr/news2000/photo/2017/0117/L20170117.99002154311i1.jpg"
tags = ['C', '42Seoul', 'CS101']
+++

{{<freshquote>}}
운영 체제에서 파일이나 입출력 자원을 식별하기 위해 사용하는...
{{</freshquote>}}

<br>

Libft 과제에 이어 get_next_line 과제를 진행하던 중 파일 디스크립터 개념을 만나게 되어 정리했다. 결국은 파일에게 접근하기 위한 번호이자 거창하게는 파일 포인터 개념으로 이해하고 넘어가자.

<br>

# File Descripot
- 리눅스 시스템에서는 모든 것이 파일
	- 객체, 행동, 디바이스, 네트워크 연결, 디렉터리, 프로세스 등등
- 이때 시스템이 이 파일들에게 접근 시 File Descriptor라는 개념을 이용함
- File Descriptor는 Non-negative Integer 값을 가짐(0 ~ OPEN_MAX)
- Process에서 열린 파일의 목록을 관리하는 테이블의 인덱스! (0, 1, 2는 기본 할당)
- FD 테이블의 각 항목은 FD 플래그와 파일 테이블로의 포인터와 같음
- 이 포인터를 이용하여 FD 를 통해 시스템의 파일을 참조!
- Process는 이런 FD 테이블과 파일 테이블의 정보를 직접 고칠 수 없으며, 반드시 커널을 통해서 수정을 해야함
- 프로그램에서 FD 참조시 index를 사용할 수도 있고, POSIX 명을 사용할 수도 있음

<br>

# FD와 열려 있는 파일의 관계
- 각 프로세스별로 커널은 `open file descriptor table` 을 가짐
- 테이블의 각 엔트리는 하나의 FD에 대한 동작 제어 플래그, 열린 파일을 가리키는 참조를 담고 있음
- 특히 `open file descriptor table` 현재 파일의 Offset, flag, 접근 모드 ,i/o 관련 설정, 파일의 i-node 객체를 가리키는 정보를 담고 있음
- 같은 `open file descripton` 을 가르키는 2개의 FD가 Offset 값을 공유함
	- 간단한 예시
		1. `file.txt` 열고 두 개의 파일 디스크립터를 얻는다.
		2. FD1을 이용해서 파일 처음으로 이동한다. (offset 0)
		3. FD2을 이용해서 파일 어딘가로 이동한다. (offset 100)
		4. FD1과 FD2는 동일한 파일 `file.txt`를 가리키지만 FD1은 파일의 처음 / FD2는 파일의 어딘가를  가리킨다.
		5. 이렇게 동시에 한 파일을 여러 위치에서 읽고 쓸 수 있다.

<br>

# 정리
1. `file.txt` 를 열면 OS는 `open file descriptor table`에 해당 항목을 추가한다.
2. 이때 인덱스는 `file.txt` 를 가르킨다.
3. `file.txt` 를 닫으면 테이블에서 해당 인덱스가 사라진다.

<br>

# Reference
- https://www.youtube.com/watch?v=EqndHT606Tw
- https://www.youtube.com/watch?v=btT4HDKNIOw
- https://dev-ahn.tistory.com/96

<br>
{{<alert>}}
<a href="https://elecbrandy.github.io/tags/CS101"> CS101 </a>
{{</alert>}}
<br>
