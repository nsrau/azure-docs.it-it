---
title: Matrice di supporto per il server di Backup di Microsoft Azure e System Center DPM
description: Questo articolo riepiloga il supporto del servizio Backup di Azure per l'uso del server di Backup di Microsoft Azure o di System Center DPM per eseguire il backup di risorse locali e di macchine virtuali di Azure.
services: backup
author: rayne-wiselman
ms.service: backup
ms.date: 02/17/2019
ms.topic: conceptual
ms.author: raynew
manager: carmonm
ms.openlocfilehash: 704bb409d2b21e2ae258dbb2d627b1c088d80db7
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57860925"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-server-or-system-center-dpm"></a>Matrice di supporto per il backup con System Center DPM o un Server di Backup di Microsoft Azure

È possibile usare la [servizio Backup di Azure](backup-overview.md) per eseguire il backup di macchine virtuali locali e i carichi di lavoro e macchine virtuali di Azure (VM). Questo articolo riepiloga le impostazioni del supporto e le limitazioni per il backup dei computer con Server di Backup di Microsoft Azure (MABS) o System Center Data Protection Manager (DPM) e Backup di Azure.

## <a name="about-dpmmabs"></a>Su DPM/MABS

[System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview?view=sc-dpm-1807) è una soluzione aziendale che consente di configurare, facilita e consente di gestire backup e ripristino di computer aziendali e dati. e fa parte della famiglia di prodotti [System Center](https://www.microsoft.com/cloud-platform/system-center-pricing).

Backup di Microsoft AZURE è un prodotto server che può essere utilizzato per eseguire il backup dei server fisici in locale, le macchine virtuali e le App in esecuzione su di essi.

Backup di Microsoft AZURE si basa su System Center DPM e offre funzionalità simili con un paio di differenze:
- Per eseguire il server di Backup di Microsoft Azure non è necessaria alcuna licenza System Center.
- Per i backup di Microsoft AZURE e Data Protection Manager, Azure offre archiviazione dei backup a lungo termine. DPM inoltre consente di eseguire il backup dei dati per l'archiviazione a lungo termine su nastro. Questa funzionalità non è disponibile con il server di Backup di Microsoft Azure.

Download di backup di Microsoft AZURE dal [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=57520). Può essere eseguito in locale o in una VM di Azure.

DPM e il server di Backup di Microsoft Azure supportano il backup di una vasta gamma di app e di sistemi operativi server e client. Essi offrono diversi scenari di backup:

- È possibile eseguire il backup a livello di computer con un backup dello stato del sistema o un backup bare metal.
- È possibile eseguire il backup di volumi, condivisioni, cartelle e file specifici.
- È possibile eseguire il backup di App specifiche usando le impostazioni app riconoscere ottimizzate.

## <a name="dpmmabs-backup"></a>Backup DPM/MABS

Backup con DPM/MABS e Backup di Azure funziona nel modo seguente:

1. Agente protezione DPM/MABS viene installato in ogni computer che verranno sottoposti a backup.
1. App e le macchine viene eseguito il backup nell'archiviazione locale in DPM/MABS.
1. L'agente di Servizi di ripristino di Microsoft Azure viene installato nel server DPM o nel server di Backup di Microsoft Azure.
1. L'agente MARS esegue il backup dei dischi DPM/MABS per un insieme di credenziali di servizi di ripristino backup in Azure usando Backup di Azure.

Per altre informazioni:

- [Vedere altre informazioni](backup-architecture.md#architecture-back-up-to-dpmmabs) sull'architettura del server di Backup di Microsoft Azure.
- [Esaminare le attività supportate](backup-support-matrix-mars-agent.md) per l'agente MARS.

## <a name="supported-scenarios"></a>Scenari supportati 

**Scenario** | **Agent** | **Posizione**
--- | --- | ---
**Backup di carichi di lavoro e computer locali** | Agente protezione DPM/MABS viene eseguito nei computer in cui si desidera eseguire il backup.<br/><br/> L'agente di MARS nel server DPM/MABS. | DPM/MABS deve essere in esecuzione in locale.
**Backup di carichi di lavoro e macchine virtuali di Azure** | Agente protezione DPM/MABS nel computer protetto.<br/><br/> L'agente di MARS nel server DPM/MABS. | DPM o il server di Backup di Microsoft Azure deve essere in esecuzione in una macchina virtuale di Azure.

## <a name="supported-deployments"></a>Distribuzioni supportate

DPM/MABS può essere distribuito come riepilogato nella tabella seguente.

**Distribuzione** | **Supporto** | **Dettagli**
--- | --- | ---
**Distribuito in locale** | Server fisico<br/><br/>Macchina virtuale Hyper-V<br/><br/> Macchina virtuale VMware | Se è installato DPM/MABS come una VM VMware, esegue solo il backup dei carichi di lavoro in esecuzione su tali macchine virtuali e macchine virtuali VMware.
**Distribuito come macchina virtuale di Azure Stack** | Solo server di Backup di Microsoft Azure | Non è possibile usare DPM per eseguire il backup di macchine virtuali di Azure Stack.
**Distribuito come macchina virtuale di Azure** | Consente di proteggere carichi di lavoro in esecuzione su tali macchine virtuali e macchine virtuali di Azure | DPM/MABS in esecuzione in Azure non è possibile eseguire il backup alle macchine virtuali.


## <a name="supported-mabs-and-dpm-operating-systems"></a>Sistemi operativi supportati per il server di Backup di Microsoft Azure e DPM

Backup di Azure è possibile eseguire il backup di istanze DPM/MABS che eseguono uno qualsiasi dei seguenti sistemi operativi. Devono essere in uso gli ultimi Service Pack e aggiornamenti dei sistemi operativi.

**Scenario** | **DPM/MABS** 
--- | --- 
**Server di Backup di Microsoft Azure in una macchina virtuale di Azure** |  Windows Server 2012 R2.<br/><br/> Windows 2016 Datacenter.<br/><br/> Windows 2019 Datacenter.<br/><br/> È consigliabile iniziare con un'immagine del marketplace.<br/><br/> Minimo A2 Standard con due core e 3,5 GB di RAM. 
**DPM in una macchina virtuale di Azure** | System Center 2012 R2 con Update 3 o versioni successive.<br/><br/> Sistema operativo Windows [richiesto da System Center](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server).<br/><br/> È consigliabile iniziare con un'immagine del marketplace.<br/><br/> Minimo A2 Standard con due core e 3,5 GB di RAM. 
**Server di Backup di Microsoft Azure in locale** | Sistemi operativi a 64 bit supportati:<br/><br/> MABS v3 e versioni successive: Windows Server 2019 (Standard, Datacenter, Essentials). <br/><br/> MABS v2 e versioni successive: Windows Server 2016 (Standard, Datacenter, Essentials).<br/><br/> Tutte le versioni del server di Backup di Microsoft Azure: Windows Server 2012 R2, Windows Server 2012 (Standard, Datacenter, Foundation).<br/><br/>Tutte le versioni del server di Backup di Microsoft Azure: Windows Storage Server 2012 R2, Windows Server 2012 (Standard, Workgroup).
**DPM in locale** | Server fisico/macchina virtuale Hyper-V: System Center 2012 SP1 o versioni successive.<br/><br/> Macchina virtuale VMware: System Center 2012 R2 con Update 5 o versioni successive. 



## <a name="management-support"></a>Supporto della gestione

**Problema** | **Dettagli**
--- | ---
**Installazione** | Installare DPM/MABS in un computer a solo scopo.<br/><br/> Non installare DPM/MABS in un controller di dominio, in un computer con l'installazione del ruolo Server applicazioni, in un computer che esegue Microsoft Exchange Server o System Center Operations Manager o su un nodo del cluster.<br/><br/> [Esaminare tutti i requisiti di sistema DPM](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server).
**Dominio** | DPM/MABS deve appartenere a un dominio. Installare e quindi aggiungere DPM o il server di Backup di Microsoft Azure a un dominio. Lo spostamento di DPM o del server di Backup di Microsoft Azure in un nuovo dominio dopo la distribuzione non è supportato.
**Archiviazione** | Archiviazione dei backup moderna (MB) è supportato da 2016 DPM/MABS v2 e versioni successive. Non è disponibile per il server di Backup di Microsoft Azure v1.
**Aggiornamento del server di Backup di Microsoft Azure** | È possibile installare direttamente il server di Backup di Microsoft Azure v3 oppure eseguire l'aggiornamento dal server di Backup di Microsoft Azure v2 al server di Backup di Microsoft Azure v3. [Altre informazioni](backup-azure-microsoft-azure-backup.md#upgrade-mabs)
**Spostamento del server di Backup di Microsoft Azure** | Se si usa MBS, è possibile spostare il server di Backup di Microsoft Azure in un nuovo server conservando l'archivio.<br/><br/> Il server deve avere lo stesso nome dell'originale. Non è possibile cambiare il nome se si intende mantenere lo stesso pool di archiviazione e usare lo stesso database del server di Backup di Microsoft Azure per archiviare i punti di ripristino dei dati.<br/><br/> Si dovrà disporre di un backup del database del server di Backup di Microsoft Azure perché sarà necessario ripristinarlo.


## <a name="mabs-support-on-azure-stack"></a>Supporto per il server di Backup di Microsoft Azure in Azure Stack

Se si distribuisce il server di Backup di Microsoft Azure in una macchina virtuale di Azure Stack, sarà possibile gestire il backup delle macchine virtuali di Azure Stack e dei carichi di lavoro da un'unica posizione.

**Componente** | **Dettagli**
--- | --- 
**Server di Backup di Microsoft Azure in una macchina virtuale di Azure Stack** | AT almeno dimensioni A2. È consigliabile che iniziare con un'immagine di Windows Server 2012 R2 o Windows Server 2016 da Azure Marketplace.<br/><br/> Non installare altre operazioni nella VM MABS.
**Archiviazione del server di Backup di Microsoft Azure** | Usare un account di archiviazione separato per la VM di MABS. L'agente di MARS in esecuzione in MABS deve avere spazio di archiviazione temporaneo per un percorso della cache e per contenere i dati ripristinati dal cloud.
**Pool di archiviazione del server di Backup di Microsoft Azure** | Le dimensioni del pool di archiviazione di backup di Microsoft AZURE sono determinata dal numero e dimensioni dei dischi collegati alla VM MABS. Per ogni dimensione di macchina virtuale di Azure Stack è previsto un numero massimo di dischi. Ad esempio, per A2 il numero massimo è quattro dischi.
**Conservazione del server di Backup di Microsoft Azure** | Non mantenere il backup dei dati sui dischi locali MABS per più di cinque giorni.
**Aumento delle prestazioni del server di Backup di Microsoft Azure** | Per aumentare le prestazioni della distribuzione, è possibile aumentare le dimensioni della macchina virtuale del server di Backup di Microsoft Azure. Ad esempio, è possibile modificare dalla alla serie D.<br/><br/> È inoltre possibile garantire che si eseguono l'offload dei dati con backup di Azure regolarmente. Se necessario, è possibile distribuire ulteriori server di backup di Microsoft AZURE.
**.NET framework su backup di Microsoft AZURE** | La VM MABS deve SP1 di .NET Framework 3.3 o versione successiva su di esso.
**Dominio del server di Backup di Microsoft Azure** | La macchina virtuale del server di Backup di Microsoft Azure deve essere aggiunta a un dominio. Un utente del dominio con privilegi di amministratore deve installare il server di Backup di Microsoft Azure nella macchina virtuale.
**Backup dei dati di macchine virtuali di Azure Stack** | È possibile eseguire il backup di file, cartelle e app.
**Backup supportato** | Questi sistemi operativi sono supportati per le macchine virtuali che si desidera eseguire il backup:<br/><br/> Canale semestrale di Windows Server (Datacenter, Enterprise, Standard)<br/><br/> Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2
**Supporto di SQL Server per le macchine virtuali di Azure Stack** | Eseguire il backup di SQL Server 2016, SQL Server 2014, SQL Server 2012 SP1.<br/><br/> Eseguire il backup e ripristinare un database.
**Supporto SharePoint per macchine virtuali di Azure Stack** | SharePoint 2016, SharePoint 2013, SharePoint 2010.<br/><br/> Eseguire il backup e ripristino di una farm, database, front-end e server web.
**Requisiti di rete per macchine virtuali sottoposte a backup** | Tutte le macchine virtuali del carico di lavoro di Azure Stack devono appartenere alla stessa rete virtuale e appartengono alla stessa sottoscrizione.

## <a name="dpmmabs-networking-support"></a>Supporto delle funzionalità di rete per DPM o il server di Backup di Microsoft Azure

### <a name="url-access"></a>Accesso a URL

Il server DPM o il server di Backup di Microsoft Azure deve poter accedere agli URL seguenti:

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.windowsazure.com
- *.microsoftonline.com
- *.windows.net

### <a name="dpmmabs-connectivity-to-azure-backup"></a>Connettività di DPM o del server di Backup di Microsoft Azure al servizio Backup di Azure

Per il corretto funzionamento dei backup, è necessaria la connettività al servizio Backup di Azure e la sottoscrizione di Azure deve essere attiva. La tabella seguente illustra il comportamento che si riscontra quando queste due condizioni non si verificano.

**Server di Backup di Microsoft Azure ad Azure** | **Sottoscrizione** | **Backup/ripristino** 
--- | --- | --- 
Connesso | Attivo | Eseguire il backup su disco DPM/MABS.<br/><br/> Backup di Azure.<br/><br/> Ripristino da disco.<br/><br/> Ripristino da Azure.
Connesso | Scaduta/sottoposta a deprovisioning | Nessun backup su disco o in Azure.<br/><br/> Se la sottoscrizione è scaduta, è possibile ripristinare dal disco o da Azure.<br/><br/> Se la sottoscrizione viene messo fuori servizio, è possibile ripristinare dal disco o da Azure. I punti di ripristino di Azure vengono eliminati.
Nessuna connettività per più di 15 giorni | Attivo | Nessun backup su disco o in Azure.<br/><br/> È possibile eseguire il ripristino dal disco o da Azure.
Nessuna connettività per più di 15 giorni | Scaduta/sottoposta a deprovisioning | Nessun backup su disco o in Azure.<br/><br/> Se la sottoscrizione è scaduta, è possibile ripristinare dal disco o da Azure.<br/><br/> Se la sottoscrizione viene messo fuori servizio, è possibile ripristinare dal disco o da Azure. I punti di ripristino di Azure vengono eliminati.

## <a name="dpmmabs-storage-support"></a>Supporto dell'archiviazione per DPM o il server di Backup di Microsoft Azure

Dati sottoposti a backup per DPM/MABS vengono archiviati sull'archiviazione su disco locale. 

**Archiviazione** | **Dettagli**
--- | ---
**MBS** | Archiviazione dei backup moderna (MB) è supportato da 2016 DPM/MABS v2 e versioni successive. Non è disponibile per il server di Backup di Microsoft Azure v1.
**Archiviazione del server di Backup di Microsoft Azure in una macchina virtuale di Azure** | I dati vengono archiviati nei dischi Azure collegati alla macchina virtuale DPM/MABS e che sono gestiti in DPM/MABS. Il numero di dischi che possono essere utilizzati per il pool di archiviazione DPM/MABS è limitato dalle dimensioni della macchina virtuale.<br/><br/> Macchina virtuale A2: 4 dischi; macchina virtuale A3: 8 dischi; macchina virtuale A4: 16 dischi, con dimensioni massime di 1 TB per ogni disco. Questa impostazione determina il pool di archiviazione di backup totale disponibile.<br/><br/> La quantità di dati di cui è possibile eseguire il backup dipende dal numero e dalle dimensioni dei dischi collegati.
**Conservazione dei dati del server di Backup di Microsoft Azure in una macchina virtuale di Azure** | È consigliabile conservare i dati per un giorno nel disco di Azure DPM/MABS ed eseguire il backup da DPM/MABS nell'insieme di credenziali per un lungo periodo. In questo modo è possibile proteggere una maggiore quantità di dati tramite l'offload nel servizio Backup di Azure.


### <a name="modern-backup-storage-mbs"></a>Modern Backup Storage (MBS)
Dal 2016 DPM/MABS v2 (in esecuzione in Windows Server 2016) e versioni successive, è possibile sfruttare i vantaggi di modern backup storage (MBS).

- I backup di MBS vengono archiviati in un disco ReFS (Resilient File System).
- MBS Usa blocchi ReFS la clonazione per il backup più veloci e un uso più efficiente di spazio di archiviazione.
- Quando si aggiungono i volumi al pool di archiviazione DPM/MABS locale, si configurarli con lettere di unità. È quindi possibile configurare l'archiviazione dei carichi di lavoro in volumi diversi.
- Quando si creano gruppi protezione dati per eseguire il backup dei dati in DPM o nel server di Backup di Microsoft Azure, si seleziona l'unità che si intende usare. Ad esempio, si potrebbe archiviare i backup per SQL o altra unità di carichi di lavoro ad alte prestazioni IOPS elevata e memorizzare i carichi di lavoro che viene eseguiti meno frequentemente in un'unità di prestazioni inferiore.


## <a name="supported-backups-to-mabs"></a>Backup nel server di Backup di Microsoft Azure supportati

La tabella seguente riepiloga gli elementi di cui è possibile eseguire il backup nel server di Backup di Microsoft Azure da computer locali e macchine virtuali di Azure.


**Backup** | **Versioni** | **Server di Backup di Microsoft Azure** | **Dettagli** |
--- | --- | --- | --- |
**Windows 10<br/>Windows 8.1<br/>Windows 8<br/>Windows 7**<br/><br/>(32/64 bit) | Server di Backup di Microsoft Azure v3, v2, V1 | In locale. | Volume/condivisione/cartella/file.<br/><br/> Volumi deduplicati supportati.<br/><br/> I volumi devono essere almeno di 1 GB e NTFS. |
**Windows Server 2016 (Datacenter, Standard, non Nano)**<br/><br/> 64/32 bit | Server di Backup di Microsoft Azure v3, v2 | In locale/macchina virtuale di Azure.| Volume/condivisione/cartella/file; stato del sistema/bare metal.<br/><br/> Volumi deduplicati supportati. |
**Windows Server 2012 R2 (Datacenter e Standard)**<br/><br/> 64/32 bit | Server di Backup di Microsoft Azure v3, v2, v1 | In locale/macchina virtuale di Azure. | **Protezione locale**: Volume/condivisione/cartella/file; stato del sistema/bare metal.<br/><br/> **Protezione della macchina virtuale di Azure**: Volume/condivisione/cartella/file.<br/><br/> Volumi deduplicati supportati. |
**Windows Server 2012 con SP1 (Datacenter e Standard)**<br/><br/> 64/32 bit | Server di Backup di Microsoft Azure v3, v2, v1<br/><br/> [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) deve essere installato. | In locale/macchina virtuale di Azure. | **Protezione locale**: Volume/condivisione/cartella/file; stato del sistema/bare metal.<br/><br/> **Protezione della macchina virtuale di Azure**: Volume/condivisione/cartella/file.<br/><br/> Volumi deduplicati supportati. |
**Windows Server 2012 (Datacenter e Standard)**<br/><br/> 64/32 bit | Server di Backup di Microsoft Azure v1 | In locale/macchina virtuale di Azure. | **Protezione locale**: Volume/condivisione/cartella/file; stato del sistema/bare metal.<br/><br/> **Protezione della macchina virtuale di Azure**: Volume/condivisione/cartella/file.<br/><br/> Volumi deduplicati supportati. |
**Windows 2008 R2 con SP1 (Standard ed Enterprise)**<br/><br/> 64/32 bit | Supportato dal server di Backup di Microsoft Azure v3, v2, v1.<br/><br/> [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) deve essere installato. | In locale/macchina virtuale di Azure. |   **Protezione locale**: Volume/condivisione/cartella/file; stato del sistema/bare metal.<br/><br/> **Protezione della macchina virtuale di Azure**: Volume/condivisione/cartella/file.<br/><br/> Volumi deduplicati supportati. |
**Windows 2008 R2 (Standard ed Enterprise)**<br/><br/> 64/32 bit | Server di Backup di Microsoft Azure v1. Per il server di Backup di Microsoft Azure v2/v3, nel sistema operativo deve essere in uso SP1. | In locale/macchina virtuale di Azure. | **Protezione locale**: Volume/condivisione/cartella/file; stato del sistema/bare metal.<br/><br/> **Protezione della macchina virtuale di Azure**: Volume/condivisione/cartella/file.<br/><br/> Volumi deduplicati supportati. |
**Windows Server 2008 con SP2**<br/><br/> 64/32 bit | Server di Backup di Microsoft Azure v1, v2, v3 | Viene fornito il supporto solo per v1 quando il server di Backup di Microsoft Azure è distribuito come computer fisico locale o come macchina virtuale Hyper-V.<br/><br/> Il server di Backup di Microsoft Azure v1, 2, 3 è supportato quando tale server è distribuito come macchina virtuale VMware.<br/><br/> Non viene fornito il supporto per il server di Backup di Microsoft Azure in esecuzione in una macchina virtuale di Azure. | Volume/condivisione/cartella/file; stato del sistema/bare metal. |
**Windows Storage Server 2008** | Server di Backup di Microsoft Azure v1, v2, v3 | MABS come locali fisici server/Hyper-V della macchina virtuale. <br/><br/> Non viene fornito il supporto per il server di Backup di Microsoft Azure in esecuzione in una macchina virtuale di Azure. | Volume/condivisione/cartella/file; stato del sistema/bare metal.
**SQL Server 2017** | Server di Backup di Microsoft Azure v3 | In locale/macchina virtuale di Azure.| Backup del database di SQL Server.<br/><br/> Backup di cluster di SQL Server supportato.<br/><br/>Database archiviati in volumi condivisi cluster non supportati. |
**SQL Server 2016/2016 con SP1** | Server di Backup di Microsoft Azure v3, v2 | In locale/macchina virtuale di Azure.| Backup del database di SQL Server.<br/><br/> Backup di cluster di SQL Server supportato.<br/><br/>Database archiviati in volumi condivisi cluster non supportati. |
**SQL Server 2014**<br/><br/> **SQL Server 2012/SP1/SP2**<br/><br/> **SQL Server 2008 R2**<br/><br/> **SQL Server 2008** | Server di Backup di Microsoft Azure v3, v2, v1 | In locale/macchina virtuale di Azure.| Backup del database di SQL Server.<br/><br/> Backup di cluster di SQL Server supportato.<br/><br/>Database archiviati in volumi condivisi cluster non supportati. |
**Exchange 2016**<br/><br/> **Exchange 2013**<br/><br/> **Exchange 2010** | Server di Backup di Microsoft Azure v3, v2, V1 | In locale. | Eseguire il backup di server Exchange autonomo, database in un DAG.<br/><br/> Ripristino della cassetta postale e del database delle cassette postali in un DAG.<br/><br/> ReFS non supportato.<br/><br/> Backup di cluster di dischi non condivisi.<br/><br/> Backup del server Exchange configurato per la replica continua. |
**SharePoint 2016**<br/><br/> **SharePoint 2013**<br/><br/> **SharePoint 2010** | Server di Backup di Microsoft Azure v3, v2, v1 | In locale/macchina virtuale di Azure. | Eseguire il backup di farm, server web front-end.<br/><br/> Ripristino di farm, database, app web, file o elemento elenco, ricerca SharePoint, server web front-end.<br/><br/> È possibile eseguire il backup di una farm utilizzando SQL Server AlwaysOn per database di contenuto. |
**Hyper-V in Windows Server 2016**<br/><br/> **Windows Server 2012 R2/2012** (Datacenter/Standard)<br/><br/> **Windows Server 2008 R2 (con SP1)** | Server di Backup di Microsoft Azure v3, v2, v1 | In locale. | **Agente del server di Backup di Microsoft Azure nell'host Hyper-V**: Backup di intere macchine virtuali e file di dati host. Backup di macchine virtuali con archiviazione locale, macchine virtuali in cluster con archiviazione in volumi condivisi cluster e macchine virtuali con archiviazione in file server SMB.<br/><br/> **Agente del server di Backup di Microsoft Azure in una macchina virtuale guest**: Backup dei carichi di lavoro in esecuzione nella macchina virtuale. Volumi condivisi cluster.<br/><br/> **Ripristino**: macchina virtuale, ripristino a livello di elemento di disco rigido virtuale/volume/cartelle/file.<br/><br/> **Macchine virtuali Linux**: Quando Hyper-V è in esecuzione in Windows Server 2012 R2 e versioni successive, eseguire il backup. Il ripristino per le macchine virtuali Linux è per l'intera macchina. |
**Macchine virtuali VMware: vCenter/vSphere ESXi 5.5/6.0/6.5** | Server di Backup di Microsoft Azure v3, v2, v1<br/><br/> Il server di Backup di Microsoft Azure v1 richiede l'aggiornamento cumulativo 1 | In locale. | Eseguire il backup di macchine virtuali VMware in archiviazione volumi condivisi cluster, NFS e SAN.<br/><br/> Ripristino dell'intera macchina virtuale.<br/><br/> Backup di Windows/Linux.<br/><br/> Ripristino a livello di elemento di cartella/file solo per le macchine virtuali Windows.<br/><br/> Le vApp VMware non sono supportate.<br/><br/> Il ripristino per le macchine virtuali Linux è per l'intera macchina. | 



## <a name="supported-backups-to-dpm"></a>Backup in DPM supportati

La tabella seguente riepiloga gli elementi di cui è possibile eseguire il backup in DPM da computer locali e macchine virtuali di Azure.



**Backup** | **DPM** | **Dettagli**
--- | --- | --- 
**Windows 10<br/>Windows 8.1<br/>Windows 8<br/>Windows 7**<br/><br/>(32/64 bit) | Solo in locale.<br/><br/> Per il backup di Windows 10 con DPM 2012 R2, si consiglia di installare [11 Update](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager). | Volume/condivisione/cartella/file.<br/><br/> Volumi deduplicati supportati.<br/><br/> I volumi devono essere almeno di 1 GB e NTFS.
**Windows Server 2016 (Datacenter, Standard, non Nano)**<br/><br/> 64/32 bit | In locale/macchina virtuale di Azure.<br/><br/> Solo DPM 2016.| Volume/condivisione/cartella/file; stato del sistema/bare metal.<br/><br/> Volumi deduplicati supportati.
**Windows Server 2012 R2 (Datacenter e Standard)**<br/><br/> 64/32 bit | In locale/macchina virtuale di Azure. | **Protezione locale**: Volume/condivisione/cartella/file; stato del sistema/bare metal.<br/><br/> **Protezione della macchina virtuale di Azure**: Volume/condivisione/cartella/file.<br/><br/> Volumi deduplicati supportati con DPM 2012 R2 e versioni successive.
**Windows Server 2012 con SP1 (Datacenter e Standard)**<br/><br/> 64/32 bit | In locale/macchina virtuale di Azure. | **Protezione locale**: Volume/condivisione/cartella/file; stato del sistema/bare metal.<br/><br/> **Protezione della macchina virtuale di Azure**: Volume/condivisione/cartella/file.<br/><br/> Volumi deduplicati supportati con DPM 2012 R2 e versioni successive.
**Windows Server 2012 (Datacenter e Standard)**<br/><br/> 64/32 bit |  In locale/macchina virtuale di Azure. | **Protezione locale**: Volume/condivisione/cartella/file; stato del sistema/bare metal.<br/><br/> **Protezione della macchina virtuale di Azure**: Volume/condivisione/cartella/file.<br/><br/> Volumi deduplicati supportati con DPM 2012 R2 e versioni successive.
**Windows 2008 R2 con SP1 (Standard ed Enterprise)**<br/><br/> 64/32 bit | In locale/macchina virtuale di Azure.<br/><br/> [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) deve essere installato. |   **Protezione locale**: Volume/condivisione/cartella/file; stato del sistema/bare metal.<br/><br/> **Protezione della macchina virtuale di Azure**: Volume/condivisione/cartella/file.
**Windows 2008 R2 (Standard ed Enterprise)**<br/><br/> 64/32 bit | In locale.<br/><br/> DPM non può essere installato come macchina virtuale VMware.<br/><br/> DPM in esecuzione in una macchina virtuale di Azure non è supportato. | **Protezione locale**: Volume/condivisione/cartella/file; stato del sistema/bare metal.
**Windows Server 2008 con SP2**<br/><br/> 64/32 bit | Solo in locale.<br/><br/> DPM è supportato se in esecuzione come macchina virtuale VMware. L'esecuzione come server fisico o macchina virtuale Hyper-V non è supportata. | Volume/condivisione/cartella/file; stato del sistema/bare metal.
**Windows Storage Server 2008** | DPM in locale in esecuzione come server fisico o macchina virtuale Hyper-V. | Volume/condivisione/cartella/file; stato del sistema/bare metal.
**SQL Server 2017** | CONFIGURAZIONE SUPERFICIE DI ATTACCO DI DPM; DPM 2016 esegue l'aggiornamento cumulativo 5 o versione successiva.<br/><br/> In locale/macchina virtuale di Azure.| Backup del database di SQL Server.<br/><br/> Backup di cluster di SQL Server supportato.<br/><br/>Database archiviati in volumi condivisi cluster non supportati.
**SQL Server 2016 con SP1** | Non viene fornito il supporto per DPM 2012 R2; viene fornito il supporto per DPM SAC, DPM 2016 con l'aggiornamento cumulativo 4 o versioni successive.<br/><br/> In locale/macchina virtuale di Azure.| Backup del database di SQL Server.<br/><br/> Backup di cluster di SQL Server supportato.<br/><br/>Database archiviati in volumi condivisi cluster non supportati.
**SQL Server 2016** | Non viene fornito il supporto per DPM 2012 R2. Supportati per DPM Configurazione superficie di attacco, DPM 2016 aggiornamento cumulativo 2 e versioni successive.<br/><br/> In locale/macchina virtuale di Azure.| Backup del database di SQL Server.<br/><br/> Backup di cluster di SQL Server supportato.<br/><br/>Database archiviati in volumi condivisi cluster non supportati.
**SQL Server 2014**<br/><br/> **SQL Server 2012/SP1/SP2**<br/><br/> **SQL Server 2008 R2**<br/><br/> **SQL Server 2008** | SQL Server 2014 con DPM 2012 R2 che esegue l'aggiornamento cumulativo 4 e versioni successive.<br/><br/> In locale/macchina virtuale di Azure.| Backup del database di SQL Server.<br/><br/> Backup di cluster di SQL Server supportato.<br/><br/>Database archiviati in volumi condivisi cluster non supportati.
**Exchange 2016**<br/><br/> **Exchange 2013**<br/><br/> **Exchange 2010** | Per Exchange 2016, DPM 2012 R2 richiede l'aggiornamento cumulativo 9 o versioni successive.<br/><br/> Locale | Eseguire il backup di server Exchange autonomo, database in un DAG.<br/><br/> Ripristino della cassetta postale e del database delle cassette postali in un DAG.<br/><br/> ReFS non supportato.<br/><br/> Backup di cluster di dischi non condivisi.<br/><br/> Backup del server Exchange configurato per la replica continua.
**SharePoint 2016**<br/><br/> **SharePoint 2013**<br/><br/> **SharePoint 2010** | SharePoint 2016 in DPM 2016 e versioni successive.<br/><br/>In locale/macchina virtuale di Azure. | Eseguire il backup di farm, server web front-end.<br/><br/> Ripristino di farm, database, app web, file o elemento elenco, ricerca SharePoint, server web front-end.<br/><br/> È possibile eseguire il backup di una farm utilizzando SQL Server AlwaysOn per database di contenuto.
**Hyper-V in Windows Server 2016**<br/><br/> **Windows Server 2012 R2/2012** (Datacenter/Standard)<br/><br/> **Windows Server 2008 R2 (con SP1)** | Hyper-V in 2016 supportati per DPM 2016 e versioni successive.<br/><br/> In locale. | **Agente del server di Backup di Microsoft Azure nell'host Hyper-V**: Backup di intere macchine virtuali e file di dati host. Backup di macchine virtuali con archiviazione locale, macchine virtuali in cluster con archiviazione in volumi condivisi cluster e macchine virtuali con archiviazione in file server SMB.<br/><br/> **Agente del server di Backup di Microsoft Azure in una macchina virtuale guest**: Backup dei carichi di lavoro in esecuzione nella macchina virtuale. Volumi condivisi cluster.<br/><br/> **Ripristino**: macchina virtuale, ripristino a livello di elemento di disco rigido virtuale/volume/cartelle/file.<br/><br/> **Macchine virtuali Linux**: Quando Hyper-V è in esecuzione in Windows Server 2012 R2 e versioni successive, eseguire il backup. Il ripristino per le macchine virtuali Linux è per l'intera macchina.
**Macchine virtuali VMware: vCenter/vSphere ESXi 5.5/6.0/6.5** | Server di Backup di Microsoft Azure v3, v2, v1<br/><br/> DPM 2012 R2 richiede l'aggiornamento cumulativo 1 di System Center <br/><br/>In locale. | Eseguire il backup di macchine virtuali VMware in archiviazione volumi condivisi cluster, NFS e SAN.<br/><br/> Ripristino dell'intera macchina virtuale.<br/><br/> Backup di Windows/Linux.<br/><br/> Ripristino a livello di elemento di cartella/file solo per le macchine virtuali Windows.<br/><br/> Le vApp VMware non sono supportate.<br/><br/> Il ripristino per le macchine virtuali Linux è per l'intera macchina.


- Si noti che i carichi di lavoro in cluster il backup di DPM/MABS deve essere nello stesso dominio DPM/MABS o in un dominio trusted o figlio. 
- È possibile usare l'autenticazione NTLM/del certificato per eseguire il backup dei dati in gruppi di lavoro o domini non attendibili.



## <a name="next-steps"></a>Passaggi successivi

- [Vedere altre informazioni](backup-architecture.md#architecture-back-up-to-dpmmabs) sull'architettura del server di Backup di Microsoft Azure.
- [Rivedere](backup-support-matrix-mars-agent.md) gli scenari supportati per l'agente di Servizi di ripristino di Microsoft Azure.
- [Installare](backup-azure-microsoft-azure-backup.md) un server di Backup di Microsoft Azure.
- [Configurare DPM](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-180).
