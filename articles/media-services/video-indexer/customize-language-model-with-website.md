---
title: Usare il sito Web di Video Indexer per personalizzare un modello linguistico - Azure
titlesuffix: Azure Media Services
description: Questo articolo illustra come personalizzare un modello linguistico con il sito Web di Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: f98cdcab2d108f8dd9d40e3770498ad17b2a8a88
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65799626"
---
# <a name="customize-a-language-model-with-the-video-indexer-website"></a>Personalizzare un modello linguistico con il sito Web di Video Indexer

Video Indexer consente di creare modelli linguistici personalizzati per personalizzare il riconoscimento vocale caricando un testo di adattamento, ovvero il testo del dominio con il vocabolario a cui si vuole adattare il motore. Dopo avere eseguito il training del modello, le nuove parole presenti nel testo di adattamento verranno riconosciute. 

Per una panoramica dettagliata e le procedure consigliate per i modelli linguistici personalizzati, vedere [Personalizzare un modello linguistico con Video Indexer](customize-language-model-overview.md).

È possibile usare il sito Web di Video Indexer per creare e modificare modelli linguistici personalizzati nel proprio account, come descritto in questo argomento. È anche possibile usare l'API, come descritto in [Personalizzare il modello linguistico usando le API](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Creare un modello linguistico

1. Passare al sito Web di [Video Indexer](https://www.videoindexer.ai/) ed eseguire l'accesso.
2. Per personalizzare un modello nel proprio account, fare clic sul pulsante **Content model customization** (Personalizzazione del modello di contenuto) nell'angolo in alto a destra della pagina.

   ![Personalizzare il modello di contenuto](./media/content-model-customization/content-model-customization.png)

3. Selezionare la scheda **Lingua**.

    Verrà visualizzato un elenco di lingue supportate. 

    ![Personalizzare il modello linguistico](./media/customize-language-model/customize-language-model.png)

4. Nella lingua desiderata fare clic su **Aggiungi modello**.
5. Digitare il nome per il modello linguistico e premere INVIO.

    Verrà creato il modello e sarà possibile caricare i file di testo nel modello.
6. Per aggiungere un file di testo, fare clic su **Aggiungi file**. Viene aperto Esplora file.

7. Individuare e selezionare il file di testo. È possibile aggiungere più file di testo a un modello linguistico.

    È anche possibile aggiungere un file di testo facendo clic sul pulsante **....** sul lato destro del modello linguistico e selezionando **Aggiungi file**.
8. Dopo aver caricato i file di testo, fare clic sull'opzione verde **Addestra**.

    ![Eseguire il training di un modello linguistico](./media/customize-language-model/train-model.png)

Il processo di training potrebbe richiedere alcuni minuti. Al termine del training, accanto al modello sarà visibile l'indicazione **Con training**. È possibile visualizzare in anteprima, scaricare ed eliminare il file dal modello.

![Modello linguistico con training](./media/customize-language-model/preview-model.png)

### <a name="using-a-language-model-on-a-new-video"></a>Uso di un modello linguistico in un nuovo video

Per usare il modello linguistico in un nuovo video, eseguire una delle operazioni seguenti:

* Fare clic sul pulsante **Carica** nella parte superiore della pagina. 

    ![Caricamento](./media/customize-language-model/upload.png)
* Rilasciare il file audio o video nel cerchio o cercare il proprio file.

    ![Caricamento](./media/customize-language-model/upload2.png)

In questo modo è possibile selezionare la **lingua di origine del video**. Fare clic sull'elenco a discesa e selezionare un modello linguistico creato dall'elenco. Dovrebbe essere indicati la lingua del modello linguistico e il nome assegnato tra parentesi.

Fare clic sull'opzione **Carica** in fondo alla pagina e il nuovo video verrà indicizzato usando il modello linguistico.

### <a name="using-a-language-model-to-reindex"></a>Utilizzo di modello linguistico per reindicizzare

Per usare il modello linguistico per reindicizzare un video nella raccolta, passare a **Account videos** (Video account) nella home page di [Video Indexer](https://www.videoindexer.ai/) e passare con il mouse sul nome del video da reindicizzare.

Vengono visualizzate le opzioni per modificare, eliminare e reindicizzare il video. Fare clic sull'opzione per reindicizzare il video.

![Reindicizzare](./media/customize-language-model/reindex1.png)

In questo modo è possibile selezionare la **lingua di origine del video** per reindicizzare il video. Fare clic sull'elenco a discesa e selezionare un modello linguistico creato dall'elenco. Dovrebbe essere indicati la lingua del modello linguistico e il nome assegnato tra parentesi.

![Reindicizzare](./media/customize-language-model/reindex.png)

Fare clic sul pulsante **Reindicizza** e il video verrà reindicizzato usando il modello linguistico.

## <a name="edit-a-language-model"></a>Modificare un modello linguistico

È possibile modificare un modello linguistico cambiando il nome, aggiungendovi file e cancellando file da esso.

Se si aggiungono o si cancellano file dal modello linguistico, è necessario eseguire di nuovo il training del modello facendo clic sull'opzione verde **Addestra**.

### <a name="rename-the-language-model"></a>Rinominare il modello linguistico

È possibile cambiare il nome del modello linguistico facendo clic su **....** sul lato destro del modello linguistico e selezionando **Rinomina**. 

Digitare il nuovo nome e premere invio.

### <a name="add-files"></a>Aggiungere file

Per aggiungere un file di testo, fare clic su **Aggiungi file**. Viene aperto Esplora file.

Individuare e selezionare il file di testo. È possibile aggiungere più file di testo a un modello linguistico.

È anche possibile aggiungere un file di testo facendo clic sul pulsante **....** sul lato destro del modello linguistico e selezionando **Aggiungi file**.

### <a name="delete-files"></a>Eliminare file

Per eliminare un file dal modello linguistico, fare clic sul pulsante **...**  a destra del file di testo e selezionare **Elimina**. Viene visualizzata una nuova finestra che indica che l'eliminazione non può essere annullata. Fare clic sull'opzione **Elimina** nella nuova finestra.

Questa azione rimuove completamente il file dal modello linguistico.

## <a name="delete-a-language-model"></a>Eliminare un modello linguistico

Per eliminare un modello linguistico dal proprio account, fare clic sul pulsante **....** sul lato destro del modello linguistico e selezionare **Elimina**.

Viene visualizzata una nuova finestra che indica che l'eliminazione non può essere annullata. Fare clic sull'opzione **Elimina** nella nuova finestra.

Questa azione rimuove completamente il modello linguistico dall'account. Qualsiasi video che usava il modello linguistico eliminato manterrà lo stesso indice finché non si reindicizza il video. Se si reindicizza il video, è possibile assegnare un nuovo modello linguistico al video. In alternativa, Video Indexer userà il modello predefinito per reindicizzare il video. 

## <a name="customize-language-models-by-correcting-transcripts"></a>Personalizzare modelli di lingua, correggere le trascrizioni

Video Indexer supporta la personalizzazione automatica della lingua modelli basati sugli utenti effettivi correzioni apportare alle trascrizioni dei video.

1. Per apportare correzioni a una trascrizione, aprire il video che si desidera modificare dai tuoi video di Account. Selezionare il **della sequenza temporale** scheda.

    ![Personalizzare il modello linguistico](./media/customize-language-model/timeline.png)
1. Fare clic sull'icona a forma di matita per modificare la trascrizione della trascrizione. 

    ![Personalizzare il modello linguistico](./media/customize-language-model/edits.png)

    Video Indexer acquisisce tutte le righe che sono stati corretti da parte dell'utente nella trascrizione del video e le aggiunge automaticamente un file di testo denominato "da modifiche trascrizione". Queste modifiche vengono usate per nuovamente il training del modello di lingua specifico che è stato usato per indicizzare in questo video. 
    
    Se non è stato specificato un modello di lingua durante l'indicizzazione in questo video, quindi tutte le modifiche per questo video verranno archiviate in un modello di lingua predefinito denominato adattamenti Account entro la lingua rilevata del video. 
    
    Nel caso in cui sono state apportate più modifiche alla stessa riga, solo l'ultima versione della riga di corretti da utilizzare per l'aggiornamento del modello di lingua.  
    
    > [!NOTE]
    > Solo correzioni testuali vengono usate per la personalizzazione. Ciò significa che le correzioni che non coinvolgono parole effettive (ad esempio, segni di punteggiatura o spazi) non sono incluse. 
    
1. Si noterà correzioni trascrizione visualizzati nella scheda lingue della pagina di personalizzazione di modello di contenuto.

    ![Personalizzare il modello linguistico](./media/customize-language-model/customize.png)

   Per esaminare il file "From trascrizione modifiche" per ciascuno dei modelli di lingua, fare clic su di esso per aprirlo. 

    ![Da modifiche trascrizione](./media/customize-language-model/from-transcript-edits.png)

## <a name="next-steps"></a>Passaggi successivi

[Personalizzare il modello linguistico usando le API](customize-language-model-with-api.md)
