---
title: Miglioramento dell'accuratezza grazie ai criteri
titleSuffix: Language Understanding - Azure Cognitive Services
description: Aggiungere modelli di schema per migliorare l'accuratezza della stima nelle applicazioni di LUIS (Language Understanding).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 202b9632b7a7faaf955874a0300edbe5134b7fa1
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/12/2019
ms.locfileid: "59521255"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Come aggiungere i modelli per migliorare l'accuratezza della stima
Dopo che un'app LUIS riceve utterances endpoint, usare una [pattern](luis-concept-patterns.md) per migliorare l'accuratezza della stima per espressioni che rivela un criterio in ordine di word e la scelta delle parole. Modelli di utilizzano specifici [sintassi](luis-concept-patterns.md#pattern-syntax) per indicare la posizione del: [entità](luis-concept-entity-types.md), entità [ruoli](luis-concept-roles.md)e il testo facoltativo.

## <a name="add-template-utterance-to-create-pattern"></a>Aggiungere l'espressione di modello per creare un criterio
1. Apri l'applicazione selezionando il corrispettivo nome nella pagina **App personali** e quindi seleziona **Criteri** nel riquadro sinistro, nella sezione **Migliora le prestazioni dell'applicazione**.

    ![Schermata dell'elenco dei criteri](./media/luis-how-to-model-intent-pattern/patterns-1.png)

2. Seleziona la finalità corretta per il criterio. 

    ![Selezionare la finalità](./media/luis-how-to-model-intent-pattern/patterns-2.png)

3. Nella casella di testo del modello, digita l'espressione del modello e premi Invio. Quando vuoi inserire il nome dell'entità, usa la sintassi dell'entità corretta del criterio. Inizia la sintassi dell'entità con `{`. Viene visualizzato l'elenco di entità. Seleziona l'entità corretta, quindi premi Invio. 

    ![Schermata dell'entità per il criterio](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Se l'entità include un [ruolo](luis-concept-roles.md), indicare il ruolo con un unico carattere due punti `:`, dopo il nome dell'entità, ad esempio `{Location:Origin}`. I ruoli per le entità vengono visualizzati in un elenco. Seleziona il ruolo, quindi fai clic su Invio. 

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

## <a name="use-contextual-toolbar"></a>Usare contestuale sulla barra degli strumenti

La barra degli strumenti contesto sopra l'elenco di modelli consente di:

* Ricerca dei modelli
* Modificare un criterio
* Riassegnare un criterio singolo a diverse finalità
* Riassegnare diversi criteri a finalità diverse
* Delete-a-singolo-pattern
* Eliminare diversi criteri
* Filtrare l'elenco dei criteri per entità
* Filter-pattern-list-by-intent
* Rimuovere il filtro entità o finalità
* Aggiungi un criterio da un'espressione esistente nella pagina delle entità o finalità

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [creare un modello](luis-tutorial-pattern.md) con un pattern.any e ruoli con un'esercitazione.
* Informazioni su come [eseguire il training](luis-how-to-train.md) dell'app.
