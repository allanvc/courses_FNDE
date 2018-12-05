Análise de Dados em R (FNDE) - Módulo 2
Sessão 6 - Manipulação de dados com dplyr
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
- conhecer a "gramática" específica de manipulação de dados trazida pelo dplyr;
<br />
- conhecer as principais tarefas que podem ser desempenhadas utilizando o pacote;
<br />
- aprender as principais funções do pacote




Pacote dplyr - Introdução (1)
========================================================
incremental: true
class: small-code


![](./img/dplyr.jpeg)

Conforme os autores asseveram, quando trabalhamos com dados nós precisamos:

- Descobrir o que desejamos fazer;

- Descrever essas tarefas na forma de um programa de computador;

- Executar o programa.


Pacote dplyr - Introdução (2)
========================================================
incremental: true
class: small-code

O pacote **dplyr** torna estes passos mais rápidos e fáceis de executar, pois:

- resume-se a poucas funções (!= R base);

- provém "verbos" simples $\rightarrow$ funções que correspondem às tarefas mais comuns de manipulação de dados $\rightarrow$  traduzir pensamentos em código;

- utiliza backends (códigos de final de processo, ou seja, mais próximos ao usuário) eficientes, de modo que gastamos menos tempo esperando pelo computador.


Pacote dplyr - Introdução (3)
========================================================
incremental: true
class: small-code

O pacote  **dplyr** proporciona uma função para cada "verbo" considerado importante em manipulação de dados:

- filter() para selecionar "casos" baseados em seus valores;

- arrange() para reordenar os "casos";

- select() e rename() para selecionar variáveis baseadas em seus nomes;

- mutate() e transmute() para adicionar novas variáveis que são funções de variáveis já existentes nos dados;

- summarise() ou summarize() para condensar multiplos valores em um único;

- sample_n() and sample_frac() para fazer amostras aleatórias.



Pacote dplyr - Exemplos (1)
========================================================
incremental: true
class: small-code

Vamos utilizar `nycflights13::flights` que contempla os dados de 336.776 voos que saíram de 3 aeroportos de Nova York em 2013. Os dados são oriundos do US Bureau of Transportation Statistics.


```r
library(dplyr)
library(nycflights13)
dim(flights)
```

```
[1] 336776     19
```

```r
flights
```

```
# A tibble: 336,776 x 19
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
# ... with 336,766 more rows, and 12 more variables: sched_arr_time <int>,
#   arr_delay <dbl>, carrier <chr>, flight <int>, tailnum <chr>,
#   origin <chr>, dest <chr>, air_time <dbl>, distance <dbl>, hour <dbl>,
#   minute <dbl>, time_hour <dttm>
```



Pacote dplyr - Exemplos (2)
========================================================
incremental: true
class: small-code

Note que:

- O *print* se dá de forma diferente do que estávamos acostumados até então;
- Isso porque na verdade temos um *tibble*, que é uma forma moderna de data.frame implementada pelo pessoal do **tidyverse**;
- Só é impresso na tela as primeiras linhas e diversos resumos/ informações sobre nossas variáveis. 
- Para saber mais sobre *tibbles*, você pode visitar *http://tibble.tidyverse.org*. Para converter data.frames em tibbles, usamos *as_tibble()*.


Filtrando linhas com filter()
========================================================
incremental: true
class: small-code

- `filter()` permite fazer um *subset* das linhas de um data.frame;

- Como todos os verbos simples de **dplyr**, o primeiro argumento será um *tibble* (ou data.frame);
- O segundo argumento e os subsequentes se referem a variáveis dentro do data.frame, em que se selecionam as linhas onde a expressão é verdadeira (TRUE).

- Vamos selecionar todos os voos em 1º de janeiro:


```r
filter(flights, month == 1, day == 1)
```

```
# A tibble: 842 x 19
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
# ... with 832 more rows, and 12 more variables: sched_arr_time <int>,
#   arr_delay <dbl>, carrier <chr>, flight <int>, tailnum <chr>,
#   origin <chr>, dest <chr>, air_time <dbl>, distance <dbl>, hour <dbl>,
#   minute <dbl>, time_hour <dttm>
```

- Isso seria equivalente ao código do base R que vimos no primeiro módulo: `flights[flights$month == 1 & flights$day == 1, ]`.


Organizando linhas com arrange()
========================================================
incremental: true
class: small-code

- `arrange()` funciona de modo semelhante a *filter*, mas ao invés de filtrar e selecionar linhas, ele apenas as reordena de acordo com alguma condição que passamos;

- Essa função recebe um data.frame (ou tibble) e um conjunto de *column names* pelo qual vai ordenar;

<!-- - Se você fornecer mais de um nome de coluna, cada coluna adicional passada será usada como critério de desempate. -->


```r
arrange(flights, year, month, day)
```

```
# A tibble: 336,776 x 19
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
# ... with 336,766 more rows, and 12 more variables: sched_arr_time <int>,
#   arr_delay <dbl>, carrier <chr>, flight <int>, tailnum <chr>,
#   origin <chr>, dest <chr>, air_time <dbl>, distance <dbl>, hour <dbl>,
#   minute <dbl>, time_hour <dttm>
```

Se quiser ordenar de forma decrescente, utilize a função `desc(nome_da_coluna)` dentro de `arrange()`.


Selecionando colunas com select() (1)
========================================================
incremental: true
class: small-code

- Geralmente trabalhamos com *datasets* com muitas colunas, mas somente algumas poucas são de nosso interesse;

- `select()` nos permite rapidamente focar num subconjunto dos dados;

- O melhor é que podemos utilizar operações - que normalmente só funcionam com as posições das colunas - direto nos nomes das variáveis.


```r
# Select columns by name
select(flights, year, month, day)
```

```
# A tibble: 336,776 x 3
    year month   day
   <int> <int> <int>
 1  2013     1     1
 2  2013     1     1
 3  2013     1     1
 4  2013     1     1
 5  2013     1     1
 6  2013     1     1
 7  2013     1     1
 8  2013     1     1
 9  2013     1     1
10  2013     1     1
# ... with 336,766 more rows
```

***


```r
# Select all columns between year and day (inclusive)
select(flights, year:day)
```

```
# A tibble: 336,776 x 3
    year month   day
   <int> <int> <int>
 1  2013     1     1
 2  2013     1     1
 3  2013     1     1
 4  2013     1     1
 5  2013     1     1
 6  2013     1     1
 7  2013     1     1
 8  2013     1     1
 9  2013     1     1
10  2013     1     1
# ... with 336,766 more rows
```

```r
# Select all columns except those from year to day (inclusive)
select(flights, -(year:day))
```

```
# A tibble: 336,776 x 16
   dep_time sched_dep_time dep_delay arr_time sched_arr_time arr_delay
      <int>          <int>     <dbl>    <int>          <int>     <dbl>
 1      517            515         2      830            819        11
 2      533            529         4      850            830        20
 3      542            540         2      923            850        33
 4      544            545        -1     1004           1022       -18
 5      554            600        -6      812            837       -25
 6      554            558        -4      740            728        12
 7      555            600        -5      913            854        19
 8      557            600        -3      709            723       -14
 9      557            600        -3      838            846        -8
10      558            600        -2      753            745         8
# ... with 336,766 more rows, and 10 more variables: carrier <chr>,
#   flight <int>, tailnum <chr>, origin <chr>, dest <chr>, air_time <dbl>,
#   distance <dbl>, hour <dbl>, minute <dbl>, time_hour <dttm>
```



Selecionando colunas com select() (2)
========================================================
incremental: true
class: small-code

- Existem *helper functions* que podemos usar dentro de ``select()``;
- São funções que lembram o funcionamento de uma *regular expression* para identificarmos nomes de colunas que atendem a determinado critério;
- São muito úteis com grandes datasets: *starts_with(), ends_with(), matches() e contains()*. 

- `select()` pode ser usada inclusive para renomear variáveis:


```r
select(flights, tail_num = tailnum)
```

```
# A tibble: 336,776 x 1
   tail_num
   <chr>   
 1 N14228  
 2 N24211  
 3 N619AA  
 4 N804JB  
 5 N668DN  
 6 N39463  
 7 N516JB  
 8 N829AS  
 9 N593JB  
10 N3ALAA  
# ... with 336,766 more rows
```

- A nova variável será chamada *tail_num* e receberá toda a informação da original *tailnum*.

***

- No entanto, *select()* "abandona" todas as demais variáveis quando você faz uma renomeação. O melhor então é usar *rename()*:


```r
rename(flights, tail_num = tailnum)
```

```
# A tibble: 336,776 x 19
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
# ... with 336,766 more rows, and 12 more variables: sched_arr_time <int>,
#   arr_delay <dbl>, carrier <chr>, flight <int>, tail_num <chr>,
#   origin <chr>, dest <chr>, air_time <dbl>, distance <dbl>, hour <dbl>,
#   minute <dbl>, time_hour <dttm>
```


Adicionando novas colunas com mutate() (1)
========================================================
incremental: true
class: small-code

- Além de selecionar subconjuntos de colunas existentes, é geralmente útil adicionar novas colunas que são funções de colunas já presentes no data.frame;

- Veja um exemplo com `mutate()`:


```r
mutate(flights,
  gain = arr_delay - dep_delay,
  speed = distance / air_time * 60
)
```

```
# A tibble: 336,776 x 21
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
# ... with 336,766 more rows, and 14 more variables: sched_arr_time <int>,
#   arr_delay <dbl>, carrier <chr>, flight <int>, tailnum <chr>,
#   origin <chr>, dest <chr>, air_time <dbl>, distance <dbl>, hour <dbl>,
#   minute <dbl>, time_hour <dttm>, gain <dbl>, speed <dbl>
```


Adicionando novas colunas com mutate() (2)
========================================================
incremental: true
class: small-code

- `mutate()` equivale a função `transform()` que vimos no Módulo 1 ...

- ... Mas uma inovação: nos permite nos referir a colunas que acabamos de criar no mesmo comando:


```r
mutate(flights,
  gain = arr_delay - dep_delay,
  gain_per_hour = gain / (air_time / 60)
)
```

```
# A tibble: 336,776 x 21
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
# ... with 336,766 more rows, and 14 more variables: sched_arr_time <int>,
#   arr_delay <dbl>, carrier <chr>, flight <int>, tailnum <chr>,
#   origin <chr>, dest <chr>, air_time <dbl>, distance <dbl>, hour <dbl>,
#   minute <dbl>, time_hour <dttm>, gain <dbl>, gain_per_hour <dbl>
```


***

- Se só nos interessarem as novas varipaveis, usamos *transmute()*:


```r
transmute(flights,
  gain = arr_delay - dep_delay,
  gain_per_hour = gain / (air_time / 60)
)
```

```
# A tibble: 336,776 x 2
    gain gain_per_hour
   <dbl>         <dbl>
 1     9          2.38
 2    16          4.23
 3    31         11.6 
 4   -17         -5.57
 5   -19         -9.83
 6    16          6.4 
 7    24          9.11
 8   -11        -12.5 
 9    -5         -2.14
10    10          4.35
# ... with 336,766 more rows
```


Sumarizando valores com summarise()
========================================================
incremental: true
class: small-code

- O último "verbo" de **dplyr** é `summarise()`;

- Ele colapsa um data.frame em uma única linha:


```r
summarise(flights,
  delay = mean(dep_delay, na.rm = TRUE)
)
```

```
# A tibble: 1 x 1
  delay
  <dbl>
1  12.6
```

... Contudo, é mais útil usarmos o "verbo" *group_by()* que veremos a seguir.


Comunalidades
========================================================
incremental: true
class: small-code

- Note que a sintaxe e funcionamento de todos os verbos de **dplyr** apresentados até aqui são bem similares:

  - o primeiro argumento é um data.frame;
  - os argumentos subsequentes descrevem o que fazer com o data.frame. Podemos nos referir às colunas do data.frame diretamente sem a necessidade de usar `$`;
  - o resultado é um noo data.frame.

- Juntas, essas propriedades facilitam encadear múltiplos passos simples para alcançar um resultado complexo;

- O restante do que **dplyr** faz, vem de aplicar as 5 funções a diferentes tipos de dados;

- O próximo passo é trabalhar com dados agrupados.


Operações agrupadas (1)
========================================================
incremental: true
class: small-code

- Podemos aplicar os verbos de dplyr a grupos presentes nos nossos dados utilizando a função `group_by()`;

- Ela "quebra" o dataset em grupos específicos de linhas;

- No início, você não percebe qualquer alteração -- É como elas ficassem em segundo plano;

- No entanto, ao aplicarmos algum dos verbos principais no dataset "alterado" por ``group_by``, eles automaticamente serão aplicados por grupo ou "by group".


Operações agrupadas - exemplos com os verbos de dplyr (1)
========================================================
incremental: true
class: small-code

No exemplo a seguir, nos separamos o dataset por aviões e então sumarizamos cada avião contando o número de voos (`count = n()`), computando a distância média (`dist = mean(distance, na.rm = TRUE`)) e *arrival delay* (`delay = mean(arr_delay, na.rm = TRUE`)).


```r
by_tailnum <- group_by(flights, tailnum)
delay <- summarise(by_tailnum,
  count = n(),
  dist = mean(distance, na.rm = TRUE),
  delay = mean(arr_delay, na.rm = TRUE))
delay <- filter(delay, count > 20, dist < 2000)
delay
```

```
# A tibble: 2,962 x 4
   tailnum count  dist   delay
   <chr>   <int> <dbl>   <dbl>
 1 N0EGMQ    371  676.   9.98 
 2 N10156    153  758.  12.7  
 3 N102UW     48  536.   2.94 
 4 N103US     46  535.  -6.93 
 5 N104UW     47  535.   1.80 
 6 N10575    289  520.  20.7  
 7 N105UW     45  525.  -0.267
 8 N107US     41  529.  -5.73 
 9 N108UW     60  534.  -1.25 
10 N109UW     48  536.  -2.52 
# ... with 2,952 more rows
```

***


```r
# notar que group_by() nao deixa alterações evidentes
by_tailnum
```

```
# A tibble: 336,776 x 19
# Groups:   tailnum [4,044]
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
# ... with 336,766 more rows, and 12 more variables: sched_arr_time <int>,
#   arr_delay <dbl>, carrier <chr>, flight <int>, tailnum <chr>,
#   origin <chr>, dest <chr>, air_time <dbl>, distance <dbl>, hour <dbl>,
#   minute <dbl>, time_hour <dttm>
```



Operações agrupadas - exemplos com os verbos de dplyr (2)
========================================================
incremental: true
class: small-code


- `summarise()` é utilizada com **aggregate functions**, as quais recebem um vetor de valores e retornam um único número;

-    Há muito exemplos úteis do *base R* que podem ser utilizados, como `min()`, `max()`, `mean()`, `sum()`, `sd()`, `median()`, etc;

- **dplyr** fornece mais algumas outras bem úteis:

  - ``n()``: número de observações no grupo atual;
  - ``n_distinct(x)``: número de valores únicos em x;
  - ``first(x)``, ``last(x)`` e ``nth(x, n)`` funcionam de forma similar a ``x[1]``, ``x[length(x)]`` e ``x[n]``, mas nos dão maior controle sobre resultado caso algum valor seja **missing**.


Operações agrupadas - exemplos com os verbos de dplyr (3)
========================================================
incremental: true
class: small-code

Quando agrupamos mais de uma variável, cada *summarise()* que executamos, "raspa"/elimina um nível do agrupamento. Exemplo:

```r
daily <- group_by(flights, year, month, day)
(per_day   <- summarise(daily, flights = n()))
```

```
# A tibble: 365 x 4
# Groups:   year, month [?]
    year month   day flights
   <int> <int> <int>   <int>
 1  2013     1     1     842
 2  2013     1     2     943
 3  2013     1     3     914
 4  2013     1     4     915
 5  2013     1     5     720
 6  2013     1     6     832
 7  2013     1     7     933
 8  2013     1     8     899
 9  2013     1     9     902
10  2013     1    10     932
# ... with 355 more rows
```

```r
# notar que da descrição "Groups:" do tibble, o número de variáveis de agrupamento vai diminuindo.
```

***


```r
(per_month <- summarise(per_day, flights = sum(flights)))
```

```
# A tibble: 12 x 3
# Groups:   year [?]
    year month flights
   <int> <int>   <int>
 1  2013     1   27004
 2  2013     2   24951
 3  2013     3   28834
 4  2013     4   28330
 5  2013     5   28796
 6  2013     6   28243
 7  2013     7   29425
 8  2013     8   29327
 9  2013     9   27574
10  2013    10   28889
11  2013    11   27268
12  2013    12   28135
```

```r
(per_year  <- summarise(per_month, flights = sum(flights)))
```

```
# A tibble: 1 x 2
   year flights
  <int>   <int>
1  2013  336776
```

<!-- ISSO SE CHAMA PROGRSSIVELY ROLLING UP SUMMARIES IN A DATA FRAME -->

<!-- COMO MEDIAS PONDERADAS E VARIANCIAS EH COMPLICADO FAZER ISSO PQ EH UM EQUIVOCO -->


Outros Exemplos com os verbos de dplyr - select (1)
========================================================
incremental: true
class: small-code

**select():**

- Uma das melhores carcterísticas de **dplyr** é que podemos nos referir as variáveis de um *tibble* ou data.frame como se fossem variáveis regulares (aquelas que estão no GlobalEnv);

Veja formas equivalentes do ponto de vista de **dplyr**:

```r
select(flights, year)
```

```
# A tibble: 336,776 x 1
    year
   <int>
 1  2013
 2  2013
 3  2013
 4  2013
 5  2013
 6  2013
 7  2013
 8  2013
 9  2013
10  2013
# ... with 336,766 more rows
```

***


```r
select(flights, 1)
```

```
# A tibble: 336,776 x 1
    year
   <int>
 1  2013
 2  2013
 3  2013
 4  2013
 5  2013
 6  2013
 7  2013
 8  2013
 9  2013
10  2013
# ... with 336,766 more rows
```


Outros Exemplos com os verbos de dplyr - select (2)
========================================================
incremental: true
class: small-code

- Se houver uma variável no GlobalEnv(surrounding conetxt - arredondezas) com o mesmo nome de uma coluna de nosso data.frame, você não poderá referenciá-la diretamente:



```r
year <- 5
select(flights, year)
```

```
# A tibble: 336,776 x 1
    year
   <int>
 1  2013
 2  2013
 3  2013
 4  2013
 5  2013
 6  2013
 7  2013
 8  2013
 9  2013
10  2013
# ... with 336,766 more rows
```


Outros Exemplos com os verbos de dplyr - mutate (1)
========================================================
incremental: true
class: small-code

**`mutate():`**

- A semântica de *mutating* é um pouco diferente da semântica de *selecting*;

- Enquanto *select()* espera por receber nomes ou posições, *mutate()* espera por vetores colunas. 


Vamos criar um *tibble* menor a partir do original para demonstrar:

```r
df <- select(flights, year:dep_time)
```

***

Quando usamos *select()*, os nomes "crus" das colunas representam as próprias colunas do *tibble*. Veja o que acontece quando usamos *mutate()* com uma *string*:

```r
mutate(df, "year", 2)
```

```
# A tibble: 336,776 x 6
    year month   day dep_time `"year"`   `2`
   <int> <int> <int>    <int> <chr>    <dbl>
 1  2013     1     1      517 year         2
 2  2013     1     1      533 year         2
 3  2013     1     1      542 year         2
 4  2013     1     1      544 year         2
 5  2013     1     1      554 year         2
 6  2013     1     1      554 year         2
 7  2013     1     1      555 year         2
 8  2013     1     1      557 year         2
 9  2013     1     1      557 year         2
10  2013     1     1      558 year         2
# ... with 336,766 more rows
```

*"year"* - um vetor de *string* de tamanho 1 - é entendido por *mutate()* como se quiséssemos acrescentar uma nova coluna no data.frame. Então, ele recicla esse vetor de tamanho 1 até atingir o número de linhas. O mesmo ocorre para o numeral 2.


Outros Exemplos com os verbos de dplyr - mutate (2)
========================================================
incremental: true
class: small-code

Se você quisesse criar uma coluna que *year + 10*, não pode utilizar aspas ("year" + 10). Estaria somando 10 a uma string e não aos valores de uma coluna. O correto seria:

```r
mutate(df, year + 10)
```

```
# A tibble: 336,776 x 5
    year month   day dep_time `year + 10`
   <int> <int> <int>    <int>       <dbl>
 1  2013     1     1      517        2023
 2  2013     1     1      533        2023
 3  2013     1     1      542        2023
 4  2013     1     1      544        2023
 5  2013     1     1      554        2023
 6  2013     1     1      554        2023
 7  2013     1     1      555        2023
 8  2013     1     1      557        2023
 9  2013     1     1      557        2023
10  2013     1     1      558        2023
# ... with 336,766 more rows
```

Podemos criar um novo vetor e adicionar ao data.frame com um nome diferente:


```r
var <- seq(1, nrow(df))
mutate(df, new = var)
```

```
# A tibble: 336,776 x 5
    year month   day dep_time   new
   <int> <int> <int>    <int> <int>
 1  2013     1     1      517     1
 2  2013     1     1      533     2
 3  2013     1     1      542     3
 4  2013     1     1      544     4
 5  2013     1     1      554     5
 6  2013     1     1      554     6
 7  2013     1     1      555     7
 8  2013     1     1      557     8
 9  2013     1     1      557     9
10  2013     1     1      558    10
# ... with 336,766 more rows
```


Outros Exemplos com os verbos de dplyr - group_by (1)
========================================================
incremental: true
class: small-code

**group_by():**

- A função **group_by** tem semântica parecida com a de *mutate()*; 

- Se passarmos uma string como nome das colunas pelas quais desejamos agrupar, ele irá reciclar um vetor apenas com aquela string $\rightarrow$ temos que passar, então, os nomes das colunas sem aspas.


```r
group_by(df, "month")
```

```
# A tibble: 336,776 x 5
# Groups:   "month" [1]
    year month   day dep_time `"month"`
   <int> <int> <int>    <int> <chr>    
 1  2013     1     1      517 month    
 2  2013     1     1      533 month    
 3  2013     1     1      542 month    
 4  2013     1     1      544 month    
 5  2013     1     1      554 month    
 6  2013     1     1      554 month    
 7  2013     1     1      555 month    
 8  2013     1     1      557 month    
 9  2013     1     1      557 month    
10  2013     1     1      558 month    
# ... with 336,766 more rows
```

***


```r
group_by(df, month)
```

```
# A tibble: 336,776 x 4
# Groups:   month [12]
    year month   day dep_time
   <int> <int> <int>    <int>
 1  2013     1     1      517
 2  2013     1     1      533
 3  2013     1     1      542
 4  2013     1     1      544
 5  2013     1     1      554
 6  2013     1     1      554
 7  2013     1     1      555
 8  2013     1     1      557
 9  2013     1     1      557
10  2013     1     1      558
# ... with 336,766 more rows
```


Outros Exemplos com os verbos de dplyr - group_by (2)
========================================================
incremental: true
class: small-code

Mas, como às vezes fazer agrupamentos utilizando a semântica de *select()* também pode ser útil, os autores do pacote adicionaram uma variante de *group_by()*: **group_by_at()**.


```r
group_by_at(df, vars(year:day))
```

```
# A tibble: 336,776 x 4
# Groups:   year, month, day [365]
    year month   day dep_time
   <int> <int> <int>    <int>
 1  2013     1     1      517
 2  2013     1     1      533
 3  2013     1     1      542
 4  2013     1     1      544
 5  2013     1     1      554
 6  2013     1     1      554
 7  2013     1     1      555
 8  2013     1     1      557
 9  2013     1     1      557
10  2013     1     1      558
# ... with 336,766 more rows
```



Pipes %>% (1)
========================================================
incremental: true
class: small-code

**dplyr** é funcional no sentido de que os chamados às funções não tem efeitos colaterais. Ou seja, você **NÃO** precisa sempre precisa salvar seus resultados. Isso faz com que não tenhámos um código tão elegante, especialmente quando vamos fazer várias operações, uma de cada vez:


```r
a1 <- group_by(flights, year, month, day)
a2 <- select(a1, arr_delay, dep_delay)
a3 <- summarise(a2,
  arr = mean(arr_delay, na.rm = TRUE),
  dep = mean(dep_delay, na.rm = TRUE))
a4 <- filter(a3, arr > 30 | dep > 30)
```


Note que há muitos resultados intermediários que vamos salvando, quando na verdade só nos interessaria o final. Para resolver isso poderíamos ir chamando uma função dentro da outra:


```r
filter(
  summarise(
    select(
      group_by(flights, year, month, day),
      arr_delay, dep_delay
    ),
    arr = mean(arr_delay, na.rm = TRUE),
    dep = mean(dep_delay, na.rm = TRUE)
  ),
  arr > 30 | dep > 30
)
```

```
# A tibble: 49 x 5
# Groups:   year, month [11]
    year month   day   arr   dep
   <int> <int> <int> <dbl> <dbl>
 1  2013     1    16  34.2  24.6
 2  2013     1    31  32.6  28.7
 3  2013     2    11  36.3  39.1
 4  2013     2    27  31.3  37.8
 5  2013     3     8  85.9  83.5
 6  2013     3    18  41.3  30.1
 7  2013     4    10  38.4  33.0
 8  2013     4    12  36.0  34.8
 9  2013     4    18  36.0  34.9
10  2013     4    19  47.9  46.1
# ... with 39 more rows
```

Embora resolva o problema de salvar os objetos intermediários, esta abordagem é difícil de lermos porque a ordem das operações se dá dentro para fora. Então, os argumentos acabam ficando longe da função propriamente dita.


Pipes %>% (2)
========================================================
incremental: true
class: small-code

- Para dar uma solução elegante ao problema anterior, **dplyr** utiliza o operador pipe **%>%** do pacote *magritrr*;

- `x %>% f(y)` equivale a `f(x, y)`;

- Então, podemos utilizar esse operador para reescrever múltiplas operações que podemos ler da esquerda para direita e de cima para baixo:

```r
flights %>%
  group_by(year, month, day) %>%
  select(arr_delay, dep_delay) %>%
  summarise(
    arr = mean(arr_delay, na.rm = TRUE),
    dep = mean(dep_delay, na.rm = TRUE)
  ) %>%
  filter(arr > 30 | dep > 30)
```

```
# A tibble: 49 x 5
# Groups:   year, month [11]
    year month   day   arr   dep
   <int> <int> <int> <dbl> <dbl>
 1  2013     1    16  34.2  24.6
 2  2013     1    31  32.6  28.7
 3  2013     2    11  36.3  39.1
 4  2013     2    27  31.3  37.8
 5  2013     3     8  85.9  83.5
 6  2013     3    18  41.3  30.1
 7  2013     4    10  38.4  33.0
 8  2013     4    12  36.0  34.8
 9  2013     4    18  36.0  34.9
10  2013     4    19  47.9  46.1
# ... with 39 more rows
```



FIM
========================================================
incremental: false
class: small-code

OBRIGADO!!
