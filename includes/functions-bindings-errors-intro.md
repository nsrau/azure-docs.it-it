---
author: ggailey777
ms.service: azure-functions
ms.topic: include
origin.date: 09/04/2018
ms.date: 12/26/2018
ms.author: v-junlch
ms.openlocfilehash: c1784111cd2fc2c93b67510f310b9e513cf2b86e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61438151"
---
[Trigger e associazioni](../articles/azure-functions/functions-triggers-bindings.md) di Funzioni di Azure comunicano con diversi servizi di Azure. In caso di integrazione con questi servizi, possono venire generati errori originati dalle API dei servizi di Azure sottostanti. Possono verificarsi errori anche quando si prova a comunicare con altri servizi dal codice della funzione usando librerie client o REST. Per evitare la perdita di dati e garantire un comportamento corretto delle funzioni, è importante gestire gli errori da entrambe le origini.

I trigger seguenti hanno il supporto di ripetizione dei tentativi incorporato:

- [Archivio BLOB di Azure](../articles/azure-functions/functions-bindings-storage-blob.md)
- [Archiviazione code di Azure](../articles/azure-functions/functions-bindings-storage-queue.md)
- [Bus di servizio Azure (coda/argomento)](../articles/azure-functions/functions-bindings-service-bus.md)

Per impostazione predefinita, per questi trigger vengono eseguiti fino a cinque nuovi tentativi. Dopo il quinto tentativo, questi trigger scrivono un messaggio in una speciale [coda non elaborabile](../articles/azure-functions/functions-bindings-storage-queue.md#trigger---poison-messages).

Per gli altri trigger di funzione, non è disponibile un meccanismo predefinito di ripetizione dei tentativi quando si verificano errori durante l'esecuzione della funzione. Per evitare perdite di informazioni dei trigger in caso di errore nella funzione, è consigliabile usare blocchi Try-Catch nel codice della funzione per intercettare gli errori. Quando si verifica un errore, scrivere le informazioni passate alla funzione dal trigger in una speciale coda "non elaborabile". Questo approccio corrisponde a quello usato dal [trigger di archiviazione BLOB](../articles/azure-functions/functions-bindings-storage-blob.md#trigger---poison-blobs).

In questo modo, è possibile acquisire gli eventi di trigger che potrebbero andare persi a causa di errori ed eseguire un nuovo tentativo in un secondo momento usando un'altra funzione per elaborare i messaggi dalla coda non elaborabile usando le informazioni archiviate.  

<!-- ms.date: 12/26/2018 -->