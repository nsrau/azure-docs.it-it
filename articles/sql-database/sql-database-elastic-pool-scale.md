---
title: Ridimensionare le risorse del pool elastico-database SQL di Azure
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
ms.openlocfilehash: 812de89b43d1cb2bc7f9b5c5c619f3860344d5dd
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690426"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Ridimensionare le risorse dei pool elastici nel database SQL di Azure

Questo articolo illustra come ridimensionare le risorse di calcolo e di archiviazione disponibili per i pool elastici e i database in pool nel database SQL di Azure.

## <a name="change-compute-resources-vcores-or-dtus"></a>Modificare le risorse di calcolo (VCore o DTU)

Dopo aver selezionato inizialmente il numero di Vcore o edtu, è possibile ridimensionare in modo dinamico un pool elastico in base all'esperienza effettiva usando il [portale di Azure](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool), l'interfaccia della riga di comando di [Azure](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)o l' [API REST](https://docs.microsoft.com/rest/api/sql/elasticpools/update).

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>Effetti della modifica delle dimensioni di calcolo del livello di servizio o di ridimensionamento

La modifica del livello di servizio o delle dimensioni di calcolo di un pool elastico segue un modello simile a quello per i database singoli e comporta principalmente il servizio che esegue i passaggi seguenti:

1. Creare una nuova istanza di calcolo per il pool elastico  

    Viene creata una nuova istanza di calcolo per il pool elastico con il livello di servizio e le dimensioni di calcolo richiesti. Per alcune combinazioni di modifiche del livello di servizio e delle dimensioni di calcolo, è necessario creare una replica di ogni database nella nuova istanza di calcolo, che prevede la copia dei dati e può influenzare fortemente la latenza complessiva. Indipendentemente dal fatto che i database rimangano online durante questo passaggio, le connessioni continuano a essere indirizzate ai database nell'istanza di calcolo originale.

2. Passare il routing delle connessioni a una nuova istanza di calcolo

    Vengono eliminate le connessioni esistenti ai database nell'istanza di calcolo originale. Vengono stabilite nuove connessioni ai database nella nuova istanza di calcolo. Per alcune combinazioni di modifiche del livello di servizio e delle dimensioni di calcolo, i file di database vengono scollegati e ricollegati durante l'opzione.  Indipendentemente dall'opzione, l'opzione può causare una breve interruzione del servizio quando i database non sono disponibili in genere per meno di 30 secondi e spesso solo per pochi secondi. Se sono in esecuzione transazioni con esecuzione prolungata quando si eliminano le connessioni, la durata di questo passaggio potrebbe richiedere più tempo per recuperare le transazioni interrotte. Il [recupero accelerato del database](sql-database-accelerated-database-recovery.md) può ridurre l'effetto di un'interruzione delle transazioni a esecuzione prolungata.

> [!IMPORTANT]
> Durante qualsiasi passaggio del flusso di lavoro non viene perso alcun dato.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>Latenza di modifica del livello di servizio o di ridimensionamento delle dimensioni di calcolo

La latenza stimata per modificare il livello di servizio o ridimensionare le dimensioni di calcolo di un singolo database o di un pool elastico è parametrizzata come indicato di seguito:

|Livello di servizio|Database singolo di base,</br>Standard (S0-S1)|Pool elastico di base,</br>Standard (S2-S12), </br>Con iperscalabilità </br>per utilizzo generico database singolo o il pool elastico|Premium o business critical database singolo o pool elastico|
|:---|:---|:---|:---|
|**Database singolo Basic,</br> standard (S0-S1)**|&bull; &nbsp;latenza temporale costante indipendentemente dallo spazio utilizzato</br>&bull; &nbsp;in genere, meno di 5 minuti|&bull; &nbsp;latenza proporzionale allo spazio del database usato a causa della copia dei dati</br>&bull; &nbsp;in genere, meno di 1 minuto per GB di spazio usato|&bull; &nbsp;latenza proporzionale allo spazio del database usato a causa della copia dei dati</br>&bull; &nbsp;in genere, meno di 1 minuto per GB di spazio usato|
|**Pool elastico Basic, </br>standard (S2-S12), </br>iperscalabilità, </br>per utilizzo generico database singolo o pool elastico**|&bull; &nbsp;latenza proporzionale allo spazio del database usato a causa della copia dei dati</br>&bull; &nbsp;in genere, meno di 1 minuto per GB di spazio usato|&bull; &nbsp;latenza temporale costante indipendentemente dallo spazio utilizzato</br>&bull; &nbsp;in genere, meno di 5 minuti|&bull; &nbsp;latenza proporzionale allo spazio del database usato a causa della copia dei dati</br>&bull; &nbsp;in genere, meno di 1 minuto per GB di spazio usato|
|**Premium o business critical database singolo o pool elastico**|&bull; &nbsp;latenza proporzionale allo spazio del database usato a causa della copia dei dati</br>&bull; &nbsp;in genere, meno di 1 minuto per GB di spazio usato|&bull; &nbsp;latenza proporzionale allo spazio del database usato a causa della copia dei dati</br>&bull; &nbsp;in genere, meno di 1 minuto per GB di spazio usato|&bull; &nbsp;latenza proporzionale allo spazio del database usato a causa della copia dei dati</br>&bull; &nbsp;in genere, meno di 1 minuto per GB di spazio usato|

> [!NOTE]
>
> - In caso di modifica del livello di servizio o di ridimensionamento del calcolo per un pool elastico, è necessario usare la somma dello spazio usato in tutti i database nel pool per calcolare la stima.
> - Nel caso in cui si sposti un database da e verso un pool elastico, solo lo spazio usato dal database influisca sulla latenza, non sullo spazio usato dal pool elastico.
>
> [!TIP]
> Per monitorare le operazioni in corso, vedere: [gestire le operazioni tramite l'API REST SQL](https://docs.microsoft.com/rest/api/sql/operations/list), [gestire le operazioni tramite l'interfaccia](/cli/azure/sql/db/op)della riga di comando, [monitorare le operazioni usando T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) e questi due comandi di PowerShell: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) e [ Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>Considerazioni aggiuntive quando si modificano le dimensioni di calcolo del livello di servizio o di ridimensionamento

- Quando si esegue il ridimensionamento di Vcore o edtu per un pool elastico, lo spazio usato nel pool deve essere inferiore alla dimensione massima consentita per il livello di servizio e il pool edtu di destinazione.
- Quando si ridimensiona Vcore o edtu per un pool elastico, viene applicato un costo di archiviazione aggiuntivo se (1) le dimensioni massime di archiviazione del pool sono supportate dal pool di destinazione e (2) le dimensioni massime di archiviazione superano la quantità di spazio di archiviazione inclusa per il pool di destinazione. Se ad esempio un pool standard da 100 eDTU con una dimensione massima di 100 GB viene ridotto a un pool standard da 50 eDTU, viene applicato un costo per le risorse di archiviazione extra, poiché il pool di destinazione supporta una dimensione massima di 100 GB e lo spazio di archiviazione incluso è solo di 50 GB. Lo spazio di archiviazione extra è quindi 100 GB - 50 GB = 50 GB. Per i prezzi delle risorse di archiviazione extra, vedere [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/). Se la quantità effettiva di spazio usato è inferiore allo spazio di archiviazione incluso, questo costo aggiuntivo può essere evitato riducendo le dimensioni massime del database fino allo spazio incluso.

### <a name="billing-during-rescaling"></a>Fatturazione durante il ridimensionamento

Viene fatturata ogni ora per cui un database esiste usando il livello di servizio più elevato e le dimensioni di calcolo applicati in quell'ora, indipendentemente dall'uso o dal fatto che il database sia stato attivo per meno di un'ora. Ad esempio, se si crea un database singolo che viene eliminato cinque minuti dopo, in fattura viene riportato l'addebito relativo a un'ora di database.

## <a name="change-elastic-pool-storage-size"></a>modifica delle dimensioni di archiviazione del pool elastico

> [!IMPORTANT]
> In alcune circostanze, può essere necessario compattare un database per recuperare spazio inutilizzato. Per altre informazioni, vedere [Gestire lo spazio file nel database SQL di Azure](sql-database-file-space-management.md).

### <a name="vcore-based-purchasing-model"></a>Modello di acquisto basato su vCore

- Il provisioning dell'archiviazione può essere effettuato fino al limite massimo di dimensioni:

  - Per l'archiviazione nei livelli di servizio per utilizzo generico o standard, aumentare o ridurre le dimensioni in incrementi di 10 GB
  - Per l'archiviazione nei livelli di servizio premium o business critical, aumentare o ridurre le dimensioni in incrementi di 250 GB
- Il provisioning dell'archiviazione per un pool elastico può essere effettuato aumentandone o diminuendone le dimensioni massime.
- Il prezzo dell'archiviazione per un pool elastico corrisponde allo spazio di archiviazione moltiplicato per il prezzo unitario dell'archiviazione del livello di servizio. Per informazioni dettagliate sul prezzo delle risorse di archiviazione extra, vedere [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> In alcune circostanze, può essere necessario compattare un database per recuperare spazio inutilizzato. Per altre informazioni, vedere [Gestire lo spazio file nel database SQL di Azure](sql-database-file-space-management.md).

### <a name="dtu-based-purchasing-model"></a>modello di acquisto basato su DTU

- Il prezzo eDTU per un pool elastico include una determinata quantità di risorse di archiviazione senza costi aggiuntivi. Le risorse di archiviazione extra rispetto alla quantità inclusa possono essere sottoposte a provisioning per un costo aggiuntivo fino alla quantità massima in incrementi di 250 GB fino a 1 TB e quindi in incrementi di 256 GB oltre 1 TB. Per gli spazi di archiviazione inclusi e i limiti massimi relativi alle dimensioni, vedere [Pool elastico: dimensioni di archiviazione e dimensioni di calcolo](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).
- Le risorse di archiviazione extra per un pool elastico possono essere sottoposte a provisioning aumentandone le dimensioni massime mediante il [portale di Azure](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool), l'[interfaccia della riga di comando di Azure](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update) o l'[API REST](https://docs.microsoft.com/rest/api/sql/elasticpools/update).
- Il prezzo delle risorse di archiviazione extra per un pool elastico corrisponde allo spazio di archiviazione extra moltiplicato per il prezzo unitario del livello di servizio. Per informazioni dettagliate sul prezzo delle risorse di archiviazione extra, vedere [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> In alcune circostanze, può essere necessario compattare un database per recuperare spazio inutilizzato. Per altre informazioni, vedere [Gestire lo spazio file nel database SQL di Azure](sql-database-file-space-management.md).

## <a name="next-steps"></a>Passaggi successivi

Per i limiti generali delle risorse, vedere [Limiti delle risorse basate su vCore per il database SQL - pool elastici](sql-database-vcore-resource-limits-elastic-pools.md) e [Limiti delle risorse basate su DTU per il database SQL - pool elastici](sql-database-dtu-resource-limits-elastic-pools.md).
