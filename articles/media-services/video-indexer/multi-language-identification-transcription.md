---
title: Identificare e trascrivere automaticamente contenuto multilingue con Video Indexer
titleSuffix: Azure Media Services
description: In questo argomento viene illustrato come identificare e trascrivere automaticamente contenuto multilingue con Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/01/2019
ms.author: juliako
ms.openlocfilehash: 983b66be6b51c9af5987e539ea1175a65c9269b3
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70862044"
---
# <a name="automatically-identify-and-transcribe-multi-language-content-preview"></a>Identificare e trascrivere automaticamente il contenuto multilingue (anteprima)

Video Indexer supporta l'identificazione e la trascrizione automatica della lingua in contenuto multilingue. Questo processo implica l'identificazione automatica della lingua parlata in segmenti diversi dall'audio, l'invio di ogni segmento del file multimediale da trascrivere e la ricombinazione della trascrizione con una trascrizione unificata. 

## <a name="choosing-multilingual-identification-on-indexing-with-portal"></a>Scelta dell'identificazione multilingue per l'indicizzazione con il portale

È possibile scegliere il **rilevamento multilingua** durante il caricamento e l'indicizzazione del video. In alternativa, è possibile scegliere il **rilevamento multilingua** durante la reindicizzazione del video. Nei passaggi seguenti viene descritto come reindicizzare:

1. Passare al sito Web di [Video Indexer](https://vi.microsoft.com/) ed eseguire l'accesso.
1. Passare alla pagina **libreria** e passare il puntatore del mouse sul nome del video che si vuole reindicizzare. 
1. Nell'angolo in basso a destra fare clic sul pulsante di **reindicizzare il video** . 
1. Nella finestra di dialogo **re-index video** scegliere **rilevamento multilingua** dalla casella di riepilogo a discesa **lingua di origine video** .

    * Quando un video viene indicizzato come multilingue, nella pagina Insight sarà inclusa tale opzione e verrà visualizzato un ulteriore tipo di informazioni, che consente all'utente di visualizzare il segmento in cui si trova la lingua "lingua parlata".
    * La conversione in tutte le lingue è completamente disponibile dalla trascrizione in più lingue.
    * Tutte le altre informazioni dettagliate verranno visualizzate nella lingua Master rilevata, ovvero la lingua che è apparsa più di recente nell'audio.
    * I sottotitoli codificati per il lettore sono disponibili anche in lingue diverse.

![Funzionalità del portale](./media/multi-language-identification-transcription/portal-experience.png)

## <a name="choosing-multilingual-identification-on-indexing-with-api"></a>Scelta dell'identificazione multilingue per l'indicizzazione con l'API

Durante l'indicizzazione o la [reindicizzazione](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) di un video tramite l' `multi-language detection` API, scegliere `sourceLanguage` l'opzione nel parametro.

### <a name="model-output"></a>Output modello

Il modello recupererà tutte le lingue rilevate nel video in un elenco

```json
"sourceLanguage": null,
"sourceLanguages": [
    "es-ES",
    "en-US"
],
```

Inoltre, ogni istanza nella sezione trascrizione includerà la lingua in cui è stata trasscritta

```json
{
  "id": 136,
  "text": "I remember well when my youth Minister took me to hear Doctor King I was a teenager.",
  "confidence": 0.9343,
  "speakerId": 1,
  "language": "en-US",
  "instances": [
    {
       "adjustedStart": "0:21:10.42",
       "adjustedEnd": "0:21:17.48",
       "start": "0:21:10.42",
       "end": "0:21:17.48"
    }
  ]
},
```

## <a name="guidelines-and-limitations"></a>Linee guida e limitazioni

* Set di lingue supportate: Inglese, francese, tedesco, spagnolo.
* Supporto per contenuti multilingue con fino a tre lingue supportate.
* Se l'audio contiene lingue diverse dall'elenco supportato, il risultato è imprevisto.
* Lunghezza minima del segmento da rilevare per ogni lingua: 15 secondi.
* L'offset del rilevamento della lingua è medio di 3 secondi.
* Il discorso dovrebbe essere continuo. Le alternanze frequenti tra le lingue possono influenzare le prestazioni dei modelli.
* Il riconoscimento vocale degli altoparlanti non nativi può influire sulle prestazioni del modello, ad esempio quando gli speaker usano la lingua nativa e passano a un'altra lingua.
* Il modello è progettato per riconoscere un discorso di conversazione spontaneo con acustica audio ragionevole (non comandi vocali, canto e così via).
* La creazione e la modifica di progetti non sono attualmente disponibili per i video multilingue.
* I modelli di linguaggio personalizzati non sono disponibili quando si usa il rilevamento in più lingue.
* L'aggiunta di parole chiave non è supportata.
* l'API "aggiornamento della trascrizione" non è supportata.
* Quando si esportano file di didascalia chiusi, l'indicazione della lingua non verrà visualizzata.


## <a name="next-steps"></a>Passaggi successivi

[Panoramica di Video Indexer](video-indexer-overview.md)
