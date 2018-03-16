---
title: Matrice di supporto per la replica Hyper-V di Azure | Microsoft Docs
description: Vengono riepilogati i componenti supportati e i requisiti per la replica Hyper-V in Azure con Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/06/2018
ms.author: raynew
ms.openlocfilehash: 81983b9287a6b8073724f0cd973929f4b4677d4a
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="support-matrix-for-hyper-v-replication-to-azure"></a>Matrice di supporto per la replica Hyper-V in Azure


Questo articolo riepiloga i componenti supportati e le impostazioni per il ripristino di emergenza di VM Hyper-V locali in Azure con il servizio [Azure Site Recovery](site-recovery-overview.md).


## <a name="supported-scenarios"></a>Scenari supportati

**Scenario** | **Dettagli**
--- | --- 
**Hyper-V con VMM** | È possibile eseguire il ripristino di emergenza in Azure per le VM in esecuzione in host Hyper-V gestiti nell'infrastruttura di System Center Virtual Machine Manager (VMM)<br/><br/> Si può distribuire questo scenario con il portale di Azure o con PowerShell.<br/><br/> Se gli host Hyper-V vengono gestiti da VMM, è anche possibile eseguire il ripristino di emergenza in un sito locale secondario. Per altre informazioni su questo scenario, vedere [questo articolo](tutorial-vmm-to-vmm.md).
**Hyper-V senza VMM** | È possibile eseguire il ripristino di emergenza in Azure per le VM in esecuzione in host Hyper-V non gestiti da VMM.<br/><br/> Si può distribuire questo scenario con il portale di Azure o con PowerShell. 


## <a name="on-premises-servers"></a>Server locali

**Server** | **Requisiti** | **Dettagli**
--- | --- | ---
**Hyper-V (in esecuzione senza VMM)** | Windows Server 2016, Windows Server 2012 R2 con gli aggiornamenti più recenti. | Quando si configura un sito Hyper-V in Site Recovery, la combinazione di host che eseguono Windows Server 2016 e 2012 R2 non è supportata.<br/><br/> Per le VM situate in un host che esegue Windows Server 2016, non è supportato il ripristino in un percorso alternativo.
**Hyper-V (in esecuzione con VMM)** | VMM 2016, VMM 2012 R2 | Se si usa VMM, gli host Windows Server 2016 devono essere gestiti in VMM 2016.<br/><br/> Un cloud VMM con una combinazione di host Hyper-V che eseguono Windows Server 2016 e 2012 R2 non è attualmente supportato.<br/><br/> Gli ambienti che includono un aggiornamento di un server di VMM 2012 R2 esistente alla versione 2016 non sono supportati.


## <a name="replicated-vms"></a>VM replicate


La tabella seguente riepiloga il supporto per VM. Site Recovery supporta i carichi di lavoro in esecuzione in un sistema operativo supportato. 

 **Componente** | **Dettagli**
--- | ---
Configurazione della macchina virtuale | Le VM che eseguono la replica in Azure devono soddisfare i [requisiti di Azure](#failed-over-azure-vm-requirements).
Sistema operativo guest | Qualsiasi sistema operativo guest [supportato da Azure](https://technet.microsoft.com/library/cc794868.aspx).<br/><br/> Windows Server 2016 Nano Server non è supportato.




## <a name="hyper-v-network-configuration"></a>Configurazione di rete Hyper-V

**Componente** | **Hyper-V con VMM** | **Hyper-V senza VMM**
--- | --- | ---
Rete host:gruppo NIC | Sì
Host di rete:VLAN | Sì
Host di rete:IPv4 | Sì
Host di rete:IPv6 | No 
Rete VM guest:gruppo NIC | No 
Rete VM guest:IPv4 | Sì
Rete VM guest:IPv6 | No 
Rete VM guest:IP statico (Windows) | Sì
Rete VM guest:IP statico (Linux) | No 
Rete VM guest:multi-NIC | Sì



## <a name="azure-vm-network-configuration-after-failover"></a>Configurazione di rete per VM di Azure (dopo il failover)

**Componente** | **Hyper-V con VMM** | **Hyper-V senza VMM**
--- | --- | ---
ExpressRoute | Sì | Sì
ILB | Sì | Sì
ELB | Sì | Sì
servizio Gestione traffico | Sì | Sì
Più NIC | Sì | Sì
IP riservato | Sì | Sì
IPv4 | Sì | Sì
Conservazione indirizzo IP di origine | Sì | Sì
Endpoint del servizio di rete virtuale<br/><br/> (firewall e reti virtuali di Archiviazione di Azure) | No  | No 


## <a name="hyper-v-host-storage"></a>Archiviazione host Hyper-V

**Archiviazione** | **Hyper-V con VMM** | Hyper-V senza VMM
--- | --- | --- | ---
NFS | ND | ND
SMB 3.0 | Sì | Sì
SAN (iSCSI) | Sì | Sì
Percorsi multipli (MPIO). Testato con:<br></br> Microsoft DSM, EMC PowerPath 5.7 SP4<br/><br/> EMC PowerPath DSM for CLARiiON | Sì | Sì

## <a name="hyper-v-vm-guest-storage"></a>Archiviazione VM guest Hyper-V

**Archiviazione** | **Hyper-V con VMM** | Hyper-V senza VMM
--- | --- | ---
VMDK | ND | ND
VHD/VHDX | Sì | Sì
VM Gen 2 | Sì | Sì
EFI/UEFI| Sì | Sì
Disco cluster condiviso | No  | No 
Disco crittografato | No  | No 
NFS | ND | ND
SMB 3.0 | No  | No 
RDM | ND | ND
Disco superiore a 1 TB | Sì, fino a 4095 GB | Sì, fino a 4095 GB
Disco: dimensioni logiche di settore e dimensioni fisiche a 4 KB | Non supportato:Gen 1/Gen 2 | Non supportato:Gen 1/Gen 2
Disco: dimensioni logiche di settore a 4 KB e dimensioni fisiche di settore a 512 byte | Sì |  Sì
Volume con disco con striping superiore a 1 TB<br/><br/> Gestione volumi logici (LVM) | Sì | Sì
Spazi di archiviazione | Sì | Sì
Aggiunta/rimozione a caldo disco | No  | No 
Esclusione disco | Sì | Sì
Percorsi multipli (MPIO) | Sì | Sì

## <a name="azure-storage"></a>Archiviazione di Azure

**Componente** | **Hyper-V con VMM** | **Hyper-V (senza VMM)**
--- | --- | ---
Archiviazione con ridondanza locale | Sì | Sì
Archiviazione con ridondanza geografica | Sì | Sì
RA-GRS | Sì | Sì
Archiviazione ad accesso sporadico | No  | No 
Archiviazione ad accesso frequente| No  | No 
BLOB in blocchi | No  | No 
Crittografia dei dati inattivi (SSE)| Sì | Sì
Archiviazione Premium | Sì | Sì
Servizio di importazione/esportazione | No  | No 
Endpoint del servizio di rete virtuale (firewall e reti virtuali di Archiviazione di Azure) in un account di archiviazione di destinazione/archiviazione della cache usato per i dati di replica | No  | No 


## <a name="azure-compute-features"></a>Funzionalità di calcolo di Azure

**Funzionalità** | **Hyper-V con VMM** | **Hyper-V senza VMM**
--- | --- | ---
Set di disponibilità | Sì | Sì
HUB | Sì | Sì  
Dischi gestiti | Sì, per il failover<br/><br/> Non è supportato il failback di dischi gestiti | Sì, per il failover<br/><br/> Non è supportato il failback di dischi gestiti

## <a name="azure-vm-requirements"></a>Requisiti per le VM di Azure

Le macchine virtuali locali replicate in Azure devono soddisfare i requisiti per le VM di Azure riepilogati in questa tabella.

**Componente** | **Requisiti** | **Dettagli**
--- | --- | ---
**Sistema operativo guest** | Site Recovery supporta tutti i sistemi operativi [supportati da Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).  | Il controllo dei prerequisiti avrà esito negativo se non supportato.
**Architettura sistema operativo guest** | 64 bit | Il controllo dei prerequisiti avrà esito negativo se non supportato.
**Dimensioni disco del sistema operativo** | Fino a 2048 GB per le VM Gen 1.<br/><br/> Fino a 300 GB per le VM Gen 2.  | Il controllo dei prerequisiti avrà esito negativo se non supportato.
**Numero di dischi del sistema operativo** | 1 | Il controllo dei prerequisiti avrà esito negativo se non supportato.
**Numero di dischi dati** | Fino a 16  | Il controllo dei prerequisiti avrà esito negativo se non supportato.
**Dimensioni dei dischi rigidi virtuali dei dischi dati** | Fino a 4095 GB | Il controllo dei prerequisiti avrà esito negativo se non supportato.
**Schede di rete** | Sono supportate più schede |
**Disco rigido virtuale condiviso** | Non supportate | Il controllo dei prerequisiti avrà esito negativo se non supportato.
**Disco FC** | Non supportate | Il controllo dei prerequisiti avrà esito negativo se non supportato.
**Formato disco rigido** | VHD  <br/><br/> VHDX | In Site Recovery VHDX viene convertito automaticamente in VHD quando si esegue il failover in Azure. Quando si esegue il failback in locale, le macchine virtuali continuano a utilizzare il formato VHDX.
**BitLocker** | Non supportate | Prima di abilitare la replica per una VM occorre disabilitare BitLocker.
**Nome VM** | Tra 1 e 63 caratteri. Limitato a lettere, numeri e trattini. Il nome della macchina virtuale deve iniziare e terminare con una lettera o un numero. | Aggiornare il valore nelle proprietà della VM in Site Recovery.
**Tipo di VM** | Prima generazione<br/><br/> Seconda generazione - Windows | Sono supportate le macchine virtuali di seconda generazione con disco del sistema operativo di base che include uno o più volumi di dati in formato VHDX e inferiori a 300 GB di spazio su disco.<br></br>Le macchine virtuali Linux di seconda generazione non sono supportate. [Altre informazioni](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)|

## <a name="recovery-services-vault-actions"></a>Azioni dell'insieme di credenziali dei Servizi di ripristino

**Azione** |  **Hyper-V con VMM** | **Hyper-V senza VMM**
--- | --- | --- 
Spostamento insieme di credenziali tra gruppi di risorse<br/><br/> All'interno e tra sottoscrizioni | No  | No  
Spostamento di risorse di archiviazione, rete e VM di Azure tra gruppi di risorse<br/><br/> All'interno e tra sottoscrizioni | No  | No  


## <a name="provider-and-agent"></a>Provider e agente

Per assicurarsi che la distribuzione sia compatibile con le impostazioni in questo articolo, accertarsi di eseguire le versioni più recenti del provider e dell'agente.

**Nome** | **Descrizione** | **Dettagli**
--- | --- | --- | --- | ---
**Provider di Azure Site Recovery** | Coordina le comunicazioni tra server locali e Azure <br/><br/> Hyper-V con VMM: installato nei server VMM<br/><br/> Hyper-V senza VMM: installato negli host Hyper-V| Versione più recente: 5.1.2700.1 (disponibile dal portale)<br/><br/> [Funzionalità e correzioni più recenti](https://aka.ms/latest_asr_updates)
**Agente di Servizi di ripristino di Microsoft Azure (MARS)** | Coordina la replica tra le macchine virtuali Hyper-V e Azure<br/><br/> Installato nei server Hyper-V locali (con o senza VMM) | Agente più recente disponibile dal portale






## <a name="next-steps"></a>Passaggi successivi
Informazioni su come [preparare Azure](tutorial-prepare-azure.md) per il ripristino di emergenza delle VM Hyper-V. 
