---
title: File di inclusione
description: File di inclusione
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 08/31/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 71294824bd3dd5215c388cfcd44382c7eee123ad
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2018
ms.locfileid: "48874016"
---
| Risorsa | Limite predefinito |
| --- | :--- |
| Numero massimo cluster per sottoscrizione | 100 |
| Numero massimo nodi per cluster | 100 |
| Numero massimo pod per nodo: [rete di base][basic-networking] con Kubenet | 110 |
| Numero massimo pod per nodo: [rete avanzata ][advanced-networking] con l'interfaccia di rete dei contenitori di Azure | Distribuzione dell'interfaccia della riga di comando di Azure: 30<sup>1</sup><br />Modello di Resource Manager: 30<sup>1</sup><br />Distribuzione portale: 30 |

<sup>1</sup> Questo valore è configurabile al momento della distribuzione del cluster quando si distribuisce un cluster del servizio Kubernetes di Azure con l'interfaccia della riga di comando di Azure o un modello di Resource Manager.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/networking-overview.md#basic-networking
[advanced-networking]: ../articles/aks/networking-overview.md#advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
