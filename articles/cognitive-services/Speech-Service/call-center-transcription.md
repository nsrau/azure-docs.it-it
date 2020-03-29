---
title: Trascrizione del Call Center - Servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Uno scenario comune per la sintesi vocale è la trascrizione di grandi volumi di dati di telefonia provenienti da vari sistemi, ad esempio IVR (Interactive Voice Response). Utilizzando il servizio di riconoscimento vocale e il modello di riconoscimento vocale unificato, un'azienda può ottenere trascrizioni di alta qualità con sistemi di acquisizione audio.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: d959f4948d6b848f3b399c1310add06991d72012
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806321"
---
# <a name="speech-service-for-telephony-data"></a>Servizio di riconoscimento vocale per i dati di telefonia

I dati di telefonia generati attraverso telefoni fissi, telefoni cellulari e radio sono in genere di bassa qualità e banda stretta compresa nell'intervallo di frequenza di 8 KHz, che crea problemi durante il riconoscimento vocale. Gli ultimi modelli di riconoscimento vocale del servizio di riconoscimento vocale eccellono nel trascrivere questi dati di telefonia, anche nei casi in cui i dati sono difficili da comprendere per un essere umano. Questi modelli vengono sottoposti a training con grandi volumi di dati di telefonia e hanno una migliore precisione di riconoscimento sul mercato, anche in ambienti rumorosi.

Uno scenario comune per il riconoscimento vocale è la trascrizione di grandi volumi di dati di telefonia che possono provenire da vari sistemi, ad esempio i bot IVR (Interactive Voice Response). L'audio fornito da questi sistemi può essere stereo o mono e raw, con poca o nessuna elaborazione eseguita sul segnale. Utilizzando il servizio di riconoscimento vocale e il modello di riconoscimento vocale unificato, un'azienda può ottenere trascrizioni di alta qualità, indipendentemente dai sistemi utilizzati per acquisire l'audio.

È possibile usare i dati di telefonia per meglio comprendere le esigenze dei clienti, identificare nuove opportunità di marketing oppure valutare le prestazioni degli operatori del call center. Dopo la trascrizione dei dati, un'azienda può usare l'output per scopi quali la telemetria migliorata, l'identificazione di frasi chiave o l'analisi del sentiment dei clienti.

Le tecnologie descritte in questa pagina sono usate internamente da Microsoft per vari servizi di elaborazione di chiamate al supporto, sia in tempo reale che in modalità batch.

Esaminiamo alcune delle tecnologie e delle funzionalità correlate offerte dal servizio di riconoscimento vocale.

> [!IMPORTANT]
> Il modello Unificato del servizio di riconoscimento vocale viene sottoposto a training con dati diversi e offre una soluzione a modello singolo per una serie di scenari, dalla Dettatura all'analisi della telefonia.

## <a name="azure-technology-for-call-centers"></a>Tecnologia Azure per i call center

Oltre all'aspetto funzionale delle funzionalità del servizio di riconoscimento vocale, il loro scopo principale, se applicato al call center, è quello di migliorare l'esperienza del cliente. A questo proposito esistono tre domini:

- Analisi post-chiamata, che è essenzialmente l'elaborazione batch delle registrazioni delle chiamate dopo la chiamata.
- Analisi in tempo reale, che è l'elaborazione del segnale audio per estrarre varie intuizioni mentre la chiamata è in corso (con sentimento è un caso d'uso prominente).
- Assistenti vocali (bot), che guidano il dialogo tra il cliente e il bot nel tentativo di risolvere il problema del cliente senza partecipazione dell'agente, o essendo l'applicazione di protocolli di intelligenza artificiale (AI) per assistere l'agente.

L'immagine sotto ![Architettura delle trascrizioni del call center](media/scenarios/call-center-transcription-architecture.png) mostra un tipico diagramma architettonico dell'implementazione di uno scenario batch

## <a name="speech-analytics-technology-components"></a>Componenti tecnologici dell'analisi vocale

Sia che il dominio sia post-chiamata o in tempo reale, Azure offre una serie di tecnologie mature ed emergenti per migliorare l'esperienza del cliente.

### <a name="speech-to-text-stt"></a>Riconoscimento vocale

[La sintesi vocale](speech-to-text.md) è la funzionalità più ricercata in qualsiasi soluzione di call center. Poiché molti dei processi di analisi a valle si basano sul testo trascritto, il tasso di errore della parola (_WER_) è della massima importanza. Tra le principali sfide da superare nella trascrizione dei call center sono inclusi il rumore prevalente nella sala (ad esempio: il sottofondo degli altri operatori che parlano), l'ampia varietà di impostazioni locali della lingua e di dialetti, nonché la bassa qualità dello stesso segnale telefonico. WER è altamente correlato con quanto bene i modelli acustici e linguistici sono addestrati per una determinata impostazione locale, quindi la capacità di personalizzare il modello per le tue impostazioni è importante. I più recenti modelli unificati di quarta generazione rappresentano la soluzione ideale sia per l'accuratezza che per la latenza della trascrizione. Addestrati con decine di migliaia di ore di dati acustici e miliardi di informazioni lessicali, i modelli Unified sono i modelli più accurati sul mercato per trascrivere i dati dei call center.

### <a name="sentiment"></a>Valutazione

Valutare se il cliente ha avuto una buona esperienza è una delle aree più importanti dell'analisi vocale, quando applicata allo spazio del call center. L'[API di trascrizione Batch](batch-transcription.md) offre l'analisi del sentiment per ogni espressione. È possibile aggregare il set di valori ottenuti come parte di una trascrizione di chiamata per determinare il sentiment della chiamata sia per gli operatori che per il cliente.

### <a name="silence-non-talk"></a>Il silenzio (nessuna conversazione)

Non è insolito per il 35% del tempo di una chiamata al supporto tecnico. Alcuni scenari per i quali si verifica una non conversazione sono: agenti che cercano la cronologia dei casi precedente con un cliente, agenti che utilizzano strumenti che consentono loro di accedere al desktop del cliente ed eseguire funzioni, clienti seduti in attesa di un trasferimento e così via. È estremamente importante valutare quando si verifica il silenzio in una chiamata in quanto esistono numerose importanti sensibilità del cliente che si verificano intorno a questi tipi di scenari e dove si verificano nella chiamata.

### <a name="translation"></a>Traduzione

Alcune aziende stanno sperimentando la fornitura di trascrizioni tradotte da chiamate di supporto in lingua straniera in modo che i responsabili delle consegne possano comprendere l'esperienza a livello mondiale dei loro clienti. Le nostre competenze di [traduzione](translation.md) non hanno rivali. Siamo in grado di tradurre audio-audio o audio-a-testo per un gran numero di impostazioni locali.

### <a name="text-to-speech"></a>Sintesi vocale

La [sintesi vocale](text-to-speech.md) è un'altra area importante nell'implementazione di bot che interagiscono con i clienti. In un percorso tipico, il cliente parla e la sua voce viene trascritta testualmente; viene analizzato l'intento del testo e quindi sintetizzata una risposta in base all'intento riconosciuto. Infine, viene presentato un asset al cliente oppure viene generata una risposta con voce sintetizzata. Naturalmente tutto questo deve avvenire rapidamente - quindi la bassa latenza è una componente importante per il successo di questi sistemi.

La latenza end-to-end è notevolmente bassa per le varie tecnologie coinvolte, ad esempio [la sintesi vocale,](speech-to-text.md) [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/), [Bot Framework](https://dev.botframework.com/), [la sintesi vocale.](text-to-speech.md)

Le nuove voci sono inoltre indistinguibili da quelle umane. Puoi usare le nostre voci per dare al tuo bot la sua personalità unica.

### <a name="search"></a>Ricerca

Un altro elemento di base dell'analisi consiste nell'identificare le interazioni in cui si è verificata un'esperienza o un evento specifico. Questa operazione viene in genere eseguita con uno dei due approcci. una ricerca ad hoc in cui l'utente semplicemente digita una frase e il sistema risponde, o una query più strutturata in cui un analista può creare un set di istruzioni logiche che identificano uno scenario in una chiamata e quindi ogni chiamata può essere indicizzata in base a tale set di query. Un buon esempio di ricerca è l'onnipresente dichiarazione di conformità "questa chiamata deve essere registrata per scopi di qualità... ". Molte aziende vogliono assicurarsi che i loro agenti stanno fornendo questa dichiarazione di non responsabilità ai clienti prima che la chiamata sia effettivamente registrata. La maggior parte dei sistemi di analisi ha la capacità di tendenze dei comportamenti rilevati dagli algoritmi di query/ricerca, e questo reporting delle tendenze è in definitiva una delle funzioni più importanti di un sistema di analisi. Grazie alla [directory Servizi cognitivi](https://azure.microsoft.com/services/cognitive-services/directory/search/), la soluzione end-to-end può essere significativamente migliorata con funzionalità di indicizzazione e ricerca.

### <a name="key-phrase-extraction"></a>Estrazione frasi chiave

Quest'area è una delle applicazioni di analisi più impegnative e che sta beneficiando dell'applicazione dell'iaformazione artificiale e dell'apprendimento automatico. Lo scenario principale in questo caso consiste nell'indurre l'intento del cliente. Qual è il motivo della chiamata? Qual è il problema del cliente? Perché il cliente ha riscontrato un'esperienza negativa? Il nostro servizio di analisi del [testo](https://azure.microsoft.com/services/cognitive-services/text-analytics/) fornisce una serie di analisi disponibili per aggiornare rapidamente la soluzione end-to-end per estrarre tali parole chiave o frasi importanti.

Verranno ora analizzate più dettagliatamente l'elaborazione batch e le pipeline in tempo reale per il riconoscimento vocale.

## <a name="batch-transcription-of-call-center-data"></a>Trascrizione batch dei dati del call center

Per la trascrizione di audio di massa abbiamo sviluppato [l'API di trascrizione batch](batch-transcription.md). L'API di trascrizione batch consente di trascrivere grandi quantità di dati audio in modo asincrono. Per quanto riguarda la trascrizione dei dati del call center, la nostra soluzione si basa su questi pilastri:

- **Precisione** - Con i modelli Unified di quarta generazione, offriamo una qualità di trascrizione insuperabile.
- **Latenza** - Comprendiamo che quando si eseguono trascrizioni collettive, le trascrizioni sono necessarie rapidamente. I processi di trascrizione avviati usando l'[API di trascrizione batch](batch-transcription.md) verranno inseriti immediatamente in coda e, una volta avviato, il processo sarà ancora più veloce della trascrizione in tempo reale.
- **Sicurezza** - Siamo consapevoli che le chiamate possono contenere dati sensibili. Assicuriamo il cliente che la sicurezza è una delle nostre massime priorità. Il nostro servizio ha ottenuto le certificazioni ISO, SOC, HIPAA e PCI.

I call center generano quotidianamente grandi volumi di dati audio. Se l'azienda archivia i dati di telefonia in una posizione centrale, ad esempio Archiviazione di Azure, è possibile usare l'[API di trascrizione batch](batch-transcription.md) per richiedere e ricevere trascrizioni in modo asincrono.

Una tipica soluzione usa questi servizi:

- Il servizio di riconoscimento vocale viene utilizzato per trascrivere la sintesi vocale. Per usare l'API di trascrizione batch, è necessaria una sottoscrizione standard (S0) per il servizio di riconoscimento vocale. Le sottoscrizioni gratuite (F0) non funzioneranno.
- Per archiviare i dati di telefonia e le trascrizioni restituite dall'API di trascrizione batch si usa [Archiviazione di Azure](https://azure.microsoft.com/services/storage/). Questo account di archiviazione deve usare le notifiche, in particolare quando vengono aggiunti nuovi file. Queste notifiche vengono usate per attivare il processo di trascrizione.
- [Funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/) consente di creare l'URI di firma di accesso condiviso per ogni registrazione e attivare la richiesta POST HTTP per avviare una trascrizione. Inoltre, Funzioni di Azure consente di creare richieste per recuperare ed eliminare trascrizioni usando l'API di trascrizione batch.

Microsoft usa internamente le suddette tecnologie per supportare le chiamate dei clienti in modalità batch.
![Architettura batch](media/scenarios/call-center-batch-pipeline.png)

## <a name="real-time-transcription-for-call-center-data"></a>Trascrizione in tempo reale dei dati del call center

Alcune aziende sono tenute a trascrivere le conversazioni in tempo reale. La trascrizione in tempo reale può essere usata per identificare parole chiave e attivare ricerche di contenuto e risorse pertinenti alla conversazione, per il monitoraggio del sentiment, per migliorare l'accessibilità o per fornire le traduzioni ai clienti e agli operatori che non sono madrelingua.

Per gli scenari che richiedono la trascrizione in tempo reale, è consigliabile usare [Speech SDK](speech-sdk.md). Il riconoscimento vocale è attualmente disponibile in [oltre 20 lingue](language-support.md), e l'SDK è disponibile in C++, C#, Java, Python, Node.js, Objective-C e JavaScript. Esempi sono disponibili in ogni lingua su [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk). Per le notizie e gli aggiornamenti più recenti, vedere le [Note sulla versione](releasenotes.md).

Internamente stiamo usando le tecnologie di cui sopra per analizzare in tempo reale le chiamate dei clienti Microsoft man mano che si verificano, come illustrato nel diagramma seguente.

![Architettura batch](media/scenarios/call-center-reatime-pipeline.png)

## <a name="a-word-on-ivrs"></a>Introduzione ai sistemi IVR

Il servizio di riconoscimento vocale può essere facilmente integrato in qualsiasi soluzione usando [l'SDK di riconoscimento vocale](speech-sdk.md) o l'API [REST.](rest-apis.md) Tuttavia, le trascrizioni del call center possono richiedere tecnologie aggiuntive. In genere, è necessaria una connessione tra un sistema IVR e Azure. Anche se non offriamo tali componenti, ecco una descrizione che comporta una connessione a un IVR.

Diversi prodotti IVR o servizio di telefonia (ad esempio Genesys o AudioCodes) offrono funzionalità di integrazione che possono essere sfruttate per abilitare il pass-through audio in ingresso e in uscita a un servizio di Azure.Several IVR or telephony service products (such as Genesys or AudioCodes) offer integration capabilities that can be leveraged to enable inbound and outbound audio pass-through to an Azure service. Fondamentalmente, un servizio di Azure personalizzato potrebbe fornire un'interfaccia specifica per definire le sessioni di chiamata telefonica (ad esempio L'inizio chiamata o la fine chiamata) ed esporre un'API WebSocket per ricevere l'audio di flusso in ingresso usato con il servizio di riconoscimento vocale. Le risposte in uscita, ad esempio la trascrizione della conversazione o le connessioni con Bot Framework, possono essere sintetizzate con il servizio di sintesi vocale di Microsoft e restituite al sistema IVR per la riproduzione.

Un altro scenario è l'integrazione diretta con SIP (Session Initiation Protocol). Un servizio di Azure si connette a un server SIP, ottenendo così un flusso in ingresso e un flusso in uscita, che viene usato per le fasi per il riconoscimento vocale e sintesi vocale. Per connettersi a un server SIP sono disponibili offerte di software commerciale, ad esempio Ozieki SDK oppure [l'API per le chiamate e le riunioni dei team](/graph/api/resources/communications-api-overview) (attualmente in versione beta), che sono progettate per supportare questo tipo di scenario per le chiamate audio.

## <a name="customize-existing-experiences"></a>Personalizzare le esperienze esistenti

 Il servizio di riconoscimento vocale funziona bene con i modelli incorporati. Tuttavia, è possibile personalizzare e ottimizzare ulteriormente l'esperienza per il prodotto o l'ambiente. Le opzioni di personalizzazione spaziano dall'ottimizzazione del modello acustico ai caratteri voce univoci per il proprio marchio. Dopo aver creato un modello personalizzato, è possibile utilizzarlo con una qualsiasi delle funzionalità del servizio di riconoscimento vocale in modalità batch o in tempo reale.

| Servizio Voce | Modello | Descrizione |
| -------------- | ----- | ----------- |
| Riconoscimento vocale | [Modello acustico](how-to-customize-acoustic-models.md) | Creazione di un modello acustico personalizzato per applicazioni, strumenti o dispositivi usati in ambienti particolari, ad esempio in un'automobile o in fabbrica, ognuno con condizioni di registrazione specifiche. Sono esempi di queste situazioni un eloquio con un forte accento, particolari rumori di sottofondo o l'uso di uno speciale microfono per la registrazione. |
|                | [Modello linguistico](how-to-customize-language-model.md) | Creazione di un modello linguistico personalizzato per migliorare la trascrizione di grammatica e terminologia specifica di settore, ad esempio terminologia medica o IT. |
|                | [Modello di pronuncia](how-to-customize-pronunciation.md) | Con un modello di pronuncia personalizzato, è possibile definire la forma fonetica e la visualizzazione per una parola o un termine. È utile per gestire i termini personalizzati, come i nomi di prodotto o gli acronimi. Tutto ciò che serve per iniziare è un `.txt` file di pronuncia, che è un semplice file. |
| Sintesi vocale | [Carattere voce](how-to-customize-voice-font.md) | I caratteri voce personalizzati consentono di creare una voce distintiva e unica per il proprio marchio. Per iniziare è necessaria solo una piccola quantità di dati. Più dati si forniscono, più naturale e umano risulterà il carattere voce. |

## <a name="sample-code"></a>Codice di esempio

Il codice di esempio è disponibile in GitHub per ognuna delle funzionalità del servizio di riconoscimento vocale. Questi esempi esaminano gli scenari comuni, ad esempio la lettura di audio da un file o streaming, il riconoscimento continuo e singolo e l'uso di modelli personalizzati. Usare i collegamenti seguenti per visualizzare esempi SDK e REST:

- [Speech-to-text and speech translation samples (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk) (Esempi di riconoscimento vocale e traduzione vocale (SDK))
- [Batch transcription samples (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch) (Esempi di trascrizione batch (REST))
- [Text-to-speech samples (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS) (Esempi di sintesi vocale (REST))

## <a name="reference-docs"></a>Documentazione di riferimento

- [Speech SDK](speech-sdk-reference.md)
- [Speech Devices SDK](speech-devices-sdk.md)
- [API REST: sintesi vocale: sintesi vocale](rest-speech-to-text.md)
- [API REST: sintesi vocale](rest-text-to-speech.md)
- [API REST: trascrizione e personalizzazione batch](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Ottenere gratuitamente una chiave di sottoscrizione per i servizi di riconoscimento vocale](get-started.md)
