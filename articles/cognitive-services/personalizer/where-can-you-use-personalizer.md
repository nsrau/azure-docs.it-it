---
title: Valutazione scenari - Personalizza esperienze
titleSuffix: Azure Cognitive Services
description: Personalizza esperienze può essere applicato in qualsiasi situazione in cui l'applicazione può selezionare l'elemento, l'azione o il prodotto giusto da visualizzare, per migliorare l'esperienza, ottenere risultati aziendali migliori o migliorare la produttività.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 465fba3a466aceaf9ef7b71e4b1957bfdbcad766
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663648"
---
# <a name="where-can-you-use-personalizer"></a>In quali situazioni è possibile usare Personalizza esperienze?

È possibile usare Personalizza esperienze in qualsiasi situazione in cui l'applicazione deve selezionare l'elemento, l'azione o il prodotto giusto da visualizzare, per migliorare l'esperienza, ottenere risultati aziendali migliori o migliorare la produttività. 

Personalizza esperienze usa l'apprendimento automatico per selezionare l'azione da mostrare all'utente. La selezione può variare notevolmente a seconda della quantità, della qualità e della distribuzione dei dati inviati al servizio.

### <a name="checklist-for-applying-personalizer"></a>Elenco di controllo per applicare Personalizza esperienze


È possibile applicare Personalizza esperienze nelle situazioni in cui:

* Si ha un obiettivo aziendale o di usabilità per l'applicazione.
* Decidere contestualmente cosa mostrare agli utenti in un determinato punto dell'applicazione migliora tale obiettivo.
* La scelta migliore può e deve essere appresa in base al comportamento collettivo degli utenti e al punteggio totale.
* L'uso dell'apprendimento automatico per la personalizzazione segue le [linee guida per l'uso responsabile](ethics-responsible-use.md) e le scelte personali.
* La decisione contestuale può essere espressa classificando la migliore opzione (azione) in un set di scelte limitato.
* Sarà quindi possibile determinare la validità della scelta classificata valutando alcuni aspetti del comportamento degli utenti ed esprimendoli con un _punteggio_, espresso con un numero compreso tra -1 e 1.
* Il punteggio non introduce troppi fattori esterni o di confusione. La durata dell'esperimento è abbastanza ridotta da poter calcolare il punteggio finché è ancora pertinente.
* È possibile esprimere il contesto per le classificazione sotto forma di elenco di almeno 5 [funzioni](concepts-features.md) che si ritiene possano orientare verso la scelta giusta, escludendo le informazioni personali .
* Sono disponibili informazioni su ciascuna scelta di contenuto, _azione_, come elenco di almeno 5 [funzioni](concepts-features.md) che si ritiene aiutino Personalizza esperienze a operare la scelta giusta.
* L'applicazione può conservare dati per il tempo sufficiente ad accumulare una cronologia di almeno 100.000 interazioni.

## <a name="machine-learning-considerations-for-applying-personalizer"></a>Considerazioni sull'apprendimento automatico per l'applicazione di Personalizza esperienze

Personalizza esperienze si basa sull'apprendimento per rinforzo, un approccio all'apprendimento automatico che si insegna in base al feedback fornito. 

Personalizza esperienze apprenderà meglio nelle situazioni in cui:

* Ci sono eventi sufficienti a tenere sotto controllo la personalizzazione ottimale se il problema devia nel corso del tempo (ad esempio, le preferenze nel campo delle notizie o della moda). Personalizza esperienze si adatta ai cambiamenti continui del mondo reale, ma i risultati non saranno ottimali se non ci sono eventi e dati sufficienti da cui apprendere per individuare e stabilire nuovi criteri. È consigliabile scegliere un caso d'uso che accada abbastanza frequentemente. Prendere in considerazione la ricerca di casi d'uso che si verifichino almeno 500 volte al giorno.
* Il contesto e le azioni offrano [funzionalità](concepts-features.md) sufficienti a facilitare l'apprendimento.
* Per ogni chiamata ci sono meno di 50 azioni da classificare.
* Le impostazioni di conservazione dati consentono a Personalizza esperienze di raccogliere dati sufficienti a eseguire valutazioni offline e l'ottimizzazione dei criteri. Si tratta in genere di almeno 50.000 punti dati.

## <a name="monitor-effectiveness-of-personalizer"></a>Monitorare l'efficacia di Personalizza esperienze

È possibile monitorare periodicamente l'efficacia di Personalizza esperienze eseguendo [valutazioni offline](concepts-offline-evaluation.md).

## <a name="use-personalizer-with-recommendation-engines"></a>Usare Personalizza esperienze con i motori di raccomandazione

Molte aziende usano motori di raccomandazione, strumenti di marketing e conduzione di campagne, segmentazione e clustering del pubblico, filtri collaborativi e altri mezzi per consigliare prodotti da un ampio catalogo ai clienti.

Il [repository GitHub Microsoft Recommenders](https://github.com/Microsoft/Recommenders) fornisce esempi e procedure consigliate per compilare sistemi di raccomandazioni sotto forma di notebook Jupyter. Fornisce esempi funzionanti per preparare dati, compilare modelli, valutare, ottimizzare e rendere operativi i motori di raccomandazione, per molti approcci comuni, tra cui xDeepFM, SAR, ALS, RBM e DKN.

Personalizza esperienze può funzionare con un motore di raccomandazione, se presente.

* I motori di raccomandazione richiedono grandi quantità di elementi (ad esempio, 500.000) e raccomandano un subset (ad esempio i primi 20) da centinaia o migliaia di opzioni.
* Personalizza esperienze accetta un numero ridotto di azioni con una grande quantità di informazioni su di esse e le classifica in tempo reale per un determinato contenuto avanzato, mentre la maggior parte dei motori di raccomandazione usano solo alcuni attributi riguardanti gli utenti, i prodotti e le relative interazioni.
* Personalizza esperienze è progettato per esplorare continuamente e in modo autonomo le preferenze dell'utente, restituendo risultati migliori nei casi in cui il contenuto cambia rapidamente, ad esempio notizie, eventi in tempo reale, contenuti live della community, contenuto con aggiornamenti quotidiani o contenuti stagionali.

Un uso comune consiste nell'usare l'output di un motore di raccomandazione (ad esempio, i primi 20 prodotti per un determinato cliente) come azioni di input per Personalizza esperienze.

## <a name="next-steps"></a>Passaggi successivi

[Etica e uso responsabile](ethics-responsible-use.md).