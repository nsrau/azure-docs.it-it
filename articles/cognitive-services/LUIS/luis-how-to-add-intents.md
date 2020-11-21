---
title: Aggiungi Intent-LUIS
titleSuffix: Azure Cognitive Services
description: Aggiungi le finalità all'app LUIS per identificare gruppi di domande o comandi con gli stessi scopi.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.service: cognitive-services
ms.openlocfilehash: f2401c032f0fc90024e0049fad5f696b8a184544
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018940"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>Aggiungere finalità per determinare l'intenzione delle espressioni dell'utente

Aggiungi le [finalità](luis-concept-intent.md) all'app LUIS per identificare gruppi di domande o comandi con lo stesso scopo.

Nel portale LUIS, gli Intent sono gestiti dalla sezione di **compilazione** della barra di spostamento superiore, quindi dal riquadro **sinistro.**

## <a name="add-an-intent-to-your-app"></a>Aggiungere una finalità all'app

1. Accedere al [portale LUIS](https://www.luis.ai) e quindi selezionare la **sottoscrizione** e la **risorsa di creazione** per vedere le app assegnate a tale risorsa.
1. Per aprire l'app, selezionarne il nome nella pagina **app personali** .
1. Nella pagina **Intent** selezionare **+ Crea**.
1. Nella finestra di dialogo **Crea nuova finalità** immettere il nome della finalità, ad esempio `ModifyOrder` , e **fare** clic su fine.

    > [!div class="mx-imgBorder"]
    > ![Aggiungi finalità](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    Per prevedere le espressioni in corrispondenza dell'endpoint di stima pubblicato, lo scopo richiede [espressioni di esempio](luis-concept-utterance.md) .

## <a name="add-an-example-utterance"></a>Aggiungere un'espressione di esempio

Le espressioni di esempio sono campioni di testo con domande o commenti degli utenti. Per insegnare a Language Understanding (LUIS) quando eseguire la stima di questa finalità, è necessario aggiungere espressioni di esempio a uno scopo. LUIS necessita di un intervallo compreso tra 15 e 30 espressioni di esempio per iniziare a comprendere lo scopo. Non aggiungere espressioni di esempio in blocco. Ogni espressione deve essere scelta in modo accurato per il modo in cui è diversa dagli esempi già presenti nello scopo.

1. Nella pagina Dettagli finalità immettere un enunciato pertinente previsto dagli utenti, ad esempio `Deliver a large cheese pizza` nella casella di testo sotto il nome della finalità, quindi premere INVIO.

    > [!div class="mx-imgBorder"]
    > ![Schermata della pagina dei dettagli delle finalità, con un'espressione evidenziata](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png)

    LUIS converte tutti gli enunciati in minuscolo e aggiunge spazi attorno ai [token](luis-language-support.md#tokenization) , ad esempio i trattini.

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>Errori di stima per finalità

Un errore di stima preventivo è determinato quando l'espressione non viene stimata con l'app sottoposta a training per lo scopo.

1. Per individuare gli errori di stima dell'espressione e correggerli, utilizzare le opzioni di **filtro** non corrette e non chiare.

    > [!div class="mx-imgBorder"]
    > ![Per individuare gli errori di stima dell'espressione e correggerli, utilizzare l'opzione filtro.](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

1. Per visualizzare il valore Score nella pagina Dettagli finalità, scegliere Mostra punteggi Details **Intent** dal menu opzioni di **visualizzazione** .

    Quando vengono applicati i filtri e la visualizzazione e sono presenti espressioni di esempio con errori, l'elenco di espressioni di esempio mostra le espressioni e i problemi.

Ogni riga indica il Punteggio di stima della formazione corrente per l'espressione di esempio, il punteggio della rivalenza più vicino, che rappresenta la differenza in questi due punteggi.

### <a name="fixing-intents"></a>Correzione di Intent

Per informazioni su come risolvere gli errori di stima degli Intent, usare il [dashboard di riepilogo](luis-how-to-use-dashboard.md). Il dashboard di riepilogo fornisce l'analisi dell'ultimo training della versione attiva e offre i suggerimenti principali per correggere il modello.

## <a name="using-the-contextual-toolbar"></a>Usare la barra degli strumenti contestuale

La barra degli strumenti del contesto fornisce altre azioni:

* Modifica o Elimina espressione di esempio
* Riassegnare l'espressione di esempio a un'altra finalità
* Filtri e visualizzazioni: Mostra solo espressioni contenenti entità filtrate o Visualizza dettagli facoltativi
* Ricerca di espressioni di esempio

## <a name="train-your-app-after-changing-model-with-intents"></a>Eseguire il training dell'app dopo la modifica del modello con le finalità

Dopo aver aggiunto, modificato o rimosso le finalità, [eseguire il training](luis-how-to-train.md) e [pubblicare](luis-how-to-publish-app.md) l'app per applicare le modifiche alle query di endpoint. Non eseguire il training dopo ogni singola modifica. Eseguire il training dopo un gruppo di modifiche.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull'aggiunta di [espressioni di esempio](./luis-how-to-add-entities.md) con entità.