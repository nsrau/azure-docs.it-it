---
title: Esempi di modelli di Azure Resource Manager per la rete virtuale | Microsoft Docs
description: Informazioni sui diversi modelli di Azure Resource Manager disponibili per la distribuzione di reti virtuali di Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: c4d0c65cfae0bb323978f4143b0058071562ddb6
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64682513"
---
# <a name="azure-resource-manager-template-samples-for-virtual-network"></a>Esempi di modelli di Azure Resource Manager per la rete virtuale

La tabella seguente include collegamenti agli esempi dei modelli di Azure Resource Manager. È possibile distribuire i modelli usando il [portale](../azure-resource-manager/resource-group-template-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) di Azure, l'[interfaccia della riga di comando](../azure-resource-manager/resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) di Azure o Azure [PowerShell](../azure-resource-manager/resource-group-template-deploy.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Per informazioni su come creare modelli personalizzati, vedere [Creare il primo modello](../azure-resource-manager/resource-manager-create-first-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Informazioni sulla struttura e sulla sintassi dei modelli di Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Per la sintassi e le proprietà JSON da usare nei modelli, vedere i [tipi di risorsa Microsoft.Network](/azure/templates/microsoft.network/allversions).


| | |
|----|----|
|[Creare una rete virtuale con due subnet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets)| Crea una rete virtuale con due subnet.|
|[Instradare il traffico attraverso un'appliance virtuale di rete](https://github.com/Azure/azure-quickstart-templates/tree/master/201-userdefined-routes-appliance)| Crea una rete virtuale con tre subnet. Distribuisce una macchina virtuale in ogni subnet. Crea una tabella di route contenente route per l'indirizzamento del traffico da una subnet a un'altra tramite la macchina virtuale nella terza subnet. Associa la tabella di route a una delle subnet.|
|[Creare un endpoint servizio di rete virtuale per Archiviazione di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vnet-2subnets-service-endpoints-storage-integration)|Crea una nuova rete virtuale con due subnet e un'interfaccia di rete in ogni subnet. Abilita un endpoint di servizio in Archiviazione di Azure per una delle subnet e protegge un nuovo account di archiviazione in tale subnet.|
|[Connettere due reti virtuali](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vnet-to-vnet-peering)| Crea due reti virtuali e un peering di rete virtuale tra tali reti.|
|[Creare una macchina virtuale con più indirizzi IP](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-multiple-ipconfig)| Crea una macchina virtuale Windows o Linux con più indirizzi IP.|
|[Configurare una rete virtuale dual stack IPv4+IPv6](https://github.com/Azure/azure-quickstart-templates/tree/master/ipv6-in-vnet)|Distribuisce una rete virtuale dual stack (IPv4+IPv6) con due macchine virtuali e un Azure Load Balancer Basic con indirizzi IP pubblici IPv4 e IPv6. |
