---
title: Installare i contenitori di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Descrive in dettaglio le opzioni di configurazione grafico helm ombrello di riconoscimento vocale.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: ed64412ccf9d192506fafe546b1ccee7941aa43a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711813"
---
### <a name="speech-umbrella-chart"></a>Riconoscimento vocale (grafico generico)

I valori del grafico di primo livello "ombrello" eseguire l'override di valori del grafico secondario corrispondente. Pertanto, tutti i valori locali personalizzati devono essere aggiunti qui.

|Parametro|Descrizione|Predefinito|
| -- | -- | -- | -- |
| `speechToText.enabled` | Se il **vocale-** servizio è abilitato. | `true` |
| `speechToText.verification.enabled` | Se il `helm test` funzionalità per la **vocale-** servizio è abilitato. | `true` |
| `speechToText.verification.image.registry` | Il repository di immagini docker che `helm test` Usa per testare **vocale-** service. Helm crea pod separata all'interno del cluster per il test ed estrae i *test-uso* immagine dal registro. | `docker.io` |
| `speechToText.verification.image.repository` | Il repository di immagini docker che `helm test` Usa per testare **vocale-** service. Pod test Helm Usa questo repository per eseguire il pull *test-uso* immagine. | `antsu/on-prem-client` |
| `speechToText.verification.image.tag` | Il tag di immagine docker usato con `helm test` per **vocale-** service. Pod test Helm Usa questo tag per eseguire il pull *test-uso* immagine. | `latest` |
| `speechToText.verification.image.pullByHash` | Se il *test-uso* immagine docker viene effettuato il pull dall'hash. Se `true`, `speechToText.verification.image.hash` devono essere aggiunti, con valore hash di immagine valido. | `false` |
| `speechToText.verification.image.arguments` | Argomenti utilizzati per eseguire la *test-uso* immagine docker. Pod test Helm passa questi argomenti per il contenitore quando si esegue `helm test`. | `"./speech-to-text-client"`<br/> `"./audio/whatstheweatherlike.wav"` <br/> `"--expect=What's the weather like"`<br/>`"--host=$(SPEECH_TO_TEXT_HOST)"`<br/>`"--port=$(SPEECH_TO_TEXT_PORT)"` |
| `textToSpeech.enabled` | Se il **sintesi vocale** servizio è abilitato. | `true` |
| `textToSpeech.verification.enabled` | Se il `helm test` funzionalità per la **vocale-** servizio è abilitato. | `true` |
| `textToSpeech.verification.image.registry` | Il repository di immagini docker che `helm test` Usa per testare **vocale-** service. Helm crea pod separata all'interno del cluster per il test ed estrae i *test-uso* immagine dal registro. | `docker.io` |
| `textToSpeech.verification.image.repository` | Il repository di immagini docker che `helm test` Usa per testare **vocale-** service. Pod test Helm Usa questo repository per eseguire il pull *test-uso* immagine. | `antsu/on-prem-client` |
| `textToSpeech.verification.image.tag` | Il tag di immagine docker usato con `helm test` per **vocale-** service. Pod test Helm Usa questo tag per eseguire il pull *test-uso* immagine. | `latest` |
| `textToSpeech.verification.image.pullByHash` | Se il *test-uso* immagine docker viene effettuato il pull dall'hash. Se `true`, `textToSpeech.verification.image.hash` devono essere aggiunti, con valore hash di immagine valido. | `false` |
| `textToSpeech.verification.image.arguments` | Gli argomenti per l'esecuzione con il *test-uso* immagine docker. I pod test helm passa questi argomenti al contenitore durante l'esecuzione `helm test`. | `"./text-to-speech-client"`<br/> `"--input='What's the weather like'"` <br/> `"--host=$(TEXT_TO_SPEECH_HOST)"`<br/>`"--port=$(TEXT_TO_SPEECH_PORT)"` |