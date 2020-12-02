---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 12/01/2020
ms.author: larryfr
ms.openlocfilehash: 6e9f349f2fd90fdb12d8d26ca904e504b75d9db2
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96445192"
---
* Quando si crea una nuova area di lavoro, è possibile creare automaticamente i servizi richiesti dall'area di lavoro o usare i servizi esistenti. Se si vuole usare i __servizi esistenti da una sottoscrizione di Azure diversa__ da quella dell'area di lavoro, è necessario registrare lo spazio dei nomi Azure Machine Learning nella sottoscrizione che contiene tali servizi. Ad esempio, se si crea un'area di lavoro nella sottoscrizione A che usa un account di archiviazione dalla sottoscrizione B, è necessario registrare lo spazio dei nomi Azure Machine Learning nella sottoscrizione B prima di poter usare l'account di archiviazione con l'area di lavoro.

    Il provider di risorse per Azure Machine Learning è __Microsoft. MachineLearningService__. Per informazioni su come verificare se è registrato e su come registrarlo, vedere l'articolo sui [tipi e i provider di risorse di Azure](../articles/azure-resource-manager/management/resource-providers-and-types.md)  .

    > [!IMPORTANT]
    > Questo vale solo per le risorse fornite durante la creazione dell'area di lavoro. Account di archiviazione di Azure, registro contenitori di Azure, Azure Key Vault e Application Insights.