# INC-005 — Problema de Rede

## 1. Identificação do incidente

| Campo | Informação |
|---|---|
| ID | INC-005 |
| Tipo | Conectividade / Rede |
| Conexão | Wi-Fi |
| Status | Conectado |
| Adaptador | Intel(R) Wi-Fi 6 AX201 160MHz |
| Status da investigação | Concluído |
| Data da investigação | Agosto de 2026 |

## 2. Resumo

Foi realizada uma investigação de conectividade de rede com o objetivo de verificar se havia falha na comunicação local, acesso à Internet, resolução DNS, perda de pacotes ou problema de rota.

Os testes demonstraram conectividade local e externa funcional, sem perda de pacotes nos destinos testados. A latência para a Internet apresentou variação entre aproximadamente 63 ms e 94 ms no teste de 20 pacotes para 8.8.8.8.

Não foram encontradas evidências suficientes de falha de conectividade durante a investigação.

## 3. Sintoma / contexto

O incidente foi aberto para investigar possível problema de rede.

A investigação foi conduzida de forma progressiva, começando pela interface de rede e avançando para conectividade local, gateway, Internet, DNS e rastreamento de rota.

## 4. Identificação da conexão

### Conexão utilizada

- Tipo: Wi-Fi
- Status: conectado
- Adaptador: Intel(R) Wi-Fi 6 AX201 160MHz
- DHCP: habilitado
- Configuração automática: habilitada
- Gateway padrão: `192.168.1.1`
- Servidor DHCP: `192.168.1.1`
- DNS local: `192.168.1.1` / `fe80::1%6`

### Observação sobre velocidade

Durante a coleta foi registrada uma taxa de transferência momentânea de aproximadamente:

- Envio: 112 kbps
- Recebimento: 32 kbps

Esses valores representam **tráfego observado naquele momento**, e não a velocidade nominal do link Wi-Fi nem a velocidade contratada da Internet. Portanto, não foram utilizados como evidência de baixa velocidade da conexão.

## 5. Configuração IP

O comando utilizado foi:

```powershell
ipconfig /all
```

O adaptador Wi-Fi apresentou configuração válida:

```text
DHCP habilitado: Sim
IPv4: configurado
Máscara: 255.255.255.0
Gateway: 192.168.1.1
Servidor DHCP: 192.168.1.1
DNS: fe80::1%6 / 192.168.1.1
```

Também foram identificados dois adaptadores virtuais Wi-Fi Direct, ambos com mídia desconectada. Eles não foram considerados a interface responsável pela conexão utilizada no teste.

## 6. Teste 1 — Loopback

Comando:

```powershell
ping 127.0.0.1
```

Resultado:

```text
Enviados = 4
Recebidos = 4
Perdidos = 0 (0%)
Mínimo = 0 ms
Máximo = 0 ms
Média = 0 ms
```

### Interpretação

O teste de loopback foi bem-sucedido.

Isso demonstra que a pilha TCP/IP local respondeu corretamente. Entretanto, esse teste **não valida a conexão Wi-Fi nem a Internet**.

## 7. Teste 2 — Gateway local

Comando:

```powershell
ping 192.168.1.1
```

Resultado:

```text
Enviados = 4
Recebidos = 4
Perdidos = 0 (0%)
Mínimo = 1 ms
Máximo = 6 ms
Média = 3 ms
```

### Interpretação

A comunicação entre o computador e o gateway local funcionou sem perda de pacotes.

A latência média de 3 ms indica comunicação local estável durante o teste.

## 8. Teste 3 — Conectividade externa por IP

Comando:

```powershell
ping 8.8.8.8
```

Resultado:

```text
Enviados = 4
Recebidos = 4
Perdidos = 0 (0%)
Mínimo = 66 ms
Máximo = 75 ms
Média = 70 ms
```

### Interpretação

Foi possível alcançar um destino externo por endereço IP sem perda de pacotes.

Isso fornece evidência de conectividade entre o computador e a Internet durante o teste.

## 9. Teste 4 — Conectividade por nome DNS

Comando:

```powershell
ping google.com
```

Resultado:

```text
Destino resolvido: 64.233.186.102
Enviados = 4
Recebidos = 4
Perdidos = 0 (0%)
Mínimo = 69 ms
Máximo = 90 ms
Média = 78 ms
```

### Interpretação

O nome `google.com` foi resolvido para um endereço IP e o destino respondeu aos testes.

Isso fornece evidência de que resolução de nome e conectividade externa estavam funcionando naquele momento.

## 10. Teste 5 — Rastreamento de rota

Comando:

```powershell
tracert 8.8.8.8
```

A rota apresentou aproximadamente 11 saltos até o destino.

Exemplo dos primeiros saltos:

```text
1    1-3 ms    192.168.1.1
2    2-7 ms    172.16.0.1
3    2-8 ms    10.201.69.161
```

O destino final respondeu:

```text
11   61-64 ms  8.8.8.8
```

### Observação importante

Alguns saltos intermediários apresentaram `*` ou `Esgotado o tempo limite do pedido`.

Isso **não foi interpretado automaticamente como falha de rede**, porque os saltos seguintes continuaram respondendo e o destino final foi alcançado.

Alguns roteadores podem não responder a determinados pacotes ICMP/TTL expirado ou podem limitar essas respostas.

## 11. Teste 6 — DNS do sistema

Comando:

```powershell
nslookup google.com
```

Resultado:

```text
Servidor: gpon.net
Address: fe80::1
```

O servidor DNS retornou registros IPv4 e IPv6 para `google.com`.

### Interpretação

O resolvedor DNS configurado no sistema respondeu corretamente.

## 12. Teste 7 — DNS público

Comando:

```powershell
nslookup google.com 8.8.8.8
```

Resultado:

```text
Servidor: dns.google
Address: 8.8.8.8
```

O DNS público também retornou registros para `google.com`.

### Interpretação

A consulta direta ao DNS público `8.8.8.8` funcionou.

Isso reduz a probabilidade de uma falha de resolução exclusivamente no servidor DNS local durante o teste.

## 13. Teste 8 — Estabilidade com 20 pacotes

Comando:

```powershell
ping 8.8.8.8 -n 20
```

Resultado:

```text
Pacotes enviados: 20
Pacotes recebidos: 20
Perda: 0%
Mínimo: 63 ms
Máximo: 94 ms
Média: 77 ms
```

### Interpretação

O teste de 20 pacotes não apresentou perda.

A latência variou de 63 ms a 94 ms, com média de 77 ms.

A variação observada demonstra alguma oscilação de latência, mas **não foi suficiente, isoladamente, para caracterizar uma falha de conectividade**.

## 14. Consolidação das evidências

| Teste | Resultado | Interpretação |
|---|---|---|
| Loopback | 0% perda | Pilha TCP/IP local respondeu |
| Gateway | 0% perda / 3 ms | Comunicação local estável |
| 8.8.8.8 | 0% perda / 70 ms | Conectividade externa funcional |
| google.com | 0% perda / 78 ms | DNS + conectividade funcional |
| tracert | Destino alcançado | Rota funcional durante o teste |
| DNS local | Respondeu | Resolução DNS funcional |
| DNS 8.8.8.8 | Respondeu | DNS público funcional |
| 20 pings | 0% perda / 77 ms | Sem perda observada |

## 15. Análise técnica

A investigação seguiu uma abordagem por camadas:

```text
Computador
   ↓
Pilha TCP/IP
   ↓
Interface Wi-Fi
   ↓
Gateway local
   ↓
Rede externa
   ↓
DNS
   ↓
Destino Internet
```

Cada camada testada apresentou resposta funcional durante a coleta.

### O que foi confirmado

- A interface Wi-Fi estava conectada.
- O computador possuía configuração IP válida.
- O gateway respondeu.
- A Internet foi alcançada por IP.
- Um domínio foi resolvido e alcançado.
- O DNS local respondeu.
- O DNS público respondeu.
- O destino foi alcançado pelo `tracert`.
- Não houve perda de pacotes no teste prolongado de 20 pacotes.

### O que não foi confirmado

Não foi possível determinar, apenas com esses testes, a velocidade contratada da Internet ou a capacidade máxima do link Wi-Fi.

Também não foi possível afirmar que a conexão apresenta desempenho perfeito em todos os horários, pois os testes representam uma amostra do estado da rede no momento da investigação.

## 16. Diagnóstico

**Não foram encontradas evidências de falha de conectividade, perda de pacotes ou falha de DNS durante a investigação.**

A rede local apresentou latência baixa e sem perda, enquanto a conectividade externa apresentou latência média de aproximadamente 70–78 ms nos testes iniciais e 77 ms no teste de 20 pacotes.

A variação de latência observada não veio acompanhada de perda de pacotes e, isoladamente, não permite concluir a existência de um problema de rede.

## 17. Conclusão

**Status: Concluído — conectividade funcional durante os testes.**

Os testes realizados não reproduziram uma falha de rede.

A investigação demonstrou funcionamento da conectividade local, gateway, Internet e DNS, sem perda de pacotes nos destinos testados.

Os `*` observados em alguns saltos do `tracert` não foram considerados falha porque os saltos seguintes e o destino final responderam.

A taxa de transferência momentânea registrada no início da investigação não foi usada como diagnóstico de baixa velocidade, pois representa tráfego naquele instante e não a velocidade nominal do link.

> **Conclusão técnica: a conectividade estava funcional no momento da investigação. Não há evidência suficiente, com os testes realizados, para atribuir o incidente a uma falha de rede.**

## 18. Recomendações

Caso o problema volte a ocorrer, coletar novos dados **no momento exato da falha**, incluindo:

- `ping 192.168.1.1 -n 20`
- `ping 8.8.8.8 -n 20`
- `ping google.com -n 20`
- `nslookup google.com`
- `tracert 8.8.8.8`
- velocidade real medida por teste de velocidade confiável;
- intensidade/sinal do Wi-Fi;
- frequência utilizada (2,4 GHz / 5 GHz);
- horário do incidente.

A comparação entre testes normais e testes realizados durante a falha permitirá identificar se o problema está relacionado à rede local, Wi-Fi, DNS, rota externa ou ao próprio serviço acessado.

## 19. Metodologia utilizada

```text
Identificação da interface
        ↓
ipconfig /all
        ↓
Teste de loopback
        ↓
Teste do gateway
        ↓
Teste de IP externo
        ↓
Teste por nome DNS
        ↓
Teste de rota
        ↓
Teste DNS local
        ↓
Teste DNS público
        ↓
Teste de estabilidade com 20 pacotes
        ↓
Correlação das evidências
        ↓
Diagnóstico
        ↓
Documentação
```

## 20. Competências demonstradas

- Troubleshooting de rede
- TCP/IP
- IPv4 e IPv6
- Wi-Fi
- Gateway
- DHCP
- DNS
- ICMP
- `ping`
- `tracert`
- `nslookup`
- `ipconfig`
- Análise de latência
- Análise de perda de pacotes
- Diagnóstico por camadas
- Coleta de evidências
- Documentação técnica
- Diferenciação entre evidência e hipótese

## 21. Relação com DevOps / Infraestrutura

O incidente desenvolve fundamentos importantes para infraestrutura e DevOps:

```text
Troubleshooting de rede
        ↓
TCP/IP
        ↓
DNS
        ↓
Linux / Windows networking
        ↓
Automação com PowerShell
        ↓
Infraestrutura
        ↓
Cloud networking
        ↓
DevOps
        ↓
DevSecOps
```
