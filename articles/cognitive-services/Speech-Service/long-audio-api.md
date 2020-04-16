---
title: API audio lunga (anteprima) - Servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Scopri come l'API Long Audio è progettata per la sintesi asincrona della sintesi vocale in formato lungo.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: trbye
ms.openlocfilehash: b7cca314ec59e46cf17751b1aec28b5c3ea029ed
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81401068"
---
# <a name="long-audio-api-preview"></a>API audio lunga (anteprima)Long Audio API (Preview)

L'API Long Audio è progettata per la sintesi asincrona della sintesi vocale in formato lungo (ad esempio: audiolibri). Questa API non restituisce l'audio sintetizzato in tempo reale, ma si prevede che si esegue il polling per le risposte e utilizzare l'output(s) come vengono resi disponibili dal servizio. A differenza dell'API di sintesi vocale utilizzata da Speech SDK, l'API Audio lungo può creare audio sintetizzato più di 10 minuti, rendendolo ideale per gli editori e le piattaforme di contenuti audio.

Ulteriori vantaggi dell'API Long Audio:

* Il parlato sintetizzato restituito dal servizio utilizza voci neurali, che garantiscono output audio ad alta fedeltà.
* Poiché le risposte in tempo reale non sono supportate, non è necessario distribuire un endpoint vocale.

> [!NOTE]
> L'API Audio lungo ora supporta solo [la voce neurale personalizzata.](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice#custom-neural-voices)

## <a name="workflow"></a>Flusso di lavoro

In genere, quando si utilizza l'API Long Audio, si invia un file di testo o file da sintetizzare, eseguire il polling per lo stato, quindi se lo stato ha esito positivo, è possibile scaricare l'output audio.

Questo diagramma fornisce una panoramica generale del flusso di lavoro.

![Diagramma del flusso di lavoro dell'API Audio lunga](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>Preparare il contenuto per la sintesi

Quando si prepara il file di testo, assicurarsi che:

* È testo normale (.txt) o testo SSML (.txt)
* È codificato come [UTF-8 con Byte Order Mark (BOM)](https://www.w3.org/International/questions/qa-utf8-bom.en#bom)
* È un singolo file, non un zip
* Contiene più di 400 caratteri per testo normale o 400 [caratteri fatturabili](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech#pricing-note) per il testo SSML e meno di 10.000 paragrafi
  * Per il testo normale, ogni paragrafo viene separato premendo **Invio/Invio** - Visualizza esempio di [input di testo normale](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt)
  * Per il testo SSML, ogni parte SSML è considerata un paragrafo. I pezzi SSML devono essere separati da paragrafi diversi - Visualizza l'esempio di input di [testo SSML](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt)
> [!NOTE]
> Per il cinese (mainland), il cinese (Hong Kong), il cinese (Taiwan), il giapponese e il coreano, una parola verrà conteggiata come due caratteri. 

## <a name="submit-synthesis-requests"></a>Inviare richieste di sintesi

Dopo aver preparato il contenuto di input, seguire la guida introduttiva alla [sintesi audio in formato lungo](https://aka.ms/long-audio-python) per inviare la richiesta. Se si dispone di più di un file di input, sarà necessario inviare più richieste. Ci sono alcune limitazioni di cui essere a conoscenza: 
* Il client può inviare fino a 5 richieste al server al secondo per ogni account di sottoscrizione di Azure.Client is allowed to submit up to 5 requests to server per second for each Azure subscription account. Se supera il limite, client otterrà un codice di errore 429 (troppe richieste). Si prega di ridurre l'importo della richiesta al secondo
* Il server può eseguire e accodare fino a 120 richieste per ogni account di sottoscrizione di Azure.Server is allowed to run and queue up to 120 requests for each Azure subscription account. Se supera il limite, il server restituirà un codice di errore 429 (troppe richieste). Si prega di attendere ed evitare di inviare nuova richiesta fino a quando alcune richieste sono completate
* Il server manterrà fino a 20.000 richieste per ogni account di sottoscrizione di Azure.Server will keep up to 20,000 requests for each Azure subscription account. Se supera il limite, elimina alcune richieste prima di inviarne di nuove

## <a name="audio-output-formats"></a>Formati di output audio

Supportiamo formati di output audio flessibili. È possibile generare uscite audio per paragrafo o concatenare gli audio in un unico output impostando il parametro 'concatenateResult'. I seguenti formati di output audio sono supportati dall'API Long Audio:

> [!NOTE]
> Il formato audio predefinito è riff-16khz-16bit-mono-pcm.

* Riff-8khz-16bit-mono-pcm
* Riff-16khz-16bit-mono-pcm
* Riff-24khz-16bit-mono-pcm
* Riff-48khz-16bit-mono-pcm
* audio-16khz-32kbitrate-mono-mp3
* audio-16khz-64kbitrate-mono-mp3
* audio-16khz-128kbitrate-mono-mp3
* audio-24khz-48kbitrate-mono-mp3
* audio-24khz-96kbitrate-mono-mp3
* audio-24khz-160kbitrate-mono-mp3

## <a name="quickstarts"></a>Avvi rapidi

Offriamo guide introduttive progettate per aiutarti a eseguire correttamente l'API Long Audio. Questa tabella include un elenco di guide introduttive dell'API Long Audio organizzate in base alla lingua.

* [Guida introduttiva: Python](https://aka.ms/long-audio-python)

## <a name="sample-code"></a>Codice di esempio
Il codice di esempio per l'API Long Audio è disponibile su GitHub.Sample code for Long Audio API is available on GitHub.

* [Codice di esempio: Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [Codice di esempio: C #](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Codice di esempio: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
