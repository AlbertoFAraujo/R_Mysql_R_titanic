![image](https://github.com/AlbertoFAraujo/R_Mysql_R_titanic/assets/105552990/29a4700a-c75e-4a65-9a1b-2d4d1c1c223f)

### Tecnologias utilizadas: 
| [<img align="center" alt="R_studio" height="60" width="60" src="https://github.com/AlbertoFAraujo/R_Petrobras/assets/105552990/02dff6df-07be-43dc-8b35-21d06eabf9e1">](https://posit.co/download/rstudio-desktop/) | [<img align="center" alt="ggplot" height="60" width="60" src="https://github.com/AlbertoFAraujo/R_Petrobras/assets/105552990/db55b001-0d4c-42eb-beb2-5131151c7114">](https://plotly.com/r/) | [<img align="center" alt="rmysql" height="60" width="60" src="https://github.com/AlbertoFAraujo/R_Mysql_R_titanic/assets/105552990/60b413a0-0402-4de5-91a9-324256197ae4">](https://plotly.com/r/) | [<img align="center" alt="dplyr" height="60" width="60" src="https://github.com/AlbertoFAraujo/R_Mysql_R_titanic/assets/105552990/7c3b4249-2c25-4657-8f48-defe42f2fc95">](https://www.rdocumentation.org/packages/tidyverse/versions/2.0.0) |
|:---:|:---:|:---:|:---:|
| R Studio | Ggplot2 | RmySQL | Dplyr |

- **RStudio:** Ambiente integrado para desenvolvimento em R, oferecendo ferramentas para escrita, execução e depuração de código.
- **Ggplot2:** Pacote para criação de visualizações de dados elegantes e flexíveis em R.
- **RMySQL:** Pacote para integração do R com o MySQL, permitindo a execução de consultas e operações de manipulação de dados diretamente no banco de dados MySQL.
- **Dplyr:** Pacote para manipulação eficiente de dados em R, com funções para filtragem, seleção, agrupamento e outras operações, independentemente do tipo de banco de dados utilizado.
<hr>

### Objetivo: 

Realizar a análise de cliente pelo método RFM :
- **Recência:** quão recente um cliente fez a compra;
- **Frequência:** com que frequência um cliente faz a compra;
- **Valor Monetário:** quanto dinheiro um cliente gasta em compras.

De acordo com essas métricas, é possível segmentar os clientes em grupos para entender quais deles compram muitas coisas com frequência, que compram poucas coisas, mas frequentemente, e que não compram nada há muito tempo.

Base de dados: https://archive.ics.uci.edu/dataset/502/online+retail+ii
<hr>

### Script R: 

**Sumário das variáveis:**
| Variável  | Descrição                                                                                      |
|-----------|------------------------------------------------------------------------------------------------|
| pclass    | Tipo de classe de passagem (Do 1 ao 3), sendo 1 a melhor classe e 3 a pior classe.            |
| survived  | Indica se o passageiro sobreviveu ao naufrágio (1) ou não (0).                                 |
| name      | Nome do passageiro.                                                                            |
| sex       | Gênero do passageiro, masculino ou feminino.                                                  |
| age       | Idade do passageiro na data da ocorrência do naufrágio.                                        |
| sibsp     | Número de irmãos / cônjuges a bordo.                                                          |
| parch     | Número de pais / filhos a bordo.                                                              |
| ticket    | Código do ticket.                                                                              |
| fare      | Valor da passagem.                                                                             |
| cabin     | Código de identificação da Cabine.                                                             |
| embarked  | Local onde o passageiro embarcou no navio.                                                     |
<hr>


```r
# 1.  Qual o percentual de sobreviventes? E de não sobreviventes?
query1 <- 
"
SELECT 
	survived,
	COUNT(*) AS 'Total',
    COUNT(*)/SUM(COUNT(*))OVER()AS 'Percentual'
FROM titanicdb.titanic
WHERE pclass <> ''
GROUP BY survived;
"
# Enviar consulta ao banco de dados
resultado_query1 <- RMySQL::dbSendQuery(conexao, query1)

# Buscar resultados na consulta
resultado1 <- RMySQL::dbFetch(resultado_query1)

# Visualizar o resultado da consulta
resultado1
```

| survived | Total | Percentual |
|----------|-------|------------|
| 0        | 808   | 61.87%     |
| 1        | 498   | 38.13%     |

Nota-se que o número de sobreviventes foi equivalente a 61,87% do total (1306) e de não sobreviventes foi de 38,13%.

<hr>

```r
# 2.Qual o percentual de sobreviventes por gênero? E de não sobreviventes?
query2 <- 
"
SELECT
	survived, 
    sex,
    COUNT(*) AS 'Total',
    COUNT(*)/SUM(COUNT(*))OVER(partition by survived)AS 'Percentual'
FROM titanicdb.titanic
WHERE survived <> ''
GROUP BY survived, sex
ORDER BY Total DESC
"
# Enviar consulta ao banco de dados
resultado_query2 <- RMySQL::dbSendQuery(conexao, query2)

# Buscar resultados na consulta
resultado2 <- RMySQL::dbFetch(resultado_query2)

# Visualizar o resultado da consulta
resultado2
```
| survived | sex    | Total | Percentual |
|----------|--------|-------|------------|
| 0        | male   | 682   | 84.41%     |
| 1        | female | 338   | 67.87%     |
| 1        | male   | 160   | 32.13%     |
| 0        | female | 126   | 15.59%     |

**Para os sobreviventes:**
- 84,41% são do gênero masculino e 15,59% são do gênero feminino
**Para os não sobreviventes:**
- 67,87% são do gênero masculino e 32,13% são do gênero feminino.

<hr>


```r
# 3.  Qual faixa de idade apresentou maior percentual de sobrevivência?
query3 <- "SELECT
          	DISTINCT(Age)
          FROM titanicdb.titanic
          ORDER BY Age DESC"

# Enviar consulta ao banco de dados
resultado_query3 <- RMySQL::dbSendQuery(conexao, query3)

# Buscar resultados na consulta
resultado3 <- RMySQL::dbFetch(resultado_query3)

# Visualizar o resultado da consulta
plot(resultado3)
```

