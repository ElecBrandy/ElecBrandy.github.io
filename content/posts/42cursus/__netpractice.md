+++
title = '[42cursus] get_next_line'
date = 2024-08-15
featured_image = "http://t1.daumcdn.net/cfile/146F1B10ACE3228B52"
tags = ['C', '42cursus']
draft = true
+++

> This document is a System Administration related exercise.

<br>

# 소개
____
<img src="https://imgur.com/2R4ZmUq.png" width="700">

42서울 본과정 입과 후 n번째로 수행한 과제로, 교육 인터페이스 상에서 소규모 네트워크를 구성하는 과제이다. 네트워킹 문제를 해결하여 네트워크가 실행되도록 만들어야한다. 총 10개의 문제를 완료하고, Git 저장소에 제출하는 것이 최종 목표이다.

<br>
<br>

# 개념정리
___
## 네트워크란?
네트워크는 두 대 이상의 컴퓨터 시스템이 서로 연결되어 데이터를 교환할 수 있는 구조를 의미한다. 이 연결은 다양한 방법으로 이루어질 수 있으며, 물리적 케이블, 무선 신호, 또는 혼합된 방식으로 네트워크를 구성할 수 있다.

<br>

## 네트워크의 구성요소
### 서버
네트워크에서 데이터를 저장하고, 관리하며, 이를 클라이언트에게 제공하는 역할을 수행한다. 예를 들어, 파일 서버는 파일을 저장하고 이를 필요로 하는 클라이언트에 제공하는 역할을 수행한다.

### 클라이언트
클라이언트는 서버에 요청을 보내고, 서버로부터 데이터를 수신하는 장치로, 클라이언트는 개인용 컴퓨터, 스마트폰, 태블릿 등이 될 수 있다.

### 라우터 (Router)
라우터는 네트워크 간에 데이터 패킷을 전송하는 장치로, 라우터는 IP 주소를 기반으로 데이터를 목적지까지 전달하며, 인터넷 연결에서 중요한 역할을 한다.

### 스위치
동일한 네트워크 내에서 장치 간에 데이터를 전송하는 장치로, 각 장치의 MAC 주소를 기반으로 데이터를 올바른 장치로 전달한다.

### 호스트
네트워크에서 **호스트**는 네트워크에 연결된 모든 장치를 의미히며, 여기에는 서버, 데스크탑 컴퓨터, 노트북, 스마트폰 등이 포함된다.

<br>

## OSI 7계층 모델
<img src="https://cf-assets.www.cloudflare.com/slt3lc6tev37/6ZH2Etm3LlFHTgmkjLmkxp/59ff240fb3ebdc7794ffaa6e1d69b7c2/osi_model_7_layers.png" width="700">
OSI(Open Systems Interconnextion) 모델은 네트워크 통신을 계층적으로 나누어 설명하는 표준화된 모델이다. 이는 통신 시스템의 다양한 기능을 7개의 계층으로 나누어 설명하며 네트워크 설계 및 문제 해결을 용이하게 한다. 현대 인터넷에서는 OSI 모델을 엄격하게 따르고 있지는 않지만, 이러한 유형의 구분은 네트워크 문제를 해결하는데 우용하다.

### 1. 물리 계층 : The physical layer
<img src="https://cf-assets.www.cloudflare.com/slt3lc6tev37/1HQ1W5P4XAinIdM37DTu4U/900ccdceda346baf03ce8b9f977d2974/osi_model_physical_layer_1.png" width="700">

상위 계층에서 전달된 데이터를 물리적 매체를 통해 전송할 수 있는 형태(Bit 형태)로 변환하여 전송한다. 즉, 베이터를 비트 스트림으로 나누어 전송하고, 수신 측에서 다시 원래 데이터로 재조립하는 것!  

결국 기계어를

단순히 데이터를 전달하는 역할만 수행하며, 내용은 관여하지 않는다. 에러 검출이나 신뢰성에 대해 무관심하고 알 수도 없다.  

네트워크 어댑터, 리피터와 허브, 케이블, 커넥터 등이 존재한다.

### 2. 데이터 연결 계층 : The Data Link Layer
<img src="https://cf-assets.www.cloudflare.com/slt3lc6tev37/3TLHavXiotb9ayyZFKECf3/9456d1c431cd71ceea7f4b407f076f11/data_link_layer_osi_model.png" width="700">

상위 계층에서 전달받은 데이터를 받아 Frame(물리 계층을 통해 전송할 수 있는 데이터 패킷)으로 나누고, 각 프레임에는 시작과 끝을 알리는 특정 비트 패턴과 제어 정보를 포함하여 데이터의 구분/무결성을 유지한다. 주로 CRC(Cyclic Redundancy Chec : 패리티 비트 또는 체크섬 방식 등)을 사용한다.  

오류 검출 기능은 수행하지만, 오류 수정은 또 다른 문제... 발견된 오류는 상위 계층에서 수정되거나 재전송을 요구할 수 있다.  

프레임을 전송할때 각 네트워크 인터페이스에 할당된 고유의 물리적 주소인 MAC 주소를 사용한다.  

송신자와 수신자의 데이터 처리 속도가 다를 경우 문제 방지를 흐름제어를 수행하며 
결과적으로 데이터가 수신자에게 너무 빠르게 전달되어 버퍼 오퍼플로우가 발생하는 것을 막는다.

네트워크 인터페이스 카드, 스위치, 브리지 등이 존재한다.

### 3. 데이터 연결 계층 : The Data Link Layer
<img src="https://cf-assets.www.cloudflare.com/slt3lc6tev37/3g2Hv0frHsql5SFauJL5EG/d8cede7b6a780e63413bd86de9eee7f9/osi_model_network_layer_3.png" width="700">

상위 계층에서 전달받은 데이터를 받아 Frame(물리 계층을 통해 전송할 수 있는 데이터 패킷)으로 나누고, 각 프레임에는 시작과 끝을 알리는 특정 비트 패턴과 제어 정보를 포함하여 데이터의 구분/무결성을 유지한다. 주로 CRC(Cyclic Redundancy Chec : 패리티 비트 또는 체크섬 방식 등)을 사용한다.  

오류 검출 기능은 수행하지만, 오류 수정은 또 다른 문제... 발견된 오류는 상위 계층에서 수정되거나 재전송을 요구할 수 있다.  

프레임을 전송할때 각 네트워크 인터페이스에 할당된 고유의 물리적 주소인 MAC 주소를 사용한다.  

송신자와 수신자의 데이터 처리 속도가 다를 경우 문제 방지를 흐름제어를 수행하며 
결과적으로 데이터가 수신자에게 너무 빠르게 전달되어 버퍼 오퍼플로우가 발생하는 것을 막는다.

### 3.

### 4.

### 5.

### 6.

### 7.


<br>

## 003


<br>
<br>

# Mandatory
____

<img src="https://imgur.com/w1qsdXS.png" width="700">

<br>

### step 01
개행문자 `\n`을 만날 때까지 또는 `EOF`에 도달할 때까지 **BUFFER_SIZE** 만큼 읽어나간다.

### step 02
개행문자 전까지만 따로 분리해서 return을 준비한다.

### step 03
추후 **BUFFER_SIZE** 실행을 위해서 `main_buf`에 나머지 남은 문자열을 저장한다.

<br>

## Mandatory 구현

### get_next_line

``` c
char	*get_next_line(int fd)
{
	static char	*main_buf;
	char		*line;

	line = NULL;
	if (fd < 0 || BUFFER_SIZE <= 0)
		return (NULL);
	main_buf = read_line_fd(fd, main_buf); // step 01
	if (!main_buf)
		return (NULL);
	line = extract_line(main_buf); // step 02
	if (!line)
		return (ft_free(&main_buf));
	main_buf = ready_main_buf(main_buf); // step 03
	return (line);
}
```
`main_buf` 변수는 **static** 변수이기 때문에, 프로그램 실행 시 단 한 번 초기화 되며, 이후에 `get_next_line` 함수가 호출되어도 초기화되지 않고 이전에 저장된 값을 유지한다.

`read_line_fd`를 통해 개행 또는 EOF 전까지 **BUFFER_SIZE**만큼씩 파일을 읽는다.

읽은 내용은 `main_buf`에 저장되며, `extrac_line` 함수를 통해 개행 전까지 잘라 `line`에 담는다.

`read_main_buf`를 통해 `main_buf`를 정리(잘라낸 line을 없애고 나머지만 남김)하여 다음 호출을 대비한다.

<br>
<br>

# BONUS
____

본 과제를 **BONUS**까지 수행한다면,
위에서 언급한 것 처럼 우리가 만든 `get_next_line`이 여러 파일 디스트립터를 관리할 수 있어야 한다. 쉽게 말하면, **a.txt**를 읽다가 갑자기 **b.txt**을 읽을 수 있어야 한다는 것! 다시 **a.txt**로 읽기 위해 돌아갔을 때, 그 전에 어디까지 읽었는지 당연히 기억하고 있어야 한다.

아래는 `OPEN_MAX`를 통해 디스크립터 테이블만큼 배열을 만들어 정적 배열을 통해 여러 파일 디스크립터를 다루는 방법이다.

<br>

## BONUS 구현
``` c
char	*get_next_line(int fd)
{
	static char	*main_buf[OPEN_MAX];
	char		*line;

	line = NULL;
	if (fd < 0 || BUFFER_SIZE <= 0)
		return (NULL);
	main_buf[fd] = read_line_fd(fd, main_buf[fd]);
	if (!main_buf[fd])
		return (NULL);
	line = extract_line(main_buf[fd]);
	if (!line)
		return (ft_free(&(main_buf[fd])));
	main_buf[fd] = ready_main_buf(main_buf[fd]);
	return (line);
}
```

<br>
<br>

# Reference
____
- https://man7.org/linux/man-pages/man2/read.2.html
- https://www.gnu.org/software/libc/manual/html_node/Streams-and-File-Descriptors.html
- https://code-lab1.tistory.com/65


<br>
{{<alert>}}
<a href="https://elecbrandy.github.io/tags/42cursus"> 42cursus </a>
{{</alert>}}
<br>


https://www.cloudflare.com/ko-kr/learning/ddos/glossary/open-systems-interconnection-model-osi/