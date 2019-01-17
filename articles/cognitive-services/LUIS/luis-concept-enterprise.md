---
title: Concetti aziendali
titleSuffix: Language Understanding - Azure Cognitive Services
description: Comprendere i concetti di progettazione per le app LUIS di grandi dimensioni o per più app, incluse LUIS e QnA Maker.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: diberry
ms.openlocfilehash: 7c12118d0be939f63a2a11fa4dcdd45b87bcd3aa
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2019
ms.locfileid: "54263170"
---
# <a name="enterprise-strategies-for-a-luis-app"></a>Strategie aziendali per un'app LUIS
Rivedere queste strategie di progettazione per l'app aziendale.

## <a name="when-you-expect-luis-requests-beyond-the-quota"></a>Quando si prevedono richieste LUIS oltre la quota
Se il tasso di richieste dell'app LUIS supera la [quota](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) consentita, suddividere il carico su più app LUIS con la [stessa definizione](#use-multiple-apps-with-same-app-definition) o creare e [assegnare più chiavi](#assign-multiple-luis-keys-to-same-app) all'app. 

### <a name="use-multiple-apps-with-same-app-definition"></a>Usare più app con la stessa definizione
Esportare l'app LUIS originale, quindi reimportarla in app separate. Ogni app dispone di un proprio ID. In fase di pubblicazione, anziché usare la stessa chiave in tutte le app, creare una chiave separata per ogni app. Bilanciare il carico tra tutte le app in modo che nessuna singola app venga sovraccaricata. Aggiungere [Application Insights](luis-tutorial-bot-csharp-appinsights.md) per monitorare l'uso. 

Per ottenere la stessa finalità principale in tutte le app, assicurarsi che la stima tra la prima e la seconda finalità sia sufficiente da non confondere LUIS, fornendo risultati diversi tra le app per variazioni minime nelle espressioni. 

Progettare una singola app come master. Qualsiasi espressione suggerita per la revisione deve essere aggiunta all'app master e spostata in tutte le altre app. Si tratta di un'esportazione completa dell'app o del caricamento delle espressioni etichettate dal master agli elementi figlio. Il caricamento può essere effettuato dal sito Web [LUIS](luis-reference-regions.md) o dall'API di creazione per una [singola espressione](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08) o per un [batch](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09). 

Pianificare una [revisione periodica delle espressioni endpoint](luis-how-to-review-endoint-utt.md) per l'apprendimento attivo, ad esempio ogni due settimane, rieseguire il training e ripubblicare. 

### <a name="assign-multiple-luis-keys-to-same-app"></a>Assegnare più chiavi LUIS alla stessa app
Se l'app LUIS riceve più accessi endpoint rispetto a quanto consentito dalla quota della chiave singola, creare e assegnare più chiavi all'app LUIS. Creare un'utilità di gestione del traffico o di bilanciamento del carico per gestire le query endpoint tra le chiavi endpoint. 

## <a name="when-your-monolithic-app-returns-wrong-intent"></a>Quando l'app monolitica restituisce la finalità errata
Se l'app deve prevedere un'ampia varietà di espressioni utente, è consigliabile implementare il [modello recapito](#dispatch-tool-and-model). La suddivisione di un'app monolitica consente a LUIS di eseguire correttamente il rilevamento delle finalità anziché confondersi tra app padre e app figlio. 

Pianificare una [revisione periodica delle espressioni endpoint](luis-how-to-review-endoint-utt.md) per l'apprendimento attivo, ad esempio ogni due settimane, rieseguire il training e ripubblicare. 

## <a name="when-you-need-to-have-more-than-500-intents"></a>Quando è necessario disporre di più di 500 finalità
Si supponga, ad esempio, di sviluppare un assistente per ufficio con oltre 500 finalità. Se 200 finalità sono correlate alla pianificazione di riunioni, 200 riguardano i promemoria, 200 il recupero di informazioni sui colleghi e 200 l'invio di messaggi di posta elettronica, raggruppare le finalità in modo che ciascun gruppo si trovi in una singola app, quindi creare un'app di livello superiore contenente ciascuna finalità. Usare l'[architettura e lo strumento recapito](#dispatch-tool-and-model) per compilare l'app di livello superiore. Modificare il bot affinché usi la chiamata a cascata come mostrato nell'[esercitazione sul recapito][dispatcher-application-tutorial]. 

## <a name="when-you-need-to-combine-several-luis-and-qna-maker-apps"></a>Quando è necessario combinare più app LUIS e QnA Maker
Se si dispone di più app LUIS e QnA Maker che devono rispondere a un bot, usare lo [strumento recapito](#dispatch-tool-and-model) per compilare l'app di livello superiore. Modificare il bot affinché usi la chiamata a cascata come mostrato nell'[esercitazione sul recapito][dispatcher-application-tutorial]. 

## <a name="dispatch-tool-and-model"></a>Modello e strumento recapito
Usare lo strumento da riga di comando [Dispatch][dispatch-tool], disponibile in [BotBuilder-tools](https://github.com/Microsoft/botbuilder-tools), per combinare più app LUIS e/o QnA Maker in un'app LUIS padre. Questo approccio consente di disporre di un dominio padre, inclusi tutti i soggetti e i domini di soggetti figlio diversi in app separate. 

![Immagine concettuale dell'architettura di recapito](./media/luis-concept-enterprise/dispatch-architecture.png)

Il dominio padre è indicato in LUIS con una versione denominata `Dispatch` nell'elenco delle app. 

Il chatbot riceve l'espressione, quindi la invia all'app LUIS padre per la stima. La finalità stimata principale dall'app padre determina quale app LUIS figlio viene chiamata dopo. Il chatbot invia l'espressione all'app figlio per una stima più specifica.

Informazioni su questa gerarchia di chiamate dall'[esercitazione dell'applicazione di recapito][dispatcher-application-tutorial] Bot Builder v4.  

### <a name="intent-limits-in-dispatch-model"></a>Limiti di finalità nel modello recapito
Al massimo un'applicazione di recapito dispone di 500 origini di recapito, equivalenti a 500 finalità. 

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [testare un batch](luis-how-to-batch-test.md)

[dispatcher-application-tutorial]: https://aka.ms/bot-dispatch
[dispatch-tool]: https://aka.ms/dispatch-tool