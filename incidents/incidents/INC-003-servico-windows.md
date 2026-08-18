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

---

## 2. Resumo

Foi realizada uma investigação sobre o serviço Adobe Acrobat
Update Service (`AdobeARMservice`) com o objetivo de verificar
se havia alguma anormalidade relacionada ao seu estado,
configuração, processo, consumo de recursos ou eventos do
Windows.

A investigação utilizou diferentes ferramentas nativas do
Windows para validar o serviço e seu processo associado.

Ao final da análise, não foram identificadas evidências de
falha ou comportamento anormal.

---

## 3. Objetivo

O objetivo da investigação foi:

- identificar o serviço;
- verificar seu estado operacional;
- verificar o tipo de inicialização;
- identificar a conta utilizada;
- identificar o executável associado;
- verificar dependências;
- verificar eventos relacionados;
- identificar o processo associado;
- confirmar o PID;
- analisar consumo de CPU e memória;
- validar as informações utilizando PowerShell.

---

# 4. Ambiente

## Sistema operacional

- Windows

## Ferramentas utilizadas

- Services (`services.msc`)
- Event Viewer (`eventvwr.msc`)
- Task Manager
- Windows PowerShell
- `Get-Service`
- `Get-CimInstance`
- `Get-Process`

---

# 5. Identificação do serviço

O serviço selecionado para investigação foi:

```text
Nome do serviço:
AdobeARMservice

Nome para exibição:
Adobe Acrobat Update Service
