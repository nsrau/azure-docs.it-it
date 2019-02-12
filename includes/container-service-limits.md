---
title: File di inclusione
description: File di inclusione
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 10/11/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 23c25953d2f493d2dd799bfd11dbbb69db002d1b
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2019
ms.locfileid: "55736256"
---
| Risorsa | Limite predefinito |
| --- | :--- |
| Numero massimo cluster per sottoscrizione | 100 |
| Numero massimo nodi per cluster | 100 |
| Numero massimo di pod per nodo: [rete di base][basic-networking] con Kubenet | 110 |
| Numero massimo di pod per nodo: [rete avanzata ][advanced-networking] con Azure CNI | Distribuzione con l'interfaccia della riga di comando di Azure: 30<sup>1</sup><br />Modello di Resource Manager: 30<sup>1</sup><br />Distribuzione con il portale: 30 |

<sup>1</sup> Quando si distribuisce un cluster servizio Azure Kubernetes con l'interfaccia della riga di comando di Azure o con un modello di Resource Manager, è possibile configurare questo valore fino a un massimo di **110 pod per nodo**. Dopo che un cluster servizio Azure Kubernetes è stato distribuito o se si distribuisce un cluster usando il portale di Azure, non è possibile configurare il numero massimo di pod per nodo.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
