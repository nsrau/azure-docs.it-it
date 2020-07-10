---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 60c754aeec3157abee02eb136dcf148c440d8da0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79477273"
---
Le voci nel `deploymentconfig.json` documento vengono mappate ai parametri per [LocalWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py). La tabella seguente descrive il mapping tra le entità nel documento JSON e i parametri per il metodo:

| Entità JSON | Parametro del metodo | Descrizione |
| ----- | ----- | ----- |
| `computeType` | ND | La destinazione di calcolo. Per le destinazioni locali, il valore deve essere `local` . |
| `port` | `port` | Porta locale su cui esporre l'endpoint HTTP del servizio. |

Questo JSON è una configurazione di distribuzione di esempio da usare con l'interfaccia della riga di comando:

```json
{
    "computeType": "local",
    "port": 32267
}
```
