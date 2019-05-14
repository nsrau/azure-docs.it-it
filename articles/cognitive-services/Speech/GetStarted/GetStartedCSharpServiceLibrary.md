---
title: Introduzione all'API Riconoscimento vocale Microsoft con la libreria di servizi C# | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Usare la libreria di servizi per il riconoscimento vocale Bing per convertire la lingua parlata in testo.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 0f445d1fff48ee7a04c0b1c1d64c808f87d824b7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60515218"
---
# <a name="quickstart-use-the-bing-speech-recognition-service-library-in-c35-for-net-windows"></a>Avvio rapido: Usare la libreria di servizi di Riconoscimento vocale Bing in C&#35; per .NET per dispositivi Windows

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

La libreria di servizi è destinata agli sviluppatori che dispongono del proprio servizio cloud e vogliono chiamare il Servizio di riconoscimento vocale dal proprio servizio. Se si vuole chiamare il Servizio di riconoscimento vocale da app associate al dispositivo, non usare questo SDK. Usare altre librerie client o le API REST.

Per usare la libreria di servizi C#, installare il [pacchetto NuGet Microsoft.Bing.Speech](https://www.nuget.org/packages/Microsoft.Bing.Speech/). Per informazioni di riferimento sull'API della libreria, vedere la [libreria di servizi C# del Servizio di riconoscimento vocale Microsoft](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html).

Le sezioni seguenti descrivono come installare, compilare ed eseguire l'applicazione di esempio in C# tramite la libreria di servizi C#.

## <a name="prerequisites"></a>Prerequisiti

### <a name="platform-requirements"></a>Requisiti di piattaforma

L'esempio seguente è stato sviluppato per Windows 8 e versioni successive e .NET Framework 4.5 e versioni successive usando [Visual Studio 2015, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs).

### <a name="get-the-sample-application"></a>Ottenere l'applicazione di esempio

Clonare l'esempio dal repository di esempio della [libreria di servizi C# per il riconoscimento vocale](https://github.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary).

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Eseguire la sottoscrizione all'API Riconoscimento vocale e ottenere una chiave di sottoscrizione della versione di valutazione gratuita

Speech API fa parte di Servizi cognitivi (noto in precedenza come Project Oxford). È possibile ottenere le chiavi di sottoscrizione della versione di valutazione gratuita dalla pagina di [sottoscrizione di Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/). Dopo aver selezionato Speech API, fare clic su **Ottieni la chiave API** per ottenere la chiave. Vengono restituite una chiave primaria e una chiave secondaria. Entrambe le chiavi sono legate alla stessa quota ed è quindi possibile usare indifferentemente una delle due.

> [!IMPORTANT]
> * Ottenere una chiave di sottoscrizione. Prima di poter usare le librerie client per il riconoscimento vocale, è necessario avere una [chiave di sottoscrizione](https://azure.microsoft.com/try/cognitive-services/).
>
> * Usare la chiave di sottoscrizione. Con l'applicazione di esempio della libreria di servizi C# fornita, è necessario specificare la chiave di sottoscrizione come uno dei parametri della riga di comando. Per altre informazioni, vedere [Eseguire l'applicazione di esempio](#step-3-run-the-sample-application).

## <a name="step-1-install-the-sample-application"></a>Passaggio 1: Installare l'applicazione di esempio

1. Avviare Visual Studio 2015 e selezionare **File** > **Apri** > **Progetto/Soluzione**.

2. Fare doppio clic per aprire il file di soluzione di Visual Studio 2015 (con estensione sln) denominato SpeechClient.sln. La soluzione viene visualizzata in Visual Studio.

## <a name="step-2-build-the-sample-application"></a>Passaggio 2: Compilare l'applicazione di esempio

Premere CTRL+MAIUSC+B oppure fare clic su **Compila** nel menu della barra multifunzione. Selezionare quindi **Compila soluzione**.

## <a name="step-3-run-the-sample-application"></a>Passaggio 3: Eseguire l'applicazione di esempio

1. Al termine della compilazione, premere F5 o fare clic su **Avvia** nel menu della barra multifunzione per eseguire l'esempio.

2. Aprire la directory di output per l'esempio, ad esempio SpeechClientSample\bin\Debug. Premere MAIUSC+freccia destra e selezionare **Apri finestra di comando qui**.

3. Eseguire `SpeechClientSample.exe` con gli argomenti seguenti:

   * Arg[0]: specificare un file audio di input in formato wav.
   * Arg[1]: specificare le impostazioni locali per l'audio.
   * Arg[2]: specificare le modalità di riconoscimento: *Short* per la modalità `ShortPhrase` e *Long* per la modalità `LongDictation`.
   * Arg[3]: specificare la chiave di sottoscrizione per accedere al servizio di riconoscimento vocale.

## <a name="samples-explained"></a>Descrizione degli esempi

### <a name="recognition-modes"></a>Modalità di riconoscimento

* Modalità `ShortPhrase`: un'espressione fino a 15 secondi di durata. Quando i dati vengono inviati al server, il client riceve più risultati parziali e il miglior risultato finale.
* Modalità `LongDictation`: un'espressione fino a 10 minuti di durata. Quando i dati vengono inviati al server, il client riceve più risultati parziali e più risultati finali, in base alle posizioni di pausa della frase indicate dal server.

### <a name="supported-audio-formats"></a>Formati audio supportati

Speech API supporta file audio/WAV tramite i codec seguenti:

* Singolo canale PCM
* Siren
* SirenSR

### <a name="preferences"></a>Preferenze

Per creare un oggetto SpeechClient, è necessario creare innanzitutto un oggetto Preferences. L'oggetto Preferences è un set di parametri che configura il comportamento del Servizio di riconoscimento vocale. È costituito dagli elementi seguenti:

* `SpeechLanguage`: le impostazioni locali del file audio inviato al servizio di riconoscimento vocale.
* `ServiceUri`: l'endpoint usato per chiamare il servizio di riconoscimento vocale.
* `AuthorizationProvider`: un'implementazione di IAuthorizationProvider usata per recuperare i token per accedere al servizio di riconoscimento vocale. Anche se l'esempio fornisce un provider di autorizzazione di Servizi cognitivi, è consigliabile creare un'implementazione personalizzata per gestire la memorizzazione nella cache del token.
* `EnableAudioBuffering`: un'opzione avanzata. Vedere [Gestione delle connessioni](#connection-management).

### <a name="speech-input"></a>Input vocale

L'oggetto SpeechInput è costituito da due campi:

* **Audio**: un'implementazione del flusso di propria scelta da cui l'SDK esegue il pull dell'audio. Può trattarsi di un qualsiasi [flusso](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx) che supporta la lettura.
   > [!NOTE]
   > L'SDK rileva la fine del flusso quando quest'ultimo restituisce **0** in lettura.

* **RequestMetadata**: metadati relativi alla richiesta di riconoscimento vocale. Per altre informazioni, vedere le [informazioni di riferimento](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html).

### <a name="speech-request"></a>Richiesta di riconoscimento vocale

Dopo aver creato un'istanza degli oggetti SpeechClient e SpeechInput, usare RecognizeAsync per effettuare una richiesta al Servizio di riconoscimento vocale.

```cs
    var task = speechClient.RecognizeAsync(speechInput);
```

Al termine della richiesta, l'attività restituita da RecognizeAsync termina. L'ultimo oggetto RecognitionResult corrisponde alla fine del riconoscimento. Se si verifica un errore imprevisto del servizio o dell'SDK, l'attività può avere esito negativo.

### <a name="speech-recognition-events"></a>Eventi di riconoscimento vocale

#### <a name="partial-results-event"></a>Eventi con risultati parziali

Questi eventi vengono chiamati ogni volta che il Servizio di riconoscimento vocale formula un'ipotesi su ciò che l'utente potrebbe dire, ancora prima che finisca di parlare (se si usa `MicrophoneRecognitionClient`) o completi l'invio dei dati (se si usa `DataRecognitionClient`). È possibile eseguire la sottoscrizione all'evento usando `SpeechClient.SubscribeToPartialResult()`. In alternativa è possibile usare il metodo di sottoscrizione a eventi generici `SpeechClient.SubscribeTo<RecognitionPartialResult>()`.

**Formato restituito** | DESCRIZIONE |
------|------
**LexicalForm** | Questo modulo è ottimale per l'uso da parte di applicazioni che richiedono risultati di riconoscimento vocale non elaborati.
**DisplayText** | Frase riconosciuta con applicazione di normalizzazione di testo inverso, maiuscole/minuscole, punteggiatura e mascheramento di contenuto volgare. Il contenuto volgare viene nascosto con asterischi dopo il carattere iniziale, ad esempio "c***". Questo modulo è ottimale per l'uso da parte di applicazioni che visualizzano i risultati di riconoscimento vocale a un utente.
**Confidence** | Livello di attendibilità rappresentato dalla frase riconosciuta per l'audio associato come definito dal server di riconoscimento vocale.
**MediaTime** | Ora corrente rispetto all'inizio del flusso audio (in unità di 100 nanosecondi).
**MediaDuration** | Durata/lunghezza della frase corrente rispetto al segmento audio (in unità di 100 nanosecondi).

#### <a name="result-event"></a>Evento con risultato
Questo evento viene chiamato quando l'utente finisce di parlare (nella modalità `ShortPhrase`). Vengono fornite N scelte migliori per il risultato. Nella modalità `LongDictation` l'evento può essere chiamato più volte, in base alle posizioni di pausa della frase indicate dal server. È possibile eseguire la sottoscrizione all'evento usando `SpeechClient.SubscribeToRecognitionResult()`. In alternativa è possibile usare il metodo di sottoscrizione a eventi generici `SpeechClient.SubscribeTo<RecognitionResult>()`.

**Formato restituito** | DESCRIZIONE |
------|------|
**RecognitionStatus** | Stato relativo alla modalità di produzione del riconoscimento. Ad esempio, è stato generato come risultato di un riconoscimento con esito positivo o come risultato dell'annullamento della connessione e così via.
**Phrases** | Set delle N migliori frasi riconosciute con il livello di attendibilità del riconoscimento.

Per altre informazioni sui risultati di riconoscimento, vedere [Formato di output](../Concepts.md#output-format).

### <a name="speech-recognition-response"></a>Risposta di riconoscimento vocale

Esempio di risposta di riconoscimento vocale:
```
--- Partial result received by OnPartialResult  
---what  
--- Partial result received by OnPartialResult  
--what's  
--- Partial result received by OnPartialResult  
---what's the web  
--- Partial result received by OnPartialResult   
---what's the weather like  
---***** Phrase Recognition Status = [Success]   
***What's the weather like? (Confidence:High)  
What's the weather like? (Confidence:High)
```

## <a name="connection-management"></a>Gestione delle connessioni

L'API usa una singola connessione WebSocket per ogni richiesta. Per un'esperienza utente ottimale, l'SDK tenta di riconnettersi al Servizio di riconoscimento vocale e avviare il riconoscimento dall'ultimo oggetto RecognitionResult ricevuto. Ad esempio, se la richiesta audio ha una durata di due minuti, l'SDK ha ricevuto un oggetto RecognitionEvent in corrispondenza del contrassegno di un minuto e si è verificato un errore di rete dopo cinque secondi, l'SDK avvia una nuova connessione che inizia dal contrassegno di un minuto.

>[!NOTE]
>L'SDK non può tentare la nuova connessione prima del contrassegno di un minuto perché il flusso potrebbe non supportare la ricerca. Al contrario, l'SDK mantiene un buffer interno che usa per memorizzare nel buffer l'audio e cancella il buffer non appena riceve gli eventi RecognitionResult.

## <a name="buffering-behavior"></a>Comportamento del buffer

Per impostazione predefinita, l'SDK memorizza nel buffer l'audio, in modo che possa eseguire il ripristino quando si verifica un'interruzione di rete. In uno scenario in cui è preferibile rimuovere la perdita di audio durante la disconnessione di rete e riavviare la connessione, è consigliabile disabilitare il buffering audio impostando `EnableAudioBuffering` nell'oggetto Preferences su `false`.

## <a name="related-topics"></a>Argomenti correlati

[Microsoft Speech C# service library reference](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html) (Informazioni di riferimento sulla libreria di servizi C# del Servizio di riconoscimento vocale Microsoft)
