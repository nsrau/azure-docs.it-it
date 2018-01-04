---
title: Matrice di supporto per la replica Hyper-V di Azure | Documenti Microsoft
description: Vengono riepilogati i componenti supportati e i requisiti per la replica Hyper-V in Azure con Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 12/31/2017
ms.author: raynew
ms.openlocfilehash: 5918c56c2b7d01c884bf846e3a7d621b3393bb96
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="support-matrix-for-hyper-v-replication-to-azure"></a>Matrice di supporto per la replica Hyper-V in Azure


In questo articolo vengono riepilogati i componenti supportati e le impostazioni per il ripristino di emergenza delle macchine virtuali Hyper-V in locale in Azure, utilizzando il [Azure Site Recovery](site-recovery-overview.md) servizio.


## <a name="supported-scenarios"></a>Scenari supportati

**Scenario** | **Dettagli**
--- | --- 
**Hyper-V con VMM** | È possibile eseguire il ripristino di emergenza in Azure per le macchine virtuali in esecuzione in host Hyper-V gestiti nell'infrastruttura di System Center Virtual Machine Manager (VMM)<br/><br/> È possibile distribuire questo scenario nel portale di Azure o tramite PowerShell.<br/><br/> Se gli host Hyper-V vengono gestiti da VMM, è anche possibile eseguire il ripristino di emergenza in un sito secondario in locale. Lettura [questo articolo](tutorial-vmm-to-vmm.md) per ulteriori informazioni su questo scenario.
**Hyper-V senza VMM** | È possibile eseguire il ripristino di emergenza in Azure per le macchine virtuali in esecuzione in host Hyper-V che non sono gestiti da VMM.<br/><br/> È possibile distribuire questo scenario nel portale di Azure o tramite Powershell. 


## <a name="on-premises-servers"></a>Server locali

**Server** | **Requisiti** | **Dettagli**
--- | --- | ---
**Hyper-V (in esecuzione senza VMM)** | Windows Server 2016, Windows Server 2012 R2 con gli aggiornamenti più recenti. | Quando si configura un sito Hyper-V in Site Recovery, la combinazione di host che eseguono Windows Server 2016 e 2012 R2 non è supportata.<br/><br/> Per le macchine virtuali che si trova in un host che esegue Windows Server 2016, non è supportato il ripristino in un percorso alternativo.
**Hyper-V (in esecuzione con VMM)** | 2016 VMM, VMM 2012 R2 | Se si utilizza VMM, gli host Windows Server 2016 devono essere gestiti in VMM 2016.<br/><br/> Un cloud VMM che combina gli host Hyper-V in esecuzione su Windows Server 2016 e 2012 R2 non è attualmente supportato.<br/><br/> Gli ambienti che includono un aggiornamento di un server di VMM 2012 R2 esistente a 2016 non sono supportati.


## <a name="replicated-vms"></a>Macchine virtuali replicate


Nella tabella seguente viene riepilogato il supporto di macchina virtuale. Il ripristino del sito supporta i carichi di lavoro in esecuzione in un sistema operativo supportato. 

 **Componente** | **Dettagli**
--- | ---
Configurazione della macchina virtuale | Macchine virtuali che vengono replicati in Azure devono soddisfare [requisiti Azure](#failed-over-azure-vm-requirements).
Sistema operativo guest | Qualsiasi sistema operativo guest [supportato da Azure](https://technet.microsoft.com/library/cc794868.aspx).<br/><br/> Windows Server 2016, Nano Server non è supportata.




## <a name="hyper-v-network-configuration"></a>Configurazione di rete Hyper-V

**Componente** | **Hyper-V con VMM** | **Hyper-V senza VMM**
--- | --- | ---
Host rete: gruppo NIC | Sì
Host di rete: VLAN | Sì
Host rete: IPv4 | Sì
Host rete: IPv6 | No 
Macchina virtuale Guest rete: gruppo NIC | No 
Rete di macchina virtuale Guest: IPv4 | Sì
Rete di macchina virtuale Guest: IPv6 | No 
IP di rete: statico macchina virtuale Guest (Windows) | Sì
IP di rete: statico macchina virtuale Guest (Linux) | No 
Rete di macchina virtuale Guest: Multi-NIC | Sì



## <a name="azure-vm-network-configuration-after-failover"></a>Configurazione della rete VM Azure (dopo il failover)

**Componente** | **Hyper-V con VMM** | **Hyper-V senza VMM**
--- | --- | ---
ExpressRoute | Sì | Sì
ILB | Sì | Sì
ELB | Sì | Sì
servizio Gestione traffico | Sì | Sì
Più NIC | Sì | Sì
IP riservato | Sì | Sì
IPv4 | Sì | Sì
Mantenere l'indirizzo IP di origine | Sì | Sì
Endpoint del servizio di rete virtuale<br/><br/> (I firewall di archiviazione di azure e le reti virtuali) | No  | No 


## <a name="hyper-v-host-storage"></a>Archiviazione di host Hyper-V

**Archiviazione** | **Hyper-V con VMM** | Hyper-V senza VMM
--- | --- | --- | ---
NFS | ND | ND
SMB 3.0 | Sì | Sì
SAN (iSCSI) | Sì | Sì
Percorsi multipli (MPIO). Testato con:<br></br> DSM Microsoft, EMC PowerPath 5.7 SP4<br/><br/> EMC PowerPath DSM per CLARiiON | Sì | Sì

## <a name="hyper-v-vm-guest-storage"></a>Archiviazione guest macchina virtuale Hyper-V

**Archiviazione** | **Hyper-V con VMM** | Hyper-V senza VMM
--- | --- | ---
VMDK | ND | ND
VHD/VHDX | Sì | Sì
Generazione 2 VM | Sì | Sì
EFI/UEFI| Sì | Sì
Disco cluster condiviso | No  | No 
Disco crittografato | No  | No 
NFS | ND | ND
SMB 3.0 | No  | No 
RDM | ND | ND
Disco superiore a 1 TB | Sì, fino a 4095 GB | Sì, fino a 4095 GB
Disco: settori logici e fisici di 4K | Non è supportato: generazione 1/generazione 2 | Non è supportato: generazione 1/generazione 2
: Disco logico di 4 KB e settore fisico a 512 byte | Sì |  Sì
Volume con disco con striping superiore a 1 TB<br/><br/> Gestione volumi logici (LVM) | Sì | Sì
Spazi di archiviazione | Sì | Sì
Aggiunta/rimozione a caldo disco | No  | No 
Esclusione disco | Sì | Sì
Percorsi multipli (MPIO) | Sì | Sì

## <a name="azure-storage"></a>Archiviazione di Azure

**Componente** | **Hyper-V con VMM** | **Hyper-V senza VMM)**
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
Rete virtuale gli endpoint del servizio firewall di archiviazione di Azure e le reti virtuali, sulla destinazione per account di archiviazione della cache usato per i dati di replica | No  | No 


## <a name="azure-compute-features"></a>Funzionalità di calcolo di Azure

**Funzionalità** | **Hyper-V con VMM** | **Hyper-V senza VMM**
--- | --- | ---
Set di disponibilità | Sì | Sì
HUB | Sì | Sì  
Dischi gestiti | Sì, per il failover<br/><br/> Non è supportato il failback di dischi gestiti | Sì, per il failover<br/><br/> Non è supportato il failback di dischi gestiti

## <a name="azure-vm-requirements"></a>Requisiti di macchina virtuale Azure

Le macchine virtuali in locale che si esegue la replica in Azure devono soddisfare i requisiti di macchina virtuale di Azure riepilogati in questa tabella.

**Componente** | **Requisiti** | **Dettagli**
--- | --- | ---
**Sistema operativo guest** | Il ripristino del sito supporta tutti i sistemi operativi [supportato da Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).  | Il controllo dei prerequisiti avrà esito negativo se non supportato.
**Architettura sistema operativo guest** | 64 bit | Il controllo dei prerequisiti avrà esito negativo se non supportato.
**Dimensioni disco del sistema operativo** | Fino a 2048 GB per le macchine virtuali di generazione 1.<br/><br/> Fino a 300 GB per le macchine virtuali di generazione 2.  | Il controllo dei prerequisiti avrà esito negativo se non supportato.
**Numero di dischi del sistema operativo** | 1 | Il controllo dei prerequisiti avrà esito negativo se non supportato.
**Numero di dischi dati** | minore o uguale a 16  | Il controllo dei prerequisiti avrà esito negativo se non supportato.
**Dimensioni dei dischi rigidi virtuali dei dischi dati** | Fino a 4095 GB | Il controllo dei prerequisiti avrà esito negativo se non supportato.
**Schede di rete** | Sono supportate più schede |
**Disco rigido virtuale condiviso** | Non supportate | Il controllo dei prerequisiti avrà esito negativo se non supportato.
**Disco FC** | Non supportate | Il controllo dei prerequisiti avrà esito negativo se non supportato.
**Formato disco rigido** | VHD  <br/><br/> VHDX | Quando si esegue il failover in Azure, il ripristino del sito converte automaticamente VHDX in VHD. Quando si esegue il failback in locale, le macchine virtuali continuano a utilizzare il formato VHDX.
**BitLocker** | Non supportate | BitLocker deve essere disabilitato prima di abilitare la replica per una macchina virtuale.
**Nome VM** | Tra 1 e 63 caratteri. Limitato a lettere, numeri e trattini. Il nome della macchina virtuale deve iniziare e terminare con una lettera o un numero. | Aggiornare il valore nelle proprietà della macchina virtuale in Site Recovery.
**Tipo di VM** | Prima generazione<br/><br/> Seconda generazione - Windows | Sono supportate le macchine virtuali di seconda generazione con disco del sistema operativo di base che include uno o più volumi di dati in formato VHDX e inferiori a 300 GB di spazio su disco.<br></br>Le macchine virtuali Linux di seconda generazione non sono supportate. [Altre informazioni](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)|

## <a name="recovery-services-vault-actions"></a>Azioni di insieme di credenziali per servizi di ripristino

**Azione** |  **Hyper-V con VMM** | **Hyper-V senza VMM**
--- | --- | --- 
Spostamento insieme di credenziali tra gruppi di risorse<br/><br/> All'interno e tra sottoscrizioni | No  | No  
Spostamento di risorse di archiviazione, rete e VM di Azure tra gruppi di risorse<br/><br/> All'interno e tra sottoscrizioni | No  | No  


## <a name="provider-and-agent"></a>Il provider e agente

Per assicurarsi che la distribuzione è compatibile con le impostazioni in questo articolo, verificare che si esegue il provider più recente e le versioni di agente.

**Nome** | **Descrizione** | **Dettagli**
--- | --- | --- | --- | ---
**Provider di Azure Site Recovery** | Coordina le comunicazioni tra server locali e Azure <br/><br/> Hyper-V con VMM: installato nei server VMM<br/><br/> Hyper-V senza VMM: installate in host Hyper-V| Versione più recente: 5.1.2700.1 (disponibile dal portale)<br/><br/> [Funzionalità e correzioni più recenti](https://aka.ms/latest_asr_updates)
**Agente di Servizi di ripristino di Microsoft Azure (MARS)** | Coordina la replica tra le macchine virtuali Hyper-V e Azure<br/><br/> Installato nel server di Hyper-V locale (con o senza VMM) | Ultima versione dell'agente disponibile dal portale






## <a name="next-steps"></a>Passaggi successivi
Informazioni su come [preparazione di Azure](tutorial-prepare-azure.md) per il ripristino di emergenza delle macchine virtuali Hyper-V locale. 
