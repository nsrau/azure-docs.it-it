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
ms.openlocfilehash: 4251f379c517d5ccfd0430987e3d5280208590ff
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49400115"
---
| Risorsa | Limite predefinito |
| --- | :--- |
| Numero massimo cluster per sottoscrizione | 100 |
| Numero massimo nodi per cluster | 100 |
| Numero massimo pod per nodo: [rete di base][basic-networking] con Kubenet | 110 |
| Numero massimo pod per nodo: [rete avanzata ][advanced-networking] con l'interfaccia di rete dei contenitori di Azure | Distribuzione dell'interfaccia della riga di comando di Azure: 30<sup>1</sup><br />Modello di Resource Manager: 30<sup>1</sup><br />Distribuzione portale: 30 |

<sup>1</sup> Quando si distribuisce un cluster AKS con l'interfaccia della riga di comando di Azure o con un modello di Resource Manager, è possibile configurare questo valore fino a un massimo di **110 pod per nodo**. Dopo che un cluster servizio Azure Kubernetes è stato distribuito o se si distribuisce un cluster usando il portale di Azure, non è possibile configurare il numero massimo di pod per nodo.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
