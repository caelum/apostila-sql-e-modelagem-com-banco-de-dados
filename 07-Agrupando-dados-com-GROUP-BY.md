# Agrupando dados com GROUP BY

A instuição solicitou a média de todos os cursos para fazer uma comparação de notas para verificar se todos os cursos possuem a mesma média, quais cursos tem menores notas e quais possuem as maiores notas. Vamos verificar a estrutura de algumas tabelas da nossa base de dados, começaremos pela tabela `curso`:

```
DESC curso;

+-------+--------------+------+-----+---------+----------------+
| Field | Type         | Null | Key | Default | Extra          |
+-------+--------------+------+-----+---------+----------------+
| id    | int(11)      | NO   | PRI | NULL    | auto_increment |
| nome  | varchar(255) | NO   |     |         |                |
+-------+--------------+------+-----+---------+----------------+
```

Agora vamos verificar a tabela `secao`:

```
DESC secao;

+------------+--------------+------+-----+---------+----------------+
| Field      | Type         | Null | Key | Default | Extra          |
+------------+--------------+------+-----+---------+----------------+
| id         | int(11)      | NO   | PRI | NULL    | auto_increment |
| curso_id   | int(11)      | NO   |     | NULL    |                |
| titulo     | varchar(255) | NO   |     |         |                |
| explicacao | varchar(255) | NO   |     | NULL    |                |
| numero     | int(11)      | NO   |     | NULL    |                |
+------------+--------------+------+-----+---------+----------------+
```

Já podemos perceber que existe uma relação entre `curso` de `secao`. Vamos também dar uma olhada na tabela `exercicio`:

```
DESC exercicio;

+------------------+--------------+------+-----+---------+----------------+
| Field            | Type         | Null | Key | Default | Extra          |
+------------------+--------------+------+-----+---------+----------------+
| id               | int(11)      | NO   | PRI | NULL    | auto_increment |
| secao_id         | int(11)      | NO   |     | NULL    |                |
| pergunta         | varchar(255) | NO   |     | NULL    |                |
| resposta_oficial | varchar(255) | NO   |     | NULL    |                |
+------------------+--------------+------+-----+---------+----------------+
```

Observe que na tabela `exercicio` temos uma associação com a tabela `secao`. Agora vamos verificar a tabela `resposta`:

```
DESC resposta

+---------------+--------------+------+-----+---------+----------------+
| Field         | Type         | Null | Key | Default | Extra          |
+---------------+--------------+------+-----+---------+----------------+
| id            | int(11)      | NO   | PRI | NULL    | auto_increment |
| exercicio_id  | int(11)      | YES  |     | NULL    |                |
| aluno_id      | int(11)      | YES  |     | NULL    |                |
| resposta_dada | varchar(255) | YES  |     | NULL    |                |
+---------------+--------------+------+-----+---------+----------------+
```

Podemos verificar que a tabela `resposta` está associada com a tabela `exercício`. Por fim, vamos verificar a tabela `nota`:

```
DESC nota;

+-------------+---------------+------+-----+---------+----------------+
| Field       | Type          | Null | Key | Default | Extra          |
+-------------+---------------+------+-----+---------+----------------+
| id          | int(11)       | NO   | PRI | NULL    | auto_increment |
| resposta_id | int(11)       | YES  |     | NULL    |                |
| nota        | decimal(18,2) | YES  |     | NULL    |                |
+-------------+---------------+------+-----+---------+----------------+
```

Note que também a tabela `nota` possui uma associação, nesse caso com a tabela `resposta`. 

Como vimos, existem muitas tabelas que podemos selecionar em nossa *query*, então vamos montar a nossa *query* por partes. Começaremos pela tabela `nota`:

```
SELECT n.nota FROM nota n;

+-------+
| nota  |
+-------+
|  8.00 |
|  0.00 |
|  7.00 |
|  6.00 |
|  9.00 |
| 10.00 |
|  4.00 |
|  4.00 |
|  7.00 |
|  8.00 |
|  6.00 |
|  7.00 |
|  4.00 |
|  9.00 |
|  3.00 |
|  5.00 |
|  5.00 |
|  5.00 |
|  6.00 |
|  8.00 |
|  8.00 |
|  9.00 |
| 10.00 |
|  2.00 |
|  0.00 |
|  1.00 |
|  4.00 |
+-------+
```

Conseguimos pegar todas as notas, agora precisamos resolver a nossa primeira associação, nesse caso, juntar a tabela `resposta` com a tabela `nota`:

```
SELECT n.nota FROM nota n
JOIN resposta r ON n.resposta_id = r.id;

+-------+
| nota  |
+-------+
|  8.00 |
|  0.00 |
|  7.00 |
|  6.00 |
|  9.00 |
| 10.00 |
|  4.00 |
|  4.00 |
|  7.00 |
|  8.00 |
|  6.00 |
|  7.00 |
|  4.00 |
|  9.00 |
|  3.00 |
|  5.00 |
|  5.00 |
|  5.00 |
|  6.00 |
|  8.00 |
|  8.00 |
|  9.00 |
| 10.00 |
|  2.00 |
|  0.00 |
|  1.00 |
|  4.00 |
+-------+
```

Devolvemos todas as notas associadas com a tabela `resposta`. Agora vamos para o próximo `JOIN` entre a tabela `resposta` e a tabela `exercicio`:

```
SELECT n.nota FROM nota n
JOIN resposta r ON n.resposta_id = r.id
JOIN exercicio e ON r.exercicio_id = e.id;

+-------+
| nota  |
+-------+
|  8.00 |
|  0.00 |
|  7.00 |
|  6.00 |
|  9.00 |
| 10.00 |
|  4.00 |
|  4.00 |
|  7.00 |
|  8.00 |
|  6.00 |
|  7.00 |
|  4.00 |
|  9.00 |
|  3.00 |
|  5.00 |
|  5.00 |
|  5.00 |
|  6.00 |
|  8.00 |
|  8.00 |
|  9.00 |
| 10.00 |
|  2.00 |
|  0.00 |
|  1.00 |
|  4.00 |
+-------+
```

Agora faremos a associação entre a tabela `exercicio` e a tabela `secao`:

```
SELECT n.nota FROM nota n
JOIN resposta r ON n.resposta_id = r.id
JOIN exercicio e ON r.exercicio_id = e.id
JOIN secao s ON e.secao_id = s.id;

+-------+
| nota  |
+-------+
|  8.00 |
|  0.00 |
|  7.00 |
|  6.00 |
|  9.00 |
| 10.00 |
|  4.00 |
|  4.00 |
|  7.00 |
|  8.00 |
|  6.00 |
|  7.00 |
|  4.00 |
|  9.00 |
|  3.00 |
|  5.00 |
|  5.00 |
|  5.00 |
|  6.00 |
|  8.00 |
|  8.00 |
|  9.00 |
| 10.00 |
|  2.00 |
|  0.00 |
|  1.00 |
|  4.00 |
+-------+
```

Por fim, faremos a última associação entre a tabela `secao` e a tabela `curso`.

```
SELECT n.nota FROM nota n
JOIN resposta r ON n.resposta_id = r.id
JOIN exercicio e ON r.exercicio_id = e.id
JOIN secao s ON e.secao_id = s.id
JOIN curso c ON s.curso_id = c.id;

+-------+
| nota  |
+-------+
|  8.00 |
|  0.00 |
|  7.00 |
|  6.00 |
|  9.00 |
| 10.00 |
|  4.00 |
|  4.00 |
|  7.00 |
|  8.00 |
|  6.00 |
|  7.00 |
|  4.00 |
|  9.00 |
|  3.00 |
|  5.00 |
|  5.00 |
|  5.00 |
|  6.00 |
|  8.00 |
|  8.00 |
|  9.00 |
| 10.00 |
|  2.00 |
|  0.00 |
|  1.00 |
|  4.00 |
+-------+
```

Fizemos todas associações que precisávamos, porém repare que ainda está retornando a nota de todos os alunos por curso uma a uma, porém nós precisamos da média por curso! No MySQL, podemos utilizar a função `AVG()` para tirar a média:

``` 
SELECT AVG(n.nota) FROM nota n
JOIN resposta r ON n.resposta_id = r.id
JOIN exercicio e ON r.exercicio_id = e.id
JOIN secao s ON e.secao_id = s.id
JOIN curso c ON s.curso_id = c.id;

+-------------+
| AVG(n.nota) |
+-------------+
|    5.740741 |
+-------------+
```

Observe que foi retornado apenas um valor, será que essa média é igual para todos os cursos? Vamos tentar retornar os cursos e verificarmos:

```
SELECT c.nome, AVG(n.nota) FROM nota n
JOIN resposta r ON n.resposta_id = r.id
JOIN exercicio e ON r.exercicio_id = e.id
JOIN secao s ON e.secao_id = s.id
JOIN curso c ON s.curso_id = c.id;

+----------------------+-------------+
| nome                 | AVG(n.nota) |
+----------------------+-------------+
| SQL e banco de dados |    5.740741 |
+----------------------+-------------+
```

Apenas 1 curso? Não era esse o resultado que esperávamos! Quando utilizamos a função `AVG()` ela calcula todos os valores existentes da *query* e retorna a média, porém em apenas uma linha! Para que a função `AVG()` calcule a média de cada curso, precisamos informar que queremos **agrupar** a média para uma determinada coluna, nesse caso, a coluna `c.nome`, ou seja, para cada curso diferente queremos que calcule a média. Para agruparmos uma coluna utilizamos a instrução `GROUP BY`, informando a coluna que queremos agrupar:

```
SELECT c.nome, AVG(n.nota) FROM nota n
JOIN resposta r ON n.resposta_id = r.id
JOIN exercicio e ON r.exercicio_id = e.id
JOIN secao s ON e.secao_id = s.id
JOIN curso c ON s.curso_id = c.id
GROUP BY c.nome;

+---------------------------------+-------------+
| nome                            | AVG(n.nota) |
+---------------------------------+-------------+
| C# e orientação a objetos       |    4.857143 |
| Desenvolvimento web com VRaptor |    8.000000 |
| Scrum e métodos ágeis           |    5.777778 |
| SQL e banco de dados            |    6.100000 |
+---------------------------------+-------------+
```

O pessoal do comercial da instituição informou que alguns alunos estão reclamando pela quantidade de exercícios nos cursos. Então vamos verificar quantos exercícios existem para cada curso. Primeiro vamos verificar quantos exercícios existem no banco usando a função `COUNT()`:

```
SELECT COUNT(*) FROM exercicio;

+----------+
| COUNT(*) |
+----------+
|       31 |
+----------+
```

Retormos a quantidade de todos os exercícios, porém nós precisamos saber o total de exercícios para cada curso, ou seja, precisamos juntar a tabela `curso`. Porém, para juntar a tabela `curso`, teremos que juntar a tabela `secao`:

```
SELECT COUNT(*) FROM exercicio e
JOIN secao s ON e.secao_id = s.id
```

Agora podemos juntar a tabela `curso` e retornar o nome do curso também:

```
SELECT c.nome, COUNT(*) FROM exercicio e
JOIN secao s ON e.secao_id = s.id
JOIN curso c ON s.curso_id = c.id;

+----------------------+----------+
| nome                 | COUNT(*) |
+----------------------+----------+
| SQL e banco de dados |       31 |
+----------------------+----------+
```

Perceba que o resultado foi similar ao que aconteceu quando tentamos tirar a média sem agrupar! Então precisamos também informar que queremos agrupar a contagem pelo nome do curso. Então vamos adicionar o `GROUP BY`:

```
SELECT c.nome, COUNT(*) FROM exercicio e
JOIN secao s ON e.secao_id = s.id
JOIN curso c ON s.curso_id = c.id
GROUP BY c.nome;

+---------------------------------+----------+
| nome                            | COUNT(*) |
+---------------------------------+----------+
| C# e orientação a objetos       |        7 |
| Desenvolvimento web com VRaptor |        7 |
| Scrum e métodos ágeis           |        9 |
| SQL e banco de dados            |        8 |
+---------------------------------+----------+
```

Note que o nome da coluna que conta todos os exercícios está um pouco estranha, vamos adicionar um alias para melhorar o resultado:
 
```
SELECT c.nome, COUNT(*) AS contagem FROM exercicio e
JOIN secao s ON e.secao_id = s.id
JOIN curso c ON s.curso_id = c.id
GROUP BY c.nome;

+---------------------------------+----------+
| nome                            | contagem |
+---------------------------------+----------+
| C# e orientação a objetos       |        7 |
| Desenvolvimento web com VRaptor |        7 |
| Scrum e métodos ágeis           |        9 |
| SQL e banco de dados            |        8 |
+---------------------------------+----------+
```

Agora o relatório faz muito mais sentido.

Todo final de semestre nós precisamos enviar um relatório para o MEC informando quantos alunos estão matriculados em cada curso da instituição. Faremos novamente a nossa *query* por partes, vamos retornar primeiro todos os cursos:

```
SELECT c.nome FROM curso c
```

Vamos juntar a tabela `matricula`:

```
SELECT c.nome FROM curso c
JOIN matricula m ON m.curso_id = c.id
```

Agora vamos juntar os alunos também:

```
SELECT c.nome FROM curso c
JOIN matricula m ON m.curso_id = c.id
JOIN aluno a ON m.aluno_id = a.id;
```

Precisamos agora contar a quantidade de alunos:

```
SELECT c.nome, COUNT(a.id) AS quantidade FROM curso c
JOIN matricula m ON m.curso_id = c.id
JOIN aluno a ON m.aluno_id = a.id;

+----------------------+------------+
| nome                 | quantidade |
+----------------------+------------+
| SQL e banco de dados |         14 |
+----------------------+------------+
``` 

Lembre-se que precisamos agrupar a contagem pelo nome do curso:

```
SELECT c.nome, COUNT(a.id) AS quantidade FROM curso c
JOIN matricula m ON m.curso_id = c.id
JOIN aluno a ON m.aluno_id = a.id
GROUP BY c.nome;

+------------------------------------+------------+
| nome                               | quantidade |
+------------------------------------+------------+
| C# e orientação a objetos          |          4 |
| Desenvolvimento mobile com Android |          2 |
| Desenvolvimento web com VRaptor    |          2 |
| Scrum e métodos ágeis              |          2 |
| SQL e banco de dados               |          4 |
+------------------------------------+------------+
```

Agora conseguimos realizar o nosso relatório conforme o esperado. 

## Resumindo

Vimos nesse capítulo como podemos gerar relatórios utilizando funções como `AVG()` e `COUNT()`. Vimos também que, se precisamos retornar as colunas para verificar qual é o valor de cada linha, como por exemplo, a média de cada curso, precisamos agrupar essas colunas por meio do `GROUP BY`. Vamos para os exercícios?

# Exercícios

1. Exiba a média das notas por curso.

2. Devolva o curso e as médias de notas, levando em conta somente alunos que tenham "Silva" ou "Santos" no sobrenome.

3. Conte a quantidade de respostas por exercício. Exiba a pergunta e o número de respostas.

4. Você pode ordenar pelo COUNT também. Basta colocar ORDER BY COUNT(coluna).

Pegue a resposta do exercício anterior, e ordene por número de respostas, em ordem decrescente.

5. Podemos agrupar por mais de um campo de uma só vez. Por exemplo, se quisermos a média de notas por aluno por curso, podemos fazer GROUP BY aluno.id, curso.id.