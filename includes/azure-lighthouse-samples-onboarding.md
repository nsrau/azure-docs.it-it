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
ms.openlocfilehash: e32b55ed655b1e47f85640eb7f494a89f3274667
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456801"
---
Sono disponibili diversi modelli in base a specifici scenari di onboarding. Scegliere l'opzione più indicata e assicurarsi di modificare il file dei parametri in base all'ambiente. Per altre informazioni su come usare questi file nella distribuzione, vedere [Eseguire l'onboarding di un cliente nella gestione risorse delegate](../articles/lighthouse/how-to/onboard-customer.md).

| **Modello** | **Descrizione** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management) | Esegue l'onboarding della sottoscrizione di un cliente nella gestione risorse delegate di Azure. Per ogni sottoscrizione è necessario eseguire una distribuzione diversa. |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) | Esegue l'onboarding di uno o più gruppi di risorse del cliente nella gestione risorse delegate di Azure. Usare **rgDelegatedResourceManagement** per un singolo gruppo di risorse oppure **multipleRgDelegatedResourceManagement** per eseguire l'onboarding di più gruppi di risorse nella stessa sottoscrizione. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management) | Se è stata [pubblicata un'offerta di servizi gestiti in Azure Marketplace](../articles/lighthouse/how-to/publish-managed-services-offers.md), facoltativamente è possibile usare questo modello per eseguire l'onboarding delle risorse per i clienti che l'hanno accettata. I valori del marketplace nel file di parametri devono corrispondere ai valori usati quando è stata pubblicata l'offerta. |

In genere per ogni sottoscrizione di cui si esegue l'onboarding è necessaria una distribuzione distinta, ma è anche possibile distribuire i modelli in più sottoscrizioni.

| **Modello** | **Descrizione** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-subscription-deployment) | Distribuisce modelli di Azure Resource Manager tra più sottoscrizioni. |
