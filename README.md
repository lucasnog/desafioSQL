

## DESAFIO SQL - MENTORIA +praTI

### Pré requisitos:
Compatível com qualquer banco de dados SQL, incluindo, mas não se limitando a:
- MySQL
- PostgreSQL
- SQL Server
- Oracle Database

### - Criar um banco de dados

```sql
CREATE DATABASE empresa1;
```

### - Criar duas tabelas: funcionario e setor.

Tabela funcionario:
- id
- salario
- nome
- setor(pode ser nulo)
- dataAdm
- dataDesligamento

```sql
USE empresa1;
CREATE TABLE funcionario (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nome VARCHAR(255),
    salario INT,
    idSetor INT NULL,
    dataAdmissao DATE,
    dataDesligamento DATE NULL
);
```


Tabela Setor
-  setor

```sql
USE empresa1;
CREATE TABLE setor (
    id INT PRIMARY KEY AUTO_INCREMENT,
    setor VARCHAR(255)
);
```


Considerações para todas as questões abaixo: 
- Todas as questões devem ser respondidas com base em funcionários ativos.
- As tabelas foram populadas com os dados fictícios: 

    <details>
    <summary>Dados</summary>

    ```sql
    USE empresa1;

    INSERT INTO funcionario (nome, salario, idSetor, dataAdmissao, dataDesligamento) 
    VALUES
        ('Alice Silva', 5000, NULL, '2023-01-01', NULL),
        ('Bob Santos', 6000, 2, '2023-02-01', NULL),
        ('Carlos Oliveira', 7000, 5, '2023-03-01', NULL),
        ('Daniela Pereira', 8000, 4, '2023-04-01', NULL),
        ('Eduardo Costa', 9000, 5, '2023-05-01', '2023-08-31'),
        ('Fernanda Lima', 10000, 3, '2023-06-01', NULL),
        ('Gustavo Souza', 5500, 2, '2023-07-01', NULL),
        ('Helena Martins', 6600, 3, '2023-08-01', '2023-05-31'),
        ('Igor Santos', 7700, NULL, '2023-09-01', NULL),
        ('Juliana Lima', 8800, 5, '2023-10-01', NULL),
        ('Kai Oliveira', 9900, 1, '2023-11-01', NULL),
        ('Laura Costa', 13000, 2, '2023-12-01', NULL),
        ('Marcos Oliveira', 6000, 1, '2024-01-01', NULL),
        ('Natalia Santos', 7000, 4, '2024-02-01', NULL),
        ('Otavio Costa', 8000, 5, '2024-03-01', NULL),
        ('Patricia Lima', 9000, 1, '2024-04-01', NULL),
        ('Quiteria Oliveira', 19000, 3, '2024-05-01', NULL),
        ('Rafael Souza', 5500, 1, '2024-06-01', '2024-07-31'),
        ('Sara Martins', 6600, 4, '2024-07-01', NULL),
        ('Thiago Lima', 7700, 5, '2024-08-01', '2024-05-31');

    INSERT INTO setor (setor)
    VALUES
        ('rh'),
        ('ti'),
        ('diretoria'),
        ('financeiro'),
        ('producao');
    ```
    </details>

<br>
1- Qual o funcionário que tem o maior salário?
<br>
<br>

<details>
  <summary>Resolução 1</summary>
  

```sql
SELECT funcionario.nome, funcionario.salario as maiorSalario
FROM funcionario
WHERE dataDesligamento IS NULL AND funcionario.salario = (SELECT MAX(funcionario.salario) 
                                              FROM funcionario);
```

</details>

2- Qual o custo da folha de pagamento?

<details>
  <summary>Resolução 2</summary>
  
- O custo total da folha pode ser obtido:

```sql
SELECT COUNT(*) as funcionariosAtivos, SUM(salario) as folhaTotal
FROM funcionario
WHERE dataDesligamento IS NULL;
```

- Para obter o custo por setor:

```sql
SELECT funcionario.salario as mediaSalarialPorSetor, setor.setor
FROM funcionario
JOIN setor ON setor.id = funcionario.idSetor
WHERE dataDesligamento IS NULL
GROUP BY setor.setor;
```

</details>

3- Qual é o nome e salário do profissional de TI que ganha o melhor valor?

<details>
  <summary>Resolução 3</summary>
  

```sql
SELECT funcionario.nome, funcionario.salario, setor.setor
FROM funcionario
JOIN setor ON setor.id = funcionario.idSetor
WHERE dataDesligamento IS NULL 
AND setor.setor = 'ti' 
AND funcionario.salario = (SELECT MAX(funcionario.salario) 
                FROM funcionario
                JOIN setor ON setor.id = funcionario.idSetor
                WHERE setor.setor = 'ti');
```

</details>

4- Quanto ganha em media um profissional de TI?
<details>
  <summary>Resolução 4</summary>
  

```sql
SELECT setor.setor, AVG(salario) as mediaSalarial
FROM funcionario
JOIN setor ON setor.id = funcionario.idSetor
WHERE dataDesligamento IS NULL 
AND setor.setor = 'ti';
```
</details>
5- Dos funcionarios de RH qual o menor salário?
<br>
<br>
<details>
  <summary>Resolução 5</summary>
  
```sql
SELECT funcionario.nome, setor.setor, funcionario.salario, funcionario.dataAdmissao
FROM funcionario
JOIN setor ON setor.id = funcionario.idSetor
WHERE dataDesligamento IS NULL 
AND setor.setor = 'rh'
AND funcionario.salario = (SELECT MIN(funcionario.salario)
               FROM funcionario
               JOIN setor ON setor.id = funcionario.idSetor
               WHERE setor.setor = 'rh'
               AND funcionario.dataDesligamento IS NULL);
```
</details>
6- Me de o nome e o salario dos maiores salarios de funcionarios de cada setor.
<br>
<br>
<details>
  <summary>Resolução 6</summary>
  
```sql
SELECT funcionario.nome, funcionario.salario, setor.setor
FROM funcionario
JOIN (SELECT idSetor, MAX(salario) AS maiorSalario
FROM funcionario
WHERE dataDesligamento IS NULL
GROUP BY idSetor) maiorPorCategoria ON funcionario.idSetor = maiorPorCategoria.idSetor 
AND funcionario.salario = maiorPorCategoria.maiorSalario
JOIN setor ON funcionario.idSetor = setor.id
```
</details>

7- Existe algum funcionário sem setor?

<details>
  <summary>Resolução 7</summary>
  
```sql
SELECT nome, salario, dataAdmissao
FROM funcionario
WHERE dataDesligamento IS NULL AND funcionario.idSetor IS NULL;
```
</details>



