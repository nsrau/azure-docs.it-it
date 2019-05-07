---
title: Domande frequenti su Direct Line vocale
titleSuffix: Azure Cognitive Services
description: Ottieni risposte alle domande più diffuse su voice-first assistenti virtuali usando il canale Direct Line vocale.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: travisw
ms.openlocfilehash: 8427417c9b579c7dfa21f834ce1ca77099159eb2
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65072614"
---
# <a name="voice-first-virtual-assistants-preview-frequently-asked-questions"></a>Voice-first assistenti virtuali Preview: Domande frequenti

Se è possibile trovare risposte alle tue domande in questo documento, consultare [altre opzioni di supporto](support.md).

## <a name="general"></a>Generale

**D: What ' s Direct Line vocale?**

**R:** Il `SpeechBotConnector` da Speech SDK fornisce bidirezionale, la comunicazione asincrona a Bot che sono connessi al canale Direct Line vocale in Bot Framework. Questo canale fornisce accesso coordinato a per il riconoscimento vocale e sintesi vocale da servizi di riconoscimento vocale Azure Bot diventano completamente la voce in, voice out esperienze colloquiale. Con il supporto per le parole di riattivazione e la verifica di Word riattivazione, questa soluzione rende possibile compilare altamente personalizzabili assistenti virtuali voice-first per il marchio o il prodotto.

**D: come iniziare?**

**R:** Consiste nell'iniziare con la migliore modalità per iniziare la creazione di un Assistente virtuale voice-first [creazione di un bot di Bot Framework base](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0). Successivamente, connettere il tuo bot per il [canale Direct Line vocale](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

## <a name="debugging"></a>Debug

**D: Viene visualizzato un errore 401 quando ci si connette e non funziona. So che la chiave di sottoscrizione di riconoscimento vocale è valida. Cosa sta succedendo?**

**R:** In fase di anteprima, Direct Line vocale presenta limitazioni molto specifiche per la sottoscrizione usata. Assicurarsi che si usa la **vocale** risorsa (Microsoft.CognitiveServicesSpeechServices, "Speech") e *non* il **servizi cognitivi** (risorsa Microsoft.CognitiveServicesAllInOne, "Tutti i servizi cognitivi"). Inoltre, tenere presente che solo le **westus2** area è attualmente supportata.

![sottoscrizione per il riconoscimento vocale linea diretta correggere](media/voice-first-virtual-assistants/faq-supported-subscription.png "esempio di una sottoscrizione di riconoscimento vocale compatibile")

**D: Ottenere nuovamente il testo di riconoscimento da Direct Line vocale, ma viene visualizzato un errore '1011' e non dal mio bot. Perché?**

**R:** Questo errore indica un problema di comunicazione tra il bot e Direct Line vocale. Assicurarsi di aver [connesso al canale Direct Line vocale](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech), [aggiunto il supporto del protocollo di Streaming](https://aka.ms/botframework/addstreamingprotocolsupport) al bot (con il supporto di WebSocket correlato) e quindi verificare che il bot sta rispondendo alle richieste in ingresso richieste dal canale.

**D: Questo comunque non funziona e/o viene visualizzato un errore diverso quando si usa un SpeechBotConnector e non è chiaro cosa fare. Che cosa *dovrebbero* fare?**

**R:** La registrazione basata su file vengono forniti dettagli aggiuntivi in modo sostanziale e può aiutare ad accelerare le richieste di supporto. A tale scopo, vedere [come usare la registrazione di file](how-to-use-logging.md).

## <a name="next-steps"></a>Passaggi successivi

* [Risoluzione dei problemi](troubleshooting.md)
* [Note sulla versione](releasenotes.md)
