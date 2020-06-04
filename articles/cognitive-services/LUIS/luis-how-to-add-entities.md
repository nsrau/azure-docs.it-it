---
title: Aggiungere entità-LUIS
description: Creare entità per estrarre i dati chiave da espressioni utente nelle app Language Understanding (LUIS). I dati di entità estratti vengono usati dall'applicazione client per rispettasse le richieste dei clienti.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.author: diberry
ms.openlocfilehash: 61e53e6110e545d253dae81e94f8738ee17c4141
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84344476"
---
# <a name="add-entities-to-extract-data"></a>Aggiungere entità per estrarre i dati

Creare entità per estrarre i dati chiave da espressioni utente nelle app Language Understanding (LUIS). I dati di entità estratti vengono usati dall'applicazione client per rispettasse le richieste dei clienti.

L'entità rappresenta una parola o una frase all'interno dell'espressione che si intende estrarre. Le entità descrivono le informazioni rilevanti per la finalità e sono talvolta essenziali affinché l'app svolga la sua attività. È possibile creare entità quando si aggiunge un enunciato di esempio a un preventivo o a parte (prima o dopo) aggiungendo un enunciato di esempio a uno scopo.

## <a name="plan-entities-then-create-and-label"></a>Pianificare entità, quindi creare ed etichettare

le entità di machine learning possono essere create a partire dalle espressioni di esempio o create dalla pagina delle **entità** .

In generale, una procedura consigliata consiste nel dedicare tempo alla pianificazione delle entità prima di creare un'entità di machine learning nel portale. Quindi, creare l'entità Machine Learning dall'espressione di esempio con tutti i dettagli delle sottoentità e delle funzionalità che si conoscono al momento. Nell' [esercitazione sull'entità decomponibile](tutorial-machine-learned-entity.md) viene illustrato come utilizzare questo metodo.

Come parte della pianificazione delle entità, è possibile che siano necessarie entità corrispondenti al testo (ad esempio, entità predefinite, entità di espressioni regolari o entità di elenco). È possibile crearli dalla pagina **entità** prima che vengano etichettati in espressioni di esempio.

Quando si etichetta, è possibile etichettare singole entità, quindi crearle in un'entità di Machine Learning padre. In alternativa, è possibile iniziare con un'entità di Machine Learning padre e decomporre le entità figlio.

> [!TIP]
>Etichettare tutte le parole che possono indicare un'entità, anche se le parole non vengono utilizzate quando vengono estratte nell'applicazione client.

## <a name="when-to-create-an-entity"></a>Quando creare un'entità

Dopo aver pianificato le entità, è necessario creare le entità e le sottoentità di machine learning. Potrebbe essere necessario aggiungere entità predefinite o entità corrispondenti al testo per fornire funzionalità per le entità di machine learning. Queste operazioni devono essere eseguite prima dell'assegnazione di etichette.

Quando si inizia a etichettare espressioni di esempio, è possibile creare entità apprese dal computer o estendere le entità dell'elenco.

Usare la tabella seguente per comprendere dove creare o aggiungere ogni tipo di entità all'app.

|Tipo di entità|Dove creare un'entità nel portale LUIS|
|--|--|
|entità Machine Learning|Entità o dettagli finalità|
|Entità elenco|Entità o dettagli finalità|
|Entità di espressione regolare|Entità|
|Entità pattern.any|Entità|
|Entità predefinita|Entità|
|Entità di dominio predefinita|Entità|

È possibile creare tutte le entità dalla pagina **entità** oppure creare un paio di entità nell'ambito dell'assegnazione di etichette all'entità nell'enunciato di esempio nella pagina dei dettagli dello **scopo** . È possibile _assegnare un'etichetta_ a un'entità solo in un enunciato di esempio dalla pagina dei **dettagli dello scopo** .



## <a name="how-to-create-a-new-custom-entity"></a>Come creare una nuova entità personalizzata

Questo processo funziona per entità, elenco ed entità di espressioni regolari apprese dal computer.

1. Accedere al [portale LUIS](https://www.luis.ai) e quindi selezionare la **sottoscrizione** e la **risorsa di creazione** per vedere le app assegnate a tale risorsa.
1. Per aprire l'app, selezionarne il nome nella pagina **app personali** .
1. Selezionare la pagina **entità** .
1. Selezionare **+ Crea**, quindi selezionare il tipo di entità.
1. Continuare la configurazione dell'entità, quindi selezionare **Crea** al termine dell'operazione.

## <a name="create-a-machine-learned-entity"></a>Creare un'entità Machine Learned

1. Accedere al [portale LUIS](https://www.luis.ai) e quindi selezionare la **sottoscrizione** e la **risorsa di creazione** per vedere le app assegnate a tale risorsa.
1. Per aprire l'app, selezionarne il nome nella pagina **app personali** .
1. Nella sezione **Build** selezionare **entità** nel pannello a sinistra e quindi selezionare **+ Crea**.
1. Nella finestra di dialogo **Crea un tipo di entità** immettere il nome dell'entità e selezionare **computer appreso**, quindi selezionare. Per aggiungere sottoentità, selezionare **Aggiungi struttura**. Selezionare **Crea**.

    > [!div class="mx-imgBorder"]
    > ![Screenshot della creazione di un'entità Machine Learned.](media/add-entities/machine-learned-entity-with-structure.png)

1. In **Aggiungi sottoentità**aggiungere una sottoentità selezionando nella **+** riga entità padre.

    > [!div class="mx-imgBorder"]
    > ![Screenshot dell'aggiunta di sottoentità.](media/add-entities/machine-learned-entity-with-subentities.png)

1. Selezionare **Crea** per completare il processo di creazione.

## <a name="add-a-feature-to-a-machine-learned-entity"></a>Aggiungere una funzionalità a un'entità appresa dal computer

1. Accedere al [portale LUIS](https://www.luis.ai) e quindi selezionare la **sottoscrizione** e la **risorsa di creazione** per vedere le app assegnate a tale risorsa.
1. Per aprire l'app, selezionarne il nome nella pagina **app personali** .
1. Nella sezione **Build** selezionare **entità** nel pannello a sinistra e quindi selezionare l'entità Machine Learned.
1. Aggiungere una funzionalità selezionando **+ Aggiungi funzionalità** nella riga entità o sottoentità.
1. Consente di selezionare tra le entità e gli elenchi di frasi esistenti.
1. Se l'entità deve essere estratta solo se la funzionalità viene trovata, selezionare l'asterisco `*` per la funzionalità.

    > [!div class="mx-imgBorder"]
    > ![Screenshot dell'aggiunta della funzionalità all'entità.](media/add-entities/machine-learned-entity-schema-with-features.png)

## <a name="create-a-regular-expression-entity"></a>Creare un'entità di espressione regolare

1. Accedere al [portale LUIS](https://www.luis.ai) e quindi selezionare la **sottoscrizione** e la **risorsa di creazione** per vedere le app assegnate a tale risorsa.
1. Per aprire l'app, selezionarne il nome nella pagina **app personali** .
1. Nella sezione **Build** selezionare **entità** nel pannello a sinistra e quindi selezionare **+ Crea**.

1. Nella finestra di dialogo **Crea un tipo di entità** immettere il nome dell'entità e selezionare **Regex**, immettere l'espressione regolare nel campo **Regex** e selezionare **Crea**.

    > [!div class="mx-imgBorder"]
    > ![Screenshot della creazione di un'entità di espressione regolare.](media/add-entities/add-regular-expression-entity.png)


<a name="add-list-entities"></a>

## <a name="create-a-list-entity"></a>Creare un'entità elenco

Le entità elenco rappresentano un set fisso e chiuso di parole correlate. Mentre l'autore può modificare l'elenco, LUIS non aumenterà o ridurrà l'elenco. È anche possibile importare in un'entità List esistente usando un [formato Entity. JSON list](reference-entity-list.md#example-json-to-import-into-list-entity).

Nell'elenco seguente vengono illustrati il nome canonico e i sinonimi.

|Nome elemento elenco colori|Colori-sinonimi|
|--|--|
|Rosso|Crimson, Blood, Apple, Fire-Engine|
|Blu|Sky, cobalto|
|Green|Kelly, limone|

Utilizzare la procedura per creare un'entità elenco. Una volta creata l'entità dell'elenco, non è necessario etichettare espressioni di esempio in uno scopo. Gli elementi dell'elenco e i sinonimi vengono confrontati con il testo esatto.
1. Accedere al [portale LUIS](https://www.luis.ai) e quindi selezionare la **sottoscrizione** e la **risorsa di creazione** per vedere le app assegnate a tale risorsa.
1. Per aprire l'app, selezionarne il nome nella pagina **app personali** .
1. Nella sezione **Build** selezionare **entità** nel pannello a sinistra e quindi selezionare **+ Crea**.

1. Nella finestra di dialogo **Crea un tipo di entità** immettere il nome dell'entità, ad esempio `Colors` e l' **elenco**di selezione.
1. Nella finestra di dialogo **Crea entità elenco** , in **Aggiungi nuovo sottoelenco**, immettere il nome dell'elemento di elenco, ad esempio `Green` , quindi aggiungere sinonimi.

    > [!div class="mx-imgBorder"]
    > ![Creare un elenco di colori come entità elenco nella pagina dei dettagli dell'entità.](media/how-to-add-entities/create-list-entity-of-colors.png)

1. Al termine dell'aggiunta di elementi elenco e sinonimi, selezionare **Crea**.

    Al termine di un gruppo di modifiche apportate all'app, ricordarsi di eseguire il **Training** dell'app. Non eseguire il training dell'app dopo una singola modifica.

    > [!NOTE]
    > Questa procedura illustra la creazione e l'assegnazione di etichette a un'entità elenco da un enunciato di esempio nella pagina dei **Dettagli Intent** . È anche possibile creare la stessa entità dalla pagina **entità** .

## <a name="add-a-role-for-an-entity"></a>Aggiungere un ruolo per un'entità

Un ruolo è un sottotipo denominato di un'entità, in base al contesto.

### <a name="add-a-role-to-distinguish-different-contexts"></a>Aggiungere un ruolo per distinguere i diversi contesti

Nell'espressione seguente sono presenti due posizioni e ognuna viene specificata in modo semantico in base alle parole, ad esempio `to` e `from` :

`Pick up the package from Seattle and deliver to New York City.`

In questa procedura aggiungere i `origin` `destination` ruoli e a un'entità geographyV2 predefinita.
1. Accedere al [portale LUIS](https://www.luis.ai) e quindi selezionare la **sottoscrizione** e la **risorsa di creazione** per vedere le app assegnate a tale risorsa.
1. Per aprire l'app, selezionarne il nome nella pagina **app personali** .
1. Nella sezione **Build** (Compila) selezionare **Entities** (Entità) nel pannello a sinistra.

1. Selezionare **+ Aggiungi entità precompilata**. Selezionare **geographyV2** e quindi **fare**clic su fine. Viene aggiunta un'entità predefinita all'app.

    Se si rileva che il criterio, quando include Pattern.any, estrae le entità in modo errato, usare un [elenco esplicito](reference-pattern-syntax.md#explicit-lists) per risolvere il problema.

1. Selezionare l'entità geographyV2 precompilata appena aggiunta dall'elenco di pagine **entità** delle entità.
1. Per aggiungere un nuovo ruolo, selezionare **+** accanto a **nessun ruolo aggiunto**.
1. Nella casella di testo **type Role..** . Immettere il nome del ruolo, `Origin` quindi immettere. Aggiungere un secondo nome ruolo `Destination` , quindi immettere.

    > [!div class="mx-imgBorder"]
    > ![Schermata dell'aggiunta del ruolo Origin all'entità Location](media/how-to-add-entities//add-role-to-prebuilt-geographyv2-entity.png)

    Il ruolo viene aggiunto all'entità precompilata, ma non viene aggiunto a espressioni con tale entità.

### <a name="label-text-with-a-role-in-an-example-utterance"></a>Testo etichetta con un ruolo in un enunciato di esempio

> [!TIP]
> I ruoli possono essere sostituiti con l'assegnazione di etichette alle sottoentità di un'entità di machine learning.

1. Accedere al [portale LUIS](https://www.luis.ai) e quindi selezionare la **sottoscrizione** e la **risorsa di creazione** per vedere le app assegnate a tale risorsa.
1. Per aprire l'app, selezionarne il nome nella pagina **app personali** .
1. Passare alla pagina Dettagli finalità, che contiene espressioni di esempio che usano il ruolo.
1. Per etichettare con il ruolo, selezionare l'etichetta dell'entità (linea continua sotto testo) nell'espressione di esempio, quindi selezionare **Visualizza nel riquadro entità** nell'elenco a discesa.

    > [!div class="mx-imgBorder"]
    > ![Screenshot della selezione della visualizzazione nella tavolozza delle entità](media/add-entities/view-in-entity-pane.png)

    La tavolozza entità si apre a destra.

1. Selezionare l'entità, quindi passare alla parte inferiore della tavolozza e selezionare il ruolo.

    > [!div class="mx-imgBorder"]
    > ![Screenshot della selezione della visualizzazione nella tavolozza delle entità](media/add-entities/select-role-in-entity-palette.png)

<a name="add-pattern-any-entities"></a>
<a name="add-a-patternany-entity"></a>
<a name="create-a-pattern-from-an-utterance"></a>

## <a name="create-a-patternany-entity"></a>Creare un modello. qualsiasi entità

Il **modello. qualsiasi** entità è disponibile solo con i [modelli](luis-how-to-model-intent-pattern.md).


## <a name="do-not-change-entity-type"></a>Non modificare il tipo di entità

LUIS non consente di modificare il tipo di entità perché non conoscere gli elementi da aggiungere o rimuovere per costruire tale entità. Per modificare il tipo, è preferibile creare una nuova entità del tipo corretto con un nome leggermente diverso. Dopo aver creato l'entità, in ogni espressione rimuovere il vecchio nome dell'entità con etichetta e aggiungere quello nuovo. Dopo che tutte le espressioni sono stata nuovamente etichettate, eliminare l'entità precedente.


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Usare modelli predefiniti](howto-add-prebuilt-models.md)

Sono disponibili altre informazioni su:
* Come eseguire il [Training](luis-how-to-train.md)
* Come eseguire il [test](luis-interactive-test.md)
* Come [pubblicare](luis-how-to-publish-app.md)
* Modelli
    * [Concetti](luis-concept-patterns.md)
    * [Sintassi](reference-pattern-syntax.md)
* [Repository GitHub delle entità predefinite](https://github.com/Microsoft/Recognizers-Text)
* [Concetti sull'estrazione dei dati](luis-concept-data-extraction.md)



