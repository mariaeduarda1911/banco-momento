# banco-momento
Atividade de consulta em banco de dados.
# Trabalho Prático: Banco de Dados da Empresa Momento

## Sobre a Empresa
**Bem-vindo à base de dados da empresa Momento!**

A Momento é uma empresa inovadora do setor de tecnologia e vendas de produtos temáticos de super-heróis e cultura pop. Fundada em 1980, a empresa começou como uma pequena loja em Gotham City e hoje possui escritórios em diversos países, com departamentos especializados em Vendas, Tecnologia, Recursos Humanos, Marketing, Financeiro e Dados.

---

## Exercícios e Resoluções SQL

### Módulo 1: Consultas Básicas e Inserção

#### 1.1 Inclusão no sistema
> **Pergunta:** Você acabou de ser contratado pela Momento! Vamos começar incluindo você no sistema.

```sql
INSERT INTO funcionarios (
    funcionario_id, primeiro_nome, sobrenome, email, senha, telefone, 
    data_contratacao, cargo_id, salario, gerente_id, departamento_id
) VALUES (
    1000, 'Maria Eduarda', 'Neres Oliveira', 'maria.neres@momento.org', 
    '2509F!rlm', '119.788.4567', '2000-06-17', 4, 15000.00, NULL, 9
);

SELECT * FROM funcionarios WHERE primeiro_nome LIKE 'Maria%';
```

#### 1.2 Total de funcionários
> **Pergunta:** Agora que você faz parte da equipe, quantos funcionários temos ao total na empresa?

```sql
SELECT COUNT(*) FROM funcionarios;
```

#### 1.3 Funcionários em Tecnologia
> **Pergunta:** Quantos funcionários trabalham especificamente no Departamento de Tecnologia?

```sql
SELECT COUNT(*) FROM funcionarios WHERE departamento_id = 6;
```

#### 1.4 Departamentos da empresa
> **Pergunta:** Liste todos os departamentos que existem na empresa. Quantos são?

```sql
SELECT * FROM departamentos;
SELECT COUNT(*) FROM departamentos;
```

#### 1.5 Escritórios e Países
> **Pergunta:** Quantos escritórios a Momento possui? Em quais países?

```sql
SELECT COUNT(*) FROM escritorios;
SELECT escritorio_nome, pais_id FROM escritorios;
```

---

### Módulo 2: Agregações e Métricas Salariais

#### 2.1 Funcionários em Vendas
> **Pergunta:** Quantos funcionários trabalham no Departamento de Vendas?

```sql
SELECT COUNT(*) FROM funcionarios WHERE departamento_id = 8;
```

#### 2.2 Custo salarial em Vendas
> **Pergunta:** Qual é o custo total com salários do Departamento de Vendas?

```sql
SELECT SUM(salario) FROM funcionarios WHERE departamento_id = 8;
```

#### 2.3 Média salarial geral (excluindo C-Level)
> **Pergunta:** Qual é a média salarial da empresa, excluindo os cargos de CEO, CMO e CFO?

```sql
SELECT AVG(salario) FROM funcionarios WHERE cargo_id NOT IN (4, 10, 7);
```

#### 2.4 Média salarial em Tecnologia
> **Pergunta:** Qual é a média salarial do Departamento de Tecnologia?

```sql
SELECT AVG(salario) FROM funcionarios WHERE departamento_id = 6;
```

#### 2.5 Departamento com maior média salarial
> **Pergunta:** Qual departamento possui a maior média salarial?

```sql
SELECT departamento_id, AVG(salario) AS media 
FROM funcionarios 
GROUP BY departamento_id 
ORDER BY media DESC 
LIMIT 1;
```

#### 2.6 Departamento com menor número de funcionários
> **Pergunta:** Qual departamento possui o menor número de funcionários?

```sql
SELECT departamento_id, COUNT(*) AS num_func 
FROM funcionarios 
GROUP BY departamento_id 
ORDER BY num_func ASC 
LIMIT 1;
```

---

### Módulo 3: Dependentes e Histórico de Contratação

#### 3.1 Funcionários com cônjuges
> **Pergunta:** Quantos funcionários da empresa Momento possuem cônjuges?

```sql
SELECT COUNT(DISTINCT f.funcionario_id) AS tot_func 
FROM funcionarios f
JOIN dependentes d ON f.funcionario_id = d.funcionario_id 
WHERE d.relacionamento = 'Cônjuge';
```

#### 3.2 Funcionários com filhos
> **Pergunta:** Quantos funcionários possuem filhos registrados?

```sql
SELECT COUNT(DISTINCT f.funcionario_id) AS tot_func 
FROM funcionarios f
JOIN dependentes d ON f.funcionario_id = d.funcionario_id 
WHERE d.relacionamento = 'Filha(o)';
```

#### 3.3 Funcionário mais antigo
> **Pergunta:** Qual funcionário foi contratado há mais tempo na empresa?

```sql
SELECT MIN(data_contratacao) FROM funcionarios;
```

#### 3.4 Funcionário mais recente
> **Pergunta:** Qual funcionário foi contratado há menos tempo na empresa?

```sql
SELECT MAX(data_contratacao) FROM funcionarios;
```

#### 3.5 Top 5 mais antigos
> **Pergunta:** Liste os 5 funcionários com mais tempo de casa, ordenados pela data de contratação.

```sql
SELECT primeiro_nome, sobrenome 
FROM funcionarios 
ORDER BY data_contratacao ASC 
LIMIT 5;
```

#### 3.6 Contratações na década de 1990
> **Pergunta:** Quantos funcionários foram contratados na década de 1990 (entre 1990-1999)?

```sql
SELECT COUNT(*) 
FROM funcionarios 
WHERE data_contratacao BETWEEN '1991-01-01' AND '1999-12-31';
```

#### 3.7 Evolução da média salarial por ano
> **Pergunta:** Como a média salarial da Momento evoluiu ao longo dos anos? Agrupe por ano de contratação e calcule a média salarial.

```sql
SELECT 
    YEAR(data_contratacao) AS ano_contratacao, 
    ROUND(AVG(salario), 2) AS media_salarial 
FROM funcionarios
GROUP BY YEAR(data_contratacao) 
ORDER BY ano_contratacao ASC;
```

---

### Módulo 4: Suprimentos e Inventário

#### 4.1 Custo de suprimentos por escritório
> **Pergunta:** Qual é o custo total de suprimentos em cada escritório? Ordene do mais caro ao mais barato.

```sql
SELECT 
    e.escritorio_nome, 
    SUM(s.quantidade_comprada * s.custo) AS custo_total 
FROM suprimentos s
JOIN escritorios e ON s.escritorio_id = e.escritorio_id 
GROUP BY e.escritorio_id 
ORDER BY custo_total DESC;
```

#### 4.2 Maior diversidade de suprimentos
> **Pergunta:** Qual escritório possui a maior quantidade de diferentes tipos de suprimentos?

```sql
SELECT 
    e.escritorio_nome, 
    COUNT(DISTINCT s.suprimento_id) AS tipos_suprimentos 
FROM escritorios e
JOIN suprimentos s ON e.escritorio_id = s.escritorio_id 
GROUP BY e.escritorio_id 
ORDER BY tipos_suprimentos DESC 
LIMIT 1;
```

#### 4.3 Suprimento mais caro
> **Pergunta:** Qual é o suprimento mais caro (considerando preço unitário) em toda a empresa?

```sql
SELECT suprimento_nome, custo 
FROM suprimentos 
ORDER BY custo DESC 
LIMIT 1;
```

#### 4.4 Valor total do inventário de suprimentos
> **Pergunta:** Calcule o valor total do inventário de suprimentos da empresa (quantidade × preço unitário de todos os itens em todos os escritórios).

```sql
SELECT SUM(quantidade_comprada * custo) AS valor_total 
FROM suprimentos;
```

---

### Módulo 5: Produtos e Vendas

#### 5.1 Produtos vendidos
> **Pergunta:** Quais produtos foram vendidos pela Momento? Liste todos os produtos únicos.

```sql
SELECT DISTINCT p.produto_id, p.produto_nome 
FROM produtos p
JOIN vendas v ON p.produto_id = v.produto_id;
```

#### 5.2 Produto mais vendido (unidades)
> **Pergunta:** Qual é o produto mais vendido (maior quantidade total)?

```sql
SELECT p.produto_nome, SUM(v.quantidade_vendida) AS total_vendido 
FROM produtos p
JOIN vendas v ON p.produto_id = v.produto_id 
GROUP BY p.produto_id 
ORDER BY total_vendido DESC 
LIMIT 1;
```

#### 5.3 Produto menos vendido
> **Pergunta:** Qual é o produto menos vendido?

```sql
SELECT p.produto_nome, SUM(v.quantidade_vendida) AS total_vendido 
FROM produtos p
JOIN vendas v ON p.produto_id = v.produto_id 
GROUP BY p.produto_id 
ORDER BY total_vendido ASC 
LIMIT 1;
```

#### 5.4 Produto com maior receita
> **Pergunta:** Pensando na relação quantidade × valor unitário, qual produto gerou mais receita para a empresa?

```sql
SELECT 
    p.produto_nome, 
    SUM(v.quantidade_vendida * CAST(p.produto_price AS DECIMAL(8,2))) AS receita_total 
FROM produtos p
JOIN vendas v ON p.produto_id = v.produto_id 
GROUP BY p.produto_id 
ORDER BY receita_total DESC 
LIMIT 1;
```

#### 5.5 Produto com maior preço unitário
> **Pergunta:** Qual é o produto mais caro (maior preço unitário) no catálogo?

```sql
SELECT produto_nome, CAST(produto_price AS DECIMAL(8,2)) AS preco 
FROM produtos 
ORDER BY preco DESC 
LIMIT 1;
```

#### 5.6 Faturamento total em vendas
> **Pergunta:** Qual foi o faturamento total da empresa em vendas?

```sql
SELECT SUM(v.quantidade_vendida * CAST(p.produto_price AS DECIMAL(8,2))) AS faturamento_total 
FROM vendas v
JOIN produtos p ON v.produto_id = p.produto_id;
```

#### 5.7 Vendas em junho de 2023
> **Pergunta:** Quantas vendas foram realizadas no mês de junho de 2023?

```sql
SELECT COUNT(*) AS total_vendas 
FROM vendas 
WHERE venda_data BETWEEN '2023-06-01' AND '2023-06-30';
```

#### 5.8 Vendedor com mais transações
> **Pergunta:** Qual vendedor realizou mais vendas (em quantidade de transações)?

```sql
SELECT f.primeiro_nome, f.sobrenome, COUNT(v.venda_id) AS total_transacoes 
FROM funcionarios f
JOIN vendas v ON f.funcionario_id = v.funcionario_id 
GROUP BY f.funcionario_id 
ORDER BY total_transacoes DESC 
LIMIT 1;
```

#### 5.9 Vendedor com maior receita
> **Pergunta:** Qual vendedor gerou mais receita para a empresa?

```sql
SELECT 
    f.primeiro_nome, 
    f.sobrenome, 
    SUM(v.quantidade_vendida * CAST(p.produto_price AS DECIMAL(8,2))) AS receita_gerada 
FROM funcionarios f
JOIN vendas v ON f.funcionario_id = v.funcionario_id 
JOIN produtos p ON v.produto_id = p.produto_id 
GROUP BY f.funcionario_id 
ORDER BY receita_gerada DESC 
LIMIT 1;
```

---

### Módulo 6: Atualizações e Operações Avançadas (DML)

#### 6.1 Criar novo departamento
> **Pergunta:** Um novo departamento foi criado: Inovações. Ele será alocado no escritório "Wayne Offices". Adicione-o ao banco de dados.

```sql
INSERT INTO departamentos (departamento_nome, escritorio_id) 
SELECT 'Inovações', escritorio_id 
FROM escritorios 
WHERE escritorio_nome = 'Wayne Offices';
```

#### 6.2 Transferir funcionários
> **Pergunta:** O departamento de Inovações está sem funcionários. Transfira 2 funcionários do departamento de Tecnologia para Inovações.

```sql
UPDATE funcionarios 
SET departamento_id = (SELECT departamento_id FROM departamentos WHERE departamento_nome = 'Inovações') 
WHERE departamento_id = (SELECT departamento_id FROM departamentos WHERE departamento_nome = 'Tecnologia') 
LIMIT 2;
```

#### 6.3 Aumento salarial para o departamento de Tecnologia
> **Pergunta:** A empresa decidiu dar um aumento de 10% para todos os funcionários do departamento de Tecnologia. Atualize os salários.

```sql
UPDATE funcionarios 
SET salario = salario * 1.10 
WHERE departamento_id = (SELECT departamento_id FROM departamentos WHERE departamento_nome = 'Tecnologia');
```

#### 6.4 Promoção de funcionário
> **Pergunta:** O funcionário "Bruce Ernst" foi promovido a "Senior Web Developer" e recebeu um aumento para $5.000. Atualize suas informações.

```sql
UPDATE funcionarios 
SET 
    cargo_id = (SELECT cargo_id FROM cargos WHERE cargo_nome = 'Senior Web Developer'), 
    salario = 5000.00 
WHERE primeiro_nome = 'Bruce' AND sobrenome = 'Ernst';
```

#### 6.5 Inserção de novo suprimento
> **Pergunta:** Adicione um novo suprimento ao escritório "Wayne Offices": 15 unidades de "Headsets" com preço unitário de $150 cada.

```sql
INSERT INTO suprimentos (suprimento_nome, quantidade_comprada, custo, escritorio_id) 
VALUES (
    'Headsets', 15, 150.00, 
    (SELECT escritorio_id FROM escritorios WHERE escritorio_nome = 'Wayne Offices')
);
```

#### 6.6 Remoção por aposentadoria
> **Pergunta:** Todos os funcionários contratados antes de 1990 estão se aposentando. Remova-os do banco de dados.

```sql
-- Verificar quantos serão afetados
SELECT COUNT(*) FROM funcionarios WHERE data_contratacao < '1990-01-01';

-- Executar remoção
DELETE FROM funcionarios WHERE data_contratacao < '1990-01-01';
```