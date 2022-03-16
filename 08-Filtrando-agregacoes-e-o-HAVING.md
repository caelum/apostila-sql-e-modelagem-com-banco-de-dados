# Filtrando agregações e o HAVING

Todo o fim de semestre, a instituição de ensino precisa montar os boletins dos alunos. Então vamos montar a *query* que retornará todas as informações para montar o boletim. Começaremos retornando todas as notas dos alunos:

```
SELECT n.nota FROM nota n
```

Agora vamos associar a com as respostas com as notas:

```
SELECT n.nota FROM nota n
JOIN resposta r ON r.id = n.resposta_id
```

Associaremos agora com os exercícios com as respostas:

```
SELECT n.nota FROM nota n
JOIN resposta r ON r.id = n.resposta_id
JOIN exercicio e ON e.id = r.exercicio_id
```

Agora associaremos a seção com os exercícios:

``` 
SELECT n.nota FROM nota n
JOIN resposta r ON r.id = n.resposta_id
JOIN exercicio e ON e.id = r.exercicio_id
JOIN secao s ON s.id = e.secao_id
```

Agora o curso com a seção:

```
SELECT n.nota FROM nota n
JOIN resposta r ON r.id = n.resposta_id
JOIN exercicio e ON e.id = r.exercicio_id
JOIN secao s ON s.id = e.secao_id
JOIN curso c ON c.id = s.curso_id
```

Por fim, a resposta com o aluno:

```
SELECT n.nota FROM nota n
JOIN resposta r ON r.id = n.resposta_id
JOIN exercicio e ON e.id = r.exercicio_id
JOIN secao s ON s.id = e.secao_id
JOIN curso c ON c.id = s.curso_id
JOIN aluno a ON a.id = r.aluno_id;
```

Verificando o resultado: 

```
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

Observe que estamos fazendo *queries* grandes, aconselhamos que, no momento que precisar fazer uma *query* complexa, faça *queries* menores e testes seus resultados, pois se existir alguma instrução errada, é mais fácil de identificar o problema. 

Agora que associamos todas as nossas tabelas necessárias, vamos tirar a média com a função de agregação `AVG()` que é capaz de tirar médias, conforme visto durante o curso:

```
SELECT AVG(n.nota) FROM nota n
JOIN resposta r ON r.id = n.resposta_id
JOIN exercicio e ON e.id = r.exercicio_id
JOIN secao s ON s.id = e.secao_id
JOIN curso c ON c.id = s.curso_id
JOIN aluno a ON a.id = r.aluno_id;

+-------------+
| AVG(n.nota) |
+-------------+
|    5.740741 |
+-------------+
```

Retornou a média, porém não queremos apenas a média! Precisamos também dos alunos e dos cursos. Então vamos adicioná-los:

```
SELECT a.nome, c.nome, AVG(n.nota) FROM nota n
JOIN resposta r ON r.id = n.resposta_id
JOIN exercicio e ON e.id = r.exercicio_id
JOIN secao s ON s.id = e.secao_id
JOIN curso c ON c.id = s.curso_id
JOIN aluno a ON a.id = r.aluno_id;

+----------------+----------------------+-------------+
| nome           | nome                 | AVG(n.nota) |
+----------------+----------------------+-------------+
| João da Silva  | SQL e banco de dados |    5.740741 |
+----------------+----------------------+-------------+
```

Lembre-se que estamos lidando com uma função de agregação, ou seja, se não informarmos a forma que ela precisa **agrupar** as colunas, ela retornará apenas uma linha! Porém, precisamos sempre pensar em qual tipo de agrupamento é necessário, nesse caso queremos que mostre a média de acada aluno, então agruparemos pelos alunos, porém também que queremos que a cada curso que o alunos fez mostre a sua :

```
SELECT a.nome, c.nome, AVG(n.nota) FROM nota n
JOIN resposta r ON r.id = n.resposta_id
JOIN exercicio e ON e.id = r.exercicio_id
JOIN secao s ON s.id = e.secao_id
JOIN curso c ON c.id = s.curso_id
JOIN aluno a ON a.id = r.aluno_id
GROUP BY a.nome, c.nome;

+-----------------+---------------------------------+-------------+
| nome            | nome                            | AVG(n.nota) |
+-----------------+---------------------------------+-------------+
| Alberto Santos  | Scrum e métodos ágeis           |    5.777778 |
| Frederico José  | Desenvolvimento web com VRaptor |    8.000000 |
| Frederico José  | SQL e banco de dados            |    5.666667 |
| João da Silva   | SQL e banco de dados            |    6.285714 |
| Renata Alonso   | C# e orientação a objetos       |    4.857143 |
+-----------------+---------------------------------+-------------+
```

## Condições com HAVING

Retornamos todas as médias dos alunos, porém a instituição precisa de um relatório separado para todos os alunos que reprovaram, ou seja, que tiraram nota baixa, nesse caso médias menores que 5. De acordo com o que vimos até agora bastaria adicionarmos um `WHERE`:

```
SELECT a.nome, c.nome, AVG(n.nota) FROM nota n
JOIN resposta r ON r.id = n.resposta_id
JOIN exercicio e ON e.id = r.exercicio_id
JOIN secao s ON s.id = e.secao_id
JOIN curso c ON c.id = s.curso_id
JOIN aluno a ON a.id = r.aluno_id
WHERE AVG(n.nota) < 5
GROUP BY a.nome, c.nome;

ERROR 1111 (HY000): Invalid use of group function
```
 
Nesse caso, estamos tentando adicionar condições para uma função de agregação, porém, quando queremos **adicionar condições para funções de agregação** precisamos utilizar o `HAVING` ao invés de `WHERE`:

```
SELECT a.nome, c.nome, AVG(n.nota) FROM nota n
JOIN resposta r ON r.id = n.resposta_id
JOIN exercicio e ON e.id = r.exercicio_id
JOIN secao s ON s.id = e.secao_id
JOIN curso c ON c.id = s.curso_id
JOIN aluno a ON a.id = r.aluno_id
HAVING AVG(n.nota) < 5
GROUP BY a.nome, c.nome;

ERROR 1064 (42000): You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'GROUP BY a.nome, c.nome' at line 8
```

Além de utilizarmos o `HAVING` existe um pequeno detalhe, precisamos **sempre** agrupar antes as colunas pelo `GROUP BY` para depois utilizarmos o `HAVING`:

```
SELECT a.nome, c.nome, AVG(n.nota) FROM nota n
JOIN resposta r ON r.id = n.resposta_id
JOIN exercicio e ON e.id = r.exercicio_id
JOIN secao s ON s.id = e.secao_id
JOIN curso c ON c.id = s.curso_id
JOIN aluno a ON a.id = r.aluno_id
GROUP BY a.nome, c.nome
HAVING AVG(n.nota) < 5;

+---------------+-----------------------------+-------------+
| nome          | nome                        | AVG(n.nota) |
+---------------+-----------------------------+-------------+
| Renata Alonso | C# e orientação a objetos   |    4.857143 |
+---------------+-----------------------------+-------------+
```

Agora conseguimos retornar o aluno que teve a média abaixo de 5. E se quiséssemos pegar todos os alunos que aprovaram? É simples, bastaria alterar o sinal para `>=`:

``` 
SELECT a.nome, c.nome, AVG(n.nota) FROM nota n
JOIN resposta r ON r.id = n.resposta_id
JOIN exercicio e ON e.id = r.exercicio_id
JOIN secao s ON s.id = e.secao_id
JOIN curso c ON c.id = s.curso_id
JOIN aluno a ON a.id = r.aluno_id
GROUP BY a.nome, c.nome
HAVING AVG(n.nota) >= 5;

+-----------------+---------------------------------+-------------+
| nome            | nome                            | AVG(n.nota) |
+-----------------+---------------------------------+-------------+
| Alberto Santos  | Scrum e métodos ágeis           |    5.777778 |
| Frederico José  | Desenvolvimento web com VRaptor |    8.000000 |
| Frederico José  | SQL e banco de dados            |    5.666667 |
| João da Silva   | SQL e banco de dados            |    6.285714 |
+-----------------+---------------------------------+-------------+
```

A instuição enviou mais uma solicitação de um relatório informando quais cursos tem poucos alunos para tomar uma decisão se vai manter os cursos ou se irá cancelá-los. Então vamos novamente fazer a nossa *query* por passos, primeiro vamos começar selecionando os cursos:

```
SELECT c.nome FROM curso c
```

Agora vamos juntar o curso com a matrícula e a matrícula com o aluno e verificar o resultado:

```
SELECT c.nome FROM curso c
JOIN matricula m ON m.curso_id = c.id
JOIN aluno a ON m.aluno_id = a.id;

+------------------------------------+
| nome                               |
+------------------------------------+
| SQL e banco de dados               |
| SQL e banco de dados               |
| Scrum e métodos ágeis              |
| C# e orientação a objetos          |
| SQL e banco de dados               |
| Scrum e métodos ágeis              |
| Desenvolvimento web com VRaptor    |
| Desenvolvimento mobile com Android |
| Desenvolvimento mobile com Android |
| SQL e banco de dados               |
| C# e orientação a objetos          |
| C# e orientação a objetos          |
| C# e orientação a objetos          |
| Desenvolvimento web com VRaptor    |
+------------------------------------+
```

Nossa *query* está funcionando, então vamos contar a quantidade de alunos com a função `COUNT()`:

```
SELECT c.nome, COUNT(a.id) FROM curso c
JOIN matricula m ON m.curso_id = c.id
JOIN aluno a ON m.aluno_id = a.id;
```

Há um detalhe nessa query, pois queremos contar todos os alunos de cada curso, ou seja, precisamos agrupar os cursos!

```
SELECT c.nome, COUNT(a.id) FROM curso c
JOIN matricula m ON m.curso_id = c.id
JOIN aluno a ON m.aluno_id = a.id
GROUP BY c.nome;

+------------------------------------+-------------+
| nome                               | COUNT(a.id) |
+------------------------------------+-------------+
| C# e orientação a objetos          |           4 |
| Desenvolvimento mobile com Android |           2 |
| Desenvolvimento web com VRaptor    |           2 |
| Scrum e métodos ágeis              |           2 |
| SQL e banco de dados               |           4 |
+------------------------------------+-------------+
```

A *query* funcionou, porém precisamos saber apenas os cursos que tem poucos alunos, nesse caso, cursos que tenham menos de 10 alunos:

```
SELECT c.nome, COUNT(a.id) FROM curso c
JOIN matricula m ON m.curso_id = c.id
JOIN aluno a ON m.aluno_id = a.id
GROUP BY c.nome
HAVING COUNT(a.id) < 10;

+------------------------------------+-------------+
| nome                               | COUNT(a.id) |
+------------------------------------+-------------+
| C# e orientação a objetos          |           4 |
| Desenvolvimento mobile com Android |           2 |
| Desenvolvimento web com VRaptor    |           2 |
| Scrum e métodos ágeis              |           2 |
| SQL e banco de dados               |           4 |
+------------------------------------+-------------+
```

Agora podemos enviar o relatório para a instituição.

## Resumindo

Sabemos que para adicionarmos filtros apenas para colunas utilizamos a instrução `WHERE` e indicamos todas as peculiaridades necessárias, porém quando precisamos adicionar filtros para funções de agregação, como por exemplo o `AVG()`, precisamos utilizar a instrução `HAVING`. Além disso, é sempre bom lembrar que, quando estamos desenvolvendo *queries* grandes, é recomendado que faça passa-a-passo *queries* menores, ou seja, resolva os menores problemas juntando cada tabela por vez e teste para verificar se está funcionando, pois isso ajuda a verificar aonde está o problema da *query*. Vamos para os exercícios?

# Exercícios

1. Qual é a principal diferença entre as instruções having e where do sql?

2. Devolva todos os alunos, cursos e a média de suas notas. Lembre-se de agrupar por aluno e por curso. Filtre também pela nota: só mostre alunos com nota média menor do que 5.

3. Exiba todos os cursos e a sua quantidade de matrículas. Mas, exiba somente cursos que tenham mais de 1 matrícula.

4. Exiba o nome do curso e a quantidade de seções que existe nele. Mostre só cursos com mais de 3 seções.
