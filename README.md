
# Gerenciamento de Máquinas Físicas com Jenkins (Windows)

Este projeto tem como objetivo explicar como usar o Jenkins para gerenciar outras máquinas físicas com **Windows**. 
O Jenkins permite **reiniciar**, **desligar**, **iniciar** e **parar processos** em cada uma das máquinas controladas, além de **atualizar os repositórios Git**.

## Requisitos

### Jenkins
- Um servidor Jenkins em **nuvem** ou em um **container Docker** na máquina que será usada para gerenciar as demais.
- Jenkins deve estar configurado para rodar na mesma rede das máquinas físicas que serão geridas.

### Máquinas Filhas (Windows)
- As máquinas físicas (filhas) precisam ter o **Java** instalado.
- Devem ter as **permissões necessárias** liberadas para permitir o **acesso remoto via PowerShell** ou SSH.
- As máquinas precisam estar na **mesma rede** que a máquina gerenciadora ou servidor Jenkins.
- Habilitar a **execução de scripts remotos** no Windows com o PowerShell. Utilize o comando:

```powershell
Set-ExecutionPolicy RemoteSigned
```

### Acesso Remoto
Para permitir que o Jenkins execute comandos remotos nas máquinas Windows, será necessário configurar:
- **WinRM (Windows Remote Management)** ou **SSH** para permitir a execução de comandos remotos.
- Habilitar o **Windows PowerShell Remoting** com o seguinte comando no PowerShell (executado como administrador):

```powershell
Enable-PSRemoting -Force
```

## Funcionalidades

### 1. Reiniciar Máquinas
O Jenkins pode ser configurado para reiniciar máquinas Windows remotamente através de **comandos PowerShell**:

```powershell
Restart-Computer -ComputerName 'remote_machine' -Force
```

### 2. Desligar Máquinas
O Jenkins pode desligar as máquinas Windows controladas com o seguinte comando PowerShell:

```powershell
Stop-Computer -ComputerName 'remote_machine' -Force
```

### 3. Ligar Processos de Automação em Python
Para iniciar um processo Python remotamente no Windows, configure o Jenkins para rodar o seguinte comando via PowerShell:

```powershell
Invoke-Command -ComputerName 'remote_machine' -ScriptBlock { python C:\caminho\para\script.py }
```

### 4. Desligar Processos de Automação em Python
O processo Python pode ser parado remotamente usando o **Taskkill** no PowerShell:

```powershell
Invoke-Command -ComputerName 'remote_machine' -ScriptBlock { Stop-Process -Name 'python' -Force }
```

### 5. Atualizar Repositório Git
Caso deseje manter o código Python nas máquinas físicas Windows sempre atualizado, o Jenkins pode ser configurado para rodar o seguinte comando para atualizar o repositório:

```powershell
Invoke-Command -ComputerName 'remote_machine' -ScriptBlock { cd C:\caminho\para\repositorio; git pull origin main }
```

## Configuração Passo a Passo

### 1. Instalação do Jenkins
Instale o Jenkins em um servidor ou numa máquina de controle. Caso utilize Docker, o comando abaixo pode ser usado para levantar um container Jenkins:

```bash
docker run -p 8080:8080 -p 50000:50000 -v jenkins_home:/var/jenkins_home jenkins/jenkins:lts
```

### 2. Configuração das Máquinas Filhas (Windows)
Nas máquinas físicas que serão gerenciadas:
- Instale o **Java**.
- Habilite o **Windows PowerShell Remoting** ou SSH.
- Verifique se os serviços de firewall estão configurados para permitir acesso remoto via **WinRM** ou **SSH** na porta correta.

### 3. Configuração de Jobs no Jenkins
- Crie um novo **job freestyle** no Jenkins.
- Na seção **"Build"**, adicione **comandos PowerShell** conforme o processo que deseja executar (reiniciar, desligar, iniciar processos, etc.).
- Verifique se o Jenkins está configurado corretamente para acessar cada máquina remota via PowerShell ou SSH.

### 4. Integração com Git
Se os scripts de automação estão num repositório Git, configure o Jenkins para atualizar o código automaticamente ao rodar as tarefas, conforme o exemplo dado na seção de atualização de repositórios.

### 5. Agendamento de Tarefas
Use o **Agendador do Jenkins (cron)** para automatizar as tarefas, como reiniciar ou atualizar as máquinas em horários específicos, conforme necessário.
