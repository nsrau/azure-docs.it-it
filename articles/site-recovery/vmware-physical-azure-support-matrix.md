---
title: Matrice di supporto per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure con Azure Site Recovery | Microsoft Docs
description: Questo articolo presenta un riepilogo dei sistemi operativi e dei componenti supportati per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure tramite Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: raynew
ms.openlocfilehash: 94fd70dccf367d43b1caaa9f3a11ed934f9950ea
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2019
ms.locfileid: "59618057"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>Matrice di supporto per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure

Questo articolo riepiloga i componenti supportati e le impostazioni per il ripristino di emergenza di macchine virtuali VMware in Azure tramite [Azure Site Recovery](site-recovery-overview.md).

Vedere le [esercitazioni](tutorial-prepare-azure.md) per iniziare a usare Azure Site Recovery con lo scenario di distribuzione più semplice. Altre informazioni sull'[architettura di Azure Site Recovery](vmware-azure-architecture.md).

## <a name="deployment-scenario"></a>Scenario di distribuzione

**Scenario** | **Dettagli**
--- | ---
Ripristino di emergenza di macchine virtuali VMware | Replica di macchine virtuali VMware locali in Azure. Si può distribuire questo scenario con il portale di Azure o tramite [PowerShell](vmware-azure-disaster-recovery-powershell.md).
Ripristino di emergenza di server fisici | Replica di server fisici Windows/Linux locali in Azure. Si può distribuire questo scenario nel portale di Azure.

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
PowerCLI | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0") non è necessaria per il server di configurazione con le versioni da [9.14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery).
Ruoli di Windows Server | Non abilitare: <br/> - Active Directory Domain Services <br/>- Internet Information Services <br/> - Hyper-V |
Criteri di gruppo| Non abilitare: <br/> - Impedisci accesso al prompt dei comandi <br/> - Impedisci accesso agli strumenti di modifica del Registro di sistema <br/> - Logica di attendibilità per file allegati <br/> - Attiva l'esecuzione di script <br/> [Altre informazioni](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Assicurarsi di:<br/><br/> - Non avere un sito Web predefinito preesistente <br/> - Abilitare l'[autenticazione anonima](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br/> - Abilitare l'impostazione di [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)  <br/> - Non avere un sito Web o un'app preesistente in ascolto sulla porta 443<br/>
Tipo di scheda di interfaccia di rete | VMXNET3 (quando distribuito come macchina virtuale VMware)
Tipo di indirizzo IP | statico
Porte | 443 usata per l'orchestrazione del canale di controllo<br/>9443 usata per il trasporto dei dati

## <a name="replicated-machines"></a>Computer replicati

Site Recovery supporta la replica di qualsiasi carico di lavoro in esecuzione in un computer supportato.

**Componente** | **Dettagli**
--- | ---
Impostazioni del computer | I computer che eseguono la replica in Azure devono soddisfare i [requisiti di Azure](#azure-vm-requirements).
Carico di lavoro del computer | Site Recovery supporta la replica di qualsiasi carico di lavoro (ad esempio Active Directory Domain Services, SQL server, ecc.) in esecuzione in un computer supportato. [Altre informazioni](https://aka.ms/asr_workload)
Sistema operativo Windows | Windows Server 2016 a 64 bit (Server Core, server con Esperienza desktop), Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 con almeno SP1. </br></br>  [Windows Server 2008 con almeno SP2 - a 32 e a 64 bit](migrate-tutorial-windows-server-2008.md) (solo per la migrazione). </br></br> Windows 2016 Nano Server non è supportato.
Architettura del sistema operativo Linux | Sistema solo a 64 bit è supportato. sistema a 32 bit non è supportato
Sistema operativo Linux | Red Hat Enterprise Linux: da 5.2 a 5.11<b>\*\*</b>, da 6.1 a 6.10<b>\*\*</b>, da 7.0 a 7.6 <br/><br/>CentOS: da 5.2 a 5.11<b>\*\*</b>, da 6.1 a 6.10<b>\*\*</b>, da 7.0 a 7.6 <br/><br/>Server Ubuntu 14.04 LTS [(versioni del kernel supportate)](#ubuntu-kernel-versions)<br/><br/>Server Ubuntu 16.04 LTS [(versioni del kernel supportate)](#ubuntu-kernel-versions)<br/><br/>Debian 7/Debian 8 [(versioni del kernel supportate)](#debian-kernel-versions)<br/><br/>SUSE Linux Enterprise Server 12 SP1, SP2, SP3 [(versioni del kernel supportate)](#suse-linux-enterprise-server-12-supported-kernel-versions)<br/><br/>SUSE Linux Enterprise Server 11 SP3<b>\*\*</b>, SUSE Linux Enterprise Server 11 SP4 * </br></br>Oracle Linux 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5 che esegue il kernel compatibile Red Hat o Unbreakable Enterprise Kernel versione 3 (UEK3) <br/><br/></br>- L'aggiornamento dei computer replicati da SUSE Linux Enterprise Server 11 da SP3 a SP4 non è supportato. Per eseguire l'aggiornamento, disabilitare la replica e abilitarla nuovamente dopo l'aggiornamento.</br></br> - [Altre informazioni](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) su Linux e sulla tecnologia open source in Azure. Site Recovery orchestra il failover per l'esecuzione di server Linux in Azure. Tuttavia i fornitori di Linux potrebbero limitare il supporto solo alle versioni di distribuzione che non hanno raggiunto la fine del ciclo di vita.<br/><br/> - Nelle distribuzioni di Linux sono supportati solo i kernel di scorta che fanno parte di un aggiornamento/versione secondaria della distribuzione.<br/><br/> - L'aggiornamento di computer protetti tra versioni di distribuzione principali di Linux non è supportato. Per eseguire l'aggiornamento, disabilitare la replica, aggiornare il sistema operativo e quindi abilitare nuovamente la replica.<br/><br/> - Sui server in cui è in esecuzione Red Hat Enterprise Linux 5.2-5.11 o CentOS 5.2-5.11 devono essere installati i [componenti di Linux Integration Services (LIS)](https://www.microsoft.com/download/details.aspx?id=55106) per consentire l'avvio dei computer in Azure.


### <a name="ubuntu-kernel-versions"></a>Versioni del kernel Ubuntu


**Versione supportata** | **Versione del servizio Mobility di Azure Site Recovery** | **Versione del kernel** |
--- | --- | --- |
14.04 LTS | [9.23][9.23 UR] | 3.13.0-24-Generic a 3.13.0-165-generic,<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>4.4.0-21-Generic a 4.4.0-142-generic,<br/>4.15.0-1023-Azure a 4.15.0-1037-azure |
14.04 LTS | [9.22][9.22 UR] | Da 3.13.0-24 generica a 3.13.0-164 generica<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>Da 4.4.0-21 generica a 4.4.0-140 generica<br/>Da 4.15.0-1023 Azure a 4.15.0-1036 Azure |
14.04 LTS | [9.21][9.21 UR] | Da 3.13.0-24 generica a 3.13.0-163 generica<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>Da 4.4.0-21 generica a 4.4.0-140 generica<br/>Da 4.15.0-1023 Azure a 4.15.0-1035 Azure |
14.04 LTS | [9.20][9.20 UR] | Da 3.13.0-24 generica a 3.13.0-153 generica<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>Da 4.4.0-21 generica a 4.4.0-138 generica<br/>Da 4.15.0-1023 Azure a 4.15.0-1025 Azure |
|||
16.04 LTS | [9.23][9.23 UR] | 4.4.0-21-Generic a 4.4.0-142-generic,<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>4.15.0-13-Generic a 4.15.0-45-generic<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure <br/>4.15.0-1012-Azure a 4.15.0-1037-azure|
16.04 LTS | [9.22][9.22 UR] | Da 4.4.0-21 generica a 4.4.0-140 generica<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>Da 4.15.0-13 generica a 4.15.0-43 generica<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure <br/>Da 4.15.0-1012 Azure a 4.15.0-1036 Azure|
16.04 LTS | [9.21][9.21 UR] | Da 4.4.0-21 generica a 4.4.0-140 generica<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>Da 4.15.0-13 generica a 4.15.0-42 generica<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure <br/>Da 4.15.0-1012 Azure a 4.15.0-1035 Azure|
16.04 LTS | [9.20][9.20 UR] | Da 4.4.0-21 generica a 4.4.0-138 generica<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>Da 4.15.0-13 generica a 4.15.0-38 generica<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure <br/>Da 4.15.0-1012 Azure a 4.15.0-1025 Azure|

### <a name="debian-kernel-versions"></a>Versioni del kernel Debian


**Versione supportata** | **Versione del servizio Mobility di Azure Site Recovery** | **Versione del kernel** |
--- | --- | --- |
Debian 7 | [9.20][9.20 UR],[9.21][9.21 UR], [9.22][9.22 UR],[9.23][9.23 UR]| Da 3.2.0-4-amd64 a 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.20][9.20 UR],[9.21][9.21 UR],[9.22][9.22 UR],[9.23][9.23 UR] | Da 3.16.0-4-amd64 a 3.16.0-7-amd64, da 4.9.0-0.bpo.4-amd64 a 4.9.0-0.bpo.8-amd64 |


### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 (versioni del kernel supportate)

**Versione** | **Versione del servizio Mobility** | **Versione del kernel** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | [9.23][9.23 UR] | Da 3.12.49-11 SP1 predefinita a 3.12.74-60.64.40 predefinita</br></br> Da 3.12.74-60.64.45 SP1 (LTSS) predefinita a 3.12.74-60.64.107 predefinita</br></br> Da 4.4.21-69 SP2 predefinita a 4.4.120-92.70 predefinita</br></br>4.4.121-92.73-default SP2(LTSS) a 4.4.121-92.101-default</br></br>4.4.73-5-default SP3 a 4.4.162-94.79-default</br></br>4.12.14-94.41-default SP4 a 4.12.14-95.6-default |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3) | [9.22][9.22 UR] | Da 3.12.49-11 SP1 predefinita a 3.12.74-60.64.40 predefinita</br></br> Da 3.12.74-60.64.45 SP1 (LTSS) predefinita a 3.12.74-60.64.107 predefinita</br></br> Da 4.4.21-69 SP2 predefinita a 4.4.120-92.70 predefinita</br></br>Da 4.4.121-92.73 SP2(LTSS) predefinita a 4.4.121-92.98 predefinita</br></br>Da 4.4.73-5 SP3 predefinita a 4.4.162-94.72 predefinita |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3) | [9.21][9.21 UR] | Da 3.12.49-11 SP1 predefinita a 3.12.74-60.64.40 predefinita</br></br> Da 3.12.74-60.64.45 SP1 (LTSS) predefinita a 3.12.74-60.64.107 predefinita</br></br> Da 4.4.21-69 SP2 predefinita a 4.4.120-92.70 predefinita</br></br>Da 4.4.121-92.73 SP2(LTSS) predefinita a 4.4.121-92.98 predefinita</br></br>Da 4.4.73-5 SP3 predefinita a 4.4.156-94.72 predefinita |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3) | [9.20][9.20 UR] | Da 3.12.49-11 SP1 predefinita a 3.12.74-60.64.40 predefinita</br></br> Da 3.12.74-60.64.45 SP1 (LTSS) predefinita a 3.12.74-60.64.107 predefinita</br></br> Da 4.4.21-69 SP2 predefinita a 4.4.120-92.70 predefinita</br></br>Da 4.4.121-92.73 SP2(LTSS) predefinita a 4.4.121-92.98 predefinita</br></br>Da 4.4.73-5 SP3 predefinita a 4.4.156-94.64 predefinita |

## <a name="linux-file-systemsguest-storage"></a>File system/archiviazione guest Linux

**Componente** | **Supportato**
--- | ---
File system | ext3, ext4, XFS
Gestore volumi | Prima della [versione 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), <br/> 1. LVM è supportata. <br/> 2. /boot nel volume LVM non è supportato. <br/> 3. Non sono supportati più dischi del sistema operativo.<br/><br/>Dal [9.20 versione](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery) e versioni successive, /boot su LVM è supportata. Non sono supportati più dischi del sistema operativo.
Dispositivi di archiviazione paravirtualizzati | I dispositivi esportati da driver paravirtualizzati non sono supportati.
Dispositivi di I/O a blocchi a code multiple | Non supportati.
Server fisici con controller di archiviazione HP CCISS | Non supportati.
Convenzione di denominazione del dispositivo e del punto di montaggio | Il nome del dispositivo o del punto di montaggio deve essere univoco. Verificare che non esistano due punti di montaggio/dispositivi con nomi distinti per maiuscole/minuscole. </br> Esempio: due dispositivi della stessa macchina virtuale denominati *dispositivo1* e *Dispositivo1* non sono consentiti.
Directory | Prima della [versione 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), <br/> 1. Le directory seguenti (se impostate come partizioni o file system separati) devono trovarsi nello stesso disco del sistema operativo nel server di origine: /(root), /boot, /usr, /usr/local, /var, /etc.</br>2. /boot deve trovarsi in una partizione del disco e non essere un volume LVM.<br/><br/> Dalla [versione 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery) in avanti, le restrizioni sopra citate non sono applicabili. La directory /boot non è supportata nel volume LVM tra più dischi.
Directory di avvio | Non sono supportati più dischi di avvio in una macchina virtuale <br/><br/> Non è possibile proteggere un computer senza disco di avvio

Requisiti di spazio disponibile nel disco | 2 GB nella partizione /root <br/><br/> 250 MB nella cartella di installazione XFSv5 | Le funzionalità di XFSv5 nei file system XFS, ad esempio il checksum dei metadati, sono supportate a partire dalla versione 9.10 del servizio Mobility. Usare l'utilità xfs_info per controllare il superblocco XFS per la partizione. Se ftype è impostato su 1, le funzionalità XFSv5 sono in uso.

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
Azure ExpressRoute | Sì
ILB | Sì
ELB | Sì
Gestione traffico di Azure | Sì
Più NIC | Sì
Indirizzo IP riservato | Sì
IPv4 | Sì
Conservazione indirizzo IP di origine | Sì
Endpoint del servizio Rete virtuale di Azure<br/> | Sì
Rete accelerata | No 

## <a name="storage"></a>Archiviazione
**Componente** | **Supportato**
--- | ---
Disco dinamico | Il disco del sistema operativo deve essere un disco di base <br/><br/>I dischi dati possono essere dinamici
Configurazione dei dischi Docker | No 
NFS host | Sì per VMware<br/><br/> No per server fisici
Host SAN (iSCSI/FC) | Sì
Host vSAN | Sì per VMware<br/><br/> N/D per server fisici
Percorsi multipli (MPIO) | Sì, testato con DSM Microsoft, EMC PowerPath 5.7 SP4, DSM EMC PowerPath per CLARiiON
Volumi virtuali host | Sì per VMware<br/><br/> N/D per server fisici
VMDK guest/server | Sì
Disco cluster condiviso guest/server | No 
Disco crittografato guest/server | No 
NFS guest/server | No 
SMB 3.0 guest/server | No 
RDM guest/server | Sì<br/><br/> N/D per server fisici
Disco guest/server > 1 TB | Sì<br/><br/>Fino a 4.095 GB<br/><br/> Le dimensioni del disco non devono superare i 1024 MB.
Disco guest/server con dimensioni logiche di settore a 4 KB e dimensioni fisiche di settore a 4 KB | Sì
Disco guest/server con dimensioni logiche di settore a 4 KB e dimensioni fisiche di settore a 512 KB | Sì
Volume con disco con striping > 4 TB guest/server <br/><br/>Gestione volumi logici (LVM)| Sì
Guest/server - Spazi di archiviazione | No 
Disco di aggiunta/rimozione a caldo guest/server | No 
Guest/server - esclusione disco | Sì
Percorsi multipli (MPIO) guest/server | No 
Avvio EFI/UEFI guest/server | Durante la migrazione di macchine virtuali VMware o server fisici che eseguono Windows Server 2012 o versioni successive per Azure è supportato.<br/><br/> È possibile replicare le macchine virtuali solo per la migrazione. Non è supportato il failback in locale.<br/><br/> Il server non deve avere più di quattro partizioni nel disco del sistema operativo.<br/><br/> Richiede il servizio Mobility versione 9.13 o successiva.<br/><br/> È supportato solo NTFS.


## <a name="azure-storage"></a>Archiviazione di Azure

**Componente** | **Supportato**
--- | ---
Archiviazione con ridondanza locale | Sì
Archiviazione con ridondanza geografica | Sì
Archiviazione con ridondanza geografica e accesso in lettura | Sì
Archiviazione ad accesso sporadico | No 
Archiviazione ad accesso frequente| No 
BLOB in blocchi | No 
Crittografia per dati inattivi (servizio di archiviazione di Azure)| Sì
Archiviazione Premium | Sì
Servizio di importazione/esportazione | No 
Firewall di Archiviazione di Azure per reti virtuali configurate in un account di archiviazione di destinazione/archiviazione della cache (usato per l'archiviazione dei dati di replica) | Sì
Account di archiviazione v2 generico (livelli di accesso frequente e sporadico) | No 

## <a name="azure-compute"></a>Calcolo di Azure

**Funzionalità** | **Supportato**
--- | ---
Set di disponibilità | Sì
Zone di disponibilità | No 
HUB | Sì
Dischi gestiti | Sì

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

## <a name="azure-site-recovery-churn-limits"></a>Limiti relativi ad Azure Site Recovery varianza

La tabella seguente indica i limiti di Azure Site Recovery. Questi limiti si basano su test di Microsoft, ma non possono coprire tutte le possibili combinazioni di I/O delle applicazioni. I risultati effettivi possono variare in base alla combinazione di I/O delle applicazioni. Per ottenere risultati ottimali, è fortemente consigliabile [Esegui strumento deployment planner](site-recovery-deployment-planner.md) ed eseguire test approfonditi delle applicazioni con un failover di test per ottenere il quadro reale delle prestazioni dell'applicazione.

**Destinazione archiviazione di replica** | **Dimensioni medie I/O disco di origine** |**Varianza dati media disco di origine** | **Varianza dati totale giornaliera disco di origine**
---|---|---|---
Archiviazione standard | 8 KB | 2 MB/s | 168 GB per disco
Disco P10 o P15 Premium | 8 KB  | 2 MB/s | 168 GB per disco
Disco P10 o P15 Premium | 16 KB | 4 MB/s |  336 GB per disco
Disco P10 o P15 Premium | 32 KB o superiori | 8 MB/s | 672 GB per disco
Disco P20, P30, P40 o P50 Premium | 8 KB    | 5 MB/s | 421 GB per disco
Disco P20, P30, P40 o P50 Premium | 16 KB o superiori |20 MB/s | 1684 GB per disco

**Varianza dati di origine** | **Limite Massimo**
---|---
Varianza media dei dati per VM| 25 MB/s
Picco di varianza dei dati su tutti i dischi in una VM | 54 MB/s
Varianza massima dei dati al giorno supportata da un server di elaborazione | 2 TB

Si tratta di numeri medi presupponendo una sovrapposizione I/O del 30%. Site Recovery può gestire una velocità effettiva maggiore in base alla percentuale di sovrapposizione, alle dimensioni di scrittura maggiori e all'effettivo I/O del carico di lavoro. I numeri precedenti presuppongono un backlog tipico di circa cinque minuti, ovvero i dati, dopo essere stati caricati, verranno elaborati e verrà creato un punto di ripristino entro cinque minuti.

## <a name="vault-tasks"></a>Attività di insieme di credenziali

**Azione** | **Supportato**
--- | ---
Spostamento insieme di credenziali tra gruppi di risorse<br/><br/> All'interno e tra sottoscrizioni | No 
Spostamento di risorse di archiviazione, rete e VM di Azure tra gruppi di risorse<br/><br/> All'interno e tra sottoscrizioni | No 


## <a name="download-latest-azure-site-recovery-components"></a>Scaricare i componenti più recenti di Azure Site Recovery

**Nome** | **Descrizione** | **Istruzioni per il download della versione più recente**
--- | --- | ---
Server di configurazione | Coordina le comunicazioni tra server VMware locali e Azure  <br/><br/>  Installato su server VMware locali | Per altre informazioni, visita le istruzioni riportate in [installazione aggiornata](vmware-azure-deploy-configuration-server.md) e [aggiornamento del componente esistente alla versione più recente](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server).
Server di elaborazione|Installato per impostazione predefinita nel server di configurazione. Riceve i dati di replica, li ottimizza attraverso la memorizzazione nella cache, la compressione e la crittografia e li invia ad Archiviazione di Azure. Con l'aumentare delle dimensioni della distribuzione, è possibile aggiungere altri server di elaborazione separati per gestire volumi più elevati di traffico di replica.| Per altre informazioni, visita le istruzioni riportate in [installazione aggiornata](vmware-azure-set-up-process-server-scale.md) e [aggiornamento del componente esistente alla versione più recente](vmware-azure-manage-process-server.md#upgrade-a-process-server).
Servizio Mobility | Coordina la replica fra server VMware locali/server fisici e sito Azure/secondario<br/><br/> Installato in server fisici o in macchine virtuali VMware da replicare | Per altre informazioni, visita le istruzioni riportate in [installazione aggiornata](vmware-azure-install-mobility-service.md) e [aggiornamento del componente esistente alla versione più recente](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal).

Per altre informazioni sulle funzionalità più recenti, visitare [note sulla versione più recente](https://aka.ms/ASR_latest_release_notes).


## <a name="next-steps"></a>Passaggi successivi
[Informazioni su come](tutorial-prepare-azure.md) preparare Azure per il ripristino di emergenza delle macchine virtuali VMware.

[9.23 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery
