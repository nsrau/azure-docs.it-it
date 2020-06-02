---
title: Comandi personalizzati (anteprima)-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Panoramica delle funzionalità, delle funzionalità e delle restrizioni per i comandi personalizzati (anteprima), una soluzione per la creazione di applicazioni vocali.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: 3c02e823202aa848a4de94885276835899562e4b
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2020
ms.locfileid: "84266016"
---
# <a name="what-is-custom-commands-preview"></a>Che cosa sono i comandi personalizzati (anteprima)?

Le applicazioni vocali come gli [assistenti vocali](voice-assistants.md) ascoltano gli utenti e intraprendono un'azione in risposta e spesso parlano. Usano il [riconoscimento vocale](speech-to-text.md) per trascrivere la voce dell'utente, quindi intervenire sulla comprensione del testo del linguaggio naturale. Questa azione include spesso l'output parlato dell'assistente generato con [sintesi vocale](text-to-speech.md). I dispositivi si connettono agli assistenti con l'oggetto dell'SDK di riconoscimento vocale `DialogServiceConnector` .

**Custom Commands (anteprima)** è una soluzione semplificata per la creazione di applicazioni vocali. Offre un'esperienza di creazione unificata, un modello di hosting automatico e una complessità relativamente inferiore rispetto ad altre opzioni come la [sintesi vocale diretta](direct-line-speech.md). Questa semplificazione, tuttavia, presenta una riduzione della flessibilità. In questo modo, i comandi personalizzati (anteprima) sono ideali per gli scenari di completamento delle attività o di comando e controllo. È particolarmente adatto per Internet delle cose e per i dispositivi senza intestazioni.

Per l'interazione e l'integrazione colloquiale complesse con altre soluzioni come la [soluzione Virtual Assistant e il modello Enterprise,](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) è consigliabile usare la voce direct line.

I candidati validi per i comandi personalizzati (anteprima) hanno un vocabolario fisso con set ben definiti di variabili. Ad esempio, le attività di automazione domestica, come il controllo di un termostato, sono ideali.

   ![Esempi di scenari di completamento delle attività](media/voice-assistants/task-completion-examples.png "esempi di completamento delle attività")

## <a name="getting-started-with-custom-commands-preview"></a>Introduzione ai comandi personalizzati (anteprima)

Il primo passaggio per l'uso di comandi personalizzati (anteprima) per creare un'applicazione vocale è [ottenere una chiave di sottoscrizione vocale](get-started.md) e accedere al generatore di comandi personalizzati (anteprima) in [speech studio](https://speech.microsoft.com). Da qui è possibile creare una nuova applicazione di comandi personalizzati (anteprima) e pubblicarla, dopo la quale un'applicazione su dispositivo può comunicare con l'applicazione tramite l'SDK di riconoscimento vocale.

   ![Flusso di creazione per i comandi personalizzati (anteprima)](media/voice-assistants/custom-commands-flow.png "Flusso di creazione dei comandi personalizzati (anteprima)")

Sono disponibili guide introduttive progettate per l'esecuzione di codice in meno di 10 minuti.

* [Creare un'applicazione di comandi personalizzati (anteprima)](quickstart-custom-speech-commands-create-new.md)
* [Creare un'applicazione di comandi personalizzati (anteprima) con parametri](quickstart-custom-speech-commands-create-parameters.md)
* [Connettersi a un'applicazione di comandi personalizzati (anteprima) con l'SDK di riconoscimento vocale, C #](quickstart-custom-speech-commands-speech-sdk.md)

Al termine delle guide introduttive, è possibile esplorare le procedure.

- [Aggiungere convalide ai parametri di comando personalizzati](./how-to-custom-speech-commands-validations.md)
- [Soddisfare i comandi nel client con l'SDK di riconoscimento vocale](./how-to-custom-speech-commands-fulfill-sdk.md)
- [Aggiungere una conferma a un comando personalizzato](./how-to-custom-speech-commands-confirmations.md)
- [Aggiungere una correzione in un passaggio a un comando personalizzato](./how-to-custom-speech-commands-one-step-correction.md)

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere gratuitamente una chiave di sottoscrizione per i servizi di riconoscimento vocale](get-started.md)
* [Passare a speech studio per provare i comandi personalizzati](https://speech.microsoft.com)
* [Ottenere Speech SDK](speech-sdk.md)
