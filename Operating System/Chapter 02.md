
## 2.1 Operating System Service

![[Pasted image 20240525183830.png]]

## helpful to the user.
운영 시스템의 서비스는 사용자와 컴퓨터 시스템 간의 상호 작용을 가능하게 하는 다양한 기능을 포함합니다. 
1. **사용자 인터페이스**: 
	1. 그래픽 사용자 인터페이스(GUI), 명령줄 인터페이스(CLI) 
2. **프로그램 실행**: 
	1. 시스템은 프로그램을 메모리에 로드하고 실행할 수 있어야 합니다. 
	2. 정상,비정상적으로 종료
3.  **입출력(I/O) 작업**: 
	1. 실행 중인 프로그램은 파일이나 I/O 장치와 같은 입출력 작업을 요구할 수 있습니다. 
	2. **효율성과 시스템 보호를 위해, 사용자는 직접적으로 입출력 장치에 접근해서는 안된다. OS가 대신 입출력 관리를 해준다.**
4. **파일 시스템 조작**: 
	1. 프로그램은 파일과 디렉토리를 읽고 쓰며, 생성하고 삭제할 필요가 있습니다. 
	2. 특정 파일을 검색하고 파일 정보를 나열할 수 있어야 합니다. 
5.  **통신**: 
	1. 여러개의 process들이 shared memory에 읽고 쓰거나 **message passing** 방식을 통해서 정보를 주고 받는다.
6. **오류 감지**: 
	1. CPU 및 메모리 하드웨어, I/O 장치, 사용자 프로그램에서 발생할 수 있는 다양한 오류를 지속적으로 감지하고 수정해야 합니다. 
7. 자원 할당(Resource Allocation)  
	1.  다수의 User가 존재하거나 다수의 작업들이 동시에 수행될 때, Resource를 적절히 할당해주어야 한다.  - 예를 들어, CPU를 최대한 효율적으로 이용하기 위해 운영체제는 CPU Scheduling Routine이 CPU의 속도, 사용 가능한 레지스터 수 등의 다양한 요인을 고려하여 자원을 할당한다.
8. 회계(Accounting)  
	1.  사용자가 컴퓨터 자원을 어떻게, 얼마나 많이 사용하는지를 알 수 있도록 해야 한다.
9. 보호(Protection)와 보안(Security)  
	1. 보호(Protection) : 시스템 자원에 대한 모든 접근이 통제되도록 보장하는 것  
	2. 보안(Security) : 각 사용자가 자원에 대한 접근을 원할 때, 시스템에게 자기 자신을 인증하는 것에서부터 시작.

## ensuring the efficient operation of the system
- BY: sharing the computer resources among the different processes

- **Resource allocation** : **multi process들이 동시에 돌아갈 때, resources은 각각 할당되어야 한다**. OS는 여러 종류의 resources을 관리한다. 
	- 몇 개의 resources(CPU, main memory, File system)에 관해서는 allocation code에 의해 할당된다.
- **Logging** : **어떤 프로그램이 얼만큼의 resources들을 사용하는지 추적해야 한다**. 이러한 사용량 통계들은 시스템 관리자에게 유용한 도구가 되어, computing service를 향상하는데 도움이 될 것이다.
- **Protection and security** : 여러개의 프로세스들이 실행되고 있을때, 한개의 프로세스가 다른 프로세스나 OS에 간섭하는 경우는 없어야 한다. 
	- Protection(보호)는 시스템 자원들에 대한 접근이 통제되어 있다는 것을 보장한다. 
	- Security(보안)은 외부로부터의 접근을 막고, 외부 입출력 장치도 보호한다.

## 2. 2 User and Operating-System Interface
### 2.2.1 Command Interpreters(셸)
- 목적: 다음 사용자 명령을 가져오고 실행하는 것
- 자체에 명령어를 실행시키는 코드를 가지고 있는 방법
- 혼자 명령어를 이해하지 못하고, 메모리에 로드되어 실행되어야 할 시스템을 identify

## 2.3 System Calls
- 운영체제의 커널이 제공하는 서비스에 대해, 응용 프로그램의 요청에 따라 커널에 접근하기 위한 인터페이스다!
- User Mode -> Kernel Mode -> 연산 수행 -> 결과 User Application에 제공

![[Pasted image 20240526092653.png]]

## 2.3.2 Application Programming Interface(API)
- API(Application Programming Interface)는 소프트웨어 간의 상호작용을 가능하게 하는 인터페이스이다. 
- API를 이루는 함수들은 System calls를 호출하여 Application Programmer 에게 도움을 준다.
- API 사용자는 System Call이 어떻게 구현되고, 실행 중 무슨 작업을 하는지 알지 못해도 되며, 단지 API를 준수하고, 시스템 호출의 결과로서 운영체제가 무엇을 할 것인지 만을 파악하면 된다.
- 설계 이유
	- Program Portability
		- 시스템 종류와 상관없이 돌아감
		- 시스템 콜 까다로워서 API가 효율적
	- Run-Time Environment
	
#### 운영체제에 매개변수를 전달하는 방법은 크게 3가지가 있다.  
1. 매개변수를 레지스터 내에 전달하는 방법  
	1. 프로세스가 운영체제의 서비스를 요청할 때 필요한 매개변수를 CPU의 레지스터에 직접 저장합니다. 
	2. 이후, 운영체제는 해당 레지스터의 내용을 읽어 매개변수를 얻습니다. 
	3. 하지만, 레지스터의 크기와 수는 제한적이기 때문에, 전달할 수 있는 매개변수의 크기와 개수에 제약이 따릅니다.
2. 매개변수를 메모리 내의 블록이나 테이블에 저장하고, 블록의 주소가 레지스터 내에 저장되는 방법  
	1.  매개변수를 메모리의 특정 블록이나 테이블에 저장하고, 해당 블록이나 테이블의 주소를 레지스터에 저장합니다. 
	2. 운영체제는 레지스터에서 주소를 읽은 후, 해당 주소를 참조하여 메모리에서 매개변수를 얻습니다. 
	3. 이 방법은 레지스터 방식에 비해 **더 많은 데이터를 전달**할 수 있지만, **메모리 접근 시간 때문에 상대적으로 느릴 수** 있습니다.
3. 매개변수를 블록이나 스택에 저장하는 방법 (매개변수의 길이를 제한하지 않는 장점이 있음.)
	1. 매개변수를 메모리 내의 블록이나 스택에 저장합니다. 
	2. 프로그램은 스택 포인터나 베이스 레지스터를 사용하여 운영체제에 이 정보의 위치를 알립니다. 
	3. 운영체제는 이 위치를 참조하여 필요한 모든 매개변수를 얻습니다. 이 방법은 매개변수의 양이 많거나 크기가 가변적일 때 유용하며, 함수 호출 시 매개변수를 전달하는 데 자주 사용되는 방식입니다.

### 2.3.3 Types of System Calls
- Process Control
	- end, abort: 실행중인 프로그램 을 정상적/비정상적으로 종료
	- load, execute: process 나 job이 다른 프로그램을 load하고 execute
	- process 생성 + 종료: execution control
	- process 속성 획득, 속성 결정: job 의 priority + maximum available t
	- 시간 기다리기: 실행을 끝나기를 기다린다
	- wait event, signal event: 특정 사건 기다리기, 일어나면 신호 보내기
	- allocate and free memory: 일관성 보장, 공유 데이터 lock
- File Management
	- create File, Delete file
	- open, close
	- read, write, reposition
	- get file attributes, set file attributes
- Device Management
	- request devices, release devices
	- device read, write,reposition
	- get device attributes, set device attributes
	- logically attach or detach devices
- Information Maintenance
	- get time or date, set time or date
	- get system data, set system data
	- get process, file, or device attributes
	- set process file, or device attributes
- Communications 
	[[IPC]]
	1. Message Passing Model
	2. Shared Memory Model
	- message passing
		- 장점: conflict가 없다, 고로 소량의 데이터 교환에 좋다
	- shared memory passing
		- 다른 프로세스가 소유한 메모리 영역에 접근하기 위해 system call
		- 장점: 최대 속도와 편리한 통신
		- 단점: 동기화 문제
	- 통신 연결의 생성, 제거
	- 메세지의 송신, 수신
	- 상태 정보 전달
	- 원격 장치의 부착 및 분리
- Protection
	- 다수의 사용자를 가지는 다중 프로그램 시스템에서만 고려되는 문제
	- networking과 internet의 출현으로 서버에서 휴대용 컴퓨터까지 모든 컴퓨터 시스템에서 고려


## 2.4 System Services
운영체제의 기능을 보조하고, 사용자 및 시스템 관리자가 시스템을 더욱 효과적으로 사용할 수 있도록 돕는 일련의 프로그램

### 운영체제와의 차이점
- 운영체제는 컴퓨터 시스템의 기본적인 기능과 자원 관리를 책임지는 핵심 소프트웨어입니다.
- 시스템 서비스(유틸리티)는 운영체제의 기능을 보조하고, 사용자가 시스템을 더 효율적으로 사용할 수 있도록 돕는 프로그램 집합입니다.

OS -> System Services -> Application Program
- convenient environment for program development and execution. Some of them are simply user interfaces to system calls. Others are consider- ably more complex
- 프로그램 개발 + 실행 환경 제공
- System Programs를 통해 많은 운영체제들은 Common Program 연산 수행
 
1. **파일 관리(File management)**: 
	1. 파일 및 디렉토리를 생성, 삭제, 복사, 이름 변경, 출력, 나열하고 일반적으로 접근 및 조작하는 프로그램입니다. 
	2. 사용자가 시스템에 저장된 파일을 쉽게 관리할 수 있게 해줍니다.
    
2. **상태 정보(Status information)**: 
	1. 시스템에 대한 날짜, 시간, 사용 가능한 메모리 또는 디스크 공간, 사용자 수 등의 단순한 상태 정보를 요청하는 프로그램부터, 세부적인 성능, 로깅, 디버깅 정보를 제공하는 보다 복잡한 프로그램까지 있습니다. 
	2. 이러한 프로그램은 일반적으로 출력을 터미널이나 다른 출력 장치, 파일에 포맷하고 출력하거나 GUI의 창에 표시합니다.
    
3. **파일 수정(File modification)**: 
	1. 디스크나 기타 저장 장치에 저장된 파일의 내용을 생성 및 수정할 수 있는 여러 텍스트 에디터가 있을 수 있습니다. 
	2. 파일 내용을 검색하거나 텍스트를 변환하는 특별한 명령어도 있을 수 있습니다.
    
4. **프로그래밍 언어 지원(Programming-language support)**: 
	1. C, C++, Java, Python과 같은 일반적인 프로그래밍 언어를 위한 컴파일러, 어셈블러, 디버거, 인터프리터가 운영 체제에 포함되어 있거나 별도로 다운로드할 수 있습니다.
    
5. **프로그램 로딩 및 실행(Program loading and execution)**: 
	1. 프로그램이 조립되거나 컴파일된 후에는 실행을 위해 메모리에 로드되어야 합니다. 
	2. 시스템은 절대 로더, 재배치 가능 로더, 링커지 에디터, 오버레이 로더를 제공할 수 있습니다. 
	3. 또한 고급 언어 또는 기계 언어에 대한 디버깅 시스템이 필요합니다.
    
6. **통신(Communications)**: 
	1. 프로세스, 사용자, 컴퓨터 시스템 간의 가상 연결을 만드는 메커니즘을 제공하는 프로그램입니다. 
	2. 사용자가 서로의 화면에 메시지를 보내거나 웹 페이지를 탐색하고, 이메일 메시지를 보내거나, 원격으로 로그인하거나, 한 기계에서 다른 기계로 파일을 전송할 수 있게 합니다.
    
7. **백그라운드 서비스(Background services)**: 
	1. 모든 일반 목적 시스템에는 부팅 시 특정 시스템 프로그램 프로세스를 시작하는 방법이 있습니다. 이러한 프로세스 중 일부는 작업을 완료한 후 종료되지만, 다른 프로세스는 시스템이 중단될 때까지 계속 실행됩니다. 네트워크 연결을 듣고 해당 요청을 올바른 프로세스에 연결하기 위해 서비스가 필요한 네트워크 데몬이 대표적인 예입니다.

## 2.5 Linkers and Loaders
![[스크린샷 2024-05-26 오후 1.36.20.png]]
1. **컴파일 단계**: 소스 코드 파일(.c, .cpp 등)은 컴파일러에 의해 오브젝트 파일(.o, .obj 등)로 변환됩니다. 이 오브젝트 파일들은 메모리의 어떤 위치에도 로드될 수 있도록 설계된 'relocatable' 형식입니다.
2. **링킹 단계**: 링커(Linker)는 **여러 오브젝트 파일들을 하나의 실행 가능한 바이너리 파일(.exe, .out 등)로 결합**합니다. 이 과정에서 필요한 라이브러리(예: 표준 C 라이브러리, 수학 라이브러리 등)도 함께 링크됩니다. 링커는 정적 링킹과 동적 링킹 두 가지 방식을 지원할 수 있으며, 동적 링킹을 사용할 경우 실행 파일은 필요한 라이브러리를 실행 시점에 로드합니다.
3. **로딩 단계**: 프로그램을 실행하려면, 로더(Loader)가 실행 가능한 바이너리 파일을 메모리로 로드해야 합니다. 이때, 로더는 프로그램의 코드와 데이터를 프로세스의 주소 공간에 매핑하고, 필요한 라이브러리를 메모리로 로드합니다.
4. **실행 단계**: 메모리로 로드된 프로그램은 CPU에서 실행될 수 있게 됩니다. 실행 파일에 포함된 프로그램의 진입점(entry point) 주소에서 첫 번째 명령어가 실행되기 시작합니다.
    
전체 과정을 요약하면, 소스 코드는 컴파일러에 의해 오브젝트 파일로 변환되고, 링커는 이 오브젝트 파일들을 하나의 실행 파일로 결합합니다. 사용자가 프로그램을 실행하려고 하면, 로더가 이 실행 파일을 메모리로 로드하고, CPU는 로드된 프로그램을 실행합니다.

## 2.7 Operating-System Design and Implementation
- 사용자 목적
	- 사용하기 편리
	- 쉽게 배우기
	- 신뢰성
	- 안전
	- 빠름
- 시스템 목적
	- 설계 , 구현, 유지보수가 쉬워야한다
	- 적응성, 신뢰성, 효율성을 가져야한다
## 2.8 Operating System Structure
- Monolithic Structure
	- OS를 구성하는 가장 간단한 방법 - 1개의 커널에 모든 기능 넣기
	- 단점: 확장하기 어렵다, 한 부분의 변화가 다른 부분 영향
	- 장점: 적은 오버헤드 + 소통 => 속도, 효율성이 매우 좋다
	![[Pasted image 20240526093519.png]]
- Layered Approach
	- Loosly coupled, 시스템을 특정 기능을 갖는 여러 부분 요소로 쪼갠다
	- => 한 부분의 변화가 그 부분만 영향을 끼친다
	- 최하위 층(Layer 0)는 Hardware이고, 최상위 층(Layer N)은 User Interface이
	- 특정 층 M은 자료구조와 상위 층에서 호출할 수 있는 루틴의 집합으로 구성된다.  
	- 층 M은 다시 자신의 하위 층에 대한 연산을 호출할 수 있다.
	- 변화 + 생성에 자유가 생긴다
	![[Pasted image 20240526121527.png]]

- 장점
	- construction
	- debugging
		- 각 층들은 자신의 하위층들의 서비스와 연산들만을 사용하도록 되어 있으며, 이 점이 디버깅을 단순화한다.
		- 하위 층부터 구현을 시도하고, 완벽히 디버깅이 끝난 뒤에 상위 층의 구현을 시도하게 된다.  
		- 이에, 디버깅 시에 오류가 나게 되면, 그 하위 층들은 모두 디버깅이 완료되었기 때문에 해당 층에 오류가  있다는 것을 알 수 있다.
- 단점
	- 여러층을 적절히 정의하기가 어렵다
	- 다른 유형의 구현방법보다 효율성이 낮다
	- 매개변수 전달시 오버헤드를 추가하여 수행시간이 오래걸린다

## 2.7.3 Microkernels
- OS의 불필요한 요소들을 커널에서 제거하고 user-level단계의 프로그램들로 별도의 공간을 두어 분리 ![[Pasted image 20240526122013.png]]

- 주요기능: Client Program과 User Space에서 수행되는 다양한 서비스 간에  
Communication을 제공
- 장점: os 확장 수정이 쉽다. 새로운 서비스는 사용자 공간에 추가되므로, Kernel의 변경을 필요로 하지 않는다.
- 단점:**system-function overhead가 증가하여, 성능이 나빠진다는 점**이다.

## 2.7.4 Modules
- Loadable Kernel Modules(LKMs)
	- 커널에 핵심 요소들이 있고, 부가 서비스들을 모듈들을 통해 추가할 수 있다
	- primary module이 코어 기능 + 다른 모듈들을 부른다
		-  부팅 시에 또는 실행 중에 부가적인 서비스들을 모듈을 통하여 link 한다.
	- 장점: 새로운 서비스들을 추가하는 것이 쉽다
	- message passing이 필요하지 않다
	- 운영체제 리눅스는 커널 모듈들의 모음이다

| 구분            | Monolithic Structure     | Microkernels                    | Layered Approach      | Loadable Kernel Modules (LKM) |
| ------------- | ------------------------ | ------------------------------- | --------------------- | ----------------------------- |
| **특징**        | 모든 기능이 하나의 큰 프로세스 내에서 실행 | 필수 기능만 커널에 포함, 나머지는 사용자 모드에서 실행 | 운영체제를 기능별로 여러 계층으로 구분 | 커널의 기능을 동적으로 확장 가능한 모듈        |
| **장점**        | 높은 성능                    | 높은 안정성과 보안성, 유연성                | 구조의 명확성, 유지보수와 개발 용이성 | 시스템 재부팅 없이 기능 추가 및 제거 가능      |
| **단점**        | 안정성과 보안성이 상대적으로 낮음       | 성능 저하 가능성                       | 성능 저하, 계층 정의의 어려움     | 부적절한 모듈 설계 시 안정성 영향           |
| **적합한 사용 사례** | 성능이 중요한 시스템              | 안정성과 보안이 중요한 시스템                | 모듈화 및 분리가 중요한 시스템     | 유연성이 요구되는 시스템                 |
예를 들어, Linux는 주로 Monolithic Structure를 사용하지만, 필요에 따라 LKM을 통해 유연성을 제공합니다.

## 2.8 운영체제 디버깅(Operating-System Debugging)
- 하드웨어와 소프트웨어에서의 시스템 오류를 발견하고 수정하는 행위
- 장애 분석
	- 프로세스 실패시 -> 메모리 캡쳐 -> core dump -> 분석을 위해 파일로 저장
	- 커널 장애 => Crash => Crash Dump에 저장

- 성능 조정
	- 병목 지점 제거 -> 성능 향상 -> 시스템 성능 감시 -> 시스템 동작을 측정하고 표시할 수 있는 방법


### 2.9 운영체제 생성(Operating-System Generation)

- 운영체제는 다양한 주변 구성을 가진, 여러 부류의 기계에서 수행되도록 설계되는 것이 일반적이다.  
- 이 경우, 시스템은 각 specific computer site를 위해 구성되거나 또는 생성되어야 한다.  
- 이 절차를 시스템 생성(SYSGEN)이라고 한다.

### 2.10 시스템 부트(System Boot)
- 운영체제가 생성된 이후에는, 하드웨어에 의해 사용 가능해야 한다.  
- 대부분의 컴퓨터 시스템에서는 Bootstrap Program이 커널을 찾은 뒤, Main Memory에 load 하고, 수행을 시작한다.  
- PC와 같은 일부 컴퓨터 시스템은 단순한 Bootstrap Program이 좀 더 복잡한 Bootstrap Program을 디스크로부터 적재하고, 이 Bootstrap Program이 다시 커널을 load 하는 2단계 절차를 사용한다.

​- 컴퓨터가 전원을 켜거나, 재부팅 등의 사건을 받으면 명령 레지스터는 미리 지정된 메모리 위치를 가리키고, 그곳에서부터 실행을 시작한다. (해당 위치에는 최초의 Bootstrap Program이 존재한다.)  
- RAM은 시스템 시작 시에 알 수 없는 상태가 되기 때문에, Bootstrap Program은 ROM 안에 저장된다.

​- bootstrap program은 먼저, 기계의 상태를 진단하여 이상이 없는지 확인한다.  
- 이후, CPU Register, device Controller, Main Memory 등 시스템 전반에 걸쳐 초기화한다.  
- 위의 작업이 끝나면, bootstrap program은 운영체제를 시작한다.

​- 휴대전화, 태블릿 등의 시스템들은 운영체제의 크기가 작은 경우가 많아서 운영체제 전체를 ROM에 저장한다.  
- 이 방식은 bootstrap code가 변경되면, ROM을 변경해야 한다는 점이다.  
- 이 문제를 해결하기 위해 쓰기가 가능한 EPROM을 사용할 수 있다.

1. **전원 켜기 또는 재부팅**
    - 컴퓨터의 전원이 켜지거나 재부팅이 발생하면, 컴퓨터는 기본적인 자가 진단을 시작한다. 이 과정을 POST(Power-On Self Test)라고 한다. POST는 컴퓨터의 주요 하드웨어 구성 요소들이 정상적으로 작동하는지 확인한다.
2. **Bootstrap Loader 실행**
    - POST 과정이 끝나면, CPU는 미리 지정된 메모리 위치(보통 ROM에 저장된 위치)를 찾아 해당 위치에 저장된 Bootstrap Loader(초기 부트스트랩 프로그램)를 실행한다.
3. **기본적인 하드웨어 상태 진단**
    - Bootstrap Loader는 컴퓨터의 기본적인 하드웨어 구성 요소(예: CPU, 메모리, 입력 장치 등)의 상태를 점검하여 이상 유무를 확인한다.
4. **시스템 초기화**
    - 하드웨어 상태 진단 후, Bootstrap Loader는 CPU 레지스터, 디바이스 컨트롤러, 주 메모리 등 시스템 전반에 걸친 초기화 작업을 수행한다. 이 과정에서 시스템의 기본 설정을 로드하고, 필요한 드라이버를 메모리에 적재한다.
5. **보조 Bootstrap Loader 적재**
    - PC와 같은 일부 시스템에서는 초기 Bootstrap Loader가 비교적 단순하므로, 더 복잡한 보조 Bootstrap Loader를 디스크로부터 메모리로 적재하는 과정을 거친다. 이 보조 Bootstrap Loader는 운영 체제의 커널을 로드하는 데 필요한 추가적인 기능을 제공한다.
6. **운영체제 커널 로드**
    - 보조 Bootstrap Loader는 하드디스크 또는 다른 저장 매체에서 운영체제의 커널을 찾아 메인 메모리로 로드한다. 커널은 시스템의 하드웨어와 소프트웨어 자원을 관리하는 핵심 부분이다.
7. **운영체제 실행**
    - 커널 로드가 완료되면, 운영체제는 자체 초기화 과정을 거쳐 필요한 시스템 서비스와 프로세스를 시작한다. 사용자 인터페이스가 로드되고, 최종적으로 사용자가 시스템을 사용할 준비가 된다.