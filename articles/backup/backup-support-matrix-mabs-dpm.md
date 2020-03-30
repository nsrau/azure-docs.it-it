---
title: MABS & matrice di supporto DPM di System Center
description: Questo articolo riepiloga il supporto di Backup di Azure quando si usa Microsoft Azure Backup Server (MABS) o System Center DPM per eseguire il backup delle risorse locali e delle macchine virtuali di Azure.This article summarizes Azure Backup support when you use Microsoft Azure Backup Server (MABS) or System Center DPM to back up on-premises and Azure VM resources.
ms.date: 02/17/2019
ms.topic: conceptual
ms.openlocfilehash: 6664f7b226b75b364fd1c83f2abc56b5a275eff9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77582654"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-server-or-system-center-dpm"></a>Matrice di supporto per il backup con Microsoft Azure Backup Server o System Center DPM

È possibile usare il servizio Backup di Azure per eseguire il backup di macchine e carichi di lavoro locali e di macchine virtuali di Azure.You can use the [Azure Backup service](backup-overview.md) to back up on on-premises machines and workloads, and Azure virtual machines (VMs). In questo articolo vengono riepilogate le impostazioni di supporto e le limitazioni per il backup dei computer tramite Microsoft Azure Backup Server (MABS) o System Center Data Protection Manager (DPM) e Backup di Azure.

## <a name="about-dpmmabs"></a>Informazioni su DPM/MABS

[System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview?view=sc-dpm-1807) è una soluzione aziendale che consente di configurare, facilitare e gestire il backup e il ripristino di computer e dati aziendali. e fa parte della famiglia di prodotti [System Center](https://www.microsoft.com/cloud-platform/system-center-pricing).

MABS è un prodotto server che può essere usato per eseguire il backup di server fisici locali, macchine virtuali e app in esecuzione su di essi.

MABS è basato su System Center DPM e fornisce funzionalità simili con alcune differenze:

- Per eseguire il server di Backup di Microsoft Azure non è necessaria alcuna licenza System Center.
- Per MABS e DPM, Azure fornisce l'archiviazione di backup a lungo termine. DPM inoltre consente di eseguire il backup dei dati per l'archiviazione a lungo termine su nastro. Questa funzionalità non è disponibile con il server di Backup di Microsoft Azure.
- È possibile eseguire il backup di un server DPM primario con un server DPM secondario. Il server secondario proteggerà il database del server primario e le repliche delle origini dati archiviate sul server primario. In caso di guasto del server primario, il server secondario può continuare a proteggere i carichi di lavoro protetti dal server primario, finché il server primario non sarà nuovamente disponibile.  Questa funzionalità non è disponibile con il server di Backup di Microsoft Azure.

Si scarica MABS [dall'Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=57520). Può essere eseguito in locale o in una macchina virtuale di Azure.It can be run on-premises or on an Azure VM.

DPM e il server di Backup di Microsoft Azure supportano il backup di una vasta gamma di app e di sistemi operativi server e client. Essi offrono diversi scenari di backup:

- È possibile eseguire il backup a livello di computer con un backup dello stato del sistema o un backup bare metal.
- È possibile eseguire il backup di volumi, condivisioni, cartelle e file specifici.
- È possibile eseguire il backup di app specifiche utilizzando impostazioni ottimizzate per la funzionalità di gestione delle app.

## <a name="dpmmabs-backup"></a>Backup DPM/MABS

Il backup con DPM/MABS e Backup di Azure funziona come segue:

1. L'agente protezione DPM/MABS viene installato in ogni computer di cui verrà eseguito il backup.
1. Il backup di computer e app viene eseguito nell'archiviazione locale in DPM/MABS.
1. L'agente di Servizi di ripristino di Microsoft Azure viene installato nel server DPM o nel server di Backup di Microsoft Azure.
1. L'agente MARS esegue il backup dei dischi DPM/MABS in un insieme di credenziali di Backup di Servizi di ripristino in Azure tramite Backup di Azure.

Per altre informazioni:

- [Vedere altre informazioni](backup-architecture.md#architecture-back-up-to-dpmmabs) sull'architettura del server di Backup di Microsoft Azure.
- [Esaminare gli elementi supportati](backup-support-matrix-mars-agent.md) per l'agente MARS.

## <a name="supported-scenarios"></a>Scenari supportati

**Scenario** | **Agente** | **Percorso**
--- | --- | ---
**Backup di carichi di lavoro e computer locali** | L'agente protezione DPM/MABS viene eseguito nei computer di cui si desidera eseguire il backup.<br/><br/> L'agente MARS sul server DPM/MABS.<br/> La versione minima dell'agente di Servizi di ripristino di Microsoft Azure o dell'agente di Backup di Azure richiesta per l'abilitazione di questa funzionalità è la 2.0.8719.0.  | DPM/MABS deve essere in esecuzione in locale.

## <a name="supported-deployments"></a>Distribuzioni supportate

DPM/MABS può essere distribuito come riepilogato nella tabella seguente.

**Distribuzione** | **Supporto** | **Dettagli**
--- | --- | ---
**Distribuito in locale** | Server fisico<br/><br/>Macchina virtuale Hyper-V<br/><br/> Macchina virtuale VMware | Se DPM/MABS è installato come macchina virtuale VMware, esegue solo il backup delle macchine virtuali VMware e dei carichi di lavoro in esecuzione in tali macchine virtuali.
**Distribuito come macchina virtuale di Azure Stack** | Solo server di Backup di Microsoft Azure | Non è possibile usare DPM per eseguire il backup di macchine virtuali di Azure Stack.
**Distribuito come macchina virtuale di Azure** | Protegge le macchine virtuali di Azure e i carichi di lavoro in esecuzione in tali macchine virtualiProtects Azure VMs and workloads that are running on those VMs | DPM/MABS in esecuzione in Azure non può eseguire il backup dei computer locali.

## <a name="supported-mabs-and-dpm-operating-systems"></a>Sistemi operativi supportati per il server di Backup di Microsoft Azure e DPM

Backup di Azure può eseguire il backup delle istanze DPM/MABS che eseguono uno dei sistemi operativi seguenti. Devono essere in uso gli ultimi Service Pack e aggiornamenti dei sistemi operativi.

**Scenario** | **DPM/MABS**
--- | ---
**Server di Backup di Microsoft Azure in una macchina virtuale di Azure** |  Windows 2016 Datacenter.<br/><br/> Windows 2019 Datacenter.<br/><br/> Ti consigliamo di iniziare con un'immagine dal marketplace.<br/><br/> Minimo Standard_A4_v2 con quattro core e 8 GB di RAM.
**DPM in una macchina virtuale di Azure** | System Center 2012 R2 con Update 3 o versioni successive.<br/><br/> Sistema operativo Windows [richiesto da System Center](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server).<br/><br/> Ti consigliamo di iniziare con un'immagine dal marketplace.<br/><br/> Minimo Standard_A4_v2 con quattro core e 8 GB di RAM.
**Server di Backup di Microsoft Azure in locale** |  MABS v3 e versioni successive: Windows Server 2016 o Windows Server 2019
**DPM in locale** | Server fisico/VM Hyper-V: System Center 2012 SP1 o versione successiva.<br/><br/> VM VMware: System Center 2012 R2 con aggiornamento 5 o versione successiva.

>[!NOTE]
>L'installazione di Backup di Azure server non è supportata in Windows Server Core o Microsoft Hyper-V Server.

## <a name="management-support"></a>Supporto della gestione

**Problema** | **Dettagli**
--- | ---
**Installazione** | Installare DPM/MABS in un computer monouso.<br/><br/> Non installare DPM/MABS in un controller di dominio, in un computer con l'installazione del ruolo Server applicazioni, in un computer che esegue Microsoft Exchange Server o System Center Operations Manager o in un nodo del cluster.<br/><br/> [Esaminare tutti i requisiti di sistema di DPM](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server).
**Dominio** | DPM/MABS deve essere aggiunto a un dominio. Installare e quindi aggiungere DPM o il server di Backup di Microsoft Azure a un dominio. Lo spostamento di DPM o del server di Backup di Microsoft Azure in un nuovo dominio dopo la distribuzione non è supportato.
**Archiviazione** | L'archiviazione di backup moderna (MBS) è supportata da DPM 2016/MABS v2 e versioni successive. Non è disponibile per il server di Backup di Microsoft Azure v1.
**Aggiornamento del server di Backup di Microsoft Azure** | È possibile installare direttamente il server di Backup di Microsoft Azure v3 oppure eseguire l'aggiornamento dal server di Backup di Microsoft Azure v2 al server di Backup di Microsoft Azure v3. [Scopri di più](backup-azure-microsoft-azure-backup.md#upgrade-mabs).
**Spostamento del server di Backup di Microsoft Azure** | Se si usa MBS, è possibile spostare il server di Backup di Microsoft Azure in un nuovo server conservando l'archivio.<br/><br/> Il server deve avere lo stesso nome dell'originale. Non è possibile cambiare il nome se si intende mantenere lo stesso pool di archiviazione e usare lo stesso database del server di Backup di Microsoft Azure per archiviare i punti di ripristino dei dati.<br/><br/> Si dovrà disporre di un backup del database del server di Backup di Microsoft Azure perché sarà necessario ripristinarlo.

## <a name="mabs-support-on-azure-stack"></a>Supporto per il server di Backup di Microsoft Azure in Azure Stack

Se si distribuisce il server di Backup di Microsoft Azure in una macchina virtuale di Azure Stack, sarà possibile gestire il backup delle macchine virtuali di Azure Stack e dei carichi di lavoro da un'unica posizione.

**Componente** | **Dettagli**
--- | ---
**Server di Backup di Microsoft Azure in una macchina virtuale di Azure Stack** | Almeno la taglia A2. È consigliabile iniziare con un'immagine di Windows Server 2012 R2 o Windows Server 2016 da Azure Marketplace.We recommend you start with a Windows Server 2012 R2 or Windows Server 2016 image from the Azure Marketplace.<br/><br/> Non installare altro nella macchina virtuale MABS.
**Archiviazione del server di Backup di Microsoft Azure** | Usare un account di archiviazione separato per la macchina virtuale MABS. L'agente MARS in esecuzione su MABS necessita di archiviazione temporanea per una posizione della cache e per contenere i dati ripristinati dal cloud.
**Pool di archiviazione del server di Backup di Microsoft Azure** | La dimensione del pool di archiviazione MABS è determinata dal numero e dalla dimensione dei dischi collegati alla macchina virtuale MABS. Per ogni dimensione di macchina virtuale di Azure Stack è previsto un numero massimo di dischi. Ad esempio, per A2 il numero massimo è quattro dischi.
**Conservazione del server di Backup di Microsoft Azure** | Non conservare i dati di backup sui dischi MABS locali per più di cinque giorni.
**Aumento delle prestazioni del server di Backup di Microsoft Azure** | Per aumentare le prestazioni della distribuzione, è possibile aumentare le dimensioni della macchina virtuale del server di Backup di Microsoft Azure. Ad esempio, è possibile passare dalla serie A alla serie D.<br/><br/> È anche possibile assicurarsi di scaricare regolarmente i dati con il backup in Azure.You can also ensure that you're offloading data with backup to Azure regularly. Se necessario, è possibile distribuire altri server MABS.
**.NET Framework nel server di Backup di Microsoft Azure** | La macchina virtuale MABS necessita di .NET Framework 3.3 SP1 o versione successiva installata.
**Dominio del server di Backup di Microsoft Azure** | La macchina virtuale del server di Backup di Microsoft Azure deve essere aggiunta a un dominio. Un utente del dominio con privilegi di amministratore deve installare il server di Backup di Microsoft Azure nella macchina virtuale.
**Backup dei dati di macchine virtuali di Azure Stack** | È possibile eseguire il backup di file, cartelle e app.
**Backup supportato** | Questi sistemi operativi sono supportati per le macchine virtuali di cui si vuole eseguire il backup:These operating systems are supported for VMs that you want to back up:<br/><br/> Canale semestrale di Windows Server (Datacenter, Enterprise, Standard)<br/><br/> Windows Server 2016, Windows Server 2012 R2, Windows Server 2008 R2
**SQL Server support for Azure Stack VMs** | Eseguire il backup di SQL Server 2016, SQL Server 2014, SQL Server 2012 SP1.<br/><br/> Eseguire il backup e il ripristino di un database.
**Supporto SharePoint per macchine virtuali di Azure Stack** | SharePoint 2016, SharePoint 2013, SharePoint 2010.<br/><br/> Eseguire il backup e il ripristino di una farm, un database, un front-end e un server Web.
**Requisiti di rete per macchine virtuali sottoposte a backup** | Tutte le macchine virtuali nel carico di lavoro di Azure Stack devono appartenere alla stessa rete virtuale e alla stessa sottoscrizione.

## <a name="dpmmabs-networking-support"></a>Supporto delle funzionalità di rete per DPM o il server di Backup di Microsoft Azure

### <a name="url-access"></a>accesso con URL

Il server DPM o il server di Backup di Microsoft Azure deve poter accedere agli URL seguenti:

- `http://www.msftncsi.com/ncsi.txt`
- *.Microsoft.com
- *.windowsazure.com
- *.microsoftonline.com
- *.windows.net

### <a name="azure-expressroute-support"></a>Supporto per Azure ExpressRouteAzure ExpressRoute support

È possibile eseguire il backup dei dati tramite Azure ExpressRoute con peering pubblico (disponibile per circuiti precedenti) e peering Microsoft.You can back up your data over Azure ExpressRoute with public peering (available for old circuits) and Microsoft peering. Il backup tramite peering privato non è supportato.

Con peering pubblico: garantire l'accesso ai domini/indirizzi seguenti:With public peering: Ensure access to the following domains/addresses:

* `http://www.msftncsi.com/ncsi.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`

Con il peering Microsoft, selezionare i seguenti servizi/aree geografiche e i valori della community pertinenti:

* Azure Active Directory (12076:5060)
* Area di Microsoft Azure (in base alla posizione dell'insieme di credenziali dei servizi di ripristino)
* Archiviazione di Azure (in base alla posizione dell'insieme di credenziali dei servizi di ripristino)Azure Storage (according to the location of your Recovery Services vault)

Per ulteriori dettagli, vedere i requisiti di [routing ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-routing).

>[!NOTE]
>Il peering pubblico è deprecato per i nuovi circuiti.

### <a name="dpmmabs-connectivity-to-azure-backup"></a>Connettività di DPM o del server di Backup di Microsoft Azure al servizio Backup di Azure

Per il corretto funzionamento dei backup, è necessaria la connettività al servizio Backup di Azure e la sottoscrizione di Azure deve essere attiva. La tabella seguente illustra il comportamento che si riscontra quando queste due condizioni non si verificano.

**Server di Backup di Microsoft Azure ad Azure** | **Sottoscrizione** | **Backup/Ripristino**
--- | --- | ---
Connesso | Attivo | Eseguire il backup su disco DPM/MABS.<br/><br/> Eseguire il backup in Azure.Back up to Azure.<br/><br/> Eseguire il ripristino dal disco.<br/><br/> Eseguire il ripristino da Azure.Restore from Azure.
Connesso | Scaduta/sottoposta a deprovisioning | Nessun backup su disco o in Azure.<br/><br/> Se la sottoscrizione è scaduta, è possibile eseguire il ripristino dal disco o da Azure.If the subscription is expired, you can restore from disk or Azure.<br/><br/> Se la sottoscrizione viene rimossa, non è possibile eseguire il ripristino dal disco o da Azure.If the subscription is decommissioned, you can't restore from disk or Azure. I punti di ripristino di Azure vengono eliminati.
Nessuna connettività per più di 15 giorni | Attivo | Nessun backup su disco o in Azure.<br/><br/> È possibile eseguire il ripristino dal disco o da Azure.
Nessuna connettività per più di 15 giorni | Scaduta/sottoposta a deprovisioning | Nessun backup su disco o in Azure.<br/><br/> Se la sottoscrizione è scaduta, è possibile eseguire il ripristino dal disco o da Azure.If the subscription is expired, you can restore from disk or Azure.<br/><br/> Se la sottoscrizione viene rimossa, non è possibile eseguire il ripristino dal disco o da Azure.If the subscription is decommissioned, you can't restore from disk or Azure. I punti di ripristino di Azure vengono eliminati.

## <a name="dpmmabs-storage-support"></a>Supporto dell'archiviazione per DPM o il server di Backup di Microsoft Azure

I dati di cui viene eseguito il backup in DPM/MABS vengono archiviati nell'archivio su disco locale.

**Archiviazione** | **Dettagli**
--- | ---
**MBS** | L'archiviazione di backup moderna (MBS) è supportata da DPM 2016/MABS v2 e versioni successive. Non è disponibile per il server di Backup di Microsoft Azure v1.
**Archiviazione del server di Backup di Microsoft Azure in una macchina virtuale di Azure** | I dati vengono archiviati in dischi di Azure collegati alla macchina virtuale DPM/MABS e gestiti in DPM/MABS. Il numero di dischi che è possibile utilizzare per il pool di archiviazione DPM/MABS è limitato dalle dimensioni della macchina virtuale.<br/><br/> A2 VM: 4 dischi; VM A3: 8 dischi; A4 VM: 16 dischi, con una dimensione massima di 1 TB per ogni disco. Questo determina il pool di archiviazione di backup totale disponibile.<br/><br/> La quantità di dati di cui è possibile eseguire il backup dipende dal numero e dalle dimensioni dei dischi collegati.
**Conservazione dei dati del server di Backup di Microsoft Azure in una macchina virtuale di Azure** | È consigliabile conservare i dati per un giorno sul disco di DPM/MABS Azure ed eseguire il backup da DPM/MABS all'insieme di credenziali per una conservazione più lunga. In questo modo è possibile proteggere una maggiore quantità di dati tramite l'offload nel servizio Backup di Azure.

### <a name="modern-backup-storage-mbs"></a>Modern Backup Storage (MBS)

Da DPM 2016/MABS v2 (in esecuzione su Windows Server 2016) e versioni successive, è possibile sfruttare i vantaggi dell'archiviazione di backup moderna (MBS).

- I backup di MBS vengono archiviati in un disco ReFS (Resilient File System).
- MBS utilizza la clonazione dei blocchi ReFS per un backup più rapido e un utilizzo più efficiente dello spazio di archiviazione.
- Quando si aggiungono volumi al pool di archiviazione DPM/MABS locale, è necessario configurarli con lettere di unità. È quindi possibile configurare l'archiviazione dei carichi di lavoro in volumi diversi.
- Quando si creano gruppi protezione dati per eseguire il backup dei dati in DPM o nel server di Backup di Microsoft Azure, si seleziona l'unità che si intende usare. Ad esempio, è possibile archiviare i backup per SQL o altri carichi di lavoro di IOPS elevati in un'unità ad alte prestazioni e archiviare i carichi di lavoro di cui viene eseguito il backup con minore frequenza in un'unità con prestazioni inferiori.

## <a name="supported-backups-to-mabs"></a>Backup nel server di Backup di Microsoft Azure supportati

Per informazioni sui vari server e carichi di lavoro che è possibile proteggere con il server di Backup di Azure, vedere Matrice di protezione del server di Backup di [Azure.](https://docs.microsoft.com/azure/backup/backup-mabs-protection-matrix#protection-support-matrix)

## <a name="supported-backups-to-dpm"></a>Backup in DPM supportati

Per informazioni sui vari server e carichi di lavoro che è possibile proteggere con Data Protection Manager, vedere l'articolo Che cosa può eseguire il backup di [DPM?](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2019).

- I carichi di lavoro cluster di cui è stato eseguito il backup da DPM/MABS devono trovarsi nello stesso dominio di DPM/MABS o in un dominio figlio/trusted.
- È possibile usare l'autenticazione NTLM/del certificato per eseguire il backup dei dati in gruppi di lavoro o domini non attendibili.

## <a name="next-steps"></a>Passaggi successivi

- [Vedere altre informazioni](backup-architecture.md#architecture-back-up-to-dpmmabs) sull'architettura del server di Backup di Microsoft Azure.
- [Rivedere](backup-support-matrix-mars-agent.md) gli scenari supportati per l'agente di Servizi di ripristino di Microsoft Azure.
- [Installare](backup-azure-microsoft-azure-backup.md) un server di Backup di Microsoft Azure.
- [Configurare DPM](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-180).
