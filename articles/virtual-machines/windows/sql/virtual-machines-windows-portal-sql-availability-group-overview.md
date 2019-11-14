---
title: Panoramica dei gruppi di disponibilità
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
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037490"
---
# <a name="introducing-sql-server-availability-groups-on-azure-virtual-machines"></a>Introduzione ai gruppi di disponibilità SQL Server in macchine virtuali di Azure

Questo articolo offre una panoramica sui gruppi di disponibilità di SQL Server in macchine virtuali di Azure. 

I gruppi di disponibilità AlwaysOn in macchine virtuali di Azure sono simili ai gruppi di disponibilità AlwaysOn locali. Per altre informazioni, vedere [Gruppi di disponibilità AlwaysOn (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx). 

Il diagramma seguente illustra le parti di un gruppo di disponibilità di SQL Server completo in macchine virtuali di Azure.

![Gruppo di disponibilità](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

La differenza principale per un gruppo di disponibilità in macchine virtuali Azure è che per queste ultime è necessario un [servizio di bilanciamento del carico](../../../load-balancer/load-balancer-overview.md). Il servizio di bilanciamento del carico contiene gli indirizzi IP per il listener del gruppo di disponibilità. Se sono disponibili più gruppi di disponibilità, è necessario un listener per ogni gruppo. Un servizio di bilanciamento del carico può supportare più listener.

Inoltre, in un cluster di failover guest VM IaaS di Azure è consigliabile usare una singola scheda di interfaccia di rete per server (nodo cluster) e una singola subnet. La ridondanza fisica della rete di Azure rende superfluo l'uso di altre schede di rete e subnet in un cluster guest di macchine virtuali IaaS di Azure. Anche se il report di convalida del cluster avviserà che i nodi sono raggiungibili solo in una rete, tale avviso potrà essere tranquillamente ignorato per i cluster di failover guest delle macchine virtuali IaaS di Azure. 

Per aumentare la ridondanza e la disponibilità elevata, le macchine virtuali SQL Server devono trovarsi nello stesso [set di disponibilità](virtual-machines-windows-portal-sql-availability-group-prereq.md#create-availability-sets)o in diverse [zone di disponibilità](/azure/availability-zones/az-overview). 

|  | Versione di Windows Server | Versione SQL Server | Edizione di SQL Server | Configurazione quorum WSFC | RIPRISTINO di emergenza con più aree | Supporto di più subnet | Supporto per un annuncio esistente | RIPRISTINO di emergenza con una stessa area geografica | Supporto di dist-AG senza dominio AD | Supporto di dist-AG senza cluster |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| [INTERFACCIA DELLA RIGA DI COMANDO DI SQL VM](virtual-machines-windows-sql-availability-group-cli.md) | 2016 | 2017 </br>2016   | ENT | Cloud di controllo | No | Sì | Sì | Sì | No | No |
| [Modelli di avvio rapido](virtual-machines-windows-sql-availability-group-quickstart-template.md) | 2016 | 2017</br>2016  | ENT | Cloud di controllo | No | Sì | Sì | Sì | No | No |
| [Modello di portale](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) | 2016 </br>2012 R2 | 2016</br>2014 | ENT | Condivisione file | No | No | No | No | No | No |
| [Manuale](virtual-machines-windows-portal-sql-availability-group-prereq.md) | Tutti | Tutti | Tutti | Tutti | Sì | Sì | Sì | Sì | Sì | Sì |
| &nbsp; | &nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |

Per compilare un gruppo di disponibilità di SQL Server in macchine virtuali di Azure, vedere le esercitazioni indicate di seguito.

## <a name="manually-with-azure-cli"></a>Manualmente con interfaccia della riga di comando di Azure
L'uso dell'interfaccia della riga di comando di Azure per configurare e distribuire un gruppo di disponibilità è l'opzione consigliata, in quanto rappresenta il modo migliore in termini di semplicità e velocità di distribuzione. Con l'interfaccia della riga di comando di Azure, la creazione del cluster di failover di Windows, l'aggiunta di SQL Server macchine virtuali al cluster, nonché la creazione del listener e della Load Balancer interna possono essere realizzate in meno di 30 minuti. Questa opzione richiede comunque una creazione manuale del gruppo di disponibilità, ma consente di automatizzare tutti gli altri passaggi di configurazione necessari. 

Per altre informazioni, vedere [usare l'interfaccia della riga di comando di Azure SQL VM per configurare always on gruppo di disponibilità per SQL Server in una macchina virtuale di Azure](virtual-machines-windows-sql-availability-group-cli.md). 

## <a name="automatically-with-azure-quickstart-templates"></a>Automaticamente con i modelli di avvio rapido di Azure
I modelli di avvio rapido di Azure usano il provider di risorse VM SQL per distribuire il cluster di failover di Windows, aggiungervi SQL Server VM, creare il listener e configurare la Load Balancer interna. Questa opzione richiede comunque una creazione manuale del gruppo di disponibilità e di Load Balancer interni (ILB), ma consente di automatizzare e semplificare tutti gli altri passaggi di configurazione necessari, inclusa la configurazione di ILB. 

Per altre informazioni, vedere [usare il modello di avvio rapido di Azure per configurare always on gruppo di disponibilità per SQL Server in una macchina virtuale di Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md).


## <a name="automatically-with-an-azure-portal-template"></a>Automaticamente con un modello di portale di Azure

[Configurare automaticamente il gruppo di disponibilità AlwaysOn in macchine virtuali di Azure con Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)


## <a name="manually-in-azure-portal"></a>Manualmente in portale di Azure

È anche possibile creare le macchine virtuali manualmente senza il modello. A tale scopo, è necessario completare prima i prerequisiti e quindi creare il gruppo di disponibilità. Vedere gli argomenti seguenti: 

- [Completare i prerequisiti per la creazione di gruppi di disponibilità AlwaysOn in macchine virtuali di Azure](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Creare un gruppo di disponibilità AlwaysOn per migliorare la disponibilità e il ripristino di emergenza](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>Passaggi successivi

[Configurare un gruppo di disponibilità SQL Server AlwaysOn nelle macchine virtuali di Azure](virtual-machines-windows-portal-sql-availability-group-dr.md)
