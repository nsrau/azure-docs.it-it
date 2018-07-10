---
title: Aggiungere modelli di criteri nelle app LUIS | Microsoft Docs
titleSuffix: Azure
description: Informazioni su come aggiungere i modelli di criteri in Language Understanding (LUIS) per migliorarne la precisione delle previsioni.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/08/2018
ms.author: v-geberr;
ms.openlocfilehash: 9dd23fd5f8a0024f127b71160899d10042037a66
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2018
ms.locfileid: "37346009"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Modalità tramite cui aggiungere Criteri per migliorare la precisione delle previsioni
Dopo che l'app LUIS ha ricevuto le espressioni di endpoint, usa il [concetto](luis-concept-patterns.md) di Criteri per migliorare la precisione della previsione per quelle espressioni che presentano un criterio a livello di ordine delle parole e di scelta lessicale. I criteri usano le [entità](luis-concept-entity-types.md) e i rispettivi ruoli per estrarre i dati tramite la sintassi di un criterio specifico. 

## <a name="add-template-utterance-to-create-pattern"></a>Aggiungere l'espressione di modello per creare un criterio
1. Apri l'applicazione selezionando il corrispettivo nome nella pagina **App personali** e quindi seleziona **Criteri** nel riquadro sinistro, nella sezione **Migliora le prestazioni dell'applicazione**.

    ![Schermata dell'elenco dei criteri](./media/luis-how-to-model-intent-pattern/patterns-1.png)

2. Seleziona la finalità corretta per il criterio. 

    ![Selezionare la finalità](./media/luis-how-to-model-intent-pattern/patterns-2.png)

3. Nella casella di testo del modello, digita l'espressione del modello e premi Invio. Quando vuoi inserire il nome dell'entità, usa la sintassi dell'entità corretta del criterio. Inizia la sintassi dell'entità con `{`. Viene visualizzato l'elenco di entità. Seleziona l'entità corretta, quindi premi Invio. 

    ![Schermata dell'entità per il criterio](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Se l'entità include un ruolo, indica il ruolo con i due punti, `:`, dopo il nome dell'entità, ad esempio `{Location:Origin}`. I ruoli per le entità vengono visualizzati in un elenco. Seleziona il ruolo, quindi fai clic su Invio. 

    ![Schermata dell'entità con un ruolo](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    Dopo aver selezionato l'entità corretta, completa l'inserimento del criterio, quindi premi Invio. Al termine dell'inserimento dei criteri [esegui il training](luis-how-to-train.md) dell'app.

    ![Schermata del criterio inserito con entrambi i tipi di entità](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="search-patterns"></a>Criteri di ricerca
La ricerca consente di trovare i criteri che contengono un dato testo.  

1. Seleziona l'icona della lente di ingrandimento.

    ![Schermata della pagina dei Criteri con l'icona dello strumento di ricerca evidenziato](./media/luis-how-to-model-intent-pattern/search-icon.png)

    Digita il testo di ricerca nella casella di ricerca nell'angolo in alto a destra dell'elenco di criteri e premi Invio. L'elenco di criteri viene aggiornato in modo tale da rendere visualizzabili solo i criteri che includono il testo di ricerca.

    ![Schermata della pagina dei Criteri con il testo di ricerca evidenziato nella casella di ricerca](./media/luis-how-to-model-intent-pattern/search-text.png)

    Per annullare la ricerca e ripristinare l'elenco completo dei criteri, elimina il testo di ricerca digitato.

<!-- TBD: should I be able to click on the magnifying glass again to close the search box? It doesn't reset the list. -->

## <a name="edit-a-pattern"></a>Modificare un criterio
1. Per modificare un criterio, seleziona l'icona dei tre punti di sospensione (...) all'estremità destra nella riga di tale criterio, quindi seleziona **Modifica**. 

    ![Schermata della voce di menu Modifica nella riga del criterio](./media/luis-how-to-model-intent-pattern/patterns-three-dots.png) 

2. Inserisci eventuali modifiche nella casella di testo. Al termine, seleziona Invio. Al termine della modifica dei criteri [esegui il training](luis-how-to-train.md) dell'app.

    ![Schermata della modifica dei criteri](./media/luis-how-to-model-intent-pattern/edit-pattern.png)

## <a name="reassign-individual-pattern-to-different-intent"></a>Riassegnare un criterio singolo a diverse finalità

Per riassegnare un singolo criterio a diverse finalità, seleziona la casella di riepilogo della finalità a destra del testo del criterio e seleziona una finalità diversa.

![Schermata con la riassegnazione di un criterio singolo a diverse finalità](./media/luis-how-to-model-intent-pattern/reassign-individual-pattern.png)

## <a name="reassign-several-patterns-to-different-intent"></a>Riassegnare diversi criteri a finalità diverse

Per riassegnare diversi criteri a diverse finalità, seleziona la casella di controllo a sinistra di ciascun criterio oppure la casella di controllo in alto. L'opzione **Riassegna finalità** viene visualizzata sulla barra degli strumenti. Seleziona la finalità corretta per i criteri. 

![Schermata con la riassegnazione di diversi criteri a diverse finalità](./media/luis-how-to-model-intent-pattern/reassign-many-patterns.png)

## <a name="delete-a-single-pattern"></a>Eliminare un criterio singolo

1. Per eliminare un criterio, seleziona l'icona dei tre punti di sospensione (...) all'estremità destra nella riga di tale criterio, quindi seleziona **Elimina**. 

    ![Schermata dell'espressione Elimina](./media/luis-how-to-model-intent-pattern/patterns-three-dots-ddl.png)

2. Seleziona **Ok** per confermare l'eliminazione.

    ![Schermata di conferma dell'operazione Elimina](./media/luis-how-to-model-intent-pattern/confirm-delete.png)

## <a name="delete-several-patterns"></a>Eliminare diversi criteri

1. Per eliminare diversi criteri, seleziona la casella di controllo a sinistra di ciascun criterio oppure la casella di controllo in alto. L'opzione **Elimina criteri** viene visualizzata sulla barra degli strumenti. Seleziona **Elimina criteri**.  

    ![Schermata dell'eliminazione di diversi criteri](./media/luis-how-to-model-intent-pattern/delete-many-patterns.png)

2. Viene visualizzata la finestra di dialogo di conferma per l'opzione **Elimina criteri**. Fai clic su **Ok** per completare l'eliminazione.

    ![Schermata dell'eliminazione di diversi criteri](./media/luis-how-to-model-intent-pattern/delete-many-patterns-confirmation.png)

## <a name="filter-pattern-list-by-entity"></a>Filtrare l'elenco dei criteri per entità

Per filtrare l'elenco dei criteri per entità specifica, seleziona **Filtri entità** dalla barra degli strumenti sopra i criteri. 

![Schermata dei criteri di filtraggio per entità](./media/luis-how-to-model-intent-pattern/filter-entities-1.png)

Dopo aver applicato il filtro, il nome dell'entità viene visualizzato sotto alla barra degli strumenti. 

## <a name="filter-pattern-list-by-intent"></a>Filtrare l'elenco dei criteri per finalità

Per filtrare l'elenco dei criteri per finalità specifica, seleziona **Filtri finalità** dalla barra degli strumenti sopra i criteri. 

![Schermata dei criteri di filtraggio per finalità](./media/luis-how-to-model-intent-pattern/filter-intents-1.png)

Dopo aver applicato il filtro, il nome della finalità viene visualizzato sotto alla barra degli strumenti. 

## <a name="remove-entity-or-intent-filter"></a>Rimuovere il filtro entità o finalità
Quando l'elenco dei criteri viene filtrato, il nome dell'entità o della finalità viene visualizzato sotto alla barra degli strumenti. Per rimuovere il filtro, seleziona il nome.

![Schermata dei criteri filtrati per entità](./media/luis-how-to-model-intent-pattern/filter-entities-2.png)

Il filtro viene rimosso e vengono visualizzati tutti i criteri. 

## <a name="add-pattern-from-existing-utterance-on-intent-or-entity-page"></a>Aggiungi un criterio da un'espressione esistente nella pagina delle entità o finalità
È possibile creare un criterio da un'espressione esistente in entrambe le pagine **Finalità** oppure **Entità**. Tutti le espressioni in qualsiasi pagina finalità o entità vengono visualizzate in un elenco, la cui colonna a destra fornisce l'accesso alle opzioni a livello di espressione, ad esempio **Modifica**, **Elimina** e **Aggiungi come criterio**.

1. Nella riga selezionata dell'espressione, seleziona i tre puntini di sospensione (...) a destra dell'espressione, quindi **Aggiungi come criterio**.

    [![](./media/luis-how-to-model-intent-pattern/add-pattern-from-utterance.png "Schermata della tabella di espressioni con Aggiungi criterio evidenziato nel menu delle opzioni")](./media/luis-how-to-model-intent-pattern/add-pattern-from-utterance.png)

2. Modifica il criterio in base alle [regole della sintassi](luis-concept-patterns.md#pattern-syntax). Se l'espressione selezionata è etichettata con le entità, queste ultime sono già presenti nel criterio con la sintassi corretta.

    ![Schermata dei criteri filtrati per entità](./media/luis-how-to-model-intent-pattern/confirm-patterns-modal.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>Eseguire il training dell'app dopo la modifica del modello con i criteri
Dopo aver aggiunto, modificato, rimosso o riassegnato un criterio, [esegui il training](luis-how-to-train.md) e [pubblica](luis-how-to-publish-app.md) l'app affinché le modifiche siano attive per le query di endpoint. 

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [compilare un criterio](luis-tutorial-pattern.md) con un pattern.any e con i ruoli.
* Informazioni su come [eseguire il training](luis-how-to-train.md) dell'app.