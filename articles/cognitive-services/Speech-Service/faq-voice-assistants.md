---
title: Domande frequenti sulle assistenti vocali
titleSuffix: Azure Cognitive Services
description: Risposte alle domande più frequenti sugli assistenti vocali che utilizzano i comandi personalizzati (anteprima) o il canale Direct Line Speech.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: 8480299c2c889a243150028ac9651f4b62656aec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74110352"
---
# <a name="voice-assistants-frequently-asked-questions"></a>Domande frequenti sulle assistenti vocali

Se non riesci a trovare le risposte alle tue domande in questo documento, consulta altre opzioni di [supporto.](support.md)

## <a name="general"></a>Generale

**D: Cos'è un assistente vocale?**

**A:** Come Cortana, un assistente vocale è una soluzione che ascolta le espressioni vocali di un utente, analizza il contenuto di tali espressioni per il significato, esegue una o più azioni in risposta alle intenzioni dell'espressione e quindi fornisce una risposta all'utente che spesso include un componente parlato. È un'esperienza "voice-in, voice-out" per interagire con un sistema. Gli autori degli assistenti vocali `DialogServiceConnector` creano un'applicazione sul dispositivo utilizzando l'SDK di riconoscimento vocale per comunicare con un assistente creato utilizzando i [comandi personalizzati (anteprima)](custom-commands.md) o il canale [di riconoscimento vocale](direct-line-speech.md) diretto di Bot Framework. Questi assistenti possono utilizzare parole chiave personalizzate, riconoscimento vocale personalizzato e voce personalizzata per offrire un'esperienza personalizzata per il tuo marchio o prodotto.

**D: È necessario usare i comandi personalizzati (anteprima) o Direct Line Speech? Qual è la differenza?**

**R:** [Comandi personalizzati (anteprima)](custom-commands.md) è un set di strumenti con complessità inferiore per creare e ospitare facilmente un assistente adatto agli scenari di completamento delle attività. [Direct Line Speech](direct-line-speech.md) offre funzionalità più ricche e sofisticate che possono consentire scenari di conversazione affidabili. Per ulteriori informazioni, vedere il [confronto delle soluzioni per gli assistenti.](voice-assistants.md#choosing-an-assistant-solution)

**D: Come iniziare?**

**A:** Il modo migliore per iniziare con la creazione di un'applicazione di comandi personalizzati (anteprima) o bot Framework di base.

- [Creare un'applicazione Di comandi personalizzati (anteprima)Create a Custom Commands (Preview) application](quickstart-custom-speech-commands-create-new.md)
- [Creare un bot di Bot Framework di baseCreate a basic Bot Framework bot](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
- [Connettere un bot al canale Direct Line Speech](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)

## <a name="debugging"></a>Debug

**D: Dov'è il mio canale segreto?**

**A:** Se hai usato la versione di anteprima di Direct Line Speech o stai leggendo la documentazione correlata, potresti aspettarti di trovare una chiave segreta nella pagina di registrazione del canale Direct Line Speech. Anche il metodo `DialogServiceConfig` `FromBotSecret` factory v1.7 in Speech SDK prevede questo valore.

La versione più recente di Direct Line Speech semplifica il processo di contatto del bot da un dispositivo. Nella pagina di registrazione del canale, il menu a discesa nella parte superiore associa la registrazione del canale Direct Line Speech a una risorsa vocale. Una volta associato, l'SDK di `BotFrameworkConfig::FromSubscription` riconoscimento vocale `DialogServiceConnector` v1.8 include un metodo factory che configurerà un oggetto per contattare il bot associato alla sottoscrizione.

Se si sta ancora eseguendo la migrazione dell'applicazione client `DialogServiceConfig::FromBotSecret` dalla versione 1.7 alla v1.8, è possibile continuare a utilizzare un valore non null non vuoto per il relativo parametro segreto del canale, ad esempio il segreto precedente utilizzato. Verrà semplicemente ignorato quando si utilizza una sottoscrizione vocale associata a una registrazione del canale più recente. Si prega di notare che il valore _deve_ essere non null e non vuoto, come questi vengono controllati sul dispositivo prima che l'associazione lato servizio sia rilevante.

Per una guida più dettagliata, vedere la [sezione tutorial](tutorial-voice-enable-your-bot-speech-sdk.md#register-the-direct-line-speech-channel) che illustra la registrazione del canale.

**D: Viene visualizzato un errore 401 durante la connessione e non funziona nulla. So che la mia chiave di sottoscrizione è valida. Cosa sta succedendo?**

**A:** Quando si gestisce la sottoscrizione nel portale di Azure, assicurarsi di usare la risorsa **Riconoscimento vocale** (Microsoft.CognitiveServicesSpeechServices, "Speech") e _non_ la risorsa **Servizi cognitivi** (Microsoft.CognitiveServicesAllInOne, "Tutti i servizi cognitivi"). Inoltre, si prega di controllare [il supporto dell'area del servizio di riconoscimento vocale per gli assistenti vocali](regions.md#voice-assistants).

![abbonamento corretto per il discorso linea diretta](media/voice-assistants/faq-supported-subscription.png "esempio di abbonamento Speech compatibile")

**D: Ottengo il testo `DialogServiceConnector`di riconoscimento dal mio , ma viene visualizzato un errore '1011' e nulla dal bot. Perché?**

**A:** Questo errore indica un problema di comunicazione tra l'assistente e il servizio assistente vocale.

- Per i comandi personalizzati (anteprima), assicurarsi che l'applicazione Comandi personalizzati (anteprima) sia pubblicata
- Per Direct Line Speech, verificare di aver [connesso il bot al canale Direct Line Speech](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech), di aver aggiunto il supporto del protocollo [Streaming](https://aka.ms/botframework/addstreamingprotocolsupport) al bot (con il supporto Web Socket correlato) e quindi verificare che il bot risponda alle richieste in arrivo dal canale.

**D: Questo codice non funziona ancora e/o ricevo un errore `DialogServiceConnector`diverso quando si utilizza un file . Cosa dovrei fare?**

**A:** La registrazione basata su file fornisce un livello di dettaglio sostanzialmente maggiore e consente di accelerare le richieste di supporto. Per attivare questa funzionalità, vedere [come utilizzare la registrazione dei file](how-to-use-logging.md).

## <a name="next-steps"></a>Passaggi successivi

- [Risoluzione dei problemi](troubleshooting.md)
- [Note sulla versione](releasenotes.md)
