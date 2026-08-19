# INC-004 — Investigação de Programa na Inicialização do Windows

## 1. Identificação do incidente

| Campo | Informação |
|---|---|
| ID | INC-004 |
| Tipo | Inicialização de aplicativos / Desempenho |
| Programa | WhatsApp Desktop |
| Editor | WhatsApp Inc. |
| Status | Concluído |
| Resultado | Consumo de memória identificado, sem impacto perceptível no desempenho |
| Data da investigação | Agosto de 2026 |

## 2. Resumo

Foi realizada uma investigação sobre o WhatsApp Desktop, identificado como aplicativo habilitado na inicialização do Windows.

O objetivo foi determinar como o aplicativo é iniciado, qual processo está associado, quanto recurso consome e se sua execução automática causa impacto perceptível no desempenho do computador.

Foram utilizados recursos nativos do Windows e PowerShell para investigar diferentes mecanismos de inicialização e realizar testes controlados de isolamento e reprodução.

Ao final da investigação, foi identificado consumo relevante de memória RAM pelo WhatsApp, porém não foram encontradas evidências de que sua execução automática seja a causa da lentidão percebida no sistema.

## 3. Objetivo

- Identificar o programa habilitado na inicialização.
- Verificar seu editor e status.
- Investigar mecanismos de inicialização.
- Verificar entradas no Registro.
- Verificar tarefas agendadas.
- Identificar o pacote AppX/MSIX.
- Localizar o processo em execução.
- Medir CPU, memória, disco e rede.
- Realizar teste de isolamento.
- Realizar teste de reprodução.
- Comparar os resultados.
- Concluir com base em evidências.

## 4. Ambiente e ferramentas

### Sistema operacional

- Windows

### Hardware

- RAM instalada: 8 GB

### Ferramentas utilizadas

- Gerenciador de Tarefas
- PowerShell
- Registro do Windows
- `Get-CimInstance`
- `Get-ItemProperty`
- `Get-ScheduledTask`
- `Get-ChildItem`
- `Get-AppxPackage`

## 5. Identificação do programa

No Gerenciador de Tarefas, em **Aplicativos de inicialização**, foi identificado:

| Campo | Resultado |
|---|---|
| Nome | WhatsApp |
| Editor | WhatsApp Inc. |
| Status | Habilitado |
| Impacto na inicialização | Não medido |

Nenhuma alteração foi realizada inicialmente.

## 6. Investigação do mecanismo de inicialização

Foi tentado utilizar a opção **Abrir local do arquivo** no Gerenciador de Tarefas.

Resultado:

> A opção não estava disponível.

A investigação prosseguiu utilizando PowerShell.

## 7. Verificação com Win32_StartupCommand

Comando utilizado:

```powershell
Get-CimInstance Win32_StartupCommand |
Where-Object {$_.Name -like "*WhatsApp*"} |
Select-Object Name, Command, Location, User
```

Resultado:

```text
Nenhum retorno.
```

Não foi encontrada uma entrada tradicional de inicialização utilizando `Win32_StartupCommand`.

## 8. Verificação das chaves Run do Registro

Foram verificadas as seguintes chaves:

```text
HKCU\Software\Microsoft\Windows\CurrentVersion\Run
HKLM\Software\Microsoft\Windows\CurrentVersion\Run
```

Resultado:

```text
Nenhuma entrada relacionada ao WhatsApp.
```

Portanto, o WhatsApp não foi identificado nas chaves tradicionais `Run`.

## 9. Verificação de tarefas agendadas

Foi executado:

```powershell
Get-ScheduledTask |
Where-Object {
    $_.TaskName -like "*WhatsApp*" -or
    $_.TaskPath -like "*WhatsApp*"
} |
Select-Object TaskName, TaskPath, State
```

Resultado:

```text
Nenhuma tarefa relacionada ao WhatsApp encontrada.
```

Não foi identificada tarefa agendada responsável pela inicialização.

## 10. Verificação StartupApproved

Foram consultadas as chaves:

```text
HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\StartupApproved\Run
HKLM\Software\Microsoft\Windows\CurrentVersion\Explorer\StartupApproved\Run
```

Foram encontradas entradas de outros aplicativos, incluindo Microsoft Edge, OneDrive, Opera, Brave e Adobe.

Não foi encontrada entrada relacionada ao WhatsApp.

## 11. Pesquisa ampla no Registro

Foi realizada uma pesquisa recursiva no Registro do Windows.

Foram encontradas referências ao pacote:

```text
5319275A.WhatsAppDesktop_cv1g1gvanyjgm
```

As referências estavam relacionadas a IndexedDB, permissões de aplicativos, acesso a microfone, localização, notificações, execução em segundo plano e dados do aplicativo.

Essas entradas confirmaram a presença do WhatsApp Desktop como aplicativo instalado, porém não identificaram uma entrada tradicional de inicialização.

## 12. Identificação do pacote AppX/MSIX

Foi executado:

```powershell
Get-AppxPackage *WhatsApp* |
Select-Object Name, PackageFullName, InstallLocation
```

Resultado:

| Campo | Resultado |
|---|---|
| Nome | 5319275A.WhatsAppDesktop |
| Versão | 2.2631.102.0 |
| Arquitetura | x64 |
| Tipo | AppX/MSIX |
| Local de instalação | `C:\Program Files\WindowsApps\...` |

A investigação demonstrou que o WhatsApp Desktop está instalado como aplicativo empacotado do Windows.

Isso ajuda a explicar por que não foi encontrada uma entrada tradicional nas chaves `Run` e nos mecanismos clássicos de inicialização investigados.

**Importante:** a identificação do pacote AppX/MSIX explica por que a investigação não encontrou uma entrada tradicional, mas **não comprova qual mecanismo específico faz o aplicativo iniciar automaticamente**. O mecanismo exato de inicialização não foi identificado nesta investigação.

## 13. Identificação do processo

No Gerenciador de Tarefas foi observado:

| Campo | Resultado |
|---|---|
| Processo | WhatsApp |
| Status | Em execução |
| CPU | 0,6% |
| Memória | 488,6 MB |
| Disco | 7,8 MB/s |
| Rede | 0 |
| Modo | Eficiência |

O processo estava ativo no momento da observação.

## 14. Linha de base — observação de 1 minuto

Após aproximadamente um minuto, sem interação com o WhatsApp, foram observados:

| Métrica | Resultado |
|---|---:|
| CPU do WhatsApp | 0,1% |
| Memória do WhatsApp | 567,0 MB |
| Disco | 0,1 MB/s |
| Rede | 0 |
| CPU total | 9% |
| Memória total em uso | 6,9 GB |
| RAM disponível | 915 MB |

### Análise

Durante a observação, a CPU caiu para 0,1%, a atividade de disco caiu para 0,1 MB/s e a rede permaneceu em 0. O consumo de memória aumentou para 567 MB.

Não foi observada atividade persistente de disco.

## 15. Teste de isolamento

O processo do WhatsApp foi encerrado temporariamente. A configuração de inicialização não foi alterada.

Após aproximadamente 1–2 minutos:

| Métrica | Resultado |
|---|---:|
| CPU total | 13% |
| Memória total em uso | 81% |
| RAM disponível | 1,3 GB |
| Disco | 4 MB/s |
| Desempenho percebido | Igual |

### Comparação

Antes de encerrar:

```text
RAM disponível: 915 MB
```

Depois de encerrar:

```text
RAM disponível: 1,3 GB
```

A memória disponível aumentou aproximadamente 400 MB.

Entretanto, não foi percebida melhora no desempenho do computador.

## 16. Teste de reprodução

O WhatsApp foi aberto novamente.

Após aproximadamente um minuto:

| Métrica | Resultado |
|---|---:|
| CPU do WhatsApp | 0,3% |
| Memória do WhatsApp | 678,5 MB |
| Disco | 3 MB/s |
| Rede | 0 |
| CPU total | 6% |
| Memória total em uso | 92% |
| RAM disponível | 652 MB |
| Desempenho percebido | Igual |

O aplicativo voltou a consumir quantidade significativa de memória, mas não foi observada lentidão perceptível.

## 17. Comparação dos testes

| Métrica | Ativo (1 min) | Encerrado | Reaberto |
|---|---:|---:|---:|
| CPU do WhatsApp | 0,1% | — | 0,3% |
| Memória do WhatsApp | 567 MB | — | 678,5 MB |
| Disco | 0,1 MB/s | 4 MB/s | 3 MB/s |
| Rede | 0 | 0 | 0 |
| CPU total | 9% | 13% | 6% |
| RAM disponível | 915 MB | 1,3 GB | 652 MB |
| Lentidão percebida | — | Igual | Igual |

## 18. Análise das evidências

### Evidência 1 — Inicialização

O Gerenciador de Tarefas mostrou o WhatsApp como habilitado na inicialização.

### Evidência 2 — Mecanismos tradicionais

Não foram encontradas entradas relacionadas ao WhatsApp em:

- `Win32_StartupCommand`;
- `HKCU...\Run`;
- `HKLM...\Run`;
- Scheduled Tasks;
- `StartupApproved\Run`.

### Evidência 3 — Aplicativo empacotado

O PowerShell confirmou que o WhatsApp Desktop está instalado como aplicativo AppX/MSIX.

### Evidência 4 — Mecanismo específico não identificado

Embora o Gerenciador de Tarefas indique o WhatsApp como habilitado na inicialização, a investigação não encontrou a entrada ou configuração específica responsável por esse comportamento. Portanto, o mecanismo exato permanece não identificado.

### Evidência 5 — Consumo de CPU

O consumo de CPU permaneceu baixo durante os testes, entre 0,1% e 0,6% nas observações realizadas.

### Evidência 6 — Consumo de memória

O consumo de memória variou entre aproximadamente 488,6 MB, 567 MB e 678,5 MB, demonstrando impacto mensurável na RAM disponível.

### Evidência 7 — Teste de isolamento

Ao encerrar o WhatsApp, a memória disponível aumentou de aproximadamente 915 MB para 1,3 GB.

### Evidência 8 — Desempenho percebido

Mesmo após encerrar e reabrir o WhatsApp, o desempenho percebido do computador permaneceu igual.

## 19. Diagnóstico

O WhatsApp Desktop apresenta consumo relevante de memória RAM em um computador com 8 GB de memória.

Durante os testes, o aplicativo chegou a consumir aproximadamente 678,5 MB.

Esse consumo contribui para a pressão de memória do sistema, especialmente quando a utilização total de RAM se aproxima de 90%.

Entretanto, os testes de isolamento e reprodução não demonstraram melhora perceptível no desempenho do computador quando o WhatsApp foi encerrado.

## 20. Causa

Não foi identificada evidência suficiente para concluir que o WhatsApp seja a causa da lentidão do computador.

Foi identificado apenas que o aplicativo possui impacto mensurável no consumo de memória RAM.

O mecanismo específico responsável pela inicialização automática também não foi identificado durante esta investigação.

## 21. Ações realizadas

Durante a investigação foram realizadas somente ações de observação e testes controlados.

Foram executadas consultas por PowerShell e encerramento temporário do processo.

Não foram realizadas alterações permanentes em:

- inicialização;
- Registro;
- tarefas agendadas;
- configuração do aplicativo.

## 22. Resultado final

**Status: Concluído — consumo de memória identificado; mecanismo específico de inicialização e relação causal com lentidão não confirmados.**

A investigação confirmou que o WhatsApp Desktop está habilitado na inicialização do Windows e instalado como aplicativo empacotado AppX/MSIX.

Os mecanismos tradicionais de inicialização investigados não apresentaram entradas relacionadas ao aplicativo.

O WhatsApp apresentou consumo relevante de memória RAM, chegando a aproximadamente 678,5 MB durante o teste.

O encerramento temporário do processo aumentou a memória disponível, porém não resultou em melhora perceptível no desempenho do computador.

Portanto:

> **O WhatsApp contribui para o consumo de memória do sistema, mas os testes realizados não demonstraram que sua inicialização automática seja a causa da lentidão percebida no computador. O mecanismo específico responsável pela inicialização automática não foi identificado nesta investigação.**

## 23. Metodologia de troubleshooting

```text
Identificação do aplicativo
        ↓
Verificação no Gerenciador de Tarefas
        ↓
Investigação via PowerShell
        ↓
Win32_StartupCommand
        ↓
Registro HKCU/HKLM
        ↓
Scheduled Tasks
        ↓
StartupApproved
        ↓
Pesquisa ampla no Registro
        ↓
Identificação do pacote AppX/MSIX
        ↓
Identificação do processo
        ↓
Medição de CPU/RAM/Disco/Rede
        ↓
Teste de isolamento
        ↓
Teste de reprodução
        ↓
Comparação dos resultados
        ↓
Diagnóstico
        ↓
Documentação
```

## 24. Competências demonstradas

- Troubleshooting de inicialização do Windows
- Gerenciador de Tarefas
- PowerShell
- Registro do Windows
- Scheduled Tasks
- Aplicativos AppX/MSIX
- Análise de processos
- Análise de CPU
- Análise de memória
- Análise de disco
- Testes controlados
- Teste de isolamento
- Teste de reprodução
- Coleta de evidências
- Diagnóstico baseado em evidências
- Documentação técnica

## 25. Relação com DevOps

Este incidente desenvolve conhecimentos úteis para a transição para DevOps, incluindo:

- investigação de processos;
- utilização de PowerShell;
- análise de mecanismos de inicialização;
- leitura do Registro;
- identificação de pacotes instalados;
- coleta de métricas de recursos;
- comparação entre estados do sistema;
- documentação de procedimentos técnicos.

A progressão de aprendizado segue:

```text
Windows Troubleshooting
        ↓
PowerShell
        ↓
Infraestrutura
        ↓
Linux
        ↓
Automação
        ↓
Git
        ↓
CI/CD
        ↓
Containers
        ↓
Cloud
        ↓
DevOps
        ↓
DevSecOps
```
