---
title: Gestire la potenza di calcolo in Azure SQL Data Warehouse (Panoramica) | Documentazione Microsoft
description: "Funzionalità relative alla scalabilità orizzontale delle prestazioni in Azure SQL Data Warehouse. Eseguire il ridimensionamento modificando le impostazioni DWU o sospendendo e riavviando le risorse di calcolo per ridurre i costi."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: 
ms.assetid: e13a82b0-abfe-429f-ac3c-f2b6789a70c6
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 03/22/2017
ms.author: elbutter
ms.translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: abe22f542a79714f6e894870872ee6b76ffe7633
ms.contentlocale: it-it
ms.lasthandoff: 04/12/2017

---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-overview"></a>Gestire la potenza di calcolo in Azure SQL Data Warehouse (Panoramica)
> [!div class="op_single_selector"]
> * [Panoramica](sql-data-warehouse-manage-compute-overview.md)
> * [Portale](sql-data-warehouse-manage-compute-portal.md)
> * [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
> * [REST](sql-data-warehouse-manage-compute-rest-api.md)
> * [TSQL](sql-data-warehouse-manage-compute-tsql.md)
>
>

L'architettura di SQL Data Warehouse separa le risorse di archiviazione e calcolo consentendo a entrambe di eseguire il ridimensionamento in modo indipendente. Pertanto, è possibile ridimensionare il calcolo per soddisfare le richieste di prestazioni, a prescindere dalla quantità di dati. Come conseguenza logica di questa architettura, la [fatturazione][billed] è separata per calcolo e archiviazione. 

Questa panoramica descrive il funzionamento della scalabilità orizzontale con SQL Data Warehouse e come usare le funzionalità di sospensione, ripresa e ridimensionamento di SQL Data Warehouse. Vedere la pagina sulle [Unità Data Warehouse (DWU)][data warehouse units (DWUs)] per altre informazioni sulla correlazione tra DWU e prestazioni. 

## <a name="how-compute-management-operations-work-in-sql-data-warehouse"></a>Funzionamento delle operazioni di gestione del calcolo in SQL Data Warehouse
L'architettura di SQL Data Warehouse consiste in un nodo di controllo, nodi di calcolo e di un livello di archiviazione suddiviso in 60 distribuzioni. 

Durante una normale sessione attiva in SQL Data Warehouse, il nodo head del sistema gestisce i metadati e contiene l'ottimizzazione delle query distribuite. Al di sotto del nodo head si trovano i nodi di calcolo e il livello di archiviazione. Considerando 400 DWU, il sistema dispone di un nodo head, di quattro nodi di calcolo e del livello di archiviazione, per 60 distribuzioni. 

Quando si eseguono operazioni di ridimensionamento o sospensione, il sistema termina innanzitutto le query in entrata, quindi esegue il rollback delle transazioni per garantire la coerenza. Per le operazioni di scalabilità, quest'ultima si verificherà solamente al completamento del rollback di transazione. Per le operazioni di scalabilità verticale con aumento di prestazioni, il sistema esegue il provisioning del numero di nodi di calcolo aggiuntivi desiderati, quindi inizia a ricollegare i nodi di calcolo al livello di archiviazione. Per un'operazione di scalabilità verticale con diminuzione delle prestazioni, i nodi non più necessari vengono rilasciati e quelli rimanenti si ricollegano al numero appropriato di distribuzioni. Per un'operazione di sospensione, vengono rilasciati tutti i nodi di calcolo e nel sistema vengono eseguite varie operazioni di metadati per lasciarlo in uno stato stabile.

| DWU  | \# di nodi di calcolo | \# di distribuzioni per nodo |
| ---- | ------------------ | ---------------------------- |
| 100  | 1                  | 60                           |
| 200  | 2                  | 30                           |
| 300  | 3                  | 20                           |
| 400  | 4                  | 15                           |
| 500  | 5                  | 12                           |
| 600  | 6                  | 10                           |
| 1000 | 10                 | 6                            |
| 1200 | 12                 | 5                            |
| 1500 | 15                 | 4                            |
| 2000 | 20                 | 3                            |
| 3000 | 30                 | 2                            |
| 6000 | 60                 | 1                            |

Le tre funzioni principali per la gestione del calcolo sono:

1. Sospendi
2. Riprendi
3. Scalabilità

Ciascuna di queste operazioni può richiedere alcuni minuti. In caso di operazioni automatiche di ridimensionamento/sospensione/ripresa, si potrebbe voler implementare la logica per assicurare il completamento di determinate operazioni prima di procedere con altre. 

Verificando lo stato del database in vari endpoint sarà possibile implementare correttamente l'automazione di tali operazioni. Il portale invierà notifiche una volta completata un'operazione e indicherà lo stato corrente del database. Tuttavia non è possibile verificare lo stato in maniera programmatica. 

>  [!NOTE]
>
>  La funzionalità di gestione del calcolo non è presente in tutti gli endpoint.
>
>  

|              | Sospensione/ripresa | Scalabilità | Controllare lo stato del database |
| ------------ | ------------ | ----- | -------------------- |
| Portale di Azure | Sì          | Sì   | **No**               |
| PowerShell   | Sì          | Sì   | Sì                  |
| API REST     | Sì          | Sì   | Sì                  |
| T-SQL        | **No**       | Sì   | Sì                  |



<a name="scale-compute-bk"></a>

## <a name="scale-compute"></a>Ridimensionare le risorse di calcolo

Le prestazioni in SQL Data Warehouse vengono misurate in [Unità Data Warehouse (DWU)][data warehouse units (DWUs)], una misura astratta delle risorse di calcolo come CPU, memoria e larghezza di banda I/O. Un utente che desidera aumentare le prestazioni del sistema può farlo in vari modi, ad esempio tramite il portale, T-SQL e le API REST. 

### <a name="how-do-i-scale-compute"></a>In che modo è possibile ridimensionare le risorse di calcolo?
La potenza di calcolo è gestita da SQL Data Warehouse modificando l'impostazione DWU. Le prestazioni aumentano in modo [lineare][linearly] man mano che si aggiungono più DWU per determinate operazioni.  Esistono varie offerte di DWU per assicurare un cambiamento netto delle prestazioni durante il ridimensionamento verticale del sistema. 

Per modificare le DWU, è possibile usare uno di questi metodi singoli.

* [Ridimensionare la potenza di calcolo con il portale di Azure][Scale compute power with Azure portal]
* [Ridimensionare la potenza di calcolo con PowerShell][Scale compute power with PowerShell]
* [Ridimensionare la potenza di calcolo con le API REST][Scale compute power with REST APIs]
* [Ridimensionare la potenza di calcolo con TSQL][Scale compute power with TSQL]

### <a name="how-many-dwus-should-i-use"></a>Quante DWU è consigliabile usare?

Per comprendere il valore di DWU ideale, provare ad aumentarlo e diminuirlo ed eseguire alcune query dopo il caricamento dei dati. Poiché il ridimensionamento è rapido, è possibile provare diversi livelli di prestazioni in un'ora o meno. 

> [!Note] 
> SQL Data Warehouse è progettato per elaborare grandi quantità di dati. Per constatarne le reali funzionalità di ridimensionamento, specialmente per DWU di dimensioni maggiori, si consiglia di usare un set di dati di grandi dimensioni, possibilmente 1 TB o più.

Raccomandazioni per individuare l'impostazione DWU più adatta al proprio carico di lavoro:

1. Per un data warehouse in sviluppo, iniziare con un numero più limitato di prestazioni DWU.  Un buon punto di partenza è DW400 o DW200.
2. Monitorare le prestazioni dell'applicazione, osservare che il numero di DWUs selezionato in relazione alle prestazioni che si osservano.
3. Determinare di quanto si vogliono aumentare o ridurre le prestazioni per raggiungere il livello ottimale per i propri requisiti presupponendo una scalabilità lineare.
4. Aumentare o diminuire il numero di DWU proporzionalmente alla velocità desiderata per le prestazioni del proprio carico di lavoro. 
5. Continuare ad apportare modifiche finché non si raggiunge un livello di prestazioni ottimale per i propri requisiti aziendali.

> [!NOTE]
>
> Le prestazioni delle query aumentano con maggiore parallelizzazione solo se il lavoro può essere suddivise tra i nodi di calcolo. Se si ritiene che la scalabilità non stia modificando le prestazioni, si consiglia di leggere gli articoli sull'ottimizzazione di queste ultime per verificare qualora i dati siano distribuiti in modo ineguale o se si stiano introducendo grandi quantità di movimenti di dati. 

### <a name="when-should-i-scale-dwus"></a>Quando è necessario ridimensionare le DWU?
Il ridimensionamento delle DWU modifica i seguenti scenari importanti:

1. Modifica lineare delle prestazioni del sistema per le analisi, l'aggregazione e le istruzioni CTAS
2. Aumento del numero di lettori e writer durante il caricamento con PolyBase
3. Numero massimo di query simultanee e slot di concorrenza

Raccomandazioni sul momento più indicato per il ridimensionamento delle DWU:

1. Prima di eseguire un'operazione di caricamento o trasformazione di quantità di dati elevate, ridimensionare le DWU in modo che i dati siano disponibili più rapidamente.
2. Durante le ore lavorative più intense, eseguire il ridimensionamento per poter ricevere un numero maggiori di query simultanee. 

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Sospendere il calcolo
[!INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Per sospendere un database, usare uno di questi metodi singoli.

* [Sospendere le risorse di calcolo con il portale di Azure][Pause compute with Azure portal]
* [Sospendere le risorse di calcolo con PowerShell][Pause compute with PowerShell]
* [Sospendere le risorse di calcolo con le API REST][Pause compute with REST APIs]

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>Riavviare le risorse di calcolo
[!INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

Per riavviare un database, usare uno di questi metodi singoli.

* [Riavviare le risorse di calcolo con il portale di Azure][Resume compute with Azure portal]
* [Riavviare le risorse di calcolo con PowerShell][Resume compute with PowerShell]
* [Riavviare le risorse di calcolo con le API REST][Resume compute with REST APIs]

<a name="check-compute-bk"></a>

## <a name="check-database-state"></a>Controllare lo stato del database 

Per riavviare un database, usare uno di questi metodi singoli.

- [Controllare lo stato del database con T-SQL][Check database state with T-SQL]
- [Controllare lo stato del database con PowerShell][Check database state with PowerShell]
- [Controllare lo stato del database con le API REST][Check database state with REST APIs]

## <a name="permissions"></a>autorizzazioni

Il ridimensionamento del database richiede le autorizzazioni descritte in [ALTER DATABASE][ALTER DATABASE].  La sospensione e la ripresa richiedono l'autorizzazione [Collaboratore Database SQL][SQL DB Contributor], in particolare Microsoft.Sql/servers/databases/action.

<a name="next-steps-bk"></a>

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
[data warehouse units (DWUs)]: ./sql-data-warehouse-overview-what-is.md#predictable-and-scalable-performance-with-data-warehouse-units
[billed]: https://azure.microsoft.com/en-us/pricing/details/sql-data-warehouse/
[linearly]: ./sql-data-warehouse-overview-what-is.md#predictable-and-scalable-performance-with-data-warehouse-units
[Scale compute power with Azure portal]: ./sql-data-warehouse-manage-compute-portal.md#scale-compute-power
[Scale compute power with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#scale-compute-bk
[Scale compute power with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#scale-compute-bk
[Scale compute power with TSQL]: ./sql-data-warehouse-manage-compute-tsql.md#scale-compute-bk

[capacity limits]: ./sql-data-warehouse-service-capacity-limits.md

[Pause compute with Azure portal]:  ./sql-data-warehouse-manage-compute-portal.md#pause-compute-bk
[Pause compute with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#pause-compute-bk
[Pause compute with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#pause-compute-bk

[Resume compute with Azure portal]:  ./sql-data-warehouse-manage-compute-portal.md#resume-compute-bk
[Resume compute with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#resume-compute-bk
[Resume compute with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#resume-compute-bk

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

