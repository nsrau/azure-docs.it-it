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
ms.openlocfilehash: 49c0a2a37fa72cd8c4750b39ff422a90721e3940
ms.sourcegitcommit: bbd66b477d0c8cb9adf967606a2df97176f6460b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93233993"
---
| Risorsa | Limite |
| --- | --- |
| Sottoscrizioni per tenant di Azure Active Directory | Nessuna limitazione |
| [Coamministratori](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) per ogni sottoscrizione |Nessuna limitazione |
| [Gruppi di risorse](../articles/azure-resource-manager/management/overview.md) per sottoscrizione |980 |
| Dimensione delle richieste delle API di Azure Resource Manager |4.194.304 byte |
| Tag per sottoscrizione<sup>1</sup> |50 |
| Calcoli di tag univoci per sottoscrizione<sup>1</sup> | 10,000 |
| [Distribuzioni a livello di sottoscrizione](../articles/azure-resource-manager/templates/deploy-to-subscription.md) per sede | 800<sup>2</sup> |

<sup>1</sup>È possibile applicare fino a 50 tag direttamente a una sottoscrizione. La sottoscrizione può tuttavia contenere un numero illimitato di tag che vengono applicati ai gruppi di risorse e alle risorse all'interno della sottoscrizione. Il numero di tag per risorsa o per gruppo di risorse è limitato a 50. Resource Manager restituisce un [elenco di nomi e valori di tag univoci](/rest/api/resources/tags) nella sottoscrizione solo quando il numero di tag non supera i 10.000. È possibile comunque trovare una risorsa in base al tag quando il numero è superiore a 10.000.  

<sup>2</sup>Se si raggiunge il limite di 800 distribuzioni, eliminare quelle non più necessarie dalla cronologia. Per eliminare le distribuzioni a livello di sottoscrizione, usare [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) o [az deployment sub delete](/cli/azure/deployment/sub#az-deployment-sub-delete).
