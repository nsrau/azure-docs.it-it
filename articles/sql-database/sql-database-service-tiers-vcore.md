---
title: Servizio del database SQL di Azure - vCore| Microsoft Docs
description: Il modello di acquisto basato su vCore consente di ridimensionare le risorse di calcolo e archiviazione in modo indipendente, soddisfare le esigenze di prestazioni locali e ottimizzare i costi.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: sashan, moslake
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: a14912c48f27c4f4d0e54239f8e0523ffa62324f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57888503"
---
# <a name="vcore-service-tiers-azure-hybrid-benefit-and-migration"></a>Livelli di servizio vCore, Vantaggio Azure Hybrid e migrazione

Il modello di acquisto basato su vCore consente di ridimensionare le risorse di calcolo e archiviazione in modo indipendente, soddisfare le esigenze di prestazioni locali e ottimizzare i costi. Permette anche di scegliere la generazione di hardware:

- Gen4 - fino a 24 CPU logiche basate su processori Intel E5-2673 v3 (Haswell) da 2.4 GHz, vCore = 1 PP (core fisici), 7 GB per core, collegato a unità SSD
- Gen5 - fino a 80 CPU logiche basate su processori Intel E5-2673 v4 (Broadwell) da 2.3 GHz, vCore = 1 LP (hyper-thread), 5.1 GB per core, velocità eNVM SSD

L'hardware Gen4 offre molta più memoria per ogni vCore. L'hardware Gen5 consente tuttavia di aumentare molto di più le risorse di calcolo.

> [!NOTE]
> Per informazioni sui livelli di servizio basati su DTU, vedere [Livelli di servizio basati su DTU](sql-database-service-tiers-dtu.md). Per informazioni sulle differenze tra i livelli di servizio basati su DTU e quelli basati su vCore, vedere [Modelli di acquisto del database SQL di Azure](sql-database-purchase-models.md).

## <a name="service-tier-characteristics"></a>Caratteristiche del livello di servizio

Il modello vCore offre tre livelli di servizio: Utilizzo generico, Hyperscale e Business Critical. I livelli di servizio si differenziano in base a dimensioni di calcolo, progettazione a disponibilità elevata, isolamento degli errori, tipi e dimensioni di archiviazione e velocità effettiva di I/O. È necessario configurare separatamente le risorse di archiviazione e il periodo di conservazione richiesti per i backup. Nel portale di Azure passare a Server (non il database) > Backup gestito > Configura criterio > Configurazione ripristino temporizzato > 7 - 35 giorni.

La tabella seguente consente di comprendere le differenze tra questi tre livelli:

||**Utilizzo generico**|**Business Critical**|**Iperscalabilità (anteprima)**|
|---|---|---|---|
|Ideale per|La maggior parte dei carichi di lavoro aziendali. Offre opzioni di calcolo e archiviazione scalabili e bilanciate a prezzi convenienti.|Applicazioni aziendali con requisiti di I/O elevati. Offre massima resilienza agli errori tramite diverse repliche isolate.|La maggior parte dei carichi di lavoro aziendali con archiviazione con scalabilità elevata e requisiti di scalabilità in lettura|
|Calcolo|Quarta generazione: da 1 a 24 vCore<br/>Quinta generazione: da 1 a 80 vCore|Quarta generazione: da 1 a 24 vCore<br/>Quinta generazione: da 1 a 80 vCore|Quarta generazione: da 1 a 24 vCore<br/>Quinta generazione: da 1 a 80 vCore|
|Memoria|Quarta generazione: 7 GB per core<br>Quinta generazione: 5,1 GB per core | Quarta generazione: 7 GB per core<br>Quinta generazione: 5,1 GB per core |Quarta generazione: 7 GB per core<br>Quinta generazione: 5,1 GB per core|
|Archiviazione|Usa l'archiviazione remota:<br/>Database singolo: 5 GB - 4 TB<br/>Istanza gestita: 32 GB - 8 TB |Usa l'archiviazione SSD locale:<br/>Database singolo: 5 GB - 4 TB<br/>Istanza gestita: 32 GB - 4 TB |Aumento automatico e flessibile dello spazio di archiviazione in base alle esigenze. Supporta fino a 100 TB di archiviazione e oltre. Archiviazione SSD locale per la cache del pool di buffer locale e l'archiviazione locale dei dati. Archiviazione remota di Azure come archivio dati a lungo termine finale. |
|Velocità effettiva di I/O (approssimativa)|Database singolo: 500 operazioni di I/O al secondo per vCore fino a un massimo di 7000</br>Istanza gestita: dipende dalle [dimensioni del file](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes)|5000 operazioni di I/O al secondo per core fino a un massimo di 200.000|Da definire|
|Disponibilità|1 replica, senza scalabilità in lettura|3 repliche, 1 [replica scalabilità in lettura](sql-database-read-scale-out.md),<br/>DISPONIBILITÀ ELEVATA con ridondanza|?|
|Backup|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), da 7 a 35 giorni (7 giorni per impostazione predefinita)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), da 7 a 35 giorni (7 giorni per impostazione predefinita)|Backup basato su snapshot nell'archiviazione remota di Azure e questi snapshot vengono usati per il ripristino rapido. I backup sono istantanei e non influiscono sulle prestazioni di I/O di calcolo. I ripristini sono molto veloci e non corrispondono a un'operazione di dimensionamento dei dati (impiegano pochi minuti invece di ore o giorni).|
|In memoria|Non supportate|Supportato|Non supportate|
|||

> [!NOTE]
> È possibile ottenere un database SQL di Azure disponibile a livello di servizio di base in combinazione con un account Azure gratuito a esplorare Azure. Per informazioni, vedere [Crea un database cloud gestito con il tuo account Azure gratuito](https://azure.microsoft.com/free/services/sql-database/).

- Per altre informazioni, vedere [Limiti delle risorse vCore nel database singolo](sql-database-vcore-resource-limits-single-databases.md) e [Limiti delle risorse vCore nell'istanza gestita](sql-database-managed-instance.md#vcore-based-purchasing-model).
- Per altre informazioni sui livelli di servizio Utilizzo generico e Business critical, vedere [Livelli di servizio Utilizzo generico e Business critical](sql-database-service-tiers-general-purpose-business-critical.md).
- Per informazioni dettagliate sul livello di servizio con iperscalabilità nel modello di acquisto basato su vCore, vedere [Livello di servizio con iperscalabilità (anteprima) per database fino a 100 TB](sql-database-service-tier-hyperscale.md).  

> [!IMPORTANT]
> Se per la capacità di calcolo è necessario meno di un vCore, usare il modello di acquisto basato su DTU.

## <a name="azure-hybrid-benefit"></a>Vantaggio Azure Hybrid

Nel modello di acquisto basato su vCore è possibile scambiare le licenze esistenti con tariffe scontate per il database SQL tramite il [Vantaggio Azure Hybrid per SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Questo vantaggio di Azure consente di risparmiare fino al 30% sul costo del database SQL di Azure usando le licenze locali di SQL Server con Software Assurance.

![prezzi](./media/sql-database-service-tiers/pricing.png)

Con Vantaggio Azure Hybrid, è possibile scegliere di pagare solo per l'infrastruttura sottostante di Azure usando la licenza SQL Server esistente per il motore di database SQL stesso (**BasePrice**) oppure di pagare per l'infrastruttura sottostante e per la licenza di SQL Server (**LicenseIncluded**). È possibile scegliere o modificare il modello di licenza nel portale di Azure o utilizzando una delle seguenti API.

- Per impostare o aggiornare il tipo di licenza con PowerShell:

  - [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase):
  - [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql)
  - [New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)
  - [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql)

- Per impostare o aggiornare il tipo di licenza con l'interfaccia della riga di comando di Azure:

  - [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)
  - [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)
  - [az sql mi create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create)
  - [az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)

- Per impostare o aggiornare il tipo di licenza con API REST:

  - [Database: crea o aggiorna](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)
  - [Databases - Update](https://docs.microsoft.com/rest/api/sql/databases/update)
  - [Managed Instances - Create Or Update](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)
  - [Managed Instances - Update](https://docs.microsoft.com/rest/api/sql/managedinstances/update)

## <a name="migration-from-dtu-model-to-vcore-model"></a>Migrazione dal modello basato su DTU al modello basato su vCore

### <a name="migration-of-a-database"></a>Migrazione di un database

La migrazione di un database dal modello di acquisto basato su DTU al modello di acquisto basato su vCore è un'operazione simile all'aggiornamento o al downgrade tra database Standard e Premium nel modello di acquisto basato su DTU.

### <a name="migration-of-databases-with-geo-replication-links"></a>Migrazione di database con collegamenti di replica geografica

La migrazione dal modello basato su DTU al modello basato su vCore è simile all'aggiornamento o downgrade delle relazioni di replica geografica tra i database Standard e Premium. Non è richiesta la terminazione della replica geografica, ma l'utente deve rispettare le regole di sequenziazione. Quando si esegue l'aggiornamento, è necessario aggiornare prima il database secondario e dopo il database primario. Quando si esegue il downgrade, è necessario seguire l'ordine inverso, ovvero eseguire prima il downgrade del database primario e dopo quello del database secondario.

Quando si usa la replica geografica tra due pool elastici, è consigliabile designarne uno come primario e l'altro come secondario. In questo caso, la migrazione dei pool elastici deve rispettare le stesse linee guida.  È tuttavia tecnicamente possibile che un pool elastico contenga sia il database primario sia quello secondario. In questo caso, per eseguire correttamente la migrazione, è necessario trattare come primario il pool con l'utilizzo più elevato e seguire le regole di sequenziazione di conseguenza.  

La tabella seguente contiene indicazioni per gli specifici scenari di migrazione:

|Livello di servizio corrente|Livello di servizio di destinazione|Tipo di migrazione|Azioni utente|
|---|---|---|---|
|Standard|Scopo generico|Laterale|L'ordine di migrazione non è rilevante, ma è necessario verificare che il numero di vCore sia appropriato*|
|Premium|Business Critical|Laterale|L'ordine di migrazione non è rilevante, ma è necessario verificare che il numero di vCore sia appropriato*|
|Standard|Business Critical|Aggiornamento|È necessario eseguire prima la migrazione del database secondario|
|Business Critical|Standard|Downgrade|È necessario eseguire prima la migrazione del database primario|
|Premium|Scopo generico|Downgrade|È necessario eseguire prima la migrazione del database primario|
|Scopo generico|Premium|Aggiornamento|È necessario eseguire prima la migrazione del database secondario|
|Business Critical|Scopo generico|Downgrade|È necessario eseguire prima la migrazione del database primario|
|Scopo generico|Business Critical|Aggiornamento|È necessario eseguire prima la migrazione del database secondario|
||||

\* È necessario almeno 1 vCore per ogni 100 DTU nel livello Standard e per ogni 125 DTU nel livello Premium

### <a name="migration-of-failover-groups"></a>Migrazione dei gruppi di failover

Per i gruppi di failover con più database è necessario eseguire separatamente la migrazione dei database primari e secondari. Durante questo processo sono valide le stesse considerazioni e regole di sequenziazione. Dopo la conversione dei database nel modello basato su vCore, il gruppo di failover rimane attivo con le stesse impostazioni di criteri.

### <a name="creation-of-a-geo-replication-secondary"></a>Creazione di un database di replica geografica secondario

È possibile creare un database di replica geografica secondario solo usando lo stesso livello di servizio del database primario. Per un database secondario con frequenza di generazione dei log elevata, è consigliabile configurare le stesse dimensioni di calcolo del database primario. Se nel pool elastico si crea un database di replica geografica secondario per un singolo database primario, è consigliabile definire per il pool un'impostazione di `maxVCore` corrispondente alle dimensioni di calcolo del database primario. Se nel pool elastico si crea un database di replica geografica secondario per un database primario incluso in un altro pool elastico, è consigliabile definire per i pool le stesse impostazioni di `maxVCore`

### <a name="using-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Uso della copia di database per convertire un database basato su DTU in un database basato su vCore

È possibile copiare un database con dimensioni di calcolo basate su DTU in un database con dimensioni di calcolo basate su vCore senza restrizioni o particolari regole di sequenziazione a condizione che le dimensioni di calcolo del database di destinazione supportino le dimensioni massime del database di origine. L'operazione di copia del database crea inizialmente uno snapshot dei dati e non esegue la sincronizzazione dei dati tra l'origine e la destinazione.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni dettagliate sulle dimensioni di calcolo specifiche e sulle opzioni relative alle dimensioni di archiviazione disponibili per database singoli, vedere [Limiti delle risorse basate su vCore del database SQL per database singoli](sql-database-vcore-resource-limits-single-databases.md#general-purpose-service-tier-storage-sizes-and-compute-sizes)
- Per informazioni dettagliate sulle dimensioni di calcolo specifiche e sulle opzioni relative alle dimensioni di archiviazione disponibili per i pool elastici, vedere [Limiti delle risorse basate su vCore del database SQL per i pool elastici](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes).
