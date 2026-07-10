# Codex do projeto

Ultima atualizacao: 10/07/2026

## Visao geral

Este repositorio contem uma analise exploratoria e um fluxo de
pre-processamento para o dataset "Gaming Addiction and Mental Health Analysis".
O trabalho atual esta concentrado em um notebook Jupyter que baixa o dataset do
Kaggle, carrega o CSV bruto, inspeciona campos principais, gera histogramas com
Plotly, trata valores ausentes, prepara uma versao traduzida para Power BI e
exporta arquivos processados em CSV e XLSX.

## Estrutura de pastas

```text
.
|-- README.md
|-- LICENSE
|-- .gitignore
|-- codex.md
|-- data/
|   |-- raw/
|   |   `-- gaming_addiction.csv
|   `-- processed/
|       |-- gaming_addiction_preprocessed.csv
|       `-- gaming_addiction_preprocessed_powerbi.xlsx
|-- notebooks/
|   `-- preprocessed.ipynb
|-- reports/
`-- venv/
```

- `data/raw/`: dados brutos. Hoje contem `gaming_addiction.csv`.
- `data/processed/`: dados tratados exportados pelo notebook.
- `notebooks/preprocessed.ipynb`: notebook principal de download, EDA, limpeza,
  traducao e exportacao.
- `reports/`: pasta presente para saidas analiticas, graficos ou relatorios,
  atualmente sem arquivos versionados.
- `venv/`: ambiente virtual local, ignorado pelo Git.

## Dataset

Fonte usada no notebook:
`dreamtensor/gaming-addiction-and-mental-health-analysis`, via `kagglehub`.

Estado local observado:

- Arquivo: `data/raw/gaming_addiction.csv`
- Linhas: 250
- Colunas: 49
- Valores ausentes totais no CSV bruto: 21
- Campos alvo ou de saida analitica provaveis:
  - `addiction_score`
  - `addiction_binary`
  - `addiction_severity`
  - `burnout_probability`
  - `mental_health_risk_score`
  - `churn_probability`

Categorias relevantes observadas:

- `addiction_severity`: `Moderate`, `Mild`, `None`, `Severe`
- `behavioral_cluster`: `Casual Enjoyer`, `Streamer/Creator`,
  `Social Gamer`, `Escape Seeker`, `Toxic Competitor`,
  `Competitive Grinder`, `Binge Player`

Arquivos processados observados:

- `data/processed/gaming_addiction_preprocessed.csv`
  - 250 linhas e 49 colunas
  - sem valores nulos apos leitura com `pandas`
  - nomes de colunas em portugues
  - valores categoricos majoritariamente traduzidos
  - colunas originalmente `float` convertidas para texto com virgula decimal
- `data/processed/gaming_addiction_preprocessed_powerbi.xlsx`
  - 250 linhas e 49 colunas
  - uma aba: `Sheet1`
  - sem valores nulos apos leitura com `pandas`

## Tecnologias e dependencias

O notebook declara kernel Python 3.12.10 e usa:

- `pandas`
- `kagglehub`
- `plotly.express`
- `os`
- `pathlib.Path`

Nao ha arquivo de dependencias no repositorio (`requirements.txt`,
`pyproject.toml`, `Pipfile` ou equivalente). Para reproduzir o ambiente atual,
instale manualmente as dependencias minimas antes de executar o notebook:

```powershell
python -m venv venv
.\venv\Scripts\Activate.ps1
pip install pandas kagglehub plotly notebook
```

Para exportar `.xlsx` com `pandas.to_excel`, o ambiente tambem precisa de um
engine de Excel compativel, como `openpyxl` ou `xlsxwriter`.

## Comandos importantes

Abrir o notebook:

```powershell
jupyter notebook notebooks/preprocessed.ipynb
```

Executar o notebook de forma automatizada, gerando uma copia executada:

```powershell
jupyter nbconvert --to notebook --execute notebooks/preprocessed.ipynb --output preprocessed.executed.ipynb
```

Verificar arquivos versionados e nao versionados:

```powershell
git status --short
```

## Fluxo principal atual

1. Importa `pandas`, `kagglehub`, `os`, `Path` e `plotly.express`.
2. Cria as pastas `../data/processed` e `../data/raw`.
3. Baixa o dataset do Kaggle com `kagglehub.dataset_download(...)`.
4. Move os arquivos baixados para `../data/raw`.
5. Carrega `../data/raw/gaming_addiction.csv` em um `DataFrame`.
6. Inspeciona dados, valores ausentes e colunas.
7. Gera histogramas para:
   - `depression_indicator`
   - `subscription_status`
   - `gpa_or_performance_score`
   - `addiction_severity`
8. Trata valores ausentes em:
   - `depression_indicator` com mediana por `addiction_severity`
   - `gpa_or_performance_score` com mediana por `occupation`
   - `subscription_status` com `No subscription`
   - `addiction_severity` com `Witout addiction`
9. Cria `df_powerbi` como copia de `df`.
10. Renomeia colunas para portugues em `df_powerbi`.
11. Traduz valores categoricos para uso no Power BI.
12. Converte colunas `float` de `df_powerbi` para texto com virgula decimal.
13. Exporta:
    - `../data/processed/gaming_addiction_preprocessed.csv`
    - `../data/processed/gaming_addiction_preprocessed_powerbi.xlsx`

## Configuracoes e variaveis de ambiente

- Nao ha variaveis de ambiente documentadas no repositorio.
- O download via `kagglehub` depende de acesso a internet e pode depender de
  credenciais locais do Kaggle, conforme a configuracao da maquina.
- O cache do `kagglehub` fica fora do repositorio, no perfil do usuario.
- `.gitignore` ja ignora ambientes virtuais, checkpoints de Jupyter, caches,
  logs e artefatos comuns de Python.
- O notebook atual usa kernel `venv`.

## Padroes de codigo e manutencao

- Manter alteracoes pequenas e focadas no fluxo de analise.
- Evitar versionar `venv/`, caches, checkpoints e saidas temporarias.
- Preferir funcoes simples e nomes claros caso o notebook evolua para scripts.
- Quando uma etapa gerar um dataset tratado, salvar explicitamente em
  `data/processed/` com nome descritivo.
- Para arquivos destinados ao Power BI, manter uma copia de trabalho
  (`df_powerbi`) separada de `df`, para nao perder tipos numericos na base
  analitica.
- Se novas dependencias forem necessarias, criar ou atualizar um arquivo de
  dependencias para tornar o ambiente reproduzivel.
- Antes de alterar arquivos existentes, preservar backup em `Backup/DD_MM_AAAA`,
  seguindo a regra operacional do projeto.

## Decisoes tecnicas observadas

- O projeto ainda esta em fase exploratoria e usa notebook como ponto central.
- Os dados brutos ficam em `data/raw/`.
- `data/processed/` armazena as saidas processadas atuais em CSV e XLSX.
- `reports/` existe como destino planejado para relatorios ou graficos.
- `df_powerbi` e usado como versao de apresentacao para dashboard, preservando
  `df` como base analitica.
- As colunas `float` sao convertidas para texto com virgula decimal antes da
  exportacao, para facilitar consumo visual em contexto pt-BR/Power BI.

## Pontos de atencao

- O notebook contem saida salva de uma execucao com erro
  `ModuleNotFoundError: No module named 'kagglehub'`, embora tambem haja saidas
  posteriores indicando execucao com `kagglehub` instalado.
- A etapa que move arquivos usa `Path.replace`, removendo os arquivos da pasta
  de origem do download em vez de copia-los.
- `subscription_status` e `addiction_severity` possuem a categoria textual
  `None` no CSV bruto, que o `pandas.read_csv` padrao interpreta como nulo.
- O valor `No subscription` permanece em ingles no arquivo processado porque o
  mapa de traducao cobre `None`, mas nao cobre `No subscription`.
- O valor `Witout addiction` permanece em ingles e contem provavel erro de
  digitacao. O mapa de traducao cobre `None`, mas nao cobre `Witout addiction`.
- A conversao de colunas `float` para texto com virgula decimal facilita a
  exibicao, mas pode exigir conversao de tipo no Power BI caso seja necessario
  agregar essas colunas como numeros.
- Nao ha testes automatizados configurados.
- Nao ha manifesto de dependencias, o que dificulta reproduzir o ambiente.
