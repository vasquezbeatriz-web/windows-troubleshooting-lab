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

---

## 2. Resumo

Foi investigado um comportamento relatado de lentidão ou
travamento do navegador Brave quando várias abas são abertas
simultaneamente.

O objetivo da investigação foi determinar se o aumento do número
de abas estava relacionado ao consumo elevado de memória e
identificar quais componentes ou páginas poderiam estar
contribuindo para o problema.

Durante a investigação, o travamento não foi reproduzido.

Entretanto, foram identificadas evidências de aumento
significativo do consumo de memória do Brave conforme o número
de abas aumentava, especialmente em determinadas páginas.

---

## 3. Ambiente

### Hardware

- RAM instalada: 8 GB
- Velocidade da memória: 4267 MT/s

### Sistema

- Windows
- Brave Browser
- Gerenciador de Tarefas
- Resource Monitor
- Brave Task Manager
- Gerenciador de extensões do Brave

---

## 4. Sintoma relatado

O comportamento relatado foi:

> O Brave apresenta lentidão ou pode travar quando muitas abas
> são abertas simultaneamente.

O problema não ocorreu de forma consistente durante a investigação.

---

## 5. Hipótese inicial

A hipótese inicial era que o aumento do número de abas poderia
estar provocando aumento significativo do consumo de memória,
levando à pressão de memória no sistema e potencialmente
causando lentidão ou travamento.

Hipótese:

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
