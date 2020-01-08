---
title: API Long audio (anteprima)-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/26/2019
ms.author: erhopf
ms.openlocfilehash: ff8cdf78d923394caf36610534eb5dcc7de571a4
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75562545"
---
# <a name="long-audio-api-preview"></a>API Long audio (anteprima)

L'API Long audio è progettata per la sintesi asincrona del testo di tipo "Long-Form", ad esempio: audiolibri. Questa API non restituisce audio sintetizzato in tempo reale, ma l'aspettativa è che si esegua il polling delle risposte e si utilizzino gli output non appena vengono resi disponibili dal servizio. A differenza dell'API sintesi vocale usata dall'SDK per la sintesi vocale, l'API Long audio può creare audio sintetizzato per più di 10 minuti, rendendola ideale per gli editori e le piattaforme di contenuto audio.

Vantaggi aggiuntivi dell'API Long audio:

* Il riconoscimento vocale sintetizzato restituito dal servizio usa le voci neurali, che garantiscono output audio ad alta fedeltà.
* Poiché le risposte in tempo reale non sono supportate, non è necessario distribuire un endpoint vocale.

> [!NOTE]
> L'API Long audio supporta ora solo la [voce neurale personalizzata](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice#custom-neural-voices).

## <a name="workflow"></a>Flusso di lavoro

In genere, quando si usa l'API Long audio, si invierà un file di testo o file da sintetizzare, eseguire il polling dello stato, quindi, se lo stato ha esito positivo, è possibile scaricare l'output audio.

Questo diagramma fornisce una panoramica di alto livello del flusso di lavoro.

![Diagramma del flusso di lavoro API Long audio](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>Preparare il contenuto per la sintesi

Quando si prepara il file di testo, verificare che:

* Testo normale (con estensione txt) o testo SSML (. txt)
* Codificata come [UTF-8 con BOM (Byte Order Mark)](https://www.w3.org/International/questions/qa-utf8-bom.en#bom)
* È un singolo file, non un file zip
* Contiene più di 400 caratteri per testo normale o 400 [caratteri fatturabili](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech#pricing-note) per il testo SSML e meno di 10.000 paragrafi
  * Per testo normale, ogni paragrafo viene **separato premendo l'** esempio di [input di testo normale](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt)
  * Per il testo SSML, ogni elemento SSML è considerato un paragrafo. Le parti SSML devono essere separate da paragrafi diversi, [ad esempio visualizzare input di testo SSML](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt)
> [!NOTE]
> Per il cinese (terraferma), il cinese (Hong Kong), il cinese (Taiwan), il giapponese e il coreano, una parola viene conteggiata come due caratteri. 

## <a name="submit-synthesis-requests"></a>Invia richieste di sintesi

Dopo aver preparato il contenuto di input, seguire la [Guida introduttiva alla sintesi audio a lungo](https://aka.ms/long-audio-python) termine per inviare la richiesta. Se è presente più di un file di input, sarà necessario inviare più richieste. È necessario tenere presenti alcune limitazioni: 
* Il client può inviare fino a 5 richieste al server al secondo per ogni account della sottoscrizione di Azure. Se supera la limitazione, il client otterrà un codice di errore 429 (troppe richieste). Ridurre il numero di richieste al secondo
* Il server può eseguire e accodare fino a 120 richieste per ogni account della sottoscrizione di Azure. Se supera la limitazione, il server restituirà un codice di errore 429 (troppe richieste). Attendere ed evitare di inviare una nuova richiesta fino al completamento di alcune richieste
* Il server manterrà fino a 20.000 richieste per ogni account della sottoscrizione di Azure. Se il problema supera il limite, eliminare alcune richieste prima di inviarne di nuove

## <a name="audio-output-formats"></a>Formati di output audio

Sono supportati formati di output audio flessibili. È possibile generare output audio per paragrafo o concatenare gli audio in un output impostando il parametro ' concatenateResult '. I formati di output audio seguenti sono supportati da Long audio API:

> [!NOTE]
> Il formato audio predefinito è riff-16kHz-16 bit-mono-PCM.

* riff-8kHz-16 bit-mono-PCM
* riff-16kHz-16 bit-mono-PCM
* riff-24kHz-16 bit-mono-PCM
* riff-kHz-16 bit-mono-PCM
* audio-16kHz-32kbitrate-mono-MP3
* audio-16kHz-64kbitrate-mono-MP3
* audio-16kHz-128kbitrate-mono-MP3
* audio-24kHz-48kbitrate-mono-MP3
* audio-24kHz-96kbitrate-mono-MP3
* audio-24kHz-160kbitrate-mono-MP3

## <a name="quickstarts"></a>Guide introduttive

Sono disponibili guide introduttive progettate per consentire l'esecuzione corretta dell'API Long audio. Questa tabella include un elenco di guide introduttive per le API audio lunghe organizzate in base alla lingua.

* [Guida introduttiva: Python](https://aka.ms/long-audio-python)

## <a name="sample-code"></a>Codice di esempio
Il codice di esempio per l'API Long audio è disponibile su GitHub.

* [Codice di esempio: Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [Codice di esempio:C#](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Codice di esempio: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
