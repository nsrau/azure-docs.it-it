---
title: Dashboard - Language Understanding Intelligent Service
titleSuffix: Azure Cognitive Services
description: Correggere gli Intent con il dashboard di analitica, uno strumento di creazione di report visualizzato.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/22/2019
ms.author: diberry
ms.openlocfilehash: 055d113a2bc77f8de1b4b881718007c869470532
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236953"
---
# <a name="how-to-use-the-dashboard-to-improve-your-app"></a>Come usare il Dashboard per migliorare l'app

Trovare e risolvere i problemi con finalità dell'app sottoposto a training, quando si usa espressioni di esempio. Il dashboard Visualizza informazioni sull'app complessiva, con le evidenziazioni di Intent che dovrebbe essere risolto. 

Esaminare l'analisi è un processo iterativo e ripetuto durante la modifica e migliorare il modello di Dashboard.

Questa pagina non avrà analysis rilevanti per le app che non dispongono di eventuali espressioni di esempio in Intent, noto come _pattern sola_ app. 

## <a name="what-issues-can-be-fixed-from-dashboard"></a>Problemi che possono essere corretti dal dashboard?

I tre problemi risolti nel dashboard sono:

|Problema|Colore del grafico|Spiegazione|
|--|--|--|
|Sbilanciamento delle classi di dati|-|Ciò si verifica quando la quantità di espressioni di esempio varia in modo significativo. Tutti gli Intent dovranno disporre _approssimativamente_ lo stesso numero di espressioni di esempio - eccetto l'intento None. Nell'app, è necessario solo il 10%, 15% della quantità totale di espressioni.<br><br> Se i dati sono sbilanciati, ma la precisione della finalità è sopra una determinata soglia, tale squilibrio non viene segnalato come un problema.<br><br>**Iniziare con questo problema, potrebbe essere la causa radice dei problemi di altri.**|
|Stime chiara|Arancione|Ciò si verifica quando lo scopo principale e i punteggi dell'intento successivo sono sufficientemente vicine che essi possono riflettere sui corsi di formazione successivo a causa dell'errore [campionamento negativo](luis-how-to-train.md#train-with-all-data) o altre espressioni di esempio aggiunti alla finalità. |
|Stime non corrette|Rosso|Ciò si verifica quando un utterance di esempio non stimato per la finalità con etichetta (l'intento è in).|

Le stime corrette sono rappresentate con il colore blu.

Il dashboard Mostra questi problemi e indica quale Intent sono interessati e suggerisce cosa deve fare per migliorare l'app. 

## <a name="before-app-is-trained"></a>Prima viene eseguito il training di app 

Prima di eseguire il training dell'app, il dashboard non contiene suggerimenti per correzioni. Il training dell'app per visualizzare questi suggerimenti.  

## <a name="check-your-publishing-status"></a>Controllare lo stato della pubblicazione

Il **lo stato di pubblicazione** scheda contiene informazioni su Attiva versione di ultima pubblicazione. 

Verificare che la versione attiva è la versione che si desidera correggere. 

![Servizi esterni dell'app Mostra dashboard, pubblicati aree e aggregati riscontri endpoint.](./media/luis-how-to-use-dashboard/analytics-card-1-shows-app-summary-and-endpoint-hits.png)

Questo inoltre Mostra tutti i servizi esterni, aree pubblicate e aggregati riscontri endpoint. 

## <a name="review-training-evaluation"></a>Esaminare la valutazione di training

Il **valutazione Training** scheda contiene il riepilogo aggregato di accuratezza complessiva dell'app per area. Il punteggio indica la qualità intent. 

![La scheda di valutazione di Training contiene la prima area delle informazioni sull'accuratezza complessiva dell'app.](./media/luis-how-to-use-dashboard/analytics-card-2-shows-app-overall-accuracy.png)

Il grafico indica l'Intent stimati correttamente e le aree problematiche con colori diversi. Come si migliora l'app con i suggerimenti, in questo modo aumentano punteggio. 

Le correzioni suggerite sono separate dal tipo di problema e sono i più importanti per l'app. Se si desidera esaminare e risolvere i problemi per finalità, usare il **[Intent con errori](#intents-with-errors)** scheda nella parte inferiore della pagina. 

Ogni area problematica ha Intent che devono essere corrette. Quando si seleziona il nome della finalità, il **finalità** verrà visualizzata la pagina con un filtro applicato per le espressioni. Questo filtro consente di concentrare le espressioni che causano il problema.

### <a name="compare-changes-across-versions"></a>Confrontare le modifiche apportate tra le versioni

Creare una nuova versione prima di apportare modifiche all'app. Nella nuova versione, apportare le modifiche suggerite per espressioni di esempio dell'intento, quindi eseguire nuovamente il training. Nella pagina del Dashboard **valutazione Training** schede, utilizzare il **Show modifiche dalla versione sottoposto a training** per confrontare le modifiche. 

![Confrontare le modifiche apportate tra le versioni](./media/luis-how-to-use-dashboard/compare-improvement-across-versions.png)

### <a name="fix-version-by-adding-or-editing-example-utterances-and-retraining"></a>Correggere versione aggiungendo o modifica di espressioni di esempio e di ripetizione del training

Il metodo principale di correggere l'app sarà quello di aggiungere o modificare espressioni di esempio e ripetere il training. È necessario attenersi alle linee guida per le espressioni nuove o modificate [variato utterances](luis-concept-utterance.md).

Aggiunta di espressioni di esempio deve essere eseguita da un utente che ha:

* ha un elevato livello di individuazione delle espressioni in diverse finalità
* sa come espressioni in un solo intent potrebbero essere confusi con un altro scopo
* è in grado di stabilire se due finalità, che vengono frequentemente confuse tra loro, devono essere compressi in un unico intento, e diversi dati estratti con entità

### <a name="patterns-and-phrase-lists"></a>Modelli ed elenchi di frase

Non indica quando usare la pagina di analitica [pattern](luis-concept-patterns.md) o [una frase elenchi](luis-concept-feature.md). Se sono state aggiunte, utili per le stime non corrette o poco chiara, ma sono inefficaci con lo sbilanciamento delle classi di dati. 

### <a name="review-data-imbalance"></a>Sbilanciamento delle classi di revisione dei dati

Iniziare con questo problema, potrebbe essere la causa radice dei problemi di altri.

Il **lo sbilanciamento delle classi di dati** preventivo elenco Mostra gli Intent che richiedono altre espressioni per correggere lo sbilanciamento di dati. 

**Per risolvere questo problema**:

* Aggiungere altre espressioni alla finalità, quindi eseguire nuovamente il training. 

Non aggiungere espressioni a None preventivo a meno che non che viene suggerito nel dashboard.

> [!Tip]
> Utilizzare la terza sezione nella pagina **Utterances per finalità** con il **Utterances (numero)** impostazione, come una guida visiva rapida dei quali gli Intent necessario altre espressioni.  
    ![Usare 'Utterances (numero)' per trovare gli Intent con lo sbilanciamento delle classi di dati.](./media/luis-how-to-use-dashboard/predictions-per-intent-number-of-utterances.png)

### <a name="review-incorrect-predictions"></a>Esaminare le stime non corrette

Il **stima corretta** preventivo elenco Mostra gli Intent con espressioni, che vengono usati come esempi per uno scopo specifico, ma vengono stimate per finalità diverse. 

**Per risolvere questo problema**:

* Modificare espressioni per essere più specifici per le finalità e training.
* Combinare gli Intent se utterances sono troppo strettamente allineato e ripetere il training.

### <a name="review-unclear-predictions"></a>Esaminare le stime chiara

Il **stima chiaro** Intent con espressioni con i punteggi di stima che non sono sufficientemente modo dal loro applicazione rivale richiede più vicino, che lo scopo principale per il utterance può cambiare ai corsi di formazione successivo a causa dell'errore sono elencate preventivo [ campionamento negativo](luis-how-to-train.md#train-with-all-data).

**Per risolvere questo problema**;

* Modificare espressioni per essere più specifici per le finalità e training.
* Combinare gli Intent se utterances sono troppo strettamente allineato e ripetere il training.

## <a name="utterances-per-intent"></a>Espressioni per finalità

Questa scheda Mostra l'integrità complessiva di app per le finalità. Come si correggono gli Intent e ripetizione del training, continuare a visualizzare questa scheda per i problemi rapidamente.

Il grafico seguente mostra un'app ben bilanciata quasi senza problemi da risolvere.

![Il grafico seguente mostra un'app ben bilanciata quasi senza problemi da risolvere.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-balance.png)

Il grafico seguente mostra un'app in modo inadeguato bilanciata con molti problemi da risolvere.

![Il grafico seguente mostra un'app ben bilanciata quasi senza problemi da risolvere.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-imbalance.png)

Passare il mouse sulla barra dell'intento ogni per ottenere informazioni sull'intento. 

![Il grafico seguente mostra un'app ben bilanciata quasi senza problemi da risolvere.](./media/luis-how-to-use-dashboard/utterances-per-intent-with-details-of-errors.png)

Usare la **per ordinare** funzionalità per disporre l'Intent per tipo di problema in modo che sia possibile concentrare la finalità più problematiche con cui il problema. 

## <a name="intents-with-errors"></a>Intent con errori

Questa scheda consente di esaminare i problemi per uno scopo specifico. La visualizzazione predefinita di questa scheda è la finalità più problematiche in modo che si conosce la posizione in cui concentrare l'attenzione.

![La finalità con carta errori consente rivedere i problemi per uno scopo specifico. La scheda viene filtrata per gli scopi più problematici, per impostazione predefinita, in modo che si conosce la posizione in cui concentrare l'attenzione.](./media/luis-how-to-use-dashboard/most-problematic-intents-with-errors.png)

Grafico ad anello superiore mostra i problemi con l'intento tra i tipi di problemi di tre. Se sono presenti problemi nei tipi di tre problema, ogni tipo ha un proprio grafico riportato di seguito, insieme a eventuali Intent rivale. 

### <a name="filter-intents-by-issue-and-percentage"></a>Filtro Intent dal problema e la percentuale

In questa sezione della scheda consente di trovare espressioni di esempio che è in diminuzione di fuori della soglia di errore. È opportuno che le stime corrette per essere significativo. Tale percentuale è basata sul cliente e business. 

Determinare le percentuali di soglia che si è pronti per la tua azienda. 

Il filtro consente di trovare gli Intent problema specifico:

|Applica filtro|Percentuale suggerita|Scopo|
|--|--|--|
|Intent più problematici|-|**Iniziare da qui** -correzione le espressioni in questa finalità migliorerà l'app più di altre correzioni.|
|Stime corrette riportato di seguito|60%|Questa è la percentuale di espressioni nell'intenzione selezionato siano corretti, ma hanno un punteggio di confidenza di sotto della soglia. |
|Stime chiara precedente|15%|Questa è la percentuale di espressioni nell'intenzione selezionato che sono confuso con l'intento rivale più vicino.|
|Stime inesatte precedente|15%|Questa è la percentuale di espressioni nell'intenzione selezionato che sono stimati in modo non corretto. |

### <a name="correct-prediction-threshold"></a>Soglia di stima corretta

Che cos'è un punteggio di confidenza stima sicuri all'utente? All'inizio dello sviluppo di app, il 60% potrebbe essere la destinazione. Usare la **correggere le stime seguenti** con la percentuale del 60% per trovare eventuali espressioni nell'intenzione selezionate che devono essere corrette.

### <a name="unclear-or-incorrect-prediction-threshold"></a>Soglia di stima non chiara o non corrette

Questi due filtri consentono di trovare utterances nell'intenzione oltre la soglia selezionata. È possibile considerare queste due percentuali sotto forma di percentuali di errore. Se si ha familiarità con una frequenza degli errori di 10-15% per le stime, impostare la soglia di filtro al 15% per trovare tutte le espressioni sopra questo valore. 

## <a name="next-steps"></a>Passaggi successivi

* [Gestire le risorse di Azure](luis-how-to-azure-subscription.md)
