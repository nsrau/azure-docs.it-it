---
title: Note sulla versione di SQL Server in una macchina virtuale Azure | Microsoft Docs
description: Informazioni sulle nuove funzionalità e i miglioramenti di SQL Server in una macchina virtuale Azure
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
manager: craigg
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 2/13/2019
ms.openlocfilehash: d5c003b2588813f8e8a1dfc2923b9d4c8d8c6cc8
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56820434"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>Note sulla versione di SQL Server in una macchina virtuale Azure

Azure consente di distribuire una macchina virtuale con un'immagine di SQL Server predefinita. Questo articolo presenta una sintesi delle nuove funzionalità e dei miglioramenti introdotti nelle versioni recenti di [SQL Server in macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). L'articolo elenca anche gli aggiornamenti del contenuto rilevanti non direttamente correlati alla versione ma pubblicati nello stesso intervallo di tempo. Per i miglioramenti apportati ad altri servizi di Azure, vedere [Aggiornamenti dei servizi](https://azure.microsoft.com/updates)


## <a name="february-2019"></a>Febbraio 2019

### <a name="service-improvements"></a>Miglioramenti al servizio

| Miglioramenti al servizio | Dettagli |
| --- | --- |
| **Miglioramento del portale** | È ora possibile modificare il modello di licenza per una VM di SQL Server dal pagamento a consumo a bring-your-own-license utilizzando il [portale di Azure](virtual-machines-windows-sql-ahb.md#with-the-azure-portal-1).|
|**Semplificazione di distribuzione del gruppo di disponibilità con macchine Virtuali di SQL Azure CLI** | È ora più facile che mai, per distribuire un gruppo di disponibilità in una VM di SQL Server in Azure. Comando della macchina virtuale di SQL Azure consente di creare il listener del gruppo di disponibilità, servizio ILB e il cluster WSFC tutto dalla riga di comando e in fase di record! Per altre informazioni, vedere [usare CLI della macchina virtuale SQL di Azure per configurare gruppi di disponibilità AlwaysOn per SQL Server in una VM di Azure](virtual-machines-windows-sql-availability-group-cli.md). | 
| &nbsp; | &nbsp; |

### <a name="documentation-improvements"></a>Miglioramenti alla documentazione

| Miglioramenti alla documentazione | Dettagli |
| --- | --- |
|Nessuno | |
| | |

## <a name="december-2018"></a>Dicembre 2018

| Miglioramenti al servizio | Dettagli |
| --- | --- |
| **Nuovo provider di risorse del gruppo della rete di cluster SQL** | È disponibile un nuovo provider di risorse (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups) che definisce i metadati del Cluster di failover di Windows. La partecipazione della VM di SQL Server a *SqlVirtualMachineGroups* esegue il bootstrap del servizio Cluster di failover di Windows e unisce le VM di SQL Server al cluster.  |
|**Automatizzare la configurazione di una distribuzione gruppo di disponibilità con modelli di avvio rapido di Azure** |È ora possibile creare il Cluster di failover di Windows, aggiungere VM di SQL Server a esso, creare il listener e configurare il bilanciamento del carico interno con due modelli di avvio rapido di Azure. Per altre informazioni, vedere [modello di avvio rapido di Azure usare per configurare gruppi di disponibilità AlwaysOn per SQL Server in una VM di Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md). | 
| **Registrazione del provider di risorse automatico della macchina virtuale SQL** | Le VM di SQL Server distribuite dopo il mese corrente vengono registrate automaticamente con il nuovo provider di risorse di SQL Server. Le VM di SQL Server distribuite prima del mese corrente devono sempre essere registrate manualmente. Per altre informazioni, vedere [registrare VM di SQL esistente con il provider di risorse di VM di SQL](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider).|
| &nbsp; | &nbsp; |


## <a name="november-2018"></a>Novembre 2018

| Miglioramenti al servizio | Dettagli |
| --- | --- |
| **Nuovo provider di risorse per VM di SQL** |  È disponibile un nuovo provider di risorse per VM di SQL Server (Microsoft.SqlVirtualMachine) che consente una migliore gestione della VM di SQL Server. Per altre informazioni sulla registrazione di una macchina virtuale, vedere [Registrare una macchina virtuale SQL esistente con un nuovo provider di risorse](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider). |
|**Cambiare il modello di licenza** |È ora possibile passare dal modello di licenza a pagamento in base all'utilizzo al modello di licenza Bring-Your-Own per la propria macchina virtuale SQL con l'interfaccia della riga di comando di Azure o Powershell. Per altre informazioni, vedere [Come cambiare il modello di licenza per una macchina virtuale SQL](virtual-machines-windows-sql-ahb.md). | 
| &nbsp; | &nbsp; |


## <a name="additional-resources"></a>Risorse aggiuntive

**Macchine virtuali Windows**:

* [Panoramica di SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-overview.md).
* [Effettuare il provisioning di una macchina virtuale Windows di SQL Server](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrazione di un database a SQL Server su una macchina virtuale di Azure](virtual-machines-windows-migrate-sql.md)
* [Disponibilità elevata e ripristino di emergenza di SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-high-availability-dr.md)
* [Performance best practices for SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-performance.md) (Procedure consigliate sulle prestazioni per SQL Server nelle macchine virtuali di Azure)
* [Modelli di applicazione e strategie di sviluppo per SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Macchine virtuali Linux**:

* [Panoramica di SQL Server in una macchina virtuale Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Effettuare il provisioning di una macchina virtuale SQL Server](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Domande frequenti (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Documentazione di SQL Server in Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)