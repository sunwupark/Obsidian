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



## 출처
https://velog.io/@kineo2k/Go-%EC%96%B8%EC%96%B4-%EC%9D%B4%EC%8A%A4%EC%BC%80%EC%9D%B4%ED%94%84-%EB%B6%84%EC%84%9D


