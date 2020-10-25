---
layout: post
title:  "The Little Schemer - Leitura comentada"
date:   2020-10-19
categories: Books
---

Olá, bem vindo ao meu espaço de anotações. Me chamo João Vitor e gosto de me divertir aprendendo novas linguagens de programação e brincar com meu editor de texto favorito *((neo)Vim)*.

Irei escrever sobre qualquer coisa que vier a mente aqui, e tenho como meta escrever um pouco sobre livros que estou lendo e coisas que aprendo durante o dia.

Para começar o blog, pensei em ler o livro The Little Schemer, escrito por Daniel P. Friedman e Matthias Felleisen, um livro curto que ensina a programar de uma maneira bem diferente! A idéia do livro consiste em várias perguntas e respostas, com o objetivo de ensinar a programar de uma forma mais prática. As perguntas são do tipo:

***
> Qual é o car da lista (1 2 3)?

> Resposta: 1

***
Assim, o aluno pode rodar as perguntas e verificar por conta própria. Achei incrível o estilo do livro e resolvi ler e comentar rapidamente os capítulos.

O que me motivou a ler, também, foi que o autor do livro já escreveu vários outros muito bons. Já tive o prazer de ler o How to Design Programs, que tem uma abordagem de ensinar programação para qualquer pessoa, recomendo fortemente!

# Sumário
<a href="#chapter1">Capítulo 1 - Hello World</a>


<a href="#chapter2">Capítulo 2 - Do it, Do It Again, and Again, and Again...
</a>

<a href="#chapter3">Capítulo 3 - Cons the Magnificent</a>

<a href="#chapter4">Capítulo 4 - Numbers</a>

<a href="#chapter5">Capítulo 5 - Oh my Gawd: It's full of stars!</a>

<a href="#chapter6">Capítulo 6 - Shadows</a>

<a href="#chapter7">Capítulo 7 - Friends and Relations</a>

<a href="#chapter8">Capítulo 8 - Lambda the Ultimate</a>

<a href="#chapter9">Capítulo 9 - And again, and Again and Again...</a>

<a href="#chapter10">Capítulo 10 - What is the value of all this?</a>

****
<h2 id="chapter1">Capítulo 1 - Hello World</h2>
O primeiro capítulo é bem simples e aborda algumas das principais palavras chaves da linguagem.

***
* Recursão é o ato de definir um objeto ou resolver um problema em termo de si mesmo.
* `car` é definido para listas não-vazias.
`car` retorna o primeiro elemento da lista não vazia
* Ex: `(check-equal? (car '(a b c)) 'a)`
* `cdr` é definido para listas não vazia. `cdr` sempre retorna outra lista
* Ex: `(check-equal? (cdr '(a b c)) '(b c))`
* `cons` recebe dois argumentos. O segundo argumento precisa ser uma lista. O resultado sempre é uma lista
* Ex: `(check-equal (cons 1 '(2 3)) '(1 2 3))`

***
O primeiro capítulo também aborda os átomos, a função de comparação de igualmente entre átomos e checagem de valores nulos.

Por fim, deixarei aqui algumas outras perguntas do primeiro capítulo.


```scheme

#lang racket
(require rackunit)

(define (atom? x)
  (and (not (pair? x)) (not (null? x))))

; is it true that this is an atom?
(check-equal? (atom? 'atom) #t)


; is it true that this is a list?
(check-equal? (list? '(atom)) #t)


; is it true that this is an atom?
(check-equal? (atom? '()) #f)

; is it true that this is a list?
(check-equal? (list? '(() () ())) #t)

; what is the car of l where l is:
; - (a b c)
(check-equal? (car '(a b c)) 'a)

; what is the cdr of l where l is:
;- (a b c)
(check-equal? (cdr '(a b c)) '(b c))


; what is the cdr of  where l is
; - ((a b c) x y z)
(check-equal? (cdr '((a b c) x y z)) '(x y z))

; what is the cdr of l where l is
; - (hamburger)
(check-equal? (cdr '(hamburger)) '())

; what is the cons of sand l
; where s is (banana and)
; and
; l is (peanut butter and jelly)
(check-equal? (cons '(banana and) '(peanut butter and jelly))
              '((banana and) peanut butter and jelly))

```

<h2 id="chapter2"> Capítulo 2 - Do it, Do It Again, and Again, and Again... </h2>

Esse capítulo é bem curtinho e nos apresenta a noção de recursão.

Todos os exemplos aqui mostrados podem ser rodados em racket, apenas adicionando:

```scheme
#lang racket
(require rackunit)
```


Primeiramente nos é apresentada uma função, chamada de `(lat? l)`, que recebe uma lista l e verifica se ela é uma lista de átomos.

```scheme
(define lat?
  (lambda (x)
    (cond
      [(null? x) #t]
      [(atom? (car x)) (lat? (cdr x))]
      [else #f])))

; testes
(check-equal? (lat? '(Jack Sprat LOL)) #t)
(check-equal (lat? '((Jack) Sprat LOL)' #f))
```

Observação:

- comentários começam com ";"
- `check-equal` testa a função
- `cond` faz perguntas
- `lambda` cria funções
- `define` dá um nome à função

Outra função apresentada nesse capítulo é a `(member? a lat)`, no qual verifica se o átomo _a_ está em lat.

Definição:
```scheme
(define member?
  (lambda (a lat)
    (cond
      [(null? lat) #f]
      [else (or (eq? (car lat ) a)
                (member? a (cdr lat)))])))
; testes
(check-equal? (member? 'feijoada '(cafe cha agua)) #f)
(check-equal? (member? 'cafe '(acucar cafe agua pó)) #t)
```

***
<center><strong>IMPORTANTE</strong></center>



> ## Primeiro Mandamento (preliminar):
> ### Sempre pergunte `null?` como primeira pergunta nas funções.

***
Para uma melhor experiência, você pode baixar a [IDE DrRacket](https://racket-lang.org/) e rodar esses exemplos. Adicione as funções e rode exemplos com o stepper, botào que fica no canto superior direito.

Para rodar na linha de comando, baixe o [Racket](https://racket-lang.org/) no Sistema Operacional e rode no terminal `racket programa.rkt`

<h2 id="chapter3"> Capítulo 3 - Cons the Magnificent</h2>

```scheme
; o que é (rember a lat)?
; onde a é moto
; e lat é
; (lanche moto hamburger geleia)
; Resposta: (lanche hamburger geleia)
```

Você consegue perceber o que a função rember faz?

Rember significa "remove member". Essa função remove o elemento _a_ de _lat_, no caso acima, retirou _moto_ da lista.

Você consegue implementar essa função? Pensando especialmente no primeiro mandamento e nas observações acima já é possível resolver esse problema. Vou implementar ela aqui com alguns testes para facilitar a visualização.

```scheme
(define rember
  (lambda (a lat)
    (cond
      [(null? lat) '()]
      [(eq? (car lat) a) (cdr lat)]
      [else (rember a (cdr lat))]
      )))
(check-equal? (rember 'bacon '(bacon lettuce and tomato))
              '(lettuce and tomato))
(check-equal? (rember 'and '(bacon lettuce and tomato))
              '(tomato))
```

Perceba pelo segundo teste que está retornando apenas _(tomato)_, você consegue identificar o erro? A função removeu todos os outros elementos, então ela está errada!

É aqui que aparece um nova palavra para a linguagen, a famosa _cons_.

***

> ## Segundo Mandamento
> ### Use _cons_ para construir listas.

***

~~Cons vem de construir lista. Brincadeira!~~

Para criar uma nova lista, usamos a palavra _cons_

Por exemplo, `(cons 1 '())` gera uma nova lista com o número 1

Arrumando a função para salvar os elementos anteriores, a nova função fica assim:
```scheme
(define rember-fixed
  (lambda (a lat)
    (cond
      [(null? lat) '()]
      [(eq? (car lat) a) (cdr lat)]
      [else
        (cons (car lat)
              (rember-fixed a (cdr lat)))])))
; is it working?
; if the test pass, it worked as expected
(check-equal? (rember-fixed 'and '(bacon lettuce and tomato))
              '(bacon lettuce tomato))
; xD
```

Por fim, temos o terceiro mandamento:

***
> ### Terceiro Mandamento
> #### Quando for construir uma lista, descreva o primeiro elemento típico e depois _cons_ ele com recursão natural.

***

No caso do _rember-fixed_, o primeiro elemento é (car lat) e a recursão natural é (rember-fixed a (cdr lat)).

Bom, por hoje é só isso que tenho para mostrar. Não estou detalhando perfeitamente os pontos do livro, estou apenas mostrando algumas anotações que fiz e que considero importante para absorver durante a leitura. Para um melhor entendimento, é imprescindível ler o livro e rodar os exemplos.

<h2 id="chapter4">Capítulo 4 - Numbers</h2>
Este capítulo foi o que mais gostei até então. O autor apresenta algumas funções primitivas, como _zero?_, _add1_, _sub1_, que, respectivamente, verifica se um número é igual a zero, adiciona 1, e subtrai 1.

Após isso, ele segue definindo as principais funções para realizar operações com os números naturais.

Por exemplo, a função de soma de dois números é definida como:
```scheme
(define my-add
  (lambda (x y)
    (cond
      [(zero? y) x]
      [else (add1 (my-add x (sub1 y)))]
      )))

(check-equal? (my-add 5 5) 10)
```

Perceba que essa função não está respeitando o primeiro mandamento, pois a primeira pergunta é se y é igual a zero. Então fazemos uma revisão no primeiro mandamento, que fica assim:

***
> ## Primeiro Mandamento (revisado)
> #### Quando a função recebe como parâmetro uma lista de átomos _lat_, a primeira pergunta deve ser (null? lat)
> #### Quando a função recebe como parâmetro uma números _n_, pergunte (zero? n)

***

Você consegue definir a função de subtração? Tente!

Função de multiplicação:
```scheme
(define times
  (lambda (x y)
    (cond
      [(zero? y) 0]
      [else
        (my-add x (times x (sub1 y)))])))

(check-equal? (times 4 3) 12)
(check-equal? (times 3 4) 12)
(check-equal? (times 2 5) 10)
```
Vamos aqui mostrar um exemplo dessa função.
Suponha que você quer saber (times 5 3), ou seja, quanto é 3 vezes 5
```scheme
(times 5 3)
===
(cond
    [(zero? 3) 0]
    [else (+ 5 (times 5 (sub1 3)))])
(cond
    [#f 0]
    [else (+ 5 (times 5 (sub1 3)))])
===
    (+ 5 (times 5 (sub1 2)))
===
(+ 5
(cond
    [#f 0]
    [else (+ 5 (times 5 (sub1 2)))]))
===
(+ 5
    (+ 5 (times 5 (sub1 2))))
===
(+ 5
    (+ 5
        (cond
            [#f 0]
            [else (+ 5 (times 5 (sub1 1)))])
===
(+ 5
    (+ 5
            (+ 5 (times 5 (sub1 1)))))
===
(+ 5
    (+ 5
         (+ 5
            (cond
                [#t 0]
                [else (+ 5 (times 5 (sub1 0)))]))))
===
(+ 5
    (+ 5
         (+ 5 0)))
```
Após a soma, teremos o resultado que é 15.


***
> ## Quinto mandamento
> #### Na construção de valores com +, sempre use 0 como valor final, pois não altera a conta.
> #### Na contrução de valores com *, sempre use 1 como valor final, pois não altera a conta.
> #### Na contrução de valores com cons, sempre considere '() como valor final.

***

## Tup+
 O que é: `(tup+ tup1 tup2)`

 _tup1_: `(3 6 9 11 4)` _tup2_ : `(8 5 2 0 7)` ?

Resposta:
`(11 11 11 11 11)`

---
Consegue perceber o que _tup+_ faz?
Aqui vai a definição:

```scheme
(define tup+
  (lambda (t1 t2)
    (cond
      [(null? t1) '()]
      [else
        (cons (my-add (car t1) (car t2))
              (tup+ (cdr t1) (cdr t2)))])))

```
O livro aponta que temos aqui uma novidade, que é o fato da função recorrer sobre duas listas ao mesmo tempo, _t1_ e _t2_. Interessante né?

Outra função legal definida é a comparação de maior e menor, assim como a de igualdade
```scheme
(define .>
  (lambda (n m)
    (cond
      [(zero? n) #f]
      [(zero? m) #t]
      [else (.> (sub1 n) (sub1 m))])))

(check-equal? (.> 1 2) #f)
(check-equal? (.> 10 10) #f)
(check-equal? (.> 20 10) #t)

(define .=
  (lambda (x y)
    (cond
      [(zero? x) (zero? y)]
      [(zero? y) #f]
      [else (.= (sub1 x) (sub1 y))])))

(check-equal? (.= 5 5) #t)
(check-equal? (.= 5 3) #f)
(check-equal? (.= 2 2) #t)
```

Tem essa muito interessante também, que vou deixar a cargo do leitor descobrir o que a função faz.
```scheme
(define d
  (lambda (x y)
    (cond
      [(< x y) 0]
      [else (add1 (d (- x y) y))])))

(check-equal? (t 15 3) 5)
(check-equal? (t 15 5) 3)
(check-equal? (t 20 2) 10)

```
Por fim, são apresentadas as funções _pick_ e _occur_. A _pick_ recebe dois argumentos, _n_ e _lat_, sendo _n_ um número e _lat_ uma lista de átomos, e retorna o átomo da n-ésima posição da lista. A função occur calcula quantas vezes o elemento _a_ ocorreu em _lat_.
```scheme

(define pick
  (lambda (n lat)
    (cond
      [(null? lat) '()]
      [(zero? n) (car lat)]
      [else (pick (sub1 n) (cdr lat))])))

(define foods '(lasanga spaghetti ravioli macaroni meatball))
(check-equal? (pick 3 foods) 'macaroni)

(define occur
  (lambda (a lat)
    (cond
      [(null? lat) 0]
      [(eq? a (car lat)) (add1 (occur a (cdr lat)))]
      [else (occur a (cdr lat))])))

(check-equal? (occur 'hello '(hello how are you, hello ?)) 2)
```
Bom, é isso, pessoal. Lembrando aqui é o objetivo desses posts é apenas mostrar os principais pontos do livro, não detalhar tudo. Portanto, se quiser se aprofundar mais nos conceitos, leia o livro, é muito divertido!
 Se você puder, tente dar uma olhada nas ilustrações do livro, são bem legais :).
 <h2 id="chapter5">Capítulo 5 - Oh my Gawd: It's full of stars!</h2>

 Esse capítulo trata de funções que recebem listas de listas.
 Por exemplo, a primeira pergunta é:
 ```
 O que é (rember* a l)?
 onde a é cup
 e l é ((coffee) cup ((tea) cup)
        (and (hick)) cup)

rember* é pronunciado como rember-star
 ```
 Então, o modo que estávamos usando até aqui vai ter que sofrer uma leve adaptação para percorrer as listas dentro de listas.

 Primeiro vou mostrar a implementação da _rember*_
 ```scheme
(define rember*
  (lambda (a l)
    (cond
      [(null? l) '()]
      [(list? (car l)) (cons (rember* a (car l))
                             (rember* a (cdr l)))]
      [(eq? a (car l)) (rember* a (cdr l))]
      [else (cons (car l) (rember* a (cdr l)))])))

(define L '(((tomato sauce))
            ((bean) sauce)
            (and ((flying)) sauce)))

(define E '(((tomato))
            ((bean))
            (and ((flying)))))

(check-equal? (rember* 'sauce L) E)
 ```

 Comparando a `rember` e `rember*` , percebemos que mais uma pergunta é feita, além de `(null?)` e `(eq?)`. No livro, o autor não utiliza `(list?)`, mas sim `(atom?)`, mas não faz diferença.
 Essa nova pergunta, `(list?)` serve para percorrer a lista interna, caso exista, fazendo duas chamadas recursivas: `(rember* a (car l)) e (rember* a (cdr l))`

 Agora temos o Primeiro Mandamento na versão finalizada

 ---
 > ## <center>Primeiro Mandamento (final)</center>
 > #### Quando se está recorrendo em uma lista de átomos, lat, faça duas perguntas: (null? lat) e else.
 > #### Quando se está recorrendo em um número n, faça duas perguntas: (zero? n) e else.
 > #### Quando se está recorrendo em uma lista de S-expressions l, faça três perguntas: (null? l), (atom? l) e else.

 ---
Temos a versão final do Quarto Mandamento também:
> ## <center>Quarto Mandamento</center>
 > #### Sempre mude pelo menos um argumento enquanto está fazendo recursão.

 > #### Em uma lista de átomos, lat, use (cdr lat);

 > #### Em um número, n, use (zero? n). Em S-expressions, l, use (car l) e (cdr l) se (null? l) e (atom? (car l)) *não* forem verdade.

 Aqui vai mais uma função que recorre em sublistas:
 ```scheme
(define subst*
  (lambda (new old l)
    (cond
      [(null? l) '()]
      [(list? (car l)) (cons (subst* new old (car l))
                             (subst* new old (cdr l)))]
      [(eq? (car l) old) (cons new (subst* new old (cdr l)))]
      [else
        (cons (car l) (subst* new old (cdr l)))])))

(define L-subst* '((banana)
                   (split ((((banana ice)))
                           (cream (banana))
                           sherbet))
                   (banana)
                   (bread)
                   (banana brandy)))
(define E-subst* '( (orange)
                   (split ((((orange ice)))
                           (cream (orange))
                           sherbet) )
                   (orange)
                   (bread)
                   (orange brandy)))

(check-equal?(subst* 'orange 'banana L-subst*) E-subst* )
 ```
 A lógica é a mesma, respeitando os mandamentos fica fácil de definir essas funções.

 Utilizando uma função que vimos no capítulo 4, podemos construir uma função que verifica se duas listas são iguais. O interessante é que a lista pode conter outras listas. Vamos para a definição e testes:
```scheme
(define eqlist?
  (lambda (l1 l2)
    (cond
      [(and (null? l1) (null? l2))
       #t]
      [(or (null? l1) (null? l2))
       #f]
      [(and
         (atom? (car l1))
         (atom? (car l2 )))
       (and (eqan? (car l1)
                   (car l2))
            (eqlist? (cdr l1)
                     (cdr l2)))]
      [(or
         (atom?
           (car l1))
         (atom? (car l2)))
       #f]
      [else
        (and
              (eqlist? (car l1)
                       (car l2))
              (eqlist? (cdr l1) (cdr l2)))
            ]
      )))
(check-equal? (eqlist? '() '()) #t)
(check-equal? (eqlist? '() '(1 2)) #f)
(check-equal? (eqlist? '(1 2) '(1 2)) #t)
(check-equal? (eqlist? '(1 2 (3 4)) '(1 2 (3 4))) #t)
(check-equal? (eqlist? '(3 4 (1 2)) '(1 2 (3 4))) #f)
```
Essa já é uma função um pouco maior, pois precisa fazer algumas comparações a mais porque ela recorre sobre duas listas ao mesmo tempo

Para facilitar ainda mais, podemos definir a função que compara igualdade, ainda mais genérica que a _equan?_, que aceita listas.

```scheme
(define equal?
  (lambda (x1 x2)
    (cond
      [(and (atom? x1)
            (atom? x2))
       (eqan? x1 x2)]
      [(or
         (atom? x1 )
         (atom? x2)) #f]
      [else
        (eqlist? x1 x2)])))
(check-equal? (equal? '() '()) #t)
(check-equal? (equal? '() '(1 2)) #f)
(check-equal? (equal? '(1 2) '(1 2)) #t)
(check-equal? (equal? '(1 2 (3 4)) '(1 2 (3 4))) #t)
(check-equal? (equal? '(3 4 (1 2)) '(1 2 (3 4))) #f)
(check-equal? (equal? 1 1) #t)
(check-equal? (equal? 1 2) #f)
```
Repare que essa função possui bastante testes, pois ela lida com muitas coisas diferentes. Perceba que quando a função lida com vários tipos de dados, o número de teste aumenta bastante!

Foi isso que gostaria de passar do capítulo 5. Sigamos para o Capítulo 6, no qual falaremos de um simples interpretador de expressões aritméticas!


<h2 id="chapter6">Capítulo 6 - Shadows</h2>

Ocorreu um pequeno imprevisto no capítulo 6,7 e 8.
Eu havia lido os capítulos e feito os exercícios do livro, que são as perguntas. No entanto, aconteceu um probleminha que já acometeu todo usuário UNIX.
Fui apagar uma pasta e digitei `rm -rf * pasta`, e na verdade o correto seria `rm -rf pasta/*`. Como era uma pasta de 16 GB, achei que a demora era por isso, mas no fim deletou todos os arquivos pessoais do Linux. Alguns eu tinha backup, mas a maioria não. Enfim, tive que refazer os exercícios :(.

As primeiras perguntas são em relação a expressões aritméticas,e suas representações.

Qual é a reprentações que temos de expressões aritméticas? São algo do tipo: _3 + 4 * 5_ correto? Então vamos tratar de criar uma função que lê esses números e retorna o valor.

Primeiro precisamos de funções auxiliares.

```scheme
(define first
  (lambda (l)
    (car l)
    ))
(define second
  (lambda (l)
    (car (cdr l))))
(define third
  (lambda (l)
    (car (cdr (cdr l)))))

; exponencial
(define e
  (lambda (n x)
    (cond
      [(zero? x) 1]
      [else (* n (e n (sub1 x)))])))
  ```
  Essas já são suficientes para termos uma melhor abstração do problema

  Como verificamos se uma determinada expressão é válida? A nossa representação vai ser uma lista, na verdade uma S-Expression, que já estamos usando faz bastante tempo. Por exemplo, '(+ 2 3) representa uma S-expression e cada elemento é um átomo. Então, o '+ é diferente da função +, ela é só um símbolo.

  Para uma S-expression ser válida, ela deve respeitar as regras de uma expressão aritmética, no caso ser do tipo:
  - exp: número
  - exp: (numero op número)
  - exp: (exp op exp)

  No qual _número_ representa qualquer valor inteiro, _op_ representa a operação e _exp_ a expressão aritmética. Trataremos de apenas três operações aqui, adição(+), multiplicação(*) e exponencial(e).

  Em racket, podemos verificar se a expressão do tipo (exp op exp) é válida com seguinte função:
```scheme
(define numbered?
  (lambda (e)
    (cond
      [(atom? e) (number? e)]
      [(or (eq? (second e) '+)
           (eq? (second e) '*)
           (eq? (second e) 'e))
       (and (numbered? (first e)) (numbered? (third e)))])))

(check-equal? (numbered? '(1 + 3)) #t)
(check-equal? (numbered? '(1 + sausage)) #f)
(check-equal? (numbered? '(1 + (1 + 2))) #t)
```

Perceba o uso das funções auxiliares para não sobrecarregar de car e cdr.

Os autores apresentam o sétimo mandamento, que é o seguinte:
> Recorra nas subpartes que são da mesma natureza.
> - Nas sublistas de uma lista
> - Nas subepressões de uma expressão aritmética


Vamos para a definição mais legal por enquanto, que é a de realizar a leitura da expressão e retornar o valor
```scheme
(define val
  (lambda (x)
    (cond
      [(number? x) x]
      [(equal? (second x) '+)
       (+ (val (car x)) (val (third x)))]
      [(equal? (second x) '*)
       (* (val (car x)) (val (third x)))]
      [(equal? (second x) 'e)
       (e (val (car x)) (val (third x)))])))

(check-equal? (val '(3 + 2)) 5)
(check-equal? (val '(3 + 1)) 4)
(check-equal? (val '(3 + (1 + 1))) 5)
(check-equal? (val '(3 * (1 + 1))) 6)
(check-equal? (val '(3 * (2 e 2))) 12) ; 3 * 2^2
```

De brinque, temos outra implementação, mas nesse caso a expressão é do formato (op exp exp), conhecido como prefix notation, a que usamos em LISP
```scheme
(define val-left
  (lambda (exp)
    (cond
      [(number? exp) exp]
      [(equal? (first exp) '+)
       (+ (val-left (second exp)) (val-left (third exp)))]
      [(equal? (first exp) '*)
       (* (val-left (second exp)) (val-left (third exp)))]
      [(equal? (first exp) 'e)
       (e (val-left (second exp)) (val-left (third exp)))])))
(check-equal? (val-left '(+ 3 2)) 5)
(check-equal? (val-left '(+ 3 1)) 4)
(check-equal? (val-left '(+ 3 (+ 1 1))) 5)
(check-equal? (val-left '(* 3 (+ 1 1))) 6)
(check-equal? (val-left '(* 3 (e 2 2 ))) 12)
```
O legal de LISP é que essas funções são muito fáceis de implementar. Em apenas algumas linhas de código já podemos considerar que temos um interpretador de expressões aritméticas.

<h2 id="chapter7">Capítulo 7 - Friends and Relations</h2>

Outro capítulo maneiro, este sobre conjuntos.

Esse capítulo explora os conjuntos e a utilização de funções auxiliares para desenvolver os programas.

O que é um conjunto? Bom, no meu conhecimento básico, definiria conjunto como uma coleção de elementos únicos no qual a ordem não importa. Ou seja, (maça banana abacate mamão) é um conjunto, (banana, maçã abacate mamão) é o mesmo conjunto, pois a ordem não importa. E (maçã maçã pêra banana) não é um conjunto porque existem elementos repetidos.

Para fazer uma função que verifica se uma coleção de elementos é um conjunto, precisamos então verificar se cada elemento da lista é único.
```scheme
(define set?
  (lambda (l)
    (cond
      [(null? l) #t]
      [(member? (car l) (cdr l)) #f]
      [else (set? (cdr l))])))

(check-equal? (set? '(apple peaches apple plum))#f) ; elemento repetido: apple
(check-equal? (set? '(apple peaches pears plum)) #t)
```
 A função member já foi definida no capítulo 2


Para criar um conjunto a partir de uma lista, fazemos assim:

```scheme
(define makeset
  (lambda (lat)
    (cond
      [(null? lat) '()]
      [(member? (car lat) (cdr lat))
       (makeset (cdr lat))]
      [else (cons (car lat) (makeset (cdr lat)))])))
(check-equal? (makeset '(apple peach pear peach
                         plum apple lemon peach))
              '(pear plum apple lemon peach))

```

Outras funções legais são a que verifica se um conjunto é subconjunto do outro, `subset? s1 s2`, e a função de união e interseção. Deixarei a implementação delas aqui.
```scheme
(define subset?
  (lambda (set1 set2)
    (cond
      [(null? set1 ) #t]
      [(member? (car set1) set2)
       (subset? (cdr set1) set2)]
      [else #f])))
(check-equal? (subset? '(5 chicken wings)
                       '(5 hamburgers
                         2 pieces fried chicken and
                         light duckling wings)) #t)

(check-equal? (subset? '(4 pounds of horseradish)
                       '(four pounds chicken
                         and 5 ounces horseradish)) #f)
(define intersect
  (lambda (set1 set2)
    (cond
      [(null? set1) '()]
      [(member? (car set1 ) set2)
       (cons (car set1) (intersect (cdr set1) set2))]
      [else (intersect (cdr set1) set2)])))
(check-equal? (intersect '(stewed tomatoes and macaroni)
                         '(macaroni and cheese)) '(and macaroni))


(define union
  (lambda (set1 set2)
    (cond
      [(null? set1) set2]
      [(member? (car set1) set2) (union (cdr set1) set2)]
      [else (cons (car set1) (union (cdr set1) set2))])))

(check-equal? (union '(stewed tomatoes and macaroni casserole)
                         '(macaroni and cheese))
              '(stewed tomatoes casserole macaroni and cheese))
```
Por fim, os autores apresentam o conceito de par, que seria uma estrutura do tipo (a b), com a e b não nulos. Vou deixar aqui a definição da função `a-pair?` e vários exemplos

```scheme
(define a-pair?
  (lambda (l)
    (cond
      [(null? l) #f]
      [(atom? l) #f]
      [(null? (cdr l)) #f]
      [(null? (cdr (cdr l))) #t]
      [else #f])))

(check-equal? (a-pair? '(pear pear)) #t)
(check-equal? (a-pair? '(pear pear pear)) #f)
(check-equal? (a-pair? '(3 7)) #t)
(check-equal? (a-pair? '(full (house))) #t)
(check-equal? (a-pair? '((2) (pair))) #t)
```

Por fim, um bom exemplo da vantagem de usarmos funções auxiliares é a função que recebe uma lista de pairs e retorna cada par invertido. Bom, vamos para a implementação mais näive e depois para a que usa funções auxiliares

```scheme
(define revrel-naive
  (lambda (rel)
    (cond
      [(null? rel) '()]
      [else
        (cons
          (cons (car (cdr (car rel)))
                (cons
                (car (car rel)) '()) )
          (revrel-naive (cdr rel))
          )])))

(check-equal? (revrel-naive '((1 2) (3 4)))
              '((2 1) (4 3)))
```
Percebe a dificuldade de entender todos esses cons, cars e cdrs? Evite que seu código chege a esse nível e sempre busque abstrair os conceitos para serem mais fáceis de serem entendidos, mas se preocupe com isso realmente depois que você pensou na implementação mais simples.

Aqui está a função com funções auxiliares
```scheme
(define build-rev
  (lambda (a-pair)
    (cons (second a-pair) (cons (first a-pair) '()))))

(define revrel
  (lambda (rel)
    (cond
      [(null? rel) '()]
      [else (cons (build-rev (first rel))
                  (revrel (cdr rel)))])))

(check-equal? (revrel '((1 2) (3 4)))
              '((2 1) (4 3)))

```
Deste capítulo, foi isso que gostaria trazer. O próximo capítulo, o 8, trata das funções lambda. Sugiro bastante dar uma olhada!

<h2 id="chapter8">Capítulo 8 - Lambda the Ultimate</h2>

Separei esse capítulo em dois porque trata de dois assuntos correlatos e que adicionam um complexidade maior no código, principalmente _continuations_.

# Parte 1 - Funções que aceitam funções como parâmetros

Vamos ver aqui as conhecidas funções que recebem funções como parâmetro.
Lembra da função _rember_? Então, ela era definida para átomos. E se quiséssemos remover uma lista dentro de outra lista usando rember, o que precisamos fazer? Talvez você tenha pensado em replicar a função e mudar a comparação que ela faz. Uma regra básica que tentamos seguir no desenvolvimento de software - uma das mais importantes -, é o princípio DRY: Don't Repeat Yourself.

Quanto mais código repetido temos, mais difícil de lidar com as mudanças. Por exemplo, suponha que você tem 5 funções `rember` diferente e descobre um bug na implementação que afeta todas essas funções. Para corrigir o bug, você precisa arrumar as 5 funções, um trabalho considerável! Imagine então que isso acontece em um sistema grande, que várias funções são dependentes umas das outas, estão em lugares separados, etc, seria muito estressante arrumar!

Portanto, com essa digressão conseguimos ver como é importante definir funções com características mais genéricas.

Um `rember` que aceita várias comparações diferentes é bem simples de implementar
```scheme
(define rember-f
  (lambda (test? a l)
    (cond
      [(null? l) '()]
      [(test? a (car l))
       (rember-f test? a (cdr l))]
      [else
        (cons (car l)
              (rember-f test? a (cdr l)))])))

(check-equal? (rember-f = 2 '(1 2 3 4 5 6))
              '(1 3 4 5 6))
(check-equal? (rember-f eq? 'jelly '(jelly beans are good))
              '(beans are good))
(check-equal? (rember-f equal?
                        '(pop corn)
                        '(lemonade (pop corn) and (cake)))
              '(lemonade and (cake)))

```
É possível fazer `test?` como parâmetro porque uma função em LISP nada mais é do que um valor também! Tudo é um valor.  A maior diferença deste _rember_ e do outro é que este aceita uma função, `test?`, e compara a partir dela na condição. Bem útil desenvolver funções que recebem funções, né?

Uma função que os autores exploram bastante é a `insert-g`, que aceita uma função _seq_, e retorna outra função com _new_, _old_, _l_ como argumento. Primeiros vamos à definição.
```scheme
(define insert-g
  (lambda (seq)
    (lambda (new old l)
      (cond
        [(null? l) '()]
        [(eq? old (car l))
         (seq new old ((insert-g seq) new old (cdr l)))]
        [else
           (cons (car l) ((insert-g seq) new old (cdr l)))]))))
```
Essa função é, pelo nome, de inserção. Olhando pela segunda condição, verificamos que ela usa a função _seq_ quando o elemento da lista for igual ao parâmetro _old_. Por exemplo

```scheme
(define insertR (insert-g (lambda (new old l) (cons old (cons new l)))))
(check-equal? (insertR 'it 'doing '(doing again)) '(doing it again))
```

passamos para o argumento _seq_ esta função que adiciona o novo elemento na direita.
E se quiséssemos remover o elemento quando encontrado? Simples! Usando _insert-g_, ficaria assim:
```scheme
(define rember
  (lambda (a l)
    ((insert-g (lambda (new old l) l)) '_ a l)))
(check-equal? (rember 'early '(the early bird catches the worm))
              '(the bird catches the worm))
  ```
  Dois pontos a serem observados aqui
  - Estamos chamamos a função com os 3 parâmetros, mas primeiro chamamos (insert-g função) e depois chamamos a outra função com os dois parâmetros.
  - o segundo argumento, _new_, pode ser qualquer coisa, pois ele não é usado.

Por último, gostaria de falar que as linguagens de programação que aceitam esses lambdas aninhados são chamadas de linguagens que implementam currying. Na verdade, a definição de currying é a de aplicação parcial de funções. As linguagens que realmente suportam esta técnica recebem um argumento por função, ou seja, o lambda recebe um único argumento. Por que isso? Para quando precisarmos, podermos usar a versão parcial delas, como no caso da _insert-g_. O legal é que você não precisa escrever cada função com um argumento, a própria linguagem traduz sua função em várias funções com um único argumento. Se quiser estudar esse assunto, procure sobre Haskell.


# Parte 2 - Continuations

Essa parte sobre Continuations foi a que achei a mais complicada do livro até então. O que seria isso?  Primeiramente, um exemplo

```scheme
(define rember&co
  (lambda (a lat col)
    (cond
      [(null? lat) (col '() '())]
      [(eq? (car lat) a)
       (rember&co a
                  (cdr lat)
                  (lambda (newlat seen)
                    (col newlat
                         (cons (car lat) seen))))]
      [else
        (rember&co a
                   (cdr lat)
                   (lambda (newlat seen)
                     (col (cons (car lat) newlat)
                          seen)))])))
```
O que seria o argumento _col_? De acordo com o livro, _col_ vem de collector, também chamado de continuation.

E qual a funcionalidade dele?
Olhando para essa nossa função _rember&co_, estamos coletando os itens removidos em um lado, e os outros itens em outra lista e aplicando a função `col` neles. Qual a utilidade disso? Estamos realizando mais de uma operação naquela lista utilizando a mesma função. Se quisermos remover os itens de acordo com o parâmetro _a_ e, ao mesmo tempo, calcular quantos itens foram removidos, podemos usar essa função facilmente.

Esse assunto é um pouco denso e o livro passa bem rapidamente, então certamente não é um aprofundamento no tema, mas deixei essa função para mostrar uma simples aplicação desse tema que não é tão incomum de ver por aí. Qualquer dúvida, pode entrar em contato comigo!


<h2 id="chapter9">Capítulo 9 - And again, and Again and Again...</h2>
O que dizer desse capítulo? São tratados alguns temas bem importantes - e complicados - em computação.

Primeiro, o problema da parada, em ingles [Halting Problem].
Vamos seguir a linha do livro e mostrar um exemplo para explorar o assunto

Digamos que temos uma função, eternity:
```scheme
(define eternity
  (lambda (x)
    (eternity x)))
  ```
  Essa função chama ela mesma infinitamente, sem alterar nenhuma estrutura. Sabemos então que essa função não para, correto?
  Agora vamos olhar essa outra função, a [função de Ackermann].
```scheme
(define A
  (lambda (n m)
    (cond
      [(zero? n) (add1 m)]
      [(zero? m) (A (sub1 n) 1)]
      [else (A (sub1 n)
               (A n (sub1 m)))])))
(check-equal? (A 1 1 ) 3)
(check-equal? (A 1 0 ) 2)
(check-equal? (A 2 2 ) 7)
```
Essa função para? Ela para para `(A 4 3)`? Não, ela fica em um loop infinito. Tente para ver! Os autores nos questionam, então, se é possível criar uma função _f_ que recebe como parâmetro outra função, _g_, e que retorna se ela para ou não. Bem fácil!

Aqui um esboço, ela é tão simples que deixo como exercício para você.
```scheme
(define will-stop?
    (lambda (f)
        ...))
```
Brincadeira, não é não!! Mas vamos supor que esta função está pronta já. Problema resolvido.

Se ela resolve todos os problemas, vamos testar funções.

Para a função `(will-stop? length)` ela funciona perfeitamente, retornando `#t`.

Vamos testar mais uma então, _last-try_,
```scheme
(define last-try
  (lambda (x)
    (and (will-stop? last-try) (eternity x))))
```
Se o argumento for `'()` ela para?
Para determinarmos isso, precisamos do resultado de
```scheme
(and (will-stop? last-try) (eternity '()))
```
E qual é esse resultado?
Depende do `(will-stop? last-try)`.
Ok, vamos supor que last-try retorne #t, então
```scheme
(and #t (eternity '()))
```
Hmm, estranho. Qual o resultado disso? O resultad depende de `(eternity '())`, mas `(eternity x)` roda infinitamente. Logo, a função não para. Mas assumimos que ela parava! Então entramos em uma contradição.

Vamos assumir então que essa função nunca vai parar.
```scheme
(and (will-stop? last-try) (eternity '()))
(and #f (eternity '()))
```
Hmm, estranho de novo. A função `and` com qualquer coisa `#f` retorna `#f`. Então essa função vai parar, entrando em uma contradição de novo!

Esse é o problema da parada! Não é possível criar uma função que determina se determinada função para ou não. Ela é importante porque marca um limite à computação.

O outro tópico abordado é sobre as Y Combinators, essa parte é relativamente difícil, então sugiro que você use o Stepper do DrRacket e leia essa parte no livro. [Aqui] está um link que ajuda a entender um pouco melhor essa parte.

[Halting Problem]: https://en.wikipedia.org/wiki/Halting_problem
[função de Ackermann]: https://en.wikipedia.org/wiki/Ackermann_function
[Aqui]: https://stackoverflow.com/questions/10499514/y-combinator-discussion-in-the-little-schemer/11864862#11864862
<h2 id="chapter10">Capítulo 10 - What is the value of all this? </h2>

Cheguei até a escrever um pouco sobre esse capítulo, mas ele é bem denso e requer alguns dias de leitura.
Salvei o [código] que fiz na leitura desse capítulo no Github. Precisa de alguns ajustes, como comentar melhor as funções e executar testes em cada função. Ademais, acredito que pode servir se estiver com alguma dúvida.



[codigo]: https://github.com/jvrn3/tls-scheme-interpreter
