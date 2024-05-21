![[스크린샷 2024-05-21 오후 11.31.58.png]]Go 의 경우에는 실행파일 안에 가비지 컬렉터가 내장된다.  
그래서 Go는 프로그램이 매우 가벼우면서도, GC에 의해 생산성이 높은 특징을 가지게 된다.

GC는 위에서 언급한 Mark & Sweep 외에도  
- 힙 내의 압축(compaction : GC 수행 시 살아남은 객체를 heap 끝으로 재배치 & heap 압축)  
- 세대별 GC(Generational GC : 객체가 살아남은 횟수로 수명을 부여, 전체 스캔 빈도 감소로 효율 증가)  
등의 기능이 있다.

비압축/비세대 원칙

https://engineering.linecorp.com/