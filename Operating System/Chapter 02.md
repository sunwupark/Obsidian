
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

## 2.8 Operating System Structure
- Monolithic Structure
	- OS를 구성하는 가장 간단한 바업ㅂ - 1개의 커널에 모든 기능 넣기
	- 단점: 확장하기 어렵다, 한 부분의 변화가 다른 부분 영향
	- 장점: 적은 오버헤드 + 소통 => 속도, 효율성이 매우 좋다
	![[Pasted image 20240526093519.png]]
- Layered Approach
	- 장점: Loosly coupled, 시스템을 특정 기능을 갖는 여러 부분 요소로 쪼갠다
	- 