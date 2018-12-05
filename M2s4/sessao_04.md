Análise de Dados em R (FNDE) - Módulo 2
Sessão 4 - Tidyverse, Manipulação de Strings e REGEX
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
- introduzir o Tidyverse;
<br />
<br />
- aprender as principais funções do pacote stringr;
<br />
<br />
- obter noções iniciais de REGEX.


Introdução Tidyverse (1)
========================================================
incremental: true
class: small-code

![Tidyverse Symbol](./img/tidyverse2.png)

<br />
*"The tidyverse is a set of packages that work with harmony because they share common data representtions and API design. The tidyverse package is designed to make it easy to install and load core packages from the tidyverse in a single command."* * **Hadley Wickham** *

<br />
Podemos definir o **Tidyverse**, então, como um *meta-package* que congrega uma coleção de diversos outros pacotes de R voltados para importação, exploração, manipulação e visualização de dados. Vão desde pacotes para manipulação de strings, expressões regulares, datas, passando por pacotes de leitura e importação, manipulação e visualização de dados, até a geração de relatórios e criação de páginas web, dentre outras coisas.

<br />
<!-- O motivo de "tornar mais fácil a vida dos Cientistas de Dados" esconde alguns problemas da linguagem R. Segundo o próprio Hadley, muitas funções no próprio R base foram crescendo "organicamente" com o passar dos anos. Isso implicou em falta de padronização em alguns casos e até mesmo funcionamentos "equivocados" em outros. Então, o tidyverse procurar estabelecer unificar uma filosofia para lidar com dados no R.  -->

<!-- TIDYVERSE EH UM DIALETO ?? - PERGUNTA DA CONFERÊNCIA -->



Pacote stringr - Introdução
========================================================
incremental: true
class: small-code

![Stringr](./img/stringr2.png)
<br />
- O pacote **stringr** contempla funções úteis para trabalhar com a manipulação de strings;
- Todas as funções que veremos tem alguma versão equivalente no R base; 
- Mas... as que serão apresentadas (stringr) são mais rápidas e/ou mais consistentes que as implementações anteriores e seguem a filosofia do *tidyverse* para manipulação de dados.
- É um pacote para manipulação de *strings* e também de expressões regulares (REGEX); 
- Manipulação de strings e REGEX são muito importantes em análise de dados, pois muitas vezes vamos lidar com trechos de textos, colunas de caracteres, onde precisamos encontrar padrões de palavras, números, e-mails, telefones, nomes, etc.


Manipulação de strings (1)
========================================================
incremental: true
class: small-code


- Existem quatro famílias principais de funções no pacote *stringr*:
  + 1. manipulação de caracteres: essas funções nos permite manipular caarcteres individuais dentro de strings e dentro de vetores;

  + 2. ferramentas para lidar com espaços em branco, com as quais poderemos adicionar, remover e manipular espaços em branco;

  + 3. operações sensíveis ao local do usuário (*locale sensitive*) - essas operações variam dependendo do local considerando o alfabeto utilizado em cada país;

  + 4. funções de *pattern matching*. Essas funções reconhecem 4 "Motores" de descrição de padrões. A mais comum são expressões regulares, a qual usaremos neste curso.


Obtendo e modificando caracteres individuais (1)
========================================================
incremental: true
class: small-code


```r
# carregamento
library(stringr)
```


- Para obter o tamanho de uma string use *string_length()*:

```r
str_length("abc")
```

```
[1] 3
```


- Para acessar caracteres individuais (posições) ou partes de uma string, podemos usar *sub_str()*;
- Esta função recebe como argumentos um vetor de caracteres, uma posição de início e uma posição final; 
- Ambas a posições podem receber um número inteiro positivo ou um inteiro negativo. 

***

```r
x <- c("abcdef", "ghifjk")

# The 3rd letter
str_sub(x, 3, 3)
```

```
[1] "c" "i"
```



```r
# The 2nd to 2nd-to-last character
str_sub(x, 2, -2)
```

```
[1] "bcde" "hifj"
```

- posição com inteiro positivo: a contagem de posições é feita da esquerda para a direita (do início da string) até a posição desejada; 
- posição com inteiro negativo: a contagem é feita da direita para esquerda (do final da string) até a posição;
- Cuidado: caso as posições passadas ultrapassem os limites da *string*, o resultado é truncado sem retornar qualquer *warning*.


Obtendo e modificando caracteres individuais (2)
========================================================
incremental: true
class: small-code

*str_sub()* também pode ser usada para modificar *strings*:

```r
str_sub(x, 3, 3) <- "X"
x
```

```
[1] "abXdef" "ghXfjk"
```

Para duplicar strings individuais, use *str_dup()*:

```r
str_dup(x, c(2, 3))
```

```
[1] "abXdefabXdef"       "ghXfjkghXfjkghXfjk"
```


Espaços em branco (1)
========================================================
incremental: true
class: small-code

As funções a seguir adicionam, removem ou modificanm espaços em branco existentes nas *strings*.

I)
- *str_pad()* preenche uma string com espaços em branco até uma largura fixa;
- Os espaços em branco podem ser adicionados à esquerda, direita ou em ambos os lados;
- Esse tipo de função é bastante útil para gerar arquivos do tipo *fwf*, ou seja com larguras/tamanhos fixos para as colunas.


```r
x <- c("abc", "defghi")
str_pad(x, 10)
```

```
[1] "       abc" "    defghi"
```


```r
str_pad(x, 10, "right")
```

```
[1] "abc       " "defghi    "
```


```r
str_pad(x, 10, "both")
```

```
[1] "   abc    " "  defghi  "
```


Espaços em branco (2)
========================================================
incremental: true
class: small-code

- Podemos ainda preencher a string com outros elementos ao invés de espaços em branco:

```r
str_pad(x, 10, "both", pad="@")
```

```
[1] "@@@abc@@@@" "@@defghi@@"
```

- ``str_pad()`` nunca encurtará uma *string*:

```r
str_pad(x, 4)
```

```
[1] " abc"   "defghi"
```


- Observação: Se você quiser assegurar que as *strings* tem o mesmo tamanho, combine ``str_pad()```com ``str_trunc()``:


```r
x <- c("Short", "This is a long string")

str_trunc(x, 10)
```

```
[1] "Short"      "This is..."
```

```r
str_pad(x, 10)
```

```
[1] "     Short"            "This is a long string"
```

```r
# x %>% 
#   str_trunc(10) %>% 
#   str_pad(10, "right")
```


Espaços em branco (3)
========================================================
incremental: true
class: small-code

II)
- O oposto de *str_pad()* é *str_trim()*, que remove os *leadind* e *trailing* *spaces*:

```r
x <- c("  a   ", "b   ",  "   c")
str_trim(x)
```

```
[1] "a" "b" "c"
```


```r
str_trim(x, "left")
```

```
[1] "a   " "b   " "c"   
```


Espaços em branco (4)
========================================================
incremental: true
class: small-code

III) 

- Podemos usar *str_wrap()* para modificar espaços em branco já existentes de forma a "envelopar" por exemplo um parágrafo de texto para que o comprimento de cada linha seja o mais similar possível. É como se justificássemos o parágrafo.

```r
jabberwocky <- str_c(
  "`Twas brillig, and the slithy toves ",
  "did gyre and gimble in the wabe: ",
  "All mimsy were the borogoves, ",
  "and the mome raths outgrabe. "
)
# uma alternativa a c() para strings
```


```r
cat(str_wrap(jabberwocky, width = 40))
```

```
`Twas brillig, and the slithy toves did
gyre and gimble in the wabe: All mimsy
were the borogoves, and the mome raths
outgrabe.
```

```r
# cat0 é uma alternativa a print
```


Locale sensitive (1)
========================================================
incremental: true
class: small-code


Uma boa parte das funções do pacote **stringr** são *locale sensitive*: elas vão performar de diferente dependendo do país/região em que o usuário se encontra.

Veja exemplos de funções que transformam letras de caixa baixa para caixa alta e vice-versa:

```r
x <- "I like horses."
str_to_upper(x)
```

```
[1] "I LIKE HORSES."
```


```r
str_to_title(x)
```

```
[1] "I Like Horses."
```


```r
str_to_lower(x)
```

```
[1] "i like horses."
```


```r
# Veja o caso do idioma turco em que há dois tipos de i: um com "ponto" e outro sem
str_to_lower(x, "tr")
```

```
[1] "ı like horses."
```


Locale sensitive (2)
========================================================
incremental: true
class: small-code

Ordenando *strings* e seus índices:


```r
x <- c("y", "i", "k")
str_order(x) # índices
```

```
[1] 2 3 1
```


```r
str_sort(x)
```

```
[1] "i" "k" "y"
```


```r
# No idioma da Lituânia, y está entre as letras i and k
str_sort(x, locale = "lt")
```

```
[1] "i" "y" "k"
```

Um aspecto importante é que a configuração *default* de stringr sempre vem com o idioma em inglês para garantir um comportamento idêntico em qualquer sistema ao se utilizar as funções genéricas. Isso é diferente do que normalmente ocorre com R base, onde a opção global *locale* normalmente varia com a versão regional do sistema operacional e gera bastante confusão quando vamos desenvolver programas.

Para obtermos uma lista das abreviações e regiões disponíveis, é só rodar ``stringi::stri_locale_list()``.
  
  
Pattern Matching (1)
========================================================
incremental: true
class: small-code

A vasta maioria das funções de **stringr** funciona com padrões. Estas funções são parametrizadas pelo tipo de taefa que elas performam e para quais padrões elas fazem o *matching* ou pareamento.

**Tasks**

Cada função de matching possui os mesmos dois primeiros argumentos: um vetor de strings para processar e um padrão para fazer o *matching*. O pacote **stringr** disponibiliza função de *pattern matching* para detectar, localizar, extrair, parear, substituir e separar strings.

<!-- São as funções **detect**, **locate**, **extract**, **match**, **replace** e **split** -->

Veja um exemplo com algumas *strings* e uma Expressão Regular para encontrar números de telefone (dos EUA):

```r
strings <- c(
  "apple", 
  "219 733 8965", 
  "329-293-8753", 
  "Work: 579-499-7527; Home: 543.355.3679"
)
phone <- "([2-9][0-9]{2})[- .]([0-9]{3})[- .]([0-9]{4})"
```




Pattern Matching (2)
========================================================
incremental: true
class: small-code

- ``str_detect()`` detecta a presença ou ausência de um padrão e retorna um vetor lógico. 
- ``str_subset()`` retorna os elementos de um vetor de caracteres que fazem a correspondência (*match*) com uma expressão regular.


```r
# Which strings contain phone numbers?
str_detect(strings, phone)
```

```
[1] FALSE  TRUE  TRUE  TRUE
```



```r
str_subset(strings, phone)
```

```
[1] "219 733 8965"                          
[2] "329-293-8753"                          
[3] "Work: 579-499-7527; Home: 543.355.3679"
```

- ``str_count()`` conta o número de correspondências:


```r
# How many phone numbers in each string?
str_count(strings, phone)
```

```
[1] 0 1 1 2
```


Pattern Matching (3)
========================================================
incremental: true
class: small-code

- ``str_locate()`` localiza a primeira posição do padrão procurado de cada string (elemento) presente no vetor e retorna uma matriz numérica com colunas indicando as posições de começo e fim. ``str_loacte_all()`` localiza todas as correspondências, retornando uma lista de matrizes numéricas.


```r
# Where in the string is the phone number located?
(loc <- str_locate(strings, phone))
```

```
     start end
[1,]    NA  NA
[2,]     1  12
[3,]     1  12
[4,]     7  18
```


```r
str_locate_all(strings, phone)
```

```
[[1]]
     start end

[[2]]
     start end
[1,]     1  12

[[3]]
     start end
[1,]     1  12

[[4]]
     start end
[1,]     7  18
[2,]    27  38
```




Pattern Matching (4)
========================================================
incremental: true
class: small-code

- ``str_extract()`` extrai o texto correspondente a primeira correspondência (*match*), retornando um vetor de caracteres. ``str_extract_all()`` extrai todos os matches e retorna uma lista de vetores de caracteres.


```r
# What are the phone numbers?
str_extract(strings, phone)
```

```
[1] NA             "219 733 8965" "329-293-8753" "579-499-7527"
```


```r
str_extract_all(strings, phone)
```

```
[[1]]
character(0)

[[2]]
[1] "219 733 8965"

[[3]]
[1] "329-293-8753"

[[4]]
[1] "579-499-7527" "543.355.3679"
```


```r
str_extract_all(strings, phone, simplify = TRUE)
```

```
     [,1]           [,2]          
[1,] ""             ""            
[2,] "219 733 8965" ""            
[3,] "329-293-8753" ""            
[4,] "579-499-7527" "543.355.3679"
```


Pattern Matching (5)
========================================================
incremental: true
class: small-code


- ``str_match()`` extrai grupos de captura de expressões regulares formados por ``()`` apenas para o primeiro *match*. ``str_match_all()`` extrai grupos de captura de todos os *macthes* e retorna uma lista de matrizes de caracteres.


```r
# Pull out the three components of the match
str_match(strings, phone)
```

```
     [,1]           [,2]  [,3]  [,4]  
[1,] NA             NA    NA    NA    
[2,] "219 733 8965" "219" "733" "8965"
[3,] "329-293-8753" "329" "293" "8753"
[4,] "579-499-7527" "579" "499" "7527"
```


```r
str_match_all(strings, phone)
```

```
[[1]]
     [,1] [,2] [,3] [,4]

[[2]]
     [,1]           [,2]  [,3]  [,4]  
[1,] "219 733 8965" "219" "733" "8965"

[[3]]
     [,1]           [,2]  [,3]  [,4]  
[1,] "329-293-8753" "329" "293" "8753"

[[4]]
     [,1]           [,2]  [,3]  [,4]  
[1,] "579-499-7527" "579" "499" "7527"
[2,] "543.355.3679" "543" "355" "3679"
```


Pattern Matching (6)
========================================================
incremental: true
class: small-code

- ``str_replace()`` substitui o primeiro padrão em que houve *match* e retorna um vetor de caracteres. ``str_replace_all()`` substitui todos os *matches*.

<!-- ESSAS SÃO AS EQUIVALENTES A SUB E GSUB!!! MOSTRAR COMO FAZER UMA FUNÇÃO PARA SUBSTITUIÇÃO DE "," E "." -->
<!-- OU PASSAR COMO EXERCÍCIO DA AULA -->


```r
# caso desejássemos esconder os telefones (mto util para CPF's)
str_replace(strings, phone, "XXX-XXX-XXXX")
```

```
[1] "apple"                                 
[2] "XXX-XXX-XXXX"                          
[3] "XXX-XXX-XXXX"                          
[4] "Work: XXX-XXX-XXXX; Home: 543.355.3679"
```



```r
str_replace_all(strings, phone, "XXX-XXX-XXXX")
```

```
[1] "apple"                                 
[2] "XXX-XXX-XXXX"                          
[3] "XXX-XXX-XXXX"                          
[4] "Work: XXX-XXX-XXXX; Home: XXX-XXX-XXXX"
```


Pattern Matching (7)
========================================================
incremental: true
class: small-code

- ``str_split_fixed()`` divide a *string* em um número fixo de partes baseado no padrão passado como argumento e retorna uma matriz de caracteres. ``str_split()`` separar um *string* em um número variável de partes e retorna uma lista de vetores de caracteres.


```r
str_split("a-b-c", "-")
```

```
[[1]]
[1] "a" "b" "c"
```


```r
str_split_fixed("a-b-c", "-", n = 2)
```

```
     [,1] [,2] 
[1,] "a"  "b-c"
```


Pattern Matching (8)
========================================================
incremental: true
class: small-code

**Obtendo índices do matching:**


``str_subset()`` é uma função envelope de ``x[str_detect(x, pattern)]``.
<!-- and is equivalent to grep(pattern, x, value = TRUE). -->
``str_which()``, por sua vez é uma função envelope de ``which(str_detect(x, pattern))``.
<!-- and is equivalent to grep(pattern, x). -->


```r
fruit <- c("apple", "banana", "pear", "pinapple")
str_subset(fruit, "a")
```

```
[1] "apple"    "banana"   "pear"     "pinapple"
```


```r
str_which(LETTERS, "F")
```

```
[1] 6
```

```r
# str_which(LETTERS, "F|Y")
```


Mecanismos de busca - REGEX
========================================================
incremental: true
class: small-code

- Expessões Regulares são formas concisas e flexíveis para descrever padrões (que estamos buscando) em *strings*;

- Expressões regulares nos permitem descrever em termos mais gerais o que desejamos buscar em uma *string*;

- Normalmente, é mais eficiente usarmos expressões regulares, porque se buscarmos caracteres simples, só obteremos o *matching* exato do caractere. Já pensou em como faríamos para buscar apenas números de telefone, endereços de e-mail, CNPJ's ou CPF's no meio de dados (por exemplo páginas da internet) que não estão exatamente organizados em colunas?

- expressões regulares são a principal *engine* de *matching* no pacote **stringr**;

- é uma técnica usada praticamente em todo o mundo da programação. Não é uma particularidade somente do **R**.


REGEX (1)
========================================================
incremental: true
class: small-code


As expressões regulares (regex ou regexps), em geral, são construídas da combinação de 3 componentes:

- 1) *literal characters*: que somente vão sofrer *matching* se houver nos dados caracteres literais idênticos;

- 2) *character classes*: que permitem o *matching* por múltiplos um caractere - são compostas de caracteres dentro de dois colchetes ``[ ]``;

- 3) *modifiers* ou *anchors*: que vão operar nos caracteres, nas classes e em combinações dos dois.


Exemplos REGEX com stringr (1)
========================================================
incremental: true
class: small-code

**Correspondências literais (basic match/ literal characters)**

O padrão mais simples para fazer a *matching* é:

```r
x <- c("apple", "banana", "pear")
str_extract(x, "an")
```

```
[1] NA   "an" NA  
```

Podemos ignorar letras maiúsculas e minúsculas com ``ignore_case = TRUE``:

```r
bananas <- c("banana", "Banana", "BANANA")
str_detect(bananas, "banana")
```

```
[1]  TRUE FALSE FALSE
```

```r
str_detect(bananas, regex("banana", ignore_case = TRUE))
```

```
[1] TRUE TRUE TRUE
```


Exemplos REGEX com stringr (2)
========================================================
incremental: true
class: small-code

Seguindo com os exemplos, o próximo passo é conhecermos o papel desempenhado por **.**, cuja função é buscar qualquer caractere com exceção de uma nova linha (\n):

```r
str_extract(x, ".a.")
```

```
[1] NA    "ban" "ear"
```

Mas se você quiser encontrar até mesmo caracteres que indicam uma nova linha, pode "setar" ``dotall = TRUE`` dentro da função responsável pelo mecanismo de *matching*, que nesse caso é ``regex()```(visto no item anterior):

```r
str_detect("\nX\n", ".X.")
```

```
[1] FALSE
```

```r
str_detect("\nX\n", regex(".X.", dotall = TRUE))
```

```
[1] TRUE
```



Escaping Characters (1)
========================================================
incremental: true
class: small-code


- Se " . " faz o *match* de qualquer caractere, como podemos fazer o *match* literal de um ponto " . " ?

- Teremos que usar um caractere de escape para dizer ao **R** que queremos um *matching* literal e não usar o comportamento especial de “ . ”

- Em expressões regulares (regexps) se usa uma barra invertida \ para tal fim

- Então, para buscarmos um simples **.** nos dados, usamos \\.

- **Mas...**

- como você já viu no Módulo 1, a barra invertida também é um caractere especial no **R** usada para *strings*

- Portanto, teríamos que acrescentar outra barra invertida formando a string \\\\. e fazermos o *matching* com um simples ponto nos dados.


Escaping Characters (2)
========================================================
incremental: true
class: small-code

Basicamente, a regra é: **em cada par de barras invertidas, temos uma barra literal**


```r
# To create the regular expression, we need \\
dot <- "\\."

# But the expression itself only contains one:
writeLines(dot)
```

```
\.
```

```r
# And this tells R to look for an explicit .
str_extract(c("abc", "a.c", "bef"), "a\\.c")
```

```
[1] NA    "a.c" NA   
```

- Facilita pensar que uma regexp é um tipo de *string*, ou seja regexps é um conjunto que está contido no conjunto de objetos que são *strings*. 

- Temos que pensar primeiro em como deve chegar a string dentro do mecanismo de regexp.



Escaping Characters (3)
========================================================
incremental: true
class: small-code


- Como faríamos então para fazer o match literal de uma barra invertida \\ ? 

- Basta seguirmos o mesmo raciocínio das duas instâncias:

- Como deve chegar literalmente a string no mecanismo de regexp (após todas as deduções de caracteres de escape)? 

- Deve chegar como " \\\\ "



Escaping Characters (4)
========================================================
incremental: true
class: small-code


- Quando formos nos referir às expressões regulares, usaremos somente o núcleo delas, exemplo " \\\\ "

- Quando formos nos referir as strings, usaremos "\\\\\";

- Lembre-se que devemos acrescentar os caracteres de escape usando os raciocínios apresentados anteriormente para que se chegue ao "padrão final" de uma regexp.

<!-- HAAHAHHAHAH NO RMARKDOWN AINDA TENHO QUE USAR 5 PARA IMPRIMIR DUAS !!! -->


```r
x <- "a\\b"
writeLines(x)
```

```
a\b
```


```r
str_extract(x, "\\\\")
```

```
[1] "\\"
```



Matching de múltiplos caracteres (classes) (1)
========================================================
incremental: true
class: small-code

Primeiro, veremos alguns atalhos para classes de caracteres:


|atalhos |classes                                                      |
|:-------|:------------------------------------------------------------|
|\\w     |alfanuméricos e _ (qualquer palavra)                         |
|\\W     |não alfanuméricos (qualquer coisa diferente de palavras e _) |
|\\d     |digitos                                                      |
|\\D     |não dígitos                                                  |
|\\s     |espaço                                                       |
|\\S     |não espaço                                                   |
<!-- TEM QUE COLOCAR DUAS BARRAS NAS STRINGS PARA SAIR UMA NESSE CASO TB -->




Matching de múltiplos caracteres (classes) (2)
========================================================
incremental: true
class: small-code

Passemos aos exemplos:

```r
str_extract_all("Don't eat that!", "\\w+")[[1]]
```

```
[1] "Don"  "t"    "eat"  "that"
```

```r
str_split("Don't eat that!", "\\W")[[1]]
```

```
[1] "Don"  "t"    "eat"  "that" ""    
```


```r
str_extract_all("1 + 2 = 3", "\\d+")[[1]]
```

```
[1] "1" "2" "3"
```


```r
(text <- "Some  \t badly\n\t\tspaced \f text")
```

```
[1] "Some  \t badly\n\t\tspaced \f text"
```

```r
str_replace_all(text, "\\s+", " ")
```

```
[1] "Some badly spaced text"
```


Matching de múltiplos caracteres (classes) (3)
========================================================
incremental: true
class: small-code

Um outro atalho interessante é "\\b" que busca por limites/bordas de palavras, ou seja, transições entre o caracteres de palavras e caracteres de não-palavras. "\\B" faz o oposto.


```r
str_replace_all("The quick brown fox", "\\b", "_")
```

```
[1] "_The_ _quick_ _brown_ _fox_"
```

```r
str_replace_all("The quick brown fox", "\\B", "_")
```

```
[1] "T_h_e q_u_i_c_k b_r_o_w_n f_o_x"
```

Outros atalhos interessantes estão em <http://stringr.tidyverse.org/articles/regular-expressions.html>.


Matching de múltiplos caracteres (classes) (4)
========================================================
incremental: true
class: small-code

Há a possibilidade, ainda, de criarmos nossas próprias classes usando **[]**:

- [abc]: busca o *match* de a, b ou c;
- [a-z]: busca qualquer caractere minúsculo entre **a** e **z**;
- [A-Z]: busca qualquer caractere maiúsculo entre **A** e **Z**;
- [\^abc]: busca qualquer coisa exceto a, b, ou c;
- [\^\-]: busca ^ or -. (Note que teremos que adicionar mais uma barra invertida na frente de cada uma das barras ao passar o comando no **R**)

<!-- NA VIGNETTE DO LUBRIDATE, há um erro para o exemplo [\^\-] -->


Matching de múltiplos caracteres (classes) (5)
========================================================
incremental: true
class: small-code

As expressões anteriores vão dentro de outros colchetes:

```r
y <- c(1234, "R", "FNDE", " ", "Olá, tudo bem ?")
str_extract_all(y, "[[:digit:]]")
```

```
[[1]]
[1] "1" "2" "3" "4"

[[2]]
character(0)

[[3]]
character(0)

[[4]]
character(0)

[[5]]
character(0)
```

```r
str_extract_all(y, "[[:digit:]á]")
```

```
[[1]]
[1] "1" "2" "3" "4"

[[2]]
character(0)

[[3]]
character(0)

[[4]]
character(0)

[[5]]
[1] "á"
```

```r
str_extract_all(y, "[[:digit:]td]")
```

```
[[1]]
[1] "1" "2" "3" "4"

[[2]]
character(0)

[[3]]
character(0)

[[4]]
character(0)

[[5]]
[1] "t" "d"
```

```r
str_extract_all(y, "[b[:upper:]m]")
```

```
[[1]]
character(0)

[[2]]
[1] "R"

[[3]]
[1] "F" "N" "D" "E"

[[4]]
character(0)

[[5]]
[1] "O" "b" "m"
```



Matching de múltiplos caracteres (classes) (6)
========================================================
incremental: true
class: small-code

Há ainda diversas classes pré-construídas que podemos usar com os colchetes:

-[:punct:]: pontuação.

-[:alpha:]: letras.

-[:lower:]: letras minúsculas.

-[:upper:]: LETRAS MAIÚSCULAS. 

<!--AQUI A UM ERRO TB NA VIGNETTE - ELE ESCREVE UPPER CLASS -->

-[:digit:]: digitos.

-[:alnum:]: letras e números.

-[:cntrl:]: caracteres de controle.

-[:graph:]: letras, números e pontuação.

-[:print:]: letras, números, pontuação e whitespace.

-[:space:]: caracteres de espaço (equivalente a \s).

-[:blank:]: espaço e tab.


Matching de múltiplos caracteres (classes) (7)
========================================================
incremental: true
class: small-code

As expressões vão dentro de outros colchetes:


```r
y <- c(1234, "R", "FNDE", " ", "Olá, tudo bem ?")
str_extract_all(y, "[[:digit:]]")
```

```
[[1]]
[1] "1" "2" "3" "4"

[[2]]
character(0)

[[3]]
character(0)

[[4]]
character(0)

[[5]]
character(0)
```

```r
str_extract_all(y, "[[:digit:]á]")
```

```
[[1]]
[1] "1" "2" "3" "4"

[[2]]
character(0)

[[3]]
character(0)

[[4]]
character(0)

[[5]]
[1] "á"
```


Matching de múltiplos caracteres (classes) (8)
========================================================
incremental: true
class: small-code


```r
str_extract_all(y, "[[:digit:]td]")
```

```
[[1]]
[1] "1" "2" "3" "4"

[[2]]
character(0)

[[3]]
character(0)

[[4]]
character(0)

[[5]]
[1] "t" "d"
```

```r
str_extract_all(y, "[b[:upper:]m]")
```

```
[[1]]
character(0)

[[2]]
[1] "R"

[[3]]
[1] "F" "N" "D" "E"

[[4]]
character(0)

[[5]]
[1] "O" "b" "m"
```



Operador Alternation
========================================================
incremental: true
class: small-code

- ``|`` é o operador de alternância que permite escolher entre uma ou mais correspondências possíveis. 

- ``abc|def`` vai fazer o *matching* com ``abc`` **OU** ``def``.


```r
str_detect(c("abc", "def", "ghi"), "abc|def")
```

```
[1]  TRUE  TRUE FALSE
```


Agrupamento (1)
========================================================
incremental: true
class: small-code

Parenteses podem servir para alterar regras de precedência ou formar grupos.

O mesmo que vimos no módulo 1 para alterar regras de precedência serve para regexp:


```r
str_extract(c("grey", "gray"), "gre|ay")
```

```
[1] "gre" "ay" 
```

```r
str_extract(c("grey", "gray"), "gr(e|a)y")
```

```
[1] "grey" "gray"
```


Agrupamento (2)
========================================================
incremental: true
class: small-code

Parênteses definem grupos e podemos retroreferenciar esses grupos indicando que eles aparecem mais uma, duas ou mais vezes. No exemplo a seguir, buscamos os pares de caracteres que aparecem duas vezes.

```r
fruit
```

```
[1] "apple"    "banana"   "pear"     "pinapple"
```

```r
pattern <- "(..)\\1"
fruit %>% 
  str_subset(pattern)
```

```
[1] "banana"
```

```r
# (..) = qualquer par de dois caracteres
# \1 = backreference to the first group - repeticao

fruit %>% 
  str_subset(pattern) %>% 
  str_match(pattern)
```

```
     [,1]   [,2]
[1,] "anan" "an"
```

```r
# help de str_match: "....First column is the complete match, followed by one column for each capture group."
```


Âncoras ou modificadores
========================================================
incremental: true
class: small-code

Ancorar significa estabelecer um padrão para o início ou final da *string* que estamos buscando.

- **^** busca o padrão no começo da *string*;
- **$** busca o padrão no final da *string*.


```r
x <- c("apple", "banana", "pear")
str_extract(x, "^a")
```

```
[1] "a" NA  NA 
```

```r
str_extract(x, "a$")
```

```
[1] NA  "a" NA 
```


Repetições (1)
========================================================
incremental: true
class: small-code

Pode-se controlar quantas vezes um padrão aparece em determinada parte da *string* com:

- ?: 0 ou 1.
- +: 1 ou mais.
- *: 0 ou mais.


```r
x <- "1888 is the longest year in Roman numerals: MDCCCLXXXVIII"
str_extract(x, "CC?")
```

```
[1] "CC"
```

```r
str_extract(x, "CC+")
```

```
[1] "CCC"
```

```r
str_extract(x, 'C[LX]+')
```

```
[1] "CLXXX"
```

```r
str_match("banana", '(na)+')
```

```
     [,1]   [,2]
[1,] "nana" "na"
```


Repetições (2)
========================================================
incremental: true
class: small-code

Podemos especificar o número exato de repetições que esperamos com:

- {n}: exactly n
- {n,}: n or more
- {n,m}: between n and m


```r
str_extract(x, "C{2}")
```

```
[1] "CC"
```

```r
str_extract(x, "C{2,}")
```

```
[1] "CCC"
```

```r
str_extract(x, "C{2,3}")
```

```
[1] "CCC"
```

Há diversos outros padrões e expressões que podem ser usadas. Busque ``??stringi_search-regex`` ou visite o site de **stringr**.






FIM
========================================================
incremental: false
class: small-code

OBRIGADO!!
