---
title: Funzionalità di Durable Functions Preview-funzioni di Azure
description: Informazioni sulle funzionalità di anteprima per Durable Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: article
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: 8f2560141eac4e7d9fed267d347990e65bfe9c26
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933252"
---
# <a name="durable-functions-20-preview-azure-functions"></a>Durable Functions 2,0 Preview (funzioni di Azure)

*Funzioni permanenti* è un'estensione di [Funzioni di Azure](../functions-overview.md) e [Processi Web di Azure](../../app-service/web-sites-create-web-jobs.md) che consente di scrivere funzioni con stato in un ambiente senza server. L'estensione gestisce automaticamente lo stato, i checkpoint e i riavvii. Se non si ha già familiarità con Durable Functions, vedere la [documentazione di panoramica](durable-functions-overview.md).

Durable Functions 1. x è una funzionalità GA (disponibile a livello generale) di funzioni di Azure, ma contiene anche alcune sottofunzionalità attualmente disponibili in anteprima pubblica. In questo articolo vengono descritte le funzionalità di anteprima appena rilasciate e vengono fornite informazioni dettagliate sul funzionamento e sul modo in cui è possibile iniziare a utilizzarle.

> [!NOTE]
> Queste funzionalità di anteprima fanno parte di una versione di Durable Functions 2,0, che attualmente è una **versione di anteprima** con diverse modifiche di rilievo. Le compilazioni di pacchetti di estensione durevoli di funzioni di Azure sono reperibili in nuget.org con versioni nel formato **2.0.0-betaX**. Queste compilazioni non sono destinate ai carichi di lavoro di produzione e le versioni successive possono contenere ulteriori modifiche di rilievo.

## <a name="breaking-changes"></a>Modifiche che causano un'interruzione

In Durable Functions 2,0 sono state introdotte diverse modifiche di rilievo. Non è previsto che le applicazioni esistenti siano compatibili con Durable Functions 2,0 senza modifiche al codice. In questa sezione sono elencate alcune delle modifiche:

### <a name="hostjson-schema"></a>Schema host. JSON

Il frammento di codice seguente mostra il nuovo schema per host. JSON. Le principali modifiche da tenere presenti sono le nuove sottosezioni:

* `"storageProvider"`(e la `"azureStorage"` sottosezione) per la configurazione specifica dell'archiviazione
* `"tracking"`per la configurazione di rilevamento e registrazione
* `"notifications"`(e la `"eventGrid"` sottosezione) per la configurazione delle notifiche di griglia di eventi

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "azureStorage": {
          "connectionStringName": <string>,
          "controlQueueBatchSize": <int?>,
          "partitionCount": <int?>,
          "controlQueueVisibilityTimeout": <hh:mm:ss?>,
          "workItemQueueVisibilityTimeout": <hh:mm:ss?>,
          "trackingStoreConnectionStringName": <string?>,
          "trackingStoreNamePrefix": <string?>,
          "maxQueuePollingInterval": <hh:mm:ss?>
        }
      },
      "tracking": {
        "traceInputsAndOutputs": <bool?>,
        "traceReplayEvents": <bool?>,
      },
      "notifications": {
        "eventGrid": {
          "topicEndpoint": <string?>,
          "keySettingName": <string?>,
          "publishRetryCount": <string?>,
          "publishRetryInterval": <hh:mm:ss?>,
          "publishRetryHttpStatus": <int[]?>,
          "publishEventTypes": <string[]?>,
        }
      },
      "maxConcurrentActivityFunctions": <int?>,
      "maxConcurrentOrchestratorFunctions": <int?>,
      "extendedSessionsEnabled": <bool?>,
      "extendedSessionIdleTimeoutInSeconds": <int?>,
      "customLifeCycleNotificationHelperType": <string?>
  }
}
```

Quando durable Functions 2,0 continua a stabilizzarsi, verranno introdotte ulteriori modifiche alla `durableTask` sezione host. JSON. Per ulteriori informazioni su queste modifiche, vedere [questo problema di GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/641).

### <a name="public-interface-changes"></a>Modifiche all'interfaccia pubblica

I vari oggetti "context" supportati da Durable Functions hanno classi di base astratte destinate all'uso nel testing unità. Come parte di Durable Functions 2,0, queste classi di base astratte sono state sostituite con le interfacce. Il codice di funzione che usa direttamente i tipi concreti non è interessato.

La tabella seguente rappresenta le modifiche principali:

| Tipo precedente | Nuovo tipo |
|----------|----------|
| DurableOrchestrationClientBase | IDurableOrchestrationClient |
| DurableOrchestrationContextBase | IDurableOrchestrationContext |
| DurableActivityContextBase | IDurableActivityContext |
| OrchestrationClientAttribute | DurableClientAttribute |

Nel caso in cui una classe base astratta contenesse metodi virtuali, questi metodi virtuali sono stati sostituiti dai metodi `DurableContextExtensions`di estensione definiti in.

## <a name="entity-functions"></a>Funzioni di entità

A partire da Durable Functions versione 2.0.0-Alpha, è stato introdotto un nuovo concetto di [funzioni di entità](durable-functions-entities.md) .

Le funzioni di entità definiscono le operazioni per la lettura e l'aggiornamento di piccoli elementi di stato, noti come *entità durevoli*. Come le funzioni dell'agente di orchestrazione, le funzioni di entità sono funzioni con un tipo di trigger speciale, *trigger di entità*. Diversamente dalle funzioni dell'agente di orchestrazione, le funzioni di entità non hanno vincoli di codice specifici. Anche le funzioni di entità gestiscono lo stato in modo esplicito anziché rappresentare in modo implicito lo stato tramite flusso di controllo.

In base ai commenti e suggerimenti degli utenti iniziali, in seguito è stato aggiunto il supporto per un modello di programmazione basato su classi per le entità in Durable Functions versione 2.0.0-beta1.

Per altre informazioni, vedere l'articolo sulle [funzioni di entità](durable-functions-entities.md) .

## <a name="durable-http"></a>HTTP durevole

A partire da Durable Functions versione 2.0.0-beta2, è stata introdotta una nuova funzionalità [http durevole](durable-functions-http-features.md) che consente di:

* Chiamare le API HTTP direttamente dalle funzioni di orchestrazione (con alcune limitazioni documentate)
* Implementa il polling dello stato HTTP 202 sul lato client automatico
* Supporto incorporato per le [identità gestite di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

Per altre informazioni, vedere l'articolo sulle [funzionalità http](durable-functions-http-features.md#consuming-http-apis) .

## <a name="alternate-storage-providers"></a>Provider di archiviazione alternativi

Il Framework di attività permanenti supporta attualmente più provider di archiviazione, tra cui [archiviazione di Azure](https://github.com/Azure/durabletask/tree/master/src/DurableTask.AzureStorage), bus di [servizio di Azure](https://github.com/Azure/durabletask/tree/master/src/DurableTask.ServiceBus), un [emulatore in memoria](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Emulator)e un provider di [Redis](https://github.com/Azure/durabletask/tree/redis/src/DurableTask.Redis) sperimentale. Tuttavia, fino ad ora, l'estensione delle attività durevoli per funzioni di Azure supporta solo il provider di archiviazione di Azure. A partire da Durable Functions 2,0, è in corso l'aggiunta del supporto per provider di archiviazione alternativi, a partire dal provider Redis.

> [!NOTE]
> Durable Functions 2,0 supporta solo provider compatibili con .NET Standard 2,0.

### <a name="emulator"></a>Emulatore

Il provider [DurableTask. Emulator](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Emulator/) è una memoria locale e un provider di archiviazione non durevole adatto per scenari di test locali. Può essere configurata con lo schema **host. JSON** minimo seguente:

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "emulator": { "enabled": true }
      }
    }
  }
}
```

### <a name="redis-experimental"></a>Redis (sperimentale)

Il provider [DurableTask. Redis](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Redis/) rende permanente tutto lo stato dell'orchestrazione a un cluster Redis configurato.

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "redis": {
          "connectionStringName": <string>,
        }
      }
    }
  }
}
```

`connectionStringName` Deve fare riferimento al nome di un'impostazione dell'app o di una variabile di ambiente. L'impostazione dell'app o la variabile di ambiente deve contenere un valore della stringa di connessione Redis nel formato *Server: porta*. Ad esempio, `localhost:6379` per la connessione a un cluster Redis locale.

> [!NOTE]
> Il provider Redis è attualmente sperimentale e supporta solo le app per le funzioni in esecuzione su un singolo nodo. Non è garantito che il provider Redis venga mai reso disponibile a livello generale e potrebbe essere rimosso in una versione futura.
