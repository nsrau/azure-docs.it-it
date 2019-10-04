---
title: Eseguire la migrazione dal riconoscimento vocale Bing al servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Informazioni su come eseguire la migrazione da una sottoscrizione di Bing Speech esistente al servizio riconoscimento vocale da servizi cognitivi di Azure.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: nitinme
ms.openlocfilehash: 5694894a78a46ad658ec18f210c6a82fb82df23f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559608"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>Eseguire la migrazione da Riconoscimento vocale Bing al Servizio di riconoscimento vocale

Usare le informazioni in questo articolo per eseguire la migrazione delle applicazioni dall'API Riconoscimento vocale Bing al Servizio di riconoscimento vocale.

Questo articolo descrive le differenze tra le API riconoscimento vocale Bing e i servizi di riconoscimento vocale e suggerisce strategie per la migrazione delle applicazioni. La chiave di sottoscrizione di Bing Speech API non funzionerà con il servizio di riconoscimento vocale. sarà necessaria una nuova sottoscrizione di servizi vocali.

Una singola chiave di sottoscrizione di servizi vocali concede l'accesso alle funzionalità seguenti. Ogni funzionalità viene misurata separatamente, di conseguenza vengono addebitati solo i consumi delle funzionalità usate.

* [Riconoscimento vocale](speech-to-text.md)
* [Riconoscimento vocale personalizzato](https://cris.ai)
* [Sintesi vocale](text-to-speech.md)
* [Voci personalizzate per Sintesi vocale](how-to-customize-voice-font.md)
* [Traduzione vocale](speech-translation.md) (non include la [traduzione testuale](../translator/translator-info-overview.md))

[Speech SDK](speech-sdk.md) sostituisce le funzioni delle librerie client di Riconoscimento vocale Bing, ma usa un'API diversa.

## <a name="comparison-of-features"></a>Confronto delle funzionalità

I servizi di riconoscimento vocale sono in gran parte simili al riconoscimento vocale Bing, con le differenze seguenti.

Funzionalità | Riconoscimento vocale Bing | Servizi Voce | Dettagli
-|-|-|-
C++ SDK | :heavy_minus_sign: | :heavy_check_mark: | Servizi vocali supporta Windows e Linux.
SDK per Java | :heavy_check_mark: | :heavy_check_mark: | Servizi vocali supporta dispositivi Android e vocali.
SDK per C# | :heavy_check_mark: | :heavy_check_mark: | Servizi vocali supporta Windows 10, piattaforma UWP (Universal Windows Platform) (UWP) e .NET Standard 2,0.
Riconoscimento vocale continuo | 10 minuti | Senza limiti, con SDK | I protocolli WebSockets di servizi vocali e riconoscimento vocale Bing supportano fino a 10 minuti per chiamata. Tuttavia Speech SDK si riconnette automaticamente in caso di timeout o disconnessione.
Risultati intermedi o parziali | :heavy_check_mark: | :heavy_check_mark: | Con il protocollo WebSocket o SDK.
Modelli conversione voce/testo personalizzati | :heavy_check_mark: | :heavy_check_mark: | Riconoscimento vocale Bing richiede una sottoscrizione separata a Riconoscimento vocale personalizzato.
Caratteri voce personalizzati | :heavy_check_mark: | :heavy_check_mark: | Riconoscimento vocale Bing richiede una sottoscrizione separata al riconoscimento vocale personalizzato.
Voci 24 kHz | :heavy_minus_sign: | :heavy_check_mark:
Riconoscimento finalità voce | Richiede una chiamata separata all'API LUIS | Integrato, con SDK |  È possibile usare una chiave LUIS con il servizio Voce.
Riconoscimento delle finalità semplice | :heavy_minus_sign: | :heavy_check_mark:
Trascrizione in batch di file audio lunghi | :heavy_minus_sign: | :heavy_check_mark:
Modalità di riconoscimento | Manuale tramite l'URI dell'endpoint | Automatico | La modalità di riconoscimento non è disponibile nel servizio Voce.
Località dell'endpoint | Global | Regionale | Gli endpoint a livello di area migliorano la latenza.
API REST | :heavy_check_mark: | :heavy_check_mark: | Le API REST di servizi vocali sono compatibili con il riconoscimento vocale Bing (endpoint diverso). Le API REST supportano la sintesi vocale e funzionalità limitate di riconoscimento vocale.
Protocolli WebSocket | :heavy_check_mark: | :heavy_check_mark: | L'API WebSocket dei servizi vocali è compatibile con il riconoscimento vocale Bing (endpoint diverso). Se possibile, eseguire la migrazione a Speech SDK per semplificare il codice.
Chiamate API da servizio a servizio | :heavy_check_mark: | :heavy_minus_sign: | Fornito in Riconoscimento vocale Bing tramite la libreria di servizio di C#.
SDK open source | :heavy_check_mark: | :heavy_minus_sign: |

I servizi di riconoscimento vocale utilizzano un modello di determinazione prezzi basato sul tempo, anziché un modello basato sulle transazioni. Per informazioni dettagliate, vedere [prezzi di servizi vocali](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) .

## <a name="migration-strategies"></a>Strategie di migrazione

Se l'utente o l'organizzazione dispone di applicazioni in fase di sviluppo o produzione che usano un Speech API Bing, è necessario aggiornarle per usare i servizi di riconoscimento vocale il prima possibile. Vedere la [documentazione di servizi vocali](index.yml) per SDK, esempi di codice ed esercitazioni disponibili.

Le [API REST](rest-apis.md) di servizi vocali sono compatibili con le API riconoscimento vocale Bing. Se attualmente si usano le API REST di riconoscimento vocale Bing, è necessario modificare solo l'endpoint REST e passare a una chiave di sottoscrizione di servizi vocali.

I protocolli WebSocket dei servizi vocali sono compatibili anche con quelli usati dal riconoscimento vocale Bing. Per le nuove attività di sviluppo è consigliabile usare Speech SDK anziché i protocolli WebSocket. È opportuno anche eseguire la migrazione di codice esistente nell'SDK. Tuttavia, come con le API REST, il codice esistente che usa Riconoscimento vocale Bing tramite WebSocket richiede solo una modifica dell'endpoint e una chiave di aggiornamento.

Se si usa una libreria client di Riconoscimento vocale Bing per un linguaggio di programmazione specifico, per eseguire la migrazione a [Speech SDK](speech-sdk.md) è necessario apportare modifiche all'applicazione perché l'API è diversa. Speech SDK può semplificare il codice permettendo anche di accedere alle nuove funzionalità.

Attualmente, l'SDK vocale supporta C# ([Dettagli qui](https://aka.ms/csspeech)), Java (Android e dispositivi personalizzati), Objective C (iOS), C++ (Windows e Linux) e JavaScript. Le API sono simili in tutte le piattaforme e ciò semplifica lo sviluppo multipiattaforma.

I servizi di riconoscimento vocale non offrono un endpoint globale. Determinare se l'applicazione funziona in modo efficiente quando usa un singolo endpoint a livello di area per tutto il traffico. In caso contrario, usare la georilevazione per determinare l'endpoint più efficiente. È necessaria una sottoscrizione separata per i servizi vocali in ogni area in uso.

Se l'applicazione usa connessioni di lunga durata e non può usare un SDK disponibile, è possibile usare una connessione WebSocket. Gestire il limite di timeout di 10 minuti eseguendo la riconnessione nei momenti appropriati.

Per iniziare a usare Speech SDK:

1. Scaricare [Speech SDK](speech-sdk.md).
1. Usare le [guide introduttive](quickstart-csharp-dotnet-windows.md) e le [esercitazioni](how-to-recognize-intents-from-speech-csharp.md)di servizi vocali. Analizzare anche gli [esempi di codice](samples.md) per acquisire familiarità con le nuove API.
1. Aggiornare l'applicazione per l'uso dei servizi di riconoscimento vocale.

## <a name="support"></a>Supporto

I clienti di Riconoscimento vocale Bing devono contattare il supporto tecnico aprendo un [ticket di supporto](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). È anche possibile contattare Microsoft se il caso richiede un [piano di supporto tecnico](https://azure.microsoft.com/support/plans/).

Per il supporto di riconoscimento vocale, SDK e API, visitare la [pagina del supporto](support.md)per i servizi di riconoscimento vocale.

## <a name="next-steps"></a>Passaggi successivi

* [Prova gratuitamente i servizi vocali](get-started.md)
* [Avvio rapido: Riconoscere i contenuti vocali in un'app UWP con Speech SDK](quickstart-csharp-uwp.md)

## <a name="see-also"></a>Vedere anche
* [Note sulla versione di servizi vocali](releasenotes.md)
* [Informazioni sul servizio Voce](overview.md)
* [Documentazione di Speech Services e Speech SDK](speech-sdk.md#get-the-sdk)
