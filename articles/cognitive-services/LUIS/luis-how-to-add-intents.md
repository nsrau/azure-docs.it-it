---
title: Pianificare le finalità nelle applicazioni LUIS | Microsoft Docs
description: Usare Language Understanding (LUIS) per aggiungere le finalità che consentono alle app di comprendere le richieste degli utenti e di risolverle in modo opportuno.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.service: cognitive-services
ms.openlocfilehash: f004a0a1726ca6c513e2cb4c137f82ae40e31a01
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2018
ms.locfileid: "37344328"
---
# <a name="manage-intents"></a>Gestire le finalità 
Aggiungi le [finalità](luis-concept-intent.md) all'app LUIS per identificare gruppi di domande o comandi con gli stessi scopi. 

È possibile aggiungere e gestire le finalità dalla pagina **Finalità**, disponibile in **Finalità** nel riquadro sinistro di LUIS. 

La procedura seguente illustra come aggiungere la finalità "Bookflight" all'app TravelAgent.

## <a name="add-intent"></a>Aggiungere le finalità

1. Apri l'app (ad esempio, TravelAgent), facendo clic sul relativo nome nella pagina **Le mie app** e selezionando **Finalità** nel riquadro sinistro. 
2. Nella pagina **Finalità** seleziona **Crea nuova finalità**.

    ![Elenco di finalità](./media/luis-how-to-add-intents/IntentsList.png)
3. Nella finestra di dialogo **Crea nuova finalità**, inserisci il nome delle finalità "BookFlight", quindi fai clic su **Fine**.

    ![Aggiungere le finalità](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    Nella pagina dei dettagli delle finalità appena aggiunte [aggiungi espressioni](#add-an-utterance-on-intent-page).

## <a name="rename-intent"></a>Rinominare le finalità

1. Dalla pagina **Finalità** fai clic sull'icona di ridenominazione ![Rinomina finalità](./media/luis-how-to-add-intents/Rename-Intent-btn.png) accanto al nome della finalità. 

2. Dalla pagina **Finalità** è possibile visualizzare il nome attuale della finalità in una finestra di dialogo. Modifica il nome della finalità e premi Invio. Il nuovo nome viene salvato e visualizzato nella pagina delle finalità.

    ![Modificare la finalità](./media/luis-how-to-add-intents/EditIntent-dialogbox.png)

## <a name="delete-intent"></a>Eliminare le finalità
Quando si elimina una finalità diversa da Nessuna, è possibile aggiungere tutte le espressioni alla finalità Nessuna, qualora sia necessario spostare le espressioni e non eliminarle.   

1. Nella pagina **Finalità** fai clic sul pulsante **Elimina finalità** alla destra del nome della finalità. 

    ![Pulsante Elimina finalità](./media/luis-how-to-add-intents/DeleteIntent.png)

2. Fai clic sul pulsante "Ok" nella finestra di dialogo di conferma.

<!--
    TBD: waiting for confirmation about which delete dialog is going to be in //BUILD

    ![Delete Intent Dialog](./media/luis-how-to-add-intents/DeleteIntent-Confirmation.png)
-->


## <a name="add-an-utterance-on-intent-page"></a>Aggiungere un'espressione nella pagina delle finalità

Nella pagina delle finalità, inserisci un'espressione pertinente che gli utenti potrebbero usare, ad esempio `book 2 adult business tickets to Paris tomorrow on Air France` nella casella di testo sotto al nome della finalità, quindi premi Invio. 
 
>[!NOTE]
>LUIS converte tutte le espressioni in caratteri minuscoli.

![Schermata della pagina dei dettagli delle finalità, con un'espressione evidenziata](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

Le espressioni vengono aggiunte all'elenco Espressioni per la finalità corrente. Dopo l'aggiunta dell'espressione [assegna un'etichetta a qualsiasi entità](luis-how-to-add-example-utterances.md) all'interno delle espressioni ed [esegui il training](luis-how-to-train.md) dell'app. 

## <a name="create-a-pattern-from-an-utterance"></a>Creare un criterio a partire da un'espressione
Vedi [Aggiungi un criterio da un'espressione esistente nella pagina delle entità o finalità](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).

## <a name="edit-an-utterance-on-intent-page"></a>Modificare un'espressione nella pagina delle finalità

Per modificare un'espressione, seleziona l'icona dei tre punti di sospensione (...) all'estremità destra nella riga di tale espressione, quindi seleziona **Modifica**. Modifica il testo, quindi premi Invio sulla tastiera.

![Schermata della pagina dei dettagli delle finalità, con i tre punti di sospensione](./media/luis-how-to-add-intents/edit-utterance.png) 

## <a name="reassign-utterances-on-intent-page"></a>Riassegnare espressioni nella pagina delle finalità
È possibile modificare la finalità di una o più espressioni riassegnandole a un'altra finalità. 

Per riassegnare una singola espressione a una diversa finalità, all'estremità destra della riga dell'espressione, seleziona il nome corretto della finalità nella colonna **Finalità etichettata**. L'espressione viene rimossa dall'elenco delle espressioni della finalità corrente. 

![Schermata della pagina delle finalità di BookFlight con la finalità dell'espressione nella colonna Finalità etichettata selezionata](./media/luis-how-to-add-intents/reassign-1-utterance.png)

Per modificare la finalità di diverse espressioni, seleziona le caselle di controllo a sinistra dell'espressione, quindi **Riassegna finalità**. Seleziona la finalità corretta dall'elenco.

![Schermata della pagina delle finalità di BookFlight con l'espressione selezionata e il pulsante Riassegna finalità evidenziato](./media/luis-how-to-add-intents/delete-several-utterances.png) 

## <a name="delete-utterances-on-intent-page"></a>Eliminare espressioni nella pagina delle finalità

Per eliminare un'espressione, seleziona l'icona dei tre punti di sospensione (...) all'estremità destra nella riga di tale espressione, quindi seleziona **Elimina**. L'espressione viene rimossa dall'elenco e dall'app LUIS.

![Schermata della pagina dei dettagli delle finalità, con l'opzione Elimina evidenziata](./media/luis-how-to-add-intents/delete-utterance-ddl.png)

Per eliminare diverse espressioni:

1. Seleziona le caselle di controllo a sinistra dell'espressione, quindi **Elimina le espressioni**. 

    ![Schermata della pagina dei dettagli delle finalità, con le espressioni selezionate e il pulsante Elimina le espressioni evidenziato](./media/luis-how-to-add-intents/delete-several-utterances.png)

2. Seleziona **Fine** nella finestra di dialogo dell'elemento popup **Elimina espressioni?**.

## <a name="search-in-utterances-on-intent-page"></a>Eseguire ricerche all'interno delle espressioni nella pagina delle finalità
In una finalità, è possibile ricercare espressioni che contengono testo (parole o frasi). Ad esempio, potresti riscontrare un errore che riguarda una particolare parola e desideri trovare tutti gli esempi che includono tale parola specifica. 

1. Seleziona l'icona della lente di ingrandimento nella barra degli strumenti.

    ![Schermata della pagina delle finalità, con l'icona di ricerca a forma di lente di ingrandimento evidenziata](./media/luis-how-to-add-intents/magnifying-glass.png)

2. Viene visualizzata una casella di testo di ricerca. Digita la parola o frase nella casella di ricerca nell'angolo in alto a destra dell'elenco delle espressioni. L'elenco delle espressioni si aggiorna per consentire solo la visualizzazione delle espressioni che includono il testo da cercare. 

    ![Schermata della pagina delle finalità, con la casella di testo di ricerca evidenziata](./media/luis-how-to-add-intents/search-textbox.png)

    Per annullare la ricerca e ripristinare l'elenco completo delle espressioni, elimina il testo di ricerca digitato. Per chiudere la casella di testo di ricerca, seleziona di nuovo l'icona della lente di ingrandimento nella barra degli strumenti.

## <a name="prediction-discrepancy-errors-on-intent-page"></a>Errori di discrepanza di previsione nella pagina delle finalità
Un'espressione in una finalità potrebbe presentare una discrepanza tra la finalità selezionata e il punteggio della previsione. LUIS indica questa discrepanza con una casella rossa intorno al punteggio. 

![Schermata della pagina delle finalità di BookFlight, con il punteggio di discrepanza di previsione evidenziato](./media/luis-how-to-add-intents/score-discrepancy.png) 

## <a name="filter-by-intent-prediction-discrepancy-errors-on-intent-page"></a>Filtrare per finalità gli errori di discrepanza di previsione nella pagina delle finalità
Per filtrare l'elenco delle espressioni in modo tale da ottenere solo espressioni con una discrepanza di previsione di finalità, attiva o disattiva l'opzione passando da **Mostra tutto** a **Solo errori** nella barra degli strumenti. 

## <a name="filter-by-entity-type-on-intent-page"></a>Filtrare in base al tipo di entità nella pagina delle finalità
Usa l'elenco a discesa **Filtri entità** nella barra degli strumenti per filtrare le espressioni per entità. 

![Schermata della pagina delle finalità, con il filtro del tipo di entità evidenziato](./media/luis-how-to-add-intents/filter-by-entities.png) 

Per rimuovere il filtro, seleziona la casella con il filtro blu con la parola o frase nella barra degli strumenti.  
<!-- TBD: waiting for ux fix - bug in ux of prebuit entity number -- when filtering by it, it doesn't show the list -->

## <a name="switch-to-token-view-on-intent-page"></a>Passare alla visualizzazione token nella pagina delle finalità
Attiva/disattiva **Vista token** per visualizzare i token anziché i nomi dei tipi di entità. Sulla tastiera, è inoltre possibile usare **Control+E** per attivare o disattivare la visualizzazione. 

![Schermata della finalità di BookFlight con il pulsante Vista token evidenziato](./media/luis-how-to-add-intents/toggle-tokens-view.png)

## <a name="train-your-app-after-changing-model-with-intents"></a>Eseguire il training dell'app dopo la modifica del modello con le finalità
Dopo aver aggiunto, modificato o rimosso le finalità, [esegui il training](luis-how-to-train.md) e [pubblica](luis-how-to-publish-app.md) l'app affinché le modifiche siano attive per le query di endpoint. 

## <a name="next-steps"></a>Passaggi successivi

Dopo aver aggiunto le finalità all'app, l'operazione successiva consiste nell'iniziare ad aggiungere [Espressioni di esempio](luis-how-to-add-example-utterances.md) per le finalità aggiunte. 
