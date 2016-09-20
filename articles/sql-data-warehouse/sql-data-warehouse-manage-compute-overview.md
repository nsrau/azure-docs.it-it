<properties
   pageTitle="Gestire la potenza di calcolo in Azure SQL Data Warehouse (Panoramica) | Microsoft Azure"
   description="Funzionalità relative alla scalabilità orizzontale delle prestazioni in Azure SQL Data Warehouse. Eseguire il ridimensionamento modificando le impostazioni DWU o sospendendo e riavviando le risorse di calcolo per ridurre i costi."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/03/2016"
   ms.author="barbkess;sonyama"/>  

# Gestire la potenza di calcolo in Azure SQL Data Warehouse (Panoramica)

> [AZURE.SELECTOR]
- [Panoramica](sql-data-warehouse-manage-compute-overview.md)
- [Portale](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [REST](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)

L'architettura di SQL Data Warehouse separa le risorse di archiviazione e calcolo consentendo a entrambe di eseguire il ridimensionamento in modo indipendente. Di conseguenza, è possibile ridimensionare le prestazioni e ridurre i costi pagando solo per le prestazioni necessarie.

Questa panoramica descrive le funzionalità di ridimensionamento delle prestazioni di SQL Data Warehouse seguenti e fornisce indicazioni su come e quando usarle.

- Ridimensionare la potenza di calcolo modificando le [Unità Data Warehouse (DWU)][]
- Sospendere o riavviare le risorse di calcolo

<a name="scale-performance-bk"></a>

## Ridimensionare le prestazioni

In SQL Data Warehouse, è possibile aumentare o ridurre rapidamente le prestazioni aumentando o riducendo le risorse di calcolo di CPU, memoria e larghezza di banda di I/O. Per ridimensionare le prestazioni, è sufficiente modificare il numero di [Unità Data Warehouse (DWU)][] allocate al database da SQL Data Warehouse. SQL Data Warehouse apporta rapidamente la modifica e gestisce tutte le modifiche sottostanti all'hardware o al software.

Sono ormai passati i tempi in cui occorreva individuare il tipo di processori, la quantità di memoria o il tipo di archiviazione appropriati per garantire prestazioni ottimali nel data warehouse. Allestendo il proprio data warehouse nel cloud, non è più necessario occuparsi dei problemi hardware di base. SQL Data Warehouse invece chiede semplicemente con quale velocità si intende elaborare i dati.

### Come ridimensionare le prestazioni?

Per aumentare o diminuire la potenza di calcolo in modo elastico, è sufficiente modificare l'impostazione [Unità Data Warehouse (DWU)][] per il database. Le prestazioni aumenteranno in modo lineare man mano che si aggiungono altre DWU. A livelli superiori di DWU, è necessario aggiungere più di 100 DWU per osservare un miglioramento significativo delle prestazioni. Per consentire la selezione di variazioni di DWU significative, vengono forniti livelli di DWU in grado di offrire i migliori risultati.
 
Per modificare le DWU, è possibile usare uno di questi metodi singoli.

- [Scale compute power with Azure portal (Ridimensionare la potenza di calcolo con il portale di Azure)][]
- [Scale compute power with PowerShell (Ridimensionare la potenza di calcolo con PowerShell)][]
- [Scale compute power with REST APIs (Ridimensionare la potenza di calcolo con le API REST)][]
- [Scale compute power with TSQL (Ridimensionare la potenza di calcolo con TSQL)][]

### Quante DWU è consigliabile usare?
 
Le prestazioni in SQL Data Warehouse vengono scalate in modo lineare e il passaggio da una scala di calcolo a un'altra (ad esempio, da 100 DWU a 2000 DWU) avviene nel giro di pochi secondi. Ciò consente di sperimentare diverse impostazioni DWU fino a determinare lo scenario più adatto alle proprie esigenze.

Per comprendere il valore di DWU ideale, provare ad aumentarlo e diminuirlo ed eseguire alcune query dopo il caricamento dei dati. Poiché il ridimensionamento è rapido, è possibile provare diversi livelli di prestazioni in un'ora o meno. Tenere presente che SQL Data Warehouse è progettato per elaborare grandi quantità di dati e per vedere le reali funzionalità per la scalabilità, soprattutto nelle implementazioni su vasta scala offerte da Microsoft, è consigliabile usare un set di dati di grandi dimensioni di circa 1 TB o superiore.

Raccomandazioni per individuare l'impostazione DWU più adatta al proprio carico di lavoro:

1. Per un data warehouse in sviluppo, iniziare con un numero limitato di DWU. Un buon punto di partenza è DW400 o DW200.
2. Monitorare le prestazioni dell'applicazione, osservare che il numero di DWUs selezionato in relazione alle prestazioni che si osservano.
3. Determinare di quanto si vogliono aumentare o ridurre le prestazioni per raggiungere il livello ottimale per i propri requisiti presupponendo una scalabilità lineare.
4. Aumentare o diminuire il numero di DWU proporzionalmente alla velocità desiderata per le prestazioni del proprio carico di lavoro. Il servizio risponderà rapidamente e adatterà le risorse di calcolo in modo che soddisfino i nuovi requisiti di DWU.
5. Continuare ad apportare modifiche finché non si raggiunge un livello di prestazioni ottimale per i propri requisiti aziendali.

### Quando è necessario ridimensionare le DWU?

Quando sono necessari risultati più rapidi, aumentare le DWUs e pagare il costo di prestazioni più elevate. Quando è richiesta una minore quantità di potenza di calcolo, diminuire le DWU e pagare solo per le prestazioni necessarie

Raccomandazioni sul momento più indicato per il ridimensionamento delle DWU:

1. Se l'applicazione ha un carico di lavoro molto variabile, aumentare o diminuire i livelli di DWU per far fronte ai picchi positivi e negativi. Ad esempio, se il carico di lavoro in genere fa registrare un picco alla fine del mese, pianificare l'aggiunta di altre DWU durante questi giorni di picco e quindi ridurre le dimensioni al termine del periodo critico.
2. Prima di eseguire un'operazione di caricamento o trasformazione di quantità di dati elevate, ridimensionare le DWU in modo che i dati siano disponibili più rapidamente.

<a name="pause-compute-bk"></a>

## Sospendere le risorse di calcolo

[AZURE.INCLUDE [Descrizione della sospensione di SQL Data Warehouse](../../includes/sql-data-warehouse-pause-description.md)]

Per sospendere un database, usare uno di questi metodi singoli.

- [Sospendere le risorse di calcolo con il portale di Azure][]
- [Sospendere le risorse di calcolo con PowerShell][]
- [Sospendere le risorse di calcolo con le API REST][]

<a name="resume-compute-bk"></a>

## Riavviare le risorse di calcolo

[AZURE.INCLUDE [Descrizione del riavvio di SQL Data Warehouse](../../includes/sql-data-warehouse-resume-description.md)]

Per riavviare un database, usare uno di questi metodi singoli.

- [Riavviare le risorse di calcolo con il portale di Azure][]
- [Riavviare le risorse di calcolo con PowerShell][]
- [Riavviare le risorse di calcolo con le API REST][]

## Autorizzazioni

Il ridimensionamento del database richiede le autorizzazioni descritte in [ALTER DATABASE][]. La sospensione e la ripresa richiedono l'autorizzazione [Collaboratore Database SQL][], in particolare Microsoft.Sql/servers/databases/action.

<a name="next-steps-bk"></a>

## Passaggi successivi
Per comprendere più facilmente altri concetti importanti sulle prestazioni, vedere gli articoli seguenti:

- [Gestione della concorrenza e del carico di lavoro in SQL Data Warehouse][]
- [Overview of tables in SQL Data Warehouse (Panoramica sulle tabelle in SQL Data Warehouse)][]
- [Distribuzione di tabelle in SQL Data Warehouse][]
- [Indicizzazione di tabelle in SQL Data Warehouse][]
- [Partizionamento delle tabelle][]
- [Managing statistics on tables in SQL Data Warehouse (Gestione delle statistiche nelle tabelle in SQL Data Warehouse)][]
- [Procedure consigliate][]

<!--Image reference-->  

<!--Article references-->
[Unità Data Warehouse (DWU)]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units

[Scale compute power with Azure portal (Ridimensionare la potenza di calcolo con il portale di Azure)]: ./sql-data-warehouse-manage-compute-portal.md#scale-compute-bk
[Scale compute power with PowerShell (Ridimensionare la potenza di calcolo con PowerShell)]: ./sql-data-warehouse-manage-compute-powershell.md#scale-compute-bk
[Scale compute power with REST APIs (Ridimensionare la potenza di calcolo con le API REST)]: ./sql-data-warehouse-manage-compute-rest-api.md#scale-compute-bk
[Scale compute power with TSQL (Ridimensionare la potenza di calcolo con TSQL)]: ./sql-data-warehouse-manage-compute-tsql.md#scale-compute-bk

[capacity limits]: ./sql-data-warehouse-service-capacity-limits.md

[Sospendere le risorse di calcolo con il portale di Azure]: ./sql-data-warehouse-manage-compute-portal.md#pause-compute-bk
[Sospendere le risorse di calcolo con PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#pause-compute-bk
[Sospendere le risorse di calcolo con le API REST]: ./sql-data-warehouse-manage-compute-rest-api.md#pause-compute-bk

[Riavviare le risorse di calcolo con il portale di Azure]: ./sql-data-warehouse-manage-compute-portal.md#resume-compute-bk
[Riavviare le risorse di calcolo con PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#resume-compute-bk
[Riavviare le risorse di calcolo con le API REST]: ./sql-data-warehouse-manage-compute-rest-api.md#resume-compute-bk

[Gestione della concorrenza e del carico di lavoro in SQL Data Warehouse]: ./sql-data-warehouse-develop-concurrency.md
[Overview of tables in SQL Data Warehouse (Panoramica sulle tabelle in SQL Data Warehouse)]: ./sql-data-warehouse-tables-overview.md
[Distribuzione di tabelle in SQL Data Warehouse]: ./sql-data-warehouse-tables-distribute.md
[Indicizzazione di tabelle in SQL Data Warehouse]: ./sql-data-warehouse-tables-index.md
[Partizionamento delle tabelle]: ./sql-data-warehouse-tables-partition.md
[Managing statistics on tables in SQL Data Warehouse (Gestione delle statistiche nelle tabelle in SQL Data Warehouse)]: ./sql-data-warehouse-tables-statistics.md
[Procedure consigliate]: ./sql-data-warehouse-best-practices.md
[development overview]: ./sql-data-warehouse-overview-develop.md

[Collaboratore Database SQL]: ../active-directory/role-based-access-built-in-roles.md#sql-db-contributor

<!--MSDN references-->
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->  
[Azure portal]: http://portal.azure.com/

<!---HONumber=AcomDC_0907_2016-->