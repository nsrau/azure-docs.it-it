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
ms.openlocfilehash: eccf2a7a1b9c7ea7a21cd5d0cf0f60728284c05d
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73579661"
---
# <a name="voice-assistants-frequently-asked-questions"></a>Assistenti vocali: domande frequenti

Se non si riesce a trovare le risposte alle domande in questo documento, vedere [altre opzioni di supporto](support.md).

## <a name="general"></a>Generale

**D: che cos'è un assistente vocale?**

**R:** Analogamente a Cortana, un assistente vocale è una soluzione che ascolta le enunciazioni vocali di un utente, analizza il contenuto di tali espressioni per significare, esegue una o più azioni in risposta alla finalità dell'espressione, quindi fornisce una risposta all'utente che spesso include un componente parlato. Si tratta di un'esperienza di "Voice-in-out" per l'interazione con un sistema. gli autori di Assistente vocale creano un'applicazione su dispositivo usando il `DialogServiceConnector` nell'SDK di riconoscimento vocale per comunicare con un assistente creato con [comandi personalizzati (anteprima)](custom-commands.md) o con il canale di [sintesi vocale diretta](direct-line-speech.md) di bot Framework. Questi assistenti possono usare parole chiave personalizzate, riconoscimento vocale personalizzato e una voce personalizzata per offrire un'esperienza personalizzata al proprio marchio o prodotto.

**D: è consigliabile usare I comandi personalizzati (anteprima) o la voce Direct Line? Qual è la differenza?**

**R:** i [comandi personalizzati (anteprima)](custom-commands.md) sono un set di strumenti di complessità inferiore che consente di creare e ospitare facilmente un assistente particolarmente adatto agli scenari di completamento delle attività. [Direct Line Speech](direct-line-speech.md) fornisce funzionalità più avanzate e sofisticate che possono consentire scenari di conversazione affidabili. Per ulteriori informazioni, vedere [confronto tra le soluzioni Assistant](voice-assistants.md#choosing-an-assistant-solution) .

**D: Come iniziare?**

**R:** Il modo migliore per iniziare con la creazione di un'applicazione comandi personalizzata (anteprima) o bot di base bot Framework.

* [Creare un'applicazione di comandi personalizzati (anteprima)](quickstart-custom-speech-commands-create-new.md)
* [Creare un bot di base per bot Framework](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [Connettere un bot al canale vocale direct line](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)

## <a name="debugging"></a>Debug

**D: dove si trova il segreto del canale?**

**R:** Se è stata usata la versione di anteprima di direct line Speech o si sta leggendo la documentazione correlata, è possibile che si desideri trovare una chiave privata nella pagina di registrazione del canale vocale direct line. Il metodo factory della `DialogServiceConfig` v 1.7 `FromBotSecret` nell'SDK di riconoscimento vocale prevede anche questo valore.

La versione più recente di direct line Speech semplifica il processo di contatto del bot da un dispositivo. Nella pagina di registrazione del canale, l'elenco a discesa nella parte superiore associa la registrazione del canale vocale direct line a una risorsa di riconoscimento vocale. Una volta associato, l'SDK di riconoscimento vocale v 1.8 include un metodo factory `BotFrameworkConfig::FromSubscription` che configurerà un `DialogServiceConnector` per contattare il bot associato alla sottoscrizione.

Se si sta ancora migrando l'applicazione client da v 1.7 a v 1.8, `DialogServiceConfig::FromBotSecret` possibile continuare a usare un valore non vuoto e non null per il relativo parametro del segreto del canale, ad esempio il segreto precedente usato. Verrà semplicemente ignorato quando si usa una sottoscrizione vocale associata a una registrazione del canale più recente. Si noti che il valore *deve* essere non null e non vuoto, perché questi vengono controllati nel dispositivo prima che l'associazione sul lato del servizio sia pertinente.


Per una guida più dettagliata, vedere la [sezione relativa all'esercitazione](tutorial-voice-enable-your-bot-speech-sdk.md#register-the-direct-line-speech-channel) che illustra la registrazione del canale.

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
