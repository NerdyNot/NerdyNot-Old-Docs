# Remote AI Script Executor with OpenAI

이 문서는 원격 서버에서 명령을 실행하고, OpenAI 또는 Azure OpenAI를 사용하여 스크립트를 생성하는 Python 스크립트를 설명하고 테스트하는 방법을 다룬다.

## 1. 라이브러리 로드

먼저, 필요한 라이브러리를 로드해야 한다. 이 스크립트는 여러 외부 라이브러리에 의존한다.

```bash
pip install openai paramiko pywinrm PyYAML rich
```

```python
import os
import re
import paramiko
import winrm
import yaml
from openai import OpenAI, AzureOpenAI
from rich.console import Console
from rich.markdown import Markdown
from rich.panel import Panel
```

## 2. OpenAI 클라이언트 설정 함수

OpenAI 또는 Azure OpenAI 클라이언트를 설정하는 함수이다. YAML 구성 파일에서 API 키와 모델 정보를 읽어와 클라이언트를 초기화한다.

```python
def set_openai_client(openai_config):
    client_type = openai_config['type'].strip().lower()
    api_key = openai_config['api_key'].strip()
    model = openai_config['model'].strip()
    
    if client_type == "azure":
        azure_endpoint = openai_config['azure_endpoint'].strip()
        azure_apiversion = openai_config['azure_apiversion'].strip()
        os.environ['AZURE_OPENAI_API_KEY'] = api_key
        return AzureOpenAI(
            api_version=azure_apiversion,
            azure_endpoint=azure_endpoint
        ), client_type, model
    else:
        os.environ['OPENAI_API_KEY'] = api_key
        return OpenAI(api_key=api_key), client_type, model
```

## 3. 원격 Linux 서버에서 명령 실행 함수

Paramiko를 사용하여 원격 Linux 서버에서 명령을 실행하는 함수이다. SSH를 통해 서버에 접속하고 명령을 실행한 후, 결과를 반환한다.

```python
def run_remote_command_linux(host, port, username, password, command):
    ssh = paramiko.SSHClient()
    ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())
    ssh.connect(host, port=port, username=username, password=password)
    stdin, stdout, stderr = ssh.exec_command(command)
    output = stdout.read().decode('utf-8') + stderr.read().decode('utf-8')
    ssh.close()
    return output
```

### Linux 서버에서 명령 실행 테스트

테스트용으로 로컬에서 작동하는 SSH 서버를 사용할 수 있다. 테스트를 위해 아래 코드를 수정하여 로컬 또는 테스트 서버의 정보를 입력하고 실행한다.

```python
# Example test for running a command on a Linux server
# Replace these values with your test server details
test_host = 'your-test-host'
test_port = 22
test_username = 'your-username'
test_password = 'your-password'
test_command = 'uname -a'

# Run the command and print the output
try:
    output = run_remote_command_linux(test_host, test_port, test_username, test_password, test_command)
    print(output)
except Exception as e:
    print(f'Failed to run command: {e}')
```

## 4. 원격 Windows 서버에서 명령 실행 함수

pywinrm을 사용하여 원격 Windows 서버에서 명령을 실행하는 함수이다. WinRM을 통해 서버에 접속하고 명령을 실행한 후, 결과를 반환한다.

```python
def run_remote_command_windows(host, port, username, password, command):
    endpoint = f'http://{host}:{port}/wsman'
    session = winrm.Session(endpoint, auth=(username, password), transport='ntlm')
    result = session.run_ps(command)
    return result.std_out.decode('utf-8') + result.std_err.decode('utf-8')
```

### Windows 서버에서 명령 실행 테스트

테스트용으로 로컬에서 작동하는 Windows 서버를 사용할 수 있다. 테스트를 위해 아래 코드를 수정하여 로컬 또는 테스트 서버의 정보를 입력하고 실행한다.

```python
# Example test for running a command on a Windows server
# Replace these values with your test server details
test_host = 'your-windows-host'
test_port = 5985
test_username = 'your-username'
test_password = 'your-password'
test_command = 'Get-Process | Select-Object -First 5'

# Run the command and print the output
try:
    output = run_remote_command_windows(test_host, test_port, test_username, test_password, test_command)
    print(output)
except Exception as e:
    print(f'Failed to run command: {e}')
```

## 5. 스크립트 생성 함수

사용자의 입력과 시스템 정보를 기반으로 OpenAI 또는 Azure OpenAI를 사용하여 스크립트를 생성하는 함수이다. 시스템 정보와 셸 버전을 기반으로 스크립트를 생성한다.

```python
def generate_script(user_input, client_type, model, os_info, shell_version, os_type):
    system_prompt = f"""
    # Instruction
     - You are an assistant for a {os_type} operating system with the following details
     - You must only provide the Script, without any additional explanation or text. like description or ```bash or ```powershell or ```sh.
     - Your responses should be informative, visually appealing, logical and actionable.
     - Your responses should be very simple and complete.

    # Script Creation Rules
     - OS Information: {os_info}
     - Shell Version: {shell_version}
     - Based on the user's input, generate a script to accomplish the task.
     - To distinguish between each server, print the hostnames on environment variables.
    """
    console = Console()
    with console.status("[bold green]Generating Script...[/bold green]"):
        response = None
        if client_type == "azure":
            response = client.chat.completions.create(
                model=model,
                messages=[
                    {"role": "system", "content": system_prompt.strip()},
                    {"role": "user", "content": user_input}
                ],
                temperature=1,
                max_tokens=1024,
                top_p=1,
                frequency_penalty=0,
                presence_penalty=0
            )
        else:
            response = client.chat.completions.create(
                model=model,
                messages=[
                    {"role": "system", "content": system_prompt.strip()},
                    {"role": "user", "content": user_input}
                ],
                temperature=1,
                max_tokens=1024,
                top_p=1,
                frequency_penalty=0,
                presence_penalty=0
            )

    full_response = response.choices[0].message.content.strip()
    # Extract script part
    script_match = re.search(r'```(powershell|bash|zsh|sh)\s(.*?)\s```', full_response, re.DOTALL)
    if script_match:
        script = script_match.group(2).strip()
    else:
        script = full_response  # Use the entire response if the script is not detected

    return script
```

## 6. 실행 결과 설명 함수

실행된 스크립트의 결과를 자연어로 설명하는 함수이다. OpenAI 또는 Azure OpenAI를 사용하여 사용자의 질문에 대한 상세한 설명을 제공한다.

```python
def explain_execution_result(user_input, results, client_type, model):
    combined_results = "\n".join(results)
    prompt = f"""
    You need to provide a detailed explanation of the results of executing a script.
    The user should not know that the explanation is based on the script's results.
    ```
    User Query: {user_input}
    Script Execution Results: {combined_results}
    ```
    Refer to the script execution results to respond simply to the user's query.
    If the query is simply to run a specific program, respond that the program has been executed.
    Always respond in the user's language.
    """
    console = Console()
    with console.status("[bold green]Generating Explanation...[/bold green]"):
        response = None
        if client_type == "azure":
            response = client.chat.completions.create(
                model=model,
                messages=[
                    {"role": "system", "content": prompt.strip()}
                ],
                temperature=1,
                max_tokens=1024,
                top_p=1,
                frequency_penalty=0,
                presence_penalty=0
            )
        else:
            response = client.chat.completions.create(
                model=model,
                messages=[
                    {"role": "system", "content": prompt.strip()}
                ],
                temperature=1,
                max_tokens=1024,
                top_p=1,
                frequency_penalty=0,
                presence_penalty=0
            )

    explanation = response.choices[0].message.content.strip()
    return explanation
```

## 7. 테스트 및 인수 설정

Notebook에서 직접 인수를 설정하고 실행할 수 있도록 `main` 함수를 수정한다. 이는 `argparse` 대신 직접 인수를 제공하는 방법이다.

```python
# Notebook 환경에서 직접 인수를 설정한다.
config_path = 'config.yaml'  # 구성 파일 경로
auto_approve = True  # 확인 없이 실행 여부
selected_group = 'default'  # 서버 그룹 지정
selected_server = None  # 단일 서버 지정
query

_command = 'uname -a'  # 실행할 명령 또는 질문

# Load configuration from YAML file
with open(config_path, 'r', encoding='utf-8') as f:
    config = yaml.safe_load(f)

openai_config = config['openai']
server_groups = config['servers']

# Set up the OpenAI client
client, client_type, model = set_openai_client(openai_config)

# Handle single server selection or group selection
if selected_server:
    all_servers = [server for group in server_groups.values() for server in group]
    selected_server = next((server for server in all_servers if server['alias'] == selected_server), None)
    if not selected_server:
        console.print("[bold red]Invalid server selected.[/bold red]")
    servers = [selected_server]
else:
    servers = server_groups.get(selected_group, [])

user_input = query_command
linux_servers = []
windows_servers = []

for server in servers:
    host = server['host']
    port = server.get('port', 22 if server['os_type'] == 'Linux' else 5985)  # Default ports based on OS type
    username = server['username']
    password = server['password']
    os_type = server['os_type']

    if os_type == 'Linux':
        os_info = run_remote_command_linux(host, port, username, password, "uname -a")
        shell_version = run_remote_command_linux(host, port, username, password, "echo $SHELL --version")
        linux_servers.append((host, port, username, password, os_info, shell_version))
    elif os_type == 'Windows':
        os_info = run_remote_command_windows(host, port, username, password, "systeminfo")
        shell_version = run_remote_command_windows(host, port, username, password, "$PSVersionTable.PSVersion")
        windows_servers.append((host, port, username, password, os_info, shell_version))
    else:
        console.print(f"[bold red]Unsupported OS type: {os_type}[/bold red]")

results = []

# Generate and execute script for Linux servers
if linux_servers:
    os_info = linux_servers[0][4]
    shell_version = linux_servers[0][5]
    linux_script = generate_script(user_input, client_type, model, os_info, shell_version, "Linux")
    console.print(Panel(f"[bold cyan]Generated Script for Linux servers:[/bold cyan]\n\n[bold]{linux_script}[/bold]"))

# Generate and execute script for Windows servers
if windows_servers:
    os_info = windows_servers[0][4]
    shell_version = windows_servers[0][5]
    windows_script = generate_script(user_input, client_type, model, os_info, shell_version, "Windows")
    console.print(Panel(f"[bold cyan]Generated Script for Windows servers:[/bold cyan]\n\n[bold]{windows_script}[/bold]"))

# Confirm and execute the command on all servers in the selected group
if auto_approve or input("Do you want to execute this command on all servers in the selected group? (yes/y/no/n): ").strip().lower() in ['yes', 'y']:
    for host, port, username, password, os_info, shell_version in linux_servers + windows_servers:
        if os_info.startswith('Linux'):
            output = run_remote_command_linux(host, port, username, password, linux_script)
        else:
            output = run_remote_command_windows(host, port, username, password, windows_script)

        results.append(f"Output from {host}:\n{output}")

    if results:
        explanation = explain_execution_result(user_input, results, client_type, model)

        # Displaying combined results
        combined_results = "\n\n".join(results)
        console.print(Panel(f"[bold green]Combined Command Execution Output:[/bold green]\n\n{combined_results}"))

        # Displaying explanation
        md = Markdown(explanation)
        console.print(Panel(md, title="[bold yellow]Explanation[/bold yellow]"))
else:
    console.print(f"[bold red]Command execution canceled.[/bold red]")
```

위의 코드를 통해 원격 서버에서 명령을 실행하고, OpenAI 또는 Azure OpenAI를 사용하여 스크립트를 생성하고 실행 결과를 설명하는 전체 흐름을 관리할 수 있다. 각 부분은 독립적으로 테스트하고 조정할 수 있다.