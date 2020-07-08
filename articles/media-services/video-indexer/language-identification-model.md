---
title: Usare Video Indexer per identificare automaticamente le lingue parlate-Azure
titleSuffix: Azure Media Services
description: Questo articolo descrive il modo in cui viene usato il modello di identificazione della lingua Video Indexer per identificare automaticamente la lingua parlata in un video.
services: media-services
author: juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/12/2020
ms.author: ellbe
ms.openlocfilehash: 3a71a29fdf4af10162e2f7961fb457d0e99b18e8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81687130"
---
# <a name="automatically-identify-the-spoken-language-with-language-identification-model"></a>Identificazione automatica della lingua parlata con il modello di identificazione della lingua

Video Indexer supporta l'identificazione automatica della lingua (LID), ovvero il processo di identificazione automatica del contenuto della lingua parlata dall'audio e l'invio del file multimediale da trascrivere nel linguaggio identificato dominante. 

Attualmente il coperchio supporta: inglese, spagnolo, francese, tedesco, italiano, cinese mandarino, giapponese, russo e portoghese (brasiliano). 

Assicurarsi di esaminare le [linee guida e le limitazioni](#guidelines-and-limitations) riportate di seguito.

## <a name="choosing-auto-language-identification-on-indexing"></a>Scelta dell'identificazione della lingua automatica nell'indicizzazione

Durante l'indicizzazione o la [reindicizzazione](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) di un video tramite l'API, scegliere l' `auto detect` opzione nel `sourceLanguage` parametro.

Quando si usa il portale, accedere ai **video dell'account** nella [video Indexer](https://www.videoindexer.ai/) Home page e passare il puntatore del mouse sul nome del video che si vuole indicizzare nuovamente. Nell'angolo in basso a destra fare clic sul pulsante re-index. Nella finestra di dialogo **re-index video** scegliere *rilevamento automatico* dalla casella di riepilogo a discesa **lingua di origine video** .

![rilevamento automatico](./media/language-identification-model/auto-detect.png)

## <a name="model-output"></a>Output del modello

Video Indexer Trascriva il video in base alla lingua più probabile se la confidenza di tale lingua è `> 0.6` . Se la lingua non può essere identificata in tutta sicurezza, presuppone che la lingua parlata sia l'inglese. 

Il linguaggio dominante del modello è disponibile nel file JSON di Insights come `sourceLanguage` attributo (in root/video/Insights). Un punteggio di confidenza corrispondente è disponibile anche nell' `sourceLanguageConfidence` attributo.

```json
"insights": {
        "version": "1.0.0.0",
        "duration": "0:05:30.902",
        "sourceLanguage": "fr-FR",
        "language": "fr-FR",
        "transcript": [...],
        . . .
        "sourceLanguageConfidence": 0.8563
      },
```

## <a name="guidelines-and-limitations"></a>Linee guida e limitazioni

* L'identificazione automatica della lingua (LID) supporta le seguenti lingue: 

    Inglese, spagnolo, francese, tedesco, italiano, mandarino Mentos, giapponese, russo e portoghese (brasiliano).
* Anche se Video Indexer supporta l'arabo (standard moderno e levantina), Hindi e coreano, questi linguaggi non sono supportati nel coperchio.
* Se l'audio contiene lingue diverse dall'elenco supportato, il risultato è imprevisto.
* Se Video Indexer non è in grado di identificare la lingua con una confidenza sufficientemente elevata ( `>0.6` ), la lingua di fallback è l'inglese.
* Non è disponibile alcun supporto corrente per file con audio in lingue miste. Se l'audio contiene lingue miste, il risultato è imprevisto. 
* L'audio di bassa qualità può influito sui risultati del modello.
* Il modello richiede almeno un minuto di riconoscimento vocale nell'audio.
* Il modello è progettato per riconoscere un discorso di conversazione spontanea (non comandi vocali, canto e così via).

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica](video-indexer-overview.md)
* [Identificare e trascrivere automaticamente contenuto multilingue](multi-language-identification-transcription.md)
