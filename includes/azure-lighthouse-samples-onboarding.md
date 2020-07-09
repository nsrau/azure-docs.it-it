---
title: includere file
description: includere file
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 07/07/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: d80786bbdfc4f53254dc98d79ac3badcf5dd3b24
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86111660"
---
Sono disponibili diversi modelli in base a specifici scenari di onboarding. Scegliere l'opzione più indicata e assicurarsi di modificare il file dei parametri in base all'ambiente. Per altre informazioni su come usare questi file nella distribuzione, vedere [Eseguire l'onboarding dei clienti in Azure Lighthouse](../articles/lighthouse/how-to/onboard-customer.md).

| **Modello** | **Descrizione** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management) | Eseguire l'onboarding della sottoscrizione di un cliente in Azure Lighthouse Per ogni sottoscrizione è necessario eseguire una distribuzione diversa. |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management) | Eseguire l'onboarding di uno o più gruppi di risorse di un cliente in Azure Lighthouse. Usare **rgDelegatedResourceManagement** per un singolo gruppo di risorse oppure **multipleRgDelegatedResourceManagement** per eseguire l'onboarding di più gruppi di risorse nella stessa sottoscrizione. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/marketplace-delegated-resource-management) | Se è stata [pubblicata un'offerta di servizi gestiti in Azure Marketplace](../articles/lighthouse/how-to/publish-managed-services-offers.md), facoltativamente è possibile usare questo modello per eseguire l'onboarding delle risorse per i clienti che l'hanno accettata. I valori del marketplace nel file di parametri devono corrispondere ai valori usati quando è stata pubblicata l'offerta. |

In genere per ogni sottoscrizione di cui si esegue l'onboarding è necessaria una distribuzione distinta, ma è anche possibile distribuire i modelli in più sottoscrizioni.

| **Modello** | **Descrizione** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/cross-subscription-deployment) | Distribuisce modelli di Azure Resource Manager tra più sottoscrizioni. |
