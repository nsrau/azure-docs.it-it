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
ms.openlocfilehash: e2d87d4322ac6b91a19a4775c23ceec75d528030
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96325333"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>Revisioni, flussi di lavoro e processi di moderazione dei contenuti

Content Moderator combina la moderazione assistita da computer con funzionalità di ciclo umano per creare un processo di moderazione ottimale per scenari reali. Questa operazione viene eseguita tramite lo strumento di [Revisione](https://contentmoderator.cognitive.microsoft.com)basato sul cloud. In questa guida verranno illustrati i concetti di base dello strumento di Revisione: recensioni, flussi di lavoro e processi.

## <a name="reviews"></a>Revisioni

In una revisione, il contenuto viene caricato nello strumento di revisione. È possibile visualizzarlo facendo clic sul relativo tipo di contenuto nella scheda **Revisione** del dashboard. Dalla schermata di revisione è possibile modificare i tag applicati e applicare i tag personalizzati in base alle esigenze. Quando si invia una revisione, i risultati vengono inviati a un endpoint di callback specificato e il contenuto viene rimosso dal sito.

> [!div class="mx-imgBorder"]
> ![Il menu a discesa verifica è evidenziato. Mostra i tipi di contenuto seguenti: immagini, testo e video.](./Review-Tool-user-Guide/images/review-tab.png)

### <a name="manage-reviews"></a>Gestisci revisioni

Dal Dashboard passare ad **Amministrazione**  ->  **Gestisci recensioni** per visualizzare la schermata di amministrazione. Qui è possibile visualizzare un elenco di tutte le revisioni (in sospeso e completate).

Il pulsante **azioni** a tre punti in ogni verifica consente di passare alla schermata di revisione o controllare la cronologia di tale revisione.

> [!div class="mx-imgBorder"]
> ![Sito Web dello strumento di revisione, nella schermata di Revisione](./Review-Tool-user-Guide/images/manage-reviews.png)

Utilizzare la barra degli strumenti di **ricerca** per ordinare le revisioni in base a un'ampia gamma di categorie, ad esempio stato di revisione, tag, tipo di contenuto, sottoteam, utenti assegnati e data di creazione/modifica.

> [!div class="mx-imgBorder"]
> ![Viene visualizzata la barra degli strumenti di ricerca. Include varie caselle combinate per l'immissione dei criteri di ricerca, ad esempio lo stato di revisione e i tag.](./Review-Tool-user-Guide/images/review-search.png)

Per informazioni su come eseguire questa operazione a livello di codice, vedere la [Guida dello strumento di revisione](./review-tool-user-guide/review-moderated-images.md) per iniziare a creare recensioni o vedere la [Guida dell'API REST](./try-review-api-review.md) .

## <a name="workflows"></a>Flussi di lavoro

Un flusso di lavoro è un filtro personalizzato basato sul cloud per il contenuto. I flussi di lavoro possono connettersi a un'ampia gamma di servizi per filtrare il contenuto in modi diversi e quindi intraprendere l'azione appropriata. Con il connettore Content Moderator, un flusso di lavoro può applicare automaticamente tag di moderazione e creare recensioni con contenuto inviato.

### <a name="view-workflows"></a>Visualizza flussi di lavoro

Per visualizzare i flussi di lavoro esistenti, passare allo [strumento di revisione](https://contentmoderator.cognitive.microsoft.com/) e selezionare **amministratori**  >  **flussi di lavoro**.

> [!div class="mx-imgBorder"]
> ![Flusso di lavoro predefinito](images/default-workflow-list.png)

I flussi di lavoro sono definiti come stringhe JSON, rendendoli accessibili a livello di codice. Se si seleziona l'opzione **modifica** per il flusso di lavoro e quindi si seleziona la scheda **JSON** , verrà visualizzata un'espressione JSON simile alla seguente:

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
* Per le revisioni, iniziare con la [console per l'API di revisione](try-review-api-review.md) e usare i codici di esempio dell'API REST. Vedere la sezione revisioni della [Guida introduttiva di .NET](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp).
* Per le revisioni di video, usare la [guida introduttiva alle revisioni di video](video-reviews-quickstart-dotnet.md) e vedere come [aggiungere trascrizioni alla revisione di un video](video-transcript-reviews-quickstart-dotnet.md).