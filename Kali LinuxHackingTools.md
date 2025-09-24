# Kali Linux Hacking Tools

> 본 문서는 `Kali Linux` 환경에서 `모의해킹을 진행`시 사용하는 `Hacking Tools 종류와 용도`를 정리한다.

***해당 문서를 통한 Hacking Tools는 교육 목적으로만 사용할 것을 엄격히 한다.***

<br>

**참고**: Usage의 경우, 필자가 보기 편한대로 작성한 경우도 있다.

추가적인 옵션 혹은, Usage 정식 형태를 알고 싶다면 아래 `Kali Tools Documentation` 사이트를 참고하는 것을 추천한다.

<br>

## Kali Tools Documentation
> 다양한 Kali 기반 Tools에 대하여 검색하고, 알아볼 수 있는 사이트이다.
> 
>> 사진을 클릭하면 홈페이지로 넘어간다.

[<img width="655" height="244" alt="image" src="https://github.com/user-attachments/assets/3dc288aa-14ed-4a55-8103-ecc94f2906df" />
](https://www.kali.org/tools/)

## 정보 수집
### `nmap`
네트워크 스캐너이다.

IP 주소나 도메인을 스캔하여 열려 있는 포트, 실행 중인 서비스, 운영체제 버전 등을 파악하는 데 사용된다. 이는 scan_result 파일로 저장해두면 나중에 다시 확인하기도 편하다.

**victim IP를 알면 제일 먼저 수행해야한다.**

<br>

### `gobuster`
웹사이트의 **숨겨진 디렉터리나 파일**을 찾아내는 도구이다.

사전에 정의된 목록(world list)를 brute-force 방식으로 숨겨진 페이지를 발견한다.

<br>

nmap을 통해 80포트가 발견되었다면, 습관처럼 수행한다.

<br>

### `nikto`
웹 서버 취약점 스캐너이다.

웹 서버에 알려진 취약점, 설정 오류, 위험한 파일 등을 자동으로 점검한다. 주로 오래된 서버 소프트웨어 및 문제를 검사할 때 사용하는 것으로 알고 있다.
> Usage: nikto -h http://$IP -p $PortNum

<br>

### `enum4linux`
윈도우 및 Samba 시스템의 정보를 열거하는 도구이다.

사용자 목록, 그룹 정보, 공유 폴더 등을 파악하는데 사용한다.
>Usage: enum4linux -a $IP
>
<br>

### `wget`
웹 서버에서 파일을 다운로드한다.

웹사이트 전체를 다운로드하여 로컬내 오프라인으로 분석하거나, 특정 파일을 수집하는 데 사용된다.

<br>

### `curl`
URL을 이용해 데이터를 전송하는 도구이다.

웹 서버의 응답 헤더를 확인하거나, 페이지의 소스 코드를 확인하는 등 웹 통신을 테스트할 때 유용하다.

<br>

### `PEASS-ng(linpeas.sh) -> 리다이렉션션`
리눅스 시스템에서 권한 상승에 사용될 수 있는 수많은 취약점과 설정 오류를 자동으로 점검해주는 스크립트이다.

침투 후 시스템을 빠르게 진단하는 데 필수적인 도구이다.
>Usage: 특정 포트를 열어 둔 뒤, victim이 linpeas.sh를 다운받게 하여 실행하면 attacker의 쉘에서 linpeas.sh의 결과가 출력된다.

<br>

### `find`
특정 조건을 만족하는 파일을 찾는 명령어이다.

* `find / -type f -perm -0400 -> 권한 상승 정보 수집`: SUID 권한이 설정된 파일을 찾을 수 있다. SUID 파일은 일반 사용자가 실행해도 관리자 권한으로 실행될 수 있기 때문에 중요한 권한 상승 경로가 될 수 있다.

<br>

## 취약점 분석 및 공격
### `searchsploit`
Exploit-DB를 검색하는 도구이다.

특정 소프트웨어나 버전에 대한 공개된 익스플로잇(취약점 공격 코드)을 빠르게 찾는다.
>Usage: searchsploit [options] term1 [term2] ... [termN]

<br>

### `hydra`
다양한 프로토콜에 대한 비밀번호를 brute-force 하는 도구이다.

약한 비밀번호를 찾아내는 데 사용된다.

<br>

### `ftp`
FTP 클라이언트이다.

* `ftp annonymouse@$IP`: 익명 로그인을 시도하여 설정 오류가 있는 FTP 서버에 접근하는 데 사용된다.
  
* `met`: 여러 파일을 한번에 다운로드하는 명령어이다.
  
<br>

### `wget ~ php-reverse-shell.php -> 쉘 리버싱`
`php-reverse-shell.php`를 다운로드하여 웹 서버에 업로드한 뒤, 원격에서 셀을 획득하는 데 사용한다.

<br>

### `crackstation`
>**Hacking Tool 명령어가 아닌, 온라인 서비스이다.**

해시된 비밀번호를 복호화하는 **온라인 서비스**이다. 방대한 DB를 통해 약한 비밀번호의 해시값을 빠르게 찾아낼 수 있다.

[<img width="547" height="123" alt="image" src="https://github.com/user-attachments/assets/5fecaafb-ec0d-41ff-8806-0d6ce1ffeec4" />](https://crackstation.net/)

<br>

### `GTFO Bins`
>**Hacking Tool 명령어가 아닌, 온라인 서비스이다.**

리눅스 시스템에 기본적으로 설치된 바이너리 파일들을 이용해 권한 상승, 보안 제한을 우회하는 방법을 정리해 둔 **데이터베이스**이다.

[<img width="965" height="258" alt="image" src="https://github.com/user-attachments/assets/6ff4f378-77ad-4533-af98-bc9448e85dd0" />
](https://gtfobins.github.io/)

<br>
