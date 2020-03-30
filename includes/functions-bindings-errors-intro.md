---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 629de079f7cc7d95d10f8ff951a47b8b8fc62dad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77474157"
---
Gli errori generati in funzioni di Azure possono provenire da una qualsiasi delle origini seguenti:Errors raised in an Azure Functions can come from any of the following origins:

- Uso di trigger e associazioni di Funzioni di Azure incorporatiUse of built-in Azure Functions [triggers and bindings](..\articles\azure-functions\functions-triggers-bindings.md)
- Chiamate alle API dei servizi di Azure sottostantiCalls to APIs of underlying Azure services
- Chiamate agli endpoint RESTCalls to REST endpoints
- Chiamate a librerie client, pacchetti o API di terze parti

Seguire solide procedure di gestione degli errori è importante per evitare la perdita di dati o messaggi persi. Le procedure consigliate per la gestione degli errori includono le azioni seguenti:Recommended error handling practices include the following actions:

- [Abilitare Application Insights](../articles/azure-functions/functions-monitoring.md)
- [Usare la gestione degli errori strutturataUse structured error handling](#use-structured-error-handling)
- [Progettare per l'idempotency](../articles/azure-functions/functions-idempotent.md)
- [Implementare i criteri di ripetizione dei tentativi](../articles/azure-functions/functions-reliable-event-processing.md) (se appropriato)Implement retry policies (where appropriate)

### <a name="use-structured-error-handling"></a>Usare la gestione degli errori strutturataUse structured error handling

Gli errori di acquisizione e pubblicazione sono fondamentali per il monitoraggio dell'integrità dell'applicazione. Il livello di livello superiore di qualsiasi codice funzione deve includere un blocco try/catch. Nel blocco catch è possibile acquisire e pubblicare errori.

### <a name="retry-support"></a>Supporto per i tentativiRetry support

I trigger seguenti hanno il supporto di ripetizione dei tentativi incorporato:

* [Archiviazione BLOB di Azure](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Archiviazione delle code di AzureAzure Queue storage](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Bus di servizio Azure (coda/argomento)](../articles/azure-functions/functions-bindings-service-bus.md)

Per impostazione predefinita, questi trigger attiva le richieste di ripetizione dei tentativi fino a cinque volte. Dopo il quinto tentativo, sia l'archiviazione della coda di Azure che il bus di servizio di Azure attiva la scrittura di un messaggio in una [coda non elaborabile.](..\articles\azure-functions\functions-bindings-storage-queue-trigger.md#poison-messages)

È necessario implementare manualmente i criteri di ripetizione dei tentativi per qualsiasi altro tipo di trigger o associazione. Le implementazioni manuali possono includere la scrittura di informazioni sugli errori in una [coda di messaggi non elaborabili.](..\articles\azure-functions\functions-bindings-storage-blob-trigger.md#poison-blobs) Scrivendo in una coda non elaborabile, è possibile ripetere le operazioni in un secondo momento. Questo approccio corrisponde a quello usato dal trigger di archiviazione BLOB.
