---
title: Modifiche alla documentazione per SQL Server in macchine virtuali di Azure | Microsoft Docs
description: Informazioni sulle nuove funzionalità e i miglioramenti per SQL Server in una macchina virtuale di Azure
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
ms.date: 08/01/2019
ms.openlocfilehash: f680eae7f8c4cc2b57e3238a14b793f9b4298a79
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828602"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Modifiche alla documentazione per SQL Server in macchine virtuali di Azure

Azure consente di distribuire una macchina virtuale (VM) con un'immagine di SQL Server incorporata. Questo articolo riepiloga le modifiche alla documentazione associate a nuove funzionalità e miglioramenti nelle versioni recenti di [SQL Server in macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). 


## <a name="october-2019"></a>2019 ottobre

| Modifiche | Dettagli |
| --- | --- |
| **Configurazione dell'archiviazione ottimizzata per le prestazioni** | È ora possibile [personalizzare completamente la configurazione dell'archiviazione](virtual-machines-windows-sql-server-storage-configuration.md#new-vms) quando si crea una nuova macchina virtuale SQL Server. |
| &nbsp; | &nbsp; |

## <a name="august-2019"></a>Agosto 2019

| Modifiche | Dettagli |
| --- | --- |
| **Host dedicato di Azure** | È possibile eseguire la macchina virtuale SQL Server in un [host dedicato di Azure](virtual-machines-windows-sql-dedicated-host.md). |
| &nbsp; | &nbsp; |


## <a name="july-2019"></a>2019 luglio


| Modifiche | Dettagli |
| --- | --- |
| **Spostare una macchina virtuale SQL in un'area diversa** | Usare Azure Site Recovery per [eseguire la migrazione della macchina virtuale SQL Server da un'area a un'altra](virtual-machines-windows-sql-move-different-region.md). |
| &nbsp; | &nbsp; |

## <a name="june-2019"></a>Giugno 2019


| Modifiche | Dettagli |
| --- | --- |
| **Nuove modalità di installazione di SQL IaaS** | È ora possibile installare l'estensione IaaS SQL Server in [modalità Lightweight](virtual-machines-windows-sql-server-agent-extension.md) per evitare di riavviare il servizio SQL Server.  |
| **Modifica di SQL Server Edition** | È ora possibile modificare la [Proprietà Edition](virtual-machines-windows-sql-change-edition.md) per la macchina virtuale SQL Server. |
| **Modifiche al provider di risorse VM SQL** | È possibile [registrare la macchina virtuale SQL Server con il provider di risorse VM SQL](virtual-machines-windows-sql-register-with-resource-provider.md) usando le nuove modalità IaaS di SQL. Questa funzionalità include le [Immagini di Windows 2008](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008-or-2008-r2-on-windows-server-2008-vms).|
| **Immagini Bring your own License con Vantaggio Azure Hybrid** | Le immagini Bring your own License distribuite da Azure Marketplace possono ora impostare il [tipo di licenza su con pagamento in base al](virtual-machines-windows-sql-ahb.md#remarks)consumo.| 
| &nbsp; | &nbsp; |


## <a name="may-2019"></a>Maggio 2019

| Modifiche | Dettagli |
| --- | --- |
| **Nuova gestione delle macchine virtuali SQL Server in portale di Azure** | È ora possibile gestire la macchina virtuale SQL Server nel portale di Azure. Per altre informazioni, vedere [gestire macchine virtuali SQL Server nel portale di Azure](virtual-machines-windows-sql-manage-portal.md).  | 
| &nbsp; | &nbsp; |



## <a name="april-2019"></a>Aprile 2019

| Modifiche | Dettagli |
| --- | --- |
| **Supporto esteso per SQL Server 2008/2008 R2** | [Estendere il supporto](virtual-machines-windows-sql-server-2008-eos-extend-support.md) per SQL Server 2008 e SQL Server 2008 R2 eseguendo *la migrazione così come avviene* in una macchina virtuale di Azure. | 
| &nbsp; | &nbsp; |


## <a name="march-2019"></a>Marzo 2019

| Modifiche | Dettagli |
| --- | --- |
| **Supporto per immagini personalizzate** | È ora possibile installare l' [estensione IaaS SQL Server](virtual-machines-windows-sql-server-agent-extension.md#installation) per le immagini del sistema operativo e di SQL personalizzate, che offre la funzionalità limitata di [licenze flessibili](virtual-machines-windows-sql-ahb.md). Quando si esegue la registrazione dell'immagine personalizzata con il provider di risorse SQL, specificare il tipo di licenza "vantaggio Ahu". In caso contrario, la registrazione avrà esito negativo. | 
| **Supporto per istanze denominate** | È ora possibile usare l' [estensione SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md#installation) con un'istanza denominata, se l'istanza predefinita è stata disinstallata correttamente. | 
| **Miglioramento del portale** | L'esperienza portale di Azure per la distribuzione di una macchina virtuale SQL Server è stata rinnovata per migliorare l'usabilità. Per altre informazioni, vedere le brevi [guide introduttive](quickstart-sql-vm-create-portal.md) e [procedure](virtual-machines-windows-portal-sql-server-provision.md) dettagliate per la distribuzione di una macchina virtuale SQL Server.|
| &nbsp; | &nbsp; |


## <a name="february-2019"></a>Febbraio 2019

| Modifiche | Dettagli |
| --- | --- |
| **Miglioramento del portale** | È ora possibile modificare il modello di licenza per una macchina virtuale SQL Server dalla versione con pagamento in base al consumo a bring your own License usando il [portale di Azure](virtual-machines-windows-sql-ahb.md#change-the-license-for-vms-already-registered-with-the-resource-provider).|
|**Semplificazione della distribuzione del gruppo di disponibilità con Azure SQL Server CLI della macchina virtuale** | È ora più facile che mai distribuire un gruppo di disponibilità in una macchina virtuale SQL Server in Azure. È possibile usare l' [interfaccia](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) della riga di comando di Azure per creare il cluster di failover Windows, il servizio di bilanciamento del carico interno e i listener del gruppo di disponibilità dalla riga di comando. Per altre informazioni, vedere [usare l'interfaccia della riga di comando di azure SQL Server VM per configurare un gruppo di disponibilità always on per SQL Server in una macchina virtuale di Azure](virtual-machines-windows-sql-availability-group-cli.md). | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018


### <a name="december-2018"></a>Dicembre 2018

| Modifiche | Dettagli |
| --- | --- |
| **Nuovo provider di risorse per un cluster SQL Server** | Un nuovo provider di risorse (Microsoft. SqlVirtualMachine/SqlVirtualMachineGroups) definisce i metadati del cluster di failover di Windows. L'aggiunta di una macchina virtuale SQL Server a *SqlVirtualMachineGroups* avvia il servizio WSFC (Windows Server failover cluster) e aggiunge la macchina virtuale al cluster.  |
|**Installazione automatica di una distribuzione del gruppo di disponibilità con i modelli di avvio rapido di Azure** |È ora possibile creare il cluster di failover di Windows, aggiungervi SQL Server macchine virtuali, creare il listener e configurare il servizio di bilanciamento del carico interno con due modelli di avvio rapido di Azure. Per altre informazioni, vedere [usare i modelli di avvio rapido di Azure per configurare un gruppo di disponibilità always on per SQL Server in una macchina virtuale di Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md). | 
| **Registrazione automatica al provider di risorse VM SQL** | Le VM di SQL Server distribuite dopo il mese corrente vengono registrate automaticamente con il nuovo provider di risorse di SQL Server. SQL Server macchine virtuali distribuite prima di questo mese devono ancora essere registrate manualmente. Per altre informazioni, vedere [registrare una macchina virtuale SQL Server in Azure con il provider di risorse della macchina virtuale SQL](virtual-machines-windows-sql-register-with-resource-provider.md).|
| &nbsp; | &nbsp; |


### <a name="november-2018"></a>Novembre 2018

| Modifiche | Dettagli |
| --- | --- |
| **Nuovo provider di risorse per VM di SQL** |  Un nuovo provider di risorse (Microsoft. SqlVirtualMachine) offre una gestione migliore delle macchine virtuali SQL Server. Per altre informazioni sulla registrazione delle macchine virtuali, vedere [registrare una macchina virtuale SQL Server in Azure con il provider di risorse VM SQL](virtual-machines-windows-sql-register-with-resource-provider.md). |
|**Cambiare il modello di licenza** | È ora possibile passare tra i modelli con pagamento in base al consumo e Bring your own License per la macchina virtuale SQL Server usando l'interfaccia della riga di comando di Azure o PowerShell. Per ulteriori informazioni, vedere [come modificare il modello di licenza per una macchina virtuale SQL Server in Azure](virtual-machines-windows-sql-ahb.md). | 
| &nbsp; | &nbsp; |


## <a name="additional-resources"></a>Risorse aggiuntive

**Macchine virtuali Windows**:

* [Panoramica di SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Effettuare il provisioning di una macchina virtuale Windows di SQL Server](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrazione di un database a SQL Server in una macchina virtuale di Azure](virtual-machines-windows-migrate-sql.md)
* [Disponibilità elevata e ripristino di emergenza per SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-high-availability-dr.md)
* [Procedure consigliate per le prestazioni per SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-performance.md)
* [Modelli di applicazione e strategie di sviluppo per SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Macchine virtuali Linux**:

* [Panoramica di SQL Server in una macchina virtuale Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Effettuare il provisioning di una macchina virtuale SQL Server Linux](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Domande frequenti (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Documentazione di SQL Server in Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
