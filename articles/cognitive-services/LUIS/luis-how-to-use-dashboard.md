---
title: Dashboard-Language Understanding
titleSuffix: Azure Cognitive Services
description: Correggere gli Intent con il dashboard di analisi, uno strumento di creazione di report visualizzato.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/22/2019
ms.author: diberry
ms.openlocfilehash: f068aa7ca6b396ebba05b9d9462d9e95faf7fbaa
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68296427"
---
# <a name="how-to-use-the-dashboard-to-improve-your-app"></a>Come usare il dashboard per migliorare l'app

Trova e correggi i problemi con gli Intent dell'app sottoposta a training quando usi espressioni di esempio. Il dashboard Visualizza le informazioni generali sull'app, con i punti salienti che devono essere corretti. 

Esaminare l'analisi del dashboard è un processo iterativo, che si ripete quando si modifica e si migliora il modello.

Questa pagina non avrà un'analisi pertinente per le app che non contengono espressioni di esempio negli Intent, note come app _solo modello_ . 

## <a name="what-issues-can-be-fixed-from-dashboard"></a>Quali problemi possono essere corretti dal dashboard?

I tre problemi trattati nel dashboard sono:

|Problema|Colore grafico|Spiegazione|
|--|--|--|
|Squilibrio dei dati|-|Questo errore si verifica quando la quantità di espressioni di esempio varia in modo significativo. Tutti gli Intent devono avere _approssimativamente_ lo stesso numero di espressioni di esempio, ad eccezione della finalità None. Deve avere solo il 10%-15% della quantità totale di espressioni nell'app.<br><br> Se i dati sono sbilanciati, ma l'accuratezza della finalità è superiore a una determinata soglia, questo squilibrio non viene segnalato come problema.<br><br>**Iniziare da questo problema. potrebbe essere la causa principale degli altri problemi.**|
|Non deselezionare le stime|Arancione|Questo errore si verifica quando i punteggi della parte superiore e della finalità successiva sono sufficientemente vicini che possono capovolgere il training successivo, a causa del [campionamento negativo](luis-how-to-train.md#train-with-all-data) o di altri enunciati di esempio aggiunti allo scopo. |
|Stime non corrette|Rosso|Questo errore si verifica quando un enunciato di esempio non viene stimato per l'intento con etichetta (l'intento è in).|

Le stime corrette sono rappresentate dal colore blu.

Il dashboard Mostra questi problemi e indica quali Intent sono interessati e suggerisce le operazioni da eseguire per migliorare l'app. 

## <a name="before-app-is-trained"></a>Prima del training dell'app 

Prima di eseguire il training dell'app, il dashboard non contiene suggerimenti per le correzioni. Eseguire il training dell'app per visualizzare questi suggerimenti.  

## <a name="check-your-publishing-status"></a>Verificare lo stato di pubblicazione

La scheda **stato pubblicazione** contiene informazioni sull'ultima pubblicazione della versione attiva. 

Verificare che la versione attiva sia la versione che si desidera correggere. 

![Il dashboard Mostra i servizi esterni, le aree pubblicate e i riscontri di endpoint aggregati dell'app.](./media/luis-how-to-use-dashboard/analytics-card-1-shows-app-summary-and-endpoint-hits.png)

Vengono inoltre visualizzati tutti i servizi esterni, le aree pubblicate e i riscontri di endpoint aggregati. 

## <a name="review-training-evaluation"></a>Esaminare la valutazione del training

La scheda di **valutazione del training** contiene il riepilogo aggregato della precisione complessiva dell'app per area. Il Punteggio indica la qualità della finalità. 

![La scheda di valutazione del training contiene la prima area di informazioni sull'accuratezza complessiva dell'app.](./media/luis-how-to-use-dashboard/analytics-card-2-shows-app-overall-accuracy.png)

Il grafico indica le finalità stimate correttamente e le aree problematiche con colori diversi. Quando si migliora l'app con i suggerimenti, questo punteggio aumenta. 

Le correzioni suggerite sono separate per tipo di problema e sono le più significative per l'app. Se si preferisce rivedere e correggere i problemi per finalità, usare la scheda **[Intent with Errors](#intents-with-errors)** nella parte inferiore della pagina. 

Per ogni area problematica è necessario correggere gli Intent. Quando si seleziona il nome della finalità, viene visualizzata la pagina **finalità** con un filtro applicato agli enunciati. Questo filtro consente di concentrarsi sulle espressioni che causano il problema.

### <a name="compare-changes-across-versions"></a>Confrontare le modifiche tra le versioni

Creare una nuova versione prima di apportare modifiche all'app. Nella nuova versione apportare le modifiche suggerite alle espressioni di esempio dello scopo, quindi eseguire di nuovo il training. Nella scheda di valutazione del **Training** della pagina dashboard usare la **versione Mostra modifica da** sottoposta a training per confrontare le modifiche. 

![Confrontare le modifiche tra le versioni](./media/luis-how-to-use-dashboard/compare-improvement-across-versions.png)

### <a name="fix-version-by-adding-or-editing-example-utterances-and-retraining"></a>Correzione della versione mediante l'aggiunta o la modifica di espressioni di esempio e la ripetizione del training

Il metodo principale per correggere l'app consiste nell'aggiungere o modificare le espressioni di esempio e ripetere il training. Le espressioni nuove o modificate devono seguire le linee guida per [varie espressioni](luis-concept-utterance.md).

L'aggiunta di espressioni di esempio deve essere eseguita da un utente che:

* ha un livello elevato di comprensione delle espressioni presenti nei diversi Intent
* sa come le espressioni possono essere confuse con un altro scopo
* è in grado di decidere se due Intent, che sono spesso confusi tra loro, devono essere compressi in un unico scopo e i diversi dati estratti con le entità

### <a name="patterns-and-phrase-lists"></a>Elenchi di modelli ed frasi

La pagina di analisi non indica quando usare i [modelli](luis-concept-patterns.md) o gli [elenchi di frasi](luis-concept-feature.md). Se si aggiungono questi elementi, può essere utile con stime non corrette o non chiare, ma non è utile per lo squilibrio dei dati. 

### <a name="review-data-imbalance"></a>Verifica squilibrio dei dati

Iniziare da questo problema. potrebbe essere la causa principale degli altri problemi.

L'elenco finalità dello **squilibrio dei dati** Mostra gli Intent che richiedono più espressioni per correggere lo squilibrio dei dati. 

**Per risolvere il problema**:

* Aggiungere più espressioni allo scopo, quindi eseguire di nuovo il training. 

Non aggiungere espressioni alla finalità None, a meno che non sia suggerito nel dashboard.

> [!Tip]
> Usare la terza sezione della pagina, **espressione per finalità** con l'impostazione **enunciazioni (numero)** , come guida visiva rapida per cui sono necessari più espressioni.  
    ![Usare ' enunciati (numero)' per individuare gli Intent con squilibrio dei dati.](./media/luis-how-to-use-dashboard/predictions-per-intent-number-of-utterances.png)

### <a name="review-incorrect-predictions"></a>Verifica stime non corrette

L'elenco di finalità di **stima non corrette** Mostra gli Intent con espressioni, che vengono usate come esempi per uno scopo specifico, ma vengono stimate per scopi diversi. 

**Per risolvere il problema**:

* Modificare le espressioni in modo che siano più specifiche per finalità e training.
* Combinare gli Intent se le espressioni sono troppo strettamente allineate e di nuovo il training.

### <a name="review-unclear-predictions"></a>Verifica le stime non chiare

L'elenco di finalità di **stima non chiare** Mostra gli Intent con i punteggi di stima che non sono sufficientemente lontani dalla rivalità più vicina, che lo scopo principale dell'espressione può variare al training successivo, a causa del campionamento [negativo](luis-how-to-train.md#train-with-all-data).

**Per risolvere il problema**:

* Modificare le espressioni in modo che siano più specifiche per finalità e training.
* Combinare gli Intent se le espressioni sono troppo strettamente allineate e di nuovo il training.

## <a name="utterances-per-intent"></a>Espressioni per finalità

Questa scheda Mostra l'integrità complessiva delle app negli Intent. Quando si correggono gli Intent e si ripete il training, continuare a esaminare questa scheda per individuare eventuali problemi.

Il grafico seguente mostra un'app ben bilanciata senza problemi da risolvere.

![Il grafico seguente mostra un'app ben bilanciata senza problemi da risolvere.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-balance.png)

Il grafico seguente mostra un'app con un bilanciamento del con molti problemi da risolvere.

![Il grafico seguente mostra un'app ben bilanciata senza problemi da risolvere.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-imbalance.png)

Passare il mouse sulla barra di ogni finalità per ottenere informazioni sullo scopo. 

![Il grafico seguente mostra un'app ben bilanciata senza problemi da risolvere.](./media/luis-how-to-use-dashboard/utterances-per-intent-with-details-of-errors.png)

Utilizzare la funzionalità **Ordina** per per organizzare gli Intent in base al tipo di problema, in modo da potersi concentrare sul problema più problematico. 

## <a name="intents-with-errors"></a>Intent con errori

Questa scheda consente di esaminare i problemi per uno scopo specifico. La visualizzazione predefinita di questa scheda è la più problematica, in modo da capire dove concentrarsi sulle attività.

![La scheda Intent with Errors consente di esaminare i problemi per uno scopo specifico. Per impostazione predefinita, la scheda viene filtrata in base alle finalità più problematiche, quindi si conosce la posizione in cui concentrare il lavoro.](./media/luis-how-to-use-dashboard/most-problematic-intents-with-errors.png)

Il grafico ad anello superiore mostra i problemi con l'intento nei tre tipi di problema. Se si verificano problemi nei tre tipi di problema, ogni tipo ha un proprio grafico di seguito, insieme a eventuali intenti ririvati. 

### <a name="filter-intents-by-issue-and-percentage"></a>Filtrare gli Intent per numero e percentuale

Questa sezione della scheda consente di trovare espressioni di esempio che esulano dalla soglia di errore. Idealmente si vuole che le stime corrette siano significative. Tale percentuale è basata sull'azienda e sui clienti. 

Determinare le percentuali di soglia con cui si ha dimestichezza per l'azienda. 

Il filtro consente di trovare gli Intent con un problema specifico:

|Filtro|Percentuale suggerita|Scopo|
|--|--|--|
|Intent più problematici|-|**Iniziare da qui** : correggere le espressioni in questo intento migliorerà l'app più di altre correzioni.|
|Correggere le stime di seguito|60%|Questa è la percentuale di enunciati nell'intento selezionato che sono corretti, ma hanno un punteggio di confidenza al di sotto della soglia. |
|Non deselezionare le stime precedenti|15%|Percentuale di enunciati nell'intento selezionato confusi con la finalità rivale più vicina.|
|Stime precedenti non corrette|15%|Percentuale di enunciati nell'intento selezionato che vengono stimati in modo errato. |

### <a name="correct-prediction-threshold"></a>Correzione della soglia di stima

Che cos'è un punteggio di confidenza per le stime sicure? All'inizio dello sviluppo di app, il 60% potrebbe essere la destinazione. Usare le **stime corrette seguenti** con la percentuale del 60% per trovare le espressioni nello scopo selezionato che deve essere corretto.

### <a name="unclear-or-incorrect-prediction-threshold"></a>Soglia di stima non chiara o non corretta

Questi due filtri consentono di trovare le espressioni nello scopo selezionato oltre la soglia. È possibile considerare queste due percentuali come percentuali di errore. Se si ha familiarità con una percentuale di errori del 10-15% per le stime, impostare la soglia di filtro su 15% per trovare tutti gli enunciati al di sopra di questo valore. 

## <a name="next-steps"></a>Passaggi successivi

* [Gestisci le tue risorse di Azure](luis-how-to-azure-subscription.md)
