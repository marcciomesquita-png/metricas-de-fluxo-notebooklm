# Métricas de fluxo: decidir por percentil, não por média

> Caderno temático construído no **NotebookLM** como Desafio de Projeto da
> [DIO](https://dio.me) — Bootcamp Santander *Automação com n8n*.

---

## 1. Contexto e Objetivos

### O assunto

Métricas de fluxo para times ágeis — **WIP, Vazão (Throughput), Idade do Item de Trabalho
(Work Item Age) e Tempo de Ciclo (Cycle Time)** — e a pergunta que separa quem coleta
número de quem decide com ele: **usar média ou percentil?**

### Por que este assunto

> ⬜ **A PREENCHER** — em 3 a 5 linhas, com as suas palavras. Sugestão de ângulo:
> você já opera um board instrumentado e já tomou uma decisão errada lendo o próprio
> dado (ver bloco 3). O objetivo aqui não é aprender o que são as métricas, é
> estabelecer **quando cada leitura engana**.

### Objetivos de estudo

- [ ] Fixar a definição **canônica** das quatro métricas obrigatórias, na fonte, sem intermediário
- [ ] Entender por que a média de Lead Time induz a erro e o que o percentil resolve
- [ ] Saber ler um **scatterplot de Cycle Time** e extrair p50 / p85 dele
- [ ] Produzir um conjunto de **prompts reutilizáveis** para revisar o tema sem recomeçar do zero
- [ ] Testar a **confiabilidade do NotebookLM** quando as fontes discordam entre si

---

## 2. Curadoria de Fontes

Cinco fontes abertas, carregadas no NotebookLM. **Três em português, duas em inglês** —
a mistura é deliberada e está explicada no bloco 3.

| # | Fonte | Formato | Idioma | Por que entrou |
|---|---|---|---|---|
| 1 | [Kanban Guide — dez/2020](https://kanbanguides.org/the-kanban-guide/2020.12/pdf/kanban-guide.v2020.12.pt-BR.pdf) | PDF | 🇧🇷 | define as quatro métricas obrigatórias; é o texto canônico |
| 2 | [Kanban Pocket Guide](https://prokanban.org/pdfs/kanban-pocket-guide-pt.pdf) | PDF | 🇧🇷 | uso prático das mesmas métricas, linguagem mais direta |
| 3 | [Kanban Guide — mai/2025](https://kanbanguides.org/the-kanban-guide/2025.5/pdf/kanban-guide.v2025.5.en.pdf) | PDF | 🇬🇧 | **versão atual**; a tradução PT está 5 anos atrás |
| 4 | [Getting to 85 — Agile Metrics with ActionableAgile](https://www.scrum.org/resources/blog/getting-85-agile-metrics-actionableagile-part-1) | web | 🇬🇧 | trata o percentil 85 diretamente |
| 5 | [Métricas Ágeis: o que o Lead Time fala sobre seu projeto](https://blog.plataformatec.com.br/2017/08/metricas-ageis-o-que-lead-time-fala-sobre-seu-projeto/) | web | 🇧🇷 | percentis aplicados, com gráficos |

**Critério de seleção:** só fonte aberta, sem cadastro e sem paywall — qualquer pessoa
consegue reproduzir este caderno. Material licenciado ou com dado pessoal ficou de fora
por princípio, não por acaso.

---

## 3. Engenharia de Prompts e Cicatrizes

> Esta é a seção que interessa. Ela registra **o que deu errado**, não só o que funcionou.

### 3.0 A cicatriz que veio antes do NotebookLM

Antes de qualquer prompt, um erro real de leitura dos meus próprios dados:

Eu tinha um conjunto de processos medidos e afirmei que eles **"morriam em média 12 dias"**.
Estava errado. **12 era o percentil 85**; a média era **6,7** e a mediana, **4**.

| Leitura | Valor | O que ela faria eu decidir |
|---|---|---|
| "média 12 dias" ❌ | — | esperar 12 dias antes de considerar um caso perdido |
| Média real | 6,7 dias | — |
| **p50 (mediana)** | **4 dias** | metade dos casos já respondeu aqui |
| **p85** | **12 dias** | passou disso, provavelmente acabou |

**O erro não foi de cálculo, foi de nome.** Chamar o p85 de "média" quase dobra o número e
troca a decisão. É exatamente o problema que este caderno investiga — e é por isso que ele
existe.

### 3.1 Perguntas estratégicas testadas

> ⬜ **A PREENCHER durante a sessão no NotebookLM.** Para cada pergunta, registrar:
> o prompt exato, a resposta obtida, **qual fonte ele citou**, e o veredito.

| # | Prompt testado | Fonte citada | Veredito |
|---|---|---|---|
| 1 | ⬜ | ⬜ | ⬜ |
| 2 | ⬜ | ⬜ | ⬜ |
| 3 | ⬜ | ⬜ | ⬜ |

### 3.2 Variações — o mesmo pedido, formulado de três formas

> ⬜ **A PREENCHER.** Pegar **uma** pergunta e reescrevê-la em três níveis de especificidade,
> registrando como a resposta muda. É aqui que se demonstra engenharia de prompt, não no
> prompt que deu certo de primeira.

### 3.3 O teste de conflito entre fontes

**Hipótese a testar:** a fonte 1 (português, 2020) e a fonte 3 (inglês, 2025) são o mesmo
documento com cinco anos de diferença. Se as definições mudaram, o NotebookLM responde com
qual? Avisa que há divergência, ou escolhe uma em silêncio?

> ⬜ **A PREENCHER.** Este é o teste mais valioso do caderno: mede se a ferramenta
> **sinaliza conflito** ou **fabrica consenso**. Registrar o prompt, a resposta e a conclusão.

### 3.4 Dificuldades encontradas (troubleshooting)

> ⬜ **A PREENCHER.** Anotar tudo, inclusive o que parecer bobo: fonte que não subiu,
> resposta genérica, citação que apontava para o trecho errado, pergunta que precisou ser
> refeita. O enunciado da DIO pede isso explicitamente.

---

## 4. Miniguia de Estudo

### 4.1 Resumos estruturados

> ⬜ **A PREENCHER** a partir das respostas validadas no bloco 3.

- **As quatro métricas obrigatórias** — ⬜
- **Média × mediana × percentil** — ⬜
- **Como ler um scatterplot de Cycle Time** — ⬜
- **WIP e Lei de Little** — ⬜

### 4.2 Glossário

| Termo | Definição | Fonte |
|---|---|---|
| **WIP** (Work in Progress) | ⬜ | ⬜ |
| **Vazão** (Throughput) | ⬜ | ⬜ |
| **Idade do Item de Trabalho** (Work Item Age) | ⬜ | ⬜ |
| **Tempo de Ciclo** (Cycle Time) | ⬜ | ⬜ |
| **Lead Time** | ⬜ | ⬜ |
| **Percentil** | ⬜ | ⬜ |
| **p50 / mediana** | ⬜ | ⬜ |
| **p85** | ⬜ | ⬜ |
| **Scatterplot de Cycle Time** | ⬜ | ⬜ |
| **CFD** (Cumulative Flow Diagram) | ⬜ | ⬜ |
| **Lei de Little** | ⬜ | ⬜ |

### 4.3 Prompts reutilizáveis

> ⬜ **A PREENCHER** — os prompts que sobreviveram ao bloco 3, prontos para reusar numa
> revisão futura sobre este mesmo tema.

```
1. ⬜
2. ⬜
3. ⬜
4. ⬜
5. ⬜
```

---

## Sobre este repositório

Entrega do Desafio de Projeto **"Treinando uma IA de Aprendizagem: Explore o Poder do
NotebookLM"** — [DIO](https://dio.me), Bootcamp Santander *Automação com n8n*.

Autor: **Márccio Mêsquita** · [LinkedIn](https://www.linkedin.com/in/márcciomêsquita)
