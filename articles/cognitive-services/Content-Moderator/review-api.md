---
title: Concetti relativi a recensioni, flussi di lavoro e processi - Content Moderator
titleSuffix: Azure Cognitive Services
description: In questo articolo verranno illustrati i concetti di base dello strumento di revisione; revisioni, flussi di lavoro e processi.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 1aba86efb9ea76fbf060e80b47f9f2f6cdf8ee71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221150"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>Revisioni, flussi di lavoro e processi sulla moderazione dei contenuti

Content Moderator combina la moderazione assistita dal computer con le funzionalità umane nel ciclo per creare un processo di moderazione ottimale per scenari reali. Lo fa attraverso lo [strumento di revisione](https://contentmoderator.cognitive.microsoft.com)basato su cloud . In questa guida verranno illustrati i concetti di base dello strumento Di revisione: revisioni, flussi di lavoro e processi.

## <a name="reviews"></a>Revisioni

In una revisione, il contenuto viene caricato nello strumento Revisione e visualizzato nella scheda **Revisione.** Da qui, gli utenti possono modificare i tag applicati e applicare i propri tag personalizzati in base alle esigenze. Quando un utente invia una revisione, i risultati vengono inviati a un endpoint di callback specificato e il contenuto viene rimosso dal sito.

![Sito Web dello strumento di revisione aperto in un browser, nella scheda Revisione](./Review-Tool-user-Guide/images/image-workflow-review.png)

Vedere la [guida allo strumento Di revisione](./review-tool-user-guide/review-moderated-images.md) per iniziare a creare recensioni o la guida all'API [REST](./try-review-api-review.md) per informazioni su come eseguire questa operazione a livello di codice.

## <a name="workflows"></a>Flussi di lavoro

Un flusso di lavoro è un filtro personalizzato basato su cloud per il contenuto. I flussi di lavoro possono connettersi a un'ampia gamma di servizi per filtrare il contenuto in modi diversi e quindi intraprendere l'azione appropriata. Con il connettore Content Moderator, un flusso di lavoro può applicare automaticamente i tag di moderazione e creare revisioni con il contenuto inviato.

### <a name="view-workflows"></a>Visualizzare i flussi di lavoro

Per visualizzare i flussi di lavoro esistenti, passare allo [strumento Revisione](https://contentmoderator.cognitive.microsoft.com/) e selezionare**Flussi di lavoro** **impostazioni** > .

![Flusso di lavoro predefinito](images/default-workflow-listed.PNG)

I flussi di lavoro possono essere descritti completamente come stringhe JSON, il che le rende accessibili a livello di codice. Se si seleziona l'opzione **Modifica** per il flusso di lavoro e quindi si seleziona la scheda **JSON,** verrà visualizzata un'espressione JSON simile alla seguente:

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

Vedere la [guida allo strumento di revisione](./review-tool-user-guide/workflows.md) per iniziare a creare e usare i flussi di lavoro oppure la guida all'API [REST](./try-review-api-workflow.md) per informazioni su come eseguire questa operazione a livello di codice.

## <a name="jobs"></a>Processi

Un processo di moderazione funge da wrapper per la funzionalità di moderazione dei contenuti, flussi di lavoro e revisioni. Il processo analizza il contenuto utilizzando l'API di moderazione dell'immagine di Content Moderator o l'API di moderazione del testo e quindi lo confronta con il flusso di lavoro designato. In base ai risultati del flusso di lavoro, può o non può creare una revisione per il contenuto nello [strumento di revisione](./review-tool-user-guide/human-in-the-loop.md). Sebbene sia le revisioni che i flussi di lavoro possano essere creati e configurati con le rispettive API, l'API del processo consente di ottenere un report dettagliato dell'intero processo (che può essere inviato a un endpoint di callback specificato).

Vedere la [guida all'API REST](./try-review-api-job.md) per iniziare a usare i processi.

## <a name="next-steps"></a>Passaggi successivi

* Eseguire il test drive della [console per l'API Job](try-review-api-job.md) e usare gli esempi di codice dell'API REST. Se si ha familiarità con Visual Studio e C#, vedere anche la [guida introduttiva a .NET per i processi](moderation-jobs-quickstart-dotnet.md). 
* Per le revisioni, iniziare con la [console per l'API di revisione](try-review-api-review.md) e usare i codici di esempio dell'API REST. Vedere quindi la sezione delle revisioni della [guida introduttiva](dotnet-sdk-quickstart.md)di .NET .
* Per le revisioni di video, usare la [guida introduttiva alle revisioni di video](video-reviews-quickstart-dotnet.md) e vedere come [aggiungere trascrizioni alla revisione di un video](video-transcript-reviews-quickstart-dotnet.md).
