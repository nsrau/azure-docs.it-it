---
title: Preparare la distribuzione di Site Recovery | Microsoft Docs
description: Questo articolo descrive come preparare la distribuzione della replica con Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: e24eea6c-50a7-4cd5-aab4-2c5c4d72ee2d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/06/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 5ed46a6bd8931d3b8829a053ace12fd219f108c4
ms.openlocfilehash: 6430ae9a97888ef993cd3dd35eea765fe95bb43f

---

# <a name="prepare-for-azure-site-recovery-deployment"></a>Preparare la distribuzione di Azure Site Recovery

Le organizzazioni necessitano di una strategia di continuità aziendale e ripristino di emergenza per determinare come app, carichi di lavoro e dati possano rimanere in esecuzione e disponibili durante i periodi di inattività, pianificati o meno, e come ripristinare le normali condizioni di lavoro il prima possibile. Il servizio Azure Site Recovery contribuisce alla strategia BCDR orchestrando la replica dei server fisici locali e delle macchine virtuali nel cloud (Azure) o in un data center secondario. In caso di interruzioni nella località primaria, verrà eseguito il failover alla località secondaria per mantenere disponibili app e carichi di lavoro. Quando la località primaria sarà di nuovo operativa, si tornerà a tale località. Per altre informazioni, vedere [Che cos'è Site Recovery?](site-recovery-overview.md)

Questo articolo offre un riepilogo dei requisiti generali per ogni scenario di replica di Site Recovery e collegamenti alle procedure dettagliate per la distribuzione.  

È possibile inserire commenti nella parte inferiore di questo articolo oppure porre domande tecniche nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).



## <a name="azure-deployment-models"></a>Modelli di distribuzione di Azure

Azure offre due [modelli di distribuzione](../azure-resource-manager/resource-manager-deployment-model.md) diversi per la creazione e l'uso delle risorse: il modello Azure Resource Manager e il modello di distribuzione classica. Sono anche disponibili il [portale di Azure classico](https://manage.windowsazure.com/), che supporta il modello di distribuzione classica, e il [portale di Azure](https://ms.portal.azure.com/), che supporta entrambi i modelli di distribuzione.

È consigliabile distribuire i nuovi scenari di Site Recovery nel [portale di Azure](https://portal.azure.com). Questo portale offre nuove funzionalità e un'esperienza di distribuzione migliorata. Nel portale classico è possibile gestire gli insiemi di credenziali, ma non crearne di nuovi.


## <a name="deployment-scenarios"></a>Scenari di distribuzione

Di seguito sono riportate le operazioni di replica che è possibile eseguire con Site Recovery.

| **Distribuzione** | **Dettagli** | **Portale di Azure** | **Portale classico** | **Distribuzione PowerShell** |
| --- | --- | --- | --- | --- |
| [Da VMware ad Azure](site-recovery-vmware-to-azure.md) | Replica di VM VMware locali in Archiviazione di Azure | Uso di reti e risorse di archiviazione classiche o Resource Manager. Failover in VM classiche o basate su Resource Manager. | Solo modalità manutenzione. Non è possibile creare nuovi insiemi di credenziali. | Attualmente non è supportata. |
| [Da server fisici ad Azure](site-recovery-vmware-to-vmware.md) | Replica da server fisici Windows e Linux all'archiviazione di Azure | Uso di reti e risorse di archiviazione classiche o Resource Manager. Failover in VM classiche o basate su Resource Manager. | Solo modalità manutenzione. Non è possibile creare nuovi insiemi di credenziali.  |
| [Da VM Hyper-V in cloud VMM ad Azure](site-recovery-vmm-to-azure.md) | Replica in Archiviazione di Azure di VM Hyper-V locali in cloud VMM.<br/><br/> | Uso di reti e risorse di archiviazione classiche o Resource Manager. Failover in VM classiche o basate su Resource Manager.   | Solo modalità manutenzione. Non è possibile creare nuovi insiemi di credenziali. | Supportato |
| [Hyper-V VMs to Azure (no VMM)](site-recovery-hyper-v-site-to-azure.md) (Da VM Hyper-V ad Azure (senza VMM)) | Replica di VM Hyper-V locali in Archiviazione di Azure. | Uso di reti e risorse di archiviazione classiche o Resource Manager. Failover in VM classiche o basate su Resource Manager. | Solo modalità manutenzione. Non è possibile creare nuovi insiemi di credenziali. | Supportato |
| Da server fisici/VM VMware a sito secondario(site-recovery-vmware-to-vmware.md) | Replica da VM VMware o server fisici Windows/Linux a un sito secondario<br/><br/> [Scaricare il manuale dell'utente](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf) di InMage Scout. | Non disponibile nel portale di Azure | Scaricare InMage Scout da un insieme di credenziali di Site Recovery. | Non supportate |
| [Da VM Hyper-V a un sito secondario](site-recovery-vmm-to-vmm.md) | Replica da VM Hyper-V in cloud VMM a un data center secondario  | Viene usata la replica Hyper-V standard. La replica SAN non è supportata. | Replica Hyper-V o [replica SAN](site-recovery-vmm-san.md) | Supportato |

## <a name="requirements-for-replication-to-azure"></a>Requisiti per la replica in Azure

| **Requisito** | **Dettagli** |
| --- | --- |
| **Account di Azure** | Account [Microsoft Azure](http://azure.microsoft.com/) .<br/><br/> È possibile iniziare con una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/). [Altre informazioni](https://azure.microsoft.com/pricing/details/site-recovery/) sui prezzi di Site Recovery. |
| **Archiviazione di Azure** | I dati replicati vengono memorizzati in Archiviazione di Azure e le macchine virtuali di Azure vengono create quando si verifica il failover. È necessario aggiungere un [account di archiviazione di Azure](../storage/storage-introduction.md).<br/><br/>Nel portale di Azure usare l'archiviazione con [ridondanza geografica](../storage/storage-redundancy.md#geo-redundant-storage) o con ridondanza locale. Nel portale classico è supportata solo l'archiviazione con ridondanza geografica.<br/><br/> Se si esegue la replica di server fisici o VM VMware nel portale di Azure, è supportata l'archiviazione Premium.<br/><br/>  |
| **Rete di Azure** | È necessaria una rete di Azure a cui si connetteranno le VM di Azure. <br/><br/> Nel portale di Azure è possibile usare una rete classica o Resource Manager.<br/><br/> La rete deve trovarsi nella stessa area dell'insieme di credenziali.<br/><br/> In caso di replica da VMM ad Azure, si configurerà il [mapping di rete](site-recovery-network-mapping.md) tra le reti di VM VMM e le reti di Azure in modo da garantire che le VM di Azure si connettano alle reti appropriate dopo il failover. |
| **Locale** |**VM VMware**: computer locale che esegue i componenti di Site Recovery. Sono anche necessari gli host VMware vSphere e il server vCenter, nonché le VM da replicare. [Altre informazioni](site-recovery-vmware-to-azure.md#configuration-server-or-additional-process-server-prerequisites).<br/><br/> **Server fisici**: computer locale che esegue i componenti di Site Recovery e i server fisici da replicare. [Altre informazioni](site-recovery-vmware-to-azure.md#configuration-server-or-additional-process-server-prerequisites). Se si vuole eseguire il [failback](site-recovery-failback-azure-to-vmware.md) di server fisici da Azure, è necessaria un'infrastruttura VMware.<br/><br/> **VM Hyper-V**: server VMM e host Hyper-V contenenti le VM da replicare. [Altre informazioni](site-recovery-vmm-to-azure.md#on-premises-prerequisites).<br/><br/> Per replicare VM Hyper-V senza VMM, sono necessari solo host Hyper-V. [Altre informazioni](site-recovery-hyper-v-site-to-azure.md#on-premises-prerequisites). |
| **Computer protetti** | I computer protetti che eseguiranno la replica ad Azure devono essere conformi ai [prerequisiti di Azure](#azure-virtual-machine-requirements) descritti di seguito. |

## <a name="requirements-for-replication-to-a-secondary-site"></a>Requisiti per la replica in un sito secondario

| **Requisito** | **Dettagli** |
| --- | --- |
| **Azzurro** | Account [Microsoft Azure](http://azure.microsoft.com/) .<br/><br/> È possibile iniziare con una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/). [Altre informazioni](https://azure.microsoft.com/pricing/details/site-recovery/) sui prezzi di Site Recovery. |
| **Locale** |**VM VMware**: nel sito primario è necessario un server di elaborazione per il caching, la compressione e la crittografia dei dati di replica. Nel sito secondario è necessario un server di configurazione per gestire e monitorare la distribuzione e un server di destinazione master. Per una gestione più semplice, è consigliabile anche un server vContinuum. Sono anche necessari uno o più host VMware vSphere e, facoltativamente, un server vCenter. [Altre informazioni](site-recovery-vmware-to-vmware.md)<br/><br/> **VM Hyper-V in cloud VMM**: server VMM e host Hyper-V contenenti le VM da replicare. [Altre informazioni](site-recovery-vmm-to-vmm.md#on-premises-prerequisites). |
| **Rete (da VMM a VMM)** | In caso di replica da VMM a VMM, si configura il [mapping di rete](site-recovery-network-mapping.md) per garantire che le VM di replica siano connesse alle reti appropriate dopo il failover e siano correttamente posizionate negli host Hyper-V. |

## <a name="url-access"></a>Accesso a URL

Gli URL seguenti dovranno essere disponibili dai server host Hyper-V, VMware e VMM.

**URL** | **Da VMM a VMM** | **Da VMM ad Azure** | **Da Hyper-V ad Azure** | **Da VMware ad Azure** |
|--- | --- | --- | --- | ---
``*.accesscontrol.windows.net`` | Consenti | Consenti | Consenti | Consenti
``*.backup.windowsazure.com`` | Facoltativo | Consenti | Consenti | Consenti
``*.hypervrecoverymanager.windowsazure.com`` | Consenti | Consenti | Consenti | Consenti
``*.store.core.windows.net`` | Consenti | Consenti | Consenti | Consenti
``*.blob.core.windows.net`` | Facoltativo | Consenti | Consenti | Consenti
``https://www.msftncsi.com/ncsi.txt`` | Consenti | Consenti | Consenti | Consenti
``https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi`` | Facoltativo | Facoltativo | Facoltativo | Consentire download SQL
``time.windows.com`` | Consenti | Consenti | Consenti | Consenti
``time.nist.gov`` | Consenti | Consenti | Consenti | Consenti



## <a name="azure-virtual-machine-requirements"></a>Requisiti delle macchine virtuali di Azure

È possibile distribuire Site Recovery per replicare macchine virtuali e server fisici che eseguono qualsiasi sistema operativo supportato da Azure, vale a dire la maggior parte delle versioni di Windows e Linux. Le macchine virtuali locali da replicare devono essere conformi ai requisiti di Azure.

**Funzionalità** | **Requisiti** | **Dettagli**
--- | --- | ---
**Host Hyper-V** | Deve eseguire Windows Server 2012 R2 o versione successiva | Il controllo dei prerequisiti avrà esito negativo se il sistema operativo non è supportato.
**Hypervisor VMware** | Sistema operativo supportato. | [Controllare i requisiti](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)
**Sistema operativo guest** | Replica da Hyper-V ad Azure: Site Recovery supporta tutti i sistemi operativi [supportati da Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> Per la replica di server fisici e VMware, controllare i [prerequisiti](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) | Il controllo dei prerequisiti avrà esito negativo se non supportato.
**Architettura sistema operativo guest** | 64 bit | Il controllo dei prerequisiti avrà esito negativo se non supportato
**Dimensioni disco del sistema operativo** | Fino a 1023 GB | Il controllo dei prerequisiti avrà esito negativo se non supportato
**Numero di dischi del sistema operativo** | 1 | Il controllo dei prerequisiti avrà esito negativo se non supportato.
**Numero di dischi dati** | 16 o meno (il valore massimo è una funzione delle dimensioni della macchina virtuale creata; 16 = XL) | Il controllo dei prerequisiti avrà esito negativo se non supportato
**Dimensioni dei dischi rigidi virtuali dei dischi dati** | Fino a 1023 GB | Il controllo dei prerequisiti avrà esito negativo se non supportato
**Schede di rete** | Sono supportate più schede |
**Indirizzo IP statico** | Supportato | Se la macchina virtuale principale usa un indirizzo IP statico, è possibile specificare l'indirizzo IP statico per la macchina virtuale che verrà creata in Azure<br/><br/> L'indirizzo IP statico non è supportato per una **VM Linux in esecuzione in Hyper-V**.
**Disco iSCSI** | Non supportate | Il controllo dei prerequisiti avrà esito negativo se non supportato
**Disco rigido virtuale condiviso** | Non supportate | Il controllo dei prerequisiti avrà esito negativo se non supportato
**Disco FC** | Non supportate | Il controllo dei prerequisiti avrà esito negativo se non supportato
**Formato disco rigido** | VHD  <br/><br/> VHDX | Anche se VHDX al momento non è supportato in Azure, in Site Recovery VHDX viene convertito automaticamente in VHD quando si esegue il failover in Azure. Quando si esegue il failback in locale, le macchine virtuali continuano a utilizzare il formato VHDX.
**BitLocker** | Non supportate | Prima di proteggere una macchina virtuale è necessario disabilitare BitLocker.
**Nome VM** | Tra 1 e 63 caratteri. Limitato a lettere, numeri e trattini. Il nome deve iniziare e terminare con una lettera o un numero | Aggiornare il valore nelle proprietà della macchina virtuale in Site Recovery
**Tipo di VM** | Prima generazione<br/><br/> Seconda generazione - Windows | Sono supportate le VM di seconda generazione con disco del sistema operativo di base che include uno o più volumi di dati in formato VHDX e inferiori a 300 GB.<br/><br/>. Le VM Linux di seconda generazione non sono supportate. [Altre informazioni](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/) |

## <a name="deployment-optimization"></a>Ottimizzazione della distribuzione

Usare i suggerimenti seguenti per ottimizzare e ridimensionare la distribuzione.

- **Dimensioni del volume del sistema operativo**: quando si replica una macchina virtuale in Azure, il volume del sistema operativo deve essere inferiore a 1 TB. Se si dispone di volumi superiori, è possibile spostarli manualmente in un altro disco prima di iniziare la distribuzione.
- **Dimensioni dei dischi dati**: se si esegue la replica in Azure è possibile avere fino a 64 dischi dati in una macchina virtuale, ognuno con un massimo di 1 TB. È possibile eseguire in modo efficiente la replica e il failover di una macchina virtuale di circa 64 TB.
- **Limiti del piano di ripristino**: Site Recovery è in grado di supportare migliaia di macchine virtuali. I piani di ripristino sono progettati come modello per le applicazioni devono eseguire il failover insieme. Pertanto, il numero di macchine in un piano di ripristino viene limitato a 50.
- **Limiti dei servizi di Azure**: ogni sottoscrizione di Azure include un set di limiti predefiniti su core, servizi cloud e così via. È consigliabile eseguire un failover di test per convalidare la disponibilità di risorse nella sottoscrizione. È possibile modificare questi limiti tramite il supporto di Azure.
- **Pianificazione della capacità**: altre informazioni sulla [pianificazione della capacità](site-recovery-capacity-planner.md) per Site Recovery.
- **Larghezza di banda della replica**: in caso di larghezza di banda insufficiente, è opportuno notare quanto indicato di seguito.
- **ExpressRoute**: Site Recovery funziona con Azure ExpressRoute e con gli ottimizzatori WAN, ad esempio Riverbed. [Ulteriori informazioni](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx) su ExpressRoute.
- **Traffico di replica**: Site Recovery esegue una replica iniziale intelligente utilizzando solo i blocchi di dati, non l'intero VHD. Solo le modifiche vengono replicate durante la replica in corso.
- **Traffico di rete**: è possibile controllare il traffico di rete utilizzato per la replica configurando [QoS di Windows](https://technet.microsoft.com/library/hh967468.aspx) con un criterio basato sull'indirizzo IP di destinazione e la porta.  Inoltre, se si esegue la replica ad Azure Site Recovery usando l'agente di backup di Azure è possibile configurare la limitazione per l'agente. [Altre informazioni](https://support.microsoft.com/kb/3056159).
- **RTO**: per misurare l'obiettivo del tempo di ripristino (RTO) che è possibile prevedere con Site Recovery, è consigliabile eseguire un failover di test e visualizzare i processi di Site Recovery per analizzare il tempo necessario per completare le operazioni. Se si sta eseguendo il failover ad Azure, per l'obiettivo RTO ottimale è consigliabile automatizzare tutte le azioni manuali mediante l'integrazione con i piani di automazione e di ripristino di Azure.
- **RPO**: Site Recovery supporta un obiettivo del punto di ripristino (RPO) quasi sincrono quando si esegue la replica ad Azure. Ciò presuppone sufficiente larghezza di banda tra il datacenter e Azure.



## <a name="next-steps"></a>Passaggi successivi
Dopo aver esaminato i requisiti di distribuzione generali, vedere i prerequisiti dettagliati e distribuire uno scenario.

* [Replicare le macchine virtuali VMWare locali in Azure](site-recovery-vmware-to-azure.md)
* [Replicare i server fisici in Azure](site-recovery-vmware-to-azure.md)
* [Replicare il server Hyper-V in cloud VMM in Azure](site-recovery-vmm-to-azure.md)
* [Replicare le macchine virtuali Hyper-V (senza VMM) in Azure](site-recovery-hyper-v-site-to-azure.md)
* [Replicare le VM Hyper-V in un sito secondario](site-recovery-vmm-to-vmm.md)
* [Replicare le VM Hyper-V con un singolo server VMM](site-recovery-single-vmm.md)



<!--HONumber=Dec16_HO1-->


