---
title: Aggiungi Intent-LUIS
titleSuffix: Azure Cognitive Services
description: Aggiungi le finalità all'app LUIS per identificare gruppi di domande o comandi con gli stessi scopi.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: b2cb7494ae3d26fa14bef906b8f5222b9dbc70e1
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83584972"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>Aggiungere finalità per determinare l'intenzione delle espressioni dell'utente

Aggiungi le [finalità](luis-concept-intent.md) all'app LUIS per identificare gruppi di domande o comandi con lo stesso scopo.

Le finalità sono gestite nella sezione **Compilazione** sulla barra di navigazione superiore nel pannello **Finalità** a sinistra.

## <a name="add-intent"></a>Aggiungere le finalità

1. Accedere al [portale Luis](https://www.luis.ai)e selezionare la **sottoscrizione** e la risorsa di **creazione** per visualizzare le app assegnate a tale risorsa di creazione.
1. Per aprire l'app, selezionarne il nome nella pagina **app personali** .
1. Nella pagina **Intent** selezionare **+ Crea**.
1. Nella finestra di dialogo **Crea nuova finalità** immettere il nome della finalità, ad esempio `ModifyOrder` , e **fare**clic su fine.

    > [!div class="mx-imgBorder"]
    > ![Aggiungi finalità](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    Lo scopo richiede espressioni di esempio.

## <a name="add-an-example-utterance"></a>Aggiungere un'espressione di esempio

Le espressioni di esempio sono campioni di testo con domande o commenti degli utenti. Per insegnare a Language Understanding (LUIS) quando eseguire la stima di questa finalità, è necessario aggiungere espressioni di esempio a uno scopo. LUIS necessita di un intervallo compreso tra 15 e 30 espressioni di esempio per iniziare a comprendere lo scopo. Non aggiungere espressioni di esempio in blocco. Ogni espressione deve essere scelta in modo accurato per il modo in cui è diversa dagli esempi già presenti nello scopo.

1. Nella pagina Dettagli finalità immettere un enunciato pertinente previsto dagli utenti, ad esempio `Deliver a large cheese pizza` nella casella di testo sotto il nome della finalità, quindi premere INVIO.

    > [!div class="mx-imgBorder"]
    > ![Schermata della pagina dei dettagli delle finalità, con un'espressione evidenziata](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png)

    LUIS converte tutti gli enunciati in minuscolo e aggiunge spazi attorno ai [token](luis-language-support.md#tokenization) , ad esempio i trattini.

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>Errori di stima per finalità

Un enunciato di esempio in uno scopo potrebbe avere un errore di stima preventivo tra la finalità in cui è attualmente presente l'espressione di esempio e l'intento determinato durante il training.

Per individuare gli errori di stima dell'espressione e correggerli, utilizzare le opzioni di **filtro** non corrette e non chiare combinate con l'opzione **Visualizza** della **visualizzazione dettagliata**.

![Per individuare gli errori di stima dell'espressione e correggerli, utilizzare l'opzione filtro.](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

Quando vengono applicati i filtri e la visualizzazione e sono presenti espressioni di esempio con errori, l'elenco di espressioni di esempio mostra le espressioni e i problemi.

> [!div class="mx-imgBorder"]
> ![! [Quando vengono applicati i filtri e la visualizzazione e sono presenti espressioni di esempio con errori, l'elenco di espressioni di esempio mostra le espressioni e i problemi.] (./media/luis-how-to-add-intents/find-errors-in-utterances.png)](./media/luis-how-to-add-intents/find-errors-in-utterances.png#lightbox)

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

Altre informazioni sull'aggiunta di [espressioni di esempio](luis-how-to-add-example-utterances.md) con entità.
