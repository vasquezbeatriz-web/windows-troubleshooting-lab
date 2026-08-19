# INC-005 — Investigação de Problema de Rede

## 1. Identificação do incidente

| Campo | Informação |
|---|---|
| ID | INC-005 |
| Tipo | Conectividade / Rede |
| Interface | Wi-Fi |
| Status inicial | Conectado |
| Velocidade do link | Não informada |
| Tráfego observado — envio | 112 kbps |
| Tráfego observado — recebimento | 32 kbps |
| Status da investigação | Concluído |
| Resultado | Problema não reproduzido |

## 2. Resumo

Foi realizada uma investigação de conectividade de rede no computador, com foco em verificar se havia falha no adaptador Wi-Fi, configuração TCP/IP, comunicação com o gateway, acesso à Internet, resolução DNS, rota de rede ou perda de pacotes.

Foram utilizados o Gerenciador de Tarefas e ferramentas nativas do Windows PowerShell para realizar testes progressivos e controlados.

Durante a investigação, não foi reproduzida uma falha de conectividade. O computador permaneceu conectado ao Wi-Fi, comunicou-se normalmente com o gateway, alcançou a Internet sem perda de pacotes, resolveu nomes via DNS e completou o traceroute até 8.8.8.8.

## 3. Objetivo

- Identificar o tipo e estado da conexão.
- Verificar a configuração IP.
- Confirmar o funcionamento do TCP/IP local.
- Testar a comunicação com o gateway.
- Testar a conectividade com a Internet.
- Verificar a resolução DNS.
- Analisar a rota até um destino externo.
- Medir perda e latência em um teste de maior duração.
- Determinar se o problema poderia ser reproduzido.
- Registrar as evidências e a conclusão técnica.

## 4. Ambiente e ferramentas

### Sistema operacional

- Windows

### Interface de rede

- Intel(R) Wi-Fi 6 AX201 160MHz

### Ferramentas utilizadas

- Gerenciador de Tarefas
- Windows PowerShell
- `ipconfig /all`
- `ping`
- `tracert`
- `nslookup`

## 5. Identificação da conexão

No Gerenciador de Tarefas, foi observado:

| Campo | Resultado |
|---|---|
| Tipo de conexão | Wi-Fi |
| Status | Conectado |
| Velocidade do link | Não informada |
| Envio | 112 kbps |
| Recebimento | 32 kbps |

Os valores de envio e recebimento representam o tráfego observado naquele momento e não a velocidade máxima contratada ou negociada da conexão.

## 6. Configuração de rede — `ipconfig /all`

Foi executado:

```powershell
ipconfig /all
```

O adaptador Wi-Fi ativo foi identificado como:

```text
Intel(R) Wi-Fi 6 AX201 160MHz
```

Principais parâmetros observados:

| Parâmetro | Resultado |
|---|---|
| DHCP | Habilitado |
| Configuração automática | Habilitada |
| Máscara de sub-rede | 255.255.255.0 |
| Gateway padrão | 192.168.1.1 |
| Servidor DHCP | 192.168.1.1 |
| DNS IPv6 | fe80::1%6 |
| DNS IPv4 | 192.168.1.1 |
| IPv4 | Configurado |

Os endereços IP e MAC foram tratados como dados de infraestrutura e não são reproduzidos neste documento quando não necessários para a análise.

### Adaptadores virtuais

Também foram identificados dois adaptadores Microsoft Wi-Fi Direct Virtual Adapter, ambos com mídia desconectada.

Isso não foi considerado uma falha, pois o adaptador Wi-Fi físico estava conectado e funcionando.

## 7. Teste de loopback

Comando utilizado:

```powershell
ping 127.0.0.1
```

Resultado:

| Métrica | Resultado |
|---|---:|
| Enviados | 4 |
| Recebidos | 4 |
| Perdidos | 0% |
| Mínimo | <1 ms |
| Máximo | <1 ms |
| Média | <1 ms |

### Análise

O loopback respondeu corretamente, indicando funcionamento normal da pilha TCP/IP local durante o teste.

## 8. Teste do gateway

Comando utilizado:

```powershell
ping 192.168.1.1
```

Resultado:

| Métrica | Resultado |
|---|---:|
| Enviados | 4 |
| Recebidos | 4 |
| Perdidos | 0% |
| Mínimo | 1 ms |
| Máximo | 6 ms |
| Média | 3 ms |

### Análise

A comunicação entre o computador e o roteador ocorreu sem perda de pacotes e com baixa latência média.

## 9. Teste de conectividade com a Internet

Comando utilizado:

```powershell
ping 8.8.8.8
```

Resultado:

| Métrica | Resultado |
|---|---:|
| Enviados | 4 |
| Recebidos | 4 |
| Perdidos | 0% |
| Mínimo | 66 ms |
| Máximo | 75 ms |
| Média | 70 ms |

### Análise

O computador alcançou o endereço 8.8.8.8 sem perda de pacotes.

## 10. Teste de resolução DNS

Comando utilizado:

```powershell
ping google.com
```

O nome `google.com` foi resolvido para `64.233.186.102` durante o teste.

Resultado:

| Métrica | Resultado |
|---|---:|
| Enviados | 4 |
| Recebidos | 4 |
| Perdidos | 0% |
| Mínimo | 69 ms |
| Máximo | 90 ms |
| Média | 78 ms |

### Análise

O teste demonstrou que a resolução de nomes estava funcionando e que o destino resolvido respondeu normalmente.

## 11. Traceroute

Comando utilizado:

```powershell
tracert 8.8.8.8
```

A rota foi concluída em 11 saltos.

Principais pontos observados:

```text
1  192.168.1.1        ~1–3 ms
2  172.16.0.1         ~2–7 ms
3  10.201.69.161      ~2–8 ms
4  * * *              sem resposta
5  10.10.58.254       ~14 ms
6  195.22.222.28      ~14–22 ms
7  195.22.221.193     ~44–70 ms
8  142.250.47.216     ~63–66 ms
9  64.233.174.147     ~62–67 ms
10 142.251.78.73      ~61–62 ms
11 8.8.8.8            ~62–64 ms
```

### Análise

O quarto salto não respondeu às requisições do `tracert`, apresentando `* * *` e timeout.

Esse comportamento, isoladamente, não foi classificado como perda de conectividade porque os saltos posteriores continuaram respondendo e o destino final também respondeu normalmente.

O traceroute foi concluído com sucesso até 8.8.8.8.

## 12. Teste de DNS com o servidor configurado

Comando utilizado:

```powershell
nslookup google.com
```

O servidor DNS local identificado foi:

```text
Servidor: gpon.net
Address: fe80::1
```

A consulta retornou endereços IPv4 e IPv6 para `google.com`.

### Resultado

**DNS local: funcionando.**

## 13. Teste de DNS público

Comando utilizado:

```powershell
nslookup google.com 8.8.8.8
```

Servidor utilizado:

```text
Servidor: dns.google
Address: 8.8.8.8
```

A consulta retornou endereços IPv4 e IPv6 para `google.com`.

### Resultado

**DNS público: funcionando.**

A diferença entre os conjuntos de endereços retornados nas duas consultas não foi considerada falha, pois o Google utiliza múltiplos endereços e servidores podem retornar conjuntos diferentes.

## 14. Teste de estabilidade — 20 pacotes

Para aumentar a qualidade da evidência, foi executado:

```powershell
ping 8.8.8.8 -n 20
```

Resultado:

| Métrica | Resultado |
|---|---:|
| Pacotes enviados | 20 |
| Pacotes recebidos | 20 |
| Pacotes perdidos | 0 |
| Perda | **0%** |
| Latência mínima | **63 ms** |
| Latência máxima | **94 ms** |
| Latência média | **77 ms** |

### Análise

O teste de 20 pacotes não apresentou perda de pacotes.

A latência variou entre 63 ms e 94 ms, com média de 77 ms. Houve variação de latência, mas não foram observados timeout ou perda de pacotes que sustentassem um diagnóstico de falha de conectividade.

## 15. Consolidação das evidências

| Área investigada | Resultado |
|---|---|
| Wi-Fi | Funcionando |
| DHCP | Funcionando |
| Configuração IPv4 | Funcionando |
| TCP/IP local | Funcionando |
| Gateway | Funcionando |
| Internet | Funcionando |
| DNS local | Funcionando |
| DNS público | Funcionando |
| Traceroute | Rota concluída |
| Perda de pacotes — 4 pacotes | 0% |
| Perda de pacotes — 20 pacotes | **0%** |
| Falha reproduzida | Não |

## 16. Diagnóstico

Durante a investigação, não foi reproduzida uma falha de rede.

O adaptador Wi-Fi apresentou configuração válida, recebeu endereço via DHCP e utilizou o gateway 192.168.1.1. O computador respondeu ao loopback e comunicou-se com o gateway sem perda de pacotes.

A conectividade com a Internet foi confirmada por meio do endereço 8.8.8.8, com 0% de perda tanto no teste inicial quanto no teste de 20 pacotes.

A resolução DNS também foi confirmada tanto pelo servidor configurado localmente quanto pelo DNS público 8.8.8.8.

## 17. Causa

**Causa não identificada / problema não reproduzido.**

Não foram encontradas evidências suficientes para atribuir a lentidão ou eventual problema percebido a uma falha de rede durante o período de investigação.

O salto sem resposta observado no `tracert` não foi considerado causa do problema porque a rota continuou normalmente e o destino final respondeu.

## 18. Ações realizadas

Foram realizadas somente ações de diagnóstico e coleta de evidências:

- identificação do adaptador Wi-Fi;
- análise da configuração IP;
- teste de loopback;
- teste do gateway;
- teste de conectividade externa;
- teste de resolução DNS;
- análise de rota;
- teste de DNS público;
- teste de estabilidade com 20 pacotes.

Não foram realizadas alterações permanentes na configuração de rede.

## 19. Resultado final

**Status: Concluído — Problema não reproduzido.**

A investigação demonstrou que a conectividade estava operacional durante os testes.

O computador apresentou:

- conexão Wi-Fi ativa;
- configuração DHCP funcional;
- gateway acessível;
- 0% de perda no gateway;
- 0% de perda para 8.8.8.8;
- resolução DNS funcional;
- rota até o destino externo concluída;
- 0% de perda em um teste de 20 pacotes.

### Conclusão profissional

> **Não foi reproduzida uma falha de rede durante a investigação. Os testes realizados indicam funcionamento normal da conectividade local, do gateway, do DNS e do acesso à Internet no momento da coleta.**

## 20. Metodologia de troubleshooting

```text
Identificação da conexão
        ↓
Configuração IP
        ↓
Teste de loopback
        ↓
Teste do gateway
        ↓
Teste da Internet
        ↓
Teste DNS
        ↓
Traceroute
        ↓
DNS público
        ↓
Teste de estabilidade
        ↓
Comparação das evidências
        ↓
Diagnóstico
        ↓
Documentação
```

## 21. Competências demonstradas

- Troubleshooting de redes
- Windows PowerShell
- TCP/IP
- IPv4
- DHCP
- DNS
- Gateway
- Wi-Fi
- ICMP
- `ping`
- `tracert`
- `nslookup`
- Análise de latência
- Análise de perda de pacotes
- Testes progressivos
- Coleta de evidências
- Diagnóstico baseado em evidências
- Documentação técnica

## 22. Relação com DevOps

Este incidente desenvolve fundamentos importantes para infraestrutura e DevOps:

- diagnóstico de conectividade;
- utilização de PowerShell;
- análise de TCP/IP;
- DNS;
- análise de rotas;
- interpretação de métricas de rede;
- troubleshooting sistemático;
- coleta e documentação de evidências.

Esses conhecimentos servem de base para etapas posteriores envolvendo Linux, automação, cloud, containers, CI/CD, observabilidade e DevSecOps.
