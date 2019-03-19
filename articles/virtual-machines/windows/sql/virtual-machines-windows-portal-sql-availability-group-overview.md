---
title: 'Gruppi di disponibilità di SQL Server: Macchine virtuali di Azure: panoramica | Documentazione Microsoft'
description: Questo articolo offre una panoramica sui gruppi di disponibilità di SQL Server in macchine virtuali di Azure.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/13/2017
ms.author: mikeray
ms.openlocfilehash: b9977965dc076ec36aa90680a1732b6640b1e41a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57861843"
---
# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>Panoramica sui gruppi di disponibilità AlwaysOn di SQL Server in macchine virtuali di Azure #

Questo articolo offre una panoramica sui gruppi di disponibilità di SQL Server in macchine virtuali di Azure. 

I gruppi di disponibilità AlwaysOn in macchine virtuali di Azure sono simili ai gruppi di disponibilità AlwaysOn locali. Per altre informazioni, vedere [Gruppi di disponibilità AlwaysOn (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx). 

Il diagramma seguente illustra le parti di un gruppo di disponibilità di SQL Server completo in macchine virtuali di Azure.

![Gruppo di disponibilità](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

La differenza principale per un gruppo di disponibilità in macchine virtuali Azure è che per queste ultime è necessario un [servizio di bilanciamento del carico](../../../load-balancer/load-balancer-overview.md). Il servizio di bilanciamento del carico contiene gli indirizzi IP per il listener del gruppo di disponibilità. Se sono disponibili più gruppi di disponibilità, è necessario un listener per ogni gruppo. Un servizio di bilanciamento del carico può supportare più listener.

Inoltre, in un cluster di failover guest VM IaaS di Azure, è consigliabile una singola scheda di rete per ogni server (nodo del cluster) e una sola subnet. La ridondanza fisica della rete di Azure rende superfluo l'uso di altre schede di rete e subnet in un cluster guest di macchine virtuali IaaS di Azure. Anche se il report di convalida del cluster avviserà che i nodi sono raggiungibili solo in una rete, tale avviso potrà essere tranquillamente ignorato per i cluster di failover guest delle macchine virtuali IaaS di Azure. 

|  | Versione di Windows Server | Versione di SQL Server | Edizione di SQL Server | Configurazione del Quorum WSFC | Ripristino di emergenza con più aree | Supporto di più subnet | Supporto per un'istanza di AD esistente | Ripristino di emergenza con più zone stessa area | Supporto Dist-AG senza dominio AD | Supporto Dist-AG con alcun cluster |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| [INTERFACCIA DELLA RIGA DI COMANDO DI SQL MACCHINA VIRTUALE](virtual-machines-windows-sql-availability-group-cli.md) | 2016 | 2017 </br>2016   | Ent | Cloud di controllo | No  | Sì | Sì | Sì | No  | No  |
| [Modelli di avvio rapido](virtual-machines-windows-sql-availability-group-quickstart-template.md) | 2016 | 2017</br>2016  | Ent | Cloud di controllo | No  | Sì | Sì | Sì | No  | No  |
| [Modello del portale](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) | 2016 </br>2012 R2 | 2016</br>2014 | Ent | Condivisione file | No  | No  | No  | No  | No  | No  |
| [Manuale](virtual-machines-windows-portal-sql-availability-group-prereq.md) | Tutti | Tutti | Tutti | Tutti | Sì | Sì | Sì | Sì | Sì | Sì |
| &nbsp; | &nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |

Per compilare un gruppo di disponibilità di SQL Server in macchine virtuali di Azure, vedere le esercitazioni indicate di seguito.

## <a name="manually-with-azure-cli"></a>Manualmente con il comando di Azure
Uso della riga di comando di Azure per configurare e distribuire un gruppo di disponibilità è l'opzione consigliata, perché è la migliore in termini di semplicità e velocità di distribuzione. Con CLI di Azure, la creazione del Cluster di Failover Windows, aggiunta di macchine virtuali SQL Server al cluster, nonché la creazione del listener e bilanciamento del carico interno possono essere ottenute in meno di 30 minuti. Questa opzione ancora richiede la creazione di un manuale del gruppo di disponibilità, ma consente di automatizzare tutti gli altri passaggi di configurazione necessarie. 

Per altre informazioni, vedere [usare CLI della macchina virtuale SQL di Azure per configurare gruppi di disponibilità AlwaysOn per SQL Server in una VM di Azure](virtual-machines-windows-sql-availability-group-cli.md). 

## <a name="automatically-with-azure-quickstart-templates"></a>Automaticamente con i modelli di avvio rapido di Azure
I modelli di avvio rapido di Azure usano il provider di risorse di VM di SQL per distribuire il Cluster di Failover di Windows, aggiungere macchine virtuali SQL Server ad esso, creare il listener e configurare il bilanciamento del carico interno. Questa opzione ancora richiede la creazione di un manuale del gruppo di disponibilità e il servizio di bilanciamento del carico interno (ILB) ma automatizza e semplifica tutti gli altri passaggi di configurazione necessari (inclusa la configurazione del servizio ILB). 

Per altre informazioni, vedere [modello di avvio rapido di Azure usare per configurare gruppi di disponibilità AlwaysOn per SQL Server in una VM di Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md).


## <a name="automatically-with-an-azure-portal-template"></a>Automaticamente con un modello del portale di Azure

[Configurare automaticamente il gruppo di disponibilità AlwaysOn in macchine virtuali di Azure con Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)


## <a name="manually-in-azure-portal"></a>Manualmente nel portale di Azure

È anche possibile creare le macchine virtuali manualmente senza il modello. A tale scopo, è necessario completare prima i prerequisiti e quindi creare il gruppo di disponibilità. Vedere gli argomenti seguenti: 

- [Completare i prerequisiti per la creazione di gruppi di disponibilità AlwaysOn in macchine virtuali di Azure](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Creare un gruppo di disponibilità AlwaysOn per migliorare la disponibilità e il ripristino di emergenza](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>Passaggi successivi

[Configurare un gruppo di disponibilità SQL Server AlwaysOn nelle macchine virtuali di Azure](virtual-machines-windows-portal-sql-availability-group-dr.md)
