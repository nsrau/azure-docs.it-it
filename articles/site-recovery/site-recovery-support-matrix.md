<properties
	pageTitle="Matrice di supporto di Azure Site Recovery | Microsoft Azure"
	description="Riepiloga i sistemi operativi e componenti supportati per Azure Site Recovery"
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="09/07/2016"
	ms.author="raynew"/>  

# Matrice di supporto di Azure Site Recovery

Questo articolo riepiloga i sistemi operativi e i componenti supportati per le distribuzioni di Azure Site Recovery. Un elenco dei prerequisiti e dei componenti supportati è disponibile per ogni scenario di distribuzione in ogni articolo sulla distribuzione, ma in questo argomento viene riportato un riepilogo.

## Sistemi operativi supportati per i server di virtualizzazione


**Replica** | **Ciò che viene replicato** | **Replicare in** | **Sistema operativo host**
---|---|---|--- 
**Host Hyper-V** | Qualsiasi carico di lavoro | Azure | Windows Server 2012 R2 con gli aggiornamenti più recenti
**Host Hyper-V in cloud VMM** | Qualsiasi carico di lavoro | Azure | Windows Server 2012 R2 con gli aggiornamenti più recenti
**Host Hyper-V in cloud VMM** | Qualsiasi carico di lavoro | Sito VMM secondario | Almeno Windows Server 2012 con gli aggiornamenti più recenti
**Host VMware/vCenter** | Qualsiasi carico di lavoro | Azure | vCenter 5.5 o 6.0 (supporto per le sole funzionalità 5.5) <br/><br/> vSphere 6.0, 5.5, o 5.1 con gli ultimi aggiornamenti
**Host VMware/vCenter** | Qualsiasi carico di lavoro | Sito secondario di VMware | vCenter 5.5 o 6.0 (supporto per le sole funzionalità 5.5) <br/><br/> vSphere 6.0, 5.5, o 5.1 con gli ultimi aggiornamenti

## Requisiti supportati per le macchine replicate

**Replica** | **Ciò che viene replicato** | **Replicare in** | **Sistema operativo host**
---|---|---|--- 
**VM Hyper-V** | Qualsiasi carico di lavoro | Azure | Ogni VM con sistema operativo guest [supportato da Azure](https://technet.microsoft.com/library/cc794868.aspx)<br/><br/> deve soddisfare i [requisiti di Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**VM Hyper-V nel cloud VMM** | Qualsiasi carico di lavoro | Azure | Ogni VM con sistema operativo guest [supportato da Azure](https://technet.microsoft.com/library/cc794868.aspx)<br/><br/> deve soddisfare i [requisiti di Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**VM Hyper-V nei cloud VMM** | Qualsiasi carico di lavoro | Sito VMM secondario | Qualsiasi sistema operativo guest [supportato da Hyper-V](https://technet.microsoft.com/library/mt126277.aspx)
**VM VMware** | Qualsiasi carico di lavoro in esecuzione nella VM Windows | Azure | Le VM Windows Server 2012 R2 a 64 bit, Windows Server 2012, Windows Server 2008 R2 con SP1 o successivo<br/><br/> devono soddisfare i [requisiti di Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**VM VMware** | Qualsiasi carico di lavoro in esecuzione nella VM Linux | Azure | Red Hat Enterprise Linux 6.7, 7.1, 7.2 <br/><br/> Centos 6.5, 6.6, 6.7, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 con il kernet compatibile Red Hat o Unbreakable Enterprise Kernel Release 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> Archiviazione richiesta: file system (EXT3, ETX4, ReiserFS, XFS); Multipath software-Device Mapper (multipath)); Archiviazione volumi: (LVM2). I server fisici con archiviazione del controller HP CCISS non sono supportati. Il file system ReiserFS è supportato solo su SUSE Linux Enterprise Server 11 SP3.<br/><br/> Le VM devono essere conformi ai [requisiti di Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**VM VMware** | Qualsiasi carico di lavoro in esecuzione nella VM Windows | Sito secondario di VMware | Windows Server 2012 R2 a 64 bit, Windows Server 2012, Windows Server 2008 R2 con SP1 o successivo
**VM VMware** | Qualsiasi carico di lavoro in esecuzione nella VM Linux | Sito secondario di VMware | Red Hat Enterprise Linux 6.7, 7.1, 7.2 <br/><br/> Centos 6.5, 6.6, 6.7, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 con il kernet compatibile Red Hat o Unbreakable Enterprise Kernel Release 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> Archiviazione richiesta: file system (EXT3, ETX4, ReiserFS, XFS); Multipath software-Device Mapper (multipath)); Archiviazione volumi: (LVM2). I server fisici con archiviazione del controller HP CCISS non sono supportati. Il file system ReiserFS è supportato solo su SUSE Linux Enterprise Server 11 SP3.
**Server fisici** | Qualsiasi carico di lavoro in esecuzione in Windows | Azure | Windows Server 2012 R2 a 64 bit, Windows Server 2012, Windows Server 2008 con SP1 o successivo
**Server fisici** | Qualsiasi carico di lavoro in esecuzione in Linux | Azure | Red Hat Enterprise Linux 6.7,7.1,7.2 <br/><br/> Centos 6.5, 6.6, 6.7, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 con il kernet compatibile Red Hat o Unbreakable Enterprise Kernel Release 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> Archiviazione richiesta: file system (EXT3, ETX4, ReiserFS, XFS); Multipath software-Device Mapper (multipath)); Archiviazione volumi: (LVM2). I server fisici con archiviazione del controller HP CCISS non sono supportati. Il file system ReiserFS è supportato solo su SUSE Linux Enterprise Server 11 SP3.
**Server fisici** | Qualsiasi carico di lavoro in esecuzione in Windows | Sito secondario | Windows Server 2012 R2 a 64 bit, Windows Server 2012, Windows Server 2008 con SP1 o successivo
**Server fisici** | Qualsiasi carico di lavoro in esecuzione in Linux | Sito secondario | Red Hat Enterprise Linux 6.7,7.1,7.2 <br/><br/> Centos 6.5, 6.6, 6.7, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 con il kernet compatibile Red Hat o Unbreakable Enterprise Kernel Release 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> Archiviazione richiesta: file system (EXT3, ETX4, ReiserFS, XFS); Multipath software-Device Mapper (multipath)); Archiviazione volumi: (LVM2). I server fisici con archiviazione del controller HP CCISS non sono supportati. Il file system ReiserFS è supportato solo su SUSE Linux Enterprise Server 11 SP3.


## Versioni del provider

**Nome** | **Descrizione** | **Versione più recente** | **Supporto** | **Dettagli**
---|---|---|---| ---
**Provider di Azure Site Recovery** | Coordina le comunicazioni tra i server locali e il sito di Azure/secondario <br/><br/> Installato sui server VMM locali o server Hyper-V se non esiste alcun server VMM | 5\.1.1700 (disponibile dal portale) | [Funzionalità e correzioni più recenti](https://support.microsoft.com/kb/3155002)
**Installazione unificata di Azure Site Recovery (da VMware ad Azure)** | Coordina le comunicazioni tra server VMware locali e Azure <br/><br/> Installato su server VMware locali | 9\.3.4246.1 (disponibile dal portale) | [Funzionalità e correzioni più recenti](https://support.microsoft.com/kb/3155002)
**Servizio Mobility** | Coordina la replica fra server VMware locali/server fisici e sito Azure/secondario | ND (disponibile dal portale) | Installato in ogni VM VMware o server fisico che si desidera replicare. **Agente di Servizi di ripristino di Microsoft Azure (MARS)** | Coordina la replica tra le macchine virtuali Hyper-V e Azure<br/><br/> Installato nei server Hyper-V locali (con o senza un server VMM) | 2\.0.8689.0 (disponibile dal portale) | Questo agente viene usato da Azure Site Recovery e Backup di Azure). [Altre informazioni](https://support.microsoft.com/it-IT/kb/2997692)

## Passaggi successivi

[Preparare la distribuzione](site-recovery-best-practices.md)

<!---HONumber=AcomDC_0907_2016-->