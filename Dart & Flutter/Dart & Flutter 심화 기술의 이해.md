
![[스크린샷 2024-05-23 오후 1.17.42.png]]

Incremental Compile
JIT?
한방에 밀어버리는 컴파일 - AOT

![[스크린샷 2024-05-23 오후 1.20.24.png]]

![[스크린샷 2024-05-23 오후 1.22.10.png]]
JIT- Javascript (Execution Time 바로 그순간에 Compile)
AOT- C/C++ (훨씬 전에 컴파일 한다)
	- 실행파일 생성은 언제 실행될거와 무관하게 만들어진다
	- 아주아주 앞서서 만들어진다

- 수정시 바뀐부분만 보여줌

Dart VM offers a just-in-time compiler (JIT) with incremental recompilation
(HotReload)
## JIT
- Easy to test the code
- Easy to debug
- Live metrics support
- Fast coding cycles
- Incremental Recompilation


Production레벨에서 AOT에 의해서 언제 실행될지에 무관하게 운영체제별로 실행파일을 만들 수 있다

# JIT, AOT - 장, 단점, 무엇인지를 이해하자!!

- AOT는 스타트타임을 줄일 수 있다. JIT에 비한 장점이다!
	- JIT는 부랴부랴 실행을 한다
- Objection Allocation - Garbage Collector

앞의 오브젝트와 연결이 된다. 클래스를 생성하고 오브젝트를 다쓰고나면 없애야하는데 개발자가 깜빡잊고 지우지 않은 하지만 안쓰는 anonymous오브젝트를 없애야한다

성능이 꾸준하게 안정적인것을 좋아한다. 서버 => 프로그램이 돌아가는데 

https://mrale.ph/dartvm/

