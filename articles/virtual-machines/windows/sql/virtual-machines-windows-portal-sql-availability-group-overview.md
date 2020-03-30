---
title: Panoramica dei gruppi di disponibilitàOverview of availability groups
description: Questo articolo offre una panoramica sui gruppi di disponibilità di SQL Server in macchine virtuali di Azure.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/13/2017
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: 8119990ab4ab4a918e325976092100086a547aa4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74037490"
---
# <a name="introducing-sql-server-availability-groups-on-azure-virtual-machines"></a>Introduzione ai gruppi di disponibilità di SQL Server nelle macchine virtuali di AzureIntroducing SQL Server availability groups on Azure virtual machines

Questo articolo offre una panoramica sui gruppi di disponibilità di SQL Server in macchine virtuali di Azure. 

I gruppi di disponibilità AlwaysOn in macchine virtuali di Azure sono simili ai gruppi di disponibilità AlwaysOn locali. Per altre informazioni, vedere [Gruppi di disponibilità AlwaysOn (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx). 

Il diagramma seguente illustra le parti di un gruppo di disponibilità di SQL Server completo in macchine virtuali di Azure.

![Gruppo di disponibilità](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

La differenza principale per un gruppo di disponibilità in macchine virtuali Azure è che per queste ultime è necessario un [servizio di bilanciamento del carico](../../../load-balancer/load-balancer-overview.md). Il servizio di bilanciamento del carico contiene gli indirizzi IP per il listener del gruppo di disponibilità. Se sono disponibili più gruppi di disponibilità, è necessario un listener per ogni gruppo. Un servizio di bilanciamento del carico può supportare più listener.

Inoltre, in un cluster di failover guest VM IaaS di Azure, è consigliabile una singola scheda di interfaccia di rete per ogni server (nodo del cluster) e una singola subnet. La ridondanza fisica della rete di Azure rende superfluo l'uso di altre schede di rete e subnet in un cluster guest di macchine virtuali IaaS di Azure. Anche se il report di convalida del cluster avviserà che i nodi sono raggiungibili solo in una rete, tale avviso potrà essere tranquillamente ignorato per i cluster di failover guest delle macchine virtuali IaaS di Azure. 

Per aumentare la ridondanza e la disponibilità elevata, le macchine virtuali di SQL Server devono trovarsi nello stesso set di [disponibilità](virtual-machines-windows-portal-sql-availability-group-prereq.md#create-availability-sets)o in zone di [disponibilità](/azure/availability-zones/az-overview)diverse. 

|  | Versione di Windows Server | Versione di SQL Server | Edizione di SQL Server | Configurazione quorum WSFC | DR con più aree geografiche | Supporto multi-subnet | Supporto per un AD esistente | DR con la stessa regione multizona | Supporto di Dist-AG senza dominio Active Directory | Supporto diDist-AG senza cluster |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| [SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) | 2016 | 2017 </br>2016   | Ent | Cloud di controllo | No | Sì | Sì | Sì | No | No |
| [Modelli di avvio rapido](virtual-machines-windows-sql-availability-group-quickstart-template.md) | 2016 | 2017</br>2016  | Ent | Cloud di controllo | No | Sì | Sì | Sì | No | No |
| [Modello portale](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) | 2016 </br>2012 R2 | 2016</br>2014 | Ent | Condivisione file | No | No | No | No | No | No |
| [Manuale](virtual-machines-windows-portal-sql-availability-group-prereq.md) | Tutti | Tutti | Tutti | Tutti | Sì | Sì | Sì | Sì | Sì | Sì |
| &nbsp; | &nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |

Per compilare un gruppo di disponibilità di SQL Server in macchine virtuali di Azure, vedere le esercitazioni indicate di seguito.

## <a name="manually-with-azure-cli"></a>Manualmente con l'interfaccia della riga di comando di AzureManually with Azure
L'uso dell'interfaccia della riga di comando di Azure per configurare e distribuire un gruppo di disponibilità è l'opzione consigliata, in quanto è il migliore in termini di semplicità e velocità di distribuzione. Con l'interfaccia della riga di comando di Azure, la creazione del cluster di failover di Windows, l'aggiunta di macchine virtuali di SQL Server al cluster, nonché la creazione del listener e del servizio di bilanciamento del carico interno, è possibile ottenere in meno di 30 minuti. Questa opzione richiede comunque una creazione manuale del gruppo di disponibilità, ma automatizza tutti gli altri passaggi di configurazione necessari. 

Per altre informazioni, vedere Usare l'interfaccia della riga di comando della macchina virtuale SQL di Azure per configurare il gruppo di disponibilità AlwaysOn per SQL Server in una macchina virtuale di Azure.For more information, see [Use Azure SQL VM CLI to configure Always On availability group for SQL Server on an Azure VM.](virtual-machines-windows-sql-availability-group-cli.md) 

## <a name="automatically-with-azure-quickstart-templates"></a>Automaticamente con i modelli di Guida introduttiva di AzureAutomatically with Azure Quickstart Templates
I modelli di avvio rapido di Azure usano il provider di risorse della macchina virtuale SQL per distribuire il cluster di failover di Windows, aggiungervi le macchine virtuali di SQL Server, creare il listener e configurare il servizio di bilanciamento del carico interno. Questa opzione richiede comunque una creazione manuale del gruppo di disponibilità e del servizio di bilanciamento del carico interno (ILB), ma automatizza e semplifica tutti gli altri passaggi di configurazione necessari (inclusa la configurazione del bilanciamento del carico interno). 

Per altre informazioni, vedere Usare il modello Di Avvio veloce di Azure per configurare il gruppo di disponibilità AlwaysOn per SQL Server in una macchina virtuale di Azure.For more information, see [Use Azure Quickstart Template to configure Always On availability group for SQL Server on an Azure VM.](virtual-machines-windows-sql-availability-group-quickstart-template.md)


## <a name="automatically-with-an-azure-portal-template"></a>Automaticamente con un modello di portale di AzureAutomatically with an Azure portal template

[Configurare automaticamente il gruppo di disponibilità AlwaysOn in macchine virtuali di Azure con Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)


## <a name="manually-in-azure-portal"></a>Manualmente nel portale di AzureManually in Azure portal

È anche possibile creare le macchine virtuali manualmente senza il modello. A tale scopo, è necessario completare prima i prerequisiti e quindi creare il gruppo di disponibilità. Vedere gli argomenti seguenti: 

- [Completare i prerequisiti per la creazione di gruppi di disponibilità AlwaysOn in macchine virtuali di Azure](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Creare un gruppo di disponibilità AlwaysOn per migliorare la disponibilità e il ripristino di emergenza](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>Passaggi successivi

[Configurare un gruppo di disponibilità SQL Server AlwaysOn in Macchine virtuali di Azure](virtual-machines-windows-portal-sql-availability-group-dr.md)
