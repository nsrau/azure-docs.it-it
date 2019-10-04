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
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "67717235"
---
### <a name="speech-umbrella-chart"></a>Sintesi vocale (grafico a ombrelli)

I valori nel grafico "Umbrella" di primo livello sostituiscono i valori corrispondenti dei sottografici. Pertanto, tutti i valori personalizzati locali devono essere aggiunti qui.

|Parametro|Descrizione|Predefinito|
| -- | -- | -- | -- |
| `speechToText.enabled` | Indica se il servizio **di riconoscimento vocale** è abilitato. | `true` |
| `speechToText.verification.enabled` | Indica se `helm test` è abilitata la funzionalità per il servizio **di riconoscimento vocale** . | `true` |
| `speechToText.verification.image.registry` | Archivio `helm test` di immagini Docker usato da per testare il servizio **di riconoscimento vocale** . Helm crea un pod separato all'interno del cluster per il testing ed estrae l'immagine di *test-use* da questo registro. | `docker.io` |
| `speechToText.verification.image.repository` | Archivio `helm test` di immagini Docker usato da per testare il servizio **di riconoscimento vocale** . Helm test Pod usa questo repository per eseguire il pull dell'immagine *test-use* . | `antsu/on-prem-client` |
| `speechToText.verification.image.tag` | Tag dell'immagine Docker usato con `helm test` per il servizio **di riconoscimento vocale** . Helm test Pod usa questo tag per eseguire il pull dell'immagine di *test-uso* . | `latest` |
| `speechToText.verification.image.pullByHash` | Indica se viene eseguito il pull dell'immagine Docker per l' *utilizzo dei test* tramite hash. Se `true` ,`speechToText.verification.image.hash` deve essere aggiunto con un valore hash dell'immagine valido. | `false` |
| `speechToText.verification.image.arguments` | Argomenti usati per eseguire l'immagine Docker per l' *uso dei test* . Helm test Pod passa questi argomenti al contenitore durante l'esecuzione `helm test`. | `"./speech-to-text-client"`<br/> `"./audio/whatstheweatherlike.wav"` <br/> `"--expect=What's the weather like"`<br/>`"--host=$(SPEECH_TO_TEXT_HOST)"`<br/>`"--port=$(SPEECH_TO_TEXT_PORT)"` |
| `textToSpeech.enabled` | Indica se il servizio **di sintesi vocale** è abilitato. | `true` |
| `textToSpeech.verification.enabled` | Indica se `helm test` è abilitata la funzionalità per il servizio **di riconoscimento vocale** . | `true` |
| `textToSpeech.verification.image.registry` | Archivio `helm test` di immagini Docker usato da per testare il servizio **di riconoscimento vocale** . Helm crea un pod separato all'interno del cluster per il testing ed estrae l'immagine di *test-use* da questo registro. | `docker.io` |
| `textToSpeech.verification.image.repository` | Archivio `helm test` di immagini Docker usato da per testare il servizio **di riconoscimento vocale** . Helm test Pod usa questo repository per eseguire il pull dell'immagine *test-use* . | `antsu/on-prem-client` |
| `textToSpeech.verification.image.tag` | Tag dell'immagine Docker usato con `helm test` per il servizio **di riconoscimento vocale** . Helm test Pod usa questo tag per eseguire il pull dell'immagine di *test-uso* . | `latest` |
| `textToSpeech.verification.image.pullByHash` | Indica se viene eseguito il pull dell'immagine Docker per l' *utilizzo dei test* tramite hash. Se `true` ,`textToSpeech.verification.image.hash` deve essere aggiunto con un valore hash dell'immagine valido. | `false` |
| `textToSpeech.verification.image.arguments` | Argomenti da eseguire con l'immagine Docker per l' *uso dei test* . Il pod di test Helm passa questi argomenti al contenitore durante `helm test`l'esecuzione. | `"./text-to-speech-client"`<br/> `"--input='What's the weather like'"` <br/> `"--host=$(TEXT_TO_SPEECH_HOST)"`<br/>`"--port=$(TEXT_TO_SPEECH_PORT)"` |