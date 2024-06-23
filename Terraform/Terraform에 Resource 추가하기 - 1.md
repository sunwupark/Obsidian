## Terraform 작동방식
![[Pasted image 20240623202949.png]]

아마 정확히 이해가 안될 수 도 있는데

전체적인 구조는 main.tf 에다가 원하는 전체적인 구성을 입력한다 + Friend 내용을 입력한다. 이것이 Terraform Core이다. 이 Terraform Core는 런다음 Sunwu Provider를 활용하여 Friend의 처리를 한다. 이 요청은 Client에서 받은다음 Product-API의 소통을 통해서 그 답을 돌려주게된다!.

즉 외부, AWS나 네이버의 새로운 API를 만들기 위해서는 AWS Provider에 새로운 리소스의 내용을 추가한다. 여기서 이 리소스는 클라이언트의 함수를 호출한다. 이 호출한 함수를 통해 실제 Upstream API(네이버, AWS)를 작성하여 인프라를 구축하는 로직을 작성하고 돌려주는 것이다!

나는 테스트를 하기 위해 간단한 Sunwu Provider를 작성하고, 이 요청을 처리할 수 있는 클라이언트 라이브러리와 Sunwu-Product-API를 작성하여 전체적인 구성을 완료할 것이다

## Sunwu Terraform Provider

나만의 프로바이더를 바로 만들떄 기본적인 틀을 하시코프사에서 제공을 해준다
링크: https://github.com/hashicorp/terraform-provider-scaffolding-framework

위의 링크의 내용을 클론하여 아래의 단계를 거쳐 추가해보자!

1. go.mod 에서 모듈을 본인이 원하는 이름으로 변경한다
2. 버전 및 require도 본인의 클라이언트에서 가져와야함으로 변경한다
3. 기존 hashicups의 주소로 되어있는 것과 registry 도 본인의 것으로 변경해야한다!
	 ![[Pasted image 20240623201546.png]]

	
4. Provider의 메타데이터도 본인의 명의로 바꿔준다
```
func (p *hashicupsProvider) Metadata(_ context.Context, _ provider.MetadataRequest, resp *provider.MetadataResponse) {
	resp.TypeName = "sunwupark"
	resp.Version = p.version
}
```
=> returns provider type name.

설명: 테라폼이 리소스를 어떻게 처리할지에 대한 내부 정보를 포함하며, 리소스의 이름이나 리소스가 속한 프로바이더의 유형 등을 명시합니다.
예를 들어, 메타 데이터를 통해 테라폼은 리소스가 어떤 프로바이더에 속하는지 알 수 있고, 이를 기반으로 적절한 프로바이더의 구현을 사용하여 리소스 관리 작업을 수행할 수 있습니다

5. Provider Schema를 변경한다
```
func (p *hashicupsProvider) Schema(_ context.Context, _ provider.SchemaRequest, resp *provider.SchemaResponse) {
	resp.Schema = schema.Schema{
		Attributes: map[string]schema.Attribute{
			"host": schema.StringAttribute{
				Optional: true,
			},
			"username": schema.StringAttribute{
				Optional: true,
			},
			"password": schema.StringAttribute{
				Optional:  true,
				Sensitive: true,
			},
		},
	}
}
```
=> Schema defines the provider-level schema for configuration data.

설명: Schema는 리소스의 구성 가능한 속성들을 정의합니다. 각 속성의 데이터 타입, 기본값, 필수 여부 등을 설정할 수 있으며, 이 정보는 테라폼 사용자가 .tf 파일에서 리소스를 정의할 때 필요한 구성 요소와 그 제약사항을 지정합니다.

6. Provider Resource에 본인이 등록하고픈 리소스를 등록한다
```
// Resources defines the resources implemented in the provider.
func (p *hashicupsProvider) Resources(_ context.Context) []func() resource.Resource {
	return []func() resource.Resource{
		NewFriendResource,
	}
}
```
=> 나는 새로운 친구 리소스를 만들었음으로 친구 리소스를 등록하였다

7. friendResource만들기
=> 친구 리소스를 등록했으니 그 리소스를 직접 만들어보자!
리소스를 만드는 과정에서 클라이언트를 만들어줘야한다! (뒤에서 설명)
```
	"github.com/sunwupark/hashicups-client-go"
```

해당하는 함수는 나중에 만드는 것으로 하고!

```
func NewFriendResource() resource.Resource {
	return &friendResource{}
}
```
위와 같이 새로운 리소스를 위의 프로바이더에서 바로 사용할 수 있도록 함수를 만들어준다
밑에다가는 필요한 내용, 모델등의 내용이 들어가게된다

```
type friendResourceModel struct {
	ID          types.String `tfsdk:"id"`
	Name        types.String `tfsdk:"name"`
	Address     types.String `tfsdk:"address"`
	Description types.String `tfsdk:"description"`
	Image       types.String `tfsdk:"image"`
}

func (r *friendResource) Metadata(_ context.Context, req resource.MetadataRequest, resp *resource.MetadataResponse) {
	resp.TypeName = req.ProviderTypeName + "_friend"
}

func (r *friendResource) Schema(_ context.Context, _ resource.SchemaRequest, resp *resource.SchemaResponse) {
	resp.Schema = schema.Schema{
		Attributes: map[string]schema.Attribute{
			"id": schema.StringAttribute{
				Computed: true,
				PlanModifiers: []planmodifier.String{
					stringplanmodifier.UseStateForUnknown(),
				},
			},
			"name": schema.StringAttribute{
				Optional: true,
			},
			"address": schema.StringAttribute{
				Optional: true,
			},
			"description": schema.StringAttribute{
				Optional: true,
			},
			"image": schema.StringAttribute{
				Optional: true,
			},
		},
	}
}
```

### What is Computed ?

테라폼에서 Computed 속성은 해당 필드가 사용자에 의해 설정되는 것이 아니라, 테라폼 실행 중에 동적으로 계산되거나, API 응답 등 외부 소스로부터 동적으로 할당받는 값을 나타냅니다. 즉, 이 속성은 테라폼 사용자가 직접 값을 제공하는 것이 아니라, 테라폼이나 해당 프로바이더의 내부 로직에 의해 자동으로 결정됩니다.

간단한 예시로 위와 같은 모델과 Schema를 만든다고 생각을 해보자.
여기서 Model과 Schema의 차이가 뭔지 궁금할 수 있는데 

friendResourceModel은 데이터를 프로바이더 내에서 관리하고 API와의 상호작용을 위해 사용되는 반면, Schema는 사용자가 Terraform 구성을 통해 프로바이더와 상호작용하는 방식을 정의


이제 이 리소스에 대해서 CRUD를 만들어줘야한다!
```
func (r *friendResource) Create(ctx context.Context, req resource.CreateRequest, resp *resource.CreateResponse) {
	var plan friendResourceModel
	diags := req.Plan.Get(ctx, &plan)
	resp.Diagnostics.Append(diags...)
	if resp.Diagnostics.HasError() {
		return
	}

	friend := hashicups.Friend{
		Name:        plan.Name.ValueString(),
		Address:     plan.Address.ValueString(),
		Description: plan.Description.ValueString(),
		Image:       plan.Image.ValueString(),
	}

	createdFriend, err := r.client.CreateFriend([]hashicups.Friend{friend})
	if err != nil {
		resp.Diagnostics.AddError(
			"Error creating friend",
			"Could not create friend, unexpected error: "+err.Error(),
		)
		return
	}

	plan.ID = types.StringValue(strconv.Itoa(createdFriend.ID))
	plan.Name = types.StringValue(createdFriend.Name)
	plan.Address = types.StringValue(createdFriend.Address)
	plan.Description = types.StringValue(createdFriend.Description)
	plan.Image = types.StringValue(createdFriend.Image)

	diags = resp.State.Set(ctx, plan)
	resp.Diagnostics.Append(diags...)
	if resp.Diagnostics.HasError() {
		return
	}
}
```

### 단계
1. req.Plan.Get(ctx, &plan)는 Terraform 계획에서 필요한 데이터를 추출하여 friendResourceModel 에 채웁니다. => 리소스 생성에 필요한 모든 데이터를 담고 있다.
2. hashicups.Friend 구조체 인스턴스를 생성하고, 계획에서 추출한 데이터로 필드를 채움
3. r.client.CreateFriend([]hashicups.Friend{friend})는 설정된 데이터로 외부 시스템(API)에 리소스 생성을 요청. 성공적으로 생성되면, 생성된 리소스의 정보를 createdFriend에 반환.
4. API로부터 반환받은 createdFriend 정보를 기반으로 plan 구조체의 필드를 업데이트 types.StringValue()는 각 필드 값들을 Terraform이 처리할 수 있는 형식으로 변환
5. resp.State.Set(ctx, plan)을 호출하여 Terraform 상태에 리소스 정보를 저장
=> 진단 정보는 계속해서 반환하여 resp.Diagnostics.Append(diags...)로 추가합니다.

위와같은 과정으로 나머지 Read, Update, Delete, 등의 함수들을 만들어줍니다!


8. Terraform Core만들기
위와 같이 작성한 내용을 바탕으로 main.tf를 만들어줍니다

```
terraform {
    required_providers {
        sunwupark = {
            source = "registry.terraform.io/study/sunwupark-ossca"
        }
    }   
}

provider "sunwupark" {
    username = "education"
    password = "test123"
    host     = "http://localhost:19090"
}

resource "sunwupark_friend" "friend" {
    name = "sunwupark"
    address = "Seoul"
    description = "Hello, I'm sunwupark"
    image = "https://avatars.githubusercontent.com/u/12345678?v=4"
}
```

물론 지금 당장은 실행이 안됩니다!! 클라이언트를 만들지 않았기 때문이죠! 다음은 클라이언트를 만들어 보겠습니다

### 다음 클라이언트와 Product-API만들기!
[[Terraform에 Resource 추가하기 - 2]]