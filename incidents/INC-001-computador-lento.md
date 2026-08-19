# INC-001 — Computador lento

## 1. Sintoma

O usuário relata que o computador está apresentando lentidão.

## 2. Impacto

Um usuário afetado.

O impacto relatado é redução de desempenho durante a utilização do computador.

## 3. Investigação

Foram utilizadas ferramentas nativas do Windows para avaliar CPU, memória, disco, processos, eventos do sistema e atividade de memória virtual.

### 3.1 Task Manager — primeira análise

Resultados iniciais:

- CPU: 5%
- Memória: 75%
- Disco: 2%

Principais processos por utilização de CPU:

- Brave Browser: 0,8%
- System: 0,7%
- Antimalware Service Executable: 0,3%

Não foi identificado processo com utilização anormal de CPU.

### 3.2 Análise de memória

Características observadas:

- RAM instalada: 8 GB
- Velocidade da RAM: 4267 MT/s
- Slots utilizados: 8 de 8
- RAM em uso inicialmente: 6,1 GB
- RAM disponível inicialmente: 1,6 GB
- Memória em cache: 985 MB
- Hardware reservado: 317 MB
- Pool paginado: 616 MB
- Pool não paginado: 647 MB
- Committed: 9,1 / 11,9 GB

Principais consumidores de memória observados:

- Brave Browser: 488,9 MB
- Antimalware Service Executable: 167,8 MB
- Pesquisar: 155,6 MB
- Notepad: 102,7 MB
- Task Manager: 85,0 MB

Não foi identificado um único processo com consumo de memória suficiente para explicar isoladamente o problema.

### 3.3 Event Viewer

Foi analisado o log System do Windows.

Foi identificado o seguinte evento:

- Fonte: Microsoft-Windows-UserModePowerService
- Event ID: 12
- Nível: Information
- Processo relacionado: WUDFHost.exe

O evento registrava uma alteração/reaplicação de esquema de política de energia.

O evento estava classificado como informativo e não apresentou evidência de relação causal com o sintoma de lentidão.

Portanto, o evento não foi considerado a causa do incidente.

### 3.4 Resource Monitor

Foi utilizado o Resource Monitor para analisar a memória e a atividade de paginação.

Durante os testes foram observados picos de aproximadamente:

- Hard Faults/sec: 125

Os picos não foram acompanhados de lentidão perceptível, travamentos ou aplicativos sem resposta.

### 3.5 Análise de desempenho anterior

Foi considerada também uma análise de desempenho realizada anteriormente no sistema.

O diagnóstico do Windows indicou:

- Sintoma: o sistema estava apresentando paginação excessiva.
- Causa: baixa disponibilidade de memória.
- Detalhes: a memória física total do sistema não conseguia lidar com a carga apresentada.
- Resolução recomendada: atualizar a memória física ou reduzir a carga do sistema.

Essa evidência histórica é relevante porque demonstra que o sistema apresentou pressão de memória em outro momento.

### 3.6 Teste controlado — Windows recém-iniciado

Após reiniciar o computador e aguardar o carregamento do Windows, foram coletados os seguintes dados:

- RAM instalada: 8 GB
- RAM em uso: 5,5 GB
- RAM disponível: 2,2 GB
- Committed: 5,7 / 11,7 GB
- Velocidade da RAM: 4267 MT/s

O computador não apresentou lentidão perceptível.

### 3.7 Teste controlado — uso normal

Após iniciar os aplicativos utilizados normalmente, foram coletados novos dados:

- RAM em uso: 6,3 GB
- RAM disponível: 1,4 GB
- Committed: 8,0 / 11,7 GB
- Utilização do disco: aproximadamente 8%
- Velocidade de leitura: aproximadamente 83 MB/s
- Velocidade de gravação: aproximadamente 305 MB/s
- Hard Faults/sec: pico de aproximadamente 125

Durante o teste:

- O computador não apresentou lentidão perceptível.
- Nenhum aplicativo travou.
- Não foram observados aplicativos sem resposta.
- CPU permaneceu em utilização baixa.
- O disco não apresentou saturação.

## 4. Evidências

As evidências coletadas indicam:

- CPU apresentou baixa utilização durante os testes.
- Disco apresentou baixa utilização e não demonstrou saturação.
- O sistema possui 8 GB de RAM.
- A utilização de memória ficou entre aproximadamente 75% e 76% durante parte da investigação.
- A memória disponível chegou a aproximadamente 1,4 GB durante o uso normal.
- O Windows apresentou anteriormente um diagnóstico de paginação excessiva.
- Foram observados picos de aproximadamente 125 Hard Faults/sec durante o teste.
- Não foi identificado um processo individual com consumo anormal de recursos.
- O evento encontrado no Event Viewer era informativo e não apresentou relação evidente com o sintoma.
- O problema de lentidão não foi reproduzido durante os testes realizados.

## 5. Hipótese

A capacidade de 8 GB de RAM pode representar uma limitação para determinadas cargas de trabalho.

A combinação de:

- baixa quantidade de RAM física;
- aproximadamente 1,4 GB de memória disponível durante o uso normal;
- utilização elevada de memória;
- evidência histórica de paginação excessiva;
- ocorrência de Hard Faults;

indica que **pressão de memória pode contribuir para episódios específicos de lentidão**.

Entretanto, os testes atuais não foram suficientes para confirmar que a pressão de memória seja a causa direta do sintoma relatado.

### Observação técnica sobre Hard Faults/sec

Hard Faults/sec representam acessos a páginas de memória que não estavam disponíveis no conjunto de trabalho do processo e precisaram ser obtidas de outra fonte, como o arquivo de paginação ou um arquivo mapeado. A ocorrência de Hard Faults, por si só, **não significa automaticamente que exista um problema grave de paginação ou que a memória RAM seja a causa da lentidão**.

Neste incidente, os picos observados foram tratados como evidência complementar de atividade de memória. A hipótese de pressão de memória foi sustentada principalmente pela combinação entre 8 GB de RAM, baixa memória disponível em determinados momentos e a evidência histórica do diagnóstico do Windows, e não pelos Hard Faults isoladamente.

## 6. Testes realizados

Foram realizados os seguintes testes:

- Análise de utilização da CPU.
- Análise de utilização da memória.
- Análise de utilização do disco.
- Identificação dos principais processos consumidores de CPU.
- Identificação dos principais processos consumidores de memória.
- Análise do Resource Monitor.
- Monitoramento de Hard Faults/sec.
- Análise do Windows Event Viewer.
- Análise de eventos do System.
- Reinicialização do sistema.
- Medição da utilização de memória após inicialização.
- Medição da utilização de memória durante uso normal.
- Monitoramento da atividade do disco durante uso normal.
- Observação do comportamento do sistema durante os testes.

## 7. Diagnóstico

Foi identificada pressão de memória em determinados momentos e existe evidência histórica de paginação excessiva.

A capacidade de 8 GB de RAM representa uma possível limitação para cargas de trabalho mais elevadas.

Durante o teste atual, entretanto, o sintoma de lentidão não foi reproduzido.

CPU e disco permaneceram com baixa utilização e não foram observados travamentos ou aplicativos sem resposta.

### Conclusão

A pressão de memória é uma **hipótese provável para episódios específicos de lentidão**, porém não foi possível confirmar uma relação causal direta durante a investigação atual.

Os Hard Faults/sec observados reforçam a existência de atividade de memória, mas não são, isoladamente, evidência suficiente para atribuir a lentidão à paginação.

O incidente deve ser considerado como um problema potencialmente intermitente, que necessita de nova coleta de evidências caso a lentidão volte a ocorrer.

## 8. Solução

Não foi aplicada nenhuma alteração no sistema durante a investigação.

Não foram alterados:

- configurações de memória virtual;
- serviços do Windows;
- Registro do Windows;
- processos do sistema;
- configurações de segurança.

A ausência de uma intervenção foi intencional, pois não havia evidência suficiente para justificar uma alteração no sistema.

## 9. Validação

Após os testes realizados, o computador permaneceu operacional sem apresentar:

- lentidão perceptível;
- travamentos;
- aplicativos sem resposta;
- saturação de CPU;
- saturação do disco.

Durante o uso normal, foram observados:

- CPU em utilização baixa;
- aproximadamente 6,3 GB de RAM em uso;
- aproximadamente 1,4 GB de RAM disponível;
- aproximadamente 8% de utilização do disco;
- picos de aproximadamente 125 Hard Faults/sec.

O problema não foi reproduzido de forma consistente.

## 10. Prevenção e recomendações

Recomenda-se:

1. Monitorar a utilização de memória quando o problema ocorrer novamente.
2. Registrar quais aplicativos estavam sendo utilizados no momento da lentidão.
3. Registrar horário, duração e frequência do problema.
4. Verificar se o problema ocorre após a abertura de determinados aplicativos ou após aumento significativo da carga do sistema.
5. Repetir a análise do Resource Monitor durante uma ocorrência real de lentidão.
6. Considerar redução da quantidade de aplicativos executados simultaneamente quando houver alta utilização de memória.
7. Avaliar a possibilidade de expansão da memória RAM caso a pressão de memória seja recorrente e compatível com as especificações do equipamento.
8. Não realizar alterações de configuração sem evidências suficientes para justificar a intervenção.

## 11. Ferramentas utilizadas

- Windows Task Manager
- Windows Resource Monitor
- Windows Event Viewer
- Windows Performance Diagnostics

## 12. Competências demonstradas

- Troubleshooting
- Coleta de evidências
- Análise de desempenho
- Análise de processos
- Análise de memória
- Análise de eventos do Windows
- Análise de memória virtual
- Formulação de hipóteses
- Validação de diagnóstico
- Documentação de incidentes
- Raciocínio técnico
