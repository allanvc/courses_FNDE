Análise de Dados em R (FNDE) - Módulo 2
Sessão 9 - Gráficos com ggplot2
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
- aprender a fazer gráficos mais sofisticados que os do  *base **R** *; 
<br />
- aprender os conceitos  da chamada **gramática de gráficos**.



Introdução (1)
========================================================
incremental: true
class: small-code


![](./img/ggplot2.png)


***

**ggplot2** 

- também é um dos pacotes do *core* do **tidyverse**;

- Foi escrito por Hadley Wickham, o qual publicou um livro sobre o pacote **"ggplot2 Elegant Graphics for Data Analysis"**;

- O sucesso de **ggplot2** é tão expressivo que foram feitos *API's (Application Programming Interface)* para que **ggplot2** pudesse ser utilizado em outras linguagens, como Python por exemplo.




A gramática de gráficos
========================================================
incremental: true
class: small-code

- A gramática de gráficos foi um conceito desenvolvido por Leland Wilkinson em 1999 e publicado por ele em *The Grammar of Graphics* em 2005;

- Essa gramática define regras para estruturar elementos matemáticos e de "embelezamento"(**aesthetics** ~ elementos perceptíveis) em um gráfico propriamente dito;

- **ggplot2** é escrito sob as regras desta gramática.


- A gramática de gráficos de **ggplot2** permite:

  - construir gráficos a partir de conceitos (do que desejamos fazer) ao invés de termos que lembrar comandos e opções;
  <br />
  - conceber novos e melhorados gráficos.


Estrutura da gramática de gráficos:
========================================================
incremental: true
class: small-code

- **Data** (dados)
  + variáveis a serem retratadas no gráfico;
  + variáveis a serem mapeadas para os *aesthetics* (elementos perceptíveis) do gráfico;

- **Geoms**
  + objetos e formas do gráfico (barras, pontos, linhas, etc)
  
- **Stats**
  + transformações estatísticas , geralmente para sumarizar dados (média, variância, mediana, etc)

***
  
- **Scales**
  + define quais elementos perceptíveis (*aesthetics*) do gráfico serão mapeados para as variáveis (ex: que cores serão mapeadas a quais valores)
  
- **Coordinate Systems**
  + define como os dados serão mapeados para o plano/área do gráfico (Cartesiano, Polar, etc)

- **Facetting**
  + divide os dados em subconjuntos para criar multiplas variações do mesmo gráfico (ex: gráficos em painel)

Todos esses elementos são tratados como *layers* e podem ser adiconados a parte principal do gráfico utilizando-se "**+**".




Data (dados) (1)
========================================================
incremental: true
class: small-code

Neste curso, veremos mais de perto os elementos *data*, *aesthetics*, *stats* e *facetting* e o que significa cada um desses elementos na estrutura da gramática de gráficos.

- Os dados utilizados com as funções de **ggplot2** **DEVEM** ser armazenados como um data.frame (ou tibble);

- Há possibilidade, inclusive de usar mais de um data.frame em um gráfico; 

- A recomendação é só que se defina e se certifique acerca dos tipos das variáveis (*numeric, factor, etc*) antes de começar a construir um gráfico


Data (dados) (2)
========================================================
incremental: true
class: small-code

A função **ggplot()**:

- Os dados que serão usados para plotagem serão especificados dentro da função **ggplot()**;

- Note que é **ggplot()** e não **ggplot2()**;

- O nosso data.frame/ *tibble* sempre será  **primeiro** argumento desta função;



aesthetics - elementos gráficos perceptíveis (1)
========================================================
incremental: true
class: small-code

- Variáveis são mapeadas para os **aesthetics**, o que significa que são traduzidas para os elementos perceptíveis do gráfico; 

- A helper function **aes()** é quem faz esse mapeamento;

- A função **aes()** pode ser especificada dentro de várias funções de **ggplot2** (*geoms, stats, etc*);

- Os **aesthetics** mais utilizados são:

  + **x** - posição no eixo x;
  + **y** - posição no eixo y;
  + **color** ou **colour** - "cor de fora" ou do contorno do objeto;
  + **fill** - "cor de dentro" do objeto;
  + **alpha** - nível transparência do gráfico;
  + **shape** - forma dos marcadores (pontos, triângilos, cruz, etc);
  + **size** - tamanho (do raio) dos objetos;


Exemplos (1)
========================================================
incremental: true
class: small-code

- utilizaremos os pacotes **mpg** e **diamonds** que já vem com o **ggplot2**;

  - **diamonds** contém os preços e outros atributos de quase 54 mil diamantes, como quilate (carat), cut, color, clarity, etc; 
  
  - **mpg** contém um *subset* de dados de economia de combustível de carros lançados entre 1999 e 2008. As variáveis envolvem modelo, ano, cilindros, etc.

***


```r
library(magrittr) # vamos usar pipe
library(ggplot2)
str(mpg)
```

```
Classes 'tbl_df', 'tbl' and 'data.frame':	234 obs. of  11 variables:
 $ manufacturer: chr  "audi" "audi" "audi" "audi" ...
 $ model       : chr  "a4" "a4" "a4" "a4" ...
 $ displ       : num  1.8 1.8 2 2 2.8 2.8 3.1 1.8 1.8 2 ...
 $ year        : int  1999 1999 2008 2008 1999 1999 2008 1999 1999 2008 ...
 $ cyl         : int  4 4 4 4 6 6 6 4 4 4 ...
 $ trans       : chr  "auto(l5)" "manual(m5)" "manual(m6)" "auto(av)" ...
 $ drv         : chr  "f" "f" "f" "f" ...
 $ cty         : int  18 21 20 21 16 18 18 18 16 20 ...
 $ hwy         : int  29 29 31 30 26 26 27 26 25 28 ...
 $ fl          : chr  "p" "p" "p" "p" ...
 $ class       : chr  "compact" "compact" "compact" "compact" ...
```

```r
str(diamonds)
```

```
Classes 'tbl_df', 'tbl' and 'data.frame':	53940 obs. of  10 variables:
 $ carat  : num  0.23 0.21 0.23 0.29 0.31 0.24 0.24 0.26 0.22 0.23 ...
 $ cut    : Ord.factor w/ 5 levels "Fair"<"Good"<..: 5 4 2 4 2 3 3 3 1 3 ...
 $ color  : Ord.factor w/ 7 levels "D"<"E"<"F"<"G"<..: 2 2 2 6 7 7 6 5 2 5 ...
 $ clarity: Ord.factor w/ 8 levels "I1"<"SI2"<"SI1"<..: 2 3 5 4 2 6 7 3 4 5 ...
 $ depth  : num  61.5 59.8 56.9 62.4 63.3 62.8 62.3 61.9 65.1 59.4 ...
 $ table  : num  55 61 65 58 58 57 57 55 61 61 ...
 $ price  : int  326 326 327 334 335 336 336 337 337 338 ...
 $ x      : num  3.95 3.89 4.05 4.2 4.34 3.94 3.95 4.07 3.87 4 ...
 $ y      : num  3.98 3.84 4.07 4.23 4.35 3.96 3.98 4.11 3.78 4.05 ...
 $ z      : num  2.43 2.31 2.31 2.63 2.75 2.48 2.47 2.53 2.49 2.39 ...
```


Exemplos (2)
========================================================
incremental: true
<!-- class: small-code -->

Para fazermos um gráfico, primeiro precisamos indicar os dados e fazer o mapeamento das variáveis aos **aesthetics**. Isso reserva uma área de gráfico a ser utilizada.


```r
mpg %>%
ggplot(aes(x=displ, y=hwy))
```

<img src="sessao_09-figure/unnamed-chunk-2-1.png" title="plot of chunk unnamed-chunk-2" alt="plot of chunk unnamed-chunk-2" style="display: block; margin: auto;" />



Exemplos (3)
========================================================
incremental: true
class: small-code

Vejamos um primeiro exemplo do consumo dos veículos galões/milhas de acordo com o número dos cilindros.


```r
mpg %>%
ggplot(mapping = aes(x=displ, y=hwy)) +
  geom_point() # isso sao os geoms que veremos a seguir
```

<img src="sessao_09-figure/unnamed-chunk-3-1.png" title="plot of chunk unnamed-chunk-3" alt="plot of chunk unnamed-chunk-3" style="display: block; margin: auto;" />


Note que não houve a necessidade de re-especificar os argumentos data e *aes()* para a função *geom_point()*.


Exemplos (4)
========================================================
incremental: true
class: small-code

- Poderíamos melhorar nosso gráfico adicionando cores dentro de *aes()*; 

- Isso acrescentaria mais informação. Poderíamos atribuir cores diferentes para cada classe de carros:


```r
mpg %>%
ggplot(mapping = aes(x=displ, y=hwy)) +
  geom_point(aes(color=class))
```

<img src="sessao_09-figure/unnamed-chunk-4-1.png" title="plot of chunk unnamed-chunk-4" alt="plot of chunk unnamed-chunk-4" style="display: block; margin: auto;" />

```r
#ou
# mpg %>%
# ggplot(mapping = aes(x=displ, y=hwy, color=class)) +
#   geom_point()
```


Exemplos (5)
========================================================
incremental: true
class: small-code

Podemos ao invés de atribuir cores, trabalhar com o nível de transparência (**alpha**) das classes:


```r
mpg %>%
ggplot(mapping = aes(x=displ, y=hwy, alpha=class)) +
  geom_point()
```

<img src="sessao_09-figure/unnamed-chunk-5-1.png" title="plot of chunk unnamed-chunk-5" alt="plot of chunk unnamed-chunk-5" style="display: block; margin: auto;" />

Isso não é muito útil porque temos várias classes, mas serve para ilustrar essa possibilidade com *ggplot2*.


Exemplos (6)
========================================================
incremental: true
class: small-code

Uma outra forma de demonstrar as diferentes classes, seria utilizar diferentes formas (**shape**):


```r
mpg %>%
ggplot(mapping = aes(x=displ, y=hwy, shape=class)) +
  geom_point()
```

<img src="sessao_09-figure/unnamed-chunk-6-1.png" title="plot of chunk unnamed-chunk-6" alt="plot of chunk unnamed-chunk-6" style="display: block; margin: auto;" />

***

- Note que **ggplot2** traz um **warning** dizendo que não possui tantos **shapes** quanto precisamos neste caso:

  - Nossa variável **class** tem sete *levels*. **suv** ficou sem forma para ser plotada. 
  
  - Só podemos ter até 6 shapes diferentes nesses casos em que mapeamos uma variável para o **aesthetics** de **shape**.


Mapping vs Setting aesthetics (1)
========================================================
incremental: true
class: small-code

- Nos exemplos anteriores, poderíamos optar por usar apenas uma única cor ou um único shape que não fosse o padrão de "bolinha" na cor preta; 

- O que deveríamos fazer então, ao invés de **mapear** variáveis para **aesthetic**, é **setar** a **aesthetic** como uma contante;

  + Quando vamos mapear um **aesthetic**, as variáveis são passadas **DENTRO** de *aes()*; 
  
  + Quando vamos setar um **aesthetic** como constante, o valor é passado **FORA** de *aes()*.

Vejamos os exemplos anteriores com um *aesthetic* constante para cor e shape:

***


```r
mpg %>%
ggplot(mapping = aes(x=displ, y=hwy)) +
  geom_point(color = "red")
```

<img src="sessao_09-figure/unnamed-chunk-7-1.png" title="plot of chunk unnamed-chunk-7" alt="plot of chunk unnamed-chunk-7" style="display: block; margin: auto;" />


```r
mpg %>%
ggplot(mapping = aes(x=displ, y=hwy)) +
  geom_point(shape=2)
```

<img src="sessao_09-figure/unnamed-chunk-8-1.png" title="plot of chunk unnamed-chunk-8" alt="plot of chunk unnamed-chunk-8" style="display: block; margin: auto;" />

<!-- Note que devemos passar as constantes para *geom_point()* (fora de *aes()*) - o elemento de objetos geométricos - e não para *ggplot()*. -->


Shapes
========================================================
incremental: true
class: small-code

Veja quantos *shapes* podemos usar no **R**:

<img src="sessao_09-figure/unnamed-chunk-9-1.png" title="plot of chunk unnamed-chunk-9" alt="plot of chunk unnamed-chunk-9" style="display: block; margin: auto;" />


Mapping vs Setting aesthetics (2)
========================================================
incremental: true
<!-- class: small-code -->

Cuidado, pois setar uma **aesthetic** como constante dentro de **aes()** pode resultar em um comportamento inesperado.

```r
mpg %>%
ggplot(mapping = aes(x=displ, y=hwy)) +
  geom_point(aes(color = "green"))
```

<img src="sessao_09-figure/unnamed-chunk-10-1.png" title="plot of chunk unnamed-chunk-10" alt="plot of chunk unnamed-chunk-10" style="display: block; margin: auto;" />


Geoms (1)
========================================================
incremental: true
class: small-code


- **Geoms** são as **FORMAS GEOMÉTRICAS** plotadas nos gráficos; 

- Eles diferem de um para o outro quanto ao tipo de **aesthetics** que eles *requerem* ou *aceitam*;

- Por exemplo: **geom_point()** requer **aes(x, y)**, ao passo que **geom_bar()** requer **aes(x)** apenas;

- Para obter ajuda digite *?geom_x*



Exemplos - geoms para uma variável (contínua)
========================================================
incremental: true
class: small-code


```r
c <- mpg %>%
ggplot(mapping = aes(x=hwy))

c + geom_density()
```

![plot of chunk unnamed-chunk-11](sessao_09-figure/unnamed-chunk-11-1.png)

```r
c + geom_dotplot()
```

![plot of chunk unnamed-chunk-11](sessao_09-figure/unnamed-chunk-11-2.png)

***


```r
c + geom_freqpoly()
```

![plot of chunk unnamed-chunk-12](sessao_09-figure/unnamed-chunk-12-1.png)

```r
c + geom_histogram()
```

![plot of chunk unnamed-chunk-12](sessao_09-figure/unnamed-chunk-12-2.png)


Exemplos - geoms para uma variável (discreta)
========================================================
incremental: true
<!-- class: small-code -->



```r
ggplot(data=mpg, mapping=aes(x=fl))+
geom_bar()
```

<img src="sessao_09-figure/unnamed-chunk-13-1.png" title="plot of chunk unnamed-chunk-13" alt="plot of chunk unnamed-chunk-13" style="display: block; margin: auto;" />


Exemplos - geoms para duas variáveis (ambas contínuas)
========================================================
incremental: true
class: small-code


```r
e <- mpg %>% 
  ggplot(mapping=aes(x=cty, y=hwy))

e + geom_point()
```

<img src="sessao_09-figure/unnamed-chunk-14-1.png" title="plot of chunk unnamed-chunk-14" alt="plot of chunk unnamed-chunk-14" style="display: block; margin: auto;" />

```r
e + geom_jitter()
```

<img src="sessao_09-figure/unnamed-chunk-14-2.png" title="plot of chunk unnamed-chunk-14" alt="plot of chunk unnamed-chunk-14" style="display: block; margin: auto;" />

***


```r
e2 <- diamonds %>%
  ggplot(aes(x, z))

e2 + geom_line()
```

<img src="sessao_09-figure/unnamed-chunk-15-1.png" title="plot of chunk unnamed-chunk-15" alt="plot of chunk unnamed-chunk-15" style="display: block; margin: auto;" />

```r
e2 + geom_smooth()
```

<img src="sessao_09-figure/unnamed-chunk-15-2.png" title="plot of chunk unnamed-chunk-15" alt="plot of chunk unnamed-chunk-15" style="display: block; margin: auto;" />



Exemplos - geoms para duas variáveis (uma discreta e uma contínua)
========================================================
incremental: true
class: small-code




```r
f <- mpg %>% 
  ggplot(aes(class, hwy))

f + geom_bar(stat="identity")
```

<img src="sessao_09-figure/unnamed-chunk-16-1.png" title="plot of chunk unnamed-chunk-16" alt="plot of chunk unnamed-chunk-16" style="display: block; margin: auto;" />

```r
f + geom_boxplot()
```

<img src="sessao_09-figure/unnamed-chunk-16-2.png" title="plot of chunk unnamed-chunk-16" alt="plot of chunk unnamed-chunk-16" style="display: block; margin: auto;" />

***


```r
f + geom_violin()
```

<img src="sessao_09-figure/unnamed-chunk-17-1.png" title="plot of chunk unnamed-chunk-17" alt="plot of chunk unnamed-chunk-17" style="display: block; margin: auto;" />

Há também outros *geoms* que você encontrará na página de ajuda.


Stats (Transformações estatísticas) (1)
========================================================
incremental: true
class: small-code


- **Stats** transformam os dados, geralmente produzindo um resumo, como média, variância, "caixas" de contagem, etc. 

- Elas podem estar associadas a um *geom* específico e, neste caso, produziraão o gráfico mesmo este *geom* não seja especificado. Veja o exemplo:



```r
# histograma
mpg %>% 
  ggplot(mapping=aes(x=cty))+
           stat_bin()
```

<img src="sessao_09-figure/unnamed-chunk-18-1.png" title="plot of chunk unnamed-chunk-18" alt="plot of chunk unnamed-chunk-18" style="display: block; margin: auto;" />

```r
# ou equivalentemente
# mpg %>% 
#   ggplot(mapping=aes(x=cty))+
#   stat_bin(geom="bar")
```


***


```r
# gráficos de barras


# ou equivalentemente

# 1)
# diamonds %>% 
#   ggplot(aes(x=cut))+
#   stat_count()
 
# 2)
# diamonds %>% 
#   ggplot()+
#   stat_count(aes(x=cut))

# 3)
# diamonds %>% 
#   ggplot(aes(x=cut))+
#   stat_count(geom="bar")

# 4) 
# diamonds %>% 
#   ggplot(aes(x=cut))+
#   geom_bar(stat="count")
```

Cabe ressaltar o *geom* associado também pode ser mudado. A lsita das transformações pode ser encontrada com *?stat*.


stat_summary (1)
========================================================
incremental: true
class: small-code


- **stat_summary** é uma função bastante utilizada que sumariza "y" para cada valor de "x" (como stat_bin), mas a função de sumarização é flexível. 

- Por *default*,  a **summary function** é **mean_se** que retorna 3 valores: **media, media+se, media-se**.


```r
mpg %>% 
  ggplot(mapping=aes(x=class, y=cty))+
           stat_summary()
```

<img src="sessao_09-figure/unnamed-chunk-20-1.png" title="plot of chunk unnamed-chunk-20" alt="plot of chunk unnamed-chunk-20" style="display: block; margin: auto;" />


stat_summary (2)
========================================================
incremental: true
class: small-code

Se quisermos alterar as funções de sumarização, podemos fazer o segunite:

- para retornar 3 valores, usamos *fun.data*:


```r
library(Hmisc)
mpg %>% 
  ggplot(mapping=aes(x=class, y=cty))+
           stat_summary(fun.data="mean_cl_boot")
```

<img src="sessao_09-figure/unnamed-chunk-21-1.png" title="plot of chunk unnamed-chunk-21" alt="plot of chunk unnamed-chunk-21" style="display: block; margin: auto;" />

***

- para retornar 1 valor, usamos *fun.y*:


```r
mpg %>% 
  ggplot(mapping=aes(x=class, y=cty))+
           stat_summary(fun.y="median", geom="point")
```

<img src="sessao_09-figure/unnamed-chunk-22-1.png" title="plot of chunk unnamed-chunk-22" alt="plot of chunk unnamed-chunk-22" style="display: block; margin: auto;" />


Positions (1)
========================================================
incremental: true
class: small-code

- Um aspecto dos gráficos de **ggplot2**, que não chega a ser uma **layer**, diz respeito a ajustes de posição. 

  + Elas serão muito importantes para gráficos de barras com duas variáveis;

- Os ajustes de posição definirão como os *geoms* se localizam no gráfico, de modo que não ocupem o mesmo espaço. 

- No exemplo do gráfico de barras, podemos fazer gráficos de barras justapostas ou empilhadas.

***

-**position="identity"** é o ajuste padrão de **geom_point()**. 

- Ela coloca cada objeto na posição exata do contexto do gráfico. No caso de gráficos de barras, teríamos barras sobrepostas:


```r
diamonds %>% 
  ggplot(mapping=aes(x=cut, fill=clarity))+
           geom_bar(position="identity")
```

<img src="sessao_09-figure/unnamed-chunk-23-1.png" title="plot of chunk unnamed-chunk-23" alt="plot of chunk unnamed-chunk-23" style="display: block; margin: auto;" />


Positions (2)
========================================================
incremental: true
class: small-code

- **position = "dodge"** coloca objetos sobrepostos um ao lado de outro. Nesse caso, teremos barras justapostas:


```r
diamonds %>% 
  ggplot(mapping=aes(x=cut, fill=clarity))+
           geom_bar(position="dodge")
```

<img src="sessao_09-figure/unnamed-chunk-24-1.png" title="plot of chunk unnamed-chunk-24" alt="plot of chunk unnamed-chunk-24" style="display: block; margin: auto;" />

***

- **position="fill"** também empilhará os elementos, mas irá normalizar a altura de todas as barras para que fiquem na mesma escala. Isso torna mais fácil comparar as proporções entre os grupos:


```r
diamonds %>% 
  ggplot(mapping=aes(x=cut, fill=clarity))+
           geom_bar(position="fill")
```

<img src="sessao_09-figure/unnamed-chunk-25-1.png" title="plot of chunk unnamed-chunk-25" alt="plot of chunk unnamed-chunk-25" style="display: block; margin: auto;" />



Positions (3)
========================================================
incremental: true
class: small-code

- **position="jitter"** é útil para gráficos de dispersão, pos hpa um problema muito comum neste tipo de gráfico que é a sobreposição de pontos (*overplotting*);

- Com *jitter*, *ggplot2* adiciona um ruído aleatório nas posições de X e Y para que eles não se sobreponham:


```r
mpg %>% 
  ggplot(mapping=aes(x=displ, y=hwy))+
           geom_point(position="jitter")
```

<img src="sessao_09-figure/unnamed-chunk-26-1.png" title="plot of chunk unnamed-chunk-26" alt="plot of chunk unnamed-chunk-26" style="display: block; margin: auto;" />

- Há outros ajustes de posição bem úteis, como:

- **position="nudge"** afasta os rótulos dos pontos;
- **position="stack"** empilha os elementos uns sobre os outros.


Facetting (1)
========================================================
incremental: true
class: small-code


- As **facets** em **ggplot2** nos permitem criar vários gráficos com base em subconjuntos de dados e plotá-los numma mesma área;

- É uma técnica muito útil (quando temos variáveis categóricas) e desejamos visualizar o comportamento de diferentes catgorias num mesmo gráfico.

- Há duas funções que permitem criar facetas: **facet_wrap()** e **facet_grid()**.

- **facet_wrap** permite a divisão com base em uma única variável ao passo que **facet_grid** permite a divisão em mais variáveis:

***


```r
mpg %>% 
  ggplot()+
  geom_point(mapping=aes(x=displ, y=hwy))+
  facet_wrap(~ class, nrow=2)
```

<img src="sessao_09-figure/unnamed-chunk-27-1.png" title="plot of chunk unnamed-chunk-27" alt="plot of chunk unnamed-chunk-27" style="display: block; margin: auto;" />

- Note acima que podemos definir o npumero de linhas desejado para plotagem;

- O símbolo **"~"** é muito utilizado no **R**. Nesse caso, significa para pegar as variáveis de **aes()** em plotar em função da variável **class**.


Facetting (2)
========================================================
incremental: true
class: small-code

Agora vamos plotar as informações referentes a 3 variáveis. 

- Neste caso especificamos que a variável "trans" deve ser plotada em função das demais variáveis passadas em **aes()**:



```r
mpg %>% 
  ggplot()+
  geom_point(mapping=aes(x=displ, y=hwy))+
  facet_grid(trans ~ .)
```

<img src="sessao_09-figure/unnamed-chunk-28-1.png" title="plot of chunk unnamed-chunk-28" alt="plot of chunk unnamed-chunk-28" style="display: block; margin: auto;" />


Títulos e rótulos
========================================================
incremental: true
class: small-code

Há duas formas de alterar títulos e rótulos dos eixos em **ggplot2**: 
  
  + Você pode optar por **ggtitle("título do gráfico")** e **xlab("rótulo eixo x")**, **ylab("rótulo eixo y")**; 
  + Ou então, **labs()**

***


```r
mpg %>% 
  ggplot() + 
  geom_point(mapping = aes(x=displ, y = hwy, color = class)) + 
  labs(title="Título do gráfico", x = "Eixo x", y = "Eixo y", color = "Categorias")
```

<img src="sessao_09-figure/unnamed-chunk-29-1.png" title="plot of chunk unnamed-chunk-29" alt="plot of chunk unnamed-chunk-29" style="display: block; margin: auto;" />

```r
#ou

# mpg %>% 
#   ggplot() + 
#   geom_point(mapping = aes(x=displ, y = hwy, color = class)) +
#   ggtitle("título do gráfico")+
#   xlab("rótulo eixo x")+
#   ylab("rótulo eixo y")
```




FIM
========================================================
incremental: false
class: small-code

OBRIGADO!!
