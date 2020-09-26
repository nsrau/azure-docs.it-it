---
title: Panoramica del modello di acquisto vCore
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Il modello di acquisto vCore ti permette di ridimensionare in modo indipendente le risorse di calcolo e archiviazione, le prestazioni locali e il prezzo per ottimizzare il database SQL di Azure e il Istanza gestita SQL di Azure.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 08/14/2020
ms.openlocfilehash: ceb1c9711d67b32f9a31e6105df0e0e0eec42907
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91321374"
---
# <a name="vcore-model-overview---azure-sql-database-and-azure-sql-managed-instance"></a>Panoramica del modello vCore: database SQL di Azure e Istanza gestita SQL di Azure 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Il modello di acquisto Virtual Core (vCore) usato dal database SQL di Azure e da Azure SQL Istanza gestita offre diversi vantaggi:

- Limiti di calcolo, memoria, I/O e archiviazione più elevati.
- Controllare la generazione dell'hardware per meglio soddisfare i requisiti di calcolo e memoria del carico di lavoro.
- Sconti sui prezzi per [vantaggio Azure Hybrid (vantaggio Azure Hybrid)](../azure-hybrid-benefit.md) e l' [istanza riservata (ri)](reserved-capacity-overview.md).
- Maggiore trasparenza nei dettagli hardware per il calcolo, che facilita la pianificazione delle migrazioni dalle distribuzioni locali.

## <a name="service-tiers"></a>Livelli di servizio

Le opzioni del livello di servizio nel modello vCore includono per utilizzo generico, business critical e iperscalabilità. Il livello di servizio in genere definisce l'architettura di archiviazione, lo spazio e I limiti di I/O e le opzioni di continuità aziendale correlate alla disponibilità e al ripristino di emergenza.

|-|**Utilizzo generico**|**Business Critical**|**Hyperscale**|
|---|---|---|---|
|Ideale per|La maggior parte dei carichi di lavoro aziendali. Offre opzioni di calcolo e archiviazione orientate al budget, bilanciate e scalabili. |Offre alle applicazioni aziendali la massima resilienza agli errori usando diverse repliche isolate e fornisce le massime prestazioni di I/O per ogni replica di database.|La maggior parte dei carichi di lavoro aziendali con requisiti di archiviazione e scalabilità a scalabilità elevata.  Offre una maggiore resilienza agli errori consentendo la configurazione di più di una replica di database isolata. |
|Archiviazione|Usa l'archiviazione remota.<br/>**Calcolo con provisioning del database SQL**:<br/>5 GB - 4 TB<br/>**Calcolo senza server**:<br/>5 GB-3 TB<br/>**Istanza gestita SQL**: 32 GB-8 TB |Usa l'archiviazione SSD locale.<br/>**Calcolo con provisioning del database SQL**:<br/>5 GB - 4 TB<br/>**Istanza gestita SQL**:<br/>32 GB - 4 TB |Aumento automatico delle dimensioni dello spazio di archiviazione in base alle esigenze. Supporta fino a 100 TB di spazio di archiviazione. Usa l'archiviazione SSD locale per la cache locale del pool di buffer e l'archiviazione dei dati locali. Usa l'archiviazione remota di Azure come archivio dati finale a lungo termine. |
|IOPS e velocità effettiva (approssimativa)|**Database SQL**: vedere i limiti delle risorse per i [database singoli](resource-limits-vcore-single-databases.md) e i [pool elastici](resource-limits-vcore-elastic-pools.md).<br/>**Istanza gestita SQL**: vedere [Panoramica dei limiti delle risorse di Azure SQL istanza gestita](../managed-instance/resource-limits.md#service-tier-characteristics).|Vedere limiti delle risorse per [database singoli](resource-limits-vcore-single-databases.md) e [pool elastici](resource-limits-vcore-elastic-pools.md).|La funzionalità iperscalabile è un'architettura a più livelli con memorizzazione nella cache a più livelli. I IOPS e la velocità effettiva effettivi dipendono dal carico di lavoro.|
|Disponibilità|1 replica, nessuna replica con scalabilità in lettura|3 repliche, 1 [replica scalabilità in lettura](read-scale-out.md),<br/>disponibilità elevata con ridondanza della zona (HA)|1 replica di lettura/scrittura, più 0-4 [repliche con scalabilità in lettura](read-scale-out.md)|
|Backup|[Archiviazione con ridondanza geografica e accesso in lettura (RA-GRS)](../../storage/common/geo-redundant-design.md), 7-35 giorni (7 giorni per impostazione predefinita)|[RA-GRS](../..//storage/common/geo-redundant-design.md), 7-35 giorni (7 giorni per impostazione predefinita)|Backup basati su snapshot nell'archiviazione remota di Azure. Questi snapshot vengono usati per il ripristino rapido. I backup sono istantanei e non influiscano sulle prestazioni di I/O di calcolo. I ripristini sono veloci e non sono un'operazione di dimensioni dei dati (che richiede minuti anziché ore o giorni).|
|In memoria|Non supportate|Supportato|Non supportate|
|||


### <a name="choosing-a-service-tier"></a>Scelta di un livello di servizio

Per informazioni sulla selezione di un livello di servizio per un carico di lavoro specifico, vedere gli articoli seguenti:

- [Quando scegliere il livello di servizio per utilizzo generico](service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [Quando scegliere il livello di servizio business critical](service-tier-business-critical.md#when-to-choose-this-service-tier)
- [Quando scegliere il livello di servizio di iperscalabilità](service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>Livelli di calcolo

Le opzioni del livello di calcolo nel modello vCore includono i livelli di calcolo con provisioning e senza server.


### <a name="provisioned-compute"></a>Calcolo con provisioning

Il livello di calcolo con provisioning fornisce una quantità specifica di risorse di calcolo di cui viene eseguito il provisioning continuo indipendentemente dall'attività del carico di lavoro e le fatture per la quantità di calcolo di cui è stato effettuato il provisioning a un prezzo fisso all'ora.


### <a name="serverless-compute"></a>Calcolo serverless

Il [livello di calcolo senza server](serverless-tier-overview.md) consente di ridimensionare automaticamente le risorse di calcolo in base all'attività del carico di lavoro e le fatture per la quantità di calcolo usata al secondo.



## <a name="hardware-generations"></a>Generazioni hardware

Le opzioni di generazione hardware nel modello vCore includono gen 4/5, serie M e serie Fsv2. La generazione hardware definisce in genere i limiti di calcolo e memoria e altre caratteristiche che influiscano sulle prestazioni del carico di lavoro.

### <a name="gen4gen5"></a>Gen4/quinta generazione

- L'hardware Gen4/quinta generazione fornisce risorse di calcolo e di memoria bilanciate ed è adatto per la maggior parte dei carichi di lavoro di database che non hanno una quantità di memoria superiore, vCore più elevata o requisiti di vCore singolo più veloci, come previsto dalla serie M o Fsv2.

Per le aree in cui è disponibile Gen4/quinta generazione, vedere [disponibilità di Gen4/quinta generazione](#gen4gen5-1).

### <a name="fsv2-series"></a>Serie Fsv2

- La serie Fsv2 è un'opzione hardware ottimizzata per il calcolo che offre bassa latenza della CPU e velocità di clock elevata per la maggior parte dei carichi di lavoro che richiedono CPU.
- A seconda del carico di lavoro, la serie Fsv2 è in grado di fornire più prestazioni della CPU per ogni vCore rispetto a Quinta generazione e le dimensioni 72 vCore possono fornire più prestazioni della CPU per un costo inferiore rispetto a 80 Vcore in quinta generazione. 
- Fsv2 fornisce meno memoria e tempdb per vCore rispetto ad altri componenti hardware, quindi i carichi di lavoro sensibili a tali limiti possono invece considerare la serie quinta generazione o M.  

La serie Fsv2 è supportata solo nel livello per utilizzo generico. Per le aree in cui è disponibile la serie Fsv2, vedere [disponibilità della serie Fsv2](#fsv2-series-1).


### <a name="m-series"></a>Serie M

- La serie M è un'opzione hardware con ottimizzazione per la memoria per carichi di lavoro che richiedono più memoria e limiti di calcolo superiori rispetto a quelli forniti da Quinta generazione.
- La serie M offre 29 GB per vCore e fino a 128 Vcore, che aumenta il limite di memoria rispetto a Quinta generazione da 8x a circa 4 TB.

La serie M è supportata solo nel livello business critical e non supporta la ridondanza della zona.  La sottoscrizione deve essere un tipo di offerta a pagamento, incluso il pagamento in base al consumo o Enterprise Agreement (EA). Per le aree in cui è disponibile la serie M, vedere [disponibilità della serie m](#m-series-1).

<!--
To enable M-series hardware for a subscription and region, a support request must be opened. The subscription must be a paid offer type including Pay-As-You-Go or Enterprise Agreement (EA).  If the support request is approved, then the selection and provisioning experience of M-series follows the same pattern as for other hardware generations. For regions where M-series is available, see [M-series availability](#m-series).
-->

### <a name="compute-and-memory-specifications"></a>Specifiche di calcolo e memoria


|Generazione dell'hardware  |Calcolo  |Memoria  |
|:---------|:---------|:---------|
|Quarta generazione     |-Processori Intel® E5-2673 V3 (Haswell) a 2,4 GHz<br>-Provisioning fino a 24 Vcore (1 vCore = 1 core fisico)  |-7 GB per vCore<br>-Effettuare il provisioning fino a 168 GB|
|Quinta generazione     |**Calcolo con provisioning**<br>-Intel® E5-2673 V4 (Broadwell) 2,3 GHz, Intel® SP-8160 (Skylake) \* e intel® 8272CL (Cascade Lake) 2,5 GHz \* Processor<br>-Provisioning fino a 80 Vcore (1 vCore = 1 Hyper-thread)<br><br>**Calcolo serverless**<br>-Processori Intel® E5-2673 V4 (Broadwell) a 2,3 GHz e Intel® SP-8160 (Skylake) *<br>-Scalabilità automatica fino a 40 Vcore (1 vCore = 1 Hyper-thread)|**Calcolo con provisioning**<br>-5,1 GB per vCore<br>-Effettuare il provisioning fino a 408 GB<br><br>**Calcolo serverless**<br>-Scalabilità automatica fino a 24 GB per vCore<br>-Scalabilità automatica fino a 120 GB max|
|Serie Fsv2     |-Processori Intel® 8168 (Skylake)<br>-Con una velocità massima di clock core a 3,4 GHz e una velocità massima di clock singolo core di 3,7 GHz.<br>-Provisioning fino a 72 Vcore (1 vCore = 1 Hyper-thread)|-1,9 GB per vCore<br>-Effettuare il provisioning fino a 136 GB|
|Serie M     |-Intel® E7-8890 V3 2,5 GHz e processori Intel® 8280M 2,7 GHz (Cascade Lake)<br>-Provisioning fino a 128 Vcore (1 vCore = 1 Hyper-thread)|-29 GB per vCore<br>-Effettuare il provisioning fino a 3,7 TB|

\* Nella vista a gestione dinamica [sys. dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) la generazione di hardware per i database che usano processori Intel® SP-8160 (Skylake) viene visualizzata come Gen6, mentre la generazione hardware per i database che usano Intel® 8272CL (Cascade Lake) viene visualizzata come GEN7. I limiti delle risorse per tutti i database quinta generazione sono gli stessi indipendentemente dal tipo di processore (Broadwell, Skylake o Cascade Lake).

Per altre informazioni sui limiti delle risorse, vedere [limiti delle risorse per i database singoli (vCore)](resource-limits-vcore-single-databases.md)o [limiti delle risorse per i pool elastici (vCore)](resource-limits-vcore-elastic-pools.md).

### <a name="selecting-a-hardware-generation"></a>Selezione di una generazione hardware

Nel portale di Azure è possibile selezionare la generazione dell'hardware per un database o un pool nel database SQL al momento della creazione oppure è possibile modificare la generazione dell'hardware di un database o di un pool esistente.

**Per selezionare una generazione hardware durante la creazione di un database o un pool SQL**

Per informazioni dettagliate, vedere [creare un database SQL](single-database-create-quickstart.md).

Nella scheda **nozioni di base** selezionare il collegamento **Configura database** nella sezione **calcolo e archiviazione** e quindi selezionare il collegamento **modifica configurazione** :

  ![configurare il database](./media/service-tiers-vcore/configure-sql-database.png)

Selezionare la generazione hardware desiderata:

  ![Seleziona hardware](./media/service-tiers-vcore/select-hardware.png)


**Per modificare la generazione dell'hardware di un database o un pool SQL esistente**

Per un database, nella pagina Panoramica selezionare il collegamento piano **tariffario** :

  ![modifica dell'hardware](./media/service-tiers-vcore/change-hardware.png)

Per un pool, nella pagina Overview (panoramica) selezionare **Configure (Configura**).

Seguire i passaggi per modificare la configurazione e selezionare la generazione hardware come descritto nei passaggi precedenti.

**Per selezionare una generazione hardware durante la creazione di un Istanza gestita SQL**

Per informazioni dettagliate, vedere [creare un istanza gestita SQL](../managed-instance/instance-create-quickstart.md).

Nella scheda **nozioni di base** selezionare il collegamento **Configura database** nella sezione **calcolo e archiviazione** e quindi selezionare generazione hardware desiderata:

  ![configurare Istanza gestita SQL](./media/service-tiers-vcore/configure-managed-instance.png)
  
**Per modificare la generazione dell'hardware di un Istanza gestita SQL esistente**

# <a name="the-azure-portal"></a>[Il portale di Azure](#tab/azure-portal)

Nella pagina SQL Istanza gestita selezionare il collegamento piano **tariffario** inserito nella sezione Impostazioni

![modificare l'hardware Istanza gestita SQL](./media/service-tiers-vcore/change-managed-instance-hardware.png)

Nella pagina piano tariffario sarà possibile modificare la generazione dell'hardware come descritto nei passaggi precedenti.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Usare lo script di PowerShell seguente:

```powershell-interactive
Set-AzSqlInstance -Name "managedinstance1" -ResourceGroupName "ResourceGroup01" -ComputeGeneration Gen5
```

Per ulteriori informazioni, vedere il comando [set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance) .

# <a name="the-azure-cli"></a>[L’interfaccia della riga di comando di Azure](#tab/azure-cli)

Usare il comando dell'interfaccia della riga di comando seguente:

```azurecli-interactive
az sql mi update -g mygroup -n myinstance --family Gen5
```

Per altri dettagli, vedere comando [AZ SQL mi Update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update) .

---

### <a name="hardware-availability"></a>Disponibilità hardware

#### <a name="gen4gen5"></a><a name="gen4gen5-1"></a> Gen4/quinta generazione

L'hardware Gen4 viene eliminato e non è più disponibile per [le](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/) nuove distribuzioni. Tutti i nuovi database devono essere distribuiti nell'hardware quinta generazione.

Quinta generazione è disponibile nella maggior parte delle aree del mondo.

#### <a name="fsv2-series"></a>Serie Fsv2

La serie Fsv2 è disponibile nelle aree seguenti: Australia centrale, Australia centrale 2, Australia orientale, Australia sudorientale, Brasile meridionale, Canada centrale, Asia orientale, Stati Uniti orientali, Francia centrale, India centrale, Corea centrale, Corea meridionale, Europa settentrionale, Sudafrica settentrionale, Asia sudorientale, Regno Unito meridionale, Regno Unito occidentale, Europa occidentale, Stati Uniti occidentali 2.


#### <a name="m-series"></a>Serie M

La serie M è disponibile nelle aree seguenti: Stati Uniti orientali, Europa settentrionale, Europa occidentale, Stati Uniti occidentali 2.
<!--
M-series may also have limited availability in additional regions. You can request a different region than listed here, but fulfillment in a different region may not be possible.

To enable M-series availability in a subscription, access must be requested by [filing a new support request](#create-a-support-request-to-enable-m-series).


##### Create a support request to enable M-series: 

1. Select **Help + support** in the portal.
2. Select **New support request**.

On the **Basics** page, provide the following:

1. For **Issue type**, select **Service and subscription limits (quotas)**.
2. For **Subscription** = select the subscription to enable M-series.
3. For **Quota type**, select **SQL database**.
4. Select **Next** to go to the **Details** page.

On the **Details** page, provide the following:

1. In the **PROBLEM DETAILS** section select the **Provide details** link. 
2. For **SQL Database quota type** select **M-series**.
3. For **Region**, select the region to enable M-series.
    For regions where M-series is available, see [M-series availability](#m-series).

Approved support requests are typically fulfilled within 5 business days.
-->

## <a name="next-steps"></a>Passaggi successivi

Per iniziare, vedere: 
- [Creazione di un database SQL tramite il portale di Azure](single-database-create-quickstart.md)
- [Creazione di un Istanza gestita SQL utilizzando il portale di Azure](../managed-instance/instance-create-quickstart.md)

Per informazioni dettagliate sui prezzi, vedere la [pagina dei prezzi del database SQL di Azure](https://azure.microsoft.com/pricing/details/sql-database/single/).

Per informazioni dettagliate sulle dimensioni di calcolo e archiviazione specifiche disponibili nei livelli di servizio utilizzo generico e business critical, vedere:

- [limiti delle risorse basate su vCore per il database SQL di Azure](resource-limits-vcore-single-databases.md).
- [limiti delle risorse basate su vCore per il database SQL di Azure in pool](resource-limits-vcore-elastic-pools.md).
- [limiti delle risorse basate su vCore per istanza gestita SQL di Azure](../managed-instance/resource-limits.md). 

