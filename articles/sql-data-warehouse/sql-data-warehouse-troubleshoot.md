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
   ms.date="04/20/2016"
   ms.author="mausher;sonyama;barbkess"/>

# Risoluzione dei problemi
Il seguente argomento fornisce un elenco di alcuni dei problemi più comuni che i clienti riscontrano con SQL Data Warehouse di Azure.

## Connettività
La connessione all’ SQL Data Warehouse di Azure può avere esito negativo per due motivi:

- Le regole del firewall non sono impostate
- Si utilizzano strumenti/protocolli non supportati

### Regole del firewall
I database SQL di Azure sono protetti da firewall a livello di server e di database per garantire che solo gli indirizzi IP noti possano accedere ai database. I firewall sono sicuri per impostazione predefinita, pertanto che è necessario consentire l'accesso al proprio indirizzo IP prima di potersi connettere.

Per configurare il firewall per l'accesso, attenersi alla procedura riportata nella sezione [Configurare l’accesso al firewall del server per l'indirizzo IP del client][] della pagina [Provisioning][].

### Si utilizzano strumenti/protocolli non supportati
SQL Data Warehouse supporta gli ambienti di sviluppo [Visual Studio 2013/2015][] e la connettività client [SQL Server Native Client 10/11 (ODBC)][].

Vedere le pagine [Connetti][] per altre informazioni..

## Prestazioni di query

### Statistiche

Le [statistiche][] sono oggetti che includono informazioni sull'intervallo e sulla frequenza di valori in una colonna di database. Il motore di query usa queste statistiche per ottimizzare l'esecuzione e migliorare le prestazioni delle query. A differenza di SQL Server o SQL DB, SQL Data Warehouse non crea o aggiorna automaticamente le statistiche. Le statistiche vanno gestite manualmente in tutte le tabelle.

È possibile usare la query seguente per determinare l'ultimo aggiornamento delle statistiche di ogni tabella.

```sql
SELECT
    sm.[name] AS [schema_name],
    tb.[name] AS [table_name],
    co.[name] AS [stats_column_name],
    st.[name] AS [stats_name],
    STATS_DATE(st.[object_id],st.[stats_id]) AS [stats_last_updated_date]
FROM
    sys.objects ob
    JOIN sys.stats st
        ON  ob.[object_id] = st.[object_id]
    JOIN sys.stats_columns sc    
        ON  st.[stats_id] = sc.[stats_id]
        AND st.[object_id] = sc.[object_id]
    JOIN sys.columns co    
        ON  sc.[column_id] = co.[column_id]
        AND sc.[object_id] = co.[object_id]
    JOIN sys.types  ty    
        ON  co.[user_type_id] = ty.[user_type_id]
    JOIN sys.tables tb    
        ON  co.[object_id] = tb.[object_id]
    JOIN sys.schemas sm    
        ON  tb.[schema_id] = sm.[schema_id]
WHERE
    st.[user_created] = 1;
```

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
    AVG(CASE WHEN rg.State = 3 THEN rg.Total_rows ELSE NULL END) < 100000
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


### Principali concetti relativi alle prestazioni

Per comprendere più facilmente alcuni ulteriori concetti importanti sulle prestazioni e la scalabilità, fare riferimento agli articoli seguenti:

- [Prestazioni e scalabilità][]
- [Modello di concorrenza][]
- [Progettazione di tabelle][]
- [Scegliere una chiave di distribuzione hash per la tabella][]

## Passaggi successivi
Per alcune indicazioni sulla creazione della soluzione SQL Data Warehouse, vedere l'articolo di [panoramica sullo sviluppo][].

<!--Image references-->

<!--Article references-->
[Prestazioni e scalabilità]: sql-data-warehouse-performance-scale.md
[Modello di concorrenza]: sql-data-warehouse-develop-concurrency.md
[Progettazione di tabelle]: sql-data-warehouse-develop-table-design.md
[Scegliere una chiave di distribuzione hash per la tabella]: sql-data-warehouse-develop-hash-distribution-key
[panoramica sullo sviluppo]: sql-data-warehouse-overview-develop.md
[Provisioning]: sql-data-warehouse-get-started-provision.md
[Configurare l’accesso al firewall del server per l'indirizzo IP del client]: sql-data-warehouse-get-started-provision.md/#step-4-configure-server-firewall-access-for-your-client-ip
[Visual Studio 2013/2015]: sql-data-warehouse-get-started-connect.md
[Connetti]: sql-data-warehouse-get-started-connect.md
[statistiche]: sql-data-warehouse-develop-statistics.md

<!--MSDN references-->
[SQL Server Native Client 10/11 (ODBC)]: https://msdn.microsoft.com/library/ms131415.aspx

<!--Other web references-->

<!---HONumber=AcomDC_0427_2016-->