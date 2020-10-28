---
title: Panoramica di Funzioni di Azure
description: Informazioni su come usare Funzioni di Azure per ottimizzare i carichi di lavoro asincroni in pochi minuti.
author: mattchenderson
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.topic: overview
ms.date: 01/16/2020
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: 0583b68df603b04d47ac6104f0cf127b3c4bedd0
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92173714"
---
# <a name="an-introduction-to-azure-functions"></a>Introduzione a Funzioni di Azure

Funzioni di Azure consente di eseguire piccole parti di codice, dette "funzioni", senza doversi preoccupare dell'infrastruttura dell'applicazione. Con Funzioni di Azure, l'infrastruttura cloud fornisce tutti i server aggiornati necessari per consentire l'esecuzione dell'applicazione su larga scala.

Una funzione viene "attivata" da un tipo specifico di evento. I [trigger supportati](./functions-triggers-bindings.md) includono la risposta alle modifiche dei dati, la risposta ai messaggi, l'esecuzione in base a una pianificazione o l'azione in seguito a una richiesta HTTP.

Sebbene sia sempre possibile scrivere codice direttamente per una miriade di servizi, l'integrazione con altri servizi risulta molto più semplice con i binding. Le associazioni consentono l'[accesso dichiarativo a un'ampia gamma di servizi di Azure e di terze parti](./functions-triggers-bindings.md).

## <a name="features"></a>Funzionalità

Ecco alcune delle principali funzionalità di Funzioni di Azure:

- **Applicazioni serverless** : Funzioni di Azure consente di sviluppare applicazioni [serverless](https://azure.microsoft.com/solutions/serverless/) in Microsoft Azure.

- **Scelta del linguaggio** : è possibile scrivere le funzioni scegliendo tra [C#, Java, JavaScript, Python e PowerShell](supported-languages.md).

- **Modello tariffario in base al consumo** : si paga solo per il tempo impiegato per l'esecuzione del codice. Visualizzare l'opzione del piano di hosting A consumo nella [sezione relativa ai prezzi](#pricing).  

- **Trasferimento delle dipendenze** : Funzioni supporta NuGet e NPM, fornendo l'accesso alle librerie preferite.

- **Sicurezza integrata** : è possibile proteggere le funzioni attivate da HTTP con provider OAuth come Azure Active Directory, Facebook, Google, Twitter e account Microsoft.

- **Integrazione semplificata** : è possibile eseguire facilmente l'integrazione con i servizi di Azure e le offerte SaaS.

- **Sviluppo flessibile** : è possibile configurare l'integrazione continua e distribuire il codice con [GitHub](../app-service/scripts/cli-continuous-deployment-github.md), [Azure DevOps Services](../app-service/scripts/cli-continuous-deployment-vsts.md) e altri [strumenti di sviluppo supportati](../app-service/deploy-local-git.md).

- **Architettura serverless con stato** : è possibile orchestrare le applicazioni serverless con [Durable Functions](durable/durable-functions-overview.md).

- **Open source** : il runtime di Funzioni è open source e [disponibile in GitHub](https://github.com/azure/azure-webjobs-sdk-script).

## <a name="what-can-i-do-with-functions"></a>Quali operazioni si possono eseguire con Funzioni?

Funzioni è un'ottima soluzione per l'elaborazione di dati in blocco, l'integrazione di sistemi, l'utilizzo di Internet delle cose (IoT) e la compilazione di semplici API e microservizi.

È disponibile una serie di modelli per familiarizzare con gli scenari principali, tra cui:

- **HTTP** : eseguire codice basato su [richieste HTTP](functions-create-first-azure-function.md)

- **Timer** : pianificare l' [esecuzione del codice in orari predefiniti](./functions-create-scheduled-function.md)

- **Azure Cosmos DB** : elaborare [documenti di Azure Cosmos DB nuovi e modificati](./functions-create-cosmos-db-triggered-function.md)

- **Archivio BLOB** : elaborare [BLOB di Archiviazione di Azure nuovi e modificati](./functions-create-storage-blob-triggered-function.md)

- **Archiviazione code** : rispondere a [messaggi della coda di archiviazione di Azure](./functions-create-storage-queue-triggered-function.md)

- **Griglia di eventi** : rispondere a [eventi di Griglia di eventi di Azure tramite sottoscrizioni e filtri](../event-grid/resize-images-on-storage-blob-upload-event.md)

- **Hub eventi** : rispondere a [volumi elevati di eventi di Hub eventi di Azure](./functions-bindings-event-hubs.md)

- **Coda del bus di servizio** : connettersi ad altri servizi di Azure o locali [rispondendo ai messaggi della coda del bus di servizio](./functions-bindings-service-bus.md)

- **Argomento del bus di servizio** : connettersi ad altri servizi di Azure o locali [rispondendo ai messaggi dell'argomento del bus di servizio](./functions-bindings-service-bus.md)

## <a name="how-much-does-functions-cost"></a><a name="pricing"></a>Quanto costa Funzioni?

Funzioni di Azure offre tre tipi di piani tariffari. Scegliere quello più adatto alle proprie esigenze:

- **Piano a consumo** : Azure fornisce tutte le risorse di calcolo necessarie. Non ci si deve preoccupare della gestione delle risorse e si paga solo in base al tempo impiegato per l'esecuzione del codice.

- **Piano Premium** : si specifica un numero di istanze preriscaldate che saranno sempre online e pronte a rispondere immediatamente. Quando la funzione viene eseguita, Azure fornisce le risorse di calcolo aggiuntive eventualmente necessarie. Vengono addebitate le istanze preriscaldate eseguite in modo continuo e le istanze aggiuntive usate quando Azure aumenta e riduce il numero di istanze dell'app.

- **Piano di servizio app** : consente di eseguire le funzioni esattamente come le app Web. Se si usa il servizio app per altre applicazioni, è possibile eseguire le funzioni nello stesso piano senza costi aggiuntivi.

Per altre informazioni sui piani di hosting, vedere [Confronto di piani di hosting per Funzioni di Azure](functions-scale.md). I dettagli completi sui prezzi sono disponibili nella [pagina Prezzi di Funzioni](https://azure.microsoft.com/pricing/details/functions/).

## <a name="next-steps"></a>Passaggi successivi

- [Creare la prima funzione di Azure](functions-create-first-function-vs-code.md)  
  Fornisce una panoramica su [Visual Studio Code](functions-create-first-function-vs-code.md), la [riga di comando](functions-create-first-azure-function-azure-cli.md) o il [portale di Azure](functions-create-first-azure-function.md).

- [Guida di riferimento per gli sviluppatori a Funzioni di Azure](functions-reference.md)  
  Include informazioni più tecniche sul runtime di Funzioni di Azure, nonché informazioni di riferimento per la codifica di funzioni e la definizione di trigger e associazioni.

- [Come aumentare le prestazioni di Funzioni di Azure](functions-scale.md)  
  Presenta i piani di servizio disponibili con Funzioni di Azure, tra cui il piano di hosting A consumo, e spiega come scegliere quello più appropriato.

- [Informazioni sul servizio app di Azure](../app-service/overview.md)  
  Funzioni di Azure sfrutta il servizio app di Azure per le funzionalità di base, ad esempio distribuzioni, variabili di ambiente e diagnostica.
