# Pulumi 다운로드 및 설치

이 페이지는 Pulumi를 다운로드하고 설치하는 자세한 방법을 제공한다.

## 운영 체제 선택

{{< chooser os "macos,windows,linux" >}}

{{% choosable os macos %}}

<h3 class="no-anchor pt-4"><i class="fas fa-box pr-2"></i> Homebrew 패키지 관리자</h3>

```bash
$ brew install pulumi/tap/pulumi
```

<h3 class="no-anchor pt-4"><i class="fas fa-download pr-2"></i> macOS 바이너리 다운로드</h3>

<a class="btn btn-secondary mx-2" href="https://get.pulumi.com/releases/sdk/pulumi-v{{< latest-version >}}-darwin-x64.tar.gz">amd64</a>
<a class="btn btn-secondary mx-2" href="https://get.pulumi.com/releases/sdk/pulumi-v{{< latest-version >}}-darwin-arm64.tar.gz">arm64</a>

macOS Sierra (10.12) 이상 버전이 필요하다.

Pulumi의 최신 버전은 {{< latest-version >}}이다. 이전 버전은 [Available Versions](/docs/install/versions/) 페이지에서 확인할 수 있다. 기능, 버그 수정 및 기타 사항은 [CHANGELOG](https://github.com/pulumi/pulumi/blob/master/CHANGELOG.md)에서 확인할 수 있다.

{{< get-started-note >}}

{{% /choosable %}}

{{% choosable os linux %}}

<div class="mb-6 border-solid border-b-2 border-gray-200">
<div class="w-full md:w-3/4">
<h3 class="no-anchor pt-4"><i class="fas fa-box pr-2"></i> 설치 스크립트</h3>

```bash
$ curl -fsSL https://get.pulumi.com | sh
```

</div>
<div class="w-full">
<h3 class="no-anchor pt-4"><i class="fas fa-download pr-2"></i> Linux 바이너리 다운로드</h3>
<p><a class="btn btn-secondary mx-2" href="https://get.pulumi.com/releases/sdk/pulumi-v{{< latest-version >}}-linux-x64.tar.gz">amd64</a></p>
</div>
</div>

Pulumi의 최신 버전은 {{< latest-version >}}이다. 이전 버전은 [Available Versions](/docs/install/versions/) 페이지에서 확인할 수 있다. 기능, 버그 수정 및 기타 사항은 [CHANGELOG](https://github.com/pulumi/pulumi/blob/master/CHANGELOG.md)에서 확인할 수 있다.

{{< get-started-note >}}

{{% /choosable %}}

{{% choosable os windows %}}

<div class="mb-6 border-solid border-b-2 border-gray-200">
<div class="w-full md:w-3/4">
<h3 class="no-anchor pt-4"><i class="fas fa-box pr-2"></i> 설치 프로그램 (MSI)</h3>
<p>
<a class="btn btn-secondary mx-2" href="https://github.com/pulumi/pulumi-winget/releases/download/v{{< latest-version >}}/pulumi-{{< latest-version >}}-windows-x64.msi">amd64</a>
</p>
</div>
<div class="w-full">
<h3 class="no-anchor pt-4"><i class="fas fa-download pr-2"></i> Windows 바이너리 다운로드</h3>
<p>
<a class="btn btn-secondary mx-2" href="https://get.pulumi.com/releases/sdk/pulumi-v{{< latest-version >}}-windows-x64.zip">amd64</a>
</p>
</div>
</div>

Windows 8 이상 버전이 지원된다.

Pulumi의 최신 버전은 {{< latest-version >}}이다. 이전 버전은 [Available Versions](/docs/install/versions/) 페이지에서 확인할 수 있다. 기능, 버그 수정 및 기타 사항은 [CHANGELOG](https://github.com/pulumi/pulumi/blob/master/CHANGELOG.md)에서 확인할 수 있다.

{{< get-started-note >}}

{{% /choosable %}}

{{% /chooser %}}

### 다른 설치 방법

Pulumi를 설치하는 방법은 다양하다:

{{< chooser os "macos,windows,linux" >}}

{{% choosable os macos %}}
<div class="accordion-item text-2xl py-3 border-t-2">
<input type="checkbox" class="absolute hidden" id="macos-official-homebrew-tap" />
<label for="macos-official-homebrew-tap" class="accordion-label">
<h5 class="mt-2 w-2/3">공식 Pulumi Homebrew Tap</h5>
<div class="flex flex-grow justify-end items-center">
<span class="closed-accordion">+</span>
<span class="open-accordion hidden">-</span>
</div>
</label>
<div class="accordion-item-body-no-animation text-base">

Pulumi는 [Homebrew 패키지 관리자](https://brew.sh/)와 공식 [Pulumi Homebrew Tap](https://github.com/pulumi/homebrew-tap/)을 통해 설치할 수 있다:

```bash
$ brew install pulumi/tap/pulumi
```

이 명령은 `pulumi` CLI를 일반적인 설치 경로(종종 `/usr/local/bin/pulumi`)에 설치하고 경로에 추가한다.

추후 업데이트는 다음 명령을 통해 설치할 수 있다:

```bash
$ brew upgrade pulumi
```

</div>
</div>

<div class="accordion-item text-2xl py-3 border-t-2">
<input type="checkbox" class="absolute hidden" id="macos-community-homebrew-tap" />
<label for="macos-community-homebrew-tap" class="accordion-label">
<h5 class="mt-2 w-2/3">커뮤니티 Homebrew</h5>
<div class="flex flex-grow justify-end items-center">
<span class="closed-accordion">+</span>
<span class="open-accordion hidden">-</span>
</div>
</label>
<div class="accordion-item-body-no-animation text-base">

Pulumi 공식 Tap이 없더라도, Pulumi를 커뮤니티 Homebrew로 설치할 수 있다:

```bash
$ brew install pulumi
```

</div>
</div>

<div class="accordion-item text-2xl py-3 border-t-2">
<input type="checkbox" class="absolute hidden" id="macos-macports" />
<label for="macos-macports" class="accordion-label">
<h5 class="mt-2 w-2/3">MacPorts</h5>
<div class="flex flex-grow justify-end items-center">
<span class="closed-accordion">+</span>
<span class="open-accordion hidden">-</span>
</div>
</label>
<div class="accordion-item-body-no-animation text-base">

Pulumi는 [MacPorts 패키지 관리자](https://www.macports.org/)를 통해 설치할 수 있다:

```bash
$ sudo port install pulumi
```

이 명령은 `pulumi` CLI를 `/opt/local/bin/pulumi`에 설치하고 경로에 추가한다.

추후 업데이트는 `upgrade outdated` 명령을 통해 설치할 수 있다:

```bash
$ sudo port upgrade outdated
```

</div>
</div>

<div class="accordion-item text-2xl py-3 border-t-2">
<input type="checkbox" class="absolute hidden" id="macos-installation-script" />
<label for="macos-installation-script" class="accordion-label">
<h5 class="mt-2 w-2/3">설치 스크립트</h5>
<div class="flex flex-grow justify-end items-center">
<span class="closed-accordion">+</span>
<span class="open-accordion hidden">-</span>
</div>
</label>
<div class="accordion-item-body-no-animation text-base">

대안으로 설치 스크립트를 실행할 수 있다:

```bash
$ curl -fsSL https://get.pulumi.com | sh
```

이 명령은 `pulumi` CLI를 `~/.pulumi/bin`에 설치하고 경로에 추가한다. 자동으로 `pulumi`를 경로에 추가할 수 없을 경우, 수동으로 추가해야 한다.

[Unix에서 영구적으로 $PATH 설정하는 방법](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux-unix)을 참조할 수 있다.

설치 스크립트는 새로운 업데이트를 설치하기 위해 재실행할 수 있다.

</div>
</div>

<div class="accordion-item text-2xl py-3 border-t-2 border-b-2">
<input type="checkbox" class="absolute hidden" id="macos-manual-installation" />
<label for="macos-manual-installation" class="accordion-label">
<h5 class="mt-2 w-2/3">수동 설치</h5>
<div class="flex flex-grow justify-end items-center">
<span class="closed-accordion">+</span>
<span class="open-accordion hidden">-</span>
</div>
</label>
<div class="accordion-item-body-no-animation text-base">

이전 방법을 사용하지 않으려면 Pulumi를 수동으로 설치할 수 있다.

1. [macOS용 Pulumi {{< latest-version >}}](https://get.pulumi.com/releases/sdk/pulumi-v{{

< latest-version >}}-darwin-x64.tar.gz)을 다운로드한다. 이전 버전 및 릴리스 노트는 [Available Versions](/docs/install/versions/) 페이지를 참조한다.

1. tarball 파일을 압축 해제하고 `pulumi` 디렉터리 내의 바이너리를 시스템 `$PATH`에 포함된 디렉터리로 이동시킨다.

</div>
</div>

{{% /choosable %}}

{{% choosable os linux %}}

<div class="accordion-item text-2xl py-3 border-t-2">
<input type="checkbox" class="absolute hidden" id="linux-installation-script" />
<label for="linux-installation-script" class="accordion-label">
<h5 class="mt-2 w-2/3">설치 스크립트</h5>
<div class="flex flex-grow justify-end items-center">
<span class="closed-accordion">+</span>
<span class="open-accordion hidden">-</span>
</div>
</label>
<div class="accordion-item-body-no-animation text-base">

설치를 위해, 설치 스크립트를 실행한다:

```bash
$ curl -fsSL https://get.pulumi.com | sh
```

이 명령은 `pulumi` CLI를 `~/.pulumi/bin`에 설치하고 경로에 추가한다. 자동으로 `pulumi`를 경로에 추가할 수 없을 경우, 수동으로 추가해야 한다.

[Unix에서 영구적으로 $PATH 설정하는 방법](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux-unix)을 참조할 수 있다.

</div>
</div>

<div class="accordion-item text-2xl py-3 border-t-2 border-b-2">
<input type="checkbox" class="absolute hidden" id="linux-manual-installation" />
<label for="linux-manual-installation" class="accordion-label">
<h5 class="mt-2 w-2/3">수동 설치</h5>
<div class="flex flex-grow justify-end items-center">
<span class="closed-accordion">+</span>
<span class="open-accordion hidden">-</span>
</div>
</label>
<div class="accordion-item-body-no-animation text-base">

대안으로, Pulumi를 수동으로 설치할 수 있다. Pulumi에서는 Linux용 미리 빌드된 바이너리를 제공한다.

1. [Linux x64용 Pulumi {{< latest-version >}}](https://get.pulumi.com/releases/sdk/pulumi-v{{< latest-version >}}-linux-x64.tar.gz)을 다운로드한다. 이전 버전 및 릴리스 노트는 [Available Versions](/docs/install/versions/) 페이지를 참조한다.

1. tarball 파일을 압축 해제하고 `pulumi` 디렉터리 내의 바이너리를 시스템 `$PATH`에 포함된 디렉터리로 이동시킨다.

</div>
</div>

{{% /choosable %}}

{{% choosable os windows %}}

<div class="accordion-item text-2xl py-3 border-t-2">
<input type="checkbox" class="absolute hidden" id="windows-chocolatey" />
<label for="windows-chocolatey" class="accordion-label">
<h5 class="mt-2 w-2/3">Chocolatey</h5>
<div class="flex flex-grow justify-end items-center">
<span class="closed-accordion">+</span>
<span class="open-accordion hidden">-</span>
</div>
</label>
<div class="accordion-item-body-no-animation text-base">

[Chocolatey 패키지 관리자](https://chocolatey.org)를 사용하여 Pulumi를 설치할 수 있다:

```powershell
> choco install pulumi
```

이 명령은 `pulumi` CLI를 일반적인 설치 경로(종종 `$($env:ChocolateyInstall)\lib\pulumi`)에 설치하고 [shims](https://docs.chocolatey.org/en-us/features/shim)(일반적으로 `$($env:ChocolateyInstall)\bin`)을 생성하여 Pulumi를 경로에 추가한다.

추후 업데이트는 다음 명령을 통해 설치할 수 있다:

```powershell
> choco upgrade pulumi
```

</div>
</div>

<div class="accordion-item text-2xl py-3 border-t-2">
<input type="checkbox" class="absolute hidden" id="windows-winget" />
<label for="windows-winget" class="accordion-label">
<h5 class="mt-2 w-2/3">Windows 패키지 관리자 (Winget)</h5>
<div class="flex flex-grow justify-end items-center">
<span class="closed-accordion">+</span>
<span class="open-accordion hidden">-</span>
</div>
</label>
<div class="accordion-item-body-no-animation text-base">

Pulumi는 Windows 패키지 관리자 [`winget`](https://github.com/microsoft/winget-cli/) CLI를 사용하여 설치할 수 있다. 이 기능은 Windows 11 이후 버전에 내장되어 있다.

```powershell
> winget install pulumi
```

Pulumi를 최신 버전으로 업데이트하려면 다음 명령을 실행한다:

```powershell
> winget upgrade pulumi
```

</div>
</div>

<div class="accordion-item text-2xl py-3 border-t-2">
<input type="checkbox" class="absolute hidden" id="windows-standalone-installer" />
<label for="windows-standalone-installer" class="accordion-label">
<h5 class="mt-2 w-2/3">독립 실행형 설치 프로그램 (MSI)</h5>
<div class="flex flex-grow justify-end items-center">
<span class="closed-accordion">+</span>
<span class="open-accordion hidden">-</span>
</div>
</label>
<div class="accordion-item-body-no-animation text-base">

최신 [Windows x64용 Pulumi 설치 프로그램](https://github.com/pulumi/pulumi-winget/releases/download/v{{< latest-version >}}/pulumi-{{< latest-version >}}-windows-x64.msi)을 다운로드하여 일반적인 설치 프로그램처럼 실행한다. Pulumi는 자동으로 경로에 추가되어 전체 시스템에서 사용 가능해진다.

</div>
</div>

<div class="accordion-item text-2xl py-3 border-t-2">
<input type="checkbox" class="absolute hidden" id="windows-installation-script" />
<label for="windows-installation-script" class="accordion-label">
<h5 class="mt-2 w-2/3">설치 스크립트</h5>
<div class="flex flex-grow justify-end items-center">
<span class="closed-accordion">+</span>
<span class="open-accordion hidden">-</span>
</div>
</label>
<div class="accordion-item-body-no-animation text-base">

1. 새 명령 프롬프트 창을 연다 (**WIN+R**: `cmd.exe`):

1. 설치 스크립트를 실행한다:

```bat
> @"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12; iex ((New-Object System.Net.WebClient).DownloadString('https://get.pulumi.com/install.ps1'))" && SET "PATH=%PATH%;%USERPROFILE%\.pulumi\bin"
```

이 명령은 `pulumi.exe` CLI를 `%USERPROFILE%\.pulumi\bin`에 설치하고 경로에 추가한다.

</div>
</div>

<div class="accordion-item text-2xl py-3 border-t-2 border-b-2">
<input type="checkbox" class="absolute hidden" id="windows-manual-installation" />
<label for="windows-manual-installation" class="accordion-label">
<h5 class="mt-2 w-2/3">수동 설치</h5>
<div class="flex flex-grow justify-end items-center">
<span class="closed-accordion">+</span>
<span class="open-accordion hidden">-</span>
</div>
</label>
<div class="accordion-item-body-no-animation text-base">

대안으로, Windows x64용 Pulumi 바이너리를 사용하여 수동으로 설치할 수 있다.

1. [Windows x64용 Pulumi {{< latest-version >}} 바이너리](https://get.pulumi.com/releases/sdk/pulumi-v{{< latest-version >}}-windows-x64.zip)을 다운로드한다. 이전 버전 및 릴리스 노트는 [Available Versions](/docs/install/versions/) 페이지를 참조한다.

1. 파일을 압축 해제하고 내용을 `C:\pulumi`와 같은 폴더에 추출한다.

1. `C:\pulumi\bin`을 경로에 추가한다: **System Properties** -> **Advanced** -> **Environment Variables** -> **User Variables** -> **Path** -> **Edit**.

</div>
</div>

{{% /choosable %}}

{{% /chooser %}}

## 설치 확인

Pulumi를 설치한 후, `pulumi` CLI를 실행하여 모든 것이 정상적으로 작동하는지 확인한다:

{{% chooser os "macos,windows,linux" %}}

{{% choosable os macos %}}

```bash
$ pulumi version
v{{< latest-version >}}
```

{{% /choosable %}}

{{% choosable os linux %}}

```bash
$ pulumi version
v{{< latest-version >}}
```

{{% /

choosable %}}

{{% choosable os windows %}}

```bash
> pulumi version
v{{< latest-version >}}
```

{{% /choosable %}}

{{% /chooser %}}

### 일반적인 오류 및 경고

다음은 설치 관련하여 자주 발생하는 오류 또는 경고들이다.

#### Pulumi를 찾을 수 없음 오류

`pulumi`를 찾을 수 없다는 오류가 발생하면, 이는 경로가 올바르게 설정되지 않았음을 의미한다. 시스템의 `$PATH`에 설치된 `pulumi` CLI가 포함된 디렉터리가 포함되어 있는지 확인해야 한다.

#### 새로운 버전 경고

Pulumi의 새로운 버전이 사용 가능할 경우, CLI는 모든 사용 가능한 명령을 실행할 때 다음과 같은 경고를 표시한다:

{{% chooser os "macos,windows,linux" %}}

{{% choosable os macos %}}

```
warning: A new version of Pulumi is available. To upgrade from version '2.17.26' to '{{< latest-version >}}', run
   $ curl -sSL https://get.pulumi.com | sh
or visit https://pulumi.com/docs/reference/install/ for manual instructions and release notes.
```

{{% /choosable %}}

{{% choosable os linux %}}

```
warning: A new version of Pulumi is available. To upgrade from version '2.17.26' to '{{< latest-version >}}', run
   $ curl -sSL https://get.pulumi.com | sh
or visit https://pulumi.com/docs/reference/install/ for manual instructions and release notes.
```

{{% /choosable %}}

{{% choosable os windows %}}

```
warning: A new version of Pulumi is available. To upgrade from version '2.17.26' to '{{< latest-version >}}', run
   > "%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://get.pulumi.com/install.ps1'))"
or visit https://pulumi.com/docs/reference/install/ for manual instructions and release notes.
```

{{% /choosable %}}

{{% /chooser %}}

{{< skip-version-check >}}

## Pulumi 업그레이드

Pulumi 2.0에서 3.0으로 업그레이드하는 경우, [migration guide](/docs/install/migrating-3.0)를 참조해야 한다.

## 베타 및 이전 버전 설치

대부분의 설치 방법은 기본적으로 최신 버전을 선택한다. 특정 버전을 설치하려면 다음 명령을 사용한다. 버전 목록은 [Available Versions](/docs/install/versions/) 페이지에서 확인할 수 있다.

{{% chooser os "macos,windows,linux" %}}

{{% choosable os macos %}}

<h3 class="no-anchor pt-4">macOS 설치 스크립트</h3>

```bash
$ curl -fsSL https://get.pulumi.com | sh -s -- --version <version>
```

{{% /choosable %}}

{{% choosable os linux %}}

<h3 class="no-anchor pt-4">Linux 설치 스크립트</h3>

설치를 위해 설치 스크립트를 실행한다:

```bash
$ curl -fsSL https://get.pulumi.com | sh -s -- --version <version>
```

{{% /choosable %}}

{{% choosable os windows %}}

<h3 class="no-anchor pt-4">Chocolatey</h3>

[Chocolatey 패키지 관리자](https://chocolatey.org)를 사용하여 특정 버전을 지정할 수 있다:

```powershell
> choco install pulumi --version <version>
```

<h3 class="no-anchor pt-4">Windows 설치 스크립트</h3>

1. 새 명령 프롬프트 창을 연다 (**WIN+R**: `cmd.exe`):

1. 설치 스크립트를 실행한다(버전 번호를 `<version>`으로 대체한다):

```powershell
> @"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12; $version = '<version>'; iex ((New-Object System.Net.WebClient).DownloadString('https://get.pulumi.com/install.ps1')).Replace('${Version}', $version)" && SET "PATH=%PATH%;%USERPROFILE%\.pulumi\bin"
```

{{% /choosable %}}

{{% /chooser %}}

## 개발 릴리스 설치

특정 버전을 설치하는 것 외에도 최신 개발 버전도 자동으로 설치할 수 있다. 이 버전에는 주 개발 브랜치에 병합된 최신 변경 사항이 포함되어 있다.

{{% chooser os "macos,windows,linux" %}}

{{% choosable os macos %}}

### macOS 설치 스크립트

```bash
$ curl -fsSL https://get.pulumi.com | sh -s -- --version dev
```

{{% /choosable %}}

{{% choosable os linux %}}

### Linux 설치 스크립트

설치를 위해 설치 스크립트를 실행한다:

```bash
$ curl -fsSL https://get.pulumi.com | sh -s -- --version dev
```

{{% /choosable %}}

{{% choosable os windows %}}

### Windows 설치 스크립트

1. 새 명령 프롬프트 창을 연다 (**WIN+R**: `cmd.exe`):

1. 설치 스크립트를 실행한다:

```powershell
> @"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12; iex ((New-Object System.Net.WebClient).DownloadString('https://get.pulumi.com/install.ps1')) -version dev" && SET "PATH=%PATH%;%USERPROFILE%\.pulumi\bin"
```

{{% /choosable %}}

{{% /chooser %}}

## Pulumi 제거

Pulumi를 제거하려면 사용한 설치 방법의 명령어를 사용한다. Pulumi를 수동으로 설치한 경우, 생성한 `pulumi` 디렉터리를 삭제한다. 이후, 플러그인 및 기타 캐시된 메타데이터가 포함된 홈 디렉터리의 `.pulumi` 폴더를 제거한다.