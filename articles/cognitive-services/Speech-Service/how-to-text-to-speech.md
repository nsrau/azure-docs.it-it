---
title: Usare Sintesi vocale tramite i servizi di riconoscimento vocale | Microsoft Docs
description: Informazioni su come usare Sintesi vocale nel Servizio di riconoscimento vocale.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 84b577cd00d333717a7a5ad1f66182605429f9f1
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213334"
---
# <a name="use-text-to-speech-in-speech-service"></a>Usare "Sintesi vocale" nel Servizio di riconoscimento vocale

Il Servizio di riconoscimento vocale offre la funzionalità Sintesi vocale attraverso una semplice richiesta HTTP. Dopo aver eseguito il POST del testo da pronunciare nell'endpoint appropriato, l'utente riceve dal servizio un file audio (`.wav`) contenente la sintesi vocale. L'applicazione può quindi usare questo audio nel modo previsto.

Il corpo della richiesta POST per Sintesi vocale può essere costituito da testo normale (ASCII o UTF8) o da un documento [SSML](speech-synthesis-markup.md). Se il testo della richiesta è in formato testo normale, viene pronunciato con una voce predefinita. Nella maggior parte dei casi, è consigliabile usare un corpo in formato SSML. La richiesta HTTP deve includere un token di [autorizzazione](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis#authentication). 

Ecco gli endpoint Sintesi vocale per diverse aree. Usare quello appropriato per la sottoscrizione.

[!include[](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

## <a name="specify-a-voice"></a>Specificare una voce

Per specificare una voce, usare il tag `<voice>` [SSML](speech-synthesis-markup.md). Ad esempio: 

```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
  <voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
    Hello, world!
  </voice>
</speak>
```

Per l'elenco delle voci disponibili e dei loro nomi, vedere [Text to Speech voices](supported-languages.md#text-to-speech) (Voci di Sintesi vocale).

## <a name="make-a-request"></a>Effettuare una richiesta

Una richiesta HTTP di Sintesi vocale viene eseguita in modalità POST con il testo da pronunciare all'interno del corpo della richiesta stessa. La lunghezza massima del corpo di una richiesta HTTP è di 1024 caratteri. La richiesta deve avere le intestazioni seguenti: 

Intestazione|Valori|Commenti
-|-|-
|`Content-Type` | `application/ssml+xml` | Formato del testo di input.
|`X-Microsoft-OutputFormat`|     `raw-16khz-16bit-mono-pcm`<br>`audio-16khz-16kbps-mono-siren`<br>`riff-16khz-16kbps-mono-siren`<br>`riff-16khz-16bit-mono-pcm`<br>`audio-16khz-128kbitrate-mono-mp3`<br>`audio-16khz-64kbitrate-mono-mp3`<br>`audio-16khz-32kbitrate-mono-mp3`<br>`raw-24khz-16bit-mono-pcm`<br>`riff-24khz-16bit-mono-pcm`<br>`audio-24khz-160kbitrate-mono-mp3`<br>`audio-24khz-96kbitrate-mono-mp3`<br>`audio-24khz-48kbitrate-mono-mp3` | Formato audio di output.
|`User-Agent`   |Nome dell'applicazione | Il nome dell'applicazione è obbligatorio e deve essere più breve di 255 caratteri.
| `Authorization`   | Token di autorizzazione ottenuto presentando la chiave di sottoscrizione al servizio token. Ogni token è valido per 10 minuti. Vedere [REST APIs: Authentication](rest-apis.md#authentication) (API REST: autenticazione).

> [!NOTE]
> Se la voce selezionata e il formato di output hanno velocità in bit diverse, il campionamento audio viene ripetuto secondo necessità. Le voci a 24 kHz non supportano i formati di output `audio-16khz-16kbps-mono-siren` e `riff-16khz-16kbps-mono-siren`. 

Di seguito è riportata una richiesta di esempio.

```xml
POST /cognitiveservices/v1
HTTP/1.1
Host: westus.tts.speech.microsoft.com
X-Microsoft-OutputFormat: riff-24khz-16bit-mono-pcm
Content-Type: application/ssml+xml
User-Agent: Test TTS application
Authorization: (authorization token)

<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
    Hello, world!
</voice> </speak>
```

Il corpo della risposta, con stato 200, contiene l'audio nel formato di output specificato.

```
HTTP/1.1 200 OK
Content-Length: XXX
Content-Type: audio/x-wav

Response audio payload
```

Se si verifica un errore, vengono usati i codici di stato seguenti. Il corpo della risposta in caso di errore contiene anche una descrizione del problema.

|Codice|DESCRIZIONE|Problema|
|-|-|-|
400 |Bad Request |Un parametro obbligatorio è mancante, vuoto o Null. In alternativa, il valore passato a un parametro obbligatorio o facoltativo non è valido. Un problema comune è la lunghezza eccessiva dell'intestazione.
401|Non autorizzata |La richiesta non è autorizzata. Assicurarsi che la chiave di sottoscrizione o il token sia valido.
413|Entità della richiesta troppo grande|La lunghezza dell'input SSML è maggiore di 1024 caratteri.
|502|Gateway non valido    | Problema di rete o lato server. Può anche indicare intestazioni non valide.

Per altre informazioni sull'API REST di Sintesi vocale, vedere [API REST](rest-apis.md#text-to-speech).

## <a name="next-steps"></a>Passaggi successivi

- [Ottenere una sottoscrizione di valutazione gratuita del Servizio di riconoscimento vocale](https://azure.microsoft.com/try/cognitive-services/)
- [Riconoscimento vocale in C++](quickstart-cpp-windows.md)
- [Riconoscimento vocale in C#](quickstart-csharp-dotnet-windows.md)
- [Riconoscimento vocale in Java](quickstart-java-android.md)
