---
title: Utilizzare l'Editor Video Indexer per creare progetti e aggiungere clip video
titleSuffix: Azure Media Services
description: In questo argomento viene illustrato come utilizzare l'Editor Video Indexer per creare progetti e aggiungere clip video.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/28/2020
ms.author: juliako
ms.openlocfilehash: 3a3ac3f2db4e23f03f83a98bee0aceaddef9f889
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96433431"
---
# <a name="add-video-clips-to-your-projects"></a>Aggiungere clip video ai progetti

Video Indexer sito Web consente di usare le informazioni approfondite dei video per trovare il contenuto multimediale appropriato, individuare le parti a cui si è interessati e usare i risultati per creare un progetto completamente nuovo. 

Una volta creato, è possibile eseguire il rendering e il download del progetto da Video Indexer e usarlo nelle applicazioni di modifica o nei flussi di lavoro downstream.

Di seguito sono riportati alcuni scenari in cui questa funzionalità può risultare utile: 

* Creazione di filmati principali per i trailer.
* Uso di vecchie clip di video nei cast di notizie.
* Creazione di contenuto più breve per i social media.

Questo articolo illustra come creare un progetto e aggiungere clip selezionate dai video al progetto. 

## <a name="create-new-project-and-manage-videos"></a>Creazione di un nuovo progetto e gestione di video

1. Passare al sito Web di [Video Indexer](https://www.videoindexer.ai/) ed eseguire l'accesso.
1. Selezionare la scheda **progetti** . Se sono stati creati progetti in precedenza, tutti gli altri progetti vengono visualizzati qui.
1. Fare clic su **Crea nuovo progetto**.  

    :::image type="content" source="./media/video-indexer-view-edit/new-project.png" alt-text="Creare un nuovo progetto":::
1. Assegnare un nome al progetto facendo clic sull'icona a matita. Sostituire il testo "progetto senza titolo" con il nome del progetto e fare clic sul segno di spunta.

    :::image type="content" source="./media/video-indexer-view-edit/new-project-edit-name.png" alt-text="Nuovo progetto":::
    
### <a name="add-videos-to-the-project"></a>Aggiungere video al progetto

> [!NOTE]
> Attualmente, i progetti possono contenere solo video indicizzati nella stessa lingua. </br>Quando si seleziona un video in una lingua, non è possibile aggiungere i video nell'account che si trovano in una lingua diversa, i video con altre lingue saranno disattivati o disabilitati.

1. Aggiungere i video che si desidera utilizzare in questo progetto selezionando **Aggiungi video**.

    Vengono visualizzati tutti i video nell'account e una casella di ricerca con la dicitura "Cerca testo, parole chiave o contenuto visivo". È possibile cercare video con una persona, un'etichetta, un marchio, una parola chiave o un'occorrenza specifici nella trascrizione e nell'OCR.
    
    Nell'immagine seguente, ad esempio, sono stati cercati i video che citano "visione personalizzata" solo nella trascrizione (usare il **filtro** se si desidera filtrare i risultati della ricerca).
    
    :::image type="content" source="./media/video-indexer-view-edit/custom-vision.png" alt-text="Screenshot mostra la ricerca di video che citano la visione personalizzata":::
1. Fare clic su **Aggiungi** per aggiungere video al progetto.
1. A questo punto, verranno visualizzati tutti i video scelti. Questi sono i video da cui verranno selezionati i clip per il progetto.

    È possibile ridisporre l'ordine dei video trascinando o facendo clic sul pulsante di menu elenco e scegliendo **Sposta giù** o **Sposta su**. Dal menu elenco sarà anche possibile rimuovere il video dal progetto. 
    
    È possibile aggiungere altri video al progetto in qualsiasi momento selezionando **Aggiungi video**. È anche possibile aggiungere più occorrenze dello stesso video al progetto. Questa operazione può essere eseguita se si desidera visualizzare una clip da un video e quindi una clip da un'altra, quindi un altro clip dal primo video. 

### <a name="select-clips-to-use-in-your-project"></a>Selezionare le clip da usare nel progetto

Se si fa clic sulla freccia verso il basso sul lato destro di ogni video, si apriranno le informazioni dettagliate nel video in base ai timestamp (clip del video). 

1. Per creare query per clip specifiche, utilizzare la casella di ricerca "Cerca in trascrizione, testo visivo, persone ed etichette".
1. Selezionare **Visualizza informazioni dettagliate** per personalizzare le informazioni che si desidera visualizzare e quali non si desidera visualizzare. 

    :::image type="content" source="./media/video-indexer-view-edit/search-try-cognitive-services.png" alt-text="Screenshot mostra la ricerca di video che dicono provare i servizi cognitivi":::
1. Aggiungere i filtri per specificare ulteriormente i dettagli sulle scene cercate selezionando le **Opzioni di filtro**.

    È possibile aggiungere più filtri. 
1. Quando si è soddisfatti dei risultati, selezionare i clip che si desidera aggiungere al progetto selezionando il segmento che si desidera aggiungere. È possibile deselezionare il clip facendo clic di nuovo sul segmento.
    
    Aggiungere tutti i segmenti di un video (o tutti quelli restituiti dopo la ricerca) facendo clic sull'opzione di menu elenco accanto al video e selezionando **Seleziona tutto**. 

Quando si selezionano e si ordinano i clip, è possibile visualizzare in anteprima il video nel lettore sul lato destro della pagina. 

> [!IMPORTANT]
> Ricordarsi di salvare il progetto quando si apportano modifiche selezionando **Salva progetto** nella parte superiore della pagina. 

### <a name="render-and-download-the-project"></a>Eseguire il rendering e scaricare il progetto

> [!NOTE]
> Per Video Indexer account a pagamento, il rendering del progetto comporta costi di codifica. Video Indexer account di valutazione sono limitati a 5 ore di rendering.

1. Al termine, verificare che il progetto sia stato salvato. È ora possibile eseguire il rendering di questo progetto. Fare clic su **Render**, viene visualizzata una finestra di dialogo popup che indica che video Indexer eseguirà il rendering di un file e il collegamento di download verrà inviato al messaggio di posta elettronica. Selezionare Continua. 

    :::image type="content" source="./media/video-indexer-view-edit/render-download.png" alt-text="Screenshot Mostra Video Indexer con l'opzione per eseguire il rendering e il download del progetto":::
    
    Verrà visualizzata anche una notifica che indica che è in corso il rendering del progetto nella parte superiore della pagina. Al termine del rendering, verrà visualizzata una nuova notifica che indica che il rendering del progetto è stato completato correttamente. Fare clic sulla notifica per scaricare il progetto. Il progetto verrà scaricato in formato MP4.
1. È possibile accedere ai progetti salvati dalla scheda **progetti** . 

    Se si seleziona questo progetto, vengono visualizzate tutte le informazioni dettagliate e la cronologia del progetto. Se si seleziona **Editor video**, è possibile continuare a apportare modifiche al progetto. Le modifiche includono l'aggiunta o la rimozione di video e clip o la ridenominazione del progetto.
    
## <a name="create-a-project-from-your-video"></a>Creare un progetto dal video

È possibile creare un nuovo progetto direttamente da un video nell'account. 

1. Passare alla scheda **Library (libreria** ) del sito web video Indexer.
1. Aprire il video che si vuole usare per creare il progetto. Nella pagina informazioni dettagliate e sequenza temporale selezionare il pulsante **Editor video** .

    Verrà quindi utilizzata la stessa pagina utilizzata per creare un nuovo progetto. A differenza del nuovo progetto, vengono visualizzati i segmenti timestamped Insights del video, che è stata avviata la modifica in precedenza.

## <a name="see-also"></a>Vedere anche

[Panoramica di Video Indexer](video-indexer-overview.md)

