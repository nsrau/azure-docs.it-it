---
title: Riconoscimento vocale direct Line - Servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Panoramica delle funzionalità, delle funzionalità e delle restrizioni per gli assistenti vocali che utilizzano Direct Line Speech con Speech Software Development Kit (SDK).
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: dapine
ms.openlocfilehash: 714cdefa7100160a510d1922a2547ae53692d76c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367806"
---
# <a name="what-is-direct-line-speech"></a>Che cos'è Direct Line Speech?

**Direct Line Speech** è una soluzione end-to-end robusta per la creazione di un assistente vocale flessibile ed estensibile. È alimentato da Bot Framework e il suo canale Direct Line Speech, ottimizzato per l'interazione voice-in, voice-out con i bot.

[Gli assistenti vocali ascoltano](voice-assistants.md) gli utenti e intervengono in risposta, spesso rispondendo. Usano [la sintesi vocale](speech-to-text.md) per trascrivere il discorso dell'utente, quindi intervengono sulla comprensione del linguaggio naturale del testo. Questa azione include spesso l'output parlato dell'assistente generato con [sintesi vocale](text-to-speech.md).

Direct Line Speech offre i più alti livelli di personalizzazione e raffinatezza per gli assistenti vocali. È progettato per scenari conversazionali aperti, naturali o ibridi dei due con completamento delle attività o l'uso di comando e controllo. Questo elevato livello di flessibilità comporta una maggiore complessità e gli scenari che hanno come ambito le attività ben definite che usano l'input in linguaggio naturale potrebbero voler prendere in considerazione i [comandi personalizzati (anteprima)](custom-commands.md) per un'esperienza di soluzione semplificata.

## <a name="getting-started-with-direct-line-speech"></a>Introduzione al riconoscimento diretto

I primi passaggi per la creazione di un assistente vocale tramite Direct Line Speech sono ottenere una chiave di [sottoscrizione,](get-started.md)creare un nuovo bot associato a tale sottoscrizione e connettere il bot al canale Direct Line Speech.

   ![Diagramma concettuale del flusso del servizio di orchestrazione Direct Line SpeechConceptual diagram of the Direct Line Speech orchestration service flow](media/voice-assistants/overview-directlinespeech.png "Il flusso del canale di riconoscimento vocale")

Per una guida completa e dettagliata sulla creazione di un semplice assistente vocale tramite Direct Line Speech, vedere [l'esercitazione per l'abilitazione](tutorial-voice-enable-your-bot-speech-sdk.md)del riconoscimento vocale con Speech SDK e il canale Direct Line Speech .

Offriamo anche guide introduttive progettate per farti eseguire il codice e imparare rapidamente le API. Questa tabella include un elenco di guide introduttive dell'assistente vocale organizzate per lingua e piattaforma.

| Guida introduttiva | Piattaforma | API (riferimento) |
|------------|----------|---------------|
| C,, UWP | WINDOWS | [Sfoglia](https://aka.ms/csspeech/csharpref) |
| Java | Windows, macOS, Linux | [Sfoglia](https://aka.ms/csspeech/javaref) |
| Java | Android | [Sfoglia](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Codice di esempio

Il codice di esempio per la creazione di un assistente vocale è disponibile in GitHub.Sample code for creating a voice assistant is available on GitHub. In questi esempi viene trattata l'applicazione client per la connessione all'assistente in diversi linguaggi di programmazione comuni.

* [Voice assistant samples (SDK)](https://aka.ms/csspeech/samples) (Esempi di assistente vocale - SDK)
* [Esercitazione: La voce abilita l'assistente con Speech SDK, C #](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="customization"></a>Personalizzazione

Gli assistenti vocali creati utilizzando il servizio di riconoscimento vocale possono utilizzare l'intera gamma di opzioni di personalizzazione disponibili per la [sintesi vocale,](speech-to-text.md) [la sintesi vocale](text-to-speech.md)e la [selezione di parole chiave personalizzate.](speech-devices-sdk-create-kws.md)

> [!NOTE]
> Le opzioni di personalizzazione variano in base alla lingua/impostazioni locali (vedere [Lingue supportate](supported-languages.md)).

Direct Line Speech e le relative funzionalità per gli assistenti vocali sono un supplemento ideale alla [soluzione Virtual Assistant e al modello Enterprise.](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) Anche se Direct Line Speech può funzionare con qualsiasi bot compatibile, queste risorse forniscono una linea di base riutilizzabile per esperienze conversazionali di alta qualità, nonché competenze e modelli di supporto comuni per iniziare rapidamente.

## <a name="reference-docs"></a>Documentazione di riferimento

* [Speech SDK](speech-sdk-reference.md)
* [Servizio bot di AzureAzure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere gratuitamente una chiave di sottoscrizione per i servizi di riconoscimento vocale](get-started.md)
* [Ottenere Speech SDK](speech-sdk.md)
* [Creare e distribuire un bot di baseCreate and deploy a basic bot](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [Ottenere la soluzione Virtual Assistant e il modello EnterpriseGet the Virtual Assistant Solution and Enterprise Template](https://github.com/Microsoft/AI)
