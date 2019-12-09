---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 11/04/2019
ms.author: larryfr
ms.openlocfilehash: c288ea4a2c62999f27b3f07c39df40dae7ab1426
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926999"
---
Le voci del documento `deploymentconfig.json` mappano i parametri per [LocalWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py). La tabella seguente descrive il mapping tra le entità nel documento JSON e i parametri per il metodo:

| Entità JSON | Parametro del metodo | Description |
| ----- | ----- | ----- |
| `computeType` | ND | La destinazione di calcolo. Per le destinazioni locali, il valore deve essere `local`. |
| `port` | `port` | Porta locale su cui esporre l'endpoint HTTP del servizio. |

Questo JSON è una configurazione di distribuzione di esempio da usare con l'interfaccia della riga di comando:

```json
{
    "computeType": "local",
    "port": 32267
}
```
