---
title: Visualizzare e modificare le informazioni dettagliate di Video Indexer
titlesuffix: Azure Media Services
description: Questo argomento illustra come visualizzare e modificare le informazioni dettagliate di Azure Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: c1b26b8a59ac0306fc06bf90f01642d4585a45be
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2019
ms.locfileid: "55991573"
---
# <a name="view-and-edit-video-indexer-insights"></a>Visualizzare e modificare le informazioni dettagliate di Video Indexer

Questo argomento illustra come visualizzare e modificare le informazioni dettagliate di Video Indexer per un video.

1. Passare al sito Web di [Video Indexer](https://www.videoindexer.ai/) ed eseguire l'accesso.
2. Trovare un video da cui si vogliono creare le informazioni dettagliate di Video Indexer. Per altre informazioni, vedere [Trovare momenti esatti nei video](video-indexer-search.md).
3. Premere **Riproduci**.

    La pagina mostra le informazioni dettagliate riepilogate del video. 

    ![Informazioni dettagliate](./media/video-indexer-view-edit/video-indexer-summarized-insights.png)

4. Visualizzare le informazioni dettagliate riepilogate del video. 

    Le informazioni dettagliate riepilogate contengono una visualizzazione aggregata dei dati: visi, parole chiave e sentiment. Ad esempio, è possibile vedere i visi delle persone, gli intervalli di tempo in cui compare ognuno di essi e la percentuale del tempo per cui viene visualizzato.

    Il lettore e le informazioni dettagliate sono sincronizzati. Ad esempio, se si fa clic su una parola chiave o sulla riga della trascrizione, il lettore passa a quel momento nel video. È possibile implementare la visualizzazione e la sincronizzazione del lettore e delle informazioni dettagliate nella propria applicazione. Per altre informazioni, vedere [Incorporare i widget di Video Indexer di Azure nelle applicazioni](video-indexer-embed-widgets.md). 

3. Modificare le informazioni dettagliate di Video Indexer.

    Premere Modifica sotto il video. La pagina che compare mostra tutte le informazioni dettagliate sul video. Queste sono suddivise in blocchi. I blocchi sono concepiti per semplificare l'esame dei dati. Ad esempio, i blocchi potrebbero essere suddivisi in base a quando cambia il parlante o alla presenza di una lunga pausa. È possibile creare una propria playlist contenente solo le righe desiderate. Per mostrare solo determinate parti del video di origine, è possibile filtrare per argomenti/parole chiave, sentiment, persone e relatori. Si può scegliere di visualizzare solo la trascrizione o OCR del video.  

    ![Informazioni dettagliate](./media/video-indexer-view-edit/video-indexer-create-new-playlist.png)

## <a name="next-steps"></a>Passaggi successivi

[Informazioni su come creare informazioni dettagliate di Video Indexer basate su video di altre persone](video-indexer-create-new.md).

## <a name="see-also"></a>Vedere anche 

[Panoramica di Video Indexer](video-indexer-overview.md)

