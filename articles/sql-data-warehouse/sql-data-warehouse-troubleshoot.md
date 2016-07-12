<properties
   pageTitle="Risoluzione dei problemi relativi a SQL Data Warehouse di Azure | Microsoft Azure"
   description="Risoluzione dei problemi relativi a SQL Data Warehouse di Azure."
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
   ms.date="06/28/2016"
   ms.author="sonyama;barbkess"/>

# Risoluzione dei problemi relativi a SQL Data Warehouse di Azure
Questo argomento fornisce un elenco di alcuni dei problemi più comuni che gli utenti riscontrano con SQL Data Warehouse di Azure.


##Errori di connessione

Se si riscontrano problemi di connessione, di seguito sono riportati alcuni dei problemi più comuni segnalati dai clienti.

### Errore CTAIP
Questo errore può verificarsi quando è stato creato un account di accesso nel database master di SQL Server, ma non nel database di SQL Data Warehouse. Se si verifica questo errore, vedere l'articolo [Panoramica della sicurezza][]. Questo articolo illustra come creare un account di accesso su master e come creare un utente nel database di SQL Data Warehouse.

### Regole del firewall
I database SQL di Azure sono protetti da firewall a livello di server e di database per garantire che solo gli indirizzi IP noti accedano a un database. I firewall sono protetti per impostazione predefinita, il che significa che è necessario abilitare in modo esplicito un indirizzo IP o un intervallo di indirizzi prima di potersi connettere. Per configurare il firewall per l'accesso, attenersi alla procedura riportata in [Configurare l'accesso al firewall del server per l'indirizzo IP del client][] nella [Procedura di configurazione del provisioning][].

### Strumenti/protocolli non supportati
SQL Data Warehouse consiglia di usare [Visual Studio 2013 o 2015][] per eseguire query sui dati. Per la connettività client, si consigliano [SQL Server Native Client 10/11 (ODBC)][]. SQL Server Management Studio (SSMS) non è ancora supportato e laddove funziona in modo parziale, l'albero Esplora oggetti non funziona con SQL Data Warehouse e la query può funzionare dopo aver ignorato alcuni messaggi di errore.


## Problemi di prestazioni

L'articolo [Procedure consigliate per SQL Data Warehouse][] è un ottimo punto di partenza per informazioni su come migliorare le prestazioni delle query. Se si sta cercando di risolvere i problemi relativi a una determinata query, un ottimo punto di partenza è l'articolo su come [imparare a monitorare le query][]. La soluzione per eseguire una query più velocemente consiste a volte nell'aggiungere semplicemente maggiore potenza di calcolo alle query [ridimensionando SQL Data Warehouse][].

## Qualità del segmento columnstore cluster

La qualità del segmento columnstore cluster è importante per prestazioni ottimali delle query sulle tabelle columnstore cluster. La qualità del segmento può essere misurata in base al numero di righe in un gruppo di righe compresso. La query seguente identifica le tabelle con qualità del segmento indice columnstore insufficiente e genera codice T-SQL per ricreare l'indice columnstore in tali tabelle. La prima colonna del risultato della query include il codice T-SQL per ricreare ogni indice. La seconda colonna suggerisce la classe di risorse minima da usare per ottimizzare la compressione.
 
**Passaggio 1:** eseguire la query su tutti i database SQL Data Warehouse per identificare gli indici columnstore cluster non ottimali. Se viene restituita alcuna riga, non sono necessarie altre azioni.

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

> [AZURE.NOTE]  LoadUser deve corrispondere a un utente valido creato per l'esecuzione dell'istruzione ALTER INDEX. La classe di risorse dell'utente db\_owner non può essere modificata. Altre informazioni sulle classi di risorse e sulla creazione di un nuovo utente sono disponibili nel collegamento riportato di seguito.

 
**Passaggio 3:** accedere con le credenziali dell'utente indicato nel passaggio 2 (ad esempio "LoadUser"), che ora usa una classe di risorse superiore, quindi eseguire le istruzioni ALTER INDEX generate dalla query nel Passaggio 1. Assicurarsi che l'utente disponga dell'autorizzazione ALTER per le tabelle identificate nella query del passaggio 1.
 
**Passaggio 4:** rieseguire la query dal passaggio 1. Se gli indici sono stati compilati in modo efficiente, questa query non restituisce alcuna riga. Se non viene restituita alcuna riga, la procedura è terminata. Se sono presenti più database SQL DW, è necessario ripetere questa procedura su ogni database. Se vengono restituite righe, procedere con il passaggio 5.
 
**Passaggio 5:** se quando si esegue nuovamente la query del Passaggio 1 vengono restituite alcune righe, potrebbero essere presenti tabelle con righe molto lunghe, che richiedono una quantità di memoria elevata per la creazione ottimale degli indici columnstore cluster. In tal caso, ripetere il processo per queste tabelle usando la classe xlargerc. Per modificare la classe di risorse, ripetere il passaggio 2 usando xlargerc. Quindi ripetere il passaggio 3 per le tabelle che continuano a restituire indici non ottimali. Se si usa l'impostazione DW100 - DW300 e si sta già usando xlargerc, è possibile scegliere di lasciare gli indici nello stato corrente oppure di incrementare temporaneamente il numero di DWU per rendere disponibile più memoria per questa operazione.
 
**Passaggi finali:** la classe di risorse sopra indicata è la classe di risorse minima consigliata per la creazione di indici columnstore di qualità elevata. È consigliabile mantenere questa impostazione per l'utente che carica i dati. Tuttavia, se si vuole annullare la modifica del passaggio 2, è possibile farlo con il comando seguente.

```sql
EXEC sp_droprolemember 'smallrc', 'LoadUser'
```

Le linee guida per la classe di risorse minima per i caricamenti in una tabella CCI prevedono l'uso di xlargerc per DW100-DW300, largerc per DW400-DW600 e mediumrc per DW1000 o valori superiori. Queste indicazioni sono consigliabili per la maggior parte dei carichi di lavoro. L'obiettivo è garantire a ogni operazione di compilazione dell'indice una quantità di memoria pari o superiore a 400 MB. Tuttavia le linee guida non sono valide per tutte le situazioni. La memoria necessaria per ottimizzare un indice columnstore dipende dai dati caricati e in particolare dalle dimensioni delle righe. Le tabelle con righe più strette richiedono meno memoria, mentre le tabelle con righe più larghe richiedono più memoria. Per fare delle prove, è possibile usare la query del passaggio 1 e verificare se si ottengono indici columnstore ottimali con allocazioni di memoria inferiori. A livello minimo, ogni gruppo di righe deve contenere in media oltre 100.000 righe. Un valore superiore a 500.000 è ancora migliore. Il valore massimo è pari a 1 milione di righe per gruppo. Per informazioni dettagliate sulla gestione delle classi e della concorrenza delle risorse, vedere il collegamento in basso.


## Passaggi successivi

Se non si riesce a trovare una soluzione al problema precedente, ecco alcune altre risorse che è possibile provare.

- [Blog]
- [Richieste di funzionalità]
- [Video]
- [Blog del team CAT]
- [Creare un ticket di supporto]
- [Forum di MSDN]
- [Forum su Stack Overflow]
- [Twitter]

<!--Image references-->

<!--Article references-->
[Panoramica della sicurezza]: ./sql-data-warehouse-overview-manage-security.md
[Creare un ticket di supporto]: ./sql-data-warehouse-get-started-create-support-ticket.md
[ridimensionando SQL Data Warehouse]: ./sql-data-warehouse-manage-compute-overview.md
[imparare a monitorare le query]: ./sql-data-warehouse-manage-monitor.md
[Procedura di configurazione del provisioning]: ./sql-data-warehouse-get-started-provision.md
[Configurare l'accesso al firewall del server per l'indirizzo IP del client]: ./sql-data-warehouse-get-started-provision.md#create-a-new-azure-sql-server-level-firewall
[Visual Studio 2013 o 2015]: ./sql-data-warehouse-get-started-connect.md
[Procedure consigliate per SQL Data Warehouse]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->
[SQL Server Native Client 10/11 (ODBC)]: https://msdn.microsoft.com/library/ms131415.aspx

<!--Other Web references-->
[Blog]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blog del team CAT]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Richieste di funzionalità]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Forum di MSDN]: https://social.msdn.microsoft.com/Forums/it-IT/home?forum=AzureSQLDataWarehouse
[Forum su Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Video]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse

<!---HONumber=AcomDC_0629_2016-->