# Análise Comparativa entre Modelos para Geração de Código

Este repositório contém o experimento para avaliação e comparação de LLMs na tarefa de geração automática de soluções de programação em python, com e sem *fine-tuning*, incluindo o uso de aumento de dados sintéticos via `sdg-hub`.

## Objetivo

Investigar a capacidade de diferentes modelos de linguagem em resolver problemas de programação de níveis variados de dificuldade, comparando desempenho entre modelos e entre estratégias de treinamento.

## Questões de Pesquisa

1. Qual a taxa de sucesso das implementações de modelos para problemas?
2. Como a dificuldade do problema apresentado afeta a qualidade da solução gerada?
3. Como os diferentes tipos de treinamento (com e sem o uso do `sdg-hub`) alteram a capacidade dos modelos de solucionar problemas de variados níveis de dificuldade?
4. De que forma modelos de naturezas diferentes, mas com parâmetros comparáveis, divergem em suas abordagens e métricas de sucesso ao resolver problemas?

## Modelos Avaliados

- **[Qwen 3.5 4B](https://huggingface.co/Qwen/Qwen3.5-4B)**
- **[Granite 4.2 3B](https://huggingface.co/ibm-granite/granite-4.2-3b)**

##  Métricas

- **Pass@k** (k = 1) — percentual de soluções dadas como accepts
- **Test Case Pass Ratio** — confiabilidade da solução frente aos casos de teste
- **Execution Error Rate** — falhas por domínio insuficiente de Python ou erros de lógica do algoritmo

## Datasets

- **[APPS Benchmark](https://huggingface.co/datasets/codeparrot/apps)** — utilizado para avaliação dos modelos, contendo problemas de diferentes níveis de dificuldade (Competition, Introductory, Interview).
- **[CodeAlpaca-20k](https://huggingface.co/datasets/HuggingFaceH4/CodeAlpaca_20K)** — utilizado como dataset para o *fine-tuning* dos modelos.

## Metodologia

### 1. Seleção da amostra
Seleção de questões com diferentes níveis de dificuldade presentes no APPS Benchmark, além do CodeAlpaca-20k para o processo de fine-tuning.

### 2. Execução dos problemas
Cada modelo recebe o mesmo problema e o mesmo prompt padronizado, gerando uma proposta de solução.

**Prompt utilizado:**
```python
system_prompt = "Act as an expert competitive programmer. Solve the following problem using Python 3. Output ONLY the raw, executable Python code. Do not include any explanations, greetings, comments, or markdown formatting. Your entire response must be valid code ready to be submitted to an online judge."

prompt_base = "Problem description:\n{question}"
```

**Parâmetros de geração:**
| Parâmetro | Valor | Justificativa |
|---|---|---|
| `temperature` | 0 | A geração de código exige lógica, não criatividade, garantindo avaliação justa |
| `max_tokens` | 2048 | Garante espaço suficiente para algoritmos mais extensos, evitando corte no meio da execução |

### 3. Avaliação das soluções
Aplicação dos testes automatizados disponibilizados no dataset para cada solução gerada.

### 4. Fine-tuning inicial
Execução do fine-tuning utilizando o `training-hub` com o dataset CodeAlpaca-20k, seguida de reavaliação dos modelos treinados com os mesmos parâmetros e prompt anteriores.

### 5. Aumento de dados com sdg-hub
Utilização do `sdg-hub` para expandir o dataset original de treino (CodeAlpaca-20k), seguida de novo fine-tuning via `training-hub` e reavaliação dos modelos com os mesmos parâmetros e prompt.

### 6. Análise final comparativa
Comparações final utilizando todos os dados obtidos.


## Ameaças à Validade

- **Validade de construto — Qualidade dos casos de teste (APPS Benchmark):** a corretude é validada exclusivamente pelos testes automatizados do dataset. Caso os testes disponíveis não sejam suficientes, a métrica de "corretude" pode registrar falsos positivos ou falsos negativos.
- **Validade externa — Limitação de linguagem de programação:** o experimento está restrito à geração de código em Python 3. Os resultados não podem ser diretamente generalizados para outras linguagens, como Java ou C++.

## Estrutura do Repositório

```
.
├── src/                  # Código-fonte do pipeline, de geração, testes e avaliação
├── results/              # Resultados e métricas coletadas
└── README.md
```

## Ferramentas Utilizadas

- **[training-hub](https://github.com/Red-Hat-AI-Innovation-Team/training_hub)** — execução do processo de fine-tuning
- **[sdg-hub](https://github.com/Red-Hat-AI-Innovation-Team/sdg_hub)** — aumento sintético do dataset de treino

---