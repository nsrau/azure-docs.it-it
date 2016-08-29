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
   ms.date="08/16/2016"
   ms.author="sonyama;barbkess"/>

# Risoluzione dei problemi relativi a SQL Data Warehouse di Azure

Questo argomento elenca alcune delle domande più comuni per la risoluzione dei problemi che vengono poste dai clienti.Questo argomento elenca alcune delle domande più comuni per la risoluzione dei problemi che vengono poste dai clienti.

## Connessione

| Problema | Risoluzione |
| :----------------------------------| :---------------------------------------------- |
| Errore CTAIP | Questo errore può verificarsi quando è stato creato un account di accesso nel database master di SQL Server, ma non nel database di SQL Data Warehouse. Se si verifica questo errore, vedere l'articolo [Panoramica della sicurezza][]. Questo articolo illustra come creare un account di accesso su master e come creare un utente nel database di SQL Data Warehouse.|
| Blocco da parte del firewall |I database SQL di Azure sono protetti da firewall a livello di server e di database per garantire che solo gli indirizzi IP noti accedano a un database. I firewall sono protetti per impostazione predefinita, il che significa che è necessario abilitare in modo esplicito un indirizzo IP o un intervallo di indirizzi prima di potersi connettere. Per configurare il firewall per l'accesso, attenersi alla procedura riportata in [Configurare l'accesso al firewall del server per l'indirizzo IP del client][] nella [Procedura di configurazione del provisioning][].|
| Impossibile connettersi con lo strumento o il driver | SQL Data Warehouse consiglia di usare [Visual Studio 2013 o 2015][] per eseguire query sui dati. Per la connettività client, si consiglia [SQL Server Native Client 10/11 (ODBC)][].|


## Strumenti

| Problema | Risoluzione |
| :----------------------------------| :---------------------------------------------- |
| Esplora oggetti di Visual Studio non riconosce gli utenti di AAD | Questo è un problema noto. Come soluzione alternativa è possibile visualizzare gli utenti in [sys.database\_principals][]. Per maggiori informazioni sull'uso di Azure Active Directory con SQL Data Warehouse, vedere [Autenticazione in Azure SQL Data Warehouse][].|

## Prestazioni

| Problema | Risoluzione |
| :----------------------------------| :---------------------------------------------- |
| Risoluzione dei problemi di prestazioni delle query | Se si sta cercando di risolvere i problemi relativi a una determinata query, un ottimo punto di partenza è l'articolo su come [imparare a monitorare le query][].|
| Piani e prestazioni delle query di scarsa qualità sono spesso causati dalla mancanza di statistiche | La causa più comune di prestazioni di scarsa qualità è la mancanza di statistiche per le tabelle. Per conoscere i dettagli su come creare statistiche e sul perché sono fondamentali per le prestazioni, vedere [Conservazione delle statistiche delle tabelle][Statistics].|
| Concorrenza bassa/query in coda | Comprendere la [gestione del carico di lavoro][] è importante per capire come bilanciare l'allocazione di memoria con la concorrenza.|
| Come implementare le procedure consigliate | L'articolo [Procedure consigliate per SQL Data Warehouse][] è un ottimo punto di partenza per informazioni su come migliorare le prestazioni delle query.|
| Come migliorare le prestazioni con la scalabilità | La soluzione per aumentare le prestazioni consiste a volte nell'aggiungere semplicemente maggiore potenza di calcolo alle query [ridimensionando SQL Data Warehouse][].|
| Scarse prestazioni delle query a causa di scarsa qualità degli indici | A volte le query possono rallentare a causa della [scarsa qualità degli indici columnstore][]. Vedere questo articolo per ulteriori informazioni e per capire come [ricompilare gli indici per migliorare la qualità dei segmenti][].|

## Gestione del sistema

| Problema | Risoluzione |
| :----------------------------------| :---------------------------------------------- |
| Messaggio 40847: Could not perform the operation because server would exceed the allowed Database Throughput Unit quota of 45000. | Ridurre il [DWU][] del database che si sta tentando di creare oppure [richiedere un aumento della quota][].|
| Analisi dell'uso dello spazio | Per comprendere l'uso dello spazio nel sistema, vedere la [tabella sulle dimensioni][].|
| Aiuto nella gestione delle tabelle | Per aiuto nella gestione delle tabelle, vedere l'articolo [Panoramica delle tabelle][Overview]. Questo articolo comprende anche collegamenti ad argomenti dettagliati come quelli su [tipi di dati delle tabelle][Data types], [distribuzione di una tabella][Distribute], [indicizzazione di una tabella][Index], [partizionamento di una tabella][Partition], [conservazione delle statistiche delle tabelle][Statistics] e [tabelle temporanee][Temporary].|

## PolyBase

| Problema | Risoluzione |
| :----------------------------------| :---------------------------------------------- |
| Errore UTF-8 | Il presente PolyBase supporta solo il caricamento di file di dati che sono stati codificati con UTF-8. Per informazioni su come risolvere questa limitazione, vedere [Risolvere il requisito PolyBase UTF-8][].|
| Caricamento non riuscito a causa di un elevato numero di righe | Attualmente PolyBase non supporta un elevato numero di righe. Ciò significa che se la tabella contiene VARCHAR(MAX), NVARCHAR(MAX) o VARBINARY(MAX), le tabelle esterne non possono essere usate per caricare i dati. Il caricamento di un elevato numero di righe è attualmente supportato solo attraverso Data Factory di Azure (con BCP), analisi di flusso di Azure, SSIS, BCP o la classe .NET SQLBulkCopy. Il supporto di un elevato numero di righe in PolyBase verrà aggiunto in una versione futura.|
| Esito negativo del caricamento bcp della tabella con tipo di dati MAX | Esiste un problema noto che richiede che venga inserito VARCHAR(MAX), NVARCHAR(MAX) o VARBINARY(MAX) alla fine della tabella in alcuni scenari. Provare a spostare le colonne MAX alla fine della tabella.|

## Differenze rispetto al database SQL

| Problema | Risoluzione |
| :----------------------------------| :---------------------------------------------- |
| Funzionalità non supportate del database SQL | Vedere [Funzionalità non supportate delle tabelle][].|
| Tipi di dati non supportati del database SQL | Vedere [Tipi di dati non supportati][].|
| Limitazioni DELETE e UPDATE | Vedere [Soluzioni alternative UPDATE][], [Soluzioni alternative DELETE][] e [Uso di CTAS per ovviare a sintassi UPDATE e DELETE non supportata][]. |
| Istruzione MERGE non supportata | Vedere [Soluzioni alternative MERGE][].|
| Limitazioni delle stored procedure | Per capire alcune limitazioni delle stored procedure, vedere [Limitazioni delle stored procedure][].|
| Le UDF non supportano istruzioni SELECT | Si tratta di una limitazione corrente delle UDF. Per conoscere la sintassi supportata, vedere [CREATE FUNCTION][]. |
'<--LocComment: Page not found "Stored procedure limitations" is broken. I've tried fixing the link in Article References -->'

## Passaggi successivi

Se non si riesce a trovare una soluzione al problema precedente, ecco alcune altre risorse che è possibile provare.

- [Blog]
- [Richieste di funzionalità]
- [Video]
- [Blog del team CAT]
- [Creare un ticket di supporto]
- [Forum MSDN]
- [Forum su Stack Overflow]
- [Twitter]

<!--Image references-->

<!--Article references-->
[Panoramica della sicurezza]: ./sql-data-warehouse-overview-manage-security.md
[Creare un ticket di supporto]: ./sql-data-warehouse-get-started-create-support-ticket.md
[ridimensionando SQL Data Warehouse]: ./sql-data-warehouse-manage-compute-overview.md
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[richiedere un aumento della quota]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change
[imparare a monitorare le query]: ./sql-data-warehouse-manage-monitor.md
[Procedura di configurazione del provisioning]: ./sql-data-warehouse-get-started-provision.md
[Configurare l'accesso al firewall del server per l'indirizzo IP del client]: ./sql-data-warehouse-get-started-provision.md#create-a-new-azure-sql-server-level-firewall
[Visual Studio 2013 o 2015]: ./sql-data-warehouse-query-visual-studio.md
[Procedure consigliate per SQL Data Warehouse]: ./sql-data-warehouse-best-practices.md
[tabella sulle dimensioni]: ./sql-data-warehouse-tables-overview.md#table-size-queries
[Funzionalità non supportate delle tabelle]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Tipi di dati non supportati]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[scarsa qualità degli indici columnstore]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[ricompilare gli indici per migliorare la qualità dei segmenti]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[gestione del carico di lavoro]: ./sql-data-warehouse-develop-concurrency.md
[Uso di CTAS per ovviare a sintassi UPDATE e DELETE non supportata]: ./sql-data-warehouse-develop-ctas.md#using-ctas-to-work-around-unsupported-features
[Soluzioni alternative UPDATE]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[Soluzioni alternative DELETE]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[Soluzioni alternative MERGE]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[Limitazioni delle stored procedure]: /sql-data-warehouse-develop-stored-procedures.md#limitations
[Autenticazione in Azure SQL Data Warehouse]: ./sql-data-warehouse-authentication.md
[Risolvere il requisito PolyBase UTF-8]: ./sql-data-warehouse-load-polybase-guide.md#working-around-the-polybase-utf-8-requirement

<!--MSDN references-->
[SQL Server Native Client 10/11 (ODBC)]: https://msdn.microsoft.com/library/ms131415.aspx
[sys.database\_principals]: https://msdn.microsoft.com/library/ms187328.aspx
[CREATE FUNCTION]: https://msdn.microsoft.com/library/mt203952.aspx

<!--Other Web references-->  
[Blog]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blog del team CAT]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Richieste di funzionalità]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Forum MSDN]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse
[Forum su Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Video]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse

<!---HONumber=AcomDC_0817_2016-->