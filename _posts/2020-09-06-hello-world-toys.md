---
layout: post
title: "Hello World - The Little Schemer Capítulo 1"
date: 2020-09-06
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

Salvei algumas anotações do primeiro capítulo que valem a pena serem registradas aqui.

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

Por fim, deixarei aqui algumas outras perguntas do livro


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
