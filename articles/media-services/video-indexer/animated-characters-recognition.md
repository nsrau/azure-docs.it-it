---
title: Rilevamento di caratteri animati con Video Indexer
titleSuffix: Azure Media Services
description: In questo argomento viene illustrato come utilizzare il rilevamento di caratteri animati con Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: af608dcfbb5d98cf3116de4e14dc12bf6facb97b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76989910"
---
# <a name="animated-character-detection-preview"></a>Rilevamento di caratteri animati (anteprima)

L'indicizzatore video di Servizi multimediali di Azure supporta il rilevamento, il raggruppamento e il riconoscimento di caratteri nel contenuto animato tramite l'integrazione con [la visione personalizzata di Servizi cognitivi.](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) Questa funzionalità è disponibile sia tramite il portale che tramite l'API.

Dopo aver caricato un video animato con un modello di animazione specifico, Video Indexer estrae i fotogrammi chiave, rileva i caratteri animati in questi fotogrammi, raggruppa caratteri simili e sceglie il campione migliore. Quindi, invia i caratteri raggruppati a Visione personalizzata che identifica i caratteri in base ai modelli su cui è stato eseguito il training. 

Prima di iniziare il training del modello, i caratteri vengono rilevati senza nome. Quando si aggiungono nomi e si allenano il modello, l'indicizzatore video riconoscerà i caratteri e li chiamerà di conseguenza.

## <a name="flow-diagram"></a>Diagramma di flusso

Il diagramma seguente illustra il flusso del processo di rilevamento dei caratteri animati.

![Diagramma di flusso](./media/animated-characters-recognition/flow.png)

## <a name="accounts"></a>Account

A seconda del tipo di account Dell'Indicizzatore Video, sono disponibili diversi set di funzionalità. Per informazioni su come connettere l'account ad Azure, vedere [Creare un account dell'indicizzatore video connesso ad Azure.For information](connect-to-azure.md)on how to connect your account to Azure, see Create a Video Indexer account connected to Azure.

* Account di prova: Video Indexer utilizza un account Custom Vision interno per creare un modello e collegarlo al tuo account Video Indexer. 
* Account a pagamento: colleghi il tuo account Visione personalizzata al tuo account Video Indexer (se non ne hai già uno, devi prima creare un account).

### <a name="trial-vs-paid"></a>Versione di prova contro.

|Funzionalità|Versione di valutazione|A pagamento|
|---|---|---|
|Account Vision personalizzato|Gestito dietro le quinte da Video Indexer. |L'account Visione personalizzata è connesso all'Indicizzatore Video.|
|Numero di modelli di animazione|Uno|Fino a 100 modelli per account (limitazione della visione personalizzata).|
|Training del modello|L'indicizzatore video forma il training del modello per i nuovi personaggi con esempi aggiuntivi di personaggi esistenti.|Il proprietario dell'account esegue il training del modello quando è pronto ad apportare modifiche.|
|Opzioni avanzate in Visione personalizzata|Nessun accesso al portale di visione personalizzata.|È possibile regolare i modelli manualmente nel portale Di visione personalizzata.|

## <a name="use-the-animated-character-detection-with-portal"></a>Usare il rilevamento dei caratteri animati con il portaleUse the animated character detection with portal 

In questa sezione vengono descritti i passaggi da eseguire per iniziare a utilizzare il modello animato di rilevamento dei caratteri. 

Poiché negli account di prova l'integrazione di Visione personalizzata è gestita da Video Indexer, puoi iniziare a creare e utilizzare il modello di personaggi animati e saltare la sezione seguente ("Connetti il tuo account di visione personalizzata").

### <a name="connect-your-custom-vision-account-paid-accounts-only"></a>Collegare l'account Visione personalizzata (solo account a pagamento)

Se possiedi un account Video Indexer a pagamento, devi prima collegare un account Vision personalizzato. Se non hai già un account Custom Vision, creane uno. Per ulteriori informazioni, consultate [Visione personalizzata.](../../cognitive-services/custom-vision-service/home.md)

> [!NOTE]
> Entrambi gli account devono trovarsi nella stessa area. L'integrazione di Custom Vision non è attualmente supportata nella regione del Giappone.

#### <a name="connect-a-custom-vision-account-with-api"></a>Connettere un account Vision personalizzato con API 

Segui questi passaggi per connettere l'account Visione personalizzata all'Indicizzatore video o per modificare l'account Visione personalizzata attualmente connesso a Video Indexer:

1. Individuare [www.customvision.ai](https://www.customvision.ai) e accedere.
1. Copiare le seguenti chiavi: 

    * Chiave di formazione (per la risorsa di formazione)
    * Chiave di stima (per la risorsa di stima)Prediction key (for the prediction resource)
    * Endpoint 
    * ID risorsa stima
    
    > [!NOTE]
    > Per fornire tutte le chiavi è necessario disporre di due risorse separate in Visione personalizzata, una per l'allenamento e una per la stima.
1. Sfogliare e accedere [all'indicizzatore video](https://vi.microsoft.com/).
1. Fare clic sul punto interrogativo nell'angolo superiore destro della pagina e scegliere **Riferimento API**.
1. Assicurarsi di essere iscritti a Gestione API facendo clic sulla scheda **Prodotti.** Se si dispone di un'API connessa, è possibile continuare con il passaggio successivo, in caso contrario, eseguire la sottoscrizione. 
1. Nel portale per sviluppatori fare clic su **Riferimento API completa** e passare a **Operazioni**.  
1. Selezionare **Connetti account visione personalizzato (PREVIEW)** e fare clic su **Prova**.
1. Compilare i campi obbligatori e il token di accesso e fare clic su **Invia**. 

    Per ulteriori informazioni su come ottenere il token di accesso All'indicizzatore video, visitare il portale per [sviluppatori](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Account-Access-Token?)e consultare la [relativa documentazione.](video-indexer-use-apis.md#obtain-access-token-using-the-authorization-api)  
1. Una volta che la chiamata restituisce 200 OK risposta, il tuo account è connesso.
1. Per verificare la connessione accedendo al portale [Video Indexer](https://vi.microsoft.com/):
1. Fare clic sul pulsante **di personalizzazione del modello** di contenuto nell'angolo in alto a destra.
1. Vai alla scheda **Personaggi animati.**
1. Una volta che si fa clic su Gestisci modelli in Visione personalizzata", si verrà trasferiti all'account Di visione personalizzata appena connesso.

> [!NOTE]
> Attualmente sono supportati solo i modelli creati tramite Video Indexer. I modelli creati tramite Visione personalizzata non saranno disponibili. Inoltre, la procedura consigliata consiste nel modificare i modelli creati tramite Video Indexer solo tramite la piattaforma Video Indexer, poiché le modifiche apportate tramite Custom Vision possono causare risultati imprevisti.

### <a name="create-an-animated-characters-model"></a>Creare un modello di personaggi animati

1. Passare al sito Web di [Video Indexer](https://vi.microsoft.com/) ed eseguire l'accesso.
1. Fare clic sul pulsante di personalizzazione del modello di contenuto nell'angolo superiore destro della pagina.

    ![Personalizzazione del modello di contenuto](./media/animated-characters-recognition/content-model-customization.png)
1. Vai alla scheda **Personaggi animati** nella sezione di personalizzazione del modello.
1. Fare clic su **Aggiungi modello**.
1. Assegnare un nome al modello e fare clic su Invio per salvarne il nome.

> [!NOTE]
> La procedura consigliata consiste nel disporre di un modello di visione personalizzato per ogni serie animata. 

### <a name="index-a-video-with-an-animated-model"></a>Indicizzare un video con un modello animato

1. Fare clic sul pulsante **Carica** dal menu in alto.
1. Scegli un video da caricare (da un file o da un URL).
1. Fare clic su **Opzioni avanzate**.
1. In **Personaggi /personaggi animati** scegliere Modelli di **animazione.**
1. Se si dispone di un modello verrà scelto automaticamente, e se si dispone di più modelli è possibile scegliere quello pertinente dal menu a discesa.
1. Clicca su Carica.
1. Una volta indicizzato il video, vedrai i caratteri rilevati nella sezione **Caratteri animati** nel riquadro **Approfondimenti.**

> [!NOTE] 
> Prima di contrassegnare e addestrare il modello, tutti i personaggi animati saranno denominati "#X sconosciuti". Dopo aver eseguito il training del modello, verranno riconosciuti anche questi.

### <a name="customize-the-animated-characters-models"></a>Personalizzare i modelli di personaggi animati

1. Contrassegnare ed eseguire il training del modello.

    1. Contrassegnare il carattere rilevato modificandone il nome. Una volta che un personaggio viene sottoposto a training nel modello, verrà riconosciuto il video successivo indicizzato con tale modello. 
    1. Per taggare un personaggio animato nel video, vai alla scheda **Insights** e fai clic sul pulsante **Modifica** nell'angolo in alto a destra della finestra.
    1. Nel riquadro **Insights,** fai clic su uno dei personaggi animati rilevati e modificane i nomi da "#X sconosciuti" (o il nome precedentemente assegnato al personaggio).
    1. Dopo aver digitato il nuovo nome, fare clic sull'icona di controllo accanto al nuovo nome. In questo modo il nuovo nome viene salvato nel modello in Video Indexer.
    1. Dopo aver modificato tutti i nomi desiderati, è necessario eseguire il training del modello.

        Aprire la pagina di personalizzazione e fare clic sulla scheda **Caratteri animati,** quindi fare clic sul pulsante **Train** per eseguire il training del modello.
         
        Se hai un account a pagamento, puoi fare clic sul link **Gestisci modelli in Visione del cliente** (come mostrato di seguito). Verrà quindi inoltrato alla pagina del modello in **Visione personalizzata**.
 
        ![Personalizzazione del modello di contenuto](./media/animated-characters-recognition/content-model-customization-tab.png)

     1. Una volta sottoposto a training, qualsiasi video che verrà indicizzato o reindicizzato con tale modello riconoscerà i caratteri sottoposti a training. 
    Gli account a pagamento che hanno accesso al loro account Vision personalizzato possono vedere i modelli e le immagini taggate. Ulteriori informazioni sul miglioramento del [classificatore in Visione personalizzata](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-improving-your-classifier).

1. Eliminare un personaggio animato.

    1. Per eliminare un personaggio animato nelle informazioni dettagliate sui video, vai alla scheda **Insights** e fai clic sul pulsante **Modifica** nell'angolo in alto a destra della finestra.
    1. Scegli il personaggio animato e quindi fai clic sul pulsante **Elimina** sotto il loro nome.

    > [!NOTE]
    > Questo eliminerà le informazioni da questo video, ma non influenzerà il modello.

1. Eliminare un modello.

    1. Fare clic sul pulsante **di personalizzazione del modello** di contenuto nel menu in alto e passare alla scheda Caratteri **animati.**
    1. Fare clic sull'icona con i socielli a destra del modello che si desidera eliminare e quindi sul pulsante Elimina.
    
    * Account a pagamento: il modello verrà disconnesso dall'indicizzatore video e non sarà possibile riconnetterlo.
    * Account di prova: il modello verrà eliminato anche dalla visione doganale. 
    
        > [!NOTE]
        > In un account di prova, hai solo un modello che puoi usare. Dopo averlo eliminato, non è possibile eseguire il training di altri modelli.

## <a name="use-the-animated-character-detection-with-api"></a>Usare il rilevamento dei caratteri animati con l'APIUse the animated character detection with API 

1. Collegare un account Visione personalizzata.

    Se possiedi un account Video Indexer a pagamento, devi prima collegare un account Vision personalizzato. <br/>
    Se non hai già un account Custom Vision, creane uno. Per ulteriori informazioni, consultate [Visione personalizzata.](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/home)

    [Connettere l'account Visione personalizzata utilizzando API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Connect-Custom-Vision-Account?tags=&pattern=&groupBy=tag).
1. Creare un modello di caratteri animati.

    Usare l'API [Crea modello di animazione.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Animation-Model?&groupBy=tag)
1. Indicizzare o reindicizzare un video.

    Usare l'API [di reindicizzazione.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) 
1. Personalizza i modelli di personaggi animati.

    Usare l'API del modello di [animazione del training.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Train-Animation-Model?&groupBy=tag)

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

* Attualmente, la funzionalità di "identificazione dell'animazione" non è supportata nell'area dell'Asia orientale.
* I personaggi che sembrano essere piccoli o lontani nel video potrebbero non essere identificati correttamente se la qualità del video è scadente.
* Si consiglia di utilizzare un modello per set di personaggi animati (ad esempio per una serie animata).

## <a name="next-steps"></a>Passaggi successivi

[Panoramica di Video Indexer](video-indexer-overview.md)
