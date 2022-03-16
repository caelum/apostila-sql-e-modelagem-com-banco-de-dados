# Alterando e restringindo o formato de nossas tabelas

Muitas vezes que inserimos dados em um banco de dados, precisamos saber quais são os tipos de dados de cada coluna da tabela que queremos popular. Vamos dar uma olhada novamente na estrutura da nossa tabela por meio da instrução `DESC`: 

```
mysql> DESC compras;
+-------------+---------------+------+-----+---------+----------------+
| Field       | Type          | Null | Key | Default | Extra          |
+-------------+---------------+------+-----+---------+----------------+
| id          | int(11)       | NO   | PRI | NULL    | auto_increment |
| valor       | decimal(18,2) | YES  |     | NULL    |                |
| data        | date          | YES  |     | NULL    |                |
| observacoes | varchar(255)  | YES  |     | NULL    |                |
| recebida    | tinyint(4)    | YES  |     | NULL    |                |
+-------------+---------------+------+-----+---------+----------------+
5 rows in set (0,00 sec)
```

Como já vimos, o MySQL demonstra algumas características das colunas da nossa tabela. Vamos verificar a coluna `Null`, o que será que ela significa? Será que está dizendo que nossas colunas aceitam valores vazios? Então vamos tentar inserir uma compra com `observacoes` **vazia**, ou seja, **nula**, com o valor *null*:

```
INSERT INTO compras (valor, data, recebida, observacoes) 
VALUES (150, '2016-01-04', 1, NULL);
Query OK, 1 row affected (0,01 sec)
```

Vamos verificar o resultado:

```
mysql> SELECT * FROM compras WHERE data = '2016-01-04';
+----+--------+------------+-------------+----------+
| id | valor  | data       | observacoes | recebida |
+----+--------+------------+-------------+----------+
| 47 | 150.00 | 2016-01-04 | NULL        |        1 |
+----+--------+------------+-------------+----------+
1 row in set (0,00 sec)
```

O nosso banco de dados permitiu o registro de uma compra sem observação. Mas em qual momento informamos ao MySQL que queriámos valores nulos? Em nenhum momento! Porém, quando criamos uma tabela no MySQL e não informamos se queremos ou não valores nulos para uma determinada coluna, por padrão, o MySQL aceita os valores nulos.

Note que um texto vazio é diferente de não ter nada! É diferente de um texto vazio como *''*. Nulo é nada, é a inexistência de um valor. Um texto sem caracteres é um texto com zero caracteres. Um valor nulo nem texto é, nem nada é. Nulo é o não ser. Uma questão filosófica milenar, discutida entre os melhores filósofos e programadores, além de ser fonte de muitos bugs. Cuidado.

## Restringindo os nulos


Para resolver esse problema podemos criar restrições, *Constraints*, que tem a capacidade de determinar as regras que as colunas de nossas tabelas terão. Antes de configurar o *Constraints*, vamos verificar todos os registros que tiverem observações nulas e vamos apagá-los. Queremos selecionar todas as observações que são nulas, são nulas, SÃO NULAS, `IS NULL`:

```
mysql> SELECT * FROM compras WHERE observacoes IS NULL;
+----+--------+------------+-------------+----------+
| id | valor  | data       | observacoes | recebida |
+----+--------+------------+-------------+----------+
| 47 | 150.00 | 2016-01-04 | NULL        |        1 |
+----+--------+------------+-------------+----------+
1 row in set (0,00 sec)
```

Vamos excluir todas as compras que tenham as observações nulas:

```
DELETE FROM compras WHERE observacoes IS NULL;
Query OK, 1 row affected (0,01 sec)
```

## Adicionando Constraints

Podemos definir *Constraints* no momento da criação da tabela, como no caso de definir que nosso valor e data não podem ser nulos (`NOT NULL`):

```
CREATE TABLE compras(
id INT AUTO_INCREMENT PRIMARY KEY, 
valor DECIMAL(18,2) NOT NULL, 
data DATE NOT NULL, 
...
```

Porém, a tabela já existe! E não é uma boa prática excluir a tabela e cria-la novamente, pois podemos **perder registros**! Para fazer alterações na estrutura da tabela, podemos utilizar a instrução `ALTER TABLE` que vimos antes:

```
ALTER TABLE compras;
```

Precisamos especificar o que queremos fazer na tabela, nesse caso modificar uma coluna e adicionar uma *Constraints*:

```
mysql> ALTER TABLE compras MODIFY COLUMN observacoes VARCHAR(255) NOT NULL;
Query OK, 45 rows affected (0,04 sec)
Records: 45  Duplicates: 0  Warnings: 0
```
Observe que foram alteradas todas as 45 linhas existentes no nosso banco de dados. Se verificarmos a estrutura da nossa tabela novamente:

``` 
mysql> DESC compras;
+-------------+---------------+------+-----+---------+----------------+
| Field       | Type          | Null | Key | Default | Extra          |
+-------------+---------------+------+-----+---------+----------------+
| id          | int(11)       | NO   | PRI | NULL    | auto_increment |
| valor       | decimal(18,2) | YES  |     | NULL    |                |
| data        | date          | YES  |     | NULL    |                |
| observacoes | varchar(255)  | NO   |     | NULL    |                |
| recebida    | tinyint(4)    | YES  |     | NULL    |                |
+-------------+---------------+------+-----+---------+----------------+
5 rows in set (0,01 sec)
```

Na coluna *Null* e na linha das `observacoes` está informando que não é mais permitido valores nulos. Vamos testar:

```
mysql> INSERT INTO compras (valor, data, recebida, observacoes) 
VALUES (150, '2016-01-04', 1, NULL);
ERROR 1048 (23000): Column 'observacoes' cannot be null
```

## Valores Default

Vamos supor que a maioria das compras que registramos não são entregues e que queremos que o próprio MySQL entenda que, quando eu não informar a coluna recebida, ela seja populada com o valor 0. No MySQL, além de *Constraints*, podemos adicionar valores padrões, no inglês *Default*, em uma coluna utilizando a instrução `DEFAULT`:

```
mysql> ALTER TABLE compras MODIFY COLUMN recebida tinyint(1) DEFAULT 0;
Query OK, 0 rows affected (0,00 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

Inserindo uma compra nova sem informa a coluna `recebida`:

```
INSERT INTO compras (valor, data, observacoes) 
VALUES (150, '2016-01-05', 'Compra de teste');
Query OK, 1 row affected (0,01 sec)
```

Verificando o valor inserido na coluna recebida:

```
mysql> SELECT * FROM compras;
+----+----------+------------+------------------------------+----------+
| id | valor    | data       | observacoes                  | recebida |
+----+----------+------------+------------------------------+----------+
|  1 |    20.00 | 2016-01-05 | Lanchonete                   |        1 |
|  2 |    15.00 | 2016-01-06 | Lanchonete                   |        1 |
|  3 |   915.50 | 2016-01-06 | Guarda-roupa                 |        0 |
| ...|      ... |        ... | ...                          |      ... |
| 48 |   150.00 | 2016-01-05 | Compra de teste              |        0 |
+----+----------+------------+------------------------------+----------+
46 rows in set (0,00 sec)
```

Se quisemos informar que o valor padrão deveria ser entregue, bastaria utilizar o mesmo comando, porém modificando o 0 para 1.

## Evolução do banco

Nossa escola deseja agora entrar em contato por email com todos os pais de alunos de um determinado bairro devido a maior taxa de ocorrência de dengue. Como fazer isso se o campo de endereço não obrigava o bairro? Podemos a partir de agora adicionar um novo campo:

```
Aluno
- id
- nascimento
- nome
- serieAtual
- salaAtual
- email
- telefone
- bairro
- endereco
- nomeDoPai
- nomeDaMae
- telefoneDoPai
- telefoneDaMae
```

Além disso, depois de um ano descobrimos um bug no sistema. Alguns alunos sairam do mesmo, como representar que não existe mais série atual e nem sala atual para esses alunos? Podemos escolher três abordagens:

1. marcar os campos serieAtual e salaAtual como NULL
2. marcar os campos como NULL e colocar um campo ativo como 0 ou 1
3. colocar um campo ativo como 0 ou 1

Repare que o primeiro e o último parecem ser simples de implementar, claramente mais simples que implementar os dois ao mesmo tempo (item 2). Mas cada um deles implica em um problema de modelagem diferente.

Se assumimos (1) e colocamos campos serieAtual e salaAtual como NULL, toda vez que queremos saber quem está inativo temos uma query bem estranha, que não parece fazer isso:

```
select * from Alunos where serieAtual is null and salaAtual is null
```

Sério mesmo? Isso significa que o aluno está inativo? Dois nulos? Além disso, se eu permito nulos, eu posso ter um caso muito estranho como o aluno a seguir:

```
Guilherme Silveira, seriaAtual 8, salaAtual null
```

Como assim? É um aluno novo que alguém/o sistema esqueceu de colocar a sala? Ou é um aluno antigo que alguém/o sistema esqueceu de remover a série?

Por outro lado, na terceira abordagem, a do campo ativo, solto, gera um possível aluno como:

```
Guilherme Silveira, serieAtual 8, salaAtual B, ativo 0
```

Como assim, o Guilherme está inativo mas está na 8B ao mesmo tempo? Ou ele está na 8B ou não está, não tem as duas coisas ao mesmo tempo. Modelo estranho.

Por fim, a abordagem de modelagem 2, que utiliza o novo campo e o valor nulo, garante que as queries sejam simples:

```
select * from Alunos where ativo=false;
```

Mas ainda permite casos estranhos como:

```
Guilherme Silveira, serieAtual 8, salaAtual B, ativo 0
Guilherme Silveira, serieAtual NULL, salaAtual B, ativo 0
Guilherme Silveira, serieAtual NULL, salaAtual NULL, ativo 1
```

Poderíamos usar recursos bem mais avançados para tentar nos proteger de alguns desses casos, mas esses recursos são, em geral, específicos de um banco determinado. Por exemplo, funcionam no Oracle mas não no MySQL. Ou no MySQL mas não no PostgreSQL. Nesse curso focamos em padrões do SQL que conseguimos aplicar, em geral, para todos eles.

E agora? Três modelagens diferentes, todas resolvem o problema e todas geram outros problemas técnicos. Modelar bancos é tomar decisões que apresentam vantagens e desvantagens. Não há regra em qual das três soluções devemos escolher, escolha a que você julgar melhor e mais justa para sua empresa/sistema/ocasião. O mais importante é tentar prever os possíveis pontos de erro como os mencionados aqui. Conhecer as falhas em nosso desenho de modelagem (design) de antemão permite que não tenhamos uma surpresa desagradável quando descobrirmos elas de uma maneira infeliz no futuro.

## Resumindo

Vimos que além de criar as tabelas em nosso banco de dados precisamos verificar se alguma coluna pode receber valores nulos ou não, podemos determinar essas regras por meio de *Constraints* no momento da criação da tabela ou utilizando a instrução `ALTER TABLE` se caso a tabela exista e queremos modificar sua estrutura. Também vimos que em alguns casos os valores de cada `INSERT` pode se repetir muito e por isso podemos definir valores padrões com a instrução `DEFAULT`.

# Exercícios

1. Configure o valor padrão para a coluna `recebida`.

2. Configure a coluna `observacoes` para não aceitar valores nulos. 

3. No nosso modelo atual, qual campo você deixaria com valores `DEFAULT` e quais não? Justifique sua decisão. Note que como estamos falando de modelagem, não existe uma regra absoluta, existe vantagens e desvantagens na decisão que tomar, tente citá-las.

4. `NOT NULL` e `DEFAULT` podem ser usados também no `CREATE TABLE`? Crie uma tabela nova e adicione *Constraints* e valores `DAFAULT`.

5. Reescreva o `CREATE TABLE` do começo do curso, marcando `observacoes` como nulo e valor padrão do `recebida` como 1.
