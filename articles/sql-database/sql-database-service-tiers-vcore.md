---
title: Servizio del database SQL di Azure - vCore| Microsoft Docs
description: Il modello di acquisto basato su vCore consente di ridimensionare in modo indipendente le risorse di calcolo e archiviazione, le prestazioni locali e l'ottimizzazione del prezzo.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 06/26/2019
ms.openlocfilehash: c35863ed1d564adf4190efa1888d24f4f4f68ddf
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147849"
---
# <a name="choose-among-the-vcore-service-tiers-and-migrate-from-the-dtu-service-tiers"></a>Scegliere tra i livelli di servizio vCore ed eseguire la migrazione dai livelli di servizio DTU

Il modello di acquisto basato su Virtual Core (vCore) consente di ridimensionare in modo indipendente le risorse di calcolo e di archiviazione, trovare le prestazioni locali e ottimizzare il prezzo. Consente inoltre di scegliere la generazione di hardware:

- **Gen4**: Fino a 24 CPU logiche basate sui processori Intel E5-2673 V3 (Haswell) a 2,4 GHz, vCore = 1 PP (core fisico), 7 GB per core, SSD collegata
- **Gen5**: Fino a 80 CPU logiche basate sui processori Intel E5-2673 V4 (Broadwell) 2,3-GHz, vCore = 1 LP (Hyper-thread), 5,1 GB per core, SSD eNVM veloce

L'hardware Gen4 offre molta più memoria per ogni vCore. L'hardware Gen5 consente tuttavia di aumentare molto di più le risorse di calcolo.

> [!IMPORTANT]
> I nuovi database Gen4 non sono più supportati nelle aree dell'Australia orientale o del Brasile meridionale.
> [!NOTE]
> Per informazioni sui livelli di servizio basati su DTU, vedere [livelli di servizio per il modello di acquisto basato su DTU](sql-database-service-tiers-dtu.md). Per informazioni sulle differenze tra i livelli di servizio per i modelli di acquisto basati su DTU e vCore, vedere [modelli di acquisto del database SQL di Azure](sql-database-purchase-models.md).

## <a name="service-tier-characteristics"></a>Caratteristiche del livello di servizio

Il modello di acquisto basato su vCore offre tre livelli di servizio: utilizzo generico, iperscalabilità e business critical. Questi livelli di servizio si differenziano in base a una gamma di dimensioni di calcolo, progettazioni a disponibilità elevata, metodi di isolamento degli errori, tipi e dimensioni di archiviazione e intervalli di I/O.

È necessario configurare separatamente le risorse di archiviazione e il periodo di conservazione richiesti per i backup. Per impostare il periodo di conservazione del backup, aprire il portale di Azure, passare al server (non al database) e quindi fare clic su **Gestisci backup** > **Configura criterio** > **ripristino temporizzato configurazione** > **7- 35 giorni**.

La tabella seguente illustra le differenze tra i tre livelli:

||**Utilizzo generico**|**Business critical**|**Con iperscalabilità**|
|---|---|---|---|
|Ideale per|La maggior parte dei carichi di lavoro aziendali. Offre opzioni di calcolo e archiviazione orientate al budget, bilanciate e scalabili.|Applicazioni aziendali con requisiti di I/O elevati. Offre la massima resilienza agli errori usando diverse repliche isolate.|La maggior parte dei carichi di lavoro aziendali con requisiti di archiviazione e scalabilità a scalabilità elevata.|
|Calcolo|**Calcolo**con provisioning:<br/>Quarta generazione: da 1 a 24 vcore<br/>Quinta generazione: da 2 a 80 vcore<br/>**Calcolo senza server**:<br/>Quinta generazione: 0,5-16 vcore|**Calcolo**con provisioning:<br/>Quarta generazione: da 1 a 24 vcore<br/>Quinta generazione: da 2 a 80 vcore|**Calcolo**con provisioning:<br/>Quarta generazione: da 1 a 24 vcore<br/>Quinta generazione: da 2 a 80 vcore|
|Memoria|**Calcolo**con provisioning:<br/>Quarta generazione: 7 GB per vCore<br/>Quinta generazione: 5,1 GB per vCore<br/>**Calcolo senza server**:<br/>Quinta generazione: 3 GB per vCore|**Calcolo**con provisioning:<br/>Quarta generazione: 7 GB per vCore<br/>Quinta generazione: 5,1 GB per vCore |**Calcolo**con provisioning:<br/>Quarta generazione: 7 GB per vCore<br/>Quinta generazione: 5,1 GB per vCore|
|Archiviazione|Usa l'archiviazione remota.<br/>Calcolo con provisioning a **database singolo**:<br/>5 GB - 4 TB<br/>**Calcolo senza server a database singolo**:<br/>5 GB-1 TB<br/>**Istanza gestita**: 32 GB - 8 TB |Usa l'archiviazione SSD locale.<br/>Calcolo con provisioning a **database singolo**:<br/>5 GB - 4 TB<br/>**Istanza gestita**:<br/>32 GB - 4 TB |Aumento automatico delle dimensioni dello spazio di archiviazione in base alle esigenze. Supporta fino a 100 TB di spazio di archiviazione. Usa l'archiviazione SSD locale per la cache locale del pool di buffer e l'archiviazione dei dati locali. Usa l'archiviazione remota di Azure come archivio dati finale a lungo termine. |
|Velocità effettiva I/O (approssimativa)|**Database singolo**: 500 IOPS per vCore con 7000 numero massimo di IOPS.<br/>**Istanza gestita**: Dipende dalle [dimensioni del file](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes).|5000 operazioni di I/O al secondo per core fino a un massimo di 200.000|La funzionalità iperscalabile è un'architettura a più livelli con memorizzazione nella cache a più livelli. Gli IOPs effettivi dipendono dal carico di lavoro.|
|Disponibilità|1 replica, nessuna replica con scalabilità in lettura|3 repliche, 1 [replica scalabilità in lettura](sql-database-read-scale-out.md),<br/>disponibilità elevata con ridondanza della zona (HA)|1 replica di lettura/scrittura, più 0-4 repliche con scalabilità in [lettura](sql-database-read-scale-out.md)|
|Backup|[Archiviazione con ridondanza geografica e accesso in lettura (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 giorni (7 giorni per impostazione predefinita)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), da 7 a 35 giorni (7 giorni per impostazione predefinita)|Backup basati su snapshot nell'archiviazione remota di Azure. Questi snapshot vengono usati per il ripristino rapido. I backup sono istantanei e non influiscano sulle prestazioni di I/O di calcolo. I ripristini sono veloci e non sono un'operazione di dimensioni dei dati (che richiede minuti anziché ore o giorni).|
|In memoria|Non supportate|Supportato|Non supportate|
|||

> [!NOTE]
> È possibile ottenere un database SQL di Azure gratuito a livello di servizio Basic insieme a un account Azure gratuito. Per altre informazioni, vedere [creare un database cloud gestito con il proprio account Azure gratuito](https://azure.microsoft.com/free/services/sql-database/).

- Per altre informazioni sui limiti delle risorse di vCore, vedere [limiti delle risorse di vCore in un database singolo](sql-database-vcore-resource-limits-single-databases.md) e [limiti delle risorse vCore in un'istanza gestita](sql-database-managed-instance.md#vcore-based-purchasing-model).
- Per ulteriori informazioni sui livelli di servizio per utilizzo generico e business critical, vedere [livelli di servizio per utilizzo generico e business critical](sql-database-service-tiers-general-purpose-business-critical.md).
- Per altre informazioni sul livello di servizio con iperscalabilità nel modello di acquisto basato su vCore, vedere [livello di servizio](sql-database-service-tier-hyperscale.md)con scalabilità automatica.  

## <a name="azure-hybrid-benefit"></a>Vantaggio Azure Hybrid

Nel livello di calcolo di cui è stato effettuato il provisioning del modello di acquisto basato su vCore, è possibile scambiare le licenze esistenti con tariffe scontate nel database SQL usando [vantaggio Azure Hybrid per SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Questo vantaggio di Azure ti permette di risparmiare fino al 30% sul database SQL di Azure usando le tue licenze SQL Server locali con Software Assurance.

![prezzi](./media/sql-database-service-tiers/pricing.png)

Con Vantaggio Azure Hybrid è possibile scegliere di pagare solo per l'infrastruttura di Azure sottostante usando la licenza di SQL Server esistente per il motore di database SQL stesso (prezzi di calcolo di base) oppure è possibile pagare sia per l'infrastruttura sottostante che per il SQL Server licenza (prezzo incluso in licenza).

È possibile scegliere o modificare il modello di licenza usando il portale di Azure o usando una delle API seguenti:

- Per impostare o aggiornare il tipo di licenza usando PowerShell:

  - [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
  - [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
  - [New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)
  - [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)

- Per impostare o aggiornare il tipo di licenza usando l'interfaccia della riga di comando di Azure:

  - [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)
  - [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)
  - [az sql mi create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create)
  - [az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)

- Per impostare o aggiornare il tipo di licenza usando l'API REST:

  - [Database: crea o aggiorna](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)
  - [Databases - Update](https://docs.microsoft.com/rest/api/sql/databases/update)
  - [Managed Instances - Create Or Update](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)
  - [Managed Instances - Update](https://docs.microsoft.com/rest/api/sql/managedinstances/update)

## <a name="migrate-from-the-dtu-based-model-to-the-vcore-based-model"></a>Eseguire la migrazione dal modello basato su DTU al modello basato su vCore

### <a name="migrate-a-database"></a>Eseguire la migrazione di un database

La migrazione di un database dal modello di acquisto basato su DTU al modello di acquisto basato su vCore è simile all'aggiornamento o al downgrade tra i livelli di servizio standard e Premium nel modello di acquisto basato su DTU.

### <a name="migrate-databases-with-geo-replication-links"></a>Eseguire la migrazione dei database con collegamenti di replica geografica

La migrazione dal modello basato su DTU al modello di acquisto basato su vCore è simile all'aggiornamento o al downgrade delle relazioni di replica geografica tra i database nei livelli di servizio standard e Premium. Durante la migrazione non è necessario arrestare la replica geografica, ma è necessario seguire le regole di sequenziazione seguenti:

- Quando si esegue l'aggiornamento, è necessario aggiornare prima il database secondario e dopo il database primario.
- Quando si esegue il downgrade, è necessario seguire l'ordine inverso, ovvero eseguire prima il downgrade del database primario e dopo quello del database secondario.

Quando si usa la replica geografica tra due pool elastici, è consigliabile designare un pool come primario e l'altro come secondario. In tal caso, quando si esegue la migrazione dei pool elastici è necessario usare le stesse linee guida per la sequenziazione. Tuttavia, se si dispone di pool elastici che contengono sia database primari che secondari, trattare il pool con l'utilizzo più elevato come primario e seguire le regole di sequenziazione di conseguenza.  

La tabella seguente fornisce indicazioni per scenari di migrazione specifici:

|Livello di servizio corrente|Livello di servizio di destinazione|Tipo di migrazione|Azioni utente|
|---|---|---|---|
|Standard|Scopo generico|Laterale|L'ordine di migrazione non è rilevante, ma è necessario verificare che il numero di vCore sia appropriato*|
|Premium|Business Critical|Laterale|L'ordine di migrazione non è rilevante, ma è necessario verificare che il numero di vCore sia appropriato*|
|Standard|Business Critical|Aggiornamento|È necessario eseguire prima la migrazione del database secondario|
|Business Critical|Standard|Downgrade|È necessario eseguire prima la migrazione del database primario|
|Premium|Scopo generico|Downgrade|È necessario eseguire prima la migrazione del database primario|
|Scopo generico|Premium|Aggiornamento|È necessario eseguire prima la migrazione del database secondario|
|Business Critical|Utilizzo generico|Downgrade|È necessario eseguire prima la migrazione del database primario|
|Utilizzo generico|Business Critical|Aggiornamento|È necessario eseguire prima la migrazione del database secondario|
||||

\*Ogni 100 DTU nel livello standard richiede almeno 1 vCore e ogni 125 DTU nel livello Premium richiede almeno 1 vCore.

### <a name="migrate-failover-groups"></a>Eseguire la migrazione dei gruppi di failover

Per i gruppi di failover con più database è necessario eseguire separatamente la migrazione dei database primari e secondari. Durante questo processo sono valide le stesse considerazioni e regole di sequenziazione. Dopo la conversione dei database nel modello di acquisto basato su vCore, il gruppo di failover rimarrà in vigore con le stesse impostazioni dei criteri.

### <a name="create-a-geo-replication-secondary-database"></a>Creare un database secondario con replica geografica

È possibile creare un database secondario con replica geografica, ovvero una replica geografica secondaria, solo usando lo stesso livello di servizio usato per il database primario. Per i database con una frequenza di generazione dei log elevata, è consigliabile creare la replica geografica secondaria con le stesse dimensioni di calcolo della replica primaria.

Se si sta creando una replica geografica secondaria nel pool elastico per un singolo database primario, assicurarsi che `maxVCore` l'impostazione per il pool corrisponda alle dimensioni di calcolo del database primario. Se si crea un database di replica geografica secondario per un database primario in un altro pool elastico, è consigliabile che `maxVCore` i pool abbiano le stesse impostazioni.

### <a name="use-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Usare la copia del database per convertire un database basato su DTU in un database basato su vCore

È possibile copiare un database con dimensioni di calcolo basate su DTU in un database con dimensioni di calcolo basate su vCore senza restrizioni o particolari regole di sequenziazione a condizione che le dimensioni di calcolo del database di destinazione supportino le dimensioni massime del database di origine. La copia del database crea uno snapshot dei dati a partire dall'ora di inizio dell'operazione di copia e non sincronizza i dati tra l'origine e la destinazione.

## <a name="next-steps"></a>Passaggi successivi

- Per le opzioni di calcolo e dimensioni di archiviazione specifiche disponibili per i singoli database, vedere [limiti delle risorse basate su vCore del database SQL per database singoli](sql-database-vcore-resource-limits-single-databases.md).
- Per le opzioni di calcolo e dimensioni di archiviazione specifiche disponibili per i pool elastici, vedere [limiti delle risorse basate su vCore del database SQL per i pool elastici](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes).
