---
title: Usare il sito Web di Video Indexer per personalizzare un modello delle persone - Azure
titlesuffix: Azure Media Services
description: Questo articolo illustra come personalizzare un modello delle persone con il sito Web di Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 37e556ca458a5b0a171664e089d39cfb448d5f96
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65800109"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>Personalizzare un modello delle persone con il sito Web di Video Indexer

Video Indexer supporta riconoscimento delle celebrità di contenuto video. La funzionalità di identificazione di celebrità include circa un milione di visi basandosi su origini dati di uso comune, ad esempio IMDB, Wikipedia e i principali influencer di LinkedIn. Per una panoramica dettagliata, vedere [personalizzare un modello di persona in Video Indexer](customize-person-model-overview.md).

È possibile usare il sito Web di Video Indexer per modificare i visi rilevati in un video, come descritto in questo argomento. È anche possibile usare l'API, come descritto in [personalizzare un modello di persona usando le API](customize-person-model-with-api.md).

## <a name="central-management-of-person-models-in-your-account"></a>Gestione centrale dei modelli di persona nel tuo account

1. Per visualizzare, modificare, eliminare i modelli di persona nel proprio account, passare al sito Web di Video Indexer e accedere.
2. Fare clic sul pulsante di personalizzazione modello di contenuto nell'angolo superiore destro della pagina.

    ![Personalizzazione del modello di contenuto](./media/customize-face-model/content-model-customization.png)
3. Selezionare la scheda utenti.

    Si noterà il modello di utente predefinito nell'account. Il modello di utente predefinito contiene tutti i volti potrebbe aver modificato o modificare le informazioni dettagliate del video per cui non è stato specificato un modello di persona personalizzato durante l'indicizzazione. 

    Se sono stati creati altri modelli di persona, questi verranno elencati in questa pagina anche. 

    ![Personalizzazione del modello di contenuto](./media/customize-face-model/content-model-customization-people-tab.png)

## <a name="create-a-new-person-model"></a>Creare un nuovo modello Persona

1. Scegliere il **+ Aggiungi modello** pulsante.

    ![Aggiungere una nuova persona](./media/customize-face-model/add-new-person.png)
2. Immettere il nome del modello e fare clic sul pulsante di controllo accanto al nome.

    ![Aggiungere una nuova persona](./media/customize-face-model/add-new-person2.png)

    È stato creato un nuovo modello di persona. È ora possibile aggiungere i visi nel nuovo modello di persona.
3. Fare clic sul pulsante di menu elenco e scegliere **+ Add person**.

    ![Aggiungere una nuova persona](./media/customize-face-model/add-new-person3.png)
    
## <a name="add-a-new-person-to-a-person-model"></a>Aggiungere una nuova persona a un modello di persona

> [!NOTE]
> Video Indexer consente di aggiungere più utenti con lo stesso nome in un modello di persona. Tuttavia, è consigliabile concedere unque nomi a ogni persona nel modello per l'usabilità e semplicità.

1. Per aggiungere un nuovo viso a un modello di persona, fare clic sul pulsante di menu elenco accanto al modello di persona che si desidera aggiungere il viso per.
1. Fare clic su **+ Aggiungi persona** dal menu di scelta.

    ![Aggiungere un nuovo viso alla persona](./media/customize-face-model/add-new-face.png)
 
    Un menu a comparsa chiederà di compilare i dettagli di una persona. Digitare il nome utente e fare clic sul pulsante di controllo.

    ![Aggiungere un nuovo viso alla persona](./media/customize-face-model/add-new-face2.png)
 
È quindi possibile scegliere da Esplora file o trascinare e rilasciare le immagini di volti del volto. Video Indexer richiederà l'immagine standard di tutti i tipi di file (ad esempio: JPG, PNG e altro ancora).

Indicizzatore video deve essere in grado di rilevare le occorrenze di questa persona nei video future tale indice si e i video correnti che erano già indicizzate, usando il modello di persona a cui si aggiunge questa nuova veste di. Riconoscimento della persona nei video corrente potrebbe richiedere del tempo per rendere effettive, poiché si tratta di un processo batch.


## <a name="rename-a-person-model"></a>Rinominare un modello di persona

È possibile rinominare qualsiasi modello di utente nell'account di cui è incluso il modello di utente predefinito. Anche se si rinomina il modello di utente predefinito, che verrà usato comunque come il modello di utente predefinito nell'account.

1. Fare clic sul pulsante di menu elenco accanto al modello di persona che si desidera rinominare.
2. Fare clic su **Rinomina** dal menu di scelta.

    ![Rinominare una persona](./media/customize-face-model/rename-person.png)
3. Fare clic sul nome del modello corrente e digitare il nuovo nome.

    ![Rinominare una persona](./media/customize-face-model/rename-person2.png)
4. Fare clic sul pulsante di controllo per il modello da rinominare.

## <a name="delete-a-person-model"></a>Eliminare un modello Persona

È possibile eliminare qualsiasi modello di persona è stato creato nell'account. Tuttavia, è possibile eliminare il modello di utente predefinito.

1. Fare clic su **Elimina** dal menu di scelta.

    ![Eliminare un utente](./media/customize-face-model/delete-person.png)
    
    Un messaggio popup verrà visualizzati e avviserà che questa azione eliminerà il modello di persona e tutte le persone e i file in esso contenuti. Questa azione non può essere annullata. 

    ![Eliminare un utente](./media/customize-face-model/delete-person2.png)
1. Se si è certi, fare clic nuovamente su Elimina.

> [!NOTE]
> I video esistenti che sono stati indicizzati usando questo modello di persona (ora eliminato) non supporterà la possibilità di aggiornare i nomi delle facce che vengono visualizzati nel video. Sarà possibile modificare i nomi dei visi in questi video solo dopo che reindex usando un altro modello di persona. Se reindex senza specificare un modello di persona, verrà utilizzato il modello predefinito. 

## <a name="manage-existing-people-in-a-person-model"></a>Gestire gli utenti esistenti in un modello di persona

Per esaminare il contenuto di uno qualsiasi dei modelli di persona, fare clic sulla freccia accanto al nome del modello di persona.
Elenco a discesa Mostra tutte le persone in quel modello specifico di persona. Se si fa clic sul pulsante di menu elenco accanto a ogni persona, vedere gestire, rinominare ed eliminare le opzioni.  

![Aggiungere un nuovo viso alla persona](./media/customize-face-model/manage-people.png)

### <a name="rename-a-person"></a>Rinominare una persona

1. Per rinominare una persona nel modello di persona, fare clic sul pulsante di menu elenco e scegliere **Rinomina** dal menu dell'elenco.
1. Selezionare il nome della persona e digitare il nuovo nome dell'utente corrente.
1. Fare clic sul pulsante di controllo e la persona verrà rinominata.

### <a name="delete-a-person"></a>Eliminare un utente

1. Per eliminare una persona dal modello di persona, fare clic sul pulsante di menu elenco e scegliere **Elimina** dal menu dell'elenco.
1. Un menu a comparsa indica che questa azione eliminerà l'utente e che questa azione non può essere annullata.
1. Fare nuovamente clic su Elimina e la persona verrà rimossa dal modello di persona.

### <a name="manage-a-person"></a>Gestire una persona

Se si fa clic su **Gestisci**, noterete che tutte le facce Training da questo modello di persona. Tali facce provengono dalle occorrenze di tale persona nei video che usano questo modello di persona o da immagini caricati manualmente. 

Facendo clic su Aggiungi immagini, è possibile aggiungere più visi alla persona.

Per rinominare la persona ed eliminare la persona dal modello di persona, è possibile utilizzare il riquadro di gestione.

## <a name="use-a-person-model-to-index-a-video"></a>Usare un modello di persona per indicizzare un video

È possibile usare un modello di persona per indicizzare il tuo video nuovo assegnando il modello di persona durante il caricamento del video.

Per usare il modello di persona in un nuovo video, eseguire le operazioni seguenti:

1. Scegliere il **caricare** pulsante nella parte superiore della pagina.

    ![Caricamento](./media/customize-face-model/upload.png)
1. Eliminare il file video nel cerchio o cercare il file.
1. Fare clic sulla freccia opzioni avanzate.

    ![Caricamento](./media/customize-face-model/upload2.png)
1. Fare clic sul menu a discesa e selezionare il modello di utente che è stato creato.

    ![Caricamento](./media/customize-face-model/upload3.png)
1. Fare clic sull'opzione di caricamento nella parte inferiore della pagina e il nuovo video verrà indicizzato con il modello di persona.

Se non si specifica un modello di persona durante il caricamento, indicizzatore Video consente l'indicizzazione del video usando il modello di utente predefinito nell'account.

## <a name="use-a-person-model-to-reindex-a-video"></a>Usare un modello di persona un video di indicizzazione 

Per usare un modello di persona di indicizzazione nella raccolta di un video, andare ai video Account Video Indexer home page e al passaggio del mouse sul nome del video che si desidera indicizzare.

Noterete che le opzioni per modificare, eliminare e reindicizzare il video. 

1. Fare clic sull'opzione di indicizzazione del video.

    ![Reindicizzare](./media/customize-face-model/reindex.png)

    È ora possibile selezionare il modello di persona reindicizzare il video.
1. Fare clic sul menu a discesa e selezionare il modello di persona che si desidera utilizzare. 

    ![Reindicizzare](./media/customize-face-model/reindex2.png)

1. Scegliere il **Reindex** pulsante e il video verrà reindicizzato tramite il modello di persona.

Eventuali nuove modifiche apportate ai volti rilevati e riconosciuto del video che appena reindicizzato verranno salvate nel modello di utente che è stato usato per indicizzare il video.

## <a name="managing-people-in-your-videos"></a>Gestione del personale nei video

È possibile gestire i volti rilevati e le persone che sono state riconosciute nei video indicizzati da modifica e l'eliminazione di visi.

L'eliminazione di un viso, rimuove un viso specifico di Insights del video.

Modifica di un viso, Rinomina un tipo di carattere è rilevato e possibilmente riconosciuto nel video. Quando si modifica un viso nel video, tale nome viene salvato come una voce di persona nel modello di persona assegnata al video durante il caricamento e l'indicizzazione.

Se non si assegna un modello di persona al video durante il caricamento, la modifica viene salvata nel modello di utente predefinito dell'account.

### <a name="edit-a-face"></a>Modificare un viso


> [!NOTE]
> Se un modello di persona dispone di due o più persone diverse con lo stesso nome, non sarà in grado di contrassegnare tale nome all'interno dei video che utilizzano tale modello di persona. Solo sarà in grado di apportare modifiche alle persone che condividono il nome nella scheda utenti della pagina di personalizzazione del modello di contenuto nel Video Indexer. Per questo motivo, è consigliabile assegnare un nome univoco a ogni persona nel modello di persona.

1. Passare al sito Web di Video Indexer e accedere.
1. Cercare un video da visualizzare e modificare nell'account.
1. Per modificare un viso nel video, andare alla scheda di Insights e fare clic sull'icona a forma di matita nell'angolo superiore destro della finestra.

    ![Modificare un viso in video](./media/customize-face-model/edit-face.png)
1. Fare clic su uno qualsiasi dei visi rilevati e cambiare i relativi nomi da "Sconosciuto #X" oppure dal nome precedentemente assegnato al viso. 
1. Dopo aver digitato il nuovo nome, fare clic sull'icona di controllo accanto al nuovo nome. Questo consente di salvare il nuovo nome e riconosce e nomi di tutte le occorrenze di questa faccia in altri video correnti e in futuro i video caricati. Riconoscimento del volto in altri video corrente potrebbe richiedere del tempo per rendere effettiva, poiché si tratta di un processo batch.

Se si assegna un viso con il nome di una persona esistente nel modello di persona che usa il video, ciò consente di unire le immagini volto rilevato da questo video di tale persona con ciò che esiste già nel modello. Se si assegna un viso con un nome completamente nuovo, si creerà una nuova voce di persona nel modello di persona che usa il video. 

    ![Edit a face in your video](./media/customize-face-model/edit-face2.png)

### <a name="delete-a-face"></a>Eliminare un viso

Per eliminare un volto rilevato nel video, andare al riquadro Insights e fare clic sull'icona a forma di matita nell'angolo in alto a destra del riquadro. Fare clic sull'opzione di eliminazione sotto il nome della faccia. In questo modo il viso rilevato verrà rimosso dal video. Viso della persona viene ancora rilevato negli altri video in cui è presente, ma è possibile eliminare il volto da questi video anche dopo che sono state indicizzate. L'utente, se era stata denominata, anche continuerà a esistere nel modello di persona utilizzato per indicizzare il video da cui eliminare il volto a meno che non si elimina in modo specifico la persona dal modello di persona.

![Eliminare un viso in video](./media/customize-face-model/delete-face.png)


## <a name="next-steps"></a>Passaggi successivi

[Personalizzare il modello delle persone usando le API](customize-person-model-with-api.md)
