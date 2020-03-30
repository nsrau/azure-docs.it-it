---
title: Ridimensionare le risorse del pool elastico
description: Questa pagina descrive come ridimensionare le risorse per i pool elastici nel database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
ms.date: 3/14/2019
ms.openlocfilehash: daca108cfc8bb2e5b2a068170a4a0244c72c9592
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462599"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Ridimensionare le risorse dei pool elastici nel database SQL di Azure

Questo articolo illustra come ridimensionare le risorse di calcolo e di archiviazione disponibili per i pool elastici e i database in pool nel database SQL di Azure.

## <a name="change-compute-resources-vcores-or-dtus"></a>Modificare le risorse di calcolo (vCoreo o DTUs)Change compute resources (vCores or DTUs)

Dopo aver selezionato inizialmente il numero di vCore o eDTU, è possibile scalare un pool elastico verso l'alto o verso il basso in modo dinamico in base all'esperienza effettiva usando il portale di [Azure,](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases) [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool), l'interfaccia [della riga di comando di Azure](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)o l'API [REST.](https://docs.microsoft.com/rest/api/sql/elasticpools/update)

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>Impatto della modifica del livello di servizio o del ridimensionamento delle dimensioni di calcolo

La modifica del livello di servizio o delle dimensioni di calcolo di un pool elastico segue un modello simile a quello dei singoli database e prevede principalmente che il servizio esegua i passaggi seguenti:Changing the service tier or compute size of an elastic pool follows a pattern similar as for single databases and mainly involves the service performing the following steps:

1. Creare una nuova istanza di calcolo per il pool elasticoCreate new compute instance for the elastic pool  

    Viene creata una nuova istanza di calcolo per il pool elastico con il livello di servizio e le dimensioni di calcolo richiesti. Per alcune combinazioni di modifiche al livello di servizio e alle dimensioni di calcolo, è necessario creare una replica di ogni database nella nuova istanza di calcolo che implica la copia dei dati e può influire fortemente sulla latenza complessiva. Indipendentemente da ciò, i database rimangono online durante questo passaggio e le connessioni continuano a essere indirizzate ai database nell'istanza di calcolo originale.

2. Passare da un'istanza di elaborazione all'switch routing delle connessioni

    Le connessioni esistenti ai database nell'istanza di calcolo originale vengono eliminate. Tutte le nuove connessioni vengono stabilite ai database nella nuova istanza di calcolo. Per alcune combinazioni di modifiche al livello di servizio e alle dimensioni di calcolo, i file di database vengono scollegati e ricollegati durante lo switch.  Indipendentemente da ciò, il passaggio può comportare una breve interruzione del servizio quando i database non sono disponibili in genere per meno di 30 secondi e spesso solo per pochi secondi. Se sono presenti transazioni a esecuzione prolungata in esecuzione quando le connessioni vengono interrotte, la durata di questo passaggio può richiedere più tempo per ripristinare le transazioni interrotte. [Il ripristino accelerato del database](sql-database-accelerated-database-recovery.md) può ridurre l'impatto derivante dall'interruzione delle transazioni a esecuzione prolungata.

> [!IMPORTANT]
> Nessun dato viene perso durante qualsiasi passaggio del flusso di lavoro.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>Latenza della modifica del livello di servizio o del ridimensionamento delle dimensioni di calcoloLatency of changing service tier or rescaling compute size

La latenza stimata per modificare il livello di servizio o ridimensionare le dimensioni di calcolo di un singolo database o pool elastico è parametrizzata come segue:

|Livello di servizio|Singolo database di base,</br>Standard (S0-S1)|Piscina elastica di base,</br>Standard (S2-S12), </br>Iperscala, </br>Scopo generale singolo database o pool elastico|Premium o Business Critical singolo database o pool elastico|
|:---|:---|:---|:---|
|**Database singolo</br> di base, Standard (S0-S1)**|&bull;&nbsp;Latenza temporale costante indipendentedallo dallo spazio utilizzato</br>&bull;&nbsp;In genere, meno di 5 minuti|&bull;&nbsp;Latenza proporzionale allo spazio del database utilizzato a causa della copia dei dati</br>&bull;&nbsp;In genere, meno di 1 minuto per GB di spazio utilizzato|&bull;&nbsp;Latenza proporzionale allo spazio del database utilizzato a causa della copia dei dati</br>&bull;&nbsp;In genere, meno di 1 minuto per GB di spazio utilizzato|
|**Pool elastico </br>di base, Standard (S2-S12), </br>Hyperscale, </br>General Purpose singolo database o pool elastico**|&bull;&nbsp;Latenza proporzionale allo spazio del database utilizzato a causa della copia dei dati</br>&bull;&nbsp;In genere, meno di 1 minuto per GB di spazio utilizzato|&bull;&nbsp;Latenza temporale costante indipendentedallo dallo spazio utilizzato</br>&bull;&nbsp;In genere, meno di 5 minuti|&bull;&nbsp;Latenza proporzionale allo spazio del database utilizzato a causa della copia dei dati</br>&bull;&nbsp;In genere, meno di 1 minuto per GB di spazio utilizzato|
|**Premium o Business Critical singolo database o pool elastico**|&bull;&nbsp;Latenza proporzionale allo spazio del database utilizzato a causa della copia dei dati</br>&bull;&nbsp;In genere, meno di 1 minuto per GB di spazio utilizzato|&bull;&nbsp;Latenza proporzionale allo spazio del database utilizzato a causa della copia dei dati</br>&bull;&nbsp;In genere, meno di 1 minuto per GB di spazio utilizzato|&bull;&nbsp;Latenza proporzionale allo spazio del database utilizzato a causa della copia dei dati</br>&bull;&nbsp;In genere, meno di 1 minuto per GB di spazio utilizzato|

> [!NOTE]
>
> - In caso di modifica del livello di servizio o di ridimensionamento del calcolo per un pool elastico, la somma di spazio utilizzata in tutti i database del pool deve essere utilizzata per calcolare la stima.
> - Nel caso di spostamento di un database da/verso un pool elastico, solo lo spazio utilizzato dal database influisce sulla latenza, non sullo spazio utilizzato dal pool elastico.
>
> [!TIP]
> Per monitorare le operazioni in corso, vedere: [Gestire le operazioni usando l'API REST SQL](https://docs.microsoft.com/rest/api/sql/operations/list), Gestire le operazioni [tramite l'interfaccia della](/cli/azure/sql/db/op)riga di comando , Monitorare le operazioni [tramite T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) e questi due comandi di PowerShell: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) e [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>Considerazioni aggiuntive quando si modifica il livello di servizio o si ridimensiona la dimensione di calcoloAdditional considerations when changing service tier or rescaling compute size

- Quando si riduce vCore o eDTUs per un pool elastico, lo spazio utilizzato del pool deve essere inferiore alla dimensione massima consentita del livello di servizio di destinazione e delle eDTUs del pool.
- Durante il ridimensionamento delle eDTU di un pool elastico viene applicato un costo per le risorse di archiviazione extra se (1) le dimensioni massime delle risorse di archiviazione del pool sono supportate dal pool di destinazione e (2) la dimensione massima delle risorse di archiviazione supera lo spazio di archiviazione incluso del pool di destinazione. Se ad esempio un pool standard da 100 eDTU con una dimensione massima di 100 GB viene ridotto a un pool standard da 50 eDTU, viene applicato un costo per le risorse di archiviazione extra, poiché il pool di destinazione supporta una dimensione massima di 100 GB e lo spazio di archiviazione incluso è solo di 50 GB. Lo spazio di archiviazione extra è quindi 100 GB - 50 GB = 50 GB. Per i prezzi delle risorse di archiviazione extra, vedere [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/). Se la quantità effettiva di spazio usato è inferiore allo spazio di archiviazione incluso, questo costo aggiuntivo può essere evitato riducendo le dimensioni massime del database fino allo spazio incluso.

### <a name="billing-during-rescaling"></a>Fatturazione durante il ridimensionamento

Viene fatturata ogni ora per cui un database esiste usando il livello di servizio più elevato e le dimensioni di calcolo applicati in quell'ora, indipendentemente dall'uso o dal fatto che il database sia stato attivo per meno di un'ora. Ad esempio, se si crea un database singolo che viene eliminato cinque minuti dopo, in fattura viene riportato l'addebito relativo a un'ora di database.

## <a name="change-elastic-pool-storage-size"></a>modifica delle dimensioni di archiviazione del pool elastico

> [!IMPORTANT]
> In alcune circostanze, può essere necessario compattare un database per recuperare spazio inutilizzato. Per altre informazioni, vedere Gestire lo spazio file nel database SQL di Azure.For more information, see [Manage file space in Azure SQL Database.](sql-database-file-space-management.md)

### <a name="vcore-based-purchasing-model"></a>Modello di acquisto basato su vCore

- Il provisioning dell'archiviazione può essere effettuato fino al limite massimo di dimensioni:

  - Per l'archiviazione nei livelli di servizio per utilizzo generico o standard, aumentare o ridurre le dimensioni in incrementi di 10 GB
  - Per l'archiviazione nei livelli di servizio premium o business critical, aumentare o ridurre le dimensioni in incrementi di 250 GB
- Il provisioning dell'archiviazione per un pool elastico può essere effettuato aumentandone o diminuendone le dimensioni massime.
- Il prezzo dell'archiviazione per un pool elastico corrisponde allo spazio di archiviazione moltiplicato per il prezzo unitario dell'archiviazione del livello di servizio. Per informazioni dettagliate sul prezzo delle risorse di archiviazione extra, vedere [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> In alcune circostanze, può essere necessario compattare un database per recuperare spazio inutilizzato. Per altre informazioni, vedere Gestire lo spazio file nel database SQL di Azure.For more information, see [Manage file space in Azure SQL Database.](sql-database-file-space-management.md)

### <a name="dtu-based-purchasing-model"></a>modello di acquisto basato su DTU

- Il prezzo eDTU per un pool elastico include una determinata quantità di risorse di archiviazione senza costi aggiuntivi. Le risorse di archiviazione extra rispetto alla quantità inclusa possono essere sottoposte a provisioning per un costo aggiuntivo fino alla quantità massima in incrementi di 250 GB fino a 1 TB e quindi in incrementi di 256 GB oltre 1 TB. Per gli spazi di archiviazione inclusi e i limiti massimi relativi alle dimensioni, vedere [Pool elastico: dimensioni di archiviazione e dimensioni di calcolo](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).
- Le risorse di archiviazione extra per un pool elastico possono essere sottoposte a provisioning aumentandone le dimensioni massime mediante il [portale di Azure](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool), l'[interfaccia della riga di comando di Azure](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update) o l'[API REST](https://docs.microsoft.com/rest/api/sql/elasticpools/update).
- Il prezzo delle risorse di archiviazione extra per un pool elastico corrisponde allo spazio di archiviazione extra moltiplicato per il prezzo unitario del livello di servizio. Per informazioni dettagliate sul prezzo delle risorse di archiviazione extra, vedere [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> In alcune circostanze, può essere necessario compattare un database per recuperare spazio inutilizzato. Per altre informazioni, vedere Gestire lo spazio file nel database SQL di Azure.For more information, see [Manage file space in Azure SQL Database.](sql-database-file-space-management.md)

## <a name="next-steps"></a>Passaggi successivi

Per i limiti generali delle risorse, vedere [Limiti delle risorse basate su vCore per il database SQL - pool elastici](sql-database-vcore-resource-limits-elastic-pools.md) e [Limiti delle risorse basate su DTU per il database SQL - pool elastici](sql-database-dtu-resource-limits-elastic-pools.md).
