**객체의 포인터(참조)가 서브 루틴Subroutine 밖으로 전파되는지를 분석하는 기술**
=> 컴파일러가 최적화를 수행할 때 객체의 메모리를 **스택Stack과 힙Heap** 중 한 곳에 할당

```
type user struct{
	name string
}

//Stored in Stack
func Stack() user{
	u := user{
		name: "Park",
	}
	return u
}

//Stored in Heap
func Heap() user{
	u := user{
		name: "Sean"
	}
	return &u
}
```
=> 객체는 컴파일시점 메모리 크기를 알기 때문에 스택에 할당
=> 객체의 포인터는 포인터가 리턴됨을 인지하면 힙에 할당

## Go언어 스택?
- 고루틴 스택은 초기에 2KB의 메모리만 할당
- 스택공간 충분한지 확인
- 스택이 커질 수 있으므로/과부화 다른 고루틴의 스택 메모리 포인터 가질 수 없다!!

> [!NOTE]
> Instead of giving each goroutine a fixed amount of stack memory, the Go runtime attempts to give goroutines the stack space they need on demand, freeing the programmer from having to make decisions about stack size
> 

C와 같은 기존 언어에서는 스레드 스택에 고정된 크기의 메모리 블록이 할당됩니다. 
문제상황: 이는 함수가 매우 재귀적인 경우 문제가 될 수 있습니다. 
해결방법: Go는 스택이 필요에 따라 증가하고 감소하도록 허용하여 이 문제를 해결합니다. 
- 고루틴이 스택 공간이 부족하면 새로운 메모리 섹션이 할당됩니다. 이 프로세스를 [[스택 분할]]이라고 합니다. 이전 스택에 대한 정보는 새 스택 세그먼트에 저장됩니다. 고루틴이 원래 크기로 축소되면 아무 조치도 취할 필요가 없습니다. 

문제상황: 그러나 스택 세그먼트라고 불리는 이 접근 방식에는 스택 축소가 비용이 많이 들기 때문에 한계가 있었습니다. 
해결방법: 이를 해결하기 위해 Go는 스택 복사라는 방법을 사용합니다. 
- [[스택 복사]]는 원래 스택 크기의 두 배 크기의 새 메모리 블록을 할당하고 원래 스택의 내용을 새 블록에 복사합니다. 이를 통해 스택을 축소할 필요가 없습니다.
문제상황: 이 접근 방식에도 스택이 복사되면 스택 내의 포인터가 유효하지 않게 되기 때문에 어려움이 있습니다. 
해결방법: Go는 스택에 들어갈 수 있는 유일한 포인터는 스택 자체에 있는 포인터라는 사실을 활용하여 스택이 복사될 때 스택 내의 포인터를 새 대상으로 업데이트합니다.

## 출처
https://velog.io/@kineo2k/Go-%EC%96%B8%EC%96%B4-%EC%9D%B4%EC%8A%A4%EC%BC%80%EC%9D%B4%ED%94%84-%EB%B6%84%EC%84%9D


