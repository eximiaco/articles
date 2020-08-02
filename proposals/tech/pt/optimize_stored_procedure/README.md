# OTIMIZANDO STORED PROCEDURES

**Há algum tempo, por ingenuidade, alguns programadores assumem que usar stored procedures é uma coisa ruim. Muitos assumem que há risco de “vazar” o domínio do código aplicação para o banco de dados. Entretanto, quando isso ocorre, a causa é o abuso da tecnologia e não da tecnologia em si.**

Utilizar *stored procedures* para consultar o banco de dados tem uma série de benefícios comparado com consultas *ad hoc*: são mais rápidas, reduzem o tráfego na rede, são mais seguras e podem encapsular código reutilizável. Além de tudo isso, são armazenadas de forma ótima pelo SQL Server.

Toda vez que uma consulta é submetida ao SQL Server, ela precisa ser interpretada (*query parser* e *algebrizer*), otimizada (*optimizer*) e, finalmente, executada. Se forem *stored procedures*, porém, a interpretação e otimização só ocorrem na primeira execução, nas demais, a execução ocorre de maneira direta. Entretanto, é necessário estar atento para identificar se o banco “entende” a consulta da forma adequada.

Consideremos, por exemplo, a seguinte *stored procedure* para a base *[AdventureWorks2019](https://docs.microsoft.com/pt-br/sql/samples/adventureworks-install-configure?view=sql-server-ver15&tabs=ssms)*.

```
CREATE OR ALTER PROCEDURE dbo.usp_GetPerson 
( 
    @BusinessEntityID	INT = NULL, 
    @StateProvinceID	INT = NULL, 
    @CountryRegionCode	NVARCHAR(3) = NULL 
) 
AS 
BEGIN
	
	SET NOCOUNT ON

	SELECT
		A.FirstName,
		A.LastName
	 FROM Person.Person as A
	 LEFT OUTER JOIN Person.BusinessEntityAddress as B
	   ON A.BusinessEntityID = B.BusinessEntityID
	 LEFT OUTER JOIN Person.Address as C
	   ON B.AddressID = C.AddressID 
	 LEFT OUTER JOIN Person.StateProvince as D
	   ON C.StateProvinceID = D.StateProvinceID 
	 LEFT OUTER JOIN Person.CountryRegion as E
	   ON D.CountryRegionCode = E.CountryRegionCode
	WHERE (@BusinessEntityID IS NULL OR A.BusinessEntityID = @BusinessEntityID) 
	  AND (@StateProvinceID IS NULL OR C.StateProvinceID = @StateProvinceID) 
	  AND (@CountryRegionCode IS NULL OR D.CountryRegionCode = @CountryRegionCode)

END
```

De maneira simples, queremos retornar um conjunto de registros filtrando com base em um conjunto de parâmetros opcionais.

> ## [information] Evite o prefixo 'sp_' para nomes de stored procedures
>Ao criar *stored procedures*, evite utilizar o padrão *sp_\<nome da procedure\>* ao nomeá-las. Esse padrão de nomenclatura é utilizado pelo *SQL Server* para as *procedures* de sistema, e faz com que ele procure se a *procedure* existe antes no banco *Master* e somente depois no banco atual.
>
>Utilizar outra nomenclatura evita a primeira busca desnecessária, um pequeno ganho de performance.

É muito comum encontramos esse padrão de escrita de *procedures* para resolver consultas que pode ter vários parâmetros opcionais como possíveis filtros. Infelizmente, apesar desse código ser rápido e prático de escrever, existem alguns problemas nessa abordagem. Eles ficam evidentes na análise do plano de execução para consultas simples.

![Plano de Execução](./storedprocedure1.png)\