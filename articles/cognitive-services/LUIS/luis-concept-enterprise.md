---
title: Concetti Enterprise-LUIS
titleSuffix: Azure Cognitive Services
description: Comprendere i concetti di progettazione per le app LUIS di grandi dimensioni o per più app, incluse LUIS e QnA Maker.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: efef3faf3cc4ff04235254f0ff6538d92a831196
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619933"
---
# <a name="enterprise-strategies-for-a-luis-app"></a>Strategie aziendali per un'app LUIS
Rivedere queste strategie di progettazione per l'app aziendale.

## <a name="when-you-expect-luis-requests-beyond-the-quota"></a>Quando si prevedono richieste LUIS oltre la quota

LUIS ha una quota mensile, oltre a una quota al secondo, in base al piano tariffario della risorsa di Azure. 

Se la frequenza delle richieste delle app LUIS supera la [frequenza di quota](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)consentita, è possibile:

* Distribuire il carico a più app LUIS con la [stessa definizione di app](#use-multiple-apps-with-same-app-definition). Questo include, facoltativamente, l'esecuzione di LUIS da un [contenitore](luis-container-howto.md). 
* Creare e [assegnare più chiavi](#assign-multiple-luis-keys-to-same-app) all'app. 

### <a name="use-multiple-apps-with-same-app-definition"></a>Usare più app con la stessa definizione
Esportare l'app LUIS originale, quindi reimportarla in app separate. Ogni app dispone di un proprio ID. In fase di pubblicazione, anziché usare la stessa chiave in tutte le app, creare una chiave separata per ogni app. Bilanciare il carico tra tutte le app in modo che nessuna singola app venga sovraccaricata. Aggiungere [Application Insights](luis-tutorial-bot-csharp-appinsights.md) per monitorare l'uso. 

Per ottenere la stessa finalità principale in tutte le app, assicurarsi che la stima tra la prima e la seconda finalità sia sufficiente da non confondere LUIS, fornendo risultati diversi tra le app per variazioni minime nelle espressioni. 

Quando si esegue il training di queste app di pari livello, assicurarsi di eseguire il training [con tutti i dati](luis-how-to-train.md#train-with-all-data).

Progettare una singola app come master. Qualsiasi espressione suggerita per la revisione deve essere aggiunta all'app master e spostata in tutte le altre app. Si tratta di un'esportazione completa dell'app o del caricamento delle espressioni etichettate dal master agli elementi figlio. Il caricamento può essere effettuato dal sito Web [LUIS](luis-reference-regions.md) o dall'API di creazione per una [singola espressione](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08) o per un [batch](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09). 

Pianificare una revisione periodica, ad esempio ogni due settimane, di [espressioni endpoint](luis-how-to-review-endpoint-utterances.md) per l'apprendimento attivo, quindi ripetere il training e la ripubblicazione. 

### <a name="assign-multiple-luis-keys-to-same-app"></a>Assegnare più chiavi LUIS alla stessa app
Se l'app LUIS riceve più accessi endpoint rispetto a quanto consentito dalla quota della chiave singola, creare e assegnare più chiavi all'app LUIS. Creare un'utilità di gestione del traffico o di bilanciamento del carico per gestire le query endpoint tra le chiavi endpoint. 

## <a name="when-your-monolithic-app-returns-wrong-intent"></a>Quando l'app monolitica restituisce la finalità errata
Se l'app deve prevedere un'ampia varietà di espressioni utente, è consigliabile implementare il [modello recapito](#dispatch-tool-and-model). La suddivisione di un'app monolitica consente a LUIS di eseguire correttamente il rilevamento delle finalità anziché confondersi tra app padre e app figlio. 

Pianificare una [revisione periodica delle espressioni endpoint](luis-how-to-review-endpoint-utterances.md) per l'apprendimento attivo, ad esempio ogni due settimane, rieseguire il training e ripubblicare. 

## <a name="when-you-need-to-have-more-than-500-intents"></a>Quando è necessario disporre di più di 500 finalità
Supponiamo che tu stia sviluppando un assistente di Office con oltre 500 di intenti. Se 200 finalità sono correlate alla pianificazione di riunioni, 200 riguardano i promemoria, 200 il recupero di informazioni sui colleghi e 200 l'invio di messaggi di posta elettronica, raggruppare le finalità in modo che ciascun gruppo si trovi in una singola app, quindi creare un'app di livello superiore contenente ciascuna finalità. Usare il [modello di invio](#dispatch-tool-and-model) per compilare l'app di livello superiore. Modificare quindi il bot per usare la chiamata a cascata, come illustrato nell' [esercitazione del modello dispatch](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs). 

## <a name="when-you-need-to-combine-several-luis-and-qna-maker-apps"></a>Quando è necessario combinare più app LUIS e QnA Maker
Se sono disponibili diverse app LUIS e QnA Maker che devono rispondere a un bot, usare il [modello dispatch](#dispatch-tool-and-model) per compilare l'app di livello superiore.  Modificare quindi il bot per usare la chiamata a cascata, come illustrato nell' [esercitazione del modello dispatch](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs). 

## <a name="dispatch-tool-and-model"></a>Modello e strumento recapito
Usare lo strumento da riga di comando [Dispatch][dispatch-tool] , disponibile in [BotBuilder-Tools](https://github.com/Microsoft/botbuilder-tools) per combinare più app luis e/o QnA Maker in un'app Luis padre. Questo approccio consente di disporre di un dominio padre, inclusi tutti i soggetti e i domini di soggetti figlio diversi in app separate. 

![Immagine concettuale dell'architettura di recapito](./media/luis-concept-enterprise/dispatch-architecture.png)

Il dominio padre è indicato in LUIS con una versione denominata `Dispatch` nell'elenco delle app. 

Il bot della chat riceve l'espressione, quindi invia all'app LUIS padre per la stima. Il primo intento stimato dall'app padre determina quale app LUIS child viene chiamata Next. La chat bot invia l'espressione all'app figlio per una stima più specifica.

Comprendere il modo in cui viene eseguita questa gerarchia di chiamate dal Bot Builder v4 [Dispatcher-Application-Tutorial](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs).  

### <a name="intent-limits-in-dispatch-model"></a>Limiti di finalità nel modello recapito
Al massimo un'applicazione di recapito dispone di 500 origini di recapito, equivalenti a 500 finalità. 

## <a name="more-information"></a>Altre informazioni

* [SDK del Framework bot](https://github.com/Microsoft/botframework)
* [Esercitazione sul modello di invio](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&branch=master&tabs=cs)
* [INTERFACCIA della riga di comando dispatch](https://github.com/Microsoft/botbuilder-tools)
* Esempio di bot modello di invio- [.NET](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch), [node. js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [testare un batch](luis-how-to-batch-test.md)

[dispatcher-application-tutorial]: https://aka.ms/bot-dispatch
[dispatch-tool]: https://aka.ms/dispatch-tool
