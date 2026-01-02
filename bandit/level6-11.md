# OverTheWire: Bandit Wargame Write-up (Level 6 → 11)

---

## OverTheWire: Bandit Level 5 → 6

### 1. 문제 상황

 - 목표: inhere 디렉터리 어딘가에 있는 파일 속에 비밀번호가 있음.

 - 조건:

    * 사람이 읽을 수 있는 파일 (human-readable)

    * 파일 크기가 정확히 1033 bytes

    * 실행 불가능한 파일 (not executable)

 - 현황: inhere 디렉터리 안에 수많은 하위 디렉터리와 파일들이 존재하여 일일이 찾기 어려움.

### 2. 해결 과정  

 - 파일의 속성(크기, 실행 권한 등)을 기반으로 검색할 수 있는 find 명령어를 사용함.

 - ! 연산자를 사용하여 "실행 권한이 없는" 조건을 표현.

 - 설명:
    * -type f      : 디렉터리가 아닌 일반 파일만 검색
    * -size 1033c  : 크기가 1033 바이트인 파일 (c=byte)
    * ! -executable : 실행 권한이 없는 파일 (NOT executable)

### 3. 명령어

 - find ./inhere -type f -size 1033c ! -executable

 - 결과: 조건에 맞는 유일한 파일이 출력됨. 해당 파일을 cat으로 읽거나 file 명령어로 텍스트 파일인지 재확인 후 비밀번호 획득.

---
## OverTheWire: Bandit Level 6 → 7

### 1. 문제 상황

 - 목표: 서버 어딘가에(somewhere on the server) 저장된 비밀번호 파일을 찾아야 함.

 - 조건:

    * 파일 소유자(User)는 bandit7

    * 소유 그룹(Group)은 bandit6

    * 파일 크기(Size)는 정확히 33 bytes

 - 난관: 현재 디렉터리가 아니라 / (루트) 전체를 뒤져야 하는데, 권한이 없는 디렉터리를 검색하면 Permission denied 에러가 쏟아져서 결과를 볼 수 없음.

### 2. 해결 과정  

 - find 명령어의 조건 검색 기능을 조합하고, Standard Error(표준 에러)를 블랙홀(/dev/null)로 버리는 테크닉을 사용함.

 - 설명:
    * /            : 루트 디렉터리부터 검색
    * -user ...    : 소유자 조건
    * -group ...   : 그룹 조건
    * -size 33c    : 크기 조건 (c는 byte 단위)
    * 2>/dev/null  : 에러 메시지(2번 스트림)는 휴지통으로 버림 (깔끔한 출력을 위해 필수)

### 3. 명령어 

 - find / -user bandit7 -group bandit6 -size 33c 2>/dev/null

 - 결과: 유일하게 출력된 파일 경로를 cat으로 읽어서 비밀번호 획득.

---
# OverTheWire: Bandit Level 7 → 8

### 1. 문제 상황 

 - 목표: data.txt 파일 안에서 millionth라는 단어 바로 옆에 있는 비밀번호를 찾아야 함.

 - 현황: data.txt 파일이 매우 커서 그냥 cat으로 열면 찾기 힘듦.

### 2. 해결 과정  

 - 파일 내에서 특정 문자열을 검색하는 grep 명령어를 사용.

 - 운영 업무 시 거대한 로그 파일에서 특정 Error Code를 찾을 때 쓰는 핵심 기술.

### 3. 명령어

 - grep "millionth" data.txt

---
# OverTheWire: Bandit Level 8 → 9

### 1. 문제 상황 

 - 목표: data.txt 파일 내에서 "유일하게 중복되지 않는(occur only once)" 줄을 찾아야 함.

 - 현황: 파일 내에 수많은 데이터가 있고, 대부분의 라인이 반복되고 있음.

### 2. 해결 과정  

 - 중복을 제거하는 uniq 명령어를 써야 하는데, uniq는 인접한 줄만 비교하므로 반드시 정렬(sort)이 선행되어야 함.

 - 파이프라인(|)을 사용하여 명령어를 연결함.

### 3. 명령어
 - sort data.txt | uniq -u

 - 옵션 설명:

 - sort: 데이터를 가나다순으로 정렬 (같은 줄끼리 붙게 만듦).

 - uniq -u: 중복되지 않는 유일한(unique) 라인만 출력.

---
# OverTheWire: Bandit Level 9 → 10

### 1. 문제 상황 

 - 목표: data.txt 파일에서 사람이 읽을 수 있는 문자열(human-readable strings) 중, = 문자가 여러 개(====) 나오는 부분 뒤에 비밀번호가 있음.

 - 현황: data.txt가 바이너리 파일이라 cat이나 grep을 바로 쓰면 깨진 문자가 나오거나 매칭이 안 됨.

### 2. 해결 과정  

 - 바이너리 파일에서 텍스트만 추출하는 strings 명령어 사용.

 - 추출된 텍스트 중에서 grep으로 원하는 패턴 검색.

### 3. 명령어

 - strings data.txt | grep "===="

 - 운영 팁: 실행 파일이나 덤프 파일 안에 하드코딩된 IP나 URL을 찾을 때 strings가 매우 유용함.

---
# OverTheWire: Bandit Level 10 → 11

### 1. 문제 상황 

 - 목표: data.txt 파일에 저장된 비밀번호를 찾아야 하는데, 내용이 Base64로 인코딩(Encoded)되어 있음.

 - 현황: 파일을 열어보면 VGhlIHBhc3N3... 같이 알 수 없는 영문자/숫자 조합만 보임.

### 2. 해결 과정  

 - 리눅스 기본 명령어인 base64를 사용하여 디코딩(Decoding) 수행.

### 3. 명령어
 - cat data.txt            # 내용 확인 (인코딩된 문자열)
 - base64 -d data.txt      # -d (decode) 옵션으로 복호화

- 핵심 개념: Base64는 암호화가 아니라 인코딩임. 인증 토큰이나 이미지 데이터를 텍스트로 전송할 때 주로 사용됨.

---