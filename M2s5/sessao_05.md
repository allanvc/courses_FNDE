Análise de Dados em R (FNDE) - Módulo 2
Sessão 5 - Trabalhando com Datas - Lubridate
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
- aprender as principais funções do pacote lubridate;
<br />
<br />
- trabalhar com datas no **R**.






Pacote lubridate - Introdução
========================================================
incremental: true
class: small-code


![](./img/lubridate.jpg)

- trabalhar com datas (e tempos) no base **R** geralmente era um processo frustrante

- as funções "nativas" do **R** para dados do tipo *date-times* são pouco intuitivas e mudam dependendo do tipo de objeto que está sendo usado;

- Datas e tempos têm uma série de especificidades, como por exemplo fusos, anos bissextos, horários de verão, etc;

- Por isso precisamos ter métodos robustos para lidar com esses tipos de peculiaridades; 

- O pacote **lubridate** nos facilita algumas operações que já existiam no base **R** e abre a possibilidade de outras que não haviam.



Transformação de números e strings em datas (1)
========================================================
incremental: true
class: small-code

- ** DD/MM/YYYY estar em número ou string não é a mesma coisa que estar no formato de data !!**

- o **R** possui uma classe específica para indicar datas

- Dito isso, ...

- Algumas funções interessantes são: *ymd(), ymd_hms, dmy(), dmy_hms, mdy()*, etc. ``??lubridate``. 



```r
library(lubridate)
ymd(20101215)
```

```
[1] "2010-12-15"
```

```r
mdy("4/1/17")
```

```
[1] "2017-04-01"
```

- Você pode ir variando as letras das funções acima, dependendo de como seus dados estão organizados.

Transformação de números e strings em datas (2)
========================================================
incremental: true
class: small-code


A informação mais completa de data-hora que você pode ter no **R** é:


```r
arrive <- ymd_hms("2011-06-04 12:00:00", tz = "Pacific/Auckland")
arrive
```

```
[1] "2011-06-04 12:00:00 NZST"
```

```r
#> [1] "2011-06-04 12:00:00 NZST"
leave <- ymd_hms("2011-08-10 14:00:00", tz = "Pacific/Auckland")
leave
```

```
[1] "2011-08-10 14:00:00 NZST"
```

```r
# help(ymd_hms)

# para verificar as timezone disponíveis:
#str_subset(OlsonNames(), "Brazil")
# ou grep("Brazil", OlsonNames(), )
```


Extração de partes de data e tempo (1)
========================================================
incremental: true
class: small-code

*year(), month(), mday(), hour(), minute() and second():*


```r
bday <- dmy("02/01/1984")
month(bday)
```

```
[1] 1
```

```r
month(bday, label=TRUE)
```

```
[1] Jan
12 Levels: Jan < Feb < Mar < Apr < May < Jun < Jul < Aug < Sep < ... < Dec
```

```r
wday(bday)
```

```
[1] 2
```

```r
wday(bday, label = TRUE)
```

```
[1] Mon
Levels: Sun < Mon < Tue < Wed < Thu < Fri < Sat
```

```r
year(bday) <- 2016
wday(bday, label = TRUE)
```

```
[1] Sat
Levels: Sun < Mon < Tue < Wed < Thu < Fri < Sat
```



Extração de partes de data e tempo (2)
========================================================
incremental: true
class: small-code


```r
now()
```

```
[1] "2018-07-31 16:37:02 -03"
```

```r
today()
```

```
[1] "2018-07-31"
```

```r
second(arrive)
```

```
[1] 0
```

```r
second(arrive) <- 25
arrive
```

```
[1] "2011-06-04 12:00:25 NZST"
```

```r
second(arrive) <- 0
```


Lidando com fuso-horários (time zones):
========================================================
incremental: true
class: small-code



```r
time <- ymd_hms("2010-12-13 15:30:30")
time
```

```
[1] "2010-12-13 15:30:30 UTC"
```

```r
# Diz a hora que seria "time" em outro fuso
with_tz(time, "America/Chicago")
```

```
[1] "2010-12-13 09:30:30 CST"
```

```r
# Mantém o print da hora, mas muda região
force_tz(time, "America/Chicago")
```

```
[1] "2010-12-13 15:30:30 CST"
```

<!-- mostrar um rleogio mundial para verificar a hora correta -->

Intervalos de tempo (1)
========================================================
incremental: true
class: small-code

- Você pode salvar intervalos de tempo em **lubridate** como uma classe *Interval*;
- Isso nos permite as criar as seguintes operações:



```r
# Primeiro, criamos um intervalo:
auckland <- interval(arrive, leave) 
auckland
```

```
[1] 2011-06-04 12:00:00 NZST--2011-08-10 14:00:00 NZST
```

```r
#> [1] 2011-06-04 12:00:00 NZST--2011-08-10 14:00:00 NZST
auckland <- arrive %--% leave
auckland
```

```
[1] 2011-06-04 12:00:00 NZST--2011-08-10 14:00:00 NZST
```


Intervalos de tempo (2)
========================================================
incremental: true
class: small-code

E podemos verificar por exemplo se esse intervalo faz *overlap* (sobrepõe) com algum outro intervalo que já temos.


```r
jsm <- interval(ymd(20110720, tz = "Pacific/Auckland"), ymd(20110831, tz = "Pacific/Auckland"))
jsm
```

```
[1] 2011-07-20 NZST--2011-08-31 NZST
```

```r
# há sobreposição?
int_overlaps(jsm, auckland)
```

```
[1] TRUE
```

```r
# quando ocorre sobreposição?
setdiff(auckland, jsm)
```

```
[1] 2011-06-04 12:00:00 NZST--2011-07-20 NZST
```

Outras funções que lidam com intervalos são: ``int_start, int_end, int_flip, int_shift, int_aligns, union, intersect, setdiff, and %within%``.


Operações aritméticas com data e horas (1)
========================================================
incremental: true
class: small-code


- Intervalos são um tipo específico de espaço de tempo;
- Além deles, **lubridate** disponibiliza outras duas classes genéricas para trabalharmos com espaços de tempo: *Durations* e *Periods*. 


```r
minutes(2) ## period
```

```
[1] "2M 0S"
```

```r
dminutes(2) ## duration
```

```
[1] "120s (~2 minutes)"
```



Operações aritméticas com data e horas (2)
========================================================
incremental: true
class: small-code

- Por que 2 classes? 

- *Durations* vão sempre gerar resultados precisos em termos de **tempo**, considerando, por exemplo, se o ano é bissexto ou não;

- *Periods*, por sua vez, vão se basear na *number line*, desconsiderando detalhes como anos bissextos etc. 

- Ambas as classes serão úteis para fazermos **operações aritméticas** com datas, dependendo do nosso objetivo.


Operações aritméticas com data e horas (3)
========================================================
incremental: true
class: small-code


```r
leap_year(2011) ## regular year
```

```
[1] FALSE
```

```r
leap_year(2012) ## leap year
```

```
[1] TRUE
```

```r
ymd(20120101) + dyears(1)
```

```
[1] "2012-12-31"
```

```r
# dyears(1) sempre somará considerando se o ano é bissexto ou não

ymd(20120101) + years(1)
```

```
[1] "2013-01-01"
```

- *Durations* foi "honesta" na presença do ano bissexto, o que resulta em um dia a menos na soma, devido ao dia a mais em fevereiro.


Operações aritméticas com data e horas (4)
========================================================
incremental: true
class: small-code

Para saber quanto tempo em dias, semanas ou minutos você passou no seu compromisso em Auckland:


```r
auckland / ddays(1)
```

```
[1] 67.08333
```

```r
auckland / dweeks(1)
```

```
[1] 9.583333
```

```r
auckland / dminutes(1)
```

```
[1] 96600
```

Para não termos que ficar trabalhando com frações de meses ou semanas por exemplo, podemos utilizar módulo:


```r
auckland %/% months(1)
```

```
[1] 2
```

```r
auckland %% months(1)
```

```
[1] 2011-08-04 12:00:00 NZST--2011-08-10 14:00:00 NZST
```


Operações aritméticas com data e horas (5)
========================================================
incremental: true
class: small-code


Outros exemplos:


```r
x <- dmy(02011984)

year(x) + 34
```

```
[1] 2018
```

```r
day(x) -1
```

```
[1] 1
```

```r
month(x)*2
```

```
[1] 2
```

Mais detalhes sobre lubridate em <http://lubridate.tidyverse.org>.


FIM
========================================================
incremental: false
class: small-code

OBRIGADO!!
