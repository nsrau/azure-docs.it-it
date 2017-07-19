---
title: Aggiungere condizioni e avviare i flussi di lavoro - App per la logica di Azure | Microsoft Docs
description: Controllare l'esecuzione dei flussi di lavoro nelle app per la logica di Azure mediante l'aggiunta di logica condizionale, trigger, azioni e parametri.
author: stepsic-microsoft-com
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: e4e24de4-049a-4b3a-a14c-3bf3163287a8
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/28/2017
ms.author: LADocs; stepsic
ms.translationtype: Human Translation
ms.sourcegitcommit: 7c69630688e4bcd68ab3b4ee6d9fdb0e0c46d04b
ms.openlocfilehash: e632c48ed31e82536db55a9c54438bece0c38fd4
ms.contentlocale: it-it
ms.lasthandoff: 06/24/2017


---
# <a name="use-logic-apps-features"></a>Usare le funzionalità delle app per la logica

In un [argomento precedente](../logic-apps/logic-apps-create-a-logic-app.md) è stata creata la prima app per la logica. Per il controllo del flusso di lavoro delle app per la logica, è possibile specificare percorsi diversi per l'app per la logica da eseguire e la modalità di elaborazione dati in matrici, raccolte e batch. È possibile includere questi elementi nel flusso di lavoro dell'app per la logica:

* Le condizioni e le [istruzioni switch](../logic-apps/logic-apps-switch-case.md) consentono all'app per la logica di eseguire azioni diverse in base al fatto che vengano soddisfatte o meno condizioni specifiche.

* I [cicli](../logic-apps/logic-apps-loops-and-scopes.md) consentono di eseguire ripetutamente passaggi dell'app per la logica. Ad esempio, è possibile ripetere azioni su una matrice quando si usa un ciclo **For_each**. Oppure è possibile ripetere azioni fino a quando non viene soddisfatta una condizione se si usa un ciclo **Until**.

* Gli [ambiti](../logic-apps/logic-apps-loops-and-scopes.md) consentono di raggruppare serie di azioni, ad esempio per implementare la gestione delle eccezioni.

* La [scomposizione dei batch](../logic-apps/logic-apps-loops-and-scopes.md) consente all'app per la logica di avviare flussi di lavoro separati per gli elementi di una matrice quando si usa il comando **SplitOn**.

Questo argomento introduce altri concetti per la compilazione dell'app per la logica:

* Visualizzazione Codice per modificare un'app per la logica esistente
* Opzioni per avviare un flusso di lavoro

## <a name="conditions-run-steps-only-after-meeting-a-condition"></a>Condizioni: eseguire i passaggi solo quando è soddisfatta una condizione

Per fare in modo che l'app per la logica esegua i passaggi solo quando i dati soddisfano criteri specifici, è possibile aggiungere una condizione che confronta i dati nel flusso di lavoro in base a campi o valori specifici.

Si supponga, ad esempio, di avere un'app per la logica che invia troppi messaggi di posta elettronica per i post sul feed RSS di un sito Web. È possibile aggiungere una condizione in modo che l'app per la logica invii un messaggio solo quando il nuovo post appartiene a una categoria specifica.

1. Nel [portale di Azure](https://portal.azure.com) individuare e aprire l'app per la logica in Progettazione app per la logica.

2. Aggiungere una condizione al percorso del flusso di lavoro da usare. 

   Per aggiungere la condizione tra i passaggi esistenti nel flusso di lavoro dell'app per la logica, spostare il puntatore sulla freccia dove si vuole aggiungere la condizione. 
   Scegliere il **segno più** (**+**), quindi **Aggiungi una condizione**. ad esempio:

   ![Aggiungere una condizione all'app per la logica](./media/logic-apps-use-logic-app-features/add-condition.png)

   > [!NOTE]
   > Se si vuole aggiungere una condizione alla fine del flusso di lavoro corrente, andare alla fine dell'app per la logica e scegliere **+ Nuovo passaggio**.

3. Ora definire la condizione. Specificare il campo di origine da valutare, l'operazione da eseguire e il valore o il campo di destinazione. Per aggiungere alla condizione campi già esistenti, sceglierli dall'**elenco Aggiungi contenuto dinamico**.

   ad esempio:

   ![Modificare la condizione nella modalità di base](./media/logic-apps-use-logic-app-features/edit-condition-basic-mode.png)

   Questa è la condizione completa:

   ![Condizione completa](./media/logic-apps-use-logic-app-features/edit-condition-basic-mode-2.png)

   > [!TIP]
   > Per definire la condizione nel codice, scegliere **Modifica in modalità avanzata**. ad esempio:
   > 
   > ![Modificare la condizione nel codice](./media/logic-apps-use-logic-app-features/edit-condition-advanced-mode.png)

4. In **SE SÌ** e **SE NO** aggiungere i passaggi da eseguire in base al fatto che la condizione sia soddisfatta o meno.

   ad esempio:

   ![Condizione con percorsi SÌ e NO](./media/logic-apps-use-logic-app-features/condition-yes-no-path.png)

   > [!TIP]
   > È possibile trascinare le azioni esistenti nei percorsi **SE SÌ** e **SE NO**.

5. Al termine, salvare l'app per la logica.

Ora si ricevono messaggi di posta elettronica solo quando i post soddisfano la condizione.

## <a name="repeat-actions-over-a-list-with-foreach"></a>Ripetere l'azione in un elenco con forEach

Il ciclo forEach specifica una matrice per la ripetizione di un'azione. Se non è una matrice, il flusso ha esito negativo. Ad esempio, se action1 genera una matrice di messaggi e si vuole inviare ogni messaggio, è possibile inserire questa istruzione forEach nelle proprietà dell'azione: `forEach : "@action('action1').outputs.messages"`

## <a name="edit-the-code-definition-for-a-logic-app"></a>Modificare la definizione di codice per un'app per la logica

Oltre che nella finestra di progettazione dell'app per la logica, è possibile modificare direttamente il codice che definisce un'app per la logica.

1. Nella barra dei comandi fare clic su **Visualizzazione Codice**.

    Verrà aperto un editor completo che mostra la definizione appena modificata.

    ![Visualizzazione Codice](media/logic-apps-use-logic-app-features/codeview.png)

    Nell'editor di testo, è possibile copiare e incollare un numero qualsiasi di azioni all'interno della stessa app per la logica o tra app per la logica. 
    È anche possibile aggiungere o rimuovere facilmente intere sezioni dalla definizione, oltre a condividere definizioni con altri.

2. Per salvare le modifiche, scegliere **Salva**.

## <a name="parameters"></a>Parametri

Alcune funzionalità delle app per la logica sono disponibili solo nella visualizzazione codice, ad esempio, i parametri. I parametri semplificano il riutilizzo dei valori all'interno di un'app per la logica. Ad esempio, se si ha un indirizzo e-mail che si vuole usare in diverse azioni, è consigliabile definirlo come parametro.

I parametri costituiscono un buon metodo per estrarre valori che probabilmente verranno modificati molto. Sono particolarmente utili quando è necessario eseguire l'override dei parametri in diversi ambienti. Per informazioni sull'esecuzione dell'override dei parametri in base all'ambiente, vedere l'argomento relativo alla [creazione di definizioni di app per la logica](../logic-apps/logic-apps-author-definitions.md) e la [documentazione dell'API REST](https://docs.microsoft.com/rest/api/logic).

Questo esempio illustra come aggiornare l'app per la logica esistente in modo che sia possibile usare i parametri per il termine della query.

1. Nella visualizzazione codice trovare l'oggetto `parameters : {}` e aggiungere un oggetto `currentFeedUrl`:

        "currentFeedUrl" : {
            "type" : "string",
            "defaultValue" : "http://rss.cnn.com/rss/cnn_topstories.rss"
        }

2. Andare all'azione `When_a_feed-item_is_published`, trovare la sezione `queries` e sostituire il valore della query con `"feedUrl": "#@{parameters('currentFeedUrl')}"`. 

    Per unire due o più stringhe, è inoltre possibile usare la funzione `concat`. 
    Ad esempio, `"@concat('#',parameters('currentFeedUrl'))"` 
    funziona come indicato sopra.

3.  Al termine dell'operazione, scegliere **Salva**. 

    Ora è possibile modificare il feed RSS del sito Web passando un URL diverso attraverso l'oggetto `currentFeedURL`.

Altre informazioni su come [creare definizioni di app per la logica](../logic-apps/logic-apps-author-definitions.md).

## <a name="start-logic-app-workflows"></a>Avviare i flussi di lavoro delle app per la logica

Sono disponibili diverse opzioni per avviare il flusso di lavoro definito nell'app per la logica. Un flusso di lavoro può sempre essere avviato su richiesta nel [Portale di Azure].

### <a name="recurrence-triggers"></a>Trigger di ricorrenza

Un trigger di ricorrenza viene eseguito a un intervallo specificato dall'utente. Quando il trigger è caratterizzato dalla logica condizionale, determina se è necessario eseguire il flusso di lavoro. Un trigger indica che il flusso di lavoro deve essere eseguito restituendo un codice di stato `200` . Quando non è necessaria l'esecuzione del flusso di lavoro, il trigger restituisce un codice di stato `202`.

### <a name="callback-using-rest-apis"></a>Callback tramite le API REST

Per avviare un flusso di lavoro, i servizi possono chiamare un endpoint dell'app per la logica. Per avviare questa tipologia di app per la logica su richiesta, fare clic su **Run now** (Esegui ora) nella barra dei comandi. Vedere [Avviare i flussi di lavoro chiamando endpoint dell'app per la logica come trigger](../logic-apps/logic-apps-http-endpoint.md). 

<!-- Shared links -->
[Portale di Azure]: https://portal.azure.com

## <a name="next-steps"></a>Passaggi successivi

* [Istruzioni switch](../logic-apps/logic-apps-switch-case.md) 
* [Cicli, ambiti e debatching](../logic-apps/logic-apps-loops-and-scopes.md)
* [Creare definizioni di app per la logica](../logic-apps/logic-apps-author-definitions.md)
