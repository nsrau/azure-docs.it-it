---
title: 'Gruppi di disponibilità di SQL Server: Macchine virtuali di Azure: panoramica | Microsoft Docs'
description: Questo articolo offre una panoramica sui gruppi di disponibilità di SQL Server in macchine virtuali di Azure.
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
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
ms.openlocfilehash: b71ba12f6c533d67b04366f05b9334e1993823fa
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2018
ms.locfileid: "43382029"
---
# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>Panoramica sui gruppi di disponibilità AlwaysOn di SQL Server in macchine virtuali di Azure #

Questo articolo offre una panoramica sui gruppi di disponibilità di SQL Server in macchine virtuali di Azure. 

I gruppi di disponibilità AlwaysOn in macchine virtuali di Azure sono simili ai gruppi di disponibilità AlwaysOn locali. Per altre informazioni, vedere [Gruppi di disponibilità AlwaysOn (SQL Server)](http://msdn.microsoft.com/library/hh510230.aspx). 

Il diagramma seguente illustra le parti di un gruppo di disponibilità di SQL Server completo in macchine virtuali di Azure.

![Gruppo di disponibilità](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

La differenza principale per un gruppo di disponibilità in macchine virtuali Azure è che per queste ultime è necessario un [servizio di bilanciamento del carico](../../../load-balancer/load-balancer-overview.md). Il servizio di bilanciamento del carico contiene gli indirizzi IP per il listener del gruppo di disponibilità. Se sono disponibili più gruppi di disponibilità, è necessario un listener per ogni gruppo. Un servizio di bilanciamento del carico può supportare più listener.

Per compilare un gruppo di disponibilità di SQL Server in macchine virtuali di Azure, vedere le esercitazioni indicate di seguito.

## <a name="automatically-create-an-availability-group-from-a-template"></a>Creare automaticamente un gruppo di disponibilità da un modello

[Configurare automaticamente il gruppo di disponibilità AlwaysOn in macchine virtuali di Azure con Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)

## <a name="manually-create-an-availability-group-in-azure-portal"></a>Creare manualmente un gruppo di disponibilità nel portale di Azure

È anche possibile creare le macchine virtuali manualmente senza il modello. A tale scopo, è necessario completare prima i prerequisiti e quindi creare il gruppo di disponibilità. Vedere gli argomenti seguenti: 

- [Completare i prerequisiti per la creazione di gruppi di disponibilità AlwaysOn in macchine virtuali di Azure](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Creare un gruppo di disponibilità AlwaysOn per migliorare la disponibilità e il ripristino di emergenza](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>Passaggi successivi

[Configurare un gruppo di disponibilità SQL Server AlwaysOn in Macchine virtuali di Azure](virtual-machines-windows-portal-sql-availability-group-dr.md).
