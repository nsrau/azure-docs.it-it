---
title: Servizio del database SQL di Azure - vCore| Microsoft Docs
description: Il modello di acquisto basato su vCore consente di ridimensionare le risorse di calcolo e archiviazione in modo indipendente, soddisfare le esigenze di prestazioni locali e ottimizzare i costi.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: sashan, moslake
manager: craigg
ms.date: 09/26/2018
ms.openlocfilehash: 3fb1357b7a70579fa527a896d5bd359749b10ad6
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2018
ms.locfileid: "47407656"
---
# <a name="choosing-a-vcore-service-tier-compute-memory-storage-and-io-resources"></a>Scelta del livello di servizio vCore e delle risorse di calcolo, memoria, archiviazione e I/O

Il modello di acquisto basato su vCore consente di ridimensionare le risorse di calcolo e archiviazione in modo indipendente, soddisfare le esigenze di prestazioni locali e ottimizzare i costi. Permette anche di scegliere la generazione di hardware:
- Generazione 4 - fino a 24 CPU logiche basate su processori Intel E5-2673 v3 (Haswell) da 2,4 GHz, vCore = 1 PP (core fisici), 7 GB per core, collegato a unità SSD
- Generazione 5 - fino a 80 CPU logiche basate su processori Intel E5-2673 v4 (Broadwell) da 2,3 GHz, vCore = 1 LP (hyper-thread), 5.5. GB per core, SSD eNVM veloce

l modello vCore offre inoltre la possibilità di usare il [Vantaggio Azure Hybrid per SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md) per ottenere un risparmio sui costi.

## <a name="service-tier-characteristics"></a>Caratteristiche del livello di servizio

Il modello vCore offre due livelli di servizio, Utilizzo generico e Business Critical. I livelli di servizio si differenziano in base a dimensioni di calcolo, progettazione a disponibilità elevata, isolamento degli errori, tipi di archiviazione e velocità effettiva di I/O. Il cliente deve configurare separatamente le risorse di archiviazione e il periodo di conservazione richiesti per i backup.

La tabella seguente consente di comprendere le differenze tra questi due livelli:

||**Utilizzo generico**|**Business Critical**|**Iperscalabilità (anteprima)**|
|---|---|---|---|
|Ideale per|La maggior parte dei carichi di lavoro aziendali. Offre opzioni di calcolo e archiviazione scalabili e bilanciate a prezzi convenienti.|Applicazioni aziendali con requisiti di I/O elevati. Offre massima resilienza agli errori tramite diverse repliche isolate.|La maggior parte dei carichi di lavoro aziendali con archiviazione con scalabilità elevata e requisiti di scalabilità in lettura|
|Calcolo|Gen4: Da 1 a 24 vCore<br/>Gen5: Da 1 a 80 vCore|Gen4: Da 1 a 24 vCore<br/>Gen5: Da 1 a 80 vCore|Gen4: Da 1 a 24 vCore<br/>Gen5: Da 1 a 80 vCore|
|Memoria|4° generazione: 7 GB per core<br>5° generazione: 5,5 GB per core | 4° generazione: 7 GB per core<br>5° generazione: 5,5 GB per core |4° generazione: 7 GB per core<br>5° generazione: 5,5 GB per core|
|Archiviazione|[Archiviazione remota Premium](../virtual-machines/windows/premium-storage.md),<br/>Database singolo: da 5 GB a 4 TB<br/>Istanza gestita: da 32 GB a 8 TB |Archiviazione SSD locale,<br/>Database singolo: da 5 GB a 4 TB<br/>Istanza gestita: da 32 GB a 4 TB |Aumento automatico e flessibile delle dimensioni in base alle esigenze. Supporta fino a 100 TB di archiviazione e oltre. Archiviazione SSD locale per la cache del pool di buffer locale e l'archiviazione locale dei dati. Archiviazione remota di Azure come archivio dati a lungo termine finale. |
|Velocità effettiva di I/O (approssimativa)|Database singolo: 500 operazioni di I/O al secondo per vCore fino a un massimo di 7000</br>Istanza gestita: dipende dalle [dimensioni dei file](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes)|5000 operazioni di I/O al secondo per core fino a un massimo di 200.000|Da definire|
|Disponibilità|1 replica, senza scalabilità in lettura|3 repliche, 1 [replica scalabilità in lettura](sql-database-read-scale-out.md),<br/>DISPONIBILITÀ ELEVATA con ridondanza|?|
|Backup|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), da 7 a 35 giorni (7 giorni per impostazione predefinita)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), da 7 a 35 giorni (7 giorni per impostazione predefinita)|Backup basato su snapshot nell'archiviazione remota di Azure e questi snapshot vengono usati per il ripristino rapido. I backup sono istantanei e non influiscono sulle prestazioni di I/O di calcolo. I ripristini sono molto veloci e non sono delle stesse dimensioni delle operazioni sui dati (vengono eseguiti in pochi minuti, anziché in ore o giorni).|
|In memoria|Non supportate|Supportato|Non supportate|
|||

Per altre informazioni, vedere [Limiti delle risorse vCore nel database singolo](sql-database-vcore-resource-limits-single-databases.md) e [Limiti delle risorse vCore nell'istanza gestita](sql-database-managed-instance.md#vcore-based-purchasing-model). 

> [!IMPORTANT]
> Se per la capacità di calcolo è necessario meno di un vCore, usare il modello di acquisto basato su DTU.

Per le risposte alle domande più frequenti, vedere [Domande frequenti sul database SQL](sql-database-faq.md). 

## <a name="storage-considerations"></a>Considerazioni sulle risorse di archiviazione

### <a name="general-purpose-and-business-critical-service-tiers"></a>Livelli di servizio Utilizzo generico e Business critical
Valutare gli aspetti seguenti:
- Le risorse di archiviazione allocate vengono usate dai file di dati (MDF) e dai file di log (LDF).
- Ogni dimensione di calcolo del database singolo supporta una dimensione massima del database. La dimensione massima predefinita è di 32 GB.
- Quando si configura la dimensione del database singolo desiderata (dimensione dei file MDF), viene aggiunto automaticamente il 30% delle risorse di archiviazione per il supporto dei file LDF
- Le dimensioni di archiviazione in istanza gestita devono essere specificate in multipli di 32 GB.
- È possibile selezionare qualsiasi dimensione di database singolo compresa tra 10 GB e il valore massimo supportato
 - Per l'archiviazione Standard, aumentare o diminuire la dimensione in base a incrementi di 10 GB
 - Per l'archiviazione Premium, aumentare o diminuire la dimensione in base a incrementi di 250 GB
- Nel livello di servizio Utilizzo generico `tempdb` usa un'unità SSD collegata e questo costo di archiviazione è incluso nel prezzo del vCore.
- Nel livello di servizio Business Critical `tempdb` condivide l'unità SSD collegata con i file MDF e LDF e il costo di archiviazione di tempDB è incluso nel prezzo del vCore.

> [!IMPORTANT]
> Il costo addebitato dipende dalla quantità totale di risorse di archiviazione allocate per i file MDF e LDF.

Per monitorare la dimensione totale corrente dei file MDF e LDF, usare [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Per monitorare la dimensione corrente dei singoli file MDF e LDF, usare [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> In alcune circostanze, può essere necessario compattare un database per recuperare spazio inutilizzato. Per altre informazioni, vedere [Gestire lo spazio file nel database SQL di Azure](sql-database-file-space-management.md).

### <a name="hyperscale-service-tier-preview"></a>Livello di servizio con iperscalabilità (anteprima)

L'archiviazione è gestita automaticamente per il database con iperscalabilità. L'archiviazione aumenta in base alle esigenze. Archiviazione con "log infinito" in unità SSD veloci di Archiviazione di Azure Premium senza necessità di troncamento frequente dei log.

## <a name="backups-and-storage"></a>Backup e archiviazione

### <a name="general-purpose-and-business-critical-service-tiers"></a>Livelli di servizio Utilizzo generico e Business critical

Le risorse di archiviazione per i backup di database vengono allocate per supportare le funzionalità di ripristino temporizzato e di [conservazione a lungo termine](sql-database-long-term-retention.md) del database SQL. Queste risorse vengono allocate separatamente per ogni database e fatturate come due costi distinti. 

- **Ripristino temporizzato**: i singoli backup di database vengono copiati automaticamente in [risorse di archiviazione con ridondanza geografica e accesso in lettura](../storage/common/storage-designing-ha-apps-with-ragrs.md) (RA-GRS, Read-Access Geographically Redundant Storage). Le dimensioni di archiviazione aumentano dinamicamente con la creazione di nuovi backup.  Le risorse di archiviazione vengono usate da backup completi settimanali, backup differenziali giornalieri e backup del log delle transazioni copiati ogni 5 minuti. L'utilizzo delle risorse di archiviazione dipende dalla frequenza con cui vengono apportate modifiche al database e dal periodo di conservazione. È possibile configurare un periodo di conservazione separato per ogni database compreso tra 7 e 35 giorni. Una quantità minima di risorse di archiviazione, equivalente al 100% della dimensione dei dati, viene fornita senza costi aggiuntivi. Per la maggior parte dei database, questa quantità è sufficiente per un periodo di archiviazione dei backup di 7 giorni.
- **Conservazione a lungo termine**: il database SQL offre la possibilità di configurare la conservazione a lungo termine dei backup completi fino a 10 anni. Se i criteri di conservazione a lungo termine sono abilitati, questi backup vengono archiviati automaticamente in risorse di archiviazione RA-GRS, ma è possibile controllare la frequenza con cui vengono copiati. A secondo dei vari requisiti di conformità, è possibile selezionare periodi di conservazione diversi per i backup settimanali, mensili e/o annuali. Questa configurazione definirà la quantità di risorse di archiviazione usate per i backup con conservazione a lungo termine. È possibile usare lo strumento di calcolo dei prezzi per la conservazione a lungo termine per stimare il costo di questo tipo di archiviazione. Per altre informazioni, vedere [Long-term retention](sql-database-long-term-retention.md) (Conservazione a lungo termine).

### <a name="hyperscale-service-tier-preview"></a>Livello di servizio con iperscalabilità (anteprima)

Nel livello di servizio con iperscalabilità, i backup sono basati su snapshot e archiviati nella risorsa di archiviazione remota di Azure. Questi snapshot vengono usati per il ripristino rapido. I backup sono istantanei e non influiscono sulle prestazioni di I/O di calcolo. I ripristini sono molto veloci e non sono delle stesse dimensioni delle operazioni sui dati (vengono eseguiti in pochi minuti, anziché in ore o giorni).

## <a name="azure-hybrid-use-benefit"></a>Vantaggio Azure Hybrid Use

Nel modello di acquisto basato su vCore è possibile scambiare le licenze esistenti con tariffe scontate per il database SQL tramite il [Vantaggio Azure Hybrid per SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md). Questo vantaggio di Azure consente di risparmiare fino al 30% sul costo del database SQL di Azure usando le licenze locali di SQL Server con Software Assurance.

![prezzi](./media/sql-database-service-tiers/pricing.png)

## <a name="migration-from-dtu-model-to-vcore-model"></a>Migrazione dal modello basato su DTU al modello basato su vCore

### <a name="migration-of-single-databases-with-geo-replication-links"></a>Migrazione di database singoli con collegamenti di replica geografica

La migrazione dal modello basato su DTU a quello basato su vCore o viceversa è simile all'aggiornamento o al downgrade delle relazioni di replica geografica tra i database Standard e Premium. Non è richiesta la terminazione della replica geografica, ma l'utente deve rispettare le regole di sequenziazione. Quando si esegue l'aggiornamento, è necessario aggiornare prima il database secondario e dopo il database primario. Quando si esegue il downgrade, è necessario seguire l'ordine inverso, ovvero eseguire prima il downgrade del database primario e dopo quello del database secondario. 

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

\* È necessario almeno 1 vCore per ogni 100 DTU nel livello Standard e per ogni 125 DTU nel livello Premium.

### <a name="migration-of-failover-groups"></a>Migrazione dei gruppi di failover 

Per i gruppi di failover con più database è necessario eseguire separatamente la migrazione dei database primari e secondari. Durante questo processo sono valide le stesse considerazioni e regole di sequenziazione. Dopo la conversione dei database nel modello basato su vCore, il gruppo di failover rimane attivo con le stesse impostazioni di criteri. 

### <a name="creation-of-a-geo-replication-secondary"></a>Creazione di un database di replica geografica secondario

È possibile creare un database di replica geografica secondario solo usando lo stesso livello di servizio del database primario. Per un database secondario con frequenza di generazione dei log elevata, è consigliabile configurare le stesse dimensioni di calcolo del database primario. Se nel pool elastico si crea un database di replica geografica secondario per un singolo database primario, è consigliabile definire per il pool un'impostazione di `maxVCore` corrispondente alle dimensioni di calcolo del database primario. Se nel pool elastico si crea un database di replica geografica secondario per un database primario incluso in un altro pool elastico, è consigliabile definire per i pool le stesse impostazioni di `maxVCore`.

### <a name="using-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Uso della copia di database per convertire un database basato su DTU in un database basato su vCore

È possibile copiare un database con dimensioni di calcolo basate su DTU in un database con dimensioni di calcolo basate su vCore senza restrizioni o particolari regole di sequenziazione a condizione che le dimensioni di calcolo del database di destinazione supportino le dimensioni massime del database di origine. Questo requisito è dovuto al fatto che l'operazione di copia del database crea inizialmente uno snapshot dei dati e non esegue la sincronizzazione dei dati tra l'origine e la destinazione. 

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni dettagliate sulle dimensioni di calcolo specifiche e sulle opzioni relative alle dimensioni di archiviazione disponibili per database singoli, vedere [Limiti delle risorse basate su vCore del database SQL per database singoli](sql-database-vcore-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes)
- Per informazioni dettagliate sulle dimensioni di calcolo specifiche e sulle opzioni relative alle dimensioni di archiviazione disponibili per i pool elastici, vedere [Limiti delle risorse basate su vCore del database SQL per i pool elastici](sql-database-vcore-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).
