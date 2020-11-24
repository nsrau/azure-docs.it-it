---
title: includere file
description: File di inclusione
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 11/22/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 32edd880e5e455393e60c87f4f963d71a3e59a20
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95560623"
---
| Risorsa | Limite |
| --- | :--- |
| Numero massimo di cluster per sottoscrizione | 100 |
| Numero massimo di nodi per cluster con set di disponibilità di macchine virtuali e SKU Basic di Load Balancer  | 100 |
| Numero massimo di nodi per cluster con set di scalabilità di macchine virtuali e [SKU di Load Balancer Standard][standard-load-balancer] | 1000 (100 nodi per [pool di nodi][node-pool]) |
| Numero massimo di pod per nodo: [rete Basic][basic-networking] con Kubenet | 110 |
| Numero massimo di pod per nodo: [rete Advanced][advanced-networking] con Azure Container Networking Interface | Distribuzione dell'interfaccia della riga di comando di Azure: 30<sup>1</sup><br />Modello di Azure Resource Manager: 30<sup>1</sup><br />Distribuzione portale: 30 |

<sup>1</sup>Quando si distribuisce un cluster del servizio Azure Kubernetes con l'interfaccia della riga di comando di Azure o con un modello di Resource Manager, è possibile configurare questo valore fino a un massimo di 250 pod per nodo. Se un cluster del servizio Azure Kubernetes è già stato distribuito o si distribuisce un cluster con il portale di Azure, non è possibile configurare il numero massimo di pod per nodo.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest