---
title: Eseguire la migrazione da riconoscimento vocale Bing di servizi di riconoscimento vocale di Azure
titleSuffix: Azure Cognitive Services
description: Informazioni su come eseguire la migrazione da una sottoscrizione esistente di riconoscimento vocale Bing in servizi di riconoscimento vocale di Azure.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: gracez
ms.openlocfilehash: 6324da55c8af4934185fa39a106939844788adba
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57848967"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>Eseguire la migrazione da Riconoscimento vocale Bing al Servizio di riconoscimento vocale

Usare le informazioni in questo articolo per eseguire la migrazione delle applicazioni dall'API Riconoscimento vocale Bing al Servizio di riconoscimento vocale.

Questo articolo vengono descritte le differenze tra le API di riconoscimento vocale Bing e i servizi di riconoscimento vocale e suggerisce le strategie per la migrazione delle applicazioni. La chiave di sottoscrizione API riconoscimento vocale Bing non funzionerà con il servizio di riconoscimento vocale. è necessario un nuovo abbonamento di servizi di riconoscimento vocale.

Una singola chiave di sottoscrizione di servizi di riconoscimento vocale concede l'accesso alle funzionalità seguenti. Ogni funzionalità viene misurata separatamente, di conseguenza vengono addebitati solo i consumi delle funzionalità usate.

* [Riconoscimento vocale](speech-to-text.md)
* [Riconoscimento vocale personalizzato](https://cris.ai)
* [Sintesi vocale](text-to-speech.md)
* [Voci personalizzate per Sintesi vocale](how-to-customize-voice-font.md)
* [Traduzione vocale](speech-translation.md) (non include la [traduzione testuale](../translator/translator-info-overview.md))

[Speech SDK](speech-sdk.md) sostituisce le funzioni delle librerie client di Riconoscimento vocale Bing, ma usa un'API diversa.

## <a name="comparison-of-features"></a>Confronto delle funzionalità

I servizi di riconoscimento vocale sono molto simili al riconoscimento vocale Bing, con le differenze seguenti.

Funzionalità | Riconoscimento vocale Bing | Servizi Voce | Dettagli
-|-|-|-
C++ SDK | :heavy_minus_sign: | :heavy_check_mark: | Supporta i servizi di riconoscimento vocale Windows e Linux.
SDK per Java | :heavy_check_mark: | :heavy_check_mark: | Servizi di riconoscimento vocale supporta dispositivi Android e riconoscimento vocale.
SDK per C# | :heavy_check_mark: | :heavy_check_mark: | Servizi di riconoscimento vocale supporta Windows 10, Universal Windows Platform (UWP) e .NET Standard 2.0.
Riconoscimento vocale continuo | 10 minuti | Senza limiti, con SDK | Riconoscimento vocale Bing e protocolli WebSockets di servizi di riconoscimento vocale supportano entrambi fino a 10 minuti per ogni chiamata. Tuttavia Speech SDK si riconnette automaticamente in caso di timeout o disconnessione.
Risultati intermedi o parziali | :heavy_check_mark: | :heavy_check_mark: | Con il protocollo WebSocket o SDK.
Modelli conversione voce/testo personalizzati | :heavy_check_mark: | :heavy_check_mark: | Riconoscimento vocale Bing richiede una sottoscrizione separata a Riconoscimento vocale personalizzato.
Caratteri voce personalizzati | :heavy_check_mark: | :heavy_check_mark: | Riconoscimento vocale Bing richiede una sottoscrizione separata al riconoscimento vocale personalizzato.
Voci 24 kHz | :heavy_minus_sign: | :heavy_check_mark:
Riconoscimento finalità voce | Richiede una chiamata separata all'API LUIS | Integrato, con SDK |  È possibile usare una chiave LUIS con il servizio Voce.
Riconoscimento delle finalità semplice | :heavy_minus_sign: | :heavy_check_mark:
Trascrizione in batch di file audio lunghi | :heavy_minus_sign: | :heavy_check_mark:
Modalità di riconoscimento | Manuale tramite l'URI dell'endpoint | Automatico | La modalità di riconoscimento non è disponibile nel servizio Voce.
Località dell'endpoint | Globale | Regionale | Gli endpoint a livello di area migliorano la latenza.
API REST | :heavy_check_mark: | :heavy_check_mark: | Le API REST di servizi di riconoscimento vocale sono compatibili con il riconoscimento vocale Bing (diversi endpoint). Le API REST supportano la sintesi vocale e funzionalità limitate di riconoscimento vocale.
Protocolli WebSocket | :heavy_check_mark: | :heavy_check_mark: | L'API WebSocket servizi di riconoscimento vocale è compatibile con il riconoscimento vocale Bing (diversi endpoint). Se possibile, eseguire la migrazione a Speech SDK per semplificare il codice.
Chiamate API da servizio a servizio | :heavy_check_mark: | :heavy_minus_sign: | Fornito in Riconoscimento vocale Bing tramite la libreria di servizio di C#.
SDK open source | :heavy_check_mark: | :heavy_minus_sign: |

Un modello di determinazione prezzi basato sul tempo, anziché un modello basato sulle transazioni, utilizzare i servizi di riconoscimento vocale. Visualizzare [prezzi di servizi di riconoscimento vocale](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) per informazioni dettagliate.

## <a name="migration-strategies"></a>Strategie di migrazione

Se l'organizzazione dispone di applicazioni in sviluppo o produzione che usano un'API di riconoscimento vocale Bing, è necessario aggiornare in modo che usino i servizi di riconoscimento vocale appena possibile. Vedere le [documentazione di servizi di riconoscimento vocale](index.yml) per gli SDK, gli esempi di codice ed esercitazioni disponibili.

I servizi di riconoscimento vocale [API REST](rest-apis.md) sono compatibili con le API di riconoscimento vocale Bing. Se attualmente si usa l'API REST di riconoscimento vocale Bing, è necessario solo modificare l'endpoint REST e passare a una chiave di sottoscrizione di servizi di riconoscimento vocale.

I protocolli WebSocket di servizi di riconoscimento vocale sono inoltre compatibili con quelle usate da riconoscimento vocale Bing. Per le nuove attività di sviluppo è consigliabile usare Speech SDK anziché i protocolli WebSocket. È opportuno anche eseguire la migrazione di codice esistente nell'SDK. Tuttavia, come con le API REST, il codice esistente che usa Riconoscimento vocale Bing tramite WebSocket richiede solo una modifica dell'endpoint e una chiave di aggiornamento.

Se si usa una libreria client di Riconoscimento vocale Bing per un linguaggio di programmazione specifico, per eseguire la migrazione a [Speech SDK](speech-sdk.md) è necessario apportare modifiche all'applicazione perché l'API è diversa. Speech SDK può semplificare il codice permettendo anche di accedere alle nuove funzionalità.

Speech SDK supporta attualmente C# (Windows 10, piattaforma UWP, .NET Standard), Java (dispositivi Android e personalizzati), Objective C (iOS), C++ (Windows e Linux) e JavaScript. Le API sono simili in tutte le piattaforme e ciò semplifica lo sviluppo multipiattaforma.

I servizi di riconoscimento vocale non offrono un endpoint globale. Determinare se l'applicazione funziona in modo efficiente quando usa un singolo endpoint a livello di area per tutto il traffico. In caso contrario, usare la georilevazione per determinare l'endpoint più efficiente. È necessaria una sottoscrizione di servizi di riconoscimento vocale separata in ogni area in che uso.

Se l'applicazione usa connessioni di lunga durata e non può usare un SDK disponibile, è possibile usare una connessione WebSocket. Gestire il limite di timeout di 10 minuti eseguendo la riconnessione nei momenti appropriati.

Per iniziare a usare Speech SDK:

1. Scaricare [Speech SDK](speech-sdk.md).
1. Lavoro tramite i servizi di riconoscimento vocale [guide introduttive](quickstart-csharp-dotnet-windows.md) e [esercitazioni](how-to-recognize-intents-from-speech-csharp.md). Analizzare anche gli [esempi di codice](samples.md) per acquisire familiarità con le nuove API.
1. Aggiornare l'applicazione di utilizzare i servizi di riconoscimento vocale.

## <a name="support"></a>Supporto

I clienti di Riconoscimento vocale Bing devono contattare il supporto tecnico aprendo un [ticket di supporto](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). È anche possibile contattare Microsoft se il caso richiede un [piano di supporto tecnico](https://azure.microsoft.com/support/plans/).

Per il servizio di riconoscimento vocale, SDK e API di supporto, visitare i servizi di riconoscimento vocale [pagina del supporto tecnico](support.md).

## <a name="next-steps"></a>Passaggi successivi

* [Prova gratuitamente i servizi di riconoscimento vocale](get-started.md)
* [Guida introduttiva: Riconoscere i contenuti vocali in un'app UWP con Speech SDK](quickstart-csharp-uwp.md)

## <a name="see-also"></a>Vedere anche 
* [Note sulla versione di servizi di riconoscimento vocale](releasenotes.md)
* [Informazioni sul servizio Voce](overview.md)
* [Documentazione di servizi di riconoscimento vocale e Speech SDK](speech-sdk.md#get-the-sdk)
