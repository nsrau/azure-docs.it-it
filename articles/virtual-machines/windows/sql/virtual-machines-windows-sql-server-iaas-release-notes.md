---
title: Modifiche alla documentazione per SQL Server in Macchine virtuali di Azure Documenti Microsoft
description: Informazioni sulle nuove funzionalità e sui miglioramenti per SQL Server in una macchina virtuale di AzureLearn about the new features and improvements for SQL Server on an Azure VM
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
ms.openlocfilehash: 27682863a96e2f190a0dafe6e4d783029e987453
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201646"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Modifiche alla documentazione per SQL Server in Macchine virtuali di AzureDocumentation changes for SQL Server on Azure Virtual Machines

Azure consente di distribuire una macchina virtuale (VM) con un'immagine di SQL Server incorporata. In questo articolo vengono riepilogate le modifiche alla documentazione associate alle nuove funzionalità e i miglioramenti apportati alle versioni recenti di SQL Server in Macchine virtuali di [Azure.](https://azure.microsoft.com/services/virtual-machines/sql-server/) 


## <a name="january-2020"></a>Gennaio 2020

| Modifiche | Dettagli |
| --- | --- |
| **Supporto per Azure per enti pubbliciAzure Government support** | È ora possibile registrare le macchine virtuali di SQL Server con il provider di risorse della macchina virtuale SQL per le macchine virtuali ospitate nel cloud [di Azure per enti pubblici.](https://azure.microsoft.com/global-infrastructure/government/) | 
| &nbsp; | &nbsp; |

## <a name="2019"></a>2019

|Modifiche | Dettagli |
 --- | --- |
| **Replica di ripristino di emergenza gratuita in AzureFree DR replica in Azure** | È possibile ospitare [un'istanza passiva gratuita](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure) per il ripristino di emergenza in Azure per l'istanza locale di SQL Server se si dispone di [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3). | 
| **Registrazione del provider di risorse in blocco** | È ora possibile [registrare in blocco le](virtual-machines-windows-sql-bulk-register-with-resource-provider.md) macchine virtuali SQL con il provider di risorse. | 
|**Configurazione ottimizzata dello storage delle prestazioniPerformance Optimized Storage Configuration** | È ora possibile [personalizzare completamente la configurazione di archiviazione](virtual-machines-windows-sql-server-storage-configuration.md#new-vms) durante la creazione di una nuova macchina virtuale di SQL Server.You can now fully customize your storage configuration when creating a new SQL Server VM. |
|**Condivisione file Premium per FCI** | È ora possibile creare un'istanza del cluster di failover usando una condivisione [file Premium](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md) anziché il metodo originale di Spazi di archiviazione [diretta.](virtual-machines-windows-portal-sql-create-failover-cluster.md) 
| **Host dedicato di AzureAzure dedicated host** | È possibile eseguire la macchina virtuale di SQL Server in un [host dedicato di Azure.](virtual-machines-windows-sql-dedicated-host.md) | 
| **Spostare la macchina virtuale SQL in un'area diversaMove SQL VM to different region** | Usare Azure Site Recovery per [eseguire la migrazione della macchina virtuale di SQL Server da un'area a un'altra.](virtual-machines-windows-sql-move-different-region.md) |
|  **Nuove modalità di installazione di SQL IaaS** | È ora possibile installare l'estensione IaaS di SQL Server in [modalità leggera](virtual-machines-windows-sql-server-agent-extension.md) per evitare di riavviare il servizio SQL Server.  |
| **Modifica dell'edizione di SQL ServerSQL Server edition modification** | È ora possibile modificare la [proprietà dell'edizione](virtual-machines-windows-sql-change-edition.md) per la macchina virtuale di SQL Server.You can now change the edition property for your SQL Server VM. |
| **Modifiche al provider di risorse della macchina virtuale SQLChanges to SQL VM resource provider** | È possibile [registrare la macchina virtuale](virtual-machines-windows-sql-register-with-resource-provider.md) di SQL Server con il provider di risorse della macchina virtuale SQL usando le nuove modalità SQL IaaS.You can register your SQL Server VM with the SQL VM resource provider by using the new SQL IaaS modes. Questa funzionalità include immagini di [Windows Server 2008.This](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes) capability includes Windows Server 2008 images.|
| **Portare le immagini con licenza personale usando il vantaggio Azure HybridBring your own-license images using Azure Hybrid Benefit** | Le immagini Bring-your own-license distribuite da Azure Marketplace possono ora cambiare il tipo di licenza in con pagamento in [base al server.](virtual-machines-windows-sql-ahb.md#remarks)| 
| **Nuova gestione delle macchine virtuali di SQL Server nel portale di AzureNew SQL Server VM management in Azure portal** | Ora è disponibile un modo per gestire la macchina virtuale di SQL Server nel portale di Azure.There's now a way to manage your SQL Server VM in the Azure portal. Per altre informazioni, vedere Gestire le macchine virtuali di SQL Server nel portale di Azure.For more information, see [Manage SQL Server VMs in the Azure portal.](virtual-machines-windows-sql-manage-portal.md)  | 
| **Supporto esteso per SQL Server 2008/2008 R2** | [Estendere il supporto](virtual-machines-windows-sql-server-2008-eos-extend-support.md) per SQL Server 2008 e SQL Server 2008 R2 eseguendo la migrazione a una macchina virtuale di Azure.Extend support for SQL Server 2008 and SQL Server 2008 R2 by migrating *as is* to an Azure VM. | 
| **Supportabilità dell'immagine personalizzata** | È ora possibile installare [l'estensione IaaS di SQL Server](virtual-machines-windows-sql-server-agent-extension.md#installation) in immagini ospersonalizzate, che offre la funzionalità limitata delle [licenze flessibili.](virtual-machines-windows-sql-ahb.md) Quando si registra l'immagine personalizzata con il provider di risorse SQL, specificare il tipo di licenza come "AHUB". In caso contrario, la registrazione avrà esito negativo. | 
| **Supportabilità dell'istanza denominata** | È ora possibile usare [l'estensione IaaS di SQL ServerSQL Server](virtual-machines-windows-sql-server-agent-extension.md#installation) con un'istanza denominata, se l'istanza predefinita è stata disinstallata correttamente. | 
| **Miglioramento del portale** | L'esperienza del portale di Azure per la distribuzione di una macchina virtuale di SQL Server è stata rinnovata per migliorare l'usabilità. Per altre informazioni, vedere la breve [guida introduttiva](quickstart-sql-vm-create-portal.md) e una guida più completa per distribuire una macchina virtuale di SQL Server.For more information, see the brief quickstart and more thorough [how-to guide](virtual-machines-windows-portal-sql-server-provision.md) to deploy a SQL Server VM.|
| **Miglioramento del portale** | È ora possibile modificare il modello di licenza per una macchina virtuale di SQL Server da con pagamento in base al costo alla licenza bring-your-own tramite il portale di [Azure](virtual-machines-windows-sql-ahb.md#vms-already-registered-with-the-resource-provider).|
| **Semplificazione della distribuzione del gruppo di disponibilità con l'interfaccia della riga di comando della macchina virtuale di Azure SQL ServerSimplification of availability group deployment with Azure SQL Server VM CLI** | Ora è più semplice che mai distribuire un gruppo di disponibilità in una macchina virtuale di SQL Server in Azure.It's now easier than mai to deploy an availability group to a SQL Server VM in Azure. È possibile usare [l'interfaccia della riga di](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) comando di Azure per creare il cluster di failover di Windows, il servizio di bilanciamento del carico interno e i listener del gruppo di disponibilità tutti dalla riga di comando. Per altre informazioni, vedere Usare l'interfaccia della riga di comando della macchina virtuale di Azure SQL Server per configurare un gruppo di disponibilità AlwaysOn per SQL Server in una macchina virtuale di Azure.For more information, see [Use the Azure SQL Server VM CLI to configure an Always On availability group for SQL Server on an Azure VM.](virtual-machines-windows-sql-availability-group-cli.md) | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018 

 Modifiche | Dettagli |
| --- | --- |
|  **Nuovo provider di risorse per un cluster di SQL ServerNew resource provider for a SQL Server cluster** | Un nuovo provider di risorse (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups) definisce i metadati del cluster di failover di Windows. L'aggiunta di una macchina virtuale di SQL Server a *SqlVirtualMachineGroups* esegue il bootstrap del servizio WSFC (Windows Server Failover Cluster) e aggiunge la macchina virtuale al cluster.  |
| **Configurazione automatica di una distribuzione di un gruppo di disponibilità con i modelli di guida introduttiva di AzureAutomated setup of an availability group deployment with Azure quickstart templates** |È ora possibile creare il cluster di failover di Windows, aggiungervi le macchine virtuali di SQL Server, creare il listener e configurare il servizio di bilanciamento del carico interno con due modelli di guida rapida di Azure.It's now possible to create the Windows failover cluster, join SQL Server VMs to it, create the listener, and configure the internal load balancer with two Azure quickstart templates. Per altre informazioni, vedere Usare i modelli delle guide rapide di Azure per configurare un gruppo di disponibilità AlwaysOn per SQL Server in una macchina virtuale di Azure.For more information, see [Use Azure quickstart templates to configure an Always On availability group for SQL Server on an Azure VM.](virtual-machines-windows-sql-availability-group-quickstart-template.md) | 
| **Registrazione automatica al provider di risorse della macchina virtuale SQLAutomatic registration to the SQL VM resource provider** | Le VM di SQL Server distribuite dopo il mese corrente vengono registrate automaticamente con il nuovo provider di risorse di SQL Server. Le macchine virtuali di SQL Server distribuite prima di questo mese devono ancora essere registrate manualmente. Per altre informazioni, vedere [Registrare una macchina virtuale SQL Server in Azure con il provider](virtual-machines-windows-sql-register-with-resource-provider.md)di risorse della macchina virtuale SQL.|
|**Nuovo provider di risorse per VM di SQL** |  Un nuovo provider di risorse (Microsoft.SqlVirtualMachine) fornisce una migliore gestione delle macchine virtuali di SQL Server.A new resource provider (Microsoft.SqlVirtualMachine) provides better management of your SQL Server VMs. Per altre informazioni sulla registrazione delle macchine virtuali, vedere [Registrare una macchina virtuale di SQL Server in Azure con il provider](virtual-machines-windows-sql-register-with-resource-provider.md)di risorse DELLA macchina virtuale SQL. |
|**Cambiare il modello di licenza** | È ora possibile passare tra i modelli pay-per-usage e bring-your own-license per la macchina virtuale di SQL Server usando l'interfaccia della riga di comando di Azure o PowerShell.You can now switch between the pay-per-usage and bring your own-license models for your SQL Server VM by using the Azure CLI or PowerShell. Per altre informazioni, vedere Come modificare il modello di licenza per una macchina virtuale di SQL Server in Azure.For more information, see How to change the [licensing model for a SQL Server virtual machine in Azure.](virtual-machines-windows-sql-ahb.md) | 
| &nbsp; | &nbsp; |

## <a name="additional-resources"></a>Risorse aggiuntive

**Macchine virtuali Windows**:

* [Panoramica di SQL Server in una macchina virtuale WindowsOverview of SQL Server on a Windows VM](virtual-machines-windows-sql-server-iaas-overview.md)
* [Effettuare il provisioning di una macchina virtuale Windows di SQL Server](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrazione di un database a SQL Server in una macchina virtuale di AzureMigrating a database to SQL Server on an Azure VM](virtual-machines-windows-migrate-sql.md)
* [Disponibilità elevata e ripristino di emergenza per SQL Server in Macchine virtuali di AzureHigh availability and disaster recovery for SQL Server on Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md)
* [Procedure consigliate per le prestazioni per SQL Server in Macchine virtuali di AzurePerformance best practices for SQL Server on Azure Virtual Machines](virtual-machines-windows-sql-performance.md)
* [Modelli di applicazione e strategie di sviluppo per SQL Server in Macchine virtuali di AzureApplication patterns and development strategies for SQL Server on Azure Virtual Machines](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Macchine virtuali Linux**:

* [Panoramica di SQL Server in una macchina virtuale Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Effettuare il provisioning di una macchina virtuale Linux di SQL ServerProvision a SQL Server Linux virtual machine](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Domande frequenti (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Documentazione di SQL Server su LinuxSQL Server on Linux documentation](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
