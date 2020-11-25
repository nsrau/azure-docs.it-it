---
title: Domande frequenti sui contenitori di servizi vocali
titleSuffix: Azure Cognitive Services
description: Installare ed eseguire i contenitori di riconoscimento vocale. riconoscimento vocale consente di trascrivere flussi audio in testo in tempo reale che possono essere utilizzati o visualizzati da applicazioni, strumenti o dispositivi. Sintesi vocale converte il testo di input in una voce sintetizzata simile a quella di un essere umano.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/12/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: a657f43ef2d889cad1608d34e9235b1d5e7cb576
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95894151"
---
# <a name="speech-service-containers-frequently-asked-questions-faq"></a>Domande frequenti sui contenitori di servizi vocali

Quando si usa il servizio di riconoscimento vocale con i contenitori, utilizzare questa raccolta di domande frequenti prima di eseguire l'escalation per supportare. Questo articolo acquisisce le domande che variano da generale a tecnico. Per espandere una risposta, fare clic sulla domanda.

## <a name="general-questions"></a>Domande generali

<details>
<summary>
<b>Come funzionano I contenitori di riconoscimento vocale e come configurarli?</b>
</summary>

**Risposta:** Quando si configura il cluster di produzione, è necessario prendere in considerazione diversi aspetti. Per prima cosa, la configurazione di un singolo linguaggio, più contenitori, nello stesso computer, non dovrebbe essere un problema di grandi dimensioni. Se si verificano problemi, potrebbe trattarsi di un problema relativo all'hardware, quindi è opportuno esaminare prima di tutto la risorsa. Specifiche della CPU e della memoria.

Prendere in considerazione per un attimo il `ja-JP` contenitore e il modello più recente. Il modello acustico è la parte più impegnativa della CPU, mentre il modello di linguaggio richiede la maggior parte della memoria. Quando l'uso è stato sottoposto a benchmark, sono necessari circa 0,6 core CPU per elaborare una singola richiesta di riconoscimento vocale quando l'audio viene propagata in tempo reale, ad esempio dal microfono. Se l'audio viene alimentato più velocemente rispetto a quello in tempo reale, ad esempio da un file, tale utilizzo può raddoppiare (1,2 x Core). Nel frattempo, la memoria elencata di seguito è memoria operativa per la decodifica del riconoscimento vocale. *Non vengono prese in* considerazione le dimensioni effettive effettive del modello di lingua, che risiederà nella cache dei file. Per `ja-JP` questo è un 2 GB aggiuntivo, per `en-US` , può essere maggiore (6-7 GB).

Se si dispone di un computer in cui la memoria è limitata e si sta tentando di distribuire più lingue, è possibile che la cache dei file sia piena e che il sistema operativo venga forzato a eseguire il paging dei modelli. Per una trascrizione in esecuzione, questo potrebbe essere disastroso e può causare rallentamenti e altre implicazioni sulle prestazioni.

Inoltre, gli eseguibili pre-pacchetto vengono preconfezionati per i computer con il set di istruzioni [Advanced Vector Extension (AVX2)](speech-container-howto.md#advanced-vector-extension-support) . Un computer con il set di istruzioni AVX512 richiede la generazione di codice per tale destinazione e l'avvio di 10 contenitori per 10 lingue potrebbe esaurire temporaneamente la CPU. Un messaggio simile al seguente verrà visualizzato nei log di Docker:

```console
2020-01-16 16:46:54.981118943 
[W:onnxruntime:Default, tvm_utils.cc:276 LoadTVMPackedFuncFromCache]
Cannot find Scan4_llvm__mcpu_skylake_avx512 in cache, using JIT...
```

È possibile impostare il numero di decodificatori desiderati all'interno di un *singolo* contenitore utilizzando la `DECODER MAX_COUNT` variabile. Fondamentalmente, è consigliabile iniziare con lo SKU (CPU/memoria) ed è possibile suggerire come sfruttare al meglio le proprie esigenze. Un ottimo punto di partenza si riferisce alle specifiche delle risorse del computer host consigliate.

<br>
</details>

<details>
<summary>
<b>È possibile semplificare la pianificazione della capacità e la stima dei costi per i contenitori di sintesi vocale locali?</b>
</summary>

**Risposta:** Per la capacità del contenitore in modalità di elaborazione batch, ogni decodificatore può gestire 2-3x in tempo reale, con due core CPU, per un singolo riconoscimento. Non è consigliabile mantenere più di due riconoscimenti simultanei per ogni istanza di contenitore, ma è consigliabile eseguire più istanze di contenitori per motivi di affidabilità e disponibilità, dietro un servizio di bilanciamento del carico.

Sebbene sia possibile eseguire ogni istanza del contenitore con più decodificatori. Ad esempio, potrebbe essere possibile configurare 7 decodificatori per ogni istanza di contenitore in un computer con otto core (a più di 2x ciascuno), ottenendo una velocità effettiva di 15x. È `DECODER_MAX_COUNT` necessario tenere presente un parametro. Per il caso estremo, si verificano problemi di affidabilità e latenza, con una velocità effettiva aumentata significativamente. Per un microfono, sarà in tempo reale. L'utilizzo complessivo deve essere di circa un core per un singolo riconoscimento.

Per lo scenario di elaborazione di 1 K ore al giorno in modalità di elaborazione batch, in un caso estremo, 3 VM possono gestirla entro 24 ore, ma non garantita. Per gestire i giorni di picco, il failover, l'aggiornamento e per fornire il backup/BCP minimo, si consigliano 4-5 computer anziché 3 per cluster e con 2 cluster +.

Per l'hardware, viene usata una macchina virtuale di Azure standard `DS13_v2` come riferimento (ogni core deve essere 2,6 GHz o superiore, con il set di istruzioni AVX2 abilitato).

| Istanza  | vCPU | RAM    | Archiviazione temporanea | Pagamento in base al consumo con vantaggio Azure Hybrid | riserva di 1 anno con vantaggio Azure Hybrid (% di risparmio) | 3 anni riservati con vantaggio Azure Hybrid (% di risparmio) |
|-----------|---------|--------|--------------|------------------------|-------------------------------------|--------------------------------------|
| `DS13 v2` | 8       | GiB 56 | 112 GiB      | $0.598/hour            | $0.3528/hour (~ 41%)                 | $0.2333/hour (~ 61%)                  |

In base al riferimento di progettazione (due cluster di 5 VM per gestire l'elaborazione batch di 1 K ore/giorno), il costo hardware di 1 anno sarà:

> 2 (cluster) * 5 (VM per cluster) * $0.3528/hour * 365 (giorni) * 24 (ore) = $31K/Year

Quando si esegue il mapping a un computer fisico, una stima generale è 1 vCPU = 1 core CPU fisica. In realtà, 1vCPU è più potente rispetto a un singolo core.

Per l'ambiente locale, entrano in gioco tutti questi fattori aggiuntivi:

- Il tipo di CPU fisico e il numero di core
- Il numero di CPU in esecuzione insieme nella stessa casella/computer
- Configurazione delle macchine virtuali
- Modalità di utilizzo di Hyper-Threading/multithread
- Modalità di condivisione della memoria
- Il sistema operativo e così via.

Normalmente non è ottimizzato per l'ambiente di Azure. Considerando il sovraccarico, si potrebbe dire che una stima sicura è 10 core CPU fisici = 8 Azure vCPU. Anche se le CPU più diffuse hanno solo otto core. Con la distribuzione locale, il costo sarà maggiore rispetto all'uso di macchine virtuali di Azure. Prendere in considerazione anche la velocità di ammortamento.

Il costo del servizio è uguale al servizio online: $1/hour per la sintesi vocale. Il costo del servizio vocale è:

> $1 * 1000 * 365 = $365K

I costi di manutenzione a Microsoft dipendono dal livello di servizio e dal contenuto del servizio. Varia da $29.99 al mese per il livello Basic a centinaia di migliaia se è stato richiesto il servizio in sede. Un numero approssimativo è di $300/ora per il servizio o la manutenzione. Il costo delle persone non è incluso. Altri costi dell'infrastruttura, ad esempio l'archiviazione, le reti e i bilanciamenti del carico, non sono inclusi.

<br>
</details>

<details>
<summary>
<b>Perché la punteggiatura manca dalla trascrizione?</b>
</summary>

**Risposta:** Il `speech_recognition_language=<YOUR_LANGUAGE>` deve essere configurato in modo esplicito nella richiesta se usa il client di Carbon.

Ad esempio:

```python
if not recognize_once(
    speechsdk.SpeechRecognizer(
        speech_config=speechsdk.SpeechConfig(
            endpoint=template.format("interactive"),
            speech_recognition_language="ja-JP"),
            audio_config=audio_config)):

    print("Failed interactive endpoint")
    exit(1)
```
Ecco l'output:

```cmd
RECOGNIZED: SpeechRecognitionResult(
    result_id=2111117c8700404a84f521b7b805c4e7, 
    text="まだ早いまだ早いは猫である名前はまだないどこで生まれたかとんと見当を検討をなつかぬ。
    何でも薄暗いじめじめした所でながら泣いていた事だけは記憶している。
    まだは今ここで初めて人間と言うものを見た。
    しかも後で聞くと、それは書生という人間中で一番同額同額。",
    reason=ResultReason.RecognizedSpeech)
```

<br>
</details>

<details>
<summary>
<b>È possibile usare un modello acustico personalizzato e un modello di linguaggio con un contenitore vocale?</b>
</summary>

Attualmente è possibile passare un solo ID modello, ovvero un modello di lingua personalizzato o un modello acustico personalizzato.

**Risposta:** La decisione di *non* supportare contemporaneamente i modelli acustici e di lingua è stata apportata. Questo rimarrà attivo fino a quando non viene creato un identificatore unificato per ridurre le interruzioni dell'API. Quindi, Sfortunatamente questa operazione non è supportata in questo momento.

<br>
</details>

<details>
<summary>
<b>È possibile spiegare questi errori dal contenitore di riconoscimento vocale personalizzato?</b>
</summary>

**Errore 1:**

```cmd
Failed to fetch manifest: Status: 400 Bad Request Body:
{
    "code": "InvalidModel",
    "message": "The specified model is not supported for endpoint manifests."
}
```

**Risposta 1:** Se si sta eseguendo il training con l'ultimo modello personalizzato, attualmente non è supportato. Se si esegue il training con una versione precedente, è possibile usare. Stiamo ancora lavorando per supportare le versioni più recenti.

In pratica, i contenitori personalizzati non supportano i modelli acustici basati su Alogenur o ONNX, che è l'impostazione predefinita nel portale di training personalizzato. Ciò è dovuto al fatto che i modelli personalizzati non vengono crittografati e non si vuole esporre i modelli ONNX, tuttavia; i modelli di linguaggio sono accurati. Il cliente dovrà selezionare esplicitamente un modello non ONNX precedente per la formazione personalizzata. L'accuratezza non sarà interessata. Le dimensioni del modello possono essere maggiori (di 100 MB).

> Modello di supporto > 20190220 (versione 4.5 unificata)

**Errore 2:**

```cmd
HTTPAPI result code = HTTPAPI_OK.
HTTP status code = 400.
Reason:  Synthesis failed.
StatusCode: InvalidArgument,
Details: Voice does not match.
```

**Risposta 2:** È necessario specificare il nome della voce corretto nella richiesta, che fa distinzione tra maiuscole e minuscole. Fare riferimento al mapping del nome completo del servizio. È necessario usare `en-US-JessaRUS` , poiché `en-US-JessaNeural` non è disponibile in questo momento nella versione contenitore di sintesi vocale.

**Errore 3:**

```json
{
    "code": "InvalidProductId",
    "message": "The subscription SKU \"CognitiveServices.S0\" is not supported in this service instance."
}
```

**Risposta 3:** Si Reed per creare una risorsa vocale, non una risorsa di servizi cognitivi.


<br>
</details>

<details>
<summary>
<b>Quali protocolli API sono supportati, REST o WS?</b>
</summary>

**Risposta:** Per i contenitori di riconoscimento vocale e di sintesi vocale personalizzati, attualmente è supportato solo il protocollo basato su WebSocket. L'SDK supporta solo la chiamata a WS ma non a REST. È previsto un piano per aggiungere il supporto REST, ma non l'ETA per il momento. Fare sempre riferimento alla documentazione ufficiale, vedere [query PREDICTION Endpoints](speech-container-howto.md#query-the-containers-prediction-endpoint).

<br>
</details>

<details>
<summary>
<b>CentOS è supportato per i contenitori di sintesi vocale?</b>
</summary>

**Risposta:** CentOS 7 non è ancora supportato da Python SDK, anche Ubuntu 19,04 non è supportato.

Il pacchetto Speech SDK per Python è disponibile per i sistemi operativi seguenti:
- **Windows** -x64 e x86
- **Mac** -MacOS X versione 10,12 o successiva
- **Linux** -Ubuntu 16,04, Ubuntu 18,04, Debian 9 su x64

Per ulteriori informazioni sulla configurazione dell'ambiente, vedere [configurazione della piattaforma Python](quickstarts/setup-platform.md?pivots=programming-language-python). Per il momento, Ubuntu 18,04 è la versione consigliata.

<br>
</details>

<details>
<summary>
<b>Perché si verificano errori durante il tentativo di chiamare gli endpoint di stima LUIS?</b>
</summary>

Si usa il contenitore LUIS in una distribuzione IoT Edge e si tenta di chiamare l'endpoint di stima LUIS da un altro contenitore. Il contenitore LUIS è in ascolto sulla porta 5001 e l'URL usato è il seguente:

```csharp
var luisEndpoint =
    $"ws://192.168.1.91:5001/luis/prediction/v3.0/apps/{luisAppId}/slots/production/predict";
var config = SpeechConfig.FromEndpoint(new Uri(luisEndpoint));
```

L'errore che sto ricevendo è:

```cmd
WebSocket Upgrade failed with HTTP status code: 404 SessionId: 3cfe2509ef4e49919e594abf639ccfeb
```

La richiesta viene visualizzata nei log del contenitore LUIS e il messaggio indica:

```cmd
The request path /luis//predict" does not match a supported file type.
```

Che cosa significa? Cosa manca? Ho seguito l'esempio per Speech SDK, da [qui](https://github.com/Azure-Samples/cognitive-services-speech-sdk). Lo scenario è che si sta rilevando l'audio direttamente dal microfono del PC e si sta provando a determinare lo scopo, in base all'app LUIS sottoposta a training. L'esempio collegato esegue esattamente questa operazione. E funziona bene con il servizio LUIS basato sul cloud. L'uso dell'SDK di riconoscimento vocale ha permesso di evitare di dover effettuare una chiamata esplicita separata all'API di sintesi vocale e quindi una seconda chiamata a LUIS.

Quindi, mi sto tentando di passare dallo scenario di utilizzo di LUIS nel cloud all'uso del contenitore LUIS. Non riesco a immaginare se l'SDK di riconoscimento vocale funziona per uno, non funzionerà per l'altro.

**Risposta:** L'SDK di riconoscimento vocale non deve essere usato in un contenitore LUIS. Per l'uso del contenitore LUIS, è necessario usare l'API REST Luis SDK o Luis. Speech SDK deve essere usato per un contenitore vocale.

Un cloud è diverso da un contenitore. Un cloud può essere composto da più contenitori aggregati (talvolta denominati Micro Services). Quindi, c'è un contenitore LUIS ed è presente un contenitore di riconoscimento vocale, due contenitori distinti. Il contenitore vocale esegue solo la sintesi vocale. Il contenitore LUIS esegue solo LUIS. Nel cloud, poiché entrambi i contenitori sono noti per essere distribuiti e si tratta di prestazioni insufficienti per un client remoto per passare al cloud, eseguire una sintesi vocale, tornare al cloud ed eseguire LUIS, viene fornita una funzionalità che consente al client di passare al linguaggio vocale, restare nel cloud, passare a LUIS e tornare al client. Quindi, anche in questo scenario, l'SDK di riconoscimento vocale passa al contenitore per la sintesi vocale con audio, quindi il contenitore di contenuti vocali del cloud comunica con il contenitore LUIS cloud con testo. Il contenitore LUIS non ha alcun concetto di accettazione dell'audio (non ha senso che il contenitore LUIS accetti lo streaming audio-LUIS è un servizio basato su testo). Con l'ambiente locale, non è certo che il cliente abbia distribuito entrambi i contenitori, non si presume che l'orchestrazione tra i contenitori sia presente nella sede dei clienti. se entrambi i contenitori vengono distribuiti in locale, dato che sono più locali per il client, non è compito di andare prima di tutto la SR, tornare al client e quindi fare in modo che il cliente lo prenda

<br>
</details>

<details>
<summary>
<b>Perché si ricevono errori con macOS, il contenitore vocale e Python SDK?</b>
</summary>

Quando si invia un file *WAV* da trascrivere, il risultato viene restituito con:

```cmd
recognition is running....
Speech Recognition canceled: CancellationReason.Error
Error details: Timeout: no recognition result received.
When creating a websocket connection from the browser a test, we get:
wb = new WebSocket("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
WebSocket
{
    url: "ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1",
    readyState: 0,
    bufferedAmount: 0,
    onopen: null,
    onerror: null,
    ...
}
```

È noto che il WebSocket è configurato correttamente.

**Risposta:** In tal caso, vedere [questo problema di GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310). Abbiamo una soluzione, [proposta qui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310#issuecomment-527542722).

Il carbonio è stato risolto nella versione 1,8.


<br>
</details>

<details>
<summary>
<b>Quali sono le differenze negli endpoint del contenitore vocale?</b>
</summary>

È possibile compilare le metriche di test seguenti, incluse le funzioni da testare e come testare l'SDK e le API REST. In particolare, le differenze in "Interactive" e "Conversation", che non ho visto da documento/campione esistente.

| Endpoint                                                | Test funzionale                                                   | SDK | API REST |
|---------------------------------------------------------|-------------------------------------------------------------------|-----|----------|
| `/speech/synthesize/cognitiveservices/v1`               | Sintetizza testo (sintesi vocale)                                  |     | Sì      |
| `/speech/recognition/dictation/cognitiveservices/v1`    | Endpoint WebSocket di servizi cognitivi per la dettatura V1        | Sì | No       |
| `/speech/recognition/interactive/cognitiveservices/v1`  | Endpoint WebSocket Interactive V1 di servizi cognitivi  |     |          |
| `/speech/recognition/conversation/cognitiveservices/v1` | Endpoint WebSocket della conversazione V1 di servizi cognitivi |     |          |

**Risposta:** Si tratta di una fusione tra:
- Gli utenti che provano l'endpoint di dettatura per i contenitori non sono certo di come hanno ottenuto tale URL.
- L'endpoint dell'entità 1<sup>St</sup> è quello in un contenitore.
- L'endpoint di<sup>1 di</sup> terze parti restituisce messaggi di sintesi vocale anziché i `speech.hypothesis` messaggi restituiti dagli endpoint della parte 3<sup>Rd</sup> per l'endpoint di dettatura.
- Tutte le guide introduttive per i carboni `RecognizeOnce` (modalità interattiva)
- Il carbonio con un'asserzione che per `speech.fragment` i messaggi che richiedono che non vengano restituiti in modalità interattiva.
- Il carbonio con le asserzioni viene attivato nelle build di rilascio (terminando il processo).

La soluzione alternativa è passare all'uso del riconoscimento continuo nel codice o (più veloce) connettersi agli endpoint interattivi o continui nel contenitore.
Per il codice, impostare l'endpoint su `host:port` /Speech/Recognition/Interactive/cognitiveservices/V1

Per le varie modalità, vedere modalità di riconoscimento vocale-vedere di seguito:

[!INCLUDE [speech-modes](includes/speech-modes.md)]

La correzione corretta è disponibile con l'SDK 1,8, che include il supporto locale (sceglierà l'endpoint appropriato, quindi non sarà più il servizio online). Nel frattempo, è disponibile un esempio per il riconoscimento continuo, perché non si fa riferimento a questo?

https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/6805d96bf69d9e95c9137fe129bc5d81e35f6309/samples/python/console/speech_sample.py#L196

<br>
</details>

<details>
<summary>
<b>Quale modalità è consigliabile utilizzare per diversi file audio?</b>
</summary>

**Risposta:** Ecco una [Guida introduttiva con Python](./get-started-speech-to-text.md?pivots=programming-language-python). È possibile trovare le altre lingue collegate al sito docs.

Solo per chiarire l'Interactive, la conversazione e la dettatura; si tratta di un metodo avanzato per specificare il modo in cui il servizio gestirà la richiesta di riconoscimento vocale. Sfortunatamente, per i contenitori locali è necessario specificare l'URI completo (poiché include il computer locale), quindi queste informazioni sono trapelate dall'astrazione. Stiamo lavorando con il team di SDK per renderlo più utilizzabile in futuro.

<br>
</details>

<details>
<summary>
<b>In che modo è possibile effettuare il benchmarking di una misura approssimativa di transazioni/secondo/core?</b>
</summary>

**Risposta:** Di seguito sono riportati alcuni dei numeri approssimativi da prevedere rispetto al modello esistente (cambierà per il migliore in quello che verrà fornito in GA):

- Per i file, la limitazione sarà nell'SDK di riconoscimento vocale, in corrispondenza di 2x. I primi cinque secondi di audio non sono limitati. Il decodificatore è in grado di eseguire circa 3x tempo reale. A tale fine, l'utilizzo complessivo della CPU sarà prossimo a 2 core per un singolo riconoscimento.
- Per MIC, sarà in tempo reale. L'utilizzo complessivo deve essere di circa 1 core per un singolo riconoscimento.

Questa operazione può essere verificata dai log di Docker. Viene effettivamente eseguito il dump della riga con le statistiche di sessione e frase/espressione e che include i numeri RTF.


<br>
</details>

<details>
<summary>
<b>È comune dividere i file audio in mandrini per l'uso del contenitore vocale?</b>
</summary>

Il piano corrente consiste nel prendere un file audio esistente e suddividerlo in blocchi di 10 secondi e inviarli tramite il contenitore. Si tratta di uno scenario accettabile?  Esiste un modo migliore per elaborare file audio di dimensioni maggiori con il contenitore?

**Risposta:** Basta usare l'SDK di riconoscimento vocale e assegnargli il file. Perché è necessario suddividere in blocchi il file?


<br>
</details>

<details>
<summary>
<b>Ricerca per categorie rendere più contenitori in esecuzione nello stesso host?</b>
</summary>

Il documento dice di esporre una porta diversa, ma il contenitore LUIS è ancora in ascolto sulla porta 5000?

**Risposta:** Provare `-p <outside_unique_port>:5000` . Ad esempio: `-p 5001:5000`.


<br>
</details>

## <a name="technical-questions"></a>Domande tecniche

<details>
<summary>
<b>Come è possibile ottenere API non batch per la gestione di &lt; 15 secondi di audio?</b>
</summary>

**Risposta:** `RecognizeOnce()` in modalità interattiva elabora solo fino a 15 secondi di audio, perché la modalità è destinata a comandi vocali in cui si prevede che le espressioni siano brevi. Se si usa `StartContinuousRecognition()` per la dettatura o la conversazione, non esiste un limite di 15 secondi.


<br>
</details>

<details>
<summary>
<b>Quali sono le risorse consigliate, la CPU e la RAM; per 50 richieste simultanee?</b>
</summary>

Quante richieste simultanee sono gestite da 4 core, 4 GB di RAM? Se è necessario gestire, ad esempio, 50 richieste simultanee, quante core e RAM sono consigliate?

**Risposta:** In tempo reale, 8 con la versione più recente `en-US` , è consigliabile usare più contenitori Docker oltre 6 richieste simultanee. Si ottiene un aspetto più folle oltre 16 core e diventa sensibile al nodo NUMA (non-Uniform Memory Access). La tabella seguente descrive l'allocazione minima e consigliata delle risorse per ogni contenitore vocale.

# <a name="speech-to-text"></a>[Riconoscimento vocale](#tab/stt)

| Contenitore      | Minima             | Implementazione consigliata         |
|----------------|---------------------|---------------------|
| Riconoscimento vocale | 2 Core, 2 GB di memoria | 4 core, 4 GB di memoria |

# <a name="custom-speech-to-text"></a>[Da Riconoscimento vocale personalizzato a testo](#tab/cstt)

| Contenitore             | Minima             | Implementazione consigliata         |
|-----------------------|---------------------|---------------------|
| Da Riconoscimento vocale personalizzato a testo | 2 Core, 2 GB di memoria | 4 core, 4 GB di memoria |

# <a name="text-to-speech"></a>[Sintesi vocale](#tab/tts)

| Contenitore      | Minima             | Implementazione consigliata         |
|----------------|---------------------|---------------------|
| Sintesi vocale | 1 core, 2 GB di memoria | 2 Core, 3 GB di memoria |

# <a name="custom-text-to-speech"></a>[Sintesi vocale personalizzata](#tab/ctts)

| Contenitore             | Minima             | Implementazione consigliata         |
|-----------------------|---------------------|---------------------|
| Sintesi vocale personalizzata | 1 core, 2 GB di memoria | 2 Core, 3 GB di memoria |

**_

- Ogni core deve essere almeno 2,6 GHz o superiore.
- Per i file, la limitazione sarà nell'SDK di riconoscimento vocale, a 2x (i primi 5 secondi di audio non sono limitati).
- Il decodificatore è in grado di eseguire circa 2-3x in tempo reale. A tale fine, l'utilizzo complessivo della CPU sarà prossimo a due core per un singolo riconoscimento. Per questo motivo non è consigliabile mantenere più di due connessioni attive, per ogni istanza di contenitore. Il lato estremo è quello di inserire circa 10 decodificatori in tempo reale 2x in un computer con otto core `DS13_V2` , ad esempio. Per il contenitore versione 1,3 e successive, è possibile provare a impostare un parametro `DECODER_MAX_COUNT=20` .
- Per il microfono, sarà in tempo reale. L'utilizzo complessivo deve essere di circa un core per un singolo riconoscimento.

Prendere in considerazione il numero totale di ore di audio disponibili. Se il numero è elevato, per migliorare l'affidabilità e la disponibilità, è consigliabile eseguire più istanze di contenitori, in una singola casella o in più caselle, dietro un servizio di bilanciamento del carico. L'orchestrazione può essere eseguita usando Kubernetes (K8S) e Helm oppure con Docker compose.

Ad esempio, per gestire 1000 ore/24 ore, è stata tentata la configurazione di macchine virtuali 3-4 con 10 istanze/decodificatori per macchina virtuale.

<br>
</details>

<details>
<summary>
<b>Il contenitore vocale supporta la punteggiatura?</b>
</summary>

_ *Risposta:** è disponibile la capitalizzazione (ITN) nel contenitore locale. La punteggiatura dipende dal linguaggio e non è supportata per alcune lingue, tra cui il cinese e il giapponese.

Sono *disponibili* supporto per la punteggiatura implicita e di base per i contenitori esistenti, ma è per `off` impostazione predefinita. Ciò significa che è possibile ottenere il `.` carattere nell'esempio, ma non il `。` carattere. Per abilitare questa logica implicita, di seguito è riportato un esempio di come eseguire questa operazione in Python usando l'SDK di riconoscimento vocale (sarebbe simile in altre lingue):

```python
speech_config.set_service_property(
    name='punctuation',
    value='implicit',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

<br>
</details>

<details>
<summary>
<b>Perché si verificano 404 errori durante il tentativo di pubblicare dati in un contenitore di riconoscimento vocale?</b>
</summary>

Di seguito è riportato un esempio HTTP POST:

```http
POST /speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Transfer-Encoding: chunked
User-Agent: PostmanRuntime/7.18.0
Cache-Control: no-cache
Postman-Token: xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Host: 10.0.75.2:5000
Accept-Encoding: gzip, deflate
Content-Length: 360044
Connection: keep-alive
HTTP/1.1 404 Not Found
Date: Tue, 22 Oct 2019 15:42:56 GMT
Server: Kestrel
Content-Length: 0
```

**Risposta:** L'API REST non è supportata in un contenitore di sintesi vocale, ma supporta solo i WebSocket tramite l'SDK di riconoscimento vocale. Fare sempre riferimento alla documentazione ufficiale, vedere [query PREDICTION Endpoints](speech-container-howto.md#query-the-containers-prediction-endpoint).

<br>
</details>


<details>
<summary>
<b> Perché il contenitore è in esecuzione come utente non root? Quali problemi possono verificarsi per questo?</b>
</summary>

**Risposta:** Si noti che l'utente predefinito all'interno del contenitore è un utente non root. In questo modo viene fornita la protezione contro i processi che sfuggono al contenitore e si ottengono autorizzazioni con escalation nel nodo host. Per impostazione predefinita, alcune piattaforme come la piattaforma OpenShift container eseguono già questa operazione eseguendo i contenitori usando un ID utente assegnato in modo arbitrario. Per queste piattaforme, l'utente non root dovrà disporre delle autorizzazioni per scrivere in qualsiasi volume con mapping esterno che richiede Scritture. Ad esempio una cartella di registrazione o una cartella di download del modello personalizzata.
<br>
</details>

<details>
<summary>
<b>Quando si usa il servizio riconoscimento vocale, perché viene ricevuto questo errore?</b>
</summary>

```cmd
Error in STT call for file 9136835610040002161_413008000252496:
{
    "reason": "ResultReason.Canceled",
    "error_details": "Due to service inactivity the client buffer size exceeded. Resetting the buffer. SessionId: xxxxx..."
}
```

**Risposta:** Questa situazione si verifica in genere quando si feed l'audio più velocemente di quanto il contenitore di riconoscimento vocale possa accettarlo. I buffer client si riempiono e viene attivato l'annullamento. È necessario controllare la concorrenza e il formato RTF in cui si invia l'audio.

<br>
</details>

<details>
<summary>
<b>È possibile spiegare questi errori del contenitore da sintesi vocale dagli esempi di C++?</b>
</summary>

**Risposta:** Se la versione del contenitore è precedente a 1,3, è necessario usare questo codice:

```cpp
const auto endpoint = "http://localhost:5000/speech/synthesize/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

I contenitori meno recenti non hanno l'endpoint necessario per il funzionamento del carbonio con l' `FromHost` API. Se i contenitori usati per la versione 1,3, è necessario usare questo codice:

```cpp
const auto host = "http://localhost:5000";
auto config = SpeechConfig::FromHost(host);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

Di seguito è riportato un esempio di utilizzo dell' `FromEndpoint` API:

```cpp
const auto endpoint = "http://localhost:5000/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text2}}}").get();
```

 La `SetSpeechSynthesisVoiceName` funzione viene chiamata perché i contenitori con un motore di sintesi vocale aggiornato richiedono il nome della voce.

<br>
</details>

<details>
<summary>
<b>Come è possibile usare v 1.7 dell'SDK vocale con un contenitore di riconoscimento vocale?</b>
</summary>

**Risposta:** Esistono tre endpoint sul contenitore di riconoscimento vocale per diversi utilizzi, che vengono definiti come modalità di riconoscimento vocale, vedere di seguito:

[!INCLUDE [speech-modes](includes/speech-modes.md)]

Sono destinati a scopi diversi e vengono usati in modo diverso.

[Esempi](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py)di Python:
- Per il riconoscimento singolo (modalità interattiva) con un endpoint personalizzato `SpeechConfig` , ovvero con un parametro endpoint, vedere `speech_recognize_once_from_file_with_custom_endpoint_parameters()` .
- Per il riconoscimento continuo (modalità conversazione) ed è sufficiente modificare per usare un endpoint personalizzato come descritto in precedenza, vedere `speech_recognize_continuous_from_file()` .
- Per abilitare la dettatura in campioni come sopra (solo se effettivamente necessario), subito dopo la creazione `speech_config` aggiungere il codice `speech_config.enable_dictation()` .

Per abilitare la dettatura in C#, richiamare la `SpeechConfig.EnableDictation()` funzione.

### <a name="fromendpoint-apis"></a>`FromEndpoint` API
| Linguaggio | Dettagli dell'API |
|----------|:------------|
| C++ | <a href="https://docs.microsoft.com/en-us/cpp/cognitive-services/speech/speechconfig#fromendpoint" target="_blank">`SpeechConfig::FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-dotnet" target="_blank">`SpeechConfig.FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromendpoint" target="_blank">`SpeechConfig.fromendpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/en-us/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithendpoint" target="_blank">`SPXSpeechConfiguration:initWithEndpoint;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | Attualmente non supportata, né pianificata. |

<br>
</details>

<details>
<summary>
<b>Come è possibile usare la versione 1.8 dell'SDK vocale con un contenitore di riconoscimento vocale?</b>
</summary>

**Risposta:** È disponibile una nuova `FromHost` API. Questa operazione non sostituisce o modifica le API esistenti. Viene semplicemente aggiunto un metodo alternativo per creare una configurazione vocale usando un host personalizzato.

### <a name="fromhost-apis"></a>`FromHost` API

| Linguaggio | Dettagli dell'API |
|--|:-|
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet" target="_blank">`SpeechConfig.FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C++ | <a href="https://docs.microsoft.com/en-us/cpp/cognitive-services/speech/speechconfig#fromhost" target="_blank">`SpeechConfig::FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromhost" target="_blank">`SpeechConfig.fromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/en-us/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithhost" target="_blank">`SPXSpeechConfiguration:initWithHost;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | Attualmente non supportato |

> Parameters: host (obbligatorio), chiave di sottoscrizione (facoltativo, se è possibile utilizzare il servizio senza di esso).

Il formato per l'host è `protocol://hostname:port` dove `:port` è facoltativo (vedere di seguito):
- Se il contenitore è in esecuzione localmente, il nome host è `localhost` .
- Se il contenitore è in esecuzione in un server remoto, utilizzare il nome host o l'indirizzo IPv4 del server.

Esempi di parametri host per la sintesi vocale:
- `ws://localhost:5000` -connessione non sicura a un contenitore locale con la porta 5000
- `ws://some.host.com:5000` -connessione non sicura a un contenitore in esecuzione in un server remoto

Esempi di Python precedenti, ma usare il `host` parametro invece di `endpoint` :

```python
speech_config = speechsdk.SpeechConfig(host="ws://localhost:5000")
```

<br>
</details>

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Contenitori di servizi cognitivi](speech-container-howto.md)