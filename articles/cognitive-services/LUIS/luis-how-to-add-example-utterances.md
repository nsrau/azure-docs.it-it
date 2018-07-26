---
title: Aggiungere espressioni di esempio nelle app LUIS | Microsoft Docs
titleSuffix: Azure
description: Informazioni su aggiungere espressioni nelle applicazioni LUIS (Language Understanding).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: diberry
ms.openlocfilehash: cf6fc131a1ae2ba0a77bdbeda942c9f85aadd189
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2018
ms.locfileid: "39226288"
---
# <a name="add-example-utterances-and-label-with-entities"></a>Aggiungere espressioni di esempio ed etichette con entità

Le espressioni di esempio sono campioni di testo con domande o commenti degli utenti. Per l'apprendimento di LUIS (Language Understanding), è necessario aggiungere [espressioni di esempio](luis-concept-utterance.md) a una [finalità](luis-concept-intent.md).

In genere, si aggiunge innanzitutto un'espressione di esempio a una finalità, quindi si creano le entità e si etichettano le espressioni nella pagina della finalità. Se si desidera invece creare prima le entità, vedere [Aggiungere entità](luis-how-to-add-entities.md).

## <a name="add-an-utterance"></a>Aggiungere un'espressione
Nella pagina di una finalità, inserire un'espressione di esempio pertinente che gli utenti potrebbero usare, ad esempio `book 2 adult business tickets to Paris tomorrow on Air France` nella casella di testo sotto al nome della finalità, quindi premere INVIO. 
 
>[!NOTE]
>LUIS converte tutte le espressioni in caratteri minuscoli.

![Schermata della pagina dei dettagli delle finalità, con un'espressione evidenziata](./media/luis-how-to-add-example-utterances/add-new-utterance-to-intent.png) 

Le espressioni vengono aggiunte all'elenco Espressioni per la finalità corrente. 

## <a name="ignoring-words-and-punctuation"></a>Procedura per ignorare parole e punteggiatura
Se si desidera che vengano ignorati parole o segni di punteggiatura specifici nell'espressione di esempio, usare un [criterio](luis-concept-patterns.md#pattern-syntax) con la sintassi _ignore_. 

## <a name="add-simple-entity-label"></a>Aggiungere un'etichetta di entità semplice
Nella procedura seguente, si creano ed etichettano le entità personalizzate all'interno dell'espressione seguente nella pagina della finalità:

```
book me 2 adult business tickets to Paris tomorrow on Air France
```

1. Selezionare "Air France" nell'espressione per etichettarla come entità semplice.

    > [!NOTE]
    > Quando si selezionano le parole da etichettare come entità:
    > * Per una parola singola, è sufficiente selezionarla. 
    > * Per una serie di due o più parole, selezionare l'inizio e la fine della serie.

2. Nella casella di riepilogo a discesa dell'entità che viene visualizzata, è possibile selezionare un'entità esistente o aggiungerne una nuova. Per aggiungere una nuova entità, digitarne il nome nella casella di testo, quindi selezionare **Crea una nuova entità**. 
 
    ![Schermata della pagina dei dettagli delle finalità, con l'opzione di etichettatura delle entità evidenziata](./media/luis-how-to-add-example-utterances/create-airline-simple-entity.png)

3. Nella finestra di dialogo popup **Che tipo di entità vuoi creare?**, verificare il nome dell'entità e selezionare il tipo di entità semplice, quindi selezionare **Fine**.

    ![Immagine della finestra di dialogo di conferma](./media/luis-how-to-add-example-utterances/create-simple-airline-entity.png)

    Per altre informazioni sull'estrazione dell'entità semplice dalla risposta alla query JSON sull'endpoint, vedere [Estrazione di dati](luis-concept-data-extraction.md#simple-entity-data). Per altre informazioni sull'uso delle entità semplici, seguire la relativa [guida introduttiva](luis-quickstart-primary-and-secondary-data.md).


## <a name="add-list-entity-and-label"></a>Aggiungere un'entità e un'etichetta elenco
Le entità elenco rappresentano un set (corrispondenze testuali esatte) chiuso e fisso di parole correlate nel sistema. 

Per un'entità di elenco di una serie di bevande, è possibile avere due valori normalizzati: acqua e bibita. Ciascun nome normalizzato ha dei sinonimi. I sinonimi vengono indicati in inglese. Per l'acqua, i sinonimi sono H20, gas, flat (liscia). Per bibita, i sinonimi sono fruit (fruit), cola, ginger. Non è necessario conoscere tutti i valori quando si crea l'entità. È possibile aggiungerne altri dopo aver esaminato le espressioni reali degli utenti con i sinonimi.

|Nome normalizzato|Sinonimi|
|--|--|
|Acqua|H20, gas, flat|
|Bevanda|Fruit, cola, ginger|

Quando si crea una nuova entità elenco dalla pagina delle finalità, si eseguono due operazioni che potrebbero non essere ovvie. In primo luogo, si crea un nuovo elenco aggiungendo il primo elemento dell'elenco. In secondo luogo, il primo elemento dell'elenco è denominato con la parola o frase selezionata dall'espressione. È possibile modificare questi elementi in un secondo momento nella pagina dell'entità, ma si fa prima a selezionare un'espressione che contenga la parola desiderata per il nome dell'elemento dell'elenco.

Ad esempio, se si desidera creare un elenco di tipi di bevande e si seleziona la parola `h2o` dall'espressione per creare l'entità, l'elenco avrebbe un elemento con il nome h20. Se si desidera usare un nome più generico, è sufficiente scegliere un'espressione che lo contenga. 

1. Nell'espressione, selezionare la parola che compare come primo elemento nell'elenco, quindi inserire il nome dell'elenco nella casella di testo e selezionare **Crea una nuova entità**.   

    ![Screenshot della pagina dei dettagli delle finalità, con il pulsante per la creazione di una nuova entità evidenziato](./media/luis-how-to-add-example-utterances/create-drink-list-entity.png)

2. Nella finestra di dialogo **Che tipo di entità vuoi creare?** finestra di dialogo, aggiungere sinonimi di questa voce dell'elenco. Per l'elemento acqua in un elenco di bevande, aggiungere `h20`, `perrier` e `waters`, quindi selezionare **Fine**. Si noti che "waters" (acque) viene aggiunto perché i sinonimi dell'elenco vengono associati a livello di token. Nella cultura inglese si usa invece il livello di parola, per cui "waters" non verrebbe associata a "water" (acqua), a meno che non sia presente nell'elenco. 

    ![Schermata della finestra di dialogo Che tipo di entità vuoi creare?](./media/luis-how-to-add-example-utterances/drink-list-ddl.png)

    Questo elenco di bevande ha un solo tipo, l'acqua. È possibile aggiungere più tipi di bevande etichettando altre espressioni o modificando l'entità dal menu **Entità** nel riquadro di spostamento sinistro. Con la [modifica](luis-how-to-add-entities.md#add-list-entities) delle entità è possibile aggiungere altri elementi con sinonimi corrispondenti o [importare](luis-how-to-add-entities.md#import-list-entity-values) un elenco. 

    Per altre informazioni sull'estrazione di entità elenco dalla risposta alla query JSON sull'endpoint, vedere [Estrazione di dati](luis-concept-data-extraction.md#list-entity-data). Per altre informazioni sull'uso di un'entità elenco, seguire la [guida introduttiva](luis-quickstart-intent-and-list-entity.md).

## <a name="add-synonyms-to-the-list-entity"></a>Aggiungere sinonimi all'entità elenco 
Aggiungere un sinonimo all'entità elenco selezionando la parola o la frase nell'espressione. Se si dispone di un'entità elenco Bevanda e si desidera aggiungere `agua` come sinonimo per "water" (acqua), seguire questi passaggi:

Nell'espressione, selezionare il sinonimo, ad esempio `aqua` per "water", quindi selezionare il nome dell'entità elenco nell'elenco a discesa, ad esempio **Bevanda**, e selezionare **Imposta come sinonimo**. A questo punto, selezionare l'elemento sinonimo nell'elenco, ad esempio **water**.

![Screenshot della pagina dei dettagli delle finalità, con il pulsante per la creazione di un nuovo sinonimo evidenziato](./media/luis-how-to-add-example-utterances/set-agua-as-synonym.png)

## <a name="create-new-item-for-list-entity"></a>Creare un nuovo elemento per l'entità elenco
Creare un nuovo elemento per un'entità elenco esistente selezionando la parola o la frase nell'espressione. Se si dispone di un elenco Bevanda e si desidera aggiungere `tea` come nuovo elemento, seguire questi passaggi:

Nell'espressione, selezionare la parola per il nuovo elemento dell'elenco, ad esempio `tea`, quindi selezionare il nome dell'entità elenco nell'elenco a discesa, ad esempio **Bevanda**. A questo punto, selezionare **Crea un nuovo sinonimo**. 

![Schermata di aggiunta di un nuovo elemento dell'elenco](./media/luis-how-to-add-example-utterances/list-entity-create-new-item.png)

La parola è ora evidenziata in blu. Se si passa il mouse sulla parola, viene visualizzato un tag che mostra il nome dell'elemento dell'elenco, ad esempio "tea" (tè).

![Screenshot del tag del nuovo elemento dell'elenco](./media/luis-how-to-add-example-utterances/list-entity-item-name-tag.png)

## <a name="wrap-entities-in-composite-label"></a>Eseguire il wrapping delle entità in un'etichetta composita
Le entità composite vengono create nella pagina **Entità** e non è possibile crearle nella pagina delle finalità. Una volta creata l'entità composita, è possibile eseguire il wrapping delle entità in un'espressione nella pagina delle finalità. 

Presupponendo l'espressione `book 2 tickets from Seattle to Cairo`, un'espressione composita può restituire informazioni di entità sul conteggio dei biglietti (2), l'origine (Seattle) e la destinazione (Il Cairo) in una singola entità padre. 

Seguire questi [passaggi](luis-how-to-add-entities.md#add-prebuilt-entity) per aggiungere l'entità predefinita **numero**. Dopo aver creato l'entità, `2` nell'espressione è blu, indicando che si tratta di un'entità con etichette. Le entità predefinite sono etichettate da LUIS. È impossibile aggiungere o rimuovere l'etichetta di un'entità predefinita da una singola espressione. È possibile solo aggiungere o rimuovere tutte le etichette predefinite aggiungendo o rimuovendo l'entità predefinita dall'applicazione.

Seguire questi [passaggi](#add-hierarchical-entity-and-label) per creare un'entità gerarchica **Location**. Etichettare l'origine e la destinazione nell'espressione di esempio. 

Prima di eseguire il wrapping delle entità in un'entità composita, assicurarsi che tutte le entità figlie siano evidenziate in blu, ovvero che siano state etichettate nell'espressione.

1. Per eseguire il wrapping di singole entità in una composito, selezionare la prima entità con etichetta nell'espressione per l'entità composita. Nell'espressione di esempio `book 2 tickets from Seattle to Cairo`, la prima entità è il numero 2. Viene visualizzato un elenco di riepilogo a discesa che mostra le opzioni disponibili per questa selezione.

    ![Screenshot del numero selezionato con le opzioni dell'elenco a discesa evidenziate](./media/luis-how-to-add-example-utterances/wrap-1.png)

2. Selezionare **Wrap in composite entity** (Esegui wrapping nell'entità composita) dall'elenco a discesa. 

    ![Schermata delle opzioni dell'elenco a discesa per il wrapping nelle entità composite con l'opzione corrispondente evidenziata](./media/luis-how-to-add-example-utterances/wrap-2.png)

3. Selezionare l'ultima parola dell'entità composita. Nell'espressione di questo esempio, selezionare "Location::Destination" (Ubicazione::Destinazione, ovvero il Cairo). La linea verde si trova ora sotto tutte le parole composite dell'espressione, incluse quelle che non rappresentano entità.

    ![Schermata della pagina della finalità BookFlight con il numero evidenziato](./media/luis-how-to-add-example-utterances/wrap-composite.png)

4. Selezionare il nome dell'entità composita dall'elenco a discesa. In questo esempio si tratta di **TicketOrder**.

    ![Schermata di wrapping delle parole con l'entità composita, con il nome di quest'ultima evidenziato nell'elenco a discesa](./media/luis-how-to-add-example-utterances/wrap-4.png)

    Quando si esegue correttamente il wrapping delle entità, una linea verde viene aggiunta sotto l'intera frase.

    ![Screenshot dell'espressione con l'entità composita evidenziata](./media/luis-how-to-add-example-utterances/wrap-5.png)

    Per altre informazioni sull'estrazione dell'entità composita dalla risposta alla query JSON sull'endpoint, vedere [Estrazione di dati](luis-concept-data-extraction.md#composite-entity-data). Per altre informazioni sull'uso di un'entità composita, seguire la relativa [esercitazione](luis-tutorial-composite-entity.md).

## <a name="add-hierarchical-entity-and-label"></a>Aggiungere un'entità e un'etichetta gerarchiche
Un'entità gerarchica è una categoria di entità con apprendimento su base contestuale e correlate per concetto. Nell'esempio seguente, l'entità contiene ubicazioni di origine e destinazione. 

Nell'emissione `Book 2 tickets from Seattle to Cairo`, Seattle è l'origine, mentre Il Cairo è la destinazione. Ogni ubicazione è diversa a livello di contesto e viene appresa dall'ordine e dalla scelta delle parole nell'espressione.

1. Nella pagina delle finalità, selezionare "Seattle" nell'espressione, quindi inserire il nome dell'entità e selezionare **Crea una nuova entità**.

    ![Screenshot della finestra di dialogo per la creazione di etichette gerarchiche di un'entità](./media/luis-how-to-add-example-utterances/create-hier-ent-1.png)

2. Nella finestra di dialogo popup, selezionare il **tipo di entità** gerarchico, quindi aggiungere `Origin` e `Destination` come elementi figlio e selezionare **Fine**.

    ![Screenshot della pagina dei dettagli delle finalità, con l'entità ToLocation evidenziata](./media/luis-how-to-add-example-utterances/create-location-hierarchical-entity.png)

3. La parola nell'espressione viene etichettata con l'entità gerarchica padre. È necessario assegnare la parola a un'entità figlio. Tornare all'espressione nella pagina delle finalità. Selezionare la parola quindi nell'elenco a discesa scegliere il nome dell'entità creata e seguire il menu a destra per scegliere l'entità figlio corretta.

    ![Screenshot della pagina dei dettagli delle finalità, con l'entità ToLocation evidenziata](./media/luis-how-to-add-example-utterances/label-tolocation.png)

    >[!CAUTION]
    >I nomi delle entità figlio devono essere univoci per tutte le entità in una singola app. Due diverse entità gerarchiche non possono contenere entità figlio con lo stesso nome. 

    Per altre informazioni sull'estrazione dell'entità gerarchica dalla risposta alla query JSON sull'endpoint, vedere [Estrazione di dati](luis-concept-data-extraction.md#hierarchical-entity-data). Per altre informazioni sull'uso delle entità gerarchiche, seguire la relativa [guida introduttiva](luis-quickstart-intent-and-hier-entity.md).


## <a name="remove-entity-labels-from-utterances"></a>Rimuovere le etichette di entità dalle espressioni
È possibile rimuovere le etichette di entità con apprendimento automatico da un'espressione nella pagina delle finalità. Se l'entità non è stata appresa automaticamente, non potrà essere rimossa da un'espressione. Se si desidera rimuovere un'entità non appresa automaticamente dall'espressione, sarà necessario eliminare l'entità dall'intera app. 

Per rimuovere l'etichetta di un'entità appresa automaticamente da un'espressione, selezionare l'entità nell'espressione. Quindi selezionare **Rimuovi etichetta** nella casella di riepilogo a discesa dell'entità che viene visualizzata.

![Schermata della pagina dei dettagli delle finalità, con l'opzione Rimuovi etichetta evidenziata](./media/luis-how-to-add-example-utterances/remove-label.png) 

## <a name="add-prebuilt-entity-label"></a>Aggiungere un'etichetta di un'entità predefinita
Se si aggiungono le entità predefinite all'app LUIS, non sarà necessario etichettare le espressioni con tali entità. Per altre informazioni sulle entità predefinite e su come aggiungerle, vedere [Aggiungere entità](luis-how-to-add-entities.md#add-prebuilt-entity).

## <a name="add-regular-expression-entity-label"></a>Aggiungere un'etichetta di un'entità di espressione regolare
Se si aggiungono le entità di un'espressione regolare all'app LUIS, non sarà necessario etichettare le espressioni con tali entità. Per altre informazioni sulle entità di espressioni regolari e su come aggiungerle, vedere [Aggiungere entità](luis-how-to-add-entities.md#add-regular-expression-entities).

## <a name="create-a-pattern-from-an-utterance"></a>Creare un criterio a partire da un'espressione
Vedi [Aggiungi un criterio da un'espressione esistente nella pagina delle entità o finalità](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).

## <a name="add-patternany-entity-label"></a>Aggiungere l'etichetta di un'entità pattern.any
Se si aggiungono le entità pattern.any all'app LUIS, non sarà possibile etichettare le espressioni con tali entità. Esse sono valide solo nei modelli. Per altre informazioni sulle entità pattern.any e su come aggiungerle, vedere [Aggiungere entità](luis-how-to-add-entities.md#add-patternany-entities).

<!--
Fix this - moved to luis-how-to-add-intents.md - how ?

## Search in utterances
## Prediction discrepancy errors
## Filter by intent prediction discrepancy errors
## Filter by entity type
## Switch to token view
## Delete utterances
## Edit an utterance
## Reassign utterances

-->
## <a name="train-your-app-after-changing-model-with-utterances"></a>Eseguire il training dell'app dopo la modifica del modello con le espressioni
Dopo aver aggiunto, modificato o rimosso le espressioni, [eseguire il training](luis-how-to-train.md) e [pubblicare](luis-how-to-publish-app.md) l'app affinché le modifiche siano attive per le query di endpoint. 

## <a name="next-steps"></a>Passaggi successivi

Dopo l'aggiunta di etichette alle espressioni nelle finalità, è ora possibile creare un'[entità composita](luis-how-to-add-entities.md).
