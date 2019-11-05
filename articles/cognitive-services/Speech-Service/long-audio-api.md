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
ms.openlocfilehash: b17b7088276aca6d7d8ed8f2d1cb554479b4de2b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507638"
---
# <a name="long-audio-api-preview"></a>API Long audio (anteprima)

L'API Long audio è progettata per la sintesi asincrona del testo di tipo "Long-Form", ad esempio: audiolibri. Questa API non restituisce audio sintetizzato in tempo reale, ma l'aspettativa è che si esegua il polling delle risposte e si utilizzino gli output non appena vengono resi disponibili dal servizio. A differenza dell'API sintesi vocale usata dall'SDK per la sintesi vocale, l'API Long audio può creare audio sintetizzato per più di 10 minuti, rendendola ideale per gli editori e le piattaforme di contenuto audio.

Vantaggi aggiuntivi dell'API Long audio:

* Il riconoscimento vocale sintetizzato restituito dal servizio usa le voci neurali, che garantiscono output audio ad alta fedeltà.
* Poiché le risposte in tempo reale non sono supportate, non è necessario distribuire un endpoint vocale.

## <a name="workflow"></a>Flusso di lavoro

In genere, quando si usa l'API Long audio, si invierà un file di testo o file da sintetizzare, eseguire il polling dello stato, quindi, se lo stato ha esito positivo, è possibile scaricare l'output audio.

Questo diagramma fornisce una panoramica di alto livello del flusso di lavoro.

![Diagramma del flusso di lavoro API Long audio](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>Preparare il contenuto per la sintesi

Quando si prepara il file di testo, verificare che:

* Testo normale (con estensione txt) o testo SSML (. txt)
  * Per testo normale, ogni paragrafo viene **separato premendo l'** esempio di [input di testo normale](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt)
  * Per il testo SSML, ogni elemento SSML è considerato un paragrafo. Le parti SSML devono essere separate da paragrafi diversi, [ad esempio visualizzare input di testo SSML](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt). Per il codice lingua, vedere [linguaggio di markup sintesi vocale (SSML)](speech-synthesis-markup.md)
* Codificata come [UTF-8 con BOM (Byte Order Mark)](https://www.w3.org/International/questions/qa-utf8-bom.en#bom)
* Contiene più di 10.000 caratteri o più di 50 paragrafi
* È un singolo file, non un file zip

## <a name="audio-output-formats"></a>Formati di output audio

I formati di output audio seguenti sono supportati da Long audio API:

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

## <a name="see-also"></a>Vedere anche

* [Riferimento API Long audio](https://aka.ms/long-audio-ref)
