---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 4f13c171c5fafb13875f5f87d4eb3d6013f0ff30
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "79486106"
---
Le voci del documento `deploymentconfig.json` vengono mappate ai parametri di [AciWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration?view=azure-ml-py). La tabella seguente descrive il mapping tra le entità nel documento JSON e i parametri per il metodo:

| Entità JSON | Parametro per il metodo | Descrizione |
| ----- | ----- | ----- |
| `computeType` | ND | La destinazione di calcolo. Per Istanze di Azure Container, il valore deve essere `ACI`. |
| `containerResourceRequirements` | ND | Contenitore per le entità CPU e memoria. |
| &emsp;&emsp;`cpu` | `cpu_cores` | Il numero di core di CPU da allocare. Il valore predefinito è `0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | La quantità di memoria (in GB) da allocare per questo servizio Web. Il valore predefinito è `0.5` |
| `location` | `location` | L'area di Azure in cui distribuire questo servizio Web. Se non specificata, verrà usata la località dell'area di lavoro. Altre informazioni sulle aree disponibili sono disponibili qui: [Aree del servizio Istanze di Azure Container](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=container-instances) |
| `authEnabled` | `auth_enabled` | Indica se abilitare o meno l'autenticazione per questo servizio Web. Il valore predefinito è False |
| `sslEnabled` | `ssl_enabled` | Indica se abilitare o meno SSL per questo servizio Web. Il valore predefinito è False. |
| `appInsightsEnabled` | `enable_app_insights` | Indica se abilitare o meno AppInsights per questo servizio Web. Il valore predefinito è False |
| `sslCertificate` | `ssl_cert_pem_file` | Il file cert necessario se SSL è abilitato |
| `sslKey` | `ssl_key_pem_file` | Il file di chiave necessario se SSL è abilitato |
| `cname` | `ssl_cname` | Il record CNAME necessario se SSL è abilitato |
| `dnsNameLabel` | `dns_name_label` | L'etichetta del nome DNS per l'endpoint di assegnazione di punteggi. Se non specificata, per l'endpoint di assegnazione di punteggi verrà generata un'etichetta univoca del nome DNS. |

Il codice JSON seguente è un esempio di configurazione della distribuzione da usare con l'interfaccia della riga di comando:

```json
{
    "computeType": "aci",
    "containerResourceRequirements":
    {
        "cpu": 0.5,
        "memoryInGB": 1.0
    },
    "authEnabled": true,
    "sslEnabled": false,
    "appInsightsEnabled": false
}
```