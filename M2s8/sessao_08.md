Análise de Dados em R (FNDE) - Módulo 2
Sessão 8 - Acessando Bancos de Dados relacionais com dplyr e dbplyr
========================================================
author: Allan Vieira
date: julho de 2018
width: 1440
height: 900
<style>
.small-code pre code {
  font-size: 1em;
}
</style>
<!-- para trazer codigos em caixas menores: -->
<!-- https://stackoverflow.com/questions/25612385/code-box-size-and-font-size-in-rpres -->



Objetivos
========================================================
incremental: true

<br />
<br />

- Utilizar a fácil gramática de manipulação de dados fornecida por **dplyr** para acessar bancos de dados relacionais SQL sem precisarmos utilizar efetivamente comandos **SQL**.




Introdução (1)
========================================================
incremental: true
class: small-code


- Usar bancos de dados pe inevitável para aqueles cuja parte do trabalho é analisar dados;

- Como desenvolvedor de programas em **R**, o instinto nos leva a fazer um *approach* com as bases de dados do mesmo modo com o qual faríamos a leitura de um arquivo de dados .txt: 

  - nos tentaríamos ler os dados todos de uma vez ou partes dele o mínimo de vezes que fosse possível; 
  
  - o objetivo seria "voltar" ao banco de dados o mínimo possível, de modo que, para isso, nossas **queries** extrairiam o máximo de dados que fosse possível;
  
  - depois disso, nós passaríamos vários ciclos analisando aqueles dados salvos na memória de nosso computador;
  

Introdução (2)
========================================================
incremental: true
class: small-code


Em suma, seguiríamos mais ou menos o seguinte esquema:

![](./img/todaydb.png)

Fonte: Databases using R


Introdução (3)
========================================================
incremental: true
class: small-code

A abordagem anterior possui alguns problemas:

- o volume de dados com que teríamos que trabalhar seria muito grande:
  
  - passaríamos alguns momentos pensando em como minimizar o consumo de recursos e tempo para chegarmos ao subset dos dados que **realmente** precisamos para trabalhar;

- para economizar recursos, optaríamos por utilizar diretamente um cliente externo de SQL: 
  
  - trabalharíamos os dados o máximo possível **em SQL** para então extrairmos o que nos interessa e só depois utilizar o R;

- precisaríamos conhecer a fundo SQL para fazermos o máximo de consultas possível usando um "client" do MS SQL Server por exemplo: 
  
  - salvaríamos os **diferentes scripts de SQL** para que conseguíssemos repetir as consultas novamente;



Introdução (4)
========================================================
incremental: true
class: small-code

Qual seria então a melhor abordagem?


![Figura 1](./img/betterdb.png)

Fonte: Databases using R



Introdução (5)
========================================================
incremental: true
class: small-code

Com **dplyr**, a tarefa de acessar bancos de dados relacionais seria **EXTREMAMENTE** otimizada, porque:

- 1º) Você não precisa conhecer sintaxe de SQL para acessar os dados. Basta saber **R** e ter uma leve noção de SQL e você já poderá fazer "miséria" (literalmente);

- 2º) Você somente precisará do RStudio e não mais de um cliente externo de SQL para fazer as *queries*;

- 3º) Os códigos que você precisaria na primeira abordagem caírão pela metade com a segunda

- 4º) Ao invés de passar horas pensando em qual base de dados você realmente precisa importar, poderemos analisar os dados dentro do servidor SQL;

- 5º) Ao invés de usar memória do seu computador, você vai usar a *engine* do servidor SQL, porque **dplyr** vai mandar as *queries* para o servidor;

- 6) Manipular dados com comandos de **R** (mais ainda, do **dplyr**) é muito mais fácil do que manipular os dados com comandos de SQL; 
  - Então, você poderá investigar e manipular os dados de forma muito mais fácil apenas com R para **só ao final** salvar o resultado no seu computador 


Carregamento (1)
========================================================
incremental: true
class: small-code

Antes de começarmos é necessário que você instale e carregue os seguintes pacotes: DBI e dbplyr: 

  - **DBI** é um *backend* que permite ao **dplyr** se comunicar com vários tipos de bancos de dados SQL utilizando o mesmo código. 
  
  - ao instalarmos e carregarmos **dbplyr** automaticamente também o será o pacote **DBI**:


```r
#install.packages("dbplyr")
# library(dbplyr)
library(dplyr) # uma vez instalado o dbplyr, eh soh chamar dplyr
```

Além de **dbplyr** (e **DBI**), nós precisaremos de um **backend específico** para o tipo de servidor SQL que vamos acessar. Os mais comuns são:

  - **RMySQL** conecta a MySQL e MAriaDB;
  - **RPostgreSQL** conecta a Postgres e Redshift;
  - **RSQLite** incorpora uma base de SQLite embutida (muito útil para treinarmos);
  - **odbc** conecta a váriás bases de dados comerciais utilizando o *open conectivity protocol*;
  - **biquery** conecta ao BigQuery do Google.




Carregamento (2)
========================================================
incremental: true
class: small-code

- Para acessar os dados do FIES, utilizaremos o *backend odbc*, uma vez que o servidor "velhas" é um SQL Server (ou MS SQL Server). 

- O código com exemplos de conexão e *queries* contra essas bases de dados do FIES serão passados nos últimos slides da aula;

- Iniciaremos utilizando **RSQlite**, porque teremos que emular uma base de dados tipo SQL !!


Conectando a um database (1)
========================================================
incremental: true
class: small-code


Para podermos trabalhar com uma base de dados no **dplyr**, primeiro devemos estabelecer uma conexão com esta base, usando **DBI::dbConnect()**:


```r
# install.packages("RSQLite")
library(dplyr)
con <- DBI::dbConnect(RSQLite::SQLite(), path = ":memory:")
```

- O argumento *DBI::dbConnect()* pode variar de database para database, mas o primeiro argumento é sempre o *backend* do tipo de banco de dados ao qual você irá se conectar: 

  - **RSQLite::SQLite()** para RSQLite; 
  - **RMySQL::MySQL()** para RMySQL; 
  - **RPostgreSQL::PostgreSQL()** para RPostgreSQL; 
  - **odbc::odbc()** para odbc;
  - **bigrquery::bigquery()** para BigQuery. 
  
- SQLite somente precisa de mais um outro argumento: o caminho para a base de dados. No nosso caso, nós usamos a *string* especial **[:memory:]** que fará com que SQLite construa uma base de dados temporária na memória de nosso computador.


Conectando a um database (2)
========================================================
incremental: true
class: small-code

Com SQLite, estamos simulando um servidor em um arquivo...

- Contudo, a maioria das databases não "vivem" em um arquivo, mas sim em um servidor. 

- Isso significa que na vida real seu código seria mais parecido com:


```r
con <- DBI::dbConnect(RMySQL::MySQL(), 
  host = "database.rstudio.com",
  user = "hadley",
  password = rstudioapi::askForPassword("Database password")
)
```



Conectando a um database (3)
========================================================
incremental: true
class: small-code

**Voltando ...**

- A base de dados temporária que criamos não possui qualquer tabela de dados ainda;

- Vamos, então, copiar o *tibble* **nycflights13::flights**, usando a função **copy_to()**. 

- Embora esta não seja a maneira mais indicada de colocar dados em uma database, é bastante útil e fácil para utilizarmos em demonstrações:


```r
copy_to(con, nycflights13::flights, "flights",
  temporary = FALSE, 
  indexes = list(
    c("year", "month", "day"), 
    "carrier", 
    "tailnum",
    "dest"
  )
)
```

***

- A função **copy_to()** possui alguns argumentos adicionais que nos permite fornecer índices para a tabela. 


<!-- NO FIES VCS NAO VAO UTILIZAR COPY_TO -->

- Nós criamos índices que nos permitirão rapidamente processar os dados por **day**, por **carrier**, por **plane** e por **destination**; 

- Criando os índices de escrita é um ponto chave para uma boa performance da base de dados ao enviarmos **queries**. No entanto, está fora do escopo deste curso.


Conectando a um database (3)
========================================================
incremental: true
class: small-code

Umavez que copiamos os dados para o servidor, podemos referenciar (ainda não estamos importando) essa tabela no **R** usando a função **tbl()**:

```r
flights_db <- tbl(con, "flights")
```

Se imprimirmos a referência recém criada, veremos que ela se parece com um *tibble*:

```r
flights_db 
```

```
# Source:   table<flights> [?? x 19]
# Database: sqlite 3.22.0 []
    year month   day dep_time sched_dep_time dep_delay arr_time
   <int> <int> <int>    <int>          <int>     <dbl>    <int>
 1  2013     1     1      517            515         2      830
 2  2013     1     1      533            529         4      850
 3  2013     1     1      542            540         2      923
 4  2013     1     1      544            545        -1     1004
 5  2013     1     1      554            600        -6      812
 6  2013     1     1      554            558        -4      740
 7  2013     1     1      555            600        -5      913
 8  2013     1     1      557            600        -3      709
 9  2013     1     1      557            600        -3      838
10  2013     1     1      558            600        -2      753
# ... with more rows, and 12 more variables: sched_arr_time <int>,
#   arr_delay <dbl>, carrier <chr>, flight <int>, tailnum <chr>,
#   origin <chr>, dest <chr>, air_time <dbl>, distance <dbl>, hour <dbl>,
#   minute <dbl>, time_hour <dbl>
```

A única diferença é a referência de que os dados estão em um banco de dados SQLite.


Gerando queries (1)
========================================================
incremental: true
class: small-code

- Para interagir com um banco de dados nós geralmente usamos *SQL* - *Structured Query Language*; 

- SQL tem mais de 40 anos e é usado em praticamente todas as bases de dados que existem; 

- O objetivo de **dbplyr** é automaticamente gerar códigos em *SQL* para que nśo não sejamos forçados a utilizá-los **: )**; 

- No entanto, **dbplyr** não faz tudo que uma linguagem SQL faz; 

  - Ele foca na declarativa **SELECT** e derivadas, o que já é muita coisa para objetivo desse curso e para o que precisamos no FNDE.






Gerando queries (2)
========================================================
incremental: true
class: small-code

- Veja exemplos de como, na maioria das vezes, não precisamos saber nada de SQL e podemos continuar utilizando os verbos de **dplyr** com os quais já estamos familiarizados:


```r
flights_db %>% select(year:day, dep_delay, arr_delay)
```

```
# Source:   lazy query [?? x 5]
# Database: sqlite 3.22.0 []
    year month   day dep_delay arr_delay
   <int> <int> <int>     <dbl>     <dbl>
 1  2013     1     1         2        11
 2  2013     1     1         4        20
 3  2013     1     1         2        33
 4  2013     1     1        -1       -18
 5  2013     1     1        -6       -25
 6  2013     1     1        -4        12
 7  2013     1     1        -5        19
 8  2013     1     1        -3       -14
 9  2013     1     1        -3        -8
10  2013     1     1        -2         8
# ... with more rows
```

***


```r
flights_db %>% filter(dep_delay > 240)
```

```
# Source:   lazy query [?? x 19]
# Database: sqlite 3.22.0 []
    year month   day dep_time sched_dep_time dep_delay arr_time
   <int> <int> <int>    <int>          <int>     <dbl>    <int>
 1  2013     1     1      848           1835       853     1001
 2  2013     1     1     1815           1325       290     2120
 3  2013     1     1     1842           1422       260     1958
 4  2013     1     1     2115           1700       255     2330
 5  2013     1     1     2205           1720       285       46
 6  2013     1     1     2343           1724       379      314
 7  2013     1     2     1332            904       268     1616
 8  2013     1     2     1412            838       334     1710
 9  2013     1     2     1607           1030       337     2003
10  2013     1     2     2131           1512       379     2340
# ... with more rows, and 12 more variables: sched_arr_time <int>,
#   arr_delay <dbl>, carrier <chr>, flight <int>, tailnum <chr>,
#   origin <chr>, dest <chr>, air_time <dbl>, distance <dbl>, hour <dbl>,
#   minute <dbl>, time_hour <dbl>
```

```r
flights_db %>% 
  group_by(dest) %>%
  summarise(delay = mean(dep_time))
```

```
# Source:   lazy query [?? x 2]
# Database: sqlite 3.22.0 []
   dest  delay
   <chr> <dbl>
 1 ABQ   2006.
 2 ACK   1033.
 3 ALB   1627.
 4 ANC   1635.
 5 ATL   1293.
 6 AUS   1521.
 7 AVL   1175.
 8 BDL   1490.
 9 BGR   1690.
10 BHM   1944.
# ... with more rows
```




Gerando queries (3)
========================================================
incremental: true
class: small-code


- A diferença mais importante entre data.frames comuns e *queries* a bancos de dados remotos é que nosso código **R** é traduzido em *SQL* e **executado no database**; não no **R**!!

- Quando trabalhamos com *databases* o **dplyr** tenta ser o mais *lazy* (do conceito de *lazy evaluation*) quanto for possível:

- Ele nunca traz dados para o **R** a não ser que explicitamente solicitamos que ele faça isso;

- Ele "atrasa" fazer qualquer tarefa até o último momento: 

  - ele coleta todos comandos e manda para o banco de dados em um único passo.

Veja o exemplo a seguir:

```r
tailnum_delay_db <- flights_db %>% 
  group_by(tailnum) %>%
  summarise(
    delay = mean(arr_delay),
    n = n()
  ) %>% 
  arrange(desc(delay)) %>%
  filter(n > 100)
```


Gerando queries (4)
========================================================
incremental: true
class: small-code


- Todo o código do exemplo anterior não chega a **"tocar"** a base de dados em um momento sequer; não até que solicitemos, fazendo um **printing** de tailnum_delay por exemplo; 

- Só, então, é que **dplyr** gera o código *SQL* e solicita os resultados do database;

- Ainda sim, ele tenta minimizar o que será impresso, trazendo apenas algumas linhas e não tudo:



***


```r
tailnum_delay_db
```

```
# Source:     lazy query [?? x 3]
# Database:   sqlite 3.22.0 []
# Ordered by: desc(delay)
   tailnum delay     n
   <chr>   <dbl> <int>
 1 N11119   30.3   148
 2 N16919   29.9   251
 3 N14998   27.9   230
 4 N15910   27.6   280
 5 N13123   26.0   121
 6 N11192   25.9   154
 7 N14950   25.3   219
 8 N21130   25.0   126
 9 N24128   24.9   129
10 N22971   24.7   230
# ... with more rows
```

- Por de trás das câmeras, **dplyr** está traduzindo o código em **R** para código *SQL*. Se você quiser ver (e aprender) o código SQL que está sendo enviado ao servidor, use **show_query()**:


```r
# EXECUTAR NO CONSOLE
tailnum_delay_db %>% show_query()
```


Gerando queries (5)
========================================================
incremental: true
class: small-code


- Mesmo com **dplyr** (**dbplyr**), nós ainda vamos fazer algumas iterações e tentativas até descobrir o que realmente vamos precisar dos dados, mas será de forma muito mais rápida;

- Uma vez que soubermos, podemos usar **collect()** para trazer todos os dados em um *tibble* (local) em nossa máquina:


```r
tailnum_delay <- tailnum_delay_db %>% collect()
tailnum_delay
```

```
# A tibble: 1,201 x 3
   tailnum delay     n
   <chr>   <dbl> <int>
 1 N11119   30.3   148
 2 N16919   29.9   251
 3 N14998   27.9   230
 4 N15910   27.6   280
 5 N13123   26.0   121
 6 N11192   25.9   154
 7 N14950   25.3   219
 8 N21130   25.0   126
 9 N24128   24.9   129
10 N22971   24.7   230
# ... with 1,191 more rows
```

- **collect()** precisa que o banco de dados trabalhe e, por isso, a operação pode tomar algum tempo até ser completada. 


Gerando queries (6)
========================================================
incremental: true
class: small-code

- **dplyr** tenta evitar que você acidentalmente faça *queries* bem custosas computacionalmente:

  - Geralmente não há forma de determinar quantas linhas uma *query* vai retornar até que realmente rodemos o comando desejado 
    
    - não podemos usar **nrow()**;  ele sempre retornará NA;

    - como não podemos encontrar as poucas últimas linhas sem executar a *query* de todo os dados, não podemos usar **tail()** também.


```r
nrow(tailnum_delay_db)
```

```
[1] NA
```

```r
tail(tailnum_delay_db)
```

```
Error: tail() is not supported by sql sources
```


Códigos para acessar servidor velhas do FIES - conexão (1)
========================================================
incremental: true
class: small-code



```r
# install.packages("odbc")
library(dplyr)

con <- DBI::dbConnect(odbc::odbc(), 
                      .connection_string = "Driver={SQL Server};",
                      server="velhas")
```


- Note que é necessário o argumento **.connection_string** nas bases **odbc** para que o **R** utilize o driver específico do MS SQL SERVER.

Códigos para acessar servidor velhas do FIES - conexão (2)
========================================================
incremental: true
class: small-code


![](./img/print_connections.png)



Códigos para acessar servidor velhas do FIES - query
========================================================
incremental: true
class: small-code



```r
# listar as tabelas que existem no servidor:
DBI::dbListTables(con)

# consultar tabelas esecificas
tbl(con, dbplyr::in_schema('[2017_10]', '[dbo].[sisfies_consolidadas_fiadores]'))

tbl(con, dbplyr::in_schema('[2017_02]', '[dbo].[BB_TabRemuneracaoContratosFies]'))

# exemplo de coleta das info:
library(dplyr)

# referenciar as tabelas, filtrar e coletar
atraso_db <- tbl(con, dbplyr::in_schema('[2017_02]', '[dbo].[BB_TabRemuneracaoContratosFies]')) %>%
  filter(Nr_Dias_Atr > 360 ) %>%
  select(CPF)

atraso_df <- atraso_db %>%
  collect()

atraso_df
```


FIM
========================================================
incremental: false
class: small-code

OBRIGADO!!
