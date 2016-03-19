<properties
	pageTitle="Scrivere una funzione con valori di tabella inline per selezionare le righe di cui eseguire la migrazione (Database Estensione) | Microsoft Azure"
	description="Informazioni su come creare un predicato del filtro per selezionare le righe di cui eseguire la migrazione."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglasl"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="douglasl"/>

# Scrivere una funzione con valori di tabella inline per selezionare le righe di cui eseguire la migrazione (Database Estensione)

Se si archiviano dati cronologici in una tabella separata, è possibile configurare il Database Estensione per eseguire la migrazione dell'intera tabella. Se la tabella contiene dati attuali e cronologici, è tuttavia possibile specificare un predicato del filtro per selezionare le righe di cui eseguire la migrazione. Il predicato del filtro deve chiamare una funzione con valori di tabella inline. Questo argomento descrive come scrivere una funzione con valori di tabella inline per selezionare le righe di cui eseguire la migrazione.

In CTP 3.1 fino a RC0 l'opzione che consente di specificare un predicato non è disponibile nella procedura guidata per l'abilitazione del database per l'estensione. È necessario usare l'istruzione ALTER TABLE per configurare il Database Estensione con questa opzione. Per altre informazioni, vedere [ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx).

Se non si specifica alcun predicato del filtro, viene eseguita la migrazione dell'intera tabella.

> [!IMPORTANTE] Se si specifica un predicato del filtro con esecuzione inadeguata, la migrazione dei dati sarà a sua volta inadeguata. Il Database Estensione applica il predicato del filtro alla tabella tramite l'operatore CROSS APPLY.

## Requisiti di base per la funzione con valori di tabella inline
La funzione con valori di tabella inline necessaria per una funzione di filtro del Database Estensione è simile all'esempio seguente.

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate(@column1 datatype1, @column2 datatype2 [, ...n])
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN	SELECT 1 AS is_eligible
		WHERE <predicate>
```
I parametri della funzione devono essere identificatori per le colonne della tabella.

L'associazione allo schema è necessaria per evitare che le colonne usate nel predicato del filtro vengano eliminato o modificato.

### Valore restituito
Se la funzione restituisce un risultato non vuoto, la riga è idonea alla migrazione. In caso contrario, ovvero se la funzione non restituisce alcuna riga, la riga non è idonea alla migrazione.

### Condizioni
Il &lt;*predicato*&gt; può essere costituito da una o più condizioni unite tramite l'operatore logico AND.

```
<predicate> ::= <condition> [ AND <condition> ] [ ...n ]
```
Ogni condizione può essere costituita a sua volta da una condizione primitiva o da più condizioni primitive unite con l'operatore logico OR.

```
<condition> ::= <primitive_condition> [ OR <primitive_condition> ] [ ...n ]
```

### Condizioni primitive
Una condizione di primitiva può eseguire uno dei confronti seguenti.

```
<primitive_condition> ::=
{
<function_parameter> <comparison_operator> constant
| <function_parameter> { IS NULL | IS NOT NULL }
| <function_parameter> IN ( constant [ ,...n ] )
}
```

-   Confrontare un parametro della funzione in un'espressione costante. ad esempio `@column1 < 1000`.

    Ecco un esempio che controlla se il valore di una colonna *date* è &lt; 1\\/1\\/2016.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate(@column1 datetime)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
    GO

    ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
    	FILTER_PREDICATE = dbo.fn_stretchpredicate(date),
    	MIGRATION_STATE = OUTBOUND
    ) )
    ```

-   Applicare l'operatore IS NULL o IS NOT NULL a un parametro della funzione.

-   Usare l'operatore IN per confrontare un parametro della funzione con un elenco di valori costanti.

    Ecco un esempio che controlla se il valore di una colonna *shipment\_status* è `IN (N'Completed', N'Returned', N'Cancelled')`.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate(@column1 nvarchar(15))
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 IN (N'Completed', N'Returned', N'Cancelled')
    GO

    ALTER TABLE table1 SET ( REMOTE_DATA_ARCHIVE = ON (
    	FILTER_PREDICATE = dbo.fn_stretchpredicate(shipment_status),
    	MIGRATION_STATE = OUTBOUND
    ) )
    ```

### Operatori di confronto
Sono supportatigli operatori di confronto seguenti.

`<, <=, >, >=, =, <>, !=, !<, !>`

```
<comparison_operator> ::= { < | <= | > | >= | = | <> | != | !< | !> }
```

### Espressioni costanti
Le costanti usate in un predicato del filtro possono essere un'espressione deterministica qualsiasi che può essere valutata quando si definisce la funzione. Le espressioni costanti possono contenere gli elementi seguenti.

-   Valori letterali. ad esempio `N’abc’, 123`.

-   Espressioni algebriche. ad esempio `123 + 456`.

-   Funzioni deterministiche. ad esempio `SQRT(900)`.

-   Conversioni deterministiche che usano CAST o CONVERT. ad esempio `CONVERT(datetime, '1/1/2016', 101)`.

### Altre espressioni
È possibile usare gli operatori BETWEEN e NOT BETWEEN se il predicato risultante è conforme alle regole descritte di seguito dopo la sostituzione degli operatori BETWEEN con le espressioni equivalenti AND e OR.

È possibile usare sottoquery o funzioni non deterministiche, ad esempio RAND() o GETDATE().

## Esempi di funzioni valide

-   L'esempio seguente combina due condizioni primitive usando l'operatore logico AND.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate((@column1 datetime, @column2 nvarchar(15))
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
      WHERE @column1 < N'20150101' AND @column2 IN (N'Completed', N'Returned', N'Cancelled')
    GO

    ALTER TABLE table1 SET ( REMOTE_DATA_ARCHIVE = ON (
    	FILTER_PREDICATE = dbo.fn_stretchpredicate(date, shipment_status),
    	MIGRATION_STATE = OUTBOUND
    ) )
    ```

-   L'esempio seguente usa diverse condizioni e una conversione deterministica con CONVERT.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example1(@column1 datetime, @column2 int, @column3 nvarchar)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2015', 101)AND (@column2 < -100 OR @column2 > 100 OR @column2 IS NULL)AND @column3 IN (N'Completed', N'Returned', N'Cancelled')
    GO
    ```

-   L'esempio seguente usa funzioni e operatori matematici.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example2(@column1 float)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 < SQRT(400) + 10
    GO
    ```

-   L'esempio seguente usa gli operatori BETWEEN e NOT BETWEEN. Questa modalità d'uso è valida perché il predicato risultante è conforme alle regole descritte di seguito dopo la sostituzione degli operatori BETWEEN con le espressioni equivalenti AND e OR.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example3(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 BETWEEN 0 AND 100
    			AND (@column2 NOT BETWEEN 200 AND 300 OR @column1 = 50)
    GO
    ```
    La funzione precedente è equivalente alla funzione riportata di seguito dopo la sostituzione degli operatori BETWEEN e NOT BETWEEN con le espressioni equivalenti AND e OR.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example4(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 >= 0 AND @column1 <= 100AND (@column2 < 200 OR @column2 > 300 OR @column1 = 50)
    GO
    ```

## Esempi di funzioni non valide

-   La funzione seguente non è valida perché contiene una conversione non deterministica.

    ```tsql
    CREATE FUNCTION dbo.fn_example5(@column1 datetime)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 < CONVERT(datetime, '1/1/2016')
    GO
    ```

-   La funzione seguente non è valida perché contiene una chiamata di funzione non deterministica.

    ```tsql
    CREATE FUNCTION dbo.fn_example6(@column1 datetime)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 < DATEADD(day, -60, GETDATE())
    GO
    ```

-   La funzione seguente non è valida perché contiene una sottoquery.

    ```tsql
    CREATE FUNCTION dbo.fn_example7(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 IN (SELECT SupplierID FROM Supplier WHERE Status = 'Defunct'))
    GO
    ```

-   Le funzioni seguenti non sono valide perché le espressioni che usano operatori algebrici o le funzioni predefinite devono restituire una costante quando si definisce la funzione. È possibile includere riferimenti a colonne in chiamate di funzione o espressioni algebriche.

    ```tsql
    CREATE FUNCTION dbo.fn_example8(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 % 2 =  0
    GO

    CREATE FUNCTION dbo.fn_example9(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE SQRT(@column1) = 30
    GO
    ```

-   La funzione seguente non è valida perché viola le regole descritte di seguito dopo la sostituzione dell'operatore BETWEEN con l'espressione equivalente AND.

    ```tsql
    CREATE FUNCTION dbo.fn_example10(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE (@column1 BETWEEN 1 AND 200 OR @column1 = 300) AND @column2 > 1000
    GO
    ```
    La funzione precedente è equivalente alla funzione riportata di seguito dopo la sostituzione dell'operatore BETWEEN con l'espressione equivalente AND. Questa funzione non è valida perché le condizioni primitive possono usare solo l'operatore logico OR.

    ```tsql
    CREATE FUNCTION dbo.fn_example11(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE (@column1 >= 1 AND @column1 <= 200 OR @column1 = 300) AND @column2 > 1000
    GO
    ```

## Come viene applicato il predicato del filtro da parte del Database Estensione
Il Database Estensione applica il predicato del filtro alla tabella e determina le righe idonee tramite l'operatore CROSS APPLY. Ad esempio:

```tsql
SELECT * FROM stretch_table_name CROSS APPLY fn_stretchpredicate(column1, column2)
```
Se la funzione restituisce un risultato non vuoto per la riga, questa è idonea alla migrazione.

## Aggiungere un predicato del filtro a una tabella
Per aggiungere un predicato del filtro a una tabella, eseguire l'istruzione ALTER TABLE e specificare una funzione con valori di tabella inline esistente come valore del parametro FILTER\_PREDICATE. ad esempio:

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
	FILTER_PREDICATE = dbo.fn_stretchpredicate(column1, column2),
	MIGRATION_STATE = <desired_migration_state>
) )
```
Dopo aver associato la funzione alla tabella con predicato, le affermazioni seguenti sono vere.

-   Alla prossima migrazione dei dati, viene eseguita la migrazione solo delle righe per cui la funzione restituisce un valore non vuoto.

-   Le colonne usate dalla funzione sono associate a schema. Non è possibile modificare queste colonne finché una tabella usa la funzione come predicato del filtro.

## Rimuovere un predicato del filtro da una tabella
Per eseguire la migrazione dell'intera tabella invece che delle righe selezionate, rimuovere il parametro FILTER\_PREDICATE esistente mediante l'impostazione su Null. Ad esempio:

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
	FILTER_PREDICATE = NULL,
	MIGRATION_STATE = <desired_migration_state>
) )
```
Dopo avere rimosso il predicato del filtro, tutte le righe nella tabella saranno idonee per la migrazione.

## Sostituire un predicato del filtro esistente
Per sostituire un predicato del filtro specificato in precedenza, eseguire di nuovo l'istruzione ALTER TABLE e specificare un nuovo valore per il parametro FILTER\_PREDICATE. Ad esempio:

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
	FILTER_PREDICATE = dbo.fn_stretchpredicate2(column1, column2),
	MIGRATION_STATE = <desired_migration_state>
```
La nuova funzione con valori di tabella inline ha i requisiti seguenti.

-   La nuova funzione deve essere meno restrittiva rispetto alla funzione precedente.

-   Tutti gli operatori presenti nella funzione precedente devono essere presenti nella nuova funzione.

-   La nuova funzione non può contenere operatori che non esistono nella funzione precedente.

-   Non è possibile modificare l'ordine degli argomenti dell'operatore.

-   Solo i valori costanti che fanno parte di un confronto `<, <=, >, >=` possono essere modificati in modo da rendere meno restrittivo il predicato.

### Esempio di sostituzione valida
Si supponga che la funzione seguente sia il predicato del filtro corrente.

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate_old (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN	SELECT 1 AS is_eligible
		WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
			AND (@column2 < -100 OR @column2 > 100)
GO
```
La funzione seguente è una sostituzione valida perché la nuova costante date, che specifica una data limite successiva, rende il predicato meno restrittivo.

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate_new (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN	SELECT 1 AS is_eligible
		WHERE @column1 < CONVERT(datetime, '2/1/2016', 101)
			AND (@column2 < -50 OR @column2 > 50)
GO
```

### Esempi di sostituzioni non valide
La funzione seguente è una sostituzione valida perché la nuova costante date, che specifica una data limite precedente, non rende il predicato meno restrittivo.

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_1 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN	SELECT 1 AS is_eligible
		WHERE @column1 < CONVERT(datetime, '1/1/2015', 101)
			AND (@column2 < -100 OR @column2 > 100)
GO
```
La funzione seguente non è una sostituzione valida perché uno degli operatori di confronto è stato rimosso.

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_2 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN	SELECT 1 AS is_eligible
		WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
			AND (@column2 < -50)
GO
```
La funzione seguente non è una sostituzione valida perché è stata aggiunta una nuova condizione con l'operatore logico AND.

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_3 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN	SELECT 1 AS is_eligible
		WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
			AND (@column2 < -100 OR @column2 > 100)
			AND (@column2 <> 0)
GO
```

## Eliminare un predicato del filtro
Non è possibile eliminare una funzione colonne con valori di tabella inline finché una tabella usa la funzione come predicato del filtro.

## Controllare il predicato del filtro applicato a una tabella
Per controllare il predicato del filtro applicato a una tabella, aprire la vista del catalogo **sys.remote\_data\_archive\_tables** e controllare il valore della colonna **filter\_predicate**. Se il valore è Null, l'intera tabella è idonea all'archiviazione. Per altre informazioni, vedere [sys.remote\_data\_archive\_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx).

## Vedere anche
[ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)

<!---HONumber=AcomDC_0302_2016-->