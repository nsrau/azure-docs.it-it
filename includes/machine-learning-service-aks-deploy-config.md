---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: c71f35a06d904b45cb014d5199197220b57cf230
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79486102"
---
Le voci nella `deploymentconfig.json` mappa documento vengono mappate ai parametri per [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py). Nella tabella seguente viene descritto il mapping tra le entità nel documento JSON e i parametri per il metodo:

| Entità JSON | Parametro del metodo | Descrizione |
| ----- | ----- | ----- |
| `computeType` | ND | La destinazione di calcolo. Per AKS, il `aks`valore deve essere . |
| `autoScaler` | ND | Contiene elementi di configurazione per la scalabilità automatica. Vedere la tabella autoscaler. |
| &emsp;&emsp;`autoscaleEnabled` | `autoscale_enabled` | Se abilitare la scalabilità automatica per il servizio Web. `numReplicas`  = Se `0` `True`, ; in `False`caso contrario, . |
| &emsp;&emsp;`minReplicas` | `autoscale_min_replicas` | Numero minimo di contenitori da usare per la scalabilità automatica di questo servizio Web. Impostazione `1`predefinita, . |
| &emsp;&emsp;`maxReplicas` | `autoscale_max_replicas` | Numero massimo di contenitori da usare per la scalabilità automatica di questo servizio Web. Impostazione `10`predefinita, . |
| &emsp;&emsp;`refreshPeriodInSeconds` | `autoscale_refresh_seconds` | Frequenza con cui il ridimensionamento automatico tenta di ridimensionare questo servizio Web. Impostazione `1`predefinita, . |
| &emsp;&emsp;`targetUtilization` | `autoscale_target_utilization` | Utilizzo di destinazione (in percentuale su 100) che il ridimensionamento automatico deve tentare di gestire per questo servizio Web. Impostazione `70`predefinita, . |
| `dataCollection` | ND | Contiene gli elementi di configurazione per la raccolta dei dati. |
| &emsp;&emsp;`storageEnabled` | `collect_model_data` | Se abilitare la raccolta dei dati del modello per il servizio Web. Impostazione `False`predefinita, . |
| `authEnabled` | `auth_enabled` | Se abilitare o meno l'autenticazione con chiave per il servizio Web. Entrambi `tokenAuthEnabled` `authEnabled` e `True`non possono essere . Impostazione `True`predefinita, . |
| `tokenAuthEnabled` | `token_auth_enabled` | Se abilitare o meno l'autenticazione token per il servizio Web. Entrambi `tokenAuthEnabled` `authEnabled` e `True`non possono essere . Impostazione `False`predefinita, . |
| `containerResourceRequirements` | ND | Contenitore per le entità della CPU e della memoria. |
| &emsp;&emsp;`cpu` | `cpu_cores` | Numero di core CPU da allocare per questo servizio Web. Impostazioni predefinite`0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | Quantità di memoria (in GB) da allocare per questo servizio Web. Predefinito`0.5` |
| `appInsightsEnabled` | `enable_app_insights` | Se abilitare la registrazione di Application Insights per il servizio Web. Impostazione `False`predefinita, . |
| `scoringTimeoutMs` | `scoring_timeout_ms` | Timeout da applicare per il punteggio delle chiamate al servizio Web. Impostazione `60000`predefinita, . |
| `maxConcurrentRequestsPerContainer` | `replica_max_concurrent_requests` | Numero massimo di richieste simultanee per nodo per questo servizio Web. Impostazione `1`predefinita, . |
| `maxQueueWaitMs` | `max_request_wait_time` | Il tempo massimo in cui una richiesta rimarrà nella coda (in millisecondi) prima che venga restituito un errore 503. Impostazione `500`predefinita, . |
| `numReplicas` | `num_replicas` | Numero di contenitori da allocare per questo servizio Web. Nessun valore predefinito. Se questo parametro non è impostato, la scalabilità automatica è abilitata per impostazione predefinita. |
| `keys` | ND | Contiene gli elementi di configurazione per le chiavi. |
| &emsp;&emsp;`primaryKey` | `primary_key` | Una chiave di autenticazione primaria da utilizzare per questo servizio Web |
| &emsp;&emsp;`secondaryKey` | `secondary_key` | Una chiave di autenticazione secondaria da utilizzare per questo servizio Web |
| `gpuCores` | `gpu_cores` | Numero di core GPU (replica per contenitore) da allocare per questo servizio Web. Il valore predefinito è 1. Supporta solo valori numerici interi. |
| `livenessProbeRequirements` | ND | Contiene gli elementi di configurazione per i requisiti del probe di liveness. |
| &emsp;&emsp;`periodSeconds` | `period_seconds` | Frequenza (in secondi) per eseguire la sonda di liveness. Il valore predefinito è 10 secondi. Il valore minimo è 1. |
| &emsp;&emsp;`initialDelaySeconds` | `initial_delay_seconds` | Numero di secondi dopo l'avvio del contenitore prima dell'avvio dei probe di liveness. Il valore predefinito è 310 |
| &emsp;&emsp;`timeoutSeconds` | `timeout_seconds` | Numero di secondi dopo i quali scade la sonda di liveness. Il valore predefinito è 2 secondi. Il valore minimo è 1 |
| &emsp;&emsp;`successThreshold` | `success_threshold` | Successo minimo consecutivo per la sonda di liveness da considerare di successo dopo che ha fallito. Assume il valore predefinito 1. Il valore minimo è 1. |
| &emsp;&emsp;`failureThreshold` | `failure_threshold` | Quando un Pod si avvia e il probe di liveness non riesce, Kubernetes tenterà failureThreshold volte prima di rinunciare. Il valore predefinito è 3. Il valore minimo è 1. |
| `namespace` | `namespace` | Lo spazio dei nomi Kubernetes in cui viene distribuito il servizio Web. Fino a 63 caratteri alfanumerici minuscoli ('a'-'z', '0'-'9') e trattini ('-') caratteri. Il primo e l'ultimo carattere non possono essere trattini. |

The following JSON is an example deployment configuration for use with the CLI:

```json
{
    "computeType": "aks",
    "autoScaler":
    {
        "autoscaleEnabled": true,
        "minReplicas": 1,
        "maxReplicas": 3,
        "refreshPeriodInSeconds": 1,
        "targetUtilization": 70
    },
    "dataCollection":
    {
        "storageEnabled": true
    },
    "authEnabled": true,
    "containerResourceRequirements":
    {
        "cpu": 0.5,
        "memoryInGB": 1.0
    }
}
```
