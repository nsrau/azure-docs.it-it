---
title: Panoramica del modello vCore
description: Il modello di acquisto vCore consente di ridimensionare in modo indipendente le risorse di elaborazione e archiviazione, abbinare le prestazioni locali e ottimizzare il prezzo.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/27/2019
ms.openlocfilehash: 5fd69dcd30292630862887ab5434764ba377b396
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481083"
---
# <a name="vcore-model-overview"></a>Panoramica del modello vCore

Il modello di core virtuale (vCore) offre diversi vantaggi:The virtual core (vCore) model provides several benefits:

- Limiti di calcolo, memoria, I/O e archiviazione più elevati.
- Controlla la generazione dell'hardware per soddisfare al meglio i requisiti di elaborazione e memoria del carico di lavoro.
- Sconti sui prezzi per [il vantaggio Azure Hybrid (AHB)](sql-database-azure-hybrid-benefit.md) e l'istanza [riservata (RI)](sql-database-reserved-capacity.md).
- Maggiore trasparenza nei dettagli hardware che alimentano il calcolo; facilita la pianificazione delle migrazioni da distribuzioni locali.

## <a name="service-tiers"></a>Livelli di servizio

Le opzioni del livello di servizio nel modello vCore includono Scopo generale, Business Critical e Hyperscale.Service tier options in the vCore model include General Purpose, Business Critical, and Hyperscale. Il livello di servizio definisce in genere l'architettura di archiviazione, i limiti di spazio e I/O e le opzioni di continuità aziendale correlate alla disponibilità e al ripristino di emergenza.

||**Finalità generale**|**Critico per le aziende**|**Hyperscale**|
|---|---|---|---|
|Ideale per|La maggior parte dei carichi di lavoro aziendali. Offre opzioni di elaborazione e archiviazione orientate al budget, bilanciate e scalabili. |Offre alle applicazioni aziendali la massima resilienza agli errori utilizzando diverse repliche isolate e offre le massime prestazioni di I/O per ogni replica di database.|La maggior parte dei carichi di lavoro aziendali con requisiti di archiviazione e scalabilità in lettura altamente scalabili.  Offre una maggiore resilienza agli errori consentendo la configurazione di più di una replica di database isolata. |
|Archiviazione|Utilizza l'archiviazione remota.<br/>**Singolo database e pool elastici di cui è stato eseguito il provisioning di calcolo:**<br/>5 GB - 4 TB<br/>**Calcolo senza server**:<br/>5 GB - 3 TB<br/>**Istanza gestita**: 32 GB - 8 TB |Utilizza l'archiviazione SSD locale.<br/>**Singolo database e pool elastici di cui è stato eseguito il provisioning di calcolo:**<br/>5 GB - 4 TB<br/>**Istanza gestita**:<br/>32 GB - 4 TB |Aumento automatico flessibile dello spazio di archiviazione in base alle esigenze. Supporta fino a 100 TB di spazio di archiviazione. Utilizza l'archiviazione SSD locale per la cache del pool di buffer locale e l'archiviazione dei dati locale. Usa l'archiviazione remota di Azure come archivio dati finale a lungo termine. |
|Operazioni di I/O al secondo e velocità effettiva (approssimativa)|**Database singoli e pool elastici**: vedere limiti di risorse per [singoli database](../sql-database/sql-database-vcore-resource-limits-single-databases.md) e [pool elastici](../sql-database/sql-database-vcore-resource-limits-elastic-pools.md).<br/>**Istanza gestita:** vedere Panoramica dei limiti delle risorse [dell'istanza gestita del database SQL di Azure.Managed](../sql-database/sql-database-managed-instance-resource-limits.md#service-tier-characteristics)Instance : See Overview Azure SQL Database managed instance resource limits .|Vedere limiti delle risorse per [singoli database](../sql-database/sql-database-vcore-resource-limits-single-databases.md) e [pool elastici](../sql-database/sql-database-vcore-resource-limits-elastic-pools.md).|Hyperscale è un'architettura a più livelli con memorizzazione nella cache a più livelli. Le operazioni di I/O al secondo e la velocità effettiva efficaci dipenderanno dal carico di lavoro.|
|Disponibilità|1 replica, nessuna replica in scala di lettura|3 repliche, 1 [replica scalabilità in lettura](sql-database-read-scale-out.md),<br/>disponibilità elevata ridondante per zona|1 replica di lettura/scrittura, più repliche in scala di lettura 0-41 read-write replica, plus 0-4 [read-scale replicas](sql-database-read-scale-out.md)|
|Backup|Archiviazione con ridondanza geografica di accesso in [lettura (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 giorni (7 giorni per impostazione predefinita)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 giorni (7 giorni per impostazione predefinita)|Backup basati su snapshot nell'archiviazione remota di Azure.Snapshot-based backups in Azure remote storage. Questi snapshot vengono usati per il ripristino rapido. I backup sono istantanei e non influiscono sulle prestazioni di I/O di calcolo. I ripristini sono veloci e non sono un'operazione di dimensione dei dati (che richiede minuti anziché ore o giorni).|
|In memoria|Non supportate|Supportato|Non supportate|
|||


### <a name="choosing-a-service-tier"></a>Scelta di un livello di servizio

Per informazioni sulla selezione di un livello di servizio per il carico di lavoro specifico, vedere gli articoli seguenti:For information on selecting a service tier for your particular workload, see the following articles:

- [Quando scegliere il livello di servizio Scopo generaleWhen to choose the General purpose service tier](sql-database-service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [Quando scegliere il livello di servizio Business CriticalWhen to choose the Business Critical service tier](sql-database-service-tier-business-critical.md#when-to-choose-this-service-tier)
- [Quando scegliere il livello di servizio HyperscaleWhen to choose the Hyperscale service tier](sql-database-service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>Livelli di calcoloCompute tiers

Le opzioni del livello di calcolo nel modello vCore includono i livelli di calcolo di cui è stato eseguito il provisioning e senza server.


### <a name="provisioned-compute"></a>Calcolo con provisioning

Il livello di calcolo di cui è stato eseguito il provisioning fornisce una quantità specifica di risorse di elaborazione di cui viene eseguito il provisioning continuo indipendentemente dall'attività del carico di lavoro e fatture per la quantità di calcolo di cui è stato eseguito il provisioning a un prezzo orario fisso.


### <a name="serverless-compute"></a>Calcolo serverless

Il livello di [calcolo senza server](sql-database-serverless.md) scala automaticamente le risorse di calcolo in base all'attività del carico di lavoro e alle fatture per la quantità di calcolo usata al secondo.



## <a name="hardware-generations"></a>Generazioni di hardware

Le opzioni di generazione hardware nel modello vCore includono Gen 4/5, serie M (anteprima) e serie Fsv2 (anteprima). La generazione dell'hardware definisce in genere i limiti di calcolo e memoria e altre caratteristiche che influiscono sulle prestazioni del carico di lavoro.

### <a name="gen4gen5"></a>Gen4/Gen5

- L'hardware Gen4/Gen5 fornisce risorse di elaborazione e memoria bilanciate ed è adatto per la maggior parte dei carichi di lavoro di database che non dispongono di memoria superiore, vCore superiore o requisiti di vCore singoli più veloci, come fornito dalla serie Fsv2 o dalla serie M.

Per le aree in cui è disponibile Gen4/Gen5, vedere [Disponibilità Gen4/Gen5](#gen4gen5-1).

### <a name="fsv2-seriespreview"></a>Serie Fsv2 (anteprima)

- La serie Fsv2 è un'opzione hardware ottimizzata per il calcolo che offre bassa latenza della CPU e un'elevata velocità di clock per i carichi di lavoro più impegnativi della CPU.
- A seconda del carico di lavoro, la serie Fsv2 può offrire più prestazioni della CPU per vCore rispetto a Gen5 e le dimensioni di 72 vCore possono fornire più prestazioni della CPU per un costo inferiore rispetto a 80 vCore in Gen5. 
- Fsv2 fornisce meno memoria e tempdb per vCore rispetto ad altri hardware, pertanto i carichi di lavoro riservati a tali limiti potrebbero prendere in considerazione la serie Gen5 o M.  

Serie Fsv2 supportate solo nel livello General Purpose.  Per le aree in cui è disponibile la serie Fsv2, vedere Disponibilità della [serie Fsv2](#fsv2-series).


### <a name="m-seriespreview"></a>Serie M (anteprima)

- M-series è un'opzione hardware ottimizzata per la memoria per carichi di lavoro che richiedono più memoria e limiti di calcolo superiori a quelli forniti da Gen5.
- La serie M fornisce 29 GB per vCore e 128 vCore, che aumenta il limite di memoria rispetto a Gen5 di 8 volte a quasi 4 TB.

La serie M è supportata solo nel livello Business Critical e non supporta la ridondanza delle zone.

Per abilitare l'hardware della serie M per una sottoscrizione e un'area, è necessario aprire una richiesta di supporto. L'abbonamento deve essere un tipo di offerta a pagamento che include pay-as-You-Go o Enterprise Agreement (EA).  Se la richiesta di supporto viene approvata, l'esperienza di selezione e provisioning della serie M segue lo stesso modello di altre generazioni di hardware. Per le aree in cui è disponibile la serie M, vedere [Disponibilità della serie M](#m-series).


### <a name="compute-and-memory-specifications"></a>Specifiche di calcolo e memoria


|Generazione dell'hardware  |Calcolo  |Memoria  |
|:---------|:---------|:---------|
|Quarta generazione     |- Processori Intel E5-2673 v3 (Haswell) da 2,4 GHz<br>- Effettuare il provisioning fino a 24 vCore (1 vCore e 1 core fisico)  |- 7 GB per vCore<br>- Provisioning fino a 168 GB|
|Quinta generazione     |**Calcolo con provisioning**<br>- Processori Intel E5-2673 v4 (Broadwell) 2.3-GHz e Intel SP-8160 (Skylake)<br>- Effettuare il provisioning di un massimo di 80 vCore (1 vCore e 1 hyper-thread)<br><br>**Calcolo serverless**<br>- Processori Intel E5-2673 v4 (Broadwell) 2.3-GHz e Intel SP-8160 (Skylake)<br>- Scalabilità automatica fino a 16 vCore (1 vCore e 1 hyper-thread)|**Calcolo con provisioning**<br>- 5,1 GB per vCore<br>- Provisioning fino a 408 GB<br><br>**Calcolo serverless**<br>- Scalabilità automatica fino a 24 GB per vCore- Auto-scale up to 24 GB per vCore<br>- Scalabilità automatica fino a 48 GB max|
|Serie Fsv2     |- Processori Intel Xeon Platinum 8168 (SkyLake)<br>- Dotato di una velocità di clock turbo core sostenuta di 3,4 GHz e una velocità massima di clock turbo singolo core di 3,7 GHz.<br>- Effettuare il provisioning di 72 vCore (1 vCore e 1 hyper-thread)|- 1,9 GB per vCore<br>- Provisioning 136 GB|
|Serie M     |- Processori Intel Xeon E7-8890 v3 da 2,5 GHz<br>- Effettuare il provisioning di 128 vCore (1 vCore e 1 hyper-thread)|- 29 GB per vCore<br>- Fornitura 3,7 TB|

\*Nella vista a gestione dinamica [sys.dm_user_db_resource_governance,](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) la generazione di hardware per i database Gen5 che utilizzano processori Intel SP-8160 (Skylake) viene visualizzata come Gen6. I limiti di risorse per tutti i database Gen5 sono gli stessi indipendentemente dal tipo di processore (Broadwell o Skylake).

Per altre informazioni sui limiti delle risorse, vedere [Limiti delle risorse per singoli database (vCore)](sql-database-vcore-resource-limits-single-databases.md)o Limiti delle risorse per i pool [elastici (vCore).](sql-database-vcore-resource-limits-elastic-pools.md)

### <a name="selecting-a-hardware-generation"></a>Selezione di una generazione di hardware

Nel portale di Azure è possibile selezionare la generazione hardware per un database o un pool SQL al momento della creazione oppure modificare la generazione hardware di un pool o di un database SQL esistente.

**Per selezionare una generazione hardware durante la creazione di un database o di un pool SQL**

Per informazioni dettagliate, vedere [Creare un database SQL](sql-database-single-database-get-started.md).

Nella scheda **Nozioni di base** selezionare il collegamento **Configura database** nella sezione **Calcolo e archiviazione** e quindi selezionare il collegamento Modifica **configurazione:**

  ![configurare il database](media/sql-database-service-tiers-vcore/configure-sql-database.png)

Selezionare la generazione hardware desiderata:

  ![selezionare l'hardware](media/sql-database-service-tiers-vcore/select-hardware.png)


**Per modificare la generazione hardware di un database o di un pool SQL esistente**

Per un database, nella pagina Panoramica selezionare il collegamento **Livello prezzi:For** a database, on the Overview page, select the Pricing tier link:

  ![modifica dell'hardware](media/sql-database-service-tiers-vcore/change-hardware.png)

Per un pool, nella pagina Panoramica selezionare **Configura**.

Seguire i passaggi per modificare la configurazione e selezionare la generazione dell'hardware come descritto nei passaggi precedenti.

**Per selezionare una generazione hardware durante la creazione di un'istanza gestitaTo select a hardware generation when creating a managed instance**

Per informazioni dettagliate, vedere [Creare un'istanza gestita](sql-database-managed-instance-get-started.md).

Nella scheda **Nozioni di base** selezionare il collegamento **Configura database** nella sezione **Calcolo e archiviazione** e quindi selezionare la generazione hardware desiderata:

  ![configurare l'istanza gestita](media/sql-database-service-tiers-vcore/configure-managed-instance.png)
  
**Per modificare la generazione hardware di un'istanza gestita esistente**

# <a name="portal"></a>[Portale](#tab/azure-portal)

Nella pagina dell'istanza gestita selezionare **Collegamento livello prezzi** posizionato nella sezione Impostazioni

![modificare l'hardware dell'istanza gestita](media/sql-database-service-tiers-vcore/change-managed-instance-hardware.png)

Nella pagina **Livello di determinazione dei prezzi** sarà possibile modificare la generazione dell'hardware come descritto nei passaggi precedenti.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Usare lo script di PowerShell seguente:

```powershell-interactive
Set-AzSqlInstance -Name "managedinstance1" -ResourceGroupName "ResourceGroup01" -ComputeGeneration Gen5
```

Per ulteriori dettagli, controllare il comando [Set-AzSqlInstance.](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli)

Utilizzare il seguente comando CLI:

```azurecli-interactive
az sql mi update -g mygroup -n myinstance --family Gen5
```

Per ulteriori dettagli controllare il comando [az sql mi update.](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)

---

### <a name="hardware-availability"></a>Disponibilità hardware

#### <a name="gen4gen5"></a><a name="gen4gen5-1"></a>Gen4/Gen5

L'hardware Gen4 è in fase di [eliminazione graduale](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/) e non è più disponibile per le nuove distribuzioni. Tutti i nuovi database devono essere distribuiti su hardware Gen5.

Gen5 è disponibile nella maggior parte delle regioni del mondo.

#### <a name="fsv2-series"></a>Serie Fsv2

La serie Fsv2 è disponibile nelle seguenti regioni: Australia centrale, Australia centrale 2, Australia Orientale, Australia Sud-est, Brasile Sud, Canada centrale, Asia orientale, Stati Uniti orientali, Francia centrale, India centrale, India occidentale, Corea centrale, Corea del Sud, Nord Europa, Sud Africa Nord, Sud-Est asiatico, Regno Unito Sud, Regno Unito Ovest, Europa Occidentale, Stati Occidentali 2.


#### <a name="m-series"></a>Serie M

La serie M è disponibile nelle seguenti regioni: Stati Uniti orientali, Nord Europa, Europa occidentale, Stati Uniti occidentali 2.
La serie M può anche avere una disponibilità limitata in altre regioni. È possibile richiedere un'area diversa da quella elencata qui, ma l'adempimento in un'area diversa potrebbe non essere possibile.

Per abilitare la disponibilità della serie M in un abbonamento, è necessario richiedere l'accesso inviando una nuova richiesta di [supporto.](#create-a-support-request-to-enable-m-series)


##### <a name="create-a-support-request-to-enable-m-series"></a>Creare una richiesta di supporto per abilitare la serie M:Create a support request to enable M-series: 

1. Selezionare **Guida e supporto** nel portale.
2. Selezionare **Nuova richiesta di supporto**.

Nella pagina **Nozioni di base** specificare quanto segue:

1. Per **Tipo di problema** selezionare **Limiti del servizio e della sottoscrizione (quote)**.
2. Per **Abbonamento:** selezionare la sottoscrizione per abilitare la serie M.For Subscription - select the subscription to enable M-series.
3. Per **Tipo di quota**, selezionare Database **SQL**.
4. Selezionare Avanti per passare alla pagina **Dettagli.Select** **Next** to go to the Details page.

Nella pagina **Dettagli** specificare quanto segue:

1. Nella sezione **DETTAGLI PROBLEMA** selezionare il collegamento **Fornisci dettagli.** 
2. Per **il tipo di quota del database SQL** selezionare Serie **M**.
3. Per **Regione**, selezionare l'area per abilitare la serie M.
    Per le aree in cui è disponibile la serie M, vedere [Disponibilità della serie M](#m-series).

Le richieste di supporto approvate vengono in genere soddisfatte entro 5 giorni lavorativi.


## <a name="next-steps"></a>Passaggi successivi

- Per creare un database SQL, vedere [Creazione di un database SQL tramite il portale](sql-database-single-database-get-started.md)di Azure.
- Per le dimensioni di calcolo specifiche e le opzioni di dimensione di archiviazione disponibili per i singoli database, vedere Limiti delle [risorse basate su vCore del database SQL per singoli database.](sql-database-vcore-resource-limits-single-databases.md)
- Per le dimensioni di calcolo specifiche e le opzioni di dimensioni di archiviazione disponibili per i pool elastici, vedere Limiti delle risorse basate su vCore del database SQL per i [pool elastici.](sql-database-vcore-resource-limits-elastic-pools.md)
- Per informazioni dettagliate sui prezzi, vedere la [pagina dei prezzi del database SQL di Azure.](https://azure.microsoft.com/pricing/details/sql-database/single/)
