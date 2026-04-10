# Monitor de Transferências do Governo Federal

Projeto acadêmico de Business Intelligence com foco em transparência pública, construído a partir de dados abertos do Portal da Transparência do Governo Federal.

## Integrantes

- João Camilo Mallmann
- Matheus Augusto Lemes
- Henrique Kafer
- Leonardo Koelzer Mendes

## Objetivo

Mapear a destinação, a eficiência de liberação financeira e a situação legal dos convênios federais, com análises comparativas e visuais interativos no Power BI.

Este projeto responde à seguinte pergunta central:

> O recurso empenhado pela União está sendo efetivamente liberado e convertido em benefício para a população?

## Contexto e Justificativa

Mesmo com grande volume de dados públicos disponíveis, a interpretação prática dessas bases ainda é limitada sem tratamento técnico. Por isso, foi criado um pipeline ETL automatizado para coleta via API e uma modelagem analítica orientada a dashboards.

Base legal e institucional:

- Lei de Acesso à Informação (Lei no 12.527/2011)
- Portal da Transparência (dados abertos governamentais)

## Arquitetura de Dados

### 1) Extração automatizada via API

A coleta foi feita com Python diretamente no endpoint de convênios:

- Endpoint: /api-de-dados/convenios
- Período analisado: 01/01/2025 a 31/12/2025
- Regra técnica da API: máximo de 1 dia por requisição
- Estratégia: iteração diária com paginação automática

Principais recursos do script:

- Retry automático por dia (até 3 tentativas)
- Tratamento de códigos HTTP (200, 204, 400, 401/403, 429)
- Flatten recursivo do JSON com separador "\_\_"
- Exportação em CSV com separador ";" e codificação UTF-8-BOM

### 2) Estrutura da tabela fato

Arquivo gerado:

- Fato_Convenios_Lajeado_2025.csv

Granularidade:

- Cada linha representa um convênio individual

Categorias de variáveis:

- Identificação: id, dimConvenio\_\_numero, numeroProcesso
- Temporalidade: dataInicioVigencia, dataFinalVigencia, dataConclusao
- Geografia: municipioConvenente**codigoIBGE, municipioConvenente**nomeIBGE, municipioConvenente**uf**sigla
- Convenente: convenente**nome, convenente**tipo
- Financeiras: valor, valorLiberado, valorContrapartida
- Governança: situacao

## Tratamento e Modelagem no Power BI

### Power Query

- Tipagem de colunas financeiras para moeda/decimal fixo
- Ajuste regional para Português (Brasil)
- Padronização textual da coluna situacao (trim e maiúsculas)
- Criação da coluna temporal Ano Inicio Vigencia

### Medidas DAX

```dax
Total Convenios =
COUNTROWS('Fato_Convenios_Lajeado_2025')

Valor Total =
SUM('Fato_Convenios_Lajeado_2025'[valor])

Valor Liberado =
SUM('Fato_Convenios_Lajeado_2025'[valorLiberado])

Contrapartida Total =
SUM('Fato_Convenios_Lajeado_2025'[valorContrapartida])

Valor por Ano =
SUM('Fato_Convenios_Lajeado_2025'[valor])
```

## Estrutura dos Dashboards

### Dashboard 1: Visão Geral Nacional

- KPIs de volume total e execução financeira
- Pizza para status dos convênios
- Linha para evolução histórica
- Barras com maiores recebedores (Top N)

### Dashboard 2: Eficiência Local - Porto Alegre (RS)

- Rosca para comparação entre valor pactuado e valor liberado
- Indicadores de maturidade e regularidade dos projetos

### Dashboard 3: O Paradoxo da Liberação - João Pessoa (PB)

- Foco em megaprojetos e lentidão de liberação
- Contraste entre regularidade legal e liquidez real

### Dashboard 4: Indicadores Gerais

- KPIs de capilaridade territorial e institucional
- Área de evolução de liberação ao longo do tempo
- Matriz por ministério e conformidade
- Dispersão entre valor e prazo de execução

## Principais Conclusões

- Estar adimplente não garante liberação rápida de recursos.
- Há diferenças relevantes de execução entre capitais analisadas.
- Dashboards ajudam a identificar gargalos de governança e execução.

## Arquivos do Repositório

- Projeto.pbix
- Fato_Convenios_Lajeado_2025.csv
- Texto.text

## Vídeo do Projeto

- https://www.youtube.com/watch?v=5HyXJjnrL9U

## Como Executar o ETL (resumo)

1. Instalar dependências Python:

```bash
pip install requests pandas
```

2. Configurar sua chave de API no script:

- CHAVE_API = "SUA_CHAVE_AQUI"

3. Executar o script Python de extração.

4. Importar o CSV no Power BI e aplicar as medidas DAX.

## Referências

- Portal da Transparência do Governo Federal
- Lei no 12.527/2011 (Lei de Acesso à Informação)

---

Projeto desenvolvido para fins acadêmicos, com foco em transparência pública, governança de dados e análise de políticas públicas por meio de Business Intelligence.
