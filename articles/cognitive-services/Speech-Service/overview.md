---
title: Informazioni sul servizio Voce
titleSuffix: Azure Cognitive Services
description: 'Il servizio Voce, parte dei Servizi Cognitivi di Azure, unisce diversi servizi vocali precedentemente disponibili in modo separato: Riconoscimento vocale Bing (che comprende il riconoscimento vocale e la sintesi vocale), Riconoscimento vocale personalizzato e Traduzione vocale.'
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: overview
ms.date: 09/24/2018
ms.author: erhopf
ms.openlocfilehash: ba4204c23f3467ff07940fd6a72464e67604dde1
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2018
ms.locfileid: "49470447"
---
# <a name="what-is-the-speech-service"></a>Informazioni sul servizio Voce


Come gli altri servizi vocali di Azure, il servizio Voce è basato su tecnologie vocali usate in prodotti quali Cortana e Microsoft Office.

Il servizio Voce combina le funzionalità vocali di Azure precedentemente disponibili tramite i servizi [API Riconoscimento vocale Bing](https://docs.microsoft.com/azure/cognitive-services/speech/home), [Traduzione vocale](https://docs.microsoft.com/azure/cognitive-services/translator-speech/), [Riconoscimento vocale personalizzato](https://docs.microsoft.com/azure/cognitive-services/custom-speech-service/cognitive-services-custom-speech-home) e [Voce personalizzata](http://customvoice.ai/). Adesso, con una sottoscrizione è possibile accedere a tutte queste funzionalità.

## <a name="main-speech-service-functions"></a>Funzionalità principali del servizio Voce

Le funzionalità principali del servizio Voce sono Riconoscimento vocale (anche noto come trascrizione), Sintesi vocale e Traduzione vocale.

|Funzione|Funzionalità|
|-|-|
|[Riconoscimento vocale](speech-to-text.md)| <li>Esegue la trascrizione del parlato continuo in tempo reale in testo.<li>Consente di eseguire trascrizioni batch da registrazioni audio. <li>Supporta risultati intermedi, rilevamento della fine del parlato, formattazione automatica del testo e maschera per il contenuto volgare. <li>Può chiamare [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) per derivare la finalità dell'utente dal parlato trascritto.\*|
|[Sintesi vocale](text-to-speech.md)| <li>Converte il testo scritto in un audio che suona naturale. <li>Offre più sessi e/o dialetti per numerose lingue supportate. <li>Supporta l'input di testo normale o il linguaggio SSML (Speech Synthesis Markup Language). |
|[Traduzione vocale](speech-translation.md)| <li>Traduce l'audio in streaming quasi in tempo reale.<li> Può anche elaborare audio registrato.<li>Fornisce i risultati come testo o voce sintetizzata. |


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


## <a name="speech-scenarios"></a>Scenari di riconoscimento vocale

Casi d'uso per il servizio Voce:

> [!div class="checklist"]
> * Creare app con attivazione vocale
> * Trascrivere registrazioni dei call center
> * Implementare i bot vocali

### <a name="voice-user-interface"></a>Interfaccia utente vocale

L'input vocale è un ottimo modo per rendere l'app flessibile, da utilizzare a mani libere e in modo rapido. In un'app con il riconoscimento vocale, gli utenti possono semplicemente chiedere le informazioni desiderate.

Se l'app è destinata al pubblico generale, è possibile usare i modelli di riconoscimento vocale predefiniti. Questi consentono di riconoscere un'ampia gamma di parlanti negli ambienti più comuni.

Se l'app è destinata a un dominio specifico, ad esempio medicina o IT, è possibile creare un [modello di lingua](how-to-customize-language-model.md). Il modello può essere usato per insegnare al servizio Voce la terminologia speciale usata dall'app.

Se l'app è destinata a un ambiente rumoroso, come una fabbrica, è possibile creare un [modello acustico](how-to-customize-acoustic-models.md) personalizzato. Questo modello consente al servizio Voce di distinguere meglio la voce dal rumore.

### <a name="call-center-transcription"></a>Trascrizioni di call center

Spesso, le registrazioni del call center vengono consultate solo se si verifica un problema con una chiamata. Con il servizio Voce, è facile trascrivere ogni registrazione in testo. È possibile indicizzare facilmente il testo per la [ricerca full-text](https://docs.microsoft.com/azure/search/search-what-is-azure-search) oppure applicare l'[Analisi del testo](https://docs.microsoft.com/azure/cognitive-services/Text-Analytics/) per rilevare sentiment, lingua e frasi chiave.

Se le registrazioni del call center contengono terminologia specializzata, ad esempio nomi di prodotti o gergo IT, è possibile creare un [modello linguistico](how-to-customize-language-model.md) per insegnare al servizio Voce quel vocabolario specifico. Un [modello acustico](how-to-customize-acoustic-models.md) personalizzato aiuta il servizio Voce a comprendere le connessioni del telefono non ottimali.

Per altre informazioni su questo scenario, vedere [trascrizione batch](batch-transcription.md) con il servizio Voce.

### <a name="voice-bots"></a>Bot vocali

I [bot](https://dev.botframework.com/) sono un modo sempre più diffuso per connettere gli utenti con le informazioni desiderate e i clienti con le aziende che preferiscono. L'aggiunta di un'interfaccia utente discorsiva per il sito Web o per l'app rende le funzionalità più semplici da trovare e l'accesso più rapido. Con il servizio Voce, la conversazione raggiunge livelli di scorrevolezza straordinari rispondendo effettivamente alle domande poste in modo naturale.

Per aggiungere una personalità univoca a un bot con servizio voce abilitato, è possibile assegnargli una voce propria. La creazione di una voce personalizzata è un processo in due passaggi. Prima di tutto, [registrare](record-custom-voice-samples.md) la voce da usare. Quindi, [inviare le registrazioni](how-to-customize-voice-font.md) insieme a una trascrizione del testo al [portale per la voce personalizzata](https://cris.ai/Home/CustomVoice) del servizio Voce, che farà il resto. Dopo aver creato la voce personalizzate, i passaggi per usarla nell'app sono molto semplici.

## <a name="next-steps"></a>Passaggi successivi

Ottieni una chiave di sottoscrizione per il servizio Voce.

> [!div class="nextstepaction"]
> [Provare gratuitamente il servizio Voce](get-started.md)
