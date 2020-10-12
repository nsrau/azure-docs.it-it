---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: 428a3ad17c81b465635207de622398e814289d87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81616825"
---
> [!TIP]
> Dopo l'accesso, viene visualizzato un elenco di sottoscrizioni associate all'account Azure. Le informazioni della sottoscrizione con `isDefault: true` rappresentano la sottoscrizione attualmente attivata per i comandi dell’interfaccia della riga di comando di Azure. Questa sottoscrizione deve corrispondere a quella che contiene l'area di lavoro Azure Machine Learning. È possibile trovare l'ID sottoscrizione dal [portale di Azure](https://portal.azure.com) visitando la pagina Panoramica dell'area di lavoro. È anche possibile usare l'SDK per ottenere l'ID sottoscrizione dall'oggetto dell'area di lavoro. Ad esempio: `Workspace.from_config().subscription_id`.
> 
> Per selezionare un'altra sottoscrizione, usare il comando `az account set -s <subscription name or ID>` e specificare il nome o l'ID sottoscrizione a cui passare. Per altre informazioni sulla selezione delle sottoscrizioni, vedere [Usare più sottoscrizioni di Azure](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest).