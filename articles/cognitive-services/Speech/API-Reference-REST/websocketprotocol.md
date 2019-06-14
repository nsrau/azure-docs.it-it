---
title: Protocollo WebSocket di Riconoscimento vocale Bing | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Documentazione del protocollo per Riconoscimento vocale Bing basato su tecnologia WebSocket
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d6601f57d87b518b2061df64174818432b822755
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60515331"
---
# <a name="bing-speech-websocket-protocol"></a>Protocollo WebSocket di Riconoscimento vocale Bing

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Riconoscimento vocale Bing è una piattaforma basata sul cloud che offre gli algoritmi più avanzati disponibili per la conversione di audio parlato in testo. Il protocollo di Riconoscimento vocale Bing definisce la [configurazione della connessione](#connection-establishment) tra le applicazioni client e il servizio e i messaggi di riconoscimento vocale scambiati tra le controparti ([Messaggi generati dal client](#client-originated-messages) e [Messaggi generati dal servizio](#service-originated-messages)). In questo articolo sono inoltre incluse sezioni sui [messaggi di telemetria](#telemetry-schema) e sulla [gestione degli errori](#error-handling).

## <a name="connection-establishment"></a>Attivazione della connessione

Il protocollo del Servizio di riconoscimento vocale è conforme alla specifica standard per WebSocket [IETF RFC 6455](https://tools.ietf.org/html/rfc6455). Una connessione WebSocket viene avviata come richiesta HTTP contenente intestazioni HTTP che indicano l'intenzione del client di aggiornare la connessione a un WebSocket anziché usare la semantica HTTP. Il server indica la volontà di partecipare alla connessione WebSocket restituendo una risposta HTTP `101 Switching Protocols`. Dopo lo scambio di questo handshake, il client e il servizio mantengono aperto il socket e iniziano a usare un protocollo basato su messaggi per inviare e ricevere informazioni.

Per iniziare l'handshake WebSocket, l'applicazione client invia una richiesta HTTPS GET al servizio. Nella richiesta sono incluse intestazioni standard di aggiornamento a WebSocket insieme ad altre intestazioni specifiche per il riconoscimento vocale.

```HTTP
GET /speech/recognition/interactive/cognitiveservices/v1 HTTP/1.1
Host: speech.platform.bing.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
Sec-WebSocket-Version: 13
Authorization: t=EwCIAgALBAAUWkziSCJKS1VkhugDegv7L0eAAJqBYKKTzpPZOeGk7RfZmdBhYY28jl&p=
X-ConnectionId: A140CAF92F71469FA41C72C7B5849253
Origin: https://speech.platform.bing.com
```

Il servizio risponde nel modo seguente:

```HTTP
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: upgrade
Sec-WebSocket-Key: 2PTTXbeeBXlrrUNsY15n01d/Pcc=
Set-Cookie: SpeechServiceToken=AAAAABAAWTC8ncb8COL; expires=Wed, 17 Aug 2016 15:39:06 GMT; domain=bing.com; path="/"
Date: Wed, 17 Aug 2016 15:03:52 GMT
```

Tutte le richieste di riconoscimento vocale richiedono la crittografia [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security). Non è supportato l'uso di richieste di riconoscimento vocale non crittografate. È supportata la versione TLS seguente:

* TLS 1.2

### <a name="connection-identifier"></a>Identificatore della connessione

Il Servizio di riconoscimento vocale richiede che tutti i client includano un ID univoco per identificare la connessione. Quando avviano un handshake WebSocket, i client *devono* includere l'intestazione *X-ConnectionId*. L'intestazione *X-ConnectionId* deve essere costituita da un valore [UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) (Universally Unique Identifier, identificatore univoco globale). Le richieste di aggiornamento a WebSocket che non contengono l'intestazione *X-ConnectionId*, non specificano un valore per *X-ConnectionId* o non includono un valore UUID valido vengono rifiutate dal servizio con una risposta HTTP `400 Bad Request`.

### <a name="authorization"></a>Authorization

Oltre alle intestazioni standard per l'handshake WebSocket, le richieste di riconoscimento vocale richiedono un'intestazione *Authorization*. Le richieste di connessione prive di questa intestazione vengono rifiutate dal servizio con una risposta HTTP `403 Forbidden`.

L'intestazione *Authorization* deve contenere un token di accesso JSON Web (JWT).

Per informazioni su come eseguire la sottoscrizione e ottenere le chiavi API che vengono usate per recuperare i token di accesso JWT validi, vedere la pagina per la [sottoscrizione a Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/).

La chiave API viene passata al servizio token. Ad esempio:

``` HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

Per l'accesso tramite token, l'intestazione deve includere le informazioni seguenti.

| NOME | Format | Descrizione |
|----|----|----|
| Ocp-Apim-Subscription-Key | ASCII | Your subscription key (Chiave della sottoscrizione) |

Il servizio token restituisce il token di accesso JWT come `text/plain`. Il token JWT viene quindi passato come `Base64 access_token` all'handshake sotto forma di intestazione *Authorization* preceduta dalla stringa `Bearer`. Ad esempio:

`Authorization: Bearer [Base64 access_token]`

### <a name="cookies"></a>Cookie

I client *devono* supportare i cookie HTTP come specificato in [RFC 6265](https://tools.ietf.org/html/rfc6265).

### <a name="http-redirection"></a>Reindirizzamento HTTP

I client *devono* supportare i meccanismi di reindirizzamento standard definiti dalla [specifica del protocollo HTTP](https://www.w3.org/Protocols/rfc2616/rfc2616.html).

### <a name="speech-endpoints"></a>Endpoint del riconoscimento vocale

I client *devono* usare un endpoint appropriato del Servizio di riconoscimento vocale. L'endpoint è basato sulla lingua e sulla modalità di riconoscimento. La tabella seguente illustra alcuni esempi.

| Modalità | `Path` | URI del servizio |
| -----|-----|-----|
| Interattività | /speech/recognition/interactive/cognitiveservices/v1 | https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=pt-BR |
| Conversazione | /speech/recognition/conversation/cognitiveservices/v1 | https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US |
| Dettatura | /speech/recognition/dictation/cognitiveservices/v1 | https://speech.platform.bing.com/speech/recognition/dictation/cognitiveservices/v1?language=fr-FR |

Per altre informazioni, vedere [URI del servizio](../GetStarted/GetStartedREST.md#service-uri).

### <a name="report-connection-problems"></a>Segnalare i problemi di connessione

I client devono segnalare immediatamente tutti i problemi rilevati durante l'attivazione di una connessione. Il protocollo dei messaggi per la segnalazione delle connessioni non riuscite è descritto in [Telemetria degli errori di connessione](#connection-failure-telemetry).

### <a name="connection-duration-limitations"></a>Limitazioni della durata delle connessioni

Rispetto alle tipiche connessioni HTTP dei servizi Web, le connessioni WebSocket hanno una *lunga* durata. Il Servizio di riconoscimento vocale pone limitazioni alla durata delle connessioni WebSocket al servizio:

 * La durata massima per ogni connessione WebSocket attiva è di 10 minuti. Una connessione è attiva se il servizio o il client invia messaggi WebSocket attraverso tale connessione. Il servizio termina la connessione senza avvisare che è stato raggiunto il limite. I client devono sviluppare scenari utente in cui non è necessario che la connessione rimanga attiva in corrispondenza o in prossimità della durata massima della connessione.

 * La durata massima per ogni connessione WebSocket inattiva è di 180 secondi. Una connessione è inattiva se né il servizio né il client ha inviato un messaggio WebSocket attraverso tale connessione. Una volta raggiunta la durata massima di inattività, la connessione WebSocket inattiva viene terminata.

## <a name="message-types"></a>Tipi di messaggio

Una volta stabilita una connessione WebSocket tra il client e il servizio, entrambi possono inviare messaggi. Questa sezione descrive il formato dei messaggi WebSocket.

Il documento [IETF RFC 6455](https://tools.ietf.org/html/rfc6455) specifica che i messaggi WebSocket possono trasmettere dati con codifica di testo o binaria. Le due codifiche usano formati di trasmissione diversi. Ogni formato è ottimizzato in modo da garantire efficienza nella codifica, la trasmissione e la decodifica del payload dei messaggi.

### <a name="text-websocket-messages"></a>Messaggi WebSocket di testo

I messaggi WebSocket di testo trasportano un payload di informazioni testuali costituito da una sezione di intestazioni e un corpo separati dalla classica coppia di simboli di nuova riga e doppio ritorno a capo usata per i messaggi HTTP. Inoltre, come i messaggi HTTP, i messaggi WebSocket di testo specificano le intestazioni nel formato *nome: valore*, separate da una coppia di simboli di nuova riga e singolo ritorno a capo. Il testo incluso in un messaggio WebSocket di testo *deve* usare la codifica [UTF-8](https://tools.ietf.org/html/rfc3629).

I messaggi WebSocket di testo devono specificare un percorso nell'intestazione *Path*. Il valore di questa intestazione deve corrispondere a uno dei tipi di messaggio del protocollo di riconoscimento vocale definiti più avanti in questo documento.

### <a name="binary-websocket-messages"></a>Messaggi WebSocket binari

I messaggi WebSocket binari trasportano un payload di dati binari. Nel protocollo del Servizio di riconoscimento vocale, l'audio viene trasmesso e ricevuto dal servizio tramite messaggi WebSocket binari. Tutti gli altri messaggi sono di tipo WebSocket di testo.

Come quelli di testo, i messaggi WebSocket binari sono costituiti da un'intestazione e da un corpo. I primi 2 byte del messaggio WebSocket binario specificano, in ordine [big-endian](https://en.wikipedia.org/wiki/Endianness), la dimensione in integer a 16 bit della sezione di intestazione. La dimensione minima della sezione di intestazione è di 0 byte. La dimensione massima è pari a 8192 byte. Per il testo nelle intestazioni dei messaggi WebSocket binari *deve* essere usata la codifica [US-ASCII](https://tools.ietf.org/html/rfc20).

Le intestazioni in un messaggio WebSocket binario vengono codificate nello stesso formato usato per i messaggi WebSocket di testo. Il formato *nome:valore* è separato da una coppia di simboli di nuova riga e singolo ritorno a capo. I messaggi WebSocket binari devono specificare un percorso nell'intestazione *Path*. Il valore di questa intestazione deve corrispondere a uno dei tipi di messaggio del protocollo di riconoscimento vocale definiti più avanti in questo documento.

Nel protocollo del Servizio di riconoscimento vocale vengono usati entrambi i tipi di messaggi WebSocket di testo e binari.

## <a name="client-originated-messages"></a>Messaggi generati dal client

Una volta stabilita la connessione, il client e il servizio iniziano a inviare messaggi. Questa sezione descrive il formato e il payload dei messaggi che vengono inviati dalle applicazioni client al Servizio di riconoscimento vocale. La sezione [Messaggi generati dal servizio](#service-originated-messages) illustra i messaggi generati dal Servizio di riconoscimento vocale e inviati alle applicazioni client.

I messaggi principali inviati dal client ai servizi sono di tipo `speech.config`, `audio` e `telemetry`. Prima della descrizione dettagliata di ogni tipo di messaggio, sono riportate le intestazioni obbligatorie comuni per tutti i messaggi.

### <a name="required-message-headers"></a>Intestazioni di messaggio obbligatorie

Le intestazioni seguenti sono obbligatorie per tutti i messaggi generati dal client.

| Intestazione | Value |
|----|----|
| `Path` | Percorso del messaggio come specificato in questo documento |
| X-RequestId | UUID nel formato senza trattini |
| X-Timestamp | Timestamp UTC dell'orologio del client in formato ISO 8601 |

#### <a name="x-requestid-header"></a>Intestazione X-RequestId

Le richieste generate dal client sono identificate in modo univoco dall'intestazione *X-RequestId* del messaggio. Questa intestazione è obbligatoria per tutti i messaggi generati dal client. Il valore dell'intestazione *X-RequestId* deve essere un UUID nel formato senza trattini, ad esempio *123e4567e89b12d3a456426655440000*. *Non può* essere riportato nel formato canonico *123e4567-e89b-12d3-a456-426655440000*. Se vengono inviate richieste senza un'intestazione *X-RequestId* o con un valore di intestazione in un formato per UUID non corretto, il servizio terminerà la connessione WebSocket.

#### <a name="x-timestamp-header"></a>Intestazione X-Timestamp

Ogni messaggio inviato al Servizio di riconoscimento vocale da un'applicazione client *deve* includere un'intestazione *X-Timestamp*. Il valore per questa intestazione corrisponde al momento in cui il client invia il messaggio. Se vengono inviate richieste senza un'intestazione *X-Timestamp* o con un valore di intestazione in un formato non corretto, il servizio terminerà la connessione WebSocket.

Il valore dell'intestazione *X-Timestamp* deve essere espresso nel formato 'aaaa'-'MM'-'gg'T'HH':'mm':'ss'.'fffffffZ', dove 'fffffff' rappresenta una frazione di un secondo. Ad esempio, '12.5' significa '12 + 5/10 secondi' e '12.526' significa '12 più 526/1000 secondi'. Questo formato è conforme allo standard [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e, a differenza dell'intestazione HTTP *Date*, può fornire una risoluzione in millisecondi. È possibile che le applicazioni client arrotondino i timestamp al millisecondo più vicino. Le applicazioni client devono verificare che l'orologio del dispositivo tenga traccia del tempo con precisione usando un [server NTP (Network Time Protocol)](https://en.wikipedia.org/wiki/Network_Time_Protocol).

### <a name="message-speechconfig"></a>Messaggio `speech.config`

Il Servizio di riconoscimento vocale deve conoscere le caratteristiche dell'applicazione per offrire la massima qualità possibile. Tra i dati obbligatori delle caratteristiche sono incluse le informazioni sul dispositivo e sul sistema operativo alla base dell'applicazione. Queste informazioni vengono specificate nel messaggio `speech.config`.

I client *devono* inviare un messaggio `speech.config` immediatamente dopo aver stabilito la connessione al Servizio di riconoscimento vocale e prima di inviare messaggi `audio`. È necessario inviare un messaggio `speech.config` una sola volta per ogni connessione.

| Campo | Descrizione |
|----|----|
| Codifica del messaggio WebSocket | Text |
| Body | Payload come struttura JSON |

#### <a name="required-message-headers"></a>Intestazioni di messaggio obbligatorie

| Nome intestazione | Value |
|----|----|
| `Path` | `speech.config` |
| X-Timestamp | Timestamp UTC dell'orologio del client in formato ISO 8601 |
| Content-Type | application/json; charset=utf-8 |

Come per tutti i messaggi generati dal client nel protocollo del Servizio di riconoscimento vocale, il messaggio `speech.config` *deve* includere un'intestazione *X-Timestamp* che registra l'ora UTC dell'orologio del client in cui il messaggio è stato inviato al servizio. Il messaggio `speech.config` *non* richiede un'intestazione *X-RequestId* perché non è associato a una particolare richiesta di riconoscimento vocale.

#### <a name="message-payload"></a>Payload del messaggio
Il payload del messaggio `speech.config` è costituito da una struttura JSON che contiene informazioni sull'applicazione. L'esempio seguente illustra queste informazioni. Le informazioni sul contesto del client e del dispositivo sono incluse nell'elemento *context* della struttura JSON.

```JSON
{
  "context": {
    "system": {
      "version": "2.0.12341",
    },
    "os": {
      "platform": "Linux",
      "name": "Debian",
      "version": "2.14324324"
    },
    "device": {
      "manufacturer": "Contoso",
      "model": "Fabrikan",
      "version": "7.341"
      }
    },
  }
}
```

##### <a name="system-element"></a>Elemento system

L'elemento system.version del messaggio `speech.config` contiene la versione del software SDK di riconoscimento vocale usata dall'applicazione client o dal dispositivo. Il valore è nel formato *major.minor.build.branch*. È possibile omettere il componente *branch* se non è applicabile.

##### <a name="os-element"></a>Elemento os

| Campo | Descrizione | Uso |
|-|-|-|
| os.platform | Piattaforma del sistema operativo che ospita l'applicazione, ad esempio Windows, Android, iOS o Linux |Obbligatorio |
| os.name | Nome del sistema operativo, ad esempio Debian o Windows 10 | Obbligatorio |
| os.version | Versione del sistema operativo nel formato *major.minor.build.branch* | Obbligatorio |

##### <a name="device-element"></a>Elemento device

| Campo | Descrizione | Uso |
|-|-|-|
| device.manufacturer | Produttore dell'hardware del dispositivo | Obbligatorio |
| device.model | Modello del dispositivo | Obbligatorio |
| device.version | Versione del software del dispositivo definita dal produttore. Questo valore specifica una versione del dispositivo di cui può tenere traccia il produttore. | Obbligatorio |

### <a name="message-audio"></a>Messaggio `audio`

Le applicazioni client abilitate per il riconoscimento vocale inviano i dati audio al Servizio di riconoscimento vocale convertendo il flusso in una serie di blocchi audio. Ogni blocco trasporta un segmento del parlato che deve essere trascritto dal servizio. La dimensione massima di un singolo blocco audio è pari a 8192 byte. I messaggi del flusso audio sono *messaggi WebSocket binari*.

I client usano il messaggio `audio` per inviare un blocco audio al servizio. I client leggono l'audio dal microfono a blocchi e inviano i blocchi al Servizio di riconoscimento vocale per la trascrizione. Il primo messaggio `audio` deve contenere un'intestazione ben formata che specifichi correttamente la conformità dell'audio a uno dei formati di codifica supportati dal servizio. Gli altri messaggi `audio` contengono solo i dati del flusso audio binario letti dal microfono.

I client possono inviare facoltativamente un messaggio `audio` con un corpo di lunghezza zero. Questo messaggio indica al servizio che il client è a conoscenza del fatto che l'utente ha smesso di parlare, il discorso è terminato e il microfono è spento.

Il Servizio di riconoscimento vocale usa il primo messaggio `audio` contenente un identificatore di richiesta univoco per segnalare l'inizio di un nuovo ciclo, o *turno*, di richiesta/risposta. Non appena riceve un messaggio `audio` con un nuovo identificatore di richiesta, il servizio rimuove gli eventuali messaggi in coda o non inviati associati a turni precedenti.

| Campo | Descrizione |
|-------------|----------------|
| Codifica del messaggio WebSocket | Binary |
| Body | Dati binari del blocco audio. La dimensione massima è pari a 8192 byte. |

#### <a name="required-message-headers"></a>Intestazioni di messaggio obbligatorie

Le intestazioni seguenti sono obbligatorie per tutti i messaggi `audio`.

| Intestazione         |  Value     |
| ------------- | ---------------- |
| `Path` | `audio` |
| X-RequestId | UUID nel formato senza trattini |
| X-Timestamp | Timestamp UTC dell'orologio del client in formato ISO 8601 |
| Content-Type | Tipo di contenuto audio. Deve essere *audio/x-wav* (PCM) o *audio/silk* (SILK). |

#### <a name="supported-audio-encodings"></a>Codifiche audio supportate

Questa sezione descrive i codec audio supportati dal Servizio di riconoscimento vocale.

##### <a name="pcm"></a>PCM

Il Servizio di riconoscimento vocale accetta audio PCM (Pulse Code Modulation) non compresso. L'audio viene inviato al servizio in formato [WAV](https://en.wikipedia.org/wiki/WAV) e pertanto il primo blocco audio *deve* contenere un'intestazione RIFF ([Resource Interchange File Format](https://en.wikipedia.org/wiki/Resource_Interchange_File_Format)) valida. Se un client avvia un turno con un blocco audio che *non* include un'intestazione RIFF valida, il servizio rifiuta la richiesta e termina la connessione WebSocket.

L'audio PCM *deve* essere campionato a 16 kHz con 16 bit per campione e un solo canale (*riff-16khz-16bit-mono-pcm*). Il Servizio di riconoscimento vocale non supporta i flussi audio stereo e rifiuta i flussi audio che non usano la velocità in bit, la frequenza di campionamento o il numero di canali specificato.

##### <a name="opus"></a>Opus

Opus è un codec audio aperto, gratuito e altamente versatile. Il Servizio di riconoscimento vocale include il supporto per Opus a una velocità in bit costante pari a `32000` o `16000`. È attualmente supportato solo il contenitore `OGG` per Opus specificato dal tipo MIME `audio/ogg`.

Per usare Opus, modificare l'[esempio JavaScript](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/master/samples/browser/Sample.html#L101) e il metodo `RecognizerSetup` per la restituzione.

```javascript
return SDK.CreateRecognizerWithCustomAudioSource(
            recognizerConfig,
            authentication,
            new SDK.MicAudioSource(
                     new SDK.OpusRecorder(
                     {
                         mimeType: "audio/ogg",
                         bitsPerSecond: 32000
                     }
              )
          ));
```

#### <a name="detect-end-of-speech"></a>Rilevamento della fine del parlato

Le persone non segnalano in modo esplicito che hanno finito di parlare. Per gestire la fine del parlato in un flusso audio, le applicazioni che accettano il parlato come input hanno due possibilità poiché la fine del parlato può essere rilevata dal servizio o dal client. Tra queste due possibilità, il rilevamento della fine del parlato da parte del servizio offre in genere una migliore esperienza utente.

##### <a name="service-end-of-speech-detection"></a>Rilevamento della fine del parlato da parte del servizio

Per offrire un'esperienza ottimale di riconoscimento vocale, le applicazioni consentono al servizio di rilevare il momento in cui l'utente smette di parlare. I client inviano l'audio proveniente dal microfono come blocchi *audio* finché il servizio non rileva un silenzio e risponde con un messaggio `speech.endDetected`.

##### <a name="client-end-of-speech-detection"></a>Rilevamento della fine del parlato da parte del client

Le applicazioni client che consentono all'utente di segnalare in qualche modo la fine del parlato possono anche inviare tale segnale al servizio. Un'applicazione client può ad esempio includere un pulsante che l'utente può premere per arrestare o disattivare l'audio. Per segnalare la fine del parlato, le applicazioni client inviano un messaggio di blocco *audio* con un corpo di lunghezza zero. Il Servizio di riconoscimento vocale interpreta questo messaggio come fine del flusso audio in ingresso.

### <a name="message-telemetry"></a>Messaggio `telemetry`

Le applicazioni client *devono* confermare la fine di ogni turno inviando i dati di telemetria relativi al turno al Servizio di riconoscimento vocale. La conferma della fine del turno consente al Servizio di riconoscimento vocale di assicurarsi che tutti i messaggi necessari per il completamento della richiesta e della risposta siano stati ricevuti correttamente dal client, oltre a verificare che le applicazioni client funzionino come previsto. Queste informazioni si rivelano particolarmente utili se è necessario ricevere supporto per risolvere i problemi dell'applicazione abilitata per il riconoscimento vocale.

I client devono confermare la fine di un turno inviando un messaggio `telemetry` subito dopo aver ricevuto un messaggio `turn.end`. La conferma di `turn.end` deve essere eseguita il prima possibile. Se un'applicazione client non riesce a eseguire questa operazione, il Servizio di riconoscimento vocale potrebbe terminare la connessione con un errore. I client devono inviare un solo messaggio `telemetry` per ogni richiesta e risposta identificate dal valore *X-RequestId*.

| Campo | Descrizione |
| ------------- | ---------------- |
| Codifica del messaggio WebSocket | Text |
| `Path` | `telemetry` |
| X-Timestamp | Timestamp UTC dell'orologio del client in formato ISO 8601 |
| Content-Type | `application/json` |
| Body | Struttura JSON contenente le informazioni del client relative al turno |

Lo schema per il corpo del messaggio `telemetry` è definito nella sezione [Schema dei dati di telemetria](#telemetry-schema).

#### <a name="telemetry-for-interrupted-connections"></a>Dati di telemetria per le connessioni interrotte

Se la connessione di rete si interrompe per qualsiasi motivo durante un turno e il client *non* riceve un messaggio `turn.end` dal servizio, il client invia un messaggio `telemetry`. Questo messaggio contiene la descrizione della richiesta non riuscita al successivo tentativo di connessione al servizio da parte del client. I client non devono provare immediatamente a stabilire una connessione per inviare il messaggio `telemetry` poiché quest'ultimo potrebbe essere memorizzato nel buffer sul client e inviato attraverso una successiva connessione richiesta dall'utente. Il messaggio `telemetry` relativo alla richiesta non riuscita *deve* usare il valore *X-RequestId* di tale richiesta. Può essere inviato al servizio non appena viene stabilita una connessione, senza attendere l'invio o la ricezione di altri messaggi.

## <a name="service-originated-messages"></a>Messaggi generati dal servizio

Questa sezione descrive i messaggi generati dal Servizio di riconoscimento vocale e inviati al client. Il Servizio di riconoscimento vocale gestisce un registro delle funzionalità dei client e genera i messaggi richiesti da ogni client. Pertanto, non tutti i client ricevono tutti i messaggi descritti in questa sezione. Per ragioni di brevità, i messaggi sono qui identificati dal valore dell'intestazione *Path*. Ad esempio, un messaggio WebSocket di testo in cui l'intestazione *Path* è impostata su `speech.hypothesis` è indicato come messaggio speech.hypothesis.

### <a name="message-speechstartdetected"></a>Messaggio `speech.startDetected`

Il messaggio `speech.startDetected` indica che il Servizio di riconoscimento vocale ha rilevato la presenza di parlato nel flusso audio.

| Campo | Descrizione |
| ------------- | ---------------- |
| Codifica del messaggio WebSocket | Text |
| `Path` | `speech.startDetected` |
| Content-Type | application/json; charset=utf-8 |
| Body | Struttura JSON contenente le informazioni sulle condizioni relative al momento in cui è stato rilevato l'inizio del parlato. Il campo *Offset* in questa struttura specifica lo scarto temporale (in unità di 100 nanosecondi) tra il momento in cui è stato rilevato il parlato nel flusso audio e l'inizio effettivo del flusso. |

#### <a name="sample-message"></a>Messaggio di esempio

```HTML
Path: speech.startDetected
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Offset": 100000
}
```

### <a name="message-speechhypothesis"></a>Messaggio `speech.hypothesis`

Durante il riconoscimento vocale, il servizio genera periodicamente ipotesi relative alle parole riconosciute e invia queste ipotesi al client ogni circa 300 millisecondi. Il messaggio `speech.hypothesis` è adatto *solo* per migliorare l'esperienza di riconoscimento vocale dell'utente. Non fare affidamento sul contenuto o sulla precisione del testo in questi messaggi.

 Il messaggio `speech.hypothesis` è applicabile ai client che includono alcune funzionalità di rendering del testo e vogliono fornire quasi in tempo reale alcuni suggerimenti al parlante in merito al riconoscimento vocale in corso.

| Campo | Descrizione |
| ------------- | ---------------- |
| Codifica del messaggio WebSocket | Text |
| `Path` | `speech.hypothesis` |
| X-RequestId | UUID nel formato senza trattini |
| Content-Type | application/json |
| Body | Struttura JSON contenente ipotesi di riconoscimento vocale |

#### <a name="sample-message"></a>Messaggio di esempio

```HTML
Path: speech.hypothesis
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Text": "this is a speech hypothesis",
  "Offset": 0,
  "Duration": 23600000
}
```

L'elemento *Offset* specifica lo scarto temporale (in unità di 100 nanosecondi) tra il momento in cui è stata riconosciuta l'espressione del parlato e l'inizio effettivo del flusso audio.

L'elemento *Duration* specifica la durata (in unità di 100 nanosecondi) dell'espressione del parlato.

I client non devono formulare supposizioni riguardo alla frequenza, all'intervallo di tempo o al testo contenuto in un'ipotesi di riconoscimento vocale o alla coerenza del testo in due di queste ipotesi. Le ipotesi sono semplicemente snapshot del processo di trascrizione nel servizio. Non rappresentano una raccolta stabile dei dati della trascrizione. Ad esempio, una prima ipotesi di riconoscimento vocale potrebbe contenere le parole "dimezza la quota" e la seconda ipotesi le parole "di mezza quota". Il Servizio di riconoscimento vocale non esegue operazioni di post-elaborazione sul testo, ad esempio correzione di maiuscole/minuscole o della punteggiatura, nelle ipotesi di riconoscimento vocale.

### <a name="message-speechphrase"></a>Messaggio `speech.phrase`

Quando stabilisce di avere informazioni sufficienti per generare un risultato stabile, il Servizio di riconoscimento vocale genera un messaggio `speech.phrase`. I risultati di questo tipo vengono generati quando il servizio rileva che l'utente ha terminato di pronunciare una frase o un'espressione.

| Campo | Descrizione |
| ------------- | ---------------- |
| Codifica del messaggio WebSocket | Text |
| `Path` | `speech.phrase` |
| Content-Type | application/json |
| Body | Struttura JSON relativa all'espressione del parlato |

Lo schema della struttura JSON relativa all'espressione del parlato include i campi seguenti: `RecognitionStatus`, `DisplayText`, `Offset` e `Duration`. Per altre informazioni su questi campi, vedere [Transcription responses](../concepts.md#transcription-responses) (Risposte della trascrizione).

#### <a name="sample-message"></a>Messaggio di esempio

```HTML
Path: speech.phrase
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": 0,
  "Duration": 12300000
}
```

### <a name="message-speechenddetected"></a>Messaggio `speech.endDetected`

Il messaggio `speech.endDetected` specifica che l'applicazione client deve arrestare il flusso audio verso il servizio.

| Campo | Descrizione |
| ------------- | ---------------- |
| Codifica del messaggio WebSocket | Text |
| `Path` | `speech.endDetected` |
| Body | Struttura JSON contenente il valore dello scarto temporale relativo al momento in cui è stata rilevata la fine del parlato. Questo valore è espresso in unità di 100 nanosecondi calcolate rispetto all'inizio del flusso audio usato per il riconoscimento. |
| Content-Type | application/json; charset=utf-8 |

#### <a name="sample-message"></a>Messaggio di esempio

```HTML
Path: speech.endDetected
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Offset": 0
}
```

L'elemento *Offset* specifica lo scarto temporale (in unità di 100 nanosecondi) tra il momento in cui è stata riconosciuta l'espressione del parlato e l'inizio effettivo del flusso audio.

### <a name="message-turnstart"></a>Messaggio `turn.start`

Il messaggio `turn.start` segnala l'inizio di un turno dal punto di vista del servizio. `turn.start` è sempre il primo messaggio di risposta che si riceve per qualsiasi richiesta. Se non si riceve un messaggio `turn.start`, è presumibile che lo stato della connessione del servizio non sia valido.

| Campo | Descrizione |
| ------------- | ---------------- |
| Codifica del messaggio WebSocket | Text |
| `Path` | `turn.start` |
| Content-Type | application/json; charset=utf-8 |
| Body | Struttura JSON |

#### <a name="sample-message"></a>Messaggio di esempio

```HTML
Path: turn.start
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "context": {
    "serviceTag": "7B33613B91714B32817815DC89633AFA"
  }
}
```

Il corpo del messaggio `turn.start` è costituito da una struttura JSON contenente il contesto per l'inizio del turno. L'elemento *context* contiene una proprietà *serviceTag* in cui è specificato un valore di tag che il servizio ha associato al turno. Questo valore può essere usato da Microsoft per fornire assistenza nella risoluzione degli errori dell'applicazione.

### <a name="message-turnend"></a>Messaggio `turn.end`

`turn.end` segnala la fine di un turno dal punto di vista del servizio. Il messaggio `turn.end` è sempre l'ultimo messaggio di risposta che si riceve per qualsiasi richiesta. I client possono usare la ricezione di questo messaggio come segnale per avviare le attività di pulizia e la transizione a uno stato di inattività. Se non si riceve un messaggio `turn.end`, è presumibile che lo stato della connessione del servizio non sia valido. In casi come questo, chiudere la connessione esistente al servizio e riconnettersi.

| Campo | Descrizione |
| ------------- | ---------------- |
| Codifica del messaggio WebSocket | Text |
| `Path` | `turn.end` |
| Body | Nessuna |

#### <a name="sample-message"></a>Messaggio di esempio

```HTML
Path: turn.end
X-RequestId: 123e4567e89b12d3a456426655440000
```

## <a name="telemetry-schema"></a>Schema dei dati di telemetria

Il corpo del messaggio di *telemetria* è una struttura JSON che contiene informazioni del client relative a un turno o a un tentativo di connessione. La struttura è costituita da timestamp del client che registrano i momenti in cui si verificano eventi client. Ogni timestamp deve essere in formato ISO 8601, come descritto nella sezione "Intestazione X-Timestamp". Se in alcuni messaggi di *telemetria* non sono specificati tutti i campi obbligatori della struttura JSON o non è usato il formato di timestamp corretto, il servizio potrebbe terminare la connessione al client. I client *devono* fornire valori validi per tutti i campi obbligatori e *dovrebbero* specificare valori per i campi facoltativi, quando opportuno. I valori negli esempi di questa sezione sono riportati solo a scopo illustrativo.

Lo schema dei dati di telemetria è suddiviso nelle parti seguenti: timestamp dei messaggi ricevuti e metriche. Le sezioni seguenti illustrano il formato e l'uso di ogni parte.

### <a name="received-message-time-stamps"></a>Timestamp dei messaggi ricevuti

I client devono includere i valori relativi ai tempi di ricezione per tutti i messaggi che ricevono dopo aver stabilito la connessione al servizio. Questi valori devono registrare il momento in cui il client ha *ricevuto* ogni messaggio dalla rete. Non devono essere registrati altri tempi. Ad esempio, il client non deve registrare il momento in cui ha *agito* sul messaggio. I timestamp dei messaggi ricevuti sono specificati in una matrice di coppie *nome:valore*. Il nome della coppia specifica il valore *Path* del messaggio. Il valore della coppia specifica il momento in cui il client ha ricevuto il messaggio. In alternativa, se sono stati ricevuti più messaggi con il nome specificato, il valore della coppia è costituito da una matrice di timestamp che indica i diversi momenti in cui sono stati ricevuti i messaggi.

```JSON
  "ReceivedMessages": [
    { "speech.hypothesis": [ "2016-08-16T15:03:48.172Z", "2016-08-16T15:03:48.331Z", "2016-08-16T15:03:48.881Z" ] },
    { "speech.endDetected": "2016-08-16T15:03:49.721Z" },
    { "speech.phrase": "2016-08-16T15:03:50.001Z" },
    { "turn.end": "2016-08-16T15:03:51.021Z" }
  ]
```

I client *devono* confermare la ricezione di tutti i messaggi inviati dal servizio includendo i timestamp corrispondenti nel corpo JSON. Se un client non conferma la ricezione di un messaggio, il servizio potrebbe terminare la connessione.

### <a name="metrics"></a>metrics

I client devono includere informazioni sugli eventi che si sono verificati durante il ciclo di vita di una richiesta. Sono supportate le metriche seguenti: `Connection`, `Microphone` e `ListeningTrigger`.

### <a name="metric-connection"></a>Metrica `Connection`

La metrica `Connection` specifica i dettagli relativi ai tentativi di connessione eseguiti dal client e deve includere i timestamp corrispondenti ai momenti in cui la connessione WebSocket è stata avviata e terminata. La metrica `Connection` è obbligatoria *solo per il primo turno di una connessione*. I turni successivi non devono includere queste informazioni. Se un client esegue più tentativi prima di riuscire a stabilire una connessione, devono essere incluse le informazioni relative a *tutti* i tentativi di connessione. Per altre informazioni, vedere [Telemetria degli errori di connessione](#connection-failure-telemetry).

| Campo | Descrizione | Uso |
| ----- | ----------- | ----- |
| Name | `Connection` | Obbligatorio |
| ID | Valore dell'identificatore di connessione che è stato usato nell'intestazione *X-ConnectionId* per la richiesta di connessione | Obbligatorio |
| Inizia | Data e ora in cui il client ha inviato la richiesta di connessione | Obbligatorio |
| End | Data e ora in cui il client ha ricevuto la notifica che la connessione è stata stabilita correttamente o, in caso di errore, è stata respinta o rifiutata oppure non è riuscita | Obbligatorio |
| Tipi di errore | Descrizione dell'eventuale errore. Se la connessione ha avuto esito positivo, i client devono omettere questo campo. Il campo può contenere al massimo 50 caratteri. | Obbligatorio in caso di errore, altrimenti omesso |

La descrizione dell'errore deve includere al massimo 50 caratteri e dovrebbe preferibilmente specificare uno dei valori elencati nella tabella seguente. Se la condizione di errore non corrisponde a uno di questi valori, i client possono usare una breve descrizione della condizione di errore usando la notazione [camelCase](https://en.wikipedia.org/wiki/Camel_case) senza spazi vuoti. Per l'invio di un messaggio di *telemetria* è necessario che sia attiva la connessione al servizio. Nel messaggio di *telemetria* possono pertanto essere segnalate solo condizioni di errore transitorie o temporanee. Le condizioni di errore che causano il blocco *permanente* della connessione del client al servizio impediscono al client di inviare messaggi al servizio, inclusi quelli di *telemetria*.

| Tipi di errore | Uso |
| ----- | ----- |
| DNSfailure | Il client non è riuscito a connettersi al servizio a causa di un errore DNS nello stack di rete. |
| NoNetwork | Il client ha tentato una connessione, ma lo stack di rete ha segnalato che non era disponibile alcuna rete fisica. |
| NoAuthorization | La connessione client non è riuscita durante il tentativo di acquisire un token di autorizzazione per la connessione. |
| NoResources | Il client ha esaurito alcune risorse locali, ad esempio la memoria, durante il tentativo di stabilire una connessione. |
| Accesso negato | Il client non è riuscito a connettersi al servizio perché quest'ultimo ha restituito un codice di stato HTTP `403 Forbidden` nella richiesta di aggiornamento a WebSocket. |
| Non autorizzata | Il client non è riuscito a connettersi al servizio perché quest'ultimo ha restituito un codice di stato HTTP `401 Unauthorized` nella richiesta di aggiornamento a WebSocket. |
| RichiestaNonValida | Il client non è riuscito a connettersi al servizio perché quest'ultimo ha restituito un codice di stato HTTP `400 Bad Request` nella richiesta di aggiornamento a WebSocket. |
| ServerUnavailable | Il client non è riuscito a connettersi al servizio perché quest'ultimo ha restituito un codice di stato HTTP `503 Server Unavailable` nella richiesta di aggiornamento a WebSocket. |
| ServerError | Il client non è riuscito a connettersi al servizio perché quest'ultimo ha restituito un codice di stato di errore interno `HTTP 500` nella richiesta di aggiornamento a WebSocket. |
| Timeout | Si è verificato il timeout della richiesta di connessione del client senza una risposta proveniente dal servizio. Il campo *End* contiene la data e l'ora in cui si è verificato il timeout del client o è stata interrotta l'attesa della connessione. |
| ClientError | Il client ha terminato la connessione a causa di alcuni errori interni del client. |

### <a name="metric-microphone"></a>Metrica `Microphone`

La metrica `Microphone` è obbligatoria per tutti i turni del parlato. Questa metrica misura il tempo registrato sul client durante il quale l'input audio viene usato attivamente per una richiesta di riconoscimento vocale.

Usare gli esempi seguenti come linee guida per la registrazione dei valori temporali *Start* per la metrica `Microphone` nell'applicazione client:

* Un'applicazione client richiede che l'utente prema un pulsante fisico per avviare il microfono. Dopo che il pulsante è stato premuto, l'applicazione client legge l'input dal microfono e lo invia al Servizio di riconoscimento vocale. Il valore *Start* per la metrica `Microphone` registra il momento successivo alla pressione del pulsante in cui il microfono viene inizializzato ed è pronto per fornire l'input. Il valore *End* per la metrica `Microphone` registra il momento in cui l'applicazione client ha arrestato il flusso audio verso il servizio dopo aver ricevuto il messaggio `speech.endDetected` dal servizio.

* Un'applicazione client usa un rilevatore di parole chiave che rimane "sempre" in ascolto. Solo dopo che il rilevatore di parole chiave ha individuato un'espressione trigger del parlato, l'applicazione client raccoglie l'input dal microfono e lo invia al Servizio di riconoscimento vocale. Il valore *Start* per la metrica `Microphone` registra il momento in cui il rilevatore di parole chiave ha notificato al client di iniziare a usare l'input proveniente dal microfono. Il valore *End* per la metrica `Microphone` registra il momento in cui l'applicazione client ha arrestato il flusso audio verso il servizio dopo aver ricevuto il messaggio `speech.endDetected` dal servizio.

* Un'applicazione client ha accesso a un flusso audio costante ed esegue il rilevamento dei silenzi o del contenuto vocale su tale flusso audio in un *modulo di rilevamento vocale*. Il valore *Start* per la metrica `Microphone` registra il momento in cui il *modulo di rilevamento vocale* ha notificato al client di iniziare a usare l'input proveniente dal flusso audio. Il valore *End* per la metrica `Microphone` registra il momento in cui l'applicazione client ha arrestato il flusso audio verso il servizio dopo aver ricevuto il messaggio `speech.endDetected` dal servizio.

* Un'applicazione client sta elaborando il secondo turno di una richiesta con più turni e riceve un messaggio di risposta del servizio in cui viene chiesto di attivare il microfono per raccogliere l'input relativo al secondo turno. Il valore *Start* per la metrica `Microphone` registra il momento in cui l'applicazione client abilita il microfono e inizia a usare l'input proveniente dall'origine audio. Il valore *End* per la metrica `Microphone` registra il momento in cui l'applicazione client ha arrestato il flusso audio verso il servizio dopo aver ricevuto il messaggio `speech.endDetected` dal servizio.

Il valore temporale *End* per la metrica `Microphone` registra il momento in cui l'applicazione client ha arrestato il flusso audio di input. Nella maggior parte delle situazioni questo evento si verifica subito dopo che il client ha ricevuto il messaggio `speech.endDetected` dal servizio. Le applicazioni client possono verificare la conformità al protocollo assicurandosi che il valore temporale *End* della metrica `Microphone` sia successivo a quello di ricezione per il messaggio `speech.endDetected`. Inoltre, poiché in genere si verifica un ritardo tra la fine di un turno e l'inizio di un altro, i client possono verificare la conformità al protocollo assicurandosi che il valore *Start* della metrica `Microphone` per i turni successivi registri correttamente la data e l'ora in cui il client ha *iniziato* a usare il microfono per inviare il flusso audio di input al servizio.

| Campo | Descrizione | Uso |
| ----- | ----------- | ----- |
| NOME | Microphone | Obbligatorio |
| Inizia | Data e ora in cui il client ha iniziato a usare l'input audio proveniente dal microfono o un altro flusso audio oppure ha ricevuto un trigger dal rilevatore di parole chiave | Obbligatorio |
| End | Data e ora in cui il client ha arrestato l'uso del microfono o del flusso audio | Obbligatorio |
| Tipi di errore | Descrizione dell'eventuale errore. Se le operazioni del microfono sono state eseguite correttamente, i client devono omettere questo campo. Il campo può contenere al massimo 50 caratteri. | Obbligatorio in caso di errore, altrimenti omesso |

### <a name="metric-listeningtrigger"></a>Metrica `ListeningTrigger`
La metrica `ListeningTrigger` misura il tempo in cui l'utente esegue l'azione che avvia l'input vocale. La metrica `ListeningTrigger` è facoltativa, ma ne è consigliato l'uso nei client.

Usare gli esempi seguenti come linee guida per la registrazione dei valori temporali *Start* e *End* per la metrica `ListeningTrigger` nell'applicazione client.

* Un'applicazione client richiede che l'utente prema un pulsante fisico per avviare il microfono. Il valore *Start* per questa metrica registra il momento in cui viene premuto il pulsante. Il valore *End* registra il momento in cui termina la pressione del pulsante.

* Un'applicazione client usa un rilevatore di parole chiave che rimane "sempre" in ascolto. Dopo che il rilevatore di parole chiave ha individuato un'espressione trigger del parlato, l'applicazione client legge l'input dal microfono e lo invia al Servizio di riconoscimento vocale. Il valore *Start* per questa metrica registra il momento in cui il rilevatore di parole chiave ha ricevuto contenuto audio che ha identificato come espressione trigger. Il valore *End* registra il momento in cui l'ultima parola dell'espressione trigger è stata pronunciata dall'utente.

* Un'applicazione client ha accesso a un flusso audio costante ed esegue il rilevamento dei silenzi o del contenuto vocale su tale flusso audio in un *modulo di rilevamento vocale*. Il valore *Start* per questa metrica registra il momento in cui il *modulo di rilevamento vocale* ha ricevuto l'audio che ha identificato come contenuto vocale. Il valore *End* registra il momento in cui il *modulo di rilevamento vocale* ha rilevato la presenza di contenuto vocale.

* Un'applicazione client sta elaborando il secondo turno di una richiesta con più turni e riceve un messaggio di risposta del servizio in cui viene chiesto di attivare il microfono per raccogliere l'input relativo al secondo turno. L'applicazione client *non* deve includere una metrica `ListeningTrigger` per questo turno.

| Campo | Descrizione | Uso |
| ----- | ----------- | ----- |
| Name | ListeningTrigger | Facoltativo |
| Inizia | Data e ora in cui il trigger di ascolto del client è iniziato | Obbligatorio |
| End | Data e ora in cui il trigger di ascolto del client è terminato | Obbligatorio |
| Tipi di errore | Descrizione dell'eventuale errore. Se l'operazione di trigger è stata eseguita correttamente, i client devono omettere questo campo. Il campo può contenere al massimo 50 caratteri. | Obbligatorio in caso di errore, altrimenti omesso |

#### <a name="sample-message"></a>Messaggio di esempio

L'esempio seguente illustra un messaggio di telemetria con entrambe le parti ReceivedMessages e Metrics:

```HTML
Path: telemetry
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000
X-Timestamp: 2016-08-16T15:03:54.183Z

{
  "ReceivedMessages": [
    { "speech.hypothesis": [ "2016-08-16T15:03:48.171Z", "2016-08-16T15:03:48.331Z", "2016-08-16T15:03:48.881Z" ] },
    { "speech.endDetected": "2016-08-16T15:03:49.721Z" },
    { "speech.phrase": "2016-08-16T15:03:50.001Z" },
    { "turn.end": "2016-08-16T15:03:51.021Z" }
  ],
  "Metrics": [
    {
      "Name": "Connection",
      "Id": "A140CAF92F71469FA41C72C7B5849253",
      "Start": "2016-08-16T15:03:47.921Z",
      "End": "2016-08-16T15:03:48.000Z",
    },
    {
      "Name": "ListeningTrigger",
      "Start": "2016-08-16T15:03:48.776Z",
      "End": "2016-08-16T15:03:48.777Z",
    },
    {
      "Name": "Microphone",
      "Start": "2016-08-16T15:03:47.921Z",
      "End": "2016-08-16T15:03:51.921Z",
    },
  ],
}
```

## <a name="error-handling"></a>Gestione degli errori

Questa sezione descrive i tipi di messaggi e condizioni di errore che devono essere gestiti dall'applicazione.

### <a name="http-status-codes"></a>Codici di stato HTTP

Durante la richiesta di aggiornamento a WebSocket, il Servizio di riconoscimento vocale può restituire uno dei codici di stato HTTP standard, ad esempio `400 Bad Request` e così via. L'applicazione deve gestire correttamente queste condizioni di errore.

#### <a name="authorization-errors"></a>Errori di autorizzazione

Se durante l'aggiornamento a WebSocket i dati di autorizzazione non risultano corretti, il Servizio di riconoscimento vocale restituisce un codice di stato HTTP `403 Forbidden`. Questo codice di errore può essere attivato dalle condizioni seguenti:

* Intestazione *Authorization* mancante

* Token di autorizzazione non valido

* Token di autorizzazione scaduto

Il messaggio di errore `403 Forbidden` indica un problema che non riguarda il Servizio di riconoscimento vocale, ma l'applicazione client.

### <a name="protocol-violation-errors"></a>Errori di violazione del protocollo

Se il Servizio di riconoscimento vocale rileva eventuali violazioni del protocollo da parte di un client, termina la connessione WebSocket dopo aver restituito un *codice di stato* e indicato il *motivo* per cui la connessione è stata terminata. Le applicazioni client possono usare queste informazioni per risolvere i problemi e correggere le violazioni.

#### <a name="incorrect-message-format"></a>Formato del messaggio non corretto

Se un client invia al servizio un messaggio di testo o binario che non è codificato nel formato corretto definito in questa specifica, il servizio chiude la connessione con un codice di stato *1007 - Dati di payload non validi*.

Questo codice di stato viene restituito per vari motivi, come illustrato negli esempi di messaggio seguenti:

* "Incorrect message format. Binary message has invalid header size prefix." Il client ha inviato un messaggio binario con un prefisso per la dimensione dell'intestazione non valido.

* "Incorrect message format. Binary message has invalid header size." Il client ha inviato un messaggio binario con una dimensione dell'intestazione non valida.

* "Incorrect message format. Binary message headers decoding into UTF-8 failed." Il client ha inviato un messaggio binario che contiene intestazioni non codificate correttamente in UTF-8.

* "Incorrect message format. Text message contains no data." Il client ha inviato un messaggio di testo che non contiene dati nel corpo.

* "Incorrect message format. Text message decoding into UTF-8 failed." Il client ha inviato un messaggio di testo che non è stato codificato correttamente in UTF-8.

* "Incorrect message format. Text message contains no header separator." Il client ha inviato un messaggio di testo che non contiene separatori di intestazione o in cui è presente un separatore di intestazione non corretto.

#### <a name="missing-or-empty-headers"></a>Intestazioni mancanti o vuote

Se un client invia un messaggio privo delle intestazioni obbligatorie *X-RequestId* o *Path*, il servizio chiude la connessione con un codice di stato *1002 - Errore del protocollo*. Il messaggio restituito è "Missing/Empty header. {Header name}." (Intestazione mancante o vuota. {Nome intestazione}.)

#### <a name="requestid-values"></a>Valori RequestId

Se un client invia un messaggio che specifica un'intestazione *X-RequestId* con un formato non corretto, il servizio chiude la connessione e restituisce uno stato *1002 - Errore del protocollo*. Il messaggio restituito è "Invalid request. X-RequestId header value was not specified in no-dash UUID format." (Richiesta non valida. Valore dell'intestazione X-RequestId non specificato nel formato UUID senza trattini.)

#### <a name="audio-encoding-errors"></a>Errori di codifica audio

Se un client invia un blocco audio che avvia un turno e il formato o la codifica audio non è conforme alla specifica richiesta, il servizio chiude la connessione e restituisce un codice di stato *1007 - Dati di payload non validi*. Il messaggio indica l'origine dell'errore di codifica del formato.

#### <a name="requestid-reuse"></a>Riutilizzo di RequestId

Al termine di un turno, se un client invia un messaggio che riutilizza l'identificatore della richiesta di tale turno, il servizio chiude la connessione e restituisce un codice di stato *1002 - Errore del protocollo*. Il messaggio restituito è "Invalid request. "Reuse of request identifiers is not allowed." (Richiesta non valida. Non è consentito riutilizzare gli identificatori di richiesta.)

## <a name="connection-failure-telemetry"></a>Telemetria degli errori di connessione

Per garantire un'esperienza utente ottimale, i client devono comunicare al Servizio di riconoscimento vocale i timestamp relativi ai checkpoint importanti di una connessione usando il messaggio di *telemetria*. È ugualmente importante che i client informino il servizio dei tentativi di connessione non riusciti.

Per ogni tentativo di connessione non riuscito, i client creano un messaggio di *telemetria* con un valore di intestazione *X-RequestId* univoco. Poiché il client non è riuscito a stabilire una connessione, il campo *ReceivedMessages* nel corpo JSON può essere omesso. Viene inclusa solo la voce `Connection` nel campo *Metrics*. Questa voce contiene i timestamp di inizio e fine e la condizione di errore rilevata.

### <a name="connection-retries-in-telemetry"></a>Retry di connessione nei dati di telemetria

I client devono distinguere i *retry* dai *tentativi multipli di connessione* in base all'evento che attiva il tentativo di connessione. I retry sono tentativi di connessione eseguiti a livello di codice senza input da parte dell'utente. I tentativi multipli di connessione vengono invece eseguiti in risposta all'input dell'utente. I client assegnano a ogni tentativo di connessione attivato dall'utente un valore di intestazione *X-RequestId* univoco e un messaggio di *telemetria*. Il valore di *X-RequestId* viene riutilizzato per i retry a livello di codice. Se sono stati eseguiti più retry per un singolo tentativo di connessione, ogni retry viene incluso come voce `Connection` nel messaggio di *telemetria*.

Si supponga ad esempio che un utente pronunci una parola chiave trigger per avviare una connessione e che il primo tentativo di connessione non riesca a causa di errori DNS, ma che invece un secondo tentativo eseguito dal client a livello di codice abbia esito positivo. Poiché il client ha eseguito il retry della connessione senza richiedere altro input da parte dell'utente, il client usa un singolo messaggio di *telemetria* con più voci `Connection` per descrivere la connessione.

Come altro esempio, si supponga che un utente pronunci una parola chiave trigger per avviare una connessione e che questo tentativo di connessione non riesca dopo tre retry. Il client rinuncia eseguire altri tentativi di connessione al servizio e informa l'utente che si è verificato un problema. L'utente pronuncia nuovamente la parola chiave trigger. Si supponga che questa volta il client riesca a connettersi al servizio. Dopo la connessione, il client invia immediatamente al servizio un messaggio di *telemetria* contenente tre voci `Connection` che descrivono gli errori di connessione. Dopo la ricezione del messaggio `turn.end`, il client invia un altro messaggio di *telemetria* che descrive l'esito positivo della connessione.

## <a name="error-message-reference"></a>Informazioni di riferimento sui messaggi di errore

### <a name="http-status-codes"></a>Codici di stato HTTP

| Stato codice HTTP | Descrizione | risoluzione dei problemi |
| - | - | - |
| 400 - Richiesta non valida | Il client ha inviato una richiesta di connessione WebSocket non corretta. | Controllare di avere specificato tutte le intestazioni HTTP e tutti i parametri obbligatori e che i valori siano corretti. |
| 401 - Non autorizzato | Il client non ha incluso tutte le informazioni richieste per l'autorizzazione. | Controllare di inviare l'intestazione *Authorization* nella connessione WebSocket. |
| 403 - Accesso negato | Il client ha inviato le informazioni per l'autorizzazione ma non erano valide. | Controllare di non inviare un valore scaduto o non valido nell'intestazione *Authorization*. |
| 404 - Non trovato | Il client ha provato ad accedere a un percorso URL non supportato. | Controllare di usare l'URL corretto per la connessione WebSocket. |
| 500 - Errore del server | Il servizio ha rilevato un errore interno e non ha potuto soddisfare la richiesta. | Nella maggior parte dei casi, questo errore è temporaneo. ripetere la richiesta. |
| 503 - Servizio non disponibile | Il servizio non era disponibile per gestire la richiesta. | Nella maggior parte dei casi, questo errore è temporaneo. ripetere la richiesta. |

### <a name="websocket-error-codes"></a>Codici di errore di WebSocket

| Codice di stato WebSocket | Descrizione | risoluzione dei problemi |
| - | - | - |
| 1000 - Chiusura normale | Il servizio ha chiuso la connessione WebSocket senza errori. | Se la chiusura della connessione WebSocket non era prevista, rileggere la documentazione per verificare di avere compreso come e quando il servizio può terminare la connessione WebSocket. |
| 1002 - Errore del protocollo | Il client non è riuscito a soddisfare i requisiti del protocollo. | Assicurarsi di comprendere il contenuto della documentazione relativa al protocollo e di conoscere esattamente i requisiti. Leggere la documentazione precedente sui motivi degli errori per verificare se si stanno violando requisiti del protocollo. |
| 1007 - Dati di payload non validi | Il client ha inviato un payload non valido in un messaggio del protocollo. | Controllare la presenza di eventuali errori nell'ultimo messaggio inviato al servizio. Leggere la documentazione precedente sugli errori di payload. |
| 1011 - Errore del server | Il servizio ha rilevato un errore interno e non ha potuto soddisfare la richiesta. | Nella maggior parte dei casi, questo errore è temporaneo. ripetere la richiesta. |

## <a name="related-topics"></a>Argomenti correlati

Vedere un [SDK JavaScript](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript) che presenta un'implementazione del protocollo del Servizio di riconoscimento vocale basato su WebSocket.
