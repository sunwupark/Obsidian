
## 2.1 

![[Pasted image 20240525183830.png]]

## helpful to the user.
운영 시스템의 서비스는 사용자와 컴퓨터 시스템 간의 상호 작용을 가능하게 하는 다양한 기능을 포함합니다. 이 서비스들은 다음과 같이 요약할 수 있습니다:

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

## ensuring the efficient operation of the system
- BY: sharing the computer resources among the different processes

- **Resource allocation** : **multi process들이 동시에 돌아갈 때, resources은 각각 할당되어야 한다**. OS는 여러 종류의 resources을 관리한다. 
	- 몇 개의 resources(CPU, main memory, File system)에 관해서는 allocation code에 의해 할당된다.
- **Logging** : **어떤 프로그램이 얼만큼의 resources들을 사용하는지 추적해야 한다**. 이러한 사용량 통계들은 시스템 관리자에게 유용한 도구가 되어, computing service를 향상하는데 도움이 될 것이다.
- **Protection and security** : 여러개의 프로세스들이 실행되고 있을때, 한개의 프로세스가 다른 프로세스나 OS에 간섭하는 경우는 없어야 한다. 
	- Protection(보호)는 시스템 자원들에 대한 접근이 통제되어 있다는 것을 보장한다. 
	- Security(보안)은 외부로부터의 접근을 막고, 외부 입출력 장치도 보호한다.

## 2. 2 User and Operating-System Interface
### 2.2.1 Command Interpreters
- 목적: 다음 사용자 명령을 가져오고 실행하는 것
- 자체에 명령어를 실행시키는 코드를 가지고 있는 방법
- 혼자 명령어를 이해하지 못하고, 메모리에 로드되어 실행되어야 할 시스템을 identify

## 2.3 System Calls
- 운영체제의 커널이 제공하는 서비스에 대해, 응용 프로그램의 요청에 따라 커널에 접근하기 위한 인터페이스다!
- User Mode -> Kernel Mode -> 연산 수행 -> 결과 User Application에 제공

![[Pasted image 20240526092653.png]]

## 2.3.2 Application Programming Interface(API)
- API를 이루는 함수들은 System calls를 호출하여 Application Programmer 에게 도움을 준다.
- 설계 이유
	- Program Portability
		- 시스템 종류와 상관없이 돌아감
		- 시스템 콜 까다로워서 API가 효율적
	- Run-Time Environment

### 2.3.3 Types of System Calls
- Process Control
- File Management
- Device Management
- Information Maintenance
- Communications
- Protection


## 2.4 System Services
- 프로그램 개발 + 실행 환경 제공
- System Programs를 통해 많은 운영체제들은 Common Program 연산 수행

2.5 Linkers and Loaders
2.6 Why Applications Are Operating-System Specific
2.7 Operating-System Design and Implementation

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