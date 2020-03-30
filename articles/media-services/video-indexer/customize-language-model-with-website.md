---
title: Personalizzare il modello di lingua con il sito Web Video Indexer
titleSuffix: Azure Media Services
description: Informazioni su come personalizzare un modello di lingua con il sito Web Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 8917a3ac302d18337d79bffce69bad108667b4d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128084"
---
# <a name="customize-a-language-model-with-the-video-indexer-website"></a>Personalizzare un modello linguistico con il sito Web di Video Indexer

Video Indexer consente di creare modelli linguistici personalizzati per personalizzare il riconoscimento vocale caricando un testo di adattamento, ovvero il testo del dominio con il vocabolario a cui si vuole adattare il motore. Dopo avere eseguito il training del modello, le nuove parole presenti nel testo di adattamento verranno riconosciute.

Per una panoramica dettagliata e sulle procedure consigliate per i modelli linguistici personalizzati, consultate Personalizzare un modello linguistico [con l'indicizzatore video.](customize-language-model-overview.md)

È possibile usare il sito Web di Video Indexer per creare e modificare modelli linguistici personalizzati nel proprio account, come descritto in questo argomento. È anche possibile usare l'API, come descritto in [Personalizzare il modello linguistico usando le API](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Creare un modello linguistico

1. Accedi al sito Web [Indicizzatore video.](https://www.videoindexer.ai/)
2. Per personalizzare un modello nel tuo account, seleziona il pulsante **di personalizzazione del modello** di contenuto nell'angolo in alto a destra della pagina.

   ![Personalizzare il modello di contenuto nell'indicizzatore video](./media/content-model-customization/content-model-customization.png)

3. Selezionare la scheda **Lingua**.

    Verrà visualizzato un elenco di lingue supportate.

    ![Elenco dei modelli di lingue nell'indicizzatore video](./media/customize-language-model/customize-language-model.png)

4. Sotto la lingua desiderata selezionare **Aggiungi modello**.
5. Digitare il nome per il modello linguistico e premere INVIO.

    Questo passaggio crea il modello e offre la possibilità di caricare file di testo nel modello.

6. Per aggiungere un file di testo, selezionare **Aggiungi file**. Si aprirà Esplora file.

7. Individuare e selezionare il file di testo. È possibile aggiungere più file di testo a un modello linguistico.

    È inoltre possibile aggiungere un file di testo selezionando il pulsante **...** sul lato destro del modello di lingua e selezionando **Aggiungi file**.

8. Una volta che hai finito di caricare i file di testo, seleziona l'opzione **Verde Train.**

    ![Modello di lingua del treno nell'indicizzatore video](./media/customize-language-model/train-model.png)

Il processo di training potrebbe richiedere alcuni minuti. Al termine del training, accanto al modello sarà visibile l'indicazione **Con training**. È possibile visualizzare in anteprima, scaricare ed eliminare il file dal modello.

![Modello linguistico con training nell'indicizzatore video](./media/customize-language-model/preview-model.png)

### <a name="using-a-language-model-on-a-new-video"></a>Uso di un modello linguistico in un nuovo video

Per usare il modello di lingua in un nuovo video, effettuate una delle seguenti operazioni:

* Seleziona il pulsante **Carica** nella parte superiore della pagina.

    ![Pulsante di caricamento Indicizzatore video](./media/customize-language-model/upload.png)

* Rilasciare il file audio o video nel cerchio o cercare il file.

    ![Caricare il file multimediale Video Indexer](./media/customize-language-model/upload2.png)

È possibile selezionare la **lingua dell'origine video**. Selezionare l'elenco a discesa e selezionare un modello di lingua creato dall'elenco. Dovrebbe essere indicati la lingua del modello linguistico e il nome assegnato tra parentesi.

Seleziona l'opzione **Carica** nella parte inferiore della pagina e il nuovo video verrà indicizzato utilizzando il modello di lingua.

### <a name="using-a-language-model-to-reindex"></a>Utilizzo di modello linguistico per reindicizzare

Per usare il modello linguistico per reindicizzare un video nella raccolta, passare a **Account videos** (Video account) nella home page di [Video Indexer](https://www.videoindexer.ai/) e passare con il mouse sul nome del video da reindicizzare.

Vedrai le opzioni per modificare il video, eliminarti il video e reindicizzare il video. Seleziona l'opzione per reindicizzare il video.

![Reindicizza con l'indicizzatore videoReindexer with Video Indexer](./media/customize-language-model/reindex1.png)

Hai la possibilità di selezionare la **lingua sorgente video** con cui reindicizzare il video. Selezionare l'elenco a discesa e selezionare un modello di lingua creato dall'elenco. Dovrebbe essere indicati la lingua del modello linguistico e il nome assegnato tra parentesi.

![Scegli la lingua della sorgente video: reindicizza un video con Video Indexer](./media/customize-language-model/reindex.png)

Seleziona il pulsante **Reindicizza** e il video verrà reindicizzato utilizzando il tuo modello di lingua.

## <a name="edit-a-language-model"></a>Modificare un modello linguistico

È possibile modificare un modello linguistico cambiando il nome, aggiungendovi file e cancellando file da esso.

Se si aggiungono o eliminano file dal modello di lingua, sarà necessario eseguire nuovamente il training del modello selezionando l'opzione **Train** verde.

### <a name="rename-the-language-model"></a>Rinominare il modello linguistico

È possibile modificare il nome del modello di lingua selezionando il pulsante con i lipsia (**...**) sul lato destro del modello di lingua e selezionando **Rinomina**.

Digitare il nuovo nome e premere invio.

### <a name="add-files"></a>Aggiungere file

Per aggiungere un file di testo, selezionare **Aggiungi file**. Si aprirà Esplora file.

Individuare e selezionare il file di testo. È possibile aggiungere più file di testo a un modello linguistico.

È inoltre possibile aggiungere un file di testo selezionando il pulsante con i lipsia (**...**) sul lato destro del modello Di lingua e selezionando **Aggiungi file**.

### <a name="delete-files"></a>Eliminare file

Per eliminare un file dal modello di linguaggio, selezionare il pulsante con i conio ai conividi (**...**) sul lato destro del file di testo e selezionare **Elimina**. Viene visualizzata una nuova finestra che indica che l'eliminazione non può essere annullata. Selezionare l'opzione **Elimina** nella nuova finestra.

Questa azione rimuove completamente il file dal modello linguistico.

## <a name="delete-a-language-model"></a>Eliminare un modello linguistico

Per eliminare un modello di lingua dall'account, selezionare il pulsante con i conipiù (**...**) sul lato destro del modello Di lingua e selezionare **Elimina**.

Viene visualizzata una nuova finestra che indica che l'eliminazione non può essere annullata. Selezionare l'opzione **Elimina** nella nuova finestra.

Questa azione rimuove completamente il modello linguistico dall'account. Qualsiasi video che utilizzava il modello di lingua eliminato manterrà lo stesso indice fino a quando non reindicizzerai il video. Se reindicizzare il video, è possibile assegnare un nuovo modello di lingua al video. In caso contrario, Video Indexer utilizzerà il modello predefinito per reindicizzare il video.

## <a name="customize-language-models-by-correcting-transcripts"></a>Personalizzare i modelli di lingua correggendo le trascrizioni

Video Indexer supporta la personalizzazione automatica dei modelli di lingua in base alle correzioni effettive che gli utenti apportano alle trascrizioni dei loro video.

1. Per apportare correzioni a una trascrizione, apri il video che desideri modificare dai video dell'account. Selezionare la scheda **Sequenza temporale.**

    ![Personalizzare la scheda della sequenza temporale del modello linguistico - Indicizzatore video](./media/customize-language-model/timeline.png)

1. Selezionare l'icona a matita per modificare la trascrizione della trascrizione.

    ![Personalizzare la trascrizione delle modifiche al modello di linguaggio - Indicizzatore video](./media/customize-language-model/edits.png)

    L'indicizzatore video acquisisce tutte le righe corrette dall'utente nella trascrizione del video e le aggiunge automaticamente a un file di testo denominato "Da modifiche trascrizione". Queste modifiche vengono utilizzate per riqualificare il modello di linguaggio specifico utilizzato per indicizzare questo video.
    
    Se non hai specificato un modello di lingua durante l'indicizzazione di questo video, tutte le modifiche per questo video verranno memorizzate in un modello di lingua predefinito chiamato "Adattamenti dell'account" all'interno della lingua rilevata del video.
    
    Nel caso in cui siano state apportate più modifiche alla stessa riga, solo l'ultima versione della riga corretta verrà utilizzata per aggiornare il modello di linguaggio.  
    
    > [!NOTE]
    > Per la personalizzazione vengono utilizzate solo correzioni testuali. Le correzioni che non coinvolgono parole effettive (ad esempio, segni di punteggiatura o spazi) non sono incluse.
    
1. Vedrai le correzioni delle trascrizioni visualizzate nella scheda Lingua della pagina Personalizzazione del modello di contenuto.

    ![Personalizzare il modello linguistico: Video Indexer](./media/customize-language-model/customize.png)

   Per esaminare il file "Da modifiche trascrizione" per ogni modello di lingua, selezionarlo per aprirlo.

    ![Da modifiche della trascrizione - Indicizzatore video](./media/customize-language-model/from-transcript-edits.png)

## <a name="next-steps"></a>Passaggi successivi

[Personalizzare il modello di linguaggio usando le APICustomize language model using APIs](customize-language-model-with-api.md)
