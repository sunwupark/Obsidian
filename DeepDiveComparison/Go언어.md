## Go 언어란? 
- Google에서 개발한 시스템 프로그래밍 언어
- Channel을 통해 동시성을 지원
- GC를 제공

## Go 언어의 GC
- GC: Concurrent Mark & Sweep 컬렉터
- JVM등에서의 일반적인 압축 + 세대별 GC를 수행하지 않는다

![[스크린샷 2024-05-22 오후 3.05.22.png]]

## Go Memory Management




## 압축의 종류
#### 정적 유형
- GC별로 객체를 재배치 하지 않는다. 
- Go언어의 Mark & Sweep GC
	- 메모리할당과 해제를 반복하면 heap fragmentation => 할당 성능이 악화
	- ![[Pasted image 20240522150640.png]]

#### 동적 유형
- GC수행시 alive상태의 객체를 heap끝으로 재배치해서 heap을 압축
- HotSpot VM의 GC등에 사용!
- 장점
	- 단편화 회피
	- [bump allocation]을 통해 고속 메모리 할당 구현 가능!
	
		![[Pasted image 20240522150823.png]]

### Go 언어가 압축을 하지 않는 이유
- [런타임 재작성 작업] 등의 시간적 제약으로 인해 압축 기능을 도입하지 않음
- 압축 자체가 CPU & 메모리 사용량을 증가, 오버헤드를 피하기 위해
- 단순성과 효율성을 중요하게 여긴다
- 채널 변수에 대한 메모리 회수를 처리하는 등의 설계로 필요하지 않음?
- 압축을 도입하지 않는 대신 Concurrent Mark & Sweep 방식을 사용하여 지연 시간을 최소화하는 것을 목표

#### Go에서 Garbage가 생기는 이유

```
func main(){
	NewDuck()
}

type Duck struct{}


//go:noinline
func NewDuck() *Duck {
	return &Duck{}
}
```
![[스크린샷 2024-05-22 오후 3.20.28.png]]

1. **값 타입**: 기본 데이터 타입(int, float, bool 등)과 구조체(struct) 등의 값 타입 변수는 스택에 저장됩니다.
2. **참조 타입**: 슬라이스, 맵, 채널, 인터페이스 등의 참조 타입 변수는 힙에 저장됩니다.
Duck 객체는 인터페이스 타입이므로 힙에 저장 (인터페이스 타입은 실제 구현 객체에 대한 참조가지기 때문에)

![[스크린샷 2024-05-22 오후 3.20.16.png]]
- 포인터는 사라져도 Duck객체는 남기 때문에 Garbage가 생긴다


https://engineering.linecorp.com/ko/blog/go-gc
https://siyul-park.github.io/system/memory-in-golang/
https://www.youtube.com/watch?v=gPxFOMuhnUU