---
title: Servizio del database SQL di Azure - vCore| Microsoft Docs
description: Il modello di acquisto basato su vCore consente di ridimensionare le risorse di calcolo e archiviazione in modo indipendente, corrispondono prestazioni locali e ottimizzare i costi.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
manager: craigg
ms.date: 05/06/2019
ms.openlocfilehash: 520dba611e6791fca990e21173424a914c3d8e14
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66693342"
---
# <a name="choose-among-the-vcore-service-tiers-and-migrate-from-the-dtu-service-tiers"></a>Scegliere tra i livelli di servizio vCore ed eseguire la migrazione dai livelli di servizio DTU

La memoria centrale virtuale (vCore)-modello di acquisto basato su consente di ridimensionare le risorse di calcolo e archiviazione in modo indipendente, corrispondono prestazioni locali e ottimizzare i costi. Consente inoltre di scegliere la generazione di hardware:

- **Gen4**: Fino a 24 CPU logiche basati su Intel E5-2673 v3 processori 2,4 GHz (Haswell), vCore = 1 PP (core fisici), 7 GB per core, collegato a unità SSD
- **Gen5**: Fino a 80 CPU logiche basati su Intel E5-2673 v4 processori 2,3 GHz (Broadwell), vCore = 1 LP (hyper-thread), 5.1 GB per core, eNVM veloce SSD

L'hardware Gen4 offre molta più memoria per ogni vCore. L'hardware Gen5 consente tuttavia di aumentare molto di più le risorse di calcolo.

> [!NOTE]
> Per informazioni sui livelli di servizio basato su DTU, vedere [livelli per il modello di acquisto basato su DTU di servizio](sql-database-service-tiers-dtu.md). Per informazioni sulle differenze tra i livelli di servizio per il basato su DTU e i modelli di acquisto basato su vCore, vedere [Database SQL di Azure i modelli di acquisto](sql-database-purchase-models.md).

## <a name="service-tier-characteristics"></a>Caratteristiche di livello di servizio

Il modello di acquisto basato su vCore offre tre livelli di servizio: generico e con scalabilità elevatissima aziendale critica. Questi livelli di servizio si differenziano per un intervallo di dimensioni di calcolo, le progettazioni a disponibilità elevata, isolamento degli errori metodi, tipi e dimensioni di archiviazione e gli intervalli dei / o.

È necessario configurare separatamente le risorse di archiviazione e il periodo di conservazione richiesti per i backup. Per impostare il periodo di conservazione dei backup, aprire il portale di Azure, passare al server (non il database) e quindi andare al **Manage Backups** > **configurare un criterio**  >   **Punto In fase di ripristino della configurazione** > **7 a 35 giorni**.

Nella tabella seguente vengono illustrate le differenze tra i tre livelli:

||**Utilizzo generico**|**Aziendale critica**|**Su scala molto vasta**|
|---|---|---|---|
|Ideale per|La maggior parte dei carichi di lavoro aziendali. Calcolo orientate al budget, bilanciata e ridimensionabili offerte e opzioni di archiviazione.|Applicazioni aziendali con requisiti elevati dei / o. Offre massima resilienza agli errori tramite diverse repliche isolate.|La maggior parte delle business i carichi di lavoro con requisiti di scalabilità in lettura e un'archiviazione altamente scalabile.|
|Calcolo|**Il provisioning di calcolo**:<br/>Quarta generazione: numero di Vcore da 1 a 24<br/>Quinta generazione: numero di Vcore da 2 a 80<br/>**Calcolo senza server**:<br/>Quinta generazione: 0,5 - 4 Vcore per utilizzo|**Il provisioning di calcolo**:<br/>Quarta generazione: numero di Vcore da 1 a 24<br/>Quinta generazione: numero di Vcore da 2 a 80|**Il provisioning di calcolo**:<br/>Quarta generazione: numero di Vcore da 1 a 24<br/>Quinta generazione: numero di Vcore da 2 a 80|
|Memoria|**Il provisioning di calcolo**:<br/>Quarta generazione: 7 GB per vCore<br/>Quinta generazione: 5,1 GB per vCore<br/>**Calcolo senza server**:<br/>Quinta generazione: 3 GB per vCore|**Il provisioning di calcolo**:<br/>Quarta generazione: 7 GB per vCore<br/>Quinta generazione: 5,1 GB per vCore |**Il provisioning di calcolo**:<br/>Quarta generazione: 7 GB per vCore<br/>Quinta generazione: 5,1 GB per vCore|
|Archiviazione|Usa l'archiviazione remota.<br/>**Il provisioning di database singolo calcolo**:<br/>5 GB - 4 TB<br/>**Calcolo senza server di database singolo**:<br/>5 GB - 1 TB<br/>**Istanza gestita**: 32 GB - 8 TB |Usa l'archiviazione SSD locale.<br/>**Il provisioning di database singolo calcolo**:<br/>5 GB - 4 TB<br/>**Istanza gestita**:<br/>32 GB - 4 TB |Aumento automatico flessibile di archiviazione in base alle esigenze. Supporta fino a 100 TB di spazio di archiviazione. Usa l'archiviazione SSD locale per la cache del pool di buffer locale e archiviazione locale dei dati. Usa l'archiviazione remota Azure come archivio di dati a lungo termine finale. |
|Velocità effettiva dei / o (approssimativa)|**Database singolo**: 500 IOPS per ogni vCore con numero massimo di 7000 IOPS.<br/>**Istanza gestita**: Dipende [alle dimensioni dei file](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes).|5000 operazioni di I/O al secondo per core fino a un massimo di 200.000|Su scala molto vasta è un'architettura a più livelli con più livelli di memorizzazione nella cache. IOPs effettivo varia in base al carico di lavoro.|
|Disponibilità|1 replica, le repliche non scalabilità in lettura|3 repliche, 1 [replica scalabilità in lettura](sql-database-read-scale-out.md),<br/>con ridondanza della zona di disponibilità elevata (HA)|1 replica di lettura / scrittura, oltre a 0 e 4 [repliche con scalabilità in lettura](sql-database-read-scale-out.md)|
|Backup|[Archiviazione con ridondanza geografica e accesso in lettura (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md)7 a 35 giorni (7 giorni per impostazione predefinita),|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), da 7 a 35 giorni (7 giorni per impostazione predefinita)|Basato su snapshot di backup nella risorsa di archiviazione Azure remoto. Questi snapshot vengono usati per il ripristino rapido. I backup vengono visualizzati immediatamente e non compromettere le prestazioni dei / o di calcolo. I ripristini sono veloci e non sono un'operazione di dimensioni dei dati (prendendo minuti anziché ore o giorni).|
|In memoria|Non supportate|Supportato|Non supportate|
|||

> [!NOTE]
> È possibile ottenere un database SQL di Azure disponibile a livello di servizio di base in combinazione con un account Azure gratuito. Per altre informazioni, vedere [creare un database cloud gestito con il tuo account gratuito Azure](https://azure.microsoft.com/free/services/sql-database/).

- Per altre informazioni sui limiti delle risorse di vCore, vedere [vCore i limiti delle risorse in un database singolo](sql-database-vcore-resource-limits-single-databases.md) e [vCore i limiti delle risorse in un'istanza gestita](sql-database-managed-instance.md#vcore-based-purchasing-model).
- Per altre informazioni sull'utilizzo generico e livelli di servizio critici di business, vedere [livelli di servizio utilizzo generico e business critica](sql-database-service-tiers-general-purpose-business-critical.md).
- Per altre informazioni sul livello di servizio su scala molto vasta nel modello di acquisto basato su vCore, vedere [livello di servizio su scala molto vasta](sql-database-service-tier-hyperscale.md).  

## <a name="azure-hybrid-benefit"></a>Vantaggio Azure Hybrid

Nel livello di calcolo sottoposte a provisioning del modello di acquisto basato su vCore, è possibile scambiare le licenze esistenti con tariffe scontate su Database SQL per usando [vantaggio Azure Hybrid per SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Questo vantaggio di Azure consente di risparmiare fino al 30% sul Database SQL di Azure usando le tue licenze di SQL Server locali con Software Assurance.

![prezzi](./media/sql-database-service-tiers/pricing.png)

Con vantaggio Azure Hybrid, è possibile scegliere di pagare solo per l'infrastruttura di Azure sottostante utilizzando la licenza di SQL Server esistente per il motore di database SQL (prezzi di calcolo di Base), oppure è possibile pagare per l'infrastruttura sottostante e SQL Server licenze (licenza inclusa di prezzo).

È possibile scegliere o modificare il modello di licenza tramite il portale di Azure o usando una delle seguenti API:

- Per impostare o aggiornare il tipo di licenza con PowerShell:

  - [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
  - [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
  - [New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)
  - [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)

- Per impostare o aggiornare il tipo di licenza tramite la CLI di Azure:

  - [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)
  - [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)
  - [az sql mi create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create)
  - [az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)

- Per impostare o aggiornare il tipo di licenza con l'API REST:

  - [Database: crea o aggiorna](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)
  - [Databases - Update](https://docs.microsoft.com/rest/api/sql/databases/update)
  - [Managed Instances - Create Or Update](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)
  - [Managed Instances - Update](https://docs.microsoft.com/rest/api/sql/managedinstances/update)

## <a name="migrate-from-the-dtu-based-model-to-the-vcore-based-model"></a>Eseguire la migrazione dal modello basato su DTU per il modello basato su vCore

### <a name="migrate-a-database"></a>Eseguire la migrazione di un database

La migrazione di un database dal modello di acquisto basato su DTU per il modello di acquisto basato su vCore è simile all'aggiornamento o downgrade tra i livelli di servizio standard e premium nel modello di acquisto basato su DTU.

### <a name="migrate-databases-with-geo-replication-links"></a>Eseguire la migrazione di database con collegamenti di replica geografica

La migrazione dal modello basato su DTU per il modello di acquisto basato su vCore è simile all'aggiornamento o downgrade le relazioni di replica geografica tra i database nei livelli di servizio standard e premium. Durante la migrazione, non è necessario arrestare la replica geografica, ma devono rispettare queste regole di sequenziazione:

- Quando si esegue l'aggiornamento, è necessario aggiornare prima il database secondario e dopo il database primario.
- Quando si esegue il downgrade, è necessario seguire l'ordine inverso, ovvero eseguire prima il downgrade del database primario e dopo quello del database secondario.

Quando si usa la replica geografica tra due pool elastici, è consigliabile che designarne uno come primario e l'altro come secondario. In tal caso, quando si esegue la migrazione di pool elastici è consigliabile usare le stesse linee guida di sequenziazione. Tuttavia, se si dispone di pool elastici che contengono database primari e secondari, trattare il pool con l'utilizzo più elevato del database primario e seguire le regole di sequenziazione di conseguenza.  

Nella tabella seguente fornisce indicazioni per specifici scenari di migrazione:

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

\* Ogni 100 Dtu nel livello standard richiede almeno 1 vCore e ogni 125 Dtu nel livello premium richiede almeno 1 vCore.

### <a name="migrate-failover-groups"></a>Eseguire la migrazione di gruppi di failover

Per i gruppi di failover con più database è necessario eseguire separatamente la migrazione dei database primari e secondari. Durante questo processo sono valide le stesse considerazioni e regole di sequenziazione. Dopo la conversione dei database per il modello di acquisto basato su vCore, il gruppo di failover rimarranno in vigore con le stesse impostazioni di criteri.

### <a name="create-a-geo-replication-secondary-database"></a>Creare un database secondario con replica geografica

È possibile creare un database secondario con replica geografica (geo-secondary) solo usando lo stesso livello di servizio perché è usato per il database primario. Per i database con una frequenza elevata di generazione log, è consigliabile creare la replica geografica secondaria con le stesse dimensioni di calcolo del database primario.

Se si crea una replica geografica secondaria nel pool elastico per un singolo database primario, assicurarsi che il `maxVCore` impostazione per il pool corrisponde alla dimensione di calcolo del database primario. Se si crea una replica geografica secondaria per un database primario in un altro pool elastico, è consigliabile che i pool hanno gli stessi `maxVCore` impostazioni.

### <a name="use-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Usare copia del database per convertire un database basato su DTU a un database basato su vCore

È possibile copiare un database con dimensioni di calcolo basate su DTU in un database con dimensioni di calcolo basate su vCore senza restrizioni o particolari regole di sequenziazione a condizione che le dimensioni di calcolo del database di destinazione supportino le dimensioni massime del database di origine. La copia del database crea uno snapshot dei dati a partire da ora di inizio dell'operazione di copia e non sincronizza dati tra l'origine e destinazione.

## <a name="next-steps"></a>Passaggi successivi

- Per le dimensioni di calcolo specifico e opzioni relative alle dimensioni di archiviazione disponibile per i singoli database, vedere [limiti delle risorse basate su Vcore per Database SQL per database singoli](sql-database-vcore-resource-limits-single-databases.md).
- Per le dimensioni di calcolo specifico e opzioni relative alle dimensioni di archiviazione disponibile per i pool elastici, vedere [limiti delle risorse basate su Vcore per Database SQL per i pool elastici](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes).
