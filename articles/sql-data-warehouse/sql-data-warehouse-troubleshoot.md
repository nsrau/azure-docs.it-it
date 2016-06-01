<properties
   pageTitle="Risoluzione dei problemi | Microsoft Azure"
   description="Risoluzione dei problemi relativi a SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/15/2016"
   ms.author="mausher;sonyama;barbkess"/>

# Risoluzione dei problemi
Il seguente argomento fornisce un elenco di alcuni dei problemi più comuni che i clienti riscontrano con SQL Data Warehouse di Azure.

## Connettività
I problemi di connettività più comuni comprendono:

- Le regole del firewall non sono impostate
- Si utilizzano strumenti/protocolli non supportati

### Regole del firewall
I database SQL di Azure sono protetti da firewall a livello di server e di database per garantire che solo gli indirizzi IP noti accedano a un database. I firewall sono protetti per impostazione predefinita, il che significa che è necessario abilitare in modo esplicito un indirizzo IP o un intervallo di indirizzi prima di potersi connettere. Per configurare il firewall per l'accesso, attenersi alla procedura riportata in [Configurare l'accesso al firewall del server per l'indirizzo IP del client][] nella [Procedura di configurazione del provisioning][].

### Si utilizzano strumenti/protocolli non supportati
SQL Data Warehouse consiglia di usare [Visual Studio 2013 o 2015][] per eseguire query sui dati. Per la connettività client, si consigliano [SQL Server Native Client 10/11 (ODBC)][]. SQL Server Management Studio (SSMS) non è ancora supportato e laddove funziona in modo parziale, l'albero Esplora oggetti non funziona con SQL Data Warehouse e la query può funzionare dopo aver ignorato alcuni messaggi di errore.

## Prestazioni di query

Esistono diverse operazioni semplici che è possibile eseguire nella progettazione del database per assicurarsi di ottenere prestazioni ottimali delle query da SQL Data Warehouse. Si può iniziare a capire la qualità delle prestazioni delle query leggendo l'articolo su come [imparare a monitorare le query][]. A volte la soluzione per far sì che si esegua una query più velocemente è quella di aggiungere semplicemente una maggiore potenza di calcolo alle query [ridimensionando SQL Datawarehouse][]. Per trovare un maggior numero di queste ottimizzazioni in un'unica posizione, consultare l'articolo [Procedure consigliate per SQL Data Warehouse][].

Di seguito sono riportate alcune delle cause più comuni dei problemi riscontrati nelle prestazioni delle query.

### Statistiche

Le [statistiche][] sulle tabelle includono informazioni sull'intervallo e sulla frequenza di valori in una colonna di database o in una combinazione di colonne. Il motore di query usa queste statistiche per ottimizzare l'esecuzione e migliorare le prestazioni delle query. A differenza di SQL Server o SQL DB, SQL Data Warehouse non crea o aggiorna automaticamente le statistiche. Le statistiche vanno gestite manualmente in tutte le tabelle. Per informazioni su come gestire le statistiche e identificare le tabelle che richiedono le statistiche, consultare l'articolo [Gestire le statistiche in SQL Data Warehouse][].

### Progettazione della tabella

Una delle scelte più importanti da fare in SQL Data Warehouse è [la scelta della chiave di distribuzione hash giusta per la tabella][] e la [progettazione della tabella][]. Quando ci si sposta dal riquadro di introduzione a quello per ottenere prestazioni più veloci da SQL Data Warehouse, assicurarsi di capire i concetti illustrati in questi articoli.

### Qualità del segmento columnstore cluster

La qualità del segmento columnstore cluster è importante per prestazioni ottimali delle query sulle tabelle columnstore cluster. La qualità del segmento può essere misurata in base al numero di righe in un gruppo di righe compresso. La query seguente identifica le tabelle con qualità del segmento indice columnstore insufficiente e genera codice T-SQL per ricreare l'indice columnstore in tali tabelle. La prima colonna del risultato della query include il codice T-SQL per ricreare ogni indice. La seconda colonna suggerisce la classe di risorse minima da usare per ottimizzare la compressione.
 
**Passaggio 1:** eseguire la query su tutti i database SQL Data Warehouse per identificare gli indici columnstore cluster non ottimali. Se non viene restituita nessuna riga, non è necessario eseguire altre operazioni.

```sql
SELECT 
     'ALTER INDEX ALL ON ' + s.name + '.' + t.NAME + ' REBUILD;' AS [T-SQL to Rebuild Index]
    ,CASE WHEN n.nbr_nodes < 3 THEN 'xlargerc' WHEN n.nbr_nodes BETWEEN 4 AND 6 THEN 'largerc' ELSE 'mediumrc' END AS [Resource Class Recommendation]
    ,s.name AS [Schema Name]
    ,t.name AS [Table Name]
    ,AVG(CASE WHEN rg.State = 3 THEN rg.Total_rows ELSE NULL END) AS [Ave Rows in Compressed Row Groups]
FROM 
    sys.pdw_nodes_column_store_row_groups rg
    JOIN sys.pdw_nodes_tables pt 
        ON rg.object_id = pt.object_id AND rg.pdw_node_id = pt.pdw_node_id AND pt.distribution_id = rg.distribution_id
    JOIN sys.pdw_table_mappings tm 
        ON pt.name = tm.physical_name
    INNER JOIN sys.tables t 
        ON tm.object_id = t.object_id
INNER JOIN sys.schemas s
    ON t.schema_id = s.schema_id
CROSS JOIN (SELECT COUNT(*) nbr_nodes  FROM sys.dm_pdw_nodes WHERE type = 'compute') n
GROUP BY 
    n.nbr_nodes, s.name, t.name
HAVING 
    AVG(CASE WHEN rg.State = 3 THEN rg.Total_rows ELSE NULL END) < 100000 OR
    AVG(CASE WHEN rg.State = 0 THEN rg.Total_rows ELSE NULL END) < 100000

ORDER BY 
    s.name, t.name
```
 
**Passaggio 2:** incrementare la classe risorse di un utente con autorizzazioni per la ricostruzione dell'indice in questa tabella alla classe di risorse consigliata nella seconda colonna della query precedente.

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

> [AZURE.NOTE]  LoadUser deve corrispondere a un utente valido creato per l'esecuzione dell'istruzione ALTER INDEX. La classe risorse dell'utente db\_owner non può essere modificata. Altre informazioni sulle classi di risorse e sulla creazione di un nuovo utente sono disponibili nel collegamento riportato di seguito.

 
**Passaggio 3:** accedere con le credenziali dell'utente indicato nel passaggio 2 (ad esempio "LoadUser"), che ora usa una classe di risorse superiore, quindi eseguire le istruzioni ALTER INDEX generate dalla query nel Passaggio 1. Assicurarsi che l'utente disponga dell'autorizzazione ALTER per le tabelle identificate nella query del Passaggio 1.
 
**Passaggio 4:** rieseguire la query dal passaggio 1. Se gli indici sono stati compilati in modo efficiente, questa query non restituisce alcuna riga. Se non viene restituita alcuna riga, la procedura è terminata. Se si dispone di più database SQL DW, è necessario ripetere questa procedura su ogni database. Se vengono restituite delle righe, procedere con il Passaggio 5.
 
**Passaggio 5:** se quando si esegue nuovamente la query del Passaggio 1 vengono restituite delle righe, potrebbero essere presenti tabelle con righe molto lunghe, che richiedono una quantità di memoria elevata per la creazione ottimale degli indici columnstore cluster. In tal caso, ripetere il processo per queste tabelle usando la classe xlargerc. Per modificare la classe di risorse, ripetere il passaggio 2 usando xlargerc. Quindi ripeter il Passaggio 3 per le tabelle che continuano a restituire indici non ottimali. Se si usa l'impostazione DW100 - DW300 e si sta già usando xlargerc, è possibile scegliere di lasciare gli indici nello stato corrente oppure di incrementare temporanemente il numero di DWU per rendere disponibile più memoria per questa operazione.
 
**Passaggi finali:** la classe di risorse sopra indicata è la classe di risorse minima consigliata per la creazione di indici columnstore di qualità elevata. È consigliabile mantenere questa impostazione per l'utente che carica i dati. Tuttavia, se si vuole annullare la modifica del passaggio 2, è possibile farlo con il comando seguente.

```sql
EXEC sp_droprolemember 'smallrc', 'LoadUser'
```

Le linee guida per la classe di risorse minima per i caricamenti in una tabella CCI prevedono l'uso di xlargerc per DW100-DW300, largerc per DW400-DW600 e mediumrc per DW1000 o valori superiori. Queste indicazioni sono consigliabili per la maggior parte dei carichi di lavoro. L'obiettivo è quello di garantire a ogni operazione di compilazione dell'indice una quantità di memoria pari o superiore a 400 MB. Tuttavia le linee guida non sono valide per tutte le situazioni. La memoria necessaria per ottimizzare un indice columnstore dipende dai dati caricati e in particolare dalle dimensioni delle righe. Le tabelle con righe più strette richiedono meno memoria, mentre le tabelle con righe più larghe richiedono più memoria. Per fare delle prove, è possibile usare la query del passaggio 1 e verificare se si ottengono indici columnstore ottimali con allocazioni di memoria inferiori. A livello minimo, ogni gruppo di righe deve contenere in media oltre 100.000 righe. Un valore superiore a 500.000 è ancora migliore. Il valore massimo è pari a 1 milione di righe per gruppo. Per informazioni dettagliate sulla gestione delle classi e della concorrenza delle risorse, vedere il collegamento in basso.


## Passaggi successivi
Consultare l'articolo sulle [Procedure consigliate per SQL Data Warehouse][] per altre informazioni su come ottimizzare la soluzione SQL Data Warehouse.

<!--Image references-->

<!--Article references-->
[ridimensionando SQL Datawarehouse]: ./sql-data-warehouse-overview-scalability.md
[progettazione della tabella]: ./sql-data-warehouse-develop-table-design.md
[la scelta della chiave di distribuzione hash giusta per la tabella]: ./sql-data-warehouse-develop-hash-distribution-key
[development overview]: ./sql-data-warehouse-overview-develop.md
[imparare a monitorare le query]: ./sql-data-warehouse-manage-monitor.md
[Gestire le statistiche in SQL Data Warehouse]: ./sql-data-warehouse-develop-statistics.md
[Procedura di configurazione del provisioning]: ./sql-data-warehouse-get-started-provision.md
[Configurare l'accesso al firewall del server per l'indirizzo IP del client]: ./sql-data-warehouse-get-started-provision.md/#create-a-new-azure-sql-server-level-firewall
[Visual Studio 2013 o 2015]: ./sql-data-warehouse-get-started-connect.md
[Procedure consigliate per SQL Data Warehouse]: ./sql-data-warehouse-best-practices.md
[statistiche]: ./sql-data-warehouse-develop-statistics.md

<!--MSDN references-->
[SQL Server Native Client 10/11 (ODBC)]: https://msdn.microsoft.com/library/ms131415.aspx

<!--Other web references-->

<!---HONumber=AcomDC_0518_2016-->