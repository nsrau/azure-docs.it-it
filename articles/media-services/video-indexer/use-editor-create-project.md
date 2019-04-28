---
title: Usare l'editor di indicizzatore Video per creare progetti
titlesuffix: Azure Media Services
description: In questo argomento viene illustrato come usare l'editor di indicizzatore Video per creare progetti.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 04/02/2019
ms.author: juliako
ms.openlocfilehash: a9d6396cab560a201b98497e787af4b6c7c2dabb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60560937"
---
# <a name="use-the-video-indexer-editor-to-create-projects"></a>Usare l'editor di indicizzatore Video per creare progetti

Sito Web di indicizzatore video, consente di usare informazioni molto dettagliate dei video per: trovare il contenuto dei supporti a destra, individuare le parti che si è interessati e utilizzare i risultati per creare un progetto totalmente nuovo. Una volta creato, il progetto può essere eseguito il rendering e scaricato da Video Indexer e utilizzabile in applicazioni o i flussi di lavoro downstream personalizzati di modifica.

Alcuni scenari in cui si può rivelarsi particolarmente utile sono: 

* Creazione di film evidenziazioni dei rimorchi.
* Utilizzo precedente clip video della durata di cast di notizie.
* Creazione di contenuto più breve destinati a social media.

Questo articolo illustra come creare un progetto da zero, nonché come creare un progetto da un video nell'account.

## <a name="create-new-project-and-manage-videos"></a>Crea nuovo progetto e gestire video

1. Passare al sito Web di [Video Indexer](https://www.videoindexer.ai/) ed eseguire l'accesso.
1. Selezionare il **progetti** scheda. Se è stato creato prima i progetti, noterete tutti gli altri progetti qui.
1. Fare clic su **Crea nuovo progetto**.  

    ![Nuovo progetto](./media/video-indexer-view-edit/new-project.png)
1. Assegnare un nome al progetto facendo clic sull'icona della matita. Sostituire il testo con la dicitura "Senza nome progetto" con il nome del progetto, quindi scegliere il controllo.

    ![Nuovo progetto](./media/video-indexer-view-edit/new-project3.png)
    
### <a name="add-videos-to-the-project"></a>Aggiungere video al progetto

> [!NOTE]
> Attualmente, i progetti possono contenere solo video indicizzati nella stessa lingua. Dopo aver selezionato un video in un linguaggio, è possibile aggiungere i video nell'account in una lingua diversa.

1. Aggiungere video che si desidera lavorare con questo progetto selezionando **aggiungere video**.

    Verranno visualizzati tutti i video nel proprio account e una casella di ricerca con la dicitura "Ricerca per testo, le parole chiave o contenuto visivo". Per cercare video con una specifica persona, etichetta, marchio, parola chiave o occorrenza nella trascrizione e OCR.
    
    Nell'immagine seguente, ad esempio, siamo alla ricerca di video che menzionano "GitHub".
    
    ![GitHub](./media/video-indexer-view-edit/github.png)

    È possibile filtrare ulteriormente i risultati selezionando **filtrare i risultati**. È possibile filtrare per visualizzare i video con una determinata persona in essi contenuti o per specificare che si desidera solo vedere risultati video che sono una in una determinata lingua o dispone di un proprietario specifico. <br/> È anche possibile specificare l'ambito della query. Ad esempio, se si desidera eseguire la ricerca di OCR "GitHub", selezionare **testo Visual**.

    ![Filtro](./media/video-indexer-view-edit/visual-text.png)

    Puoi aggiungere livelli di più filtri alla query. Usare la **+** / **-** pulsanti per aggiungere o rimuovere i filtri. Uso **cancellare i filtri** per rimuovere tutti i filtri.
1. Per aggiungere video, selezionarli e quindi selezionare **Add**.
1. A questo punto, verranno visualizzati tutti i video che scelto. Questi sono i video da cui si desidera selezionare clip per il progetto.

    È possibile riorganizzare l'ordine dei video mediante trascinamento della selezione o la selezione del pulsante di menu di scelta di elenco e selezionando **Sposta giù** oppure **spostare verso l'alto**. Nel menu elenco anche sarà in grado di rimuovere il video da questo progetto. 

    ![Ridisporre](./media/video-indexer-view-edit/rearrange.png)
    
    È possibile aggiungere altri video su questo progetto in qualsiasi momento selezionando **aggiungere video**. È anche possibile aggiungere più occorrenze del video stesso al progetto. Si potrebbe voler eseguire questa operazione se si desidera mostrare un clip da un video e quindi una clip da un altro e quindi un'altra raccolta dal primo video. 

### <a name="select-clips-to-use-in-your-project"></a>Selezionare clip da usare nel progetto

Se si fa clic sulla freccia verso il basso sul lato destro di ogni video, aprirà l'Insights nel video basato su timestamp (clip del video). 

1. Selezionare **Visualizza informazioni dettagliate** personalizzare quali insights si desidera vedere e che non si vuole visualizzare. 

    ![Visualizza informazioni dettagliate](./media/video-indexer-view-edit/insights.png)
1. Per creare query per clip specifica, usare la casella di ricerca con la dicitura "Eseguire ricerche nella trascrizione, testo visual, persone e le etichette".
1. Aggiungere filtri per specificare ulteriormente le informazioni dettagliate su quali scene di cui si sta cercando selezionando **opzioni di filtro**.

    ![Opzioni di filtro](./media/video-indexer-view-edit/filter-options.png)

    Ad esempio, si desideri vedere clip in cui viene menzionato GitHub mentre Donovan Brown è sullo schermo. A tale scopo, è necessario aggiungere un filtro "include" con "People" come tipo di informazioni dettagliate. È quindi necessario immettere "Donovan Brown" nella casella di ricerca per il filtro.
    
    ![Includi](./media/video-indexer-view-edit/include.png)
    
    Se si desidera che clip in cui viene menzionato GitHub mentre Donovan Brown riveste _non_ sullo schermo, è sufficiente modificare il filtro "include" in un filtro "exclude" menu a discesa. 

1. Aggiungere una clip al progetto selezionando il segmento da aggiungere. È possibile deselezionare questa raccolta facendo clic sul segmento di nuovo.
    
    Aggiungere tutti i segmenti di un video, fare clic sull'opzione di menu elenco accanto al video e selezionando **selezionare tutti i segmenti**. 

    ![Aggiungi tutti](./media/video-indexer-view-edit/add-all.png)

    È possibile deselezionare tutti la selezione selezionando annullare la selezione.

> [!TIP]
> Quando si selezionano e ordinamento delle clip, è possibile visualizzare in anteprima il video del lettore sul lato destro della pagina. 

![Anteprima](./media/video-indexer-view-edit/preview.png)

Ricordarsi di salvare il progetto quando si apportano modifiche selezionando **Salva progetto**. 

### <a name="render-and-download-the-project"></a>Eseguire il rendering e scaricare il progetto

> [!NOTE]
> Per l'indicizzatore Video a pagamento di account, il rendering del progetto ha costi di codifica. Gli account di prova indicizzatore video sono limitati a 5 ore per il rendering.

1. Al termine, assicurarsi che il progetto è stato salvato. È ora possibile eseguire il rendering di questo progetto. Selezionare **eseguire il rendering e scaricare**. 

    ![Salva](./media/video-indexer-view-edit/save.png)

    Sarà presente una finestra popup che indica che un indicizzatore Video verrà eseguito il rendering di un file e quindi il collegamento di download verrà inviato al tuo indirizzo e-mail. Selezionare procedere. 
    
    Noterete anche una notifica che il progetto viene eseguito il rendering nella parte superiore della pagina. Al termine viene eseguito il rendering, si noterà una nuova notifica che il progetto è stato eseguito il rendering. Fare clic sulla notifica per scaricare il progetto. Verrà scaricato il progetto in formato mp4.

    ![Operazione eseguito per il rendering](./media/video-indexer-view-edit/rendering-done.png)

1. È possibile accedere a progetti salvati dal **progetti** scheda. 

    Se si seleziona questo progetto, vengono visualizzati tutte le informazioni dettagliate e la sequenza temporale del progetto. Se si seleziona **editor Video**, potrai continuare a inviare modifiche al progetto. Le modifiche includono aggiungendo o rimuovendo i video e clip o ridenominazione del progetto.

    ![Editor video](./media/video-indexer-view-edit/video-editor.png)
     
## <a name="create-a-project-from-your-video"></a>Creare un progetto da un video

È possibile creare un nuovo progetto direttamente da un video nell'account. 

1. Andare alla **libreria** scheda del sito Web del Video Indexer.
1. Aprire il video che si desidera utilizzare per creare il progetto. Nella pagina della sequenza temporale e informazioni dettagliate, selezionare la **editor Video** pulsante.

    Verrà visualizzata la stessa pagina in cui è utilizzato per creare un nuovo progetto. A differenza di nuovo progetto, noterete che i segmenti di insights con timestamp del video, che si era ha iniziato la modifica in precedenza.

## <a name="see-also"></a>Vedere anche 

[Panoramica di Video Indexer](video-indexer-overview.md)

