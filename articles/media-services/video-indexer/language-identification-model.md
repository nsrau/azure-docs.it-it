---
title: Usare l'indicizzatore video per identificare automaticamente le lingue parlate - AzureUse Video Indexer to auto identify spoken languages - Azure
titleSuffix: Azure Media Services
description: Questo articolo descrive come viene utilizzato il modello di identificazione della lingua dell'indicizzatore video per identificare automaticamente la lingua parlata in un video.
services: media-services
author: juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/12/2020
ms.author: ellbe
ms.openlocfilehash: ba1521581316d559eb4e67bafba0061c31cc666b
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272951"
---
# <a name="automatically-identify-the-spoken-language-with-language-identification-model"></a>Identificare automaticamente la lingua parlata con il modello di identificazione della lingua

Video Indexer supporta l'identificazione automatica della lingua (LID), che è il processo di identificazione automatica del contenuto linguistico vocale dall'audio e l'invio del file multimediale da trascrivere nella lingua preferita dominante. 

Attualmente LID supporta: inglese, spagnolo, francese, tedesco, italiano, mandarino chines, giapponese, russo e portoghese (brasiliano). 

Assicurati di leggere la sezione [Linee guida e limitazioni](#guidelines-and-limitations) di seguito.

## <a name="choosing-auto-language-identification-on-indexing"></a>Scelta dell'identificazione automatica della lingua sull'indicizzazione

Quando indicizzare o [reindicizzare](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) un `auto detect` video `sourceLanguage` utilizzando l'API, scegliere l'opzione nel parametro.

Quando utilizzi il portale, vai ai video del tuo **account** nella home page [dell'indicizzatore video](https://www.videoindexer.ai/) e passa il mouse sopra il nome del video che vuoi reindicizzare. Nell'angolo inferiore destro fare clic sul pulsante di reindicizzazione. Nella finestra di dialogo **Reindicizza video,** scegliete *Rilevamento automatico* dalla casella di riepilogo a discesa **Lingua sorgente video.**

![rilevamento automatico](./media/language-identification-model/auto-detect.png)

## <a name="model-output"></a>Output del modello

Video Indexer trascrive il video in base alla lingua più `> 0.6`probabile se la fiducia per quella lingua è . Se la lingua non può essere identificata con fiducia, presuppone che la lingua parlata sia l'inglese. 

Il linguaggio dominante del modello `sourceLanguage` è disponibile nelle informazioni dettagliate JSON come attributo (in root/videos/insights). Un punteggio di confidenza corrispondente è disponibile anche sotto l'attributo. `sourceLanguageConfidence`

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

* LID (Automatic Language Identification) supporta le seguenti lingue: 

    Inglese, spagnolo, francese, tedesco, italiano, mandarino Chines, giapponese, russo e portoghese (brasiliano).
* Anche se Video Indexer supporta l'arabo (Standard moderno e Levante), l'hindi e il coreano, queste lingue non sono supportate in LID.
* Se l'audio contiene lingue diverse dall'elenco supportato sopra, il risultato è imprevisto.
* Se Video Indexer non è in grado`>0.6`di identificare la lingua con una confidenza sufficientemente elevata ( ), la lingua di fallback è l'inglese.
* Non è disponibile alcun supporto corrente per i file con audio in lingue miste. Se l'audio contiene lingue miste, il risultato è imprevisto. 
* L'audio di bassa qualità può influire sui risultati del modello.
* Il modello richiede almeno un minuto di discorso nell'audio.
* Il modello è progettato per riconoscere un discorso conversazionale spontaneo (non comandi vocali, canti, ecc.).

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica](video-indexer-overview.md)
* [Identificare e trascrivere automaticamente contenuti multilingue](multi-language-identification-transcription.md)
