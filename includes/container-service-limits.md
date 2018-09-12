---
title: File di inclusione
description: File di inclusione
services: container-service
author: mmacy
ms.service: container-service
ms.topic: include
ms.date: 08/31/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 1e8913d31677f3da9b6eb9d2216d8d9ec8b186b4
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666997"
---
| Risorsa | Limite predefinito |
| --- | :--- |
| Numero massimo cluster per sottoscrizione | 100 |
| Numero massimo nodi per cluster | 100 |
| Numero massimo pod per nodo: [rete di base][basic-networking] con Kubenet | 110 |
| Numero massimo pod per nodo: [rete avanzata ][advanced-networking] con l'interfaccia di rete dei contenitori di Azure | Distribuzione dell'interfaccia della riga di comando di Azure: 110<sup>1</sup><br />Modello di Resource Manager: 110<sup>1</sup><br />Distribuzione portale: 30 |

<sup>1</sup> Questo valore è configurabile al momento della distribuzione del cluster quando si distribuisce un cluster del servizio Kubernetes di Azure con l'interfaccia della riga di comando di Azure o un modello di Resource Manager.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/networking-overview.md#basic-networking
[advanced-networking]: ../articles/aks/networking-overview.md#advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
