---
title: includere file
description: File di inclusione
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 94fd7e692be31ba247e3342246d3940ed08ef9b7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80334648"
---
| Risorsa | Limite |
| --- | --- |
| Sottoscrizioni per tenant di Azure Active Directory | Senza limiti. |
| [Coamministratori](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) per ogni sottoscrizione |Senza limiti. |
| [Gruppi di risorse](../articles/azure-resource-manager/management/overview.md) per sottoscrizione |980 |
| Dimensione delle richieste delle API di Azure Resource Manager |4\.194.304 byte. |
| Tag per sottoscrizione<sup>1</sup> |50 |
| Calcoli di tag univoci per sottoscrizione<sup>1</sup> | 10,000 |
| [Distribuzioni a livello di sottoscrizione](../articles/azure-resource-manager/templates/deploy-to-subscription.md) per sede | 800<sup>2</sup> |

<sup>1</sup>È possibile applicare fino a 50 tag direttamente a una sottoscrizione. La sottoscrizione può tuttavia contenere un numero illimitato di tag che vengono applicati ai gruppi di risorse e alle risorse all'interno della sottoscrizione. Il numero di tag per risorsa o per gruppo di risorse è limitato a 50. Resource Manager restituisce un [elenco di nomi e valori di tag univoci](/rest/api/resources/tags) nella sottoscrizione solo quando il numero di tag non supera i 10.000. È possibile comunque trovare una risorsa in base al tag quando il numero è superiore a 10.000.  

<sup>2</sup>Se si raggiunge il limite di 800 distribuzioni, eliminare dalla cronologia le distribuzioni che non sono più necessarie. Per eliminare le distribuzioni a livello di sottoscrizione, usare [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) o [az deployment sub delete](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-delete).
