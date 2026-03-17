# Analise-Campanha-Deposito-Prazo
Projecto de Análise de dados de uma campanha de marketing bancário para depósitos a prazo, utilizando técnicas de análise de dados para identificar padrões e fatores que influenciam a adesão dos clientes.

![SQL](https://img.shields.io/badge/Ferramenta-SQL-4479A1?style=flat&logo=postgresql&logoColor=white)
![Status](https://img.shields.io/badge/Status-Concluído-brightgreen)

---

## Problema do Negócio

Um banco necessitava de compreender melhor o desempenho da sua campanha de marketing para adesão ao produto de Depósito à Prazo, avaliar o perfil financeiro e demográfico dos clientes contactados e identificar os factores que mais influenciam a decisão de subscrição.

O Gestor levantou as seguintes perguntas:

1. Verificar os campos com valores desconhecidos (`job`, `contact`, `education` e `poutcome`) e separar uma amostra para que outro analista faça uma análise manual.
2. Quantos clientes a campanha obteve? Qual o percentual?
3. A campanha visa que os clientes adoptem o Depósito à Prazo — logo, queremos clientes com bastante capital. Será que conseguimos?
4. A taxa de sucesso da campanha muda de acordo com o meio de contacto (celular, telefone ou desconhecido)?
5. Como o padrão de habitação, idade e estado civil muda entre subscritores e não subscritores?

---

## Contexto

A base de dados utilizada contém informações de **45.211 clientes** de um banco, com dados sobre o perfil demográfico, situação financeira, histórico de contactos e resultado da campanha de marketing para adesão ao Depósito à Prazo. A variável central da análise é a coluna `y`, que indica se o cliente subscreveu ou não o produto.

> **Fonte dos dados:** [UCI Machine Learning Repository — Bank Marketing Dataset](https://archive.ics.uci.edu/ml/datasets/Bank+Marketing)

---

## Premissas da Análise

- Os dados foram analisados em **SQL (PostgreSQL)** e complementados com **Excel**.
- Antes de iniciar a análise, foi realizado um **sanity check** para identificar registos com valores desconhecidos (`unknown`) nas colunas críticas.
- Os registos com `unknown` não foram removidos da análise geral, mas foram isolados numa amostra separada para análise manual por outro analista.
- A coluna `y` é a variável central, indicando se o cliente aderiu (`yes`) ou não (`no`) ao Depósito à Prazo.

---

## Estratégia da Solução

### Passo 1 — Resumo do Contexto em Pergunta Aberta
> *Qual é o perfil dos clientes que aderem ao Depósito à Prazo e que factores influenciam o sucesso desta campanha?*

### Passo 2 — Transformação em Perguntas Fechadas
> - Os dados estão em condições de ser analisados ou existem problemas de qualidade?
> - Qual a taxa de adesão real à campanha?
> - Os clientes aderentes têm capital suficiente para sustentar o produto?
> - Qual o canal de contacto mais eficaz?
> - Que perfil demográfico caracteriza os subscritores?

### Passo 3 — Definição da Coluna Facto
A coluna facto principal é **y** (subscrição do Depósito à Prazo), complementada pelo **balance** (saldo bancário), que mede a capacidade financeira dos clientes.

### Passo 4 — Identificação das Dimensões

| Dimensão | Coluna |
|----------|--------|
| Perfil Demográfico | `age`, `marital`, `education` |
| Perfil Profissional | `job` |
| Perfil Financeiro | `balance`, `default`, `housing`, `loan` |
| Canal de Contacto | `contact` |
| Campanha | `campaign`, `pdays`, `previous`, `poutcome` |
| Resultado | `y` |

### Passo 5 — Hipóteses Analíticas

- H1: A maioria dos registos tem qualidade suficiente para análise directa.
- H2: A taxa de adesão à campanha é baixa em relação ao total de clientes contactados.
- H3: Clientes que aderiram possuem saldo médio superior aos não aderentes.
- H4: O canal cellular apresenta maior taxa de conversão do que o telephone.
- H5: Clientes sem crédito habitação e solteiros têm maior propensão à subscrição.

### Passo 6 — Priorização das Hipóteses Analíticas

| Prioridade | Hipótese | Justificativa |
|------------|----------|---------------|
| Alta | H2 — Taxa de adesão à campanha | Mede directamente a eficácia do investimento em marketing |
| Alta | H3 — Perfil financeiro dos aderentes | Valida se o produto está a atingir o público-alvo correcto |
| Alta | H4 — Eficácia por canal de contacto | Orienta a estratégia de comunicação em campanhas futuras |
| Média | H5 — Perfil demográfico dos subscritores | Apoia a segmentação para futuras campanhas |
| Baixa | H1 — Qualidade dos dados | Base para garantir a fiabilidade de toda a análise |

---

## Insights da Análise

### Qualidade dos Dados — Sanity Check

<img width="633" height="145" alt="primeira_analise" src="https://github.com/user-attachments/assets/63b971ef-32b1-445d-a7fc-9cb7e207a778" />

Foram identificados valores `unknown` nas seguintes colunas críticas:

| Coluna | Registos Unknown | Percentual |
|--------|-----------------|------------|
| `poutcome` | 36.959 | 81,75% |
| `contact` | 13.020 | 28,80% |
| `education` | 1.857 | 4,11% |
| `job` | 288 | 0,64% |

No total, **37.369 registos (82,65%)** possuem pelo menos um campo desconhecido, com destaque para a coluna `poutcome`, onde a grande maioria dos clientes não tinha histórico de campanhas anteriores. Estes registos foram isolados numa amostra separada para análise manual, garantindo a qualidade das conclusões.

### Adesão à Campanha

<img width="796" height="482" alt="analise2" src="https://github.com/user-attachments/assets/2944d87f-0c87-41ad-8713-7dc5dd307a48" />

| Resultado | Total | Percentual | Média de Saldo | Média de Idade |
|-----------|-------|------------|----------------|----------------|
| Não aderiu | 39.922 | 88,30% | 1.303,71 | 40,84 anos |
| Aderiu | 5.289 | 11,70% | 1.804,27 | 41,67 anos |

A campanha obteve uma **taxa de adesão de 11,70%**, o que representa **5.289 clientes** num universo de 45.211 contactados. Os aderentes apresentam uma média de idade ligeiramente superior e um saldo médio mais elevado.

### Perfil Financeiro dos Clientes

<img width="795" height="502" alt="analise3" src="https://github.com/user-attachments/assets/82e0e2fe-e3b0-447b-8ba9-52a039c9b147" />

| Resultado | Saldo Médio | Saldo Mínimo | Saldo Máximo |
|-----------|-------------|--------------|--------------|
| Não aderiu | 1.303,71 | -8.019 | 102.127 |
| Aderiu | 1.804,27 | -3.058 | 81.204 |

Os clientes que aderiram apresentam um **saldo médio 38% superior** aos não aderentes (1.804 vs. 1.303), o que indica que a campanha conseguiu atrair clientes com maior capacidade financeira. No entanto, a elevada variação nos saldos mostra que existem aderentes com saldos muito baixos, pelo que não se pode afirmar que todos possuem capital elevado.

### Taxa de Sucesso por Canal de Contacto

<img width="797" height="503" alt="analise4" src="https://github.com/user-attachments/assets/cc4dd5b4-53db-4656-b8a8-a86d5f3cb74a" />

| Canal | Total Contactos | Aderiram | Taxa de Sucesso |
|-------|----------------|----------|-----------------|
| Cellular | 29.285 | 4.369 | 14,92% |
| Telephone | 2.906 | 390 | 13,42% |
| Unknown | 13.020 | 530 | 4,07% |

O canal **cellular** apresenta a maior taxa de conversão (14,92%), seguido do **telephone** (13,42%). O canal desconhecido regista apenas 4,07% de conversão, reforçando a importância de registar correctamente o meio de contacto e priorizar o telemóvel em campanhas futuras.

### Perfil Demográfico — Subscritores vs. Não Subscritores

<img width="801" height="559" alt="analise5" src="https://github.com/user-attachments/assets/854788b0-4b35-452e-b168-05770d9f7278" />

Entre os **não subscritores**, predominam clientes casados com crédito habitação activo e idade média de 40,8 anos, sugerindo maiores compromissos financeiros que limitam a adesão a novos produtos.

Entre os **subscritores**, destacam-se dois perfis:
- **Casados sem crédito habitação** — maior grupo (1.746 clientes), com média de 49,7 anos, perfil financeiramente mais estável.
- **Solteiros sem crédito habitação** — segundo maior grupo (1.235 clientes), com média de 31,5 anos, perfil jovem e sem grandes compromissos financeiros.

---

## Resultados e Recomendações

Com base na análise, é possível concluir que:

- A **taxa de adesão de 11,70%** é relativamente baixa e pode ser melhorada com uma segmentação mais precisa do público-alvo.
- O banco deve **priorizar o canal cellular** nas próximas campanhas, dado que apresenta a maior taxa de conversão.
- O perfil ideal de cliente a contactar é **casado ou solteiro, sem crédito habitação activo e com saldo médio superior a 1.800**.
- A **elevada percentagem de valores desconhecidos** em `poutcome` e `contact` deve ser endereçada para melhorar a qualidade dos dados em campanhas futuras.
- Clientes com **maior saldo médio** têm maior propensão à adesão, pelo que as campanhas devem ser segmentadas com base na capacidade financeira.

---

##  Estrutura do Projecto

```
 analise-campanha-deposito-prazo
│
├── dados
│   └── bank-full.csv                    # Base de dados original
└── README.md                         # Documentação do projecto
```

---

## Ferramentas Utilizadas

- **SQL (PostgreSQL)** — Extracção, limpeza, tratamento e análise dos dados

---

## Autor

**Santiago Casseca**
[LinkedIn](https://www.linkedin.com/in/santiago-casseca) • [GitHub](https://github.com)
