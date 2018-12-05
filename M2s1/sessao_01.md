Análise de Dados em R (FNDE) - Módulo 2
Sessão 1 - Introdução ao Curso e Criação de Funções
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



Introdução
========================================================
incremental: true

No Módulo 1 vimos...
<br />
- o funcionamento básico/ estrutura do R;
- como obter ajuda dentro e fora do R;

<!-- mostrar meu stackoverflow para eles -->

- como criar objetos;
- técnicas de programação (condicional, etc);
- como importar/ler, manipular e escrever arquivos de dados;
- como confeccionar gráficos básicos;


***
</br>
</br>
- O que vocês acharam?
  + fácil? difícil? interessante? arghhh?

Objetivos
========================================================
incremental: true

- Criação de Funções;
- Loops;
- Família apply;
- Manipulação de strings e expressões regulares;
- Manipulação de datas;
- Manipulação de dados com dplyr;
- Acesso a dados em servidores SQL;
- Visualização com ggplot2.


O curso
========================================================
incremental: true

- Tudo estará na apostila que foi feita totalmente em R!
<!-- dois ## são os returns -->

- Este módulo é uma continuidade do anterior;

- Divide -se em 2 partes:

  + Base R & Tidyverse
  
- Primeiro faremos uma continuação "linear" do que aprendemos no Módulo1;

- Depois veremos um outro paradigma de programação em $\textbf{R}$:

![alt text](./img/tidyverse2.png)

***
Uma breve noção sobre o Tidyverse...

![alt text](./img/tidyverse_stickers3.jpg)

<!-- Já na parte 2, daremos início a introdução do Tidyverse, que é uma coleção de pacotes que visa tornar a Análise de Dados em R mais fácil e consistente. -->

<!-- Neste módulo, além de novos conceitos relacionados ao R base, conheceremos uma coleção de pacotes do R que torna ainda mais fácil a Análise de Dados: o Tidyverse. Esta coleção é desenvolvida por diversos profissionais de todo o mundo que estão na vanguarda da linguagem R. O Tidyverse reúne diversos pacotes, que atuam desde a leitura e manipulação de dados até a visualização e execução de modelos estatísticos. Nosso foco será na leitura, manipulação e visualização de dados. -->


RStudio::conf 2018 e a importância do campo Data Science
========================================================
incremental: true

<!-- Explicar o porque do Tidyverse, o que é o Tidyverse, falar sobre a Conferência -->

<!-- Dizer o quanto é importante o FNDE/ empresas estimularem isso -->
<!-- E o quanto DataScience hj te proporciona - é o futuro -->

<!-- pessoal lá fora vive as mesmas dificuldades que nós no FNDE -->

<!-- O gasto não é nem próximo ao retorno que o órgão terá: -->
<!-- - formação de mais de 20 pessoas com a vanguarda do conhecimento da linguagem -->
<!-- - FisFa (falar que faltam só ~ 178 milhões 990 mil ) para eu e o FNDE ficarmos quites (sem contar que não contratou empresa de TI) -->
<!-- - Utilizarei agora para resolver o problema (gargalo gigantesco da prestação de contas) -->
<!-- Falar que os problemas que os caras tão tendo lá é o mesmo que estamos tendo aqui -->
<!-- Falar de como é difícil tentar mudar sozinho a mentalidade da autarquia e que eu conto com a ajuda deles agora -->


</br>
![alt text](./img/beautiful_sandiego_s.png)

![alt text](./img/conf1_s.png)

***
</br>
![alt text](./img/me_hadley1_s.png)

![alt text](./img/me_hadley2_s.png)


Desenferrujando...
========================================================
incremental: true

<br />
- crie 3 vetores (com o mesmo tamanho): numérico, de strings/caracteres; lógico;
</br>
</br>
- crie uma matriz numérica;
</br>
</br>
- crie um data frame utilizando os vetores criados e atribua um nome para cada coluna;
</br>
</br>
- crie uma lista contendo todos os objetos anteriores;


Desenferrujando - meu exemplo
========================================================
incremental: false
class: small-code


```r
# vetores
(v1 <- 1:3)
```

```
[1] 1 2 3
```

```r
(v2 <- c("R", "Fnde", "Allan"))
```

```
[1] "R"     "Fnde"  "Allan"
```

```r
(v3 <- c(TRUE, FALSE, T))
```

```
[1]  TRUE FALSE  TRUE
```

```r
# matriz
(m <- matrix(1:9, nrow=3, byrow=TRUE))
```

```
     [,1] [,2] [,3]
[1,]    1    2    3
[2,]    4    5    6
[3,]    7    8    9
```

***

```r
# data.frame
(df <- data.frame("col1" = v1, "col2" = v2, "col3" = v3))
```

```
  col1  col2  col3
1    1     R  TRUE
2    2  Fnde FALSE
3    3 Allan  TRUE
```

```r
# ou
# df <- data.frame(v1, v2, v3)
# colnames(df) <- paste("col", 1:3, sep = "")

# lista
(minha_lista <- list(v1, v2, v3, m, df))
```

```
[[1]]
[1] 1 2 3

[[2]]
[1] "R"     "Fnde"  "Allan"

[[3]]
[1]  TRUE FALSE  TRUE

[[4]]
     [,1] [,2] [,3]
[1,]    1    2    3
[2,]    4    5    6
[3,]    7    8    9

[[5]]
  col1  col2  col3
1    1     R  TRUE
2    2  Fnde FALSE
3    3 Allan  TRUE
```


Algo está faltando
========================================================
incremental: true
class: small-code

No módulo 1, vimos que $\textbf{R}$ é uma linguagem essencialmente funcional (Hadley Wickham).

- Então, está faltando um tipo de objeto muito importante que também vimos no Módulo 1...

  > FUNÇÕES
  

Criação de funções (1)
========================================================
incremental: true
class: small-code

- Funções também são um objeto;
- Vimos que, grosso modo, as funções tem a seguinte estrutura:

  $\textbf{função(argumentos)}$
  
- lembre-se que, para criar os vetores e demais objetos anteriormente, você utilizou as funções: c(), data.frame(), matrix(), list();

![alt text](./img/chambers_quote.png)

Fonte: Advanced R

- O que faltou aprendermos no Módulo 1 foi: como criarmos nossas próprias funções!!


Criação de funções (2)
========================================================
incremental: true


- Assim como objetos, funções também podem receber um nome ao serem criadas.

- Veja o exemplo abaixo:


```r
myfun <- function(argumentos){
  # executar algum comando
  # (...)
  return(objeto_a_retornar)
}
```

- quando atribuímos um nome, nossa função automaticamente é salva no Global Environment.


Criação de funções (3)
========================================================
incremental: true
class: small-code

- Ao atribuirmos nomes e salvarmos nossa função como um objeto, podemos:
  + passar funções como argumentos de outras funções;
  + aninhar funções uma dentro da outra.
  
- Veremos alguns exemplos disso mais a frente - vocês já até fizeram algo parecido no Módulo 1.


Criação de funções (4)
========================================================
incremental: true


```r
myfun <- function(argumentos){
  # executar algum comando
  # (...)
  return(objeto_a_retornar)
}
```

***
- Observação Importante:

  + se não especificarmos nada no $\textbf{return()}$, nossa função retornará o último objeto que foi avaliado no "corpo" da função.
  

Criação de funções (5)
========================================================
class: small-code

- Quando criamos uma função, automaticamente estamos definindo um $\textbf{environment}$ separado.

  + as variáveis que forem criadas dentro da função, existirão somente no environment da função;


![alt text](./img/envsearch_path.png)

Fonte: Advanced R


Criação de funções (6)
========================================================
incremental: true
class: small-code

- Quando temos funções aninhadas, uma forma de "carregar" informação do environment de uma função para um environment "superior" é justamente pelo $\textbf{return()}$
  
- OBSERVAÇÃO:

  + as funções no R, por definição retornam somente um único objeto por vez!
  + se você quiser retornar mais de um objeto (ex: 3 vetores, 1 data frame e uma matriz), deverá utilizar uma lista !
  


Criação de funções (7)
========================================================
incremental: true
class: small-code

Exemplo - criação de função e retorno de múltiplos objetos:


```r
myfun2 <- function(n_rep){
  
  df1 <- data.frame(col1 = rep("R", n_rep), col2 = rep(2018, n_rep))
  df2 <- data.frame(col1 = rep("R", n_rep), col2 = rep("FNDE", n_rep))  
  
  return(list(df1,df2))
}

myfun2(n_rep=3)
```

```
[[1]]
  col1 col2
1    R 2018
2    R 2018
3    R 2018

[[2]]
  col1 col2
1    R FNDE
2    R FNDE
3    R FNDE
```

- Note como o argumeto ``n_rep`` é passado e utilizado dentro da função;


Criação de funções (7)
========================================================
incremental: true

- Experimente remover a linha do $\textbf{return()}$...


```r
myfun2 <- function(n_rep){
  
  df1 <- data.frame(col1 = rep("R", n_rep), col2 = rep(2018, n_rep))
  df2 <- data.frame(col1 = rep("R", n_rep), col2 = rep("FNDE", n_rep))
}

res <- myfun2(n_rep=3) # atribuindo a saída da função a um objeto
# sem return(), ela não imprime no console

res
```

```
  col1 col2
1    R FNDE
2    R FNDE
3    R FNDE
```


Criação de funções (8)
========================================================
incremental: true
class: small-code

- O fato de o R ser de código aberto traz uma grande vantagem: as coisas não são uma caixa preta!

- Você consegue investigar (e também alterar!) o funcionamento de tudo (todas as funções/ programas);

- Para ver o código de uma função já existente no R, você precisa somente digitar o nome dela no console;

- Vamos ver como funciona a função scan(), por exemplo:

```r
scan
```

```
function (file = "", what = double(), nmax = -1L, n = -1L, sep = "", 
    quote = if (identical(sep, "\n")) "" else "'\"", dec = ".", 
    skip = 0L, nlines = 0L, na.strings = "NA", flush = FALSE, 
    fill = FALSE, strip.white = FALSE, quiet = FALSE, blank.lines.skip = TRUE, 
    multi.line = TRUE, comment.char = "", allowEscapes = FALSE, 
    fileEncoding = "", encoding = "unknown", text, skipNul = FALSE) 
{
    na.strings <- as.character(na.strings)
    if (!missing(n)) {
        if (missing(nmax)) 
            nmax <- n/pmax(length(what), 1L)
        else stop("either specify 'nmax' or 'n', but not both.")
    }
    if (missing(file) && !missing(text)) {
        file <- textConnection(text, encoding = "UTF-8")
        encoding <- "UTF-8"
        on.exit(close(file))
    }
    if (is.character(file)) 
        if (file == "") 
            file <- stdin()
        else {
            file <- if (nzchar(fileEncoding)) 
                file(file, "r", encoding = fileEncoding)
            else file(file, "r")
            on.exit(close(file))
        }
    if (!inherits(file, "connection")) 
        stop("'file' must be a character string or connection")
    .Internal(scan(file, what, nmax, sep, dec, quote, skip, nlines, 
        na.strings, flush, fill, strip.white, quiet, blank.lines.skip, 
        multi.line, comment.char, allowEscapes, encoding, skipNul))
}
<bytecode: 0x37df898>
<environment: namespace:base>
```



Criação de funções (9)
========================================================
incremental: true

Vamos resolver agora um problema de ordem prática através da criação de uma nova função e modificação de uma já existente.

- Lembra-se do parâmetro $\textbf{stringsAsFactors}$ da função $\textbf{read.table}$?

- a recomendação era que sempre fosse "setado" como FALSE;

- Para não termos que ficar digitando ``stringsAsFactors=FALSE`` toda vez que formos ler um arquivo .txt, .csv, etc, vamos criar uma função nossa que já traz automaticamente este parâmetro setado como false:


```r
my_read.fun <- function(file, stringsAsFactors=FALSE, ...){
  read.table(file, stringsAsFactors = stringsAsFactors, ...)  
}
```


Criação de funções (10)
========================================================
incremental: true
class: small-code


```r
my_read.fun <- function(file, stringsAsFactors=FALSE, ...){
  read.table(file, stringsAsFactors = stringsAsFactors, ...)  
}
```

> OBSERVAÇÕES:

- não utilizamos return(), então o úlitmo objeto avaliado será retornado - que é nosso arquivo de dados lido;

- esse arquivo é lido pela read.table chamada de dentro da nossa my_read.fun

- nossa nova função recebe 3 parâmetros, que por consequência serão passados para read.table de dentro;

***
- file indica a localização do nosso arquivo, igual em read.table;

- stringsAsFactors já vai de "fábrica" setado como FALSE - que é nosso objetivo;

- os ``...`` indicam que o usuário poderá, caso deseje, ainda passar outros parâmetros (que serão aproveitados em read.table)

  + lembre-se que read.table pode receber outros parâmetros opcionais como header, sep, dec, etc.

  + o argumento ... também é útil quando não podemos definir de antemão quais e quantos parâmetros. É o caso da função paste(), por exemplo.


Criação de funções (11)
========================================================
incremental: true

Vamos olhar agora para os argumentos dentro da read.table:

```r
my_read.fun <- function(file, stringsAsFactors=FALSE, ...){
  read.table(file, stringsAsFactors = stringsAsFactors, ...)  
}
```

- a indicação $\textbf{stringsAsFactors=stringsAsFactors}$ serve para deixar ao usuário a opção de mudar o padrão do parâmetro stringsAsFactors para TRUE ao utilizar my_read.fun

- caso isso não seja feito, automaticamente será buscado o default de sringsAsFactors em read.table, que é TRUE e sua função será inócua;

- o melhor é sempre deixarmos nossas funções mais flexíveis quanto seja possível para o usuário, mesmo quando somente nós iremos utilizá-las.


Criação de funções (12)
========================================================
incremental: true

- teste a função num arquivo real utilizado no Módulo 1 

```r
#df.loc <- file.choose()
df.loc <- "/home/allan/Documents/teaching/FNDE/cursoR_FNDE_mod1/sessao_06/REPASSE122014.csv"

df <- my_read.fun(df.loc, sep=";", header=TRUE)
```


Criação de funções (13)
========================================================
incremental: true
class: small-code

Também teste removendo stringsAsFactors=stringsAsFactors de dentro de read.table:


```r
## sem stringsAsFactors=stringsAsFactors

# 1)
my_read.fun <- function(file, stringsAsFactors=FALSE, ...){
  read.table(file, ...)  
} # não obedece e pega o default de read.table() que eh TRUE
df <- my_read.fun(df.loc, sep=";", header=TRUE,)
str(df)
```

```
'data.frame':	41731 obs. of  7 variables:
 $ CANDIDATO            : int  481452 15011208 7061341 4223578 9208454 5140868 5242874 13084300 7007404 9073875 ...
 $ CONTRATO             : Factor w/ 1891 levels "","1,00016E+17",..: 980 1307 678 1755 1876 294 255 1460 186 465 ...
 $ DT_VENCIMENTO_EXTRATO: Factor w/ 580 levels "","05/01/2003",..: 242 261 201 133 454 493 115 324 470 406 ...
 $ DT_BASE_INADIMPLENCIA: Factor w/ 580 levels "","03/02/2006",..: 311 262 225 137 372 490 191 300 538 395 ...
 $ VR_DIVIDA            : Factor w/ 39957 levels "10.001,20","10.001,22",..: 9179 39441 17942 34375 36409 8863 14531 26022 21706 36380 ...
 $ PERC_RISCO           : int  20 25 20 20 20 20 20 25 20 20 ...
 $ VR_REPASSE           : Factor w/ 38545 levels "0,81","0,83",..: 19568 14274 28570 5454 7317 19245 25021 837 32274 7294 ...
```

***

```r
#2)
my_read.fun <- function(file, ...){
  read.table(file, stringsAsFactors=FALSE,...)  
} # INFLEXÍVEL - não há opção de mudar para TRUE!
df <- my_read.fun(df.loc, sep=";", header=TRUE,  stringsAsFactors = TRUE)
```

```
Error in read.table(file, stringsAsFactors = FALSE, ...): formal argument "stringsAsFactors" matched by multiple actual arguments
```

```r
# Erro!

# por isso o correto eh a forma com stringsasFactors=stringsAsFactors
```


Criação de funções (14)
========================================================
class: small-code

Teste agora sem ... 

```r
# 1)
# sem ... na my_read.fun
my_read.fun <- function(file, stringsAsFactors=FALSE){
  read.table(file, stringsAsFactors=stringsAsFactors, ...)  
} # reclama de parâmetros inexistentes

df <- my_read.fun(df.loc, sep=";", header=TRUE)
```

```
Error in my_read.fun(df.loc, sep = ";", header = TRUE): unused arguments (sep = ";", header = TRUE)
```

***

```r
# 2)
# sem ... na read.table
my_read.fun <- function(file, stringsAsFactors=FALSE, ...){
  read.table(file, stringsAsFactors=stringsAsFactors)  
} # erro de leitura
df <- my_read.fun(df.loc, sep=";", header=TRUE)
```

```
Error in scan(file = file, what = what, sep = sep, quote = quote, dec = dec, : line 2 did not have 4 elements
```

```r
# 3)
# sem ... nas duas
my_read.fun <- function(file, stringsAsFactors=FALSE){
  read.table(file, stringsAsFactors=stringsAsFactors)  
}
#df <- my_read.fun(df.loc) # erro de leitura
df <- my_read.fun(df.loc, sep=";", header=TRUE) # erro nos parâmetros
```

```
Error in my_read.fun(df.loc, sep = ";", header = TRUE): unused arguments (sep = ";", header = TRUE)
```


Criação de funções (15)
========================================================

Se você quiser editar/ criar uma função fora do seu script para melhor organização do código, utilize a função $\textbf{edit()}$. Será aberto um editor fora do arquivo de script.

- Se for uma função nova:

```r
newfun <- edit(function(){})
```
<!-- Erro na apostila - saiu uma chave errada }-->

- Se for uma função já existente:

```r
my_read.fun <- edit(my_read.fun)
```
<!-- Erro na apostila -->


Partes de uma função - arguments
========================================================
incremental: false
class: small-code

Se quiser identificar quais são os argumentos de uma função, para que não tenha que olhar todo o help, você pode utilizar:

```r
args(my_read.fun)
```

```
function (file, stringsAsFactors = FALSE) 
NULL
```

ou


```r
formals(my_read.fun)
```

```
$file


$stringsAsFactors
[1] FALSE
```

Obs: 
  + O retorno de formals() normalmente é uma lista, ao passo que o de args() seria do tipo função(argumentos); 
  + uma outra diferença é que quando forem funções primitivas do R - ou seja, funções que estão implementadas em C ou Fortran como sum(), c(), etc - você não terá qualquer retorno de formals().


Partes de uma função - body e environment
========================================================
incremental: false

Para identificar o códio do “corpo” da função use:


```r
body(my_read.fun)
```

```
{
    read.table(file, stringsAsFactors = stringsAsFactors)
}
```

Para identificar o environment de onde a função é chamada:

```r
environment(my_read.fun)
```

```
<environment: R_GlobalEnv>
```


Funções - Arguments Matching (1)
========================================================
incremental: false
class: small-code

- O R tem um processo inteligente de argument macthing - por isso, muitas vezes não precisamos especificar o nome do argumento que estamos passando em uma função!

- O R tenta “casar” os argumentos passados utilizando a seguinte sequência:
  + correspondência exata para nomes de argumentos (exact matching for named arguments);
  + correspondência parcial para nomes de argumentos (partial matching);
  + correspondência posicional de argumentos (positional matching);


<!-- Funções - Arguments Matching (2) -->
<!-- ======================================================== -->
<!-- incremental: false -->
<!-- class: small-code -->

<!-- ```{r} -->
<!-- # criando um vetor: -->
<!-- vetor1 <- c(1:20) -->
<!-- ``` -->

<!-- O método de arguments matching do R faz com que todos os códigos a seguir sejam equivalentes: -->

<!-- **** -->
<!-- ```{r} -->
<!-- sum(vetor1) #positional -->

<!-- sum(x=vetor1) #exact -->

<!-- sum(x=vetor1, na.rm=FALSE) #exact -->

<!-- sum(na.rm=FALSE, x=vetor1) #exact -->

<!-- sum(na=FALSE, vetor1) #partial & positional (descobriu um que não era o primeiro, o próximo é lido como x=) -->

<!-- sum(na.rm=FALSE, vetor1) #exact & positional -->
<!-- ``` -->

<!-- NAO PODEMOS FAZER PARTIAL MATCHING COM A FUNÇÃO SUM, PQ ELE EH UMA FUNÇÃO QUE UTILIZA O ARGUMENTO ... E PORTANTO SOH ACEITA O EXACT MATCHING !!!! -->

Funções - Arguments Matching (2)
========================================================
incremental: false
class: small-code


```r
# criando um vetor:
df <- data.frame(col1 = c(1,2,3,NA,5), col2 = c(7,8,9,10,NA))

# verificando os argumentos da função colSums
colSums
```

```
function (x, na.rm = FALSE, dims = 1L) 
{
    if (is.data.frame(x)) 
        x <- as.matrix(x)
    if (!is.array(x) || length(dn <- dim(x)) < 2L) 
        stop("'x' must be an array of at least two dimensions")
    if (dims < 1L || dims > length(dn) - 1L) 
        stop("invalid 'dims'")
    n <- prod(dn[id <- seq_len(dims)])
    dn <- dn[-id]
    z <- if (is.complex(x)) 
        .Internal(colSums(Re(x), n, prod(dn), na.rm)) + (0+1i) * 
            .Internal(colSums(Im(x), n, prod(dn), na.rm))
    else .Internal(colSums(x, n, prod(dn), na.rm))
    if (length(dn) > 1L) {
        dim(z) <- dn
        dimnames(z) <- dimnames(x)[-id]
    }
    else names(z) <- dimnames(x)[[dims + 1L]]
    z
}
<bytecode: 0x476a308>
<environment: namespace:base>
```

O método de arguments matching do R faz com que todos os códigos a seguir sejam equivalentes:

****

```r
colSums(df) #positional
```

```
col1 col2 
  NA   NA 
```

```r
colSums(x=df) #exact
```

```
col1 col2 
  NA   NA 
```

```r
colSums(x=df, na.rm=FALSE) #exact
```

```
col1 col2 
  NA   NA 
```

```r
colSums(na.rm=FALSE, x=df) #exact
```

```
col1 col2 
  NA   NA 
```

```r
colSums(na=FALSE, df) #partial & positional (descobriu um que não era o primeiro, o próximo é lido como x=)
```

```
col1 col2 
  NA   NA 
```

```r
colSums(na.rm=FALSE, df) #exact & positional
```

```
col1 col2 
  NA   NA 
```


Funções - Arguments Matching (2)
========================================================
incremental: false
class: small-code


```r
# criando um vetor:
vetor1 <- c(1:20)
```

O método de arguments matching do R faz com que todos os códigos a seguir sejam equivalentes:

****

```r
sum(vetor1) #positional
```

```
[1] 210
```

```r
sum(x=vetor1) #exact
```

```
[1] 210
```

```r
sum(x=vetor1, na.rm=FALSE) #exact
```

```
[1] 210
```

```r
sum(na.rm=FALSE, x=vetor1) #exact
```

```
[1] 210
```

```r
sum(na=FALSE, vetor1) #partial & positional (descobriu um que não era o primeiro, o próximo é lido como x=)
```

```
[1] 210
```

```r
sum(na.rm=FALSE, vetor1) #exact & positional
```

```
[1] 210
```




Funções - Arguments Matching (3)
========================================================
incremental: true
class: small-code

- Há um caso em que só poderemos usar exact matching: quando temos funções que utilizam o argumento … e este argumento aparece a frente dos demais -- exemplo: paste(); 


```r
paste
```

```
function (..., sep = " ", collapse = NULL) 
.Internal(paste(list(...), sep, collapse))
<bytecode: 0x1e48a30>
<environment: namespace:base>
```


- Exceção: funções do tipo .Primitive cuja avaliação dos argumentos ocorre diretamente em C -- exemplo: sum();


```r
sum
```

```
function (..., na.rm = FALSE)  .Primitive("sum")
```


Funções - Lazy Evaluation (1)
========================================================
incremental: true
class: small-code

- por definição, o $\textbf{R}$ só utiliza os argumentos que necessariamente forem chamados/ utilizados no código;

- isso se chama $\textbf{lazy evaluation}$;

- Faça o seguinte teste:

```r
f <- function(a, b){
  a^2
}

f(2)
```

```
[1] 4
```

***
- Note que, embora houvesse um parâmetro "b", a função não retornou um erro ao passarmos somente o parâmetro "a";

- Isso se deve ao processo de $\textbf{lazy evaluation}$: a função nunca usará o parâmetro (não está no seu corpo), então tanto faz recebê-lo ou não;

- O número 2 passado foi atribuído ao parâmetro "a" via positional matching.


Funções - Lazy Evaluation (2)
========================================================
incremental: true
class: small-code

- Vamos construir uma função agora que obrigatoriamente utilizará o parâmetro "b":

- Faça o seguinte teste:

```r
f <- function(a, b){
  print(a)
  print(b)
}

f("FNDE")
```

```
[1] "FNDE"
```

```
Error in print(b): argument "b" is missing, with no default
```


***
- Neste caso há um erro: o $\textbf{R}$ reclama de não termos passado nenhum valor para o parâmetro "b"

- Dessa vez, o parâmetro "b" é obrigatoriaente utilizado no corpo da função - linha ``print(b)``.

<!-- São conceitos relativamente avançados ao objetivo do curso -- não se preocupar muito com eles -->


Funções - Closures (1)
========================================================
incremental: true
class: small-code

- $\textbf{closures}$ são funções que retornam outras funções;

- o nome closure vem do fato de as funções da parte interna da estrutura anexarem/incluirem (enclose) o environment da função parent (pai) de modo que este possa ser acessado pela função de dentro.

- A utilidade disso está no fato de possibilitar dois níveis de parâmetros: um nível parent que controla a operação e um child que faz o trabalho.


Funções - Closures (2)
========================================================
incremental: true

- Veja o seguinte exemplo - vamos criar uma função power() para criar putras duas funções chamadas square() e cube():

```r
power <- function(exponent) {
  function(x) {
    x ^ exponent
  }
}
```

- Note que a função do nível interno não recebe qualquer nome;

- O que a função power retorna?

- Se atribuirmos power() com um parâmetro especificado a um nome (criando um novo objeto), o que será esse novo objeto?


Funções - Closures (3)
========================================================
incremental: true



```r
square <- power(2)
square(2)
```

```
[1] 4
```

```r
cube <- power(3)
cube(2)
```

```
[1] 8
```

- O nome (objeto) para o qual atribuirmos a função power com um parâmetro passará a ser uma função também!!


Funções - Closures (4)
========================================================
incremental: false
class: small-code


```r
power <- function(exponent) {
  function(x) {
    x ^ exponent
  }
}
```



```r
square <- power(2)
square(2)
```


![alt text](./img/envsearch_path.png)

Fonte: Advanced R

***
Resumo da ideia:

- a função do nível mais alto retorna a função do nível mais baixo;
- ao atribuirmos a um nome qualquer, então, estamos atribuindo na verdade uma função àquele nome. 
- portanto, o nome square, na verdade está se tornando um objeto de classe function;
- com isso, estamos criando uma closure. 
- seguindo o search path mostrado na Figura, a função de dentro terá acesso ao environment da função de cima e as variáveis lá contidas. - isso seguinifica que a função square() terá acesso aos parâmetros da função power().


Funções - Closures (5)
========================================================
incremental: false
class: small-code

- Mas qual seria um caso prático em que as closures seriam uteis?

  + em casos em que temos que criar funções muito parecidas, mas que mudam só por poucos parâmetros;
  + imagine, por exemplo, que temos que criar uma função que soma 2 aos elementos de um vetor:


```r
add_2 <- function(x) {
  x + 2
}
```

***

- Mas e se você descobrisse que para alguns casos deve somar 10, em outros 5 e em outros 7, além do 2?

  + para não ter que criar mais três funções diferentes, como todos envolvem soma e diferenciam-se apenas pelo número a ser adicionado, podemos criar closures para esses casos:
  

```r
add <- function(x){
  function(a){
    x + a
  }
}

add_2 <- add(2)

add_10 <- add(10)

add_5 <- add(5)

add_7 <- add(7)
```
  
  
Funções - Closures (5)
========================================================
incremental: false
class: small-code

Vamos testar as closures que criamos em um vetor de 1's:


```r
# criando vetor de 1's
teste <- rep(1, 5)

add_2(teste)
```

```
[1] 3 3 3 3 3
```

```r
add_10(teste)
```

```
[1] 11 11 11 11 11
```

```r
add_5(teste)
```

```
[1] 6 6 6 6 6
```

```r
add_7(teste)
```

```
[1] 8 8 8 8 8
```


- Closures definitivamente não são um assunto trivial. Contudo, com prática e quando estiver mais fluente na linguagem, você começará a perceber que as closures podem ser simplificar bastante o seu código e suas tarefas.

Exercício
========================================================
incremental: false
class: small-code

Altere o parâmetro na.rm da função ``colSums()`` para que seu default passe a ser TRUE. Utilize algum data.frame do módulo 1 que possuía colunas numéricas com missing values e teste sua nova função.

FIM
========================================================
incremental: false
class: small-code

OBRIGADO!!
