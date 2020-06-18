---
title: Panoramica sui gruppi di disponibilità
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
ms.openlocfilehash: 62dce0204f77ab65473fc1735015e41f483dddb1
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84036952"
---
# <a name="introducing-sql-server-availability-groups-on-azure-virtual-machines"></a>Panoramica sui gruppi di disponibilità di SQL Server in macchine virtuali di Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Questo articolo offre una panoramica sui gruppi di disponibilità di SQL Server in macchine virtuali di Azure. 

I gruppi di disponibilità AlwaysOn in macchine virtuali di Azure sono simili ai gruppi di disponibilità AlwaysOn locali. Per altre informazioni, vedere [Gruppi di disponibilità AlwaysOn (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx). 

Il diagramma seguente illustra le parti di un gruppo di disponibilità di SQL Server completo in macchine virtuali di Azure.

![Gruppo di disponibilità](./media/availability-group-overview/00-EndstateSampleNoELB.png)

La differenza principale per un gruppo di disponibilità in macchine virtuali Azure è che per queste ultime è necessario un [servizio di bilanciamento del carico](../../../load-balancer/load-balancer-overview.md). Il servizio di bilanciamento del carico contiene gli indirizzi IP per il listener del gruppo di disponibilità. Se sono disponibili più gruppi di disponibilità, è necessario un listener per ogni gruppo. Un servizio di bilanciamento del carico può supportare più listener.

Inoltre, in un cluster di failover guest di macchine virtuali IaaS di Azure è consigliabile usare una sola scheda di rete per ogni server (nodo del cluster) e una sola subnet. La ridondanza fisica della rete di Azure rende superfluo l'uso di altre schede di rete e subnet in un cluster guest di macchine virtuali IaaS di Azure. Anche se il report di convalida del cluster avviserà che i nodi sono raggiungibili solo in una rete, tale avviso potrà essere tranquillamente ignorato per i cluster di failover guest delle macchine virtuali IaaS di Azure. 

Per aumentare la ridondanza e la disponibilità elevata, le VM di SQL Server devono trovarsi nello stesso [set di disponibilità](availability-group-manually-configure-prerequisites-tutorial.md#create-availability-sets) o in diverse [zone di disponibilità](/azure/availability-zones/az-overview). 

|  | Versione di Windows Server | Versione di SQL Server | Edizione di SQL Server | Configurazione del quorum WSFC | Ripristino di emergenza con più aree | Supporto di più subnet | Supporto di un dominio dell'applicazione esistente | Ripristino di emergenza con più aree della stessa regione | Supporto di dist-AG senza dominio AD | Supporto di dist-AG senza cluster |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| [Interfaccia della riga di comando delle VM SQL](availability-group-az-cli-configure.md) | 2016 | 2017 </br>2016   | Enterprise | Cloud di controllo | No | Sì | Sì | Sì | No | No |
| [Modelli di avvio rapido](availability-group-quickstart-template-configure.md) | 2016 | 2017</br>2016  | Enterprise | Cloud di controllo | No | Sì | Sì | Sì | No | No |
| [Modello del portale](availability-group-azure-marketplace-template-configure.md) | 2016 </br>2012 R2 | 2016</br>2014 | Enterprise | Condivisione file | No | No | No | No | No | No |
| [Manuale](availability-group-manually-configure-prerequisites-tutorial.md) | Tutti | Tutti | Tutti | Tutti | Sì | Sì | Sì | Sì | Sì | Sì |
| &nbsp; | &nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |

Per compilare un gruppo di disponibilità di SQL Server in macchine virtuali di Azure, vedere le esercitazioni indicate di seguito.

## <a name="manually-with-azure-cli"></a>Manualmente con l'interfaccia della riga di comando di Azure
L'uso dell'interfaccia della riga di comando di Azure per configurare e distribuire un gruppo di disponibilità è l'opzione consigliata, poiché rappresenta il modo migliore in termini di semplicità e velocità di distribuzione. Con l'interfaccia della riga di comando di Azure, la creazione del cluster di failover di Windows, l'aggiunta di VM di SQL Server al cluster, nonché la creazione del listener e del bilanciamento del carico interno possono essere realizzate in meno di 30 minuti. Questa opzione richiede comunque una creazione manuale del gruppo di disponibilità, ma consente di automatizzare tutti gli altri passaggi di configurazione necessari. 

Per altre informazioni, vedere [Usare l'interfaccia della riga di comando della VM di SQL Server in Azure per configurare un gruppo di disponibilità Always On per SQL Server in una macchina virtuale di Azure](availability-group-az-cli-configure.md). 

## <a name="automatically-with-azure-quickstart-templates"></a>Automaticamente con i modelli di avvio rapido di Azure
I modelli di avvio rapido di Azure usano il provider di risorse delle VM di SQL per distribuire il cluster di failover di Windows, aggiungervi VM di SQL Server, creare il listener e configurare il bilanciamento del carico interno. Questa opzione richiede comunque una creazione manuale del gruppo di disponibilità e del bilanciamento del carico interno (ILB), ma consente di automatizzare e semplificare tutti gli altri passaggi di configurazione necessari (inclusa la configurazione dell'ILB). 

Per altre informazioni, vedere [Usare modelli di avvio rapido di Azure per configurare un gruppo di disponibilità Always On per SQL Server in una macchina virtuale di Azure](availability-group-quickstart-template-configure.md).


## <a name="automatically-with-an-azure-portal-template"></a>Automaticamente con un modello del portale di Azure

[Configurare automaticamente il gruppo di disponibilità AlwaysOn in macchine virtuali di Azure con Resource Manager](availability-group-azure-marketplace-template-configure.md)


## <a name="manually-in-azure-portal"></a>Manualmente nel portale di Azure

È anche possibile creare le macchine virtuali manualmente senza il modello. A tale scopo, è necessario completare prima i prerequisiti e quindi creare il gruppo di disponibilità. Vedere gli argomenti seguenti: 

- [Completare i prerequisiti per la creazione di gruppi di disponibilità AlwaysOn in macchine virtuali di Azure](availability-group-manually-configure-prerequisites-tutorial.md)

- [Creare un gruppo di disponibilità AlwaysOn per migliorare la disponibilità e il ripristino di emergenza](availability-group-manually-configure-tutorial.md)

## <a name="next-steps"></a>Passaggi successivi

[Configurare un gruppo di disponibilità SQL Server AlwaysOn nelle macchine virtuali di Azure](availability-group-manually-configure-multiple-regions.md)
