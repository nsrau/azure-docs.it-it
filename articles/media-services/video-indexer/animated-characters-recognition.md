---
title: Rilevamento dei caratteri animati con Video Indexer
titleSuffix: Azure Media Services
description: In questo argomento viene illustrato come utilizzare il rilevamento di caratteri animati con Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/05/2019
ms.author: juliako
ms.openlocfilehash: ac31a2945e59df3b57000a0ffdcbca8344e44c18
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122305"
---
# <a name="animated-character-detection-preview"></a>Rilevamento di caratteri animati (anteprima)

Servizi multimediali di Azure Video Indexer supporta il rilevamento, il raggruppamento e il riconoscimento di caratteri nel contenuto animato tramite l'integrazione con la [visione personalizzata di servizi cognitivi](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/). Questa funzionalità è disponibile sia tramite il portale che tramite l'API.

Dopo aver caricato un video animato con un modello di animazione specifico, Video Indexer estrae i fotogrammi chiave, rileva i caratteri animati in questi frame, raggruppa un carattere simile e sceglie l'esempio migliore. Invia quindi i caratteri raggruppati a Visione personalizzata che identifica i caratteri in base ai modelli su cui è stato eseguito il training. 

Prima di iniziare a eseguire il training del modello, i caratteri vengono rilevati senza alcun nome. Quando si aggiungono nomi e si esegue il training del modello, il Video Indexer riconoscerà i caratteri e li definirà di conseguenza.

## <a name="flow-diagram"></a>Diagramma di flusso

Il diagramma seguente illustra il flusso del processo di rilevamento dei caratteri animati.

![Diagramma di flusso](./media/animated-characters-recognition/flow.png)

## <a name="accounts"></a>Account

A seconda di un tipo di account di Video Indexer, sono disponibili diversi set di funzionalità. Per informazioni su come connettere l'account ad Azure, vedere [creare un account video Indexer connesso ad Azure](connect-to-azure.md).

* Account di valutazione: Video Indexer usa un account di Visione personalizzata interno per creare un modello e connetterlo all'account Video Indexer. 
* Account a pagamento: è possibile connettere l'account Visione personalizzata all'account di Video Indexer (se non si dispone già di un account, è necessario creare prima un account).

### <a name="trial-vs-paid"></a>Versione di valutazione e a pagamento

|Funzionalità|Versione di prova|A pagamento|
|---|---|---|
|Account Visione personalizzata|Gestito dietro le quinte da Video Indexer. |L'account Visione personalizzata è connesso al Video Indexer.|
|Numero di modelli di animazione|Uno|Fino a 100 modelli per account (limitazione Visione personalizzata).|
|Training del modello|Video Indexer addestra il modello per i nuovi caratteri esempi aggiuntivi di caratteri esistenti.|Il proprietario dell'account esegue il training del modello quando è pronto per apportare modifiche.|
|Opzioni avanzate in Visione personalizzata|Nessun accesso al portale di Visione personalizzata.|È possibile modificare i modelli nel portale di Visione personalizzata.|

## <a name="use-the-animated-character-detection-with-portal"></a>Usare il rilevamento dei caratteri animati con il portale 

Questa sezione descrive i passaggi da eseguire per iniziare a usare il modello di rilevamento dei caratteri animati.

### <a name="connect-your-custom-vision-account-paid-accounts-only"></a>Connetti il tuo account di Visione personalizzata (solo account a pagamento)

Se si è proprietari di un account Video Indexer a pagamento, è necessario prima connettere un account di Visione personalizzata. Se non si dispone già di un account Visione personalizzata, crearne uno. Per ulteriori informazioni, vedere [visione personalizzata](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/home).

### <a name="create-an-animated-characters-model"></a>Creare un modello di caratteri animati

1. Passare al sito Web di [Video Indexer](https://vi.microsoft.com/) ed eseguire l'accesso.
1. Fare clic sul pulsante personalizzazione modello di contenuto nell'angolo superiore destro della pagina.

    ![Personalizzazione del modello di contenuto](./media/animated-characters-recognition/content-model-customization.png)
1. Passare alla scheda **caratteri animati** nella sezione personalizzazione del modello.
1. Fare clic su **Aggiungi modello**.
1. Assegnare un nome al modello e fare clic su invio per salvare il nome.

> [!NOTE]
> La procedura consigliata consiste nel disporre di un modello di visione artificiale personalizzato per ogni serie animata. 

### <a name="index-a-video-with-an-animated-model"></a>Indicizzare un video con un modello animato

1. Fare clic sul pulsante **upload (carica** ) nel menu in alto.
1. Scegliere un video da caricare (da un file o un URL).
1. Fare clic su **Opzioni avanzate**.
1. In **persone/caratteri animati** scegliere **modelli di animazione**.
1. Se si dispone di un modello, questo verrà scelto automaticamente e se si dispone di più modelli è possibile scegliere quello pertinente dal menu a discesa.
1. Fare clic su carica.
1. Una volta indicizzato il video, i caratteri rilevati vengono visualizzati nella sezione **caratteri animati** del riquadro **Insights** .

> [!NOTE] 
> Prima di contrassegnare e eseguire il training del modello, tutti i caratteri animati verranno denominati "Unknown #X". Dopo aver eseguito il training del modello, verranno riconosciuti anche.

### <a name="customize-the-animated-characters-models"></a>Personalizzare i modelli di caratteri animati

1. Contrassegnare ed eseguire il training del modello.

    1. Contrassegnare il carattere rilevato modificando il relativo nome. Una volta eseguito il training di un carattere nel modello, esso verrà riconosciuto come il prossimo video indicizzato con il modello. 
    1. Per contrassegnare un carattere animato nel video, passare alla scheda **Insights** e fare clic sul pulsante **Edit (modifica** ) nell'angolo superiore destro della finestra.
    1. Nel riquadro **Insights** fare clic su uno dei caratteri animati rilevati e modificare i relativi nomi da "Unknown #X" o dal nome assegnato in precedenza al carattere.
    1. Dopo aver digitato il nuovo nome, fare clic sull'icona di controllo accanto al nuovo nome. In questo modo il nuovo nome viene salvato nel modello in Video Indexer.
    1. Al termine della modifica di tutti i nomi desiderati, è necessario eseguire il training del modello.

        Aprire la pagina Personalizzazione e fare clic sulla scheda **caratteri animati** . Se si dispone di un account a pagamento, è possibile fare clic sul collegamento **Gestisci modelli in visione cliente** (come illustrato di seguito). Verrà quindi inviato alla pagina del modello in Visione personalizzata. Fare clic sul pulsante **Train (Train** ) per eseguire il training del modello. 
        
        ![Personalizzazione del modello di contenuto](./media/animated-characters-recognition/content-model-customization-tab.png)

        Se si dispone di un account di valutazione, il collegamento **Gestisci modelli in visione cliente** non sarà presente. In questo caso, è possibile premere **Train** right nella pagina di **personalizzazione del modello di contenuto** .
    1. Una volta eseguito il training, qualsiasi video che verrà indicizzato o reindicizzato con tale modello riconoscerà i caratteri sottoposti a training. 
    Gli account a pagamento che hanno accesso al proprio account di Visione personalizzata possono visualizzare i modelli e le immagini con tag. Altre informazioni su come [migliorare il classificatore in visione personalizzata](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-improving-your-classifier).

1. Eliminare un carattere animato.

    1. Per eliminare un carattere animato in Insights video, passare alla scheda **Insights** e fare clic sul pulsante Edit ( **modifica** ) nell'angolo superiore destro della finestra.
    1. Scegliere il carattere animato, quindi fare clic sul pulsante **Elimina** sotto il nome.

    > [!NOTE]
    > Questa operazione eliminerà le informazioni da questo video, ma non influirà sul modello.

1. Eliminare un modello.

    1. Fare clic sul pulsante **personalizzazione modello di contenuto** nel menu in alto e passare alla scheda **caratteri animati** .
    1. Fare clic sull'icona con i puntini di sospensione a destra del modello che si desidera eliminare e quindi sul pulsante Elimina.
    
    * Account a pagamento: il modello verrà disconnesso da Video Indexer e non sarà possibile riconnetterlo.
    * Account di valutazione: il modello verrà eliminato anche dalla visione doganale. 
    
        > [!NOTE]
        > In un account di valutazione è presente un solo modello che è possibile usare. Dopo averla eliminata, non è possibile eseguire il training di altri modelli.

## <a name="use-the-animated-character-detection-with-api"></a>Usare il rilevamento dei caratteri animati con l'API 

1. Connettere un account Visione personalizzata.

    Se si è proprietari di un account Video Indexer a pagamento, è necessario prima connettere un account di Visione personalizzata. <br/>
    Se non si dispone già di un account Visione personalizzata, crearne uno. Per ulteriori informazioni, vedere [visione personalizzata](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/home).

    [Connettere l'account visione personalizzata usando l'API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Connect-Custom-Vision-Account?tags=&pattern=&groupBy=tag).
1. Creare un modello di caratteri animati.

    Usare l'API [Crea modello di animazione](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Animation-Model?&groupBy=tag) .
1. Indicizzare o reindicizzare un video.

    Usare l'API di [reindicizzazione](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) . 
1. Personalizzare i modelli di caratteri animati.

    Usare l'API del [modello di animazione del training](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Train-Animation-Model?&groupBy=tag) .

### <a name="view-the-output"></a>Visualizzare l'output

Vedere i caratteri animati nel file JSON generato.

```json
"animatedCharacters": [
    {
    "videoId": "e867214582",
    "confidence": 0,
    "thumbnailId": "00000000-0000-0000-0000-000000000000",
    "seenDuration": 201.5,
    "seenDurationRatio": 0.3175,
    "isKnownCharacter": true,
    "id": 4,
    "name": "Bunny",
    "appearances": [
        {
            "startTime": "0:00:52.333",
            "endTime": "0:02:02.6",
            "startSeconds": 52.3,
            "endSeconds": 122.6
        },
        {
            "startTime": "0:02:40.633",
            "endTime": "0:03:16.6",
            "startSeconds": 160.6,
            "endSeconds": 196.6
        },
    ]
    },
]
```

## <a name="limitations"></a>Limitazioni

* Attualmente, la funzionalità di identificazione dell'animazione non è supportata nell'area Asia orientale.
* I caratteri che sembrano essere piccoli o lontani nel video potrebbero non essere identificati correttamente se la qualità del video è insufficiente.
* Si consiglia di usare un modello per set di caratteri animati, ad esempio per una serie animata.

## <a name="next-steps"></a>Passaggi successivi

[Panoramica di Video Indexer](video-indexer-overview.md)
