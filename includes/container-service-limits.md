---
title: File di inclusione
description: File di inclusione
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 11/22/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 6b4678b381e769993b01bbedd1cb4c0aeefc0cc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335060"
---
| Risorsa | Limite |
| --- | :--- |
| Numero massimo di cluster per sottoscrizioneMaximum clusters per subscription | 100 |
| Numero massimo di nodi per cluster con set di disponibilità della macchina virtuale e SKU di basic load balancerMaximum nodes per cluster with Virtual Machine Availability Sets and Basic Load Balancer SKU  | 100 |
| Numero massimo di nodi per cluster con set di scalabilità di macchine virtuali e [SKU di Load Balancer standardMaximum nodes][standard-load-balancer] per cluster with Virtual Machine Scale Sets and Standard Load Balancer SKU | 1000 (100 nodi per [pool di nodi)][node-pool] |
| Numero massimo di pod per nodo: [rete di base][basic-networking] con Kubenet | 110 |
| Numero massimo di pod per nodo: [rete avanzata][advanced-networking] con l'interfaccia di rete contenitore di AzureMaximum pods per node: Advanced networking with Azure Container Networking Interface | Distribuzione dell'interfaccia della riga di comando di Azure: 30<sup>1</sup><br />Modello di Azure Resource Manager: 30<sup>1Azure</sup> Resource Manager template: 30 1<br />Distribuzione portale: 30 |

<sup>1 : il</sup> nome del Quando si distribuisce un cluster del servizio Azure Kubernetes (AKS) con l'interfaccia della riga di comando di Azure o un modello di Resource Manager, questo valore è configurabile fino a 250 pod per nodo. Non è possibile configurare i pod massimi per nodo dopo aver già distribuito un cluster AKS o se si distribuisce un cluster tramite il portale di Azure.You can't configure maximum pods per node after you've already deployed an AKS cluster, or if you deploy a cluster by using the Azure portal.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-standard-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
