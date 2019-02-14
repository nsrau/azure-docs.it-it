---
title: Eseguire la migrazione da Riconoscimento vocale Bing al Servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Informazioni sulle differenze tra Riconoscimento vocale Bing e il servizio Voce dal punto di vista dello sviluppatore e su come eseguire la migrazione dell'applicazione per l'uso del servizio Voce.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: gracez
ms.openlocfilehash: 73df93345cf73939f33afedfd655cef847d895dc
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55863568"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>Eseguire la migrazione da Riconoscimento vocale Bing al Servizio di riconoscimento vocale

Usare le informazioni in questo articolo per eseguire la migrazione delle applicazioni dall'API Riconoscimento vocale Bing al Servizio di riconoscimento vocale.

Questo articolo descrive le differenze tra le API Riconoscimento vocale Bing e il Servizio di riconoscimento vocale e suggerisce strategie per la migrazione delle applicazioni. La chiave di sottoscrizione dell'API Riconoscimento vocale Bing non verrà accettata dal Servizio di riconoscimento vocale, ma è necessaria una nuova sottoscrizione al Servizio di riconoscimento vocale.

Un'unica chiave di sottoscrizione del servizio Voce consente di accedere alle funzionalità seguenti. Ogni funzionalità viene misurata separatamente, di conseguenza vengono addebitati solo i consumi delle funzionalità usate.

* [Riconoscimento vocale](speech-to-text.md)
* [Riconoscimento vocale personalizzato](https://cris.ai)
* [Sintesi vocale](text-to-speech.md)
* [Voci personalizzate per Sintesi vocale](how-to-customize-voice-font.md)
* [Traduzione vocale](speech-translation.md) (non include la [traduzione testuale](../translator/translator-info-overview.md))

[Speech SDK](speech-sdk.md) sostituisce le funzioni delle librerie client di Riconoscimento vocale Bing, ma usa un'API diversa.

## <a name="comparison-of-features"></a>Confronto delle funzionalità

Il servizio Voce è molto simile a Riconoscimento vocale Bing, con le differenze seguenti.

Funzionalità | Riconoscimento vocale Bing | Servizio di riconoscimento vocale | Dettagli
-|-|-|-
C++ SDK | :heavy_minus_sign: | :heavy_check_mark: | Il servizio Voce supporta Windows e Linux.
SDK per Java | :heavy_check_mark: | :heavy_check_mark: | Il servizio Voce supporta i dispositivi Android e di riconoscimento vocale.
SDK per C# | :heavy_check_mark: | :heavy_check_mark: | Il servizio Voce supporta Windows 10, la piattaforma UWP (Universal Windows Platform) e .NET Standard 2.0.
Riconoscimento vocale continuo | 10 minuti | Senza limiti, con SDK | Sia il protocollo WebSocket del Servizio di riconoscimento vocale sia quello di Riconoscimento vocale Bing supportano fino a 10 minuti per chiamata. Tuttavia Speech SDK si riconnette automaticamente in caso di timeout o disconnessione.
Risultati intermedi o parziali | :heavy_check_mark: | :heavy_check_mark: | Con il protocollo WebSocket o SDK.
Modelli conversione voce/testo personalizzati | :heavy_check_mark: | :heavy_check_mark: | Riconoscimento vocale Bing richiede una sottoscrizione separata a Riconoscimento vocale personalizzato.
Caratteri voce personalizzati | :heavy_check_mark: | :heavy_check_mark: | Riconoscimento vocale Bing richiede una sottoscrizione separata al riconoscimento vocale personalizzato.
Voci 24 kHz | :heavy_minus_sign: | :heavy_check_mark: 
Riconoscimento finalità voce | Richiede una chiamata separata all'API LUIS | Integrato, con SDK |  È possibile usare una chiave LUIS con il servizio Voce.
Riconoscimento delle finalità semplice | :heavy_minus_sign: | :heavy_check_mark: 
Trascrizione in batch di file audio lunghi | :heavy_minus_sign: | :heavy_check_mark:
Modalità di riconoscimento | Manuale tramite l'URI dell'endpoint | Automatico | La modalità di riconoscimento non è disponibile nel servizio Voce.
Località dell'endpoint | Globale | Regionale | Gli endpoint a livello di area migliorano la latenza.
API REST | :heavy_check_mark: | :heavy_check_mark: | L'API REST del Servizio di riconoscimento vocale è compatibile con Riconoscimento vocale Bing (endpoint diverso). Le API REST supportano la sintesi vocale e funzionalità limitate di riconoscimento vocale.
Protocolli WebSocket | :heavy_check_mark: | :heavy_check_mark: | L'API WebSocket del Servizio di riconoscimento vocale è compatibile con Riconoscimento vocale Bing (endpoint diverso). Se possibile, eseguire la migrazione a Speech SDK per semplificare il codice.
Chiamate API da servizio a servizio | :heavy_check_mark: | :heavy_minus_sign: | Fornito in Riconoscimento vocale Bing tramite la libreria di servizio di C#. 
SDK open source | :heavy_check_mark: | :heavy_minus_sign: |

Il Servizio di riconoscimento vocale usa un modello di determinazione dei prezzi basato sul tempo, anziché un modello basato sulle transazioni. Per informazioni dettagliate, vedere [Prezzi di Servizi cognitivi - Servizi Voce](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="migration-strategies"></a>Strategie di migrazione

Se l'organizzazione dispone di applicazioni in fase di sviluppo o in produzione che usano un'API Riconoscimento vocale Bing, è necessario aggiornarle appena possibile per l'uso del Servizio di riconoscimento vocale. Per informazioni sugli SDK disponibili, esempi di codice ed esercitazioni, vedere la [documentazione del Servizio di riconoscimento vocale](index.yml).

Le [API REST](rest-apis.md) del Servizio di riconoscimento vocale sono compatibili con le API Riconoscimento vocale Bing. Se attualmente si usa l'API REST Riconoscimento vocale Bing, è sufficiente modificare l'endpoint REST e passare a una chiave di sottoscrizione al servizio Voce.

I protocolli WebSocket del Servizio di riconoscimento vocale sono compatibili con quelli utilizzati da Riconoscimento vocale Bing. Per le nuove attività di sviluppo è consigliabile usare l'SDK del servizio Voce anziché i protocolli WebSocket. È opportuno anche eseguire la migrazione di codice esistente nell'SDK. Tuttavia, come con le API REST, il codice esistente che usa Riconoscimento vocale Bing tramite WebSocket richiede solo una modifica dell'endpoint e una chiave di aggiornamento.

Se si usa una libreria client di Riconoscimento vocale Bing per un linguaggio di programmazione specifico, per eseguire la migrazione a [Speech SDK](speech-sdk.md) è necessario apportare modifiche all'applicazione perché l'API è diversa. Speech SDK può semplificare il codice permettendo anche di accedere alle nuove funzionalità.

Speech SDK supporta attualmente C# (Windows 10, piattaforma UWP, .NET Standard), Java (dispositivi Android e personalizzati), Objective C (iOS), C++ (Windows e Linux) e JavaScript. Le API sono simili in tutte le piattaforme e ciò semplifica lo sviluppo multipiattaforma.

Il Servizio di riconoscimento vocale attualmente non offre un endpoint globale. Determinare se l'applicazione funziona in modo efficiente quando usa un singolo endpoint a livello di area per tutto il traffico. In caso contrario, usare la georilevazione per determinare l'endpoint più efficiente. È necessaria una sottoscrizione distinta al servizio Voce in ogni area usata.

Se l'applicazione usa connessioni di lunga durata e non può usare un SDK disponibile, è possibile usare una connessione WebSocket. Gestire il limite di timeout di 10 minuti eseguendo la riconnessione nei momenti appropriati.

Per iniziare a usare Speech SDK:

1. Scaricare [Speech SDK](speech-sdk.md).
1. Esaminare le [guide introduttive](quickstart-csharp-dotnet-windows.md) e le [esercitazioni](how-to-recognize-intents-from-speech-csharp.md) del servizio Voce. Analizzare anche gli [esempi di codice](samples.md) per acquisire familiarità con le nuove API.
1. Aggiornare l'applicazione client per l'uso del servizio Voce e delle API.

## <a name="support"></a>Supporto

I clienti di Riconoscimento vocale Bing devono contattare il supporto tecnico aprendo un [ticket di supporto](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). È anche possibile contattare Microsoft se il caso richiede un [piano di supporto tecnico](https://azure.microsoft.com/support/plans/).

Per ottenere assistenza sul Servizio di riconoscimento vocale, l'SDK e le API, visitare la [pagina del supporto tecnico](support.md) del Servizio di riconoscimento vocale.

## <a name="next-steps"></a>Passaggi successivi

* [Provare gratuitamente il Servizio di riconoscimento vocale](get-started.md)
* [Avvio rapido: Riconoscere i contenuti vocali in un'app UWP con Speech SDK](quickstart-csharp-uwp.md)

## <a name="see-also"></a>Vedere anche 
* [Note sulla versione del Servizio di riconoscimento vocale](releasenotes.md)
* [Informazioni sul servizio Voce](overview.md)
* [Documentazione sul Servizio di riconoscimento vocale e Speech SDK](speech-sdk.md#get-the-sdk)
