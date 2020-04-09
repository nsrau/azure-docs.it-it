---
title: Installare i contenitori di riconoscimento vocaleInstall Speech containers
titleSuffix: Azure Cognitive Services
description: Dettaglio le opzioni di configurazione del grafico del timone ombrello vocale vocale.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 0257f3af44cc85d0a3656472db224ae5a7e19161
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80874348"
---
### <a name="speech-umbrella-chart"></a>Discorso (grafico ombrello)

I valori nel grafico "ombrello" di primo livello sostituiscono i valori dei sottografi corrispondenti. Pertanto, tutti i valori personalizzati locali devono essere aggiunti qui.

|Parametro|Descrizione|Predefinito|
| -- | -- | -- | -- |
| `speechToText.enabled` | Se il servizio **di sintesi vocale** è abilitato. | `true` |
| `speechToText.verification.enabled` | Se `helm test` la funzionalità per il servizio **di sintesi vocale** è abilitata. | `true` |
| `speechToText.verification.image.registry` | Archivio immagini docker che `helm test` utilizza per testare il servizio di **sintesi vocale.** Helm crea un pod separato all'interno del cluster per il test ed estrae l'immagine di *test-uso* da questo Registro di sistema. | `docker.io` |
| `speechToText.verification.image.repository` | Archivio immagini docker che `helm test` utilizza per testare il servizio di **sintesi vocale.** Il pod di test Helm utilizza questo repository per eseguire il pull dell'immagine *di test.* | `antsu/on-prem-client` |
| `speechToText.verification.image.tag` | Tag dell'immagine docker utilizzato con `helm test` per il servizio di **sintesi vocale.** Il pod test Helm utilizza questo tag per estrarre l'immagine *di prova.* | `latest` |
| `speechToText.verification.image.pullByHash` | Se l'immagine docker *di utilizzo* del test viene estratta dall'hash. Se `true` `speechToText.verification.image.hash` , è necessario aggiungere , con valore hash immagine valido. | `false` |
| `speechToText.verification.image.arguments` | Argomenti utilizzati per eseguire l'immagine docker di utilizzo del *test.* Helm test pod passa questi argomenti `helm test`al contenitore durante l'esecuzione di . | `"./speech-to-text-client"`<br/> `"./audio/whatstheweatherlike.wav"` <br/> `"--expect=What's the weather like"`<br/>`"--host=$(SPEECH_TO_TEXT_HOST)"`<br/>`"--port=$(SPEECH_TO_TEXT_PORT)"` |
| `textToSpeech.enabled` | Se il servizio di **sintesi vocale** è abilitato. | `true` |
| `textToSpeech.verification.enabled` | Se `helm test` la funzionalità per il servizio **di sintesi vocale** è abilitata. | `true` |
| `textToSpeech.verification.image.registry` | Archivio immagini docker che `helm test` utilizza per testare il servizio di **sintesi vocale.** Helm crea un pod separato all'interno del cluster per il test ed estrae l'immagine di *test-uso* da questo Registro di sistema. | `docker.io` |
| `textToSpeech.verification.image.repository` | Archivio immagini docker che `helm test` utilizza per testare il servizio di **sintesi vocale.** Il pod di test Helm utilizza questo repository per eseguire il pull dell'immagine *di test.* | `antsu/on-prem-client` |
| `textToSpeech.verification.image.tag` | Tag dell'immagine docker utilizzato con `helm test` per il servizio di **sintesi vocale.** Il pod test Helm utilizza questo tag per estrarre l'immagine *di prova.* | `latest` |
| `textToSpeech.verification.image.pullByHash` | Se l'immagine docker *di utilizzo* del test viene estratta dall'hash. Se `true` `textToSpeech.verification.image.hash` , è necessario aggiungere , con valore hash immagine valido. | `false` |
| `textToSpeech.verification.image.arguments` | Argomenti da eseguire con l'immagine docker di utilizzo del *test.* Il pod di test del timone passa questi argomenti al contenitore durante l'esecuzione `helm test`di . | `"./text-to-speech-client"`<br/> `"--input='What's the weather like'"` <br/> `"--host=$(TEXT_TO_SPEECH_HOST)"`<br/>`"--port=$(TEXT_TO_SPEECH_PORT)"` |