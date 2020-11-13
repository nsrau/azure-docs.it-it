---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/26/2020
ms.author: larryfr
ms.openlocfilehash: 2bba53410834aadce5627a34a759e65aa0e11c28
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94574522"
---
> [!IMPORTANT]
> L'istanza Cosmos DB viene creata in un gruppo di risorse gestito da Microsoft nella __sottoscrizione__ , insieme alle risorse necessarie. Ciò significa che viene addebitato il costo per questa istanza di Cosmos DB. Al gruppo di risorse gestito viene assegnato un nome nel formato `<AML Workspace Resource Group Name><GUID>`. Se l'area di lavoro Azure Machine Learning usa un endpoint privato, viene creata anche una rete virtuale per l'istanza di Cosmos DB. Questo VNet viene usato per proteggere le comunicazioni tra Cosmos DB e Azure Machine Learning.
> 
> * __Non eliminare il gruppo di risorse__ che contiene questa istanza di Cosmos DB o una delle risorse create automaticamente in questo gruppo. Se è necessario eliminare il gruppo di risorse, Cosmos DB istanza e così via, è necessario eliminare l'area di lavoro Azure Machine Learning che la utilizza. Il gruppo di risorse, l'istanza Cosmos DB e altre risorse create automaticamente vengono eliminate quando l'area di lavoro associata viene eliminata.
> * Le [__Unità richiesta__](../articles/cosmos-db/request-units.md) per l'account Cosmos DB sono impostate su __8000__.
> * __Non è possibile specificare VNet personalizzati da usare con l'istanza di Cosmos DB__ creata. Non è inoltre __possibile modificare la rete virtuale__. Ad esempio, non è possibile modificare l'intervallo di indirizzi IP usato.