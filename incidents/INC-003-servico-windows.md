# INC-003 — Investigação de Serviço do Windows

## 1. Identificação do incidente

| Campo | Informação |
|---|---|
| ID | INC-003 |
| Tipo | Serviço do Windows / Infraestrutura |
| Serviço | Adobe Acrobat Update Service |
| Nome técnico | AdobeARMservice |
| Status | Concluído |
| Resultado | Serviço operando normalmente |
| Data da investigação | Agosto de 2026 |

## 2. Resumo

Foi realizada uma investigação sobre o serviço Adobe Acrobat Update Service (`AdobeARMservice`) para verificar estado, configuração, processo, consumo de recursos, dependências e eventos do Windows.

Ao final da análise, não foram identificadas evidências de falha ou comportamento anormal.

## 3. Objetivo

- Identificar o serviço e seu executável.
- Verificar estado e tipo de inicialização.
- Verificar conta utilizada.
- Verificar dependências.
- Investigar eventos relacionados.
- Identificar o processo associado e seu PID.
- Validar CPU e memória.
- Confirmar as informações por PowerShell/CIM.

## 4. Ambiente e ferramentas

- Windows
- Services (`services.msc`)
- Event Viewer (`eventvwr.msc`)
- Task Manager
- Windows PowerShell
- `Get-Service`
- `Get-CimInstance`
- `Get-Process`

## 5. Identificação e configuração do serviço

| Campo | Resultado |
|---|---|
| Nome do serviço | AdobeARMservice |
| Nome para exibição | Adobe Acrobat Update Service |
| Status | Em execução |
| Tipo de inicialização | Automático |
| Conta | Sistema Local |
| Executável | `armsvc.exe` |

### Caminho do executável

```text
"C:\Program Files (x86)\Common Files\Adobe\ARM\1.0\armsvc.exe"
```

## 6. Dependências

Resultado da aba de dependências:

- Este serviço depende de: **Nenhum**
- Serviços que dependem deste serviço: **Nenhum**

## 7. Event Viewer

Foi realizada pesquisa em:

```text
Windows Logs > System
```

Termo pesquisado:

```text
AdobeARMservice
```

Resultado:

**Nenhum evento relacionado encontrado.**

Não foram identificados eventos de erro, aviso ou falha relacionados ao serviço durante a investigação.

## 8. Identificação do processo

No Gerenciador de Tarefas foi identificado:

| Campo | Resultado |
|---|---:|
| Processo | `armsvc.exe` |
| PID | 4508 |
| CPU | 0% |
| Memória observada | 0,4 MB |
| Status | Em execução |

## 9. Validação via PowerShell

Comando:

```powershell
Get-Service -Name AdobeARMservice
```

Resultado:

```text
Status   Name               DisplayName
------   ----               -----------
Running  AdobeARMservice    Adobe Acrobat Update Service
```

O resultado confirmou que o serviço estava em execução.

## 10. Validação via CIM

Comando:

```powershell
Get-CimInstance Win32_Service -Filter "Name='AdobeARMservice'" | Select-Object Name, State, StartMode, StartName, PathName, ProcessId
```

Resultado:

```text
Name      : AdobeARMservice
State     : Running
StartMode : Auto
StartName : LocalSystem
PathName  : "C:\Program Files (x86)\Common Files\Adobe\ARM\1.0\armsvc.exe"
ProcessId : 4508
```

Os dados confirmaram o estado, inicialização automática, conta, caminho do executável e PID.

## 11. Validação do processo via PowerShell

Foi realizada consulta pelo PID:

```powershell
Get-Process -Id 4508 | Select-Object Id, ProcessName, CPU, WorkingSet64, StartTime
```

Resultado:

```text
Id           : 4508
ProcessName  : armsvc
CPU          :
WorkingSet64 : 8474624
StartTime    :
```

Também foi realizada consulta pelo nome:

```powershell
Get-Process -Name armsvc | Select-Object Id, ProcessName, CPU, WorkingSet64, StartTime
```

Resultado:

```text
Id           : 4508
ProcessName  : armsvc
CPU          :
WorkingSet64 : 8474624
StartTime    :
```

As duas consultas localizaram o mesmo processo e o mesmo PID.

O `WorkingSet64` informado foi de 8.474.624 bytes, aproximadamente 8,1 MB. A diferença em relação ao valor observado no Gerenciador de Tarefas não foi considerada evidência de falha, pois as ferramentas podem apresentar métricas de memória diferentes.

Os campos `CPU` e `StartTime` não retornaram valor nessa consulta. Isso, isoladamente, não foi considerado evidência de problema.

## 12. Análise das evidências

### Evidência 1 — Serviço operacional

O serviço estava em estado `Running` / Em execução.

### Evidência 2 — Inicialização automática

O serviço estava configurado como `Auto` / Automático.

### Evidência 3 — Conta de execução

O serviço estava executando como `LocalSystem` / Sistema Local.

### Evidência 4 — Processo associado

O processo `armsvc.exe` estava em execução.

### Evidência 5 — PID

O serviço e o processo apresentaram o mesmo PID: **4508**.

### Evidência 6 — Dependências

Nenhuma dependência de serviço foi identificada.

### Evidência 7 — Event Viewer

Nenhum evento relacionado ao `AdobeARMservice` foi encontrado no log System durante a investigação.

### Evidência 8 — Validação cruzada

As informações obtidas através do `services.msc` foram confirmadas utilizando PowerShell e CIM.

## 13. Diagnóstico

O Adobe Acrobat Update Service está funcionando normalmente no momento da investigação.

Não foram identificadas evidências de:

- serviço parado;
- falha de inicialização;
- dependência ausente;
- processo inexistente;
- PID inconsistente;
- erro relacionado no Event Viewer;
- consumo anormal de CPU;
- comportamento anormal evidente do processo.

## 14. Causa

Não foi identificada uma causa de falha porque nenhum problema operacional foi reproduzido durante a investigação.

O resultado correto do troubleshooting foi determinar, com base nas evidências coletadas, que o serviço estava operacional.

## 15. Ações realizadas

Foram realizadas somente ações de observação e coleta de informações.

Nenhuma configuração do serviço foi alterada.

O serviço não foi parado, reiniciado ou reconfigurado.

## 16. Resultado final

**Status: Concluído — serviço operando normalmente.**

A investigação confirmou que o `AdobeARMservice` está em execução e configurado para inicialização automática.

O processo `armsvc.exe` foi identificado e seu PID foi confirmado através de diferentes ferramentas.

A configuração apresentada pelo `services.msc` foi validada através do PowerShell e do CIM.

Não foram encontradas dependências ou eventos relevantes relacionados ao serviço durante a investigação.

Não foram identificadas evidências de falha ou comportamento anormal.

> **O serviço Adobe Acrobat Update Service encontra-se operacional e não apresentou anormalidades durante a investigação.**

## 17. Metodologia de troubleshooting

```text
Identificação do serviço
        ↓
Verificação do estado
        ↓
Análise da configuração
        ↓
Verificação das dependências
        ↓
Análise dos eventos
        ↓
Identificação do processo
        ↓
Verificação do PID
        ↓
Análise de recursos
        ↓
Validação via PowerShell
        ↓
Validação via CIM
        ↓
Análise das evidências
        ↓
Diagnóstico
        ↓
Documentação
```

## 18. Competências demonstradas

- Windows Services
- Service Management
- Troubleshooting
- Event Viewer
- Task Manager
- PowerShell
- WMI/CIM
- `Get-Service`
- `Get-CimInstance`
- `Get-Process`
- Análise de processos
- Identificação de PID
- Análise de configuração
- Coleta de evidências
- Validação cruzada
- Diagnóstico baseado em evidências
- Documentação técnica
- Raciocínio de infraestrutura

## 19. Relação com DevOps

Este laboratório desenvolve conceitos importantes para a transição para DevOps, incluindo:

- administração de serviços;
- identificação e análise de processos;
- uso de linha de comando;
- consultas com PowerShell;
- uso de WMI/CIM;
- relacionamento entre serviços, processos e PID;
- validação de configurações;
- coleta de evidências antes de alterações;
- documentação técnica.

A evolução natural desses conhecimentos inclui:

```text
Windows
   ↓
PowerShell
   ↓
Infraestrutura
   ↓
Linux
   ↓
Redes
   ↓
Git
   ↓
Automação
   ↓
CI/CD
   ↓
Docker
   ↓
Cloud
   ↓
DevOps
   ↓
DevSecOps
```
