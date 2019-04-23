---
title: Requisiti del server di configurazione per il ripristino di emergenza di VMware in Azure con Azure Site Recovery | Microsoft Docs
description: Questo articolo descrive il supporto e i requisiti relativi alla distribuzione di un server di configurazione per il ripristino di emergenza di VMware in Azure con Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 03/18/2019
ms.author: raynew
ms.openlocfilehash: 399dcd744819cf4cb5d9f5f9636967c34e186a0e
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60004602"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>Requisiti del server di configurazione per il ripristino di emergenza di VMware in Azure

Si distribuisce un server di configurazione locale quando si usa [Azure Site Recovery](site-recovery-overview.md) per il ripristino di emergenza di server fisici e macchine virtuali VMware in Azure.

- Il server di configurazione coordina le comunicazioni tra VMware locale e Azure. Gestisce anche la replica dei dati.
- [Altre informazioni](vmware-azure-architecture.md) sui processi e i componenti del server di configurazione.

## <a name="configuration-server-deployment"></a>Distribuzione del server di configurazione

Per il ripristino di emergenza di macchine virtuali VMware in Azure occorre distribuire il server di configurazione come macchina virtuale VMware.

- Site Recovery fornisce un modello OVA da scaricare dal portale di Azure e da importare nel server vCenter per impostare la macchina virtuale del server di configurazione.
- Quando si distribuisce il server di configurazione mediante il modello OVA, la macchina virtuale si conforma automaticamente ai requisiti elencati in questo articolo.
- È consigliabile impostare il server di configurazione con il modello OVA. Se però si sta configurando il ripristino di emergenza per le macchine virtuali VMware e non è possibile usare il modello OVA, è possibile distribuire il server di configurazione seguendo [queste istruzioni](physical-azure-set-up-source.md).
- Se si distribuisce il server di configurazione per il ripristino di emergenza di computer fisici locali in Azure, seguire le istruzioni riportate in [questo articolo](physical-azure-set-up-source.md). 


## <a name="hardware-requirements"></a>Requisiti hardware

**Componente** | **Requisito** 
--- | ---
Core CPU | 8 
RAM | 16 GB
Numero di dischi | 3, inclusi disco del sistema operativo, disco della cache del server di elaborazione e unità di conservazione per il failback 
Spazio libero su disco (cache del server di elaborazione) | 600 GB
Spazio libero su disco (disco di conservazione) | 600 GB

## <a name="software-requirements"></a>Requisiti software

**Componente** | **Requisito** 
--- | ---
Sistema operativo | Windows Server 2012 R2 <br> Windows Server 2016
Impostazioni locali del sistema operativo | Inglese (en-us)
Ruoli di Windows Server | Non abilitare questi ruoli: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V 
Criteri di gruppo | Non abilitare questi criteri di gruppo: <br> - Impedisci accesso al prompt dei comandi <br> - Impedisci accesso agli strumenti di modifica del Registro di sistema <br> - Logica di attendibilità per file allegati <br> - Attiva l'esecuzione di script <br> [Altre informazioni](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - Nessun sito Web predefinito preesistente <br> - Nessun sito Web o applicazione preesistente in ascolto sulla porta 443 <br>- Abilitare l'[autenticazione anonima](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - Abilitare l'impostazione di [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 

## <a name="network-requirements"></a>Requisiti di rete

**Componente** | **Requisito** 
--- | --- 
Tipo di indirizzo IP | statico 
Accesso a Internet | Il server deve poter accedere a questi URL (direttamente o tramite proxy): <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com  <br> - https:\//management.azure.com <br> - *.services.visualstudio.com <br> - time.nist.gov <br> - time.windows.com <br> OVF necessita anche di accedere agli URL seguenti: <br> - https:\//login.microsoftonline.com <br> - https:\//secure.aadcdn.microsoftonline-p.com <br> - https:\//login.live.com  <br> - https:\//auth.gfx.ms <br> - https:\//graph.windows.net <br> - https:\//login.windows.net <br> - https:\//www.live.com <br> - https:\//www.microsoft.com <br> - https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi 
Porte | 443 (orchestrazione del canale di controllo)<br>9443 (trasporto dei dati) 
Tipo di scheda di interfaccia di rete | VMXNET3 (se il server di configurazione è una macchina virtuale VMware)

## <a name="required-software"></a>Requisiti software

**Componente** | **Requisito** 
--- | ---
VMware vSphere PowerCLI | [PowerCLI versione 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) deve essere installato se il server di configurazione è in esecuzione in una macchina virtuale VMware.
MYSQL | MySQL deve essere installato. È possibile eseguire l'installazione manualmente o con Site Recovery.

## <a name="sizing-and-capacity-requirements"></a>Requisiti di dimensioni e capacità

La tabella seguente riepiloga i requisiti di capacità del server di configurazione. Se si esegue la replica di più macchine virtuali VMware, rivedere le [considerazioni sulla pianificazione della capacità](site-recovery-plan-capacity-vmware.md) ed eseguire lo strumento [Azure Site Recovery Deployment Planner](site-recovery-deployment-planner.md) per la replica VMWare. 

**Componente** | **Requisito** 
--- | ---

| **CPU** | **Memoria** | **Disco cache** | **Frequenza di modifica dei dati** | **Computer replicati** |
| --- | --- | --- | --- | --- |
| 8 vCPU<br/><br/> 2 socket * 4 core \@ 2,5 GHz | 16 GB | 300 GB | 500 GB o inferiore | Meno di 100 computer |
| 12 vCPU<br/><br/> 2 socket * 6 core \@ 2,5 GHz | 18 GB | 600 GB | 500 GB-1 TB | Da 100 a 150 computer |
| 16 vCPU<br/><br/> 2 socket * 8 core \@ 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150-200 computer | 



## <a name="next-steps"></a>Passaggi successivi
Configurare il ripristino di emergenza di [macchine virtuali VMware](vmware-azure-tutorial.md) in Azure.
