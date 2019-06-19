---
title: Sintesi vocale con servizi di riconoscimento vocale di Azure
titleSuffix: Azure Cognitive Services
description: Sintesi vocale da servizi di Azure sono un servizio che consente le applicazioni, strumenti o i dispositivi di convertire testo in naturale simile a umane sintesi vocale. Scegliere tra standard e neurale voices oppure creare la tua voce personalizzato univoco per un prodotto o un marchio. 75 voices standard sono disponibili in più di 45 lingue e impostazioni locali e 5 voices neurale sono disponibili in 4 lingue e impostazioni locali.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/14/2019
ms.author: erhopf
ms.openlocfilehash: a3469e4f7fe1c234a6df694d7bdd6d9e2c46407a
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204880"
---
# <a name="what-is-text-to-speech"></a>Che cos'è sintesi vocale?

Sintesi vocale da servizi di Azure sono un servizio che consente le applicazioni, strumenti o i dispositivi di convertire testo in naturale simile a umane sintesi vocale. Scegliere tra standard e neurale voices oppure creare la tua voce personalizzato univoco per un prodotto o un marchio. 75 voices standard sono disponibili in più di 45 lingue e impostazioni locali e 5 voices neurale sono disponibili in 4 lingue e impostazioni locali. Per un elenco completo, vedere [lingue supportate](language-support.md#text-to-speech).

Tecnologia di riconoscimento vocale consente ai creatori di contenuti interagire con i propri utenti in modi diversi. Sintesi vocale può migliorare l'accessibilità, fornendo agli utenti con un'opzione per interagire con il contenuto acustico. Se l'utente ha problemi di vista, una formazione disabilità, o richiede informazioni sulla navigazione mentre si Guida, sintesi vocale può migliorare un'esperienza esistente. Sintesi vocale è anche un componente aggiuntivo prezioso per i robot vocali e assistenti virtuali.

### <a name="standard-voices"></a>Voci standard

Voices standard vengono create utilizzando le tecniche di sintesi parametriche statistica e/o sintesi di concatenazione. Queste voci siano naturale altamente comprensibili e audio. È possibile abilitare facilmente le applicazioni da leggere a voce in più di 45 lingue, con un'ampia gamma di opzioni relative alla voce. Queste voci forniscono precisione elevata pronuncia, incluso il supporto per le abbreviazioni, le espansioni acronimo, data/ora interpretazioni, polyphones e altro ancora. Usa la voce standard per migliorare l'accessibilità per le applicazioni e servizi, consentendo agli utenti di interagire con i contenuti acustico.

### <a name="neural-voices"></a>Voci neurali

Voices neurale usare reti neurali profonde per superare i limiti dei tradizionali sistemi di sintesi vocale in corrispondenti ai modelli di stress e intonazione in linguaggio parlato e la sintesi le unità di riconoscimento vocale in una voce di computer. Sintesi vocale standard suddivide prosodia separato per analisi linguistica e intervalli per la stima acustico governate dai modelli indipendenti, che possono comportare la sintesi vocale ovattato. La funzionalità neurale esegue sintesi vocale e stima di prosodia contemporaneamente, offrendo una voce più fluida e naturale.

Le voci neurali possono essere usate per rendere più naturali e coinvolgenti le interazioni con chatbot e assistenti virtuali, per convertire testo digitale, come gli e-book, in audiolibri e per migliorare i sistemi dei navigatori per le automobili. Prosodia naturale human-like e la chiaro tempestiva delle parole, voices neurale ridurre notevolmente la fatica di ascolto quando si interagisce con i sistemi di intelligenza artificiale.

Voices neurale supportano stili diversi, ad esempio indipendente e persone. Pronunciare, ad esempio, la voce Jessa (en-US) possa cheerfully, che è ottimizzata per la conversazione a caldo, felice. È possibile modificare l'output vocale, come segnale acustico, passo e facilitare l'uso delle [linguaggio di Markup sintesi vocale](speech-synthesis-markup.md). Per un elenco completo delle voci disponibili, vedere [lingue supportate](language-support.md#text-to-speech).

Per altre informazioni sui vantaggi di voci neurale, vedere [servizio di sintesi vocale neurale nuovo di Microsoft ti macchine pronunciare, ad esempio persone](https://azure.microsoft.com/blog/microsoft-s-new-neural-text-to-speech-service-helps-machines-speak-like-people/).

### <a name="custom-voices"></a>Voci personalizzate

Personalizzazione casella vocale consente di creare una voce riconoscibile, uno di tipo per il tuo marchio. Per creare il carattere voce personalizzato, si apportano una registrazione in studio e caricare gli script associati come dati di training. Il servizio crea quindi un modello vocale univoco ottimizzato per la registrazione. È possibile utilizzare questo carattere voce personalizzato per sintesi vocale. Per altre informazioni, vedere [voci personalizzate](how-to-customize-voice-font.md).

## <a name="speech-synthesis-markup-language-ssml"></a>Speech Synthesis Markup Language (SSML)

Linguaggio di Markup sintesi della voce (SSML) è un linguaggio di markup basato su XML che consente agli sviluppatori di specificare il testo come input viene convertito in sintesi vocale, utilizzando il servizio di sintesi vocale. Rispetto al testo normale, SSML consente agli sviluppatori di ottimizzare il tono, pronuncia, velocità di pronuncia, volume e più output di sintesi vocale. Punteggiatura normali, ad esempio tramite l'intonazione corretto quando si termina una frase con un punto interrogativo o sospensione dopo un periodo vengono gestiti automaticamente.

Tutti gli input di testo inviati al servizio di sintesi vocale devono essere strutturati con SSML. Per altre informazioni, vedere [linguaggio di Markup sintesi vocale](speech-synthesis-markup.md).

### <a name="pricing-note"></a>Nota sui prezzi

Quando si usa il servizio di sintesi vocale, ti vengono fatturate per ogni carattere viene convertito nel riconoscimento vocale, inclusi segni di punteggiatura. Anche se il documento SSML stesso non fatturabile, elementi facoltativi che consentono di modificare la modalità di conversione il testo per il riconoscimento vocale, come fonemi e tono, vengono conteggiati come caratteri fatturabile. Ecco un elenco di cosa è fatturabile:

* Testo passato al servizio di sintesi vocale nel corpo della richiesta SSML
* Tutti i commenti all'interno del campo di testo del corpo della richiesta nel formato SSML, ad eccezione di `<speak>` e `<voice>` tag
* Lettere, punteggiatura, spazi, tabulazioni, markup e tutti i caratteri spazi vuoti
* Ogni elemento di codice definito in Unicode

Per informazioni dettagliate, vedere [Pricing](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

> [!IMPORTANT]
> Ogni carattere di lingua cinese, giapponese e coreano viene conteggiata come due caratteri per la fatturazione.

## <a name="core-features"></a>Funzionalità di base

Questa tabella elenca le funzionalità di base per la sintesi vocale:

| Caso d'uso | SDK | REST |
|----------|-----|------|
| Convertire sintesi vocale. | Yes | Yes |
| Caricare i set di dati per l'adattamento del vocali. | No | Sì\* |
| Creare e gestire i modelli del tipo di carattere vocali. | No | Sì\* |
| Creare e gestire le distribuzioni di tipo di carattere vocali. | No | Sì\* |
| Creare e gestire i test del tipo di carattere vocali. | No | Sì\* |
| Gestire le sottoscrizioni. | No | Sì\* |

\* *Questi servizi sono disponibili tramite l'endpoint cris.ai. Visualizzare [Swagger riferimento](https://westus.cris.ai/swagger/ui/index). Questi corsi di formazione vocali personalizzati e API di gestione di implementare la limitazione delle richieste che limiti le richieste a 25 per ogni 5 secondi, mentre la sintesi vocale, API stessa implementa la limitazione delle richieste che consente a 200 richieste al secondo come la più alta. Quando si verifica una limitazione, verrà segnalato tramite le intestazioni del messaggio.*

## <a name="get-started-with-text-to-speech"></a>Introduzione a sintesi vocale

Sono disponibili guide introduttive progettato per l'esecuzione di codice in meno di 10 minuti. Questa tabella include un elenco delle guide introduttive di sintesi vocale organizzati per linguaggio.

### <a name="sdk-quickstarts"></a>Guide introduttive SDK

| Quickstart (SDK) | Piattaforma | Informazioni di riferimento sulle API |
|------------|----------|---------------|
| [C#, .NET framework](quickstart-text-to-speech-dotnet-windows.md) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C++](quickstart-text-to-speech-cpp-windows.md) | Windows | [Browse](https://aka.ms/csspeech/cppref) |
| [C++](quickstart-text-to-speech-cpp-linux.md) | Linux | [Browse](https://aka.ms/csspeech/cppref) |

### <a name="rest-quickstarts"></a>Guide introduttive REST

| Guida introduttiva (REST) | Piattaforma | Informazioni di riferimento sulle API |
|------------|----------|---------------|
| [C#, .NET core](quickstart-dotnet-text-to-speech.md) | Windows, macOS, Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Node.js](quickstart-nodejs-text-to-speech.md) | Finestra, macOS, Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Python](quickstart-python-text-to-speech.md) | Finestra, macOS, Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |

## <a name="sample-code"></a>Codice di esempio

Codice di esempio per la sintesi vocale è disponibile in GitHub. Questi esempi coprono sintesi vocale nei linguaggi di programmazione più diffusi.

* [Esempi di sintesi vocale (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Text-to-speech samples (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS) (Esempi di sintesi vocale (REST))

## <a name="reference-docs"></a>Documentazione di riferimento

* [Speech SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [API REST: Riconoscimento vocale](rest-speech-to-text.md)
* [API REST: Sintesi vocale](rest-text-to-speech.md)
* [API REST: Trascrizione e personalizzazione batch](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere una sottoscrizione di valutazione gratuita per i servizi Voce](get-started.md)
* [Creare caratteri voce personalizzati](how-to-customize-voice-font.md)
