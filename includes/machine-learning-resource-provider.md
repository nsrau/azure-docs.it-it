---
author: blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/09/2020
ms.author: larryfr
ms.openlocfilehash: 8fd774f8a3a73ceaffa7902b35e1b1dff12ef5af
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75893981"
---
Quando si crea un'area di lavoro Azure Machine Learning o una risorsa utilizzata dall'area di lavoro, è possibile che venga visualizzato un errore simile ai messaggi seguenti:

* `No registered resource provider found for location {location}`
* `The subscription is not registered to use namespace {resource-provider-namespace}`

La maggior parte dei provider di risorse viene registrata automaticamente, ma non tutti. Se si riceve questo messaggio, è necessario registrare il provider indicato.

Per informazioni sulla registrazione dei provider di risorse, vedere [risolvere gli errori per la registrazione del provider di risorse](../articles/azure-resource-manager/templates/error-register-resource-provider.md).