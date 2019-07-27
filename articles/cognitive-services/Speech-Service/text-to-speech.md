---
title: Servizio sintesi vocale
titleSuffix: Azure Cognitive Services
description: La funzionalità di sintesi vocale del servizio riconoscimento vocale è una funzionalità che consente a applicazioni, strumenti o dispositivi di convertire testo in sintesi vocale naturale. È possibile scegliere tra le voci standard e neurali oppure creare una voce personalizzata univoca per il prodotto o il marchio. 75 e le voci standard sono disponibili in più di 45 lingue e impostazioni locali e 5 voci neurali sono disponibili in 4 lingue e impostazioni locali.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: erhopf
ms.openlocfilehash: 3d2c3e2bf693f763db13d90961a31e871aa25235
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68558876"
---
# <a name="what-is-text-to-speech"></a>Che cos'è la sintesi vocale?

La sintesi vocale da servizi vocali di Azure è un servizio che consente a applicazioni, strumenti o dispositivi di convertire testo in sintesi vocale naturale. È possibile scegliere tra le voci standard e neurali oppure creare una voce personalizzata univoca per il prodotto o il marchio. 75 e le voci standard sono disponibili in più di 45 lingue e impostazioni locali e 5 voci neurali sono disponibili in 4 lingue e impostazioni locali. Per un elenco completo, vedere [linguaggi supportati](language-support.md#text-to-speech).

La tecnologia sintesi vocale consente agli autori di contenuti di interagire con gli utenti in modi diversi. La sintesi vocale può migliorare l'accessibilità offrendo agli utenti la possibilità di interagire con il contenuto in modo impercettibile. Indipendentemente dal fatto che l'utente abbia problemi visivi, una disabilità di apprendimento o che richiede informazioni di navigazione durante la guida, la sintesi vocale può migliorare un'esperienza esistente. Il riconoscimento vocale è anche un componente aggiuntivo prezioso per bot vocali e assistenti virtuali.


Grazie all'uso di SSML (Speech Synthesis Markup Language), un linguaggio di markup basato su XML, gli sviluppatori che usano il servizio di sintesi vocale possono specificare il modo in cui il testo di input viene convertito in sintesi vocale. Con SSML è possibile modificare il pitch, la pronuncia, la velocità di pronuncia, il volume e altro ancora. Per ulteriori informazioni, vedere [SSML](#speech-synthesis-markup-language-ssml).

### <a name="standard-voices"></a>Voci standard

Le voci standard vengono create tramite la sintesi parametrica statistica e/o le tecniche di sintesi della concatenazione. Queste voci sono molto comprensibili e sono naturali. È possibile abilitare facilmente le applicazioni in modo che parlino in più di 45 lingue, con una vasta gamma di opzioni vocali. Queste voci forniscono un'accuratezza della pronuncia elevata, incluso il supporto per le abbreviazioni, le espansioni di acronimo, le interpretazioni di data/ora, i politelefoni e altro ancora. Usare la voce standard per migliorare l'accessibilità per le applicazioni e i servizi consentendo agli utenti di interagire con i contenuti in modo impercettibile.

### <a name="neural-voices"></a>Voci neurali

Le voci neurali usano reti neurali profonde per superare i limiti dei sistemi di sintesi vocale tradizionali, in modo da abbinare i modelli di stress e intonazione in linguaggio parlato e sintetizzare le unità di riconoscimento vocale in una voce del computer. La sintesi vocale standard suddivide prosodia in operazioni di analisi linguistica separate e di stime acustiche gestite da modelli indipendenti, che possono comportare una sintesi vocale ovattata. La nostra funzionalità neurale prosodia la stima e la sintesi vocale simultaneamente, il che comporta una voce più fluida e naturale.

Le voci neurali possono essere usate per rendere più naturali e coinvolgenti le interazioni con chatbot e assistenti virtuali, per convertire testo digitale, come gli e-book, in audiolibri e per migliorare i sistemi dei navigatori per le automobili. Con la prosodia naturale e una chiara articolazione di parole, le voci neurali riducono significativamente la fatica in ascolto quando si interagisce con i sistemi di intelligenza artificiale.

Le voci neurali supportano stili diversi, ad esempio neutro e allegro. Ad esempio, la voce Jessa (en-US) può parlare allegramente, che è ottimizzata per conversazioni calde e felici. È possibile modificare l'output vocale, ad esempio tono, passo e velocità usando [sintesi vocale linguaggio di markup](speech-synthesis-markup.md). Per un elenco completo delle voci disponibili, vedere [linguaggi supportati](language-support.md#text-to-speech).

Per altre informazioni sui vantaggi delle voci neurali, vedere il [nuovo servizio di sintesi vocale neurale Microsoft per aiutare i computer a parlare di persone](https://azure.microsoft.com/blog/microsoft-s-new-neural-text-to-speech-service-helps-machines-speak-like-people/).

### <a name="custom-voices"></a>Voci personalizzate

La personalizzazione vocale consente di creare una voce riconoscibile, uno di tipo, per il marchio. Per creare il tipo di carattere vocale personalizzato, è necessario eseguire una registrazione in studio e caricare gli script associati come dati di training. Il servizio crea quindi un modello vocale univoco ottimizzato per la registrazione. È possibile utilizzare questo tipo di carattere vocale personalizzato per sintetizzare la sintesi vocale. Per altre informazioni, vedere [voci personalizzate](how-to-customize-voice-font.md).

## <a name="speech-synthesis-markup-language-ssml"></a>Speech Synthesis Markup Language (SSML)

Speech Synthesis Markup Language (SSML) è un linguaggio di markup basato su XML che consente agli sviluppatori di specificare il modo in cui il testo di input viene convertito in sintesi vocale usando il servizio di sintesi vocale. Rispetto a testo normale, SSML consente agli sviluppatori di ottimizzare il pitch, la pronuncia, la velocità di pronuncia, il volume e altro ancora dell'output da sintesi vocale. La punteggiatura normale, ad esempio la sospensione dopo un periodo, o l'uso dell'intonazione corretta quando una frase termina con un punto interrogativo viene gestita automaticamente.

Tutti gli input di testo inviati al servizio di sintesi vocale devono essere strutturati come SSML. Per ulteriori informazioni, vedere [linguaggio di markup sintesi vocale](speech-synthesis-markup.md).

### <a name="pricing-note"></a>Nota sui prezzi

Quando si usa il servizio di sintesi vocale, viene addebitato ogni carattere convertito in sintesi vocale, inclusa la punteggiatura. Anche se il documento SSML non è fatturabile, gli elementi facoltativi utilizzati per regolare la conversione del testo in sintesi vocale, ad esempio fonemi e pitch, vengono conteggiati come caratteri fatturabili. Ecco un elenco degli elementi fatturabili:

* Testo passato al servizio di sintesi vocale nel corpo SSML della richiesta
* Tutti i markup all'interno del campo di testo del corpo della richiesta nel formato SSML, `<speak>` ad `<voice>` eccezione dei tag e
* Lettere, punteggiatura, spazi, tabulazioni, markup e tutti gli spazi vuoti
* Ogni elemento di codice definito in Unicode

Per informazioni dettagliate, vedere [prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

> [!IMPORTANT]
> Ogni carattere di lingua cinese, giapponese e coreano viene conteggiato come due caratteri per la fatturazione.

## <a name="core-features"></a>Funzionalità di base

Questa tabella elenca le funzionalità di base per sintesi vocale:

| Caso d'uso | SDK | REST |
|----------|-----|------|
| Converte il testo in sintesi vocale. | Sì | Yes |
| Caricare i set di impostazioni per l'adattamento vocale. | No | Sì\* |
| Creare e gestire i modelli di tipo carattere voce. | No | Sì\* |
| Creare e gestire le distribuzioni di tipi di carattere voce. | No | Sì\* |
| Creare e gestire i test del tipo di carattere voce. | No | Sì\* |
| Gestisci sottoscrizioni. | No | Sì\* |

\**Questi servizi sono disponibili tramite l'endpoint cris.ai. Vedere informazioni di [riferimento su spavalderia](https://westus.cris.ai/swagger/ui/index). Queste API di gestione e formazione vocale personalizzate implementano la limitazione che limita le richieste a 25 per 5 secondi, mentre l'API di sintesi vocale stessa implementa la limitazione che consente 200 richieste al secondo come massime. Quando si verifica la limitazione, si riceverà una notifica tramite le intestazioni del messaggio.*

## <a name="get-started-with-text-to-speech"></a>Inizia a usare sintesi vocale

Sono disponibili guide introduttive progettate per l'esecuzione di codice in meno di 10 minuti. Questa tabella include un elenco di guide introduttive di sintesi vocale organizzate in base alla lingua.

### <a name="sdk-quickstarts"></a>Guide introduttive per SDK

| Guida introduttiva (SDK) | Piattaforma | Informazioni di riferimento per l'API |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-text-to-speech-dotnetcore.md) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#, .NET Framework](quickstart-text-to-speech-dotnet-windows.md) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](quickstart-text-to-speech-csharp-uwp.md) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#, Unity](quickstart-text-to-speech-csharp-unity.md) | Windows, Android | [Browse](https://aka.ms/csspeech/csharpref) |
| [C++](quickstart-text-to-speech-cpp-windows.md) | Windows | [Browse](https://aka.ms/csspeech/cppref) |
| [C++](quickstart-text-to-speech-cpp-linux.md) | Linux | [Browse](https://aka.ms/csspeech/cppref) |

### <a name="rest-quickstarts"></a>Guide introduttive REST

| Guida introduttiva (REST) | Piattaforma | Informazioni di riferimento per l'API |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-dotnet-text-to-speech.md) | Windows, macOS, Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Node.js](quickstart-nodejs-text-to-speech.md) | Finestra, macOS, Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Python](quickstart-python-text-to-speech.md) | Finestra, macOS, Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |

## <a name="sample-code"></a>Codice di esempio

Il codice di esempio per sintesi vocale è disponibile su GitHub. Questi esempi coprono la conversione da sintesi vocale nei linguaggi di programmazione più diffusi.

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
