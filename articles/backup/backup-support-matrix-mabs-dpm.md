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
ms.openlocfilehash: 74b6d953939a569a3240c9d64134d143dc4f179e
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2019
ms.locfileid: "56431025"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-serversystem-center-dpm"></a>Matrice di supporto per il backup con il server di Backup di Microsoft Azure o System Center DPM

È possibile usare il [servizio Backup di Azure](backup-overview.md) per eseguire il backup di computer e carichi di lavoro locali, nonché di macchine virtuali di Azure. Questo articolo riepiloga le impostazioni e le limitazioni del supporto per l'esecuzione del backup di computer tramite il server di Backup di Microsoft Azure o System Center Data Protection Manager (DPM) e il servizio Backup di Azure.

## <a name="about-mabsdpm"></a>Informazioni sul server di Backup di Microsoft Azure e DPM

[System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview?view=sc-dpm-1807) è una soluzione aziendale in grado di configurare, facilitare e gestire il backup e il ripristino dei dati e dei computer di un'organizzazione e fa parte della famiglia di prodotti [System Center](https://www.microsoft.com/cloud-platform/system-center-pricing).


Il server di Backup di Microsoft Azure è un prodotto server che può essere usato per eseguire il backup dei server fisici locali, delle macchine virtuali (VM) e delle app in esecuzione su di essi, nonché delle macchine virtuali di Azure.

- Il server di Backup di Microsoft Azure è basato su System Center Data Protection Manager (DPM) e offre funzionalità analoghe, con queste due differenze:
    - Per eseguire il server di Backup di Microsoft Azure non è necessaria alcuna licenza System Center.
    - Sia per il server di Backup di Microsoft Azure che per DPM, Azure offre un archivio di backup a lungo termine. DPM inoltre consente di eseguire il backup dei dati per l'archiviazione a lungo termine su nastro. Questa funzionalità non è disponibile con il server di Backup di Microsoft Azure.
- È possibile scaricare il server di Backup di Microsoft Azure dall'[Area download Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=57520) ed eseguirlo in locale oppure in una macchina virtuale di Azure in Azure.

DPM e il server di Backup di Microsoft Azure supportano il backup di una vasta gamma di app e di sistemi operativi server e client. Essi offrono diversi scenari di backup:
    - È possibile eseguire il backup a livello di computer con un backup dello stato del sistema o un backup bare metal.
    - È possibile eseguire il backup di volumi, condivisioni, cartelle e file specifici.
    - È possibile eseguire il backup di app specifiche usando impostazioni ottimizzate con riconoscimento delle app stesse.

## <a name="mabsdpm-backup"></a>Backup tramite il server di Backup di Microsoft Azure o DPM

Il backup tramite il server di Backup di Microsoft Azure o DPM e il servizio Backup di Azure avviene come descritto di seguito:

1. L'agente di protezione di DPM o del server di Backup di Microsoft Azure viene installato in ogni computer di cui verrà eseguito il backup. 
2. Viene eseguito il backup dei computer e delle app nell'archivio locale in DPM o nel server di Backup di Microsoft Azure.
3. L'agente di Servizi di ripristino di Microsoft Azure viene installato nel server DPM o nel server di Backup di Microsoft Azure.
4. L'agente di Servizi di ripristino di Microsoft Azure esegue il backup dei dischi di DPM o del server di Backup di Microsoft Azure in un insieme di credenziali di Servizi di ripristino di backup in Azure tramite il servizio Backup di Azure.

Per altre informazioni:
- [Vedere altre informazioni](backup-architecture.md#architecture-back-up-to-dpmmabs) sull'architettura del server di Backup di Microsoft Azure.
- [Rivedere](backup-support-matrix-mars-agent.md) gli scenari supportati per l'agente di Servizi di ripristino di Microsoft Azure.

## <a name="supported-scenarios"></a>Scenari supportati 

**Scenario** | **Agente** | **Posizione**
--- | --- | ---
**Backup di carichi di lavoro e computer locali** | L'agente di protezione di DPM o del server di Backup di Microsoft Azure viene eseguito nei computer di cui si intende eseguire il backup<br/><br/> Agente di Servizi di ripristino di Microsoft Azure nel server di Backup di Microsoft Azure o nel server DPM | Il server di Backup di Microsoft Azure o DPM deve essere in esecuzione in locale
**Backup di carichi di lavoro e macchine virtuali di Azure** | Agente di protezione di DPM o del server di Backup di Microsoft Azure nel computer protetto<br/><br/> Agente di Servizi di ripristino di Microsoft Azure nel server di Backup di Microsoft Azure o in DPM | DPM o il server di Backup di Microsoft Azure deve essere in esecuzione in una macchina virtuale di Azure.

## <a name="supported-deployments"></a>Distribuzioni supportate

È possibile distribuire DPM o il server di Backup di Microsoft Azure come riepilogato nella tabella.

**Distribuzione** | **Supporto** | **Dettagli**
--- | --- | ---
**Distribuito in locale** | Server fisico<br/><br/>Macchina virtuale Hyper-V<br/><br/> Macchina virtuale VMware | Se DPM o il server di Backup di Microsoft Azure viene installato come macchina virtuale VMware, eseguirà il backup solo delle macchine virtuali VMware e dei carichi di lavoro in esecuzione in tali macchine virtuali.
**Distribuito come macchina virtuale di Azure Stack** | Solo server di Backup di Microsoft Azure | Non è possibile usare DPM per eseguire il backup di macchine virtuali di Azure Stack.
**Distribuito come macchina virtuale di Azure** | Protegge le macchine virtuali di Azure e i carichi di lavoro in esecuzione in tali macchine virtuali. | Se eseguito in Azure, il server di Backup di Microsoft Azure o DPM non può eseguire il backup di computer locali.


## <a name="supported-mabs-and-dpm-operating-systems"></a>Sistemi operativi supportati per il server di Backup di Microsoft Azure e DPM

Il servizio Backup di Azure è in grado di eseguire il backup di DPM o del server di Backup di Microsoft Azure con uno qualsiasi dei sistemi operativi seguenti. Devono essere in uso gli ultimi Service Pack e aggiornamenti dei sistemi operativi.

**Scenario** | **Server di Backup di Microsoft Azure/DPM** 
--- | --- 
**Server di Backup di Microsoft Azure in una macchina virtuale di Azure** | Windows Server 2012 R2<br/><br/> Windows 2016 Datacenter<br/><br/> Windows 2019 Datacenter<br/><br/> È consigliabile iniziare con un'immagine proveniente dal marketplace.<br/><br/> Almeno A2 Standard con due core e 3,5 GB di RAM. 
**DPM in una macchina virtuale di Azure** | System Center 2012 R2 con Update 3 o versioni successive.<br/><br/> Sistema operativo Windows [richiesto da System Center](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server).<br/><br/> È consigliabile iniziare con un'immagine proveniente dal marketplace.<br/><br/> Almeno A2 Standard con due core e 3,5 GB di RAM. 
**Server di Backup di Microsoft Azure in locale** | Sistemi operativi a 64 bit supportati:<br/><br/> - A partire dal server di Backup di Microsoft Azure v3: Windows Server 2019 (Standard, Datacenter, Essentials) <br/><br/> A partire dal server di Backup di Microsoft Azure v2: Windows Server 2016 (Standard, Datacenter, Essentials)<br/><br/> Tutte le versioni del server di Backup di Microsoft Azure: Windows Server 2012 R2/2012 (Standard, Datacenter, Foundation)<br/><br/>Tutte le versioni del server di Backup di Microsoft Azure: Windows Storage Server 2012 R2/2012 (Standard/Workgroup)
**DPM in locale** | Server fisico/macchina virtuale Hyper-V: System Center 2012 SP1 o versioni successive.<br/><br/> Macchina virtuale VMware: System Center 2012 R2 con Update 5 o versioni successive. 



## <a name="management-support"></a>Supporto della gestione
**Problema** | **Dettagli**
--- | ---
**Installazione** | È necessario installare DPM o il server di Backup di Microsoft Azure in un computer con un unico scopo.<br/><br/> Non installare DPM o il server di Backup di Microsoft Azure in un controller di dominio, in un computer con l'installazione del ruolo Server applicazioni, in un computer che esegue Exchange Server o System Center Operations Manager oppure in un nodo di un cluster.<br/><br/> [Rivedere](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server) tutti i requisiti di sistema di DPM.
**Dominio** | Il server DPM o il server di Backup di Microsoft Azure deve essere aggiunto a un dominio. Installare e quindi aggiungere DPM o il server di Backup di Microsoft Azure a un dominio. Lo spostamento di DPM o del server di Backup di Microsoft Azure in un nuovo dominio dopo la distribuzione non è supportato.
**Archiviazione** | La funzionalità Modern Backup Storage (MBS) è supportata a partire da DPM 2016 o a partire dal server di Backup di Microsoft Azure v2. Non è disponibile per il server di Backup di Microsoft Azure v1.
**Aggiornamento del server di Backup di Microsoft Azure** | È possibile installare direttamente il server di Backup di Microsoft Azure v3 oppure eseguire l'aggiornamento dal server di Backup di Microsoft Azure v2 al server di Backup di Microsoft Azure v3. [Vedere altre informazioni](backup-azure-microsoft-azure-backup.md#upgrade-mabs).
**Spostamento del server di Backup di Microsoft Azure** | Se si usa MBS, è possibile spostare il server di Backup di Microsoft Azure in un nuovo server conservando l'archivio.<br/><br/> Il server deve avere lo stesso nome dell'originale. Non è possibile cambiare il nome se si intende mantenere lo stesso pool di archiviazione e usare lo stesso database del server di Backup di Microsoft Azure per archiviare i punti di ripristino dei dati.<br/><br/> Si dovrà disporre di un backup del database del server di Backup di Microsoft Azure perché sarà necessario ripristinarlo.




## <a name="mabs-support-on-azure-stack"></a>Supporto per il server di Backup di Microsoft Azure in Azure Stack

Se si distribuisce il server di Backup di Microsoft Azure in una macchina virtuale di Azure Stack, sarà possibile gestire il backup delle macchine virtuali di Azure Stack e dei carichi di lavoro da un'unica posizione.

**Componente** | **Dettagli**
--- | --- 
**Server di Backup di Microsoft Azure in una macchina virtuale di Azure Stack** | Almeno con dimensioni A2. È consigliabile iniziare con un'immagine di Windows Server 2012 R2 o Windows Server 2016 da Azure Marketplace.<br/><br/> Non installare altro nella macchina virtuale del server di Backup di Microsoft Azure.
**Archiviazione del server di Backup di Microsoft Azure** | Usare un account di archiviazione separato per la macchina virtuale di Backup di Microsoft Azure. L'agente di Servizi di ripristino di Microsoft Azure in esecuzione nel server di Backup di Microsoft Azure necessita di spazio di archiviazione temporaneo per il percorso della cache e per contenere i dati ripristinati dal cloud.
**Pool di archiviazione del server di Backup di Microsoft Azure** | Le dimensioni del pool di archiviazione del server di Backup di Microsoft Azure dipendono dal numero e dalle dimensioni dei dischi collegati alla macchina virtuale di tale server. Per ogni dimensione di macchina virtuale di Azure Stack è previsto un numero massimo di dischi. Ad esempio, per le dimensioni A2 il numero massimo è quattro dischi.
**Conservazione del server di Backup di Microsoft Azure** | Conservare nei dischi locali del server di Backup di Microsoft Azure al massimo per cinque giorni i dati di cui è stato eseguito il backup.
**Aumento delle prestazioni del server di Backup di Microsoft Azure** | Per aumentare le prestazioni della distribuzione, è possibile aumentare le dimensioni della macchina virtuale del server di Backup di Microsoft Azure. Ad esempio, passare dalla serie A alla serie D.<br/><br/> È anche possibile eseguire con regolarità l'offload dei dati con backup in Azure e, se necessario, distribuire server di Backup di Microsoft Azure aggiuntivi.
.NET Framework nel server di Backup di Microsoft Azure | Nella macchina virtuale del server di Backup di Microsoft Azure deve essere installato .NET Framework 3.3 SP1 o versioni successive.
**Dominio del server di Backup di Microsoft Azure** | La macchina virtuale del server di Backup di Microsoft Azure deve essere aggiunta a un dominio. Un utente del dominio con privilegi di amministratore deve installare il server di Backup di Microsoft Azure nella macchina virtuale.
**Backup dei dati di macchine virtuali di Azure Stack** | È possibile eseguire il backup di file, cartelle e app.
**Backup supportato** | Per le macchine virtuali di cui si intende eseguire il backup sono supportati questi sistemi operativi:<br/><br/> - Windows Server Canale semestrale (Datacenter/Enterprise/Standard)<br/><br/> Windows Server 2016/2012 R2/2012/2008 R2.
**Supporto SQL per macchine virtuali di Azure Stack** | È possibile eseguire il backup di SQL Server 2016/2014/2012 SP1.<br/><br/> È possibile eseguire il backup e il ripristino del database.
**Supporto SharePoint per macchine virtuali di Azure Stack** | SharePoint 2016/2013/2010.<br/><br/> È possibile eseguire il backup e il ripristino della farm, del database, del server Web front-end.
**Requisiti di rete per macchine virtuali sottoposte a backup** | Tutte le macchine virtuali nel carico di lavoro di Azure Stack devono essere connesse alla stessa rete virtuale e devono appartenere alla stessa sottoscrizione.


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
Connesso | Attiva | Backup su disco di DPM o del server di Backup di Microsoft Azure<br/><br/> Backup in Azure<br/><br/> Ripristino da disco<br/><br/> Ripristino da Azure
Connesso | Scaduta/sottoposta a deprovisioning | Nessun backup su disco o in Azure.<br/><br/> Se la sottoscrizione è scaduta, è possibile eseguire il ripristino dal disco o da Azure.<br/><br/> Se sono state rimosse le autorizzazioni della sottoscrizione, non è possibile eseguire il ripristino dal disco o da Azure. I punti di ripristino di Azure vengono eliminati.
Nessuna connettività per più di 15 giorni | Attiva | Nessun backup su disco o in Azure.<br/><br/> È possibile eseguire il ripristino dal disco o da Azure.
Nessuna connettività per più di 15 giorni | Scaduta/sottoposta a deprovisioning | Nessun backup su disco o in Azure.<br/><br/> Se la sottoscrizione è scaduta, è possibile eseguire il ripristino dal disco o da Azure.<br/><br/> Se sono state rimosse le autorizzazioni della sottoscrizione, non è possibile eseguire il ripristino dal disco o da Azure. I punti di ripristino di Azure vengono eliminati.



## <a name="dpmmabs-storage-support"></a>Supporto dell'archiviazione per DPM o il server di Backup di Microsoft Azure

I dati di cui è stato eseguito il backup in DPM o nel server di Backup di Microsoft Azure vengono archiviati nel disco locale. 

**Archiviazione** | **Dettagli**
--- | ---
**MBS** | La funzionalità Modern Backup Storage (MBS) è supportata a partire da DPM 2016 o a partire dal server di Backup di Microsoft Azure v2. Non è disponibile per il server di Backup di Microsoft Azure v1.
**Archiviazione del server di Backup di Microsoft Azure in una macchina virtuale di Azure** | I dati vengono archiviati nei dischi di Azure collegati alla macchina virtuale di DPM o del server di Backup di Microsoft Azure e gestiti in DPM o nel server di Backup di Microsoft Azure. Il numero di dischi che è possibile usare per il pool di archiviazione di DPM o del server di Backup di Microsoft Azure dipende dalle dimensioni della macchina virtuale.<br/><br/> Macchina virtuale A2: 4 dischi; macchina virtuale A3: 8 dischi; macchina virtuale A4: 16 dischi, con dimensioni massime di 1 TB per ogni disco. Questo determina il pool di archiviazione di backup totale disponibile.<br/><br/> La quantità di dati di cui è possibile eseguire il backup dipende dal numero e dalle dimensioni dei dischi collegati.
**Conservazione dei dati del server di Backup di Microsoft Azure in una macchina virtuale di Azure** | È consigliabile conservare i dati per un giorno nel disco di Azure di DPM o del server di Backup di Microsoft Azure ed eseguire il backup da DPM o dal server di Backup di Microsoft Azure all'insieme di credenziali per conservarli più a lungo. In questo modo è possibile proteggere una maggiore quantità di dati tramite l'offload nel servizio Backup di Azure.


### <a name="modern-backup-storage-mbs"></a>Modern Backup Storage (MBS)
A partire da DPM 2016 o a partire dal server di Backup di Microsoft Azure v2 (in esecuzione in Windows Server 2016), è possibile sfruttare i vantaggi offerti da Modern Backup Storage (MBS).

- I backup di MBS vengono archiviati in un disco ReFS (Resilient File System).
- MBS use la clonazione dei blocchi ReFS per un backup più rapido e un uso più efficiente dello spazio di archiviazione.
- Quando si aggiungono volumi al pool di archiviazione locale di DPM o del server di Backup di Microsoft Azure, li si configura con le lettere. È quindi possibile configurare l'archiviazione dei carichi di lavoro in volumi diversi.
- Quando si creano gruppi protezione dati per eseguire il backup dei dati in DPM o nel server di Backup di Microsoft Azure, si seleziona l'unità che si intende usare. Ad esempio, è possibile decidere di archiviare i backup per SQL o altri carichi di lavoro con un numero elevato di operazioni di I/O al secondo in un'unità ad alte prestazioni e di archiviare i carichi di lavoro sottoposti a backup meno frequentemente in un'unità con prestazioni inferiori.


## <a name="supported-backups-to-mabs"></a>Backup nel server di Backup di Microsoft Azure supportati

La tabella seguente riepiloga gli elementi di cui è possibile eseguire il backup nel server di Backup di Microsoft Azure da computer locali e macchine virtuali di Azure.


**Backup** | **Versioni** | **Server di Backup di Microsoft Azure** | **Dettagli**
--- | --- | --- | ---
**Windows 10, 8.1, 8, 7**<br/><br/>(32/64 bit) | Server di Backup di Microsoft Azure v3, v2, V1 | In locale | Volume/condivisione/cartella/file<br/><br/> Volumi deduplicati supportati<br/><br/> I volumi devono essere almeno di 1 GB e NTFS.
**Windows Server 2016 (Datacenter, Standard, non Nano)**<br/><br/> 64/32 bit | Server di Backup di Microsoft Azure v3, v2 | In locale/macchina virtuale di Azure.| Volume/condivisione/cartella/file; stato del sistema/bare metal<br/><br/> Volumi deduplicati supportati.
**Windows Server 2012 R2 (Datacenter e Standard)**<br/><br/> 64/32 bit | Server di Backup di Microsoft Azure v3, v2, v1 | In locale/macchina virtuale di Azure. | **Protezione locale**: Volume/condivisione/cartella/file; stato del sistema/bare metal<br/><br/> **Protezione della macchina virtuale di Azure**: Volume/condivisione/cartella/file<br/><br/> Volumi deduplicati supportati.
**Windows Server 2012 con SP1 (Datacenter e Standard)**<br/><br/> 64/32 bit | Server di Backup di Microsoft Azure v3, v2, v1<br/><br/> [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) deve essere installato. | In locale/macchina virtuale di Azure. | **Protezione locale**: Volume/condivisione/cartella/file; stato del sistema/bare metal<br/><br/> **Protezione della macchina virtuale di Azure**: Volume/condivisione/cartella/file<br/><br/> Volumi deduplicati supportati.
**Windows Server 2012 (Datacenter e Standard)**<br/><br/> 64/32 bit | Server di Backup di Microsoft Azure v1 | In locale/macchina virtuale di Azure. | **Protezione locale**: Volume/condivisione/cartella/file; stato del sistema/bare metal<br/><br/> **Protezione della macchina virtuale di Azure**: Volume/condivisione/cartella/file<br/><br/> Volumi deduplicati supportati.
**Windows 2008 R2 con SP1 (Standard ed Enterprise)**<br/><br/> 64/32 bit | Supportato dal server di Backup di Microsoft Azure v3, v2, v1.<br/><br/> [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) deve essere installato. | In locale/macchina virtuale di Azure. |   **Protezione locale**: Volume/condivisione/cartella/file; stato del sistema/bare metal<br/><br/> **Protezione della macchina virtuale di Azure**: Volume/condivisione/cartella/file<br/><br/> Volumi deduplicati supportati.
**Windows 2008 R2 (Standard ed Enterprise)**<br/><br/> 64/32 bit | Server di Backup di Microsoft Azure v1. Per il server di Backup di Microsoft Azure v2/v3, nel sistema operativo deve essere in uso SP1. | In locale/macchina virtuale di Azure. | **Protezione locale**: Volume/condivisione/cartella/file; stato del sistema/bare metal<br/><br/> **Protezione della macchina virtuale di Azure**: Volume/condivisione/cartella/file<br/><br/> Volumi deduplicati supportati.
**Windows Server 2008 con SP2**<br/><br/> 64/32 bit | Server di Backup di Microsoft Azure v1, v2, v3 | Viene fornito il supporto solo per v1 quando il server di Backup di Microsoft Azure è distribuito come computer fisico locale o come macchina virtuale Hyper-V.<br/><br/> Il server di Backup di Microsoft Azure v1, 2, 3 è supportato quando tale server è distribuito come macchina virtuale VMware.<br/><br/> Non viene fornito il supporto per il server di Backup di Microsoft Azure in esecuzione in una macchina virtuale di Azure | Volume/condivisione/cartella/file; stato del sistema/bare metal.
**Windows Storage Server 2008** | Server di Backup di Microsoft Azure v1, v2, v3 | Server di Backup di Microsoft Azure come server fisico locale/macchina virtuale Hyper-V | Non viene fornito il supporto per il server di Backup di Microsoft Azure in esecuzione in una macchina virtuale di Azure. | Volume/condivisione/cartella/file; stato del sistema/bare metal.
**SQL Server 2017** | Server di Backup di Microsoft Azure v3 | In locale/macchina virtuale di Azure.| Backup del database di SQL Server.<br/><br/> Backup di cluster di SQL Server supportato.<br/><br/>Database archiviati in volumi condivisi cluster non supportati.
**SQL Server 2016/2016 con SP1** | Server di Backup di Microsoft Azure v3, v2 | In locale/macchina virtuale di Azure.| Backup del database di SQL Server.<br/><br/> Backup di cluster di SQL Server supportato.<br/><br/>Database archiviati in volumi condivisi cluster non supportati.
**SQL Server 2014**<br/><br/> **SQL Server 2012/SP1/SP2**<br/><br/> **SQL Server 2008 R2**<br/><br/> **SQL Server 2008** | Server di Backup di Microsoft Azure v3, v2, v1 | In locale/macchina virtuale di Azure.| Backup del database di SQL Server.<br/><br/> Backup di cluster di SQL Server supportato.<br/><br/>Database archiviati in volumi condivisi cluster non supportati.
**Exchange 2016**<br/><br/> **Exchange 2013**<br/><br/> **Exchange 2010** | Server di Backup di Microsoft Azure v3, v2, V1 | In locale. | Backup del server Exchange autonomo, database in un gruppo di disponibilità dei database (DAG)<br/><br/> Ripristino della cassetta postale e del database delle cassette postali in un DAG.<br/><br/> ReFS non supportato.<br/><br/> Backup di cluster di dischi non condivisi.<br/><br/> Backup del server Exchange configurato per la replica continua.
**SharePoint 2016**<br/><br/> **SharePoint 2013**<br/><br/> **SharePoint 2010** | Server di Backup di Microsoft Azure v3, v2, v1 | In locale/macchina virtuale di Azure | Backup di farm, server Web front-end.<br/><br/> Ripristino di farm, database, app Web, file o elemento elenco, ricerca SharePoint, server Web front-end.<br/><br/> Non è possibile eseguire il backup di una farm con AlwaysOn per i database del contenuto.
**Hyper-V in Windows Server 2016**<br/><br/> **Windows Server 2012 R2/2012** (Datacenter/Standard)<br/><br/> **Windows Server 2008 R2 (con SP1)** | Server di Backup di Microsoft Azure v3, v2, v1 | In locale | **Agente del server di Backup di Microsoft Azure nell'host Hyper-V**: Backup di intere macchine virtuali e file di dati host. Backup di macchine virtuali con archiviazione locale, macchine virtuali in cluster con archiviazione in volumi condivisi cluster e macchine virtuali con archiviazione in file server SMB.<br/><br/> **Agente del server di Backup di Microsoft Azure in una macchina virtuale guest**: Backup dei carichi di lavoro in esecuzione nella macchina virtuale. Volumi condivisi cluster.<br/><br/> **Ripristino**: macchina virtuale, ripristino a livello di elemento di disco rigido virtuale/volume/cartelle/file.<br/><br/> **Macchine virtuali Linux**: backup quando Hyper-V è in esecuzione in Windows Server 2012 R2 e versioni successive. Il ripristino per le macchine virtuali Linux è per l'intera macchina.
**Macchine virtuali VMware: vCenter/vSphere ESXi 5.5/6.0/6.5** | Server di Backup di Microsoft Azure v3, v2, v1.<br/><br/> Il server di Backup di Microsoft Azure v1 richiede l'aggiornamento cumulativo 1 | In locale | Backup delle macchine virtuali VMware in volumi condivisi cluster, NFS e rete di archiviazione.<br/><br/> Ripristino dell'intera macchina virtuale.<br/><br/> Backup di Windows/Linux.<br/><br/> Ripristino a livello di elemento di cartella/file solo per le macchine virtuali Windows.<br/><br/> Le vApp VMware non sono supportate.<br/><br/> Il ripristino per le macchine virtuali Linux è per l'intera macchina.



## <a name="supported-backups-to-dpm"></a>Backup in DPM supportati

La tabella seguente riepiloga gli elementi di cui è possibile eseguire il backup in DPM da computer locali e macchine virtuali di Azure.



**Backup** | **DPM** | **Dettagli**
--- | --- | --- 
**Windows 10, 8.1, 8, 7**<br/><br/>(32/64 bit) | Solo in locale.<br/><br/> Per il back di Windows 10 con DPM 2012 R2, è consigliabile installare l'[Update 11](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager). | Volume/condivisione/cartella/file<br/><br/> Volumi deduplicati supportati<br/><br/> I volumi devono essere almeno di 1 GB e NTFS.
**Windows Server 2016 (Datacenter, Standard, non Nano)**<br/><br/> 64/32 bit | In locale/macchina virtuale di Azure.<br/><br/> Solo DPM 2016.| Volume/condivisione/cartella/file; stato del sistema/bare metal<br/><br/> Volumi deduplicati supportati.
**Windows Server 2012 R2 (Datacenter e Standard)**<br/><br/> 64/32 bit | In locale/macchina virtuale di Azure | **Protezione locale**: Volume/condivisione/cartella/file; stato del sistema/bare metal<br/><br/> **Protezione della macchina virtuale di Azure**: Volume/condivisione/cartella/file<br/><br/> Volumi deduplicati supportati a partire da DPM 2012 R2.
**Windows Server 2012 con SP1 (Datacenter e Standard)**<br/><br/> 64/32 bit | In locale/macchina virtuale di Azure. | **Protezione locale**: Volume/condivisione/cartella/file; stato del sistema/bare metal<br/><br/> **Protezione della macchina virtuale di Azure**: Volume/condivisione/cartella/file<br/><br/> Volumi deduplicati supportati a partire da DPM 2012 R2.
**Windows Server 2012 (Datacenter e Standard)**<br/><br/> 64/32 bit |  In locale/macchina virtuale di Azure. | **Protezione locale**: Volume/condivisione/cartella/file; stato del sistema/bare metal<br/><br/> **Protezione della macchina virtuale di Azure**: Volume/condivisione/cartella/file<br/><br/> Volumi deduplicati supportati a partire da DPM 2012 R2.
**Windows 2008 R2 con SP1 (Standard ed Enterprise)**<br/><br/> 64/32 bit | In locale/macchina virtuale di Azure <br/><br/> [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) deve essere installato. |   **Protezione locale**: Volume/condivisione/cartella/file; stato del sistema/bare metal<br/><br/> **Protezione della macchina virtuale di Azure**: Volume/condivisione/cartella/file.
**Windows 2008 R2 (Standard ed Enterprise)**<br/><br/> 64/32 bit | In locale.<br/><br/> DPM non può essere installato come macchina virtuale VMware.<br/><br/> DPM in esecuzione in una macchina virtuale di Azure non è supportato. | **Protezione locale**: Volume/condivisione/cartella/file; stato del sistema/bare metal
**Windows Server 2008 con SP2**<br/><br/> 64/32 bit | Solo in locale.<br/><br/> DPM è supportato se in esecuzione come macchina virtuale VMware. L'esecuzione come server fisico o macchina virtuale Hyper-V non è supportata. | Volume/condivisione/cartella/file; stato del sistema/bare metal.
**Windows Storage Server 2008** | DPM in locale in esecuzione come server fisico o macchina virtuale Hyper-V. | Volume/condivisione/cartella/file; stato del sistema/bare metal.
**SQL Server 2017** | DPM SAC; DPM 2016 con l'aggiornamento cumulativo 5 o versioni successive<br/><br/> In locale/macchina virtuale di Azure.| Backup del database di SQL Server.<br/><br/> Backup di cluster di SQL Server supportato.<br/><br/>Database archiviati in volumi condivisi cluster non supportati.
**SQL Server 2016 con SP1** | Non viene fornito il supporto per DPM 2012 R2; viene fornito il supporto per DPM SAC, DPM 2016 con l'aggiornamento cumulativo 4 o versioni successive.<br/><br/> In locale/macchina virtuale di Azure.| Backup del database di SQL Server.<br/><br/> Backup di cluster di SQL Server supportato.<br/><br/>Database archiviati in volumi condivisi cluster non supportati.
**SQL Server 2016** | Non viene fornito il supporto per DPM 2012 R2. Viene fornito il supporto per DPM SAC, DPM 2016 a partire dall'aggiornamento cumulativo 2.<br/><br/> In locale/macchina virtuale di Azure.| Backup del database di SQL Server.<br/><br/> Backup di cluster di SQL Server supportato.<br/><br/>Database archiviati in volumi condivisi cluster non supportati.
**SQL Server 2014**<br/><br/> **SQL Server 2012/SP1/SP2**<br/><br/> **SQL Server 2008 R2**<br/><br/> **SQL Server 2008** | SQL Server 2014 con DPM 2012 R2 che esegue l'aggiornamento cumulativo 4 e versioni successive.<br/><br/> In locale/macchina virtuale di Azure.| Backup del database di SQL Server.<br/><br/> Backup di cluster di SQL Server supportato.<br/><br/>Database archiviati in volumi condivisi cluster non supportati.
**Exchange 2016**<br/><br/> **Exchange 2013**<br/><br/> **Exchange 2010** | Per Exchange 2016, DPM 2012 R2 richiede l'aggiornamento cumulativo 9 o versioni successive.<br/><br/> In locale | Backup del server Exchange autonomo, database in un gruppo di disponibilità dei database (DAG)<br/><br/> Ripristino della cassetta postale e del database delle cassette postali in un DAG.<br/><br/> ReFS non supportato.<br/><br/> Backup di cluster di dischi non condivisi.<br/><br/> Backup del server Exchange configurato per la replica continua.
**SharePoint 2016**<br/><br/> **SharePoint 2013**<br/><br/> **SharePoint 2010** | SharePoint 2016 in DPM 2016 e versioni successive.<br/><br/>In locale/macchina virtuale di Azure | Backup di farm, server Web front-end.<br/><br/> Ripristino di farm, database, app Web, file o elemento elenco, ricerca SharePoint, server Web front-end.<br/><br/> Non è possibile eseguire il backup di una farm con AlwaysOn per i database del contenuto.
**Hyper-V in Windows Server 2016**<br/><br/> **Windows Server 2012 R2/2012** (Datacenter/Standard)<br/><br/> **Windows Server 2008 R2 (con SP1)** | Hyper-V in 2016 supportato per DPM 2016 e versioni successive.<br/><br/> In locale | **Agente del server di Backup di Microsoft Azure nell'host Hyper-V**: Backup di intere macchine virtuali e file di dati host. Backup di macchine virtuali con archiviazione locale, macchine virtuali in cluster con archiviazione in volumi condivisi cluster e macchine virtuali con archiviazione in file server SMB.<br/><br/> **Agente del server di Backup di Microsoft Azure in una macchina virtuale guest**: Backup dei carichi di lavoro in esecuzione nella macchina virtuale. Volumi condivisi cluster.<br/><br/> **Ripristino**: macchina virtuale, ripristino a livello di elemento di disco rigido virtuale/volume/cartelle/file.<br/><br/> **Macchine virtuali Linux**: backup quando Hyper-V è in esecuzione in Windows Server 2012 R2 e versioni successive. Il ripristino per le macchine virtuali Linux è per l'intera macchina.
**Macchine virtuali VMware: vCenter/vSphere ESXi 5.5/6.0/6.5** | Server di Backup di Microsoft Azure v3, v2, v1.<br/><br/> DPM 2012 R2 richiede l'aggiornamento cumulativo 1 di System Center | In locale | Backup delle macchine virtuali VMware in volumi condivisi cluster, NFS e rete di archiviazione.<br/><br/> Ripristino dell'intera macchina virtuale.<br/><br/> Backup di Windows/Linux.<br/><br/> Ripristino a livello di elemento di cartella/file solo per le macchine virtuali Windows.<br/><br/> Le vApp VMware non sono supportate.<br/><br/> Il ripristino per le macchine virtuali Linux è per l'intera macchina.


- Si noti che i carichi di lavoro in cluster sottoposti a backup tramite DPM o server di Backup di Microsoft Azure devono trovarsi nello stesso dominio di DPM o del server di Backup di Microsoft Azure oppure in un dominio figlio o attendibile. 
- È possibile usare l'autenticazione NTLM/del certificato per eseguire il backup dei dati in gruppi di lavoro o domini non attendibili.



## <a name="next-steps"></a>Passaggi successivi

- [Vedere altre informazioni](backup-architecture.md#architecture-back-up-to-dpmmabs) sull'architettura del server di Backup di Microsoft Azure.
- [Rivedere](backup-support-matrix-mars-agent.md) gli scenari supportati per l'agente di Servizi di ripristino di Microsoft Azure.
- [Installare](backup-azure-microsoft-azure-backup.md) un server di Backup di Microsoft Azure.
- [Eseguire l'installazione](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-180
