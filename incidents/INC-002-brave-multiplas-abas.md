# INC-002 — Brave com múltiplas abas

## 1. Identificação do incidente

| Campo | Informação |
|---|---|
| ID | INC-002 |
| Tipo | Desempenho / Aplicativo |
| Aplicativo | Brave Browser |
| Sistema operacional | Windows |
| Status | Concluído |
| Severidade | Baixa / Média |
| Data da investigação | Agosto de 2026 |

## 2. Resumo

Foi investigado um comportamento relatado de lentidão ou travamento do navegador Brave quando várias abas são abertas simultaneamente.

Durante a investigação, o travamento não foi reproduzido. Entretanto, foram identificadas evidências de aumento significativo do consumo de memória do Brave conforme o número de abas aumentava, especialmente em determinadas páginas.

## 3. Ambiente

### Hardware

- RAM instalada: 8 GB
- Velocidade da memória: 4267 MT/s

### Ferramentas

- Windows Task Manager
- Windows Resource Monitor
- Brave Task Manager
- Gerenciador de extensões do Brave

## 4. Sintoma relatado

O Brave apresenta lentidão ou pode travar quando muitas abas são abertas simultaneamente.

O problema não ocorreu de forma consistente durante a investigação.

## 5. Hipótese inicial

O aumento do número de abas poderia provocar aumento significativo do consumo de memória, levando à pressão de memória no sistema e potencialmente causando lentidão ou travamento.

```text
Múltiplas abas
      ↓
Maior consumo de RAM
      ↓
Pressão de memória
      ↓
Possível paginação
      ↓
Lentidão / travamento
```

## 6. Linha de base — 4 abas

| Métrica | Resultado |
|---|---:|
| Abas | 4 |
| CPU do Brave | 5,8% |
| Memória do Brave | 687,8 MB |
| Disco | 0,1 |
| Rede | 0,1 |
| CPU total | 12% |
| Memória do sistema | 86% |
| Memória disponível | 1,1 GB |

## 7. Teste — 9 abas

| Métrica | Resultado |
|---|---:|
| Abas | 9 |
| CPU do Brave | 1,4% |
| Memória do Brave | 1.105 MB |
| Disco | 0,1 |
| Rede | 0,1 |
| CPU total | 7% |
| Memória do sistema | 87% |
| Memória disponível | 1,2 GB |

Não foi observada lentidão ou travamento.

## 8. Teste — 14 abas

| Métrica | Resultado |
|---|---:|
| Abas | 14 |
| CPU do Brave | 3% |
| Memória do Brave | 1.567,7 MB |
| Disco | 0,8 |
| Rede | 0,1 |
| CPU total | 6% |
| Memória do sistema | 94% |
| Memória disponível | 725 MB |
| Lentidão | Não |
| Brave “Não respondendo” | Não |

## 9. Análise de memória

No Resource Monitor:

- Hard Faults/sec atual: 0
- Maior pico observado: 47
- Memória disponível: aproximadamente 957 MB

Não foi observada paginação persistente durante o teste.

## 10. Brave Task Manager

| Página | Memória |
|---|---:|
| Topitop | 529 MB |
| ChatGPT | 432 MB |
| Falabella | 142 MB |
| Mercado Livre | 113 MB |
| Facebook | 102 MB |

As páginas Topitop e ChatGPT apresentaram consumo significativamente maior que as demais páginas observadas.

## 11. Teste da extensão

Extensões identificadas:

- Google Docs Offline — ativa
- Adobe Acrobat — desativada

O Google Docs Offline foi temporariamente desativado.

| Métrica | Extensão ativa | Extensão desativada |
|---|---:|---:|
| Memória do Brave | 1.567,7 MB | 1.552,2 MB |
| CPU do Brave | 3% | 15,7% |
| CPU total | 6% | 9% |
| Disco | 0,8 | 0,1 |

Não houve redução significativa do consumo de memória. Não foram encontradas evidências suficientes para considerar a extensão como causa do problema.

## 12. Teste de isolamento — Topitop

Antes de fechar a aba:

- Memória do Brave: 1.552,2 MB
- RAM em uso: 6,9 GB
- RAM disponível: 814 MB
- CPU do Brave: 15,7%
- CPU total: 9%
- Disco: 0,1

Depois de fechar a aba Topitop:

- Memória do Brave: 1.023,7 MB
- RAM em uso: 6,2 GB
- RAM disponível: 1,5 GB
- CPU do Brave: 1,1%
- CPU total: 4%
- Disco: 0,1

Redução observada no Brave: aproximadamente 528,5 MB.

O navegador permaneceu estável.

## 13. Teste de isolamento — ChatGPT

Após fechar a aba ChatGPT:

- Memória do Brave: 940,4 MB
- RAM em uso: 6,3 GB
- RAM disponível: 1,4 GB
- CPU do Brave: 0,6%
- CPU total: 4%
- Disco: 0,1

O navegador permaneceu estável.

## 14. Teste de comparação — 5 abas

| Métrica | Resultado |
|---|---:|
| Abas | 5 |
| Memória do Brave | 715,1 MB |
| RAM em uso | 6,3 GB |
| RAM disponível | 1,3 GB |
| CPU do Brave | 0,8% |
| CPU total | 6% |
| Disco | 0,1 |
| Lentidão | Não |

## 15. Evidências

### Evidência 1 — aumento do número de abas

```text
4 abas   →   687,8 MB
9 abas   → 1.105 MB
14 abas  → 1.567,7 MB
```

Existe relação clara entre o aumento do número de abas e o aumento do consumo de memória do Brave.

### Evidência 2 — páginas específicas

O Brave Task Manager identificou consumo elevado principalmente em:

- Topitop — 529 MB
- ChatGPT — 432 MB

### Evidência 3 — isolamento

O fechamento da aba Topitop reduziu o consumo do Brave em aproximadamente 528,5 MB.

### Evidência 4 — extensão

A desativação do Google Docs Offline não produziu redução significativa de memória.

### Evidência 5 — travamento não reproduzido

Durante os testes, o Brave não apresentou “Não respondendo”, o computador não apresentou lentidão perceptível e não foi observada paginação persistente.

## 16. Diagnóstico

O aumento do número de abas provoca aumento significativo do consumo de memória do Brave. Páginas específicas podem contribuir de forma significativa para esse consumo.

Em um equipamento com 8 GB de RAM, esse comportamento reduz a margem de memória disponível e contribui para a pressão de memória do sistema.

### Causa provável

A causa provável da degradação está relacionada à combinação de:

- quantidade de abas abertas;
- consumo de memória das páginas;
- quantidade limitada de RAM disponível no equipamento.

Entretanto, a causa direta do travamento relatado não pôde ser confirmada porque o problema não foi reproduzido durante os testes.

## 17. Limitações da investigação

O travamento relatado não ocorreu durante o período de testes. Portanto, não foi possível determinar com certeza o evento exato responsável pelo travamento.

Uma investigação futura poderia analisar:

- páginas específicas no momento do travamento;
- extensões;
- versões do Brave;
- aceleração de hardware;
- logs do navegador;
- eventos do Windows próximos ao horário do incidente;
- comportamento após reinicialização;
- comportamento com outras aplicações abertas.

## 18. Recomendações

- Reduzir o número de abas abertas simultaneamente.
- Fechar páginas com consumo elevado quando não estiverem sendo utilizadas.
- Utilizar recursos de suspensão de abas quando apropriado.
- Revisar extensões instaladas.
- Manter o Brave atualizado.
- Quando o problema ocorrer novamente, registrar abas, memória, CPU, disco, Hard Faults/sec e processos do Brave.
- Considerar aumento de RAM como melhoria de capacidade caso a utilização elevada seja frequente, sem tratá-lo como causa confirmada.

## 19. Resultado final

**Status: Concluído — causa provável identificada, mas travamento não reproduzido.**

A investigação demonstrou que o Brave apresenta aumento significativo de consumo de memória conforme o número de abas aumenta. Determinadas páginas podem consumir centenas de megabytes individualmente.

Os testes de isolamento confirmaram a contribuição significativa de páginas específicas para o consumo total do navegador.

Entretanto, não foi possível reproduzir o travamento relatado.

> Pressão de memória causada pela combinação entre múltiplas abas, páginas com alto consumo de recursos e apenas 8 GB de RAM é considerada uma causa provável de degradação do desempenho, mas não foi comprovada como causa direta do travamento.

## 20. Competências demonstradas

- Troubleshooting
- Coleta de evidências
- Análise de desempenho
- Windows Task Manager
- Windows Resource Monitor
- Brave Task Manager
- Análise de processos
- Análise de memória
- Testes controlados
- Teste de isolamento
- Formulação e validação de hipóteses
- Análise de causa provável
- Documentação de incidentes
- Raciocínio técnico
- Diferenciação entre correlação e causalidade

## 21. Metodologia utilizada

```text
Sintoma
   ↓
Coleta de dados
   ↓
Hipótese inicial
   ↓
Teste controlado
   ↓
Análise de memória
   ↓
Análise dos processos
   ↓
Teste de extensão
   ↓
Teste de isolamento
   ↓
Teste de comparação
   ↓
Análise das evidências
   ↓
Diagnóstico
   ↓
Mitigação
   ↓
Documentação
```
