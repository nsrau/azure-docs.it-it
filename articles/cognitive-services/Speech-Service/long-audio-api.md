---
title: API Long audio (anteprima)-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Scopri in che modo l'API Long audio è progettata per la sintesi asincrona del testo in formato esteso.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: trbye
ms.openlocfilehash: ca6bff4c1e99bb8e63db212ca57693870afc30e7
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85080641"
---
# <a name="long-audio-api-preview"></a>API Long audio (anteprima)

L'API Long audio è progettata per la sintesi asincrona del testo a lungo termine (ad esempio, libri audio, articoli di notizie e documenti). Questa API non restituisce audio sintetizzato in tempo reale, ma l'aspettativa è che si esegua il polling delle risposte e si utilizzino gli output non appena vengono resi disponibili dal servizio. A differenza dell'API sintesi vocale usata dall'SDK per la sintesi vocale, l'API Long audio può creare audio sintetizzato per più di 10 minuti, rendendola ideale per gli editori e le piattaforme di contenuto audio.

Vantaggi aggiuntivi dell'API Long audio:

* Il riconoscimento vocale sintetizzato restituito dal servizio usa le migliori voci neurali.
* Non è necessario distribuire un endpoint vocale perché sintetizza le voci in modalità batch None in tempo reale.

> [!NOTE]
> L'API Long audio supporta ora sia le [voci neurali pubbliche](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices) che le [voci neurali personalizzate](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice#custom-neural-voices).

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
> Per il cinese (terraferma), il cinese (Hong Kong SAR), il cinese (Taiwan), il giapponese e il coreano, una parola viene conteggiata come due caratteri. 

## <a name="submit-synthesis-requests"></a>Invia richieste di sintesi

Dopo aver preparato il contenuto di input, seguire la [Guida introduttiva alla sintesi audio a lungo](https://aka.ms/long-audio-python) termine per inviare la richiesta. Se è presente più di un file di input, sarà necessario inviare più richieste. 

I **codici di stato http** indicano errori comuni.

| API | Stato codice HTTP | Descrizione | Proposal |
|-----|------------------|-------------|----------|
| Create | 400 | La sintesi vocale non è abilitata in questa area. | Modificare la chiave di sottoscrizione vocale con un'area supportata. |
|        | 400 | Per questa area è valida solo la sottoscrizione vocale **standard** . | Modificare la chiave di sottoscrizione vocale nel piano tariffario "standard". |
|        | 400 | Superare il limite di richieste 20.000 per l'account Azure. Rimuovere alcune richieste prima di inviarne di nuove. | Il server manterrà fino a 20.000 richieste per ogni account Azure. Eliminare alcune richieste prima di inviarne di nuove. |
|        | 400 | Non è possibile usare questo modello nella sintesi vocale: {modelID}. | Verificare che lo stato di {modelID} sia corretto. |
|        | 400 | L'area per la richiesta non corrisponde all'area del modello: {modelID}. | Verificare che l'area di {modelID} corrisponda all'area della richiesta. |
|        | 400 | La sintesi vocale supporta solo il file di testo nella codifica UTF-8 con il marcatore dell'ordine dei byte. | Verificare che i file di input siano nella codifica UTF-8 con il marcatore dell'ordine dei byte. |
|        | 400 | Nella richiesta di sintesi vocale sono consentiti solo input SSML validi. | Verificare che le espressioni SSML di input siano corrette. |
|        | 400 | Il nome vocale {voicename} non è stato trovato nel file di input. | Il nome della voce SSML di input non è allineato con l'ID modello. |
|        | 400 | Il numero di paragrafi nel file di input deve essere minore di 10.000. | Verificare che il numero di paragrafi nel file sia inferiore a 10.000. |
|        | 400 | Il file di input deve contenere più di 400 caratteri. | Verificare che il file di input superi 400 caratteri. |
|        | 404 | Il modello dichiarato nella definizione di sintesi vocale non è stato trovato: {modelID}. | Assicurarsi che {modelID} sia corretto. |
|        | 429 | Superare il limite di sintesi vocale attiva. Attendere il completamento di alcune richieste. | Il server può eseguire e accodare fino a 120 richieste per ogni account Azure. Attendere ed evitare di inviare nuove richieste finché alcune richieste non vengono completate. |
| Tutti       | 429 | Troppe richieste. | Il client può inviare fino a 5 richieste al server al secondo per ogni account Azure. Ridurre il numero di richieste al secondo. |
| Delete    | 400 | L'attività di sintesi vocale è ancora in uso. | È possibile eliminare solo le richieste **completate** o **non riuscite**. |
| GetByID   | 404 | Impossibile trovare l'entità specificata. | Verificare che l'ID di sintesi sia corretto. |

## <a name="regions-and-endpoints"></a>Aree ed endpoint

L'API Long audio è disponibile in più aree con endpoint univoci.

| Region | Endpoint |
|--------|----------|
| Australia orientale | `https://australiaeast.customvoice.api.speech.microsoft.com` |
| Canada centrale | `https://canadacentral.customvoice.api.speech.microsoft.com` |
| Stati Uniti orientali | `https://eastus.customvoice.api.speech.microsoft.com` |
| India centrale | `https://centralindia.customvoice.api.speech.microsoft.com` |
| Stati Uniti centro-meridionali | `https://southcentralus.customvoice.api.speech.microsoft.com` |
| Asia sud-orientale | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| Regno Unito meridionale | `https://uksouth.customvoice.api.speech.microsoft.com` |
| Europa occidentale | `https://westeurope.customvoice.api.speech.microsoft.com` |
| Stati Uniti occidentali 2 | `https://westus2.customvoice.api.speech.microsoft.com` |

## <a name="audio-output-formats"></a>Formati di output audio

Sono supportati formati di output audio flessibili. È possibile generare output audio per paragrafo o concatenare gli output audio in un singolo output impostando il parametro ' concatenateResult '. I formati di output audio seguenti sono supportati da Long audio API:

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

## <a name="quickstarts"></a>Avvi rapidi

Sono disponibili guide introduttive progettate per consentire l'esecuzione corretta dell'API Long audio. Questa tabella include un elenco di guide introduttive per le API audio lunghe organizzate in base alla lingua.

* [Guida introduttiva: Python](https://aka.ms/long-audio-python)

## <a name="sample-code"></a>Codice di esempio
Il codice di esempio per l'API Long audio è disponibile su GitHub.

* [Codice di esempio: Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [Codice di esempio: C #](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Codice di esempio: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
