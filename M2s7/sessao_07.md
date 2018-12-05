Análise de Dados em R (FNDE) - Módulo 2
Sessão 7 - TWO TABLE verbs com dplyr
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

- Conhecer e aprender como utilizar os Two Table verbs do pacote **dplyr**




Introdução (1)
========================================================
incremental: true
class: small-code


- A Análise de dados, na maioria das vezes, envolve mais de uma base de dados;

- Na prática, normalmente teremos diversas bases de dados que contribuem para a análise e precisamos de ferramentas flexiveis para combiná-las;

- No pacote **dplyr**, existem três famílias de verbos que trabalham com duas tabelas de uma vez.


  - **mutating joins**: adicionam novas variáveis a uma tabela a partir da correspondência (*matching*) das linhas em outra;
  
  - **filtering joins**: filtram observações de uma tabela se estas observações fazem o *match* um observação em outra tabela;
  
  - **set oeprations**: combinam observações nos datasets caso eles sejam elementos do conjunto passado.



Pacote dplyr - Introdução (2)
========================================================
incremental: true
class: small-code


- Esses verbos assumem que seus dados encontram-se no formto de *tidy data*, ou seja, linhas são observações e colunas são variáveis.

- Todos os *two-table verbs* funcionam de forma similar: os primeiros dois argumentos são *x* e *y* e fornecem as duas tabelas que desejamos comparar e combinar. 

- O output será semore uma nova tabela com o mesmo tipo de *x*.


Mutating joins
========================================================
incremental: true
class: small-code

- **Mutating joins** nos permitem combinar variáveis de múltiplas tabelas; 

- Vamos dividir o data.frame flights em dois e depois tentaremos juntar os dois com base no nome da cia aérea:


```r
library(dplyr)
library("nycflights13")
# Drop unimportant variables so it's easier to understand the join results.
flights2 <- flights %>% select(year:day, hour, origin, dest, tailnum, carrier)

flights2 %>% 
  left_join(airlines)
```

```
# A tibble: 336,776 x 9
    year month   day  hour origin dest  tailnum carrier name              
   <int> <int> <int> <dbl> <chr>  <chr> <chr>   <chr>   <chr>             
 1  2013     1     1     5 EWR    IAH   N14228  UA      United Air Lines …
 2  2013     1     1     5 LGA    IAH   N24211  UA      United Air Lines …
 3  2013     1     1     5 JFK    MIA   N619AA  AA      American Airlines…
 4  2013     1     1     5 JFK    BQN   N804JB  B6      JetBlue Airways   
 5  2013     1     1     6 LGA    ATL   N668DN  DL      Delta Air Lines I…
 6  2013     1     1     5 EWR    ORD   N39463  UA      United Air Lines …
 7  2013     1     1     6 EWR    FLL   N516JB  B6      JetBlue Airways   
 8  2013     1     1     6 LGA    IAD   N829AS  EV      ExpressJet Airlin…
 9  2013     1     1     6 JFK    MCO   N593JB  B6      JetBlue Airways   
10  2013     1     1     6 LGA    ORD   N3ALAA  AA      American Airlines…
# ... with 336,766 more rows
```



Mutating join - controlando como as tabelas sofrem *match* (1)
========================================================
incremental: true
class: small-code



- Além dos argumentos **x** e **y**, cada *mutating join* recebe também um argumento *by* utilizado como índice para fazer o *match* entre as tabelas;

- Controlar o **match** é controlar o parâmetro **by**!

- Vejamos as maneiras de como especificar o parâmetro *by*:

  - **NULL** é o *default*: **dplyr** vai usar todas as variáveis comuns às duas tabelas(**natural join**)
  
  

```r
flights2 %>% left_join(weather)
```

```
# A tibble: 336,776 x 18
    year month   day  hour origin dest  tailnum carrier  temp  dewp humid
   <dbl> <dbl> <int> <dbl> <chr>  <chr> <chr>   <chr>   <dbl> <dbl> <dbl>
 1  2013     1     1     5 EWR    IAH   N14228  UA       39.0  28.0  64.4
 2  2013     1     1     5 LGA    IAH   N24211  UA       39.9  25.0  54.8
 3  2013     1     1     5 JFK    MIA   N619AA  AA       39.0  27.0  61.6
 4  2013     1     1     5 JFK    BQN   N804JB  B6       39.0  27.0  61.6
 5  2013     1     1     6 LGA    ATL   N668DN  DL       39.9  25.0  54.8
 6  2013     1     1     5 EWR    ORD   N39463  UA       39.0  28.0  64.4
 7  2013     1     1     6 EWR    FLL   N516JB  B6       37.9  28.0  67.2
 8  2013     1     1     6 LGA    IAD   N829AS  EV       39.9  25.0  54.8
 9  2013     1     1     6 JFK    MCO   N593JB  B6       37.9  27.0  64.3
10  2013     1     1     6 LGA    ORD   N3ALAA  AA       39.9  25.0  54.8
# ... with 336,766 more rows, and 7 more variables: wind_dir <dbl>,
#   wind_speed <dbl>, wind_gust <dbl>, precip <dbl>, pressure <dbl>,
#   visib <dbl>, time_hour <dttm>
```


As tabelas *flights* e *wheathers* foram "juntadas" com base nas variáveis comuns: *year, month, day, hour e origin*.



Mutating join - controlando como as tabelas sofrem *match* (2)
========================================================
incremental: true
class: small-code

- Um vetor de caracteres, **by = "x"**:
  
  - opera como se fosse um *natural join*, mas usa somente algumas das variáveis comuns. 
  
  <!-- Por exemplo, *flights* e *planes* possuem uma variável *year*, mas elas significam coisas diferentes em cada data.frame. Então, queremos especificar uma coluna que sabemos que significa a mesma coisa em ambos os data.frames e que possa servir de índice para o *matching*. Vamos usar *tailnum* que é o número do avião. -->


```r
flights2 %>% left_join(planes, by = "tailnum")
```

```
# A tibble: 336,776 x 16
   year.x month   day  hour origin dest  tailnum carrier year.y type 
    <int> <int> <int> <dbl> <chr>  <chr> <chr>   <chr>    <int> <chr>
 1   2013     1     1     5 EWR    IAH   N14228  UA        1999 Fixe…
 2   2013     1     1     5 LGA    IAH   N24211  UA        1998 Fixe…
 3   2013     1     1     5 JFK    MIA   N619AA  AA        1990 Fixe…
 4   2013     1     1     5 JFK    BQN   N804JB  B6        2012 Fixe…
 5   2013     1     1     6 LGA    ATL   N668DN  DL        1991 Fixe…
 6   2013     1     1     5 EWR    ORD   N39463  UA        2012 Fixe…
 7   2013     1     1     6 EWR    FLL   N516JB  B6        2000 Fixe…
 8   2013     1     1     6 LGA    IAD   N829AS  EV        1998 Fixe…
 9   2013     1     1     6 JFK    MCO   N593JB  B6        2004 Fixe…
10   2013     1     1     6 LGA    ORD   N3ALAA  AA          NA <NA> 
# ... with 336,766 more rows, and 6 more variables: manufacturer <chr>,
#   model <chr>, engines <int>, seats <int>, speed <int>, engine <chr>
```

Note que ao juntar todas as colunas de ambos os data.frames, **dplyr** acrescenta um sufixo à segunda variável *year* para diferenciar de qual tabela cada uma veio


Mutating join - controlando como as tabelas sofrem *match* (3)
========================================================
incremental: true
class: small-code

- um vetor de caracteres com nomes: **by = c("x" = "y")**. 

  - Isto vai fazer o *match* da variável *x* na tabela *a* com a variável *y* na tabela *b*; 
  - As variáveis da tabela de origem serão usadas no output.

Cada voo tem uma origem e um aeroporto de destino. Então precisamos especificar qual dessas variáveis do dataset *flight* queremos fazer o *match* com a coluna *faa* do dataset *airports*.


```r
# ??airports
airports
```

```
# A tibble: 1,458 x 8
   faa   name                   lat    lon   alt    tz dst   tzone        
   <chr> <chr>                <dbl>  <dbl> <int> <dbl> <chr> <chr>        
 1 04G   Lansdowne Airport     41.1  -80.6  1044    -5 A     America/New_…
 2 06A   Moton Field Municip…  32.5  -85.7   264    -6 A     America/Chic…
 3 06C   Schaumburg Regional   42.0  -88.1   801    -6 A     America/Chic…
 4 06N   Randall Airport       41.4  -74.4   523    -5 A     America/New_…
 5 09J   Jekyll Island Airpo…  31.1  -81.4    11    -5 A     America/New_…
 6 0A9   Elizabethton Munici…  36.4  -82.2  1593    -5 A     America/New_…
 7 0G6   Williams County Air…  41.5  -84.5   730    -5 A     America/New_…
 8 0G7   Finger Lakes Region…  42.9  -76.8   492    -5 A     America/New_…
 9 0P2   Shoestring Aviation…  39.8  -76.6  1000    -5 U     America/New_…
10 0S9   Jefferson County In…  48.1 -123.    108    -8 A     America/Los_…
# ... with 1,448 more rows
```


***


```r
# ??flights
flights2 %>% left_join(airports, c("dest" = "faa"))
```

```
# A tibble: 336,776 x 15
    year month   day  hour origin dest  tailnum carrier name    lat   lon
   <int> <int> <int> <dbl> <chr>  <chr> <chr>   <chr>   <chr> <dbl> <dbl>
 1  2013     1     1     5 EWR    IAH   N14228  UA      Geor…  30.0 -95.3
 2  2013     1     1     5 LGA    IAH   N24211  UA      Geor…  30.0 -95.3
 3  2013     1     1     5 JFK    MIA   N619AA  AA      Miam…  25.8 -80.3
 4  2013     1     1     5 JFK    BQN   N804JB  B6      <NA>   NA    NA  
 5  2013     1     1     6 LGA    ATL   N668DN  DL      Hart…  33.6 -84.4
 6  2013     1     1     5 EWR    ORD   N39463  UA      Chic…  42.0 -87.9
 7  2013     1     1     6 EWR    FLL   N516JB  B6      Fort…  26.1 -80.2
 8  2013     1     1     6 LGA    IAD   N829AS  EV      Wash…  38.9 -77.5
 9  2013     1     1     6 JFK    MCO   N593JB  B6      Orla…  28.4 -81.3
10  2013     1     1     6 LGA    ORD   N3ALAA  AA      Chic…  42.0 -87.9
# ... with 336,766 more rows, and 4 more variables: alt <int>, tz <dbl>,
#   dst <chr>, tzone <chr>
```


```r
flights2 %>% left_join(airports, c("origin" = "faa"))
```

```
# A tibble: 336,776 x 15
    year month   day  hour origin dest  tailnum carrier name    lat   lon
   <int> <int> <int> <dbl> <chr>  <chr> <chr>   <chr>   <chr> <dbl> <dbl>
 1  2013     1     1     5 EWR    IAH   N14228  UA      Newa…  40.7 -74.2
 2  2013     1     1     5 LGA    IAH   N24211  UA      La G…  40.8 -73.9
 3  2013     1     1     5 JFK    MIA   N619AA  AA      John…  40.6 -73.8
 4  2013     1     1     5 JFK    BQN   N804JB  B6      John…  40.6 -73.8
 5  2013     1     1     6 LGA    ATL   N668DN  DL      La G…  40.8 -73.9
 6  2013     1     1     5 EWR    ORD   N39463  UA      Newa…  40.7 -74.2
 7  2013     1     1     6 EWR    FLL   N516JB  B6      Newa…  40.7 -74.2
 8  2013     1     1     6 LGA    IAD   N829AS  EV      La G…  40.8 -73.9
 9  2013     1     1     6 JFK    MCO   N593JB  B6      John…  40.6 -73.8
10  2013     1     1     6 LGA    ORD   N3ALAA  AA      La G…  40.8 -73.9
# ... with 336,766 more rows, and 4 more variables: alt <int>, tz <dbl>,
#   dst <chr>, tzone <chr>
```


Tipos de join (1)
========================================================
incremental: true
class: small-code

- Há 4 tipos de *mutating join*, que diferem pelo comportamento quando **NÃO** ocorre o *match*.

Vamos criar dois data.frames e depois veremos exemplos de cada caso.

```r
library(dplyr)
(df1 <- data_frame(x = c(1, 2), y = 2:1))
```

```
# A tibble: 2 x 2
      x     y
  <dbl> <int>
1     1     2
2     2     1
```

```r
# note que a função dplyr::data_frame é diferente da função data.frame do R base

(df2 <- data_frame(x = c(1, 3), a = 10, b = "a"))
```

```
# A tibble: 2 x 3
      x     a b    
  <dbl> <dbl> <chr>
1     1    10 a    
2     3    10 a    
```

***

- **inner_join(x, y)**: inclui somente observações que possuem correspondência tanto em *x* quanto em *y* (ou seja linhas iguais nos data.frames).


```r
df1 %>% inner_join(df2)
```

```
# A tibble: 1 x 4
      x     y     a b    
  <dbl> <int> <dbl> <chr>
1     1     2    10 a    
```

- Note que o "by" foi o *default* e utilizou a coluna "x" como índice para juntar os dois data frames;

- As linhas iguais para a variável "x" em ambos os dataframes são trazidas na íntegra (ou seja, traz-se todas as colunas).


Tipos de join (2)
========================================================
incremental: true
class: small-code

- **left_join(x, y)**: inclui todas as observações em  "x", independente de haver *match* ou não;

- Esse é o tipo de *join* mais usado, porque ele garante que nós não perderemos nenhuma informação da nossa tabela primária.


```r
df1 %>% left_join(df2)
```

```
# A tibble: 2 x 4
      x     y     a b    
  <dbl> <int> <dbl> <chr>
1     1     2    10 a    
2     2     1    NA <NA> 
```


Tipos de join (3)
========================================================
incremental: true
class: small-code

- **right_join(x, y)**: inclui todas as observações em "y";

- É equivalente a *left_join(**y**, **x**)*, mas a ordenação das variáveis será diferente neste último caso:


```r
df1 %>% right_join(df2)
```

```
# A tibble: 2 x 4
      x     y     a b    
  <dbl> <int> <dbl> <chr>
1     1     2    10 a    
2     3    NA    10 a    
```

```r
df2 %>% left_join(df1)
```

```
# A tibble: 2 x 4
      x     a b         y
  <dbl> <dbl> <chr> <int>
1     1    10 a         2
2     3    10 a        NA
```



Tipos de join (4)
========================================================
incremental: true
class: small-code

- **full_join()**: inclui todas as observações de x e y:


```r
df1 %>% full_join(df2)
```

```
# A tibble: 3 x 4
      x     y     a b    
  <dbl> <int> <dbl> <chr>
1     1     2    10 a    
2     2     1    NA <NA> 
3     3    NA    10 a    
```


- Os *left, right e full joins* são conhecidos cletivamente como **outer joins** (ou joins externos):
  
  - Quando uma linha não faz o match em um **outer join**, as novas variáveis são preenchidas com *missing values*.


Tipos de join (5)
========================================================
incremental: true
class: small-code

- Embora os *mutating joins* existam para adicionar novas variáveis, em alguns casos eles podem gerar novas observações:
  
  - se uma correspondência não é única, um *join* vai acrescentar linhas para todas as combinações possíveis (produto cartesiano) do *matching* das observações:


```r
df1 <- data_frame(x = c(1, 1, 2), y = 1:3)
df2 <- data_frame(x = c(1, 1, 2), z = c("a", "b", "a"))

df1 %>% left_join(df2)
```

```
# A tibble: 5 x 3
      x     y z    
  <dbl> <int> <chr>
1     1     1 a    
2     1     1 b    
3     1     2 a    
4     1     2 b    
5     2     3 a    
```


Tipos de join - resumo
========================================================
incremental: true
class: small-code

**Resumindo ...**

![](./img/combine_cheatsheet.png)



Filtering joins (1)
========================================================
incremental: true
class: small-code


- **Filtering joins** "casam" observações da mesma forma que os *mutating joins*, mas afetam as próprias observações (**linhas**) e não as variáveis (**colunas**);

- Existem dois tipos de *filtering joins*:

  - **semi_join() MANTÉM** todas as observações em "x" que possuem *match* em "y";
  - **anti_join() RETIRA** todas as observações em "x"que tem *match* em "y".

- Esses joins são muito úteis para identificar "descasamentos" entre tabelas:

  - por exemplo, há diversos voos no dataset *flights* que não possuem correspondências com base no *tailnum* no dataset *planes*:


```r
library("nycflights13")
flights %>% 
  anti_join(planes, by = "tailnum") %>% 
  count(tailnum, sort = TRUE)
```

```
# A tibble: 722 x 2
   tailnum     n
   <chr>   <int>
 1 <NA>     2512
 2 N725MQ    575
 3 N722MQ    513
 4 N723MQ    507
 5 N713MQ    483
 6 N735MQ    396
 7 N0EGMQ    371
 8 N534MQ    364
 9 N542MQ    363
10 N531MQ    349
# ... with 712 more rows
```




Filtering joins (2)
========================================================
incremental: true
class: small-code

- Caso você esteja preocupado com quais observações nosso *join* vai fazer o *match*, sugere-se iniciar com um *semi_join()* ou *anti_join()*;

- Esses joins nunca duplicam as observações, eles somente removem:



```r
df1 <- data_frame(x = c(1, 1, 3, 4), y = 1:4)
df2 <- data_frame(x = c(1, 1, 2), z = c("a", "b", "a"))

# Four rows to start with:
df1 %>% nrow()
```

```
[1] 4
```

```r
# And we get four rows after the join
df1 %>% inner_join(df2, by = "x") %>% nrow()
```

```
[1] 4
```

```r
# But only two rows actually match
df1 %>% semi_join(df2, by = "x") %>% nrow()
```

```
[1] 2
```


Set operations (1)
========================================================
incremental: true
class: small-code


- O último tipo de verbo  *two-table* são as *set operations* (ou operações com conjuntos);

- Esses verbos/ funções esperam que os inputs "x" e "y" possuam as mesmas variáveis e tratam as observações como conjuntos:

  - **intersect(x, y)**: retorna somente observações que estejam tanto em "x" quanto em "y".
  
  - **union(x,y)**: retornam observações únicas em "x" e "y";
  
  - **setdiff(x,y)**: retornam observações em "x", mas não em "y".


Set operations (2)
========================================================
incremental: true
class: small-code


Considerando os dados a seguir:

```r
(df1 <- data_frame(x = 1:2, y = c(1L, 1L)))
```

```
# A tibble: 2 x 2
      x     y
  <int> <int>
1     1     1
2     2     1
```

```r
(df2 <- data_frame(x = 1:2, y = 1:2))
```

```
# A tibble: 2 x 2
      x     y
  <int> <int>
1     1     1
2     2     2
```

Há 4 possibilidades:


```r
intersect(df1, df2)
```

```
# A tibble: 1 x 2
      x     y
  <int> <int>
1     1     1
```

```r
# Note that we get 3 rows, not 4
```

***


```r
union(df1, df2)
```

```
# A tibble: 3 x 2
      x     y
  <int> <int>
1     2     2
2     2     1
3     1     1
```

```r
setdiff(df1, df2)
```

```
# A tibble: 1 x 2
      x     y
  <int> <int>
1     2     1
```

```r
setdiff(df2, df1)
```

```
# A tibble: 1 x 2
      x     y
  <int> <int>
1     2     2
```

- Por fim, cabe fazer menção a funções que seriam úteis caso você tivesse que trabalhar com 3 ou mais tabelas:  purrr::reduce() ou Reduce()




FIM
========================================================
incremental: false
class: small-code

OBRIGADO!!
