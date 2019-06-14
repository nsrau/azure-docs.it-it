---
title: Aggiungere espressioni di esempio
titleSuffix: Language Understanding - Azure Cognitive Services
description: Le espressioni di esempio sono campioni di testo con domande o commenti degli utenti. Per l'apprendimento di LUIS (Language Understanding), è necessario aggiungere espressioni di esempio a una finalità.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: badf351f8336e501b3ee1c035fcb389a570750c0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65072844"
---
# <a name="add-an-entity-to-example-utterances"></a>Aggiungere un'entità in espressioni di esempio 

Le espressioni di esempio sono campioni di testo con domande o commenti degli utenti. Per l'apprendimento di LUIS (Language Understanding), è necessario aggiungere [espressioni di esempio](luis-concept-utterance.md) a una [finalità](luis-concept-intent.md).

In genere si aggiunge innanzitutto un'espressione di esempio a una finalità, quindi si creano le entità e si etichettano le espressioni nella pagina della finalità. Se si desidera invece creare prima le entità, vedere [Aggiungere entità](luis-how-to-add-entities.md).

## <a name="marking-entities-in-example-utterances"></a>Contrassegnare le entità in espressioni di esempio

Quando si seleziona il testo nell'espressione di esempio da contrassegnare per un'entità, viene visualizzato un menu a comparsa. Usare questo menu per creare o selezionare un'entità. 

Alcuni tipi di entità, ad esempio le entità predefinite e le entità di espressione regolare, non possono essere taggati nelle espressioni di esempio perché vengono taggati automaticamente. 

## <a name="add-a-simple-entity"></a>Aggiungere un'entità semplice

Nella procedura seguente si crea e tagga un'entità personalizzata all'interno dell'espressione seguente nella pagina della finalità:

```text
Are there any SQL server jobs?
```

1. Selezionare `SQL server` nell'espressione per etichettarla come entità semplice. Nella casella di riepilogo a discesa dell'entità che viene visualizzata, è possibile selezionare un'entità esistente o aggiungerne una nuova. Per aggiungere una nuova entità, digitarne il nome `Job` nella casella di testo e quindi selezionare **Crea nuova entità**.

    ![Screenshot di immissione del nome dell'entità](./media/luis-how-to-add-example-utterances/create-simple-entity.png)

    > [!NOTE]
    > Quando si selezionano le parole da taggare come entità:
    > * Per una parola singola, è sufficiente selezionarla. 
    > * Per una serie di due o più parole, selezionare l'inizio e la fine della serie.

1. Nella finestra di dialogo popup **What type of entity do you want to create?** (Che tipo di entità vuoi creare?) verificare il nome dell'entità e selezionare il tipo di entità **Semplice**, quindi selezionare **Done** (Chiudi).

    Un [elenco di frasi](luis-concept-feature.md) viene in genere usato per aumentare il segnale di un'entità semplice.

## <a name="add-a-list-entity"></a>Aggiungere un'entità elenco

Elenco di entità rappresenta un set di corrispondenze di testo esatta di parole correlate nel sistema. 

Per un elenco dei reparti aziendali è possibile avere valori normalizzati: `Accounting` e `Human Resources`. Ciascun nome normalizzato ha dei sinonimi. I sinonimi vengono indicati in inglese. Per quanto riguarda un reparto, questi sinonimi possono includere qualsiasi acronimo, numero o gergo relativo al reparto. Non è necessario conoscere tutti i valori quando si crea l'entità. È possibile aggiungerne altri dopo aver esaminato le espressioni reali degli utenti con i sinonimi.

1. In un utterance di esempio nel **Intent** pagina, selezionare la parola o frase che desidera che nel nuovo elenco. Quando viene visualizzato l'elenco a discesa entità, immettere il nome per la nuova entità di elenco nella casella di testo superiore e quindi selezionare **creare una nuova entità**.   

1. Nella casella popup **What type of entity do you want to create?** (Che tipo di entità vuoi creare?) denominare l'entità e selezionare **List** (Elenco) come tipo. Aggiungere i sinonimi di questo elemento elenco, quindi selezionare **Done** (Chiudi). 

    ![Screenshot di immissione dei sinonimi dell'entità elenco](./media/luis-how-to-add-example-utterances/hr-create-list-2.png)

    È possibile aggiungere più elementi elenco o più sinonimi di elementi etichettando altre espressioni o modificando l'entità da **Entities** (Entità) nel riquadro di spostamento a sinistra. Con la [modifica](luis-how-to-add-entities.md#add-list-entities) delle entità è possibile aggiungere altri elementi con sinonimi corrispondenti o importare un elenco. 

## <a name="add-composite-entity"></a>Aggiungere un'entità composita

Vengono create entità composite da **entità** esistenti in un'entità padre. 

Supponendo che il utterance `Does John Smith work in Seattle?`, un utterance composito può restituire le informazioni sulle entità del nome del dipendente `John Smith`e il percorso `Seattle` in un'entità composta. Le entità figlio devono esistere già nell'app ed essere contrassegnate in utterance l'esempio prima di creare l'entità composta.

1. Per eseguire il wrapping di entità figlio in un'entità composta, selezionare la **primo** etichettato entity (all'estrema sinistra) nel utterance per l'entità composta. Viene visualizzato un elenco di riepilogo a discesa per visualizzare le opzioni disponibili per questa selezione.

1. Selezionare **eseguire il wrapping in entità composta** nell'elenco a discesa. 

1. Selezionare l'ultima parola dell'entità composita (la più a destra). Si noti che una linea verde segue l'entità composita. Questo è l'indicatore visivo per un'entità composta e deve essere eseguita per tutte le parole nell'entità composta dall'entità figlio a sinistra per l'entità figlio più a destra.

1. Inserire il nome dell'entità composita nell'elenco a discesa.

    Quando si esegue correttamente il wrapping delle entità, una linea verde viene aggiunta sotto l'intera frase.

1. Convalidare i dettagli dell'entità composita nella finestra popup **What type of entity do you want to create?** (Che tipo di entità vuoi creare?) quindi selezionare **Done** (Chiudi).

    ![Screenshot della finestra popup dei dettagli dell'entità](./media/luis-how-to-add-example-utterances/hr-create-composite-3.png)

1. L'entità composita viene visualizzata con entrambe le evidenziazioni blu per le singole entità e una sottolineatura verde per l'intera entità composita. 

    ![Schermata della pagina dei dettagli delle finalità, con l'entità composita](./media/luis-how-to-add-example-utterances/hr-create-composite-4.png)

## <a name="add-entitys-role-to-utterance"></a>Aggiungere il ruolo dell'entità a utterance

Un ruolo è un sottotipo di un'entità, determinato dal contesto del utterance denominato. È possibile contrassegnare un'entità all'interno di un utterance come entità o selezionare un ruolo all'interno di tale entità. Qualsiasi entità possono avere ruoli, inclusi le entità personalizzate che sono Appresa macchina (entità semplice e composta), non sono Appresa macchina (le entità predefinite, le entità di espressione regolare, elenco di entità). 

Scopri [come contrassegnare un utterance con i ruoli delle entità](tutorial-entity-roles.md) da un'esercitazione pratica. 

## <a name="entity-status-predictions"></a>Previsioni dello stato dell'entità

Quando si immette una nuova espressione nel portale LUIS, è possibile che l'espressione contenga errori di previsione dell'entità. Per errore di previsione si intende la differenza tra come un'entità viene etichettata e come viene prevista in LUIS. 

Questa differenza è rappresentata visivamente nel portale LUIS con una sottolineatura rossa nell'espressione. La sottolineatura rossa può essere visualizzata all'interno o all'esterno delle parentesi quadre che racchiudono l'entità. 

![Screenshot relativo alla discrepanza della previsione dello stato dell'entità](./media/luis-how-to-add-example-utterances/entity-prediction-error.png)

Selezionare le parole sottolineate in rosso nell'espressione. 

In caso di discrepanza della previsione la casella dell'entità visualizza un punto esclamativo rosso accanto a **Entity status** (Stato entità). Per visualizzare lo stato dell'entità unitamente alle informazioni sulla differenza tra entità etichettate e previste, selezionare **Entity status** (Stato entità) e quindi l'elemento a destra.

![Selezione dello stato di screenshot di entità](./media/luis-how-to-add-example-utterances/entity-prediction-error-correction.png)

La linea rossa può essere presente nelle circostanze seguenti:

   * Quando un'espressione viene immessa prima che l'entità venga etichettata
   * Quando l'etichetta viene applicata all'entità
   * Quando l'etichetta viene rimossa dall'entità
   * Quando per il testo viene prevista più di un'etichetta di entità 

Le soluzioni seguenti consentono di risolvere la discrepanza nella previsione dell'entità:

|Entità|Indicatore visivo|Previsione|Soluzione|
|--|--|--|--|
|Espressione immessa, ma entità non ancora etichettata.|sottolineatura rossa|Previsione corretta|Etichettare l'entità con il valore previsto.|
|Testo non etichettato|sottolineatura rossa|Previsione errata|Le espressioni correnti che usano questa entità errata devono essere esaminate in tutte le finalità. Le espressioni correnti hanno indicato erroneamente a LUIS che questo testo è l'entità prevista.
|Testo correttamente etichettato|entità con evidenziazione blu, sottolineatura rossa|Previsione errata|Fornire più espressioni con l'entità correttamente etichettata in diverse posizioni e ambiti di utilizzo. Le espressioni correnti non sono sufficienti per insegnare a LUIS che questa è l'entità oppure nel contesto sono presenti entità simili. Le entità simili devono essere combinate in un'unica entità in modo da non confondere LUIS. Un'altra soluzione consiste nell'aggiungere un elenco di frasi per migliorare la significatività delle parole. |
|Testo non correttamente etichettato|entità con evidenziazione blu, sottolineatura rossa|Previsione corretta| Fornire più espressioni con l'entità correttamente etichettata in diverse posizioni e ambiti di utilizzo. 

## <a name="other-actions"></a>Altre azioni

È possibile eseguire azioni su espressioni di esempio come un gruppo selezionato o un singolo elemento. I gruppi di espressioni di esempio selezionati cambiano il menu a comparsa sopra l'elenco. I singoli elementi possono usare sia il menu a comparsa sopra l'elenco sia i singoli puntini di sospensione alla fine di ogni riga di espressione. 

### <a name="remove-entity-labels-from-utterances"></a>Rimuovere le etichette di entità dalle espressioni

È possibile rimuovere le etichette di entità con apprendimento automatico da un'espressione nella pagina delle finalità. Se l'entità non è stata appresa automaticamente, non potrà essere rimossa da un'espressione. Se si desidera rimuovere un'entità non appresa automaticamente dall'espressione, sarà necessario eliminare l'entità dall'intera app. 

Per rimuovere l'etichetta di un'entità appresa automaticamente da un'espressione, selezionare l'entità nell'espressione. Quindi selezionare **Rimuovi etichetta** nella casella di riepilogo a discesa dell'entità che viene visualizzata.

### <a name="add-prebuilt-entity-label"></a>Aggiungere un'etichetta di un'entità predefinita

Quando si aggiungono le entità predefinite all'app LUIS, non è necessario taggare le espressioni con tali entità. Per altre informazioni sulle entità predefinite e su come aggiungerle, vedere [Aggiungere entità](luis-how-to-add-entities.md#add-a-prebuilt-entity-to-your-app).

### <a name="add-regular-expression-entity-label"></a>Aggiungere un'etichetta di un'entità di espressione regolare

Se si aggiungono le entità di un'espressione regolare all'app LUIS, non sarà necessario taggare le espressioni con tali entità. Per altre informazioni sulle entità di espressioni regolari e su come aggiungerle, vedere [Aggiungere entità](luis-how-to-add-entities.md#add-regular-expression-entities-for-highly-structured-concepts).


### <a name="create-a-pattern-from-an-utterance"></a>Creare un criterio a partire da un'espressione

Vedi [Aggiungi un criterio da un'espressione esistente nella pagina delle entità o finalità](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).


### <a name="add-patternany-entity"></a>Aggiungere l'entità pattern.any

Se si aggiungono le entità pattern.any all'app LUIS, non sarà possibile etichettare le espressioni con tali entità. Esse sono valide solo nei modelli. Per altre informazioni sulle entità pattern.any e su come aggiungerle, vedere [Aggiungere entità](luis-how-to-add-entities.md#add-patternany-entities-to-capture-free-form-entities).

## <a name="train-your-app-after-changing-model-with-utterances"></a>Eseguire il training dell'app dopo la modifica del modello con le espressioni

Dopo aver aggiunto, modificato o rimosso le espressioni, [eseguire il training](luis-how-to-train.md) e [pubblicare](luis-how-to-publish-app.md) l'app affinché le modifiche siano attive per le query di endpoint. 

## <a name="next-steps"></a>Passaggi successivi

Dopo l'aggiunta di etichette alle espressioni nelle finalità, è ora possibile creare un'[entità composita](luis-how-to-add-entities.md).
