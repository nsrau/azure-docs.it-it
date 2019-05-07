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
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65072866"
---
| Resource | Limite predefinito |
| --- | :--- |
| Numero massimo cluster per ogni sottoscrizione | 100 |
| Numero massimo di nodi al cluster | 100 |
| Numero massimo POD per ogni nodo: [rete di base][basic-networking] con Kubenet | 110 |
| Numero massimo POD per ogni nodo: [Advanced networking] [ advanced-networking] con interfaccia di rete di contenitori di Azure | Distribuzione con l'interfaccia della riga di comando di Azure: 30<sup>1</sup><br />Modello di Azure Resource Manager: 30<sup>1</sup><br />Distribuzione con il portale: 30 |

<sup>1</sup>quando si distribuisce un cluster Azure Kubernetes Service (AKS) con la CLI di Azure o un modello di Resource Manager, questo valore è configurabile fino a 250 POD per ogni nodo. Dopo che è già stato distribuito un cluster AKS, o se si distribuisce un cluster usando il portale di Azure, è possibile configurare il numero massimo POD per ogni nodo.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
