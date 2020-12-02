---
title: Panoramica di Funzioni di Azure
description: Informazioni su come usare Funzioni di Azure per creare app serverless scalabili.
author: craigshoemaker
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.topic: overview
ms.date: 11/20/2020
ms.author: cshoe
ms.custom: contperfq2
ms.openlocfilehash: 514f2e9a82a50f95f9c054c6a54e7b5af3c0af15
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96167778"
---
# <a name="introduction-to-azure-functions"></a>Introduzione alle funzioni di Azure

Spesso vengono sviluppati sistemi per reagire a una serie di eventi critici. Sia che si scelga di creare un'API Web, rispondere alle modifiche di database, elaborare flussi di dati IoT o anche gestire code di messaggi, è necessario che in ogni applicazione sia possibile eseguire parte di codice quando si verificano tali eventi.

Per rispondere a questa esigenza, la soluzione Funzioni di Azure prevede risorse di calcolo su richiesta, in due modi significativi.

Prima di tutto, Funzioni di Azure consente di implementare logica di sistema personalizzata in blocchi di codice immediatamente disponibili. Questi blocchi di codice si chiamano "funzioni". Le diverse funzioni possono essere eseguite in qualsiasi momento sia necessario rispondere a eventi critici.

In secondo luogo, quando le richieste aumentano, Funzioni di Azure soddisfa la domanda con il numero necessario di risorse e istanze di funzioni, ma solo fintanto che servono. Quando le richieste diminuiscono, le eventuali risorse e istanze dell'applicazione aggiuntive vengono rimosse automaticamente.

Da dove provengono tutte le risorse di calcolo? Funzioni di Azure [fornisce il numero necessario di risorse di calcolo](./functions-scale.md), aumentandole o riducendole in base alle richieste dell'applicazione.

La disponibilità di risorse di calcolo su richiesta rappresenta l'essenza dell'[elaborazione serverless](https://azure.microsoft.com/solutions/serverless/) in Funzioni di Azure.

## <a name="scenarios"></a>Scenari

In molti casi una funzione [si integra con una serie di servizi cloud](./functions-triggers-bindings.md) per offrire implementazioni complete e avanzate.

Di seguito è riportata una serie comune _ma assolutamente non completa_ di scenari di Funzioni di Azure.

| Per... | Quindi... |
| --- | --- |
| **Creare un'API Web** | Implementare un endpoint per le applicazioni Web usando il [trigger HTTP](./functions-bindings-http-webhook.md) |
| **Elaborare caricamenti di file** | Eseguire codice quando un file viene caricato o cambiato in [archiviazione BLOB](./functions-bindings-storage-blob.md) |
| **Creare un flusso di lavoro serverless** | Concatenare una serie di funzioni tra loro tramite [Durable Functions](./durable/durable-functions-overview.md) |
| **Rispondere alle modifiche di database** | Eseguire logica personalizzata quando un documento viene creato o aggiornato in [Cosmos DB](./functions-bindings-cosmosdb-v2.md) |
| **Eseguire attività pianificate** | Eseguire codice in [orari impostati](./functions-bindings-timer.md) |
| **Creare sistemi affidabili per le code di messaggi** | Elaborare le code di messaggi con [Archiviazione code](./functions-bindings-storage-queue.md), [bus di servizio](./functions-bindings-service-bus.md) o [Hub eventi](./functions-bindings-event-hubs.md) |
| **Analizzare flussi di dati IoT** | Raccogliere ed elaborare i [dati dei dispositivi IoT](./functions-bindings-event-iot.md) |
| **Elaborare dati in tempo reale** | Usare [Funzioni e Signal R](./functions-bindings-signalr-service.md) per rispondere ai dati al momento |

Per creare funzioni personalizzate, sono disponibili le opzioni e le risorse seguenti:

- **Usare il linguaggio preferito**: scrivere funzioni in [C#, Java, JavaScript, PowerShell o Python](./supported-languages.md) oppure usare un [gestore personalizzato](./functions-custom-handlers.md) per usare di fatto qualsiasi altro linguaggio.

- **Automatizzare la distribuzione**: è disponibile un'[ampia varietà di opzioni di distribuzione](./functions-deployment-technologies.md), che vanno da un approccio basato su strumenti all'uso di pipeline esterne.

- **Risolvere i problemi di una funzione**: usare [strumenti di monitoraggio](./functions-monitoring.md) e [strategie di test](./functions-test-a-function.md) per acquisire informazioni dettagliate sulle app.

- **Opzioni di prezzi flessibili**: con il piano [Consumo](./pricing.md) si paga solo per le funzioni in esecuzione, mentre il piano [Premium](./pricing.md) e il [piano di servizio app](./pricing.md) offrono funzionalità per esigenze speciali.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Per iniziare, vedere lezioni, esempi ed esercitazioni interattive](./functions-get-started.md)