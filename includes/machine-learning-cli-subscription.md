---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: 428a3ad17c81b465635207de622398e814289d87
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81616825"
---
> [!TIP]
> Dopo l'accesso, viene visualizzato un elenco di sottoscrizioni associate all'account Azure. Le informazioni sulla sottoscrizione `isDefault: true` con sono la sottoscrizione attualmente attivata per i comandi dell'interfaccia della riga di comando di Azure. Questa sottoscrizione deve corrispondere a quella che contiene l'area di lavoro Azure Machine Learning. È possibile trovare l'ID sottoscrizione dal [portale di Azure](https://portal.azure.com) visitando la pagina Panoramica dell'area di lavoro. È anche possibile usare l'SDK per ottenere l'ID sottoscrizione dall'oggetto dell'area di lavoro. Ad esempio, `Workspace.from_config().subscription_id`
> 
> Per selezionare un'altra sottoscrizione, usare `az account set -s <subscription name or ID>` il comando e specificare il nome o l'ID della sottoscrizione a cui passare. Per altre informazioni sulla selezione delle [sottoscrizioni, vedere usare più sottoscrizioni di Azure](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest).