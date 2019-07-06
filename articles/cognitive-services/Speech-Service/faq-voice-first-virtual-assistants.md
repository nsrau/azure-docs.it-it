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
ms.date: 07/05/2019
ms.author: travisw
ms.openlocfilehash: bd7e1f87fea03d0aac7fd9f746b777e3b15e917e
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606632"
---
# <a name="voice-first-virtual-assistants-preview-frequently-asked-questions"></a>Voice-first assistenti virtuali Preview: Domande frequenti

Se è possibile trovare risposte alle tue domande in questo documento, consultare [altre opzioni di supporto](support.md).

## <a name="general"></a>Generale

**D: What ' s Direct Line vocale?**

**R:** Il `DialogServiceConnector` da Speech SDK fornisce bidirezionale, la comunicazione asincrona a Bot che sono connessi al canale Direct Line vocale in Bot Framework. Questo canale fornisce accesso coordinato a per il riconoscimento vocale e sintesi vocale da servizi di riconoscimento vocale Azure Bot diventano completamente la voce in, voice out esperienze colloquiale. Con il supporto per le parole di riattivazione e la verifica di Word riattivazione, questa soluzione rende possibile compilare altamente personalizzabili assistenti virtuali voice-first per il marchio o il prodotto.

**D: come iniziare?**

**R:** Consiste nell'iniziare con la migliore modalità per iniziare la creazione di un Assistente virtuale voice-first [creazione di un bot di Bot Framework base](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0). Successivamente, connettere il tuo bot per il [canale Direct Line vocale](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

## <a name="debugging"></a>Debug

**D: Viene visualizzato un errore 401 quando ci si connette e non funziona. So che la chiave di sottoscrizione di riconoscimento vocale è valida. Cosa sta succedendo?**

**R:** In fase di anteprima, Direct Line vocale presenta alcune limitazioni specifiche sulle sottoscrizioni che è possibile utilizzare. Verificare che si usa la **vocale** risorsa (Microsoft.CognitiveServicesSpeechServices, "Speech") e *non* il **servizi cognitivi** (risorsa Microsoft.CognitiveServicesAllInOne, "Tutti i servizi cognitivi"). Solo [un subset di aree di servizi di riconoscimento vocale](regions.md#voice-first-virtual-assistants) sono attualmente supportati per i dialoghi riga diretto.

![sottoscrizione per il riconoscimento vocale linea diretta correggere](media/voice-first-virtual-assistants/faq-supported-subscription.png "esempio di una sottoscrizione di riconoscimento vocale compatibile")

**D: Ottenere nuovamente il testo di riconoscimento da Direct Line vocale, ma viene visualizzato un errore '1011' e non dal mio bot. Perché?**

**R:** Questo errore indica un problema di comunicazione tra il bot e Direct Line vocale. Assicurarsi di aver [connesso al canale Direct Line vocale](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech), [aggiunto il supporto del protocollo di Streaming](https://aka.ms/botframework/addstreamingprotocolsupport) al bot (con il supporto di WebSocket correlato) e quindi verificare che il bot sta rispondendo alle richieste in ingresso richieste dal canale.

**D: Questo codice sembra funzionare e/o viene visualizzato un errore diverso quando si usa un DialogServiceConnector. Cosa devo fare?**

**R:** La registrazione basata su file vengono forniti dettagli aggiuntivi in modo sostanziale e può aiutare ad accelerare le richieste di supporto. Per abilitare questa funzionalità, vedere [come usare la registrazione di file](how-to-use-logging.md).

## <a name="next-steps"></a>Passaggi successivi

* [Risoluzione dei problemi](troubleshooting.md)
* [Note sulla versione](releasenotes.md)
