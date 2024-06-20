
```
func main() {
    var debug bool
    flag.BoolVar(&debug, "debug", false, "set to true to run the provider with support for debuggers like delve")

    flag.Parse()
    opts := providerserver.ServeOpts{
        // TODO: Update this string with the published name of your provider.
        // Also update the tfplugindocs generate command to either remove the
        // -provider-name flag or set its value to the updated provider name.
        Address: "registry.terraform.io/hashicorp/scaffolding",
        Debug:   debug,
    }
    err := providerserver.Serve(context.Background(), provider.New(version), opts)
    if err != nil {
        log.Fatal(err.Error())
    }
}
```

위의 코드를 통해 기본적인 서버를 실행하고 프로바이더를 가져오게 된다

![[Pasted image 20240620225019.png]]

다음의 프로바이더 코드에서 가져오고 이  프로바이더 코드에게다가 원하는 프로바이더를 작성하여 테라폼이 작동하도록 한다

```
go mod tidy 실행

go run main.go로 실행한다
```

```
func (p *ScaffoldingProvider) Schema(ctx context.Context, req provider.SchemaRequest, resp *provider.SchemaResponse) {
	resp.Schema = schema.Schema{
	Attributes: map[string]schema.Attribute{
	"endpoint": schema.StringAttribute{
		MarkdownDescription: "Example provider attribute",
		Optional: true,
	},
	},
	}
}
```

- Provider가 동작하기 위해 필요한 정보들을 Schema부분에 넣는다

```
func (p *ScaffoldingProvider) Configure(ctx context.Context, req provider.ConfigureRequest, resp *provider.ConfigureResponse) {

var data ScaffoldingProviderModel

resp.Diagnostics.Append(req.Config.Get(ctx, &data)...)

if resp.Diagnostics.HasError() {
	return
}

// Configuration values are now available.
// if data.Endpoint.IsNull() { /* ... */ }

// Example client configuration for data sources and resources
client := http.DefaultClient
resp.DataSourceData = client
resp.ResourceData = client
}
```
- 어떤 인증 과정등을 수행하기 위한 클라이언트 설정 작업이 필요한데 실제 api를 호출하기 위한 사전 작업들을 여기서 수행한다.
- 위의 디펄트 클라이언트를 통해서 작업한다

```
func (p *ScaffoldingProvider) Resources(ctx context.Context) []func() resource.Resource {

return []func() resource.Resource{

NewExampleResource,

}

}
```
- 만약 vpc 리소스를 추가할 것이면 밑에 NewVPCResource등을 만들고 내용 추가하면 됩니다!

![[Pasted image 20240620231353.png]]

- Configure에서 Schema의 값을 가져온다
- 스키마와 같은 형태가 아니면 에러를 반환한다

![[Pasted image 20240620231633.png]]
- 다음과 같은 방법으로 예외처리를 할 수 있다

![[Pasted image 20240620231723.png]]
- 다음과 같이 클라이언트를 만든다

프로바이더가 동작하기 위한 기본적인 설정이라고 볼 수 있다

![[Pasted image 20240620231835.png]]

- 다음과 같은 도커를 띄워서 기본적인 디비와 api서버를 올림

![[스크린샷 2024-06-20 오후 11.19.41.png]]
회원가입을 진행해야함

![[Pasted image 20240620232020.png]]
- 이렇게 실행

즉 테라폼 코어따로 테라폼 프로바이더가 따로 서버가 띄어져서 통신을 하는 방식으로 IAC하는 것이다
테라폼 코어 사용자 입력 - 프로바이더에 전달 - 다시 코어에 전달하는 방식이다


데이터소스가 무엇인가??
