---
title: Flussi di lavoro, revisioni e i concetti di processi - Content Moderator
titlesuffix: Azure Cognitive Services
description: Informazioni sulle revisioni dei flussi di lavoro e processi
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: sajagtap
ms.openlocfilehash: c1d4ef640e2ae072dacba7a665b6689e3224c55c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60607287"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>Le verifiche di moderazione dei contenuti, i flussi di lavoro e processi

Content Moderator combina moderazione assistita da computer con funzionalità human-in-the-loop per creare un processo di moderazione ottimale per scenari reali. Ciò avviene tramite basato su cloud [strumento di revisione](https://contentmoderator.cognitive.microsoft.com). In questa guida si apprenderà i concetti di base dello strumento di revisione: processi, i flussi di lavoro e le revisioni.

## <a name="reviews"></a>Recensioni

In una revisione, il contenuto viene caricato per lo strumento di revisione e viene visualizzato sotto il **esaminare** scheda. A questo punto, gli utenti possono modificare i tag applicati e applicare i propri tag personalizzati come appropriato. Quando un utente invia una recensione, i risultati vengono inviati a un endpoint di callback specificati e il contenuto viene rimosso dal sito.

![Sito Web dello strumento di revisione aprire in un browser, nella scheda verifica](./Review-Tool-user-Guide/images/image-workflow-review.png)

Vedere le [manuale dello strumento di revisione](./review-tool-user-guide/review-moderated-images.md) Introduzione alla creazione di recensioni, o vedere la [Guida all'API REST](./try-review-api-review.md) per imparare a eseguire questa operazione a livello di codice.

## <a name="workflows"></a>Flussi di lavoro

Un flusso di lavoro è un filtro personalizzato basato sul cloud per il contenuto. I flussi di lavoro possono connettersi a un'ampia gamma di servizi per filtrare il contenuto in modi diversi e quindi intraprendere l'azione appropriata. Con il connettore di Content Moderator, un flusso di lavoro può automaticamente applicare i tag di moderazione e creare verifiche con contenuto inviato.

### <a name="view-workflows"></a>Flussi di lavoro di visualizzazione

Per i flussi di lavoro esistente, vedere la [strumento di revisione](https://contentmoderator.cognitive.microsoft.com/) e selezionare **impostazioni** > **i flussi di lavoro**.

![Flusso di lavoro predefinito](images/default-workflow-listed.PNG)

I flussi di lavoro possono essere descritti completamente sotto forma di stringhe JSON, che li rende accessibili a livello di codice. Se si seleziona il **Edit** l'opzione del flusso di lavoro e quindi selezionare la **JSON** scheda, si noterà un'espressione JSON simile al seguente:

```json
{
    "Type": "Logic",
    "If": {
        "ConnectorName": "moderator",
        "OutputName": "isAdult",
        "Operator": "eq",
        "Value": "true",
        "Type": "Condition"
        },
    "Then": {
    "Perform": [
    {
        "Name": "createreview",
        "CallbackEndpoint": null,
        "Tags": []
    }
    ],
    "Type": "Actions"
    }
}
```

Vedere il [manuale dello strumento di revisione](./review-tool-user-guide/workflows.md) per iniziare a creare e usare i flussi di lavoro o visualizzare i [Guida all'API REST](./try-review-api-workflow.md) per imparare a eseguire questa operazione a livello di codice.

## <a name="jobs"></a>Processi

Un processo di moderazione funziona come una specie di wrapper per la funzionalità di moderazione dei contenuti, i flussi di lavoro e le revisioni. Il processo analizza i contenuti mediante la moderazione di immagini Content Moderator, API o l'API di moderazione di testo e quindi viene confrontato con il flusso di lavoro designato. Basate sui risultati del flusso di lavoro, può o non è stato possibile creare una revisione per il contenuto di [strumento di revisione](./review-tool-user-guide/human-in-the-loop.md). Anche se entrambe le revisioni e i flussi di lavoro possono essere creati e configurati con le rispettive API, il processo di API consente di ottenere un report dettagliato dell'intero processo (che può essere inviato a un endpoint di callback specificato).

Vedere le [Guida all'API REST](./try-review-api-job.md) per iniziare a usare i processi.

## <a name="next-steps"></a>Passaggi successivi

* Eseguire il test drive della [console per l'API Job](try-review-api-job.md) e usare gli esempi di codice dell'API REST. Se si ha familiarità con Visual Studio e C#, vedere anche la [guida introduttiva a .NET per i processi](moderation-jobs-quickstart-dotnet.md). 
* Per le revisioni, iniziare con la [console per l'API di revisione](try-review-api-review.md) e usare i codici di esempio dell'API REST. Vedere quindi la [guida introduttiva a .NET per le revisioni](moderation-reviews-quickstart-dotnet.md).
* Per le revisioni di video, usare la [guida introduttiva alle revisioni di video](video-reviews-quickstart-dotnet.md) e vedere come [aggiungere trascrizioni alla revisione di un video](video-transcript-reviews-quickstart-dotnet.md).
