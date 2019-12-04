---
title: Servizio riconoscimento vocale vocale
titleSuffix: Azure Cognitive Services
description: La funzionalità di riconoscimento vocale consente la trascrizione in tempo reale dei flussi audio in testo che le applicazioni, gli strumenti o i dispositivi possono utilizzare, visualizzare e agire come input del comando. Questo servizio funziona senza interruzioni con le funzionalità di sintesi vocale e traduzione vocale.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: erhopf
ms.openlocfilehash: f04ad388922ad7f73bf4409f9a846291cbb08da3
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74774009"
---
# <a name="what-is-speech-to-text"></a>Che cos'è il riconoscimento vocale?

Riconoscimento vocale da servizi vocali di Azure, noto anche come riconoscimento vocale, consente la trascrizione in tempo reale dei flussi audio in testo che le applicazioni, gli strumenti o i dispositivi possono utilizzare, visualizzare e agire come input del comando. Questo servizio è basato sulla stessa tecnologia di riconoscimento utilizzata da Microsoft per i prodotti Cortana e Office e funziona senza interruzioni con la traduzione e la sintesi vocale. Per un elenco completo delle lingue di sintesi vocale disponibili, vedere [linguaggi supportati](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#speech-to-text).

Per impostazione predefinita, il servizio riconoscimento vocale usa il modello di linguaggio universale. Questo modello è stato sottoposto a training usando i dati di proprietà di Microsoft e viene distribuito nel cloud. È ideale per gli scenari di conversazione e di dettatura. Se si usa il riconoscimento vocale per il riconoscimento e la trascrizione in un ambiente univoco, è possibile creare ed eseguire il training di modelli acustici, linguistici e di pronuncia personalizzati destinati a un ambiente rumoroso o una terminologica specifica del settore.

È possibile acquisire facilmente audio da un microfono, leggere da un flusso o accedere a file audio dalla risorsa di archiviazione con l'SDK di riconoscimento vocale e le API REST. Speech SDK supporta audio WAV/PCM a 16 bit, 16 kHz/8 kHz, a singolo canale per il riconoscimento vocale. Sono supportati formati audio aggiuntivi usando l' [endpoint REST di riconoscimento vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) o il servizio di [trascrizione batch](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats).

## <a name="core-features"></a>Funzionalità di base

Di seguito sono riportate le funzionalità disponibili tramite l'SDK vocale e le API REST:

| Caso d'uso | SDK | REST |
|--------- | --- | ---- |
| Trascrivere brevi espressioni (< 15 secondi). Supporta solo un risultato finale della trascrizione. | SÌ | Sì\* |
| Trascrizione continua di espressioni Long e audio di streaming (> 15 secondi). Supporta i risultati della trascrizione temporanea e finale. | SÌ | No |
| Derivare gli Intent dai risultati del riconoscimento con [Luis](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis). | SÌ | Nessun\*\* |
| Trascrizione batch di file audio in modo asincrono. | No  | Sì\*\*\* |
| Creare e gestire i modelli di riconoscimento vocale. | No | Sì\*\*\* |
| Creare e gestire distribuzioni di modelli personalizzati. | No  | Sì\*\*\* |
| Creare test di accuratezza per misurare l'accuratezza del modello di base rispetto ai modelli personalizzati. | No  | Sì\*\*\* |
| Gestisci sottoscrizioni. | No  | Sì\*\*\* |

\*_usando la funzionalità Rest è possibile trasferire fino a 60 secondi di audio e ricevere un risultato finale della trascrizione._

\*\*_Luis Intent ed Entities possono essere derivati usando una sottoscrizione Luis separata. Con questa sottoscrizione, l'SDK chiama LUIS per l'utente e fornisce risultati di entità e finalità. Con l'API REST, si chiama LUIS per ricavare gli Intent e le entità con la sottoscrizione LUIS._

\*\*\*_questi servizi sono disponibili tramite l'endpoint cris.ai. Vedere informazioni di [riferimento su spavalderia](https://westus.cris.ai/swagger/ui/index)._

## <a name="get-started-with-speech-to-text"></a>Introduzione alla sintesi vocale

Sono disponibili modelli di avvio rapido nei linguaggi di programmazione più diffusi, ognuno progettato per consentire all'utente di disporre dell'esecuzione di codice in meno di 10 minuti. [Questa tabella](https://aka.ms/csspeech#5-minute-quickstarts) include un elenco completo delle guide introduttive per l'SDK vocale organizzate in base alla piattaforma e alla lingua. Informazioni di riferimento sulle API sono disponibili anche [qui](https://aka.ms/csspeech#reference).

Se si preferisce usare il servizio REST di riconoscimento vocale, vedere [API REST](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

## <a name="tutorials-and-sample-code"></a>Esercitazioni e codice di esempio

Dopo aver usato i servizi Voce, provare l'esercitazione che illustra come riconoscere le finalità dai contenuti vocali con Speech SDK e LUIS.

- [Esercitazione: riconoscere gli Intent dalla sintesi vocale con l'SDK vocale e LUIS,C#](how-to-recognize-intents-from-speech-csharp.md)

Il codice di esempio per l'SDK di riconoscimento vocale è disponibile su GitHub. Questi esempi esaminano gli scenari comuni, ad esempio la lettura di audio da un file o streaming, il riconoscimento continuo e singolo e l'uso di modelli personalizzati.

- [Esempi di riconoscimento vocale (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Batch transcription samples (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch) (Esempi di trascrizione batch (REST))

## <a name="customization"></a>Personalizzazione

Oltre al modello di base standard usato dai servizi di riconoscimento vocale, è possibile personalizzare i modelli in base alle esigenze con i dati disponibili, per superare gli ostacoli al riconoscimento vocale, ad esempio lo stile parlato, il vocabolario e il rumore di fondo, vedere [riconoscimento vocale personalizzato](how-to-custom-speech.md)

> [!NOTE]
> Le opzioni di personalizzazione variano in base alla lingua o alle impostazioni locali (vedere [lingue supportate](supported-languages.md)).

## <a name="migration-guides"></a>Guide alla migrazione

> [!WARNING]
> Il riconoscimento vocale Bing è stato ritirato il 15 ottobre 2019.

Se le applicazioni, gli strumenti o i prodotti usano le API riconoscimento vocale Bing o Riconoscimento vocale personalizzato, sono state create guide che consentono di eseguire la migrazione ai servizi di riconoscimento vocale.

- [Eseguire la migrazione dal riconoscimento vocale Bing ai servizi di riconoscimento vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-bing-speech)
- [Eseguire la migrazione da Riconoscimento vocale personalizzato ai servizi di riconoscimento vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-custom-speech-service)

## <a name="reference-docs"></a>Documenti di riferimento

- [Speech SDK](https://aka.ms/csspeech)
- [Speech Devices SDK](speech-devices-sdk.md)
- [API REST: sintesi vocale](rest-speech-to-text.md)
- [API REST: sintesi vocale](rest-text-to-speech.md)
- [API REST: trascrizione e personalizzazione di batch](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Passaggi successivi

- [Ottieni gratuitamente una chiave di sottoscrizione per i servizi Voce](get-started.md)
- [Ottenere l'SDK di riconoscimento vocale](speech-sdk.md)
