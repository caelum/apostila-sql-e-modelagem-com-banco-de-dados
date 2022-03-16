# Muitos alunos e o LIMIT

Precisamos de um relatório que retorne todos os alunos, algo como selecionar o nome de todos eles, com um `SELECT` simples:

```
SELECT a.nome FROM aluno a;

+------------------+
| nome             |
+------------------+
| João da Silva    |
| Frederico José   |
| Alberto Santos   |
| Renata Alonso    |
| Paulo da Silva   |
| Carlos Cunha     |
| Paulo José       |
| Manoel Santos    |
| Renata Ferreira  |
| Paula Soares     |
| Jose da Silva    |
| Danilo Cunha     |
| Zilmira José     |
| Cristaldo Santos |
| Osmir Ferreira   |
| Claudio Soares   |
+------------------+
```

Para melhorar o resultado podemos ordernar a *query* por ordem alfabética do nome:

```
SELECT a.nome FROM aluno a ORDER BY a.nome;

+------------------+
| nome             |
+------------------+
| Alberto Santos   |
| Carlos Cunha     |
| Claudio Soares   |
| Cristaldo Santos |
| Danilo Cunha     |
| Frederico José   |
| João da Silva    |
| Jose da Silva    |
| Manoel Santos    |
| Osmir Ferreira   |
| Paula Soares     |
| Paulo da Silva   |
| Paulo José       |
| Renata Alonso    |
| Renata Ferreira  |
| Zilmira José     |
+------------------+
```

Vamos verificar agora quantos alunos estão cadastrados:

```
SELECT count(*) FROM aluno;

+----------+
| count(*) |
+----------+
|       16 |
+----------+
```

Como podemos ver, é uma quantidade relativamente baixa, pois quando estamos trabalhando em uma aplicação real, geralmente o volume de informações é muito maior.

No facebook, por exemplo, quantos amigos você tem? Quando você entra no facebook, aparece todas as atualizações dos seus amigos de uma vez? Todas as milhares de atualizações de uma única vez? Imagine a loucura que é trazer milhares de dados de uma única vez para você ver apenas 5, 10 notificações. Provavelmente vai aparecendo aos poucos, certo? Então que tal mostrarmos os alunos aos poucos também? Ou seja, fazermos uma **paginação** no nosso relatório, algo como  5 alunos "por página". Mas como podemos fazer isso? No MySQL, podemos **limitar** em 5 a quantidade de registros que desejamos retornar:

```
SELECT a.nome FROM aluno a 
ORDER BY a.nome
LIMIT 5;

+------------------+
| nome             |
+------------------+
| Alberto Santos   |
| Carlos Cunha     |
| Claudio Soares   |
| Cristaldo Santos |
| Danilo Cunha     |
+------------------+
```

Nesse caso retornamos os primeiros 5 alunos em ordem alfabética. O ato de limitar é extremamente importante a medida que os dados crescem. Se você tem mil mensagens antigas, não vai querer ver as mil de uma vez só, traga somente as 10 primeiras e, se tiver interesse, mais 10, mais 10 etc.

## Limitando e buscando a partir de uma quantidade específica

Agora vamos pegar os próximos 5 alunos, isto é, senhor MySQL, ignore os 5 primeiros, e depois pegue para mim os próximos 5:

```
SELECT a.nome FROM aluno a 
ORDER BY a.nome
LIMIT 5,5;

+-----------------+
| nome            |
+-----------------+
| Frederico José  |
| João da Silva   |
| Jose da Silva   |
| Manoel Santos   |
| Osmir Ferreira  |
+-----------------+
```

`LIMIT 5`? `LIMIT 5,5`? Parece um pouco estranho, o que será que isso significa? Quando utilizamos o `LIMIT` funciona da seguinte maneira: `LIMIT linha_inicial,qtd_de_linhas_para_avançar`, ou seja, quando fizemos `LIMIT 5`, informamos ao MySQL que avançe 5 linhas apenas, pois por padrão ele iniciará pela primeira linha, chamada de linha `0`. Se fizéssemos `LIMIT 0,5`, por exemplo:

```
SELECT a.nome FROM aluno a 
ORDER BY a.nome
LIMIT 0,5;

+------------------+
| nome             |
+------------------+
| Alberto Santos   |
| Carlos Cunha     |
| Claudio Soares   |
| Cristaldo Santos |
| Danilo Cunha     |
+------------------+
```

Perceba que o resultado é o mesmo que `LIMIT 5`! Se pedimos `LIMIT 5,10` o que ele nos trás?

```
SELECT a.nome FROM aluno a 
ORDER BY a.nome
LIMIT 5,10;

+-----------------+
| nome            |
+-----------------+
| Frederico José  |
| João da Silva   |
| Jose da Silva   |
| Manoel Santos   |
| Osmir Ferreira  |
| Paula Soares    |
| Paulo da Silva  |
| Paulo José      |
| Renata Alonso   |
| Renata Ferreira |
+-----------------+
``` 

O resultado iniciará após a linha 5, ou seja, linha 6 e avançará 10 linhas. Vamos demonstrar os exemplos todos de uma única vez:

Todos os alunos:

```
SELECT a.nome FROM aluno a;

+------------------+
| nome             |
+------------------+
| Alberto Santos   |
| Carlos Cunha     |
| Claudio Soares   |
| Cristaldo Santos |
| Danilo Cunha     |
| Frederico José   |
| João da Silva    |
| Jose da Silva    |
| Manoel Santos    |
| Osmir Ferreira   |
| Paula Soares     |
| Paulo da Silva   |
| Paulo José       |
| Renata Alonso    |
| Renata Ferreira  |
| Zilmira José     |
+------------------+
```

Pegando os 5 primeiros:

```
SELECT a.nome FROM aluno a 
ORDER BY a.nome
LIMIT 5;

+------------------+
| nome             |
+------------------+
| Alberto Santos   |
| Carlos Cunha     |
| Claudio Soares   |
| Cristaldo Santos |
| Danilo Cunha     |
+------------------+
```

Ignorando os 5 primeiros, pegando os próximos 5 alunos:

```
SELECT a.nome FROM aluno a 
ORDER BY a.nome
LIMIT 5,5;

+-----------------+
| nome            |
+-----------------+
| Frederico José  |
| João da Silva   |
| Jose da Silva   |
| Manoel Santos   |
| Osmir Ferreira  |
+-----------------+
```

## Resumindo

Nesse capítulo vimos como nem sempre retornar todos os registros das tabelas são necessários, algumas vezes, precisamos filtrar a quantidade de linhas, pois em uma aplicação real, podemos lidar com uma quantidade bem grande de dados. Justamente por esse caso, podemos limitar as nossas *queries* utilizando a instrução `LIMIT`. Vamos para os exercícios?

# Exercícios

1. Escreva uma query que traga apenas os dois primeiros alunos da tabela.

2. Escreva uma SQL que devolva os 3 primeiros alunos que o e-mail termine com o domínio ".com".

3. Devolva os 2 primeiros alunos que o e-mail termine com ".com", ordenando por nome.

4. Devolva todos os alunos que tenham Silva em algum lugar no seu nome.
