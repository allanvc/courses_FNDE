Análise de Dados em R (FNDE) - Módulo 2
Sessão 3 - Funções da família apply
========================================================
author: Allan Vieira
date: março de 2018
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
- aprender as principais funções da família apply;
<br />
<br />
- saber quando utilizá-las para evitar loops.


Introdução (1)
========================================================
incremental: true
class: small-code

<br />
- No início do tópico anterior (Loops), dissemos que seria bom evitar laços sempre que possível;

<br />
- No R, há uma família de funções que nos permitem substituir loops e deixar a programação muito mais eficiente e elegante;

<br />
- elas são uma grande ferramenta para que façamos Análise de Dados em R, pois executam combinações complexas nos dados com poucas linhas de código; 


Introdução (2)
========================================================
incremental: true
class: small-code

<br />
- As funções da família apply são consideradas funções de mais alta ordem no R (higher-order functions): são funções que recebem funções como argumentos;

<br />
- São chamadas de Functionals;

<br />
- Essa família de funções é uma especificidade do R;


Introdução (3)
========================================================
incremental: true
class: small-code


<br />
- A família apply é composta por uma série de funções que permitem manipular “fatias” de dados de matrizes, arrays, data.frames e listas de forma iterativa - ou seja, repetindo procedimentos; 

<br />
- Ao possibilitarem o cruzamento de dados em diversas formas, essas funções acabam evitando o uso explícito de loops n linguagem R;

<br />
- Na verdade, dentro das funções da família apply há loops implementados em linguagem C;

<br />
- Além de rapidez, ganhamos principalmente simplicidade de código ao usar a família apply: ao invés de termos que ficar digitando várias linhas com for, while, etc, precisaremos apenas de uma única linha com alguma das funções apply e seus argumentos;


Introdução (4)
========================================================
incremental: true
class: small-code

<br />
- O funcionamento básico de uma função do tipo apply é: receber uma matriz, array, data.frame ou lista como input e aplicar uma função (com um ou vários argumentos) a pedaços desse objeto de input; 

<br />
- A função chamada pode ser:
  + uma função que já existe no R: funções que geram um número/escalar como sum e mean; funções que façam um subsetting ou uma transformaçãos nos dados; ou ainda funções que se comportam de forma vetorizada e produzem um objeto mais complexo como uma matriz ou data.frame;
  + uma função anônima criada por você para efetuar alguma operação semelhante as supracitadas, mas de forma personalizada (veremos isso mais a frente).

<br />
- A família apply é composta pelas seguintes funções: apply(), lapply(), sapply(), vapply(), mapply() e tapply(). Como e quando usar essas funções dependem do tipo de objeto em que vamos aplicá-las e também o tipo de objeto que desejamos ter na saída.


apply() (1)
========================================================
incremental: true
class: small-code

- É a patriarca da família apply;

- Basicamente, apply() opera em arrays. Para simplificar o entendimento, podemos dizer que é utilizada para matrizes (que são arrays de duas dimensões como vimos no módulo 1);

- apply() vai aplicar alguma função às linhas ou às colunas de uma matriz; 

- Exemplo: calcular a média das linhas ou das colunas de uma matriz.


apply() (1)
========================================================
incremental: true
class: small-code

- Vejamos a estrutura e argumentos da função **apply()**:

```r
args(apply)
```

```
function (X, MARGIN, FUN, ...) 
NULL
```

- *X* é um array ou matriz (array de dimensão 2);

- *FUN* é a função que você vai aplicar aos dados;

<!-- coloqueiFUN antes para quando dizer função aser aplicada em margem, ele não acharem que é a apply -->

- *MARGIN* define onde a função (a definir) será aplicada: se *MARGIN=1*, a função será aplicada nas linhas; se *MARGIN=2*, a função será aplicada nas colunas. Caso você deseje aplicar a função tanto às linhas quanto às colunas, você deve usar *MARGIN=c(1,2)*.


apply() (2)
========================================================
incremental: true
class: small-code

- Veja um exemplo da utilização de *apply()*:


```r
# Construindo uma matriz 5x6
X <- matrix(1:30, nrow=5, ncol=6)
X
```

```
     [,1] [,2] [,3] [,4] [,5] [,6]
[1,]    1    6   11   16   21   26
[2,]    2    7   12   17   22   27
[3,]    3    8   13   18   23   28
[4,]    4    9   14   19   24   29
[5,]    5   10   15   20   25   30
```

```r
# somar o TOTAL de cada coluna com apply()
apply(X, 2, sum)
```

```
[1]  15  40  65  90 115 140
```


***

- Para entendermos o que está sendo feito no código ao lado, vejamos a figura:


- ![Figura 2](./img/apply1.png)
Fonte: Datacamp

*apply(X, 2, sum)* siginifica: aplique a função *sum* sobre as colunas (ou ao longo da margem/dimensão 2) da matriz *X*.

apply() - OBSERVAÇÕES (1)
========================================================
incremental: true
<!-- class: small-code -->

- Para somas e médias das dimensões de uma matriz, o R já possui alguns atalhos, os quais inclusive foram apresentados no módulo 1:

  + rowSums(X) equivale a apply(X,1, sum);
  + rowMeans(X) equivale a apply(X,1, mean);
  + colSums(X) equivale a apply(X,2, sum);
  + colMeans(X) equivale a apply(X,2, mean);
  + rowSums(X, dims=2) equivale a apply(X,c(1,2), sum);
  + rowMeans(X, dims=2) equivale a apply(X,c(1,2), mean);

- As funções atalhos são mais rápidas que suas respectivas versões em apply. No entanto, há alguns casos em que não temos funções atalhos e fatalmente teremos que usar *apply()*;

- Há casos em que temos que criar funções *anônimas* dentro da chamada de *apply()* para executar um cálculo ou operação personalizada (daqui 2 slides !!).


apply() - OBSERVAÇÕES (2)
========================================================
incremental: true
class: small-code

- Caso você deseje especificar/passar algum parâmetro da função (*FUN*) a ser utilizada, pode proceder como no exemplo a seguir:

```r
# Construindo uma matriz 5x6
X <- matrix(1:30, nrow=5, ncol=6)

# inserindo alguns NA's
X[1,2] <- NA
X[4,5] <- NA

X
```

```
     [,1] [,2] [,3] [,4] [,5] [,6]
[1,]    1   NA   11   16   21   26
[2,]    2    7   12   17   22   27
[3,]    3    8   13   18   23   28
[4,]    4    9   14   19   NA   29
[5,]    5   10   15   20   25   30
```

```r
# somar o valor de cada coluna com apply(), agora incluindo o parâmetro na.rm= TRUE
apply(X, 2, sum, na.rm = TRUE)
```

```
[1]  15  34  65  90  91 140
```

***
- Note que o parâmetro *na.rm*, embora pertencente a função *sum()*, não é passado dentro desta, mas sim como um argumento de *apply()*. Será a função *apply()* que se encarregará de direcionar este argumento para a função *sum()*;

- Veja a diferença caso não tivessemos especificado na.rm = TRUE

```r
apply(X, 2, sum)
```

```
[1]  15  NA  65  90  NA 140
```


apply() - OBSERVAÇÕES (3)
========================================================
incremental: true
class: small-code

</br>
- E se queremos fazer uma operação específica com as linhas ou colunas da matriz, cuja função não está implementada em **R**? 
</br>
- Há duas opções: 
  + criamos a nossa própria função dentro de apply  - é o que se chama de *anonymous function*; 
  + ou criamos uma função fora da função *apply()* e depois chamamos a função criada (utilizando o nome atribuído).

- Vejamos um exemplo ...

apply() - OBSERVAÇÕES (4)
========================================================
incremental: true
class: small-code

- Considere a situação em que desejamos pegar o valor MÍNIMO de cada linha e aplicarmos uma taxa de correção de 25%, salvando os resultados num vetor:


```r
# criando matriz
set.seed(1984) # semente para reproducibilidade dos valores
M <- matrix(sample(1:1000, 30), 5)
M
```

```
     [,1] [,2] [,3] [,4] [,5] [,6]
[1,]  659  859  874   23  599  154
[2,]  437   33   12  295    4  826
[3,]  373  445  698  654  600  566
[4,]  330  824  711  906  839  493
[5,]  735  213  200  202  780  538
```

```r
# 1º caso - utlizando função anônima
apply(M, 1, function(x) min(x)*1.025)
```

```
[1]  23.575   4.100 382.325 338.250 205.000
```

```r
# 2º caso - criando uma função fora de apply()
myfun <- function(x) min(x)*1.025

apply(M, 1, myfun)
```

```
[1]  23.575   4.100 382.325 338.250 205.000
```

***

- Os resultados são exatamente os mesmos!

- Note que a criação da função anônima se dá da mesma forma em que criaríamos uma função normal, conforme visto no primeiro capítulo. 

- As únicas diferenças são: a função é criada dentro do argumento *FUN* da função da família **apply** e não atribuímos qualquer nome para a função que está sendo criada.


lapply() (1)
========================================================
incremental: true
class: small-code


- **lapply()** é uma das funções mais básicas da família *apply*;
- Ela receberá como input uma lista, aplicará uma função aos elementos desta lista, retornando também uma lista;
- Uma observação a ser feita é que, caso o objeto inputado não seja uma lista, internamente, **lapply()** forçará esse objeto a ser uma lista utilizando *as.list()*;
- Portanto, fica claro o motivo do **l** em **l***apply()*;

***
- Veja a imagem exemplificando o funcionamento de *lapply()*:

- ![Figura 3](./img/lapply.png)

Fonte: Advanced R


lapply() (2)
========================================================
incremental: true
class: small-code

</br>
- data.frames também são em sua essência uma lista (uma lista de vetores-coluna);
</br>
- Portanto, data.frames também podem ser passados como argumento de *lapply()*;
</br>
- Você pode passar também um vetor como input -- internamente *lapply()* irá transformá-lo em uma lista.

  
lapply() (3)
========================================================
incremental: true
class: small-code

- A estrutura de argumentos de *lapply()* é bem parecida com a função *apply()*;
- A diferença é que desta vez não temos o argumento *MARGIN*.

```r
args(lapply)
```

```
function (X, FUN, ...) 
NULL
```

- Veja um exemplo do uso de lapply():

```r
# criando 3 matrizes:
A <- matrix(1:9, 3)
B <- matrix(4:15, 4)
C <- matrix(rep(8:10, 2), 3)

# Criando lista de matrizes
MyList <- list(A,B,C)

# Extratraindo a primeira linha de todas as matrizes da lista `MyList`
lapply(MyList,"[", 1,) 
```

```
[[1]]
[1] 1 4 7

[[2]]
[1]  4  8 12

[[3]]
[1] 8 8
```

```r
# note como é passado o argumento da primira linha
```

<!-- Lembrá-los que isto podem ser nomes de arquivos de dados a serem lidos no computador, etc -->
<!-- FAZER UM EXERCÍCIO COM ESSA IDEIA !!! -->

***
- A figura a seguir auxilia na compreensão do exemplo:

- ![Figura 3](./img/lapply2.png)

Fonte: Datacamp.

- Para extrair somente o segundo elemento da primeira linha de cada matriz, por exemplo, passaríamos o índice da coluna além da linha:


```r
lapply(MyList,"[", 1,2) 
```

```
[[1]]
[1] 4

[[2]]
[1] 8

[[3]]
[1] 8
```


lapply() (4)
========================================================
incremental: true
class: small-code

- Vejamos como ficaria lapply, utilizando uma função anônima. Vamos tentar calcular a média de todas as primeiras colunas das matrizes A, B, C do exemplo anterior:


```r
lapply(MyList, function(x) mean(x[,1],na.rm=TRUE))
```

```
[[1]]
[1] 2

[[2]]
[1] 5.5

[[3]]
[1] 9
```

```r
# OU
lapply(MyList, function(x, ...) mean(x[,1]), na.rm=TRUE)
```

```
[[1]]
[1] 2

[[2]]
[1] 5.5

[[3]]
[1] 9
```

```r
# perceba o uso de ... e o posicionamento de na.rm nas duas formas equivalentes acima
```


***

```r
# OU AINDA:
# de uma maneira menos eficiente (porque teríamos que criar um vetor, ocupando mais memória) e mais complicada porque teríamos que usar 2 vezes lapply()

out <- lapply(MyList, "[", ,1) 
# esse só funciona com lapply. Com sapply não dá certo por causa da 3ª vírgula, me parece. Como sapply() é um wrapper para lapply, na hora de ela tentar mandar para lapply, ela entende a 3ª vírgula como se fosse um agumento e não um parâmetro.

lapply(out, mean)
```

```
[[1]]
[1] 2

[[2]]
[1] 5.5

[[3]]
[1] 9
```

```r
# mean aplicado sobre uma lista não funciona
# ... então não adiantaria usar o código abaixo:
# mean(out, na.rm = TRUE)
```
  

lapply() - Looping patterns (1)
========================================================
incremental: true
class: small-code

- Retomando aos *looping patterns* que apresentamos na seção 2.1.1, esses mesmos padrões de construção de *loops* podem ser utilizados com as funções da família *apply*.

- Com **lapply** teríamos o seguinte:

  + 1) loop sobre os elementos: **lapply(MyList, function(x) {})**
  + 2) loop sobre os índices numéricos: **lapply(1:length(MyList), function(i) {})** ou **lapply(seq_along(MyList), function(i) {})** 
  + 3) loop sobre os nomes: **lapply(names(MyList), function(nm) {})**

- Embora no caso dos *loops* havia a recomendação de que se opte pelo caso 2, na família *apply* as estruturas mais comuns serão tanto a 1 quanto a 2, dependendo do problema que se quer resolver.

<!-- DAR UM EXEMPO PRA ELES DURANTE A AULA USANDO O CASO 2 - PODE SER PARA ACESSAR UM ELEMENTO ESPECÍFICO DE CADA DATA.FRAME -->


lapply() - Looping patterns (2)
========================================================
incremental: true
class: small-code

- Veja um exemplo de utilização do caso 2, em que queremos acessar um elemento específico de cada objeto da lista, ou seja, de cada data.frame dentro da lista:


```r
lapply(seq_along(MyList), function(i){
  MyList[[i]][1,2]
})
```

```
[[1]]
[1] 4

[[2]]
[1] 8

[[3]]
[1] 8
```

- O fato de utilizarmos os índices para iteração, nos permite maior controle sobre o que deseamos fazer com os dados.

- OBS: Note que em todos os casos que usamos **lapply()**,  os objetos retornados eram listas. Se quiser confirmar repita os códigos anteriores salvando as saídas e depois aplicando *str()*.

sapply() (1)
========================================================
incremental: true
class: small-code

- A função **sapply()** funciona como *lapply()* com a diferença de que ela tenta **simplificar** ( daí o **s** em *sapply*) o resultado para o objeto mais elementar quanto for possível - que é um vetor.

- Em termos gerais, temos normalmente as seguintes situações:

  + se o resultado for uma lista em que todos os elementos tem tamanho 1, então um vetor é retornado;
  + se o resultado é uma lista onde todos os elementos são vetores de mesmo tamanho (e > 1), então uma matriz é retornada;
  + se *sapply()* não souber o que fazer, então uma lista será retornada


sapply() (2)
========================================================
incremental: true
class: small-code

- Vamos re-executar alguns dos códigos anteriores, utilizando *sapply()* desta vez:


```r
sapply(MyList, function(x) mean(x[,1],na.rm=TRUE))
```

```
[1] 2.0 5.5 9.0
```

```r
# OU
sapply(MyList, function(x, ...) mean(x[,1]), na.rm=TRUE)
```

```
[1] 2.0 5.5 9.0
```

```r
# perceba o uso de ... e o posicionamento de na.rm nas duas formas equivalentes acima


sapply(seq_along(MyList), function(i){
  MyList[[i]][1,2]
})
```

```
[1] 4 8 8
```


vapply() (1)
========================================================
incremental: true
class: small-code

- **vapply()** é muito similar a *sapply()*, no sentido de que também busca produzir um vetor como saída;
- A diferença é: *sapply()* tenta 'advinhar' qual seria o output, ao passo que **vapply()** utiliza um argumento adicional que serve para especificar o **tipo** e **tamanho** de saída/ output que esperamos após aplicar a função nos dados;
- **vapply** somente retornará um vetor (ou array), daí o **v** de **vapply()**;

***
- Hadley Wickham sugere utilizar *vapply()* (ao invés de sapply()):
  + ela "reclamará" sempre que houver algum erro no tipo de input; 
  + *sapply()*, por sua vez pode falhar de modo "silencioso" sem indicar algo de errado que esteja ocorrendo.
- Para programação de forma interativa (quando sabemos o tipo de dado que está sendo inputado) *sapply()* pode ser utilizada sem problemas;
- No entanto, quando vamos escrever programas longos que serão executados de forma automática por outros usuários, onde a função da família *apply* seria executada dentro de outras funções, a opção mais segura seria *vapply()*.

vapply() (2)
========================================================
incremental: true
class: small-code

- Os argumentos de **vapply()** são:

```r
args(vapply)
```

```
function (X, FUN, FUN.VALUE, ..., USE.NAMES = TRUE) 
NULL
```

- Os diferenciais aqui são:
  + *FUN.VALUE*: onde se especifica o tipo e tamanho do vetor de retorno;
  + *USE.NAMES*: caso seu objeto de entrada já possua nomes para os elementos, eles podem (TRUE) ou não ser utilizados (FALSE).

- O principal argumento *FUN.VALUE*, que é o que nos permite restringir a saída de *vapply()* e identificar possíveis erros antes que aconteçam - digamos - em um ambiente de produção.


vapply() (3)
========================================================
incremental: true
class: small-code

Comparando *vapply()* e *sapply()*... 

- No exemplo a seguir, vamos criar obter os tipos de colunas de um data.frame simulado. Primeiro o teste com *sapply()*:

```r
# criando um data.frame
df <- data.frame(x = 1:10, y = letters[1:10])

# aplicando sapply
sapply(df, class)
```

```
        x         y 
"integer"  "factor" 
```

***
- E agora, o teste com *vapply()*. Note que estamos passando o tipo e tamanho do vetor que esperamos ter como resultado:


```r
# aplicando vapply
vapply(df, class, character(1))
```

```
        x         y 
"integer"  "factor" 
```

- Veja o que ocorre caso tivéssemos especificado erroneamente o tipo ou o tamanho do vetor de saída:

```r
# errando no tipo:
vapply(df, class, numeric(1))

# errando no tamanho:
vapply(df, class, character()) # R vai entender como tamanho zero.
```


vapply() (4)
========================================================
incremental: true
class: small-code

- Para visualizarmos efetivamente a importância de usar *vapply()* no sentido de controlarmos o input dos dados e detectarmos possíveis erros, vejamos o seguinte exemplo: verificar as classes das colunas d eum data.frame:

```r
# primeiro com sapply()
# criando um data.frame com uma coluna de datas
df2 <- data.frame(x = 1:10, y = Sys.time() + 1:10)

sapply(df2, class)
```

```
$x
[1] "integer"

$y
[1] "POSIXct" "POSIXt" 
```

- E depois com *vapply()*:

```r
vapply(df2, class, character(1))
```

```
Error in vapply(df2, class, character(1)): values must be length 1,
 but FUN(X[[2]]) result is length 2
```

***
- Ao usar *sapply()*, esperávamos classes com um único nome (um único elemento por coluna); 
  + No entanto, a coluna de datas possui um nome "duplo" e sapply acaba separando a segunda parte do nome, retornando um vetor com duas entradas quando esperávamos apenas uma;
  + Isso pode nos levar a um engano de achar que havia uma terceira coluna no data.frame, o que não é verdade.

- Por outro lado, *vapply()* retorna um erro; 
  + Isso é bom, pois mostra que estávamos enganados ao esperar apenas vetores de caracteres com tamanho 1 como  a classe de cada coluna;
  + Nos força a conhecer exatamente o que deve ser retornado em cada caso. 


vapply() (5)
========================================================
incremental: true
class: small-code

Observações:

- Não haveria uma forma de ajustar **vapply()** para que ela "aceitasse" vetores de caracteres com tamanhos diferentes na saída;
- O que poderíamos fazer no caso era construir uma função que fizessse a checagem do tamanho dos nomes das classes após aplicarmos uma *sapply()*;
- A questão é que: só descobrimos que havia classes de colunas com nomes compostos após utilizar *vapply()*. Por isso ela é mais segura.

<!-- https://stackoverflow.com/questions/42817309/can-vapply-be-used-with-a-variable-length-fun-value# -->
<!-- https://stackoverflow.com/questions/12339650/why-is-vapply-safer-than-sapply -->


vapply() (6)
========================================================
incremental: true
class: small-code

sapply vs vapply:

- Por fim, cabe destacar que *sapply()* é um *wrapper* (ou função envelope) de *lapply()*, que apenas transforma o output em um vetor ou matriz no final. 
- *vapply()*, por sua vez, é uma implementação diferenciada de *lapply()*, ou seja é uma função da família *apply* com código diferenciado;
- A similaridade entre ambas estaria apenas no output: tentativa de fazê-lo como um vetor/array.

![Figura 1](./img/sapply-vapply.png)

Fonte: Advanced R.


mapply() (1)
========================================================
incremental: true
class: small-code


- **mapply()** é uma forma multivariada de *lapply()* que aplica a função desejada de forma paralela sobre um conjunto de argumentos;

- Se você passar dois vetores como argumentos para uma função, na primeira rodada/ chamada, **mapply()** vai olhar para os primeiros elementos de ambos os vetores na primeira chamada, depois para os segundos, etc.

- Com *lapply()* (e suas derivadas), somente um argumento da função pode variar. Os outros, obrigatoriamente, devem ser fixos. Para alguns casos, isso pode ser um problema. 

- Note que a função a ser aplicada agora será nossso primeiro argumento:

```r
args(mapply)
```

```
function (FUN, ..., MoreArgs = NULL, SIMPLIFY = TRUE, USE.NAMES = TRUE) 
NULL
```

- O argumento ao qual devemos nos atentar aqui é *MoreArgs*, que é uma lista de outros argumentos a serem passados para *FUN*;
- *simplify=TRUE* é só para tentar simplificar o resultado, tentando transformá-lo em um vetor.


mapply() (2)
========================================================
incremental: true
class: small-code


- Pense em como faríamos para somar todos os primeiros elementos de objetos de duas listas diferentes, todos os elementos na segunda posição, todos os terceiros e assim por diante? A saída seria utilizar **mapply()**: 


```r
# criando duas listas com sequências de vetores:
l1 <- list(a = c(1:5), b = c(6:10))

l2 <- list(c = c(11:20), d = c(21:30))

mapply(sum, l1$a, l1$b, l2$c, l2$d)
```

```
 [1] 39 43 47 51 55 49 53 57 61 65
```

```r
#Map(sum, l1$a, l1$b, l2$c, l2$d)
# Reduce('+', c(l1, l2))
```

- O que está acontecendo acima é que estamos passando múltiplos argumentos a serem somados posição por posição pela função *sum()*;

- Se passarmos os vetores inteiros para a função sum() sem mapply() todos os elementos seriam somados de uma vez;

<!-- - Enquanto, normalmente, a função *sum()* só poderia receber um único vetor para o argumento *X*, ao usarmos **mapply()**, podemos passar vários vetores de modo que ela some as resectivas posições de forma **paralela**: *l1$a[1] + l1$b[1] + l2$c[1] + l2$d[1] = 39* e assim por diante. ERRADO NA APOSTILA -->

***
- Ao usarmos **mapply()**, podemos passar vários vetores para função sum() de modo que ela some as resectivas posições de forma **paralela**: *l1$a[1] + l1$b[1] + l2$c[1] + l2$d[1] = 39* e assim por diante.

- **mapply()** nos permite, então, vetorizar argumentos para uma função que normalmente não funcionaria de forma **vetorizada**;

- Com *lapply()* isso não seria possível, porque os argumentos adicionais são passados para todas as chamadas. Com *mapply()* é como se conseguíssemos segmentar conjuntos de argumentos para cada chamada.

  
mapply() (3)
========================================================
incremental: false
class: small-code

- Outro exemplo: precisamos criar uma lista onde uma sequência de números de 1 até 10 são repetidos justamente o valor do próprio número: 1 deve ser repetido uma vez, 2 deve ser repetido duas vezes e assim por diante. A primeira ideia seria usar *rep()*. Ela até funcionaria, mas o output não seria exatamente o que estamos esperando.


```r
out <- rep(1:10, 1:10)
out
```

```
 [1]  1  2  2  3  3  3  4  4  4  4  5  5  5  5  5  6  6  6  6  6  6  7  7
[24]  7  7  7  7  7  8  8  8  8  8  8  8  8  9  9  9  9  9  9  9  9  9 10
[47] 10 10 10 10 10 10 10 10 10
```

```r
str(out)
```

```
 int [1:55] 1 2 2 3 3 3 4 4 4 4 ...
```

- rep() retornou um grande vetor, ao passo que precisávamos de elementos separados como objetos de uma lista;

- Com **mapply()** isso ocorreria diretamente:

```r
mapply(rep, 1:10, 1:10)
```

```
[[1]]
[1] 1

[[2]]
[1] 2 2

[[3]]
[1] 3 3 3

[[4]]
[1] 4 4 4 4

[[5]]
[1] 5 5 5 5 5

[[6]]
[1] 6 6 6 6 6 6

[[7]]
[1] 7 7 7 7 7 7 7

[[8]]
[1] 8 8 8 8 8 8 8 8

[[9]]
[1] 9 9 9 9 9 9 9 9 9

[[10]]
 [1] 10 10 10 10 10 10 10 10 10 10
```

```r
# Map(rep, 1:10, 1:10)

#Reduce(rep, c(1:10, 1:10))
```


mapply() (4)
========================================================
incremental: false
class: small-code

Observações:

- Há uma função equivalente a *mapply()* que sempre retorna listas ao invés de tentar simplificar a saída em um vetor. É a função **Map()**. Ela funciona exatamente da mesma forma que *mapply()*. Nos exemplos anteriores, tente substituir *mapply()* por **Map()**.


tapply() (1)
========================================================
incremental: false
class: small-code

- **tapply()** sumariza um vetor a partir de outro vetor;
- Esta função é usada para aplicar outra função a *subsets* (subconjuntos) dos dados.


```r
args(tapply)
```

```
function (X, INDEX, FUN = NULL, ..., default = NA, simplify = TRUE) 
NULL
```

- *X* é um vetor;
- *INDEX* é um fator ou lista de fatores. Se não for fator, será coercido como tal;
- *FUN* é a função a ser aplicada;
- *simplify* indica se o resultado dee ou não ser simplificado.


tapply() (2)
========================================================
incremental: false
class: small-code

Um caso muito comum do uso de **tapply()** é quando queremos encontrar quantidades resumo de grupos dos nossos dados: 

```r
set.seed(123)
banco <- as.factor(sample( rep(c("BB", "CEF"), 100), 20) )

saldo <- sample(1:1000, 20) 

df <- data.frame(saldo, banco)

# média por banco:
tapply(df$saldo, df$banco, mean)
```

```
      BB      CEF 
565.2500 577.4167 
```

```r
# range (intervalo) por banco
tapply(df$saldo, df$banco, range)
```

```
$BB
[1] 228 705

$CEF
[1]  25 992
```


aggregate()
========================================================
incremental: false
class: small-code


- Há uma função parecida com *tapply()* que permite apresentar os resultados no formato de uma tabela/ data.frame;
- Ela nos permite aplicar funções de sumarização dos dados quando temos grupos;
- Você pode aplicar a sumarização a mais de uma variável ao mesmo tempo:

```r
# criação de um novo vetor:
set.seed(123)
dias <- sample(1:360, 20)
df$dias <- dias

aggregate(df[,c("saldo", "dias")], by = df['banco'], mean)
```

```
  banco    saldo     dias
1    BB 565.2500 245.5000
2   CEF 577.4167 164.5833
```

```r
# como o data.frame eh em sua essência uma lista, seus elementos são as colunas. Então ao indexarmos smente pelas colunas, sem indicar as vírgulas, ele automaticamente traz um data.frame (ou seja, uma lista). Dessa forma, conseguimos manter os nomes das variáveis !!!
# str(df['banco'])

aggregate(df[,c("saldo", "dias")], by = df['banco'], range)
```

```
  banco saldo.1 saldo.2 dias.1 dias.2
1    BB     228     705     17    357
2   CEF      25     992     15    356
```

***
- Se não usarmos a forma natural de lista no argumento *by*, teremos que forçar o resultado como uma lista usando *list()*;

- Teremos o mesmo resultado, mas a diferença é que não teremos o nome da coluna de indexação trazido automaticamente como no exemplo acima:

```r
aggregate(df[,c("saldo", "dias")], by = list(df$banco), mean)
```

```
  Group.1    saldo     dias
1      BB 565.2500 245.5000
2     CEF 577.4167 164.5833
```

```r
aggregate(df[,c("saldo", "dias")], by = list(df$banco), range)
```

```
  Group.1 saldo.1 saldo.2 dias.1 dias.2
1      BB     228     705     17    357
2     CEF      25     992     15    356
```

  
FIM
========================================================
incremental: false
class: small-code

OBRIGADO!!
