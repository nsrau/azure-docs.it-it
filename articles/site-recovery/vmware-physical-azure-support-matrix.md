---
title: Matrice di supporto per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure con Azure Site Recovery | Microsoft Docs
description: Viene riepilogato il supporto per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure usando Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: raynew
ms.openlocfilehash: 5dc98048099264942552862498b5137b4954c200
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491638"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>Matrice di supporto per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure

Questo articolo riepiloga i componenti supportati e le impostazioni per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure usando [Azure Site Recovery](site-recovery-overview.md).

- [Altre informazioni](vmware-azure-architecture.md) sull'architettura di ripristino di emergenza di server fisico/VM VMware.
- Seguire nostri [esercitazioni](tutorial-prepare-azure.md) per provare il ripristino di emergenza.

## <a name="deployment-scenarios"></a>Scenari di distribuzione

**Scenario** | **Dettagli**
--- | ---
Ripristino di emergenza di macchine virtuali VMware | Replica di macchine virtuali VMware locali in Azure. Si può distribuire questo scenario con il portale di Azure o tramite [PowerShell](vmware-azure-disaster-recovery-powershell.md).
Ripristino di emergenza di server fisici | Replica di server fisici Windows/Linux locali in Azure. Si può distribuire questo scenario nel portale di Azure.

## <a name="on-premises-virtualization-servers"></a>Server di virtualizzazione locali

**Server** | **Requisiti** | **Dettagli**
--- | --- | ---
Server vCenter | Versione 6.7, 6.5, 6.0 o 5.5 | È consigliabile usare un server vCenter nella distribuzione di ripristino di emergenza.
host vSphere | Versione 6.7, 6.5, 6.0 o 5.5 | È consigliabile che gli host di vSphere e i server vCenter si trovino nella stessa rete del server di elaborazione. Per impostazione predefinita il server di elaborazione viene eseguito nel server di configurazione. [Altre informazioni](vmware-physical-azure-config-process-server-overview.md)


## <a name="site-recovery-configuration-server"></a>Server di configurazione di Site Recovery

Il server di configurazione è un computer locale in cui vengono eseguiti tutti i componenti locali di Site Recovery, inclusi il server di configurazione, il server di elaborazione e il server di destinazione master.

- Per le macchine virtuali VMware è impostare il server di configurazione, scaricare un modello OVF per creare una VM VMware.
- Per i server fisici, è impostato il server di configurazione manualmente.

**Componente** | **Requisiti**
--- |---
Core CPU | 8
RAM | 16 GB
Numero di dischi | 3 dischi<br/><br/> I dischi includono il disco del sistema operativo, il disco della cache del server di elaborazione e l'unità di conservazione per il failback.
Spazio disponibile su disco | 600 GB di spazio per la cache del server di elaborazione.
Spazio disponibile su disco | 600 GB di spazio per l'unità di conservazione.
Sistema operativo  | Windows Server 2012 R2 o Windows Server 2016 con esperienza Desktop |
Impostazioni locali del sistema operativo | Inglese (en-us)
[PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) | Non è necessaria per versione server di configurazione [9.14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) o versione successiva. 
Ruoli di Windows Server | Non abilitare Servizi di dominio di Active Directory; Internet Information Services (IIS) o Hyper-V. 
Criteri di gruppo| - Impedisci accesso al prompt dei comandi <br/> - Impedisci accesso agli strumenti di modifica del Registro di sistema <br/> - Logica di attendibilità per file allegati <br/> - Attiva l'esecuzione di script <br/> - [Ulteriori informazioni](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Assicurarsi di:<br/><br/> - Non avere un sito Web predefinito preesistente <br/> - Abilitare l'[autenticazione anonima](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br/> - Abilitare l'impostazione di [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)  <br/> - Non avere un sito Web o un'app preesistente in ascolto sulla porta 443<br/>
Tipo di scheda di interfaccia di rete | VMXNET3 (quando distribuito come macchina virtuale VMware)
Tipo di indirizzo IP | statico
Porte | 443 usata per l'orchestrazione del canale di controllo<br/>9443 per il trasporto di dati

## <a name="replicated-machines"></a>Computer replicati

Site Recovery supporta la replica di qualsiasi carico di lavoro in esecuzione in un computer supportato.

**Componente** | **Dettagli**
--- | ---
Impostazioni del computer | I computer che eseguono la replica in Azure devono soddisfare i [requisiti di Azure](#azure-vm-requirements).
Carico di lavoro del computer | Site Recovery supporta la replica di qualsiasi carico di lavoro in esecuzione in un computer supportato. [Altre informazioni](https://aka.ms/asr_workload)
Windows | -Windows Server 2019 (supportato da [aggiornamento cumulativo 34](https://support.microsoft.com/help/4490016) (versione 9.22 del servizio Mobility) o versione successiva.<br/> -Windows Server 2016 (64 bit Server Core, Server con esperienza Desktop)<br/> - Windows Server 2012 R2, Windows Server 2012<br/> -Windows Server 2008 R2 con almeno SP1.<br/> -Windows Server 2008, 64 e 32 bit per con almeno SP2]. Supportato per la migrazione solo. [Altre informazioni](migrate-tutorial-windows-server-2008.md)<br/> -Windows 10, Windows 8.1, Windows 8, Windows 7 a 64 bit (supportato da [aggiornamento cumulativo 36](https://support.microsoft.com/help/4503156) (versione 9.22 del servizio Mobility e versioni successive). Windows 7 RTM non è supportata. 
Linux | Sistema solo a 64 bit è supportato. sistema a 32 bit non è supportata.<br/><br/>Tutti i server Linux deve avere [i componenti di Linux Integration Services (LIS)](https://www.microsoft.com/download/details.aspx?id=55106) installato. È necessario avviare il server in Azure dopo il failover di test/failover. Se LIS componenti risultano mancanti, assicurarsi di installare il [componenti](https://www.microsoft.com/download/details.aspx?id=55106) prima di abilitare la replica per i computer per l'avvio in Azure. <br/><br/> Site Recovery orchestra il failover per l'esecuzione di server Linux in Azure. Tuttavia i fornitori di Linux potrebbero limitare il supporto solo alle versioni di distribuzione che non hanno raggiunto la fine del ciclo di vita.<br/><br/> Nelle distribuzioni di Linux sono supportati solo i kernel di scorta che fanno parte di una versione/aggiornamento secondaria della distribuzione.<br/><br/> L'aggiornamento di computer protetti tra versioni di distribuzione principali di Linux non è supportato. Per eseguire l'aggiornamento, disabilitare la replica, aggiornare il sistema operativo e quindi abilitare nuovamente la replica.<br/><br/> [Altre informazioni](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) sul supporto per Linux e le tecnologie open source in Azure.
Linux Red Hat Enterprise | 5.2 a 5.11</b><br/> 6.1 per 6.10</b><br/> 7.0 a 7.6<br/> <br/> Server che eseguono Red Hat Enterprise Linux da 5.2 a 5.11 & 6.1 6.10 non dispongono [i componenti di Linux Integration Services (LIS)](https://www.microsoft.com/download/details.aspx?id=55106) pre-installati. Assicurarsi di installare il [componenti](https://www.microsoft.com/download/details.aspx?id=55106) prima di abilitare la replica per i computer per l'avvio in Azure.
Linux: CentOS | 5.2 a 5.11</b><br/> 6.1 per 6.10</b><br/> 7.0 a 7.6<br/> <br/> Server che eseguono CentOS 5.2-5.11 & 6.1 6.10 non dispongono [i componenti di Linux Integration Services (LIS)](https://www.microsoft.com/download/details.aspx?id=55106) pre-installati. Assicurarsi di installare il [componenti](https://www.microsoft.com/download/details.aspx?id=55106) prima di abilitare la replica per i computer per l'avvio in Azure.
Ubuntu | Server Ubuntu 14.04 LTS [(versioni del kernel revisione supportata)](#ubuntu-kernel-versions)<br/><br/>Server Ubuntu 16.04 LTS [(versioni del kernel revisione supportata)](#ubuntu-kernel-versions)
Debian | Debian 7/Debian 8 [(versioni del kernel revisione supportata)](#debian-kernel-versions)
SUSE Linux | SUSE Linux Enterprise Server 12 SP1, SP2, SP3, SP4 [(versioni del kernel revisione supportata)](#suse-linux-enterprise-server-12-supported-kernel-versions)<br/> SUSE Linux Enterprise Server 11 SP3, SUSE Linux Enterprise Server 11 SP4<br/> L'aggiornamento le macchine replicate da SUSE Linux Enterprise Server 11 SP3 a SP4 non è supportata. Per eseguire l'aggiornamento, disabilitare la replica e abilitare di nuovo dopo l'aggiornamento.
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6<br/><br/> Che esegue il kernel compatibile Red Hat o Unbreakable Enterprise Kernel Release 3, 4 e 5 (UEK3, UEK4, UEK5) 


### <a name="ubuntu-kernel-versions"></a>Versioni del kernel Ubuntu


**Versione supportata** | **Versione del servizio Mobility** | **Versione del kernel** |
--- | --- | --- |
14.04 LTS | [9.24][9.25 UR]  | 3.13.0-24-Generic a 3.13.0-169-generic,<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>4.4.0-21-Generic a 4.4.0-146-generic,<br/>4.15.0-1023-Azure a 4.15.0-1042-azure |
14.04 LTS | [9.24][9.24 UR] | 3.13.0-24-Generic a 3.13.0-167-generic,<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>4.4.0-21-Generic a 4.4.0-143-generic,<br/>4.15.0-1023-Azure a 4.15.0-1040-azure |
14.04 LTS | [9.23][9.23 UR] | 3.13.0-24-Generic a 3.13.0-165-generic,<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>4.4.0-21-Generic a 4.4.0-142-generic,<br/>4.15.0-1023-Azure a 4.15.0-1037-azure |
14.04 LTS | [9.22][9.22 UR] | Da 3.13.0-24 generica a 3.13.0-164 generica<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>Da 4.4.0-21 generica a 4.4.0-140 generica<br/>Da 4.15.0-1023 Azure a 4.15.0-1036 Azure |
|||
16.04 LTS | [9.25][9.25 UR] | 4.4.0-21-Generic a 4.4.0-146-generic,<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>4.15.0-13-Generic a 4.15.0-48-generic<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure <br/>4.15.0-1012-Azure a 4.15.0-1042-azure|
16.04 LTS | [9.24][9.24 UR] | 4.4.0-21-Generic a 4.4.0-143-generic,<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>4.15.0-13-Generic a 4.15.0-46-generic<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure <br/>4.15.0-1012-Azure a 4.15.0-1040-azure|
16.04 LTS | [9.23][9.23 UR] | 4.4.0-21-Generic a 4.4.0-142-generic,<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>4.15.0-13-Generic a 4.15.0-45-generic<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure <br/>4.15.0-1012-Azure a 4.15.0-1037-azure|
16.04 LTS | [9.22][9.22 UR] | Da 4.4.0-21 generica a 4.4.0-140 generica<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>Da 4.15.0-13 generica a 4.15.0-43 generica<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure <br/>Da 4.15.0-1012 Azure a 4.15.0-1036 Azure|

### <a name="debian-kernel-versions"></a>Versioni del kernel Debian


**Versione supportata** | **Versione del servizio Mobility** | **Versione del kernel** |
--- | --- | --- |
Debian 7 | [9.21][9.21 UR], [9.22][9.22 UR],[9.23][9.23 UR], [9.24][9.24 UR]| Da 3.2.0-4-amd64 a 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.25][9.25 UR] | 3.16.0-4-amd64 to 3.16.0-8-amd64, 4.9.0-0.bpo.4-amd64 to 4.9.0-0.bpo.8-amd64 |
Debian 8 | [9.22][9.22 UR],[9.23][9.23 UR], [9,24][9.24 UR] | Da 3.16.0-4-amd64 a 3.16.0-7-amd64, da 4.9.0-0.bpo.4-amd64 a 4.9.0-0.bpo.8-amd64 |


### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 (versioni del kernel supportate)

**Versione** | **Versione del servizio Mobility** | **Versione del kernel** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | [9.25][9.25 UR] | Da 3.12.49-11 SP1 predefinita a 3.12.74-60.64.40 predefinita</br></br> Da 3.12.74-60.64.45 SP1 (LTSS) predefinita a 3.12.74-60.64.107 predefinita</br></br> Da 4.4.21-69 SP2 predefinita a 4.4.120-92.70 predefinita</br></br>4\.4.121-92.73-default SP2(LTSS) a 4.4.121-92.104-default</br></br>4\.4.73-5-default SP3 a 4.4.176-94.88-default</br></br>4\.4.138-4.7-azure SP3 a 4.4.176-4.25-azure</br></br>4\.12.14-94.41-default SP4 a 4.12.14-95.13-default</br>4\.12.14-6.3-azure SP4 a 4.12.14-6.9-azure |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | [9.24][9.24 UR] | Da 3.12.49-11 SP1 predefinita a 3.12.74-60.64.40 predefinita</br></br> Da 3.12.74-60.64.45 SP1 (LTSS) predefinita a 3.12.74-60.64.107 predefinita</br></br> Da 4.4.21-69 SP2 predefinita a 4.4.120-92.70 predefinita</br></br>4\.4.121-92.73-default SP2(LTSS) a 4.4.121-92.101-default</br></br>4\.4.73-5-default SP3 a 4.4.175-94.79-default</br></br>4\.12.14-94.41-default SP4 a 4.12.14-95.6-default |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | [9.23][9.23 UR] | Da 3.12.49-11 SP1 predefinita a 3.12.74-60.64.40 predefinita</br></br> Da 3.12.74-60.64.45 SP1 (LTSS) predefinita a 3.12.74-60.64.107 predefinita</br></br> Da 4.4.21-69 SP2 predefinita a 4.4.120-92.70 predefinita</br></br>4\.4.121-92.73-default SP2(LTSS) a 4.4.121-92.101-default</br></br>Da 4.4.73-5 SP3 predefinita a 4.4.162-94.69 predefinita</br></br>4\.12.14-94.41-default SP4 a 4.12.14-95.6-default |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3) | [9.22][9.22 UR] | Da 3.12.49-11 SP1 predefinita a 3.12.74-60.64.40 predefinita</br></br> Da 3.12.74-60.64.45 SP1 (LTSS) predefinita a 3.12.74-60.64.107 predefinita</br></br> Da 4.4.21-69 SP2 predefinita a 4.4.120-92.70 predefinita</br></br>Da 4.4.121-92.73 SP2(LTSS) predefinita a 4.4.121-92.98 predefinita</br></br>Da 4.4.73-5 SP3 predefinita a 4.4.162-94.72 predefinita |


## <a name="linux-file-systemsguest-storage"></a>File system/archiviazione guest Linux

**Componente** | **Supportato**
--- | ---
File system | ext3, ext4, XFS
Gestore volumi | -LVM è supportata.<br/> -avvio su LVM è supportata dal [aggiornamento cumulativo 31](https://support.microsoft.com/help/4478871/) (versione 9.20 del servizio Mobility) o versione successiva. Non è supportata nelle versioni precedenti del servizio Mobility.<br/> -Più dischi del sistema operativo non sono supportati.
Dispositivi di archiviazione paravirtualizzati | I dispositivi esportati da driver paravirtualizzati non sono supportati.
Dispositivi di I/O a blocchi a code multiple | Non supportati.
Server fisici con controller di archiviazione HP CCISS | Non supportati.
Convenzione di denominazione del dispositivo e del punto di montaggio | Il nome del dispositivo o del punto di montaggio deve essere univoco.<br/> Verificare che nessuna due punti di montaggio/dispositivi abbiano nomi distinzione maiuscole/minuscole. Ad esempio i dispositivi di denominazione per la stessa VM *device1* e *Device1* non è supportato.
Directory | Se si esegue una versione del servizio Mobility precedente alla versione 9.20 (rilasciato nel [aggiornamento cumulativo 31](https://support.microsoft.com/help/4478871/)), quindi applicano queste restrizioni:<br/><br/> -Queste directory (se configurato come separare le partizioni o file System) devono essere nello stesso disco del sistema operativo nel server di origine: / (root), /boot, /usr, /usr/local, /var, /etc.</br> : La directory /boot deve trovarsi in una partizione del disco e non un volume LVM.<br/><br/> Da versione 9.20 e versioni successive, non si applicano queste restrizioni. 
Directory di avvio | -Più dischi di avvio sono supportati in una macchina virtuale <br/> -avvio in un volume LVM su più di un disco non è supportato.<br/> -Un computer senza un disco di avvio non è possibile replicare.
Requisiti di spazio disponibile nel disco| 2 GB nella partizione /root <br/><br/> 250 MB nella cartella di installazione
XFSv5 | Funzionalità di XFSv5 nei file System XFS, ad esempio il checksum dei metadati, sono supportati (versione del servizio Mobility 9.10 o versione successiva).<br/> Usare l'utilità xfs_info per controllare il superblocco XFS per la partizione. Se `ftype` è impostato su 1, quindi le funzionalità XFSv5 sono in uso.
BTRFS | BTRFS è supportata dal [aggiornamento cumulativo 34](https://support.microsoft.com/help/4490016) (versione 9.22 del servizio Mobility) o versione successiva. BTRFS non è supportato se:<br/><br/> -Il subvolume di sistema BTRFS file viene modificato dopo l'abilitazione della protezione.</br> -Il file system BTRFS è distribuito tra più dischi.</br> -Il file system BTRFS supporta RAID.

## <a name="vmdisk-management"></a>Gestione della macchina virtuale e del disco

**Azione** | **Dettagli**
--- | ---
Ridimensionamento del disco nella macchina virtuale replicata | Supportato.
Aggiunta del disco alla macchina virtuale replicata | Non supportati.<br/> Disabilitare la replica per la macchina virtuale, aggiungere il disco e quindi riabilitare la replica.

## <a name="network"></a>Rete

**Componente** | **Supportato**
--- | ---
Gruppo NIC della rete host | Supportato per le macchine virtuali VMware. <br/><br/>Non supportato per la replica di computer fisici.
VLAN rete host | Sì.
IPv4 rete host | Sì.
IPv6 rete host | No.
Gruppo NIC della rete guest/server | No.
IPv4 rete guest/server | Sì.
IPv6 rete guest/server | No.
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
Endpoint servizio di rete virtuale di Azure<br/> | Yes
Rete accelerata | No

## <a name="storage"></a>Archiviazione
**Componente** | **Supportato**
--- | ---
Disco dinamico | Disco del sistema operativo deve essere un disco di base. <br/><br/>I dischi dati possono essere dinamici
Configurazione dei dischi Docker | No
NFS host | Sì per VMware<br/><br/> No per server fisici
Host SAN (iSCSI/FC) | Yes
Host vSAN | Sì per VMware<br/><br/> N/D per server fisici
Percorsi multipli (MPIO) | Sì, testato con DSM Microsoft, EMC PowerPath 5.7 SP4, DSM EMC PowerPath per CLARiiON
Volumi virtuali host | Sì per VMware<br/><br/> N/D per server fisici
VMDK guest/server | Yes
Disco cluster condiviso guest/server | No
Disco crittografato guest/server | No
NFS guest/server | No
Guest/server iSCSI | No
SMB 3.0 guest/server | No
RDM guest/server | Yes<br/><br/> N/D per server fisici
Disco guest/server > 1 TB | Yes<br/><br/>Fino a 4.095 GB<br/><br/> Le dimensioni del disco non devono superare i 1024 MB.
Disco guest/server con dimensioni logiche di settore a 4 KB e dimensioni fisiche di settore a 4 KB | No
Disco guest/server con logico di 4 KB e dimensioni fisiche di settore di 512 byte | No
Volume con disco con striping > 4 TB guest/server <br/><br/>Gestione volumi logici (LVM)| Yes
Guest/server - Spazi di archiviazione | No
Disco di aggiunta/rimozione a caldo guest/server | No
Guest/server - esclusione disco | Yes
Percorsi multipli (MPIO) guest/server | No
Partizione GPT guest/server | Cinque partizioni sono supportate dal [aggiornamento cumulativo 37](https://support.microsoft.com/help/4508614/) (versione 9.25 del servizio Mobility) o versione successiva. In precedenza erano supportate quattro.
Avvio EFI/UEFI guest/server | -Supportato durante l'esecuzione di versione del servizio Mobility 9.13 o successiva.<br/> -Supportato durante la migrazione di macchine virtuali VMware o server fisici che eseguono Windows Server 2012 o versioni successive in Azure.<br/> -È possibile replicare le macchine virtuali solo per la migrazione. Non è supportato il failback in locale.<br/> -È supportato solo NTFS & tipo di avvio protetto UEFI non è supportato. <br/> -Dimensioni del settore disco devono essere di 512 byte per settore fisico.

## <a name="replication-channels"></a>Canali di replica

|**Tipo di replica**   |**Supportato**  |
|---------|---------|
|Trasferimenti ODX (ODX)    |       No  |
|Seeding offline        |   No      |
| Azure Data Box | No

## <a name="azure-storage"></a>Archiviazione di Azure

**Componente** | **Supportato**
--- | ---
Archiviazione con ridondanza locale | Yes
Archiviazione con ridondanza geografica | Yes
Archiviazione con ridondanza geografica e accesso in lettura | Yes
Archiviazione ad accesso sporadico | No
Archiviazione ad accesso frequente| No
BLOB in blocchi | No
Crittografia dei dati inattivi (SSE)| Yes
Archiviazione Premium | Yes
Servizio di importazione/esportazione | No
Firewall di archiviazione di Azure per le reti virtuali | Sì.<br/> Configurato in un account di archiviazione/cache di archiviazione di destinazione (usato per archiviare i dati della replica).
Account di archiviazione per utilizzo generico v2 (livelli ad accesso frequente e sporadico) | Sì (transazione i costi sono decisamente più elevati per la versione V2 rispetto alla versione 1)

## <a name="azure-compute"></a>Calcolo di Azure

**Funzionalità** | **Supportato**
--- | ---
Set di disponibilità | Yes
Zone di disponibilità | No
HUB | Yes
Dischi gestiti | Yes

## <a name="azure-vm-requirements"></a>Requisiti per le VM di Azure

In locale le macchine virtuali replicate in Azure devono soddisfare i requisiti di macchina virtuale di Azure riepilogati in questa tabella. Quando viene eseguito un controllo dei prerequisiti per la replica di Site Recovery, la verifica avrà esito negativo se alcuni dei requisiti non vengono soddisfatti.

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

## <a name="churn-limits"></a>Varianza dei limiti

La tabella seguente indica i limiti di Azure Site Recovery. 
- Questi limiti si basano su test di Microsoft, ma non coprono tutte le combinazioni dei / o app possibili.
- I risultati effettivi possono variare in base alla combinazione di I/O delle applicazioni.
- Per ottenere risultati ottimali, è consigliabile eseguire la [dello strumento Deployment Planner](site-recovery-deployment-planner.md)ed eseguire test approfonditi con failover per ottenere il quadro reale delle prestazioni per l'app di test.

**Destinazione replica** | **Dimensioni medie I/O disco di origine** |**Varianza dati media disco di origine** | **Varianza dati totale giornaliera disco di origine**
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

- Si tratta di numeri medi presupponendo una sovrapposizione I/O del 30%.
- Site Recovery può gestire una velocità effettiva maggiore in base alla percentuale di sovrapposizione, alle dimensioni di scrittura maggiori e all'effettivo I/O del carico di lavoro.
- Questi numeri presuppongono un backlog tipico di circa cinque minuti. ovvero i dati, dopo essere stati caricati, verranno elaborati e verrà creato un punto di ripristino entro cinque minuti.

## <a name="vault-tasks"></a>Attività di insieme di credenziali

**Azione** | **Supportato**
--- | ---
Spostamento insieme di credenziali tra gruppi di risorse | No
Spostamento insieme di credenziali all'interno e tra sottoscrizioni | No
Spostamento di risorse di archiviazione, rete e VM di Azure tra gruppi di risorse | No
Spostare risorse di archiviazione, rete, macchine virtuali di Azure all'interno e tra sottoscrizioni. | No


## <a name="obtain-latest-components"></a>Ottenere componenti più recenti

**Nome** | **Descrizione** | **Dettagli**
--- | --- | ---
Server di configurazione | Installato in locale.<br/> Coordina le comunicazioni tra server VMware locali o computer fisici e Azure. | - [Informazioni su](vmware-physical-azure-config-process-server-overview.md) il server di configurazione.<br/> - [Informazioni su](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) l'aggiornamento alla versione più recente.<br/> - [Informazioni su](vmware-azure-deploy-configuration-server.md) la configurazione del server di configurazione. 
Server di elaborazione | Installato per impostazione predefinita nel server di configurazione.<br/> Riceve i dati di replica, li ottimizza attraverso la memorizzazione nella cache, la compressione e la crittografia e li invia ad Azure.<br/> Man mano che aumenta la distribuzione, è possibile aggiungere altri server di elaborazione per gestire volumi più elevati di traffico di replica. | - [Informazioni su](vmware-physical-azure-config-process-server-overview.md) il server di elaborazione.<br/> - [Informazioni su](vmware-azure-manage-process-server.md#upgrade-a-process-server) l'aggiornamento alla versione più recente.<br/> - [Informazioni su](vmware-physical-large-deployment.md#set-up-a-process-server) configurando i server di elaborazione scale-out.
Servizio Mobility | Installato su VM VMware o server fisici da replicare.<br/> Coordina la replica tra locale server VMware/server fisici e Azure.| - [Informazioni su](vmware-physical-mobility-service-overview.md) il servizio Mobility.<br/> - [Informazioni su](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) l'aggiornamento alla versione più recente.<br/> 



## <a name="next-steps"></a>Passaggi successivi
[Informazioni su come](tutorial-prepare-azure.md) preparare Azure per il ripristino di emergenza delle macchine virtuali VMware.

[9.25 UR]: https://support.microsoft.com/en-in/help/4508614/update-rollup-37-for-azure-site-recovery
[9.24 UR]: https://support.microsoft.com/en-in/help/4503156
[9.23 UR]: https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery
[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery
