---
title: Aggiungere espressioni di esempio in app LUIS
titleSuffix: Azure Cognitive Services
description: Informazioni su aggiungere espressioni nelle applicazioni LUIS (Language Understanding).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 10/24/2018
ms.author: diberry
ms.openlocfilehash: caf8628569481149ee41e8253f4759e15e49150f
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2018
ms.locfileid: "50139124"
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
Does John Smith work in Seattle?
```

1. Selezionare `Seattle` nell'espressione per etichettarla come entità semplice.

    [![Screenshot della selezione del testo nell'espressione per un'entità semplice](./media/luis-how-to-add-example-utterances/hr-create-simple-1.png)](./media/luis-how-to-add-example-utterances/hr-create-simple-1.png)

    > [!NOTE]
    > Quando si selezionano le parole da taggare come entità:
    > * Per una parola singola, è sufficiente selezionarla. 
    > * Per una serie di due o più parole, selezionare l'inizio e la fine della serie.

1. Nella casella di riepilogo a discesa dell'entità che viene visualizzata, è possibile selezionare un'entità esistente o aggiungerne una nuova. Per aggiungere una nuova entità, digitarne il nome nella casella di testo, quindi selezionare **Crea una nuova entità**. 

    ![Screenshot di immissione del nome dell'entità](./media/luis-how-to-add-example-utterances/hr-create-simple-2.png)

1. Nella finestra di dialogo popup **What type of entity do you want to create?** (Che tipo di entità vuoi creare?) verificare il nome dell'entità e selezionare il tipo di entità **Semplice**, quindi selezionare **Done** (Chiudi).

    Un [elenco di frasi](luis-concept-feature.md) viene in genere usato per aumentare il segnale di un'entità semplice.

## <a name="add-a-list-entity"></a>Aggiungere un'entità elenco

Le entità elenco rappresentano un set (corrispondenze testuali esatte) chiuso e fisso di parole correlate nel sistema. 

Per un elenco dei reparti aziendali è possibile avere valori normalizzati: `Accounting` e `Human Resources`. Ciascun nome normalizzato ha dei sinonimi. I sinonimi vengono indicati in inglese. Per quanto riguarda un reparto, questi sinonimi possono includere qualsiasi acronimo, numero o gergo relativo al reparto. Non è necessario conoscere tutti i valori quando si crea l'entità. È possibile aggiungerne altri dopo aver esaminato le espressioni reali degli utenti con i sinonimi.

1. Nell'elenco di espressioni di esempio per un'espressione specifica selezionare la parola o la frase che si desidera nel nuovo elenco. Immettere il nome dell'elenco nella casella di testo in alto, quindi selezionare **Crea nuova entità**.   

    ![Screenshot di immissione del nome dell'entità elenco](./media/luis-how-to-add-example-utterances/hr-create-list-1.png)


1. Nella casella popup **What type of entity do you want to create?** (Che tipo di entità vuoi creare?) denominare l'entità e selezionare **List** (Elenco) come tipo. Aggiungere i sinonimi di questo elemento elenco, quindi selezionare **Done** (Chiudi). 

    ![Screenshot di immissione dei sinonimi dell'entità elenco](./media/luis-how-to-add-example-utterances/hr-create-list-2.png)

    È possibile aggiungere più elementi elenco o più sinonimi di elementi etichettando altre espressioni o modificando l'entità da **Entities** (Entità) nel riquadro di spostamento a sinistra. Con la [modifica](luis-how-to-add-entities.md#add-list-entities) delle entità è possibile aggiungere altri elementi con sinonimi corrispondenti o importare un elenco. 

## <a name="add-composite-entity"></a>Aggiungere un'entità composita

Vengono create entità composite da **entità** esistenti in un'entità padre. 

Ipotizzando l'espressione, `Does John Smith work in Seattle?`, un'espressione composita può restituire informazioni sull'entità del nome del dipendente e la posizione in un singolo oggetto padre. 

Il nome del dipendente, John Smith, è un'entità [personName](luis-reference-prebuilt-person.md) precompilata. La posizione, Seattle, è un'entità personalizzata semplice. Dopo aver creato e taggato le due entità in un'espressione di esempio, tali entità possono essere sottoposte a wrapping in un'entità composita. 

1. Per eseguire il wrapping di singole entità in una composita, selezionare la **prima** entità con etichetta (la più a sinistra) nell'espressione per l'entità composita. Viene visualizzato un elenco di riepilogo a discesa che mostra le opzioni disponibili per questa selezione.

1. Selezionare **Wrap in composite entity** (Esegui wrapping nell'entità composita) dall'elenco a discesa. 

    ![Screenshot di selezione "Wrap in composite entity" (Esegui wrapping nell'entità composita)](./media/luis-how-to-add-example-utterances/hr-create-composite-1.png)

1. Selezionare l'ultima parola dell'entità composita (la più a destra). Si noti che una linea verde segue l'entità composita.

1. Inserire il nome dell'entità composita nell'elenco a discesa.

    ![Screenshot di immissione del nome dell'entità](./media/luis-how-to-add-example-utterances/hr-create-composite-2.png)

    Quando si esegue correttamente il wrapping delle entità, una linea verde viene aggiunta sotto l'intera frase.

1. Convalidare i dettagli dell'entità composita nella finestra popup **What type of entity do you want to create?** (Che tipo di entità vuoi creare?) quindi selezionare **Done** (Chiudi).

    ![Screenshot della finestra popup dei dettagli dell'entità](./media/luis-how-to-add-example-utterances/hr-create-composite-3.png)

1. L'entità composita viene visualizzata con entrambe le evidenziazioni blu per le singole entità e una sottolineatura verde per l'intera entità composita. 

    ![Schermata della pagina dei dettagli delle finalità, con l'entità composita](./media/luis-how-to-add-example-utterances/hr-create-composite-4.png)

## <a name="add-hierarchical-entity"></a>Aggiungere un'entità gerarchica

Un'entità gerarchica è una categoria di entità con apprendimento su base contestuale e correlate per concetto. Nell'esempio seguente, l'entità contiene ubicazioni di origine e destinazione. 

Nell'emissione `Move John Smith from Seattle to Cairo`, Seattle è l'origine, mentre Il Cairo è la destinazione. Ogni ubicazione è diversa a livello di contesto e viene appresa dall'ordine e dalla scelta delle parole nell'espressione.

1. Nella pagina delle finalità selezionare `Seattle` nell'espressione, quindi inserire il nome dell'entità `Location` e premere INVIO sulla tastiera.

    ![Screenshot della finestra di dialogo per la creazione di etichette gerarchiche di un'entità](./media/luis-how-to-add-example-utterances/hr-hier-1.png)

1. Nella finestra popup **What type of entity do you want to create?** (Che tipo di entità vuoi creare?) selezionare il _gerarchico_ per **Tipo di entità**, quindi aggiungere `Origin` e `Destination` come elementi figlio e selezionare **Done** (Chiudi).

    ![Screenshot della pagina dei dettagli delle finalità, con l'entità ToLocation evidenziata](./media/luis-how-to-add-example-utterances/create-location-hierarchical-entity.png)

1. La parola nell'espressione viene etichettata con l'entità gerarchica padre. È necessario assegnare la parola a un'entità figlio. Tornare all'espressione nella pagina dei dettagli delle finalità. Selezionare la parola quindi nell'elenco a discesa scegliere il nome dell'entità creata e seguire il menu a destra per scegliere l'entità figlio corretta.

    ![Screenshot della pagina dei dettagli delle finalità, con l'entità ToLocation evidenziata](./media/luis-how-to-add-example-utterances/hr-hier-3.png)

    >[!CAUTION]
    >I nomi delle entità figlio devono essere univoci per tutte le entità in una singola app. Due diverse entità gerarchiche non possono contenere entità figlio con lo stesso nome. 

## <a name="entity-status-predictions"></a>Previsioni dello stato dell'entità

Quando si immette una nuova espressione nel portale LUIS, è possibile che l'espressione contenga errori di previsione dell'entità. Per errore di previsione si intende la differenza tra come un'entità viene etichettata e come viene prevista in LUIS. 

Questa differenza è rappresentata visivamente nel portale LUIS con una sottolineatura rossa nell'espressione. La sottolineatura rossa può essere visualizzata all'interno o all'esterno delle parentesi quadre che racchiudono l'entità. 

![Screenshot relativo alla discrepanza della previsione dello stato dell'entità](./media/luis-how-to-add-example-utterances/entity-prediction-error.png)

Selezionare le parole sottolineate in rosso nell'espressione. 

In caso di discrepanza della previsione la casella dell'entità visualizza un punto esclamativo rosso accanto a **Entity status** (Stato entità). Per visualizzare lo stato dell'entità unitamente alle informazioni sulla differenza tra entità etichettate e previste, selezionare **Entity status** (Stato entità) e quindi l'elemento a destra.

![Screenshot relativo alla discrepanza della previsione dello stato dell'entità](./media/luis-how-to-add-example-utterances/entity-status.png)

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

![Schermata della pagina dei dettagli delle finalità, con l'opzione Rimuovi etichetta evidenziata](./media/luis-how-to-add-example-utterances/remove-label.png) 

### <a name="add-prebuilt-entity-label"></a>Aggiungere un'etichetta di un'entità predefinita

Quando si aggiungono le entità predefinite all'app LUIS, non è necessario taggare le espressioni con tali entità. Per altre informazioni sulle entità predefinite e su come aggiungerle, vedere [Aggiungere entità](luis-how-to-add-entities.md#add-prebuilt-entity).

### <a name="add-regular-expression-entity-label"></a>Aggiungere un'etichetta di un'entità di espressione regolare

Se si aggiungono le entità di un'espressione regolare all'app LUIS, non sarà necessario taggare le espressioni con tali entità. Per altre informazioni sulle entità di espressioni regolari e su come aggiungerle, vedere [Aggiungere entità](luis-how-to-add-entities.md#add-regular-expression-entities).


### <a name="create-a-pattern-from-an-utterance"></a>Creare un criterio a partire da un'espressione

Vedi [Aggiungi un criterio da un'espressione esistente nella pagina delle entità o finalità](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).


### <a name="add-patternany-entity"></a>Aggiungere l'entità pattern.any

Se si aggiungono le entità pattern.any all'app LUIS, non sarà possibile etichettare le espressioni con tali entità. Esse sono valide solo nei modelli. Per altre informazioni sulle entità pattern.any e su come aggiungerle, vedere [Aggiungere entità](luis-how-to-add-entities.md#add-patternany-entities).

## <a name="train-your-app-after-changing-model-with-utterances"></a>Eseguire il training dell'app dopo la modifica del modello con le espressioni

Dopo aver aggiunto, modificato o rimosso le espressioni, [eseguire il training](luis-how-to-train.md) e [pubblicare](luis-how-to-publish-app.md) l'app affinché le modifiche siano attive per le query di endpoint. 

## <a name="next-steps"></a>Passaggi successivi

Dopo l'aggiunta di etichette alle espressioni nelle finalità, è ora possibile creare un'[entità composita](luis-how-to-add-entities.md).
