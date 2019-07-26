---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/19/2019
ms.author: larryfr
ms.openlocfilehash: fd9527714b87cc35fcfc61f4858cbacee7fa95f5
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348538"
---
Le voci nel `deploymentconfig.json` documento vengono mappate ai parametri per [AciWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration?view=azure-ml-py). La tabella seguente descrive il mapping tra le entità nel documento JSON e i parametri per il metodo:

| Entità JSON | Parametro del metodo | DESCRIZIONE |
| ----- | ----- | ----- |
| `computeType` | ND | La destinazione di calcolo. Per ACI, il valore deve essere `ACI`. |
| `containerResourceRequirements` | ND | Contenitore per le entità CPU e memoria. |
| &emsp;&emsp;`cpu` | `cpu_cores` | Numero di core CPU da allocare. Impostazioni predefinite`0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | Quantità di memoria (in GB) da allocare per questo servizio Web. Default, `0.5` |
| `location` | `location` | Area di Azure in cui distribuire il servizio Web. Se non è specificato, verrà usato il percorso dell'area di lavoro. Altre informazioni sulle aree disponibili sono disponibili qui: [Aree ACI](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=container-instances) |
| `authEnabled` | `auth_enabled` | Indica se abilitare l'autenticazione per questo servizio Web. Il valore predefinito è false |
| `sslEnabled` | `ssl_enabled` | Indica se abilitare SSL per questo servizio Web. Il valore predefinito è false. |
| `appInsightsEnabled` | `enable_app_insights` | Indica se abilitare AppInsights per questo servizio Web. Il valore predefinito è false |
| `sslCertificate` | `ssl_cert_pem_file` | Il file cert è necessario se SSL è abilitato |
| `sslKey` | `ssl_key_pem_file` | Il file di chiave necessario se SSL è abilitato |
| `cname` | `ssl_cname` | CNAME per se SSL è abilitato |
| `dnsNameLabel` | `dns_name_label` | Etichetta del nome DNS per l'endpoint di assegnazione dei punteggi. Se non viene specificata, verrà generata un'etichetta univoca per il nome DNS per l'endpoint di assegnazione dei punteggi. |

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