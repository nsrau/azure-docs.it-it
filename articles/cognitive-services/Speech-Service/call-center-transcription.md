---
title: Trascrizione del Call Center-servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Uno scenario comune per la sintesi vocale è la traduzione di grandi volumi di dati di telefonia provenienti da diversi sistemi, ad esempio IVR (Interactive Voice Response). Con il servizio riconoscimento vocale e il modello di riconoscimento vocale unificato, un'azienda può ottenere trascrizioni di alta qualità con i sistemi di acquisizione audio.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 2a8bea01d67c1820dc4f5c0a4922872541449a9e
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74538166"
---
# <a name="speech-service-for-telephony-data"></a>Servizio di riconoscimento vocale per i dati di telefonia

I dati di telefonia generati attraverso telefoni fissi, telefoni cellulari e radio sono in genere di bassa qualità e banda stretta compresa nell'intervallo di frequenza di 8 KHz, che crea problemi durante il riconoscimento vocale. I modelli di riconoscimento vocale più recenti del servizio di riconoscimento vocale sono in fase di trascrizione di questi dati di telefonia, anche nei casi in cui i dati sono difficili da comprendere per un uomo. Questi modelli vengono sottoposti a training con volumi elevati di dati di telefonia e hanno un'accuratezza del riconoscimento del mercato migliore, anche in ambienti rumorosi.

Uno scenario comune per il riconoscimento vocale è la trascrizione di grandi volumi di dati di telefonia che possono provenire da vari sistemi, ad esempio i bot IVR (Interactive Voice Response). L'audio fornito da questi sistemi può essere stereo o mono e raw, con poca o nessuna elaborazione eseguita sul segnale. Usando il servizio riconoscimento vocale e il modello di riconoscimento vocale unificato, un'azienda può ottenere trascrizioni di alta qualità, indipendentemente dal sistema usato per acquisire audio.

È possibile usare i dati di telefonia per meglio comprendere le esigenze dei clienti, identificare nuove opportunità di marketing oppure valutare le prestazioni degli operatori del call center. Una volta che i dati sono stati trascritti, un'azienda può usare l'output per scopi quali la telemetria migliorata, l'identificazione di frasi chiave o l'analisi delle opinioni dei clienti.

Le tecnologie descritte in questa pagina sono usate internamente da Microsoft per vari servizi di elaborazione di chiamate al supporto, sia in tempo reale che in modalità batch.

Si esamineranno alcune delle tecnologie e le funzionalità correlate offerte dal servizio di riconoscimento vocale.

> [!IMPORTANT]
> Il modello unificato del servizio di riconoscimento vocale viene sottoposto a training con dati diversi e offre una soluzione a modello singolo a una serie di scenari dalla dettatura all'analisi della telefonia.

## <a name="azure-technology-for-call-centers"></a>Tecnologia Azure per i call center

Oltre all'aspetto funzionale delle funzionalità del servizio vocale, il loro scopo principale, quando applicato al Call Center, è quello di migliorare l'esperienza del cliente. A questo proposito esistono tre domini:

- Analisi post-chiamata, che è essenzialmente l'elaborazione batch delle registrazioni delle chiamate dopo la chiamata.
- Analisi in tempo reale, che sta elaborando il segnale audio per estrarre varie informazioni quando si verifica la chiamata (in caso di utilizzo rilevante).
- Assistenti vocali (bot), guidando il dialogo tra il cliente e il bot nel tentativo di risolvere il problema del cliente senza alcuna partecipazione dell'agente o l'applicazione di protocolli di intelligenza artificiale per supportare l'agente.

L'immagine sotto ![Architettura delle trascrizioni del call center](media/scenarios/call-center-transcription-architecture.png) mostra un tipico diagramma architettonico dell'implementazione di uno scenario batch

## <a name="speech-analytics-technology-components"></a>Componenti tecnologici dell'analisi vocale

Se il dominio è post-chiamata o in tempo reale, Azure offre un set di tecnologie avanzate ed emergenti per migliorare l'esperienza del cliente.

### <a name="speech-to-text-stt"></a>Riconoscimento vocale

Il [riconoscimento vocale](speech-to-text.md) è la funzionalità più ricercata in qualsiasi soluzione Call Center. Poiché molti dei processi di analisi downstream si basano sul testo trascritto, la frequenza degli errori di parola (_wer_) è di importanza fondamentale. Tra le principali sfide da superare nella trascrizione dei call center sono inclusi il rumore prevalente nella sala (ad esempio: il sottofondo degli altri operatori che parlano), l'ampia varietà di impostazioni locali della lingua e di dialetti, nonché la bassa qualità dello stesso segnale telefonico. WER è strettamente correlato al modo in cui viene eseguito il training dei modelli acustici e di lingua per le impostazioni locali, quindi è importante la possibilità di personalizzare il modello in base alle impostazioni locali. I più recenti modelli unificati di quarta generazione rappresentano la soluzione ideale sia per l'accuratezza che per la latenza della trascrizione. Con training con decine di migliaia di ore di dati acustici e miliardi di informazioni lessicali, i modelli unificati sono i modelli più accurati sul mercato per la trascrizione dei dati del Call Center.

### <a name="sentiment"></a>Valutazione

Valutare se il cliente ha avuto una buona esperienza è una delle aree più importanti dell'analisi vocale, quando applicata allo spazio del call center. L'[API di trascrizione Batch](batch-transcription.md) offre l'analisi del sentiment per ogni espressione. È possibile aggregare il set di valori ottenuti come parte di una trascrizione di chiamata per determinare il sentiment della chiamata sia per gli operatori che per il cliente.

### <a name="silence-non-talk"></a>Il silenzio (nessuna conversazione)

Non è insolito per il 35% del tempo di una chiamata al supporto tecnico. Di seguito sono riportati alcuni scenari per i quali non si verificano i rilasci: agenti che cercano la cronologia dei casi precedenti con un cliente, agenti che usano strumenti che consentono loro di accedere al desktop del cliente ed eseguire funzioni, i clienti che si trovano in attesa di un trasferimento e così via. È estremamente importante valutare quando il silenzio si verifica in una chiamata, perché il numero di importanti sensibilità dei clienti si verifica intorno a questi tipi di scenari e quando si verificano nella chiamata.

### <a name="translation"></a>Traduzione

Alcune aziende sperimentano la fornitura di trascrizioni tradotte da chiamate al supporto per le lingue straniere, in modo che i responsabili della distribuzione possano comprendere l'esperienza globale dei clienti. Le nostre competenze di [traduzione](translation.md) non hanno rivali. Per un numero elevato di impostazioni locali è possibile convertire audio-audio o audio-testo.

### <a name="text-to-speech"></a>Sintesi vocale

La [sintesi vocale](text-to-speech.md) è un'altra area importante nell'implementazione di bot che interagiscono con i clienti. In un percorso tipico, il cliente parla e la sua voce viene trascritta testualmente; viene analizzato l'intento del testo e quindi sintetizzata una risposta in base all'intento riconosciuto. Infine, viene presentato un asset al cliente oppure viene generata una risposta con voce sintetizzata. Naturalmente tutti questi elementi devono essere eseguiti rapidamente, di conseguenza bassa latenza è un componente importante del successo di questi sistemi.

La latenza end-to-end è notevolmente bassa per le varie tecnologie interattive, ad esempio [sintesi vocale](speech-to-text.md), [Luis](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/), [bot Framework](https://dev.botframework.com/), sintesi [vocale](text-to-speech.md).

Le nuove voci sono inoltre indistinguibili da quelle umane. Puoi usare le nostre voci per offrire al tuo bot la sua personalità univoca.

### <a name="search"></a>Cerca

Un altro elemento di base dell'analisi consiste nell'identificare le interazioni in cui si è verificata un'esperienza o un evento specifico. Questa operazione viene in genere eseguita con uno dei due approcci seguenti: una ricerca ad hoc in cui l'utente digita semplicemente una frase e il sistema risponde oppure una query più strutturata in cui un analista può creare un set di istruzioni logiche che identificano uno scenario in una chiamata e quindi ogni chiamata può essere indicizzata in base a tale set di query. Un esempio di ricerca valido è la dichiarazione di conformità universale "questa chiamata verrà registrata per scopi qualitativi... ". Molte aziende desiderano assicurarsi che i loro agenti forniscano questa dichiarazione di non responsabilità ai clienti prima che la chiamata venga effettivamente registrata. La maggior parte dei sistemi di analisi è in grado di eseguire la tendenza dei comportamenti individuati dagli algoritmi di ricerca/query e questa segnalazione delle tendenze è in definitiva una delle funzioni più importanti di un sistema di analisi. Grazie alla [directory Servizi cognitivi](https://azure.microsoft.com/services/cognitive-services/directory/search/), la soluzione end-to-end può essere significativamente migliorata con funzionalità di indicizzazione e ricerca.

### <a name="key-phrase-extraction"></a>Estrazione frasi chiave

Quest'area è una delle applicazioni di analisi più complesse e ne trae vantaggio dall'applicazione di intelligenza artificiale e machine learning. Lo scenario principale in questo caso consiste nel dedurre il cliente intento. Qual è il motivo della chiamata? Qual è il problema del cliente? Perché il cliente ha riscontrato un'esperienza negativa? Il [servizio analisi del testo](https://azure.microsoft.com/services/cognitive-services/text-analytics/) fornisce un set di analisi predefinite per l'aggiornamento rapido della soluzione end-to-end per l'estrazione di parole chiave o frasi importanti.

Verranno ora analizzate più dettagliatamente l'elaborazione batch e le pipeline in tempo reale per il riconoscimento vocale.

## <a name="batch-transcription-of-call-center-data"></a>Trascrizione batch dei dati del call center

Per la trascrizione dell'audio in blocco, è stata sviluppata l' [API di trascrizione batch](batch-transcription.md). L'API di trascrizione batch consente di trascrivere grandi quantità di dati audio in modo asincrono. Per quanto riguarda la trascrizione dei dati del Call Center, la soluzione si basa su questi pilastri:

- **Accuratezza** : grazie ai modelli unificati di quarta generazione, offriamo qualità trascrizione insuperata.
- **Latenza** : quando si eseguono le trascrizioni bulk, le trascrizioni sono necessarie in modo rapido. I processi di trascrizione avviati usando l'[API di trascrizione batch](batch-transcription.md) verranno inseriti immediatamente in coda e, una volta avviato, il processo sarà ancora più veloce della trascrizione in tempo reale.
- **Sicurezza** : si è consapevoli che le chiamate possono contenere dati riservati. Assicuriamo il cliente che la sicurezza è una delle nostre massime priorità. Il nostro servizio ha ottenuto le certificazioni ISO, SOC, HIPAA e PCI.

I Call Center generano grandi volumi di dati audio su base giornaliera. Se l'azienda archivia i dati di telefonia in una posizione centrale, ad esempio Archiviazione di Azure, è possibile usare l'[API di trascrizione batch](batch-transcription.md) per richiedere e ricevere trascrizioni in modo asincrono.

Una tipica soluzione usa questi servizi:

- Il servizio di riconoscimento vocale viene usato per trascrivere il riconoscimento vocale. Per usare l'API di trascrizione batch, è necessaria una sottoscrizione standard (S0) per il servizio di riconoscimento vocale. Le sottoscrizioni gratuite (F0) non funzioneranno.
- Per archiviare i dati di telefonia e le trascrizioni restituite dall'API di trascrizione batch si usa [Archiviazione di Azure](https://azure.microsoft.com/services/storage/). Questo account di archiviazione deve usare le notifiche, in particolare quando vengono aggiunti nuovi file. Queste notifiche vengono usate per attivare il processo di trascrizione.
- [Funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/) consente di creare l'URI di firma di accesso condiviso per ogni registrazione e attivare la richiesta POST HTTP per avviare una trascrizione. Inoltre, Funzioni di Azure consente di creare richieste per recuperare ed eliminare trascrizioni usando l'API di trascrizione batch.

Microsoft usa internamente le suddette tecnologie per supportare le chiamate dei clienti in modalità batch.
![Architettura batch](media/scenarios/call-center-batch-pipeline.png)

## <a name="real-time-transcription-for-call-center-data"></a>Trascrizione in tempo reale dei dati del call center

Alcune aziende sono tenute a trascrivere le conversazioni in tempo reale. La trascrizione in tempo reale può essere usata per identificare parole chiave e attivare ricerche di contenuto e risorse pertinenti alla conversazione, per il monitoraggio del sentiment, per migliorare l'accessibilità o per fornire le traduzioni ai clienti e agli operatori che non sono madrelingua.

Per gli scenari che richiedono la trascrizione in tempo reale, è consigliabile usare [Speech SDK](speech-sdk.md). Il riconoscimento vocale è attualmente disponibile in [oltre 20 lingue](language-support.md), e l'SDK è disponibile in C++, C#, Java, Python, Node.js, Objective-C e JavaScript. Esempi sono disponibili in ogni lingua su [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk). Per le notizie e gli aggiornamenti più recenti, vedere le [Note sulla versione](releasenotes.md).

Internamente si usano le tecnologie precedenti per analizzare in tempo reale le chiamate dei clienti Microsoft, come illustrato nella figura seguente.

![Architettura batch](media/scenarios/call-center-reatime-pipeline.png)

## <a name="a-word-on-ivrs"></a>Introduzione ai sistemi IVR

Il servizio riconoscimento vocale può essere facilmente integrato in qualsiasi soluzione usando l' [SDK di riconoscimento vocale](speech-sdk.md) o l' [API REST](rest-apis.md). Tuttavia, le trascrizioni del call center possono richiedere tecnologie aggiuntive. In genere, è necessaria una connessione tra un sistema IVR e Azure. Sebbene non siano disponibili tali componenti, ecco una descrizione della connessione a un IVR.

Diversi prodotti di servizi IVR o di telefonia (ad esempio Genesys o AudioCodes) offrono funzionalità di integrazione che possono essere sfruttate per consentire il pass-through audio in ingresso e in uscita a un servizio di Azure. In pratica, un servizio di Azure personalizzato potrebbe fornire un'interfaccia specifica per definire le sessioni di chiamata telefonica, ad esempio l'avvio della chiamata o la chiamata finale, ed esporre un'API WebSocket per ricevere l'audio del flusso in ingresso usato con il servizio di riconoscimento vocale. Le risposte in uscita, ad esempio la trascrizione della conversazione o le connessioni con Bot Framework, possono essere sintetizzate con il servizio di sintesi vocale di Microsoft e restituite al sistema IVR per la riproduzione.

Un altro scenario è l'integrazione diretta con SIP (Session Initiation Protocol). Un servizio di Azure si connette a un server SIP, ottenendo così un flusso in ingresso e un flusso in uscita, che viene usato per le fasi per il riconoscimento vocale e sintesi vocale. Per connettersi a un server SIP sono disponibili offerte di software commerciale, ad esempio Ozieki SDK oppure [l'API per le chiamate e le riunioni dei team](/graph/api/resources/communications-api-overview) (attualmente in versione beta), che sono progettate per supportare questo tipo di scenario per le chiamate audio.

## <a name="customize-existing-experiences"></a>Personalizzare le esperienze esistenti

 Il servizio riconoscimento vocale funziona bene con i modelli predefiniti. Tuttavia, potrebbe essere necessario personalizzare ulteriormente e ottimizzare l'esperienza per il prodotto o l'ambiente. Le opzioni di personalizzazione spaziano dall'ottimizzazione del modello acustico ai caratteri voce univoci per il proprio marchio. Dopo aver compilato un modello personalizzato, è possibile usarlo con le funzionalità del servizio riconoscimento vocale in modalità batch o in tempo reale.

| Servizio Voce | Modello | DESCRIZIONE |
| -------------- | ----- | ----------- |
| Riconoscimento vocale | [Modello acustico](how-to-customize-acoustic-models.md) | Creazione di un modello acustico personalizzato per applicazioni, strumenti o dispositivi usati in ambienti particolari, ad esempio in un'automobile o in fabbrica, ognuno con condizioni di registrazione specifiche. Sono esempi di queste situazioni un eloquio con un forte accento, particolari rumori di sottofondo o l'uso di uno speciale microfono per la registrazione. |
|                | [Modello linguistico](how-to-customize-language-model.md) | Creazione di un modello linguistico personalizzato per migliorare la trascrizione di grammatica e terminologia specifica di settore, ad esempio terminologia medica o IT. |
|                | [Modello di pronuncia](how-to-customize-pronunciation.md) | Con un modello di pronuncia personalizzato, è possibile definire il form fonetico e visualizzarlo per una parola o un termine. È utile per gestire i termini personalizzati, come i nomi di prodotto o gli acronimi. Per iniziare, è sufficiente un file di pronuncia, ovvero un semplice file di `.txt`. |
| Sintesi vocale | [Carattere voce](how-to-customize-voice-font.md) | I caratteri voce personalizzati consentono di creare una voce distintiva e unica per il proprio marchio. Per iniziare è necessaria solo una piccola quantità di dati. Più dati si forniscono, più naturale e umano risulterà il carattere voce. |

## <a name="sample-code"></a>Codice di esempio

Il codice di esempio è disponibile in GitHub per ognuna delle funzionalità del servizio vocale. Questi esempi esaminano gli scenari comuni, ad esempio la lettura di audio da un file o streaming, il riconoscimento continuo e singolo e l'uso di modelli personalizzati. Usare i collegamenti seguenti per visualizzare esempi SDK e REST:

- [Speech-to-text and speech translation samples (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk) (Esempi di riconoscimento vocale e traduzione vocale (SDK))
- [Batch transcription samples (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch) (Esempi di trascrizione batch (REST))
- [Text-to-speech samples (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS) (Esempi di sintesi vocale (REST))

## <a name="reference-docs"></a>Documentazione di riferimento

- [Speech SDK](speech-sdk-reference.md)
- [Speech Devices SDK](speech-devices-sdk.md)
- [API REST: sintesi vocale](rest-speech-to-text.md)
- [API REST: sintesi vocale](rest-text-to-speech.md)
- [API REST: trascrizione e personalizzazione di batch](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Ottenere gratuitamente una chiave di sottoscrizione per i servizi di riconoscimento vocale](get-started.md)
