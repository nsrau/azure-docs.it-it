---
title: Gestire la risorsa di calcolo per il pool SQL dedicato (in precedenza SQL DW)
description: Informazioni sulle funzionalità di scalabilità orizzontale delle prestazioni per il pool SQL dedicato (in precedenza SQL DW) in Azure sinapsi Analytics. Scalabilità orizzontale regolando DWU o costi inferiori sospendendo il pool SQL dedicato.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/12/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 300759b4ab6f806c02e748ff4c9a63a6a772bff4
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461083"
---
# <a name="manage-compute-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Gestire il calcolo per il pool SQL dedicato (in precedenza SQL DW) in Azure sinapsi Analytics

Informazioni sulla gestione del pool SQL dedicato delle risorse di calcolo (in precedenza SQL DW) in Azure sinapsi Analytics. Riduzione dei costi sospendendo il pool SQL dedicato oppure ridimensionare il pool SQL dedicato per soddisfare le esigenze di prestazioni.

## <a name="what-is-compute-management"></a>Informazioni sulla gestione del calcolo

L'architettura del pool SQL dedicato (in precedenza SQL DW) separa le risorse di archiviazione e di calcolo, consentendo a ognuno di ridimensionarsi in modo indipendente. Di conseguenza, è possibile ridimensionare il calcolo per soddisfare le esigenze in termini di prestazioni indipendenti dall'archiviazione dei dati. È anche possibile sospendere e riprendere le risorse di calcolo. Come conseguenza logica di questa architettura, la [fatturazione](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) per calcolo e archiviazione è separata. Se non è necessario usare il pool SQL dedicato (in precedenza SQL DW) per un periodo di tempo, è possibile risparmiare sui costi di calcolo sospendendo il calcolo.

## <a name="scaling-compute"></a>Ridimensionamento delle risorse di calcolo

È possibile aumentare o ridurre le risorse di calcolo modificando l'impostazione delle [unità data warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md) per il pool SQL dedicato (in precedenza SQL DW). Le prestazioni di caricamento e query possono aumentare in modo lineare man mano che si aggiungono più unità di data warehouse.

Per le procedure per la scalabilità orizzontale, vedere le guide introduttive al [portale di Azure](quickstart-scale-compute-portal.md), a [PowerShell](quickstart-scale-compute-powershell.md) oppure a [T-SQL](quickstart-scale-compute-tsql.md). È anche possibile eseguire le operazioni di scalabilità orizzontale con un'[API REST](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

Per eseguire un'operazione di ridimensionamento, il pool SQL dedicato (denominato in precedenza SQL DW) interrompe prima tutte le query in ingresso e quindi esegue il rollback delle transazioni per garantire uno stato coerente. Il ridimensionamento ha effetto solo quando il rollback della transazione è completato. Per un'operazione di ridimensionamento, il sistema scollega il livello di archiviazione dai nodi di calcolo, aggiunge nodi di calcolo e quindi riconnette il livello di archiviazione al livello di calcolo. Ogni pool SQL dedicato (denominato in precedenza SQL DW) viene archiviato come distribuzioni di 60, che sono distribuiti uniformemente nei nodi di calcolo. L'aggiunta di più nodi di calcolo aumenta la potenza di calcolo. Con l'aumentare del numero di nodi di calcolo, il numero di distribuzioni per nodo di calcolo diminuisce, garantendo una maggiore potenza di calcolo per le query. Analogamente, la riduzione delle unità data warehouse riduce il numero di nodi di calcolo, riducendo le risorse di calcolo per le query.

La tabella seguente illustra come il numero di distribuzioni per nodo di calcolo si modifica in base alla modifica delle unità di data warehouse.  DW30000c fornisce 60 nodi di calcolo e ottiene prestazioni di query molto più elevate rispetto a DW100c.

| Unità di data warehouse  | \# dei nodi di calcolo | \# di distribuzioni per nodo |
| -------- | ---------------- | -------------------------- |
| DW100c   | 1                | 60                         |
| DW200c   | 1                | 60                         |
| DW300c   | 1                | 60                         |
| DW400c   | 1                | 60                         |
| DW500c   | 1                | 60                         |
| DW1000c  | 2                | 30                         |
| DW1500c  | 3                | 20                         |
| DW2000c  | 4                | 15                         |
| DW2500c  | 5                | 12                         |
| DW3000c  | 6                | 10                         |
| DW5000c  | 10               | 6                          |
| DW6000c  | 12               | 5                          |
| DW7500c  | 15               | 4                          |
| DW10000c | 20               | 3                          |
| DW15000c | 30               | 2                          |
| DW30000c | 60               | 1                          |

## <a name="finding-the-right-size-of-data-warehouse-units"></a>Ricerca delle dimensioni giuste delle unità di data warehouse

Per ottenere vantaggi della scalabilità orizzontale in termini di prestazioni, in particolare per le unità di warehouse dati di dimensioni maggiori, è necessario usare un set di dati di almeno 1 TB. Per trovare il numero migliore di unità di data warehouse per il pool SQL dedicato (in precedenza SQL DW), provare a eseguire la scalabilità verso l'alto e verso il basso. Dopo il caricamento dei dati, eseguire alcune query con numeri di unità di data warehouse diversi. Poiché il ridimensionamento è rapido, è possibile provare diversi livelli di prestazioni in un'ora o meno.

Indicazioni per la ricerca del numero di unità di data warehouse più adatto.

- Per un pool SQL dedicato (in precedenza SQL DW) in fase di sviluppo, iniziare selezionando un numero inferiore di unità di data warehouse.  Un punto di partenza valido è DW400c o DW200c.
- Monitorare le prestazioni dell'applicazione, osservando il numero di unità di data warehouse selezionato rispetto alle prestazioni ottenute.
- Presupporre una scalabilità lineare e determinare quanto è necessario aumentare o ridurre le unità di data warehouse.
- Continuare ad apportare modifiche finché non si raggiunge un livello di prestazioni ottimale per i propri requisiti aziendali.

## <a name="when-to-scale-out"></a>Quando applicare la scalabilità orizzontale

L'aumento del numero di unità di data warehouse influisce sugli aspetti delle prestazioni indicati di seguito.

- Aumento lineare delle prestazioni del sistema per analisi, aggregazione e istruzioni CTAS
- Aumento del numero di lettori e scrittori per il caricamento dei dati.
- Numero massimo di query simultanee e slot di concorrenza.

Indicazioni per i momenti in cui è opportuno aumentare le unità di data warehouse.

- Prima di eseguire un'operazione di caricamento o di trasformazione di dati con impatto elevato, applicare la scalabilità orizzontale per rendere disponibili i dati più rapidamente.
- Durante le ore lavorative di maggiore picco, applicare la scalabilità orizzontale per gestire un numero maggiore di query simultanee.

## <a name="what-if-scaling-out-does-not-improve-performance"></a>Cosa accade se la scalabilità orizzontale non migliora le prestazioni?

L'aggiunta di unità di data warehouse determina un aumento del parallelismo. Se il lavoro viene suddiviso in modo uniforme tra i nodi di calcolo, il maggiore parallelismo migliora le prestazioni delle query. Se la scalabilità orizzontale non modifica le prestazioni, i motivi possono essere diversi. I dati potrebbero essere presenti in modo non uniforme tra le distribuzioni o le query potrebbero introdurre spostamenti dei dati in notevole quantità. Per analizzare i problemi di prestazioni delle query, vedere [Risoluzione dei problemi di prestazioni](sql-data-warehouse-troubleshoot.md#performance).

## <a name="pausing-and-resuming-compute"></a>Sospensione e ripresa del calcolo

La sospensione del calcolo determina la disconnessione del livello di archiviazione dai nodi di calcolo. Le risorse di calcolo vengono rilasciate dall'account. Durante la sospensione del calcolo, non viene addebitato alcun costo correlato. Alla ripresa del calcolo, l'archiviazione viene ricollegata ai nodi di calcolo e le spese di calcolo vengono nuovamente addebitate.
Quando si sospende un pool SQL dedicato (in precedenza SQL DW):

- Le risorse di calcolo e memoria vengono restituite al pool di risorse disponibili nel data center.
- I costi delle unità di data warehouse sono pari a zero per la durata della sospensione.
- L'archivio dati non è interessato e i dati rimangano invariati.
- Tutte le operazioni in esecuzione o in coda vengono annullate.

Quando si riprende un pool SQL dedicato (in precedenza SQL DW):

- Il pool SQL dedicato (denominato in precedenza SQL DW) acquisisce risorse di calcolo e di memoria per l'impostazione di unità data warehouse.
- I costi di calcolo per le unità di data warehouse vengono nuovamente addebitati.
- I dati diventano disponibili.
- Dopo che il pool SQL dedicato (denominato in precedenza SQL DW) è online, è necessario riavviare le query del carico di lavoro.

Se si vuole che il pool SQL dedicato (denominato in precedenza SQL DW) sia sempre accessibile, provare a ridimensionarlo fino alla dimensione più piccola invece che alla sospensione.

Per le procedure di sospensione e ripresa, vedere le guide introduttive al [portale di Azure](pause-and-resume-compute-portal.md) oppure a [PowerShell](pause-and-resume-compute-powershell.md). È anche possibile usare l'[API REST di sospensione](sql-data-warehouse-manage-compute-rest-api.md#pause-compute) o l'[API REST di ripresa](sql-data-warehouse-manage-compute-rest-api.md#resume-compute).

## <a name="drain-transactions-before-pausing-or-scaling"></a>Scaricare le transazioni prima della sospensione o del ridimensionamento

Prima di avviare un'operazione di sospensione o ridimensionamento, è consigliabile consentire il completamento delle transazioni esistenti.

Quando si sospende o si ridimensiona il pool SQL dedicato (in precedenza SQL DW), dietro le quinte le query vengono annullate quando si avvia la richiesta di sospensione o di ridimensionamento. L'annullamento di una semplice query SELECT è un'operazione rapida senza quasi alcun impatto sul tempo necessario per sospendere o ridimensionare l'istanza.  Le query transazionali, che modificano i dati o la struttura dei dati, potrebbero tuttavia richiedere più tempo per l'arresto. **Le query transazionali, per definizione, devono essere completate interamente oppure è necessario il rollback delle modifiche.**  Il rollback del lavoro svolto da una query transazionale può richiedere la stessa quantità di tempo, o anche maggiore, della modifica originale applicata dalla query. Se, ad esempio, si annulla una query che sta eliminando righe e che è in esecuzione già da un'ora, potrebbe essere necessaria un'ora affinché il sistema inserisca di nuovo le righe che sono state eliminate. Se si sospende o si ridimensiona il servizio mentre sono in corso transazioni, potrebbe sembrare che l'operazione richieda molto tempo perché per la sospensione o il ridimensionamento è necessario attendere il completamento del rollback.

Vedere anche [Informazioni sulle transazioni](sql-data-warehouse-develop-transactions.md) e [Ottimizzazione delle transazioni](sql-data-warehouse-develop-best-practices-transactions.md).

## <a name="automating-compute-management"></a>Automazione della gestione del calcolo

Per automatizzare le operazioni di gestione di calcolo, vedere [Manage compute with Azure functions](manage-compute-with-azure-functions.md) (Gestire il calcolo con Funzioni di Azure).

Ogni operazione di scalabilità orizzontale, sospensione e ripresa può richiedere alcuni minuti. Se queste operazioni vengono eseguite automaticamente, è consigliabile implementare la logica necessaria per garantire che determinate operazioni siano completate prima di procedere con un'altra azione. Il controllo dello stato del pool SQL dedicato (in precedenza SQL DW) tramite vari endpoint consente di implementare correttamente l'automazione di tali operazioni.

Per controllare lo stato del pool SQL dedicato (in precedenza SQL DW), vedere la Guida introduttiva a [PowerShell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) o [T-SQL](quickstart-scale-compute-tsql.md#check-dedicated-sql-pool-formerly-sql-dw-state) . È anche possibile controllare lo stato del pool SQL dedicato (in precedenza SQL DW) con un' [API REST](sql-data-warehouse-manage-compute-rest-api.md#check-database-state).

## <a name="permissions"></a>Autorizzazioni

Il ridimensionamento del pool SQL dedicato (in precedenza SQL DW) richiede le autorizzazioni descritte in [ALTER database](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  La sospensione e la ripresa richiedono l'autorizzazione [Collaboratore Database SQL](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#sql-db-contributor), in particolare Microsoft.Sql/servers/databases/action.

## <a name="next-steps"></a>Passaggi successivi

Vedere la Guida alle procedure per [gestire](manage-compute-with-azure-functions.md) un altro aspetto della gestione delle risorse di calcolo consiste nell'allocazione di risorse di calcolo diverse per le singole query. Per altre informazioni, vedere [Classi di risorse per la gestione del carico di lavoro](resource-classes-for-workload-management.md).
