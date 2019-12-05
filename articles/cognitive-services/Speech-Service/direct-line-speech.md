---
title: Servizio di riconoscimento vocale della linea diretta
titleSuffix: Azure Cognitive Services
description: Cenni preliminari sulle funzionalità, le funzionalità e le restrizioni per gli assistenti vocali che usano Direct Line Speech con il Software Development Kit (SDK) di Speech.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: cec3131d791d591375fd87d1c080294c9034a815
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806219"
---
# <a name="about-direct-line-speech"></a>Informazioni sul discorso diretto sulla linea

Gli [assistenti vocali](voice-assistants.md) ascoltano gli utenti e intraprendono un'azione in risposta e spesso parlano. Usano il [riconoscimento vocale](speech-to-text.md) per trascrivere la voce dell'utente, quindi intervenire sulla comprensione del testo del linguaggio naturale. Questa azione include spesso l'output parlato dell'assistente generato con [sintesi vocale](text-to-speech.md). I dispositivi si connettono agli assistenti con l'oggetto `DialogServiceConnector` dell'SDK di riconoscimento vocale.

**Direct Line Speech** è una soluzione end-to-end affidabile per la creazione di un assistente vocale flessibile ed estendibile, basato su bot Framework e sul canale per la comunicazione diretta diretta, ottimizzato per l'interazione con il Voice-in e l'intervento vocale con i bot.

Direct Line Speech offre i livelli più elevati di personalizzazione e complessità per gli assistenti vocali. È particolarmente adatto agli scenari di conversazione aperti, naturali o ibridi di questi scenari con il completamento delle attività o l'uso del comando e del controllo. Questo elevato livello di flessibilità è caratterizzato da una maggiore complessità e gli scenari che hanno come ambito attività ben definite usando input in linguaggio naturale potrebbero voler prendere in considerazione [comandi personalizzati (anteprima)](custom-commands.md) per un'esperienza di soluzione semplificata.

## <a name="getting-started-with-direct-line-speech"></a>Introduzione a direct line Speech

I primi passaggi per la creazione di un assistente vocale con la sintesi vocale diretta sono l' [ottenimento di una chiave di sottoscrizione vocale](get-started.md), la creazione di un nuovo bot associato a tale sottoscrizione e la connessione del bot al canale di riconoscimento vocale diretto.

   ![Diagramma concettuale del flusso del servizio di orchestrazione vocale direct line](media/voice-assistants/overview-directlinespeech.png "Flusso del canale vocale")

Per una guida completa e dettagliata sulla creazione di un semplice assistente vocale con la voce Direct Line, vedere [l'esercitazione per l'abilitazione vocale del bot con l'SDK di riconoscimento vocale e il canale di sintesi vocale diretta](tutorial-voice-enable-your-bot-speech-sdk.md).

Sono inoltre disponibili guide introduttive progettate per l'esecuzione di codice in meno di 10 minuti. Questa tabella include un elenco di guide introduttive per Assistente vocale organizzate in base alla lingua.

| Avvio rapido | Piattaforma | Informazioni di riferimento sulle API |
|------------|----------|---------------|
| C#, UWP | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| Java | Windows, macOS, Linux | [Browse](https://aka.ms/csspeech/javaref) |
| Java | Android | [Browse](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Codice di esempio

Il codice di esempio per la creazione di un assistente vocale è disponibile su GitHub. Questi esempi coprono l'applicazione client per la connessione all'assistente in diversi linguaggi di programmazione più diffusi.

* [Voice assistant samples (SDK)](https://aka.ms/csspeech/samples) (Esempi di assistente vocale - SDK)
* [Esercitazione: abilitare l'Assistente vocale con l'SDK di riconoscimento vocale,C#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="customization"></a>Personalizzazione

Gli assistenti vocali creati con il servizio di riconoscimento vocale possono usare la gamma completa di opzioni di personalizzazione disponibili per la scelta [vocale](speech-to-text.md) [, sintesi vocale e](text-to-speech.md)selezione di [parole chiave personalizzate](speech-devices-sdk-create-kws.md).

> [!NOTE]
> Le opzioni di personalizzazione variano in base alla lingua o alle impostazioni locali (vedere [lingue supportate](supported-languages.md)).

Il riconoscimento diretto della linea e la relativa funzionalità associata per gli assistenti vocali costituiscono un complemento ideale per la [soluzione Virtual Assistant e il modello Enterprise](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview). Sebbene la comunicazione diretta diretta possa funzionare con qualsiasi bot compatibile, queste risorse forniscono una linea di base riutilizzabile per le esperienze di alta qualità, nonché le competenze e i modelli di supporto più comuni per iniziare rapidamente.

## <a name="reference-docs"></a>Documenti di riferimento

* [Speech SDK](speech-sdk-reference.md)
* [Servizio Azure Bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere gratuitamente una chiave di sottoscrizione per i servizi di riconoscimento vocale](get-started.md)
* [Ottenere l'SDK di riconoscimento vocale](speech-sdk.md)
* [Creare e distribuire un bot di base](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [Ottenere la soluzione Virtual Assistant e il modello Enterprise](https://github.com/Microsoft/AI)
