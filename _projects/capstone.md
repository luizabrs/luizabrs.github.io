---
layout: page
title: Capstone Project
description: This is a quick reading about my undergraduation thesis project 
img: assets/img/ffpir5.png
importance: 1
category: projects
related_publications: false
toc:
  sidebar: left
---
This post is dedicated to my Capstone Project (Trabalho de Conclusão de Curso) for the Computer Science BSc. degree.


## Student and Supervisors

> **Student:**Luiza Barros Reis Soezima
>
> NUSP: 11221842
>
> University of São Paulo (USP), Instituto de Matemática e Estatística (IME)

> **Supervisor:** Hilder Vitor Lima Pereira (hilder@unicamp.br)
>
> Universidade Estadual de Campinas (UNICAMP), Instituto de Computação, Campinas, Brazil

> **Co-supervisor:** Alfredo Goldman (gold@ime.usp.br)
>
> University of São Paulo (USP), Instituto de Matemática e Estatística (IME)

## About the Capstone project

**Title:** Folding FrodoPIR: Private Information Retrieval With Optimization

**Abstract:**

Retrieving information from databases pulls a constant activity on the daily routine, concurrently, 
its privacy concern when it comes to sensitive data develop a parallel problem. 
Private information retrieval (PIR) is a privacy protocol that allows a user to download a required message 
from a set of messages stored in a database without revealing the index of the required message to the databases. 

In other words, PIR is protocol in which from one side, a possibly untrusted server holds a public database $$DB$$ with $$N$$ records. 
On the other side, a client wants to query for record $$i \in \{0 \cdots N-1\}$$, 
without letting the server learn the queried item they are looking up 
(and, hence, learning the value $$v$$ associated with $$i$$ they are interested in). 
A naive solution involves the client locally downloading the whole $$DB$$, 
but that can be  expensive: the goal of PIR is to both preserve privacy and be more 
efficient than the total cost of downloading the whole $$DB$$. There are many proposed solutions for this problem, 
and for this Capstone Project, we will explore the ones that uses 
Fully Homomorphic Encryption (FHE) as cryptographic primitive. 


## Monograph

Click there to can download the [monograph](/assets/pdf/monograph.pdf)
## Other informations

If you want, you can access my official project website [here](https://www.linux.ime.usp.br/~lbrsoezima/) [To be modified]
