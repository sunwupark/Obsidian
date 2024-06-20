
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

