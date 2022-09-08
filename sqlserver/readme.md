# SQL Server

A documentação da Microsoft é no geral bem boa e com exemplos suficientes.

O problema é que em alguns cenários, com dúvidas bem pontuais - ex. ordem de palavras chave de um update - fica um pouco poluída a explicação pois geralmente existem muitas possibilidades de partes "opcionais" de um comando.

Mantendo o exemplo do [UPDATE](https://docs.microsoft.com/pt-br/sql/t-sql/queries/update-transact-sql?view=sql-server-ver16), todas as possibilidades de sintaxe são expostas de cara, e isso pode ser confuso para quem está tendo um primeiro contato com a documentação:

```tsql
-- Syntax for SQL Server and Azure SQL Database  

[ WITH <common_table_expression> [...n] ]  
UPDATE   
    [ TOP ( expression ) [ PERCENT ] ]   
    { { table_alias | <object> | rowset_function_limited   
         [ WITH ( <Table_Hint_Limited> [ ...n ] ) ]  
      }  
      | @table_variable      
    }  
    SET  
        { column_name = { expression | DEFAULT | NULL }  
          | { udt_column_name.{ { property_name = expression  
                                | field_name = expression }  
                                | method_name ( argument [ ,...n ] )  
                              }  
          }  
          | column_name { .WRITE ( expression , @Offset , @Length ) }  
          | @variable = expression  
          | @variable = column = expression  
          | column_name { += | -= | *= | /= | %= | &= | ^= | |= } expression  
          | @variable { += | -= | *= | /= | %= | &= | ^= | |= } expression  
          | @variable = column { += | -= | *= | /= | %= | &= | ^= | |= } expression  
        } [ ,...n ]   
  
    [ <OUTPUT Clause> ]  
    [ FROM{ <table_source> } [ ,...n ] ]   
    [ WHERE { <search_condition>   
            | { [ CURRENT OF   
                  { { [ GLOBAL ] cursor_name }   
                      | cursor_variable_name   
                  }   
                ]  
              }  
            }   
    ]   
    [ OPTION ( <query_hint> [ ,...n ] ) ]  
[ ; ]  
  
<object> ::=  
{   
    [ server_name . database_name . schema_name .   
    | database_name .[ schema_name ] .   
    | schema_name .  
    ]  
    table_or_view_name}
```

## [T-SQL - Comandos e Dicas](tsql/)

## Índices

[Entendendo o funcionamento dos índices no SQL Server](https://www.dirceuresende.com/blog/entendendo-o-funcionamento-dos-indices-no-sql-server/)

[Índices e o excesso de coisa boa – Parte 01: identificando casos críticos](https://imasters.com.br/banco-de-dados/indices-e-o-excesso-de-coisa-boa-parte01-identificando-casos-criticos)

[Índices e o excesso de coisa boa – Parte 02: objetos sem uso](https://imasters.com.br/banco-de-dados/indices-e-o-excesso-de-coisa-boa-parte-02-objetos-sem-uso)

## Código eficiente

[Construindo códigos T-SQL eficientes (sargable)](https://portosql.wordpress.com/2018/10/04/construindo-codigos-t-sql-eficientes-sargability/)

## Transações

[Entendendo Transaction Isolation Level no SQL Server](https://imasters.com.br/data/entendendo-transaction-isolation-level-no-sql-server)
