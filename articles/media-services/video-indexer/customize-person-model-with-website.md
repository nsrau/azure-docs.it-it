---
title: Usare il sito Web di Video Indexer per personalizzare un modello delle persone - Azure
titleSuffix: Azure Media Services
description: Questo articolo illustra come personalizzare un modello delle persone con il sito Web di Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 060d94d6181e894c18d268845b48eb802c52730c
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838284"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>Personalizzare un modello delle persone con il sito Web di Video Indexer

Video Indexer supporta il riconoscimento celebrità per il contenuto video. La funzionalità di identificazione di celebrità include circa un milione di visi basandosi su origini dati di uso comune, ad esempio IMDB, Wikipedia e i principali influencer di LinkedIn. Per una panoramica dettagliata, vedere [personalizzare un modello di persona in video Indexer](customize-person-model-overview.md).

È possibile usare il sito Web di Video Indexer per modificare i visi rilevati in un video, come descritto in questo argomento. È anche possibile usare l'API, come descritto in [personalizzare un modello di persona usando le API](customize-person-model-with-api.md).

## <a name="central-management-of-person-models-in-your-account"></a>Gestione centrale dei modelli di persona nell'account

1. Per visualizzare, modificare ed eliminare i modelli Person nell'account, passare al sito Web Video Indexer ed eseguire l'accesso.
2. Fare clic sul pulsante personalizzazione modello di contenuto nell'angolo superiore destro della pagina.

    ![Personalizzazione del modello di contenuto](./media/customize-face-model/content-model-customization.png)
3. Selezionare la scheda People (persone).

    Nel tuo account viene visualizzato il modello person predefinito. Il modello person predefinito contiene i visi che potrebbero essere stati modificati o modificati nelle informazioni dettagliate dei video per i quali non è stato specificato un modello Person personalizzato durante l'indicizzazione. 

    Se sono stati creati altri modelli di persona, verranno elencati anche in questa pagina. 

    ![Personalizzazione del modello di contenuto](./media/customize-face-model/content-model-customization-people-tab.png)

## <a name="create-a-new-person-model"></a>Creare un nuovo modello Persona

1. Fare clic sul pulsante **+ Aggiungi modello** .

    ![Aggiungi una nuova persona](./media/customize-face-model/add-new-person.png)
2. Immettere il nome del modello e fare clic sul pulsante con il segno di spunta accanto al nome.

    ![Aggiungi una nuova persona](./media/customize-face-model/add-new-person2.png)

    È stato creato un nuovo modello person. È ora possibile aggiungere visi al nuovo modello person.
3. Fare clic sul pulsante di menu elenco e scegliere **+ Aggiungi persona**.

    ![Aggiungi una nuova persona](./media/customize-face-model/add-new-person3.png)
    
## <a name="add-a-new-person-to-a-person-model"></a>Aggiungere una nuova persona a un modello person

> [!NOTE]
> Video Indexer consente di aggiungere più persone con lo stesso nome in un modello di persona. Tuttavia, è consigliabile assegnare nomi unque a ogni persona nel modello per l'usabilità e la chiarezza.

1. Per aggiungere un nuovo volto a un modello person, fare clic sul pulsante del menu elenco accanto al modello person a cui si desidera aggiungere la faccia.
1. Fare clic su **+ Aggiungi persona** dal menu.

    ![Aggiungere un nuovo volto alla persona](./media/customize-face-model/add-new-face.png)
 
    Verrà visualizzata una finestra popup in cui è necessario compilare i dettagli della persona. Digitare il nome della persona e fare clic sul pulsante con il segno di spunta.

    ![Aggiungere un nuovo volto alla persona](./media/customize-face-model/add-new-face2.png)
 
È quindi possibile scegliere da Esplora file o trascinare e rilasciare le immagini facciali della faccia. Video Indexer accetta tutti i tipi di file di immagine standard (ad esempio, JPG, PNG e altro).

Video Indexer dovrebbe essere in grado di rilevare le occorrenze di questa persona nei video futuri indicizzati e nei video attuali già indicizzati, usando il modello person a cui è stato aggiunto questo nuovo volto. Il riconoscimento della persona nei video correnti potrebbe richiedere del tempo per essere applicato, perché si tratta di un processo batch.


## <a name="rename-a-person-model"></a>Rinominare un modello person

È possibile rinominare qualsiasi modello Person nell'account, incluso il modello person predefinito. Anche se si rinomina il modello person predefinito, verrà comunque utilizzato come modello di persona predefinito nell'account.

1. Fare clic sul pulsante di menu elenco accanto al modello Person che si desidera rinominare.
2. Scegliere **Rinomina** dal menu.

    ![Rinominare una persona](./media/customize-face-model/rename-person.png)
3. Fare clic sul nome corrente del modello e digitare il nuovo nome.

    ![Rinominare una persona](./media/customize-face-model/rename-person2.png)
4. Fare clic sul pulsante con il segno di spunta per rinominare il modello.

## <a name="delete-a-person-model"></a>Eliminare un modello Persona

È possibile eliminare qualsiasi modello di persona creato nell'account. Tuttavia, non è possibile eliminare il modello person predefinito.

1. Fare clic su **Elimina** nel menu.

    ![Eliminare una persona](./media/customize-face-model/delete-person.png)
    
    Verrà visualizzata una finestra popup che informa che questa azione eliminerà il modello Person e tutti gli utenti e i file in esso contenuti. Questa azione non può essere annullata. 

    ![Eliminare una persona](./media/customize-face-model/delete-person2.png)
1. In caso di dubbi, fare di nuovo clic su Elimina.

> [!NOTE]
> I video esistenti indicizzati con questo modello di persona (ora eliminato) non supportano la possibilità di aggiornare i nomi dei visi visualizzati nel video. Sarà possibile modificare i nomi dei visi in questi video solo dopo averli reindicizzati usando un altro modello di persona. Se si esegue la reindicizzazione senza specificare un modello person, verrà utilizzato il modello predefinito. 

## <a name="manage-existing-people-in-a-person-model"></a>Gestire persone esistenti in un modello di persona

Per esaminare il contenuto dei modelli di persona, fare clic sulla freccia accanto al nome del modello person.
Nell'elenco a discesa vengono visualizzati tutti gli utenti del modello di persona specifico. Se si fa clic sul pulsante di menu elenco accanto a ogni persona, vengono visualizzate le opzioni Gestisci, Rinomina ed Elimina.  

![Aggiungere un nuovo volto alla persona](./media/customize-face-model/manage-people.png)

### <a name="rename-a-person"></a>Rinominare una persona

1. Per rinominare una persona nel modello person, fare clic sul pulsante di menu elenco e scegliere **Rinomina** dal menu elenco.
1. Fare clic sul nome corrente della persona e digitare il nuovo nome.
1. Fare clic sul pulsante con il segno di spunta e la persona verrà rinominata.

### <a name="delete-a-person"></a>Eliminare una persona

1. Per eliminare una persona dal modello di persona, fare clic sul pulsante di menu elenco e scegliere **Elimina** dal menu elenco.
1. Una finestra popup indica che questa azione eliminerà la persona e che questa azione non può essere annullata.
1. Fare di nuovo clic su Elimina per rimuovere la persona dal modello person.

### <a name="manage-a-person"></a>Gestire una persona

Se si fa clic su **Gestisci**, vengono visualizzati tutti i visi da cui viene eseguito il training del modello di persona. Questi visi provengono da occorrenze di tale persona nei video che usano questo modello di persona o da immagini caricate manualmente. 

È possibile aggiungere più visi alla persona facendo clic su Aggiungi immagini.

È possibile utilizzare il riquadro Gestisci per rinominare la persona ed eliminare la persona dal modello person.

## <a name="use-a-person-model-to-index-a-video"></a>Usare un modello di persona per indicizzare un video

È possibile usare un modello person per indicizzare il nuovo video assegnando il modello person durante il caricamento del video.

Per usare il modello di persona in un nuovo video, seguire questa procedura:

1. Fare clic sul pulsante **carica** nella parte superiore della pagina.

    ![Carica](./media/customize-face-model/upload.png)
1. Rilasciare il file video nel cerchio o cercare il file.
1. Fare clic sulla freccia Opzioni avanzate.

    ![Carica](./media/customize-face-model/upload2.png)
1. Fare clic sull'elenco a discesa e selezionare il modello person creato.

    ![Carica](./media/customize-face-model/upload3.png)
1. Fare clic sull'opzione Upload (carica) nella parte inferiore della pagina e il nuovo video verrà indicizzato usando il modello Person (persona).

Se non si specifica un modello di persona durante il caricamento, Video Indexer indicizza il video usando il modello person predefinito nell'account.

## <a name="use-a-person-model-to-reindex-a-video"></a>Usare un modello di persona per reindicizzare un video 

Per usare un modello di persona per reindicizzare un video nella raccolta, accedere ai video dell'account nella Video Indexer home page e passare il puntatore del mouse sul nome del video che si vuole riindicizzare.

Vengono visualizzate le opzioni per modificare, eliminare e reindicizzare il video. 

1. Fare clic sull'opzione per reindicizzare il video.

    ![Reindicizzare](./media/customize-face-model/reindex.png)

    È ora possibile selezionare il modello di persona con cui reindicizzare il video.
1. Fare clic sull'elenco a discesa e selezionare il modello Person che si desidera utilizzare. 

    ![Reindicizzare](./media/customize-face-model/reindex2.png)

1. Fai clic sul pulsante **REINDEX** e il video verrà reindicizzato usando il tuo modello person.

Qualsiasi nuova modifica apportata ai visi rilevati e riconosciuti nel video appena reindicizzato verrà salvata nel modello person usato per reindicizzare il video.

## <a name="managing-people-in-your-videos"></a>Gestione di persone nei video

È possibile gestire i visi rilevati e gli utenti che sono stati riconosciuti nei video indicizzati modificando ed eliminando i visi.

Eliminando un volto, viene rimosso un volto specifico dalle informazioni dettagliate del video.

Modifica di un volto, rinomina una faccia rilevata e possibilmente riconosciuta nel video. Quando si modifica una faccia nel video, il nome viene salvato come voce Person nel modello person assegnato al video durante il caricamento e l'indicizzazione.

Se non si assegna un modello di persona al video durante il caricamento, la modifica viene salvata nel modello person predefinito del proprio account.

### <a name="edit-a-face"></a>Modificare un viso


> [!NOTE]
> Se un modello Person ha due o più persone diverse con lo stesso nome, non sarà possibile contrassegnare tale nome all'interno dei video che usano il modello person. Sarà possibile apportare modifiche solo alle persone che condividono tale nome nella scheda people della pagina di personalizzazione del modello di contenuto in Video Indexer. Per questo motivo, è consigliabile assegnare nomi univoci a ogni persona nel modello person.

1. Passare al sito Web di Video Indexer ed eseguire l'accesso.
1. Cercare un video da visualizzare e modificare nell'account.
1. Per modificare una faccia nel video, passare alla scheda Insights e fare clic sull'icona a matita nell'angolo superiore destro della finestra.

    ![Modificare una faccia nel video](./media/customize-face-model/edit-face.png)
1. Fare clic su uno qualsiasi dei visi rilevati e cambiare i relativi nomi da "Sconosciuto #X" oppure dal nome precedentemente assegnato al viso. 
1. Dopo aver digitato il nuovo nome, fare clic sull'icona di controllo accanto al nuovo nome. In questo modo, il nuovo nome viene salvato e vengono riconosciute e denominate tutte le occorrenze di questo aspetto negli altri video correnti e nei video futuri caricati. Il riconoscimento del volto negli altri video correnti potrebbe richiedere del tempo per essere applicato, perché si tratta di un processo batch.

Se si nomina un volto con il nome di una persona esistente nel modello person usato dal video, in questo modo le immagini del viso rilevate vengono unite da questo video di tale persona con quello già esistente nel modello. Se si rinomina un volto con un nome completamente nuovo, verrà creata una nuova voce Person nel modello person usato dal video. 

    ![Edit a face in your video](./media/customize-face-model/edit-face2.png)

### <a name="delete-a-face"></a>Eliminare un viso

Per eliminare un volto rilevato nel video, passare al riquadro Insights e fare clic sull'icona a matita nell'angolo superiore destro del riquadro. Fare clic sull'opzione Elimina sotto il nome della faccia. In questo modo il viso rilevato verrà rimosso dal video. Il volto della persona sarà ancora rilevato negli altri video in cui viene visualizzato, ma è possibile eliminarlo anche da questi video dopo che sono stati indicizzati. La persona, se denominata, continuerà a esistere anche nel modello person usato per indicizzare il video da cui è stato eliminato il volto, a meno che non si elimini in modo specifico la persona dal modello person.

![Eliminare una faccia nel video](./media/customize-face-model/delete-face.png)


## <a name="next-steps"></a>Passaggi successivi

[Personalizzare il modello delle persone usando le API](customize-person-model-with-api.md)
