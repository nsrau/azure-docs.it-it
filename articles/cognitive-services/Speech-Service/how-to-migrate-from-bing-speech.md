---
title: Eseguire la migrazione da Riconoscimento vocale Bing al Servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Informazioni sulle differenze tra Riconoscimento vocale Bing e il Servizio di riconoscimento vocale dal punto di vista dello sviluppatore ed eseguire la migrazione dell'applicazione per l'uso del Servizio di riconoscimento vocale.
services: cognitive-services
author: wsturman
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: gracez
ms.openlocfilehash: baf9b9cd9b3f57c1d708dd404d59c036df6c169f
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2018
ms.locfileid: "49466648"
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

Il Servizio di riconoscimento vocale è abbastanza allineato dal punto di vista di funzionalità, piattaforma e linguaggio di programmazione con il Riconoscimento vocale Bing, a parte le differenze indicate di seguito.

Funzionalità | Riconoscimento vocale Bing | Servizio di riconoscimento vocale | Dettagli
-|-|-|-
C++ SDK | :heavy_minus_sign: | :heavy_check_mark: | Il Servizio di riconoscimento vocale supporta Windows e Linux
SDK per Java | :heavy_check_mark: | :heavy_check_mark: | Il Servizio di riconoscimento vocale supporta i dispositivi Android e di riconoscimento vocale
SDK per C# | :heavy_check_mark: | :heavy_check_mark: | Il Servizio di riconoscimento vocale supporta Windows 10, la piattaforma UWP e .NET Standard 2.0
Riconoscimento vocale continuo | 10 minuti | Senza limiti, con SDK | Sia il protocollo WebSocket del Servizio di riconoscimento vocale sia quello di Riconoscimento vocale Bing supportano fino a 10 minuti per chiamata. Tuttavia Speech SDK si riconnette automaticamente in caso di timeout o disconnessione.
Risultati intermedi o parziali | :heavy_check_mark: | :heavy_check_mark: | Con il protocollo WebSocket o SDK
Modelli conversione voce/testo personalizzati | :heavy_check_mark: | :heavy_check_mark: | Riconoscimento vocale Bing richiede una sottoscrizione separata al Riconoscimento vocale personalizzato
Caratteri voce personalizzati | :heavy_check_mark: | :heavy_check_mark: | Riconoscimento vocale Bing richiede una sottoscrizione separata al Riconoscimento vocale personalizzato
Voci 24 kHz | :heavy_minus_sign: | :heavy_check_mark: 
Riconoscimento finalità voce | Richiede una chiamata separata all'API LUIS | Integrato, con SDK |  È possibile usare una chiave LUIS con il Servizio di riconoscimento vocale.
Riconoscimento delle finalità semplice | :heavy_minus_sign: | :heavy_check_mark: 
Trascrizione in batch di file audio lunghi | :heavy_minus_sign: | :heavy_check_mark:
Modalità di riconoscimento | Manuale tramite l'URI dell'endpoint | Automatico | La modalità di riconoscimento non è disponibile nel Servizio di riconoscimento vocale
Località dell'endpoint | Globale | Regionale | Gli endpoint a livello di area migliorano la latenza. Microsoft sta considerando di introdurre un endpoint globale per il Servizio di riconoscimento vocale.
API REST | :heavy_check_mark: | :heavy_check_mark: | L'API REST del Servizio di riconoscimento vocale è compatibile con Riconoscimento vocale Bing (endpoint diverso). Le API REST supportano la sintesi vocale e funzionalità limitate di riconoscimento vocale.
Protocolli WebSocket | :heavy_check_mark: | :heavy_check_mark: | L'API WebSocket del Servizio di riconoscimento vocale è compatibile con Riconoscimento vocale Bing (endpoint diverso). Se possibile, eseguire la migrazione a Speech SDK per semplificare il codice.
Chiamate API da servizio a servizio | :heavy_check_mark: | :heavy_minus_sign: | Fornito in Riconoscimento vocale Bing tramite la libreria di servizio di C#. 
SDK open source | :heavy_check_mark: | :heavy_minus_sign: |

Il Servizio di riconoscimento vocale usa un modello di determinazione dei prezzi basato sul tempo, anziché un modello basato sulle transazioni. Per informazioni dettagliate, vedere [Prezzi di Servizi cognitivi - Servizi Voce](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="migration-strategies"></a>Strategie di migrazione

Se l'organizzazione dispone di applicazioni in fase di sviluppo o in produzione che usano un'API Riconoscimento vocale Bing, è necessario aggiornarle appena possibile per l'uso del Servizio di riconoscimento vocale. Per informazioni sugli SDK disponibili, esempi di codice ed esercitazioni, vedere la [documentazione del Servizio di riconoscimento vocale](index.yml).

Le [API REST](rest-apis.md) del Servizio di riconoscimento vocale sono compatibili con le API Riconoscimento vocale Bing. Se attualmente si usa l'API REST Riconoscimento vocale Bing, basta modificare l'endpoint REST e passare a una chiave di sottoscrizione del Servizio di riconoscimento vocale.

I protocolli WebSocket del Servizio di riconoscimento vocale sono compatibili con quelli utilizzati da Riconoscimento vocale Bing. Per le nuove attività di sviluppo si consiglia di usare l'SDK del Servizio di riconoscimento vocale anziché WebSocket e si consiglia anche di eseguire la migrazione del codice esistente verso l'SDK. Tuttavia, come con le API REST, il codice esistente che usa Riconoscimento vocale Bing tramite WebSocket richiede solo una modifica dell'endpoint e una chiave di aggiornamento.

Se si usa una libreria client di Riconoscimento vocale Bing per un linguaggio di programmazione specifico, per poter eseguire la migrazione verso [Speech SDK](speech-sdk.md) sarà necessario apportare modifiche all'applicazione perché l'API è diversa. Speech SDK può semplificare il codice permettendo anche di accedere alle nuove funzionalità.

Speech SDK supporta attualmente C# (Windows 10, piattaforma UWP, .NET Standard), Java (dispositivi Android e personalizzati), Objective C (iOS), C++ (Windows e Linux) e JavaScript. Le API sono simili in tutte le piattaforme e ciò semplifica lo sviluppo multipiattaforma.

Il Servizio di riconoscimento vocale attualmente non offre un endpoint globale. È necessario determinare se l'applicazione funzionerà in modo efficiente usando un singolo endpoint a livello di area per tutto il traffico. In caso contrario, usare la georilevazione per determinare l'endpoint più efficiente. Sarà necessaria una sottoscrizione al Servizio di riconoscimento vocale diversa in ogni area utilizzata.

Se l'applicazione usa connessioni di lunga durata e non può usare un SDK disponibile, è possibile usare una connessione WebsSocket e gestire il limite di timeout di 10 minuti eseguendo la riconnessione nel momento opportuno.

Per iniziare a usare Speech SDK:

1. Scaricare [Speech SDK](speech-sdk.md).
1. Completare le [guide introduttive](quickstart-csharp-dotnet-windows.md) e le [esercitazioni](how-to-recognize-intents-from-speech-csharp.md) del Servizio di riconoscimento vocale ed esaminare gli [esempi di codice](samples.md) relativi per acquisire familiarità con le nuove API.
1. Aggiornare l'applicazione client per l'uso del Servizio di riconoscimento vocale e delle API.

## <a name="support"></a>Supporto

I clienti di Riconoscimento vocale Bing devono contattare il supporto tecnico aprendo un [ticket di supporto](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). È anche possibile contattare Microsoft se il caso richiede un [piano di supporto tecnico](https://azure.microsoft.com/support/plans/).

Per ottenere assistenza sul Servizio di riconoscimento vocale, l'SDK e le API, visitare la [pagina del supporto tecnico](support.md) del Servizio di riconoscimento vocale.

## <a name="next-steps"></a>Passaggi successivi

* [Provare gratuitamente il Servizio di riconoscimento vocale](get-started.md)
* [Avvio rapido: Riconoscimento vocale in un'applicazione della piattaforma UWP con Speech SDK](quickstart-csharp-uwp.md)

## <a name="see-also"></a>Vedere anche 
* [Note sulla versione del Servizio di riconoscimento vocale](releasenotes.md)
* [Informazioni sul Servizio di riconoscimento vocale](overview.md)
* [Documentazione sul Servizio di riconoscimento vocale e Speech SDK](speech-sdk.md#get-the-sdk)
