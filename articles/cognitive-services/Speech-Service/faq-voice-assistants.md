---
title: Domande frequenti sugli assistenti vocali
titleSuffix: Azure Cognitive Services
description: Ottieni risposte alle domande più diffuse sugli assistenti vocali usando i comandi personalizzati (anteprima) o il canale di sintesi vocale diretta.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: 7ad3f932e9a10723d6cc1bae2fc4854c932d4c64
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507077"
---
# <a name="voice-assistants-frequently-asked-questions"></a>Assistenti vocali: domande frequenti

Se non si riesce a trovare le risposte alle domande in questo documento, vedere [altre opzioni di supporto](support.md).

## <a name="general"></a>Generale

**D: che cos'è un assistente vocale?**

**R:** Analogamente a Cortana, un assistente vocale è una soluzione che ascolta le enunciazioni vocali di un utente, analizza il contenuto di tali espressioni per significare, esegue una o più azioni in risposta alla finalità dell'espressione, quindi fornisce una risposta all'utente che spesso include un componente parlato. Si tratta di un'esperienza di "Voice-in-out" per l'interazione con un sistema. gli autori di Assistente vocale creano un'applicazione su dispositivo usando il `DialogServiceConnector` nell'SDK di riconoscimento vocale per comunicare con un assistente creato con [comandi personalizzati (anteprima)](custom-commands.md) o con il canale di [sintesi vocale diretta](direct-line-speech.md) di bot Framework. Questi assistenti possono usare parole chiave personalizzate, riconoscimento vocale personalizzato e una voce personalizzata per offrire un'esperienza personalizzata al proprio marchio o prodotto.

**D: è consigliabile usare I comandi personalizzati (anteprima) o la voce Direct Line? Qual è la differenza?**

**R:** i [comandi personalizzati (anteprima)](custom-commands.md) sono un set di strumenti di complessità inferiore che consente di creare e ospitare facilmente un assistente particolarmente adatto agli scenari di completamento delle attività. [Direct Line Speech](direct-line-speech.md) fornisce funzionalità più avanzate e sofisticate che possono consentire scenari di conversazione affidabili. Per ulteriori informazioni, vedere [confronto tra le soluzioni Assistant](voice-assistants.md#comparing-assistant-solutions) .

**D: Come iniziare?**

**R:** Il modo migliore per iniziare con la creazione di un'applicazione comandi personalizzata (anteprima) o bot di base bot Framework.

* [Creare un'applicazione di comandi personalizzati (anteprima)](quickstart-custom-speech-commands-create-new.md)
* [Creare un bot di base per bot Framework](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [Connettere un bot al canale vocale direct line](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)

## <a name="debugging"></a>Debug

**D: si verifica un errore 401 durante la connessione e non funziona nulla. So che la mia chiave di sottoscrizione vocale è valida. Cosa sta succedendo?**

**R:** Quando si gestisce la sottoscrizione nel portale di Azure, assicurarsi di usare la risorsa **vocale** (Microsoft. CognitiveServicesSpeechServices, "speech") e *non* la risorsa **Servizi cognitivi** ( Microsoft. CognitiveServicesAllInOne, "tutti i servizi cognitivi". Controllare anche il [supporto per l'area del servizio vocale per gli assistenti vocali](regions.md#voice-assistants).

![correzione della sottoscrizione per la voce Direct Line](media/voice-assistants/faq-supported-subscription.png "esempio di sottoscrizione vocale compatibile")

**D: il testo di riconoscimento viene restituito dalla mia `DialogServiceConnector`, ma viene visualizzato un errore "1011" e niente dal bot. Perché?**

**R:** Questo errore indica un problema di comunicazione tra l'assistente e il servizio Voice Assistant.

* Per i comandi personalizzati (anteprima), assicurarsi che l'applicazione comandi personalizzati (anteprima) sia pubblicata
* Per la comunicazione diretta della linea, assicurarsi di aver [connesso il bot al canale di riconoscimento vocale diretto](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech), [aggiunto il supporto](https://aka.ms/botframework/addstreamingprotocolsupport) per il protocollo di streaming al bot (con il supporto per socket Web correlato) e quindi verificare che il bot stia rispondendo alle richieste in ingresso. dal canale.

**D: questo codice non funziona ancora e/o si verifica un errore diverso quando si usa un `DialogServiceConnector`. Cosa dovrei fare?**

**R:** La registrazione basata su file fornisce un maggior numero di dettagli e consente di accelerare le richieste di supporto. Per abilitare questa funzionalità, vedere [come usare la registrazione di file](how-to-use-logging.md).

## <a name="next-steps"></a>Passaggi successivi

* [Risoluzione dei problemi](troubleshooting.md)
* [Note sulla versione](releasenotes.md)
