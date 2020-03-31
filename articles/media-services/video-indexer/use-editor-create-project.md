---
title: Utilizzare l'editor dell'indicizzatore video per creare progetti
titleSuffix: Azure Media Services
description: In questo argomento viene illustrato come utilizzare l'editor dell'indicizzatore video per creare progetti.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/02/2019
ms.author: juliako
ms.openlocfilehash: 9f16ab34dc9b37806f9c58b22a3f02afe839632e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73839174"
---
# <a name="use-the-video-indexer-editor-to-create-projects"></a>Utilizzare l'editor dell'indicizzatore video per creare progetti

Sito Web dell'indicizzatore video, ti consente di utilizzare le informazioni approfondite dei tuoi video: trovare i contenuti multimediali giusti, individuare le parti che ti interessano e utilizzare i risultati per creare un progetto completamente nuovo. Una volta creato, il progetto può essere sottoposto a rendering e scaricato da Video Indexer ed essere utilizzato nelle proprie applicazioni di modifica o flussi di lavoro a valle.

Alcuni scenari in cui questa funzionalità può essere utile sono alcuni scenari che possono essere utili:Some scenarios where you may find this feature useful are: 

* Creazione di evidenziazioni di film per i trailer.
* Utilizzando vecchi clip di video in cast di notizie.
* Creazione di contenuti più brevi per i social media.

Questo articolo mostra come creare un progetto da zero e anche come creare un progetto da un video nel tuo account.

## <a name="create-new-project-and-manage-videos"></a>Creare un nuovo progetto e gestire i video

1. Passare al sito Web di [Video Indexer](https://www.videoindexer.ai/) ed eseguire l'accesso.
1. Selezionare la scheda **Progetti.** Se hai creato progetti in precedenza, vedrai tutti gli altri progetti qui.
1. Fare clic su **Crea nuovo progetto**.  

    ![Nuovo progetto](./media/video-indexer-view-edit/new-project.png)
1. Dai un nome al tuo progetto cliccando sull'icona a matita. Sostituire il testo "Progetto senza titolo" con il nome del progetto e fare clic sull'assegno.

    ![Nuovo progetto](./media/video-indexer-view-edit/new-project3.png)
    
### <a name="add-videos-to-the-project"></a>Aggiungere video al progetto

> [!NOTE]
> Attualmente, i progetti possono contenere solo video indicizzati nella stessa lingua. Una volta selezionato un video in una lingua, non puoi aggiungere i video nel tuo account che si trovano in una lingua diversa.

1. Aggiungere i video che si desidera utilizzare in questo progetto selezionando **Aggiungi video**.

    Vedrai tutti i video nel tuo account e una casella di ricerca che dice "Cerca testo, parole chiave o contenuti visivi". Per cercare i video con una persona, un'etichetta, un marchio, una parola chiave o un'occorrenza specificati nella trascrizione e nell'OCR.
    
    Ad esempio, nell'immagine qui sotto, stiamo cercando video che menzionano "GitHub".
    
    ![GitHub](./media/video-indexer-view-edit/github.png)

    È possibile filtrare ulteriormente i risultati selezionando **Filtra risultati**. Puoi filtrare per mostrare i video che conserino una determinata persona o per specificare che desideri visualizzare solo i risultati dei video che rientrano in una determinata lingua o hanno un proprietario specifico. <br/> È inoltre possibile specificare l'ambito della query. Ad esempio, se si desidera cercare "GitHub" nell'OCR, selezionare **Visual Text**.

    ![Filtro](./media/video-indexer-view-edit/visual-text.png)

    È possibile sovrapporre più filtri alla query. Utilizzare **+** / **-** i pulsanti per aggiungere/rimuovere filtri. Utilizzare **i filtri Cancella** per rimuovere tutti i filtri.
1. Per aggiungere video, selezionarli e quindi selezionare **Aggiungi**.
1. Ora, vedrai tutti i video che hai scelto. Questi sono i video da cui si sta per selezionare clip per il progetto.

    È possibile modificare l'ordine dei video trascinandoli o selezionando il pulsante del menu elenco e selezionando **Sposta in basso** o Sposta **su**. Dal menu elenco, potrai anche rimuovere il video da questo progetto. 

    ![Riorganizzare](./media/video-indexer-view-edit/rearrange.png)
    
    Hai la possibilità di aggiungere altri video a questo progetto in qualsiasi momento selezionando **Aggiungi video**. È inoltre possibile aggiungere più occorrenze dello stesso video al progetto. Potresti volerlo fare se vuoi mostrare una clip da un video e poi una clip da un altro e poi un'altra clip dal primo video. 

### <a name="select-clips-to-use-in-your-project"></a>Selezionare le clip da utilizzare nel progetto

Se si fa clic sulla freccia verso il basso sul lato destro di ogni video, si apriranno le informazioni dettagliate nel video in base ai timestamp (clip del video). 

1. Selezionare **Visualizza dati statistici** per personalizzare le informazioni dettagliate che si desidera visualizzare e quali non si desidera visualizzare. 

    ![Visualizza informazioni dettagliate](./media/video-indexer-view-edit/insights.png)
1. Per creare query per clip specifici, utilizzare la casella di ricerca "Cerca nella trascrizione, nel testo visivo, nelle persone e nelle etichette".
1. Aggiungere filtri per specificare ulteriormente i dettagli sulle scene che si stanno cercando selezionando **Opzioni filtro**.

    ![Opzioni di filtro](./media/video-indexer-view-edit/filter-options.png)

    Ad esempio, potresti voler vedere le clip in cui GitHub è menzionato mentre Donovan Brown è sullo schermo. A tale scopo, è necessario aggiungere un filtro "include" con "Persone" come tipo di informazioni dettagliate. È quindi necessario digitare "Donovan Brown" nella casella di ricerca per il filtro.
    
    ![Includi](./media/video-indexer-view-edit/include.png)
    
    Se si desidera clip in cui GitHub è menzionato mentre Donovan Brown _non_ è sullo schermo, è sufficiente modificare il filtro "include" in un filtro "escludere" utilizzando il menu a discesa. 

1. Aggiungere una clip al progetto selezionando il segmento da aggiungere. È possibile deselezionare questa clip facendo nuovamente clic sul segmento.
    
    Aggiungere tutti i segmenti di un video facendo clic sull'opzione del menu elenco accanto al video e selezionando **Seleziona tutti i segmenti**. 

    ![Aggiungi tutto](./media/video-indexer-view-edit/add-all.png)

    È possibile cancellare tutta la selezione selezionando Cancella selezione.

> [!TIP]
> Durante la selezione e l'ordine delle clip, è possibile visualizzare in anteprima il video nel lettore sul lato destro della pagina. 

![Anteprima](./media/video-indexer-view-edit/preview.png)

Ricordarsi di salvare il progetto quando si apportano modifiche selezionando **Salva progetto**. 

### <a name="render-and-download-the-project"></a>Rendering e download del progetto

> [!NOTE]
> Per gli account a pagamento dell'indicizzatore video, il rendering del progetto ha costi di codifica. Gli account di prova dell'indicizzatore video sono limitati a 5 ore di rendering.

1. Al termine, assicurarsi che il progetto sia stato salvato. È ora possibile eseguire il rendering di questo progetto. Selezionare **Rendering e download**. 

    ![Salvare](./media/video-indexer-view-edit/save.png)

    Ci sarà un popup che ti dice che l'indicizzatore video eseguirà il rendering di un file e quindi il link di download verrà inviato alla tua e-mail. Selezionare Continua. 
    
    Verrà inoltre visualizzata una notifica che indica che viene eseguito il rendering del progetto nella parte superiore della pagina. Al termine del rendering, verrà visualizzata una nuova notifica che indica che il progetto è stato eseguito correttamente il rendering. Fare clic sulla notifica per scaricare il progetto. Scaricherà il progetto in formato mp4.

    ![Rendering completato](./media/video-indexer-view-edit/rendering-done.png)

1. È possibile accedere ai progetti salvati dalla scheda **Progetti.** 

    Se si seleziona questo progetto, vengono visualizzate tutte le informazioni dettagliate e la sequenza temporale di questo progetto. Se si seleziona **Editor video**, è possibile continuare ad apportare modifiche a questo progetto. Le modifiche includono l'aggiunta o la rimozione di video e clip o la ridenominazione del progetto.

    ![Editor video](./media/video-indexer-view-edit/video-editor.png)
     
## <a name="create-a-project-from-your-video"></a>Creare un progetto dal video

Puoi creare un nuovo progetto direttamente da un video nel tuo account. 

1. Vai alla scheda **Libreria** del sito Web Indicizzatore video.
1. Aprire il video che si desidera utilizzare per creare il progetto. Nella pagina Informazioni dettagliate e sequenza temporale seleziona il pulsante **Editor video.**

    Verrà visualizzata la stessa pagina utilizzata per creare un nuovo progetto. A differenza del nuovo progetto, vengono visualizzati i segmenti di informazioni dettagliate con timestamp del video, che in precedenza avevi iniziato a modificare.

## <a name="see-also"></a>Vedere anche

[Panoramica di Video Indexer](video-indexer-overview.md)

