---
title: Identificazione automatica della lingua parlata con Video Indexer-Azure
titleSuffix: Azure Media Services
description: Questo articolo descrive il modo in cui viene usato il modello di identificazione della lingua Video Indexer per identificare automaticamente la lingua parlata in un video.
services: media-services
author: juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/12/2019
ms.author: ellbe
ms.openlocfilehash: ce3e488a6387f9a823d7c1b514b52af24944776b
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839004"
---
# <a name="automatically-identify-the-spoken-language-with-language-identification-model"></a>Identificazione automatica della lingua parlata con il modello di identificazione della lingua

Video Indexer supporta l'identificazione automatica della lingua (LID), ovvero il processo di identificazione automatica del contenuto della lingua parlata dall'audio e l'invio del file multimediale da trascrivere nel linguaggio identificato dominante. Attualmente il coperchio supporta inglese, spagnolo, francese, tedesco, italiano, cinese (semplificato), giapponese, russo e portoghese (brasiliano). 

## <a name="choosing-auto-language-identification-on-indexing"></a>Scelta dell'identificazione della lingua automatica nell'indicizzazione

Durante l'indicizzazione o la [reindicizzazione](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) di un video tramite l'API, scegliere l'opzione `auto detect` nel parametro `sourceLanguage`.

Quando si usa il portale, accedere ai **video dell'account** nella [video Indexer](https://www.videoindexer.ai/) Home page e passare il puntatore del mouse sul nome del video che si vuole indicizzare nuovamente. Nell'angolo in basso a destra fare clic sul pulsante re-index. Nella finestra di dialogo **re-index video** scegliere *rilevamento automatico* dalla casella di riepilogo a discesa **lingua di origine video** .

![rilevamento automatico](./media/language-identification-model/auto-detect.png)

## <a name="model-output"></a>Output modello

Video Indexer Trascriva il video in base alla lingua più probabile se la confidenza per tale lingua è `> 0.6`. Se la lingua non può essere identificata in tutta sicurezza, presuppone che la lingua parlata sia l'inglese. 

Il linguaggio dominante del modello è disponibile nel file JSON di Insights come attributo `sourceLanguage` (in root/video/Insights). Un punteggio di confidenza corrispondente è disponibile anche nell'attributo `sourceLanguageConfidence`.

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

* Le lingue supportate sono inglese, spagnolo, francese, tedesco, italiano, cinese (semplificato), giapponese, russo e portoghese (Brasile).
* Se l'audio contiene lingue diverse dall'elenco supportato, il risultato è imprevisto.
* Se Video Indexer non è in grado di identificare la lingua con una confidenza sufficientemente elevata (`>0.6`), la lingua di fallback è l'inglese.
* Non è disponibile alcun supporto corrente per file con audio in lingue miste. Se l'audio contiene lingue miste, il risultato è imprevisto. 
* L'audio di bassa qualità può influito sui risultati del modello.
* Il modello richiede almeno un minuto di riconoscimento vocale nell'audio.
* Il modello è progettato per riconoscere un discorso di conversazione spontanea (non comandi vocali, canto e così via).

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica](video-indexer-overview.md)
* [Identificare e trascrivere automaticamente contenuto multilingue](multi-language-identification-transcription.md)
