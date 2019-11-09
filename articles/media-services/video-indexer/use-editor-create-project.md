---
title: Utilizzare l'Editor Video Indexer per creare progetti
titleSuffix: Azure Media Services
description: In questo argomento viene illustrato come utilizzare l'Editor Video Indexer per creare progetti.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/02/2019
ms.author: juliako
ms.openlocfilehash: 9f16ab34dc9b37806f9c58b22a3f02afe839632e
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839174"
---
# <a name="use-the-video-indexer-editor-to-create-projects"></a>Utilizzare l'Editor Video Indexer per creare progetti

Video Indexer sito Web consente di usare le informazioni approfondite dei video per trovare il contenuto multimediale appropriato, individuare le parti a cui si è interessati e usare i risultati per creare un progetto completamente nuovo. Una volta creato, è possibile eseguire il rendering e il download del progetto da Video Indexer e usarlo nelle applicazioni di modifica o nei flussi di lavoro downstream.

Di seguito sono riportati alcuni scenari in cui questa funzionalità può risultare utile: 

* Creazione di filmati principali per i trailer.
* Uso di vecchie clip di video nei cast di notizie.
* Creazione di contenuto più breve per i social media.

Questo articolo illustra come creare un progetto da zero e come creare un progetto da un video nell'account.

## <a name="create-new-project-and-manage-videos"></a>Creazione di un nuovo progetto e gestione di video

1. Passare al sito Web di [Video Indexer](https://www.videoindexer.ai/) ed eseguire l'accesso.
1. Selezionare la scheda **progetti** . Se sono stati creati progetti in precedenza, tutti gli altri progetti vengono visualizzati qui.
1. Fare clic su **Crea nuovo progetto**.  

    ![Nuovo progetto](./media/video-indexer-view-edit/new-project.png)
1. Assegnare un nome al progetto facendo clic sull'icona a matita. Sostituire il testo "progetto senza titolo" con il nome del progetto e fare clic sul segno di spunta.

    ![Nuovo progetto](./media/video-indexer-view-edit/new-project3.png)
    
### <a name="add-videos-to-the-project"></a>Aggiungere video al progetto

> [!NOTE]
> Attualmente, i progetti possono contenere solo video indicizzati nella stessa lingua. Dopo aver selezionato un video in una lingua, non è possibile aggiungere i video nell'account che si trovano in una lingua diversa.

1. Aggiungere i video che si desidera utilizzare in questo progetto selezionando **Aggiungi video**.

    Vengono visualizzati tutti i video nell'account e una casella di ricerca con la dicitura "Cerca testo, parole chiave o contenuto visivo". Per cercare i video con una persona, un'etichetta, un marchio, una parola chiave o un'occorrenza specifici nella trascrizione e nell'OCR.
    
    Nell'immagine seguente, ad esempio, si cercano i video che citano "GitHub".
    
    ![GitHub](./media/video-indexer-view-edit/github.png)

    È possibile filtrare ulteriormente i risultati selezionando **Filtra risultati**. È possibile filtrare per visualizzare i video che contengono una determinata persona o per specificare che si desidera visualizzare solo i risultati video che sono in una determinata lingua o hanno un proprietario specifico. <br/> È inoltre possibile specificare l'ambito della query. Se ad esempio si vuole cercare "GitHub" nell'OCR, selezionare **testo visivo**.

    ![Filtro](./media/video-indexer-view-edit/visual-text.png)

    È possibile eseguire il layer di più filtri per la query. Usare i pulsanti **+** / **-** per aggiungere o rimuovere i filtri. Utilizzare **Cancella filtri** per rimuovere tutti i filtri.
1. Per aggiungere video, selezionarli e quindi selezionare **Aggiungi**.
1. A questo punto, verranno visualizzati tutti i video scelti. Questi sono i video da cui verranno selezionati i clip per il progetto.

    È possibile ridisporre l'ordine dei video trascinando o facendo clic sul pulsante di menu elenco e scegliendo **Sposta giù** o **Sposta su**. Dal menu elenco sarà anche possibile rimuovere il video dal progetto. 

    ![Riorganizzare](./media/video-indexer-view-edit/rearrange.png)
    
    È possibile aggiungere altri video al progetto in qualsiasi momento selezionando **Aggiungi video**. È anche possibile aggiungere più occorrenze dello stesso video al progetto. Questa operazione può essere eseguita se si desidera visualizzare una clip da un video e quindi una clip da un'altra, quindi un altro clip dal primo video. 

### <a name="select-clips-to-use-in-your-project"></a>Selezionare le clip da usare nel progetto

Se si fa clic sulla freccia verso il basso sul lato destro di ogni video, si apriranno le informazioni dettagliate nel video in base ai timestamp (clip del video). 

1. Selezionare **Visualizza informazioni dettagliate** per personalizzare le informazioni che si desidera visualizzare e quali non si desidera visualizzare. 

    ![Visualizza informazioni dettagliate](./media/video-indexer-view-edit/insights.png)
1. Per creare query per clip specifiche, utilizzare la casella di ricerca "Cerca in trascrizione, testo visivo, persone ed etichette".
1. Aggiungere i filtri per specificare ulteriormente i dettagli sulle scene cercate selezionando le **Opzioni di filtro**.

    ![Opzioni di filtro](./media/video-indexer-view-edit/filter-options.png)

    Ad esempio, è possibile visualizzare i clip in cui GitHub è indicato mentre Donovan Brown è sullo schermo. A tale proposito, è necessario aggiungere un filtro "include" con "people" come tipo di informazioni dettagliate. È quindi necessario digitare "Donovan Brown" nella casella di ricerca per il filtro.
    
    ![Includi](./media/video-indexer-view-edit/include.png)
    
    Se si desiderano le clip in cui GitHub è indicato mentre Donovan Brown _non_ è visualizzato sullo schermo, è sufficiente modificare il filtro "Includi" in un filtro "Escludi" utilizzando l'elenco a discesa. 

1. Aggiungere una clip al progetto selezionando il segmento che si vuole aggiungere. È possibile deselezionare il clip facendo clic di nuovo sul segmento.
    
    Per aggiungere tutti i segmenti di un video, fare clic sull'opzione di menu elenco accanto al video e **selezionare Seleziona tutti i segmenti**. 

    ![Aggiungi tutto](./media/video-indexer-view-edit/add-all.png)

    È possibile cancellare tutte le selezioni selezionando Cancella selezione.

> [!TIP]
> Quando si selezionano e si ordinano i clip, è possibile visualizzare in anteprima il video nel lettore sul lato destro della pagina. 

![Preview](./media/video-indexer-view-edit/preview.png)

Ricordarsi di salvare il progetto quando si apportano modifiche selezionando **Salva progetto**. 

### <a name="render-and-download-the-project"></a>Eseguire il rendering e scaricare il progetto

> [!NOTE]
> Per Video Indexer account a pagamento, il rendering del progetto comporta costi di codifica. Video Indexer account di valutazione sono limitati a 5 ore di rendering.

1. Al termine, verificare che il progetto sia stato salvato. È ora possibile eseguire il rendering di questo progetto. Selezionare **il rendering e il download**. 

    ![Salva](./media/video-indexer-view-edit/save.png)

    Sarà presente un popup che indica che l'indicizzatore video eseguirà il rendering di un file e il collegamento per il download verrà inviato al messaggio di posta elettronica. Selezionare continua. 
    
    Verrà visualizzata anche una notifica che indica che è in corso il rendering del progetto nella parte superiore della pagina. Al termine del rendering, verrà visualizzata una nuova notifica che indica che il rendering del progetto è stato completato correttamente. Fare clic sulla notifica per scaricare il progetto. Il progetto verrà scaricato in formato MP4.

    ![Rendering completato](./media/video-indexer-view-edit/rendering-done.png)

1. È possibile accedere ai progetti salvati dalla scheda **progetti** . 

    Se si seleziona questo progetto, vengono visualizzate tutte le informazioni dettagliate e la cronologia del progetto. Se si seleziona **Editor video**, è possibile continuare a apportare modifiche al progetto. Le modifiche includono l'aggiunta o la rimozione di video e clip o la ridenominazione del progetto.

    ![Editor video](./media/video-indexer-view-edit/video-editor.png)
     
## <a name="create-a-project-from-your-video"></a>Creare un progetto dal video

È possibile creare un nuovo progetto direttamente da un video nell'account. 

1. Passare alla scheda **Library (libreria** ) del sito web video Indexer.
1. Aprire il video che si vuole usare per creare il progetto. Nella pagina informazioni dettagliate e sequenza temporale selezionare il pulsante **Editor video** .

    Verrà quindi utilizzata la stessa pagina utilizzata per creare un nuovo progetto. A differenza del nuovo progetto, vengono visualizzati i segmenti timestamped Insights del video, che è stata avviata la modifica in precedenza.

## <a name="see-also"></a>Vedere anche

[Panoramica di Video Indexer](video-indexer-overview.md)

