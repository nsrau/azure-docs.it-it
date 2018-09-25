---
title: API Sintesi vocale del Servizio di riconoscimento vocale Microsoft | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Usare l'API Sintesi vocale per fornire la sintesi vocale in tempo reale in una varietà di voci e lingue
services: cognitive-services
author: priyaravi20
manager: yanbo
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: priyar
ROBOTS: NOINDEX
ms.openlocfilehash: 664b696d5323177eaac2e7ce7b80a7c81ce601f1
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46949401"
---
# <a name="bing-text-to-speech-api"></a>API Sintesi vocale Bing

## <a name="Introduction"></a>Introduzione

L'API Sintesi vocale Bing consente a un'applicazione di inviare richieste HTTP a un server cloud, dove il testo viene immediatamente sintetizzato in una voce dal suono umano e restituita come file audio. Questa API può essere usata in numerosi contesti diversi per fornire la sintesi vocale in tempo reale in una varietà di voci e lingue.

## <a name="VoiceSynReq"></a>Richiesta di sintesi vocale

### <a name="Subscription"></a>Token di autorizzazione

Ogni richiesta di sintesi vocale deve avere un token di accesso JSON Web (JWT). Questo token di accesso viene passato nell'intestazione della richiesta di riconoscimento vocale. Il token ha una scadenza di 10 minuti. Per informazioni su come eseguire la sottoscrizione e ottenere le chiavi API che vengono usate per recuperare i token di accesso JWT validi, vedere la pagina per la [sottoscrizione dei Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/).

La chiave API viene passata al servizio token. Ad esempio: 

```HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

Le informazioni di intestazione necessarie per il token di accesso sono le seguenti.

NOME| Format | DESCRIZIONE
----|----|----
Ocp-Apim-Subscription-Key | ASCII | Your subscription key (Chiave della sottoscrizione)

Il servizio token restituisce il token di accesso JWT come `text/plain`. Il token JWT viene quindi passato come `Base64 access_token` all'endpoint di sintesi vocale sotto forma di intestazione Authorization preceduta dalla stringa di prefisso `Bearer`. Ad esempio: 

`Authorization: Bearer [Base64 access_token]`

I client devono usare l'endpoint seguente per accedere al servizio di sintesi vocale:

`https://speech.platform.bing.com/synthesize`

>[!NOTE]
>Finché non si acquisisce un token di accesso con la chiave di sottoscrizione come descritto in precedenza, questo collegamento genera un errore di risposta `403 Forbidden`.

### <a name="Http"></a>Intestazioni HTTP

La tabella seguente mostra le intestazioni HTTP usate per le richieste di sintesi vocale.

Intestazione |Valore |Commenti
----|----|----
Content-Type | application/ssml+xml | Tipo di contenuto di input.
X-Microsoft-OutputFormat | **1.** ssml-16khz-16bit-mono-tts <br> **2.** raw-16khz-16bit-mono-pcm <br>**3.** audio-16khz-16kbps-mono-siren <br> **4.** riff-16khz-16kbps-mono-siren <br> **5.** riff-16khz-16bit-mono-pcm <br> **6.** audio-16khz-128kbitrate-mono-mp3 <br> **7.** audio-16khz-64kbitrate-mono-mp3 <br> **8.** audio-16khz-32kbitrate-mono-mp3 | Formato audio di output.
X-Search-AppId | GUID (solo esadecimale, senza trattini) | ID che identifica l'applicazione client in modo univoco. Può essere l'ID dello Store per le app. Se non è disponibile, l'ID può essere generato dall'utente per un'applicazione.
X-Search-ClientID | GUID (solo esadecimale, senza trattini) | ID che identifica in modo univoco un'istanza dell'applicazione per ogni installazione.
User-Agent | Nome dell'applicazione | Il nome dell'applicazione è obbligatorio e deve essere più breve di 255 caratteri.
Authorization | Token di autorizzazione |  Vedere le sezione <a href="#Subscription">Token di autorizzazione</a>.

### <a name="InputParam"></a>Parametri di input

Le richieste all'API Sintesi vocale Bing vengono inviate tramite chiamate POST HTTP. Le intestazioni sono specificate nella sezione precedente. Il corpo contiene input SSML (Speech Synthesis Markup Language) che rappresenta il testo da convertire in sintesi vocale. Per una descrizione del markup usato per controllare aspetti della sintesi vocale come la lingua e il sesso del parlante, vedere la [specifica W3C SSML](http://www.w3.org/TR/speech-synthesis/).

>[!NOTE]
>Le dimensioni massime supportate per l'input SSML sono pari a 1.024 caratteri, inclusi tutti i tag.

###  <a name="SampleVoiceOR"></a>Esempio: richiesta di output vocale

Di seguito è riportato un esempio di richiesta di output vocale:

```HTTP
POST /synthesize
HTTP/1.1
Host: speech.platform.bing.com

X-Microsoft-OutputFormat: riff-8khz-8bit-mono-mulaw
Content-Type: application/ssml+xml
Host: speech.platform.bing.com
Content-Length: 197
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female' name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>Microsoft Bing Voice Output API</voice></speak>
```

## <a name="VoiceOutResponse"></a>Risposta di output vocale

L'API Sintesi vocale Bing usa HTTP POST per restituire l'audio al client. La risposta dell'API contiene il flusso audio e il codec e corrisponde al formato di output richiesto. L'audio restituito per una determinata richiesta non deve superare i 15 secondi.

### <a name="SuccessfulRecResponse"></a>Esempio: risposta di sintesi vocale corretta

Il codice seguente è un esempio di risposta JSON a una richiesta di sintesi vocale corretta. I commenti e la formattazione del codice sono stati inclusi solo ai fini dell'esempio, ma non sono presenti nella risposta effettiva.

```HTTP
HTTP/1.1 200 OK
Content-Length: XXX
Content-Type: audio/x-wav

Response audio payload
```

### <a name="RecFailure"></a>Esempio: errore di sintesi vocale

L'esempio di codice seguente mostra una risposta JSON a una query di sintesi vocale non riuscita:

```HTTP
HTTP/1.1 400 XML parser error
Content-Type: text/xml
Content-Length: 0
```

### <a name="ErrorResponse"></a>Risposte di errore

Tipi di errore | DESCRIZIONE
----|----
HTTP/400 (Richiesta non valida) | Un parametro obbligatorio è assente, vuoto o Null oppure il valore passato a un parametro obbligatorio o facoltativo non è valido. Uno dei motivi per cui si riceve una risposta di tipo "non valido" è il passaggio di un valore di stringa più lungo del limite massimo consentito. È inclusa una breve descrizione del parametro che causa il problema.
HTTP/401 (Non autorizzato) | La richiesta non è autorizzata.
HTTP/413 RequestEntityTooLarge  | Le dimensioni dell'input SSML superano il limite supportato.
HTTP/502 BadGateway | Si è verificato un problema a livello di rete o sul lato server.

Di seguito è riportato un esempio di risposta di errore:

```HTTP
HTTP/1.0 400 Bad Request
Content-Length: XXX
Content-Type: text/plain; charset=UTF-8

Voice name not supported
```

## <a name="ChangeSSML"></a>Modifica dell'output vocale tramite SSML

L'API Sintesi vocale Microsoft supporta SSML 1.0 secondo la definizione del W3C [Speech Synthesis Markup Language (SSML) Version 1.0](http://www.w3.org/TR/2009/REC-speech-synthesis-20090303/). Questa sezione contiene esempi di modifica di determinate caratteristiche dell'output vocale generato, come la velocità del parlato, la pronuncia e altro, mediante tag SSML.

1. Aggiungere una pausa

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, BenjaminRUS)'> Welcome to use Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.</voice> </speak>
  ```

2. Cambiare la velocità del parlato

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody rate="+30.00%">Welcome to use Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
  ```

3. Pronuncia

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'> <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme></voice> </speak>
  ```

4. Cambiare il volume

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody volume="+20.00%">Welcome to use Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
  ```

5. Cambiare il tono

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>Welcome to use <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
  ```

6. Cambiare il contorno prosodico

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody contour="(80%,+20%) (90%,+30%)" >Good morning.</prosody></voice> </speak>
  ```

> [!NOTE]
> I dati audio devono essere inclusi in un file WAV 8k o 16k nel formato seguente: **codice CRC** (CRC-32): 4 byte (DWORD) con intervallo valido 0x00000000 ~ 0xFFFFFFFF; **flag del formato audio**: 4 byte (DWORD) con intervallo valido 0x00000000 ~ 0xFFFFFFFF; **conteggio campione**: 4 byte (DWORD) con intervallo valido 0x00000000 ~ 0x7FFFFFFF; **dimensioni del corpo binario**: 4 byte (DWORD) con intervallo valido 0x00000000 ~ 0x7FFFFFFF; **corpo binario**: n byte.

## <a name="SampleApp"></a>Applicazione di esempio

Per i dettagli relativi all'implementazione, vedere l'[applicazione di esempio di sintesi vocale di Visual C#.NET](https://github.com/Microsoft/Cognitive-Speech-TTS/blob/master/Samples-Http/CSharp/TTSProgram.cs).

## <a name="SupLocales"></a>Impostazioni locali e caratteri voce supportati

La tabella seguente identifica alcune delle impostazioni locali supportate e i relativi caratteri voce.

Impostazioni locali | Sesso | Mapping nome del servizio
---------|--------|------------
ar-EG* | Femmina | "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)"
ar-SA | Maschio | "Microsoft Server Speech Text to Speech Voice (ar-SA, Naayf)"
bg-BG | Maschio | "Microsoft Server Speech Text to Speech Voice (bg-BG, Ivan)"
ca-ES | Femmina | "Microsoft Server Speech Text to Speech Voice (ca-ES, HerenaRUS)"
cs-CZ | Maschio | "Microsoft Server Speech Text to Speech Voice (cs-CZ, Jakub)"
da-DK | Femmina | "Microsoft Server Speech Text to Speech Voice (da-DK, HelleRUS)"
de-AT | Maschio | "Microsoft Server Speech Text to Speech Voice (de-AT, Michael)"
de-CH | Maschio | "Microsoft Server Speech Text to Speech Voice (de-CH, Karsten)"
de-DE | Femmina | "Microsoft Server Speech Text to Speech Voice (de-DE, Hedda) "
de-DE | Femmina | "Microsoft Server Speech Text to Speech Voice (de-DE, HeddaRUS)"
de-DE | Maschio | "Microsoft Server Speech Text to Speech Voice (de-DE, Stefan, Apollo) "
el-GR | Maschio | "Microsoft Server Speech Text to Speech Voice (el-GR, Stefanos)"
en-AU | Femmina | "Microsoft Server Speech Text to Speech Voice (en-AU, Catherine)"
en-AU | Femmina | "Microsoft Server Speech Text to Speech Voice (en-AU, HayleyRUS)"
en-CA | Femmina | "Microsoft Server Speech Text to Speech Voice (en-CA, Linda)"
en-CA | Femmina | "Microsoft Server Speech Text to Speech Voice (en-CA, HeatherRUS)"
en-GB | Femmina | "Microsoft Server Speech Text to Speech Voice (en-GB, Susan, Apollo)"
en-GB | Femmina | "Microsoft Server Speech Text to Speech Voice (en-GB, HazelRUS)"
en-GB | Maschio | "Microsoft Server Speech Text to Speech Voice (en-GB, George, Apollo)"
en-IE | Maschio | "Microsoft Server Speech Text to Speech Voice (en-IE, Sean)"
en-IN | Femmina | "Microsoft Server Speech Text to Speech Voice (en-IN, Heera, Apollo)"
en-IN | Femmina | "Microsoft Server Speech Text to Speech Voice (en-IN, PriyaRUS)"
en-IN | Maschio | "Microsoft Server Speech Text to Speech Voice (en-IN, Ravi, Apollo)"
it-IT | Femmina | "Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)"
it-IT | Femmina | "Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)"
it-IT | Maschio | "Microsoft Server Speech Text to Speech Voice (en-US, BenjaminRUS)"
es-ES | Femmina | "Microsoft Server Speech Text to Speech Voice (es-ES, Laura, Apollo)"
es-ES | Femmina | "Microsoft Server Speech Text to Speech Voice (es-ES, HelenaRUS)"
es-ES | Maschio | "Microsoft Server Speech Text to Speech Voice (es-ES, Pablo, Apollo)"
es-MX | Femmina | "Microsoft Server Speech Text to Speech Voice (es-MX, HildaRUS)"
es-MX | Maschio | "Microsoft Server Speech Text to Speech Voice (es-MX, Raul, Apollo)"
fi-FI | Femmina | "Microsoft Server Speech Text to Speech Voice (fi-FI, HeidiRUS)"
fr-CA | Femmina | "Microsoft Server Speech Text to Speech Voice (fr-CA, Caroline)"
fr-CA | Femmina | "Microsoft Server Speech Text to Speech Voice (fr-CA, HarmonieRUS)"
fr-CH | Maschio | "Microsoft Server Speech Text to Speech Voice (fr-CH, Guillaume)"
fr-FR | Femmina | "Microsoft Server Speech Text to Speech Voice (fr-FR, Julie, Apollo)"
fr-FR | Femmina | "Microsoft Server Speech Text to Speech Voice (fr-FR, HortenseRUS)"
fr-FR | Maschio | "Microsoft Server Speech Text to Speech Voice (fr-FR, Paul, Apollo)"
he-IL| Maschio| "Microsoft Server Speech Text to Speech Voice (he-IL, Asaf)"
hi-IN | Femmina | "Microsoft Server Speech Text to Speech Voice (hi-IN, Kalpana, Apollo)"
hi-IN | Femmina | "Microsoft Server Speech Text to Speech Voice (hi-IN, Kalpana)"
hi-IN | Maschio | "Microsoft Server Speech Text to Speech Voice (hi-IN, Hemant)"
hr-HR | Maschio | "Microsoft Server Speech Text to Speech Voice (hr-HR, Matej)"
hu-HU | Maschio | "Microsoft Server Speech Text to Speech Voice (hu-HU, Szabolcs)"
id-ID | Maschio | "Microsoft Server Speech Text to Speech Voice (id-ID, Andika)"
it-IT | Maschio | "Microsoft Server Speech Text to Speech Voice (it-IT, Cosimo, Apollo)"
it-IT | Femmina | "Microsoft Server Speech Text to Speech Voice (it-IT, LuciaRUS)"
ja-JP | Femmina | "Microsoft Server Speech Text to Speech Voice (ja-JP, Ayumi, Apollo)"
ja-JP | Maschio | "Microsoft Server Speech Text to Speech Voice (ja-JP, Ichiro, Apollo)"
ja-JP | Femmina | "Microsoft Server Speech Text to Speech Voice (ja-JP, HarukaRUS)"
ko-KR | Femmina | "Microsoft Server Speech Text to Speech Voice (ko-KR, HeamiRUS)"
ms-MY | Maschio | "Microsoft Server Speech Text to Speech Voice (ms-MY, Rizwan)"
nb-NO | Femmina | "Microsoft Server Speech Text to Speech Voice (nb-NO, HuldaRUS)"
nl-NL | Femmina | "Microsoft Server Speech Text to Speech Voice (nl-NL, HannaRUS)"
pl-PL | Femmina | "Microsoft Server Speech Text to Speech Voice (pl-PL, PaulinaRUS)"
pt-BR | Femmina | "Microsoft Server Speech Text to Speech Voice (pt-BR, HeloisaRUS)"
pt-BR | Maschio | "Microsoft Server Speech Text to Speech Voice (pt-BR, Daniel, Apollo)"
pt-PT | Femmina | "Microsoft Server Speech Text to Speech Voice (pt-PT, HeliaRUS)"
ro-RO | Maschio | "Microsoft Server Speech Text to Speech Voice (ro-RO, Andrei)"
ru-RU | Femmina | "Microsoft Server Speech Text to Speech Voice (ru-RU, Irina, Apollo)"
ru-RU | Maschio | "Microsoft Server Speech Text to Speech Voice (ru-RU, Pavel, Apollo)"
ru-RU | Femmina | "Microsoft Server Speech Text to Speech Voice (ru-RU, EkaterinaRUS)"
sk-SK | Maschio | "Microsoft Server Speech Text to Speech Voice (sk-SK, Filip)"
sl-SI | Maschio | "Microsoft Server Speech Text to Speech Voice (sl-SI, Lado)"
sv-SE | Femmina | "Microsoft Server Speech Text to Speech Voice (sv-SE, HedvigRUS)"
ta-IN | Maschio | "Microsoft Server Speech Text to Speech Voice (ta-IN, Valluvar)"
th-TH | Maschio | "Microsoft Server Speech Text to Speech Voice (th-TH, Pattara)"
tr-TR | Femmina | "Microsoft Server Speech Text to Speech Voice (tr-TR, SedaRUS)"
vi-VN | Maschio | "Microsoft Server Speech Text to Speech Voice (vi-VN, An)"
zh-CN | Femmina | "Microsoft Server Speech Text to Speech Voice (zh-CN, HuihuiRUS)"
zh-CN | Femmina | "Microsoft Server Speech Text to Speech Voice (zh-CN, Yaoyao, Apollo)"
zh-CN | Maschio | "Microsoft Server Speech Text to Speech Voice (zh-CN, Kangkang, Apollo)"
zh-HK | Femmina | "Microsoft Server Speech Text to Speech Voice (zh-HK, Tracy, Apollo)"
zh-HK | Femmina | "Microsoft Server Speech Text to Speech Voice (zh-HK, TracyRUS)"
zh-HK | Maschio | "Microsoft Server Speech Text to Speech Voice (zh-HK, Danny, Apollo)"
zh-TW | Femmina | "Microsoft Server Speech Text to Speech Voice (zh-TW, Yating, Apollo)"
zh-TW | Femmina | "Microsoft Server Speech Text to Speech Voice (zh-TW, HanHanRUS)"
zh-TW | Maschio | "Microsoft Server Speech Text to Speech Voice (zh-TW, Zhiwei, Apollo)"
 *ar-EG supporta lo standard moderno della lingua araba.

> [!NOTE]
> Si noti che i nomi di servizio **Microsoft Server Speech Text to Speech Voice (cs-CZ, Vit)** e **Microsoft Server Speech Text to Speech Voice (en-IE, Shaun)** sono deprecati dal 31/3/2018, per ottimizzare le funzionalità dell'API Sintesi vocale Bing. Aggiornare il codice con i nuovi nomi.

## <a name="TrouNSupport"></a>Risoluzione dei problemi e supporto

Inserire tutte le domande e i problemi nel forum MSDN sul [servizio di Riconoscimento vocale Bing](https://social.msdn.microsoft.com/Forums/en-US/home?forum=SpeechService). Includere i dettagli completi, come:

* Un esempio della stringa di richiesta completa.
* Se disponibile, l'output completo di una richiesta non riuscita, che include gli ID log.
* La percentuale delle richieste non riuscite.
