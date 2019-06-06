---
title: Unità Data Warehouse (DWU, DWU a elevato utilizzo di calcolo) in Azure SQL Data Warehouse | Microsoft Docs
description: Raccomandazioni per la scelta del numero ideale di unità Data Warehouse (DWU, DWU a elevato utilizzo di calcolo) per ottimizzare prezzo e prestazioni e per come modificarne il numero.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 05/30/2019
ms.author: martinle
ms.reviewer: igorstan
mscustom: sqlfreshmay19
ms.openlocfilehash: d20a600951a0fe586e981adf12127072df1b744c
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428019"
---
# <a name="data-warehouse-units-dwus-and-compute-data-warehouse-units-cdwus"></a>Unità Data Warehouse (DWU) e DWU a elevato utilizzo di calcolo (cDWU)

Raccomandazioni per la scelta del numero ideale di unità Data Warehouse (DWU, DWU a elevato utilizzo di calcolo) per ottimizzare prezzo e prestazioni e per come modificarne il numero.

## <a name="what-are-data-warehouse-units"></a>Quali sono unità Data Warehouse

Azure SQL Data Warehouse di CPU, memoria e i/o vengono aggregate in unità di calcolo note unità Data Warehouse (Dwu). Un'unità DWU rappresenta una misura astratta e normalizzata delle risorse e delle prestazioni di calcolo. Una modifica a livello di servizio viene modificato il numero di Dwu sono disponibili nel sistema, che a sua volta consente di regolare le prestazioni e il costo del sistema.

Per prestazioni più elevate, è possibile aumentare il numero di unità data warehouse. Per ottenere prestazioni minori, ridurre unità data warehouse. I costi di archiviazione e calcolo vengono fatturati separatamente, pertanto la modifica delle unità Data Warehouse non influisce sui costi di archiviazione.

Le prestazioni per le unità Data Warehouse sono basate su queste metriche per il carico di lavoro del data warehouse:

- Velocità con cui un data warehousing query standard può analizzare un numero elevato di righe e quindi eseguire un'aggregazione complessa. Questa è un'operazione con uso intensivo di I/O e CPU.
- La velocità con cui il data warehouse può inserire dati dai BLOB di archiviazione di Azure o Azure Data Lake. Questa è un'operazione con uso intensivo di rete e CPU.
- Velocità con cui il [ `CREATE TABLE AS SELECT` ](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) comando T-SQL è possibile copiare una tabella. Questa operazione comporta la lettura dei dati dall'archiviazione, la relativa distribuzione tra tutti i nodi dell'appliance e la riscrittura nella risorsa di archiviazione. È un'operazione con uso intensivo di CPU, I/O e rete.

L'aumento delle DWU:

- Modifica in modo lineare le prestazioni del sistema per le analisi, l'aggregazione e le istruzioni CTAS
- Aumenta il numero di lettori e writer per operazioni di caricamento di PolyBase
- Aumenta il numero massimo di query simultanee e slot di concorrenza.

## <a name="service-level-objective"></a>Obiettivo del livello di servizio

L'obiettivo del livello di servizio (SLO) è l'impostazione di scalabilità che determina il livello di costi e prestazioni del data warehouse. I livelli di servizio per la seconda generazione sono misurati in unità di calcolo data warehouse (DWU a elevato utilizzo di calcolo), ad esempio DW2000c. I livelli di servizio di prima generazione sono misurati in DWU, ad esempio DW2000.
  > [!NOTE]
  > Azure SQL Data Warehouse Gen2 ha di recente aggiunto funzionalità di scalabilità aggiuntive per supportare livelli di calcolo minimo di 100 DWU a elevato utilizzo di calcolo. I data warehouse esistenti attualmente su Gen1 che richiedono i livelli di calcolo più bassi possono ora eseguire l'aggiornamento a Gen2 nelle aree attualmente disponibili senza costi aggiuntivi.  Se la propria area non è ancora supportata, è comunque possibile eseguire l'aggiornamento a un'area supportata. Per altre informazioni, vedere [Aggiornamento a Gen2](upgrade-to-latest-generation.md).

In T-SQL, l'impostazione di SERVICE_OBJECTIVE determina il livello di servizio e il livello di prestazioni per il data warehouse.

```sql
--Gen1
CREATE DATABASE myElasticSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000'
)
;

--Gen2
CREATE DATABASE myComputeSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

## <a name="performance-tiers-and-data-warehouse-units"></a>Livelli di prestazioni e unità Data Warehouse

Ogni livello di prestazioni usa un'unità di misura leggermente diversa per le unità Data Warehouse. Questa differenza si rispecchia nella fattura, dato che l'unità di scala ha una corrispondenza diretta nella fatturazione.

- I data warehouse di prima generazione sono misurati in unità data warehouse (DWU a elevato utilizzo di calcolo).
- I data warehouse di seconda generazione sono misurati in unità di calcolo data warehouse (DWU a elevato utilizzo di calcolo).

Sia le unità DWU che le unità cDWU supportano l'aumento o la riduzione delle risorse di calcolo, oltre alla sospensione delle operazioni di calcolo quando non è necessario usare il data warehouse. Queste operazioni sono tutte su richiesta. La seconda generazione usa anche una cache basata su disco locale nei nodi di calcolo per migliorare le prestazioni. Quando si ridimensiona o si sospende il sistema, la cache viene invalidata ed è quindi necessario un periodo di aggiornamento della cache prima di ottenere prestazioni ottimali.  

Con l'aumentare delle unità Data Warehouse si aumentano in modo lineare le risorse di calcolo. Gen2 fornisce le migliori prestazioni di query e conseguire la massima scalabilità. I sistemi Gen2 anche ottimizzare l'uso della cache.

### <a name="capacity-limits"></a>Limiti di capacità

Ogni server SQL (ad esempio, myserver.database.windows.net) ha una quota di [unità di transazione di database (DTU)](../sql-database/sql-database-what-is-a-dtu.md) che consente un numero specifico di unità di data warehouse. Per altre informazioni, vedere i [limiti della capacità di gestione del carico di lavoro](sql-data-warehouse-service-capacity-limits.md#workload-management).

## <a name="how-many-data-warehouse-units-do-i-need"></a>Quante unità data warehouse è necessario

Il numero ideale di unità Data Warehouse dipende molto dal carico di lavoro e dalla quantità di dati che sono stati caricati nel sistema.

Procedure per individuare l'impostazione DWU ottimale per il carico di lavoro:

1. Iniziare selezionando un'unità data warehouse più piccola.
2. Monitorare le prestazioni dell'applicazione durante il caricamento dei dati di test nel sistema, osservando il numero di DWU selezionato in relazione alle prestazioni rilevate.
3. Identificare eventuali requisiti aggiuntivi per i periodici periodi di punta delle attività. I carichi di lavoro che mostrano notevole picco e spalmare nell'attività potrebbero dover essere ridimensionato spesso.

SQL Data Warehouse è un sistema con scalabilità orizzontale che supporta il provisioning di enormi quantità di dati adattabili alle esigenze di calcolo e query. Per verificare le effettive capacità nell'ottica della scalabilità, in particolare per le maggiori quantità di DWU, è consigliabile ridimensionare il set di dati durante gli interventi di scalabilità per assicurarsi che siano disponibili dati sufficienti per le CPU. Per i test di scalabilità è consigliabile usare almeno 1 TB.

> [!NOTE]
>
> Le prestazioni delle query aumentano con maggiore parallelizzazione solo se il lavoro può essere suddivise tra i nodi di calcolo. Se si riscontra che gli interventi di scalabilità non hanno effetti sulle prestazioni, potrebbe essere necessario ottimizzare la progettazione delle tabelle e/o le query. Per informazioni sull'ottimizzazione delle query, vedere [Gestire le query utente](sql-data-warehouse-overview-manage-user-queries.md).

## <a name="permissions"></a>Autorizzazioni

Per modificare le unità Data Warehouse sono necessarie le autorizzazioni descritte in [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql).

I ruoli predefiniti per le risorse di Azure, ad esempio Collaboratore database SQL e Collaboratore SQL Server, possono modificare le impostazioni DWU.

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

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Per modificare il numero di Dwu o Cdwu, usare il [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) cmdlet di PowerShell. L'esempio seguente imposta l'obiettivo del livello di servizio su DW1000 per il database MySQLDW ospitato nel server MyServer.

```Powershell
Set-AzSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

Per altre informazioni, vedere i [cmdlet di PowerShell per SQL Data Warehouse](sql-data-warehouse-reference-powershell-cmdlets.md)

### <a name="t-sql"></a>T-SQL

Con T-SQL è possibile visualizzare le impostazioni correnti per DWU o DWU a elevato utilizzo di calcolo, modificare le impostazioni e controllare lo stato.

Per modificare le unità DWU o DWU a elevato utilizzo di calcolo:

1. Connettersi al database master associato al server di database SQL logico.
2. Usare l'istruzione TSQL [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql) . L'esempio seguente imposta l'obiettivo del livello di servizio su DW1000 per il database MySQLDW.

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

### <a name="rest-apis"></a>API REST

Per modificare le DWU, usare l'API REST descritta in [Create or Update Database](/rest/api/sql/databases/createorupdate) (Creare o aggiornare il database). Nell'esempio seguente imposta l'obiettivo del livello di servizio su DW1000 per il database MySQLDW, ospitato nel server MyServer. Il server appartiene al gruppo di risorse di Azure ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

Per altri esempi di API REST, vedere [API REST per SQL Data Warehouse](sql-data-warehouse-manage-compute-rest-api.md).

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

1. Inviare la query seguente per controllare lo stato dell'operazione.

```sql
SELECT    *
FROM      sys.dm_operation_status
WHERE     resource_type_desc = 'Database'
AND       major_resource_id = 'MySQLDW'
;
```

Questa DMV restituisce informazioni sulle varie operazioni di gestione in SQL Data Warehouse, ad esempio l'operazione e lo stato dell'operazione, che può essere IN_PROGRESS o COMPLETED.

## <a name="the-scaling-workflow"></a>Flusso di lavoro del ridimensionamento

Quando si avvia un'operazione di ridimensionamento, il sistema termina innanzitutto tutte le sessioni aperte, eseguire il rollback delle transazioni aperte per garantire uno stato coerente. Le operazioni di ridimensionamento vengono eseguite solo dopo il completamento del rollback delle transazioni.  

- Per un'operazione di scalabilità verticale, il sistema disconnette tutti i nodi di calcolo, effettua il provisioning di nodi di calcolo aggiuntivi e quindi ricollegare quest'ultimo al livello di archiviazione.
- Per un'operazione di riduzione delle prestazioni, il sistema disconnette tutti i nodi di calcolo e quindi ricollegare quest'ultimo solo i nodi necessari per il livello di archiviazione.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla gestione delle prestazioni, vedere [Classi di risorse per la gestione del carico di lavoro](resource-classes-for-workload-management.md) e [Limiti di memoria e concorrenza](memory-and-concurrency-limits.md).
