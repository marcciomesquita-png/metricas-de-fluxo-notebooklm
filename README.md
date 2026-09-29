# Métricas de fluxo: decidir por percentil, não por média

> Caderno temático construído no **NotebookLM** como Desafio de Projeto da
> [DIO](https://dio.me) — Bootcamp Santander *Automação com n8n*.

---

## 1. Contexto e Objetivos

### O assunto

Métricas de fluxo para times ágeis — **WIP, Vazão (Throughput), Idade do Item de Trabalho
(Work Item Age) e Tempo de Ciclo (Cycle Time)** — e a pergunta que separa quem coleta número de
quem decide com ele: **usar média ou percentil?**

### Por que este assunto

> ⬜ **A COMPLETAR com as suas palavras** — 3 a 5 linhas. O material está na seção 3.0: você já
> tomou uma decisão errada lendo o próprio dado. O objetivo aqui não é aprender o que são as
> métricas, é estabelecer **quando cada leitura engana**.

### Objetivos de estudo

- [x] Fixar a definição **canônica** das quatro métricas obrigatórias, na fonte, sem intermediário
- [x] Entender por que a média de Lead Time induz a erro e o que o percentil resolve
- [x] Testar a **confiabilidade do NotebookLM** quando as fontes discordam entre si
- [ ] Saber ler um **scatterplot de Cycle Time** e extrair p50 / p85 dele
- [x] Produzir um conjunto de **prompts reutilizáveis** para revisar o tema sem recomeçar do zero

---

## 2. Curadoria de Fontes

Cinco fontes abertas, carregadas no NotebookLM. **Três em português, duas em inglês** — a mistura
é deliberada e está explicada na seção 3.

| # | Fonte | Formato | Idioma | Por que entrou |
|---|---|---|---|---|
| 1 | [Kanban Guide — dez/2020](https://kanbanguides.org/the-kanban-guide/2020.12/pdf/kanban-guide.v2020.12.pt-BR.pdf) | PDF, 11 pág. | 🇧🇷 | define as quatro métricas obrigatórias; é o texto canônico |
| 2 | [Kanban Pocket Guide](https://prokanban.org/pdfs/kanban-pocket-guide-pt.pdf) | PDF, 114 pág. | 🇧🇷 | uso prático das mesmas métricas, linguagem mais direta |
| 3 | [Kanban Guide — mai/2025](https://kanbanguides.org/the-kanban-guide/2025.5/pdf/kanban-guide.v2025.5.en.pdf) | PDF | 🇬🇧 | **versão atual**; a tradução PT está cinco anos atrás |
| 4 | [Getting to 85 — Agile Metrics with ActionableAgile](https://www.scrum.org/resources/blog/getting-85-agile-metrics-actionableagile-part-1) | web | 🇬🇧 | trata o percentil 85 diretamente |
| 5 | [Métricas Ágeis: o que o Lead Time fala sobre seu projeto](https://blog.plataformatec.com.br/2017/08/metricas-ageis-o-que-lead-time-fala-sobre-seu-projeto/) | web | 🇧🇷 | percentis aplicados, com gráficos |

**Critério:** só fonte aberta, sem cadastro e sem paywall — qualquer pessoa consegue reproduzir
este caderno. Material licenciado ou com dado pessoal ficou de fora por princípio, não por acaso.

---

## 3. Engenharia de Prompts e Cicatrizes

> Esta seção registra **o que deu errado**, não só o que funcionou.

### 3.0 A cicatriz que veio antes do NotebookLM

Antes de qualquer prompt, um erro real de leitura dos meus próprios dados.

Eu tinha um conjunto de processos medidos e afirmei que eles **"morriam em média 12 dias"**.
Estava errado. **12 era o percentil 85**; a média era **6,7** e a mediana, **4**.

| Leitura | Valor | O que ela faria eu decidir |
|---|---|---|
| "média 12 dias" ❌ | — | esperar 12 dias antes de considerar um caso perdido |
| Média real | 6,7 dias | — |
| **p50 (mediana)** | **4 dias** | metade dos casos já respondeu aqui |
| **p85** | **12 dias** | passou disso, provavelmente acabou |

**O erro não foi de cálculo, foi de nome.** Chamar o p85 de "média" quase dobra o número e troca a
decisão. É exatamente o problema que este caderno investiga.

### 3.1 O experimento: a mesma pergunta, duas formulações

O caderno tinha uma pergunta central: **as definições mudaram entre a versão de 2020 e a de 2025?**
Rodei dois prompts sobre isso. A diferença entre eles é a lição inteira.

#### Prompt 1 — formulação aberta

```
Entre as fontes carregadas existem duas versões do mesmo documento: o Kanban Guide
traduzido para português em dezembro de 2020 e a versão em inglês de maio de 2025.
Compare as duas e liste todas as diferenças nas definições das métricas de fluxo
obrigatórias. Se não houver nenhuma diferença, diga isso explicitamente.
```

**Resposta:** afirmou que não há diferença conceitual, transcreveu as quatro definições lado a lado
nos dois idiomas, e listou três "ajustes formais": renomeação do capítulo, exemplos de nomes
alternativos e a remoção do termo "imutabilidade".

**Verificação:** ✅ tudo correto. Conferi as quatro definições palavra por palavra nos dois PDFs.

**⚠️ Mas o método não foi comparação.** O PDF de 2025 publica o próprio changelog, numa seção
chamada **"2025 Adaptations"**, e ele diz literalmente:

> *"Kanban Measures renamed to Flow Metrics"* · *"More explicit about the flexibility around flow
> metric names"* · *"Deleted reference to immutability of Kanban"*

**São exatamente os três itens da resposta, na mesma ordem.** Ele leu a auto-descrição do documento
e entregou como análise comparativa.

**Por que isso importa:** se o changelog estivesse incompleto ou errado, a resposta herdaria o erro
sem nenhum sinal — com a mesma confiança e a mesma formatação. E como a resposta estava **certa**,
quem confiasse teria acertado. É a falha que nunca se manifesta, até o dia em que se manifesta.

#### Prompt 2 — a mesma pergunta, com o atalho fechado

```
Existe alguma diferença entre as duas versões do Kanban Guide que NÃO esteja listada
na seção de mudanças do documento de 2025? Responda citando o trecho exato de cada
versão.
```

Uma cláusula a mais — `que NÃO esteja listada na seção de mudanças` — e o changelog deixou de
servir. **Aí ele comparou de verdade**, e devolveu quatro achados:

| # | Achado | Verificado |
|---|---|---|
| 1 | A lista de *gerenciamento ativo* tinha **4 marcadores em 2020 e tem 3 em 2025** — sumiu *"Evitar a acumulação de itens de trabalho em qualquer parte do fluxo"* | ✅ |
| 2 | 2025 acrescenta *"The order in which these are implemented is not important as long as they are all adopted"* sobre os 6 elementos da DoW | ✅ |
| 3 | Agradecimentos reformulados, com colaboradores trocados | ✅ |
| 4 | A capa de 2025 passou a trazer `Authors: John Coleman · Daniel Vacanti` | ✅ |

Conferi os quatro no texto extraído dos PDFs. **Todos verdadeiros.** O item 4 é mais do que
formatação: em 2020 o guia era de Vacanti e da Orderly Disruption; em 2025 **John Coleman aparece
como coautor** — é mudança de governança do documento.

### 3.2 ⭐ A quinta diferença — que a IA não achou

Conferindo o item 1 no PDF, li a frase **seguinte** à lista. Ela também mudou, e ninguém apontou:

| | |
|---|---|
| **PT 2020** | *"Uma prática comum é que os membros revisem regularmente o gerenciamento ativo dos itens. **Embora alguns possam escolher uma reunião diária**, não…"* |
| **EN 2025** | *"A common practice is for Kanban system members to review the active items regularly. **This review can occur continuously, at regular intervals, or through a combination of both.**"* |

**A referência à reunião diária foi removida do guia.** Para quem trabalha com agilidade isso não é
detalhe: é o Kanban Guide tirando a daily do texto e substituindo por "contínuo, em intervalos
regulares, ou ambos".

### 3.3 A conclusão do experimento

**O mesmo modelo, a mesma base de fontes, duas formulações — dois níveis de esforço.**

A qualidade da resposta não foi determinada pela capacidade da ferramenta, e sim por **o prompt ter
fechado o caminho barato**. Enquanto existia um atalho válido (o changelog), ele foi usado. Quando
o atalho foi excluído por escrito, o trabalho real aconteceu.

E há uma camada final: **mesmo a boa resposta estava incompleta.** A IA achou quatro diferenças; a
conferência humana achou a quinta — justamente a mais relevante para a profissão.

> **A IA acelera. A validação encontra o que ela deixou.**

### 3.4 Troubleshooting — o que atrapalhou no caminho

| Problema | O que era | Como resolvi |
|---|---|---|
| O produto mudou de nome | **NotebookLM virou Gemini Notebook**, e a URL migrou para `notebook.google.com`. O material do curso fala em NotebookLM o tempo todo | seguir pelo produto, não pelo nome no tutorial |
| Upload de PDF parecia obrigatório | a opção *"Sites"* avisa que importa "apenas o texto visível no site" | **testei mesmo assim: PDF entra por URL direta.** Os três PDFs foram carregados por link, sem upload |
| Duas fontes pareciam fora do ar | `curl` devolvia **403** para Scrum.org e Medium | era **bloqueio de robô, não link quebrado**. No navegador abrem normalmente — a medição estava errada, não a fonte |
| A tradução PT estava desatualizada | a versão em português é de **dez/2020**; a atual em inglês é de **mai/2025** | virou o objeto do estudo em vez de um obstáculo |

---

## 4. Miniguia de Estudo

### 4.1 Resumos estruturados

#### As quatro métricas de fluxo obrigatórias

Definições transcritas do **Kanban Guide**, não parafraseadas:

| Métrica | Definição (PT, 2020) | Definição (EN, 2025) |
|---|---|---|
| **WIP** | "O número de itens de trabalho iniciados mas não terminados." | "The number of work items started but not finished." |
| **Vazão / Throughput** | "O número de itens de trabalho terminados por unidade de tempo. Observe que a medição da vazão é a contagem exata dos itens de trabalho." | "The number of work items finished per unit of time… the exact count of work items." |
| **Idade do Item de Trabalho** | "A quantidade de tempo decorrido entre quando um item de trabalho começou e o tempo atual." | "The elapsed time between when a work item started and the current date." |
| **Tempo de Ciclo** | "A quantidade de tempo transcorrido entre quando um item de trabalho começou e quando um item de trabalho terminou." | "The elapsed time between when a work item started and when a work item finished." |

⚠️ **"Iniciado" e "terminado" não são universais.** As duas versões deixam explícito que esses
pontos são os que a equipe definiu na sua **Definição do Fluxo de Trabalho (DoW)**. Comparar Cycle
Time entre dois times com DoW diferentes é comparar coisas diferentes.

#### ⭐⭐ O percentil está no guia — mas sem esse nome

**Descoberta ao conferir a fonte: a palavra "percentil" não aparece nenhuma vez em nenhuma das duas
versões do Kanban Guide.** Zero ocorrências, em português e em inglês.

O conceito, no entanto, está lá — dentro da definição de **SLE (Service Level Expectation)**:

> *"O SLE é uma previsão de quanto tempo deveria levar um único item de trabalho do início ao fim.
> **O SLE em si tem duas partes: um período de tempo decorrido e uma probabilidade associada a esse
> período** (por exemplo, **"85% dos itens de trabalho estarão acabados em oito dias ou menos"**).
> O SLE deve ser baseado no tempo de ciclo histórico, e uma vez calculado, deve ser visualizado no
> quadro Kanban."*
> — Kanban Guide, dez/2020 (PT-BR)

**"Uma probabilidade associada a um período" é a definição de percentil.** E o exemplo do próprio
guia usa **85%** — o mesmo p85.

**Consequência para a tese deste caderno:** o guia canônico **não manda usar média em lugar
nenhum**. Ele manda coletar as quatro métricas e expressar a previsão como *probabilidade + prazo*.
Quem reporta "lead time médio" está usando uma estatística que a fonte nunca pediu.

#### Média × mediana × percentil

- A **média** é puxada por poucos casos extremos e descreve um item que talvez não exista.
- A **mediana (p50)** responde: *"metade dos casos termina até aqui."*
- O **p85** responde: *"85% dos casos termina até aqui"* — e é a forma que o SLE exige.
- **Percentil por nearest-rank:** ordena-se a amostra e toma-se a posição correspondente. Com
  amostra pequena, **um único caso novo move o p85** — por isso o número deve ser recalculado.

> ⬜ **A COMPLETAR:** ler um scatterplot de Cycle Time e extrair p50/p85 dele (fontes 4 e 5).

### 4.2 Glossário

| Termo | Definição | Fonte |
|---|---|---|
| **WIP** (Work in Progress) | Itens de trabalho iniciados mas não terminados | Kanban Guide |
| **Vazão** (Throughput) | Itens terminados por unidade de tempo — contagem exata, não estimativa | Kanban Guide |
| **Idade do Item** (Work Item Age) | Tempo decorrido entre o início do item e **hoje**; só existe para item não terminado | Kanban Guide |
| **Tempo de Ciclo** (Cycle Time) | Tempo decorrido entre início e fim de um item | Kanban Guide |
| **DoW** (Definition of Workflow) | Acordo explícito do time sobre o que é item, onde começa, onde termina, estados, controle de WIP, políticas e SLE | Kanban Guide |
| **SLE** (Service Level Expectation) | Previsão com **duas partes**: um prazo e uma probabilidade associada — ex.: "85% em oito dias ou menos" | Kanban Guide |
| **Percentil** | Valor abaixo do qual cai uma dada porcentagem das observações. ⚠️ **Termo ausente do Kanban Guide**, embora o SLE seja um enunciado de percentil | fontes 4 e 5 |
| **p50 / mediana** | Metade das observações está abaixo | fontes 4 e 5 |
| **p85** | 85% das observações estão abaixo; é o valor usado no exemplo de SLE do próprio guia | Kanban Guide (implícito) + fonte 4 |
| **Lead Time** | ⬜ a completar — ⚠️ **não é termo do Kanban Guide**, que usa Cycle Time | fonte 5 |
| **Scatterplot de Cycle Time** | ⬜ a completar | fonte 4 |
| **CFD** (Cumulative Flow Diagram) | ⬜ a completar | fonte 5 |

### 4.3 Prompts reutilizáveis

Os cinco que sobreviveram ao teste, prontos para reusar em qualquer caderno de estudo.

```
1. Liste as definições de [conceito] exatamente como aparecem nas fontes, sem
   parafrasear, indicando de qual fonte veio cada uma.

2. Existe alguma diferença entre [fonte A] e [fonte B] que NÃO esteja listada na
   seção de mudanças/resumo de nenhuma delas? Cite o trecho exato de cada versão.

3. Se as fontes divergirem sobre [tema], mostre a divergência em vez de conciliar.
   Se não divergirem, diga isso explicitamente.

4. Qual afirmação sua nesta resposta NÃO está sustentada por uma citação direta das
   fontes? Liste-as separadamente.

5. Esta fonte usa o termo [X]? Se não usar, qual termo ela usa no lugar, e onde o
   conceito aparece?
```

⭐ **O padrão que faz os cinco funcionarem: cada um fecha um caminho barato.** Proibir paráfrase,
excluir o resumo pronto, proibir a conciliação, obrigar a separar o que não tem lastro, e não deixar
assumir que o termo existe. Prompt bom não é o mais detalhado — é o que **remove a saída fácil**.

---

## Sobre este repositório

Entrega do Desafio de Projeto **"Treinando uma IA de Aprendizagem: Explore o Poder do NotebookLM"**
— [DIO](https://dio.me), Bootcamp Santander *Automação com n8n*.

As citações do Kanban Guide foram conferidas diretamente no texto dos PDFs (`pdftotext`), não na
resposta da IA. Os arquivos-fonte estão em [`/fontes`](./fontes).

Autor: **Márccio Mêsquita** · [LinkedIn](https://www.linkedin.com/in/márcciomêsquita)
