---
title: Concetti relativi a revisioni, flussi di lavoro e processi-Content Moderator
titleSuffix: Azure Cognitive Services
description: In questo articolo vengono illustrati i concetti di base dello strumento di Revisione; revisioni, flussi di lavoro e processi.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 1aba86efb9ea76fbf060e80b47f9f2f6cdf8ee71
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73744416"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>Revisioni, flussi di lavoro e processi di moderazione dei contenuti

Content Moderator combina la moderazione assistita da computer con funzionalità di ciclo umano per creare un processo di moderazione ottimale per scenari reali. Questa operazione viene eseguita tramite lo strumento di [Revisione](https://contentmoderator.cognitive.microsoft.com)basato sul cloud. In questa guida vengono illustrati i concetti di base dello strumento di Revisione: revisioni, flussi di lavoro e processi.

## <a name="reviews"></a>Revisioni

In una revisione, il contenuto viene caricato nello strumento di revisione e viene visualizzato nella scheda **Verifica** . Da qui gli utenti possono modificare i tag applicati e applicare i propri tag personalizzati in base alle esigenze. Quando un utente invia una revisione, i risultati vengono inviati a un endpoint di callback specificato e il contenuto viene rimosso dal sito.

![Sito Web dello strumento di verifica aperto in un browser, nella scheda Verifica](./Review-Tool-user-Guide/images/image-workflow-review.png)

Per informazioni su come eseguire questa operazione a livello di codice, vedere la [Guida dello strumento di revisione](./review-tool-user-guide/review-moderated-images.md) per iniziare a creare recensioni o vedere la [Guida dell'API REST](./try-review-api-review.md) .

## <a name="workflows"></a>Flussi di lavoro

Un flusso di lavoro è un filtro personalizzato basato sul cloud per il contenuto. I flussi di lavoro possono connettersi a un'ampia gamma di servizi per filtrare il contenuto in modi diversi e quindi intraprendere l'azione appropriata. Con il connettore Content Moderator, un flusso di lavoro può applicare automaticamente tag di moderazione e creare recensioni con contenuto inviato.

### <a name="view-workflows"></a>Visualizza flussi di lavoro

Per visualizzare i flussi di lavoro esistenti, passare allo [strumento di revisione](https://contentmoderator.cognitive.microsoft.com/) e selezionare **Impostazioni** > **flussi di lavoro**.

![Flusso di lavoro predefinito](images/default-workflow-listed.PNG)

I flussi di lavoro possono essere descritti completamente come stringhe JSON, rendendoli accessibili a livello di codice. Se si seleziona l'opzione **modifica** per il flusso di lavoro e quindi si seleziona la scheda **JSON** , verrà visualizzata un'espressione JSON simile alla seguente:

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

Per informazioni su come eseguire questa operazione a livello di codice, vedere la [Guida dello strumento di revisione](./review-tool-user-guide/workflows.md) per iniziare a creare e usare i flussi di lavoro o vedere la [Guida dell'API REST](./try-review-api-workflow.md) .

## <a name="jobs"></a>Processi

Un processo di moderazione funge da wrapper per la funzionalità di moderazione del contenuto, flussi di lavoro e revisioni. Il processo esegue l'analisi dei contenuti usando l'API di moderazione delle immagini Content Moderator o l'API di moderazione del testo e quindi la verifica rispetto al flusso di lavoro designato. In base ai risultati del flusso di lavoro, è possibile o meno creare una verifica per il contenuto nello [strumento di revisione](./review-tool-user-guide/human-in-the-loop.md). Sebbene sia possibile creare e configurare le revisioni e i flussi di lavoro con le rispettive API, l'API del processo consente di ottenere un report dettagliato dell'intero processo, che può essere inviato a un endpoint di callback specificato.

Vedere la [Guida dell'API REST](./try-review-api-job.md) per iniziare a usare i processi.

## <a name="next-steps"></a>Passaggi successivi

* Eseguire il test drive della [console per l'API Job](try-review-api-job.md) e usare gli esempi di codice dell'API REST. Se si ha familiarità con Visual Studio e C#, vedere anche la [guida introduttiva a .NET per i processi](moderation-jobs-quickstart-dotnet.md). 
* Per le revisioni, iniziare con la [console per l'API di revisione](try-review-api-review.md) e usare i codici di esempio dell'API REST. Vedere la sezione revisioni della [Guida introduttiva di .NET](dotnet-sdk-quickstart.md).
* Per le revisioni di video, usare la [guida introduttiva alle revisioni di video](video-reviews-quickstart-dotnet.md) e vedere come [aggiungere trascrizioni alla revisione di un video](video-transcript-reviews-quickstart-dotnet.md).
