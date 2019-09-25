---
title: Matrice di supporto per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure con Azure Site Recovery | Microsoft Docs
description: Riepiloga il supporto per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure tramite Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: raynew
ms.openlocfilehash: cb46acf3f54b5955ba8542adf73b7ca896f20c28
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266402"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>Matrice di supporto per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure

Questo articolo riepiloga i componenti supportati e le impostazioni per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure con [Azure Site Recovery](site-recovery-overview.md).

- [Altre](vmware-azure-architecture.md) informazioni sull'architettura di ripristino di emergenza di macchine virtuali VMware/server fisici.
- Seguire le [esercitazioni](tutorial-prepare-azure.md) per provare il ripristino di emergenza.

## <a name="deployment-scenarios"></a>Scenari di distribuzione

**Scenario** | **Dettagli**
--- | ---
Ripristino di emergenza di macchine virtuali VMware | Replica di macchine virtuali VMware locali in Azure. Si può distribuire questo scenario con il portale di Azure o tramite [PowerShell](vmware-azure-disaster-recovery-powershell.md).
Ripristino di emergenza di server fisici | Replica di server fisici Windows/Linux locali in Azure. Si può distribuire questo scenario nel portale di Azure.

## <a name="on-premises-virtualization-servers"></a>Server di virtualizzazione locali

**Server** | **Requisiti** | **Dettagli**
--- | --- | ---
Server vCenter | Versione 6,7, 6,5, 6,0 o 5,5 | Si consiglia di usare un server vCenter nella distribuzione di ripristino di emergenza.
host vSphere | Versione 6,7, 6,5, 6,0 o 5,5 | È consigliabile che gli host di vSphere e i server vCenter si trovino nella stessa rete del server di elaborazione. Per impostazione predefinita, il server di elaborazione viene eseguito nel server di configurazione. [Altre informazioni](vmware-physical-azure-config-process-server-overview.md)


## <a name="site-recovery-configuration-server"></a>Server di configurazione di Site Recovery

Il server di configurazione è un computer locale in cui vengono eseguiti tutti i componenti locali di Site Recovery, inclusi il server di configurazione, il server di elaborazione e il server di destinazione master.

- Per le macchine virtuali VMware è possibile impostare il server di configurazione scaricando un modello OVF per creare una VM VMware.
- Per i server fisici, configurare manualmente il computer del server di configurazione.

**Componente** | **Requisiti**
--- |---
Core CPU | 8
RAM | 16 GB
Numero di dischi | 3 dischi<br/><br/> I dischi includono il disco del sistema operativo, il disco della cache del server di elaborazione e l'unità di conservazione per il failback.
Spazio disponibile su disco | 600 GB di spazio per la cache del server di elaborazione.
Spazio disponibile su disco | 600 GB di spazio per l'unità di conservazione.
Sistema operativo  | Windows Server 2012 R2 o Windows Server 2016 con esperienza desktop |
Impostazioni locali del sistema operativo | Inglese (en-us)
[PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) | Non necessario per il server di configurazione [9,14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) o versione successiva. 
Ruoli di Windows Server | Non abilitare Active Directory Domain Services; Internet Information Services (IIS) o Hyper-V. 
Criteri di gruppo| - Impedisci accesso al prompt dei comandi <br/> - Impedisci accesso agli strumenti di modifica del Registro di sistema <br/> - Logica di attendibilità per file allegati <br/> - Attiva l'esecuzione di script <br/> - [Ulteriori informazioni](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Assicurarsi di:<br/><br/> - Non avere un sito Web predefinito preesistente <br/> - Abilitare l'[autenticazione anonima](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br/> - Abilitare l'impostazione di [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)  <br/> - Non avere un sito Web o un'app preesistente in ascolto sulla porta 443<br/>
Tipo di scheda di interfaccia di rete | VMXNET3 (quando distribuito come macchina virtuale VMware)
Tipo di indirizzo IP | Static
Porte | 443 usato per l'orchestrazione del canale di controllo<br/>9443 per il trasporto dati

## <a name="replicated-machines"></a>Computer replicati

Site Recovery supporta la replica di qualsiasi carico di lavoro in esecuzione in un computer supportato.

**Componente** | **Dettagli**
--- | ---
Impostazioni del computer | I computer che eseguono la replica in Azure devono soddisfare i [requisiti di Azure](#azure-vm-requirements).
Carico di lavoro del computer | Site Recovery supporta la replica di qualsiasi carico di lavoro in esecuzione in un computer supportato. [Altre informazioni](https://aka.ms/asr_workload)
Windows | -Windows Server 2019 (supportato dall' [aggiornamento cumulativo 34](https://support.microsoft.com/help/4490016) (versione 9,22 del servizio Mobility) in poi.<br/> -Windows Server 2016 (64-bit Server Core, server con esperienza desktop)<br/> -Windows Server 2012 R2, Windows Server 2012<br/> -Windows Server 2008 R2 con almeno SP1.<br/> -Windows Server 2008, 64 e 32 bit con almeno SP2]. Supportato solo per la migrazione. [Altre informazioni](migrate-tutorial-windows-server-2008.md)<br/> -Windows 10, Windows 8.1, Windows 8, Windows 7 64 bit (supportato dall' [aggiornamento cumulativo 36](https://support.microsoft.com/help/4503156) (versione 9,22 del servizio Mobility in poi). Windows 7 RTM non è supportato. 
Linux | È supportato solo il sistema a 64 bit. il sistema a 32 bit non è supportato.<br/><br/>Ogni server Linux deve avere [componenti di linux Integration Services (LIS)](https://www.microsoft.com/download/details.aspx?id=55106) installati. È necessario avviare il server in Azure dopo il failover o il failover di test. Se i componenti LIS risultano mancanti, assicurarsi di installare i [componenti](https://www.microsoft.com/download/details.aspx?id=55106) prima di abilitare la replica per l'avvio dei computer in Azure. <br/><br/> Site Recovery orchestra il failover per l'esecuzione di server Linux in Azure. Tuttavia i fornitori di Linux potrebbero limitare il supporto solo alle versioni di distribuzione che non hanno raggiunto la fine del ciclo di vita.<br/><br/> Nelle distribuzioni di Linux sono supportati solo i kernel di scorta che fanno parte di una versione/aggiornamento secondaria della distribuzione.<br/><br/> L'aggiornamento di computer protetti tra versioni di distribuzione principali di Linux non è supportato. Per eseguire l'aggiornamento, disabilitare la replica, aggiornare il sistema operativo e quindi abilitare nuovamente la replica.<br/><br/> [Scopri di più](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) sul supporto per Linux e tecnologia open source in Azure.
Linux Red Hat Enterprise | da 5,2 a 5,11</b><br/> da 6,1 a 6,10</b><br/> da 7,0 a 7,6<br/> <br/> I server che eseguono Red Hat Enterprise Linux 5.2-5.11 & 6.1-6.10 non dispongono di [componenti di Linux Integration Services (LIS)](https://www.microsoft.com/download/details.aspx?id=55106) pre-installati. Assicurarsi di installare i [componenti](https://www.microsoft.com/download/details.aspx?id=55106) prima di abilitare la replica per l'avvio dei computer in Azure.
Linux: CentOS | da 5,2 a 5,11</b><br/> da 6,1 a 6,10</b><br/> da 7,0 a 7,6<br/> <br/> I server in cui è in esecuzione CentOS 5.2-5.11 & 6.1-6.10 non dispongono di [componenti di Linux Integration Services (LIS)](https://www.microsoft.com/download/details.aspx?id=55106) pre-installati. Assicurarsi di installare i [componenti](https://www.microsoft.com/download/details.aspx?id=55106) prima di abilitare la replica per l'avvio dei computer in Azure.
Ubuntu | Server Ubuntu 14,04 LTS [(rivedere le versioni del kernel supportate)](#ubuntu-kernel-versions)<br/><br/>Server Ubuntu 16,04 LTS [(rivedere le versioni del kernel supportate)](#ubuntu-kernel-versions)
Debian | Debian 7/Debian 8 [(rivedere le versioni del kernel supportate)](#debian-kernel-versions)
SUSE Linux | SUSE Linux Enterprise Server 12 SP1, SP2, SP3, SP4 [(rivedere le versioni del kernel supportate)](#suse-linux-enterprise-server-12-supported-kernel-versions)<br/> SUSE Linux Enterprise Server 11 SP3, SUSE Linux Enterprise Server 11 SP4<br/> L'aggiornamento dei computer replicati da SUSE Linux Enterprise Server 11 SP3 a SP4 non è supportato. Per eseguire l'aggiornamento, disabilitare la replica e riabilitarla dopo l'aggiornamento.
Oracle Linux | 6,4, 6,5, 6,6, 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6<br/><br/> Esecuzione del kernel compatibile Red Hat o Unbreakable Enterprise kernel Release 3, 4 & 5 (UEK3, UEK4, UEK5) 


### <a name="ubuntu-kernel-versions"></a>Versioni del kernel Ubuntu


**Versione supportata** | **Versione del servizio Mobility** | **Versione del kernel** |
--- | --- | --- |
14.04 LTS | [9,27][9.27 UR]| 3.13.0-24-generico per 3.13.0-170-generico,<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>4.4.0-21-generico per 4.4.0-148-generico,<br/>4.15.0-1023-Azure a 4.15.0-1045-Azure |
14.04 LTS | [9.26][9.26 UR]| 3.13.0-24-generico per 3.13.0-170-generico,<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>4.4.0-21-generico per 4.4.0-148-generico,<br/>4.15.0-1023-Azure a 4.15.0-1045-Azure |
14.04 LTS | [9.25][9.25 UR]  | 3.13.0-24-generico per 3.13.0-169-generico,<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>4.4.0-21-generico per 4.4.0-146-generico,<br/>4.15.0-1023-da Azure a 4.15.0-1042-Azure |
14.04 LTS | [9.24][9.24 UR] | 3.13.0-24-generico per 3.13.0-167-generico,<br/>Da 3.16.0-25 generica a 3.16.0-77 generica<br/>Da 3.19.0-18 generica a 3.19.0-80 generica<br/>Da 4.2.0-18 generica a 4.2.0-42 generica<br/>4.4.0-21-generico per 4.4.0-143-generico,<br/>4.15.0-1023-Azure a 4.15.0-1040-Azure |
|||
16.04 LTS | [9,27][9.27 UR] | 4.4.0-21-generico a 4.4.0-154-Generic,<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>da 4.15.0-13-generico a 4.15.0-54-generico<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure <br/>4.15.0-1012-Azure a 4.15.0-1050-Azure|
16.04 LTS | [9.26][9.26 UR] | 4.4.0-21-generico per 4.4.0-148-generico,<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>da 4.15.0-13-generico a 4.15.0-50-generico<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure <br/>4.15.0-1012-Azure a 4.15.0-1045-Azure|
16.04 LTS | [9.25][9.25 UR] | 4.4.0-21-generico per 4.4.0-146-generico,<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>da 4.15.0-13-generico a 4.15.0-48-generico<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure <br/>4.15.0-1012-Azure a 4.15.0-1042-Azure|
16.04 LTS | [9.24][9.24 UR] | 4.4.0-21-generico per 4.4.0-143-generico,<br/>Da 4.8.0-34 generica a 4.8.0-58 generica<br/>Da 4.10.0-14 generica a 4.10.0-42 generica<br/>Da 4.11.0-13 generica a 4.11.0-14 generica<br/>Da 4.13.0-16 generica a 4.13.0-45 generica<br/>da 4.15.0-13-generico a 4.15.0-46-generico<br/>Da 4.11.0-1009 Azure a 4.11.0-1016 Azure<br/>Da 4.13.0-1005 Azure a 4.13.0-1018 Azure <br/>4.15.0-1012-Azure a 4.15.0-1040-Azure|

### <a name="debian-kernel-versions"></a>Versioni del kernel Debian


**Versione supportata** | **Versione del servizio Mobility** | **Versione del kernel** |
--- | --- | --- |
Debian 7 | [9,24][9.24 UR], [9,25][9.25 UR],[9,26][9.26 UR], [9,27][9.27 UR]| Da 3.2.0-4-amd64 a 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9,27][9.27 UR] | da 3.16.0-4-amd64 a 3.16.0-9-amd64, 4.9.0-0. BPO. 4-amd64 a 4.9.0 0. BPO. 9-amd64 |
Debian 8 | [9,24][9.24 UR], [9,25][9.25 UR], [9,26][9.26 UR] | da 3.16.0-4-amd64 a 3.16.0-8-amd64, 4.9.0 0. BPO. 4-amd64 a 4.9.0 0. BPO. 8-amd64 |

### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 (versioni del kernel supportate)

**Versione** | **Versione del servizio Mobility** | **Versione del kernel** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9,27][9.27 UR] | Da 3.12.49-11 SP1 predefinita a 3.12.74-60.64.40 predefinita</br></br> SP1 (LTSS) 3.12.74-60.64.45-default a 3.12.74-60.64.115-default</br></br> Da 4.4.21-69 SP2 predefinita a 4.4.120-92.70 predefinita</br></br>SP2 (LTSS) 4.4.121-92.73-default a 4.4.121-92.114-default</br></br>SP3 4.4.73-5-il valore predefinito è 4.4.180-94.97-default</br></br>SP3 4.4.138-4.7-da Azure a 4.4.180-4.31-Azure</br></br>SP4 4.12.14-94.41-impostazione predefinita per 4.12.14-95.19-impostazione predefinita</br>SP4 4.12.14-6.3-Azure per 4.12.14-6.15-Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9.26][9.26 UR] | Da 3.12.49-11 SP1 predefinita a 3.12.74-60.64.40 predefinita</br></br> SP1 (LTSS) 3.12.74-60.64.45-default a 3.12.74-60.64.110-default</br></br> Da 4.4.21-69 SP2 predefinita a 4.4.120-92.70 predefinita</br></br>SP2 (LTSS) 4.4.121-92.73-default a 4.4.121-92.109-default</br></br>SP3 4.4.73-5-il valore predefinito è 4.4.178-94.91-default</br></br>SP3 4.4.138-4.7-da Azure a 4.4.178-4,28-Azure</br></br>SP4 4.12.14-94.41-impostazione predefinita per 4.12.14-95.16-impostazione predefinita</br>SP4 4.12.14-6.3-da Azure a 4.12.14-6.9-Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9.25][9.25 UR] | Da 3.12.49-11 SP1 predefinita a 3.12.74-60.64.40 predefinita</br></br> Da 3.12.74-60.64.45 SP1 (LTSS) predefinita a 3.12.74-60.64.107 predefinita</br></br> Da 4.4.21-69 SP2 predefinita a 4.4.120-92.70 predefinita</br></br>SP2 (LTSS) 4.4.121-92.73-default a 4.4.121-92.104-default</br></br>SP3 4.4.73-5-il valore predefinito è 4.4.176-94.88-default</br></br>SP3 4.4.138-4.7-da Azure a 4.4.176-4.25-Azure</br></br>SP4 4.12.14-94.41-impostazione predefinita per 4.12.14-95.13-impostazione predefinita</br>SP4 4.12.14-6.3-da Azure a 4.12.14-6.9-Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9.24][9.24 UR] | Da 3.12.49-11 SP1 predefinita a 3.12.74-60.64.40 predefinita</br></br> Da 3.12.74-60.64.45 SP1 (LTSS) predefinita a 3.12.74-60.64.107 predefinita</br></br> Da 4.4.21-69 SP2 predefinita a 4.4.120-92.70 predefinita</br></br>SP2 (LTSS) 4.4.121-92.73-default a 4.4.121-92.101-default</br></br>SP3 4.4.73-5-il valore predefinito è 4.4.175-94.79-default</br></br>SP4 4.12.14-94.41-impostazione predefinita per 4.12.14-95.6-impostazione predefinita |


## <a name="linux-file-systemsguest-storage"></a>File system/archiviazione guest Linux

**Componente** | **Supportato**
--- | ---
File system | ext3, ext4, XFS
Gestore volumi | -LVM è supportato.<br/> -/boot in LVM è supportato dall' [aggiornamento cumulativo 31](https://support.microsoft.com/help/4478871/) (versione 9,20 del servizio Mobility) in poi. Non è supportata nelle versioni precedenti del servizio Mobility.<br/> -Non sono supportati più dischi del sistema operativo.
Dispositivi di archiviazione paravirtualizzati | I dispositivi esportati da driver paravirtualizzati non sono supportati.
Dispositivi di I/O a blocchi a code multiple | Non supportati.
Server fisici con controller di archiviazione HP CCISS | Non supportati.
Convenzione di denominazione del dispositivo e del punto di montaggio | Il nome del dispositivo o del punto di montaggio deve essere univoco.<br/> Verificare che due dispositivi o punti di montaggio non includano nomi con distinzione tra maiuscole e minuscole. Ad esempio, i dispositivi di denominazione per la stessa macchina virtuale di *device1* e *device1* non sono supportati.
Directory | Se si esegue una versione del servizio Mobility precedente alla versione 9,20 (rilasciata nell' [aggiornamento cumulativo 31](https://support.microsoft.com/help/4478871/)), si applicano le restrizioni seguenti:<br/><br/> -Queste directory (se impostate come partizioni o file System separati) devono trovarsi nello stesso disco del sistema operativo nel server di origine:/(root),/boot,/usr,/usr/local,/var, ecc</br> -La directory/boot deve trovarsi in una partizione del disco e non essere un volume LVM.<br/><br/> Dalla versione 9,20 in poi, queste restrizioni non si applicano. 
Directory di avvio | -I dischi di avvio non devono essere nel formato di partizione GPT. Si tratta di una limitazione dell'architettura di Azure. I dischi GPT sono supportati come dischi dati.<br/><br/> Non sono supportati più dischi di avvio in una macchina virtuale<br/><br/> -/boot su un volume LVM in più dischi non è supportato.<br/> -Non è possibile replicare un computer senza un disco di avvio.
Requisiti di spazio disponibile nel disco| 2 GB nella partizione /root <br/><br/> 250 MB nella cartella di installazione
XFSv5 | Sono supportate le funzionalità di XFSv5 nei file system XFS, ad esempio il checksum dei metadati (versione del servizio Mobility 9,10 in poi).<br/> Usare l'utilità xfs_info per controllare il superblocco XFS per la partizione. Se `ftype` è impostato su 1, le funzionalità di XFSv5 sono in uso.
BTRFS | BTRFS è supportato dall' [aggiornamento cumulativo 34](https://support.microsoft.com/help/4490016) (versione 9,22 del servizio Mobility) in poi. BTRFS non è supportato se:<br/><br/> -Il sottovolume file system BTRFS viene modificato dopo l'abilitazione della protezione.</br> -Il file system BTRFS viene distribuito su più dischi.</br> -Il file system BTRFS supporta RAID.

## <a name="vmdisk-management"></a>Gestione della macchina virtuale e del disco

**Azione** | **Dettagli**
--- | ---
Ridimensionamento del disco nella macchina virtuale replicata | Supportato.
Aggiunta del disco alla macchina virtuale replicata | Non supportati.<br/> Disabilitare la replica per la macchina virtuale, aggiungere il disco e quindi abilitare nuovamente la replica.

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
Disco dinamico | Il disco del sistema operativo deve essere un disco di base. <br/><br/>I dischi dati possono essere dinamici
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
ISCSI Guest/server | Per la migrazione-Sì<br/>Per il ripristino di emergenza-No, iSCSI effettuerà il failback come disco collegato alla macchina virtuale
SMB 3.0 guest/server | No
RDM guest/server | Yes<br/><br/> N/D per server fisici
Disco guest/server > 1 TB | Sì, il disco deve avere dimensioni maggiori di 1024 MB<br/><br/>Fino a 8.192 GB durante la replica a Managed Disks (9,26 versioni successive)<br></br> Fino a 4.095 GB durante la replica in account di archiviazione
Disco guest/server con dimensioni logiche di settore a 4 KB e dimensioni fisiche di settore a 4 KB | No
Disco Guest/server con dimensioni del settore fisico 4K logiche e 512 byte | No
Volume con disco con striping > 4 TB guest/server <br/><br/>Gestione volumi logici (LVM)| Yes
Guest/server - Spazi di archiviazione | No
Disco di aggiunta/rimozione a caldo guest/server | No
Guest/server - esclusione disco | Yes
Percorsi multipli (MPIO) guest/server | No
Partizioni GPT Guest/server | Sono supportate cinque partizioni dall' [aggiornamento cumulativo 37](https://support.microsoft.com/help/4508614/) (versione 9,25 del servizio Mobility). In precedenza ne erano supportate quattro.
ReFS | Resilient file System è supportato con il servizio Mobility versione 9,23 o successiva
Avvio EFI/UEFI Guest/server | -Supportato quando si esegue il servizio Mobility versione 9,13 o successiva.<br/> -Supportato quando si esegue la migrazione di macchine virtuali VMware o server fisici che eseguono Windows Server 2012 o versioni successive in Azure.<br/> -È possibile replicare solo le macchine virtuali per la migrazione. Il failback in locale non è supportato.<br/> -È supportato solo NTFS <br/> -Il tipo di avvio UEFI sicuro non è supportato. <br/> -Le dimensioni del settore disco devono essere di 512 byte per ogni settore fisico.

## <a name="replication-channels"></a>Canali di replica

|**Tipo di replica**   |**Supportato**  |
|---------|---------|
|Trasferimenti di dati con offload (ODX)    |       No  |
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
Crittografia inattiva (SSE)| Yes
Archiviazione Premium | Yes
Servizio di importazione/esportazione | No
Firewall di archiviazione di Azure per reti virtuali | Sì.<br/> Configurato nell'account di archiviazione di destinazione/archiviazione della cache (usato per archiviare i dati di replica).
Account di archiviazione per utilizzo generico V2 (livelli ad accesso frequente e sporadico) | Sì (i costi delle transazioni sono sostanzialmente superiori per la versione V2 rispetto alla V1)

## <a name="azure-compute"></a>Calcolo di Azure

**Funzionalità** | **Supportato**
--- | ---
Set di disponibilità | Yes
Zone di disponibilità | No
HUB | Yes
Dischi gestiti | Yes

## <a name="azure-vm-requirements"></a>Requisiti per le VM di Azure

Le macchine virtuali locali replicate in Azure devono soddisfare i requisiti della macchina virtuale di Azure riepilogati in questa tabella. Quando Site Recovery esegue un controllo dei prerequisiti per la replica, il controllo ha esito negativo se alcuni dei requisiti non vengono soddisfatti.

**Componente** | **Requisiti** | **Dettagli**
--- | --- | ---
Sistema operativo guest | Verificare i [sistemi operativi supportati](#replicated-machines) per le macchine replicate. | Il controllo ha esito negativo se non supportato.
Architettura del sistema operativo guest | 64 bit. | Il controllo ha esito negativo se non supportato.
Dimensioni disco del sistema operativo | Fino a 2.048 GB. | Il controllo ha esito negativo se non supportato.
Conteggio dischi del sistema operativo | 1 | Il controllo ha esito negativo se non supportato.
Conteggio dischi dati | 64 o un numero inferiore. | Il controllo ha esito negativo se non supportato.
Dimensioni del disco dati | Fino a 8.192 GB durante la replica in un disco gestito (versione 9,26 e successive)<br></br>Fino a 4.095 GB durante la replica nell'account di archiviazione| Il controllo ha esito negativo se non supportato.
Schede di rete | Sono supportate più schede. |
VHD condiviso | Non supportati. | Il controllo ha esito negativo se non supportato.
Disco FC | Non supportati. | Il controllo ha esito negativo se non supportato.
BitLocker | Non supportati. | Prima di abilitare la replica per un computer, occorre disabilitare BitLocker. |
Nome VM | Da 1 a 63 caratteri.<br/><br/> Limitato a lettere, numeri e trattini.<br/><br/> Il nome del computer deve iniziare e terminare con una lettera o un numero. |  Aggiornare il valore nelle proprietà del computer in Site Recovery.

## <a name="resource-group-limits"></a>Limiti del gruppo di risorse

Per informazioni sul numero di macchine virtuali che possono essere protette in un singolo gruppo di risorse, vedere l'articolo relativo ai [limiti e alle quote della sottoscrizione](https://docs.microsoft.com/azure/azure-subscription-service-limits#resource-group-limits)

## <a name="churn-limits"></a>Limiti di varianza

La tabella seguente indica i limiti di Azure Site Recovery. 
- Questi limiti sono basati sui test, ma non coprono tutte le possibili combinazioni di I/O delle app.
- I risultati effettivi possono variare in base alla combinazione di I/O delle applicazioni.
- Per ottenere risultati ottimali, è consigliabile eseguire lo [strumento Deployment Planner](site-recovery-deployment-planner.md)ed eseguire test completi dell'applicazione usando i failover di test per ottenere il quadro reale delle prestazioni per l'app.

**Destinazione di replica** | **Dimensioni medie I/O disco di origine** |**Varianza dati media disco di origine** | **Varianza dati totale giornaliera disco di origine**
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
Spostare l'insieme di credenziali in e tra sottoscrizioni | No
Spostamento di risorse di archiviazione, rete e VM di Azure tra gruppi di risorse | No
Spostare le macchine virtuali di archiviazione, di rete e di Azure tra le sottoscrizioni. | No


## <a name="obtain-latest-components"></a>Ottenere i componenti più recenti

**Name** | **Descrizione** | **Dettagli**
--- | --- | ---
Server di configurazione | Installata in locale.<br/> Coordina le comunicazioni tra i server VMware locali o i computer fisici e Azure. | - Informazioni [sul server di](vmware-physical-azure-config-process-server-overview.md) configurazione.<br/> - Informazioni [sull'](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) aggiornamento alla versione più recente.<br/> - Informazioni sulla configurazione del server [di](vmware-azure-deploy-configuration-server.md) configurazione. 
Server di elaborazione | Installato per impostazione predefinita nel server di configurazione.<br/> Riceve i dati di replica, li ottimizza con la memorizzazione nella cache, la compressione e la crittografia e li invia ad Azure.<br/> Man mano che la distribuzione cresce, è possibile aggiungere altri server di elaborazione per gestire volumi più elevati di traffico di replica. | - Informazioni [sul server di](vmware-physical-azure-config-process-server-overview.md) elaborazione.<br/> - Informazioni [sull'](vmware-azure-manage-process-server.md#upgrade-a-process-server) aggiornamento alla versione più recente.<br/> - Informazioni [sulla configurazione dei server di](vmware-physical-large-deployment.md#set-up-a-process-server) elaborazione con scalabilità orizzontale.
Servizio di mobilità | Installato nella macchina virtuale VMware o nei server fisici che si desidera replicare.<br/> Coordina la replica tra server VMware locali/server fisici e Azure.| - Informazioni [sul](vmware-physical-mobility-service-overview.md) servizio Mobility.<br/> - Informazioni [sull'](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) aggiornamento alla versione più recente.<br/> 



## <a name="next-steps"></a>Passaggi successivi
[Informazioni su come](tutorial-prepare-azure.md) preparare Azure per il ripristino di emergenza delle macchine virtuali VMware.

[9.27 UR]: https://support.microsoft.com/en-in/help/4513507/update-rollup-38-for-azure-site-recovery
[9.26 UR]: https://support.microsoft.com/en-in/help/4513507/update-rollup-38-for-azure-site-recovery
[9.25 UR]: https://support.microsoft.com/en-in/help/4508614/update-rollup-37-for-azure-site-recovery
[9.24 UR]: https://support.microsoft.com/en-in/help/4503156
[9.23 UR]: https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery
[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery
