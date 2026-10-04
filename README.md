# Pratique – Coleta de dados do Banco Central via API

Projeto da atividade prática do curso (Python para Dados, EBAC): consumir uma API pública do Banco Central do Brasil com `requests`, transformar o retorno em DataFrame, tratar e validar os dados e salvar os resultados em CSV e Parquet.

**Autor:** Pedro Arantes Oliveira
**Data:** 01/10/2026

---

## Objetivo

Extrair a série histórica do **IPCA (variação mensal, %)** do Sistema Gerenciador de Séries Temporais (SGS) do Banco Central, preparar os dados para análise e documentar boas práticas de coleta de dados (incluindo aspectos legais e éticos).

## Fonte dos dados

| Item | Descrição |
|---|---|
| Instituição | Banco Central do Brasil – Dados Abertos (SGS) |
| Série | 433 – IPCA, variação mensal (%) |
| Endpoint | `https://api.bcb.gov.br/dados/serie/bcdata.sgs.433/dados` |
| Parâmetros | `formato=json`, `dataInicial=01/01/2015`, `dataFinal=<data de hoje>` |
| Autenticação | Não necessária (API pública) |
| Data da coleta | 01/10/2026 |

## Estrutura do projeto

```
projeto/
├── pratique_ibge_bcb.ipynb   # notebook principal (código + respostas teóricas)
├── bcb_tabela.csv            # dado bruto, exatamente como veio da API
├── dados_tratados.csv        # dado tratado (CSV)
├── dados_tratados.parquet    # dado tratado (Parquet)
├── requirements.txt          # dependências
├── .gitignore
└── README.md
```

## Como executar (PyCharm)

1. Abra a pasta do projeto no PyCharm (**File → Open**).
2. Crie/ative o ambiente virtual (`.venv`).
3. No terminal do PyCharm, instale as dependências:
   ```bash
   pip install -r requirements.txt
   ```
4. Abra `pratique_ibge_bcb.ipynb` e execute todas as células (**Restart Kernel and Run All Cells**).
   > O notebook foi executado diretamente no PyCharm. Alternativamente, ele pode ser aberto com `jupyter lab`, VSCode ou Google Colab.

Os arquivos de saída são gravados na mesma pasta do notebook.

## Etapas do notebook

1. **Requisição:** `requests.get` com `timeout` e `raise_for_status()`.
2. **DataFrame:** conversão do JSON, exibição de `head()`, `shape` e `dtypes`.
3. **Dado bruto:** salvo em `bcb_tabela.csv`.
4. **Tratamentos:**
   - renomear colunas;
   - ajustar tipos (data → `datetime`, valor → `float`);
   - remover nulos e duplicados;
   - criar colunas `ano` e `mes` e ordenar por data.
5. **Validação:** verificação de tipos, nulos, duplicados, período e estatísticas descritivas.
6. **Saída tratada:** `dados_tratados.csv` e `dados_tratados.parquet`.
7. **Respostas teóricas:** importância do web scraping, riscos legais/éticos (termos de uso, direitos autorais, LGPD), medidas de mitigação e API vs. requisição sem API.

## Dicionário de dados

### `bcb_tabela.csv` (bruto)

| Coluna | Tipo | Descrição |
|---|---|---|
| `data` | texto | Data de referência no formato dd/mm/aaaa |
| `valor` | texto | Variação mensal do IPCA (%) |

### `dados_tratados.csv` / `.parquet`

| Coluna | Tipo | Descrição |
|---|---|---|
| `data_referencia` | datetime | Mês de referência (primeiro dia do mês) |
| `ipca_mensal_pct` | float | Variação mensal do IPCA (%) |
| `ano` | inteiro | Ano extraído da data |
| `mes` | inteiro | Mês extraído da data (1–12) |

## Resultados da validação

- 140 registros, de janeiro/2015 a agosto/2026.
- 0 valores nulos e 0 datas duplicadas.
- IPCA mensal: média de cerca de 0,45%, mínimo de -0,68% (deflação) e máximo de 1,62%.

## Boas práticas aplicadas

- `timeout` em todas as requisições.
- Tratamento feito em **cópia** do DataFrame, preservando o dado bruto.
- Validações explícitas (tipos, nulos, duplicados) com evidências no notebook.
- Dado bruto e dado tratado salvos separadamente.
- Uso de **API oficial** em vez de scraping, respeitando os termos de uso da fonte.
- Nenhum dado pessoal coletado (série econômica agregada, compatível com a LGPD).

## Limitações

- Os dados refletem o que o Banco Central/IBGE publicam na data da coleta e podem ser revisados.
- A série é mensal e agregada nacionalmente; não há detalhamento por região ou item.
- A série coletada cobre 140 meses, de janeiro/2015 a agosto/2026.

## Licença e uso dos dados

Os dados são públicos e disponibilizados pelo Banco Central do Brasil. Ao reutilizá-los, cite a fonte (Banco Central do Brasil – SGS). Este projeto tem finalidade educacional.
