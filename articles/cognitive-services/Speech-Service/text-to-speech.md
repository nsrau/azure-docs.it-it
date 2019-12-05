---
title: Servizio sintesi vocale
titleSuffix: Azure Cognitive Services
description: La funzionalità di sintesi vocale nel servizio di riconoscimento vocale consente a applicazioni, strumenti o dispositivi di convertire testo in sintesi vocale naturale, ad esempio. Scegliere le voci predefinite o creare una voce personalizzata.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: erhopf
ms.openlocfilehash: 85022d52c9513e53da31cc3c962f3a54dd538371
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815480"
---
# <a name="what-is-text-to-speech"></a>Che cos'è la sintesi vocale?

Sintesi vocale dal servizio di riconoscimento vocale consente a applicazioni, strumenti o dispositivi di convertire testo in sintesi vocale naturale, ad esempio. È possibile scegliere tra le voci standard e neurali oppure creare una voce personalizzata univoca per il prodotto o il marchio. 75 e le voci standard sono disponibili in più di 45 lingue e impostazioni locali e 5 voci neurali sono disponibili in 4 lingue e impostazioni locali. Per un elenco completo, vedere [linguaggi supportati](language-support.md#text-to-speech).

La tecnologia sintesi vocale consente agli autori di contenuti di interagire con gli utenti in modi diversi. La sintesi vocale può migliorare l'accessibilità offrendo agli utenti la possibilità di interagire con il contenuto in modo impercettibile. Indipendentemente dal fatto che l'utente abbia problemi visivi, una disabilità di apprendimento o che richiede informazioni di navigazione durante la guida, la sintesi vocale può migliorare un'esperienza esistente. Il riconoscimento vocale è anche un componente aggiuntivo prezioso per bot vocali e assistenti vocali.

Grazie all'uso di SSML (Speech Synthesis Markup Language), un linguaggio di markup basato su XML, gli sviluppatori che usano il servizio di sintesi vocale possono specificare il modo in cui il testo di input viene convertito in sintesi vocale. Con SSML è possibile modificare il pitch, la pronuncia, la velocità di pronuncia, il volume e altro ancora. Per ulteriori informazioni, vedere [SSML](#speech-synthesis-markup-language-ssml).

### <a name="standard-voices"></a>Voci standard

Le voci standard vengono create tramite la sintesi parametrica statistica e/o le tecniche di sintesi della concatenazione. Queste voci sono molto comprensibili e sono naturali. È possibile abilitare facilmente le applicazioni in modo che parlino in più di 45 lingue, con una vasta gamma di opzioni vocali. Queste voci forniscono un'accuratezza della pronuncia elevata, incluso il supporto per le abbreviazioni, le espansioni di acronimo, le interpretazioni di data/ora, i politelefoni e altro ancora. Usare la voce standard per migliorare l'accessibilità per le applicazioni e i servizi consentendo agli utenti di interagire con i contenuti in modo impercettibile.

### <a name="neural-voices"></a>Voci neurali

Le voci neurali usano reti neurali profonde per superare i limiti dei sistemi di sintesi vocale tradizionali, in modo da abbinare i modelli di stress e intonazione in linguaggio parlato e sintetizzare le unità di riconoscimento vocale in una voce del computer. La sintesi vocale standard suddivide prosodia in operazioni di analisi linguistica separate e di stime acustiche gestite da modelli indipendenti, che possono comportare una sintesi vocale ovattata. La funzionalità neurale esegue contemporaneamente la previsione della prosodia e la sintesi vocale e genera quindi una voce più fluida e più realistica.

Le voci neurali possono essere usate per rendere più naturali e accattivanti le interazioni con chatbot e gli assistenti vocali, convertire i testi digitali, ad esempio gli e-book in Audiolibri e migliorare i sistemi di navigazione in auto. Con la prosodia naturale e una chiara articolazione di parole, le voci neurali riducono significativamente la fatica in ascolto quando si interagisce con i sistemi di intelligenza artificiale.

Le voci neurali supportano stili diversi, ad esempio neutro e allegro. Ad esempio, la voce Jessa (en-US) può parlare allegramente, che è ottimizzata per conversazioni calde e felici. È possibile modificare l'output vocale, ad esempio tono, passo e velocità usando [sintesi vocale linguaggio di markup](speech-synthesis-markup.md). Per un elenco completo delle voci disponibili, vedere [linguaggi supportati](language-support.md#text-to-speech).

Per altre informazioni sui vantaggi delle voci neurali, vedere il [nuovo servizio di sintesi vocale neurale Microsoft per aiutare i computer a parlare di persone](https://azure.microsoft.com/blog/microsoft-s-new-neural-text-to-speech-service-helps-machines-speak-like-people/).

### <a name="custom-voices"></a>Voci personalizzate

La personalizzazione vocale consente di creare una voce riconoscibile, uno di tipo, per il marchio. Per creare il tipo di carattere vocale personalizzato, è necessario eseguire una registrazione in studio e caricare gli script associati come dati di training. Il servizio crea quindi un modello vocale univoco ottimizzato per la registrazione. È possibile utilizzare questo tipo di carattere vocale personalizzato per sintetizzare la sintesi vocale. Per altre informazioni, vedere [voci personalizzate](how-to-customize-voice-font.md).

## <a name="speech-synthesis-markup-language-ssml"></a>Speech Synthesis Markup Language (SSML)

Speech Synthesis Markup Language (SSML) è un linguaggio di markup basato su XML che consente agli sviluppatori di specificare il modo in cui il testo di input viene convertito in sintesi vocale usando il servizio di sintesi vocale. Rispetto a testo normale, SSML consente agli sviluppatori di ottimizzare il pitch, la pronuncia, la velocità di pronuncia, il volume e altro ancora dell'output da sintesi vocale. La punteggiatura normale, ad esempio la sospensione dopo un periodo, o l'uso dell'intonazione corretta quando una frase termina con un punto interrogativo viene gestita automaticamente.

Tutti gli input di testo inviati al servizio di sintesi vocale devono essere strutturati come SSML. Per ulteriori informazioni, vedere [linguaggio di markup sintesi vocale](speech-synthesis-markup.md).

### <a name="pricing-note"></a>Nota sui prezzi

Quando si usa il servizio di sintesi vocale, viene addebitato ogni carattere convertito in sintesi vocale, inclusa la punteggiatura. Anche se il documento SSML non è fatturabile, gli elementi facoltativi utilizzati per regolare la conversione del testo in sintesi vocale, ad esempio fonemi e pitch, vengono conteggiati come caratteri fatturabili. Ecco un elenco degli elementi fatturabili:

- Testo passato al servizio di sintesi vocale nel corpo SSML della richiesta
- Tutti i markup all'interno del campo di testo del corpo della richiesta nel formato SSML, ad eccezione dei tag `<speak>` e `<voice>`
- Lettere, punteggiatura, spazi, tabulazioni, markup e tutti gli spazi vuoti
- Ogni elemento di codice definito in Unicode

Per informazioni dettagliate, vedere [prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

> [!IMPORTANT]
> Ogni carattere di lingua cinese, giapponese e coreano viene conteggiato come due caratteri per la fatturazione.

## <a name="core-features"></a>Funzionalità di base

Questa tabella elenca le funzionalità di base per sintesi vocale:

| Caso d'uso                                  | SDK | REST  |
| ----------------------------------------- | --- | ----- |
| Converte il testo in sintesi vocale.                   | SÌ | SÌ   |
| Caricare i set di impostazioni per l'adattamento vocale.     | No  | Sì\* |
| Creare e gestire i modelli di tipo carattere voce.      | No  | Sì\* |
| Creare e gestire le distribuzioni di tipi di carattere voce. | No  | Sì\* |
| Creare e gestire i test del tipo di carattere voce.       | No  | Sì\* |
| Gestisci sottoscrizioni.                     | No  | Sì\* |

\*_questi servizi sono disponibili tramite l'endpoint cris.ai. Vedere informazioni di [riferimento su spavalderia](https://westus.cris.ai/swagger/ui/index). Queste API di gestione e formazione vocale personalizzate implementano la limitazione che limita le richieste a 25 per 5 secondi, mentre l'API di sintesi vocale stessa implementa la limitazione che consente 200 richieste al secondo come massime. Quando si verifica la limitazione, si riceverà una notifica tramite le intestazioni del messaggio._

## <a name="get-started-with-text-to-speech"></a>Inizia a usare sintesi vocale

Sono disponibili guide introduttive progettate per l'esecuzione di codice in meno di 10 minuti. Questa tabella include un elenco di guide introduttive di sintesi vocale organizzate in base alla lingua.

### <a name="sdk-quickstarts"></a>Guide introduttive per SDK

| Guida introduttiva (SDK) | Piattaforma | Informazioni di riferimento sulle API |
| ---------------- | -------- | ------------- |
| [C#, .NET Core](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp&tabs=dotnetcore)  | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#.NET Framework](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp&tabs=dotnet) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp&tabs=uwp) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#, Unity](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp&tabs=unity) | Windows, Android | [Browse](https://aka.ms/csspeech/csharpref) |
| [C++](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-cpp&tabs=windows) | Windows | [Browse](https://aka.ms/csspeech/cppref) |
| [C++](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-cpp&tabs=linux) | Linux | [Browse](https://aka.ms/csspeech/cppref) |
| [Java](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-java&tabs=jre) | Windows, Linux, macOS | [Browse](https://aka.ms/csspeech/javaref) |
| [Java](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-java&tabs=android) | Android | [Browse](https://aka.ms/csspeech/javaref) |
| [Objective-C](~/articles/cognitive-services/speech-service/quickstarts/text-to-speech-langs/objectivec-macos.md) | macOS | [Browse](https://aka.ms/csspeech/objectivecref) |
| [Objective-C](~/articles/cognitive-services/speech-service/quickstarts/text-to-speech-langs/objectivec-ios.md) | iOS | [Browse](https://aka.ms/csspeech/objectivecref) |
| Swift (~/articles/cognitive-Services/Speech-Service/QuickStarts/Text-to-Speech-langs/Swift-MacOS.MD | macOS | [Browse](https://aka.ms/csspeech/objectivecref) |
| [Swift](~/articles/cognitive-services/speech-service/quickstarts/text-to-speech-langs/swift-ios.md) | iOS | [Browse](https://aka.ms/csspeech/objectivecref) |
| [Python](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-python) | Windows, Linux, macOS | [Browse](https://aka.ms/csspeech/pythonref) |

### <a name="rest-quickstarts"></a>Guide introduttive REST

| Guida introduttiva (REST) | Piattaforma | Informazioni di riferimento sulle API |
| ----------------- | -------- | ------------- |
| [C#, .NET Core](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp) | Windows, macOS, Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Node.js](quickstart-nodejs-text-to-speech.md) | Finestra, macOS, Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Python](quickstart-python-text-to-speech.md) | Finestra, macOS, Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |

## <a name="sample-code"></a>Codice di esempio

Il codice di esempio per sintesi vocale è disponibile su GitHub. Questi esempi coprono la conversione da sintesi vocale nei linguaggi di programmazione più diffusi.

- [Esempi di sintesi vocale (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Text-to-speech samples (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS) (Esempi di sintesi vocale (REST))

## <a name="reference-docs"></a>Documenti di riferimento

- [Speech SDK](speech-sdk-reference.md)
- [Speech Devices SDK](speech-devices-sdk.md)
- [API REST: sintesi vocale](rest-speech-to-text.md)
- [API REST: sintesi vocale](rest-text-to-speech.md)
- [API REST: trascrizione e personalizzazione di batch](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Passaggi successivi

- [Ricevi una sottoscrizione gratuita per il servizio riconoscimento vocale](get-started.md)
- [Creare caratteri voce personalizzati](how-to-customize-voice-font.md)
