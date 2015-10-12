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
   ms.date="09/22/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

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
- [espressioni tabella comune ricorsive (CTE)] (#Recursive-common-table-expressions-(CTE)
- [Aggiornamenti tramite espressioni tabella comune](#Updates-through-CTEs)
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

### Espressioni tabella comune ricorsive

Si tratta di uno scenario complesso senza correzioni rapide. Le espressioni tabella comune ricorsive dovranno essere suddivise e gestite nei passaggi. In genere è possibile utilizzare un ciclo piuttosto complesso; popolamento di una tabella temporanea mentre si scorrono le query provvisorie ricorsive. Una volta che viene popolata la tabella temporanea, è quindi possibile restituire i dati come un unico set di risultati. Un approccio simile è stato utilizzato per risolvere `GROUP BY WITH CUBE` nell'articolo [Raggruppamento per clausola con opzioni di rollup/cubo/set di raggruppamento][].

### Aggiornamenti tramite espressioni tabella comune

Se le espressioni tabella comune non sono ricorsive, è possibile scrivere nuovamente la query per l'utilizzo di sottoquery. Per le espressioni tabella comune ricorsive è necessario creare il set di risultati prima come descritto sopra, quindi aggiungere il set di risultati finale per la tabella di destinazione ed eseguire l'aggiornamento.

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

```
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

<!---HONumber=Oct15_HO1-->