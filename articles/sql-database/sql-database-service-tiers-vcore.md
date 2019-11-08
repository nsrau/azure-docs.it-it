---
title: Panoramica del modello vCore
description: Il modello di acquisto vCore ti permette di ridimensionare in modo indipendente le risorse di calcolo e archiviazione, le prestazioni locali e l'ottimizzazione del prezzo.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/04/2019
ms.openlocfilehash: b9de02bf0836727ac88b78194641238621e87a79
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821066"
---
# <a name="vcore-model-overview"></a>Panoramica del modello vCore

Il modello Virtual Core (vCore) offre diversi vantaggi:

- Limiti di calcolo, memoria, i/o e archiviazione più elevati.
- Controllare la generazione dell'hardware per meglio soddisfare i requisiti di calcolo e memoria del carico di lavoro.
- Sconti sui prezzi per [vantaggio Azure Hybrid (vantaggio Azure Hybrid)](sql-database-azure-hybrid-benefit.md) e l' [istanza riservata (ri)](sql-database-reserved-capacity.md).
- Maggiore trasparenza nei dettagli dell'hardware che alimentano il calcolo; semplifica la pianificazione delle migrazioni dalle distribuzioni locali.

## <a name="service-tiers"></a>Livelli di servizio

Le opzioni del livello di servizio nel modello vCore includono per utilizzo generico, business critical e iperscalabilità. Il livello di servizio in genere definisce l'architettura di archiviazione, i limiti di spazio e i/o e le opzioni di continuità aziendale correlate alla disponibilità e al ripristino di emergenza.

||**Utilizzo generico**|**Business critical**|**Con iperscalabilità**|
|---|---|---|---|
|Ideale per|La maggior parte dei carichi di lavoro aziendali. Offre opzioni di calcolo e archiviazione orientate al budget, bilanciate e scalabili. |Offre alle applicazioni aziendali la massima resilienza agli errori usando diverse repliche isolate e fornisce le massime prestazioni di I/O per ogni replica di database.|La maggior parte dei carichi di lavoro aziendali con requisiti di archiviazione e scalabilità a scalabilità elevata.  Offre una maggiore resilienza agli errori consentendo la configurazione di più di una replica di database isolata. |
|Archiviazione|Usa l'archiviazione remota.<br/>**Calcolo con provisioning di database singolo e pool elastico**:<br/>5 GB - 4 TB<br/>**Calcolo senza server**:<br/>5 GB-3 TB<br/>**Istanza gestita**: 32 GB-8 TB |Usa l'archiviazione SSD locale.<br/>**Calcolo con provisioning di database singolo e pool elastico**:<br/>DA 5 GB A 8 TB<br/>**Istanza gestita**:<br/>32 GB - 4 TB |Aumento automatico delle dimensioni dello spazio di archiviazione in base alle esigenze. Supporta fino a 100 TB di spazio di archiviazione. Usa l'archiviazione SSD locale per la cache locale del pool di buffer e l'archiviazione dei dati locali. Usa l'archiviazione remota di Azure come archivio dati finale a lungo termine. |
|Velocità effettiva I/O (approssimativa)|**Database singolo e pool elastico**: 500 IOPS per vCore fino a 40000 numero massimo di IOPS.<br/>**Istanza gestita**: dipende dalle [dimensioni del file](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes).|5000 IOPS per vCore fino a 320.000 numero massimo di IOPS|La funzionalità iperscalabile è un'architettura a più livelli con memorizzazione nella cache a più livelli. Gli IOPs effettivi dipendono dal carico di lavoro.|
|Disponibilità|1 replica, nessuna replica con scalabilità in lettura|3 repliche, 1 [replica scalabilità in lettura](sql-database-read-scale-out.md),<br/>disponibilità elevata con ridondanza della zona (HA)|1 replica di lettura/scrittura, più 0-4 [repliche con scalabilità in lettura](sql-database-read-scale-out.md)|
|Backup|[Archiviazione con ridondanza geografica e accesso in lettura (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 giorni (7 giorni per impostazione predefinita)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), da 7 a 35 giorni (7 giorni per impostazione predefinita)|Backup basati su snapshot nell'archiviazione remota di Azure. Questi snapshot vengono usati per il ripristino rapido. I backup sono istantanei e non influiscano sulle prestazioni di I/O di calcolo. I ripristini sono veloci e non sono un'operazione di dimensioni dei dati (che richiede minuti anziché ore o giorni).|
|In memoria|Non supportate|Supportato|Non supportate|
|||


### <a name="choosing-a-service-tier"></a>Scelta di un livello di servizio

Per informazioni sulla selezione di un livello di servizio per un carico di lavoro specifico, vedere gli articoli seguenti:

- [Quando scegliere il livello di servizio per utilizzo generico](sql-database-service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [Quando scegliere il livello di servizio business critical](sql-database-service-tier-business-critical.md#when-to-choose-this-service-tier)
- [Quando scegliere il livello di servizio di iperscalabilità](sql-database-service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>Livelli di calcolo

Le opzioni del livello di calcolo nel modello vCore includono i livelli di calcolo con provisioning e senza server.


### <a name="provisioned-compute"></a>Calcolo con provisioning

Il livello di calcolo con provisioning fornisce una quantità specifica di risorse di calcolo di cui viene eseguito il provisioning continuo indipendentemente dall'attività del carico di lavoro e le fatture per la quantità di calcolo di cui è stato effettuato il provisioning a un prezzo fisso all'ora.


### <a name="serverless-compute"></a>Elaborazione serverless

Il [livello di calcolo senza server](sql-database-serverless.md) consente di ridimensionare automaticamente le risorse di calcolo in base all'attività del carico di lavoro e le fatture per la quantità di calcolo usata al secondo.



## <a name="hardware-generations"></a>Generazioni hardware

Le opzioni di generazione hardware nel modello vCore includono gen 4/5, serie M (anteprima) e serie Fsv2 (anteprima). La generazione hardware definisce in genere i limiti di calcolo e memoria e altre caratteristiche che influiscano sulle prestazioni del carico di lavoro.

### <a name="gen4gen5"></a>Gen4/quinta generazione

- L'hardware Gen4/quinta generazione fornisce risorse di calcolo e di memoria bilanciate ed è adatto per la maggior parte dei carichi di lavoro di database che non hanno una quantità di memoria superiore, vCore più elevata o requisiti di vCore singolo più veloci, come previsto dalla serie M o Fsv2.

Per le aree in cui è disponibile Gen4/quinta generazione, vedere [disponibilità di Gen4/quinta generazione](#gen4gen5-1).

### <a name="fsv2-seriespreview"></a>Serie Fsv2 (anteprima)

- La serie Fsv2 è un'opzione hardware ottimizzata per il calcolo che offre bassa latenza della CPU e velocità di clock elevata per la maggior parte dei carichi di lavoro che richiedono CPU.
- A seconda del carico di lavoro, la serie Fsv2 è in grado di fornire più prestazioni della CPU per ogni vCore rispetto a Quinta generazione e le dimensioni 72 vCore possono fornire più prestazioni della CPU per un costo inferiore rispetto a 80 Vcore in quinta generazione. 
- Fsv2 fornisce meno memoria e tempdb per vCore rispetto ad altri componenti hardware, quindi i carichi di lavoro sensibili a tali limiti possono invece considerare la serie quinta generazione o M.  

Per le aree in cui è disponibile la serie Fsv2, vedere [disponibilità della serie Fsv2](#fsv2-series).


### <a name="m-seriespreview"></a>Serie M (anteprima)

- La serie M è un'opzione hardware con ottimizzazione per la memoria per carichi di lavoro che richiedono più memoria e limiti di calcolo superiori rispetto a quelli forniti da Quinta generazione.
- La serie M offre 29 GB per vCore e 128 Vcore, che aumenta il limite di memoria rispetto a Quinta generazione da 8x a circa 4 TB.

Per abilitare l'hardware della serie M per una sottoscrizione e un'area, è necessario che sia aperta una richiesta di supporto. Se la richiesta di supporto è approvata, l'esperienza di selezione e provisioning della serie M segue lo stesso modello di altre generazioni di hardware. Per le aree in cui è disponibile la serie M, vedere [disponibilità della serie m](#m-series).


### <a name="compute-and-memory-specifications"></a>Specifiche di calcolo e memoria


|Generazione dell'hardware  |Calcolo  |Memoria  |
|:---------|:---------|:---------|
|Gen4     |-Processori Intel E5-2673 V3 (Haswell) a 2,4 GHz<br>-Provisioning fino a 24 Vcore (1 vCore = 1 core fisico)  |-7 GB per vCore<br>-Effettuare il provisioning fino a 168 GB|
|Quinta generazione     |**Calcolo con provisioning**<br>-Processori Intel E5-2673 V4 (Broadwell) a 2,3 GHz<br>-Provisioning fino a 80 Vcore (1 vCore = 1 Hyper-thread)<br><br>**Calcolo serverless**<br>-Processori Intel E5-2673 V4 (Broadwell) a 2,3 GHz<br>-Scalabilità automatica fino a 16 Vcore (1 vCore = 1 Hyper-thread)|**Calcolo con provisioning**<br>-5,1 GB per vCore<br>-Effettuare il provisioning fino a 408 GB<br><br>**Calcolo serverless**<br>-Scalabilità automatica fino a 24 GB per vCore<br>-Scalabilità automatica fino a 48 GB max|
|Serie Fsv2     |-Processori Intel Xeon Platinum 8168 (SkyLake)<br>-Con una velocità massima di clock core a 3,4 GHz e una velocità massima di clock singolo core di 3,7 GHz.<br>-Provisioning 72 Vcore (1 vCore = 1 Hyper-thread)|-1,9 GB per vCore<br>-Provisioning di 136 GB|
|Serie M     |-Processori Intel Xeon E7-8890 V3 2,5 GHz<br>-Provisioning 128 Vcore (1 vCore = 1 Hyper-thread)|-29 GB per vCore<br>-Provisioning 3,7 TB|


Per altre informazioni sui limiti delle risorse, vedere [limiti delle risorse per i database singoli (vCore)](sql-database-vcore-resource-limits-single-databases.md)o [limiti delle risorse per i pool elastici (vCore)](sql-database-vcore-resource-limits-elastic-pools.md).

### <a name="selecting-a-hardware-generation"></a>Selezione di una generazione hardware

Nel portale di Azure è possibile selezionare la generazione hardware per un database o un pool SQL al momento della creazione, oppure è possibile modificare la generazione dell'hardware di un database o un pool SQL esistente.

**Per selezionare una generazione hardware durante la creazione di un database o un pool SQL**

Per informazioni dettagliate, vedere [creare un database SQL](sql-database-single-database-get-started.md).

Nella scheda **nozioni di base** selezionare il collegamento **Configura database** nella sezione **calcolo e archiviazione** e quindi selezionare il collegamento **modifica configurazione** :

  ![configurare il database](media/sql-database-service-tiers-vcore/configure-sql-database.png)

Selezionare la generazione hardware desiderata:

  ![Seleziona hardware](media/sql-database-service-tiers-vcore/select-hardware.png)


**Per modificare la generazione dell'hardware di un database o un pool SQL esistente**

Per un database, nella pagina Panoramica selezionare il collegamento piano **tariffario** :

  ![modifica hardware](media/sql-database-service-tiers-vcore/change-hardware.png)

Per un pool, nella pagina Overview (panoramica) selezionare **Configure (Configura**).

Seguire i passaggi per modificare la configurazione e selezionare la generazione hardware come descritto nei passaggi precedenti.

### <a name="hardware-availability"></a>Disponibilità hardware

#### <a name="gen4gen5"></a>Gen4/quinta generazione

I nuovi database Gen4 non sono più supportati nelle aree dell'Australia orientale o del Brasile meridionale. 

Quinta generazione è disponibile nella maggior parte delle aree del mondo.

#### <a name="fsv2-series"></a>Serie Fsv2

La serie Fsv2 è disponibile nelle aree seguenti: Australia centrale, Australia centrale 2, Australia orientale, Australia sudorientale, Brasile meridionale, Canada centrale, Asia orientale, Stati Uniti orientali, Francia centrale, India centrale, India occidentale, Corea centrale, Corea meridionale, Nord Europa, Sudafrica settentrionale, Asia sudorientale, Regno Unito meridionale, Regno Unito occidentale, Europa occidentale, Stati Uniti occidentali 2.


#### <a name="m-series"></a>Serie M

La serie M è disponibile nelle aree seguenti: Stati Uniti orientali, Europa settentrionale, Europa occidentale, Stati Uniti occidentali 2.
La serie M può anche avere disponibilità limitata in altre aree. È possibile richiedere un'area diversa da quella elencata, ma l'evasione in un'area diversa potrebbe non essere possibile.

Per abilitare la disponibilità della serie M in una sottoscrizione, è necessario richiedere l'accesso inviando [una nuova richiesta di supporto](#create-a-support-request-to-enable-m-series).


##### <a name="create-a-support-request-to-enable-m-series"></a>Creare una richiesta di supporto per abilitare la serie M: 

1. Selezionare **Guida e supporto** nel portale.
2. Selezionare **Nuova richiesta di supporto**.

Nella pagina **nozioni di base** , specificare quanto segue:

1. Per **Tipo di problema** selezionare **Limiti del servizio e della sottoscrizione (quote)** .
2. Per **Subscription** = selezionare la sottoscrizione per abilitare la serie M.
3. Per **tipo di quota**selezionare **database SQL**.
4. Selezionare **Avanti** per passare alla pagina dei **Dettagli** .

Nella pagina **Dettagli** specificare quanto segue:

5. Nella sezione **Dettagli problema** selezionare il collegamento **specificare i dettagli** . 
6. Per **tipo di quota del database SQL** selezionare **serie M**.
7. Per **Region (area**) selezionare l'area per abilitare la serie M.
    Per le aree in cui è disponibile la serie M, vedere [disponibilità della serie m](#m-series).

Le richieste di supporto approvate vengono in genere soddisfatte entro 5 giorni lavorativi.


## <a name="next-steps"></a>Passaggi successivi

- Per creare un database SQL, vedere [creazione di un database SQL tramite il portale di Azure](sql-database-single-database-get-started.md).
- Per le opzioni di calcolo e dimensioni di archiviazione specifiche disponibili per i singoli database, vedere [limiti delle risorse basate su vCore del database SQL per database singoli](sql-database-vcore-resource-limits-single-databases.md).
- Per le opzioni di calcolo e dimensioni di archiviazione specifiche disponibili per i pool elastici, vedere [limiti delle risorse basate su vCore del database SQL per i pool elastici](sql-database-vcore-resource-limits-elastic-pools.md).
- Per informazioni dettagliate sui prezzi, vedere la [pagina dei prezzi del database SQL di Azure](https://azure.microsoft.com/pricing/details/sql-database/single/).
