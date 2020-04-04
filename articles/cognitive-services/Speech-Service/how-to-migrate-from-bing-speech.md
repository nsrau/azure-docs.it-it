---
title: Eseguire la migrazione dal servizio di riconoscimento vocale Bing
titleSuffix: Azure Cognitive Services
description: Informazioni su come eseguire la migrazione da una sottoscrizione di Bing al servizio di riconoscimento vocale esistente al servizio di riconoscimento vocale da Servizi cognitivi di Azure.Learn how to migrate from an existing Bing Speech subscription to the Speech service from Azure Cognitive Services.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: nitinme
ms.openlocfilehash: 7b78bdb070cdf1364fe7fbdc75f175be7ce145ff
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656457"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>Eseguire la migrazione da Bing Speech al servizio di riconoscimento vocale

Usare questo articolo per eseguire la migrazione delle applicazioni dall'API di riconoscimento vocale Bing al servizio di riconoscimento vocale.

Questo articolo descrive le differenze tra le API di riconoscimento vocale Bing e il servizio di riconoscimento vocale e suggerisce strategie per la migrazione delle applicazioni. La chiave di sottoscrizione dell'API Bing Speech non funziona con il servizio di riconoscimento vocale. è necessario un nuovo abbonamento al servizio di riconoscimento vocale.

Una singola chiave di sottoscrizione del servizio di riconoscimento vocale concede l'accesso alle funzionalità seguenti. Ogni funzionalità viene misurata separatamente, di conseguenza vengono addebitati solo i consumi delle funzionalità usate.

* [Sintesi vocale](speech-to-text.md)
* [Riconoscimento vocale personalizzato](https://cris.ai)
* [Sintesi vocale](text-to-speech.md)
* [Voci personalizzate per Sintesi vocale](how-to-customize-voice-font.md)
* [Traduzione vocale](speech-translation.md) (non include la [traduzione testuale](../translator/translator-info-overview.md))

[Speech SDK](speech-sdk.md) sostituisce le funzioni delle librerie client di Riconoscimento vocale Bing, ma usa un'API diversa.

## <a name="comparison-of-features"></a>Confronto delle funzionalità

Il servizio di riconoscimento vocale è in gran parte simile a Bing Speech, con le differenze seguenti.

| Funzionalità | Riconoscimento vocale Bing | Servizio Voce | Dettagli |
|--|--|--|--|
| SDK per C# | :heavy_check_mark: | :heavy_check_mark: | Il servizio di riconoscimento vocale supporta Windows 10, UWP (Universal Windows Platform) e .NET Standard 2.0. |
| C++ SDK | :heavy_minus_sign: | :heavy_check_mark: | Il servizio di riconoscimento vocale supporta Windows e Linux.Speech service supports Windows and Linux. |
| SDK per Java | :heavy_check_mark: | :heavy_check_mark: | Il servizio di riconoscimento vocale supporta i dispositivi Android e Speech. |
| Riconoscimento vocale continuo | 10 minuti | Senza limiti, con SDK | Entrambi i protocolli WebSockets del servizio di riconoscimento vocale Bing supportano fino a 10 minuti per chiamata. Tuttavia Speech SDK si riconnette automaticamente in caso di timeout o disconnessione. |
| Risultati intermedi o parziali | :heavy_check_mark: | :heavy_check_mark: | Con il protocollo WebSocket o SDK. |
| Modelli conversione voce/testo personalizzati | :heavy_check_mark: | :heavy_check_mark: | Riconoscimento vocale Bing richiede una sottoscrizione separata a Riconoscimento vocale personalizzato. |
| Caratteri voce personalizzati | :heavy_check_mark: | :heavy_check_mark: | Riconoscimento vocale Bing richiede una sottoscrizione separata al riconoscimento vocale personalizzato. |
| Voci a 24 kHz | :heavy_minus_sign: | :heavy_check_mark: |
| Riconoscimento finalità voce | Richiede una chiamata separata all'API LUIS | Integrato, con SDK | È possibile utilizzare una chiave LUIS con il servizio di riconoscimento vocale. |
| Riconoscimento delle finalità semplice | :heavy_minus_sign: | :heavy_check_mark: |
| Trascrizione in batch di file audio lunghi | :heavy_minus_sign: | :heavy_check_mark: |
| Modalità di riconoscimento | Manuale tramite l'URI dell'endpoint | Automatico | La modalità di riconoscimento non è disponibile nel servizio di riconoscimento vocale. |
| Località dell'endpoint | Global | Regionale | Gli endpoint a livello di area migliorano la latenza. |
| API REST | :heavy_check_mark: | :heavy_check_mark: | Le API REST del servizio di riconoscimento vocale sono compatibili con Bing Speech (endpoint diverso). Le API REST supportano la sintesi vocale e funzionalità limitate di riconoscimento vocale. |
| Protocolli WebSocket | :heavy_check_mark: | :heavy_check_mark: | Il servizio di riconoscimento vocale WebSockets API è compatibile con Bing Speech (endpoint diverso). Se possibile, eseguire la migrazione a Speech SDK per semplificare il codice. |
| Chiamate API da servizio a servizio | :heavy_check_mark: | :heavy_minus_sign: | Fornito in Riconoscimento vocale Bing tramite la libreria di servizio di C#. |
| SDK open source | :heavy_check_mark: | :heavy_minus_sign: |

Il servizio di riconoscimento vocale utilizza un modello di determinazione dei prezzi basato sul tempo (anziché un modello basato su transazioni). Per informazioni [dettagliate, vedere Prezzi del servizio di riconoscimento vocale.](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)

## <a name="migration-strategies"></a>Strategie di migrazione

Se l'utente o l'organizzazione dispone di applicazioni in fase di sviluppo o produzione che usano un'API di riconoscimento vocale Bing, è consigliabile aggiornarle per usare il servizio di riconoscimento vocale il prima possibile. Vedere la documentazione del [servizio di riconoscimento vocale](index.yml) per SDK, esempi di codice ed esercitazioni disponibili.

Le [API REST](rest-apis.md) del servizio di riconoscimento vocale sono compatibili con le API Bing Speech. Se si usano attualmente le API REST di Bing Speech, è sufficiente modificare l'endpoint REST e passare a una chiave di sottoscrizione del servizio di riconoscimento vocale.

Anche i protocolli WebSockets del servizio di riconoscimento vocale sono compatibili con quelli utilizzati da Bing Speech. Per le nuove attività di sviluppo è consigliabile usare Speech SDK anziché i protocolli WebSocket. È opportuno anche eseguire la migrazione di codice esistente nell'SDK. Tuttavia, come con le API REST, il codice esistente che usa Riconoscimento vocale Bing tramite WebSocket richiede solo una modifica dell'endpoint e una chiave di aggiornamento.

Se si usa una libreria client di Riconoscimento vocale Bing per un linguaggio di programmazione specifico, per eseguire la migrazione a [Speech SDK](speech-sdk.md) è necessario apportare modifiche all'applicazione perché l'API è diversa. Speech SDK può semplificare il codice permettendo anche di accedere alle nuove funzionalità. Speech SDK è disponibile in un'ampia gamma di linguaggi di programmazione. Le API sono simili in tutte le piattaforme e ciò semplifica lo sviluppo multipiattaforma.

Il servizio di riconoscimento vocale non offre un endpoint globale. Determinare se l'applicazione funziona in modo efficiente quando usa un singolo endpoint a livello di area per tutto il traffico. In caso contrario, usare la georilevazione per determinare l'endpoint più efficiente. È necessaria una sottoscrizione al servizio di riconoscimento vocale separata in ogni area in uso.

Se l'applicazione usa connessioni di lunga durata e non può usare un SDK disponibile, è possibile usare una connessione WebSocket. Gestire il limite di timeout di 10 minuti eseguendo la riconnessione nei momenti appropriati.

Per iniziare a usare Speech SDK:

1. Scaricare [Speech SDK](speech-sdk.md).
1. Utilizzare le [guide introduttive](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet) e [le esercitazioni](how-to-recognize-intents-from-speech-csharp.md)del servizio di riconoscimento vocale. Analizzare anche gli [esempi di codice](samples.md) per acquisire familiarità con le nuove API.
1. Aggiornare l'applicazione per utilizzare il servizio di riconoscimento vocale.

## <a name="support"></a>Supporto

I clienti di Riconoscimento vocale Bing devono contattare il supporto tecnico aprendo un [ticket di supporto](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). È anche possibile contattare Microsoft se il caso richiede un [piano di supporto tecnico](https://azure.microsoft.com/support/plans/).

Per il servizio di riconoscimento vocale, l'SDK e il supporto API, visita la [pagina di supporto](support.md)del servizio di riconoscimento vocale.

## <a name="next-steps"></a>Passaggi successivi

* [Prova il servizio di riconoscimento vocale gratuitamente](get-started.md)
* [Avvio rapido: Riconoscimento vocale in un'applicazione piattaforma UWP con Speech SDK](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)

## <a name="see-also"></a>Vedere anche
* [Note sulla versione del servizio di riconoscimento vocale](releasenotes.md)
* [Che cos'è il servizio di riconoscimento vocale](overview.md)
* [Servizio di riconoscimento vocale e documentazione di Speech SDK](speech-sdk.md#get-the-speech-sdk)
