---
title: Informazioni sugli scenari di riconoscimento vocale di Servizi cognitivi di Azure
description: Scenari
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.technology: Speech to Text
ms.topic: article
ms.date: 07/02/2018
ms.author: v-jerkin
ms.openlocfilehash: 98e79112ba70ff578975cea9d872b4fcc41512bc
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2018
ms.locfileid: "41929732"
---
# <a name="speech-scenarios"></a>Scenari di riconoscimento vocale

Esistono molti scenari che possono essere attivati con la tecnologia di riconoscimento vocale. Nella documentazione vengono analizzati alcuni tra i più comuni e vengono evidenziate le funzionalità rilevanti. Per la maggior parte del contenuto, l'[SDK](speech-sdk.md) svolge un ruolo centrale nell'abilitazione di questi scenari.

La pagina illustra come:
> [!div class="checklist"]
> * Creare app con attivazione vocale
> * Trascrivere chiamate audio dei call center
> * Bot vocali

## <a name="voice-triggered-apps"></a>App con attivazione vocale

Molti utenti vogliono abilitare l'input vocale nelle proprie applicazioni. L'input vocale è un ottimo modo per rendere l'applicazione flessibile, sia per usarla in viva voce (ad esempio in un'automobile) o per velocizzare le varie attività come richiedere indicazioni, notizie o informazioni meteo. 

### <a name="voice-triggered-apps-with-baseline-models"></a>App con attivazione vocale con modelli di base

Se l'app verrà usata dal pubblico in ambienti in cui il rumore di fondo non è eccessivo, il modo più semplice e rapido per eseguire questa operazione è quello di scaricare semplicemente [Speech SDK](speech-sdk.md) e seguire i relativi [Esempi](quickstart-csharp-dotnet-windows.md). L'SDK basato sulla [chiave di sottoscrizione di Azure](https://azure.microsoft.com/try/cognitive-services/) consente agli sviluppatori di caricare l'audio nei i modelli di riconoscimento vocale di base che attivano Cortana e Skype. I modelli sono all'avanguardia e vengono usati dai prodotti citati. Si può essere operativi in pochi minuti.

### <a name="voice-triggered-apps-with-custom-models"></a>App con attivazione vocale con modelli personalizzati

Se l'app è relativa a un dominio specifico, ad esempio chimica, biologia o particolari esigenze dietetiche, è possibile prendere in considerazione di adattare un [modello linguistico](how-to-customize-language-model.md). L'adattamento di un modello linguistico insegnerà al decodificatore le frasi e i termini più comuni usati dall'app. Il decodificatore sarà in grado di trascrivere in modo più accurato un input vocale con un modello linguistico personalizzato per un particolare dominio anziché il modello di base. Allo stesso modo se il rumore di fondo in cui l'app verrà usata è rilevante, l'utente è potrebbe voler adattare un modello acustico. Esplorare la documentazione per gli altri casi in cui l'[adattamento di un modello linguistico](how-to-customize-language-model.md) e l'[adattamento acustico](how-to-customize-acoustic-models.md) forniscono valore e visitare il nostro [portale per l'adattamento](https://customspeech.ai) per iniziare subito l'esperienza di creazione del modello. Analogamente ai modelli di base, i modelli personalizzati vengono chiamati tramite [Speech SDK](speech-sdk.md) e seguono gli [esempi](quickstart-csharp-dotnet-windows.md) rilevanti.

## <a name="transcribe-call-center-audio-calls"></a>Trascrivere chiamate audio dei call center

I call center accumulano grandi quantità di audio. Nascosto all'interno di tali file audio si trova il valore che può essere ottenuto tramite la trascrizione. La durata della chiamata, il sentiment, la soddisfazione del cliente e il valore generale della chiamata forniti al chiamante possono essere individuati ottenendo le trascrizioni delle chiamate.

Il punto di partenza migliore è l'[API di trascrizione Batch](batch-transcription.md) insieme all'[esempio](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI) correlato.

È necessario innanzitutto ottenere una [chiave di sottoscrizione di Azure ](https://azure.microsoft.com/try/cognitive-services/), quindi si dovrà consultare la documentazione [API di trascrizione Batch](batch-transcription.md).

### <a name="transcribe-call-center-audio-calls-with-baseline-models"></a>Trascrivere le chiamate audio dei call center con i modelli di base

La decisione che deve essere presa è se usare i modelli di base interni per eseguire la trascrizione, adattare un linguaggio o un modello acustico o entrambe le cose. Per usare i modelli di base nell'API è richiesta solo la chiave API. Internamente l'API richiamerà il modello migliore per i dati e per l'adattamento.

### <a name="transcribe-call-center-audio-calls-with-custom-models"></a>Trascrivere chiamate audio dei call center con i modelli personalizzati

Se si prevede di usare un modello personalizzato sarà necessario l'ID del modello e la chiave API. L'ID del modello viene ottenuto tramite il [portale per l'adattamento](https://customspeech.ai). Non si tratta dell'ID di endpoint che si trova nella visualizzazione "Dettagli endpoint", ma dell'ID del modello che è possibile recuperare facendo clic sui dettagli del modello.

## <a name="voice-bots"></a>Bot vocali

Gli sviluppatori possono potenziare l'applicazione con l'output vocale. Il servizio di riconoscimento vocale può sintetizzare la voce per numerose [lingue](supported-languages.md) e fornisce gli [endpoint](rest-apis.md) per l'accesso e l'aggiunta di tale funzionalità all'app.

Inoltre, per gli utenti che vogliono personalizzare maggiormente i propri bot, il servizio di riconoscimento vocale consente agli sviluppatori di personalizzare un carattere voce univoco. Analogamente alla personalizzazione dei modelli di riconoscimento vocale, i caratteri voce richiedono i dati dell'utente. Gli sviluppatori devono caricare i dati sul [portale per l'adattamento vocale](https://customspeech.ai) e iniziare a creare la voce esclusiva per il bot. I dettagli sono descritti [qui](how-to-text-to-speech.md), nonché nella pagine delle [domande frequenti](faq-text-to-speech.md) 

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere una sottoscrizione di valutazione gratuita del Servizio di riconoscimento vocale](https://azure.microsoft.com/try/cognitive-services/)
* [Iniziare a usare Speech SDK](speech-sdk.md)
