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
ms.openlocfilehash: 32d39493e526a4b95369e2998d3f9ade9f2964dc
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77133702"
---
| Resource | Limite predefinito | Limite massimo |
| --- | --- | --- |
| [Gruppi di risorse](../articles/azure-resource-manager/management/overview.md) per sottoscrizione |980 |980 |
| Dimensioni richieste API Azure Resource Manager |4\.194.304 byte. |4\.194.304 byte. |
| Tag per sottoscrizione<sup>1</sup> |Illimitato. |Illimitato. |
| Calcoli di tag univoci per ogni sottoscrizione<sup>1</sup> | 10,000 | 10,000 |
| [Distribuzioni a livello di sottoscrizione](../articles/azure-resource-manager/templates/deploy-to-subscription.md) per località | 800<sup>2</sup> | 800 |
| Sottoscrizioni per Azure Active Directory tenant | Illimitato. | Illimitato. |
| [Coamministratori](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) per sottoscrizione |Illimitato. |Illimitato. |

<sup>1</sup> È possibile applicare un numero illimitato di tag per ogni sottoscrizione. Il numero di tag per risorsa o gruppo di risorse è limitato a 50. Gestione risorse restituisce un [elenco di nomi e valori di tag univoci](/rest/api/resources/tags) nella sottoscrizione solo se il numero di tag è 10.000 o meno. È ancora possibile trovare una risorsa in base al tag quando il numero supera 10.000.  

<sup>2</sup> Se si raggiunge il limite di 800 distribuzioni, eliminare le distribuzioni dalla cronologia che non sono più necessarie. Per eliminare le distribuzioni a livello di sottoscrizione, usare [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) o [AZ Deployment Delete](/cli/azure/deployment?view=azure-cli-latest#az-deployment-delete).
