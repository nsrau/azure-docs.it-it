---
title: Comandi personalizzati (anteprima) - Servizio di riconoscimento vocale
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
ms.openlocfilehash: 2e1b6ee0bd6c392804915fac6ff23278a00b6d33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367840"
---
# <a name="what-are-custom-commands-preview"></a>Che cosa sono i comandi personalizzati (anteprima)?

Le applicazioni vocali come [gli assistenti vocali ascoltano](voice-assistants.md) gli utenti e intervengono in risposta, spesso rispondendo. Usano [la sintesi vocale](speech-to-text.md) per trascrivere il discorso dell'utente, quindi intervengono sulla comprensione del linguaggio naturale del testo. Questa azione include spesso l'output parlato dell'assistente generato con [sintesi vocale](text-to-speech.md). I dispositivi si connettono agli `DialogServiceConnector` assistenti con l'oggetto speech SDK.

**I comandi personalizzati (anteprima)** sono una soluzione semplificata per la creazione di applicazioni vocali. Fornisce un'esperienza di creazione unificata, un modello di hosting automatico e una complessità relativamente inferiore rispetto ad altre opzioni come [Direct Line Speech](direct-line-speech.md). Questa semplificazione, tuttavia, comporta una riduzione della flessibilità. Pertanto, i comandi personalizzati (anteprima) è più adatto per il completamento delle attività o per gli scenari di comando e controllo. È particolarmente adatto per Internet of Things (IoT) e dispositivi headless.

Per complesse interazioni conversazionali e integrazione con altre soluzioni come la [soluzione Virtual Assistant e il modello Enterprise,](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) ti invitiamo a usare Direct Line Speech.

I buoni candidati per i comandi personalizzati (anteprima) hanno un vocabolario fisso con set di variabili ben definiti. Ad esempio, le attività di domotica, come il controllo di un termostato, sono ideali.

   ![Esempi di scenari di completamento delle attivitàExamples of task completion scenarios](media/voice-assistants/task-completion-examples.png "esempi di completamento delle attività")

## <a name="getting-started-with-custom-commands-preview"></a>Introduzione ai comandi personalizzati (anteprima)Getting started with Custom Commands (Preview)

Il primo passaggio per l'utilizzo di comandi personalizzati (anteprima) per creare un'applicazione vocale consiste [nell'ottenere un tasto](get-started.md) di sottoscrizione vocale e accedere al generatore di comandi personalizzati (anteprima) in Speech [Studio](https://speech.microsoft.com). Da lì, è possibile creare una nuova applicazione di comandi personalizzati (anteprima) e pubblicarla, dopo di che un'applicazione sul dispositivo può comunicare con essa utilizzando l'SDK di riconoscimento vocale.

   ![Flusso di creazione per i comandi personalizzati (anteprima)Authoring flow for Custom Commands (Preview)](media/voice-assistants/custom-commands-flow.png "Flusso di creazione di comandi personalizzati (anteprima)The Custom Commands (Preview) authoring flow")

Offriamo guide introduttive progettate per avere l'esecuzione di codice in meno di 10 minuti.

* [Creare un'applicazione Di comandi personalizzati (anteprima)Create a Custom Commands (Preview) application](quickstart-custom-speech-commands-create-new.md)
* [Creare un'applicazione di comandi personalizzati (anteprima) con parametriCreate a Custom Commands (Preview) application with parameters](quickstart-custom-speech-commands-create-parameters.md)
* [Connettersi a un'applicazione di comandi personalizzati (anteprima) con Speech SDK, C #](quickstart-custom-speech-commands-speech-sdk.md)

Una volta che hai finito con le guide introduttive, esplora le nostre come-to.

- [Aggiungere convalide ai parametri di comando personalizzatoAdd validations to Custom Command parameters](./how-to-custom-speech-commands-validations.md)
- [Soddisfare i comandi sul client con Speech SDK](./how-to-custom-speech-commands-fulfill-sdk.md)
- [Aggiungere una conferma a un comando personalizzato](./how-to-custom-speech-commands-confirmations.md)
- [Aggiungere una correzione in un passaggio a un comando personalizzato](./how-to-custom-speech-commands-one-step-correction.md)

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere gratuitamente una chiave di sottoscrizione per i servizi di riconoscimento vocale](get-started.md)
* [Vai a Speech Studio per provare i comandi personalizzati](https://speech.microsoft.com)
* [Ottenere Speech SDK](speech-sdk.md)
