# Agrupando dados e fazendo consultas mais inteligentes

Já adicionamos muitos dados em nosso banco de dados e seria mais interessante fazermos *queries* mais robustas, como por exemplo, saber o total que já gastei. O MySQL fornece a função `SUM()` que soma todos dos valores de uma coluna:

```
mysql> SELECT SUM(valor) FROM compras;
+------------+
| SUM(valor) |
+------------+
|   43967.91 |
+------------+
1 row in set (0,00 sec)
```

Vamos verificar o total de todas as compras recebidas:

``` 
mysql> SELECT SUM(valor) FROM compras WHERE recebida = 1;
+------------+
| SUM(valor) |
+------------+
|   31686.75 |
+------------+
1 row in set (0,00 sec)
```

Agora todas as compras que não foram recebidas:
 
``` 
mysql> SELECT SUM(valor) FROM compras WHERE recebida = 0;
+------------+
| SUM(valor) |
+------------+
|   12281.16 |
+------------+
1 row in set (0,00 sec)
```

Podemos também, contar quantas compras foram recebidas por meio da função `COUNT()`:

```
SELECT COUNT(*) FROM compras WHERE recebida = 1;
+----------+
| COUNT(*) |
+----------+
|       26 |
+----------+
```

Agora vamos fazer com que seja retornado a soma de todas as compras recebidas e não recebidas, porém retornaremos a coluna recebida, ou seja, em uma linha estará as compras recebidas e a sua soma e em outra as não recebidas e sua soma:

```
mysql> SELECT recebida, SUM(valor) FROM compras;
+----------+------------+
| recebida | SUM(valor) |
+----------+------------+
|        1 |   43967.91 |
+----------+------------+
1 row in set (0,00 sec)
```

Observe que o resultado não saiu conforme o esperado... Mas por que será que isso aconteceu? Quando utilizamos a função `SUM()` do MySQL ela soma todos os valores da coluna e retorna apenas uma única linha, pois é uma **função de agregração**! Para resolvermos esse problema, podemos utilizar a instrução `GROUP BY` que indica como a soma precisa ser agrupada, ou seja, some todas as compras recebidas e agrupe em uma linha, some todas as compras não recebidas e agrupe em outra linha:

```
mysql> SELECT recebida, SUM(valor) FROM compras GROUP BY recebida;
+----------+------------+
| recebida | SUM(valor) |
+----------+------------+
|        0 |   12281.16 |
|        1 |   31686.75 |
+----------+------------+
2 rows in set (0,00 sec)
```

O resultado foi conforme o esperado, porém note que o nome da coluna para a soma está um pouco estranho, pois estamos aplicando uma função ao invés de retornar uma coluna, seria melhor se o nome retornado fosse apenas "soma". Podemos nomear as colunas por meio da instrução `AS`:

``` 
mysql> SELECT recebida, SUM(valor) AS soma FROM compras 
GROUP BY recebida;
+----------+----------+
| recebida | soma     |
+----------+----------+
|        0 | 12281.16 |
|        1 | 31686.75 |
+----------+----------+
2 rows in set (0,00 sec)
```

Também podemos aplicar filtros em *queries* que utilizam funções de agregação:

```
mysql> SELECT recebida, SUM(valor) AS soma FROM compras 
WHERE valor < 1000
GROUP BY recebida;
+----------+---------+
| recebida | soma    |
+----------+---------+
|        0 | 4325.96 |
|        1 | 8682.83 |
+----------+---------+
2 rows in set (0,00 sec)
```

Suponhamos uma *query* mais robusta, onde podemos verificar em qual mês e ano a compra foi entregue ou não e o valor da soma. Podemos retornar a informação de ano utilizando a função `YEAR()` e a informação de mês utilizando a função `MONTH()`:

```
mysql> SELECT MONTH(data) as mes, YEAR(data) as ano, recebida, 
SUM(valor) AS soma FROM compras 
GROUP BY recebida;
+------+------+----------+----------+
| mes  | ano  | recebida | soma     |
+------+------+----------+----------+
|    1 | 2016 |        0 | 12281.16 |
|    1 | 2016 |        1 | 31686.75 |
+------+------+----------+----------+
2 rows in set (0,00 sec)
```

Lembre-se que estamos lidando com uma função de agregação! Por isso precisamos informar todas as colunas que queremos **agrupar**, ou seja, a coluna de mês e de ano:

```
mysql> SELECT MONTH(data) as mes, YEAR(data) as ano, recebida, 
SUM(valor) AS soma FROM compras 
GROUP BY recebida, mes, ano;
+------+------+----------+----------+
| mes  | ano  | recebida | soma     |
+------+------+----------+----------+
|    1 | 2013 |        0 |    12.39 |
|    1 | 2016 |        0 |  2015.49 |
|    4 | 2013 |        0 |    54.98 |
|    4 | 2014 |        0 |   434.00 |
|    5 | 2012 |        0 |  3500.00 |
|    5 | 2013 |        0 |    12.34 |
|    5 | 2015 |        0 |    87.43 |
|    6 | 2014 |        0 |  1616.90 |
|    7 | 2015 |        0 |    12.34 |
|    8 | 2014 |        0 |  1709.00 |
|    9 | 2014 |        0 |   567.09 |
|    9 | 2015 |        0 |   213.50 |
|   10 | 2014 |        0 |    98.00 |
|   10 | 2015 |        0 |  1245.20 |
|   12 | 2013 |        0 |    78.65 |
|   12 | 2014 |        0 |   623.85 |
|    1 | 2013 |        1 |  8046.90 |
|    1 | 2014 |        1 |   827.50 |
|    1 | 2016 |        1 |    35.00 |
|    2 | 2012 |        1 |   200.00 |
|    2 | 2014 |        1 |   921.11 |
|    2 | 2015 |        1 |   986.36 |
|    3 | 2013 |        1 |   795.05 |
|    4 | 2012 |        1 |  1576.40 |
|    4 | 2014 |        1 | 11705.30 |
|    5 | 2014 |        1 |   678.43 |
|    7 | 2013 |        1 |    98.12 |
|    7 | 2015 |        1 |    32.09 |
|    9 | 2015 |        1 |   576.12 |
|   10 | 2014 |        1 |   631.53 |
|   11 | 2013 |        1 |  3212.40 |
|   11 | 2015 |        1 |   954.12 |
|   12 | 2012 |        1 |   163.45 |
|   12 | 2013 |        1 |   223.09 |
|   12 | 2015 |        1 |    23.78 |
+------+------+----------+----------+
35 rows in set (0,00 sec)
```


## Ordenando os resultados

Conseguimos criar uma *query* bem robusta, mas perceba que as informações estão bem desordenadas, a primeira vista é difícil de ver a soma do mês de janeiro em todos os anos, como também a soma de todos os meses em um único ano. Para podermos ordernar as informações das colunas, podemos utilizar a instrução `ORDER BY` passando as colunas que queremos que sejam ordenadas. Vamos ordenar por mês primeiro:

```
mysql> SELECT MONTH(data) as mes, YEAR(data) as ano, recebida, 
SUM(valor) AS soma FROM compras 
GROUP BY recebida, mes, ano
ORDER BY mes;
+------+------+----------+----------+
| mes  | ano  | recebida | soma     |
+------+------+----------+----------+
|    1 | 2013 |        1 |  8046.90 |
|    1 | 2014 |        1 |   827.50 |
|    1 | 2016 |        1 |    35.00 |
|    1 | 2016 |        0 |  2015.49 |
|    1 | 2013 |        0 |    12.39 |
|    2 | 2014 |        1 |   921.11 |
|    2 | 2012 |        1 |   200.00 |
|    2 | 2015 |        1 |   986.36 |
|    3 | 2013 |        1 |   795.05 |
|    4 | 2014 |        0 |   434.00 |
|    4 | 2013 |        0 |    54.98 |
|    4 | 2014 |        1 | 11705.30 |
|    4 | 2012 |        1 |  1576.40 |
|    5 | 2013 |        0 |    12.34 |
|    5 | 2014 |        1 |   678.43 |
|    5 | 2015 |        0 |    87.43 |
|    5 | 2012 |        0 |  3500.00 |
|    6 | 2014 |        0 |  1616.90 |
|    7 | 2015 |        0 |    12.34 |
|    7 | 2015 |        1 |    32.09 |
|    7 | 2013 |        1 |    98.12 |
|    8 | 2014 |        0 |  1709.00 |
|    9 | 2014 |        0 |   567.09 |
|    9 | 2015 |        0 |   213.50 |
|    9 | 2015 |        1 |   576.12 |
|   10 | 2014 |        1 |   631.53 |
|   10 | 2015 |        0 |  1245.20 |
|   10 | 2014 |        0 |    98.00 |
|   11 | 2013 |        1 |  3212.40 |
|   11 | 2015 |        1 |   954.12 |
|   12 | 2012 |        1 |   163.45 |
|   12 | 2013 |        1 |   223.09 |
|   12 | 2015 |        1 |    23.78 |
|   12 | 2014 |        0 |   623.85 |
|   12 | 2013 |        0 |    78.65 |
+------+------+----------+----------+
35 rows in set (0,01 sec)
```

Já está melhor! Mas agora vamos ordernar por mês e por ano:

``` 
mysql> SELECT MONTH(data) as mes, YEAR(data) as ano, recebida, 
SUM(valor) AS soma FROM compras 
GROUP BY recebida, mes, ano
ORDER BY mes, ano;
+------+------+----------+----------+
| mes  | ano  | recebida | soma     |
+------+------+----------+----------+
|    1 | 2013 |        1 |  8046.90 |
|    1 | 2013 |        0 |    12.39 |
|    1 | 2014 |        1 |   827.50 |
|    1 | 2016 |        1 |    35.00 |
|    1 | 2016 |        0 |  2015.49 |
|    2 | 2012 |        1 |   200.00 |
|    2 | 2014 |        1 |   921.11 |
|    2 | 2015 |        1 |   986.36 |
|    3 | 2013 |        1 |   795.05 |
|    4 | 2012 |        1 |  1576.40 |
|    4 | 2013 |        0 |    54.98 |
|    4 | 2014 |        0 |   434.00 |
|    4 | 2014 |        1 | 11705.30 |
|    5 | 2012 |        0 |  3500.00 |
|    5 | 2013 |        0 |    12.34 |
|    5 | 2014 |        1 |   678.43 |
|    5 | 2015 |        0 |    87.43 |
|    6 | 2014 |        0 |  1616.90 |
|    7 | 2013 |        1 |    98.12 |
|    7 | 2015 |        0 |    12.34 |
|    7 | 2015 |        1 |    32.09 |
|    8 | 2014 |        0 |  1709.00 |
|    9 | 2014 |        0 |   567.09 |
|    9 | 2015 |        0 |   213.50 |
|    9 | 2015 |        1 |   576.12 |
|   10 | 2014 |        1 |   631.53 |
|   10 | 2014 |        0 |    98.00 |
|   10 | 2015 |        0 |  1245.20 |
|   11 | 2013 |        1 |  3212.40 |
|   11 | 2015 |        1 |   954.12 |
|   12 | 2012 |        1 |   163.45 |
|   12 | 2013 |        1 |   223.09 |
|   12 | 2013 |        0 |    78.65 |
|   12 | 2014 |        0 |   623.85 |
|   12 | 2015 |        1 |    23.78 |
+------+------+----------+----------+
35 rows in set (0,00 sec)
```

Ficou mais fácil de visualizar, só que ainda não conseguimos verificar de uma forma clara a soma de cada mês durante um ano. Perceba que a instrução `ORDER BY` prioriza as colunas pela ordem em que são informadadas, ou seja, quando fizemos:

``` 
ORDER BY mes, ano;
```

Informamos que queremos que dê prioridade à ordenação da coluna mês e as demais colunas sejam ordenadas de acordo com o mês! Isso significa que para ordenarmos o ano e depois o mês basta apenas colocar o ano no início do `ORDER BY`:

```
mysql> SELECT MONTH(data) as mes, YEAR(data) as ano, recebida, 
SUM(valor) AS soma FROM compras 
GROUP BY recebida, mes, ano
ORDER BY ano, mes;
+------+------+----------+----------+
| mes  | ano  | recebida | soma     |
+------+------+----------+----------+
|    2 | 2012 |        1 |   200.00 |
|    4 | 2012 |        1 |  1576.40 |
|    5 | 2012 |        0 |  3500.00 |
|   12 | 2012 |        1 |   163.45 |
|    1 | 2013 |        1 |  8046.90 |
|    1 | 2013 |        0 |    12.39 |
|    3 | 2013 |        1 |   795.05 |
|    4 | 2013 |        0 |    54.98 |
|    5 | 2013 |        0 |    12.34 |
|    7 | 2013 |        1 |    98.12 |
|   11 | 2013 |        1 |  3212.40 |
|   12 | 2013 |        1 |   223.09 |
|   12 | 2013 |        0 |    78.65 |
|    1 | 2014 |        1 |   827.50 |
|    2 | 2014 |        1 |   921.11 |
|    4 | 2014 |        0 |   434.00 |
|    4 | 2014 |        1 | 11705.30 |
|    5 | 2014 |        1 |   678.43 |
|    6 | 2014 |        0 |  1616.90 |
|    8 | 2014 |        0 |  1709.00 |
|    9 | 2014 |        0 |   567.09 |
|   10 | 2014 |        1 |   631.53 |
|   10 | 2014 |        0 |    98.00 |
|   12 | 2014 |        0 |   623.85 |
|    2 | 2015 |        1 |   986.36 |
|    5 | 2015 |        0 |    87.43 |
|    7 | 2015 |        0 |    12.34 |
|    7 | 2015 |        1 |    32.09 |
|    9 | 2015 |        0 |   213.50 |
|    9 | 2015 |        1 |   576.12 |
|   10 | 2015 |        0 |  1245.20 |
|   11 | 2015 |        1 |   954.12 |
|   12 | 2015 |        1 |    23.78 |
|    1 | 2016 |        1 |    35.00 |
|    1 | 2016 |        0 |  2015.49 |
+------+------+----------+----------+
35 rows in set (0,00 sec)
``` 

Agora conseguimos verificar de uma forma clara a soma dos meses em cada ano. Além da soma podemos também utilizar outras funções de agragação como por exemplo, a `AVG()` que retorna a média de uma coluna:

```
mysql> SELECT MONTH(data) as mes, YEAR(data) as ano, recebida, 
AVG(valor) AS soma FROM compras 
GROUP BY recebida, mes, ano
ORDER BY ano, mes;
+------+------+----------+-------------+
| mes  | ano  | recebida | soma        |
+------+------+----------+-------------+
|    2 | 2012 |        1 |  200.000000 |
|    4 | 2012 |        1 | 1576.400000 |
|    5 | 2012 |        0 | 3500.000000 |
|   12 | 2012 |        1 |  163.450000 |
|    1 | 2013 |        0 |   12.390000 |
|    1 | 2013 |        1 | 2682.300000 |
|    3 | 2013 |        1 |  397.525000 |
|    4 | 2013 |        0 |   54.980000 |
|    5 | 2013 |        0 |   12.340000 |
|    7 | 2013 |        1 |   98.120000 |
|   11 | 2013 |        1 | 3212.400000 |
|   12 | 2013 |        1 |  223.090000 |
|   12 | 2013 |        0 |   78.650000 |
|    1 | 2014 |        1 |  827.500000 |
|    2 | 2014 |        1 |  460.555000 |
|    4 | 2014 |        0 |  434.000000 |
|    4 | 2014 |        1 | 5852.650000 |
|    5 | 2014 |        1 |  678.430000 |
|    6 | 2014 |        0 |  808.450000 |
|    8 | 2014 |        0 | 1709.000000 |
|    9 | 2014 |        0 |  567.090000 |
|   10 | 2014 |        1 |  631.530000 |
|   10 | 2014 |        0 |   98.000000 |
|   12 | 2014 |        0 |  311.925000 |
|    2 | 2015 |        1 |  493.180000 |
|    5 | 2015 |        0 |   87.430000 |
|    7 | 2015 |        1 |   32.090000 |
|    7 | 2015 |        0 |   12.340000 |
|    9 | 2015 |        1 |  576.120000 |
|    9 | 2015 |        0 |  213.500000 |
|   10 | 2015 |        0 | 1245.200000 |
|   11 | 2015 |        1 |  954.120000 |
|   12 | 2015 |        1 |   23.780000 |
|    1 | 2016 |        1 |   17.500000 |
|    1 | 2016 |        0 |  671.830000 |
+------+------+----------+-------------+
35 rows in set (0,00 sec)
```

## Resumindo

Vimos como podemos fazer *queries* mais robustas e inteligentes utilizando funções de agregação, como por exemplo, a `SUM()` para somar e a `AVG()` para tirar a média. Vimos também que quando queremos retornar outras colunas ao utilizar funções de agregação, precisamos utilizar a instrução `GROUP BY` para determinar quais serão as colunas que queremos que seja feita o agrupamento e que nem sempre o resultado vem organizado e por isso, em determinados casos, precisamos utilizar a instrução `ORDER BY` para ordenar a nossa *query* por meio de uma coluna.

# Exercícios

1. Calcule a média de todas as compras com datas inferiores a 12/05/2013. 

2. Calcule a quantidade de compras com datas inferiores a 12/05/2013 e que já foram recebidas.

3. Calcule a soma de todas as compras, agrupadas se a compra recebida ou não.
