---
title: File di inclusione
description: File di inclusione
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 12/19/2019
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 1c0be8ed8c176fe32b8fe7fd420d65727c5288d2
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986789"
---
Questi esempi mostrano come usare Criteri di Azure con le sottoscrizioni di cui è stato eseguito l'onboarding per la gestione risorse delegate.

| **Modello** | **Descrizione** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag) | Assegna un criterio che aggiunge o rimuove un tag (usando l'effetto modify) in una sottoscrizione delegata. Per altre informazioni, vedere [Distribuire un criterio che è possibile correggere all'interno di una sottoscrizione delegata](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-audit-delegation) | Assegna un criterio che controlla le assegnazioni di delega. |
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring) | Assegna un criterio che abilita la diagnostica per le risorse di Azure Key Vault in una sottoscrizione delegata (usando l'effetto deployIfNotExists). Per altre informazioni, vedere [Distribuire un criterio che è possibile correggere all'interno di una sottoscrizione delegata](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-sub-monitoring) | Assegna diversi criteri per abilitare la diagnostica in una sottoscrizione delegata e connette tutte le VM Windows e Linux all'area di lavoro Log Analytics creata dal criterio. Per altre informazioni, vedere [Distribuire un criterio che è possibile correggere all'interno di una sottoscrizione delegata](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-initiative) | Applica un'iniziativa (più definizioni di criteri correlate) a una sottoscrizione delegata. |

