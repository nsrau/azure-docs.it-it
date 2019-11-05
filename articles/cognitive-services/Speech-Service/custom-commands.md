---
title: Comandi personalizzati (anteprima)-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Panoramica delle funzionalità, delle funzionalità e delle restrizioni per i comandi personalizzati (anteprima), una soluzione per la creazione di assistenti vocali.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: e41a037e657a8d2b484a3fcb62eb3bc03f53682d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507088"
---
# <a name="custom-commands-preview"></a>Comandi personalizzati (anteprima)

Gli [assistenti vocali](voice-assistants.md) ascoltano gli utenti e intraprendono un'azione in risposta e spesso parlano. Usano il [riconoscimento vocale](speech-to-text.md) per trascrivere la voce dell'utente, quindi intervenire sulla comprensione del testo del linguaggio naturale. Questa azione include spesso l'output parlato dell'assistente generato con [sintesi vocale](text-to-speech.md). I dispositivi si connettono agli assistenti con l'oggetto `DialogServiceConnector` dell'SDK di riconoscimento vocale.

**Custom Commands (anteprima)** è una soluzione semplificata per la creazione di un assistente vocale. Offre un'esperienza di creazione unificata, un modello di hosting automatico e una complessità relativamente inferiore rispetto ad altre opzioni di creazione di assistenti come la comunicazione [diretta](direct-line-speech.md). Questa semplificazione, tuttavia, presenta una riduzione della flessibilità. In questo modo, i comandi personalizzati (anteprima) sono ideali per gli scenari di completamento delle attività o di comando e controllo.

Per l'interazione e l'integrazione colloquiale complesse con altre soluzioni come la [soluzione Virtual Assistant e il modello Enterprise,](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) è consigliabile usare la voce direct line.

I candidati validi per i comandi personalizzati (anteprima) hanno un vocabolario fisso con set ben definiti di variabili. Ad esempio, le attività di automazione domestica, come il controllo di un termostato, sono ideali.

   ![Esempi di scenari di completamento delle attività](media/voice-assistants/task-completion-examples.png "esempi di completamento delle attività")

## <a name="getting-started-with-custom-commands-preview"></a>Introduzione ai comandi personalizzati (anteprima)

Il primo passaggio per l'uso di comandi personalizzati (anteprima) per creare un assistente vocale è [ottenere una chiave di sottoscrizione vocale](get-started.md) e accedere al generatore di comandi personalizzati (anteprima) in [speech studio](https://speech.microsoft.com). Da qui è possibile creare una nuova applicazione di comandi personalizzati (anteprima) e pubblicarla, dopo la quale un'applicazione su dispositivo può comunicare con l'applicazione tramite l'SDK di riconoscimento vocale.

   ![Flusso di creazione per i comandi personalizzati (anteprima)](media/voice-assistants/custom-commands-flow.png "Flusso di creazione dei comandi personalizzati (anteprima)")

Sono disponibili guide introduttive progettate per l'esecuzione di codice in meno di 10 minuti.

* [Creare un'applicazione di comandi personalizzati (anteprima)](quickstart-custom-speech-commands-create-new.md)
* [Creare un'applicazione di comandi personalizzati (anteprima) con parametri](quickstart-custom-speech-commands-create-parameters.md)
* [Connettersi a un'applicazione di comandi personalizzati (anteprima) con l'SDK di riconoscimento vocale,C#](quickstart-custom-speech-commands-speech-sdk.md)

## <a name="sample-code"></a>Codice di esempio

Il codice di esempio per la creazione di un assistente vocale con comandi personalizzati (anteprima) è disponibile in GitHub.

* [Esempi di Assistente vocale (SDK)](https://aka.ms/csspeech/samples)

## <a name="customization"></a>Personalizzazione

Gli assistenti vocali creati usando i servizi di riconoscimento vocale di Azure possono usare la gamma completa di opzioni di personalizzazione disponibili per la selezione [vocale](speech-to-text.md) [, la sintesi vocale e](text-to-speech.md)la selezione di [parole chiave personalizzate](speech-devices-sdk-create-kws.md).

> [!NOTE]
> Le opzioni di personalizzazione variano in base alla lingua o alle impostazioni locali (vedere [lingue supportate](supported-languages.md)).

## <a name="reference-docs"></a>Documentazione di riferimento

* [Speech SDK](speech-sdk-reference.md)

## <a name="next-steps"></a>Passaggi successivi

* [Ottieni gratuitamente una chiave di sottoscrizione per i servizi Voce](get-started.md)
* [Ottenere l'SDK di riconoscimento vocale](speech-sdk.md)
