---
title: Comandi personalizzati-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Panoramica delle funzionalità, delle funzionalità e delle restrizioni per i comandi personalizzati, una soluzione per la creazione di applicazioni vocali.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: 0800a287b747a8a421958d20f0b1ce56247d6d01
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2020
ms.locfileid: "85362160"
---
# <a name="what-is-custom-commands"></a>Che cosa sono i comandi personalizzati?

Le applicazioni, ad esempio gli [assistenti vocali](voice-assistants.md) , restano in ascolto degli utenti e intraprendono un'azione in risposta e spesso parlano. Usano il [riconoscimento vocale](speech-to-text.md) per trascrivere la voce dell'utente, quindi intervenire sulla comprensione del testo del linguaggio naturale. Questa azione include spesso l'output parlato dell'assistente generato con [sintesi vocale](text-to-speech.md). I dispositivi si connettono agli assistenti con l'oggetto dell'SDK di riconoscimento vocale `DialogServiceConnector` .

I **comandi personalizzati** semplificano la creazione di app avanzate per i comandi vocali ottimizzate per esperienze di interazione vocali-First. Offre un'esperienza di creazione unificata, un modello di hosting automatico e una complessità relativamente inferiore, che ti aiuta a concentrarti sulla creazione della soluzione migliore per gli scenari di comandi vocali.

I comandi personalizzati sono ideali per gli scenari di completamento delle attività, di comando e di controllo, in particolare per i dispositivi Internet delle cose (Internet), per i dispositivi di ambiente e senza intestazioni. Gli esempi includono soluzioni per l'ospitalità, il commercio al dettaglio e il settore automobilistico, che consente di creare le migliori esperienze di controllo vocale in locale per gli utenti guest, gestire l'inventario nell'archivio e controllare le funzionalità in-car durante lo spostamento.

> [!TIP]
> Visualizza le demo di esempio nella pagina di destinazione all'indirizzo [https://speech.microsoft.com/customcommands](https://speech.microsoft.com/customcommands) .

Se si è interessati a creare app di conversazione complesse, è consigliabile provare il Framework bot usando la [soluzione Virtual Assistant](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview). È possibile aggiungere voce a qualsiasi bot del Framework bot usando Direct Line Speech.

I candidati validi per i comandi personalizzati hanno un vocabolario fisso con set di variabili ben definiti. Ad esempio, le attività di automazione domestica, come il controllo di un termostato, sono ideali.

   ![Esempi di scenari di completamento delle attività](media/voice-assistants/task-completion-examples.png "esempi di completamento delle attività")

## <a name="getting-started-with-custom-commands"></a>Introduzione ai comandi personalizzati

Il nostro obiettivo con i comandi personalizzati è quello di ridurre il carico cognitivo per apprendere tutte le diverse tecnologie e concentrare la creazione dell'app per i comandi vocali. Primo passaggio per l'uso di comandi personalizzati per <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">creare una risorsa <span class="docon docon-navigate-external x-hidden-focus"></span> vocale di Azure </a>. È possibile creare l'app per i comandi personalizzati in speech studio e pubblicarla, dopo la quale un'applicazione su dispositivo può comunicare con l'app usando l'SDK di riconoscimento vocale.

#### <a name="authoring-flow-for-custom-commands"></a>Creazione di un flusso per i comandi personalizzati
   ![Creazione di un flusso per i comandi personalizzati](media/voice-assistants/custom-commands-flow.png "Flusso di creazione dei comandi personalizzati")

Seguire la Guida introduttiva per fare in modo che la prima app comandi personalizzati esegua il codice in meno di 10 minuti.

* [Creare un assistente vocale con comandi personalizzati](quickstart-custom-commands-application.md)

Una volta completata la Guida introduttiva, esplorare le guide alle procedure per i passaggi dettagliati per la progettazione, lo sviluppo, il debug, la distribuzione e l'integrazione di un'applicazione comandi personalizzata.

## <a name="building-voice-assistants-with-custom-commands"></a>Creazione di assistenti vocali con comandi personalizzati
> [!VIDEO https://www.youtube.com/embed/1zr0umHGFyc]

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere gratuitamente una chiave di sottoscrizione per i servizi di riconoscimento vocale](get-started.md)
* [Visualizzare il repository di assistenti vocali su GitHub per gli esempi](https://aka.ms/speech/cc-samples)
* [Passare a speech studio per provare i comandi personalizzati](https://speech.microsoft.com/customcommands)
* [Ottenere Speech SDK](speech-sdk.md)
