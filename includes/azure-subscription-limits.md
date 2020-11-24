---
title: includere il file
description: File di inclusione
author: rothja
ms.service: azure-resource-manager
ms.topic: include
ms.date: 05/18/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 74f9c5304237ec77a629bc914d95c345882b784f
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95556999"
---
| Risorsa | Limite predefinito | Limite massimo |
| --- | --- | --- |
| vCPU per ogni [sottoscrizione](https://azure.microsoft.com/pricing/)<sup>1</sup> |20 |10,000 |
| [Coamministratori](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) per ogni sottoscrizione |200 |200 |
| [Account di archiviazione](../articles/storage/common/storage-account-create.md) per ogni sottoscrizione<sup>2</sup> |100 |100 |
| [Servizi cloud](../articles/cloud-services/cloud-services-choose-me.md) per sottoscrizione |20 |200 |
| [Reti locali](/previous-versions/azure/reference/jj157100(v=azure.100)) per sottoscrizione |10 |500 |
| Server DNS per sottoscrizione |9 |100 |
| IP riservati per sottoscrizione |20 |100 |
| [Gruppi di affinità](/previous-versions/azure/virtual-network/virtual-networks-migrate-to-regional-vnet) per sottoscrizione |256 |256 |
| Lunghezza del nome della sottoscrizione (caratteri) | 64 | 64 |

<sup>1</sup>Le istanze di dimensioni estremamente ridotte vengono conteggiate come una vCPU ai fini del limite di vCPU anche se viene usato un core CPU parziale.

<sup>2</sup>Il limite dell'account di archiviazione include sia l'account di archiviazione Standard che l'account di archiviazione Premium.