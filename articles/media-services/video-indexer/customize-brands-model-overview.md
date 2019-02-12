---
title: Personalizzare il modello dei marchi con Video Indexer - Azure
titlesuffix: Azure Media Services
description: Questo articolo fornisce informazioni generali sul modello dei marchi in Video Indexer e come personalizzarlo.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 12/05/2018
ms.author: anzaman
ms.openlocfilehash: 59a865cf6e822ecd1918d0c04b98f0edc0355223
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55815473"
---
# <a name="customize-a-brands-model-in-video-indexer"></a>Personalizzare un modello dei marchi con Video Indexer

Video Indexer supporta il rilevamento del marchio dal parlato e dal testo visivo durante l'indicizzazione e la reindicizzazione di contenuto audio e video. La funzionalità di rilevamento del marchio identifica citazioni di prodotti, servizi e aziende suggerite dal database dei marchi di Bing. Se, ad esempio, il nome Microsoft viene citato in un contenuto audio o video o se appare nel testo visivo di un video, Video Indexer lo rileva come un marchio. I marchi vengono contraddistinti senza ambiguità dagli altri termini in base al contesto.

Il rilevamento del marchio è utile in un'ampia gamma di scenari aziendali come l'archiviazione e l'individuazione di contenuti, la pubblicità contestuale, l'analisi dei social media, l'analisi comparativa di punti vendita al dettaglio e molto altro ancora. La funzione di rilevamento del marchio di Video Indexer consente inoltre di indicizzare citazioni del marchio nel parlato e nel testo visivo tramite il database dei marchi di Bing o mediante la personalizzazione compilando un modello dei marchi personalizzato per ogni account di Video Indexer. Il modello dei marchi personalizzato consente di selezionare se Video Indexer dovrà fare riferimento al database dei marchi di Bing, escludere determinati marchi dal rilevamento (essenzialmente creando un elenco di marchi non consentiti) e includere i marchi che devono far parte del modello ma che potrebbero non essere nel database dei marchi di Bing (essenzialmente creando un elenco di marchi consentiti).

## <a name="out-of-the-box-detection-example"></a>Esempio di rilevamento predefinito

Nella presentazione [Microsoft Build 2017 Giorno 2](https://www.videoindexer.ai/media/ed6ede78ad/), il marchio "Microsoft Windows" appare più volte, talvolta nella trascrizione, altre volte come testo visivo e mai come verbatim. Video Indexer rileva con alta precisione che un termine è un marchio in base al contesto e copre oltre 90.000 marchi predefiniti (in costante aggiornamento). Al minuto 02:25 Video Indexer identifica il brand nel parlato e al minuto 02:40 lo rileva dal testo visivo come parte del logo Windows.

![Panoramica sui marchi](./media/content-model-customization/brands-overview.png)

Se si parla di "windows" (finestre) in ambito edilizio, la parola "windows" non verrà rilevata come marchio grazie ad avanzati algoritmi di Machine Learning in grado di risolvere l'ambiguità in base al contesto. Lo stesso vale per Box (scatola), Apple (mela) e Fox (volpe). Il rilevamento del marchio viene usato per tutte le lingue supportate. Fare clic qui per [l'indice e il video completo dell'intervento su Microsoft Build 2017 Giorno 2](http://www.videoindexer.ai/media/ed6ede78ad/).

Per inserire i propri marchi, consultare la sezione Passaggi successivi.

## <a name="next-steps"></a>Passaggi successivi

[Personalizzare il modello dei marchi usando le API](customize-brands-model-with-api.md)

[Personalizzare il modello dei marchi usando il sito Web](customize-brands-model-with-website.md)
