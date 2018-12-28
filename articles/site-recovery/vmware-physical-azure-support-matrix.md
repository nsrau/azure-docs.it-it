---
title: Matrice di supporto per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure con Azure Site Recovery | Microsoft Docs
description: Questo articolo presenta un riepilogo dei sistemi operativi e dei componenti supportati per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure tramite Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/12/2018
ms.author: raynew
ms.openlocfilehash: 7593183093a722f55b2bd638ef551fb1343433c4
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2018
ms.locfileid: "53323474"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>Matrice di supporto per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure

Questo articolo riepiloga i componenti supportati e le impostazioni per il ripristino di emergenza di macchine virtuali VMware in Azure tramite [Azure Site Recovery](site-recovery-overview.md).

Vedere le [esercitazioni](tutorial-prepare-azure.md) per iniziare a usare Azure Site Recovery con lo scenario di distribuzione più semplice. Altre informazioni sull'[architettura di Azure Site Recovery](vmware-azure-architecture.md).

## <a name="replication-scenario"></a>Scenario di replica

**Scenario** | **Dettagli**
--- | ---
VM VMware | Replica di macchine virtuali VMware locali in Azure. Si può distribuire questo scenario con il portale di Azure o tramite [PowerShell](vmware-azure-disaster-recovery-powershell.md).
Server fisici | Replica di server fisici Windows/Linux locali in Azure. Si può distribuire questo scenario nel portale di Azure.

## <a name="on-premises-virtualization-servers"></a>Server di virtualizzazione locali

**Server** | **Requisiti** | **Dettagli**
--- | --- | ---
VMware | Server vCenter 6.7, 6.5, 6.0 o 5.5 oppure vSphere 6.7, 6.5, 6.0 o 5.5 | È consigliabile usare un server vCenter.<br/><br/> È consigliabile che gli host di vSphere e i server vCenter si trovino nella stessa rete del server di elaborazione. Per impostazione predefinita i componenti del server di elaborazione vengono eseguiti nel server di configurazione, quindi questa sarà la rete in cui configurare il server di configurazione, a meno che non si configuri un server di elaborazione dedicato.
Fisico | N/D

## <a name="site-recovery-configuration-server"></a>Server di configurazione di Site Recovery

Il server di configurazione è un computer locale in cui vengono eseguiti tutti i componenti locali di Site Recovery, inclusi il server di configurazione, il server di elaborazione e il server di destinazione master. Per la replica VMware viene configurato il server di configurazione con tutti i requisiti, usando un modello OVF per creare una macchina virtuale VMware. Per la replica del server fisico, è necessario configurare manualmente il server di configurazione.

**Componente** | **Requisiti**
--- |---
Core CPU | 8
RAM | 16 GB
Numero di dischi | 3 dischi<br/><br/> I dischi includono il disco del sistema operativo, il disco della cache del server di elaborazione e l'unità di conservazione per il failback.
Spazio disponibile su disco | 600 GB di spazio necessario per la cache del server di elaborazione.
Spazio disponibile su disco | 600 GB di spazio necessario per l'unità di conservazione.
Sistema operativo  | Windows Server 2012 R2 o Windows Server 2016 |
Impostazioni locali del sistema operativo | Inglese (en-us)
PowerCLI | Deve essere installato [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0").
Ruoli di Windows Server | Non abilitare: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V |
Criteri di gruppo| Non abilitare: <br> - Impedisci accesso al prompt dei comandi <br> - Impedisci accesso agli strumenti di modifica del Registro di sistema <br> - Logica di attendibilità per file allegati <br> - Attiva l'esecuzione di script <br> [Altre informazioni](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Assicurarsi di:<br/><br/> - Non avere un sito Web predefinito preesistente <br> - Abilitare l'[autenticazione anonima](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - Abilitare l'impostazione di [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)  <br> - Non avere un sito Web o un'app preesistente in ascolto sulla porta 443<br>
Tipo di scheda di interfaccia di rete | VMXNET3 (quando distribuito come macchina virtuale VMware)
Tipo di indirizzo IP | statico
Porte | 443 usata per l'orchestrazione del canale di controllo<br>9443 usata per il trasporto dei dati

## <a name="replicated-machines"></a>Computer replicati

Site Recovery supporta la replica di qualsiasi carico di lavoro in esecuzione in un computer supportato.

**Componente** | **Dettagli**
--- | ---
Impostazioni del computer | I computer che eseguono la replica in Azure devono soddisfare i [requisiti di Azure](#azure-vm-requirements).
Sistema operativo Windows | Windows Server 2016 a 64 bit (Server Core, server con Esperienza desktop), Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 con almeno SP1. </br></br>  [Windows Server 2008 con almeno SP2 - a 32 e a 64 bit](migrate-tutorial-windows-server-2008.md) (solo per la migrazione). </br></br> Windows 2016 Nano Server non è supportato.
Sistema operativo Linux | Red Hat Enterprise Linux: da 5.2 a 5.11<b>\*\*</b>, da 6.1 a 6.10<b>\*\*</b>, da 7.0 a 7.5 <br/><br/>CentOS: da 5.2 a 5.11<b>\*\*</b>, da 6.1 a 6.10<b>\*\*</b>, da 7.0 a 7.5 <br/><br/>Server Ubuntu 14.04 LTS[ (versioni del kernel supportate)](#ubuntu-kernel-versions)<br/><br/>Server Ubuntu 16.04 LTS[ (versioni del kernel supportate)](#ubuntu-kernel-versions)<br/><br/>Debian 7/Debian 8[ (versioni del kernel supportate)](#debian-kernel-versions)<br/><br/>SUSE Linux Enterprise Server 12 SP1, SP2, SP3 [ (versioni del kernel supportate)](#suse-linux-enterprise-server-12-supported-kernel-versions)<br/><br/>SUSE Linux Enterprise Server 11 SP3<b>\*\*</b>, SUSE Linux Enterprise Server 11 SP4 * </br></br>Oracle Linux 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5 che esegue il kernel compatibile Red Hat o Unbreakable Enterprise Kernel versione 3 (UEK3) <br/><br/></br>- L'aggiornamento dei computer replicati da SUSE Linux Enterprise Server 11 da SP3 a SP4 non è supportato. Per eseguire l'aggiornamento, disabilitare la replica e abilitarla nuovamente dopo l'aggiornamento.</br></br> - [Altre informazioni](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) su Linux e sulla tecnologia open source in Azure. Site Recovery orchestra il failover per l'esecuzione di server Linux in Azure. Tuttavia i fornitori di Linux potrebbero limitare il supporto solo alle versioni di distribuzione che non hanno raggiunto la fine del ciclo di vita.<br/><br/> - Nelle distribuzioni di Linux sono supportati solo i kernel di scorta che fanno parte di un aggiornamento/versione secondaria della distribuzione.<br/><br/> - L'aggiornamento di computer protetti tra versioni di distribuzione principali di Linux non è supportato. Per eseguire l'aggiornamento, disabilitare la replica, aggiornare il sistema operativo e quindi abilitare nuovamente la replica.<br/><br/> - Sui server in cui è in esecuzione Red Hat Enterprise Linux 5.2-5.11 o CentOS 5.2-5.11 devono essere installati i [componenti di Linux Integration Services (LIS)](https://www.microsoft.com/download/details.aspx?id=55106) per consentire l'avvio dei computer in Azure.



### <a name="ubuntu-kernel-versions"></a>Versioni del kernel Ubuntu


**Versione supportata** | **Versione del servizio Mobility di Azure Site Recovery** | **Versione del kernel** |
--- | --- | --- |
14.04 LTS | [9.19](https://support.microsoft.com/en-us/help/4468181/azure-site-recovery-update-rollup-30) | Da 3.13.0-24 generica a 3.13.0-153 generica<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>Da 4.4.0-21 generica a 4.4.0-131 generica |
14.04 LTS | [9.18](https://support.microsoft.com/en-us/help/4055712/update-rollup-27-for-azure-site-recovery) | Da 3.13.0-24 generica a 3.13.0-153 generica<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>Da 4.4.0-21 generica a 4.4.0-130 generica |
14.04 LTS | [9.17](https://support.microsoft.com/en-us/help/4344054/update-rollup-26-for-azure-site-recovery) | Da 3.13.0-24 generica a 3.13.0-149 generica<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>Da 4.4.0-21 generica a 4.4.0-127 generica |
14.04 LTS | [9.16](https://support.microsoft.com/en-in/help/4278275/update-rollup-25-for-azure-site-recovery) | Da 3.13.0-24 generica a 3.13.0-144 generica<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>Da 4.4.0-21 generica a 4.4.0-119 generica |
|||
16.04 LTS | [9.19](https://support.microsoft.com/en-us/help/4468181/azure-site-recovery-update-rollup-30) | Da 4.4.0-21 generica a 4.4.0-131 generica<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>Da 4.15.0-13 generica a 4.15.0-30 generica<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure <br/>Da 4.15.0-1012 Azure a 4.15.0-1019 Azure|
16.04 LTS | [9.18](https://support.microsoft.com/en-us/help/4055712/update-rollup-27-for-azure-site-recovery) | Da 4.4.0-21 generica a 4.4.0-130 generica<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica |
16.04 LTS | [9.17](https://support.microsoft.com/en-us/help/4344054/update-rollup-26-for-azure-site-recovery) | Da 4.4.0-21 generica a 4.4.0-127 generica<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-43 generica |
16.04 LTS | [9.16](https://support.microsoft.com/en-in/help/4278275/update-rollup-25-for-azure-site-recovery) | Da 4.4.0-21 generica a 4.4.0-119 generica<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-38 generica |

### <a name="debian-kernel-versions"></a>Versioni del kernel Debian


**Versione supportata** | **Versione del servizio Mobility di Azure Site Recovery** | **Versione del kernel** |
--- | --- | --- |
Debian 7 | [9.17](https://support.microsoft.com/en-us/help/4344054/update-rollup-26-for-azure-site-recovery),[9.18](https://support.microsoft.com/en-us/help/4055712/update-rollup-27-for-azure-site-recovery),[9.19](https://support.microsoft.com/en-us/help/4468181/azure-site-recovery-update-rollup-30) | Da 3.2.0-4-amd64 a 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
Debian 7 | [9.16](https://support.microsoft.com/en-in/help/4278275/update-rollup-25-for-azure-site-recovery) | Da 3.2.0-4-amd64 a 3.2.0-5-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.19](https://support.microsoft.com/en-us/help/4468181/azure-site-recovery-update-rollup-30) | Da 3.16.0-4-amd64 a 3.16.0-6-amd64, da 4.9.0-0.bpo.4-amd64 a 4.9.0-0.bpo.7-amd64 |
Debian 8 | [9.17](https://support.microsoft.com/en-us/help/4344054/update-rollup-26-for-azure-site-recovery), [9.18](https://support.microsoft.com/en-us/help/4055712/update-rollup-27-for-azure-site-recovery) | Da 3.16.0-4-amd64 a 3.16.0-6-amd64, da 4.9.0-0.bpo.4-amd64 a 4.9.0-0.bpo.6-amd64 |
Debian 8 | [9.16](https://support.microsoft.com/en-in/help/4278275/update-rollup-25-for-azure-site-recovery) | Da 3.16.0-4-amd64 a 3.16.0-5-amd64, da 4.9.0-0.bpo.4-amd64 a 4.9.0-0.bpo.6-amd64 |


### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 (versioni del kernel supportate)

**Versione** | **Versione del servizio Mobility** | **Versione del kernel** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3) | 9.20 | Da 3.12.49-11 SP1 predefinita a 3.12.74-60.64.40 predefinita</br></br> Da 3.12.74-60.64.45 SP1 (LTSS) predefinita a 3.12.74-60.64.107 predefinita</br></br> Da 4.4.21-69 SP2 predefinita a 4.4.120-92.70 predefinita</br></br>Da 4.4.121-92.73 SP2(LTSS) predefinita a 4.4.121-92.98 predefinita</br></br>Da 4.4.73-5 SP3 predefinita a 4.4.156-94.64 predefinita |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3) | 9.19 | Da 3.12.49-11 SP1 predefinita a 3.12.74-60.64.40 predefinita</br></br> Da 3.12.74-60.64.45 SP1 (LTSS) predefinita a 3.12.74-60.64.96 predefinita</br></br> Da 4.4.21-69 SP2 predefinita a 4.4.120-92.70 predefinita</br></br>Da 4.4.121-92.73 SP2(LTSS) predefinita a 4.4.121-92.85 predefinita</br></br>Da 4.4.73-5 SP3 predefinita a 4.4.140-94.42 predefinita |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3) | 9.18 | Da 3.12.49-11 SP1 predefinita a 3.12.74-60.64.40 predefinita</br></br> Da 3.12.74-60.64.45 SP1 (LTSS) predefinita a 3.12.74-60.64.96 predefinita</br></br> Da 4.4.21-69 SP2 predefinita a 4.4.120-92.70 predefinita</br></br>Da 4.4.121-92.73 SP2(LTSS) predefinita a 4.4.121-92.85 predefinita</br></br>Da 4.4.73-5 SP3 predefinita a 4.4.138-94.39 predefinita |

## <a name="linux-file-systemsguest-storage"></a>File system/archiviazione guest Linux

**Componente** | **Supportato**
--- | ---
File system | ext3, ext4, XFS
Gestore volumi | Prima della [versione 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), <br/> 1. LVM2 è supportata. <br/> 2. LVM è supportata solo per i dischi dati. <br/> 3. Le macchine virtuali di Azure hanno un solo disco di sistema operativo.<br/><br/>Dalla [versione 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery) in avanti, sono supportate LVM e LVM2.
Dispositivi di archiviazione paravirtualizzati | I dispositivi esportati da driver paravirtualizzati non sono supportati.
Dispositivi di I/O a blocchi a code multiple | Non supportati.
Server fisici con controller di archiviazione HP CCISS | Non supportati.
Directory | Prima della [versione 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), <br/> 1. Le directory seguenti (se impostate come partizioni o file system separati) devono trovarsi nello stesso disco del sistema operativo nel server di origine: /(root), /boot, /usr, /usr/local, /var, /etc.</br>2. /boot deve trovarsi in una partizione del disco e non essere un volume LVM.<br/><br/> Dalla [versione 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery) in avanti, le restrizioni sopra citate non sono applicabili.
Requisiti di spazio disponibile nel disco| 2 GB nella partizione /root <br/><br/> 250 MB nella cartella di installazione
XFSv5 | Le funzionalità di XFSv5 nei file system XFS, ad esempio il checksum dei metadati, sono supportate a partire dalla versione 9.10 del servizio Mobility. Usare l'utilità xfs_info per controllare il superblocco XFS per la partizione. Se ftype è impostato su 1, le funzionalità XFSv5 sono in uso.

## <a name="vmdisk-management"></a>Gestione della macchina virtuale e del disco

**Azione** | **Dettagli**
--- | ---
Ridimensionamento del disco nella macchina virtuale replicata | Supportato.
Aggiunta del disco alla macchina virtuale replicata | Disabilitare la replica per la macchina virtuale e aggiungere il disco, quindi riabilitare la replica. L'aggiunta di un disco a una macchina virtuale di replica non è attualmente supportata.

## <a name="network"></a>Rete

**Componente** | **Supportato**
--- | ---
Gruppo NIC della rete host | Supportato per le macchine virtuali VMware. <br/><br/>Non supportato per la replica di computer fisici.
VLAN rete host | Sì.
IPv4 rete host | Sì.
IPv6 rete host |  No.
Gruppo NIC della rete guest/server |  No.
IPv4 rete guest/server | Sì.
IPv6 rete guest/server |  No.
IP statico (Windows) rete guest/server | Sì.
IP statico (Linux) rete guest/server | Sì. <br/><br/>Le VM sono configurate per l'uso di DHCP in caso di failback.
Più schede di interfaccia rete guest/server | Sì.


## <a name="azure-vm-network-after-failover"></a>Rete VM di Azure (dopo il failover)

**Componente** | **Supportato**
--- | ---
Azure ExpressRoute | Yes
ILB | Yes
ELB | Yes
Gestione traffico di Azure | Yes
Più NIC | Yes
Indirizzo IP riservato | Yes
IPv4 | Yes
Conservazione indirizzo IP di origine | Yes
Endpoint del servizio Rete virtuale di Azure<br/> (senza firewall di Archiviazione di Azure) | Yes
Rete accelerata | No 

## <a name="storage"></a>Archiviazione
**Componente** | **Supportato**
--- | ---
NFS host | Sì per VMware<br/><br/> No per server fisici
Host SAN (iSCSI/FC) | Yes
Host vSAN | Sì per VMware<br/><br/> N/D per server fisici
Percorsi multipli (MPIO) | Sì, testato con DSM Microsoft, EMC PowerPath 5.7 SP4, DSM EMC PowerPath per CLARiiON
Volumi virtuali host | Sì per VMware<br/><br/> N/D per server fisici
VMDK guest/server | Yes
EFI/UEFI guest/server| Parziale (migrazione ad Azure per Windows Server 2012 e versioni successive) </br></br> Vedere la nota alla fine della tabella
Disco cluster condiviso guest/server | No 
Disco crittografato guest/server | No 
NFS guest/server | No 
SMB 3.0 guest/server | No 
RDM guest/server | Yes<br/><br/> N/D per server fisici
Disco guest/server > 1 TB | Yes<br/><br/>Fino a 4.095 GB
Disco guest/server con dimensioni logiche di settore a 4 KB e dimensioni fisiche di settore a 4 KB | Yes
Disco guest/server con dimensioni logiche di settore a 4 KB e dimensioni fisiche di settore a 512 KB | Yes
Volume con disco con striping > 4 TB guest/server <br><br/>Gestione volumi logici (LVM)| Yes
Guest/server - Spazi di archiviazione | No 
Disco di aggiunta/rimozione a caldo guest/server | No 
Guest/server - esclusione disco | Yes
Percorsi multipli (MPIO) guest/server | No 

> [!NOTE]
> È possibile eseguire la migrazione in Azure di macchine virtuali VMware con avvio UEFI che eseguono Windows Server 2012 o versioni successive. Si applicano le restrizioni seguenti:

> - È supportata solo la migrazione in Azure. Il failback nel sito VMware locale non è supportato.
> - Il server non deve avere più di quattro partizioni nel disco del sistema operativo.
> - Richiede il servizio Mobility versione 9.13 o successiva.

## <a name="azure-storage"></a>Archiviazione di Azure

**Componente** | **Supportato**
--- | ---
Archiviazione con ridondanza locale | Yes
Archiviazione con ridondanza geografica | Yes
Archiviazione con ridondanza geografica e accesso in lettura | Yes
Archiviazione ad accesso sporadico | No 
Archiviazione ad accesso frequente| No 
BLOB in blocchi | No 
Crittografia per dati inattivi (servizio di archiviazione di Azure)| Yes
Archiviazione Premium | Yes
Servizio di importazione/esportazione | No 
Firewall di Archiviazione di Azure per reti virtuali configurate in un account di archiviazione di destinazione/archiviazione della cache (usato per l'archiviazione dei dati di replica) | No 
Account di archiviazione v2 generico (livelli di accesso frequente e sporadico) | No 

## <a name="azure-compute"></a>Calcolo di Azure

**Funzionalità** | **Supportato**
--- | ---
Set di disponibilità | Yes
HUB | Yes
Dischi gestiti | Yes

## <a name="azure-vm-requirements"></a>Requisiti per le VM di Azure

Le macchine virtuali locali replicate in Azure devono soddisfare i requisiti per le VM di Azure riepilogati in questa tabella. Un controllo dei prerequisiti eseguito da Site Recovery avrà esito negativo se alcuni dei requisiti non vengono soddisfatti.

**Componente** | **Requisiti** | **Dettagli**
--- | --- | ---
Sistema operativo guest | Verificare i [sistemi operativi supportati](#replicated-machines) per le macchine replicate. | Il controllo ha esito negativo se non supportato.
Architettura del sistema operativo guest | 64 bit. | Il controllo ha esito negativo se non supportato.
Dimensioni disco del sistema operativo | Fino a 2.048 GB. | Il controllo ha esito negativo se non supportato.
Conteggio dischi del sistema operativo | 1 | Il controllo ha esito negativo se non supportato.  
Conteggio dischi dati | 64 o un numero inferiore. | Il controllo ha esito negativo se non supportato.  
Dimensioni del disco dati | Fino a 4.095 GB | Il controllo ha esito negativo se non supportato.
Schede di rete | Sono supportate più schede. |
VHD condiviso | Non supportati. | Il controllo ha esito negativo se non supportato.
Disco FC | Non supportati. | Il controllo ha esito negativo se non supportato.
BitLocker | Non supportati. | Prima di abilitare la replica per un computer, occorre disabilitare BitLocker. |
Nome della VM. | Da 1 a 63 caratteri.<br/><br/> Limitato a lettere, numeri e trattini.<br/><br/> Il nome del computer deve iniziare e terminare con una lettera o un numero. |  Aggiornare il valore nelle proprietà del computer in Site Recovery.


## <a name="vault-tasks"></a>Attività di insieme di credenziali

**Azione** | **Supportato**
--- | ---
Spostamento insieme di credenziali tra gruppi di risorse<br/><br/> All'interno e tra sottoscrizioni | No 
Spostamento di risorse di archiviazione, rete e VM di Azure tra gruppi di risorse<br/><br/> All'interno e tra sottoscrizioni | No 


## <a name="download-latest-azure-site-recovery-components"></a>Scaricare i componenti più recenti di Azure Site Recovery

**Nome** | **Descrizione** | **Istruzioni per il download della versione più recente**
--- | --- | --- | --- | ---
Server di configurazione | Coordina le comunicazioni tra server VMware locali e Azure  <br/><br/>  Installato su server VMware locali | Fare clic [qui](vmware-azure-deploy-configuration-server.md) per una nuova installazione. Fare clic [qui](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) per aggiornare i componenti esistenti alla versione più recente.
Server di elaborazione|Installato per impostazione predefinita nel server di configurazione. Riceve i dati di replica, li ottimizza attraverso la memorizzazione nella cache, la compressione e la crittografia e li invia ad Archiviazione di Azure. Con l'aumentare delle dimensioni della distribuzione, è possibile aggiungere altri server di elaborazione separati per gestire volumi più elevati di traffico di replica.| Fare clic [qui](vmware-azure-set-up-process-server-scale.md) per una nuova installazione. Fare clic [qui](vmware-azure-manage-process-server.md#upgrade-a-process-server) per aggiornare i componenti esistenti alla versione più recente.
Servizio Mobility | Coordina la replica fra server VMware locali/server fisici e sito Azure/secondario<br/><br/> Installato in server fisici o in macchine virtuali VMware da replicare | Fare clic [qui](vmware-azure-install-mobility-service.md) per una nuova installazione. Fare clic [qui](vmware-physical-mobility-service-overview.md#update-the-mobility-service) per aggiornare i componenti esistenti alla versione più recente.

Fare clic [qui](https://aka.ms/ASR_latest_release_notes) per informazioni sulle funzionalità e correzioni più recenti.


## <a name="next-steps"></a>Passaggi successivi
[Informazioni su come](tutorial-prepare-azure.md) preparare Azure per il ripristino di emergenza delle macchine virtuali VMware.
