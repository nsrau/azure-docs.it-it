---
title: Installare i contenitori di sintesi vocale
titleSuffix: Azure Cognitive Services
description: Descrive le opzioni di configurazione del grafico Helm Umbrella.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: ed64412ccf9d192506fafe546b1ccee7941aa43a
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73523183"
---
### <a name="speech-umbrella-chart"></a>Sintesi vocale (grafico a ombrelli)

I valori nel grafico "Umbrella" di primo livello sostituiscono i valori corrispondenti dei sottografici. Pertanto, tutti i valori personalizzati locali devono essere aggiunti qui.

|Parametro|Descrizione|Default|
| -- | -- | -- | -- |
| `speechToText.enabled` | Indica se il servizio **di riconoscimento vocale** è abilitato. | `true` |
| `speechToText.verification.enabled` | Indica se la funzionalità `helm test` per il servizio **riconoscimento vocale** è abilitata. | `true` |
| `speechToText.verification.image.registry` | Archivio di immagini Docker usato da `helm test` per testare il servizio **di riconoscimento vocale** . Helm crea un pod separato all'interno del cluster per il testing ed estrae l'immagine di *test-use* da questo registro. | `docker.io` |
| `speechToText.verification.image.repository` | Archivio di immagini Docker usato da `helm test` per testare il servizio **di riconoscimento vocale** . Helm test Pod usa questo repository per eseguire il pull dell'immagine *test-use* . | `antsu/on-prem-client` |
| `speechToText.verification.image.tag` | Tag dell'immagine Docker usato con `helm test` per il servizio **di riconoscimento vocale** . Helm test Pod usa questo tag per eseguire il pull dell'immagine di *test-uso* . | `latest` |
| `speechToText.verification.image.pullByHash` | Indica se viene eseguito il pull dell'immagine Docker per l' *utilizzo dei test* tramite hash. Se `true`, è necessario aggiungere `speechToText.verification.image.hash` con un valore hash dell'immagine valido. | `false` |
| `speechToText.verification.image.arguments` | Argomenti usati per eseguire l'immagine Docker per l' *uso dei test* . Helm test Pod passa questi argomenti al contenitore durante l'esecuzione `helm test`. | `"./speech-to-text-client"`<br/> `"./audio/whatstheweatherlike.wav"` <br/> `"--expect=What's the weather like"`<br/>`"--host=$(SPEECH_TO_TEXT_HOST)"`<br/>`"--port=$(SPEECH_TO_TEXT_PORT)"` |
| `textToSpeech.enabled` | Indica se il servizio **di sintesi vocale** è abilitato. | `true` |
| `textToSpeech.verification.enabled` | Indica se la funzionalità `helm test` per il servizio **riconoscimento vocale** è abilitata. | `true` |
| `textToSpeech.verification.image.registry` | Archivio di immagini Docker usato da `helm test` per testare il servizio **di riconoscimento vocale** . Helm crea un pod separato all'interno del cluster per il testing ed estrae l'immagine di *test-use* da questo registro. | `docker.io` |
| `textToSpeech.verification.image.repository` | Archivio di immagini Docker usato da `helm test` per testare il servizio **di riconoscimento vocale** . Helm test Pod usa questo repository per eseguire il pull dell'immagine *test-use* . | `antsu/on-prem-client` |
| `textToSpeech.verification.image.tag` | Tag dell'immagine Docker usato con `helm test` per il servizio **di riconoscimento vocale** . Helm test Pod usa questo tag per eseguire il pull dell'immagine di *test-uso* . | `latest` |
| `textToSpeech.verification.image.pullByHash` | Indica se viene eseguito il pull dell'immagine Docker per l' *utilizzo dei test* tramite hash. Se `true`, è necessario aggiungere `textToSpeech.verification.image.hash` con un valore hash dell'immagine valido. | `false` |
| `textToSpeech.verification.image.arguments` | Argomenti da eseguire con l'immagine Docker per l' *uso dei test* . Il pod di test Helm passa questi argomenti al contenitore durante l'esecuzione `helm test`. | `"./text-to-speech-client"`<br/> `"--input='What's the weather like'"` <br/> `"--host=$(TEXT_TO_SPEECH_HOST)"`<br/>`"--port=$(TEXT_TO_SPEECH_PORT)"` |