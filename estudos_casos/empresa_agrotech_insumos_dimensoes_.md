# Estudo de Caso - Análise Estratégica de Compras - Empresa AgroTech Insumos Agrícolas

Seguindo o estudo de Caso sobre a Análise Estratégica de Compras - Empresa AgroTech Insumos Agrícolas
 , a diretoria quer que algumas informações sejam incorporadas diretamente na tabela fato e que algumas análises sejam feitas a partir das dimensões.

Para isso, será necessário utilizar:

* 🔹RELATED()
    * RELATED() → quando você está em uma tabela do lado “muitos” e quer buscar algo do lado “1”
* 🔹RELATEDTABLE()
    * RELATEDTABLE() → quando você está em uma tabela do lado “1” e quer acessar várias linhas do lado “muitos”

## Etapa 1 - Usando RELATED()

A empresa quer facilitar análises futuras exportando a tabela fCompras já com informações descritivas.

Vamos criar colunas calculadas na tabela fCompras.

1. Trazer Estado do Fornecedor para fCompras

```DAX
Estado Fornecedor =
RELATED ( dFornecedores[Estado] )
```
2. Trazer Nome do Fornecedor

```DAX
Nome Fornecedor =
RELATED ( dFornecedores[Nome] )
```

3. Trazer Categoria do Produto

```DAX
Categoria Produto =
RELATED ( dProdutos[Categoria] )
```

A tabela fato ficará enriquecida com:

* Nome do fornecedor
* Estado
* Categoria

## Etapa 2

A diretoria quer saber:

* Quantas compras cada fornecedor realizou

* Qual o total financeiro por fornecedor

* Quantos produtos cada fornecedor forneceu

Agora estamos no lado 1 querendo acessar o lado muitos.

1. Quantidade de Compras por Fornecedor

```DAX
Qtd Compras =
COUNTROWS (
    RELATEDTABLE ( fCompras )
)
```
2. Total Comprado por Fornecedor

```DAX
Total Fornecedor =
SUMX (
    RELATEDTABLE ( fCompras ),
    fCompras[Valor]
)
```

3. Quantidade de Produtos Diferentes por Fornecedor

```DAX
Qtd Produtos Diferentes = 
COUNTROWS (
    SUMMARIZE (
        RELATEDTABLE ( fCompras ),
        fCompras[Codigo produto]
    )
)
```
* RELATEDTABLE → traz todas as compras do fornecedor

* SUMMARIZE → agrupa por Código do produto

* COUNTROWS → conta quantos produtos distintos existem

# Calculando Ano Fiscal

A empresa NutriFoods trabalha com:
* Ano Fiscal iniciando em JUlho
| Período             | Ano Fiscal |
| ------------------- | ---------- |
| Jul/2024 – Jun/2025 | 2025       |
| Jul/2025 – Jun/2026 | 2026       |

## Criando Ano Fiscal no dCalendario

Na tabela dCalendario, criar coluna:
```DAX
Ano Fiscal =
IF (
    MONTH ( dCalendario[Data] ) >= 7,
    YEAR ( dCalendario[Data] ) + 1,
    YEAR ( dCalendario[Data] )
)
```
## Criando Trimestre Fiscal

```DAX
Trimestre Fiscal =
SWITCH(
    TRUE(),
    MONTH(dCalendario[Data]) IN {7,8,9}, "T1",
    MONTH(dCalendario[Data]) IN {10,11,12}, "T2",
    MONTH(dCalendario[Data]) IN {1,2,3}, "T3",
    "T4"
)
```
* MONTH(dCalendario[Data]):Essa função extrai o número do mês da data. (15/07/2025 - RETORNA 7)

* Normalmente o SWITCH funciona assim:`SWITCH(expressão, valor1, resultado1, valor2, resultado2...)`
    * Usado: SWITCH(TRUE(), condição1, resultado1, condição2, resultado2...) - transforma o SWITCH em uma estrutura parecida com vários IF
    * Verificar cada condição e retornar o resultado da primeira que for verdadeira.

| Mês           | Trimestre Fiscal |
| ------------- | ---------------- |
| Julho (7)     | T1               |
| Agosto (8)    | T1               |
| Setembro (9)  | T1               |
| Outubro (10)  | T2               |
| Novembro (11) | T2               |
| Dezembro (12) | T2               |
| Janeiro (1)   | T3               |
| Fevereiro (2) | T3               |
| Março (3)     | T3               |
| Abril (4)     | T4               |
| Maio (5)      | T4               |
| Junho (6)     | T4               |



Para o ano fiscal poderíamos ter utilizado a função: `CALENDARAUTO(7)`, direto ao criar o `dCalendario, pois terminar o ano fiscal começa em julho e termina em junho.


| Situação      | Parâmetro     |
| -------- | ------------------ |
| Ano fiscal termina em Junho   | `CALENDARAUTO(6)`  |
| Ano fiscal termina em Julho     | `CALENDARAUTO(7)`  |
| Ano fiscal termina em Dezembro (ano civil) | `CALENDARAUTO(12)` |



