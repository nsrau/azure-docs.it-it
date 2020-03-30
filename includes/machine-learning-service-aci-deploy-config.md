---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 4f13c171c5fafb13875f5f87d4eb3d6013f0ff30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79486106"
---
Le voci nel `deploymentconfig.json` documento vengono mappate ai parametri per [AciWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration?view=azure-ml-py). Nella tabella seguente viene descritto il mapping tra le entità nel documento JSON e i parametri per il metodo:

| Entità JSON | Parametro del metodo | Descrizione |
| ----- | ----- | ----- |
| `computeType` | ND | La destinazione di calcolo. Per ACI, il `ACI`valore deve essere . |
| `containerResourceRequirements` | ND | Contenitore per le entità della CPU e della memoria. |
| &emsp;&emsp;`cpu` | `cpu_cores` | Numero di core CPU da allocare. Impostazioni predefinite`0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | Quantità di memoria (in GB) da allocare per questo servizio Web. Predefinito`0.5` |
| `location` | `location` | Area di Azure in cui distribuire questa servizio Web. Se non specificato, verrà utilizzata la posizione dell'area di lavoro. Maggiori dettagli sulle regioni disponibili sono disponibili qui: [ACI Regions](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=container-instances) |
| `authEnabled` | `auth_enabled` | Se abilitare l'autenticazione per questo servizio Web. Il valore predefinito è False |
| `sslEnabled` | `ssl_enabled` | Se abilitare SSL per questo servizio Web. Il valore predefinito è False. |
| `appInsightsEnabled` | `enable_app_insights` | Se abilitare AppInsights per questo servizio Web. Il valore predefinito è False |
| `sslCertificate` | `ssl_cert_pem_file` | Il file del certificato necessario per abilitare SSL |
| `sslKey` | `ssl_key_pem_file` | Il file di chiave necessario se SSL è abilitato |
| `cname` | `ssl_cname` | Il cname per se SSL è abilitato |
| `dnsNameLabel` | `dns_name_label` | Etichetta del nome dns per l'endpoint di assegnazione del punteggio. Se non viene specificata, verrà generata un'etichetta univoca del nome dns per l'endpoint di assegnazione del punteggio. |

The following JSON is an example deployment configuration for use with the CLI:

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