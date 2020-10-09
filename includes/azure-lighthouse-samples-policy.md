---
title: includere file
description: includere file
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 09/24/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: a9c9b5081232988cb4ab80bfcbb9f2d92d1d025f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91336574"
---
Questi esempi illustrano come usare Criteri di Azure con le sottoscrizioni di cui è stato eseguito l'onboarding in Azure Lighthouse.

| **Modello** | **Descrizione** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag) | Assegna un criterio che aggiunge o rimuove un tag (usando l'effetto modify) in una sottoscrizione delegata. Per altre informazioni, vedere [Distribuire un criterio che è possibile correggere all'interno di una sottoscrizione delegata](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-audit-delegation) | Assegna un criterio che controlla le assegnazioni di delega. |
| [policy-delegate-management-groups](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-delegate-management-groups) | Assegna un criterio per confermare che le sottoscrizioni all'interno di un gruppo di gestione sono state delegate a un tenant di gestione e, in caso contrario, crea l'assegnazione.
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring) | Assegna un criterio che abilita la diagnostica per le risorse di Azure Key Vault in una sottoscrizione delegata (usando l'effetto deployIfNotExists). Per altre informazioni, vedere [Distribuire un criterio che è possibile correggere all'interno di una sottoscrizione delegata](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-sub-monitoring) | Assegna diversi criteri per abilitare la diagnostica in una sottoscrizione delegata e connette tutte le VM Windows e Linux all'area di lavoro Log Analytics creata dal criterio. Per altre informazioni, vedere [Distribuire un criterio che è possibile correggere all'interno di una sottoscrizione delegata](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-initiative) | Applica un'iniziativa (più definizioni di criteri correlate) a una sottoscrizione delegata. |

