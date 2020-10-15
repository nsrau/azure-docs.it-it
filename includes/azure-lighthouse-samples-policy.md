---
title: includere file
description: includere file
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 10/12/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 20d80fe1a09d388552b6289f8a9b23c878672f94
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91974287"
---
Questi esempi illustrano come usare Criteri di Azure con le sottoscrizioni di cui è stato eseguito l'onboarding in Azure Lighthouse.

| **Modello** | **Descrizione** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag) | Assegna un criterio che aggiunge o rimuove un tag (usando l'effetto modify) in una sottoscrizione delegata. Per altre informazioni, vedere [Distribuire un criterio che è possibile correggere all'interno di una sottoscrizione delegata](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-allow-certain-managing-tenants](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-allow-certain-managing-tenants) | Assegna un criterio limitando le deleghe di Azure Lighthouse a tenant di gestione specifici. |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-audit-delegation) | Assegna un criterio che controlla le assegnazioni di delega. |
| [policy-delegate-management-groups](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-delegate-management-groups) | Assegna un criterio per confermare che le sottoscrizioni all'interno di un gruppo di gestione sono state delegate a un tenant di gestione e, in caso contrario, crea l'assegnazione.
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring) | Assegna un criterio che abilita la diagnostica per le risorse di Azure Key Vault in una sottoscrizione delegata (usando l'effetto deployIfNotExists). Per altre informazioni, vedere [Distribuire un criterio che è possibile correggere all'interno di una sottoscrizione delegata](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-sub-monitoring) | Assegna diversi criteri per abilitare la diagnostica in una sottoscrizione delegata e connette tutte le VM Windows e Linux all'area di lavoro Log Analytics creata dal criterio. Per altre informazioni, vedere [Distribuire un criterio che è possibile correggere all'interno di una sottoscrizione delegata](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-initiative) | Applica un'iniziativa (più definizioni di criteri correlate) a una sottoscrizione delegata. |

