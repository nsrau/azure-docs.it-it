---
title: Aggiungere finalità - LUISAdd intents - LUIS
titleSuffix: Azure Cognitive Services
description: Aggiungi le finalità all'app LUIS per identificare gruppi di domande o comandi con gli stessi scopi.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: 66a3350dee60772ce706af8995179dcd8c485b64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73904300"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>Aggiungere finalità per determinare l'intenzione delle espressioni dell'utente

Aggiungi le [finalità](luis-concept-intent.md) all'app LUIS per identificare gruppi di domande o comandi con lo stesso scopo. 

Le finalità sono gestite nella sezione **Compilazione** sulla barra di navigazione superiore nel pannello **Finalità** a sinistra. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-intent"></a>Aggiungere le finalità

1. Nel portale di [anteprima LUIS](https://preview.luis.ai)selezionare **Compila** per visualizzare le finalità. 
1. Nella pagina **Intents (Intents(** ) selezionare **Crea (Crea).**
1. Nella finestra di dialogo **Crea nuova finalità** immettere il nome della finalità, ad esempio `ModifyOrder`, e selezionare **Fatto**.

    > [!div class="mx-imgBorder"]
    > ![Aggiungi finalità](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    La finalità richiede espressioni di esempio.

## <a name="add-an-example-utterance"></a>Aggiungere un'espressione di esempio

Le espressioni di esempio sono campioni di testo con domande o commenti degli utenti. Per insegnare la comprensione del linguaggio (LUIS) quando prevedere questa finalità, è necessario aggiungere espressioni di esempio a una finalità. LUIS deve essere compreso tra 15 e 30 espressioni di esempio per iniziare a comprendere la finalità. Non aggiungere espressioni di esempio in blocco. Ogni espressione deve essere scelta con attenzione per il modo in cui è diversa rispetto agli esempi già presenti nella finalità. 

1. Nella pagina dei dettagli della finalità immettere un'espressione `Deliver a large cheese pizza` pertinente prevista dagli utenti, ad esempio nella casella di testo sotto il nome della finalità, quindi premere INVIO.
 
    > [!div class="mx-imgBorder"]
    > ![Schermata della pagina dei dettagli delle finalità, con un'espressione evidenziata](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

    LUIS converte tutte le espressioni in lettere minuscole e aggiunge spazi intorno [ai token,](luis-language-support.md#tokenization) ad esempio trattini.

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>Errori di stima dell'intenzioneIntent prediction errors 

Un'espressione di esempio in una finalità potrebbe avere un errore di stima della finalità tra la finalità in cui si trova attualmente l'espressione di esempio e la finalità determinata durante il training. 

Per trovare gli errori di stima dell'espressione e correggerli, utilizzare le opzioni **di filtro** di Non corretto e Non chiaro combinato con l'opzione **Visualizza** della **visualizzazione dettagliata**. 

![Per trovare gli errori di stima dell'espressione e correggerli, usare l'opzione Filtro.To find utterance prediction errors and fix them, use the Filter option.](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

Quando vengono applicati i filtri e la visualizzazione e sono presenti espressioni di esempio con errori, l'elenco delle espressioni di esempio mostra le espressioni e i problemi.

> [!div class="mx-imgBorder"]
> ![! [Quando vengono applicati i filtri e la visualizzazione e sono presenti espressioni di esempio con errori, l'elenco delle espressioni di esempio mostra le espressioni e i problemi.] (./media/luis-how-to-add-intents/find-errors-in-utterances.png)](./media/luis-how-to-add-intents/find-errors-in-utterances.png#lightbox)

Ogni riga mostra il punteggio di stima dell'allenamento corrente per l'espressione di esempio, il punteggio del rivale più vicino, che è la differenza in questi due punteggi. 

### <a name="fixing-intents"></a>Finalità di fissaggio

Per informazioni su come correggere gli errori di stima delle finalità, usare il [dashboard di riepilogo](luis-how-to-use-dashboard.md). Il dashboard di riepilogo fornisce l'analisi per l'ultimo training della versione attiva e offre i suggerimenti principali per correggere il modello.  

## <a name="using-the-contextual-toolbar"></a>Usare la barra degli strumenti contestuale

La barra degli strumenti contestuale fornisce altre azioni:

* Modificare o eliminare espressioni di esempio
* Riassegnare l'espressione di esempio a una finalità diversaReassign example utterance to a different intent
* Filtri e visualizzazioni: mostra solo le espressioni contenenti entità filtrate o visualizza i dettagli facoltativi
* Ricerca nelle espressioni di esempio

## <a name="train-your-app-after-changing-model-with-intents"></a>Eseguire il training dell'app dopo la modifica del modello con le finalità

Dopo aver aggiunto, modificato o rimosso le finalità, [eseguire il training](luis-how-to-train.md) e [pubblicare](luis-how-to-publish-app.md) l'app per applicare le modifiche alle query di endpoint. Non allenarti dopo ogni singolo cambiamento. Allenati dopo un gruppo di cambiamenti. 

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull'aggiunta di [espressioni di esempio](luis-how-to-add-example-utterances.md) con entità. 
