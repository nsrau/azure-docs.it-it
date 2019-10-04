---
title: Assistenti virtuali personalizzati per la prima volta (anteprima)-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Panoramica delle funzionalità, delle funzionalità e delle restrizioni per gli assistenti virtuali personalizzati per la prima voce tramite il canale di sintesi vocale direct line in bot Framework e il Software Development Kit (SDK) per i servizi cognitivi.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: travisw
ms.openlocfilehash: f77a95af1edcd37043ea1e067560577140a81a04
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967597"
---
# <a name="about-custom-voice-first-virtual-assistants-preview"></a>Informazioni sull'anteprima degli assistenti virtuali per la prima voce personalizzata

Gli assistenti virtuali personalizzati che usano i servizi Voce di Azure consentono agli sviluppatori di creare interfacce di conversazione naturali e simili a quelle umane per applicazioni ed esperienze. Il canale Direct Line Speech di Bot Framework migliora tali funzionalità offrendo un punto di ingresso coordinato e orchestrato a un bot compatibile che abilita un'interazione vocale in ingresso e in uscita caratterizzata da una bassa latenza e da un'elevata affidabilità. Questi bot possono usare Microsoft Language Understanding (LUIS) per l'interazione del linguaggio naturale. L'accesso diretto alla comunicazione diretta è consentito ai dispositivi che usano Speech Software Development Kit (SDK).

   ![Diagramma concettuale del flusso del servizio di orchestrazione vocale Direct Line](media/voice-first-virtual-assistants/overview.png "Flusso del canale vocale")


Il riconoscimento diretto della linea e la relativa funzionalità associata per assistenti virtuali personalizzati per la prima volta rappresentano un complemento ideale per la [soluzione Virtual Assistant e per il modello Enterprise](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview). Sebbene la comunicazione diretta diretta possa funzionare con qualsiasi bot compatibile, queste risorse forniscono una linea di base riutilizzabile per le esperienze di alta qualità, nonché le competenze e i modelli di supporto più comuni per iniziare rapidamente.


## <a name="core-features"></a>Funzionalità di base

| Category | Funzionalità |
|----------|----------|
|[Parola di riattivazione personalizzata](speech-devices-sdk-create-kws.md) | È possibile consentire agli utenti di iniziare le conversazioni con i bot usando una parola chiave personalizzata, ad esempio "Hey contoso". Questa attività viene eseguita con un motore di Word di riattivazione personalizzato nell'SDK vocale, che può essere configurato con una parola di riattivazione personalizzata [che è possibile generare qui](speech-devices-sdk-create-kws.md). Il canale di sintesi vocale della linea diretta include la verifica della parola di riattivazione sul lato servizio che migliora l'accuratezza dell'attivazione della parola di riattivazione rispetto al dispositivo.
|[Riconoscimento vocale](speech-to-text.md) | Il canale di sintesi vocale della linea diretta include la trascrizione in tempo reale dell'audio nel testo riconosciuto usando il [riconoscimento vocale](speech-to-text.md) da servizi vocali di Azure. Questo testo è disponibile sia per il bot che per l'applicazione client mentre viene trasformata.
|[Sintesi vocale](text-to-speech.md) | Le risposte testuali dal bot verranno sintetizzate tramite sintesi [vocale](text-to-speech.md) da servizi vocali di Azure. Questa sintesi verrà quindi resa disponibile all'applicazione client come flusso audio. Microsoft offre la possibilità di creare una voce personalizzata neurale e di alta qualità che fornisca una voce al proprio marchio, per saperne di più su [Contattaci](mailto:mstts@microsoft.com).
|[Sintesi vocale diretta](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech) | Come canale in bot Framework, direct line Speech consente una connessione semplice e uniforme tra l'applicazione client, un bot compatibile e le funzionalità dei servizi di riconoscimento vocale di Azure. Per ulteriori informazioni sulla configurazione del bot per l'utilizzo del canale di riconoscimento vocale diretto, vedere la [relativa pagina nella documentazione di bot Framework](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

## <a name="get-started-with-virtual-assistants"></a>Inizia a usare gli assistenti virtuali

Sono disponibili guide introduttive progettate per l'esecuzione di codice in meno di 10 minuti. Questa tabella include un elenco di guide introduttive di assistente virtuale per la prima voce organizzate in base alla lingua.

| Avvio rapido | Piattaforma | Informazioni di riferimento per l'API |
|------------|----------|---------------|
| C#, UWP | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| Java | Windows, macOS, Linux | [Browse](https://aka.ms/csspeech/javaref) |
| Java | Android | [Browse](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Codice di esempio

Il codice di esempio per la creazione di un assistente virtuale Voice-First è disponibile su GitHub. Questi esempi coprono l'applicazione client per la connessione al bot in diversi linguaggi di programmazione più diffusi.

* [Esempi di assistente virtuale per la prima voce (SDK)](https://aka.ms/csspeech/samples)
* [Esercitazione: Voice Abilita il bot con l'SDK di riconoscimento vocale,C#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="tutorial"></a>Esercitazione
Esercitazione su come [abilitare il bot usando l'SDK di riconoscimento vocale e il canale di sintesi vocale Direct Line](tutorial-voice-enable-your-bot-speech-sdk.md).

## <a name="customization"></a>Personalizzazione

Gli assistenti virtuali vocali creati con i servizi di riconoscimento vocale di Azure possono usare la gamma completa di opzioni di personalizzazione disponibili per la selezione [vocale](speech-to-text.md), [sintesi vocale](text-to-speech.md) e [parola chiave personalizzata](speech-devices-sdk-create-kws.md).

> [!NOTE]
> Le opzioni di personalizzazione variano in base alla lingua o alle impostazioni locali (vedere [lingue supportate](supported-languages.md)).

## <a name="reference-docs"></a>Documentazione di riferimento

* [Speech SDK](speech-sdk-reference.md)
* [Servizio Azure Bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Passaggi successivi

* [Ottieni gratuitamente una chiave di sottoscrizione per i servizi Voce](get-started.md)
* [Ottenere l'SDK di riconoscimento vocale](speech-sdk.md)
* [Creare e distribuire un bot di base](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [Ottenere la soluzione Virtual Assistant e il modello Enterprise](https://github.com/Microsoft/AI)
