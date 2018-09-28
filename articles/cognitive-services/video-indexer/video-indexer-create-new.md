---
title: 'Esercitazione: Creare informazioni dettagliate del video da video esistenti'
titlesuffix: Azure Cognitive Services
description: Questo argomento illustra come creare e pubblicare informazioni dettagliate basate su file video esistenti.
services: cognitive services
author: juliako
manager: cgronlun
ms.service: cognitive-services
ms.component: video-indexer
ms.topic: tutorial
ms.date: 09/15/2018
ms.author: juliako
ms.openlocfilehash: 3a3e5c62d9eb96563d395106db62cc91a8307658
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2018
ms.locfileid: "45983918"
---
# <a name="tutorial-create-highlights-from-existing-videos"></a>Esercitazione: Creare informazioni dettagliate da video esistenti

Questo argomento illustra come creare e pubblicare informazioni dettagliate basate sui video di altri utenti.

1. Passare al sito Web di [Video Indexer](https://www.videoindexer.ai/) ed eseguire l'accesso.
2. Trovare un video da cui si vogliono creare le informazioni dettagliate.
3. Premere **Riproduci**.

    La pagina mostra le informazioni dettagliate riepilogate del video. 

    ![Informazioni dettagliate](./media/video-indexer-create-new/video-indexer-summarized-insights.png)

3. Fare clic sul pulsante **Modifica**.

    Questa pagina mostra tutte le informazioni dettagliate sul video. Queste sono suddivise in blocchi. I blocchi sono concepiti per semplificare l'esame dei dati. Ad esempio, i blocchi potrebbero essere suddivisi in base a quando cambia il parlante o alla presenza di una lunga pausa. È possibile creare una propria playlist contenente solo le righe desiderate. Per mostrare solo determinate parti del video di origine, è possibile filtrare per argomenti/parole chiave, sentiment, persone e relatori. Si può scegliere di visualizzare solo la trascrizione o OCR del video.    

    ![Informazioni dettagliate](./media/video-indexer-create-new/video-indexer-create-new-playlist.png)

4. Creare la playlist.

    Per aggiungere o rimuovere righe dalla playlist, premere **+**/**-**.

5. Visualizzare in anteprima la playlist.

    Dopo aver creato la playlist, premere **Anteprima**.
6. Pubblicare la playlist.

    Dopo aver visualizzato in anteprima la playlist, è possibile pubblicarla.

    Una volta pubblicata, la playlist viene aggiunta all'elenco delle informazioni dettagliate sul video.


## <a name="next-steps"></a>Passaggi successivi 

Dopo aver creato la nuova playlist, è possibile continuare l'elaborazione, come descritto in uno degli argomenti seguenti: 

- [Elaborare contenuti con l'API REST Video Indexer](video-indexer-use-apis.md)
- [Embed visual widgets in your application](video-indexer-embed-widgets.md) (Incorporare i widget visivi nell'applicazione)

## <a name="see-also"></a>Vedere anche 

[Panoramica di Video Indexer](video-indexer-overview.md) 
