---
title: Personalizzare un modello Persona con il sito Web Indicizzatore video
titleSuffix: Azure Media Services
description: Informazioni su come personalizzare un modello Persona con il sito Web Indicizzatore video.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: f29e651f5c8542722f0dc2c9878184ac0d2a6a1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499963"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>Personalizzare un modello delle persone con il sito Web di Video Indexer

L'indicizzatore video supporta il riconoscimento delle celebrità per i contenuti video. La funzionalità di identificazione di celebrità include circa un milione di visi basandosi su origini dati di uso comune, ad esempio IMDB, Wikipedia e i principali influencer di LinkedIn. Per una panoramica dettagliata, consultate [Personalizzare un modello Person nell'indicizzatore video.](customize-person-model-overview.md)

È possibile usare il sito Web di Video Indexer per modificare i visi rilevati in un video, come descritto in questo argomento. È anche possibile usare l'API, come descritto in [Personalizzare il modello Person usando le API](customize-person-model-with-api.md).

## <a name="central-management-of-person-models-in-your-account"></a>Gestione centralizzata dei modelli di persona nel tuo account

1. Per visualizzare, modificare ed eliminare i modelli Persona nel tuo account, accedi al sito Web Indicizzatore video e accedi.

2. Selezionare il pulsante di personalizzazione del modello di contenuto nell'angolo superiore destro della pagina.

    ![Personalizzazione del modello di contenuto](./media/customize-face-model/content-model-customization.png)

3. Selezionare la scheda Persone.

    Vedrai il modello Persona predefinita nel tuo account. Il modello Persona predefinita contiene i volti che potresti aver modificato o modificato nelle informazioni dettagliate dei tuoi video per i quali non hai specificato un modello Persona personalizzato durante l'indicizzazione.

    Se hai creato altri modelli Person, saranno elencati anche in questa pagina.

    ![Personalizzazione del modello di contenuto](./media/customize-face-model/content-model-customization-people-tab.png)

## <a name="create-a-new-person-model"></a>Creare un nuovo modello Persona

1. Selezionare il pulsante **: Aggiungi modello.**

    ![Aggiungere un nuovo modello di persona](./media/customize-face-model/add-new-person.png)

2. Immettere il nome del modello e selezionare il pulsante di controllo accanto al nome.

    ![Aggiungere un nuovo modello di persona](./media/customize-face-model/add-new-person2.png)

    È stato creato un nuovo modello Persona.You've created a new Person model. È ora possibile aggiungere facce al nuovo modello Persona.

3. Selezionare il pulsante del menu dell'elenco e scegliere **Aggiungi persona**.

    ![Aggiungere un nuovo modello di persona](./media/customize-face-model/add-new-person3.png)

## <a name="add-a-new-person-to-a-person-model"></a>Aggiungere una nuova persona a un modello Persona

> [!NOTE]
> L'indicizzatore video consente di aggiungere più persone con lo stesso nome in un modello Persona. Tuttavia, ti consigliamo di dare nomi univoci a ogni persona nel tuo modello per l'usabilità e la chiarezza.

1. Per aggiungere un nuovo volto a un modello Persona, selezionare il pulsante del menu Elenco accanto al modello Persona a cui si desidera aggiungere la faccia.

1. Dal menu, selezionare **Aggiungi persona.**

    ![Aggiungere un nuovo volto alla persona](./media/customize-face-model/add-new-face.png)

    Un pop-up ti chiederà di compilare i dettagli della persona. Digitare il nome della persona e selezionare il pulsante di controllo.

    ![Aggiungere un nuovo volto alla persona](./media/customize-face-model/add-new-face2.png)

    È quindi possibile scegliere da Esplora file o trascinare e rilasciare le immagini del volto del viso. Video Indexer prenderà tutti i tipi di file di immagine standard (ad es. JPG, PNG e altro ancora).

    L'Indicizzatore Video è in grado di rilevare le occorrenze di questa persona nei video futuri che indicizzi e nei video correnti che hai già indicizzato, utilizzando il modello Persona a cui hai aggiunto questo nuovo volto. Il riconoscimento della persona nei tuoi video attuali potrebbe richiedere del tempo per entrare in vigore, poiché si tratta di un processo batch.

## <a name="rename-a-person-model"></a>Rinominare un modello Persona

Puoi rinominare qualsiasi modello Persona nel tuo account, incluso il modello Persona predefinita. Anche se rinomini il modello Persona predefinito, verrà comunque utilizzato come modello Persona predefinita nel tuo account.

1. Selezionare il pulsante del menu elenco accanto al modello Persona che si desidera rinominare.
2. Seleziona **Rinomina** dal menu.

    ![Rinominare un modello di persona](./media/customize-face-model/rename-person.png)

3. Selezionare il nome corrente del modello e digitare il nuovo nome.

    ![Rinominare un modello di persona](./media/customize-face-model/rename-person2.png)

4. Selezionare il pulsante di controllo per il modello da rinominare.

## <a name="delete-a-person-model"></a>Eliminare un modello Persona

Puoi eliminare qualsiasi modello di persona che hai creato nel tuo account. Tuttavia, non è possibile eliminare il modello Persona predefinita.

1. Seleziona **Elimina** dal menu.

    ![Eliminare un modello di persona](./media/customize-face-model/delete-person.png)

    Verrà visualizzato un popup che informa che questa azione eliminerà il modello Persona e tutte le persone e i file in esso contenuti. Questa azione non può essere annullata.

    ![Eliminare un modello di persona](./media/customize-face-model/delete-person2.png)

1. Se sei sicuro, seleziona di nuovo Elimina.

> [!NOTE]
> I video esistenti che sono stati indicizzati utilizzando questo modello di persona (ora eliminato) non supportano la possibilità di aggiornare i nomi dei volti visualizzati nel video. Potrai modificare i nomi dei volti in questi video solo dopo averli reindicizzati utilizzando un altro modello Persona. Se si reindicizza senza specificare un modello Person, verrà utilizzato il modello predefinito.

## <a name="manage-existing-people-in-a-person-model"></a>Gestire le persone esistenti in un modello Persona

Per visualizzare il contenuto di uno qualsiasi dei modelli Persona, selezionare la freccia accanto al nome del modello Persona. L'elenco a discesa mostra tutte le persone in quel particolare modello di persona. Se si seleziona il pulsante del menu elenco accanto a ciascuna delle persone, vengono visualizzate le opzioni di gestione, ridenominazione ed eliminazione.  

![Aggiungere un nuovo volto alla persona](./media/customize-face-model/manage-people.png)

### <a name="rename-a-person"></a>Rinominare una persona

1. Per rinominare una persona nel modello Persona, selezionare il pulsante del menu elenco e scegliere **Rinomina** dal menu dell'elenco.
1. Selezionare il nome corrente della persona e digitare il nuovo nome.
1. Selezionare il pulsante di controllo e la persona verrà rinominata.

### <a name="delete-a-person"></a>Eliminare una persona

1. Per eliminare una persona dal modello Persona, selezionare il pulsante del menu elenco e scegliere **Elimina** dal menu elenco.
1. Un popup indica che questa azione eliminerà la persona e che questa azione non può essere annullata.
1. Selezionare di nuovo **Elimina** per rimuovere la persona dal modello Persona.

### <a name="manage-a-person"></a>Gestire una persona

Se si seleziona **Gestisci**, vengono visualizzati tutti i volti da cui viene eseguito il training del modello Persona. Questi volti provengono dalle occorrenze di quella persona nei video che usano questo modello di persona o dalle immagini caricate manualmente.

È possibile aggiungere altri volti alla persona selezionando **Aggiungi immagini**.

È possibile utilizzare il riquadro Gestione per rinominare la persona ed eliminare la persona dal modello Persona.

## <a name="use-a-person-model-to-index-a-video"></a>Usare un modello Persona per indicizzare un video

È possibile utilizzare un modello Persona per indicizzare il nuovo video assegnando il modello Persona durante il caricamento del video.

Per usare il modello Persona in un nuovo video, procedi nel seguente modo:

1. Seleziona il pulsante **Carica** nella parte superiore della pagina.

    ![Caricare un modello di persona](./media/customize-face-model/upload.png)

1. Rilasciare il file video nel cerchio o cercare il file.
1. Selezionare la freccia **Opzioni avanzate.**

    ![Caricare un modello di persona](./media/customize-face-model/upload2.png)

1. Selezionare l'elenco a discesa e selezionare il modello Persona creato.

    ![Caricare un modello di persona](./media/customize-face-model/upload3.png)

1. Seleziona l'opzione **Carica** nella parte inferiore della pagina e il nuovo video verrà indicizzato utilizzando il modello Persona.

Se non specifichi un modello Persona durante il caricamento, Video Indexer indicizzerà il video utilizzando il modello Persona predefinita nel tuo account.

## <a name="use-a-person-model-to-reindex-a-video"></a>Usare un modello Persona per reindicizzare un video

Per utilizzare un modello Persona per reindicizzare un video nella raccolta, accedi ai video del tuo account nella home page dell'indicizzatore video e passa il mouse sopra il nome del video che desideri reindicizzare.

Vengono visualizzate le opzioni per modificare, eliminare e reindicizzare il video.

1. Seleziona l'opzione per reindicizzare il video.

    ![Usare il modello Persona per reindicizzare un video](./media/customize-face-model/reindex.png)

    È ora possibile selezionare il modello Persona con cui reindicizzare il video.
1. Selezionare l'elenco a discesa e selezionare il modello Persona che si desidera utilizzare.

    ![Usare il modello Persona per reindicizzare un video](./media/customize-face-model/reindex2.png)

1. Seleziona il pulsante **Reindicizza** e il video verrà reindicizzato utilizzando il modello Person.

Tutte le nuove modifiche apportate ai volti rilevati e riconosciuti nel video appena reindicizzato verranno salvate nel modello Persona utilizzato per reindicizzare il video.

## <a name="managing-people-in-your-videos"></a>Gestione delle persone nei tuoi video

Puoi gestire i volti rilevati e le persone riconosciute nei video che indicizzi modificando ed eliminando i volti.

L'eliminazione di un volto rimuove un volto specifico dalle informazioni dettagliate del video.

La modifica di un volto consente di rinominare un volto rilevato ed eventualmente riconosciuto nel video. Quando modifichi un volto nel video, tale nome viene salvato come voce persona nel modello Persona assegnata al video durante il caricamento e l'indicizzazione.

Se non assegni un modello Persona al video durante il caricamento, la modifica viene salvata nel modello Persona predefinita del tuo account.

### <a name="edit-a-face"></a>Modificare un viso

> [!NOTE]
> Se un modello Persona ha due o più persone diverse con lo stesso nome, non potrai taggare tale nome all'interno dei video che usano quel modello Persona. Sarà possibile apportare modifiche solo alle persone che condividono tale nome nella scheda Persone della pagina di personalizzazione del modello di contenuto in Video Indexer. Per questo motivo, si consiglia di assegnare nomi univoci a ogni persona nel modello Persona.

1. Passare al sito Web di Video Indexer ed eseguire l'accesso.
1. Cercare un video da visualizzare e modificare nell'account.
1. Per modificare un volto nel video, vai alla scheda Insights e seleziona l'icona a matita nell'angolo in alto a destra della finestra.

    ![Modificare un volto nel video](./media/customize-face-model/edit-face.png)

1. Selezionare una delle facce rilevate e modificarne il nome da "#X sconosciuti" (o il nome precedentemente assegnato alla faccia).
1. Dopo aver digitato il nuovo nome, selezionare l'icona di controllo accanto al nuovo nome. Questa azione salva il nuovo nome e riconosce e denomina tutte le occorrenze di questo volto negli altri video attuali e nei video futuri che carichi. L'identificazione del viso negli altri video attuali può richiedere alcuni minuti poiché si tratta di un processo batch.

Se si assegna un nome a un volto con il nome di una persona esistente nel modello Persona utilizzato dal video, le immagini del volto rilevate da questo video di tale persona verranno unite a ciò che già esiste nel modello. Se si assegna un nome a un volto con un nuovo nome, nel modello Persona viene creata una nuova voce Persona in uso nel modello Persona.

![Modificare un volto nel video](./media/customize-face-model/edit-face2.png)

### <a name="delete-a-face"></a>Eliminare un viso

Per eliminare un volto rilevato nel video, vai al riquadro Approfondimenti e seleziona l'icona a matita nell'angolo in alto a destra del riquadro. Selezionare l'opzione **Elimina** sotto il nome della faccia. Questa azione rimuove il volto rilevato dal video. Il volto della persona verrà comunque rilevato negli altri video in cui viene visualizzato, ma puoi eliminare il volto da tali video anche dopo che sono stati indicizzati.

La persona, se è stata nominata, continuerà anche a esistere nel modello Persona utilizzato per indicizzare il video da cui è stato eliminato il volto, a meno che non si elimini specificamente la persona dal modello Persona.

![Eliminare un volto nel video](./media/customize-face-model/delete-face.png)

## <a name="next-steps"></a>Passaggi successivi

[Personalizzare il modello delle persone usando le API](customize-person-model-with-api.md)
