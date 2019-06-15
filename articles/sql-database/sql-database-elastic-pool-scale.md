---
title: Ridimensionare le risorse dei pool elastici - Database SQL di Azure | Microsoft Docs
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
manager: craigg
ms.date: 3/14/2019
ms.openlocfilehash: d8aaf51c836a8e88c4e9b92798067167cd044e72
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60848086"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Ridimensionare le risorse dei pool elastici nel database SQL di Azure

Questo articolo illustra come ridimensionare le risorse di calcolo e di archiviazione disponibili per i pool elastici e i database in pool nel database SQL di Azure.

## <a name="change-compute-resources-vcores-or-dtus"></a>Modifica le risorse di calcolo (Vcore o Dtu)

Dopo aver selezionato inizialmente il numero di Edtu o Vcore, è possibile ridimensionare un pool elastico verso l'alto o verso il basso in modo dinamico base effettiva esperienza tramite il [portale di Azure](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool), il [CLI di Azure ](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update), o il [API REST](https://docs.microsoft.com/rest/api/sql/elasticpools/update).

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>Impatto della modifica delle dimensioni di calcolo ridimensionamento o un livello di servizio

Modifica del servizio livello o di calcolo delle dimensioni di un pool elastico segue un modello simile a quelli di database singoli e principalmente prevede che il servizio eseguendo i passaggi seguenti:

1. Creare una nuova istanza di calcolo per il pool elastico  

    Viene creata una nuova istanza di calcolo per il pool elastico con il livello di servizio di richiesta e le dimensioni di calcolo. Per alcune combinazioni di livello di servizio e delle modifiche alle dimensioni di calcolo, una replica di ogni database deve essere creata in una nuova istanza di calcolo che comporta la copia dei dati e può influire pesantemente sulla latenza complessiva. Indipendentemente da ciò, i database rimangono online durante questo passaggio e continuano le connessioni essere reindirizzati ai database nell'istanza di calcolo originale.

2. Passare il routing delle connessioni alla nuova istanza di calcolo

    Le connessioni esistenti ai database nell'istanza originale di calcolo vengono eliminate. Tutte le nuove connessioni vengono stabilite con i database nella nuova istanza di calcolo. Per alcune combinazioni di livello di servizio e delle modifiche alle dimensioni di calcolo, file di database sono scollegati e ricollegati durante l'operazione switch.  Indipendentemente da ciò, l'opzione può causare una breve interruzione del servizio quando i database non sono disponibili a livello generale per meno di 30 secondi e spesso solo alcuni secondi. Se sono presenti con esecuzione prolungata le transazioni in esecuzione quando le connessioni vengono rimosse, la durata di questo passaggio può richiedere più tempo per eseguire il ripristino transazioni interrotte. [Accelerazione di recupero del Database](sql-database-accelerated-database-recovery.md) può ridurre l'impatto di interruzione in corso transazioni a esecuzione prolungata.

> [!IMPORTANT]
> Durante un qualsiasi passaggio nel flusso di lavoro viene perso alcun dato.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>Latenza di modifica delle dimensioni di calcolo ridimensionamento o un livello di servizio

La latenza per modificare il livello di servizio o ridimensionare le dimensioni di calcolo di un database singolo o pool elastico con i parametri come indicato di seguito:

|Livello di servizio|Database singolo Basic,</br>Standard (S0-S1)|Pool elastici Basic,</br>Standard (S2-S12), </br>Con Iperscalabilità, </br>Database singolo a scopo generale o il pool elastico|Premium o Business Critical database singolo o pool elastico|
|:---|:---|:---|:---|
|**Database singolo Basic</br> Standard (S0-S1)**|&bull; &nbsp;Latenza tempo costante indipendente dello spazio utilizzato</br>&bull; &nbsp;In genere meno di 5 minuti|&bull; &nbsp;Latenza proporzionale allo spazio del database utilizzato destinata alla copia dei dati</br>&bull; &nbsp;In genere meno di 1 minuto per ogni GB di spazio utilizzato|&bull; &nbsp;Latenza proporzionale allo spazio del database utilizzato destinata alla copia dei dati</br>&bull; &nbsp;In genere meno di 1 minuto per ogni GB di spazio utilizzato|
|**Pool elastici Basic, </br>Standard (S2-S12), </br>con Iperscalabilità e </br>generico singolo database o pool elastico**|&bull; &nbsp;Latenza proporzionale allo spazio del database utilizzato destinata alla copia dei dati</br>&bull; &nbsp;In genere meno di 1 minuto per ogni GB di spazio utilizzato|&bull; &nbsp;Latenza tempo costante indipendente dello spazio utilizzato</br>&bull; &nbsp;In genere meno di 5 minuti|&bull; &nbsp;Latenza proporzionale allo spazio del database utilizzato destinata alla copia dei dati</br>&bull; &nbsp;In genere meno di 1 minuto per ogni GB di spazio utilizzato|
|**Premium o Business Critical database singolo o pool elastico**|&bull; &nbsp;Latenza proporzionale allo spazio del database utilizzato destinata alla copia dei dati</br>&bull; &nbsp;In genere meno di 1 minuto per ogni GB di spazio utilizzato|&bull; &nbsp;Latenza proporzionale allo spazio del database utilizzato destinata alla copia dei dati</br>&bull; &nbsp;In genere meno di 1 minuto per ogni GB di spazio utilizzato|&bull; &nbsp;Latenza proporzionale allo spazio del database utilizzato destinata alla copia dei dati</br>&bull; &nbsp;In genere meno di 1 minuto per ogni GB di spazio utilizzato|

> [!NOTE]
>
> - In caso di modifica del livello di servizio o ridimensionamento delle risorse di calcolo per un pool elastico, la somma dello spazio utilizzato in tutti i database nel pool di deve essere utilizzata per calcolare la stima.
> - Nel caso di spostamento di un database da e verso un pool elastico, solo lo spazio utilizzato dal database influisce sulla latenza, non lo spazio usato dal pool elastico.
>
> [!TIP]
> Per monitorare le operazioni in corso, vedere: [Gestire le operazioni tramite l'API REST SQL](https://docs.microsoft.com/rest/api/sql/operations/list), [Gestire le operazioni tramite l'interfaccia della riga di comando](/cli/azure/sql/db/op), [Monitorare le operazioni tramite T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) e i due comandi PowerShell seguenti: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) e [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>Considerazioni aggiuntive durante la modifica del servizio livello o ridimensionamento delle dimensioni di calcolo

- Quando si passa Vcore o Edtu per un pool elastico, lo spazio del pool usato deve essere minore di dimensioni massime consentite di Edtu e pool il servizio di destinazione.
- Durante il ridimensionamento delle Edtu per un pool elastico o Vcore, applicato un costo di archiviazione extra se (1) le dimensioni massime di archiviazione del pool sono supportata dal pool di destinazione e (2) le dimensioni massime di archiviazione superano la quantità inclusa del pool di destinazione. Se ad esempio un pool standard da 100 eDTU con una dimensione massima di 100 GB viene ridotto a un pool standard da 50 eDTU, viene applicato un costo per le risorse di archiviazione extra, poiché il pool di destinazione supporta una dimensione massima di 100 GB e lo spazio di archiviazione incluso è solo di 50 GB. Lo spazio di archiviazione extra è quindi 100 GB - 50 GB = 50 GB. Per i prezzi delle risorse di archiviazione extra, vedere [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/). Se la quantità effettiva di spazio usato è inferiore allo spazio di archiviazione incluso, questo costo aggiuntivo può essere evitato riducendo le dimensioni massime del database fino allo spazio incluso.

### <a name="billing-during-rescaling"></a>Durante il ridimensionamento di fatturazione

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
