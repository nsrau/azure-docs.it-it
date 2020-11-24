---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 9240f2616134055d6c97b9b85c264aa7635139cc
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95562736"
---
Le voci del documento `deploymentconfig.json` vengono associate ai parametri di [LocalWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py). La tabella seguente descrive il mapping tra le entità nel documento JSON e i parametri per il metodo:

| Entità JSON | Parametro per il metodo | Descrizione |
| ----- | ----- | ----- |
| `computeType` | ND | La destinazione di calcolo. Per le destinazioni locali, il valore deve essere `local`. |
| `port` | `port` | La porta locale su cui esporre l'endpoint HTTP del servizio. |

Il codice JSON seguente è un esempio di configurazione della distribuzione da usare con l'interfaccia della riga di comando:

```json
{
    "computeType": "local",
    "port": 32267
}
```