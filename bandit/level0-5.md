# OverTheWire: Bandit Write-up (Level 0 ~ 5)

---

## OverTheWire: Bandit Level 0 → 1

### 1. 문제 상황
- 목표: 홈 디렉터리에 있는 readme 파일을 읽어서 비밀번호를 획득해야 함.
- 현황: 리눅스 기본 명령어 사용법을 익히는 단계.

### 2. 해결 과정
- 파일의 내용을 출력하는 cat 명령어를 사용함.

### 3. 명령어
- ls              # 파일 확인
- cat readme      # 내용 출력

---
# OverTheWire: Bandit Level 1 → 2

### 1. 문제 상황
- 목표: - 라는 이름을 가진 파일 안에 있는 비밀번호를 읽어야 함.
- 트러블: cat - 명령어를 입력하면 파일이 열리는 게 아니라, 사용자의 입력을 기다리는 상태(Stdin)가 됨.

### 2. 해결 과정
- 리눅스에서 -는 종종 '표준 입력(Standard Input)'을 의미함.
- 따라서 경로를 명시적으로 지정해주어야 파일로 인식함.

### 3. 명령어
- ls -l
- cat ./-    # 현재 디렉터리(./)를 명시함

---
# OverTheWire: Bandit Level 2 → 3

### 1. 문제 상황
- 목표: spaces in this filename이라는, 공백이 포함된 파일명을 가진 파일을 읽어야 함.
- 트러블: cat spaces in this filename이라고 입력하면, cat 명령어는 공백을 구분자로 인식하여 spaces, in, this, filename이라는 4개의 파일을 각각 찾으려 시도함. 당연히 해당 파일들이 없으므로 에러 발생.

### 2. 해결 과정
- 따옴표(Quoting): 파일명 전체를 ' ' 또는 " "로 감싸기.

### 3. 명령어
- cat "spaces in this filename"

---
# OverTheWire: Bandit Level 3 → 4

### 1. 문제 상황mv
- 목표: inhere 디렉터리 내부에 있는 숨겨진 파일(hidden file)을 찾아야 함.
- 트러블: cd inhere로 디렉터리에 진입한 후 ls 명령어를 입력했으나, 아무런 파일도 출력되지 않음 (Empty directory 처럼 보임).

### 2. 해결 과정
- 리눅스 시스템에서 파일명이 .(점)으로 시작하는 파일은 기본적으로 숨김 처리됨 (예: .bashrc, .profile).
- ls 명령어에 -a (all) 옵션을 추가하여 숨김 속성의 파일까지 모두 리스팅함.

### 3. 명령어
- cd inhere
- ls -a           # .hidden 파일 발견
- cat ./.hidden

---
# OverTheWire: Bandit Level 4 → 5

### 1. 문제 상황
- 목표: inhere 디렉터리 안에 있는 여러 파일 중, 사람이 읽을 수 있는(human-readable) 유일한 파일을 찾아야 함.
- 트러블: 디렉터리 안에 -file00부터 -file09까지 다수의 파일이 존재함. 일반적인 cat 명령어로 열어보면 깨진 문자(바이너리 데이터)가 출력되어 내용을 알아볼 수 없음.

### 2. 해결 과정
- 모든 파일을 일일이 열어보는 것은 비효율적이며, 바이너리 파일을 캣(cat)할 경우 터미널이 깨질 위험이 있음.
- 파일의 실제 데이터 형식(MIME type 등)을 판별해주는 file 명령어를 사용하여 텍스트 파일(ASCII text)을 선별함.

### 3. 명령어
- cd inhere
- file ./* # 현재 디렉터리 내 모든 파일의 타입 분석
- cat ./-file07

---