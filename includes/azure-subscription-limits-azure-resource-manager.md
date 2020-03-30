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
| Sottoscrizioni per tenant di Azure Active DirectorySubscriptions per Azure Active Directory tenant | Senza limiti. |
| [Coamministratori](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) per sottoscrizione |Senza limiti. |
| Gruppi di risorse per [sottoscrizioneResource groups](../articles/azure-resource-manager/management/overview.md) per subscription |980 |
| Dimensioni richieste API di Azure Resource ManagerAzure Resource Manager API request size |4.194.304 byte. |
| Tag per abbonamento<sup>1</sup> |50 |
| Calcoli di tag univoci per sottoscrizione<sup>1</sup> | 10,000 |
| [Distribuzioni a livello di sottoscrizione per posizioneSubscription-level deployments](../articles/azure-resource-manager/templates/deploy-to-subscription.md) per location | 800<sup>2</sup> |

<sup>1 : il</sup> nome del Puoi applicare fino a 50 tag direttamente a una sottoscrizione. Tuttavia, la sottoscrizione può contenere un numero illimitato di tag applicati ai gruppi di risorse e alle risorse all'interno della sottoscrizione. Il numero di tag per risorsa o gruppo di risorse è limitato a 50.The number of tags per resource or resource group is limited to 50. Resource Manager restituisce un [elenco di nomi e valori](/rest/api/resources/tags) univoci di tag nella sottoscrizione solo quando il numero di tag è pari o inferiore a 10.000. È comunque possibile trovare una risorsa in base al tag quando il numero supera 10.000.You still can find a resource by tag when the number exceeds 10,000.  

<sup>2 Il</sup> nome del sistema Se si raggiunge il limite di 800 distribuzioni, eliminare le distribuzioni dalla cronologia che non sono più necessarie. Per eliminare le distribuzioni a livello di sottoscrizione, utilizzare [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) o [az deployment sub delete](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-delete).
