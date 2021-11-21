# project1

**쉘스크립트 명령어 getopt / getopts**

getopt와 getopts를 사용하기 위해서는 먼저 short 옵션과 long 옵션을 알아야 한다.

* **short 옵션**
 
  short 옵션은 `-`로 시작하는 short 옵션이다. 예를 들면 `-p`와 같은 옵션이다.
  이 short 옵션은 여러가지 방법으로 사용 가능하다. 여기서 getopts명령을 이용하지 않고 직접 옵션을 해석해 처리하면,
  옵션 처리에 스크립트가 매우 복잡해질 수 있다.

* **long 옵션**
  
  long 옵션은 `--`가 붙는 long옵션이다. 예를 들면 `--posix`, `--wrning level` 등과 같은 옵션이다.
  long 옵션은 short 옵션과 달리 붙여 쓸 수 가 없기 때문에 사용방법이 간단하여 직접 해석해서 처리하는 것이 어렵지 않다.

*****

* **getopt**

  쉘 스크립트를 사용하다 보면 실행 인자 및 옵션을 필요로 하는 경우가 많이 생긴다.
  이 때, getopt를 통해 쉘스크립트에서 실행 옵션을 구현한다.
  getopt는 다양한 입력 값이 존재할 경우 **사용자와 개발의 편의를 보장**하기 위해, 스크립트를 보다 **체계적으로 관리**하기 위해 사용된다.
  * **getopt 사용방법**
  
  getopt는 short 옵션과 long 옵션을 모두 지원한다. 옵션 인수를 가질 경우 `:` 문자를 사용하는 것은 getopts builtin 명령과 동일하다.
  **getopt는 한개의 문자만을 구분자로 사용하며 문자열 뒤에 `:`를 붙이게 되면 뒤에 Value가 붙게 된다는 것을 의미한다.**
  
  ```bash
  # short 옵션 지정은 -o 옵션으로 한다.
  # ':'에 따라서 옵션 -a 는 옵션 인수를 갖는다.
  getopt -o a:bc #-a, -a, -c 를 옵션으로 가지고 -a는 argument가 있다.
  
  # long 옵션 지정은 -l 옵션으로 하고 옵션명은 ',' 로 구분한다.
  # ':' 에 따라 옵션 --path 와 --name 은 옵션 인수를 갖는다.
  getopt -l help,path:,name:
  # --help
  # --path 'argument'     --path='argument' 둘 다 동일
  # --name 'argument'     --name='argument' 둘 다 동일
  
  # 명령 마지막에는 -- 와 함께 "$@" 를 붙인다. (short, long 공통)
  # invalid option이나 argument가 빠진 경우 오류 메시지를 출력한다.
  getopt -o a:bc -l help,path:,name: -- "$@"
  ```
  
  short, long 혼용 예
  ```bash
  #!/bin/bash
  
  options=$( getopt -o a:bc -l help,path:,name: -- "$@" )
  echo "$options"
  ```
 
    * **getopt의 특징**
   
      사용자가 입력한 옵션들을 case 문에서 사용하기 좋게 정렬해준다.
      ```bash
      #!/bin/bash
      
      options=$( getopt -o a:bc -l help,path:,name: -- "$@" )
      echo "$options"
      --------------------------------------------------------
      
      # -123 옵션이 -a '123'으로 분리
      # -bc 옵션이 -b -c 로 분리
      # 옵션에 해당하지 않는 hello.c 는 '--' 뒤로 이동
      $ ./test.sh -a123 -bc hello.c
      -a '123' -b -b -- 'hello.c'
      
      # --path=/usr/bin 옵션이 --path '/usr/bin' 로 분리
      # '--' 는 항상 끝부분에
      $ ./test.sh --name foo --path=/usr/bin
      --name 'foo' --path '/usr/bin' --

      # '--' ( end of options ) 처리
      $ ./test.sh -a123 -bc hello.c -- -x --yyy
      -a '123' -b -c -- 'hello.c' '-x' '--yyy'
      ```

********

* **getopts**

  getopts는 c의 getopt 라이브러리 함수의 bash 버전이다. getopts는 스크립트로 넘어오는 여러개의 옵션과 그 해당 인자들을 처리해준다. getopts는 내부적으로 두 개의 변수를 사용한다. **$OPTIND(option index)는 인자 포인터**고 **$OPTARG(option argument)는 옵션에 딸려 넘어오는 해당 인자**이다. 선언 태그의 옵션 이름 뒤에 `:`이 있으면 해당 인자가 있다는 뜻이다.
  getopts는 보통 **while 루프와 같이 써서** 옵션과 인자를 한 번에 하나씩 처리하고 $OPTIND 변수값을 하나씩 줄여서 그 다음을 처리하게 한다. getopts는 short 옵션만 처리한다. ***예전의 getopt대신 getopts가 새롭게 쓰인다.***
  
  * **getopts 사용방법**

    **while 문 뒤에 getopts를 사용한다**
    getopts 뒤에는 arguments로 어떤 문자를 지정할 것인지 그 항목을 나열한다.
    ![image](https://user-images.githubusercontent.com/94671864/142757386-2a53a5aa-06cb-484f-b459-7d5e76b73398.png)
    
    **옵션은 *$opt*에 loop 하나씩 들어간다**
    ```bash
    #!/bin/bash
    
    # ==== main ==== #
    while getopts "ahd" opt; do
        case $opt in
            a)
                -a 옵션이 들어오면 할 일
                ;;
            h)
                -h 옵션이 들어오면 할 일
                ;;
            t)
                -t 옵션이 들어오면 할 일
                ;;
            \?) # 지정 이외 옵션은 이 문자로 할당된다.
                echo $@ is not valid option
                exit 0
                ;;    
        esac
    done
    ```

  
  * **Option string 과 $OPTIND**

    명령에서 `-a`, `-b`, `-c` 세 개의 옵션을 사용한다면 getopts명령에 설정하는 optstring값은 `abc`가 된다.
    args에는 명령 실행시 사용된 인수 값들이 오는데 생략할 경우 `"$@"`가 사용된다.
    
    **shell이 처음 실행 되면 $OPTIND 값은 1을 가리키고 getopts 명령이 실행될 때마다 다음 옵션의 index값을 가리키게 된다.**
    ```bash
    # 다음 set 명령은 실제 "command -a -bc hello world"명령을 실행했을 때와 같이 positional parameters를 설정한다.
    $ set -- -a -bc hello world
    $ echo "$@"
    -a -bc hello world
    
    
    # 처음 $OPTIND 값은 1로 -a를 가리킨다.
    $ echo $OPTIND
    1
    
    $ getopts abc opt "$@"     # getopts 명령을 실행할 때마다 opt 변수값과
    $ echo $opt, $OPTIND       # OPTIND 값이 변경되는 것을 볼 수 있다.
    a, 2                       # 다음 옵션 "b"의 index 값은 2가 된다.
    
    $ getopts abc opt "$@"     # 다음 옵션 "c"는 "b"와 붙여 쓰기를 하여 같은
    $ echo $opt, $OPTIND       # OPTIND 값을 가진다.
    b, 2                       # (sh의 경우에는 b, 3 가 출력된다.)
    
    $ getopts abc opt          # args 부분을 생략하면 default 값은 "$@"이다.
    $ echo $opt, $OPTIND
    c, 3
    
    # 옵션 스트링을 처리하고 난 후 다음과 같이 shift를 하면 나머지 명령 인수만 남게 된다.
    
    $ echo "$@"
    -a -bc hello world
    
    $ shift $(( OPTIND - 1 ))
    $ echo "$@"
    hello world
    ```
  * **Option argument**

    옵션은 옵션인수를 가질 수 있다. 이 때 옵션 스트링에서 해당 옵션 문자 뒤 `:`를 붙인다.
    그러면 getopts명령은 옵션인수 값을 `$OPTARG` 변수에 설정해준다.
    ```bash
    $ OPTIND=1
    $ set -- -a xyz -b -c hello world
    
    # 옵션 a가 옵션인수를 가지므로 옵션 스트링으로 a: 를 설정함
    $ getopts a:bc opt
    $ echo $opt, $OPTARG, $OPTIND
    a, xyz, 3
    
    $ getopts a:bc opt
    $ echo $opt, $OPTARG, $OPTIND
    c, , 5
    ```
  
  
******
**리눅스 명령어 sed / awk**

* **sed**

  **sed [-e script][-f script-file][file...]**
  기본적인 기능은 ed에서 가져왔다고 한다. 이 기능들은 모두 sed에 적용이 된다.
  ***sed는 스트리밍 편집기***라고 한다. 스트리밍 편집기는 파일을 인자로 받아 명령어를 통해 작업한 후 결과를 화면으로 확인하는 방식이다. 편집기를 명령어 쓰듯 사용하는 것과 같다.

  * **sed의 특징**
    sed 명령어를 이용하여 파일을 변경했을 경우 **원본은 손상되지 않는다**.
    쉘 리다이렉션을 이용해 편집 결과를 저장하기 전까지는 파일에 아무런 변경도 가하지 않는다.
    모든 결과는 명령을 수행한 후 화면에 출력되는데 **출력된 결과라 원본과 다르더라도 원본에는 손해가 없다**.
    그래서 내부적으로 특수한 저장 공간인 버퍼를 사용한다. 두 가지 버퍼는 **패턴 스페이스(패턴 버퍼)**와 **홀드 스페이스(홀드 버퍼)**이다.
    
  * **패턴 스페이스와 홀드 스페이스**

    ![image](https://user-images.githubusercontent.com/94671864/142758691-6cbe761a-cfce-4154-9d52-74cdf1a2a59d.png)

    그림을 보면 sed는 **InputStream**으로 파일의 내용을 가져온다. 그리고 난 후에 패턴 버퍼에 그 내용을 담고 있고 데이터의 변형과 추가를 위해 다시 임시 버퍼를 사용하게 되는데, 그게 홀드 버퍼이다. 그리고 작업이 전부 완료되었다면 패턴 버퍼에 그 내용이 담기는데, 그 내용을 **OutputStream**으로 보내주게 되면 비로소 원하는 결과가 출력되는 것이다. 기본적으로 OutputStream은 콘솔화면인 **stdout**이다. ***즉, sed가 내부적으로 2개의 버퍼를 가지고 있다고 보면 된다.*** 
  
  **1.찾기(search), 출력(print)**
     
    `-n` : 작업한 부분만 억제해서 출력하기 위해 사용되는 옵션
     
    `p` : 프린트
     
    `,` : 범위를 정함
     
    `$` : 끝
     
    `-e` : 여러 조건 출력하기 위해 사용되는 옵션
    
    ```bash
    sed -n '/abd/p' list.txt
    # list.txt파일을 한줄씩 읽으면서 (-n : 읽은 것을 출력하지 않음)
    # abd 문자를 찾으면 그 줄을 출력(p)한다.
    sed -n '1,3p' list.txt # 1~3번째 라인 출력
    sed -n '3,$p' list.txt # 3번째 라인부터 끝까지 출력
    sed -n -e '1p' -e '3,$p' list.txt # -e옵션을 활용해서 여러 조건 출력
    ```
  **2.치환(substitute)**
    
   `s` : switch의 약자. 치환할 때 사용하는 subcommand이다.
     
   `g` : 치환이 행에서 전체를 대상으로 이루어지는 것을 말한다.
        (바꿀 것이 여러개 있으면 다 바꾼다는 것. 원래 sed는 행별로 하는데 g플래그를 사용하면 전체에서 다 뽑힌다.)
    
   `i` : 변경 대상 단어를 찾을 때 대소문자를 무시
     
   ```bash
   sed 's/addrass/address/' list.txt
   # addrass를 address로 바꾼다. 단, 원본파일은 바꾸지 않고 출력을 바꿔서 한다.
   ```
  **3.추가(insert)**
    ```bash
    scriptfile -s/
    ```
  **4.삭제(delete)**
   
   `/` : /사이에 들어있는 단어
   
   `d` : delete의 약자
   
   `^` : 행의 시작
   
   `$` : 행의 끝
   
   `*` : 메타문자로 앞의 문자를 0개 이상 찾음
    
   ```bash
   sed '/TD/d' 1.html   # TD 문자가 포함된 줄을 삭제하여 출력한다.
   sed '/Src/!d' 1.html # Src 문자가 있는 줄만 지우지 않는다.
   sed '1,2d' 1.html    # 처음 1줄, 2줄을 지운다.
   sed '/^$/d 1.html    # 공백라인을 삭제하는 명령이다
   ```


* **awk**
