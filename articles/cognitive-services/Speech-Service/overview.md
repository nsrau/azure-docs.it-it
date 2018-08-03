---
title: Informazioni sul Servizio di riconoscimento vocale (anteprima) | Microsoft Docs
description: 'Il servizio Voce, parte dei Servizi Cognitivi Microsoft, unisce diversi servizi vocali di Azure precedentemente disponibili in modo separato: Riconoscimento vocale Bing (che comprende il riconoscimento vocale e la sintesi vocale), Riconoscimento vocale personalizzato e Traduzione vocale.'
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: adfc854fc24b9e285c405f3038a21ec84cd2f4c2
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989336"
---
# <a name="what-is-the-speech-service-preview"></a>Informazioni sul Servizio di riconoscimento vocale (anteprima)

Il servizio Voce, parte dei Servizi Cognitivi Microsoft, unisce diversi servizi vocali di Azure precedentemente disponibili in modo separato: Riconoscimento vocale Bing (che comprende il riconoscimento vocale e la sintesi vocale), Riconoscimento vocale personalizzato e Traduzione vocale. Come i suoi precursori, il servizio Voce si basa sulle tecnologie utilizzate in altri prodotti Microsoft, tra cui Cortana e Microsoft Office.

> [!NOTE]
> Il servizio Voce è attualmente disponibile in anteprima pubblica. Si consiglia di consultare regolarmente questa pagina per aggiornamenti della documentazione, esempi di codici aggiuntivi e altro.

Con una sola sottoscrizione, il servizio Voce unificato offre agli sviluppatori un modo semplice per dotare le loro applicazioni di potenti funzioni abilitate per la sintesi vocale. Le applicazioni possono ora offrire comandi vocali, trascrizione, dettatura, sintesi vocale e traduzione.

|Funzione|DESCRIZIONE|
|-|-|
|Riconoscimento vocale|Converte il parlato umano continuo in testo che può essere utilizzato come input per l'applicazione. Può essere integrato con il servizio [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) per captare la finalità dell'utente dalle parole.|
|Sintesi vocale|Converte il testo in file audio di sintesi vocale naturale.|
|Traduzione&nbsp;vocale|Fornisce traduzioni vocali in altre lingue, con testo o output di testo.|

## <a name="using-the-speech-service"></a>Utilizzo del Servizio di riconoscimento vocale

Il servizio di riconoscimento vocale viene reso disponibile in due modi. [L'SDK](speech-sdk.md) elimina i dettagli dei protocolli di rete. L’[API REST](rest-apis.md) funziona con qualsiasi linguaggio di programmazione, ma non offre tutte le funzioni offerte dall’SDK.

|<br>Metodo|Sintesi vocale<br>Riconoscimento vocale|Sintesi vocale<br>Sintesi vocale|Sintesi vocale<br>Traduzione|<br>DESCRIZIONE|
|-|-|-|-|-|
|[SDK](speech-sdk.md)|sì|No |sì|Librerie per determinati linguaggi di programmazione che semplificano lo sviluppo.|
|[REST](rest-apis.md)|sì|sì|No |Un'API semplice basato su HTTP che rende più facile aggiungere comandi vocali all'applicazione.|

## <a name="speech-to-text"></a>Riconoscimento vocale

Il servizio di [Riconoscimento vocale](speech-to-text.md) (STT) trascrive in API i flussi audio in testo che l'applicazione può accettare come input. L'applicazione può quindi, ad esempio, inserire il testo in un documento o agire su di esso come comando.

Speech to Text è stato ottimizzato separatamente per gli scenari interattivi, di conversazione e di dettatura. Di seguito sono riportati casi d'uso comuni per l’API di riconoscimento vocale. 

* Riconoscere un'espressione breve, ad esempio un comando, senza risultati temporanei
* Trascrivere un'espressione lunga e registrata in precedenza, ad esempio un messaggio vocale
* Trascrivere il parlato in tempo reale, con risultati parziali, per la dettatura
* Determinare le finalità degli utenti in base a una richiesta in linguaggio naturale vocale

L’API di riconoscimento vocale supporta la trascrizione vocale interattiva con risultati in tempo reale continui e temporanei di riconoscimento. Supporta anche il rilevamento della fine del discorso, l'uso automatico opzionale di lettere maiuscole e minuscole e della punteggiatura, il mascheramento di contenuto volgare e la normalizzazione del testo.

Il servizio consente di personalizzare i modelli di riconoscimento vocale acustici e linguistici per vocabolari specifici, ambienti rumorosi e per i diversi modi di parlare.

## <a name="text-to-speech"></a>Sintesi vocale

La [Sintesi vocale](text-to-speech.md) (TTS) converte tramite API il testo normale in parlato dal suono naturale che viene trasmesso all'applicazione in un file audio. Sono disponibili più voci, che variano per genere o accento, per molte lingue supportate.

L'API supporta tag [Speech-Synthesis-Markup-Language (SSML)](speech-synthesis-markup.md) che consentono di specificare la pronuncia fonetica esatta per le parole complesse. Il linguaggio SSML può anche indicare le caratteristiche del parlato (tra cui enfasi, velocità, volume, genere e intonazione) direttamente nel testo.

Di seguito sono riportati casi d'uso comuni per l'API di Sintesi vocale.

* Output vocale come output alternativo della schermata per utenti ipovedenti
* Comandi vocali per le applicazioni usate in auto, come i navigatori
* Interfacce utente di conversazione che interagiscono con l'API di Riconoscimento vocale

Se è necessario un sottolinguaggio non supportato o una voce univoca per l'applicazione, l'API di sintesi vocale supporta [modelli vocali personalizzati](how-to-customize-voice-font.md).

## <a name="speech-translation"></a>Traduzione vocale

L’API di [traduzione vocale](speech-translation.md) può essere utilizzata per traslare i flussi audio in tempo quasi reale o elaborare la voce registrata. Con la traduzione vocale in streaming, il servizio restituisce risultati temporanei che possono essere visualizzati all'utente per indicare lo stato di avanzamento della traduzione. I risultati possono essere restituiti sia in formato testuale che vocale.

I casi d'uso della traduzione vocale includono:

* Implementare un'app per dispositivi mobili o un dispositivo di traduzione delle conversazioni per coloro che viaggiano 
* Fornire traduzioni automatiche per la sottotitolazione di registrazioni audio e video

## <a name="speech-devices-sdk"></a>Speech Devices SDK

Con l'introduzione del Servizio di riconoscimento vocale unificato, Microsoft e i suoi partner offrono una piattaforma hardware/software integrata ottimizzata per lo sviluppo di dispositivi con funzioni vocali, ovvero [Speech Devices SDK](speech-devices-sdk.md). Questo SDK è adatto per lo sviluppo di dispositivi vocali intelligenti per tutti i tipi di applicazioni.

Speech Devices SDK consente di creare dispositivi per il proprio ambiente con una parola di attivazione personalizzata, in modo che la parola chiave che attiva l'acquisizione audio sia esclusiva del proprio marchio. Offre inoltre un'eccellente elaborazione dell'audio di sorgenti multicanale per un riconoscimento vocale più accurato, con soppressione del rumore, voci in campo lontano e beamforming.

L'SDK si basa su Web Socket che usano la porta 443.

## <a name="next-steps"></a>Passaggi successivi

Ottenere una versione di prova gratuita della chiave di sottoscrizione per il servizio Voce.

> [!div class="nextstepaction"]
> [Provare gratuitamente il Servizio di riconoscimento vocale](get-started.md)
