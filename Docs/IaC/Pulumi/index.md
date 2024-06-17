# Pulumi 소개

Pulumi는 현대적인 인프라 코드 플랫폼으로, 기존의 프로그래밍 언어(TypeScript, JavaScript, Python, Go, .NET, Java)와 마크업 언어(YAML)를 활용하여 클라우드 리소스와 상호 작용하는 시스템이다. Pulumi는 다운로드 가능한 CLI, 런타임, 라이브러리, 호스팅 서비스가 함께 작동하여 클라우드 인프라를 프로비저닝, 업데이트 및 관리하는 강력한 플랫폼을 제공한다.

Pulumi를 처음 사용하는 경우, AWS, Azure, Google Cloud 또는 Kubernetes 배포를 처음부터 끝까지 안내하는 시작 가이드를 참조하는 것이 좋다.

## Pulumi란 무엇인가?

Pulumi는 친숙한 프로그래밍 언어와 도구를 사용하여 클라우드 인프라를 구축, 배포 및 관리할 수 있는 인프라 코드 플랫폼이다. Pulumi는 무료이며, 오픈 소스이고, Pulumi Cloud와 선택적으로 페어링하여 인프라 관리를 안전하고 신뢰할 수 있으며 간편하게 만든다.

## 지원 언어 및 SDK

Pulumi는 멀티 언어 인프라 코드 도구로서 오늘날 가장 일반적인 범용 프로그래밍 및 마크업 언어를 지원한다. 모든 Pulumi 지원 언어는 주요 클라우드에서 인프라를 프로비저닝 및 관리할 수 있으며, 일부 언어는 다른 언어에서 아직 사용할 수 없는 기능을 제공할 수 있다. 현재 지원되는 언어 및 런타임은 다음과 같다.

- TypeScript & JavaScript (Node.js)
- Python
- Go
- C#, VB, F# (.NET)
- Java
- Pulumi YAML

원하는 언어가 목록에 없으면 곧 추가될 수 있다. Pulumi는 오픈 소스이며, 직접 언어를 추가할 수도 있다. 추가 언어에 대한 질문은 Pulumi의 언어 및 SDK 문서를 참조하면 된다.

## Pulumi 작동 원리

Pulumi 플랫폼은 여러 구성 요소로 이루어져 있다.

- **소프트웨어 개발 키트 (SDK)**: Pulumi SDK는 공급자가 관리할 수 있는 각 유형의 리소스에 대한 바인딩을 제공한다. 이를 통해 모든 클라우드 및 모든 공급자에서 클라우드 리소스를 정의하고 관리할 수 있는 필요한 도구와 라이브러리를 제공한다.
- **명령줄 인터페이스 (CLI)**: Pulumi는 주로 CLI를 사용하여 제어된다. Pulumi Cloud와 협력하여 클라우드 앱 및 인프라에 변경 사항을 배포한다. 팀 내에서 누가 언제 무엇을 업데이트했는지에 대한 기록을 유지한다. 이 CLI는 내부 루프 생산성뿐만 아니라 지속적 통합 및 전달 시나리오를 위해 설계되었다.
- **배포 엔진**: 배포 엔진은 인프라의 현재 상태를 프로그램이 표현한 원하는 상태로 구동하는 데 필요한 작업 세트를 계산하는 역할을 한다.

Pulumi 프로그램은 일반적인 프로그래밍 언어로 작성되어 클라우드 인프라가 어떻게 구성되어야 하는지를 설명한다. 프로그램에서 새로운 인프라를 선언하려면 리소스 객체를 할당하고 해당 속성이 인프라의 원하는 상태에 해당하도록 한다. 이러한 속성은 리소스 간의 필요한 종속성을 처리하고 필요한 경우 스택 외부로 내보낼 수도 있다.

프로그램은 프로젝트에 위치하며, 이는 프로그램의 소스 코드와 프로그램을 실행하는 방법에 대한 메타데이터가 포함된 디렉토리이다. 프로그램을 작성한 후 프로젝트 디렉토리 내에서 `pulumi up` 명령을 실행한다. 이 명령은 스택이라고 하는 프로그램의 격리되고 구성 가능한 인스턴스를 만든다. 스택은 애플리케이션 업데이트를 테스트하고 배포할 때 사용하는 서로 다른 배포 환경과 유사하다. 예를 들어, 개발, 스테이징 및 프로덕션 스택을 각각 생성하고 테스트할 수 있다.

## 예제

다음 프로그램은 단일 인그레스 규칙과 해당 보안 그룹을 사용하는 t2.micro 크기의 EC2 인스턴스를 가진 `web-sg`라는 이름의 AWS EC2 보안 그룹을 생성하는 방법을 보여준다.

보안 그룹을 사용하기 위해 EC2 리소스는 보안 그룹의 ID가 필요하다. Pulumi는 보안 그룹 리소스의 출력 속성 `id`를 통해 이를 가능하게 한다. Pulumi는 리소스 간의 종속성을 이해하고 리소스 간의 관계를 사용하여 병렬 처리를 최대화하고 스택이 인스턴스화될 때 올바른 순서를 보장한다.

마지막으로 서버의 결과 IP 주소와 DNS 이름은 스택 출력으로 내보내어 CLI 명령이나 다른 스택에서 해당 값을 액세스할 수 있도록 한다.

### Python 예제 코드

```python
import pulumi
import pulumi_aws as aws

group = aws.ec2.SecurityGroup(
    "web-sg",
    description="Enable HTTP access",
    ingress=[
        {
            "protocol": "tcp",
            "from_port": 80,
            "to_port": 80,
            "cidr_blocks": ["0.0.0.0/0"],
        }
    ],
)

server = aws.ec2.Instance(
    "web-server",
    ami="ami-0319ef1a70c93d5c8",
    instance_type="t2.micro",
    vpc_security_group_ids=[group.id],
)

pulumi.export("public_ip", server.public_ip)
pulumi.export("public_dns", server.public_dns)

```

[GitHub에서 전체 예제 보기](https://github.com/pulumi/examples)

## 심화 개념

다음 주제들은 Pulumi의 핵심 개념과 사용 방법에 대해 더 자세히 설명한다.

- [Pulumi 개념](https://www.pulumi.com/docs/intro/concepts/)
- [Pulumi 사용 방법](https://www.pulumi.com/docs/intro/usage/)
- [Pulumi CLI 문서](https://www.pulumi.com/docs/reference/cli/)

이 문서는 Pulumi의 기본 개념과 사용 방법을 이해하는 데 도움이 된다. Pulumi를 통해 클라우드 인프라를 보다 효율적으로 관리할 수 있다!

---

**Pulumi의 Infrastructure as Code SDK**는 모든 아키텍처와 클라우드에서 인프라를 구축하고 배포하는 가장 쉬운 방법이다. 익숙한 프로그래밍 언어를 사용하여 인프라를 더 빠르게 코드로 작성하고 배포할 수 있다. [Automation API](https://www.pulumi.com/docs/guides/automation-api/)를 사용하여 어디서든 IaC를 삽입할 수 있다.

선호하는 언어로 코드를 작성하면 Pulumi가 자동으로 자원을 프로비저닝하고 관리한다. [AWS](https://www.pulumi.com/docs/reference/clouds/aws/?utm_campaign=pulumi-pulumi-github-repo&utm_source=github.com&utm_medium=aws-reference-link), [Azure](https://www.pulumi.com/docs/reference/clouds/azure/?utm_campaign=pulumi-pulumi-github-repo&utm_source=github.com&utm_medium=azure-reference-link), [Google Cloud Platform](https://www.pulumi.com/docs/reference/clouds/gcp/?utm_campaign=pulumi-pulumi-github-repo&utm_source=github.com&utm_medium=gcp-reference-link), [Kubernetes](https://www.pulumi.com/docs/reference/clouds/kubernetes/?utm_campaign=pulumi-pulumi-github-repo&utm_source=github.com&utm_medium=kuberneters-reference-link) 및 [120+ 제공업체](https://www.pulumi.com/registry/?utm_campaign=pulumi-pulumi-github-repo&utm_source=github.com&utm_medium=providers-reference-link)를 사용하는 [infrastructure-as-code](https://www.pulumi.com/what-is/what-is-infrastructure-as-code/) 접근 방식을 통해 가능하다. YAML을 생략하고 익숙한 루프, 함수, 클래스 및 패키지 관리와 같은 표준 언어 기능을 사용할 수 있다.

예를 들어, 세 개의 웹 서버를 생성하려면 다음과 같이 한다:

```tsx
const aws = require("@pulumi/aws");
const sg = new aws.ec2.SecurityGroup("web-sg", {
    ingress: [{ protocol: "tcp", fromPort: 80, toPort: 80, cidrBlocks: ["0.0.0.0/0"] }],
});
for (let i = 0; i < 3; i++) {
    new aws.ec2.Instance(`web-${i}`, {
        ami: "ami-7172b611",
        instanceType: "t2.micro",
        vpcSecurityGroupIds: [sg.id],
        userData: `#!/bin/bash
            echo "Hello, World!" > index.html
            nohup python -m SimpleHTTPServer 80 &`,
    });
}

```

또는 매일 오전 8시 30분에 Hacker News를 아카이브하는 간단한 서버리스 타이머:

```tsx
const aws = require("@pulumi/aws");

const snapshots = new aws.dynamodb.Table("snapshots", {
    attributes: [{ name: "id", type: "S", }],
    hashKey: "id", billingMode: "PAY_PER_REQUEST",
});

aws.cloudwatch.onSchedule

("daily-yc-snapshot", "cron(30 8 * * ? *)", () => {
    require("https").get("<https://news.ycombinator.com>", res => {
        let content = "";
        res.setEncoding("utf8");
        res.on("data", chunk => content += chunk);
        res.on("end", () => new aws.sdk.DynamoDB.DocumentClient().put({
            TableName: snapshots.name.get(),
            Item: { date: Date.now(), content },
        }).promise());
    }).end();
});

```

컨테이너, 서버리스 및 인프라에 걸친 많은 예제들은 [pulumi/examples](https://github.com/pulumi/examples)에 있다.

Pulumi는 [Apache 2.0 라이선스](https://github.com/pulumi/pulumi/blob/master/LICENSE) 하에 오픈 소스이며, 많은 언어와 클라우드를 지원하고 쉽게 확장할 수 있다. 이 리포지토리에는 `pulumi` CLI, 언어 SDK 및 핵심 Pulumi 엔진이 포함되어 있으며, 개별 라이브러리는 자체 리포지토리에 있다.

## Pulumi 공식 사이트 링크

<img align="right" width="400" src="https://www.pulumi.com/images/docs/quickstart/console.png" />

- [**Pulumi 시작하기**](https://www.pulumi.com/docs/get-started/): Pulumi를 사용하여 AWS, Azure, Google Cloud 또는 Kubernetes에서 간단한 애플리케이션을 배포할 수 있다.
- [**학습하기**](https://www.pulumi.com/learn/): Pulumi 학습 경로를 따라가면서 실제 예제를 통해 모범 사례와 아키텍처 패턴을 배울 수 있다.
- [**예제**](https://github.com/pulumi/examples): 여러 언어, 클라우드 및 시나리오에 걸친 다양한 예제를 탐색할 수 있다. 여기에는 컨테이너, 서버리스 및 인프라가 포함된다.
- [**문서**](https://www.pulumi.com/docs/): Pulumi 개념을 배우고, 사용자 가이드를 따르고, 참조 문서를 확인할 수 있다.
- [**레지스트리**](https://www.pulumi.com/registry/): 필요한 자원을 포함한 Pulumi 패키지를 찾아볼 수 있다. 패키지를 프로젝트에 직접 설치하고 API 문서를 탐색하며 빌딩을 시작할 수 있다.
- [**Pulumi 로드맵**](https://github.com/orgs/pulumi/projects/44): 다가오는 분기와 아직 일정이 잡히지 않은 백로그의 계획된 작업을 검토할 수 있다.
- [**커뮤니티 Slack**](https://slack.pulumi.com/?utm_campaign=pulumi-pulumi-github-repo&utm_source=github.com&utm_medium=welcome-slack): Pulumi 커뮤니티 Slack에 참여할 수 있다.
- [**GitHub Discussions**](https://github.com/pulumi/pulumi/discussions): 질문을 하거나 Pulumi로 무엇을 구축하고 있는지 공유할 수 있다.

## 시작하기

선호하는 플랫폼과 클라우드에서 Pulumi를 사용하여 빠르게 시작하려면 [Get Started](https://www.pulumi.com/docs/quickstart/?utm_campaign=pulumi-pulumi-github-repo&utm_source=github.com&utm_medium=getting-started-quickstart) 가이드를 참조하면 된다.

다음 단계에서는 AWS 서버리스 람다를 사용하여 첫 번째 Pulumi 프로그램을 몇 분 만에 배포하는 방법을 보여준다:

1. **설치**:
    
    최신 Pulumi 릴리스를 설치하려면 다음 명령을 실행하면 된다(추가 설치 옵션에 대한 전체 [설치 지침](https://www.pulumi.com/docs/reference/install/?utm_campaign=pulumi-pulumi-github-repo&utm_source=github.com&utm_medium=getting-started-install)을 참조하면 된다):
    
    ```bash
    $ curl -fsSL <https://get.pulumi.com/> | sh
    
    ```
    
2. **프로젝트 생성**:
    
    설치 후 `pulumi new` 명령을 사용하여 시작할 수 있다:
    
    ```bash
    
    $ mkdir pulumi-demo && cd pulumi-demo
    $ pulumi new hello-aws-javascript
    
    ```
    
    `new` 명령은 모든 언어와 클라우드에 대한 템플릿을 제공한다. 인수를 지정하지 않고 실행하면 사용 가능한 프로젝트를 프롬프트한다. 이 명령은 JavaScript로 작성된 AWS 서버리스 람다 프로젝트를 생성했다.
    
3. **클라우드에 배포**:
    
    코드를 클라우드에 업로드하려면 `pulumi up`을 실행한다:
    
    ```bash
    $ pulumi up
    
    ```
    
    이 명령은 코드를 실행하는 데 필요한 모든 클라우드 자원을 생성한다. 프로젝트를 편집하고 후속 `pulumi up` 명령을 실행하면 변경 사항을 배포하기 위한 최소한의 차이를 계산한다.
    
4. **프로그램 사용**:
    
    코드가 배포되면 상호작용할 수 있다. 위의 예에서 엔드포인트를 커맨드라인에서 호출할 수 있다:
    
    ```bash
    $ curl $(pulumi stack output url)
    
    ```
    
5. **로그 액세스**:
    
    컨테이너나 함수를 사용하는 경우 Pulumi의 통합 로그 명령을 사용하여 모든 로그를 확인할 수 있다:
    
    ```bash
    $ pulumi logs -f
    
    ```
    
6. **자원 제거**:
    
    완료되면 프로그램에서 생성한 모든 자원을 제거할 수 있다.
    
    ```bash
    $ pulumi destroy -y
    
    ```
    

더 자세한 내용을 원하면 [pulumi.com](https://pulumi.com/?utm_campaign=pulumi-pulumi-github-repo&utm_source=github.com&utm_medium=getting-started-learn-more-home)를 방문하여 [튜토리얼](https://www.pulumi.com/docs/reference/tutorials/?utm_campaign=pulumi-pulumi-github-repo&utm_source=github.com&utm_medium=getting-started-learn-more-tutorials), [예제](https://github.com/pulumi/examples) 및 핵심 Pulumi CLI와 [프로그래밍 모델 개념](https://www.pulumi.com/docs/reference/concepts/?utm_campaign=pulumi-pulumi-github-repo&utm_source=github.com&utm_medium=getting-started-learn-more-concepts)에 대한 자세한 내용을 확인하면 된다.

## 플랫폼

### 언어

|  | 언어 | 상태 | 런타임 | 버전 |
| --- | --- | --- | --- | --- |
| <img src="https://www.pulumi.com/logos/tech/logo-js.png" height=38 /> | https://www.pulumi.com/docs/intro/languages/javascript/ | 안정됨 | Node.js | https://nodejs.org/en/about/previous-releases |
| <img src="https://www.pulumi.com/logos/tech/logo-ts.png" height=38 /> | https://www.pulumi.com/docs/intro/languages/javascript/ | 안정됨 | Node.js | https://nodejs.org/en/about/previous-releases |
| <img src="https://www.pulumi.com/logos/tech/logo-python.svg" height=38 /> | https://www.pulumi.com/docs/intro/languages/python/ | 안정됨 | Python | https://devguide.python.org/versions/#versions |
| <img src="https://www.pulumi.com/logos/tech/logo-golang.png" height=38 /> | https://www.pulumi.com/docs/intro/languages/go/ | 안정됨 | Go | https://go.dev/doc/devel/release#policy |
| <img src="https://www.pulumi.com/logos/tech/dotnet.svg" height=38 /> | https://www.pulumi.com/docs/intro/languages/dotnet/ | 안정됨 | .NET | https://dotnet.microsoft.com/en-us/platform/support/policy/dotnet-core#lifecycle |
| <img src="https://www.pulumi.com/logos/tech/java.svg" height=38 /> | https://www.pulumi.com/docs/intro/languages/java/ | 공개 미리보기 | JDK | 11+ |
| <img src="https://www.pulumi.com/logos/tech/yaml.svg" height=38 /> | https://www.pulumi.com/docs/intro/languages/yaml/ | 안정됨 | n/a | n/a |

### 지원 종료 릴리스

Pulumi CLI v1 및 v2는 더 이상 지원되지 않는다. 

- v2에서 v3로 마이그레이션하려면 [v3 마이그레이션 가이드](https://www.pulumi.com/docs/install/migrating-3.0/)를 참조하면 된다.

### 클라우드

지원되는 클라우드 및 인프라 제공업체의 전체 목록은 [레지스트리](https://www.pulumi.com/registry/)를 참조하면 된다.
