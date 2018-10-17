---
title: Informazioni sul Servizio di riconoscimento vocale
description: 'Il servizio Voce, parte dei Servizi Cognitivi Microsoft, unisce diversi servizi vocali di Azure precedentemente disponibili in modo separato: Riconoscimento vocale Bing (che comprende il riconoscimento vocale e la sintesi vocale), Riconoscimento vocale personalizzato e Traduzione vocale.'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: overview
ms.date: 09/24/2018
ms.author: v-jerkin
ms.openlocfilehash: b014144dd819aa354596175068bfb2c5905a4377
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2018
ms.locfileid: "48857993"
---
# <a name="what-is-the-speech-service"></a>Informazioni sul Servizio di riconoscimento vocale


Come gli altri servizi vocali di Azure, il servizio Voce è basato su tecnologie vocali usate in prodotti quali Cortana e Microsoft Office.

Il servizio Voce combina le funzionalità vocali di Azure precedentemente disponibili tramite i servizi [API Riconoscimento vocale Bing](https://docs.microsoft.com/azure/cognitive-services/speech/home), [Traduzione vocale](https://docs.microsoft.com/azure/cognitive-services/translator-speech/), [Riconoscimento vocale personalizzato](https://docs.microsoft.com/azure/cognitive-services/custom-speech-service/cognitive-services-custom-speech-home) e [Voce personalizzata](http://customvoice.ai/). Adesso, con una sottoscrizione è possibile accedere a tutte queste funzionalità.

## <a name="main-speech-service-functions"></a>Funzionalità principali del servizio Voce

Le funzionalità principali del servizio Voce sono Riconoscimento vocale (anche noto come trascrizione), Sintesi vocale e Traduzione vocale.

|Funzione|Funzionalità|
|-|-|
|[Riconoscimento vocale](speech-to-text.md)| <ul><li>Esegue la trascrizione del parlato continuo in tempo reale in testo.<li>Consente di eseguire trascrizioni batch da registrazioni audio. <li>Supporta risultati intermedi, rilevamento della fine del parlato, formattazione automatica del testo e maschera per il contenuto volgare. <li>Può chiamare [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) per derivare la finalità dell'utente dal parlato trascritto.\*|
|[Sintesi vocale](text-to-speech.md)| <ul><li>Converte il testo scritto in un audio che suona naturale. <li>Offre più sessi e/o dialetti per numerose lingue supportate. <li>Supporta l'input di testo normale o il linguaggio SSML (Speech Synthesis Markup Language). |
|[Traduzione vocale](speech-translation.md)| <ul><li>Traduce l'audio in streaming quasi in tempo reale.<li> Può anche elaborare audio registrato.<li>Fornisce i risultati come testo o voce sintetizzata. |

\* *Il riconoscimento della finalità richiede una sottoscrizione di LUIS.*

## <a name="customize-speech-features"></a>Personalizzare le funzionalità vocali

È possibile usare dati personalizzati per eseguire il training dei modelli sottostanti delle funzionalità Riconoscimento vocale e Sintesi vocale.

|Funzionalità|Modello|Scopo|
|-|-|-|
|Riconoscimento vocale|[Modello acustico](how-to-customize-acoustic-models.md)|Semplifica la trascrizione di parlanti e ambienti specifici, ad esempio con rumori di automobili o in stabilimenti.|
||[Modello linguistico](how-to-customize-language-model.md)|Semplifica la trascrizione di grammatica e vocabolario specifici di settore, ad esempio medicale o IT.|
||[Modello di pronuncia](how-to-customize-pronunciation.md)|Semplifica la trascrizione di abbreviazioni e acronimi, ad esempio "IOU" per "i o u" |
|Sintesi vocale|[Carattere voce](how-to-customize-voice-font.md)|Conferisce una voce unica all'app, grazie al training del modello con esempi di voce umana.|

È possibile usare i modelli personalizzati ovunque vengano usati i modelli standard nelle funzionalità di Riconoscimento vocale o Sintesi vocale dell'app.

## <a name="use-the-speech-service"></a>Usare il servizio Voce

Per semplificare lo sviluppo di applicazioni con funzioni vocali, Microsoft offre [Speech SDK](speech-sdk.md), da usare con il servizio Voce. Speech SDK fornisce API di Riconoscimento vocale e Traduzione vocale native e coerenti per C#, C++ e Java. Se si usa uno di questi linguaggi di sviluppo, Speech SDK semplifica le attività di sviluppo grazie alla gestione automatica dei dettagli della rete.

Il servizio Voce dispone inoltre di un'[API REST](rest-apis.md) che funziona con qualsiasi linguaggio di programmazione in grado di effettuare richieste HTTP. L'interfaccia REST non offre le funzionalità di streaming in tempo reale offerte invece dall'SDK.

|<br>Metodo|Sintesi vocale<br>Riconoscimento vocale|Sintesi vocale<br>Sintesi vocale|Sintesi vocale<br>Traduzione|<br>Descrizione|
|-|-|-|-|-|
|[Speech SDK](speech-sdk.md)|Yes|No |Yes|API native per C#, C++ e Java per semplificare lo sviluppo.|
|[REST](rest-apis.md)|Yes|Yes|No |Una semplice API basata su HTTP che agevola l'aggiunta di funzionalità vocali alle applicazioni.|

### <a name="websockets"></a>Websocket

Il servizio Voce dispone inoltre dei protocolli WebSocket per il Riconoscimento vocale e la Traduzione vocale in streaming. Speech SDK usa questi protocolli per comunicare con il servizio Voce. Usare Speech SDK anziché tentare di implementare comunicazioni WebSocket personalizzate con il servizio Voce.

Se il codice esistente usa Riconoscimento vocale Bing o Traduzione vocale tramite protocolli WebSocket, è possibile aggiornarlo per usare il servizio Voce. I protocolli WebSocket sono compatibili, solo gli endpoint sono diversi.

### <a name="speech-devices-sdk"></a>Speech Devices SDK

[Speech Devices SDK](speech-devices-sdk.md) è una piattaforma hardware e software integrata per gli sviluppatori di dispositivi abilitati ai servizi voce. Il partner per l'hardware fornisce i progetti di riferimento e le unità di sviluppo. Microsoft fornisce un SDK ottimizzato per i dispositivi che sfrutta al meglio tutte le funzionalità dell'hardware.

## <a name="why-move-to-the-speech-service"></a>Perché passare al servizio Voce?

Il servizio Voce offre tutte le funzionalità dell'API Riconoscimento vocale Bing e di altri tre servizi di riconoscimento vocale di Azure, ovvero Riconoscimento vocale personalizzato, Modello voce personalizzato e Traduzione vocale. Invitiamo gli utenti di questi servizi a eseguire la migrazione al servizio Voce.

Il servizio Voce incorpora numerosi aggiornamenti di questi altri servizi, tra cui:

* Offre una maggiore accuratezza del riconoscimento vocale. I modelli usati nel servizio vengono infatti migliorati regolarmente.

* È caratterizzato da una maggiore scalabilità. Il servizio può gestire facilmente più richieste simultanee, con conseguente riduzione della latenza.

* Il servizio Voce usa un modello tariffario basato sul tempo. Per informazioni dettagliate, vedere [Prezzi di Servizi cognitivi - Servizi Voce](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

* Il servizio Voce è disponibile in [più aree](regions.md) per soddisfare le esigenze di clienti in tutto il mondo. È necessaria una [sottoscrizione di Azure](https://docs.microsoft.com/azure/cognitive-services/welcome) per ogni area usata dall'applicazione.

* Un'unica chiave di sottoscrizione del servizio Voce consente di accedere alle funzionalità seguenti. Ogni funzionalità viene misurata separatamente, di conseguenza vengono addebitati solo i consumi delle funzionalità usate.

    * [Riconoscimento vocale](speech-to-text.md)
    * [Riconoscimento vocale personalizzato](https://cris.ai/CustomSpeech)
    * [Sintesi vocale](text-to-speech.md)
    * [Voci personalizzate per Sintesi vocale](https://cris.ai/CustomVoice)
    * [Traduzione vocale](speech-translation.md) (non include la [traduzione testuale](https://docs.microsoft.com/azure/cognitive-services/translator/translator-info-overview))

* La funzione di riconoscimento vocale del servizio Voce si integra con il servizio [Language Understanding Service](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) per fornire il riconoscimento delle finalità del parlante. Con il servizio Voce è anche possibile usare una chiave dell'endpoint LUIS. Per informazioni dettagliate, vedere l'[esercitazione sul riconoscimento delle finalità](how-to-recognize-intents-from-speech-csharp.md).

* Per il riconoscimento vocale non è più necessario specificare una modalità di riconoscimento.

* Il servizio Voce supporta voci a 24 KHz per il riconoscimento vocale, di conseguenza la qualità audio risulta migliorata. All'epoca della stesura di questo documento sono disponibili due voci (solo per inglese Stati Uniti), ovvero `Jessa24kRUS` e `Guy24kRUS`.

* La funzionalità di [trascrizione batch](batch-transcription.md) del servizio Voce consente di trascrivere in modo efficiente volumi elevati di parlato registrato, ad esempio le registrazioni di call center, in modo da poterle analizzare e usare nelle ricerche.

* Quando si usa Speech SDK, non esiste alcun limite temporale alla trascrizione dei dati del riconoscimento vocale.

* [Speech SDK](speech-sdk.md) offre un'API per il servizio Voce utilizzabile in diversi linguaggi di programmazione e ambienti di esecuzione (inclusi Windows 10, piattaforma UWP e .NET Core), facilitando di fatto lo sviluppo in particolare su più piattaforme.

* Il servizio Voce è compatibile con le API REST e il protocollo WebSockets usati da altri servizi di riconoscimento vocale di Azure in modo che sia possibile eseguire facilmente la migrazione di applicazioni client esistenti al servizio Voce.

## <a name="speech-scenarios"></a>Scenari di riconoscimento vocale

Casi d'uso per il servizio Voce:

> [!div class="checklist"]
> * Creare app con attivazione vocale
> * Trascrivere registrazioni dei call center
> * Implementare i bot vocali

### <a name="voice-user-interface"></a>Interfaccia utente vocale

L'input vocale è un ottimo modo per rendere l'app flessibile utilizzabile in vivavoce e rapida da usare. In un'app con il riconoscimento vocale, gli utenti possono semplicemente chiedere le informazioni desiderate.

Se l'app è destinata al pubblico generale, è possibile usare i modelli di riconoscimento vocale predefiniti. Questi consentono di riconoscere un'ampia gamma di parlanti negli ambienti più comuni.

Se l'app è destinata a un dominio specifico, ad esempio medicina o IT, è possibile creare un [modello di lingua](how-to-customize-language-model.md). Il modello può essere usato per insegnare al servizio Voce la terminologia speciale usata dall'app.

Se l'app è destinata a un ambiente rumoroso, come una fabbrica, è possibile creare un [modello acustico](how-to-customize-acoustic-models.md) personalizzato. Questo modello consente al servizio Voce di distinguere meglio la voce dal rumore.

Le procedure iniziali sono molto semplici. Scaricare [Speech SDK](speech-sdk.md) e seguire l'[articolo introduttivo](quickstart-csharp-dotnet-windows.md) pertinente.

### <a name="call-center-transcription"></a>Trascrizioni di call center

Spesso, le registrazioni del call center vengono consultate solo se si verifica un problema con una chiamata. Con il servizio di riconoscimento vocale, è facile trascrivere ogni registrazione in testo. È possibile indicizzare facilmente il testo per la [ricerca full-text](https://docs.microsoft.com/azure/search/search-what-is-azure-search) oppure applicare l'[Analisi del testo](https://docs.microsoft.com/azure/cognitive-services/Text-Analytics/) per rilevare sentiment, lingua e frasi chiave.

Se le registrazioni del call center contengono terminologia specializzata, ad esempio nomi di prodotti o gergo IT, è possibile creare un [modello linguistico](how-to-customize-language-model.md) per insegnare al servizio Voce quel vocabolario specifico. Un [modello acustico](how-to-customize-acoustic-models.md) personalizzato aiuta il servizio di riconoscimento vocale a comprendere le connessioni del telefono non ottimali.

Per altre informazioni su questo scenario, vedere [trascrizione batch](batch-transcription.md) con il servizio riconoscimento vocale.

### <a name="voice-bots"></a>Bot vocali

I [bot](https://dev.botframework.com/) sono un modo sempre più diffuso per connettere gli utenti con le informazioni desiderate e i clienti con le aziende che preferiscono. L'aggiunta di un'interfaccia utente discorsiva per il sito Web o per l'app rende le funzionalità più semplici da trovare e l'accesso più rapido. Con il servizio Voce, la conversazione raggiunge livelli di scorrevolezza straordinari rispondendo effettivamente alle domande poste in modo naturale.

Per aggiungere una personalità univoca a un bot con servizio voce abilitato, è possibile assegnargli una voce propria. La creazione di una voce personalizzata è un processo in due passaggi. Prima di tutto, [registrare](record-custom-voice-samples.md) la voce da usare. Quindi, [inviare le registrazioni](how-to-customize-voice-font.md) insieme a una trascrizione del testo al [portale per la voce personalizzata](https://cris.ai/Home/CustomVoice) del servizio Voce, che farà il resto. Dopo aver creato la voce personalizzate, i passaggi per usarla nell'app sono molto semplici.

## <a name="next-steps"></a>Passaggi successivi

Ottieni una chiave di sottoscrizione per il servizio di riconoscimento vocale.

> [!div class="nextstepaction"]
> [Provare gratuitamente il Servizio di riconoscimento vocale](get-started.md)
