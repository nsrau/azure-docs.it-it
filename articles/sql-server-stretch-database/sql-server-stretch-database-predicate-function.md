<properties
	pageTitle="Usare un predicato del filtro per selezionare righe di cui eseguire la migrazione (Estensione database) | Microsoft Azure"
	description="Informazioni su come usare un predicato del filtro per selezionare righe di cui eseguire la migrazione."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/14/2016"
	ms.author="douglasl"/>

# Usare un predicato del filtro per selezionare righe di cui eseguire la migrazione (Estensione database)

Se si archiviano dati cronologici in una tabella separata, è possibile configurare il Database Estensione per eseguire la migrazione dell'intera tabella. Se la tabella contiene dati attuali e cronologici, è tuttavia possibile specificare un predicato del filtro per selezionare le righe di cui eseguire la migrazione. Il predicato del filtro è una funzione con valori di tabella inline. Questo argomento descrive come scrivere una funzione con valori di tabella inline per selezionare le righe di cui eseguire la migrazione.

>   [AZURE.NOTE] Se si specifica un predicato del filtro con esecuzione inadeguata, la migrazione dei dati sarà a sua volta inadeguata. Il Database Estensione applica il predicato del filtro alla tabella tramite l'operatore CROSS APPLY.

Se non si specifica alcun predicato del filtro, viene eseguita la migrazione dell'intera tabella.

Quando si esegue la procedura guidata Abilitare il database per l'estensione, è possibile eseguire la migrazione di un'intera tabella o specificare un predicato semplice nella procedura guidata. Per usare un tipo di predicato del filtro diverso per selezionare le righe di cui eseguire la migrazione, completare una delle operazioni seguenti.

-   Chiudere la procedura guidata ed eseguire l'istruzione ALTER TABLE per abilitare l'estensione per la tabella e specificare un predicato.

-   Eseguire l'istruzione ALTER TABLE per specificare un predicato dopo aver chiuso la procedura guidata.

La sintassi di ALTER TABLE per l'aggiunta di un predicato è descritta più avanti in questo argomento.

## Requisiti di base per il predicato del filtro
La funzione con valori di tabella inline necessaria per un predicato del filtro del Database Estensione è simile all'esempio seguente.

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
Se la funzione restituisce un risultato non vuoto, la riga è idonea alla migrazione. In caso contrario, ovvero se la funzione non restituisce un risultato, la riga non è idonea alla migrazione.

### Condizioni
Il &lt;*predicato*&gt; può essere costituito da una condizione o da più condizioni unite dall'operatore logico AND.

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

    Questo esempio verifica se il valore di una colonna *date* è &lt; 1/1/2016.

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

    Questo esempio verifica se il valore di una colonna *shipment\_status* è `IN (N'Completed', N'Returned', N'Cancelled')`.

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

Non è possibile eliminare una funzione colonne con valori di tabella inline finché una tabella usa la funzione come predicato del filtro.

>   [AZURE.NOTE] Per migliorare le prestazioni della funzione di filtro, creare un indice nelle colonne usate dalla funzione.

### Passaggio dei nomi di colonna al predicato del filtro
Quando si assegna una funzione di filtro a una tabella, specificare un nome composto da una sola parte per i nomi di colonna passati alla funzione di filtro. Se in questo passaggio si specifica un nome in tre parti, le successive query sulla tabella abilitata per l'estensione avranno esito negativo.

Ad esempio, se si specifica un nome di colonna di tre parti, come illustrato nell'esempio seguente, l'istruzione verrà eseguita correttamente, ma le successive query sulla tabella avranno esito negativo.

```tsql
ALTER TABLE SensorTelemetry
  SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE=dbo.fn_stretchpredicate(dbo.SensorTelemetry.ScanDate),
    MIGRATION_STATE = OUTBOUND )
  )
```

Specificare invece la funzione di filtro con un nome di colonna in una sola parte, come illustrato nell'esempio seguente.

```tsql
ALTER TABLE SensorTelemetry
  SET ( REMOTE_DATA_ARCHIVE = ON  (
    FILTER_PREDICATE=dbo.fn_stretchpredicate(ScanDate),
    MIGRATION_STATE = OUTBOUND )
  )
```

## <a name="addafterwiz"></a>Aggiungere un predicato del filtro dopo l'esecuzione della procedura guidata  

Se si desidera usare un predicato che non è possibile creare nella procedura guidata **Abilitare il database per l'estensione**, è possibile eseguire l'istruzione ALTER TABLE per specificare un predicato dopo aver chiuso la procedura guidata. Prima di applicare un predicato è tuttavia necessario interrompere la migrazione di dati già in corso e ripristinare i dati migrati. (Per altre informazioni sui motivi che lo rendono necessario, vedere [Sostituire un predicato del filtro esistente](#replacePredicate)).

1. Invertire la direzione della migrazione e recuperare i dati di cui è già stata eseguita la migrazione. Non è possibile annullare questa operazione dopo l'avvio. I trasferimenti di dati in uscita comportano anche costi in Azure. Per altre informazioni, vedere [Dettagli prezzi dei trasferimenti di dati](https://azure.microsoft.com/pricing/details/data-transfers/).  

    ```tsql  
    ALTER TABLE <table name>  
         SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = INBOUND ) ) ;   
    ```  

2. Attendere il completamento della migrazione. È possibile controllare lo stato in **Stretch Database Monitor** da SQL Server Management Studio oppure è possibile eseguire una query della vista **sys.dm\_db\_rda\_migration\_status**. Per altre informazioni, vedere [Monitoraggio e risoluzione dei problemi di migrazione dei dati](sql-server-stretch-database-monitor.md) o [sys.dm\_db\_rda\_migration\_status](https://msdn.microsoft.com/library/dn935017.aspx).

3. Creare il predicato del filtro che si desidera applicare alla tabella.

4. Aggiungere il predicato alla tabella e riavviare la migrazione dei dati in Azure.

    ```tsql  
    ALTER TABLE <table name>  
        SET ( REMOTE_DATA_ARCHIVE  
            (           
                FILTER_PREDICATE = <predicate>,  
                MIGRATION_STATE = OUTBOUND  
            )  
        );   
    ```  

## Filtrare le righe per data
Nell'esempio seguente viene eseguita la migrazione delle righe in cui la colonna **date** contiene un valore precedente a 01/01/2016.

```tsql
-- Filter by date
--
CREATE FUNCTION dbo.fn_stretch_by_date(@date datetime2)
RETURNS TABLE
WITH SCHEMABINDING
AS
       RETURN SELECT 1 AS is_eligible WHERE @date < CONVERT(datetime2, '1/1/2016', 101)
GO
```

## Filtrare le righe in base al valore della colonna dello stato
Nell'esempio seguente viene eseguita la migrazione delle righe in cui la colonna **status** contiene uno dei valori specificati.

```tsql
-- Filter by status column
--
CREATE FUNCTION dbo.fn_stretch_by_status(@status nvarchar(128))
RETURNS TABLE
WITH SCHEMABINDING
AS
       RETURN SELECT 1 AS is_eligible WHERE @status IN (N'Completed', N'Returned', N'Cancelled')
GO
```

## Filtrare le righe tramite una finestra temporale scorrevole
Per filtrare le righe usando una finestra temporale scorrevole, tenere presente i requisiti seguenti per la funzione di filtro.

-   La funzione deve essere deterministica. Non è possibile quindi creare una funzione che ricalcola automaticamente la finestra temporale scorrevole col passare del tempo.

-   La funzione usa l'associazione allo schema. Non è quindi possibile limitarsi ad aggiornare ogni giorno la funzione "sul posto" chiamando l'istruzione ALTER FUNCTION per spostare la finestra temporale scorrevole.

Iniziare con un predicato del filtro come nell'esempio seguente, che esegue la migrazione delle righe in cui la colonna **systemEndTime** contiene un valore precedente a 01/01/2016.

```tsql
CREATE FUNCTION dbo.fn_StretchBySystemEndTime20160101(@systemEndTime datetime2)
RETURNS TABLE
WITH SCHEMABINDING  
AS  
RETURN SELECT 1 AS is_eligible
  WHERE @systemEndTime < CONVERT(datetime2, '2016-01-01T00:00:00', 101) ;
```

Applicare il predicato del filtro alla tabella.

```tsql
ALTER TABLE <table name>
SET (
        REMOTE_DATA_ARCHIVE = ON
                (
                        FILTER_PREDICATE = dbo.fn_StretchBySystemEndTime20160101 (SysEndTime)
                                , MIGRATION_STATE = OUTBOUND
                )
        )
;
```

Quando si desidera aggiornare la finestra temporale scorrevole, eseguire le operazioni seguenti.

1.  Creare una nuova funzione che specifica la nuova finestra temporale scorrevole. Nell'esempio seguente vengono selezionate date precedenti al 02/01/2016 anziché al 01/01/2016.

2.  Sostituire il predicato del filtro precedente con quello nuovo chiamando ALTER TABLE, come illustrato nell'esempio seguente.

3. Facoltativamente, eliminare la precedente funzione di filtro non più in uso chiamando l'istruzione DROP FUNCTION. (Questo passaggio non è illustrato nell'esempio).

```tsql
BEGIN TRAN
GO
        /*(1) Create new predicate function definition */
        CREATE FUNCTION dbo.fn_StretchBySystemEndTime20160102(@systemEndTime datetime2)
        RETURNS TABLE
        WITH SCHEMABINDING
        AS
        RETURN SELECT 1 AS is_eligible
               WHERE @systemEndTime < CONVERT(datetime2,'2016-01-02T00:00:00', 101)
        GO

        /*(2) Set the new function as filter predicate */
        ALTER TABLE <table name>
        SET
        (
               REMOTE_DATA_ARCHIVE = ON
               (
                       FILTER_PREDICATE = dbo.fn_StretchBySystemEndTime20160102(SysEndTime),
                       MIGRATION_STATE = OUTBOUND
               )
        )
COMMIT ;
```

## Altri esempi di predicati del filtro validi

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

## Esempi di predicati del filtro non validi

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

## <a name="replacePredicate"></a>Sostituire un predicato del filtro esistente
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

## Rimuovere un predicato del filtro da una tabella
Per eseguire la migrazione dell'intera tabella invece che delle righe selezionate, rimuovere il parametro FILTER\_PREDICATE esistente mediante l'impostazione su Null. Ad esempio:

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
	FILTER_PREDICATE = NULL,
	MIGRATION_STATE = <desired_migration_state>
) )
```
Dopo avere rimosso il predicato del filtro, tutte le righe nella tabella saranno idonee per la migrazione. Di conseguenza, non è possibile specificare un predicato del filtro per la stessa tabella in un secondo momento, a meno che prima non si ripristinino da Azure tutti i dati remoti per la tabella. Questa restrizione esiste per evitare la situazione in cui righe non idonee per la migrazione, quando si specifica un nuovo predicato del filtro, siano già state migrate in Azure.

## Controllare il predicato del filtro applicato a una tabella
Per controllare il predicato del filtro applicato a una tabella, aprire la vista del catalogo **sys.remote\_data\_archive\_tables** e verificare il valore della colonna **filter\_predicate**. Se il valore è Null, l'intera tabella è idonea all'archiviazione. Per altre informazioni, vedere [sys.remote\_data\_archive\_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx).

## Note sulla protezione per i predicati del filtro  
Un account compromesso con privilegi db\_owner può eseguire le operazioni seguenti.

-   Creare e applicare una funzione con valori di tabella che usa grandi quantità di risorse del server o che resta in attesa per un periodo prolungato con conseguente Denial of Service.  

-   Creare e applicare una funzione con valori di tabella che rende possibile dedurre il contenuto di una tabella per la quale all'utente è stato esplicitamente negato l'accesso in lettura.

## Vedere anche

[ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)

<!---HONumber=AcomDC_0615_2016-->