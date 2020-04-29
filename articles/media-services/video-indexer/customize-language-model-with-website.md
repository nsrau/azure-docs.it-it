---
title: Personalizzare il modello di lingua con Video Indexer sito Web
titleSuffix: Azure Media Services
description: Informazioni su come personalizzare un modello di linguaggio con il sito Web Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 8917a3ac302d18337d79bffce69bad108667b4d2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80128084"
---
# <a name="customize-a-language-model-with-the-video-indexer-website"></a>Personalizzare un modello linguistico con il sito Web di Video Indexer

Video Indexer consente di creare modelli linguistici personalizzati per personalizzare il riconoscimento vocale caricando un testo di adattamento, ovvero il testo del dominio con il vocabolario a cui si vuole adattare il motore. Dopo avere eseguito il training del modello, le nuove parole presenti nel testo di adattamento verranno riconosciute.

Per una panoramica dettagliata e procedure consigliate per i modelli di linguaggio personalizzati, vedere [personalizzare un modello di linguaggio con video Indexer](customize-language-model-overview.md).

È possibile usare il sito Web di Video Indexer per creare e modificare modelli linguistici personalizzati nel proprio account, come descritto in questo argomento. È anche possibile usare l'API, come descritto in [Personalizzare il modello linguistico usando le API](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Creare un modello linguistico

1. Accedere al sito Web di [video Indexer](https://www.videoindexer.ai/) ed eseguire l'accesso.
2. Per personalizzare un modello nell'account, selezionare il pulsante **personalizzazione modello di contenuto** nell'angolo superiore destro della pagina.

   ![Personalizzare il modello di contenuto in Video Indexer](./media/content-model-customization/content-model-customization.png)

3. Selezionare la scheda **Lingua**.

    Verrà visualizzato un elenco di lingue supportate.

    ![Elenco dei modelli di lingue in Video Indexer](./media/customize-language-model/customize-language-model.png)

4. In lingua desiderata selezionare **Aggiungi modello**.
5. Digitare il nome per il modello linguistico e premere INVIO.

    Questo passaggio consente di creare il modello e di caricare i file di testo nel modello.

6. Per aggiungere un file di testo, selezionare **Aggiungi file**. Si aprirà Esplora file.

7. Individuare e selezionare il file di testo. È possibile aggiungere più file di testo a un modello linguistico.

    È anche possibile aggiungere un file di testo selezionando il pulsante **...** sul lato destro del modello di lingua e selezionando **Aggiungi file**.

8. Al termine del caricamento dei file di testo, selezionare l'opzione di **traino** verde.

    ![Modello di linguaggio di training in Video Indexer](./media/customize-language-model/train-model.png)

Il processo di training potrebbe richiedere alcuni minuti. Al termine del training, accanto al modello sarà visibile l'indicazione **Con training**. È possibile visualizzare in anteprima, scaricare ed eliminare il file dal modello.

![Modello di linguaggio sottoposto a training in Video Indexer](./media/customize-language-model/preview-model.png)

### <a name="using-a-language-model-on-a-new-video"></a>Uso di un modello linguistico in un nuovo video

Per usare il modello di linguaggio in un nuovo video, effettuare una delle operazioni seguenti:

* Selezionare il pulsante **carica** nella parte superiore della pagina.

    ![Pulsante Carica Video Indexer](./media/customize-language-model/upload.png)

* Rilasciare il file audio o video nel cerchio o cercare il file.

    ![Carica file multimediale Video Indexer](./media/customize-language-model/upload2.png)

Si ha la possibilità di selezionare la **lingua di origine del video**. Selezionare l'elenco a discesa e selezionare un modello di lingua creato dall'elenco. Dovrebbe essere indicati la lingua del modello linguistico e il nome assegnato tra parentesi.

Selezionare l'opzione **carica** nella parte inferiore della pagina e il nuovo video verrà indicizzato usando il modello di lingua.

### <a name="using-a-language-model-to-reindex"></a>Utilizzo di modello linguistico per reindicizzare

Per usare il modello linguistico per reindicizzare un video nella raccolta, passare a **Account videos** (Video account) nella home page di [Video Indexer](https://www.videoindexer.ai/) e passare con il mouse sul nome del video da reindicizzare.

Verranno visualizzate le opzioni per modificare il video, eliminare il video e reindicizzare il video. Selezionare l'opzione per reindicizzare il video.

![Reindicizzare con Video Indexer](./media/customize-language-model/reindex1.png)

Si ha la possibilità di selezionare la **lingua di origine video** con cui reindicizzare il video. Selezionare l'elenco a discesa e selezionare un modello di lingua creato dall'elenco. Dovrebbe essere indicati la lingua del modello linguistico e il nome assegnato tra parentesi.

![Scegliere la lingua di origine video: reindicizzare un video con Video Indexer](./media/customize-language-model/reindex.png)

Selezionare il pulsante **REINDEX (re-index** ). il video verrà reindicizzato usando il modello di lingua.

## <a name="edit-a-language-model"></a>Modificare un modello linguistico

È possibile modificare un modello linguistico cambiando il nome, aggiungendovi file e cancellando file da esso.

Se si aggiungono o eliminano file dal modello di lingua, sarà necessario eseguire di nuovo il training del modello selezionando l'opzione di **traino** verde.

### <a name="rename-the-language-model"></a>Rinominare il modello linguistico

È possibile modificare il nome del modello di lingua selezionando il pulsante con i puntini di sospensione (**...**) sul lato destro del modello di lingua e selezionando **Rinomina**.

Digitare il nuovo nome e premere invio.

### <a name="add-files"></a>Aggiungere file

Per aggiungere un file di testo, selezionare **Aggiungi file**. Si aprirà Esplora file.

Individuare e selezionare il file di testo. È possibile aggiungere più file di testo a un modello linguistico.

È anche possibile aggiungere un file di testo selezionando il pulsante con i puntini di sospensione (**...**) sul lato destro del modello di lingua e selezionando **Aggiungi file**.

### <a name="delete-files"></a>Eliminare file

Per eliminare un file dal modello di lingua, selezionare il pulsante con i puntini di sospensione (**...**) a destra del file di testo e selezionare **Elimina**. Viene visualizzata una nuova finestra che informa che l'eliminazione non può essere annullata. Selezionare l'opzione **Elimina** nella nuova finestra.

Questa azione rimuove completamente il file dal modello linguistico.

## <a name="delete-a-language-model"></a>Eliminare un modello linguistico

Per eliminare un modello di lingua dall'account, selezionare il pulsante con i puntini di sospensione (**...**) sul lato destro del modello di lingua e selezionare **Elimina**.

Viene visualizzata una nuova finestra che informa che l'eliminazione non può essere annullata. Selezionare l'opzione **Elimina** nella nuova finestra.

Questa azione rimuove completamente il modello linguistico dall'account. Tutti i video che usano il modello di linguaggio eliminato manterranno lo stesso indice fino a quando non si riindicizza il video. Se si reindicizza il video, è possibile assegnare un nuovo modello di lingua al video. In caso contrario, Video Indexer utilizzerà il modello predefinito per reindicizzare il video.

## <a name="customize-language-models-by-correcting-transcripts"></a>Personalizzare i modelli di lingua correggendo le trascrizioni

Video Indexer supporta la personalizzazione automatica dei modelli di lingua in base alle effettive correzioni effettuate dagli utenti alle trascrizioni dei video.

1. Per apportare correzioni a una trascrizione, aprire il video che si vuole modificare dai video dell'account. Selezionare la scheda **sequenza temporale** .

    ![Scheda della sequenza temporale del modello di linguaggio personalizzato: Video Indexer](./media/customize-language-model/timeline.png)

1. Selezionare l'icona a matita per modificare la trascrizione della trascrizione.

    ![Personalizzare la trascrizione di modifica del modello di linguaggio: Video Indexer](./media/customize-language-model/edits.png)

    Video Indexer acquisisce tutte le righe che vengono corrette dall'utente nella trascrizione del video e le aggiunge automaticamente in un file di testo denominato "da modifiche della trascrizione". Queste modifiche vengono utilizzate per ripetere il training del modello di linguaggio specifico utilizzato per indicizzare questo video.
    
    Se non è stato specificato un modello di lingua durante l'indicizzazione del video, tutte le modifiche per questo video verranno archiviate in un modello di lingua predefinito denominato "adattamenti account" all'interno della lingua rilevata del video.
    
    Nel caso in cui siano state apportate più modifiche alla stessa riga, per aggiornare il modello di lingua viene utilizzata solo l'ultima versione della riga corretta.  
    
    > [!NOTE]
    > Per la personalizzazione vengono utilizzate solo correzioni testuali. Non sono incluse le correzioni che non coinvolgono parole effettive (ad esempio, segni di punteggiatura o spazi).
    
1. Verranno visualizzate le correzioni della trascrizione nella scheda linguaggio della pagina Personalizzazione del modello di contenuto.

    ![Personalizzare il modello di linguaggio: Video Indexer](./media/customize-language-model/customize.png)

   Per esaminare il file "da modifiche della trascrizione" per ognuno dei modelli di lingua, selezionarlo per aprirlo.

    ![Da modifiche della trascrizione: Video Indexer](./media/customize-language-model/from-transcript-edits.png)

## <a name="next-steps"></a>Passaggi successivi

[Personalizzare il modello di lingua usando le API](customize-language-model-with-api.md)
