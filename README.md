# 시스템 프로그래밍 실습 2 - 2 과제

### 이름 : 이준휘

### 학번 : 2018202046

### 교수 : 최상호 교수님

### 강의 시간 : 화

### 실습 분반 : 목 7 , 8


## 1. Introduction

```
해당 과제는 Proxy server에서 server와 client 간의 기본적인 연결을 구현하게 된다.
socket을 통해 다중의 client를 수용할 수 있는 서버를 구현한다. 해당 과제에서는 이전
2 - 1 과 달리 firefox가 client가 된다. firefox로부터 URL 요청을 받을 경우 해당 Message를
분석하여 url을 추출한다. 추출한 url을 바탕으로 이전 1 과제에서 만든 내용을 수행하고,
HIT 또는 Miss의 상태에 따라 HTML message를 작성하여 client에게 보낸다.
```
## 2. Flow Chart

- proxy_cache.c



## 3. Pseudo Code

```
static void handler(){
pid_t pid;
int status;
Wait Any child with WNOHANG
```
}

Make_Cache_Dir_Log_File(char* cache_dir, char* log_file){

getHomeDirectory();

cache_dir = ~/cache;

log_file = ~/logfile;


set umask 000;

make cache and log directory;

log_file += /logfile.txt;

}

```
Check_Exist_File(char *path, char *file_name, int is_exist_file){
if(directory isn’t exist)
return 0;
```
```
DIR *dir = Open path directory
While(struct dirent *d = Read path directory){
If(d->name == file name)
Close directory and return 1;
}
Close directory and return 0;
```
}

```
Void Write_Log_File(File *log_file, char *input_url,
char *hashed_url_dir, char* hashed_url_file, int is_exist_file){
time_t now;
struct tm *ltp;
```
```
ltp = current local time;
if(miss state)
Write miss, input_url, local time in log_file;
Else
```

```
Write hit, hashed dir/file, local time, input_url in log_file;
```
}

void Check_Cache(char *url, char *cache_dir, char *log_file, int current_pid, int *hit, int *miss){

```
char[60] hashed_url;
char[4] first_dir;
char *dir_div;
char[100] temp_dir;
int is_exist_file = 1;
File *temp_file;
```
```
hashed_url = hashed url using sha1;
first_dir = { hashed_url[0~3], ‘\0’ };
dir_div = hashed_url + 3 address
temp_dir = ~/cache/first_dir;
if make temp_dir directory(permission = drwxrwxrwx)
is_exist_file = 0;
is_exist_file = hit or miss state;
Write state, url, dir/file name, time in logfile.txt;
temp_dir = ~/cache/first_dir/dir_div;
temp_file = make temp_dir file and open file;
temp_file close;
```
return is_exist_file;

}

void Sub_Process_Work(int client_fd, struct sock_addr, char *buf, char *char_dir, FILE


*log_file){

```
char response_header[BUFFSIZE] = { 0 };
char response_message[BUFFSIZE] = { 0 };
char temp[BUFFSIZE] = { 0 };
char method[BUFFSIZE] = { 0 };
char url[BUFFSIZE] = { 0 };
char *token = NULL;
int len;
int state, hit = 0, miss = 0;
```
```
pid_t current_pid = Current process ID;
time_t start_process_time, end_process_time;
Save start process time;
print Connect success with client address and port;
if read data from client_fd to buf{
print Request message;
method = Request message’s method;
if(method == GET){
url = Request message’s url
state = Ceck_Cache’s state(Hit or Miss)
write response message and header with client address, port, and state;
Send message to client;
}
}
check end time;
print terminate state, running time, hit or miss state in logfile.txt
```

```
print Terminate connection with client address and port;
return;
}
```
```
main(void){
Make Cache and log directory and store path’s information;
if open socket is failed, print error and return;
update server’s address information;
if binding socket and server’s address data is failed, print error and return;
```
```
Waiting Connection and Collect SIGCHID signal using handler;
while true{
if connection didn’t occur, print error and return;
if make child process failed, close file descriptor and socket and continue;
if child process, Do Sub_Process_Work() and exit;
close client file descriptor;
}
```
close socket file descriptor;

}

## 4. 결과 화면


해당 함수는 signal함수에서 handling을 위해 만들어진 함수다. 해당 함수에서는
waitpid(WNOHANG)을 통해 child process의 종료를 확인시켜주는 역할을 수행한다.

해당 함수는 기존의 cache directory와 log directory를 생성하고 log_file과
cache_dir의 path를 저장하는 역할을 수행한다.


해당 함수는 기존의 Sub_Process_Work 함수에서 URL을 받아 hashing, cache 생성,
logfile 작성을 맡고 있었던 부분을 분리하여 작성한 함수다.

해당 함수는 2 - 1 에서 존재하던 Sub_Process_Work 함수에서 일부 수정하였다. 우선
큰 변경점으로는 read()를 받을 경우 해당 message를 출력하고 해당 read()의
method와 url을 분리한다. method가 get인 경우에 한해 url을 Ceck_Cache()함수를
통해 처리하며 state를 통해 HIT/MISS 상태를 기억한다. 기억한 상태는 response
message를 작성하는데 사용하며 header와 함께 message를 client에게 전송한다.
이외의 부분은 이전 함수와 동일하다.


해당 그림은 server의 메인함수다. 해당 함수에서는 socket을 열고 binding을 통해
server의 정보를 묶는다. 그리고 listen을 통해 연결을 받는다. 연결을 accept할 경
우 child process를 생성하여 Sub_Process_Work 작업을 수행한다.

해당 파일은 변경된 Makefile이다. proxy_cache.c의 파일을 proxy_cache 실행파일로
만드는 역할을 수행한다.


해당 그림은 기존의 cache와 logfile이 없는 상태에서 시작한다. make 명령을 통해
실행파일이 정상 생성되는 것을 볼 수 있다.

proxy_cache를 실행 후 klas.kw.ac.kr을 입력하였을 때 기존 cache가 없었기 때문에
MISS가 뜬 모습을 볼 수 있다. 많은 출력이 나오는 것은 firefox에서 지속적으로 신
호를 보내기 때문에 그렇다.


다시 한번 실행하였을 때에는 cache가 있기 때문에 HIT 상태를 출력하는 모습이다.


google.com을 통해서도 동일한 결과를 얻을 수 있었다.


```
logfile을 살펴보면 URL을 받았을 때 url 정보와 해당 URL에 대한 HIT 또는 MISS
상태가 기존 출력대로 제대로 출력되는 모습을 볼 수 있다. 이를 통해 해당 프로그
램이 정상적으로 구현되었음을 확인하였다.
```
## 5. 고찰

```
해당 과제를 통해서 단순하게 server와 client간에 message를 전달하는 것이 아니라
Request 요청을 받아서 처리하고 Response Message를 보내는 일련의 과정을 이해할 수
있었다. 또한 컴퓨터 네트워크 강의에서 배웠던 HTTP Protocol을 실제적으로 눈으로 확인
할 수 있던 과제였다. 또한 Response Message를 작성하면서 해당 Header와 MTML 양식
에 대해서도 공부할 수 있었던 과제었다.
```

## 6. Reference

### 강의 자료만을 참고


