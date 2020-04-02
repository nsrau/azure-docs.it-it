---
title: Domande frequenti sui contenitori del servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Installare ed eseguire contenitori vocali. la sintesi vocale trascrive i flussi audio in un testo in tempo reale che le applicazioni, gli strumenti o i dispositivi possono utilizzare o visualizzare. Sintesi vocale converte il testo di input in una voce sintetizzata simile a quella di un essere umano.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: dapine
ms.openlocfilehash: 2afb7c84c4d737703917d12a43aa7c3bb799836b
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80537021"
---
# <a name="speech-service-containers-frequently-asked-questions-faq"></a>Domande frequenti sui contenitori del servizio di riconoscimento vocale

Quando si usa il servizio di riconoscimento vocale con i contenitori, fare affidamento su questa raccolta di domande frequenti prima di eseguire l'escalation al supporto. Questo articolo cattura le domande in varia misura, da generale a tecnico. Per espandere una risposta, fare clic sulla domanda.

## <a name="general-questions"></a>Domande generali

<details>
<summary>
<b>Come funzionano i contenitori di riconoscimento vocale e come è possibile configurarli?</b>
</summary>

**Risposta:** Quando si configura il cluster di produzione, è necessario considerare diversi aspetti. In primo luogo, la configurazione di una singola lingua, più contenitori, sulla stessa macchina, non dovrebbe essere un problema di grandi dimensioni. Se si verificano problemi, potrebbe trattarsi di un problema relativo all'hardware, quindi esamineremo prima le risorse, ovvero; Specifiche della CPU e della memoria.

Considerare per un `ja-JP` momento, il contenitore e l'ultimo modello. Il modello acustico è il pezzo più esigente CPU-saggio, mentre il modello linguistico richiede la maggior quantità di memoria. Quando abbiamo benchmarkato l'uso, ci vogliono circa 0,6 core cpu per elaborare una singola richiesta di sintesi vocale quando l'audio scorre in tempo reale (come dal microfono). Se si alimenta l'audio più velocemente rispetto al tempo reale (come da un file), tale utilizzo può raddoppiare (1,2 x core). Nel frattempo, la memoria elencata di seguito è la memoria operativa per la decodifica vocale. *Non* prende in considerazione l'effettiva dimensione completa del modello linguistico, che risiederà nella cache dei file. Per `ja-JP` questo è un ulteriore 2 GB; per `en-US`, potrebbe essere più (6-7 GB).

Se si dispone di un computer in cui la memoria è scarsa e si sta tentando di distribuire più lingue su di esso, è possibile che la cache dei file sia piena e che il sistema operativo sia costretto a eseguire modelli di pagina in ingresso e in uscita. Per una trascrizione in esecuzione, che potrebbe essere disastroso, e può portare a rallentamenti e altre implicazioni di prestazioni.

Inoltre, preconfezionatiamo i file eseguibili per le macchine con il set di istruzioni [di estensione vettoriale avanzata (AVX2).](speech-container-howto.md#advanced-vector-extension-support) Una macchina con il set di istruzioni AVX512 richiederà la generazione di codice per tale destinazione e l'avvio di 10 contenitori per 10 lingue potrebbe esaurire temporaneamente la CPU. Un messaggio come questo apparirà nei registri docker:

```console
2020-01-16 16:46:54.981118943 
[W:onnxruntime:Default, tvm_utils.cc:276 LoadTVMPackedFuncFromCache]
Cannot find Scan4_llvm__mcpu_skylake_avx512 in cache, using JIT...
```

Infine, è possibile impostare il numero di decodificatori che si desidera all'interno di un *singolo* contenitore utilizzando `DECODER MAX_COUNT` la variabile. Quindi, fondamentalmente, dovremmo iniziare con il tuo SKU (CPU / memoria), e possiamo suggerire come ottenere il meglio da esso. Un ottimo punto di partenza si riferisce alle specifiche consigliate per le risorse del computer host.

<br>
</details>

<details>
<summary>
<b>È possibile aiutare con la pianificazione della capacità e la stima dei costi dei contenitori di riconoscimento vocale in un server di gestione dell'onm?</b>
</summary>

**Risposta:** Per la capacità del contenitore in modalità di elaborazione batch, ogni decodificatore è in grado di gestire 2-3 volte in tempo reale, con due core CPU, per un singolo riconoscimento. Non è consigliabile mantenere più di due riconoscimenti simultanei per istanza del contenitore, ma è consigliabile eseguire più istanze di contenitori per motivi di affidabilità/disponibilità, dietro un servizio di bilanciamento del carico.

Anche se è possibile avere ogni istanza del contenitore in esecuzione con più decodificatori. Ad esempio, potremmo essere in grado di impostare 7 decodificatori per istanza contenitore su una macchina a otto core (a più di 2 volte ciascuno), producendo una velocità effettiva di 15 volte. C'è `DECODER_MAX_COUNT` un parametro di cui essere a conoscenza. Per il caso estremo, sorgono problemi di affidabilità e latenza, con una maggiore velocità effettiva aumentata in modo significativo. Per un microfono, sarà a 1x tempo reale. L'utilizzo complessivo deve essere a circa un core per un singolo riconoscimento.

Per lo scenario di elaborazione di 1 K ore al giorno in modalità di elaborazione batch, in un caso estremo, 3 macchine virtuali potrebbero gestirlo entro 24 ore ma non garantito. Per gestire i giorni di picco, il failover, l'aggiornamento e fornire il backup minimo/BCP, è consigliabile 4-5 computer invece di 3 per cluster e con più cluster.

Per l'hardware, utilizziamo la macchina virtuale `DS13_v2` standard di Azure come riferimento (ogni core deve essere pari o superiore a 2,6 GHz, con il set di istruzioni AVX2 abilitato).

| Istanza  | vCPU | RAM    | Archiviazione temporanea | Pagamento in base al tuo asta con AHB | Riserva di 1 anno con AHB (% di risparmio) | Riservato a 3 anni con AHB (% di risparmio) |
|-----------|---------|--------|--------------|------------------------|-------------------------------------|--------------------------------------|
| `DS13 v2` | 8       | 56 GiB | 112 GiB      | 0,598 USD all'ora            | 0,3528 USD/ora (41 USD)                 | 0,2333 USD/ora (61%)                  |

In base al riferimento di progettazione (due cluster di 5 macchine virtuali per gestire l'elaborazione batch audio di 1 K ore al giorno), il costo hardware di 1 anno sarà:

> 2 (cluster) - 5 (VM per cluster) - 0,3528 USD/ora, 365 (giorni) , 24 (ore) , 31K USD /anno

Quando si esegue il mapping alla macchina fisica, una stima generale è 1 vCPU e 1 Core CPU fisica. In realtà, 1vCPU è più potente di un singolo core.

Per il pre-prem, entrano in gioco tutti questi fattori aggiuntivi:

- Su che tipo è la CPU fisica e quanti core su di essa
- Quante CPU eseguite insieme sulla stessa scatola/macchina
- Modalità di configurazione delle macchine virtualiHow VMs are set up
- Come viene utilizzata l'hyperthreading / multithreading
- Come viene condivisa la memoria
- Il sistema operativo, ecc.

Normalmente non è così sintonizzato come Azure l'ambiente. Considerando altri sovraccarichi, direi che una stima sicura è 10 core della CPU fisica - 8 Azure vCPU. Anche se le CPU popolari hanno solo otto core. Con la distribuzione pun-a-prem, il costo sarà maggiore rispetto all'uso di macchine virtuali di Azure.With on-prem deployment, the cost will be higher than using Azure VMs. Considerare inoltre il tasso di ammortamento.

Il costo del servizio è lo stesso del servizio online: 1 USD/ora per la sintesi vocale. Il costo del servizio di riconoscimento vocale è:The Speech service cost is:

> 1 USD , 1000 , 365 , 365 K

Il costo di manutenzione pagato a Microsoft dipende dal livello di servizio e dal contenuto del servizio. E 'varia da 29,99 dollari al mese per il livello di base a centinaia di migliaia se il servizio in loco coinvolti. Un numero approssimativo è di 300 dollari all'ora per il servizio / manutenzione. Il costo delle persone non è incluso. Altri costi dell'infrastruttura (ad esempio archiviazione, reti e servizi di bilanciamento del carico) non sono inclusi.

<br>
</details>

<details>
<summary>
<b>Perché manca la punteggiatura dalla trascrizione?</b>
</summary>

**Risposta:** L'oggetto `speech_recognition_language=<YOUR_LANGUAGE>` deve essere configurato in modo esplicito nella richiesta se si utilizza il client Carbon.

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
<b>È possibile usare un modello acustico personalizzato e un modello linguistico con il contenitore Speech?</b>
</summary>

Attualmente siamo in grado di passare solo un ID modello, sia modello di linguaggio personalizzato o modello acustico personalizzato.

**Risposta:** È stata presa contemporaneamente la decisione di *non* supportare contemporaneamente sia i modelli acustici che quelli linguistici. Questo rimarrà attivo, fino a quando non viene creato un identificatore unificato per ridurre le interruzioni DELL'API. Quindi, purtroppo questo non è supportato in questo momento.

<br>
</details>

<details>
<summary>
<b>È possibile spiegare questi errori dal contenitore di sintesi vocale personalizzato?</b>
</summary>

**Errore 1:**

```cmd
Failed to fetch manifest: Status: 400 Bad Request Body:
{
    "code": "InvalidModel",
    "message": "The specified model is not supported for endpoint manifests."
}
```

**Risposta 1:** Se stai lavorando con il modello personalizzato più recente, al momento non lo supportiamo. Se ti alleni con una versione precedente, dovrebbe essere possibile usare. Stiamo ancora lavorando per supportare le ultime versioni.

Essenzialmente, i contenitori personalizzati non supportano i modelli acustici basati su Halide o ONNX (impostazione predefinita nel portale di training personalizzato). Ciò è dovuto a modelli personalizzati non crittografati e non vogliamo esporre modelli ONNX, tuttavia; i modelli linguistici vanno bene. Il cliente dovrà selezionare in modo esplicito un modello non ONNX precedente per la formazione personalizzata. L'accuratezza non verrà influenzata. Le dimensioni del modello possono essere maggiori (di 100 MB).

> Modello di supporto > 20190220 (v4.5 Unified)

**Errore 2:**

```cmd
HTTPAPI result code = HTTPAPI_OK.
HTTP status code = 400.
Reason:  Synthesis failed.
StatusCode: InvalidArgument,
Details: Voice does not match.
```

**Risposta 2:** È necessario fornire il nome vocale corretto nella richiesta, che fa distinzione tra maiuscole e minuscole. Fare riferimento al mapping del nome completo del servizio. È necessario `en-US-JessaRUS`utilizzare `en-US-JessaNeural` , come non è al momento disponibile nella versione contenitore della sintesi vocale.

**Errore 3:**

```json
{
    "code": "InvalidProductId",
    "message": "The subscription SKU \"CognitiveServices.S0\" is not supported in this service instance."
}
```

**Risposta 3:** È stato reed per creare una risorsa di riconoscimento vocale, non una risorsa di servizi cognitivi.


<br>
</details>

<details>
<summary>
<b>Quali protocolli API sono supportati, REST o WS?</b>
</summary>

**Risposta:** Per i contenitori di sintesi vocale e di sintesi vocale personalizzati, attualmente è supportato solo il protocollo basato su websocket. L'SDK supporta solo chiamate in WS ma non REST. C'è un piano per aggiungere il supporto REST, ma non ETA per il momento. Fare sempre riferimento alla documentazione ufficiale, vedere Endpoint di [previsione delle query](speech-container-howto.md#query-the-containers-prediction-endpoint).

<br>
</details>

<details>
<summary>
<b>CentOS è supportato per i contenitori Speech?</b>
</summary>

**Risposta:** CentOS 7 non è ancora supportato da Python SDK, anche Ubuntu 19.04 non è supportato.

Il pacchetto Speech SDK per Python è disponibile per i sistemi operativi seguenti:
- **Windows** - x64 e x86
- **Mac** - macOS X versione 10.12 o successiva
- **Linux** - Ubuntu 16.04, Ubuntu 18.04, Debian 9 su x64

Per ulteriori informazioni sulla configurazione dell'ambiente, consultate [Configurazione della piattaforma Python](quickstarts/setup-platform.md?pivots=programming-language-python). Per ora, Ubuntu 18.04 è la versione consigliata.

<br>
</details>

<details>
<summary>
<b>Perché si verificano errori quando si tenta di chiamare gli endpoint di stima LUIS?</b>
</summary>

Sto usando il contenitore LUIS in una distribuzione di IoT Edge e sto tentando di chiamare l'endpoint di stima LUIS da un altro contenitore. Il contenitore LUIS è in ascolto sulla porta 5001 e l'URL che sto usando è il seguente:

```csharp
var luisEndpoint =
    $"ws://192.168.1.91:5001/luis/prediction/v3.0/apps/{luisAppId}/slots/production/predict";
var config = SpeechConfig.FromEndpoint(new Uri(luisEndpoint));
```

L'errore che ricevo è:

```cmd
WebSocket Upgrade failed with HTTP status code: 404 SessionId: 3cfe2509ef4e49919e594abf639ccfeb
```

Viene visualizzata la richiesta nei log del contenitore LUIS e il messaggio indica che viene visualizzato il messaggio:

```cmd
The request path /luis//predict" does not match a supported file type.
```

Che cosa significa? Cosa mi sto perdendo? Stavo seguendo l'esempio per il Speech SDK, da [qui](https://github.com/Azure-Samples/cognitive-services-speech-sdk). Lo scenario è che stiamo rilevando l'audio direttamente dal microfono del PC e cercando di determinare l'intento, in base all'app LUIS di cui abbiamo eseguito il training. L'esempio a cui ho collegato fa esattamente questo. E funziona bene con il servizio basato su cloud LUIS. L'utilizzo di Speech SDK sembrava evitare di dover effettuare una chiamata esplicita separata all'API speech-to-text e quindi una seconda chiamata a LUIS.

Quindi, tutto quello che sto cercando di fare è passare dallo scenario di utilizzo di LUIS nel cloud per l'utilizzo del contenitore LUIS. Non riesco a immaginare se l'SDK di riconoscimento vocale funziona per uno, non funzionerà per l'altro.

**Risposta:** Speech SDK non deve essere utilizzato su un contenitore LUIS. Per l'utilizzo del contenitore LUIS, è necessario utilizzare l'SDK LUIS o l'API REST LUIS. Speech SDK deve essere usato contro un contenitore vocale.

Un cloud è diverso da un contenitore. Un cloud può essere composto da più contenitori aggregati (talvolta denominati microservizi). Quindi c'è un contenitore LUIS e poi c'è un contenitore di riconoscimento vocale - due contenitori separati. Il contenitore Riconoscimento vocale esegue solo la sintesi vocale. Il contenitore LUIS esegue solo LUIS. Nel cloud, poiché entrambi i contenitori sono noti per essere distribuiti ed è una cattiva prestazione per un client remoto per andare al cloud, fare voce, tornare indietro, poi andare al cloud di nuovo e fare LUIS, forniamo una funzionalità che consente al client di andare a discorso, rimanere nel cloud, andare a LUIS quindi tornare al client. Così anche in questo scenario l'SDK di riconoscimento vocale va a Speech contenitore cloud con audio e quindi Speech contenitore cloud comunica al contenitore cloud LUIS con testo. Il contenitore LUIS non ha alcun concetto di accettare l'audio (non sarebbe opportuno per il contenitore LUIS accettare l'audio in streaming - LUIS è un servizio basato su testo). Con on-prem, non abbiamo alcuna certezza che il nostro cliente ha distribuito entrambi i contenitori, non presumiamo di orchestrare tra i contenitori nei locali dei nostri clienti, e se entrambi i contenitori vengono distribuiti in locale, dato che sono più locali per il cliente, non è un onere andare la SR prima, tornare al cliente, e avere il cliente poi prendere quel testo e andare a LUIS.

<br>
</details>

<details>
<summary>
<b>Perché stiamo ricevendo errori con macOS, il contenitore Speech e Python SDK?</b>
</summary>

Quando inviamo un file *.wav* da trascrivere, il risultato torna con:

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

Sappiamo che la websocket è configurata correttamente.

**Risposta:** In tal caso, vedere [questo problema di GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310). Abbiamo una soluzione, [proposta qui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310#issuecomment-527542722).

Carbon ha risolto questo problema alla versione 1.8.


<br>
</details>

<details>
<summary>
<b>Quali sono le differenze negli endpoint del contenitore di riconoscimento vocale?</b>
</summary>

È possibile contribuire a compilare le metriche di test seguenti, incluse le funzioni da testare e come testare l'SDK e le API REST? Soprattutto, le differenze in "interattivo" e "conversazione", che non ho visto da doc/sample esistente.

| Endpoint                                                | Test funzionale                                                   | SDK | API REST |
|---------------------------------------------------------|-------------------------------------------------------------------|-----|----------|
| `/speech/synthesize/cognitiveservices/v1`               | Sintesi del testo (sintesi vocale)                                  |     | Sì      |
| `/speech/recognition/dictation/cognitiveservices/v1`    | Endpoint websocket di dettatura prem cognitiva        | Sì | No       |
| `/speech/recognition/interactive/cognitiveservices/v1`  | Endpoint websocket interattivo v1 interattivo di Servizi cognitivi  |     |          |
| `/speech/recognition/conversation/cognitiveservices/v1` | Endpoint websocket della conversazione sui servizi cognitivi on-prem conversation v1 |     |          |

**Risposta:** Questa è una fusione di:
- Persone che provano l'endpoint di dettatura per i contenitori, (non sono sicuro di come hanno ottenuto l'URL)
- L'endpoint di 1<sup>st</sup> party è quello in un contenitore.
- Endpoint di 1<sup>st</sup> party che restituisce `speech.hypothesis` messaggi speech.fragment anziché i messaggi restituiti dagli endpoint della<sup>terza</sup> parte per l'endpoint di dettatura.
- Il Carbon quickstarts `RecognizeOnce` tutti gli usi (modalità interattiva)
- Carbonio con un'asserzione che per `speech.fragment` i messaggi che richiedono che non vengono restituiti in modalità interattiva.
- Carbonio con le asserzioni fuoco in build di rilascio (uccidere il processo).

La soluzione alternativa consiste nell'usare il riconoscimento continuo nel codice o (più veloce) connettersi agli endpoint interattivi o continui nel contenitore.
Per il codice, impostare l'endpoint su <host:port>/speech/recognition/interactive/cognitiveservices/v1

Per le varie modalità, vedere Modalità di riconoscimento vocale - vedere di seguito:

[!INCLUDE [speech-modes](includes/speech-modes.md)]

La correzione corretta è in arrivo con SDK 1.8, che ha il supporto punsi dal prem (sceglierà l'endpoint giusto, quindi non saremo peggio del servizio online). Nel frattempo, c'è un campione per il riconoscimento continuo, perché non lo puntiamo?

https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/6805d96bf69d9e95c9137fe129bc5d81e35f6309/samples/python/console/speech_sample.py#L196

<br>
</details>

<details>
<summary>
<b>Quale modalità devo usare per vari file audio?</b>
</summary>

**Risposta:** Ecco una [guida introduttiva con Python](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-python). Puoi trovare le altre lingue collegate nel sito della documentazione.

Solo per chiarire per l'interattivo, conversazione, e dettatura; questo è un modo avanzato per specificare il modo particolare in cui il nostro servizio gestirà la richiesta vocale. Sfortunatamente, per i contenitori locali è necessario specificare l'URI completo (poiché include il computer locale), quindi queste informazioni sono trapelate dall'astrazione. Stiamo lavorando con il team SDK per rendere questo più utilizzabile in futuro.

<br>
</details>

<details>
<summary>
<b>Come possiamo valutare una misura approssimativa delle transazioni/secondo/core?</b>
</summary>

**Risposta:** Ecco alcuni dei numeri approssimativi che ci si aspettano dal modello esistente (cambierà in meglio in quello che spediremo in GA):

- Per i file, la limitazione delle richieste sarà nell'SDK vocale, in 2x. I primi cinque secondi di audio non sono limitati. Decoder è in grado di fare circa 3 volte in tempo reale. Per questo, l'utilizzo complessivo della CPU sarà vicino a 2 core per un singolo riconoscimento.
- Per il microfono, sarà a 1x tempo reale. L'utilizzo complessivo deve essere di circa 1 core per un singolo riconoscimento.

Tutto questo può essere verificato dai registri docker. In realtà abbiamo scaricato la linea con le statistiche di sessione e frase / espressione, e che include i numeri RTF.


<br>
</details>

<details>
<summary>
<b>È comune dividere i file audio in mandrini per l'utilizzo del contenitore Di scorso?</b>
</summary>

Il mio piano attuale è quello di prendere un file audio esistente e dividerlo in pezzi di 10 secondi e inviarli attraverso il contenitore. È uno scenario accettabile?  C'è un modo migliore per elaborare file audio più grandi con il contenitore?

**Risposta:** Basta usare l'SDK vocale e dargli il file, farà la cosa giusta. Perché è necessario suddividere il file in blocco?


<br>
</details>

<details>
<summary>
<b>Come si eseguono più contenitori nello stesso host?</b>
</summary>

Il documento dice di esporre una porta diversa, che faccio, ma il contenitore LUIS è ancora in ascolto sulla porta 5000?

**Risposta:** Provare `-p <outside_unique_port>:5000`. Ad esempio: `-p 5001:5000`.


<br>
</details>

## <a name="technical-questions"></a>Domande tecniche

<details>
<summary>
<b>Come è possibile ottenere API non batch &lt;per gestire l'audio lungo 15 secondi?</b>
</summary>

**Risposta:** Questa modalità è in modalità interattiva. Se si utilizza dettatura o conversazione che non è un problema.


<br>
</details>

<details>
<summary>
<b>Quali sono le risorse consigliate, CPU e RAM; per 50 richieste simultanee?</b>
</summary>

Quante richieste simultanee saranno un 4 core, 4 GB di RAM gestire? Se dobbiamo servire, ad esempio, 50 richieste simultanee, quanti Core e RAM sono consigliati?

**Risposta:** In tempo reale, 8 `en-US`con la nostra ultima , quindi si consiglia di utilizzare più contenitori docker oltre 6 richieste simultanee. Diventa più pazzo oltre 16 core, e diventa non uniforme accesso alla memoria (NUMA) nodo sensibile. Nella tabella seguente viene descritta l'allocazione minima e consigliata delle risorse per ogni contenitore di riconoscimento vocale.

# <a name="speech-to-text"></a>[Riconoscimento vocale](#tab/stt)

| Contenitore      | Minima             | Consigliato         |
|----------------|---------------------|---------------------|
| Riconoscimento vocale | 2 core, 2-GB di memoria | 4 core, 4 GB di memoria |

# <a name="custom-speech-to-text"></a>[Sintesi vocale personalizzata](#tab/cstt)

| Contenitore             | Minima             | Consigliato         |
|-----------------------|---------------------|---------------------|
| Sintesi vocale personalizzata | 2 core, 2-GB di memoria | 4 core, 4 GB di memoria |

# <a name="text-to-speech"></a>[Sintesi vocale](#tab/tts)

| Contenitore      | Minima             | Consigliato         |
|----------------|---------------------|---------------------|
| Sintesi vocale | 1 core, 2 GB di memoria | 2 core, 3 GB di memoria |

# <a name="custom-text-to-speech"></a>[Sintesi vocale personalizzata](#tab/ctts)

| Contenitore             | Minima             | Consigliato         |
|-----------------------|---------------------|---------------------|
| Sintesi vocale personalizzata | 1 core, 2 GB di memoria | 2 core, 3 GB di memoria |

***

- Ogni core deve essere almeno 2,6 GHz o superiore.
- Per i file, la limitazione delle richieste sarà in Speech SDK, a 2x (i primi 5 secondi di audio non sono limitati).
- Il decodificatore è in grado di fare circa 2-3 volte in tempo reale. A tale scopo, l'utilizzo complessivo della CPU sarà vicino a due core per un singolo riconoscimento. Ecco perché non è consigliabile mantenere più di due connessioni attive, per ogni istanza del contenitore. Il lato estremo sarebbe quello di mettere circa 10 decodificatori `DS13_V2`a 2 volte in tempo reale in una macchina a otto core come . Per la versione contenitore 1.3 e successive, è `DECODER_MAX_COUNT=20`disponibile un parametro che è possibile provare a impostare .
- Per il microfono, sarà a 1x tempo reale. L'utilizzo complessivo deve essere a circa un core per un singolo riconoscimento.

Considera il numero totale di ore di audio che hai. Se il numero è elevato, per migliorare l'affidabilità/disponibilità, è consigliabile eseguire più istanze di contenitori, in una singola casella o in più caselle, dietro un servizio di bilanciamento del carico. L'orchestrazione potrebbe essere fatta usando Kubernetes (K8S) e Helm, o con Docker compose.

Ad esempio, per gestire 1000 ore/24 ore, abbiamo provato a configurare 3-4 macchine virtuali, con 10 istanze/decodificatori per macchina virtuale.

<br>
</details>

<details>
<summary>
<b>Il contenitore Speech supporta la punteggiatura?</b>
</summary>

**Risposta:** Abbiamo le maiuscole (ITN) disponibili nel contenitore in loco. La punteggiatura dipende dalla lingua e non è supportata per alcune lingue, tra cui il cinese e il giapponese.

Abbiamo *do* il supporto di punteggiatura implicito e di base `off` per i contenitori esistenti, ma è per impostazione predefinita. Ciò significa che puoi `.` ottenere il personaggio nel `。` tuo esempio, ma non il personaggio. Per abilitare questa logica implicita, ecco un esempio di come farlo in Python usando il nostro Speech SDK (sarebbe simile in altri linguaggi):To enable this implicit logic, here's an example of how to do so in Python using our Speech SDK (it would be similar in other languages):

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
<b>Perché si verificano errori 404 quando si tenta di inviare dati tramite POST al contenitore di sintesi vocale?</b>
</summary>

Di seguito è riportato un esempio di HTTP POST:Here is an example HTTP POST:

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

**Risposta:** Non è supportata l'API REST in nessun contenitore di sintesi vocale, ma solo WebSockets tramite Speech SDK. Fare sempre riferimento alla documentazione ufficiale, vedere Endpoint di [previsione delle query](speech-container-howto.md#query-the-containers-prediction-endpoint).

<br>
</details>

<details>
<summary>
<b>Quando si utilizza il servizio di sintesi vocale, perché viene visualizzato questo errore?</b>
</summary>

```cmd
Error in STT call for file 9136835610040002161_413008000252496:
{
    "reason": "ResultReason.Canceled",
    "error_details": "Due to service inactivity the client buffer size exceeded. Resetting the buffer. SessionId: xxxxx..."
}
```

**Risposta:** Ciò si verifica in genere quando si alimenta l'audio più velocemente di quanto il contenitore di riconoscimento vocale può prendere. I buffer client si riempiono e l'annullamento viene attivato. È necessario controllare la concorrenza e il FORMATO RTF a cui si invia l'audio.

<br>
</details>

<details>
<summary>
<b>È possibile spiegare questi errori del contenitore di sintesi vocale riportati dagli esempi di C?</b>
</summary>

**Risposta:** Se la versione del contenitore è precedente a 1.3, è necessario usare questo codice:If the container version is older than 1.3, then this code should be used:

```cpp
const auto endpoint = "http://localhost:5000/speech/synthesize/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

I contenitori meno recenti non dispongono dell'endpoint necessario per l'utilizzo dell'API da parte di Carbon.Older containers don't have the required endpoint for Carbon to work with the `FromHost` API. Se i contenitori utilizzati per la versione 1.3, è necessario usare questo codice:If the containers used for version 1.3, then this code should be used:

```cpp
const auto host = "http://localhost:5000";
auto config = SpeechConfig::FromHost(host);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

Di seguito è riportato un esempio di utilizzo dell'API: `FromEndpoint`

```cpp
const auto endpoint = "http://localhost:5000/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text2}}}").get();
```

 La `SetSpeechSynthesisVoiceName` funzione viene chiamata perché i contenitori con un motore di sintesi vocale aggiornato richiedono il nome vocale.

<br>
</details>

<details>
<summary>
<b>Come posso usare v1.7 di Speech SDK con un contenitore Speech?</b>
</summary>

**Risposta:** Esistono tre endpoint nel contenitore di riconoscimento vocale per usi diversi, sono definiti come modalità di riconoscimento vocale- vedere di seguito:There are three endpoints on the Speech container for different usages, they're defined as Speech modes - see below:

[!INCLUDE [speech-modes](includes/speech-modes.md)]

Sono per scopi diversi e sono utilizzati in modo diverso.

[Esempi](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py)di Python :
- Per il riconoscimento singolo (modalità interattiva) con un endpoint personalizzato,ovvero; `SpeechConfig` con un parametro `speech_recognize_once_from_file_with_custom_endpoint_parameters()`endpoint), vedere .
- Per il riconoscimento continuo (modalità conversazione) e solo `speech_recognize_continuous_from_file()`modificare per utilizzare un endpoint personalizzato come sopra, vedere .
- Per abilitare la dettatura in esempi come sopra (solo `speech_config`se è `speech_config.enable_dictation()`realmente necessario), subito dopo aver creato , aggiungere codice .

Nel linguaggio c'è per `SpeechConfig.EnableDictation()` abilitare la dettatura, richiamare la funzione.

### <a name="fromendpoint-apis"></a>`FromEndpoint`Api
| Linguaggio | Dettagli dell'API |
|----------|:------------|
| C++ | <a href="https://docs.microsoft.com/en-us/cpp/cognitive-services/speech/speechconfig#fromendpoint" target="_blank">`SpeechConfig::FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-dotnet" target="_blank">`SpeechConfig.FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-java-stable" target="_blank">`SpeechConfig.fromendpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/en-us/objectivec/cognitive-services/speech/spxspeechconfiguration#initWithEndpoint" target="_blank">`SPXSpeechConfiguration:initWithEndpoint;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | Attualmente non è supportato, né è previsto. |

<br>
</details>

<details>
<summary>
<b>Come posso usare v1.8 di Speech SDK con un contenitore Speech?</b>
</summary>

**Risposta:** C'è una `FromHost` nuova API. Ciò non sostituisce o modifica le API esistenti. Aggiunge solo un modo alternativo per creare una configurazione vocale usando un host personalizzato.

### <a name="fromhost-apis"></a>`FromHost`Api

| Linguaggio | Dettagli dell'API |
|--|:-|
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet" target="_blank">`SpeechConfig.FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C++ | <a href="https://docs.microsoft.com/en-us/cpp/cognitive-services/speech/speechconfig#fromhost" target="_blank">`SpeechConfig::FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-java-stable" target="_blank">`SpeechConfig.fromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/en-us/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithhost" target="_blank">`SPXSpeechConfiguration:initWithHost;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | Attualmente non supportato |

> Parametri: host (obbligatorio), chiave di sottoscrizione (facoltativo, se è possibile utilizzare il servizio senza di essa).

Formato per `protocol://hostname:port` host `:port` è dove è facoltativo (vedi sotto):
- Se il contenitore è in `localhost`esecuzione localmente, il nome host è .
- Se il contenitore è in esecuzione su un server remoto, utilizzare il nome host o l'indirizzo IPv4 di tale server.

Esempi di parametri host per la sintesi vocale:
- `ws://localhost:5000`- connessione non protetta a un contenitore locale utilizzando la porta 5000- non-secure connection to a local container using port 5000
- `ws://some.host.com:5000`- connessione non protetta a un contenitore in esecuzione su un server remoto- non-secure connection to a container running on a remote server

Python campioni dall'alto, ma utilizzare `host` parametro invece di: `endpoint`

```python
speech_config = speechsdk.SpeechConfig(host="ws://localhost:5000")
```

<br>
</details>

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Contenitori di Servizi cognitivi](speech-container-howto.md)
