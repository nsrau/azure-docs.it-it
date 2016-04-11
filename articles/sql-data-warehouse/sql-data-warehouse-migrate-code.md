<properties
   pageTitle="Eseguire la migrazione del codice SQL in SQL Data Warehouse | Microsoft Azure"
   description="Suggerimenti per la migrazione del codice SQL in Azure SQL Data Warehouse per lo sviluppo di soluzioni."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/23/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Eseguire la migrazione del codice SQL in SQL Data Warehouse

Per garantire che il codice sia compatibile con SQL Data Warehouse, è molto probabile che debbano essere apportate modifiche alla base di codice. Alcune funzionalità di SQL Data Warehouse possono migliorare in modo significativo le prestazioni perché sono progettate per funzionare direttamente in modalità distribuita. Per mantenere tuttavia le prestazioni e la scalabilità, alcune funzionalità non sono disponibili.

## Modifiche del codice Transact-SQL

L'elenco seguente riepiloga le funzionalità principali non supportate in Azure SQL Data Warehouse: I collegamenti consentono di realizzare soluzioni alternative per la caratteristica non supportata:

- [Join ANSI sugli aggiornamenti][]
- [Join ANSI sulle eliminazioni][]
- [Istruzione merge][]
- Join tra database
- [Cursori][]
- [SELECT..INTO][]
- [INSERT..EXEC][]
- Clausola di output
- Funzioni inline definite dall'utente
- Funzioni a istruzioni multiple
- [espressioni di tabella comune](#Common-table-expressions)
- [espressioni tabella comune ricorsive (CTE)] (#Recursive-common-table-expressions-(CTE)
- Funzioni e procedure CLR
- Funzione $partition
- Variabili di tabella
- Parametri di valori di tabella
- Transazioni distribuite
- Commit / rollback
- Transazione di salvataggio
- Contesti di esecuzione (EXECUTE AS)
- [Raggruppamento per clausola con opzioni di rollup/cubo/set di raggruppamento][]
- [Livelli di annidamento superiori a 8][]
- [Aggiornamento tramite visualizzazioni][]
- [Uso di select per l'assegnazione di variabili][]
- [Nessun tipo di dati MAX per stringhe SQL dinamiche][]

Fortunatamente è possibile ovviare alla maggior parte di queste limitazioni. Le spiegazioni sono fornite dagli articoli pertinenti a cui viene fatto riferimento più indietro.

### Espressioni di tabella comune
L'implementazione corrente delle espressioni di tabella comune (CTE) all'interno di SQL Data Warehouse presenta le funzionalità e le limitazioni seguenti:

**Funzionalità CTE**
+ Un'espressione CTE può essere specificata in un'istruzione SELECT.
+ Un'espressione CTE può essere specificata in un'istruzione CREATE VIEW.
+ Un'espressione CTE può essere specificata in un'istruzione CREATE TABLE AS SELECT (CTAS).
+ Un'espressione CTE può essere specificata in un'istruzione CREATE REMOTE TABLE AS SELECT (CRTAS).
+ Un'espressione CTE può essere specificata in un'istruzione CREATE EXTERNAL TABLE AS SELECT (CETAS).
+ È possibile fare riferimento a una tabella remota da un'espressione CTE.
+ È possibile fare riferimento a una tabella esterna da un'espressione CTE.
+ In un'espressione CTE possono essere definite più definizioni di query CTE.

**Limitazioni CTE**
+ Un'espressione CTE deve essere seguita da una singola istruzione SELECT. Le istruzioni INSERT, UPDATE, DELETE e MERGE non sono supportate.
+ Un'espressione di tabella comune che include riferimenti a se stessa (un'espressione di tabella comune ricorsiva) non è supportata (vedere la sezione sotto).
+ Non è consentito specificare più di una clausola WITH in un'espressione CTE. Se, ad esempio, CTE\_query\_definition contiene una sottoquery, tale sottoquery non può contenere una clausola WITH annidata che definisce un'altra CTE.
+ Una clausola ORDER BY non può essere usata in CTE\_query\_definition, tranne quando viene specificata una clausola TOP.
+ Quando un'espressione CTE viene usata in un'istruzione che fa parte di un batch, l'istruzione precedente deve essere seguita da un punto e virgola.
+ Quando vengono usate in istruzioni preparate da sp\_prepare, le espressioni CTE si comporteranno esattamente come le altre istruzioni SELECT in PDW. Tuttavia, se le CTE vengono usate come parte di istruzioni CETAS preparate da sp\_prepare, il comportamento può variare rispetto a SQL Server e altre istruzioni PDW per la modalità di implementazione del binding per sp\_prepare. Se l'istruzione SELECT che fa riferimento alla CTE usa una colonna non corretta che non esiste nella CTE, sp\_prepare passa senza rilevare l'errore, che invece viene generato durante sp\_execute.

### Espressioni tabella comune ricorsive

Si tratta di uno scenario di migrazione complesso e il modo migliore di procedere è suddividere l'espressione CTE e gestire i singoli passaggi. In genere è possibile usare un ciclo e popolare una tabella temporanea mentre si scorrono le query provvisorie ricorsive. Una volta che viene popolata la tabella temporanea, è quindi possibile restituire i dati come un unico set di risultati. Un approccio simile è stato usato per risolvere `GROUP BY WITH CUBE` nell'articolo relativo al [raggruppamento per clausola con opzioni di rollup/cubo/set di raggruppamento][].

### Funzioni di sistema

Anche alcune funzioni di sistema non sono supportate. Alcune di queste funzioni principali che in genere vengono usate nel data warehousing sono:

- NEWID()
- NEWSEQUENTIALID()
- @@NESTLEVEL()
- @@IDENTITY()
- @@ROWCOUNT()
- ROWCOUNT\_BIG
- ERROR\_LINE()

Anche per questi problemi è possibile trovare una soluzione.

Il codice seguente ad esempio è una soluzione alternativa per recuperare informazioni @@ROWCOUNT:

```sql
SELECT  SUM(row_count) AS row_count
FROM    sys.dm_pdw_sql_requests
WHERE   row_count <> -1
AND     request_id IN
                    (   SELECT TOP 1    request_id
                        FROM            sys.dm_pdw_exec_requests
                        WHERE           session_id = SESSION_ID()
                        ORDER BY end_time DESC
                    )
;
```

## Passaggi successivi
Per suggerimenti sullo sviluppo di codice, vedere la [panoramica dello sviluppo][].

<!--Image references-->

<!--Article references-->
[Join ANSI sugli aggiornamenti]: sql-data-warehouse-develop-ctas.md
[Join ANSI sulle eliminazioni]: sql-data-warehouse-develop-ctas.md
[Istruzione merge]: sql-data-warehouse-develop-ctas.md
[INSERT..EXEC]: sql-data-warehouse-develop-temporary-tables.md

[Cursori]: sql-data-warehouse-develop-loops.md
[SELECT..INTO]: sql-data-warehouse-develop-ctas.md
[Raggruppamento per clausola con opzioni di rollup/cubo/set di raggruppamento]: sql-data-warehouse-develop-group-by-options.md
[Livelli di annidamento superiori a 8]: sql-data-warehouse-develop-transactions.md
[Aggiornamento tramite visualizzazioni]: sql-data-warehouse-develop-views.md
[Uso di select per l'assegnazione di variabili]: sql-data-warehouse-develop-variable-assignment.md
[Nessun tipo di dati MAX per stringhe SQL dinamiche]: sql-data-warehouse-develop-dynamic-sql.md
[panoramica dello sviluppo]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0330_2016-->