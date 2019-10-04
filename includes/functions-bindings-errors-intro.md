---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: f771b6b0416c5777c1ebde7e2cf2c4ffc6f375ff
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155290"
---
Gli errori generati in funzioni di Azure possono provenire da una qualsiasi delle origini seguenti:

- Uso di [trigger e associazioni](..\articles\azure-functions\functions-triggers-bindings.md) di funzioni di Azure predefinite
- Chiamate alle API dei servizi di Azure sottostanti
- Chiamate agli endpoint REST
- Chiamate a librerie client, pacchetti o API di terze parti

Per evitare la perdita di dati o messaggi mancanti, è importante seguire le procedure di gestione degli errori. Le procedure consigliate per la gestione degli errori includono le azioni seguenti:

- [Abilita Application Insights](../articles/azure-functions/functions-monitoring.md)
- [Usa la gestione degli errori strutturata](#use-structured-error-handling)
- [Progettazione per idempotenza](../articles/azure-functions/functions-idempotent.md)
- Implementare i criteri di ripetizione dei tentativi (laddove appropriato)

### <a name="use-structured-error-handling"></a>Usa la gestione degli errori strutturata

L'acquisizione e la pubblicazione di errori è fondamentale per il monitoraggio dell'integrità dell'applicazione. Il livello superiore di qualsiasi codice di funzione deve includere un blocco try/catch. Nel blocco catch è possibile acquisire e pubblicare errori.

### <a name="retry-support"></a>Supporto per tentativi

I trigger seguenti hanno il supporto di ripetizione dei tentativi incorporato:

* [Archivio BLOB di Azure](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Archiviazione code di Azure](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Bus di servizio Azure (coda/argomento)](../articles/azure-functions/functions-bindings-service-bus.md)

Per impostazione predefinita, questi trigger ritentano le richieste fino a cinque volte. Dopo il quinto tentativo, entrambi i trigger scrivono un messaggio in una [coda non elaborabile](..\articles\azure-functions\functions-bindings-storage-queue.md#trigger---poison-messages).

È necessario implementare manualmente i criteri di ripetizione dei tentativi per tutti gli altri tipi di trigger o associazioni. Le implementazioni manuali possono includere la scrittura di informazioni sugli errori in una [coda di messaggi non elaborabili](..\articles\azure-functions\functions-bindings-storage-blob.md#trigger---poison-blobs). Scrivendo in una coda non elaborabile, si ha la possibilità di ritentare le operazioni in un secondo momento. Questo approccio è identico a quello usato dal trigger di archiviazione BLOB.
