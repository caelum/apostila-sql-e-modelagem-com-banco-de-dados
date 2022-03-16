# Atualizando e excluindo dados

Cadastramos todas as nossas compras no banco de dados, porém, no meu rascunho onde eu coloco todas as minhas compras alguns valores não estão batendo. Vamos selecionar o valor e a observação de todas as compras com valores entre 1000 e 2000:

```
mysql> SELECT valor, observacoes FROM compras 
WHERE valor >= 1000 AND valor <= 2000;
+---------+------------------------+
| valor   | observacoes            |
+---------+------------------------+
| 1576.40 | Material de construcao |
| 1203.00 | Quartos                |
| 1501.00 | Presente da sogra      |
| 1709.00 | Parcela da casa        |
| 1245.20 | Roupas                 |
+---------+------------------------+
5 rows in set (0,00 sec)
```

Fizemos um filtro para um intervalo de valor, ou seja, **entre** 1000 e 2000. Em SQL, quando queremos filtrar um intervalo, podemos utilizar o operador `BETWEEN`:

```
mysql> SELECT valor, observacoes FROM compras 
WHERE valor BETWEEN 1000 AND 2000;
+---------+------------------------+
| valor   | observacoes            |
+---------+------------------------+
| 1576.40 | Material de construcao |
| 1203.00 | Quartos                |
| 1501.00 | Presente da sogra      |
| 1709.00 | Parcela da casa        |
| 1245.20 | Roupas                 |
+---------+------------------------+
5 rows in set (0,00 sec)
```

O resultado é o mesmo que a nossa *query* anterior, porém agora está mais clara e intuitura. 

No meu rascunho informa que a compra foi no ano de 2013, porém não foi feito esse filtro. Então vamos adicionar mais um filtro pegando o intervalo de 01/01/2013 e 31/12/2013:

```
mysql> SELECT valor, observacoes FROM compras 
WHERE valor BETWEEN 1000 AND 2000
AND data BETWEEN '2013-01-01' AND '2013-12-31';
+---------+-------------+
| valor   | observacoes |
+---------+-------------+
| 1203.00 | Quartos     |
+---------+-------------+
1 row in set (0,00 sec)
```

## Utilizando o UPDATE

Encontrei a compra que foi feita com o valor errado, no meu rascunho a compra de Quartos o valor é de 1500. Então agora precisamos **alterar** esse valor na nossa tabela. Para alterar/atualizar valores em SQL utilizamos a instrução `UPDATE`:

```
UPDATE compras
```

Agora precisamos adicionar o que queremos alterar por meio da instrução `SET`:

```
UPDATE compras SET valor = 1500;
```

Precisamos sempre **tomar cuidado** com a instrução `UPDATE`, pois o exemplo acima executa normalmente, porém o que ele vai atualizar? Não informamos em momento algum qual compra precisa ser atualizar, ou seja, ele iria **atualizar TODAS as compras** e não é isso que queremos. Antes de executar o `UPDATE`, precisamos verificar o `id` da compra que queremos alterar:

```
mysql> SELECT id, valor, observacoes FROM compras 
WHERE valor BETWEEN 1000 AND 2000
AND data BETWEEN '2013-01-01' AND '2013-12-31';
+----+---------+-------------+
| id | valor   | observacoes |
+----+---------+-------------+
| 11 | 1203.00 | Quartos     |
+----+---------+-------------+
1 row in set (0,00 sec)
``` 

Agora que sabemos qual é o `id` da compra, basta informá-lo por meio da instrução `WHERE`:

```
mysql> UPDATE compras SET valor = 1500 WHERE id = 11;
Query OK, 1 row affected (0,01 sec)
Rows matched: 1  Changed: 1  Warnings: 0
```

Vamos verificar se o valor foi atualizado:  

```
mysql> SELECT valor, observacoes FROM compras 
WHERE valor BETWEEN 1000 AND 2000
AND data BETWEEN '2013-01-01' AND '2013-12-31';
+---------+-------------+
| valor   | observacoes |
+---------+-------------+
| 1500.00 | Quartos     |
+---------+-------------+
1 row in set (0,00 sec)
```

Analisando melhor essa compra eu sei que se refere aos móveis do quarto, mas uma observação como "Quartos" que dizer o que? Não é clara o suficiente, pode ser que daqui a 6 meses, 1 ano ou mais tempo eu nem saiba mais o que se refere essa observação e vou acabar pensando: "Será que eu comprei móveis ou comprei um quarto todo?". Um tanto confuso... Então vamos alterar também as observações e deixar algo mais claro, como por exemplo: "Reforma de quartos".

```
UPDATE compra SET observacoes = 'Reforma de quartos' WHERE id = 11;
```

Verificando a nova observação:

```
mysql> SELECT valor, observacoes FROM compras 
    -> WHERE valor BETWEEN 1000 AND 2000
    -> AND data BETWEEN '2013-01-01' AND '2013-12-31';
+---------+--------------------+
| valor   | observacoes        |
+---------+--------------------+
| 1500.00 | Reforma de quartos |
+---------+--------------------+
1 row in set (0,00 sec)
```

## Atualizando várias colunas ao mesmo tempo

Assim como usamos o `UPDATE` para atualizar uma única coluna, poderíamos atualizar dois ou mais valores, separando os mesmos com vírgula. Por exemplo, se desejasse atualizar o valor e as observações:

```
mysql> UPDATE compras SET valor = 1500, observacoes = 'Reforma de quartos cara' 
    -> WHERE id = 11;
Query OK, 1 row affected (0,00 sec)
Rows matched: 1  Changed: 1  Warnings: 0
```

E o resultado:

```
mysql> SELECT valor, observacoes FROM compras WHERE id = 11;
+---------+-------------------------+
| valor   | observacoes             |
+---------+-------------------------+
| 1500.00 | Reforma de quartos cara |
+---------+-------------------------+
1 row in set (0,00 sec)
```

Muito cuidado aqui! Diferentemente do `WHERE`, no caso do `SET` não utilize o operador `AND` para atribuir valores, como no exemplo a seguir, que **NÃO** é o que queremos:

```
errado: UPDATE compras SET valor = 1500 AND observacoes = 'Reforma de quartos novos' WHERE id = 8;
certo:  UPDATE compras SET valor = 1500,    observacoes = 'Reforma de quartos novos' WHERE id = 8;
```

## Utilizando uma coluna como referência para outra coluna

Esqueci de adicionar 10% de imposto que paguei na compra de id 11... portanto quero fazer algo como:

```
mysql> SELECT valor FROM compras WHERE id = 11;
+---------+
| valor   |
+---------+
| 1500.00 |
+---------+
1 row in set (0,00 sec)
```

Descobri o valor atual, agora multiplico por `1.1` para aumentar `10%`: `1500 * 1.1` são `1650` reais. Então atualizo agora manualmente:

```
UPDATE compras SET valor = 1650 AND observacoes = 'Reforma de quartos novos' WHERE id = 11;
```

Mas e se eu quisesse fazer o mesmo para diversas linhas?

```
mysql> SELECT valor FROM compras WHERE id > 11 AND id <= 14;
+--------+
| valor  |
+--------+
| 402.90 |
|  54.98 |
|  12.34 |
+--------+
3 rows in set (0,00 sec)

UPDATE .... e agora???
```

E agora? Vou ter que calcular na mão cada um dos casos, com 10% a mais, e fazer uma linha de `UPDATE` para cada um? A solução é fazer um único `UPDATE` em que digo que quero alterar o valor para o valor dele mesmo, vezes os meus `1.1` que já queria antes:

```
UPDATE compras SET valor = valor * 1.1 
WHERE id >= 11 AND id <= 14;
```

Nessa query fica claro que eu posso usar o valor de um campo (qualquer) para atualizar um campo da mesma linha. No nosso caso usamos o valor original para calcular o novo valor. Mas estou livre para usar outros campos da mesma linha, desde que faça sentido para o meu problema, claro. Por exemplo, se eu tenho uma tabela de `produtos` com os campos `precoLiquido` eu posso atualizar o `precoBruto` quando o imposto mudar para 15%:

```
UPDATE produtos SET precoBruto = precoLiquido * 1.15;
```

## Utilizando o DELETE

Observei o meu rascunho e percebi que essa compra eu não devia ter sido cadastrada na minha tabela de compras, ou seja, eu preciso excluir esse registro do meu banco de dados. Em SQL, quando queremos excluir algum registro, utilizamos a instrução `DELETE`:

```
DELETE FROM compras;
```

O `DELETE` tem o comportamento similar ao `UPDATE`, ou seja, **precisamos sempre tomar cuidado** quando queremos excluir algum registro da tabela. Da mesma forma que fizemos com o `UPDATE`, precisamos adicionar a instrução `WHERE` para informa o que queremos excluir, justamente para **evitamos a exclusão de todos os dados**:

``` 
mysql> DELETE FROM compras WHERE id = 11;
Query OK, 1 row affected (0,01 sec)
```

Se verificarmos novamente se existe o registro dessa compra:

```
mysql> SELECT valor, observacoes FROM compras 
WHERE valor BETWEEN 1000 AND 2000
AND data BETWEEN '2013-01-01' AND '2013-12-31';
Empty set (0,00 sec)
```

A compra foi excluída conforme o esperado!

## Cuidados com o DELETE e UPDATE

Vimos como o `DELETE` e `UPDATE` podem ser **perigosos** em banco de dados, se não definirmos uma condição para cada uma dessas instruções podemos excluir/alterar **TODAS** as informações da nossa tabela. A boa prática para executar essas instruções é **sempre** escrever a instrução `WHERE` antes, ou seja, definir primeiro qual será a condição para executar essas instruções:

```
WHERE id = 11;
```

Então adicionamos o `DELETE`/`UPDATE`:

``` 
DELETE FROM compras WHERE id = 11;

UPDATE compras SET valor = 1500 WHERE id = 11;
```

Dessa forma garantimos que o nosso banco de dados não tenha risco.

## Resumindo

Nesse capítulo aprendemos como fazer *queries* por meio de intervalos utilizando o `BETWEEN` e como alterar/excluir os dados da nossa tabela utilizando o `DELETE` e o `UPDATE`. Vamos para os exercícios?

# Exercícios

1. Altere as compras, colocando a observação 'preparando o natal' para todas as que foram efetuadas no dia 20/12/2014.

2. Altere o VALOR das compras feitas antes de 01/06/2013. Some R$10,00 ao valor atual.

3. Atualize todas as compras feitas entre 01/07/2013 e 01/07/2014 para que elas tenham a observação 'entregue antes de 2014' e a coluna recebida com o valor TRUE.

4. Em um comando WHERE é possível especificar um intervalo de valores. Para tanto, é preciso dizer qual o valor mínimo e o valor máximo que define o intervalo. Qual é o operador que é usado para isso?

5. Qual operador você usa para remover linhas de compras de sua tabela?

6. Exclua as compras realizadas entre 05 e 20 março de 2013.

7. Existe um operador lógico chamado `NOT`. Esse operador pode ser usado para negar qualquer condição. Por exemplo, para selecionar qualquer registro com data diferente de 03/11/2014, pode ser construído o seguinte `WHERE`:

```
WHERE NOT DATA = '2011-11-03'
```

Use o operador `NOT` e monte um `SELECT` que retorna todas as compras com valor diferente de R$ 108,00.
