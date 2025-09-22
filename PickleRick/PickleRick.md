# TryHackMe: Pickle Rick

[<img width="638" height="157" alt="image" src="https://github.com/user-attachments/assets/8d96d369-0dd8-4471-a80f-a513d0d57a4a" />](https://tryhackme.com/room/picklerick
)

# 1. 개요
>This Rick and Morty-themed challenge requires you to exploit a web server and find three ingredients to help Rick make his potion and transform himself back into a human from a pickle.
>
>* What is the first ingredient that Rick needs?
>
>* What is the second ingredient in Rick’s potion?
>
>* What is the last and final ingredient?

해당 문제는 포션 제조를 위한 재료 3가지를 구하는 게 목표이다!
문제에서 언급한 것과 같이 web 서버 탐색을 하는데에 중점을 둔 문제이다.

<br>

# 2. 풀이

## 1. 정보수집

### [`nmap` 스캐닝]
`nmap` 스캐닝을 통해 열린 포트를 확인한다. 당시에 tcp 포트와 80포트가 열려있음을 확인했다. 

ssh 혹은 ftp 포트는 열리지 않은 것을 보아, 확실히 웹 서버 단에서 해결하는 문제로 예상된다.

<br>

### [`curl` | username 발견]

victim IP를 `curl`뿐만 아니라 브라우저에서 `View Page Source`를 사용해 해당 웹의 페이지 소스를 확인할 수 있다.

페이지 소스를 확인한 결과, 사이트 상에서 보이지 않게 작성해둔 `username`을 확인한다.

<br>

### [`gobuster` 탐색]
  
숨겨진 db를 탐색한다. `/assets`가 발견되자마자 탐색을 중단하고 해당 db로 접속한다.

<br>

### [`wget`]

`/assets`을 통째로 로컬에 다운 받는다.

다운 받은 파일을 확인하면 웹에서 보이지 않은 파일 `robot.txt`를 발견할 수 있다. 이를 통해 계정의 비밀번호까지 탈취가 가능해졌다.

<br>

### [`nikto` 탐색]

>`Nikto`는 웹 서버에서 취약한 파일과 공통 게이트웨이 인터페이스, 오래된 서버 소프트웨어 및 기타 문제를 검사할때 사용하는 자유 소프트웨어 명령줄 취약점 스캐너이다. 일반 검사와 서버 유형별 검사를 수행할 수 있으며 수신된 쿠키를 캡처하고 저장한다.
>
>_위키 백과_

웹서버단에서 숨겨진 `login.php`를 발견해 접속한다.

해당 페이지에서 `username`과 `password`를 얻은 정보를 통해 기입하면 접속이 가능해진다.

<br>

## 2. Command Panel

### [`portal.php`]

로그인에 성공하면 `portal.php`로 접속하게 된다.

이때 커맨드 패널에 여러가지 명령어를 작성해보면 먹히는게 있고, 아닌게 있다. 안되는 명령어는 disabled command라며 거절당한다.

그리고 potion 등 여러가지 카테고리가 있는데, 커맨드 패널을 제외한 모든 카테고리는 **REAL** rick만 접근이 가능하다고 뜬다. 아마 rick의 계정이 root급의 권한이 아닐까 생각된다.

<br>

### [`sudo -l`]

`whoami` 를 입력하면 `www-data` 가 나온다. 즉, 높은 권한의 계정은 아니다.

`sudo -l`을 입력하니, 예상외로 모든 계정은 비밀번호가 필요없음을 확인할 수 있다. 이 점을 통해 리버스 쉘 혹은 더이상의 계정 탈취는 필요하지 않다고 예상하였다.

<br>

## 3. Ingredients

### [first ingredient]

커맨드 패널에 냅다 `ls`를 입력한다.

html 디렉토리 하위의 파일들이 확인된다.

그 중, 수상해보이는 txt 파일을 열어보니 첫번째 재료를 쉽게 얻을 수 있었다.

첫번째 재료를 구한 방식을 고려하면, 나머지 두번째와 세번째의 재료들 또한 커맨드 패널에서 적절히 `ls`로 탐색하는 방식으로 예측된다.

<br>

### [`ls -la`]

모든 디렉토리를 확인한다.
rick 혹은 root 와 같이 중요해 보이는 폴더명이 있는지 확인한다.

<br>

### [second ingredient]
이때 `/home/rick` 폴더가 유용해 보여 확인해본다. 그럼 `second ingredients`라는 파일명을 발견할 수 있다.

`sudo less` 명령어를 사용해 해당 파일을 읽으면 두번째 재료를 구할 수 있다.

이때 `sudo less` 명령어를 바로 사용할 수 있는 이유는 `sudo -l`을 통해 sudo 권한을 위한 여분의 계정 상승이 필요하지 않음을 알기 때문이다.

<br>

### [last ingredient]

`ls -la` 입력시 `root` 폴더도 보였기에, 해당 폴더도 확인한다.

예상처럼 `3rd.txt` 파일이 발견되어 해당 파일 또한 `sudo less`로 읽을 수 있다.


***이로써 3가지 재료를 모두 찾아 문제를 해결하였다.***

<br>

# 3. 총평
Easy 단계이지만, 초반에 감만 잡으면 상당히 쉬운 문제였을 듯 싶다. 리버스 쉘을 사용하는 등의 다른 기술이 필요하지 않고, 정말 문제 설명에 써있는 것과 같이 '웹서버 탐색'을 열심히 하면 된다.

처음 혼자 풀어본 문제 치고는 명령어 등 기본기를 다지기 좋은 문제였다.

그리고 약간의 여담을 더하자면,

<br>

### [rabbit hole]
로그인하고 바로 뜬 **portal.php** 에서, 이전에 `view page source`를 통해 비밀번호를 알아냈던 것을 떠올려 마찬가지로 페이지 소스를 탐색했다.
이때 또한 페이지 소스에서만 확인 가능한 암호가 발견되었다.

<br>
난 Gemini의 도움을 받으며 문제를 풀고 있었기에, 곧바로 이게 해쉬 분석기로 분석해야할지, word list로 brute 탐색을 할지 물어보았다.

Gemini는 단순한 디코딩으로도 암호를 풀수 있다하였고...

난 철썩같이 믿으며 계속해서 사람이 읽을 수 있는 글자가 나올 때 까지 디코딩하였다....

<br>
결국 수 번의 디코딩 끝에 얻은 글자는

<br>

**`rabbit hole`**

이었다.

<br>
이전에 강의 시간 때 문제를 풀 때에는, 교수님께서 가이드를 주며 ppt를 따라갔기에 try hack me 문제에 이런 유쾌한 함정이 있는지 이번에 처음 알았다....
