---
title: Esempi e modelli di Azure Lighthouse
description: Questi esempi e modelli di Azure Resource Manager mostrano come eseguire l'onboarding di clienti per la gestione risorse delegate di Azure e supportare scenari di Azure Lighthouse.
author: JnHs
manager: carmonm
ms.service: lighthouse
ms.topic: sample
ms.date: 07/11/2019
ms.author: jenhayes
ms.openlocfilehash: d888fb0d50bf0336f8fe830c567586e66065871f
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286146"
---
# <a name="azure-lighthouse-samples"></a>Esempi di Azure Lighthouse

La tabella seguente include collegamenti ai modelli di Azure Resource Manager per Azure Lighthouse. Questi e altri file sono anche disponibili nel [repository di esempi di Azure Lighthouse](https://github.com/Azure/Azure-Lighthouse-samples/).

| **Modello** | **Descrizione** |
|---------|---------|
| [create-multiple-rgs](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/create-multiple-rgs) | Crea più gruppi di risorse usando un singolo modello di Azure Resource Manager. |
| [cross-rg-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-rg-deployment) | Distribuisce account di archiviazione in due diversi gruppi di risorse. |
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-subscription-deployment) | Distribuisce modelli di Azure Resource Manager tra più sottoscrizioni. |
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management) | Esegue l'onboarding della sottoscrizione di un cliente nella gestione risorse delegate di Azure. |
| [deploy-azure-mgmt-services](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-mgmt-services) | Crea servizi di gestione di Azure, li collega e distribuisce soluzioni aggiuntive. |
| [deploy-azure-security-center](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-security-center) | Abilita e configura Centro sicurezza di Azure all'interno della sottoscrizione di Azure di destinazione. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management) | Esegue l'onboarding della sottoscrizione di un cliente nella gestione risorse delegate di Azure, in base a un'offerta di servizi gestiti pubblicata nel Marketplace. |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) | Esegue l'onboarding di uno o più gruppi di risorse del cliente nella gestione risorse delegate di Azure. |

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulla [gestione risorse delegate di Azure](../concepts/azure-delegated-resource-management.md).
- Informazioni sull'uso di modelli di Azure Resource Manager per [eseguire l'onboarding di clienti per la gestione risorse delegate di Azure](../how-to/onboard-customer.md).
