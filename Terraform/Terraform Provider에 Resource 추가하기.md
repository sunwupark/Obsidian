순서를 정해보자

1. Terraform Provider Scaffolding Framework
	1. go.mod 에서 모듈을 본인이 원하는 이름으로 변경한다
	2. 버전 및 require도 본인의 클라이언트에서 가져와야함으로 변경한다
	3. 기존 hashicups의 주소로 되어있는 것과 registry 도 본인의 것으로 변경해야한다!
	4. Provider의 메타데이터도 본인의 명의로 바꿔준다

```
func (p *hashicupsProvider) Metadata(_ context.Context, _ provider.MetadataRequest, resp *provider.MetadataResponse) {
	resp.TypeName = "sunwupark"
	resp.Version = p.version
}
```

