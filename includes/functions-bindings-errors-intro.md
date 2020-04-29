---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 629de079f7cc7d95d10f8ff951a47b8b8fc62dad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77474157"
---
Gli errori generati in funzioni di Azure possono provenire da una qualsiasi delle origini seguenti:

- Uso di [trigger e associazioni](..\articles\azure-functions\functions-triggers-bindings.md) di funzioni di Azure predefinite
- Chiamate alle API dei servizi di Azure sottostanti
- Chiamate agli endpoint REST
- Chiamate a librerie client, pacchetti o API di terze parti

Per evitare la perdita di dati o messaggi mancanti, è importante seguire le procedure di gestione degli errori. Le procedure consigliate per la gestione degli errori includono le azioni seguenti:

- [Abilitare Application Insights](../articles/azure-functions/functions-monitoring.md)
- [Usa la gestione degli errori strutturata](#use-structured-error-handling)
- [Progettazione per idempotenza](../articles/azure-functions/functions-idempotent.md)
- [Implementare i criteri di ripetizione dei tentativi](../articles/azure-functions/functions-reliable-event-processing.md) (laddove appropriato)

### <a name="use-structured-error-handling"></a>Usa la gestione degli errori strutturata

L'acquisizione e la pubblicazione di errori è fondamentale per il monitoraggio dell'integrità dell'applicazione. Il livello superiore di qualsiasi codice di funzione deve includere un blocco try/catch. Nel blocco catch è possibile acquisire e pubblicare errori.

### <a name="retry-support"></a>Supporto per tentativi

I trigger seguenti hanno il supporto di ripetizione dei tentativi incorporato:

* [Archiviazione BLOB di Azure](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Archiviazione code di Azure](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Bus di servizio Azure (coda/argomento)](../articles/azure-functions/functions-bindings-service-bus.md)

Per impostazione predefinita, questi trigger ritentano le richieste fino a cinque volte. Dopo il quinto tentativo, i trigger di archiviazione code di Azure e del bus di servizio di Azure scrivono un messaggio in una [coda non elaborabile](..\articles\azure-functions\functions-bindings-storage-queue-trigger.md#poison-messages).

È necessario implementare manualmente i criteri di ripetizione dei tentativi per tutti gli altri tipi di trigger o associazioni. Le implementazioni manuali possono includere la scrittura di informazioni sugli errori in una [coda di messaggi non elaborabili](..\articles\azure-functions\functions-bindings-storage-blob-trigger.md#poison-blobs). Scrivendo in una coda non elaborabile, si ha la possibilità di ritentare le operazioni in un secondo momento. Questo approccio corrisponde a quello usato dal trigger di archiviazione BLOB.
