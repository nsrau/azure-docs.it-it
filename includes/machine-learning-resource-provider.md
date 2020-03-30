---
author: blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/09/2020
ms.author: larryfr
ms.openlocfilehash: 8fd774f8a3a73ceaffa7902b35e1b1dff12ef5af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75893981"
---
Quando si crea un'area di lavoro di Azure Machine Learning o una risorsa usata dall'area di lavoro, è che venga visualizzato un errore simile ai messaggi seguenti:When creating an Azure Machine Learning workspace, or a resource used by the workspace, you may receive an error similar to the following messages:

* `No registered resource provider found for location {location}`
* `The subscription is not registered to use namespace {resource-provider-namespace}`

La maggior parte dei provider di risorse viene registrata automaticamente, ma non tutti. Se viene visualizzato questo messaggio, è necessario registrare il provider indicato.

Per informazioni sulla registrazione dei provider di risorse, vedere [Risolvere gli errori per la registrazione](../articles/azure-resource-manager/templates/error-register-resource-provider.md)del provider di risorse .