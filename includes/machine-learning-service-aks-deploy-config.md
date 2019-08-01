---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/26/2019
ms.author: larryfr
ms.openlocfilehash: d59482c04c4875cb6b17ceb405e9d3ab97a741d6
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565151"
---
Le voci nel `deploymentconfig.json` documento vengono mappate ai parametri per [AksWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py). La tabella seguente descrive il mapping tra le entità nel documento JSON e i parametri per il metodo:

| Entità JSON | Parametro del metodo | DESCRIZIONE |
| ----- | ----- | ----- |
| `computeType` | ND | La destinazione di calcolo. Per AKS, il valore deve essere `aks`. |
| `autoScaler` | ND | Contiene gli elementi di configurazione per la scalabilità automatica. Vedere la tabella scalabilità automatica. |
| &emsp;&emsp;`autoscaleEnabled` | `autoscale_enabled` | Indica se abilitare la scalabilità automatica per il servizio Web. Se `numReplicas` ,;in = caso contrario ,`False`. `0` `True` |
| &emsp;&emsp;`minReplicas` | `autoscale_min_replicas` | Il numero minimo di contenitori da usare durante la scalabilità automatica di questo servizio Web. Impostazione predefinita `1`. |
| &emsp;&emsp;`maxReplicas` | `autoscale_max_replicas` | Numero massimo di contenitori da usare durante la scalabilità automatica di questo servizio Web. Impostazione predefinita `10`. |
| &emsp;&emsp;`refreshPeriodInSeconds` | `autoscale_refresh_seconds` | Frequenza con cui il ridimensionatore automatico tenta di ridimensionare questo servizio Web. Impostazione predefinita `1`. |
| &emsp;&emsp;`targetUtilization` | `autoscale_target_utilization` | Utilizzo di destinazione (in percentuale di 100) che il ridimensionatore automatico deve tentare di gestire per questo servizio Web. Impostazione predefinita `70`. |
| `dataCollection` | ND | Contiene elementi di configurazione per la raccolta di dati. |
| &emsp;&emsp;`storageEnabled` | `collect_model_data` | Indica se abilitare la raccolta dei dati del modello per il servizio Web. Impostazione predefinita `False`. |
| `authEnabled` | `auth_enabled` | Indica se abilitare l'autenticazione per il servizio Web. Impostazione predefinita `True`. |
| `containerResourceRequirements` | ND | Contenitore per le entità CPU e memoria. |
| &emsp;&emsp;`cpu` | `cpu_cores` | Numero di core CPU da allocare per questo servizio Web. Impostazioni predefinite`0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | Quantità di memoria (in GB) da allocare per questo servizio Web. Default, `0.5` |
| `appInsightsEnabled` | `enable_app_insights` | Indica se abilitare la registrazione Application Insights per il servizio Web. Impostazione predefinita `False`. |
| `scoringTimeoutMs` | `scoring_timeout_ms` | Timeout da applicare per la valutazione delle chiamate al servizio Web. Impostazione predefinita `60000`. |
| `maxConcurrentRequestsPerContainer` | `replica_max_concurrent_requests` | Numero massimo di richieste simultanee per nodo per questo servizio Web. Impostazione predefinita `1`. |
| `maxQueueWaitMs` | `max_request_wait_time` | Tempo massimo di permanenza di una richiesta nella coda (in millisecondi) prima della restituzione di un errore 503. Impostazione predefinita `500`. |
| `numReplicas` | `num_replicas` | Numero di contenitori da allocare per questo servizio Web. Nessun valore predefinito. Se questo parametro non è impostato, il ridimensionamento automatico è abilitato per impostazione predefinita. |
| `keys` | ND | Contiene elementi di configurazione per le chiavi. |
| &emsp;&emsp;`primaryKey` | `primary_key` | Chiave di autenticazione primaria da usare per questo servizio Web |
| &emsp;&emsp;`secondaryKey` | `secondary_key` | Chiave di autenticazione secondaria da usare per questo servizio Web |
| `gpuCores` | `gpu_cores` | Numero di core GPU da allocare per questo servizio Web. Il valore predefinito è 1. Supporta solo valori numerici interi. |
| `livenessProbeRequirements` | ND | Contiene gli elementi di configurazione per i requisiti del probe di liveity. |
| &emsp;&emsp;`periodSeconds` | `period_seconds` | Frequenza (in secondi) per l'esecuzione del probe di liveity. Il valore predefinito è 10 secondi. Il valore minimo è 1. |
| &emsp;&emsp;`initialDelaySeconds` | `initial_delay_seconds` | Numero di secondi trascorsi i quali il contenitore è stato avviato prima dell'avvio dei probe di Livezza. Il valore predefinito è 310 |
| &emsp;&emsp;`timeoutSeconds` | `timeout_seconds` | Numero di secondi dopo i quali si verifica il timeout del probe di Livezza. Il valore predefinito è 2 secondi. Il valore minimo è 1 |
| &emsp;&emsp;`successThreshold` | `success_threshold` | Esito positivo minimo consecutivo affinché il probe di liveity venga considerato riuscito dopo l'errore. Assume il valore predefinito 1. Il valore minimo è 1. |
| &emsp;&emsp;`failureThreshold` | `failure_threshold` | Quando un pod viene avviato e il probe di Livenza ha esito negativo, Kubernetes tenterà di failureThreshold volte prima di rinunciare. Il valore predefinito è 3. Il valore minimo è 1. |
| `namespace` | `namespace` | Spazio dei nomi Kubernetes in cui è distribuito il servizio Web. Fino a 63 caratteri alfanumerici minuscoli (' a-z ',' 0'-' 9') e trattini ('-'). Il primo e l'ultimo carattere non possono essere trattini. |

Il codice JSON seguente è un esempio di configurazione della distribuzione da usare con l'interfaccia della riga di comando:

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
