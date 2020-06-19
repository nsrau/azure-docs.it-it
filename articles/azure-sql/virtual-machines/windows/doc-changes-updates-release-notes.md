---
title: Modifiche alla documentazione di SQL Server in Macchine virtuali di Azure| Microsoft Docs
description: Informazioni sulle nuove funzionalità e i miglioramenti per SQL Server in una macchina virtuale Azure
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
manager: craigg
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/06/2020
ms.openlocfilehash: f87d72df977e98c60948389d794eb102ac08f8d2
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84032472"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Modifiche alla documentazione di SQL Server in Macchine virtuali di Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Azure consente di distribuire una macchina virtuale (VM) con un'immagine di SQL Server predefinita. Questo articolo presenta una sintesi delle modifiche alla documentazione associate alle nuove funzionalità e ai miglioramenti introdotti nelle recenti versioni di [SQL Server in Macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). 


## <a name="january-2020"></a>Gennaio 2020

| Modifiche | Dettagli |
| --- | --- |
| **Supporto di Azure per enti pubblici** | È ora possibile registrare le macchine virtuali SQL Server con il provider di risorse di macchine virtuali SQL per le macchine virtuali ospitate nel cloud [Azure per enti pubblici](https://azure.microsoft.com/global-infrastructure/government/). | 
| &nbsp; | &nbsp; |

## <a name="2019"></a>2019

|Modifiche | Dettagli |
 --- | --- |
| **Replica di ripristino di emergenza gratuita in Azure** | È possibile ospitare un'[istanza passiva gratuita](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) per il ripristino di emergenza in Azure per l'istanza di SQL Server locale se si è aderito al programma [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3). | 
| **Registrazione in blocco con il provider di risorse** | È ora possibile [registrare in blocco](sql-vm-resource-provider-bulk-register.md) le macchine virtuali SQL con il provider di risorse. | 
|**Configurazione dell'archiviazione ottimizzata per le prestazioni** | Quando si crea una nuova VM di SQL Server è ora possibile [personalizzare completamente la configurazione di archiviazione](storage-configuration.md#new-vms). |
|**Condivisione file Premium per FCI** | È ora possibile creare un'istanza del cluster di failover usando una [condivisione file Premium](failover-cluster-instance-premium-file-share-manually-configure.md) anziché il metodo originale di [Spazi di archiviazione diretta](failover-cluster-instance-storage-spaces-direct-manually-configure.md). 
| **Host dedicato di Azure** | È possibile eseguire la VM di SQL Server in un [host dedicato di Azure](dedicated-host.md). | 
| **Spostamento della macchina virtuale SQL in un'area diversa** | Usare Azure Site Recovery per la [migrazione della VM di SQL Server da un'area a un'altra](move-sql-vm-different-region.md). |
|  **Nuove modalità di installazione di SQL IaaS** | È ora possibile installare l'estensione IaaS di SQL Server in [modalità leggera](sql-server-iaas-agent-extension-automate-management.md) per evitare di riavviare il servizio SQL Server.  |
| **Modifica dell'edizione di SQL Server** | È ora possibile modificare la [proprietà dell'edizione](change-sql-server-edition.md) per la VM di SQL Server. |
| **Modifiche al provider di risorse di macchine virtuali SQL** | È possibile [registrare la VM di SQL Server con il provider di risorse di macchine virtuali SQL](sql-vm-resource-provider-register.md) usando le nuove modalità di SQL IaaS. Questa funzionalità include le immagini di [Windows Server 2008](sql-vm-resource-provider-register.md#management-modes).|
| **Immagini Bring Your Own License tramite il Vantaggio Azure Hybrid** | Per le immagini Bring Your Own License distribuite da Azure Marketplace è ora possibile modificare il [tipo di licenza in pagamento in base al consumo](licensing-model-azure-hybrid-benefit-ahb-change.md#remarks).| 
| **Nuova gestione delle VM di SQL Server nel portale di Azure** | È ora possibile gestire le VM di SQL Server nel portale di Azure. Per altre informazioni, vedere [Gestire le VM di SQL Server nel portale di Azure](manage-sql-vm-portal.md).  | 
| **Supporto "Extended" per SQL Server 2008/2008 R2** | [Estendere il supporto](sql-server-2008-extend-end-of-support.md) per SQL Server 2008 e SQL Server 2008 R2 eseguendo la migrazione *senza modifiche* a una macchina virtuale di Azure. | 
| **Supportabilità di immagini personalizzate** | Per personalizzare le immagini del sistema operativo e di SQL è ora possibile installare l'[estensione IaaS di SQL Server](sql-server-iaas-agent-extension-automate-management.md#installation) che offre funzionalità limitate di [licenze flessibili](licensing-model-azure-hybrid-benefit-ahb-change.md). Al momento della registrazione dell'immagine personalizzata con il provider di risorse SQL, specificare "AHUB" come tipo di licenza. In caso contrario, la registrazione non riesce. | 
| **Supportabilità di istanze denominate** | È ora possibile usare l'[estensione IaaS di SQL Server](sql-server-iaas-agent-extension-automate-management.md#installation) con un'istanza denominata, se l'istanza predefinita è stata disinstallata correttamente. | 
| **Miglioramenti del portale** | L'esperienza del portale di Azure per la distribuzione di una VM di SQL Server è stata rinnovata per migliorare l'usabilità. Per altre informazioni, vedere la breve guida di [avvio rapido](sql-vm-create-portal-quickstart.md) e altre indicazioni disponibili nella [guida pratica](create-sql-vm-portal.md) per distribuire una VM di SQL Server.|
| **Potenziamento del portale** | Ora è possibile cambiare il modello di licenza per una VM di SQL Server passando dal modello con pagamento in base al consumo al modello BYOL (Bring Your Own License) tramite il [portale di Azure](licensing-model-azure-hybrid-benefit-ahb-change.md#vms-already-registered-with-the-resource-provider).|
| **Semplificazione della distribuzione dei gruppi di disponibilità con l'interfaccia della riga di comando della VM di SQL Server in Azure** | Distribuire un gruppo di disponibilità in una VM di SQL Server in Azure è ora più facile che mai. È possibile usare l'[interfaccia della riga di comando di Azure](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) per creare il cluster di failover di Windows, il servizio di bilanciamento del carico interno e i listener del gruppo di disponibilità dalla riga di comando. Per altre informazioni, vedere [Usare l'interfaccia della riga di comando della VM di SQL Server in Azure per configurare un gruppo di disponibilità Always On per SQL Server in una macchina virtuale di Azure](availability-group-az-cli-configure.md). | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018 

 Modifiche | Dettagli |
| --- | --- |
|  **Nuovo provider di risorse per un cluster SQL Server** | Un nuovo provider di risorse (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups) definisce i metadati del cluster di failover di Windows. L'aggiunta di una VM di SQL Server a *SqlVirtualMachineGroups* determina l'avvio del servizio Windows Server Failover Cluster (WSFC) e l'aggiunta della VM al cluster.  |
| **Configurazione automatica della distribuzione di un gruppo di disponibilità con modelli di avvio rapido di Azure** |È ora possibile creare il cluster di failover di Windows, aggiungervi VM di SQL Server, creare il listener e configurare il servizio di bilanciamento del carico interno con due modelli di avvio rapido di Azure. Per altre informazioni, vedere [Usare modelli di avvio rapido di Azure per configurare un gruppo di disponibilità Always On per SQL Server in una macchina virtuale di Azure](availability-group-quickstart-template-configure.md). | 
| **Registrazione automatica nel provider di risorse di macchine virtuali SQL** | Le VM di SQL Server distribuite dopo il mese corrente vengono registrate automaticamente con il nuovo provider di risorse di SQL Server. Le VM di SQL Server distribuite prima del mese corrente devono ancora essere registrate manualmente. Per altre informazioni, vedere [Registrare una macchina virtuale di SQL Server in Azure con il provider di risorse di macchine virtuali SQL](sql-vm-resource-provider-register.md).|
|**Nuovo provider di risorse per VM di SQL** |  Un nuovo provider di risorse (Microsoft.SqlVirtualMachine) consente una migliore gestione delle VM di SQL Server. Per altre informazioni sulla registrazione delle macchine virtuali, vedere [Registrare una macchina virtuale di SQL Server in Azure con il provider di risorse di macchine virtuali SQL](sql-vm-resource-provider-register.md). |
|**Cambiare il modello di licenza** | Tramite l'interfaccia della riga di comando di Azure o PowerShell è ora possibile passare dal modello con pagamento in base all'utilizzo al modello BYOL (Bring Your Own License) per la VM di SQL Server. Per altre informazioni, vedere [Come cambiare il modello di licenza per una macchina virtuale SQL Server in Azure](licensing-model-azure-hybrid-benefit-ahb-change.md). | 
| &nbsp; | &nbsp; |

## <a name="additional-resources"></a>Risorse aggiuntive

**Macchine virtuali Windows**:

* [Panoramica di SQL Server in una macchina virtuale Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Effettuare il provisioning di una macchina virtuale Windows di SQL Server](create-sql-vm-portal.md)
* [Migrazione di un database a SQL Server in una VM di Azure](migrate-to-vm-from-sql-server.md)
* [Disponibilità elevata e ripristino di emergenza per SQL Server in Macchine virtuali di Azure](business-continuity-high-availability-disaster-recovery-hadr-overview.md)
* [Procedure consigliate per le prestazioni per SQL Server in Macchine virtuali di Azure](performance-guidelines-best-practices.md)
* [Modelli di applicazione e strategie di sviluppo per SQL Server in Macchine virtuali di Azure](application-patterns-development-strategies.md)

**Macchine virtuali Linux**:

* [Panoramica di SQL Server in una macchina virtuale Linux](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
* [Effettuare il provisioning di una macchina virtuale Linux con SQL Server](../linux/sql-vm-create-portal-quickstart.md)
* [Domande frequenti (Linux)](../linux/frequently-asked-questions-faq.md)
* [Documentazione di SQL Server in Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
