---
title: Trascrizioni di call center - Servizi Voce
titleSuffix: Azure Cognitive Services
description: Uno scenario comune per il riconoscimento vocale è la trascrizione di grandi volumi di dati di telefonia che possono provenire da vari sistemi, ad esempio i bot IVR (Interactive Voice Response). L'audio può essere stereo o mono e raw, con poca o nessuna elaborazione eseguita sul segnale. Grazie all'uso dei servizi Voce e del modello conversione voce/testo unificato, un'azienda può ottenere trascrizioni di alta qualità, con molti sistemi di acquisizione audio.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 7d844f4d2ad77f5b7cc53275a24167e5f2e71b78
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025382"
---
# <a name="speech-services-for-telephony-data"></a>Servizi Voce per i dati di telefonia

I dati di telefonia generati attraverso telefoni fissi, telefoni cellulari e radio sono in genere di bassa qualità e banda stretta compresa nell'intervallo di frequenza di 8 KHz, che crea problemi durante il riconoscimento vocale. I modelli di riconoscimento vocale più recenti dei servizi Voce di Azure eccellono nella trascrizione dei dati di telefonia, persino nei casi in cui la comprensione dei dati è complessa per un umano. Questi modelli vengono sottoposti a training con grandi volumi di dati di telefonia e sono caratterizzati dall'accuratezza di riconoscimento migliore sul mercato, anche in ambienti rumorosi.

Uno scenario comune per il riconoscimento vocale è la trascrizione di grandi volumi di dati di telefonia che possono provenire da vari sistemi, ad esempio i bot IVR (Interactive Voice Response). L'audio fornito da questi sistemi può essere stereo o mono e raw, con poca o nessuna elaborazione eseguita sul segnale. Grazie all'uso dei servizi Voce e del modello conversione voce/testo unificato, un'azienda può ottenere trascrizioni di alta qualità, qualunque sia il sistema usato per acquisire l'audio.

È possibile usare i dati di telefonia per meglio comprendere le esigenze dei clienti, identificare nuove opportunità di marketing oppure valutare le prestazioni degli operatori del call center. Dopo aver trascritto i dati, un'azienda può impiegare l'output per migliorare la telemetria, identificare frasi chiave o analizzare il sentiment del cliente.

Le tecnologie descritte in questa pagina sono usate internamente da Microsoft per vari servizi di elaborazione di chiamate al supporto, sia in tempo reale che in modalità batch.

Verranno ora esaminate alcune tecnologie e funzionalità correlate che sono offerte dai servizi Voce di Azure.

> [!IMPORTANT]
> Il training del modello unificato dei servizi Voce viene eseguito con dati diversi per offrire una singola soluzione in una serie di scenari, dalla dettatura all'analisi dei dati di telefonia.

## <a name="azure-technology-for-call-centers"></a>Tecnologia Azure per i call center

Oltre all'aspetto funzionale, l'obiettivo principale dei servizi Voce, se applicati al call center, è di migliorare l'esperienza del cliente. A questo proposito esistono tre domini 

* L'analisi post-chiamata, cioè l'elaborazione batch delle registrazioni delle chiamate 
* L'elaborazione analitica in tempo reale del segnale audio per estrarre varie informazioni dettagliate mentre la chiamata è in corso (in cui la valutazione è un caso d'uso importante)
* Gli assistenti virtuali, o bot, che favoriscono il dialogo tra cliente e bot nel tentativo di risolvere il problema del cliente senza la partecipazione dell'operatore oppure applicano i protocolli di intelligenza artificiale per assistere l'operatore.

L'immagine sotto ![Architettura delle trascrizioni del call center](media/scenarios/call-center-transcription-architecture.png) mostra un tipico diagramma architettonico dell'implementazione di uno scenario batch

## <a name="speech-analytics-technology-components"></a>Componenti tecnologici dell'analisi vocale

Indipendentemente dal fatto che il dominio sia post-chiamata o in tempo reale, Azure offre una serie di tecnologie emergenti e avanzate per migliorare l'esperienza del cliente. 

### <a name="speech-to-text-stt"></a>Riconoscimento vocale 

[Riconoscimento vocale](speech-to-text.md) è la soluzione più ambita in qualsiasi soluzione di call center. Dal momento che molti processi analitici downstream si basano sul testo trascritto, la percentuale di parole errate è della massima importanza. Una delle principali sfide da superare nella trascrizione dei call center è il rumore prevalente nella sala (ad esempio: il sottofondo degli altri operatori che parlano), l'ampia varietà di impostazioni locali della lingua e di dialetti, nonché la bassa qualità dello stesso segnale telefonico. La percentuale di parole errate è strettamente correlata all'efficacia del training dei modelli acustici e linguistici per determinate impostazioni locali; di conseguenza, è importante poter personalizzare il modello sulle proprie impostazioni locali. I più recenti modelli unificati di quarta generazione rappresentano la soluzione ideale sia per l'accuratezza che per la latenza della trascrizione. Sottoposti a un training di decine di migliaia di ore di dati acustici e miliardi di informazioni lessicali, i modelli unificati sono i più accurati sul mercato per la trascrizione dei dati del call center.

### <a name="sentiment"></a>Valutazione
Valutare se il cliente ha avuto una buona esperienza è una delle aree più importanti dell'analisi vocale, quando applicata allo spazio del call center. L'[API di trascrizione Batch](batch-transcription.md) offre l'analisi del sentiment per ogni espressione. È possibile aggregare il set di valori ottenuti come parte di una trascrizione di chiamata per determinare il sentiment della chiamata sia per gli operatori che per il cliente.

### <a name="silence-non-talk"></a>Il silenzio (nessuna conversazione)
non è insolito per il 35% di una chiamata al supporto tecnico. Alcuni scenari in cui gli interlocutori restano in silenzio si hanno, ad esempio, quando l'operatore si mette a cercare la precedente case history del cliente o usa strumenti che gli consentono di accedere al desktop del cliente ed eseguire funzioni, o ancora quando i clienti restano in attesa di un trasferimento della chiamata, e così via. È di estrema importanza poter valutare a che punto della chiamata si ferma la conversazione, perché in questo tipo di scenario occorre tenere presenti una serie di importanti aspetti sensibili del cliente e il punto della chiamata in cui si verificano.

### <a name="translation"></a>Traduzione
Alcune aziende stanno sperimentando con le trascrizioni tradotte delle chiamate al supporto in lingue straniere, in modo che i Delivery Manager possano comprendere l'esperienza mondiale dei propri clienti. Le nostre competenze di [traduzione](translation.md) non hanno rivali. Possiamo tradurre da audio a audio o da audio a testo in una vasta gamma di impostazioni locali.

### <a name="text-to-speech"></a>Sintesi vocale
La [sintesi vocale](text-to-speech.md) è un'altra area importante nell'implementazione di bot che interagiscono con i clienti. In un percorso tipico, il cliente parla e la sua voce viene trascritta testualmente; viene analizzato l'intento del testo e quindi sintetizzata una risposta in base all'intento riconosciuto. Infine, viene presentato un asset al cliente oppure viene generata una risposta con voce sintetizzata. Naturalmente tutto questo deve verificarsi rapidamente, quindi la latenza è un componente importante nel successo di questi sistemi. 

La latenza end-to-end è piuttosto bassa, considerando le varie tecnologie coinvolte, ad esempio [Riconoscimento vocale](speech-to-text.md), [Luis](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/), [Bot Framework](https://dev.botframework.com/) e [Sintesi vocale](text-to-speech.md). 

Le nuove voci sono inoltre indistinguibili da quelle umane. È possibile conferire al bot una personalità unica con le nostre voci.

### <a name="search"></a>Ricerca
Un altro elemento di base dell'analisi consiste nell'identificare le interazioni in cui si è verificata un'esperienza o un evento specifico. Di norma questa operazione segue uno di due approcci: una ricerca ad hoc, in cui l'utente si limita a digitare una frase ricevendo una risposta dal sistema, oppure una query più strutturata, in cui un analista può creare un set di istruzioni logiche che identifica uno scenario in una chiamata, e quindi indicizzare ciascuna chiamata in base allo stesso set di query. Un esempio di ricerca ottimale è la dichiarazione di conformità diffusa che recita: "Questa chiamata verrà registrata per garantire la qualità del servizio...", perché molte aziende vogliono assicurarsi che i propri operatori comunichino questa dichiarazione di non responsabilità ai clienti prima che la chiamata venga effettivamente registrata. La maggior parte dei sistemi analitici ha la capacità di individuare le tendenze di comportamento rilevate dagli algoritmi di ricerca/query, poiché la creazione di rapporti sulle tendenze è in definitiva una delle funzioni più importanti di un sistema analitico. Grazie alla [directory Servizi cognitivi](https://azure.microsoft.com/services/cognitive-services/directory/search/), la soluzione end-to-end può essere significativamente migliorata con funzionalità di indicizzazione e ricerca.

### <a name="key-phrase-extraction"></a>Estrazione frasi chiave
Quest'area è una delle applicazioni analitiche più impegnative, che trae vantaggio dall'applicazione dell'intelligenza artificiale e del Machine Learning. In questo caso, lo scenario principale consiste nel dedurre l'intento del cliente. Qual è il motivo della chiamata? Qual è il problema del cliente? Perché il cliente ha riscontrato un'esperienza negativa? Il [servizio Analisi del testo](https://azure.microsoft.com/services/cognitive-services/text-analytics/) offre un set di analisi predefinita per l'aggiornamento rapido della soluzione end-to-end, al fine di estrarre parole chiave o frasi importanti.

Verranno ora analizzate più dettagliatamente l'elaborazione batch e le pipeline in tempo reale per il riconoscimento vocale.

## <a name="batch-transcription-of-call-center-data"></a>Trascrizione batch dei dati del call center

Per trascrivere in blocco l'audio abbiamo sviluppato l'[API di trascrizione batch](batch-transcription.md). L'API di trascrizione batch consente di trascrivere grandi quantità di dati audio in modo asincrono. Per quanto riguarda la trascrizione dei dati del call center, la nostra soluzione si basa su questi concetti fondamentali:

* **Accuratezza**: grazie ai modelli unificati di quarta generazione, la qualità delle nostre trascrizioni non ha rivali.
* **Latenza**: ovviamente la rapidità è un fattore importante durante le trascrizioni in blocco. I processi di trascrizione avviati usando l'[API di trascrizione batch](batch-transcription.md) verranno inseriti immediatamente in coda e, una volta eseguito, il processo sarà ancora più veloce della trascrizione in tempo reale.
* **Sicurezza**: siamo consapevoli che le chiamate potrebbero contenere dati riservati. Assicuriamo il cliente che la sicurezza è una delle nostre massime priorità. Il nostro servizio ha ottenuto le certificazioni ISO, SOC, HIPAA e PCI.

I call center generano grandi volumi di dati audio su base giornaliera. Se l'azienda archivia i dati di telefonia in una posizione centrale, ad esempio Archiviazione di Azure, è possibile usare l'[API di trascrizione batch]((batch-transcription.md) per richiedere e ricevere trascrizioni in modo asincrono.

Una tipica soluzione usa questi servizi:

* Per il riconoscimento vocale vengono usati i servizi Voce di Azure. Per usare l'API di trascrizione batch è necessaria una sottoscrizione standard (SO) ai servizi Voce. Le sottoscrizioni gratuite (F0) non funzioneranno.
* Per archiviare i dati di telefonia e le trascrizioni restituite dall'API di trascrizione batch si usa [Archiviazione di Azure](https://azure.microsoft.com/services/storage/). Questo account di archiviazione deve usare le notifiche, in particolare quando vengono aggiunti nuovi file. Queste notifiche vengono usate per attivare il processo di trascrizione.
* [Funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/) consente di creare l'URI di firma di accesso condiviso per ogni registrazione e attivare la richiesta POST HTTP per avviare una trascrizione. Inoltre, Funzioni di Azure consente di creare richieste per recuperare ed eliminare trascrizioni usando l'API di trascrizione batch.
* I [webhook](webhooks.md) vengono usati per ricevere notifiche quando le trascrizioni sono state completate.

Microsoft usa internamente le suddette tecnologie per supportare le chiamate dei clienti in modalità batch.
![Architettura batch](media/scenarios/call-center-batch-pipeline.png)

## <a name="real-time-transcription-for-call-center-data"></a>Trascrizione in tempo reale dei dati del call center

Alcune aziende sono tenute a trascrivere le conversazioni in tempo reale. La trascrizione in tempo reale può essere usata per identificare parole chiave e attivare ricerche di contenuto e risorse pertinenti alla conversazione, per il monitoraggio del sentiment, per migliorare l'accessibilità o per fornire le traduzioni ai clienti e agli operatori che non sono madrelingua.

Per gli scenari che richiedono la trascrizione in tempo reale, è consigliabile usare [Speech SDK](speech-sdk.md). Il riconoscimento vocale è attualmente disponibile in [oltre 20 lingue](language-support.md), e l'SDK è disponibile in C++, C#, Java, Python, Node.js e Javascript. Esempi sono disponibili in ogni lingua su [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk). Per le notizie e gli aggiornamenti più recenti, vedere le [Note sulla versione](releasenotes.md).

Microsoft usa internamente le suddette tecnologie per analizzare in tempo reale le chiamate dei clienti.

![Architettura batch](media/scenarios/call-center-reatime-pipeline.png)

## <a name="a-word-on-ivrs"></a>Introduzione ai sistemi IVR

È possibile integrare facilmente i servizi Voce in qualsiasi soluzione usando [Speech SDK](speech-sdk.md) o l'[API REST](rest-apis.md). Tuttavia, le trascrizioni del call center possono richiedere tecnologie aggiuntive. In genere, è necessaria una connessione tra un sistema IVR e Azure. Pur non offrendo tali componenti, vorremmo descrivere cosa implica una connessione a un sistema IVR.

Diversi sistemi IVR o prodotti di servizi telefonici (ad esempio Genesys o AudioCodes) offrono funzionalità di integrazione che è possibile sfruttare per attivare il passthrough audio in ingresso e in uscita verso un servizio di Azure. In sostanza, un servizio di Azure personalizzato potrebbe fornire una specifica interfaccia per definire le sessioni di chiamata telefonica (ad esempio Inizio chiamata e Fine chiamata) ed esporre un'API WebSocket per la ricezione di flusso audio in ingresso che viene usato con i servizi Voce. Le risposte in uscita, ad esempio la trascrizione della conversazione o le connessioni con Bot Framework, possono essere sintetizzate con il servizio di sintesi vocale di Microsoft e restituite al sistema IVR per la riproduzione.

Un altro scenario è l'integrazione diretta con SIP. Un servizio di Azure si connette a un server SIP, ottenendo così un flusso in ingresso e un flusso in uscita, che viene usato per le fasi per il riconoscimento vocale e sintesi vocale. Per connettersi a un server SIP sono disponibili offerte di software commerciale, ad esempio Ozieki SDK oppure [l'API di chiamata e riunione dei team](https://docs.microsoft.com/graph/api/resources/calls-api-overview?view=graph-rest-beta) (attualmente in versione beta), che sono progettate per supportare questo tipo di scenario per le chiamate audio.

## <a name="customize-existing-experiences"></a>Personalizzare le esperienze esistenti

I servizi Voce di Azure funzionano bene con i modelli predefiniti, tuttavia, è possibile personalizzare e ottimizzare l'esperienza per un prodotto o ambiente. Le opzioni di personalizzazione spaziano dall'ottimizzazione del modello acustico ai caratteri voce univoci per il proprio marchio. Dopo aver creato un modello personalizzato, è possibile usarlo con uno qualsiasi dei servizi Voce di Azure, in tempo reale o in modalità batch.

| Servizio Voce | Modello | DESCRIZIONE |
|----------------|-------|-------------|
| Riconoscimento vocale | [Modello acustico](how-to-customize-acoustic-models.md) | Creazione di un modello acustico personalizzato per applicazioni, strumenti o dispositivi usati in ambienti particolari, ad esempio in un'automobile o in fabbrica, ognuno con condizioni di registrazione specifiche. Sono esempi di queste situazioni un eloquio con un forte accento, particolari rumori di sottofondo o l'uso di uno speciale microfono per la registrazione. |
| | [Modello linguistico](how-to-customize-language-model.md) | Creazione di un modello linguistico personalizzato per migliorare la trascrizione di grammatica e terminologia specifica di settore, ad esempio terminologia medica o IT. |
| | [Modello di pronuncia](how-to-customize-pronunciation.md) | Con un modello di pronuncia personalizzato è possibile definire la forma fonetica e visualizzare una parola o un termine. È utile per gestire i termini personalizzati, come i nomi di prodotto o gli acronimi. Tutto ciò che serve è un file di pronunce, un semplice file con estensione TXT. |
| Sintesi vocale | [Carattere voce](how-to-customize-voice-font.md) | I caratteri voce personalizzati consentono di creare una voce distintiva e unica per il proprio marchio. Per iniziare è necessaria solo una piccola quantità di dati. Più dati si forniscono, più naturale e umano risulterà il carattere voce. |

## <a name="sample-code"></a>Codice di esempio

Il codice di esempio è disponibile in GitHub per ognuno dei servizi Voce di Azure. Questi esempi esaminano gli scenari comuni, ad esempio la lettura di audio da un file o streaming, il riconoscimento continuo e singolo e l'uso di modelli personalizzati. Usare i collegamenti seguenti per visualizzare esempi SDK e REST:

* [Speech-to-text and speech translation samples (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk) (Esempi di riconoscimento vocale e traduzione vocale (SDK))
* [Batch transcription samples (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch) (Esempi di trascrizione batch (REST))
* [Text-to-speech samples (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS) (Esempi di sintesi vocale (REST))

## <a name="reference-docs"></a>Documentazione di riferimento

* [Speech SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [API REST: Riconoscimento vocale](rest-speech-to-text.md)
* [API REST: Sintesi vocale](rest-text-to-speech.md)
* [API REST: Trascrizione e personalizzazione batch](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Ottieni gratuitamente una chiave di sottoscrizione per i servizi Voce](get-started.md)
