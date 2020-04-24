---
title: File di inclusione
description: File di inclusione
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 94fd7e692be31ba247e3342246d3940ed08ef9b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334648"
---
| Risorsa | Limite |
| --- | --- |
| Sottoscrizioni per Azure Active Directory tenant | Senza limiti. |
| [Coamministratori](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) per sottoscrizione |Senza limiti. |
| [Gruppi di risorse](../articles/azure-resource-manager/management/overview.md) per sottoscrizione |980 |
| Dimensioni richieste API Azure Resource Manager |4.194.304 byte. |
| Tag per sottoscrizione<sup>1</sup> |50 |
| Calcoli di tag univoci per ogni sottoscrizione<sup>1</sup> | 10,000 |
| [Distribuzioni a livello di sottoscrizione](../articles/azure-resource-manager/templates/deploy-to-subscription.md) per località | 800<sup>2</sup> |

<sup>1</sup> È possibile applicare fino a 50 tag direttamente a una sottoscrizione. Tuttavia, la sottoscrizione può contenere un numero illimitato di tag che vengono applicati ai gruppi di risorse e alle risorse all'interno della sottoscrizione. Il numero di tag per risorsa o gruppo di risorse è limitato a 50. Gestione risorse restituisce un [elenco di nomi e valori di tag univoci](/rest/api/resources/tags) nella sottoscrizione solo se il numero di tag è 10.000 o meno. È ancora possibile trovare una risorsa in base al tag quando il numero supera 10.000.  

<sup>2</sup> Se si raggiunge il limite di 800 distribuzioni, eliminare le distribuzioni dalla cronologia che non sono più necessarie. Per eliminare le distribuzioni a livello di sottoscrizione, usare [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) o [AZ Deployment Sub Delete](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-delete).
