---
title: I modelli aggiungono precisione - LUISPatterns add accuracy - LUIS
titleSuffix: Azure Cognitive Services
description: Aggiungere modelli di modello per migliorare l'accuratezza della stima nelle applicazioni LUIS (Language Understanding).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: diberry
ms.openlocfilehash: 21afb12bf2464218119ebf52ebd980745e3d731d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76311717"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Come aggiungere modelli per migliorare l'accuratezza della stimaHow to add patterns to improve prediction accuracy
Dopo che un'app LUIS riceve le espressioni endpoint, usare un [modello](luis-concept-patterns.md) per migliorare l'accuratezza della stima per le espressioni che rivelano un modello nell'ordine delle parole e nella scelta delle parole. I modelli utilizzano [una sintassi](luis-concept-patterns.md#pattern-syntax) specifica per indicare la posizione di: [entità](luis-concept-entity-types.md), [ruoli](luis-concept-roles.md)di entità e testo facoltativo.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

> [!CAUTION]
> I modelli includono solo gli elementi padre di entità appresi dal computer, non i sottocomponenti.

## <a name="adding-example-utterances-as-pattern"></a>Aggiunta di espressioni di esempio come modelloAdding example utterances as pattern

Se si desidera aggiungere un motivo per un'entità, il modo _più semplice_ consiste nel creare il motivo dalla pagina Dettagli intento. In questo modo la sintassi corrisponde all'espressione di esempio.

1. Nel [portale LUIS](https://preview.luis.ai)preview selezionare l'app nella pagina **App personali.**
1. Nella pagina elenco **Finalità** selezionare il nome della finalità dell'espressione di esempio da cui si vuole creare un'espressione del modello.
1. Nella pagina Dettagli intento selezionare la riga per l'espressione di esempio che si vuole usare come espressione del modello, quindi selezionare **Aggiungi come modello** dalla barra degli strumenti di contesto.

    > [!div class="mx-imgBorder"]
    > ![Screenshot della selezione dell'espressione di esempio come modello di modello nella pagina Dettagli finalità.](./media/luis-how-to-model-intent-pattern/add-example-utterances-as-pattern-template-utterance-from-intent-detail-page.png)

1. Nella casella popup, selezionare **Fatto** nella pagina **Conferma modelli.** Non è necessario definire sottocomponenti, vincoli o descrittori delle entità. È sufficiente elencare l'entità appresa dal computer.

    > [!div class="mx-imgBorder"]
    > ![Screenshot della conferma dell'espressione di esempio come modello nella pagina Dettagli finalità.](./media/luis-how-to-model-intent-pattern/confirm-patterns-from-example-utterance-intent-detail-page.png)

1. Se è necessario modificare il modello, ad esempio `[]` selezionando il testo come facoltativo, con le parentesi quadre (quadrate), è necessario apportare questa modifica dalla pagina **Modelli.**

1. Nella barra di spostamento seleziona **Addestra** per addestrare l'app con il nuovo modello.

## <a name="add-template-utterance-using-correct-syntax"></a>Aggiungere l'espressione del modello usando la sintassi correttaAdd template utterance using correct syntax

1. Apri l'applicazione selezionando il corrispettivo nome nella pagina **App personali** e quindi seleziona **Criteri** nel riquadro sinistro, nella sezione **Migliora le prestazioni dell'applicazione**.

    > [!div class="mx-imgBorder"]
    > ![Schermata dell'elenco dei criteri](./media/luis-how-to-model-intent-pattern/patterns-1.png)

1. Seleziona la finalità corretta per il criterio.

1. Nella casella di testo del modello, digita l'espressione del modello e premi Invio. Quando vuoi inserire il nome dell'entità, usa la sintassi dell'entità corretta del criterio. Inizia la sintassi dell'entità con `{`. Viene visualizzato l'elenco di entità. Selezionare l'entità corretta.

    > [!div class="mx-imgBorder"]
    > ![Schermata dell'entità per il criterio](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Se l'entità include un [ruolo](luis-concept-roles.md), indicare il ruolo con un singolo segno di due punti, `:`, dopo il nome dell'entità, ad `{Location:Origin}`esempio . I ruoli per le entità vengono visualizzati in un elenco. Seleziona il ruolo, quindi fai clic su Invio.

    > [!div class="mx-imgBorder"]
    > ![Schermata dell'entità con un ruolo](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    Dopo aver selezionato l'entità corretta, completa l'inserimento del criterio, quindi premi Invio. Al termine dell'inserimento dei criteri [esegui il training](luis-how-to-train.md) dell'app.

    > [!div class="mx-imgBorder"]
    > ![Schermata del criterio inserito con entrambi i tipi di entità](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>Eseguire il training dell'app dopo la modifica del modello con i criteri
Dopo aver aggiunto, modificato, rimosso o riassegnato un criterio, [esegui il training](luis-how-to-train.md) e [pubblica](luis-how-to-publish-app.md) l'app affinché le modifiche siano attive per le query di endpoint.

<a name="search-patterns"></a>
<a name="edit-a-pattern"></a>
<a name="reassign-individual-pattern-to-different-intent"></a>
<a name="reassign-several-patterns-to-different-intent"></a>
<a name="delete-a-single-pattern"></a>
<a name="delete-several-patterns"></a>
<a name="filter-pattern-list-by-entity"></a>
<a name="filter-pattern-list-by-intent"></a>
<a name="remove-entity-or-intent-filter"></a>
<a name="add-pattern-from-existing-utterance-on-intent-or-entity-page"></a>

## <a name="use-contextual-toolbar"></a>Utilizzare la barra degli strumenti contestuale

La barra degli strumenti contestuale sopra l'elenco dei modelli consente di:

* Ricerca di modelli
* Modificare un criterio
* Riassegnare un criterio singolo a diverse finalità
* Riassegnare diversi criteri a finalità diverse
* Eliminare un modello singoloDelete-a-single-pattern
* Eliminare diversi criteri
* Filtrare l'elenco dei criteri per entità
* Filtro-modello-elenco-per finalità
* Rimuovere il filtro entità o finalità
* Aggiungi un criterio da un'espressione esistente nella pagina delle entità o finalità

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [creare un modello](luis-tutorial-pattern.md) con un pattern.any e ruoli con un'esercitazione.
* Informazioni su come [eseguire il training](luis-how-to-train.md) dell'app.
