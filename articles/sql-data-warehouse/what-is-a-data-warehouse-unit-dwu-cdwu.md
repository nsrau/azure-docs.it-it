---
title: "Cosa sono le unità Data Warehouse (DWU, DWU a elevato utilizzo di calcolo) in Azure SQL Data Warehouse? | Microsoft Docs"
description: "Funzionalità relative alla scalabilità orizzontale delle prestazioni in Azure SQL Data Warehouse. Aumentare le prestazioni regolando DWU e DWU a elevato utilizzo di calcolo oppure sospendendo e riavviando le risorse di calcolo per ridurre i costi."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: e13a82b0-abfe-429f-ac3c-f2b6789a70c6
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 11/10/2017
ms.author: jrj;barbkess
ms.openlocfilehash: 02998c48dcab5d3ed191b168665c9e47bbfbd232
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2017
---
# <a name="data-warehouse-units-dwus-and-compute-data-warehouse-units-cdwus"></a>Unità Data Warehouse (DWU) e DWU a elevato utilizzo di calcolo (cDWU)
Informazioni sulle unità Data Warehouse (DWU) e le DWU a elevato utilizzo di calcolo (cDWU) per Azure SQL Data Warehouse. Sono incluse raccomandazioni per la scelta del numero ideale di unità Data Warehouse e per come modificarne il numero. 

## <a name="what-are-data-warehouse-units"></a>Cosa sono le unità Data Warehouse?
In SQL Data Warehouse, le risorse di CPU, memoria e I/O vengono aggregate in unità di calcolo note come unità Data Warehouse (DWU). Un'unità DWU rappresenta una misura astratta e normalizzata delle risorse e delle prestazioni di calcolo. Modificando il livello di servizio si modifica il numero di DWU allocate al sistema, regolando di conseguenza le prestazioni e il costo del sistema. 

Per ottenere prestazioni migliori a pagamento, è possibile aumentare il numero di unità Data Warehouse. Per ottenere prestazioni minori, ridurre le unità Data Warehouse. I costi di archiviazione e calcolo vengono fatturati separatamente, pertanto la modifica delle unità Data Warehouse non influisce sui costi di archiviazione.

Le prestazioni per le unità Data Warehouse sono basate su queste metriche per il carico di lavoro del data warehouse:

- A quale velocità una query di data warehousing standard può analizzare un numero elevato di righe e quindi eseguire un'aggregazione complessa? Questa è un'operazione con uso intensivo di I/O e CPU.
- A quale velocità il data warehouse è in grado di inserire dati dai BLOB del servizio di archiviazione di Azure o da Azure Data Lake? Questa è un'operazione con uso intensivo di rete e CPU. 
- A quale velocità il comando T-SQL [CREATE TABLE AS SELECT](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) è in grado di copiare una tabella? Questa operazione comporta la lettura dei dati dall'archiviazione, la relativa distribuzione tra tutti i nodi dell'appliance e la riscrittura nella risorsa di archiviazione. È un'operazione con uso intensivo di CPU, I/O e rete.

L'aumento delle DWU:
- Modifica in modo lineare le prestazioni del sistema per le analisi, l'aggregazione e le istruzioni CTAS
- Aumenta il numero di lettori e writer per operazioni di caricamento di PolyBase
- Aumenta il numero massimo di query simultanee e slot di concorrenza.

## <a name="performance-tiers-and-data-warehouse-units"></a>Livelli di prestazioni e unità Data Warehouse

Ogni livello di prestazioni usa un'unità di misura leggermente diversa per le unità Data Warehouse. Questa differenza si rispecchia nella fattura, dato che l'unità di scala ha una corrispondenza diretta nella fatturazione.

- Il livello di prestazioni ottimizzato per l'elasticità viene misurato in unità Data Warehouse (DWU).
- Il livello di prestazioni ottimizzato per il calcolo viene misurato in DWU a elevato utilizzo di calcolo (cDWU). 

Sia le unità DWU che le unità cDWU supportano l'aumento o la riduzione delle risorse di calcolo, oltre alla sospensione delle operazioni di calcolo quando non è necessario usare il data warehouse. Queste operazioni sono tutte su richiesta. Il livello di prestazioni ottimizzato per il calcolo usa anche una cache basata su disco locale nei nodi di calcolo per migliorare le prestazioni. Quando si ridimensiona o si sospende il sistema, la cache viene invalidata ed è quindi necessario un periodo di aggiornamento della cache prima di ottenere prestazioni ottimali.  

Con l'aumentare delle unità Data Warehouse si aumentano in modo lineare le risorse di calcolo. Il livello di prestazioni ottimizzato per il calcolo offre le migliori prestazioni per le query e la massima scalabilità, ma con un prezzo di ingresso maggiore. Questo livello è progettato per le aziende con una richiesta costante di prestazioni elevate. Questi sistemi usano al massimo la cache. 

### <a name="capacity-limits"></a>Limiti di capacità
Ogni server SQL (ad esempio, myserver.database.windows.net) ha una quota di [unità di transazione di database (DTU)](../sql-database/sql-database-what-is-a-dtu.md) che consente un numero specifico di unità di data warehouse. Per altre informazioni, vedere i [limiti della capacità di gestione del carico di lavoro](sql-data-warehouse-service-capacity-limits.md#workload-management).


## <a name="how-many-data-warehouse-units-do-i-need"></a>Quante unità Data Warehouse sono necessarie?
Il numero ideale di unità Data Warehouse dipende molto dal carico di lavoro e dalla quantità di dati che sono stati caricati nel sistema.

Procedure per individuare l'impostazione DWU ottimale per il carico di lavoro:

1. Durante lo sviluppo, iniziare selezionando un numero di unità DWU inferiore usando il livello di prestazioni ottimizzato per l'elasticità.  Dato che l'esigenza prioritaria in questa fase è la convalida funzionale, il livello di prestazioni Ottimizzato per l'elasticità è un'opzione ragionevole. Un buon punto di partenza è DW200. 
2. Monitorare le prestazioni dell'applicazione durante il caricamento dei dati di test nel sistema, osservando il numero di DWU selezionato in relazione alle prestazioni rilevate.
3. Identificare eventuali requisiti aggiuntivi per i periodici periodi di punta delle attività. Se il carico di lavoro mostra picchi e valli significativi nelle attività ed esiste un buon motivo per modificare il livello di risorse frequentemente, privilegiare il livello di prestazioni Ottimizzato per l'elasticità.
4. Se sono necessarie più di 1000 DWU, preferire il livello Ottimizzato per il calcolo perché offre le prestazioni migliori.

SQL Data Warehouse è un sistema con scalabilità orizzontale che supporta il provisioning di enormi quantità di dati adattabili alle esigenze di calcolo e query. Per verificare le effettive capacità nell'ottica della scalabilità, in particolare per le maggiori quantità di DWU, è consigliabile ridimensionare il set di dati durante gli interventi di scalabilità per assicurarsi che siano disponibili dati sufficienti per le CPU. Per i test di scalabilità è consigliabile usare almeno 1 TB.

> [!NOTE]
>
> Le prestazioni delle query aumentano con maggiore parallelizzazione solo se il lavoro può essere suddivise tra i nodi di calcolo. Se si riscontra che gli interventi di scalabilità non hanno effetti sulle prestazioni, potrebbe essere necessario ottimizzare la progettazione delle tabelle e/o le query. Per indicazioni sull'ottimizzazione delle query, fare riferimento agli articoli seguenti sulle [prestazioni](sql-data-warehouse-overview-manage-user-queries.md). 

## <a name="permissions"></a>Autorizzazioni

Per modificare le unità Data Warehouse sono necessarie le autorizzazioni descritte in [ALTER DATABASE][ALTER DATABASE]. 

## <a name="view-current-dwu-settings"></a>Visualizzare le impostazioni DWU correnti

Per visualizzare l'impostazione corrente per le unità DWU:

1. Aprire Esplora oggetti di SQL Server in Visual Studio.
2. Connettersi al database master associato al server di database SQL logico.
3. Effettuare una selezione nella vista a gestione dinamica sys.database_service_objectives. Di seguito è fornito un esempio:  

```sql
SELECT  db.name [Database]
,       ds.edition [Edition]
,       ds.service_objective [Service Objective]
FROM    sys.database_service_objectives   AS ds
JOIN    sys.databases                     AS db ON ds.database_id = db.database_id
;
```

## <a name="change-data-warehouse-units"></a>Modificare le unità Data Warehouse

### <a name="azure-portal"></a>Portale di Azure
Per modificare DWU o DWU a elevato utilizzo di calcolo:

1. Aprire il [portale di Azure](https://portal.azure.com), aprire il database e quindi fare clic su **Ridimensiona**.

2. In **Ridimensiona** spostare il dispositivo di scorrimento verso sinistra o destra per modificare l'impostazione delle DWU.

3. Fare clic su **Save**. Viene visualizzato un messaggio di conferma. Fare clic su **Sì** per confermare o su **No** per annullare.

### <a name="powershell"></a>PowerShell
Per modificare il numero di DWU o di DWU a elevato utilizzo di calcolo usare il cmdlet di PowerShell [Set-AzureRmSqlDatabase][Set-AzureRmSqlDatabase]. L'esempio seguente imposta l'obiettivo del livello di servizio su DW1000 per il database MySQLDW ospitato nel server MyServer.

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

### <a name="t-sql"></a>T-SQL
Con T-SQL è possibile visualizzare le impostazioni correnti per DWU o DWU a elevato utilizzo di calcolo, modificare le impostazioni e controllare lo stato. 

Per modificare le unità DWU o DWU a elevato utilizzo di calcolo:

1. Connettersi al database master associato al server di database SQL logico.
2. Usare l'istruzione T-SQL [ALTER DATABASE][ALTER DATABASE]. L'esempio seguente imposta l'obiettivo del livello di servizio su DW1000 per il database MySQLDW. 

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

### <a name="rest-apis"></a>API REST

Per modificare le DWU, usare l'API REST [Create or Update Database][Create or Update Database]. L'esempio seguente imposta l'obiettivo del livello di servizio su DW1000 per il database MySQLDW ospitato nel server MyServer. Il server appartiene al gruppo di risorse di Azure ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```


## <a name="check-status-of-dwu-changes"></a>Controllare lo stato delle modifiche alle DWU

Il completamento delle modifiche alle DWU può richiedere alcuni minuti. In caso di operazioni automatiche di ridimensionamento, valutare l'implementazione della logica per assicurare il completamento di determinate operazioni prima di procedere con altre. 

Verificando lo stato del database in vari endpoint sarà possibile implementare correttamente l'automazione. Il portale invia notifiche una volta completata un'operazione e indica lo stato corrente del database. Tuttavia non è possibile verificare lo stato in maniera programmatica. 

Non è possibile verificare lo stato del database per le operazioni di scalabilità orizzontale con il portale di Azure.

Per controllare lo stato delle modifiche alle DWU:

1. Connettersi al database master associato al server di database SQL logico.
2. Inviare la query seguente per controllare lo stato del database.


```sql
SELECT    *
FROM      sys.databases
;
```

3. Inviare la query seguente per controllare lo stato dell'operazione.

```sql
SELECT    *
FROM      sys.dm_operation_status
WHERE     resource_type_desc = 'Database'
AND       major_resource_id = 'MySQLDW'
;
```

Questa DMV restituisce informazioni sulle varie operazioni di gestione in SQL Data Warehouse, ad esempio l'operazione e lo stato dell'operazione, che può essere IN_PROGRESS o COMPLETED.

## <a name="the-scaling-workflow"></a>Flusso di lavoro del ridimensionamento

Quando si avvia un'operazione di ridimensionamento, il sistema termina prima di tutto tutte le sessioni aperte, eseguendo il rollback delle transazioni in sospeso per garantire uno stato coerente. Le operazioni di ridimensionamento vengono eseguite solo dopo il completamento del rollback delle transazioni.  

- Per un'operazione di aumento, il sistema esegue il provisioning delle unità di calcolo aggiuntive e quindi riattiva il collegamento al livello di archiviazione. 
- Per un'operazione di riduzione, i nodi non necessari vengono scollegati dall'archiviazione e vengono ricollegati i nodi rimanenti.

## <a name="next-steps"></a>Passaggi successivi
Per comprendere più facilmente altri concetti importanti sulle prestazioni, vedere gli articoli seguenti:

* [Gestione della concorrenza e del carico di lavoro][Workload and concurrency management]
* [Panoramica delle tabelle][Table design overview]
* [Distribuzione di tabelle][Table distribution]
* [Indicizzazione di tabelle][Table indexing]
* [Partizionamento di tabelle][Table partitioning]
* [Statistiche delle tabelle][Table statistics]
* [Procedure consigliate][Best practices]

<!--Image reference-->

<!--Article references-->

[capacity limits]: ./sql-data-warehouse-service-capacity-limits.md


[Check database state with T-SQL]: ./sql-data-warehouse-manage-compute-tsql.md#check-database-state-and-operation-progress
[Check database state with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#check-database-state
[Check database state with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#check-database-state

[Workload and concurrency management]: ./sql-data-warehouse-develop-concurrency.md
[Table design overview]: ./sql-data-warehouse-tables-overview.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Table indexing]: ./sql-data-warehouse-tables-index.md
[Table partitioning]: ./sql-data-warehouse-tables-partition.md
[Table statistics]: ./sql-data-warehouse-tables-statistics.md
[Best practices]: ./sql-data-warehouse-best-practices.md
[development overview]: ./sql-data-warehouse-overview-develop.md

[SQL DB Contributor]: ../active-directory/role-based-access-built-in-roles.md#sql-db-contributor

<!--MSDN references-->
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->
[Azure portal]: http://portal.azure.com/
