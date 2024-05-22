모든 GC의 목표!: Stop-The-World 시간을 줄이자!!!
- 이유: GC를 실행하기 위해 JVM이 애플리케이션 실행을 멈춤

자바는 하나의 가설을 바탕으로 Garbage Collection작업을 한다

> [!Weak Generational Hypothesis]
> 
> 1. 대부분의 객체는 금방 접근 불가능 상태가 된다
> 2. 오래된 객체에서 젊은 객체로의 참조는 아주 적게 존재한다

접근 불가능?
- 더 이상 해당 객체를 참조하는 변수나 데이터 구조가 없어서 프로그램 내에서 해당 객체를 사용할 수 없게 된 상태
##### HotSpot VM에서의 방식

HotSpot=JVM

JVM에서는 두가지의 영역으로 나누었다

- Young 영역: 
	- 새롭게 생성한 객체의 대부분이 여기에 위치
	- 대부분의 객체가 금방 접근 불가 => 많은 객체가 Young 영역에 생성되었다가 사라진다!
	- 객체가 사라지면 => Minor GC
- Old 영역 
	- 접근 불가능 상태로 되지 않아 Young 영역에서 살아남은 객체가 여기로 복사
	- Young보다 크게 할당
	- GC적게 발생
	- 객체가 사라지면 => Major GC(Full GC)
		![[Pasted image 20240522162950.png]]
	- Permanent Generation 영역은 Method Area라고 한다
	- 객체나 억류된 문자열 정보 저장? 
	- 영원히 남아있는 것은 아님 => GC발생(Major)
![[스크린샷 2024-05-22 오후 4.30.21.png]]


### Card Table?
- Old 영역의 객체가 Young 영역의 객체를 참조할때 정보 표시 => Young 영역의 GC를 실행할때 Old영역의 모든 객체를 참조하지 않고 이 테이블만 뒤져서 GC대상인지 식별
- write barrier방법!

## Young 영역의 구성
- [[Eden]] 영역
- Survivor 영역(2개)
- => 총 3개의 영역으로 나뉘느 ㄴ것이다!
	![[Pasted image 20240522163241.png]]
#### Copy & Scavenge
절차: 
	1. `Eden` 영역은 자바 객체가 생성되자마자 저장되는 곳이다. 이렇게 생성된 객체는 `Minor GC`가 발생할 때 `Survivor` 영역으로 이동하게 된다.
	2. `Survivor` 영역은 `Survivor1`과 `Survivor2` 두 영역으로 나뉘는데, `Minor GC`가 발생하면 `Eden`과 `Survivor1`에 활성 객체를 `Survivor2`로 복사한다.
	3. 활성이 아닌 객체는 자연스럽게 `Survivor1`에 남아있게 되고, `Survivor1`과 `Eden` 영역을 클리어 한다. (결과적으로 활성 객체만 `Survivor2`)로 이동하게 된 것이다.
	4. 다음번 `Minor GC`가 발생하면 같은 원리로 `Eden`과 `Survivor2` 영역에서 활성 객체를 `Survivor1`으로 이동시키게 된다. 계속 이런 방식을 반복하면서 `Minor GC`를 수행한다.
	5. 이렇게 `Minor GC`를 수행하다가 `Survivor` 영역에서 오래된 객체는 `Old` 영역으로 옮기게 된다.
	
	![[Pasted image 20240522162854.png]]

JVM에서는 빠른 메모리 할당을 위해 두가지 기술을 사용한다
1. bump-the-pointer 기술
	1. Eden영역의 마지막 객체를 추적(top)
	2. 크기가 적당한지 판정 => 넣는다
	3. 마지막에 추가된 객체만 점검함으로써 빠르게 메모리를 할당한다
	4. 멀티쓰레드에서 사용하는 여러 객체를 Eden영역에 저장하려면 락이 발생 => TLABs로 해결
2. TLABs(Thread-Local Allocation Buffers)
	1. 각각의 스레드가 각각의 몫에 해당하는 Eden 영역의 작은 덩어리를 가지게 한다
	2. 각 쓰레드는 자신의 TLAB에만 접근하므로 락 없이 할당 가능
	3. 

## Old 영역에 대한 GC
![[Pasted image 20240522164146.png]]

알고리즘: Mark & Compact
	객체들의 참조를 확인하면서 참조가 연결되지 않은 객체를 표시! 작업이 끝나면 삭제
	- 속도가 매우 느리다! Stop-the-world현상이 일어난다! 

1. Parallel GC
	1. 처리하는 쓰레드가 여러 개
	![[Pasted image 20240522164325.png]]
	종류:
	- Low-Pause
		- GC가 일어날때 애플리케이션이 멈추는 현상을 최소화하는데 역점을 두었다
	- Throughput방식
		- Minor GC가 발생하였을 때 되도록이면 신속하게 수행하도록 throughput에 중점을 두었다.

2. Concurrent GC (CMS GC)
	1. Stop-the-world 최소화
	2. 일부는 애플리케이션이 돌아가는 단계에서 수행하고 최소한의 작업만을 애플리케이션이 멈췄을 때 수행하는 방식이다.
		![[Pasted image 20240522165022.png]]
		1. 초기 initial Mark단계에서 가장 가까운 객체중 살아 있는 개체만 찾는다 => 멈추는 시간이 굉장히 짧다
		2. Concurrent Mark단계에서 살아있다고 확인한 객체 참조하는 객체 따라가서 확인
		3. Remark => 앞의 단계에서 새로 추가되거나 참조가 끊긴 객체 확인 
		4. 마지막 Concurrent Sweep단계에서 쓰레기 정리
			=> Concurrent Mark & Concurrent Sweep에서는 다른 스레드가 실행되는 상황에서 진행한다!!!!

		- 하지만 다른 GC방식보다 메모리와 CPU를 더 많이 사용
		- Compaction단계가 기본적으로 제공되지 않는다!!!


3. Incremental GC (Train GC)
	1. `Minor GC`가 일어날 때마다 `Old` 영역을 조금씩 `GC` 해서 `Full GC`가 발생하는 횟수나 시간을 줄이는 방식
	2. 하지만 자원을 많이 소모 + Minor GC를 자주 일으킴 => 오히려 느릴 수도?

4. Garbage First(G1 GC)
	1. G1 GC는 바둑판의 각 영역에 객체를 할당하고 GC를 실행한다. 
	2. 그러다가, 해당 영역이 꽉 차면 다른 영역에서 객체를 할당하고 GC를 실행한다. 
	3. 즉, 지금까지 설명한 Young의 세가지 영역에서 데이터가 Old 영역으로 이동하는 단계가 사라진 GC 방식이라고 이해하면 된다


## 자바 GC로그 모니터링
- 자바 옵션에 `-verbosegc` 라는 옵션을 주면 되고, `>` 리다이렉션 명령어를 통해서 파일로 저장하고 분석할 수 있다.
- ![[스크린샷 2024-05-22 오후 4.47.02.png]]

```
java -jar -verbosegc app.jar
```

![[스크린샷 2024-05-22 오후 4.48.05.png]]