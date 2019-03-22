---
title: Sintesi vocale con servizi di riconoscimento vocale di Azure
titleSuffix: Azure Cognitive Services
description: Sintesi vocale da servizi di Azure sono un servizio basato su REST che consente le applicazioni, strumenti o i dispositivi di convertire testo in naturale simile a umane sintesi vocale. Scegliere tra standard e neurale voices oppure creare la tua voce personalizzato univoco per un prodotto o un marchio. 75 voices standard sono disponibili in più di 45 lingue e impostazioni locali e 5 voices neurale sono disponibili in 4 lingue e impostazioni locali.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 05028704c08ebd06f9b9e4e3f45c5137eb1e6b58
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226913"
---
# <a name="what-is-text-to-speech"></a>Che cos'è sintesi vocale?

Sintesi vocale da servizi di Azure sono un servizio basato su REST che consente le applicazioni, strumenti o i dispositivi di convertire testo in naturale simile a umane sintesi vocale. Scegliere tra standard e neurale voices o creane di nuove [vocali personalizzati](#custom-voice-fonts) univoco per un prodotto o un marchio. 75 voices standard sono disponibili in più di 45 lingue e impostazioni locali e 5 voices neurale sono disponibili in 4 lingue e impostazioni locali. Per un elenco completo, vedere [lingue supportate](language-support.md#text-to-speech).

Tecnologia di riconoscimento vocale consente ai creatori di contenuti interagire con i propri utenti in modi diversi. Sintesi vocale può migliorare l'accessibilità, fornendo agli utenti con un'opzione per interagire con il contenuto acustico. Se l'utente ha problemi di vista, una formazione disabilità, o richiede informazioni sulla navigazione mentre si Guida, sintesi vocale può migliorare un'esperienza esistente. Sintesi vocale è anche un componente aggiuntivo prezioso per i robot vocali e assistenti virtuali.

### <a name="neural-voices"></a>Voci neurali

Le voci neurali possono essere usate per rendere più naturali e coinvolgenti le interazioni con chatbot e assistenti virtuali, per convertire testo digitale, come gli e-book, in audiolibri e per migliorare i sistemi dei navigatori per le automobili. Prosodia naturale human-like e la chiaro tempestiva delle parole, voices neurale ridurre notevolmente la fatica di ascolto quando si interagisce con i sistemi di intelligenza artificiale. Per altre informazioni sulle voci neurali, vedere [Supporto per le lingue](language-support.md#text-to-speech).

### <a name="custom-voices"></a>Voci personalizzate

Personalizzazione casella vocale consente di creare una voce riconoscibile, uno di tipo per il tuo marchio. Per creare il carattere voce personalizzato, si apportano una registrazione in studio e caricare gli script associati come dati di training. Il servizio crea quindi un modello vocale univoco ottimizzato per la registrazione. È possibile utilizzare questo carattere voce personalizzato per sintesi vocale. Per altre informazioni, vedere [voci personalizzate](how-to-customize-voice-font.md).

## <a name="core-features"></a>Funzionalità di base

Questa tabella elenca le funzionalità di base per la sintesi vocale:

| Caso d'uso | SDK | REST |
|----------|-----|------|
| Convertire sintesi vocale. | No  | Sì |
| Caricare i set di dati per l'adattamento del vocali. | No  | Sì\* |
| Creare e gestire i modelli del tipo di carattere vocali. | No  | Sì\* |
| Creare e gestire le distribuzioni di tipo di carattere vocali. | No  | Sì\* |
| Creare e gestire i test del tipo di carattere vocali. | No  | Sì\* |
| Gestire le sottoscrizioni. | No  | Sì\* |

\* *Questi servizi sono disponibili tramite l'endpoint cris.ai. Visualizzare [Swagger riferimento](https://westus.cris.ai/swagger/ui/index).*

> [!NOTE]
> L'endpoint di sintesi vocale viene implementata la limitazione delle richieste che limita le richieste a 25 per ogni 5 secondi. Quando si verifica una limitazione, verrà segnalato tramite le intestazioni del messaggio.

## <a name="get-started-with-text-to-speech"></a>Introduzione a sintesi vocale

Sono disponibili guide introduttive progettato per l'esecuzione di codice in meno di 10 minuti. Questa tabella include un elenco delle guide introduttive di sintesi vocale organizzati per linguaggio.

| Guida introduttiva | Piattaforma | Informazioni di riferimento sulle API |
|------------|----------|---------------|
| [C#, .NET core](quickstart-dotnet-text-to-speech.md) | Windows, macOS, Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech-api) |
| [Node.js](quickstart-nodejs-text-to-speech.md) | Finestra, macOS, Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech-api) |
| [Python](quickstart-python-text-to-speech.md) | Finestra, macOS, Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech-api) |

## <a name="sample-code"></a>Codice di esempio

Codice di esempio per la sintesi vocale è disponibile in GitHub. Questi esempi coprono sintesi vocale nei linguaggi di programmazione più diffusi.

* [Esempi di sintesi vocale (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>Documentazione di riferimento

* [Speech SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [API REST: Per il riconoscimento vocale](rest-speech-to-text.md)
* [API REST: Sintesi vocale](rest-text-to-speech.md)
* [API REST: Personalizzazione e la trascrizione di batch](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere una sottoscrizione di valutazione gratuita per i servizi Voce](get-started.md)
* [Creare caratteri voce personalizzati](how-to-customize-voice-font.md)
