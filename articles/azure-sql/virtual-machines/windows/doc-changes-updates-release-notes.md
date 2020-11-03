---
title: Modifiche alla documentazione di SQL Server in Macchine virtuali di Azure
description: Informazioni sulle nuove funzionalità e i miglioramenti per le diverse versioni di SQL Server in macchine virtuali di Azure.
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: reference
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/15/2020
ms.openlocfilehash: 249e65b5a9440678015598d9969c0d2ed689c626
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93285364"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Modifiche alla documentazione di SQL Server in Macchine virtuali di Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Azure consente di distribuire una macchina virtuale (VM) con un'immagine di SQL Server predefinita. Questo articolo presenta una sintesi delle modifiche alla documentazione associate alle nuove funzionalità e ai miglioramenti introdotti nelle recenti versioni di [SQL Server in Macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). 

## <a name="october-2020"></a>Ottobre 2020

| Modifiche | Dettagli |
| --- | --- |
| **DNN per AG** | È ora possibile configurare un [listener DNN (Distributed Network Name](availability-group-distributed-network-name-dnn-listener-configure.md) ) per SQL Server 2019 CU8 e versioni successive per sostituire il [listener VNN](availability-group-overview.md#connectivity)tradizionale, negando la necessità di un Azure Load Balancer.   | 

## <a name="september-2020"></a>Settembre 2020

| Modifiche | Dettagli |
| --- | --- |
| **Registrazione RP automatica** | È ora possibile abilitare la funzionalità di [registrazione automatica](sql-vm-resource-provider-automatic-registration.md) per registrare automaticamente tutte le macchine virtuali SQL Server già distribuite nella sottoscrizione, nonché tutte le VM SQL Server aggiunte in futuro.  | 


## <a name="august-2020"></a>Agosto 2020

| Modifiche | Dettagli |
| --- | --- |
| **Configurare il gruppo di disponibilità nel portale** | È ora possibile [configurare il gruppo di disponibilità tramite il portale di Azure](availability-group-azure-portal-configure.md). Questa funzionalità è attualmente in fase di anteprima e viene distribuita, quindi, se l'area desiderata non è disponibile, riprovare più tardi. | 


## <a name="july-2020"></a>Luglio 2020


| Modifiche | Dettagli |
| --- | --- |
| **Migrare il log su disco Ultra** | Informazioni su come è possibile [eseguire la migrazione del file di log a un disco Ultra](storage-migrate-to-ultradisk.md) per sfruttare le prestazioni elevate e la bassa latenza. | 
| **Creare un'AG usando PowerShell** | È ora possibile semplificare la creazione di un gruppo di disponibilità usando [PowerShell](availability-group-az-commandline-configure.md) e l'interfaccia della riga di comando di Azure. | 


## <a name="june-2020"></a>Giugno 2020

| Modifiche | Dettagli |
| --- | --- |
| **DNN (nome di rete distribuita)** | SQL Server 2019 in Windows Server 2016 + è ora in anteprima il supporto per il routing del traffico all'istanza del cluster di failover (FCI) utilizzando un [nome di rete distribuita](./failover-cluster-instance-distributed-network-name-dnn-configure.md) anziché l'utilizzo di Azure Load Balancer. Questo supporto semplifica e semplifica la connessione alla soluzione a disponibilità elevata in Azure. | 
| **FCI con dischi condivisi di Azure** | È ora possibile distribuire l'istanza del [cluster di failover (FCI)](failover-cluster-instance-overview.md) usando i [dischi condivisi di Azure](failover-cluster-instance-azure-shared-disks-manually-configure.md). |
| **Documentazione FCI riorganizzata** | La documentazione relativa alle [istanze del cluster di failover con SQL Server nelle VM di Azure](failover-cluster-instance-overview.md) è stata riscritta e riorganizzata per maggiore chiarezza. Il contenuto della configurazione è stato separato, ad esempio le [procedure consigliate](hadr-cluster-best-practices.md)per la configurazione del cluster, come preparare una [macchina virtuale per una SQL Server FCI](failover-cluster-instance-prepare-vm.md)e come configurare [Azure Load Balancer](./availability-group-vnn-azure-load-balancer-configure.md). | 
| &nbsp; | &nbsp; |


## <a name="may-2020"></a>Maggio 2020 

| Modifiche | Dettagli |
| --- | --- |
| **Famiglia SQL di Azure** | SQL Server in macchine virtuali di Azure fa ora parte della [famiglia di prodotti SQL di Azure](../../azure-sql-iaas-vs-paas-what-is-overview.md). Scopri il nostro [nuovo aspetto](../index.yml). Non sono state apportate modifiche al prodotto, ma la documentazione ha lo scopo di semplificare la decisione sul prodotto SQL di Azure. | 


## <a name="january-2020"></a>Gennaio 2020

| Modifiche | Dettagli |
| --- | --- |
| **Supporto di Azure per enti pubblici** | È ora possibile registrare SQL Server macchine virtuali con il provider di risorse VM SQL per le macchine virtuali ospitate nel cloud di [Azure per enti pubblici](https://azure.microsoft.com/global-infrastructure/government/) . | 
| &nbsp; | &nbsp; |

## <a name="2019"></a>2019

|Modifiche | Dettagli |
 --- | --- |
| **Replica di ripristino di emergenza gratuita in Azure** | È possibile ospitare un'[istanza passiva gratuita](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) per il ripristino di emergenza in Azure per l'istanza di SQL Server locale se si è aderito al programma [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3). | 
| **Registrazione in blocco con il provider di risorse** | È ora possibile [registrare in blocco](sql-vm-resource-provider-bulk-register.md) SQL Server macchine virtuali con il provider di risorse. | 
|**Configurazione dell'archiviazione ottimizzata per le prestazioni** | Quando si crea una nuova VM di SQL Server è ora possibile [personalizzare completamente la configurazione di archiviazione](storage-configuration.md#new-vms). |
|**Condivisione file Premium per FCI** | È ora possibile creare un'istanza del cluster di failover usando una [condivisione file Premium](failover-cluster-instance-premium-file-share-manually-configure.md) anziché il metodo originale di [spazi di archiviazione diretta](failover-cluster-instance-storage-spaces-direct-manually-configure.md). 
| **Host dedicato di Azure** | È possibile eseguire la macchina virtuale SQL Server nell' [host dedicato di Azure](dedicated-host.md). | 
| **SQL Server la migrazione di macchine virtuali in un'area diversa** | Usare Azure Site Recovery per la [migrazione della VM di SQL Server da un'area a un'altra](move-sql-vm-different-region.md). |
|  **Nuove modalità di installazione di SQL IaaS** | È ora possibile installare l'estensione IaaS di SQL Server in [modalità leggera](sql-server-iaas-agent-extension-automate-management.md) per evitare di riavviare il servizio SQL Server.  |
| **Modifica dell'edizione di SQL Server** | È ora possibile modificare la [proprietà dell'edizione](change-sql-server-edition.md) per la VM di SQL Server. |
| **Modifiche al provider di risorse VM SQL** | È possibile [registrare la VM di SQL Server con il provider di risorse di macchine virtuali SQL](sql-vm-resource-provider-register.md) usando le nuove modalità di SQL IaaS. Questa funzionalità include le immagini di [Windows Server 2008](sql-server-iaas-agent-extension-automate-management.md#management-modes).|
| **Immagini Bring Your Own License tramite il Vantaggio Azure Hybrid** | Per le immagini Bring Your Own License distribuite da Azure Marketplace è ora possibile modificare il [tipo di licenza in pagamento in base al consumo](licensing-model-azure-hybrid-benefit-ahb-change.md#remarks).| 
| **Nuova SQL Server gestione delle macchine virtuali nel portale di Azure** | È ora possibile gestire le VM di SQL Server nel portale di Azure. Per altre informazioni, vedere [Gestire le VM di SQL Server nel portale di Azure](manage-sql-vm-portal.md).  | 
| **Supporto esteso per SQL Server 2008 e 2008 R2** | [Estendere il supporto](sql-server-2008-extend-end-of-support.md) per SQL Server 2008 e SQL Server 2008 R2 eseguendo la migrazione *senza modifiche* a una macchina virtuale di Azure. | 
| **Supportabilità di immagini personalizzate** | È ora possibile installare l' [estensione IaaS SQL Server](sql-server-iaas-agent-extension-automate-management.md#installation) per le immagini del sistema operativo e del SQL Server personalizzato, che offre la funzionalità limitata di [licenze flessibili](licensing-model-azure-hybrid-benefit-ahb-change.md). Quando si esegue la registrazione dell'immagine personalizzata con il provider di risorse VM SQL, specificare il tipo di licenza "vantaggio Ahu". In caso contrario, la registrazione non riesce. | 
| **Supportabilità di istanze denominate** | È ora possibile usare l'[estensione IaaS di SQL Server](sql-server-iaas-agent-extension-automate-management.md#installation) con un'istanza denominata, se l'istanza predefinita è stata disinstallata correttamente. | 
| **Miglioramenti del portale** | L'esperienza del portale di Azure per la distribuzione di una VM di SQL Server è stata rinnovata per migliorare l'usabilità. Per altre informazioni, vedere la breve guida di [avvio rapido](sql-vm-create-portal-quickstart.md) e altre indicazioni disponibili nella [guida pratica](create-sql-vm-portal.md) per distribuire una VM di SQL Server.|
| **Potenziamento del portale** | Ora è possibile cambiare il modello di licenza per una VM di SQL Server passando dal modello con pagamento in base al consumo al modello BYOL (Bring Your Own License) tramite il [portale di Azure](licensing-model-azure-hybrid-benefit-ahb-change.md#vms-already-registered-with-the-resource-provider).|
| **Semplificazione della distribuzione del gruppo di disponibilità in una VM SQL Server tramite l'interfaccia della riga di comando di Azure** | Distribuire un gruppo di disponibilità in una VM di SQL Server in Azure è ora più facile che mai. È possibile usare l' [interfaccia](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid&preserve-view=true) della riga di comando di Azure per creare il cluster di failover Windows, il servizio di bilanciamento del carico interno e i listener del gruppo di disponibilità dalla riga di comando. Per altre informazioni, vedere [usare l'interfaccia della riga di comando di Azure per configurare un gruppo di disponibilità always on per SQL Server in una macchina virtuale di Azure](./availability-group-az-commandline-configure.md). | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018 

 Modifiche | Dettagli |
| --- | --- |
|  **Nuovo provider di risorse per un cluster SQL Server** | Un nuovo provider di risorse (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups) definisce i metadati del cluster di failover di Windows. L'aggiunta di una VM di SQL Server a *SqlVirtualMachineGroups* determina l'avvio del servizio Windows Server Failover Cluster (WSFC) e l'aggiunta della VM al cluster.  |
| **Configurazione automatica della distribuzione di un gruppo di disponibilità con modelli di avvio rapido di Azure** |È ora possibile creare il cluster di failover di Windows, aggiungervi SQL Server macchine virtuali, creare il listener e configurare il servizio di bilanciamento del carico interno usando due modelli di avvio rapido di Azure. Per altre informazioni, vedere [Usare modelli di avvio rapido di Azure per configurare un gruppo di disponibilità Always On per SQL Server in una macchina virtuale di Azure](availability-group-quickstart-template-configure.md). | 
| **Registrazione automatica nel provider di risorse di macchine virtuali SQL** | SQL Server macchine virtuali distribuite dopo questo mese vengono registrate automaticamente con il nuovo provider di risorse VM SQL. Le VM di SQL Server distribuite prima del mese corrente devono ancora essere registrate manualmente. Per altre informazioni, vedere [Registrare una macchina virtuale di SQL Server in Azure con il provider di risorse di macchine virtuali SQL](sql-vm-resource-provider-register.md).|
|**Nuovo provider di risorse per VM di SQL** |  Un nuovo provider di risorse (Microsoft.SqlVirtualMachine) consente una migliore gestione delle VM di SQL Server. Per altre informazioni sulla registrazione delle macchine virtuali, vedere [Registrare una macchina virtuale di SQL Server in Azure con il provider di risorse di macchine virtuali SQL](sql-vm-resource-provider-register.md). |
|**Cambiare il modello di licenza** | Tramite l'interfaccia della riga di comando di Azure o PowerShell è ora possibile passare dal modello con pagamento in base all'utilizzo al modello BYOL (Bring Your Own License) per la VM di SQL Server. Per altre informazioni, vedere [Come cambiare il modello di licenza per una macchina virtuale SQL Server in Azure](licensing-model-azure-hybrid-benefit-ahb-change.md). | 
| &nbsp; | &nbsp; |

## <a name="additional-resources"></a>Risorse aggiuntive

**Macchine virtuali Windows** :

* [Panoramica di SQL Server in una macchina virtuale Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Effettuare il provisioning di SQL Server in una macchina virtuale Windows](create-sql-vm-portal.md)
* [Eseguire la migrazione di un database a SQL Server in una macchina virtuale di Azure](migrate-to-vm-from-sql-server.md)
* [Disponibilità elevata e ripristino di emergenza per SQL Server in Macchine virtuali di Azure](business-continuity-high-availability-disaster-recovery-hadr-overview.md)
* [Procedure consigliate per le prestazioni per SQL Server in Macchine virtuali di Azure](performance-guidelines-best-practices.md)
* [Modelli di applicazione e strategie di sviluppo per SQL Server in Macchine virtuali di Azure](application-patterns-development-strategies.md)

**Macchine virtuali Linux** :

* [Panoramica di SQL Server in una macchina virtuale Linux](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
* [Effettuare il provisioning di SQL Server in una macchina virtuale Linux](../linux/sql-vm-create-portal-quickstart.md)
* [Domande frequenti (Linux)](../linux/frequently-asked-questions-faq.md)
* [Documentazione di SQL Server in Linux](/sql/linux/sql-server-linux-overview)