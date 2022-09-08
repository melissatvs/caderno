# T-SQL

## Comandos e Dicas

### Variáveis

Usando o `DECLARE` você pode declarar variáveis e usá-las dentro do escopo.

Para definir o valor de uma variável, pode ser feito na declaração, usando `SET` ou `SELECT`.

#### Exemplos de declaração, inicialização e uso do `SET`

```TSQL
DECLARE
    @DataHora DATETIME,
    @Id       INT,
    @Nome     VARCHAR(20) = 'Um nome qualquer';

SET @DataHora = GETDATE();
SET @Id = 100;

SELECT @DataHora AS DataHora, @Id AS Id, @Nome AS Nome;
```

#### Exemplo de definição de valor com `SELECT`

```TSQL
DECLARE @QuantidadeTabelas INT;

SELECT @QuantidadeTabelas = COUNT(*)
FROM [sys].[objects] o
WHERE o.[type] = 'U'

SELECT @QuantidadeTabelas;
```

### Elementos de linguagem

#### `GO` [:mag:](https://docs.microsoft.com/pt-br/sql/t-sql/language-elements/sql-server-utilities-statements-go?view=sql-server-ver15)

Define o fim de um lote de comandos. As instruções entre dois comandos `GO` são do mesmo escopo, assim as variáveis declaradas não ficam acessíveis após um comando `GO`.

Pode ser usado um inteiro junto do `GO`, o valor informado é o número de vezes que será executado o lote de comandos até o `GO` anterior.

##### Exemplos 


No exemplo abaixo, o primeiro select será executado duas vezes, e o segundo select uma vez, mostrando 3 resultados.

```TSQL
SELECT COUNT(*)
FROM [sys].[objects] o
WHERE o.[type] = 'U';
GO 2

SELECT COUNT(*)
FROM [sys].[objects] o
WHERE o.[type] = 'U';
GO
```

Nesse exemplo o segundo bloco está com erro, pois a variável @Id não fica acessível após o primeiro `GO`.

```TSQL
DECLARE
    @Id INT;

SET @Id = 1;

SELECT @Id;
GO

SET @Id = 2; -- erro 

SELECT @Id; -- erro 
GO
```

#### `USE` [:mag:](https://docs.microsoft.com/pt-br/sql/t-sql/language-elements/use-transact-sql?view=sql-server-ver15)

Define o banco que estará conectado.

##### Exemplo 

Nesse exemplo a conexão é mudada de base e os selects são feitos em bases diferentes sem a necessidade de especificar a base junto do nome da tabela.

```TSQL
USE tempdb;
GO

SELECT COUNT(*)
FROM [sys].[objects] o
WHERE o.[type] = 'U'
GO

USE [master];
GO

SELECT COUNT(*)
FROM [sys].[objects] o
WHERE o.[type] = 'U'
GO
```

#### `PRINT` [:mag:](https://docs.microsoft.com/pt-br/sql/t-sql/language-elements/print-transact-sql?view=sql-server-ver15)

Mostra uma mensagem em modo texto, pode ser útil para mostrar mensagens ao invés de resultados em modo de grade.

```TSQL
PRINT 'Olá mundo';
```

Também pode ser passado valores de variáveis ao `PRINT`:

```TSQL
DECLARE
    @Texto VARCHAR(10) = 'pessoa',
    @Id    INT = 10;

PRINT 'Olá, ' + @Texto + '! Tudo bem?';
PRINT @Id;
PRINT 'Id: ' + CAST(@Id AS VARCHAR);
```

#### `IF..ELSE` [:mag:](https://docs.microsoft.com/pt-br/sql/t-sql/language-elements/if-else-transact-sql?view=sql-server-ver15)

Para definir o início e fim do bloco usar o par `BEGIN..END`.

```TSQL
DECLARE
    @QuantidadeTabelas INT;

SELECT @QuantidadeTabelas = COUNT(*)
FROM [sys].[objects] o
WHERE o.[type] = 'U'

IF @QuantidadeTabelas > 0
BEGIN
   PRINT 'Foram encontradas ' + CAST(@QuantidadeTabelas AS VARCHAR) + ' tabelas'
END
ELSE
BEGIN
   PRINT 'Nenhuma tabela encontrada'
END
```

#### `WHILE` [:mag:](https://docs.microsoft.com/pt-br/sql/t-sql/language-elements/while-transact-sql?view=sql-server-ver15)

Funciona como nas demais linguagens, os delimitadores de início e fim do bloco são literais como no `IF` (`BEGIN..END`).

* [ ] Pensar em exemplo

#### Cursor [:mag:](https://docs.microsoft.com/pt-br/sql/t-sql/language-elements/cursors-transact-sql?view=sql-server-ver15)

> :warning: **Máxima: Nunca use cursor**
>
> Sim, existem extremistas que te falarão para nunca usar cursor!
> 
> Eles tem parcela de razão, pois na maioria dos cenários um WHILE pode te atender, ou consultas otimizadas (que explorem bem a teoria de conjuntos, dentro o SQL Server).
>  E o ponto crucial dessa posição é que o Cursor é muito caro em termos de processamento, realmente o uso dele deve ser em último caso.
>
> Mas o cursor não deixa de existir quando ele não é usado, ou quando o nome dele não é citado. Pode existir algum cenário em que um cursor já exista! E se vc precisar transferir ele pra outra forma de código ou dar manutenção? 
>
> O mais importante é: Conheça o *maior número de objetos e ferramentas disponibilizadas pelo SQL Server e entenda quando e como usar*

Permite posicionar em um registro específico de uma consulta, sendo possível processar uma linha por vez.

Para montar uma estrutura básica, de um cursor que percorra linha-a-linha, você precisa:

1. Declarar um cursor

   ```TSQL
   DECLARE <nome_do_cursor> CURSOR FOR
      <consulta>;
   ```

2. Abrir o cursor

   ```TSQL
   OPEN <nome_do_cursor>;
   ```

3. Posicionar na primeira linha

   ```TSQL
   FETCH NEXT FROM <nome_do_cursor>;
   ```
   
   > Como o cursor acabou de ser criado, e não está posicionado em nenhuma linha, o comando de "próxima linha" posiciona na primeira linha.

4. Verificar o estado do cursor para percorrer

   ```TSQL
   WHILE @@FETCH_STATUS = 0
   ```

   > `@@FETCH_STATUS` retorna o estado do último comando `FETCH` executado em qualquer cursor aberto na conexão atual.
   >
   > Os valores retornados dessa função são:
   > Valor retornado | Descrição
   > ----------------|-----------
   > 0               | A instrução FETCH foi bem-sucedida.
   > -1              | A instrução FETCH falhou ou a linha estava além do conjunto de resultados.
   > -2              | A linha buscada está ausente.
   > -9              | O cursor não está executando uma operação de busca.

5. Dentro do `WHILE` para iterar, usar o `FETCH NEXT`:

   ```TSQL
   FETCH NEXT FROM <nome_do_cursor>;
   ```

6. Fora do `WHILE` para finalizar o cursor:

   ```TSQL
   CLOSE <nome_do_cursor>
   DEALLOCATE <nome_do_cursor>
   ```

* [ ] Pensar em exemplo

### Metadados

#### Identity [:mag:](https://docs.microsoft.com/pt-br/sql/t-sql/functions/ident-current-transact-sql?view=sql-server-ver15)

Para saber qual valor foi gerado por um campo *Identity*, existem as formas abaixo (cada uma com suas particularidades):

- `IDENT_CURRENT` retorna o último valor de identidade gerado para uma tabela específica em qualquer sessão e escopo.

- `@@IDENTITY`: retorna o último valor de identidade gerado para qualquer tabela na sessão atual, em todos os escopos.

- `SCOPE_IDENTITY`: retorna o último valor de identidade gerado para qualquer tabela na sessão e no escopo atuais.

Importante observar a necessidade de acordo com a possível concorrência no campo em específico, geralmente na inserção de dados em tabelas já existentes.

Se tabelas ou campos são novos, essa preocupação pode não existir.

* [ ] Pensar em exemplo

#### DB_ID [:mag:](https://docs.microsoft.com/pt-br/sql/t-sql/functions/db-id-transact-sql?view=sql-server-ver15)

Passando no nome da base de dados é retornado o id, caso não exista retorna NULL.

##### Exemplo

```TSQL
DECLARE
   @NomeBase SYSNAME = 'NomeBancoExemplo';

IF (DB_ID(@NomeBase) IS NULL)
BEGIN
   PRINT @NomeBase + ' não existe';
END
ELSE
BEGIN
   PRINT @NomeBase + ' existe';
END;
```

#### OBJECT_ID [:mag:](https://docs.microsoft.com/pt-br/sql/t-sql/functions/object-id-transact-sql?view=sql-server-ver15)

Passando o nome e o tipo do objeto é retornado o id, caso não exista retorna NULL.

##### Exemplo

```TSQL
DECLARE
   @NomeTabela SYSNAME = 'TabelaXYZ';

IF (OBJECT_ID(@NomeTabela, 'U') IS NULL)
BEGIN
   PRINT 'Tabela ' + @NomeTabela + ' não existe';
END
ELSE
BEGIN
   PRINT 'Tabela ' + @NomeTabela + ' existe';
END;
```

##### Tipos

Tipo | Descrição
-----|:------------------------------------------------ 
C    | Check
D    | Default
F    | Foreign Key
FN   | Function
P    | Stored Procedure
PK   | Primary Key
SN   | Sinônimo
SO   | Sequence
U    | Tabela
V    | View
TR   | Trigger*

*SQL Server 2012 ou superior

#### COLUMNPROPERTY [:mag:](https://docs.microsoft.com/pt-br/sql/t-sql/functions/columnproperty-transact-sql?view=sql-server-ver15)

Informando Id da tabela, nome da coluna e nome da propriedade, é retornado o valor da propriedade.

Em scripts pode ser útil a combinação com `OBJECT_ID` para verificar se uma coluna existe, como no exemplo a seguir.

##### Exemplo

```TSQL
DECLARE
   @Tabela SYSNAME = 'TabelaConhecida',
   @Coluna SYSNAME = 'ColunaMisteriosa',
   @Propriedade VARCHAR(20) = 'ColumnId';

IF COLUMNPROPERTY(OBJECT_ID(@Tabela, 'U'), @Coluna, @Propriedade) IS NULL
BEGIN
   PRINT CONCAT('Coluna ', @Coluna, ' não existe na tabela ', @Tabela);
END
ELSE
BEGIN
   PRINT CONCAT('Coluna ', @Coluna, ' existe na tabela ', @Tabela);
END;
```