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
ms.openlocfilehash: a2729af6a689daa551fc01f585324d53a8770a9b
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "67180510"
---
| Risorsa | Limite predefinito |
| --- | :--- |
| Numero massimo di cluster per sottoscrizione | 100 |
| Numero massimo di nodi per cluster | 100 |
| Numero massimo di pod per nodo: [Rete di base][basic-networking] con Kubenet | 110 |
| Numero massimo di pod per nodo: [Rete avanzata][advanced-networking] con l'interfaccia di rete di contenitori di Azure | Distribuzione con l'interfaccia della riga di comando di Azure: 30<sup>1</sup><br />Modello di Azure Resource Manager: 30<sup>1</sup><br />Distribuzione con il portale: 30 |

<sup>1</sup> Quando si distribuisce un cluster Azure Kubernetes Service (AKS) con l'interfaccia della riga di comando di Azure o un modello di Gestione risorse, questo valore è configurabile fino a 250 pod per ogni nodo. Non è possibile configurare il numero massimo di pod per nodo dopo che è già stato distribuito un cluster AKS o se si distribuisce un cluster usando il portale di Azure.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
