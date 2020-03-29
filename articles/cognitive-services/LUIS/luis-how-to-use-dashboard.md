---
title: Dashboard - Informazioni sulla lingua - LUIS
titleSuffix: Azure Cognitive Services
description: Correggere finalità ed entità con il dashboard dell'app sottoposta a training. Il dashboard visualizza informazioni generali sull'app, con i punti salienti delle finalità che devono essere corrette.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.openlocfilehash: d9ae126753f55349f9bf3eefd20bc4d222866af1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73888209"
---
# <a name="how-to-use-the-dashboard-to-improve-your-app"></a>Come usare il dashboard per migliorare l'app

Trova e risolvi i problemi con le finalità dell'app addestrata quando usi espressioni di esempio. Il dashboard visualizza informazioni generali sull'app, con i punti salienti delle finalità che devono essere corrette. 

L'analisi del dashboard di revisione è un processo iterativo, si ripete quando si modifica e si migliora il modello.

Questa pagina non dia analisi pertinente per le app che non concedano espressioni di esempio nelle finalità, note come app _solo modello._ 

## <a name="what-issues-can-be-fixed-from-dashboard"></a>Quali problemi possono essere risolti dal dashboard?

I tre problemi risolti nel dashboard sono:

|Problema|Colore grafico|Spiegazione|
|--|--|--|
|Squilibrio dei dati|-|Ciò si verifica quando la quantità di espressioni di esempio varia in modo significativo. Tutte le finalità devono avere _all'incirca_ lo stesso numero di espressioni di esempio, ad eccezione della finalità Nessuno.All intents need to have roughly the same number of example utterances - except the None intent. Deve avere solo il 10%-15% della quantità totale di espressioni nell'app.<br><br> Se i dati sono sbilanciati ma l'accuratezza dell'intento è superiore a una determinata soglia, questo squilibrio non viene segnalato come un problema.<br><br>**Iniziare con questo problema - può essere la causa principale degli altri problemi.**|
|Previsioni poco chiare|Arancione|Ciò si verifica quando la finalità superiore e i punteggi della finalità successiva sono abbastanza vicini da poter eseguire il capovolgimento del training successivo, a causa del [campionamento negativo](luis-how-to-train.md#train-with-all-data) o di più espressioni di esempio aggiunte alla finalità. |
|Previsioni errate|Rosso|Ciò si verifica quando un'espressione di esempio non è prevista per la finalità etichettata (la finalità in).|

Le stime corrette sono rappresentate con il colore blu.

Il dashboard mostra questi problemi e indica quali finalità sono interessate e suggerisce cosa è necessario fare per migliorare l'app. 

## <a name="before-app-is-trained"></a>Prima che l'app venga addestrata 

Prima di eseguire il training dell'app, il dashboard non contiene suggerimenti per le correzioni. Addestrare l'app per visualizzare questi suggerimenti.  

## <a name="check-your-publishing-status"></a>Controllare lo stato della pubblicazione

La scheda **Stato pubblicazione** contiene informazioni sull'ultima pubblicazione della versione attiva. 

Verificare che la versione attiva sia la versione che si desidera correggere. 

![Il dashboard mostra i servizi esterni dell'app, le aree pubblicate e gli hit degli endpoint aggregati.](./media/luis-how-to-use-dashboard/analytics-card-1-shows-app-summary-and-endpoint-hits.png)

Vengono visualizzati anche tutti i servizi esterni, le aree pubblicate e gli hit degli endpoint aggregati. 

## <a name="review-training-evaluation"></a>Rivedere la valutazione della formazione

La scheda **di valutazione Training** contiene il riepilogo aggregato dell'accuratezza complessiva dell'app in base all'area. Il punteggio indica la qualità dell'intento. 

![La scheda di valutazione Formazione contiene la prima area di informazioni sull'accuratezza complessiva dell'app.](./media/luis-how-to-use-dashboard/analytics-card-2-shows-app-overall-accuracy.png)

Il grafico indica le finalità previste correttamente e le aree problematiche con colori diversi. Man mano che migliori l'app con i suggerimenti, questo punteggio aumenta. 

Le correzioni suggerite sono separate dal tipo di problema e sono le più significative per l'app. Se si preferisce esaminare e risolvere i problemi per finalità, utilizzare la scheda **[Intenti con errori](#intents-with-errors)** nella parte inferiore della pagina. 

Ogni area problematica ha intenzioni che devono essere risolte. Quando si seleziona il nome della finalità, viene visualizzata la pagina **Intento** con un filtro applicato alle espressioni. Questo filtro consente di concentrarsi sulle espressioni che causano il problema.

### <a name="compare-changes-across-versions"></a>Confrontare le modifiche tra le versioni

Crea una nuova versione prima di apportare modifiche all'app. Nella nuova versione apportare le modifiche suggerite alle espressioni di esempio della finalità, quindi eseguire nuovamente il training. Nella scheda di **valutazione Formazione** della pagina Dashboard usare la scheda di valutazione Mostra modifica rispetto alla **versione addestrata** per confrontare le modifiche. 

![Confrontare le modifiche tra le versioni](./media/luis-how-to-use-dashboard/compare-improvement-across-versions.png)

### <a name="fix-version-by-adding-or-editing-example-utterances-and-retraining"></a>Correggere la versione aggiungendo o modificando espressioni di esempio e riqualificando

Il metodo principale per correggere l'app consiste nell'aggiungere o modificare espressioni di esempio e rieseguire il training. Le espressioni nuove o modificate devono seguire le linee guida per [le varie espressioni.](luis-concept-utterance.md)

L'aggiunta di espressioni di esempio deve essere eseguita da un utente che:Adding example utterances should be done by someone who:

* ha un alto grado di comprensione di ciò che le espressioni sono nelle diverse finalità.
* sa come le espressioni in una finalità possono essere confuse con un'altra finalità.
* è in grado di decidere se due finalità, che sono spesso confuse tra loro, devono essere compresse in un'unica finalità. In questo caso, i diversi dati devono essere estratti con le entità.

### <a name="patterns-and-phrase-lists"></a>Modelli ed elenchi di frasi

La pagina di analisi non indica quando utilizzare [modelli](luis-concept-patterns.md) o [elenchi di frasi.](luis-concept-feature.md) Se li aggiungi, può aiutare con previsioni errate o poco chiare, ma non aiuterà con lo squilibrio dei dati. 

### <a name="review-data-imbalance"></a>Esaminare lo squilibrio dei dati

Iniziare con questo problema - può essere la causa principale degli altri problemi.

L'elenco di finalità **dello squilibrio dati** mostra le finalità che richiedono più espressioni per correggere lo squilibrio dati. 

**Per risolvere questo problema:**

* Aggiungere altre espressioni alla finalità quindi eseguire nuovamente il training. 

Non aggiungere espressioni alla finalità Nessuno a meno che non venga suggerito nel dashboard.

> [!Tip]
> Usare la terza sezione della pagina **Utterances per finalità** con l'impostazione **Utterances (numero),** come guida visiva rapida delle quali finalità richiedono più espressioni.  
    ![Utilizzare 'Utterances (number)' per trovare finalità con squilibrio dei dati.](./media/luis-how-to-use-dashboard/predictions-per-intent-number-of-utterances.png)

### <a name="review-incorrect-predictions"></a>Esaminare le stime errateReview incorrect predictions

L'elenco delle finalità di **stima non corretto** mostra le finalità con espressioni, che vengono usate come esempi per una finalità specifica, ma sono stimate per finalità diverse. 

**Per risolvere questo problema:**

* Modificare le espressioni in modo che siano più specifiche per la finalità ed eseguire nuovamente il training.
* Combinare le finalità se le espressioni sono troppo allineate ed eseguire nuovamente il training.

### <a name="review-unclear-predictions"></a>Esaminare le previsioni poco chiare

L'elenco delle finalità **di stima non chiaro** mostra le finalità con espressioni con punteggi di stima che non sono abbastanza lontano dal rivale più vicino, che la finalità superiore per l'espressione può cambiare nel training successivo, a causa del [campionamento negativo.](luis-how-to-train.md#train-with-all-data)

**Per risolvere questo problema**;

* Modificare le espressioni in modo che siano più specifiche per la finalità ed eseguire nuovamente il training.
* Combinare le finalità se le espressioni sono troppo allineate ed eseguire nuovamente il training.

## <a name="utterances-per-intent"></a>Espressioni per finalità

Questa scheda mostra l'integrità complessiva dell'app tra le finalità. Quando si risolvono le finalità e si riaddestramenta, continuare a dare un'occhiata a questa scheda per i problemi.

Il grafico seguente mostra un'app ben bilanciata con quasi nessun problema da risolvere.

![Il grafico seguente mostra un'app ben bilanciata con quasi nessun problema da risolvere.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-balance.png)

Il grafico seguente mostra un'app scarsamente bilanciata con molti problemi da risolvere.

![Il grafico seguente mostra un'app ben bilanciata con quasi nessun problema da risolvere.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-imbalance.png)

Passare il mouse sulla barra di ogni finalità per ottenere informazioni sulla finalità. 

![Il grafico seguente mostra un'app ben bilanciata con quasi nessun problema da risolvere.](./media/luis-how-to-use-dashboard/utterances-per-intent-with-details-of-errors.png)

Utilizzare la funzionalità **Ordina** per per disporre le finalità in base al tipo di problema in modo da potersi concentrare sulle finalità più problematiche con tale problema. 

## <a name="intents-with-errors"></a>Intenti con errori

Questa scheda consente di esaminare i problemi per un intento specifico. La visualizzazione predefinita di questa scheda è l'intento più problematico in modo da sapere dove concentrare i propri sforzi.

![La scheda Intenti con errori consente di esaminare i problemi relativi a un intento specifico. La scheda viene filtrata in base alle finalità più problematiche, per impostazione predefinita, in modo da sapere dove concentrare i propri sforzi.](./media/luis-how-to-use-dashboard/most-problematic-intents-with-errors.png)

Il grafico ad anello superiore mostra i problemi con l'intento nei tre tipi di problema. Se sono presenti problemi nei tre tipi di problema, ogni tipo ha il proprio grafico di seguito, insieme a tutte le finalità rivali. 

### <a name="filter-intents-by-issue-and-percentage"></a>Filtrare le finalità per problema e percentuale

Questa sezione della scheda consente di trovare espressioni di esempio che non rientrano nella soglia di errore. Idealmente si desidera che le stime corrette siano significative. Tale percentuale è guidata dal business e dal cliente. 

Determina le percentuali di soglia con cui ti senti a tuo agio per la tua azienda. 

Il filtro consente di trovare finalità con problemi specifici:

|Filtro|Percentuale suggerita|Scopo|
|--|--|--|
|Finalità più problematiche|-|**Inizia da qui:** la correzione delle espressioni in questa finalità migliorerà l'app più di altre correzioni.|
|Correggere le previsioni di seguito|60%|Si tratta della percentuale di espressioni nella finalità selezionata corrette ma con un punteggio di confidenza inferiore alla soglia. |
|Previsioni poco chiare di cui sopra|15%|Si tratta della percentuale di espressioni nella finalità selezionata che vengono confuse con l'intento rivale più vicino.|
|Previsioni errate sopra|15%|Si tratta della percentuale di espressioni nella finalità selezionata stimate in modo non corretto. |

### <a name="correct-prediction-threshold"></a>Correggere la soglia di stima

Qual è un punteggio di confidenza di stima sicuro per te? All'inizio dello sviluppo di app, il 60% potrebbe essere il tuo obiettivo. Usare le **stime corrette di seguito** con la percentuale del 60% per trovare eventuali espressioni nella finalità selezionata che devono essere corrette.

### <a name="unclear-or-incorrect-prediction-threshold"></a>Soglia di stima non chiara o non corretta

Questi due filtri consentono di trovare espressioni nella finalità selezionata oltre la soglia. È possibile considerare queste due percentuali come percentuali di errore. Se si ha familiarità con una percentuale di errore del 10-15% per le stime, impostare la soglia del filtro su 15% per trovare tutte le espressioni al di sopra di questo valore. 

## <a name="next-steps"></a>Passaggi successivi

* [Gestire le risorse di Azure](luis-how-to-azure-subscription.md)
