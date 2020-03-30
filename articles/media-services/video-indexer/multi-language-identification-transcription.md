---
title: Identificare e trascrivere automaticamente i contenuti multilingue con Video Indexer
titleSuffix: Azure Media Services
description: In questo argomento viene illustrato come identificare e trascrivere automaticamente i contenuti in più lingue con Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/01/2019
ms.author: juliako
ms.openlocfilehash: f0dede42891069bb5d01ddc33f3797c20c5493d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72968733"
---
# <a name="automatically-identify-and-transcribe-multi-language-content-preview"></a>Identificare e trascrivere automaticamente contenuti multilingue (anteprima)

L'indicizzatore video supporta l'identificazione e la trascrizione automatica del linguaggio nei contenuti multilingue. Questo processo prevede l'identificazione automatica della lingua parlata in segmenti diversi dall'audio, l'invio di ogni segmento del file multimediale da trascrivere e combinare la trascrizione in una trascrizione unificata. 

## <a name="choosing-multilingual-identification-on-indexing-with-portal"></a>Scelta dell'identificazione multilingue sull'indicizzazione con il portale

Puoi scegliere il **rilevamento multilingue** durante il caricamento e l'indicizzazione del video. In alternativa, puoi scegliere il **rilevamento multilingue** quando reindicizza il video. I passaggi seguenti descrivono come reindicizzare:

1. Passare al sito Web di [Video Indexer](https://vi.microsoft.com/) ed eseguire l'accesso.
1. Vai alla pagina **Libreria** e passa il mouse sopra il nome del video che vuoi reindicizzare. 
1. Nell'angolo in basso a destra, fai clic sul pulsante **Reindicizza video.** 
1. Nella finestra di dialogo **Reindicizza video,** scegliete **Rilevamento multilingue** dalla casella di riepilogo a discesa **Lingua sorgente video.**

    * Quando un video viene indicizzato come multilingue, la pagina di informazioni includerà tale opzione e verrà visualizzato un tipo di informazioni aggiuntive, che consente all'utente di visualizzare quale segmento è trascritto in quale lingua "lingua parlata".
    * La traduzione in tutte le lingue è completamente disponibile dalla trascrizione multilingue.
    * Tutte le altre informazioni verranno visualizzate nella lingua principale rilevata, ovvero la lingua più visualizzata nell'audio.
    * I sottotitoli codificati sul lettore sono disponibili anche in più lingue.

![Funzionalità del portale](./media/multi-language-identification-transcription/portal-experience.png)

## <a name="choosing-multilingual-identification-on-indexing-with-api"></a>Scelta dell'identificazione multilingue sull'indicizzazione con API

Quando indicizzare o [reindicizzare](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) `multi-language detection` un video `sourceLanguage` utilizzando l'API, scegliere l'opzione nel parametro.

### <a name="model-output"></a>Output del modello

Il modello recupererà tutte le lingue rilevate nel video in un unico elenco

```json
"sourceLanguage": null,
"sourceLanguages": [
    "es-ES",
    "en-US"
],
```

Inoltre, ogni istanza nella sezione di trascrizione includerà il linguaggio in cui è stata trascritta

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

* Set di lingue supportate: inglese, francese, tedesco, spagnolo.
* Supporto per contenuti multilingue con un massimo di tre lingue supportate.
* Se l'audio contiene lingue diverse dall'elenco supportato sopra, il risultato è imprevisto.
* Lunghezza minima del segmento da rilevare per ogni lingua: 15 secondi.
* L'offset del rilevamento della lingua è di 3 secondi in media.
* Si prevede che il discorso sarà continuo. Le diverse alternanze tra le lingue possono influire sulle prestazioni dei modelli.
* Il riconoscimento vocale di persone non madrelingua può influire sulle prestazioni del modello (ad esempio, quando gli oratori utilizzano la lingua madre e passano a un'altra lingua).
* Il modello è progettato per riconoscere un discorso conversazionale spontaneo con un'acustica audio ragionevole (non comandi vocali, canto, ecc.).
* La creazione e l'editing di progetti non sono attualmente disponibili per i video in più lingue.
* I modelli linguistici personalizzati non sono disponibili quando si utilizza il rilevamento multilingue.
* L'aggiunta di parole chiave non è supportata.
* Quando si esportano i file di sottotitoli codificati, l'indicazione della lingua non viene visualizzata.
* L'API della trascrizione di aggiornamento non supporta i file in più lingue.

## <a name="next-steps"></a>Passaggi successivi

[Panoramica di Video Indexer](video-indexer-overview.md)
