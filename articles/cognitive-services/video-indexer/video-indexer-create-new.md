---
title: Usare Video Indexer di Azure per creare informazioni dettagliate video da video esistenti | Microsoft Docs
description: Questo argomento illustra come creare e pubblicare informazioni dettagliate basate sui video di altri utenti.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 07/25/2018
ms.author: juliako
ms.openlocfilehash: 161a47f72a0f8038a515c09f25ec2a8e8a520547
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2018
ms.locfileid: "39390841"
---
# <a name="create-highlights-from-existing-videos"></a>Creare informazioni dettagliate da video esistenti

Questo argomento illustra come creare e pubblicare informazioni dettagliate basate sui video di altri utenti.

1. Accedere all'account di [Video Indexer](https://api-portal.videoindexer.ai/).
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
