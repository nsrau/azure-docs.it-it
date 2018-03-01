---
title: Gestire risorse di calcolo in Azure SQL Data Warehouse | Microsoft Docs
description: "Informazioni sulle funzionalità di scalabilità orizzontale per le prestazioni in Azure SQL Data Warehouse. Applicare la scalabilità orizzontale modificando le DWU o ridurre i costi tramite la sospensione delle funzioni di data warehouse."
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
ms.date: 02/20/2018
ms.author: elbutter
ms.openlocfilehash: 7e6ae6e59b53dd79dab5e2504cf7a43a30e55353
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="manage-compute-in-azure-sql-data-warehouse"></a>Gestire le risorse di calcolo in Azure SQL Data Warehouse
Informazioni sulla gestione delle risorse di calcolo in Azure SQL Data Warehouse. Ridurre i costi sospendendo il data warehouse o ridimensionare il data warehouse per soddisfare le esigenze in termini di prestazioni. 

## <a name="what-is-compute-management"></a>Informazioni sulla gestione del calcolo
L'architettura di SQL Data Warehouse separa le risorse di archiviazione e calcolo consentendo a entrambe di eseguire il ridimensionamento in modo indipendente. Di conseguenza, è possibile ridimensionare il calcolo per soddisfare le esigenze in termini di prestazioni indipendenti dall'archiviazione dei dati. È anche possibile sospendere e riprendere le risorse di calcolo. Come conseguenza logica di questa architettura, la [fatturazione](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) per calcolo e archiviazione è separata. Se non è necessario usare il data warehouse per un periodo di tempo, è possibile sospendere le funzioni di calcolo al fine di risparmiare i costi associati. 

## <a name="scaling-compute"></a>Ridimensionamento delle risorse di calcolo
È possibile applicare la scalabilità orizzontale o ridurre le risorse di calcolo modificando l'impostazione delle [unità di data warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md) per il data warehouse in uso. Le prestazioni di caricamento e relative alle query possono aumentare in modo lineare man mano che si aggiungono più unità di data warehouse. SQL Data Warehouse offre [livelli di servizio](performance-tiers.md#service-levels) per le unità di data warehouse che determinano una modifica significativa delle prestazioni quando si aumentano o si diminuiscono le risorse. 

Per le procedure per la scalabilità orizzontale, vedere le guide introduttive al [portale di Azure](quickstart-scale-compute-portal.md), a [PowerShell](quickstart-scale-compute-powershell.md) oppure a [T-SQL](quickstart-scale-compute-tsql.md). È anche possibile eseguire le operazioni di scalabilità orizzontale con un'[API REST](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

Per eseguire un'operazione di ridimensionamento, SQL Data Warehouse interrompe prima tutte le query in ingresso e quindi esegue il rollback delle transazioni per garantire uno stato coerente. Il ridimensionamento ha effetto solo quando il rollback della transazione è completato. Per un'operazione di ridimensionamento, il sistema disconnette il livello di archiviazione dai nodi di calcolo, aggiunge nodi di calcolo e quindi ricollega il livello di archiviazione a quello di calcolo. Ogni data warehouse viene archiviato come 60 distribuzioni, uniformemente distribuite ai nodi di calcolo. L'aggiunta di più nodi di calcolo determina un aumento della potenza di calcolo. Con l'aumento del numero di nodi di calcolo si riduce il numero di distribuzioni per ogni nodo di calcolo, con un conseguente incremento della potenza di calcolo per le query. In modo analogo, la diminuzione delle unità di data warehouse riduce il numero di nodi di calcolo e di conseguenza le risorse di calcolo per le query.

La tabella seguente illustra come il numero di distribuzioni per nodo di calcolo si modifica in base alla modifica delle unità di data warehouse.  DWU6000 fornisce 60 nodi di calcolo e consente di ottenere prestazioni delle query molto più elevate di DWU100. 

| Unità di data warehouse  | \# di nodi di calcolo | \# di distribuzioni per nodo |
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


## <a name="finding-the-right-size-of-data-warehouse-units"></a>Ricerca delle dimensioni giuste delle unità di data warehouse

Per ottenere vantaggi della scalabilità orizzontale in termini di prestazioni, in particolare per le unità di warehouse dati di dimensioni maggiori, è necessario usare un set di dati di almeno 1 TB. Per trovare la quantità di unità di data warehouse più adatta per il data warehouse in uso, provare ad aumentarne e a ridurne il numero. Dopo il caricamento dei dati, eseguire alcune query con numeri di unità di data warehouse diversi. Poiché il ridimensionamento è rapido, è possibile provare diversi livelli di prestazioni in un'ora o meno. 

Indicazioni per la ricerca del numero di unità di data warehouse più adatto.

- Per un data warehouse in fase di sviluppo, iniziare selezionando un numero minore di unità di data warehouse.  Un buon punto di partenza è DW400 o DW200.
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
La sospensione del calcolo determina la disconnessione del livello di archiviazione dai nodi di calcolo. Le risorse di calcolo vengono rilasciate dall'account dell'utente. Durante la sospensione del calcolo, non viene addebitato alcun costo correlato. Alla ripresa del calcolo, l'archiviazione viene ricollegata ai nodi di calcolo e le spese di calcolo vengono nuovamente addebitate. La sospensione di un data warehouse provoca gli effetti seguenti:

* Le risorse di calcolo e memoria vengono restituite al pool di risorse disponibili nel data center.
* I costi delle unità di data warehouse sono pari a zero per la durata della sospensione.
* L'archivio dati non è interessato e i dati rimangano invariati. 
* SQL Data Warehouse annulla tutte le operazioni in esecuzione o in coda.

La ripresa di un data warehouse provoca gli effetti seguenti:

* SQL Data Warehouse acquisisce risorse di calcolo e memoria per le impostazioni delle unità di data warehouse.
* I costi di calcolo per le unità di data warehouse vengono nuovamente addebitati.
* I dati diventano disponibili.
* Dopo che il data warehouse è online, è necessario riavviare le query del carico di lavoro.

Se si vuole che il data warehouse sia sempre accessibile, è opportuno ridurne al minimo le dimensioni anziché sospenderlo. 

Per le procedure di sospensione e ripresa, vedere le guide introduttive al [portale di Azure](pause-and-resume-compute-portal.md) oppure a [PowerShell](pause-and-resume-compute-powershell.md). È anche possibile usare l'[API REST di sospensione](sql-data-warehouse-manage-compute-rest-api.md#pause-compute) o l'[API REST di ripresa](sql-data-warehouse-manage-compute-rest-api.md#resume-compute).

## <a name="drain-transactions-before-pausing-or-scaling"></a>Scaricare le transazioni prima della sospensione o del ridimensionamento
Prima di avviare un'operazione di sospensione o ridimensionamento, è consigliabile consentire il completamento delle transazioni esistenti.

Quando si sospende o ridimensiona SQL Data Warehouse, dietro le quinte le query vengono annullate all'avvio di tale richiesta.  L'annullamento di una semplice query SELECT è un'operazione rapida senza quasi alcun impatto sul tempo necessario per sospendere o ridimensionare l'istanza.  Le query transazionali, che modificano i dati o la struttura dei dati, potrebbero tuttavia richiedere più tempo per l'arresto.  **Le query transazionali, per definizione, devono essere completate interamente oppure è necessario il rollback delle modifiche.**  Il rollback del lavoro svolto da una query transazionale può richiedere la stessa quantità di tempo, o anche maggiore, della modifica originale applicata dalla query.  Se, ad esempio, si annulla una query che sta eliminando righe e che è in esecuzione già da un'ora, potrebbe essere necessaria un'ora affinché il sistema inserisca di nuovo le righe che sono state eliminate.  Se si sospende o si ridimensiona il servizio mentre sono in corso transazioni, potrebbe sembrare che l'operazione richieda molto tempo perché per la sospensione o il ridimensionamento è necessario attendere il completamento del rollback.

Vedere anche [Transazioni in SQL Data Warehouse](sql-data-warehouse-develop-transactions.md) e [Ottimizzazione delle transazioni per SQL Data Warehouse][Ottimizzazione delle transazioni per SQL Data Warehouse](sql-data-warehouse-develop-best-practices-transactions.md).

## <a name="automating-compute-management"></a>Automazione della gestione del calcolo
Per automatizzare le operazioni di gestione di calcolo, vedere [Manage compute with Azure functions](manage-compute-with-azure-functions.md) (Gestire il calcolo con Funzioni di Azure).

Ogni operazione di scalabilità orizzontale, sospensione e ripresa può richiedere alcuni minuti. Se queste operazioni vengono eseguite automaticamente, è consigliabile implementare la logica necessaria per garantire che determinate operazioni siano completate prima di procedere con un'altra azione. La verifica dello stato del data warehouse tramite vari endpoint consente di implementare correttamente l'automazione di tali operazioni. 

Per verificare lo stato del data warehouse, vedere le guide introduttive a [PowerShell](quickstart-scale-compute-powershell.md#check-database-state) oppure a [T-SQL](quickstart-scale-compute-tsql.md#check-database-state). È anche possibile verificare lo stato del data warehouse con un'[API REST](sql-data-warehouse-manage-compute-rest-api.md#check-database-state).


## <a name="permissions"></a>Autorizzazioni

Il ridimensionamento del database richiede le autorizzazioni descritte in [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse.md).  La sospensione e la ripresa richiedono l'autorizzazione [Collaboratore Database SQL](../active-directory/role-based-access-built-in-roles.md#sql-db-contributor), in particolare Microsoft.Sql/servers/databases/action.


## <a name="next-steps"></a>Passaggi successivi
Un altro aspetto della gestione delle risorse di calcolo è l'allocazione di risorse diverse per ogni singola query. Per altre informazioni, vedere [Classi di risorse per la gestione del carico di lavoro](resource-classes-for-workload-management.md).
