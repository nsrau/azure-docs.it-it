---
title: Modelli Aggiungi accuratezza-LUIS
titleSuffix: Azure Cognitive Services
description: Aggiungere modelli modello per migliorare l'accuratezza della stima nelle applicazioni Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 7b6c8ba1517de44d01ffbceec812749403465e63
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638083"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Come aggiungere modelli per migliorare l'accuratezza della stima
Dopo la ricezione di espressioni di endpoint da un'app LUIS, usare un [modello](luis-concept-patterns.md) per migliorare l'accuratezza della stima per le espressioni che rivelano un modello in Word Order e Word Choice. I modelli usano una [sintassi](luis-concept-patterns.md#pattern-syntax) specifica per indicare la posizione di: [entità](luis-concept-entity-types.md), [ruoli](luis-concept-roles.md)di entità e testo facoltativo.

## <a name="add-template-utterance-to-create-pattern"></a>Aggiungere l'espressione di modello per creare un criterio
1. Apri l'applicazione selezionando il corrispettivo nome nella pagina **App personali** e quindi seleziona **Criteri** nel riquadro sinistro, nella sezione **Migliora le prestazioni dell'applicazione**.

    ![Schermata dell'elenco dei criteri](./media/luis-how-to-model-intent-pattern/patterns-1.png)

2. Seleziona la finalità corretta per il criterio. 

    ![Selezionare la finalità](./media/luis-how-to-model-intent-pattern/patterns-2.png)

3. Nella casella di testo del modello, digita l'espressione del modello e premi Invio. Quando vuoi inserire il nome dell'entità, usa la sintassi dell'entità corretta del criterio. Inizia la sintassi dell'entità con `{`. Viene visualizzato l'elenco di entità. Seleziona l'entità corretta, quindi premi Invio. 

    ![Schermata dell'entità per il criterio](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Se l'entità include un [ruolo](luis-concept-roles.md), indicare il ruolo con un solo segno di `:`due punti,, dopo il `{Location:Origin}`nome dell'entità, ad esempio. I ruoli per le entità vengono visualizzati in un elenco. Seleziona il ruolo, quindi fai clic su Invio. 

    ![Schermata dell'entità con un ruolo](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    Dopo aver selezionato l'entità corretta, completa l'inserimento del criterio, quindi premi Invio. Al termine dell'inserimento dei criteri [esegui il training](luis-how-to-train.md) dell'app.

    ![Schermata del criterio inserito con entrambi i tipi di entità](./media/luis-how-to-model-intent-pattern/patterns-5.png)

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

## <a name="use-contextual-toolbar"></a>Usa barra degli strumenti contestuale

La barra degli strumenti contestuale sopra l'elenco di modelli consente di:

* Ricerca di modelli
* Modificare un criterio
* Riassegnare un criterio singolo a diverse finalità
* Riassegnare diversi criteri a finalità diverse
* Delete-a-singolo modello
* Eliminare diversi criteri
* Filtrare l'elenco dei criteri per entità
* Filter-pattern-list-by-Intent
* Rimuovere il filtro entità o finalità
* Aggiungi un criterio da un'espressione esistente nella pagina delle entità o finalità

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [creare un modello](luis-tutorial-pattern.md) con un modello. tutti i ruoli e con un'esercitazione.
* Informazioni su come [eseguire il training](luis-how-to-train.md) dell'app.
