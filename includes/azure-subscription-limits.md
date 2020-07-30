---
title: includere il file
description: File di inclusione
author: rothja
ms.service: azure-resource-manager
ms.topic: include
ms.date: 05/18/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: db822722b12921ab98b3e5cae67e28f4ca7ede04
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87298810"
---
| Risorsa | Limite predefinito | Limite massimo |
| --- | --- | --- |
| vCPU per ogni [sottoscrizione](../articles/billing-buy-sign-up-azure-subscription.md)<sup>1</sup> |20 |10,000 |
| [Coamministratori](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) per ogni sottoscrizione |200 |200 |
| [Account di archiviazione](../articles/storage/common/storage-create-storage-account.md) per ogni sottoscrizione<sup>2</sup> |100 |100 |
| [Servizi cloud](../articles/cloud-services/cloud-services-choose-me.md) per sottoscrizione |20 |200 |
| [Reti locali](/previous-versions/azure/reference/jj157100(v=azure.100)) per sottoscrizione |10 |500 |
| Server DNS per sottoscrizione |9 |100 |
| IP riservati per sottoscrizione |20 |100 |
| [Gruppi di affinità](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) per sottoscrizione |256 |256 |
| Lunghezza del nome della sottoscrizione (caratteri) | 64 | 64 |

<sup>1</sup>Le istanze di dimensioni estremamente ridotte vengono conteggiate come una vCPU ai fini del limite di vCPU anche se viene usato un core CPU parziale.

<sup>2</sup>Il limite dell'account di archiviazione include sia l'account di archiviazione Standard che l'account di archiviazione Premium. 

