---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: df4735699f71256598bfd418a505ae8d3e5b816c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80296896"
---
Dopo l'accesso, viene visualizzato un elenco di sottoscrizioni associate all'account Azure. Le informazioni `isDefault: true` sulla sottoscrizione con è la sottoscrizione attualmente attivata per i comandi dell'interfaccia della riga di comando di Azure.The subscription information with is the currently activated subscription for Azure CLI commands. Questa sottoscrizione deve essere la stessa che contiene l'area di lavoro di Azure Machine Learning.This subscription must be the same that contains your Azure Machine Learning workspace. È possibile trovare l'ID sottoscrizione dal [portale di Azure](https://portal.azure.com) visitando la pagina di panoramica dell'area di lavoro. È anche possibile usare l'SDK per ottenere l'ID sottoscrizione dall'oggetto dell'area di lavoro. Ad esempio: `Workspace.from_config().subscription_id`.
    
Per selezionare un'altra sottoscrizione, usare il comando [az account set](https://docs.microsoft.com/cli/azure/account#az-account-set) con l'ID sottoscrizione a cui passare. Per altre informazioni sulla selezione delle sottoscrizioni, vedere Usare più sottoscrizioni di Azure.For more information about subscription selection, see [Use multiple Azure Subscriptions.](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)