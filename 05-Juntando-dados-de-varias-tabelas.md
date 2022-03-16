# Juntando dados de várias tabelas

A nossa tabela de compras está bem populada, com muitas informações das compras realizadas, porém está faltando uma informação muito importante, que são os compradores. Por vezes foi eu quem comprou algo, mas também o meu irmão pode ter comprado ou até mesmo o meu primo... Como podemos fazer para identificar o comprador de uma compra? De acordo com o que vimos até agora podemos adicionar uma nova coluna chamada `comprador` com o tipo `varchar(200)` :

```
mysql> ALTER TABLE compras ADD COLUMN comprador VARCHAR(200);
Query OK, 0 rows affected (0,04 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

Vamos verificar como ficou a estrutura da nossa tabela:

```
mysql> DESC compras;
+-------------+---------------+------+-----+---------+----------------+
| Field       | Type          | Null | Key | Default | Extra          |
+-------------+---------------+------+-----+---------+----------------+
| id          | int(11)       | NO   | PRI | NULL    | auto_increment |
| valor       | decimal(18,2) | YES  |     | NULL    |                |
| data        | date          | YES  |     | NULL    |                |
| observacoes | varchar(255)  | NO   |     | NULL    |                |
| recebida    | tinyint(1)    | YES  |     | 0       |                |
| comprador   | varchar(200)  | YES  |     | NULL    |                |
+-------------+---------------+------+-----+---------+----------------+
6 rows in set (0,00 sec)
```

Ótimo! Agora podemos adicionar os compradores de cada compra! Mas, antes de adicionarmos precisamos verificar novamente verificar as informações das compras e identificar quem foi que comprou:

```
mysql> SELECT id, valor, observacoes, data FROM compras;
+----+----------+------------------------------+------------+
| id | valor    | observacoes                  | data       |
+----+----------+------------------------------+------------+
|  1 |    20.00 | Lanchonete                   | 2016-01-05 |
|  2 |    15.00 | Lanchonete                   | 2016-01-06 |
|  3 |   915.50 | Guarda-roupa                 | 2016-01-06 |
|  4 |   949.99 | Smartphone                   | 2016-01-10 |
|  5 |   200.00 | Material escolar             | 2012-02-19 |
|  6 |  3500.00 | Televisao                    | 2012-05-21 |
|  7 |  1576.40 | Material de construcao       | 2012-04-30 |
|  8 |   163.45 | Pizza pra familia            | 2012-12-15 |
|  9 |  4780.00 | Sala de estar                | 2013-01-23 |
| 10 |   392.15 | Quartos                      | 2013-03-03 |
| 12 |   402.90 | Copa                         | 2013-03-21 |
| 13 |    54.98 | Lanchonete                   | 2013-04-12 |
| 14 |    12.34 | Lanchonete                   | 2013-05-23 |
| 15 |    78.65 | Lanchonete                   | 2013-12-04 |
| 16 |    12.39 | Sorvete no parque            | 2013-01-06 |
| 17 |    98.12 | Hopi Hari                    | 2013-07-09 |
| 18 |  2498.00 | Compras de janeiro           | 2013-01-12 |
| 19 |  3212.40 | Compras do mes               | 2013-11-13 |
| 20 |   223.09 | Compras de natal             | 2013-12-17 |
| 21 |   768.90 | Festa                        | 2013-01-16 |
| 22 |   827.50 | Festa                        | 2014-01-09 |
| 23 |    12.00 | Salgado no aeroporto         | 2014-02-19 |
| 24 |   678.43 | Passagem pra Bahia           | 2014-05-21 |
| 25 | 10937.12 | Carnaval em Cancun           | 2014-04-30 |
| 26 |  1501.00 | Presente da sogra            | 2014-06-22 |
| 27 |  1709.00 | Parcela da casa              | 2014-08-25 |
| 28 |   567.09 | Parcela do carro             | 2014-09-25 |
| 29 |   631.53 | IPTU                         | 2014-10-12 |
| 30 |   909.11 | IPVA                         | 2014-02-11 |
| 31 |   768.18 | Gasolina viagem Porto Alegre | 2014-04-10 |
| 32 |   434.00 | Rodeio interior de Sao Paulo | 2014-04-01 |
| 33 |   115.90 | Dia dos namorados            | 2014-06-12 |
| 34 |    98.00 | Dia das crianças             | 2014-10-12 |
| 35 |   253.70 | Natal - presentes            | 2014-12-20 |
| 36 |   370.15 | Compras de natal             | 2014-12-25 |
| 37 |    32.09 | Lanchonete                   | 2015-07-02 |
| 38 |   954.12 | Show da Ivete Sangalo        | 2015-11-03 |
| 39 |    98.70 | Lanchonete                   | 2015-02-07 |
| 40 |   213.50 | Roupas                       | 2015-09-25 |
| 41 |  1245.20 | Roupas                       | 2015-10-17 |
| 42 |    23.78 | Lanchonete do Zé             | 2015-12-18 |
| 43 |   576.12 | Sapatos                      | 2015-09-13 |
| 44 |    12.34 | Canetas                      | 2015-07-19 |
| 45 |    87.43 | Gravata                      | 2015-05-10 |
| 46 |   887.66 | Presente para o filhao       | 2015-02-02 |
| 48 |   150.00 | Compra de teste              | 2016-01-05 |
+----+----------+------------------------------+------------+
46 rows in set (0,00 sec)
```

Agora que já sei as informações das compras posso adicionar os seus compradores. Começaremos pelas 5 primeiras compras:

```
|  1 |    20.00 | Lanchonete                   | 2016-01-05 |
|  2 |    15.00 | Lanchonete                   | 2016-01-06 |
|  3 |   915.50 | Guarda-roupa                 | 2016-01-06 |
|  4 |   949.99 | Smartphone                   | 2016-01-10 |
|  5 |   200.00 | Material escolar             | 2012-02-19 |
```

De acordo com as informações do meu rascunho, a primeira compra fui eu mesmo que comprei: 

```
mysql> UPDATE compras SET comprador = 'Alex Felipe' WHERE id = 1;
Query OK, 1 row affected (0,00 sec)
Rows matched: 1  Changed: 1  Warnings: 0
```

A segunda o meu primo e xará Alex Vieira que comprou:

```
mysql> UPDATE compras SET comprador = 'Alex Vieira' WHERE id = 2;
Query OK, 1 row affected (0,00 sec)
Rows matched: 1  Changed: 1  Warnings: 0
```

O guarda-roupa foi o meu tio João da Silva que comprou:

```
mysql> UPDATE compras SET comprador = 'João da Silva' WHERE id = 3;
Query OK, 1 row affected (0,00 sec)
Rows matched: 1  Changed: 1  Warnings: 0
```

O smartphone fui eu:

```
mysql> UPDATE compras SET comprador = 'Alex Felipe' WHERE id = 4;
Query OK, 1 row affected (0,01 sec)
Rows matched: 1  Changed: 1  Warnings: 0
```

E o material escolar foi o meu tio João da Silva:

```
mysql> UPDATE compras SET comprador = 'João da Silva' WHERE id = 5;
Query OK, 1 row affected (0,01 sec)
Rows matched: 1  Changed: 1  Warnings: 0
```

Vejamos o resultado:

```
mysql> SELECT * FROM compras;
+----+----------+------------+------------------------------+----------+----------------+
| id | valor    | data       | observacoes                  | recebida | comprador      |
+----+----------+------------+------------------------------+----------+----------------+
|  1 |    20.00 | 2016-01-05 | Lanchonete                   |        1 | Alex Felipe    |
|  2 |    15.00 | 2016-01-06 | Lanchonete                   |        1 | Alex Vieira    |
|  3 |   915.50 | 2016-01-06 | Guarda-roupa                 |        0 | João da Silva  |
|  4 |   949.99 | 2016-01-10 | Smartphone                   |        0 | Alex Felipe    |
|  5 |   200.00 | 2012-02-19 | Material escolar             |        1 | João da Silva  |
|... |      ... |        ... | ...                          |      ... | ...            |
+----+----------+------------+------------------------------+----------+----------------+
46 rows in set (0,01 sec)
```

Pensando bem, não foi o meu tio João da Silva que comprou Material escolar e sim o meu tio João Vieira, eu devo ter anotado errado... Então vamos alterar:

```
mysql> UPDATE compras SET comprador = 'João vieira' WHERE comprador = 'João da Silva';
Query OK, 2 rows affected (0,01 sec)
Rows matched: 2  Changed: 2  Warnings: 0
```

Vejamos como ficou o resultado: 

```
mysql> select * from compras limit 5;
+----+--------+------------+------------------+----------+--------------+
| id | valor  | data       | observacoes      | recebida | comprador    |
+----+--------+------------+------------------+----------+--------------+
|  1 |  20.00 | 2016-01-05 | Lanchonete       |        1 | Alex Felipe  |
|  2 |  15.00 | 2016-01-06 | Lanchonete       |        1 | Alex Vieira  |
|  3 | 915.50 | 2016-01-06 | Guarda-roupa     |        0 | João vieira  |
|  4 | 949.99 | 2016-01-10 | Smartphone       |        0 | Alex Felipe  |
|  5 | 200.00 | 2012-02-19 | Material escolar |        1 | João vieira  |
+----+--------+------------+------------------+----------+--------------+
5 rows in set (0,00 sec)
```

Opa! Espera aí! O meu tio João da Silva sumiu!? E o meu tio João Vieira foi inserido com o sobrenome minúsculo... Vamos alterar novamente... Primeiro o nome do João Vieira:

```
mysql> UPDATE compras SET comprador = 'João Vieira' WHERE comprador = 'João vieira';
Query OK, 2 rows affected (0,01 sec)
Rows matched: 2  Changed: 2  Warnings: 0
```

Agora vamos reenserir o meu tio João da Silva para a compra do guarda-roupa:

```
mysql>  UPDATE compras SET comprador = 'João da Silva' WHERE id = 3;
Query OK, 1 row affected (0,01 sec)
Rows matched: 1  Changed: 1  Warnings: 0
```

Vamos verificar novamente a nossa tabela:

```
mysql> SELECT * FROM compras LIMIT 5;
+----+--------+------------+------------------+----------+----------------+
| id | valor  | data       | observacoes      | recebida | comprador      |
+----+--------+------------+------------------+----------+----------------+
|  1 |  20.00 | 2016-01-05 | Lanchonete       |        1 | Alex Felipe    |
|  2 |  15.00 | 2016-01-06 | Lanchonete       |        1 | Alex Vieira    |
|  3 | 915.50 | 2016-01-06 | Guarda-roupa     |        0 | João da Silva  |
|  4 | 949.99 | 2016-01-10 | Smartphone       |        0 | Alex Felipe    |
|  5 | 200.00 | 2012-02-19 | Material escolar |        1 | João Vieira    |
|... | ...    |        ... | ...              |      ... | ...            |
+----+--------+------------+------------------+----------+----------------+
5 rows in set (0,00 sec)
```

Nossa, que trabalheira! Por causa de um errinho eu tive que **alterar tudo novamente**! Se eu não ficar atento, com certeza acontecerá uma caca gigante no meu banco. Além disso, eu preciso também adicionar o telefone do comprador para um dia, se for necessário, entrar em contato! Então, novamente, iremos adicionar um coluna nova com o nome `telefone` e tipo `VARCHAR(15)`:

```
mysql> ALTER TABLE compras ADD COLUMN telefone VARCHAR(30);
Query OK, 0 rows affected (0,06 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

Vamos adicionar o telefone de cada um. Primeiro o meu telefone:

```
mysql> UPDATE compras SET telefone = '5571-2751' WHERE comprador = 'Alex Felipe';
Query OK, 2 rows affected (0,01 sec)
Rows matched: 2  Changed: 2  Warnings: 0
```

Agora o do meu xará, Alex Vieira:

```
mysql> UPDATE compras SET telefone = '5083-3884' WHERE comprador = 'Alex Vieira';
Query OK, 1 row affected (0,00 sec)
Rows matched: 1  Changed: 1  Warnings: 0
```

Por fim, os telefones dos meus tios:

```
mysql> UPDATE compras SET telefone = '2220-4156' WHERE comprador = 'João da Silva';
Query OK, 1 row affected (0,00 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> UPDATE compras SET telefone = '2297-0033' WHERE comprador = 'João Vieira';
Query OK, 1 row affected (0,00 sec)
Rows matched: 1  Changed: 1  Warnings: 0
```

Vejamos como ficou a nossa tabela:

```
SELECT * FROM compras;
+----+----------+------------+------------------------------+----------+----------------+-----------+
| id | valor    | data       | observacoes                  | recebida | comprador      | telefone  |
+----+----------+------------+------------------------------+----------+----------------+-----------+
|  1 |    20.00 | 2016-01-05 | Lanchonete                   |        1 | Alex Felipe    | 5571-2751 |
|  2 |    15.00 | 2016-01-06 | Lanchonete                   |        1 | Alex Vieira    | 5083-3884 |
|  3 |   915.50 | 2016-01-06 | Guarda-roupa                 |        0 | João da Silva  | 2220-4156 |
|  4 |   949.99 | 2016-01-10 | Smartphone                   |        0 | Alex Felipe    | 5571-2751 |
|  5 |   200.00 | 2012-02-19 | Material escolar             |        1 | João Vieira    | 2297-0033 |
|... |      ... |        ... | ...                          |      ... | ...            | ...       |
+----+----------+------------+------------------------------+----------+----------------+-----------+
46 rows in set (0,00 sec)
```

Certo, agora nossos compradores possuem telefone também. Ainda faltam mais compras sem comprador, então continuaremos adicionando os compradores. A próxima compra foi o meu primo Alex Vieira que comprou. Vamos inseri-lo novamente:

```
mysql> UPDATE compras SET comprador = 'Alex Vieira' WHERE id = 6;
Query OK, 1 row affected (0,00 sec)
Rows matched: 1  Changed: 1  Warnings: 0
```

Consultando nossa tabela para verificar como está ficando:

```
mysql> SELECT * FROM compras;
+----+----------+------------+------------------------------+----------+----------------+-----------+
| id | valor    | data       | observacoes                  | recebida | comprador      | telefone  |
+----+----------+------------+------------------------------+----------+----------------+-----------+
|  1 |    20.00 | 2016-01-05 | Lanchonete                   |        1 | Alex Felipe    | 5571-2751 |
|  2 |    15.00 | 2016-01-06 | Lanchonete                   |        1 | Alex Vieira    | 5083-3884 |
|  3 |   915.50 | 2016-01-06 | Guarda-roupa                 |        0 | João da Silva  | 2220-4156 |
|  4 |   949.99 | 2016-01-10 | Smartphone                   |        0 | Alex Felipe    | 5571-2751 |
|  5 |   200.00 | 2012-02-19 | Material escolar             |        1 | João Vieira    | 2297-0033 |
|  6 |  3500.00 | 2012-05-21 | Televisao                    |        0 | Alex Vieira    | NULL      |
|... |      ... | ...        | ...                          |      ... | ...            | ...       |
+----+----------+------------+------------------------------+----------+----------------+-----------+
46 rows in set (0,00 sec)
```

Ah não! Esqueci de colocar o telefone. Deixa eu colocar:

```
mysql> UPDATE compras SET telefone = '5571-2751' WHERE id = 6;
Query OK, 1 row affected (0,00 sec)
Rows matched: 1  Changed: 1  Warnings: 0
```

Vamos ver se agora vai dar certo:

```
mysql> SELECT * FROM compras;
+----+----------+------------+------------------------------+----------+----------------+-----------+
| id | valor    | data       | observacoes                  | recebida | comprador      | telefone  |
+----+----------+------------+------------------------------+----------+----------------+-----------+
|  1 |    20.00 | 2016-01-05 | Lanchonete                   |        1 | Alex Felipe    | 5571-2751 |
|  2 |    15.00 | 2016-01-06 | Lanchonete                   |        1 | Alex Vieira    | 5083-3884 |
|  3 |   915.50 | 2016-01-06 | Guarda-roupa                 |        0 | João da Silva  | 2220-4156 |
|  4 |   949.99 | 2016-01-10 | Smartphone                   |        0 | Alex Felipe    | 5571-2751 |
|  5 |   200.00 | 2012-02-19 | Material escolar             |        1 | João Vieira    | 2297-0033 |
|  6 |  3500.00 | 2012-05-21 | Televisao                    |        0 | Alex Vieira    | 5571-2751 |
|... |      ... | ...        | ...                          |      ... | ...            | ...       |
+----+----------+------------+------------------------------+----------+----------------+-----------+
46 rows in set (0,00 sec)
```

Poxa vida... Percebi que ao invés de colocar o telefone do meu primo acabei colocando o meu... Quanto sofrimento por causa de 2 colunas novas! Além de ter que me preocupar se os nomes dos compradores estão sendo exatamente iguais, agora eu preciso me preocupar se os telefones também estão corretos e mais, todas as vezes que eu preciso inserir um comprador eu preciso lembrar de inserir o seu telefone... E se agora eu precisasse adicionar o endereço, e-mail ou quaisquer informações do comprador? Eu teria que **lembrar todas** essas informações **cada vez** que eu **inserir apenas uma compra**! Que horror!

Veja o quão problemático está sendo manter as informações de um comprador em uma tabela de compras. Além de trabalhosa, a inserção é bem problemática, pois há um grande risco de falhas no meu banco de dados como por exemplo: informações redundantes (que se repetem) ou então informações incoerentes (o mesmo comprador com alguma informação diferente). Com toda certeza não é uma boa solução para a nossa necessidade! Será que não existe uma forma diferente de resolver isso? 

## Normalizando nosso modelo

Repara que temos dois elementos, duas entidades, em uma unica tabela: a compra e o comprador. Quando nos deparamos com esses tipos de problemas **criamos novas tabelas**. Então vamos criar uma tabela chamada compradores.

No nosso caso queremos que cada comprador seja representado pelo seu nome, endereço e telefone. Como já pensamos em modelagem antes, aqui fica o nosso modelo de tabela para representar os compradores:

```
mysql> CREATE TABLE compradores (
id INT PRIMARY KEY AUTO_INCREMENT,
nome VARCHAR(200),
endereco VARCHAR(200),
telefone VARCHAR(30)
);
Query OK, 0 rows affected (0,01 sec)
```

Vamos verificar a nossa tabela por meio do `DESC`:

```
mysql> DESC compradores;
+----------+--------------+------+-----+---------+----------------+
| Field    | Type         | Null | Key | Default | Extra          |
+----------+--------------+------+-----+---------+----------------+
| id       | int(11)      | NO   | PRI | NULL    | auto_increment |
| nome     | varchar(200) | YES  |     | NULL    |                |
| endereco | varchar(200) | YES  |     | NULL    |                |
| telefone | varchar(30)  | YES  |     | NULL    |                |
+----------+--------------+------+-----+---------+----------------+
4 rows in set (0,00 sec)
```

Agora que criamos a nossa tabela, vamos inserir alguns compradores, no meu caso, as compras foram feitas apenas por mim e pelo meu tio João da Silva, então adicionaremos apenas 2 compradores:

```
mysql> INSERT INTO compradores (nome, endereco, telefone) VALUES 
('Alex Felipe', 'Rua Vergueiro, 3185', '5571-2751');
Query OK, 1 row affected (0,01 sec)

mysql> INSERT INTO compradores (nome, endereco, telefone) VALUES 
('João da Silva', 'Av. Paulista, 6544', '2220-4156');
Query OK, 1 row affected (0,01 sec)
```

Vamos verificar os nossos compradores:

```
mysql> SELECT * FROM compradores;
+----+----------------+---------------------+-----------+
| id | nome           | endereco            | telefone  |
+----+----------------+---------------------+-----------+
|  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
|  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
+----+----------------+---------------------+-----------+
2 rows in set (0,00 sec)
```

Criamos agora duas tabelas diferentes na nossa base de dados, a tabela `compras` e a tabela `compradores`. Então não precisamos mais das inforções dos compradores na tabela `compras`, ou seja, vamos excluir as colunas `comprador` e `telefone`, mas como podemos excluir uma coluna? Precisamos alterar a estrutura da tabela, então começaremos pelo `ALTER TABLE`:

```
ALTER TABLE compras
```

Se para adicionar uma coluna utilizamos a instrução `ADD COLUMN`, logo, para excluir uma tabela, utilizaremos a instrução `DROP COLUMN`:

```
mysql> ALTER TABLE compras DROP COLUMN comprador;
Query OK, 0 rows affected (0,06 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

Vamos verificar a estrutura da nossa tabela:

```
mysql> DESC compras;
+-------------+---------------+------+-----+---------+----------------+
| Field       | Type          | Null | Key | Default | Extra          |
+-------------+---------------+------+-----+---------+----------------+
| id          | int(11)       | NO   | PRI | NULL    | auto_increment |
| valor       | decimal(18,2) | YES  |     | NULL    |                |
| data        | date          | YES  |     | NULL    |                |
| observacoes | varchar(255)  | NO   |     | NULL    |                |
| recebida    | tinyint(1)    | YES  |     | 0       |                |
| telefone    | varchar(30)   | YES  |     | NULL    |                |
+-------------+---------------+------+-----+---------+----------------+
6 rows in set (0,00 sec)
```

A coluna comprador foi excluída com sucesso! Por fim, excluiremos a coluna telefone:

```
mysql> ALTER TABLE compras DROP COLUMN telefone;
Query OK, 0 rows affected (0,03 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

Agora que excluímos todas as colunas relacionadas aos compradores da tabela `compras`, precisamos de alguma forma associar uma compra com um comprador. Então vamos criar uma nova coluna na tabela compras e adicionar o `id` do comprador nessa coluna: 

```
mysql> ALTER TABLE compras ADD COLUMN id_compradores int;
Query OK, 0 rows affected (0,03 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

Por que o `id` dos compradores precisa ficar na tabela `compras` e não o `id` da tabela `compras` na tabela `compradores`? Precisamos pensar  seguinte forma: uma compra só pode ser de 1 único comprador e o comprador pode ter 1 ou mais compras, como podemos garantir que **1 compra perteça apenas a 1 comprador**? Fazendo com que a compra saiba quem é o seu **único comprador**! Ou seja, recebendo a chave do seu dono! Por isso adicionamos o `id` dos compradores na tabela de `compras`, **se fizéssemos o contrário**, ou seja, adicionássemos o `id` da compra na tabela `compradores`, iríamos permitir que **1 única `compra`** fosse realizada por 1 ou mais compradores, o que não faz sentido algum sabendo que as minhas compras só foram realizadas por mim apenas e as do meu tio por ele apenas.

Temos a nossa nova coluna para associar o comprador com a sua compra, então vamos fazer `UPDATE` na tabela compras inserindo o comprador de `id` 1 para a primeira metade das compras e inserindo a segunda metade para o comprador de `id` 2. Primeiro vamos verificar quantas compras nós temos usando a função `COUNT()`;

```
mysql> SELECT count(*) FROM compras;
+----------+
| count(*) |
+----------+
|       46 |
+----------+
1 row in set (0,00 sec)
```

Vamos atualizar a primeira metade:

```
mysql> UPDATE compras SET id_compradores = 1 WHERE id < 22;
Query OK, 20 rows affected (0,01 sec)
Rows matched: 20  Changed: 20  Warnings: 0
```

Agora a segunda metade:

```
mysql> UPDATE compras SET id_compradores = 2 WHERE id > 21;
Query OK, 26 rows affected (0,01 sec)
Rows matched: 26  Changed: 26  Warnings: 0
```

Verificando as compras no nosso banco de dados:

```
mysql> SELECT * FROM compras;
+----+----------+------------+------------------------------+----------+----------------+
| id | valor    | data       | observacoes                  | recebida | id_compradores |
+----+----------+------------+------------------------------+----------+----------------+
|  1 |    20.00 | 2016-01-05 | Lanchonete                   |        1 |              1 |
|  2 |    15.00 | 2016-01-06 | Lanchonete                   |        1 |              1 |
|  3 |   915.50 | 2016-01-06 | Guarda-roupa                 |        0 |              1 |
|  4 |   949.99 | 2016-01-10 | Smartphone                   |        0 |              1 |
|  5 |   200.00 | 2012-02-19 | Material escolar             |        1 |              1 |
|  6 |  3500.00 | 2012-05-21 | Televisao                    |        0 |              1 |
|  7 |  1576.40 | 2012-04-30 | Material de construcao       |        1 |              1 |
|  8 |   163.45 | 2012-12-15 | Pizza pra familia            |        1 |              1 |
|  9 |  4780.00 | 2013-01-23 | Sala de estar                |        1 |              1 |
| 10 |   392.15 | 2013-03-03 | Quartos                      |        1 |              1 |
| 12 |   402.90 | 2013-03-21 | Copa                         |        1 |              1 |
| 13 |    54.98 | 2013-04-12 | Lanchonete                   |        0 |              1 |
| 14 |    12.34 | 2013-05-23 | Lanchonete                   |        0 |              1 |
| 15 |    78.65 | 2013-12-04 | Lanchonete                   |        0 |              1 |
| 16 |    12.39 | 2013-01-06 | Sorvete no parque            |        0 |              1 |
| 17 |    98.12 | 2013-07-09 | Hopi Hari                    |        1 |              1 |
| 18 |  2498.00 | 2013-01-12 | Compras de janeiro           |        1 |              1 |
| 19 |  3212.40 | 2013-11-13 | Compras do mes               |        1 |              1 |
| 20 |   223.09 | 2013-12-17 | Compras de natal             |        1 |              1 |
| 21 |   768.90 | 2013-01-16 | Festa                        |        1 |              1 |
| 22 |   827.50 | 2014-01-09 | Festa                        |        1 |              2 |
| 23 |    12.00 | 2014-02-19 | Salgado no aeroporto         |        1 |              2 |
| 24 |   678.43 | 2014-05-21 | Passagem pra Bahia           |        1 |              2 |
| 25 | 10937.12 | 2014-04-30 | Carnaval em Cancun           |        1 |              2 |
| 26 |  1501.00 | 2014-06-22 | Presente da sogra            |        0 |              2 |
| 27 |  1709.00 | 2014-08-25 | Parcela da casa              |        0 |              2 |
| 28 |   567.09 | 2014-09-25 | Parcela do carro             |        0 |              2 |
| 29 |   631.53 | 2014-10-12 | IPTU                         |        1 |              2 |
| 30 |   909.11 | 2014-02-11 | IPVA                         |        1 |              2 |
| 31 |   768.18 | 2014-04-10 | Gasolina viagem Porto Alegre |        1 |              2 |
| 32 |   434.00 | 2014-04-01 | Rodeio interior de Sao Paulo |        0 |              2 |
| 33 |   115.90 | 2014-06-12 | Dia dos namorados            |        0 |              2 |
| 34 |    98.00 | 2014-10-12 | Dia das crianças             |        0 |              2 |
| 35 |   253.70 | 2014-12-20 | Natal - presentes            |        0 |              2 |
| 36 |   370.15 | 2014-12-25 | Compras de natal             |        0 |              2 |
| 37 |    32.09 | 2015-07-02 | Lanchonete                   |        1 |              2 |
| 38 |   954.12 | 2015-11-03 | Show da Ivete Sangalo        |        1 |              2 |
| 39 |    98.70 | 2015-02-07 | Lanchonete                   |        1 |              2 |
| 40 |   213.50 | 2015-09-25 | Roupas                       |        0 |              2 |
| 41 |  1245.20 | 2015-10-17 | Roupas                       |        0 |              2 |
| 42 |    23.78 | 2015-12-18 | Lanchonete do Zé             |        1 |              2 |
| 43 |   576.12 | 2015-09-13 | Sapatos                      |        1 |              2 |
| 44 |    12.34 | 2015-07-19 | Canetas                      |        0 |              2 |
| 45 |    87.43 | 2015-05-10 | Gravata                      |        0 |              2 |
| 46 |   887.66 | 2015-02-02 | Presente para o filhao       |        1 |              2 |
| 48 |   150.00 | 2016-01-05 | Compra de teste              |        0 |              2 |
+----+----------+------------+------------------------------+----------+----------------+
46 rows in set (0,00 sec)
```

## One to Many/Many to One

Repare que o que fizemos foi deixar claro que um comprador pode ter muitas compras (um para muitos), ou ainda que muitas compras podem vir do mesmo comprador (many to one), só depende do ponto de vista. Chamamos então de uma relação One to Many (ou Many to One).

## FOREIGN KEY

Conseguimos associar uma compra com um comprador, então agora vamos buscar as informações das compras e seus respectivos compradores:

```
mysql> SELECT * FROM compras;
+----+----------+------------+------------------------------+----------+----------------+
| id | valor    | data       | observacoes                  | recebida | id_compradores |
+----+----------+------------+------------------------------+----------+----------------+
|  1 |    20.00 | 2016-01-05 | Lanchonete                   |        1 |              1 |
|  2 |    15.00 | 2016-01-06 | Lanchonete                   |        1 |              1 |
|  3 |   915.50 | 2016-01-06 | Guarda-roupa                 |        0 |              1 |
|  4 |   949.99 | 2016-01-10 | Smartphone                   |        0 |              1 |
|  5 |   200.00 | 2012-02-19 | Material escolar             |        1 |              1 |
|  6 |  3500.00 | 2012-05-21 | Televisao                    |        0 |              1 |
|  7 |  1576.40 | 2012-04-30 | Material de construcao       |        1 |              1 |
|  8 |   163.45 | 2012-12-15 | Pizza pra familia            |        1 |              1 |
|  9 |  4780.00 | 2013-01-23 | Sala de estar                |        1 |              1 |
| 10 |   392.15 | 2013-03-03 | Quartos                      |        1 |              1 |
| 12 |   402.90 | 2013-03-21 | Copa                         |        1 |              1 |
| 13 |    54.98 | 2013-04-12 | Lanchonete                   |        0 |              1 |
| 14 |    12.34 | 2013-05-23 | Lanchonete                   |        0 |              1 |
| 15 |    78.65 | 2013-12-04 | Lanchonete                   |        0 |              1 |
| 16 |    12.39 | 2013-01-06 | Sorvete no parque            |        0 |              1 |
| 17 |    98.12 | 2013-07-09 | Hopi Hari                    |        1 |              1 |
| 18 |  2498.00 | 2013-01-12 | Compras de janeiro           |        1 |              1 |
| 19 |  3212.40 | 2013-11-13 | Compras do mes               |        1 |              1 |
| 20 |   223.09 | 2013-12-17 | Compras de natal             |        1 |              1 |
| 21 |   768.90 | 2013-01-16 | Festa                        |        1 |              1 |
| 22 |   827.50 | 2014-01-09 | Festa                        |        1 |              2 |
| 23 |    12.00 | 2014-02-19 | Salgado no aeroporto         |        1 |              2 |
| 24 |   678.43 | 2014-05-21 | Passagem pra Bahia           |        1 |              2 |
| 25 | 10937.12 | 2014-04-30 | Carnaval em Cancun           |        1 |              2 |
| 26 |  1501.00 | 2014-06-22 | Presente da sogra            |        0 |              2 |
| 27 |  1709.00 | 2014-08-25 | Parcela da casa              |        0 |              2 |
| 28 |   567.09 | 2014-09-25 | Parcela do carro             |        0 |              2 |
| 29 |   631.53 | 2014-10-12 | IPTU                         |        1 |              2 |
| 30 |   909.11 | 2014-02-11 | IPVA                         |        1 |              2 |
| 31 |   768.18 | 2014-04-10 | Gasolina viagem Porto Alegre |        1 |              2 |
| 32 |   434.00 | 2014-04-01 | Rodeio interior de Sao Paulo |        0 |              2 |
| 33 |   115.90 | 2014-06-12 | Dia dos namorados            |        0 |              2 |
| 34 |    98.00 | 2014-10-12 | Dia das crianças             |        0 |              2 |
| 35 |   253.70 | 2014-12-20 | Natal - presentes            |        0 |              2 |
| 36 |   370.15 | 2014-12-25 | Compras de natal             |        0 |              2 |
| 37 |    32.09 | 2015-07-02 | Lanchonete                   |        1 |              2 |
| 38 |   954.12 | 2015-11-03 | Show da Ivete Sangalo        |        1 |              2 |
| 39 |    98.70 | 2015-02-07 | Lanchonete                   |        1 |              2 |
| 40 |   213.50 | 2015-09-25 | Roupas                       |        0 |              2 |
| 41 |  1245.20 | 2015-10-17 | Roupas                       |        0 |              2 |
| 42 |    23.78 | 2015-12-18 | Lanchonete do Zé             |        1 |              2 |
| 43 |   576.12 | 2015-09-13 | Sapatos                      |        1 |              2 |
| 44 |    12.34 | 2015-07-19 | Canetas                      |        0 |              2 |
| 45 |    87.43 | 2015-05-10 | Gravata                      |        0 |              2 |
| 46 |   887.66 | 2015-02-02 | Presente para o filhao       |        1 |              2 |
| 48 |   150.00 | 2016-01-05 | Compra de teste              |        0 |              2 |
+----+----------+------------+------------------------------+----------+----------------+
46 rows in set (0,00 sec)

mysql> SELECT * FROM compradores;
+----+----------------+---------------------+-----------+
| id | nome           | endereco            | telefone  |
+----+----------------+---------------------+-----------+
|  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
|  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
+----+----------------+---------------------+-----------+
2 rows in set (0,00 sec)
```

Não... Não era isso que eu queria, eu quero que, em apenas uma *query*, nesse caso um `SELECT`, retorne tanto as informações da tabela `compras` e da tabela `compradores`. Será que podemos fazer isso? Sim, podemos! Lembra da instrução `FROM` que indica qual é a tabela que estamos buscando as informações? Além de buscar por uma única tabela, podemos indicar que queremos buscar por mais de 1 tabela separando as tabelas por ",":

```
SELECT * FROM compras, compradores, tabela3, tabela4, ...;
```

Então vamos adicionar em um único `SELECT` as tabelas: `compras` e `compradores`:

```
mysql> SELECT * FROM compras, compradores;
+----+----------+------------+------------------------------+----------+----------------+----+----------------+---------------------+-----------+
| id | valor    | data       | observacoes                  | recebida | id_compradores | id | nome           | endereco            | telefone  |
+----+----------+------------+------------------------------+----------+----------------+----+----------------+---------------------+-----------+
|  1 |    20.00 | 2016-01-05 | Lanchonete                   |        1 |              1 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
|  1 |    20.00 | 2016-01-05 | Lanchonete                   |        1 |              1 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
|  2 |    15.00 | 2016-01-06 | Lanchonete                   |        1 |              1 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
|  2 |    15.00 | 2016-01-06 | Lanchonete                   |        1 |              1 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
|  3 |   915.50 | 2016-01-06 | Guarda-roupa                 |        0 |              1 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
|  3 |   915.50 | 2016-01-06 | Guarda-roupa                 |        0 |              1 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
|  4 |   949.99 | 2016-01-10 | Smartphone                   |        0 |              1 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
|  4 |   949.99 | 2016-01-10 | Smartphone                   |        0 |              1 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
|  5 |   200.00 | 2012-02-19 | Material escolar             |        1 |              1 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
|  5 |   200.00 | 2012-02-19 | Material escolar             |        1 |              1 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
|  6 |  3500.00 | 2012-05-21 | Televisao                    |        0 |              1 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
|  6 |  3500.00 | 2012-05-21 | Televisao                    |        0 |              1 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
|  7 |  1576.40 | 2012-04-30 | Material de construcao       |        1 |              1 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
|  7 |  1576.40 | 2012-04-30 | Material de construcao       |        1 |              1 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
|  8 |   163.45 | 2012-12-15 | Pizza pra familia            |        1 |              1 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
|  8 |   163.45 | 2012-12-15 | Pizza pra familia            |        1 |              1 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
|  9 |  4780.00 | 2013-01-23 | Sala de estar                |        1 |              1 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
|  9 |  4780.00 | 2013-01-23 | Sala de estar                |        1 |              1 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 10 |   392.15 | 2013-03-03 | Quartos                      |        1 |              1 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 10 |   392.15 | 2013-03-03 | Quartos                      |        1 |              1 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 12 |   402.90 | 2013-03-21 | Copa                         |        1 |              1 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 12 |   402.90 | 2013-03-21 | Copa                         |        1 |              1 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 13 |    54.98 | 2013-04-12 | Lanchonete                   |        0 |              1 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 13 |    54.98 | 2013-04-12 | Lanchonete                   |        0 |              1 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 14 |    12.34 | 2013-05-23 | Lanchonete                   |        0 |              1 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 14 |    12.34 | 2013-05-23 | Lanchonete                   |        0 |              1 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 15 |    78.65 | 2013-12-04 | Lanchonete                   |        0 |              1 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 15 |    78.65 | 2013-12-04 | Lanchonete                   |        0 |              1 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 16 |    12.39 | 2013-01-06 | Sorvete no parque            |        0 |              1 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 16 |    12.39 | 2013-01-06 | Sorvete no parque            |        0 |              1 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 17 |    98.12 | 2013-07-09 | Hopi Hari                    |        1 |              1 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 17 |    98.12 | 2013-07-09 | Hopi Hari                    |        1 |              1 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 18 |  2498.00 | 2013-01-12 | Compras de janeiro           |        1 |              1 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 18 |  2498.00 | 2013-01-12 | Compras de janeiro           |        1 |              1 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 19 |  3212.40 | 2013-11-13 | Compras do mes               |        1 |              1 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 19 |  3212.40 | 2013-11-13 | Compras do mes               |        1 |              1 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 20 |   223.09 | 2013-12-17 | Compras de natal             |        1 |              1 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 20 |   223.09 | 2013-12-17 | Compras de natal             |        1 |              1 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 21 |   768.90 | 2013-01-16 | Festa                        |        1 |              1 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 21 |   768.90 | 2013-01-16 | Festa                        |        1 |              1 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 22 |   827.50 | 2014-01-09 | Festa                        |        1 |              2 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 22 |   827.50 | 2014-01-09 | Festa                        |        1 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 23 |    12.00 | 2014-02-19 | Salgado no aeroporto         |        1 |              2 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 23 |    12.00 | 2014-02-19 | Salgado no aeroporto         |        1 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 24 |   678.43 | 2014-05-21 | Passagem pra Bahia           |        1 |              2 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 24 |   678.43 | 2014-05-21 | Passagem pra Bahia           |        1 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 25 | 10937.12 | 2014-04-30 | Carnaval em Cancun           |        1 |              2 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 25 | 10937.12 | 2014-04-30 | Carnaval em Cancun           |        1 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 26 |  1501.00 | 2014-06-22 | Presente da sogra            |        0 |              2 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 26 |  1501.00 | 2014-06-22 | Presente da sogra            |        0 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 27 |  1709.00 | 2014-08-25 | Parcela da casa              |        0 |              2 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 27 |  1709.00 | 2014-08-25 | Parcela da casa              |        0 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 28 |   567.09 | 2014-09-25 | Parcela do carro             |        0 |              2 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 28 |   567.09 | 2014-09-25 | Parcela do carro             |        0 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 29 |   631.53 | 2014-10-12 | IPTU                         |        1 |              2 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 29 |   631.53 | 2014-10-12 | IPTU                         |        1 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 30 |   909.11 | 2014-02-11 | IPVA                         |        1 |              2 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 30 |   909.11 | 2014-02-11 | IPVA                         |        1 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 31 |   768.18 | 2014-04-10 | Gasolina viagem Porto Alegre |        1 |              2 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 31 |   768.18 | 2014-04-10 | Gasolina viagem Porto Alegre |        1 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 32 |   434.00 | 2014-04-01 | Rodeio interior de Sao Paulo |        0 |              2 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 32 |   434.00 | 2014-04-01 | Rodeio interior de Sao Paulo |        0 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 33 |   115.90 | 2014-06-12 | Dia dos namorados            |        0 |              2 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 33 |   115.90 | 2014-06-12 | Dia dos namorados            |        0 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 34 |    98.00 | 2014-10-12 | Dia das crianças             |        0 |              2 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 34 |    98.00 | 2014-10-12 | Dia das crianças             |        0 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 35 |   253.70 | 2014-12-20 | Natal - presentes            |        0 |              2 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 35 |   253.70 | 2014-12-20 | Natal - presentes            |        0 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 36 |   370.15 | 2014-12-25 | Compras de natal             |        0 |              2 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 36 |   370.15 | 2014-12-25 | Compras de natal             |        0 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 37 |    32.09 | 2015-07-02 | Lanchonete                   |        1 |              2 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 37 |    32.09 | 2015-07-02 | Lanchonete                   |        1 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 38 |   954.12 | 2015-11-03 | Show da Ivete Sangalo        |        1 |              2 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 38 |   954.12 | 2015-11-03 | Show da Ivete Sangalo        |        1 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 39 |    98.70 | 2015-02-07 | Lanchonete                   |        1 |              2 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 39 |    98.70 | 2015-02-07 | Lanchonete                   |        1 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 40 |   213.50 | 2015-09-25 | Roupas                       |        0 |              2 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 40 |   213.50 | 2015-09-25 | Roupas                       |        0 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 41 |  1245.20 | 2015-10-17 | Roupas                       |        0 |              2 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 41 |  1245.20 | 2015-10-17 | Roupas                       |        0 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 42 |    23.78 | 2015-12-18 | Lanchonete do Zé             |        1 |              2 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 42 |    23.78 | 2015-12-18 | Lanchonete do Zé             |        1 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 43 |   576.12 | 2015-09-13 | Sapatos                      |        1 |              2 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 43 |   576.12 | 2015-09-13 | Sapatos                      |        1 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 44 |    12.34 | 2015-07-19 | Canetas                      |        0 |              2 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 44 |    12.34 | 2015-07-19 | Canetas                      |        0 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 45 |    87.43 | 2015-05-10 | Gravata                      |        0 |              2 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 45 |    87.43 | 2015-05-10 | Gravata                      |        0 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 46 |   887.66 | 2015-02-02 | Presente para o filhao       |        1 |              2 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 46 |   887.66 | 2015-02-02 | Presente para o filhao       |        1 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 48 |   150.00 | 2016-01-05 | Compra de teste              |        0 |              2 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 48 |   150.00 | 2016-01-05 | Compra de teste              |        0 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
+----+----------+------------+------------------------------+----------+----------------+----+----------------+---------------------+-----------+
92 rows in set (0,00 sec)
```

Opa! Observe que essa *query* está um pouco estranha, pois ela devolveu 92 linhas, sendo que temos apenas 46 compras registradas! Duplicou os nossos registros... Esse problema aconteceu, pois o MySQL não sabe como associar a tabela compras e a tabela compradores. Precisamos informar ao MySQL por meio de qual coluna ele fará essa associação. Perceba que a coluna que contém a chave (*primary key*) do comprador é justamente a `id_compradores`, chamamos esse tipo de coluna de *FOREIGN KEY* ou chave estrangeira. Para juntarmos a chave estrangeira com a chave primária de uma tabela, utilizamos a instrução `JOIN`, informando a tabela e a chave que queremos associar:

```
mysql> SELECT * FROM compras JOIN compradores on compras.id_compradores = compradores.id;
+----+----------+------------+------------------------------+----------+----------------+----+----------------+---------------------+-----------+
| id | valor    | data       | observacoes                  | recebida | id_compradores | id | nome           | endereco            | telefone  |
+----+----------+------------+------------------------------+----------+----------------+----+----------------+---------------------+-----------+
|  1 |    20.00 | 2016-01-05 | Lanchonete                   |        1 |              1 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
|  2 |    15.00 | 2016-01-06 | Lanchonete                   |        1 |              1 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
|  3 |   915.50 | 2016-01-06 | Guarda-roupa                 |        0 |              1 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
|  4 |   949.99 | 2016-01-10 | Smartphone                   |        0 |              1 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
|  5 |   200.00 | 2012-02-19 | Material escolar             |        1 |              1 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
|  6 |  3500.00 | 2012-05-21 | Televisao                    |        0 |              1 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
|  7 |  1576.40 | 2012-04-30 | Material de construcao       |        1 |              1 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
|  8 |   163.45 | 2012-12-15 | Pizza pra familia            |        1 |              1 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
|  9 |  4780.00 | 2013-01-23 | Sala de estar                |        1 |              1 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 10 |   392.15 | 2013-03-03 | Quartos                      |        1 |              1 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 12 |   402.90 | 2013-03-21 | Copa                         |        1 |              1 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 13 |    54.98 | 2013-04-12 | Lanchonete                   |        0 |              1 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 14 |    12.34 | 2013-05-23 | Lanchonete                   |        0 |              1 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 15 |    78.65 | 2013-12-04 | Lanchonete                   |        0 |              1 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 16 |    12.39 | 2013-01-06 | Sorvete no parque            |        0 |              1 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 17 |    98.12 | 2013-07-09 | Hopi Hari                    |        1 |              1 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 18 |  2498.00 | 2013-01-12 | Compras de janeiro           |        1 |              1 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 19 |  3212.40 | 2013-11-13 | Compras do mes               |        1 |              1 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 20 |   223.09 | 2013-12-17 | Compras de natal             |        1 |              1 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 21 |   768.90 | 2013-01-16 | Festa                        |        1 |              1 |  1 | Alex Felipe    | Rua Vergueiro, 3185 | 5571-2751 |
| 22 |   827.50 | 2014-01-09 | Festa                        |        1 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 23 |    12.00 | 2014-02-19 | Salgado no aeroporto         |        1 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 24 |   678.43 | 2014-05-21 | Passagem pra Bahia           |        1 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 25 | 10937.12 | 2014-04-30 | Carnaval em Cancun           |        1 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 26 |  1501.00 | 2014-06-22 | Presente da sogra            |        0 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 27 |  1709.00 | 2014-08-25 | Parcela da casa              |        0 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 28 |   567.09 | 2014-09-25 | Parcela do carro             |        0 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 29 |   631.53 | 2014-10-12 | IPTU                         |        1 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 30 |   909.11 | 2014-02-11 | IPVA                         |        1 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 31 |   768.18 | 2014-04-10 | Gasolina viagem Porto Alegre |        1 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 32 |   434.00 | 2014-04-01 | Rodeio interior de Sao Paulo |        0 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 33 |   115.90 | 2014-06-12 | Dia dos namorados            |        0 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 34 |    98.00 | 2014-10-12 | Dia das crianças             |        0 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 35 |   253.70 | 2014-12-20 | Natal - presentes            |        0 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 36 |   370.15 | 2014-12-25 | Compras de natal             |        0 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 37 |    32.09 | 2015-07-02 | Lanchonete                   |        1 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 38 |   954.12 | 2015-11-03 | Show da Ivete Sangalo        |        1 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 39 |    98.70 | 2015-02-07 | Lanchonete                   |        1 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 40 |   213.50 | 2015-09-25 | Roupas                       |        0 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 41 |  1245.20 | 2015-10-17 | Roupas                       |        0 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 42 |    23.78 | 2015-12-18 | Lanchonete do Zé             |        1 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 43 |   576.12 | 2015-09-13 | Sapatos                      |        1 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 44 |    12.34 | 2015-07-19 | Canetas                      |        0 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 45 |    87.43 | 2015-05-10 | Gravata                      |        0 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 46 |   887.66 | 2015-02-02 | Presente para o filhao       |        1 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
| 48 |   150.00 | 2016-01-05 | Compra de teste              |        0 |              2 |  2 | João da Silva  | Av. Paulista, 6544  | 2220-4156 |
+----+----------+------------+------------------------------+----------+----------------+----+----------------+---------------------+-----------+
46 rows in set (0,01 sec)
```

A instrução `JOIN` espera uma tabela que precisa ser juntada: `FROM compras JOIN compradores`. Nesse caso estamos juntando a tabela compras com a tabela compradores. Para passarmos o critério de junção, utilizamos a instrução `ON`: `ON compras.id_compradores = compradores.id`. Nesse momento estamos informando a *FOREIGN KEY* da tabela compras (compras.id_compradores) e qual é a chave (compradores.id) da tabela compradores que **referencia** essa *FOREIGN KEY*. 

Agora a nossa *query* retornou os nossos registros corretamente. Porém, ainda existe um problema na nossa tabela compras. Observe esse `INSERT`:

```
mysql> INSERT INTO compras (valor, data, observacoes, id_compradores) 
VALUES (1500, '2016-01-05', 'Playstation 4', 100);
Query OK, 1 row affected (0,00 sec)
```

Vamos verificar a nossa tabela compras:

``` 
mysql> SELECT * FROM compras WHERE id_compradores = 100;
+----+---------+------------+---------------+----------+----------------+
| id | valor   | data       | observacoes   | recebida | id_compradores |
+----+---------+------------+---------------+----------+----------------+
| 49 | 1500.00 | 2016-01-05 | Playstation 4 |        0 |            100 |
+----+---------+------------+---------------+----------+----------------+
1 row in set (0,00 sec)
``` 

Note que não existe o comprador com `id` 100, mas, mesmo assim, conseguimos adicioná-lo na nossa tabela de `compras`. Precisamos garantir a integridade dos nossos dados, informando ao MySQL que a coluna id_compradores da tabela compras é uma *FOREIGN KEY* da tabela compradores, ou seja, só poderemos adicionar um `id` apenas se estiver registrado na tabela compradores. Antes de adicionarmos a *FOREIGN KEY*, precisamos excluir o registro com o `id_compradores` = 100, pois não é possível adicionar uma *FOREIGN KEY* com dados que não exista na tabela que iremos referenciar.

```
mysql> DELETE FROM compras WHERE id_compradores = 100;
Query OK, 1 row affected (0,01 sec)
```

Quando adicionamos uma *FOREIGN KEY* em uma tabela, estamos adicionando uma Constraints, então precisaremos alterar a estrutura da tabela compras utilizando a instrução `ALTER TABLE`:

``` 
mysql> ALTER TABLE compras ADD CONSTRAINT fk_compradores FOREIGN KEY (id_compradores) 
REFERENCES compradores (id);
Query OK, 46 rows affected (0,04 sec)
Records: 46  Duplicates: 0  Warnings: 0
```

Se tentarmos adicionar a compra anterior novamente:

```
mysql> INSERT INTO compras (valor, data, observacoes, id_compradores) 
VALUES (1500, '2016-01-05', 'Playstation 4', 100);
ERROR 1452 (23000): Cannot add or update a child row: a foreign key constraint fails (`ControleDeGastos`.`compras`, CONSTRAINT `fk_compradores` FOREIGN KEY (`id_compradores`) REFERENCES `compradores` (`id`))
```

Agora o nosso banco de dados não permite a inserção de registros com compradores inexistentes! Se tentarmos adicionar essa mesma compra com um comprador existente:

```
INSERT INTO compras (valor, data, observacoes, id_compradores) 
VALUES (1500, '2016-01-05', 'Playstation 4', 1);
Query OK, 1 row affected (0,00 sec)
```

se verificarmos essa compra: 

``` 
mysql> SELECT * FROM compras WHERE observacoes = 'Playstation 4';
+----+---------+------------+---------------+----------+----------------+
| id | valor   | data       | observacoes   | recebida | id_compradores |
+----+---------+------------+---------------+----------+----------------+
| 51 | 1500.00 | 2016-01-05 | Playstation 4 |        0 |              1 |
+----+---------+------------+---------------+----------+----------------+
1 row in set (0,00 sec)
```

## Determinando valores fixos na tabela

Conseguimos deixar a nossa base de dados bem robusta, restringindo as nossas colunas para não permitir valores nulos e não aceitar a inserção de compradores inexistentes, mas agora surgiu uma nova necessidade que precisa ser implementada, precisamos informar também qual é a forma de pagamento que foi realizada na compra, por exemplo, existem 2 formas de pagamento, boleto e crédito. Como poderíamos implementar essa nova informação na nossa tabela atualmente? Criamos uma nova coluna com o tipo `VARCHAR` e corresmos o risco de inserir uma forma de pagamento inválida? Não parece uma boa solução... Que tal criarmos uma nova tabela chamada `id_forma_de_pagto` e fazermos uma *FOREIGN KEY*? Aparentemente é uma boa solução, porém iremos criar uma nova tabela para resolver um problema bem pequeno? Lembre-se que a cada *FOREIGN KEY* mais `JOIN`s as nossas *queries* terão... No MySQL, existe o tipo de dado `ENUM` que permite que informemos quais serão os dados que ele pode aceitar. Vamos adicionar o `ENUM` na nossa tabela de compras:

```
mysql> ALTER TABLE compras ADD COLUMN forma_pagto ENUM('BOLETO', 'CREDITO');
Query OK, 0 rows affected (0,04 sec)
Records: 0  Duplicates: 0  Warnings: 0
```
Vamos tentar adicionar uma compra:

```
mysql> INSERT INTO compras (valor, data, observacoes, id_compradores, forma_pagto) 
VALUES (400, '2016-01-06', 'SSD 128GB', 1, 'BOLETO');
Query OK, 1 row affected (0,00 sec)
```

Retornando essa nova compra:

```
mysql> SELECT * FROM compras WHERE observacoes = 'SSD 128GB';
+----+--------+------------+-------------+----------+----------------+-------------+
| id | valor  | data       | observacoes | recebida | id_compradores | forma_pagto |
+----+--------+------------+-------------+----------+----------------+-------------+
| 52 | 400.00 | 2016-01-06 | SSD 128GB   |        0 |              1 | BOLETO      |
+----+--------+------------+-------------+----------+----------------+-------------+
1 row in set (0,00 sec)
```

Mas e se tentarmos adicionar uma nova compra, porém com a forma de pagamento em dinheiro?

```
mysql> INSERT INTO compras (valor, data, observacoes, id_compradores, forma_pagto) 
VALUES (80, '2016-01-07', 'Bola de futebol', 2, 'DINHEIRO');
Query OK, 1 row affected, 1 warning (0,00 sec)
```

Vamos verificar como ficou na nossa tabela de compras:

```
mysql> SELECT * FROM compras WHERE observacoes = 'Bola de futebol';
+----+-------+------------+-----------------+----------+----------------+-------------+
| id | valor | data       | observacoes     | recebida | id_compradores | forma_pagto |
+----+-------+------------+-----------------+----------+----------------+-------------+
| 53 | 80.00 | 2016-01-07 | Bola de futebol |        0 |              2 |             |
+----+-------+------------+-----------------+----------+----------------+-------------+
1 row in set (0,00 sec)
``` 

## Server SQL Modes

O MySQL impediu que fosse adicionado um valor diferente de "BOLETO" ou "CREDITO", mas o que realmente precisamos é que ele simplesmente não deixe adicionar uma compra que não tenha pelo menos uma dessas formas de pagamento. Além das configurações das tabelas, podemos também configurar o próprio servidor do MySQL. O servidor do MySQL opera em diferentes *<a href="https://dev.mysql.com/doc/refman/5.0/en/sql-mode.html">SQL modes</a>* e dentre esses modos, existe o *strict mode* que tem a finalidade de tratar valores inválidos que configuramos em nossas tabelas para instruções de `INSERT` e `UPDATE`, como por exemplo, o nosso `ENUM`. Para habilitar o *strict mode* precisamos alterar o *SQL mode* da **nossa sessão**. Nesse caso usaremos o modo "STRICT_ALL_TABLES":

``` 
mysql> SET SESSION sql_mode = 'STRICT_ALL_TABLES';
Query OK, 0 rows affected (0,00 sec)
```

Se quisermos verificar se o modo foi modificado podemos retornar esse valor por meio da instrução `SELECT`:

``` 
mysql> SELECT @@SESSION.sql_mode;
+--------------------+
| @@SESSION.sql_mode |
+--------------------+
| STRICT_ALL_TABLES  |
+--------------------+
1 row in set (0,00 sec)
``` 

Agora que configuramos o *SQL mode* do MySQL para impedir a inserção de valores inválidos, vamos apagar o último registro que foi inserido com valor inválido e tentar adicioná-lo novamente:

```
mysql> DELETE FROM compras WHERE observacoes = 'BOLA DE FUTEBOL';
Query OK, 1 row affected (0,00 sec)
``` 

Testando novamente a mesma inserção:

```
mysql> INSERT INTO compras (valor, data, observacoes, id_compradores, forma_pagto) 
    -> VALUES (80, '2016-01-07', 'BOLA DE FUTEBOL', 2, 'DINHEIRO');
ERROR 1265 (01000): Data truncated for column 'forma_pagto' at row 1
```

Perceba que agora o MySQL impediu que a linha fosse inserida, pois o valor não é válido para a coluna `forma_pagto`.

O *SQL mode* é uma configuração do servidor e nós alteramos apenas a *sessão que estávamos logado*, o que aconteceria se caso saímos da sessão que configuramos e entrássemos em uma nova? O MySQL adotaria o *SQL mode* padrão já configurado! Ou seja, teriámos que alterar novamente para o *strict mode*. Mas além da sessão, podemos fazer a configuração global do *SQL mode*.

```
mysql> SET GLOBAL sql_mode = 'STRICT_ALL_TABLES';
Query OK, 0 rows affected (0,00 sec)
``` 

Se verificarmos a configuração global para o *SQL mode*:

```
mysql> SELECT @@GLOBAL.sql_mode;
+-------------------+
| @@GLOBAL.sql_mode |
+-------------------+
| STRICT_ALL_TABLES |
+-------------------+
1 row in set (0,00 sec)
```

Agora, todas as vezes que entrarmos no MySQL, será adotado o *strict mode*. 

O `ENUM` é uma boa solução quando queremos restringir valores específicos e já esperados em um coluna, porém não faz parte do padrão *ANSI* que é o padrão para a escrita de instruções `SQL`, ou seja, é um recurso exclusivo do MySQL e cada banco de dados possui a sua própria implementação para essa mesma funcionadalidade. 

## Resumindo

Nesse capítulo aprendemos a fazer uma relação entre duas tabelas utilizando *FOREIGN KEYS*. Vimos também que para fazermos *queries* com duas tabelas diferentes utilizamos as chaves estrangeiras por meio da instrução `JOIN` que informa ao MySQL quais serão os critérios para associar as tabelas distintas. E sempre precisamos lembrar que, quando estamos lidando com *FOREIGN KEY*, precisamos criar uma Constraint para garantir que todas as chaves estrangeiras precisa existir na tabela que fazemos referência. Além disso, vimos também que quando queremos adicionar uma coluna nova para que aceite apenas determinados valores já esperado por nós, como é o caso da forma de pagamento, podemos utilizar o `ENUM` do MySQL como um solução, porém é importante lembrar que é uma solução que não faz parte do padrão ANSI, ou seja, cada banco de dados possui sua própria implementação. Vamos para os exercícios?

Vamos para os exercícios?

# Exercícios

1. Crie a tabela compradores com `id`, `nome`, `endereco` e `telefone`. 

2. Insira os compradores, Guilherme e João da Silva.

3. Adicione a coluna `id_compradores` na tabela `compras` e defina a chave estrangeira (*FOREIGN KEY*) referenciando o `id` da tabela `compradores`.

4. Atualize a tabela `compras` e insira o `id` dos compradores na coluna `id_compradores`.

5. Exiba o NOME do comprador e o VALOR de todas as compras feitas antes de 09/08/2014. 

6. Exiba todas as compras do comprador que possui ID igual a 2.

7. Exiba todas as compras (mas sem os dados do comprador), cujo comprador tenha nome que começa com 'GUILHERME'.

8. Exiba o nome do comprador e a soma de todas as suas compras.

9. A tabela `compras` foi alterada para conter uma *FOREIGN KEY* referenciando a coluna `id` da tabela `compradores`. O objetivo é deixar claro para o banco de dados que `compras.id_compradores` está de alguma forma relacionado com a tabela `compradores` através da coluna `compradores.id`. Mesmo sem criar a `FOREIGN KEY` é possível relacionar tabelas através do comando JOIN.  

10. Qual a vantagem em utilizar a `FOREIGN KEY`? 

11. Crie uma coluna chamada "forma_pagto" do tipo `ENUM` e defina os valores: 'BOLETO' e 'CREDITO'.

12. Ative o *strict mode* na sessão que está utilizando para impossibilitar valores inválidos. Utilize o modo "STRICT_ALL_TABLES". E verifique se o *SQL mode* foi alterado fazendo um `SELECT` na sessão.

13. Tente inserir uma compra com forma de pagamento diferente de 'BOLETO' ou 'CREDITO', por exemplo, 'DINHEIRO' e verifique se o MySQL recusa a inserção. 

14. Adicione as formas de pagamento para todas as compras por meio da instrução `UPDATE`.

15. Faça a configuração global do MySQL para que ele sempre entre no *strict mode*.
