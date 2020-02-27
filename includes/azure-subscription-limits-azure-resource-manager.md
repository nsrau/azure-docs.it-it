---
title: File di inclusione
description: File di inclusione
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 02/10/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: b7db49d09d2292c08ce33ce86b1b7f427ef75fbc
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77618479"
---
| Risorsa | Limite predefinito | Limite massimo |
| --- | --- | --- |
| Sottoscrizioni per Azure Active Directory tenant | Senza limiti. | Senza limiti. |
| [Coamministratori](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) per sottoscrizione |Senza limiti. |Senza limiti. |
| [Gruppi di risorse](../articles/azure-resource-manager/management/overview.md) per sottoscrizione |980 |980 |
| Dimensioni richieste API Azure Resource Manager |4\.194.304 byte. |4\.194.304 byte. |
| Tag per sottoscrizione<sup>1</sup> |Senza limiti. |Senza limiti. |
| Calcoli di tag univoci per ogni sottoscrizione<sup>1</sup> | 10,000 | 10,000 |
| [Distribuzioni a livello di sottoscrizione](../articles/azure-resource-manager/templates/deploy-to-subscription.md) per località | 800<sup>2</sup> | 800 |

<sup>1</sup> È possibile applicare un numero illimitato di tag per ogni sottoscrizione. Il numero di tag per risorsa o gruppo di risorse è limitato a 50. Gestione risorse restituisce un [elenco di nomi e valori di tag univoci](/rest/api/resources/tags) nella sottoscrizione solo se il numero di tag è 10.000 o meno. È ancora possibile trovare una risorsa in base al tag quando il numero supera 10.000.  

<sup>2</sup> Se si raggiunge il limite di 800 distribuzioni, eliminare le distribuzioni dalla cronologia che non sono più necessarie. Per eliminare le distribuzioni a livello di sottoscrizione, usare [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) o [AZ Deployment Delete](/cli/azure/deployment?view=azure-cli-latest#az-deployment-delete).
