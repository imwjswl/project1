# project1

**쉘스크립트 명령어 getopt / getopts**

getopt와 getopts를 사용하기 위해서는 먼저 short 옵션과 long 옵션을 알아야 한다.

* **short 옵션**
 
  short 옵션은 ```-```로 시작하는 short 옵션이다. 예를 들면 ```-p```와 같은 옵션이다.
  이 short 옵션은 여러가지 방법으로 사용 가능하다. 여기서 getopts명령을 이용하지 않고 직접 옵션을 해석해 처리하면,
  옵션 처리에 스크립트가 매우 복잡해질 수 있다.

* **long 옵션**
  
  long 옵션은 ```--```가 붙는 long옵션이다. 예를 들면 ```--posix```, ```--wrning level``` 등과 같은 옵션이다.
  long 옵션은 short 옵션과 달리 붙여 쓸 수 가 없기 때문에 사용방법이 간단하여 직접 해석해서 처리하는 것이 어렵지 않다.

*****

* **getopt**

  쉘 스크립트를 사용하다 보면 실행 인자 및 옵션을 필요로 하는 경우가 많이 생긴다.
  이 때, getopt를 통해 쉘스크립트에서 실행 옵션을 구현한다.
  getopt는 다양한 입력 값이 존재할 경우 **사용자와 개발의 편의를 보장**하기 위해, 스크립트를 보다 **체계적으로 관리**하기 위해 사용된다.
  getopt는 short 옵션과 long 옵션을 모두 지원한다. 옵션 인수를 가질 경우 ':' 문자를 사용하는 것은 getopts builtin 명령과 동일하다.
  
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
 
    * getopt의 특징
   
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

리눅스 명령어 sed / awk

* **sed**

* **awk**
