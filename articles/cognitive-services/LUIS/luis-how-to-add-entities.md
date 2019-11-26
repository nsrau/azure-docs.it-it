---
title: Aggiungere entità-LUIS
titleSuffix: Azure Cognitive Services
description: Creare entità per estrarre i dati chiave da espressioni utente nelle app Language Understanding (LUIS). I dati di entità estratti vengono usati dall'applicazione client per rispettasse le richieste dei clienti.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 1f2b293acdc77e25e6b932c47d466cc28a04a2b6
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383668"
---
# <a name="add-entities-to-extract-data"></a>Aggiungere entità per estrarre i dati 

Creare entità per estrarre i dati chiave da espressioni utente nelle app Language Understanding (LUIS). I dati di entità estratti vengono usati dall'applicazione client per rispettasse le richieste dei clienti.

L'entità rappresenta una parola o una frase all'interno dell'espressione che si intende estrarre. Le entità descrivono le informazioni rilevanti per la finalità e sono talvolta essenziali affinché l'app svolga la sua attività. È possibile creare entità quando si aggiunge un enunciato di esempio a un preventivo o a parte (prima o dopo) aggiungendo un enunciato di esempio a uno scopo.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="plan-entities-then-create-and-label"></a>Pianificare entità, quindi creare ed etichettare

Le entità apprese dal computer possono essere create dalle espressioni di esempio o create dalla pagina **entità** . 

In generale, una procedura consigliata consiste nel dedicare tempo alla pianificazione delle entità prima di creare un'entità appresa dal computer nel portale. Creare quindi l'entità Machine-learned dall'espressione di esempio con tutti i dettagli nei sottocomponenti e nei descrittori e nei vincoli che si conoscono al momento. Nell' [esercitazione sull'entità decomponibile](tutorial-machine-learned-entity.md) viene illustrato come utilizzare questo metodo. 

Come parte della pianificazione delle entità, è possibile che siano necessarie entità corrispondenti al testo (ad esempio, entità predefinite, entità di espressioni regolari o entità di elenco). È possibile crearli dalla pagina **entità** prima che vengano etichettati in espressioni di esempio. 

Quando si etichetta, è possibile etichettare singole entità, quindi creare fino a un'entità padre appresa dal computer. In alternativa, è possibile iniziare con un'entità padre acquisita dal computer e decomporre le entità figlio. 

> [!TIP] 
>Etichettare tutte le parole che possono indicare un'entità, anche se le parole non vengono utilizzate quando vengono estratte nell'applicazione client. 

## <a name="creating-an-entity-before-or-with-labeling"></a>Creazione di un'entità prima o con l'assegnazione di etichette

Usare la tabella seguente per comprendere quali entità devono creare o aggiungere ogni entità all'app. 

|Tipo di entità|Dove creare un'entità nel portale LUIS|
|--|--|
|Entità basata su Machine Learning|Entità o dettagli finalità|
|Entità elenco|Entità o dettagli finalità|
|Entità di espressione regolare|Entità|
|Entità pattern.any|Entità|
|Entità predefinite|Entità|
|Entità di dominio predefinita|Entità|

È possibile creare tutte le entità dalla pagina **entità** oppure creare un paio di entità nell'ambito dell'assegnazione di etichette all'entità nell'enunciato di esempio nella pagina dei dettagli dello **scopo** . È possibile _assegnare un'etichetta_ a un'entità solo in un enunciato di esempio dalla pagina dei **dettagli dello scopo** . 

## <a name="create-a-machine-learned-entity"></a>Creare un'entità Machine-Learned

[!INCLUDE [Create and label entities in machine-learned tutorial](includes/decomposable-tutorial-links.md)]

## <a name="create-a-text-matching-entity"></a>Creare un'entità corrispondente al testo

Usare le entità di corrispondenza testo per estrarre i dati in diversi modi:

|Entità corrispondenti al testo|Scopo|
|--|--|
|[Elenca entità](#add-list-entities-for-exact-matches)|elenco di nomi canonici insieme a sinonimi come form alternativi|
|Entità di espressione regolare|trovare la corrispondenza con il testo usando un'entità di espressione regolare|
|[Entità predefinita](tutorial-machine-learned-entity.md#add-prebuilt-number-to-help-extract-data)|corrispondenza tra tipi di dati comuni, ad esempio numero, posta elettronica, data|
|Entità di dominio predefinita|corrispondenza con i domini oggetto selezionati|
|[Pattern.any](#add-a-patternany-entity)| per individuare le entità che possono essere confuse facilmente con il testo circostante|  

Le entità predefinite funzionano senza fornire dati di training personalizzati. Per le altre entità è necessario fornire i dati di training del cliente (ad esempio, gli elementi dell'entità elenco) o un'espressione (ad esempio, un'espressione regolare o un modello. Any).

<a name="add-list-entities"></a>

### <a name="how-to-create-a-new-custom-entity"></a>Come creare una nuova entità personalizzata

1. Nel portale LUIS, passare alla sezione **Gestisci** e quindi alla pagina **entità** . 
1. Selezionare **+ Crea**, quindi selezionare il tipo di entità. 
1. Continuare la configurazione dell'entità, quindi selezionare **Crea** al termine dell'operazione. 

### <a name="add-list-entities-for-exact-matches"></a>Aggiungere entità elenco per corrispondenze esatte

Le entità elenco rappresentano un set fisso e chiuso di parole correlate. Mentre l'autore può modificare l'elenco, LUIS non aumenterà o ridurrà l'elenco. È anche possibile importare in un'entità List esistente usando un [list Entity. JSON format (Reference-Entity-List. MD # example-JSON-to-Import-into-list-Entity). 

Nell'elenco seguente vengono illustrati il nome canonico e i sinonimi. 

|Nome elemento elenco colori|Colori-sinonimi|
|--|--|
|Rosso|Crimson, Blood, Apple, Fire-Engine|
|Blu|Sky, Azure, Cobalt|
|Verde|Kelly, limone|

Utilizzare la procedura per creare un'entità elenco. Una volta creata l'entità dell'elenco, non è necessario etichettare espressioni di esempio in uno scopo. Gli elementi dell'elenco e i sinonimi vengono confrontati con il testo esatto. 

1. Nella sezione **Build** selezionare **entità** nel pannello a sinistra e quindi selezionare **+ Crea**.

1. Nella finestra di dialogo **Crea un tipo di entità** immettere il nome dell'entità, ad esempio `Colors` e Select **List**.
1. Nella finestra di dialogo **Crea entità elenco** , in **Aggiungi nuovo sottoelenco**, immettere il nome dell'elemento di elenco, ad esempio `Green`, quindi aggiungere sinonimi.

    > [!div class="mx-imgBorder"]
    > ![creare un elenco di colori come entità elenco nella pagina dei dettagli dell'entità.](media/how-to-add-entities/create-list-entity-of-colors.png) 

1. Al termine dell'aggiunta di elementi elenco e sinonimi, selezionare **Crea**.

    Al termine di un gruppo di modifiche apportate all'app, ricordarsi di eseguire il **Training** dell'app. Non eseguire il training dell'app dopo una singola modifica. 

    > [!NOTE]
    > Questa procedura illustra la creazione e l'assegnazione di etichette a un'entità elenco da un enunciato di esempio nella pagina dei **Dettagli Intent** . È anche possibile creare la stessa entità dalla pagina **entità** .

## <a name="add-a-role-for-an-entity"></a>Aggiungere un ruolo per un'entità

Un ruolo è un sottotipo denominato di un'entità, in base al contesto. 

### <a name="add-a-role-to-distinguish-different-contexts"></a>Aggiungere un ruolo per distinguere i diversi contesti

Nell'espressione seguente sono presenti due posizioni e ognuna viene specificata in modo semantico in base alle parole, ad esempio `to` e `from`: 

`Pick up the package from Seattle and deliver to New York City.`

In questa procedura aggiungere `origin` e ruoli di `destination` a un'entità geographyV2 predefinita.

1. Nella sezione **Build** (Compila) selezionare **Entities** (Entità) nel pannello a sinistra.

1. Selezionare **+ Aggiungi entità precompilata**. Selezionare **geographyV2** e quindi **fare**clic su fine. Viene aggiunta un'entità predefinita all'app.
    
    Se si rileva che il criterio, quando include Pattern.any, estrae le entità in modo errato, usare un [elenco esplicito](reference-pattern-syntax.md#explicit-lists) per risolvere il problema. 

1. Selezionare l'entità geographyV2 precompilata appena aggiunta dall'elenco di pagine **entità** delle entità. 
1. Per aggiungere un nuovo ruolo, selezionare **+** accanto a **nessun ruolo aggiunto**.
1. Nella casella di testo **type Role..** . Immettere il nome del ruolo `Origin` quindi INVIO. Aggiungere un secondo nome ruolo di `Destination` quindi immettere. 

    > [!div class="mx-imgBorder"]
    > ![screenshot dell'aggiunta del ruolo Origin all'entità location](media/how-to-add-entities//add-role-to-prebuilt-geographyv2-entity.png)

    Il ruolo viene aggiunto all'entità precompilata, ma non viene aggiunto a espressioni con tale entità. 

### <a name="label-text-with-a-role-in-an-example-utterance"></a>Testo etichetta con un ruolo in un enunciato di esempio

1. Passare alla pagina Dettagli finalità, che contiene espressioni di esempio che usano il ruolo. 
1. Per assegnare un'etichetta con il ruolo, selezionare l'etichetta dell'entità (linea continua sotto testo) nell'espressione di esempio, quindi selezionare **Visualizza in tavolozza entità** nell'elenco a discesa. 

    > [!div class="mx-imgBorder"]
    > ![screenshot della selezione della vista nella tavolozza delle entità](media/how-to-add-entities/select-text-label-with-entity-palette-for-role.png)   

    La tavolozza entità si apre a destra. 

1. Selezionare l'entità, quindi passare alla parte inferiore della tavolozza e selezionare il ruolo. 

    > [!div class="mx-imgBorder"]
    > ![screenshot della selezione della vista nella tavolozza delle entità](media/how-to-add-entities/select-role-from-entity-palette-entity-inspector.png)

<a name="add-pattern-any-entities"></a>

## <a name="add-a-patternany-entity"></a>Aggiungere un pattern. Any (entità)

[Modello. le](luis-concept-entity-types.md) entità sono valide solo nei [modelli](luis-how-to-model-intent-pattern.md), non negli enunciati di esempio. Questo tipo di entità consente a LUIS di trovare la fine di entità di lunghezza e scelta delle parole variabili. Poiché questa entità viene usata in un criterio, LUIS sa dove si trova la fine dell'entità nel modello dell'espressione.

### <a name="steps-to-create-a-patternany-entity"></a>Procedura per creare un modello. qualsiasi entità

1. Nella sezione **Build** selezionare **entità** nel pannello a sinistra e quindi selezionare **+ Crea**.

1. Nella finestra di dialogo **scegliere un tipo di entità** immettere il nome dell'entità nella **casella nome** e selezionare **pattern. any** come **tipo** e quindi selezionare **Crea**.

    Una volta [creato un modello di espressione](luis-how-to-model-intent-pattern.md) usando questa entità, l'entità viene estratta con un algoritmo combinato di ricerca di computer e di corrispondenza del testo. 

### <a name="create-a-pattern-template-utterance-to-use-patternany-entity"></a>Creare un'espressione modello di modello per usare pattern. any Entity

Per usare l'entità pattern.any, aggiungere un criterio nella pagina **Patterns** (Criteri) nella sezione **Improve app performance** (Migliora prestazioni app) con la sintassi con parentesi graffe corretta, ad esempio `Where is **{HumanResourcesFormTitle}** on the server?`.

Se si rileva che il criterio, quando include Pattern.any, estrae le entità in modo errato, usare un [elenco esplicito](reference-pattern-syntax.md#explicit-lists) per risolvere il problema. 

## <a name="do-not-change-entity-type"></a>Non modificare il tipo di entità

LUIS non consente di modificare il tipo di entità perché non conoscere gli elementi da aggiungere o rimuovere per costruire tale entità. Per modificare il tipo, è preferibile creare una nuova entità del tipo corretto con un nome leggermente diverso. Dopo aver creato l'entità, in ogni espressione rimuovere il vecchio nome dell'entità con etichetta e aggiungere quello nuovo. Dopo che tutte le espressioni sono stata nuovamente etichettate, eliminare l'entità precedente. 

<a name="create-a-pattern-from-an-utterance"></a>

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"] 
> [USA modelli predefiniti](howto-add-prebuilt-models.md) 

Altre informazioni su:
* Come eseguire il [Training](luis-how-to-train.md)
* Come eseguire il [test](luis-interactive-test.md)
* Come [pubblicare](luis-how-to-publish-app.md)
* Modelli
    * [Concetti](luis-concept-patterns.md)
    * [Sintassi](reference-pattern-syntax.md)
* [Repository GitHub delle entità predefinite](https://github.com/Microsoft/Recognizers-Text)
* [Concetti sull'estrazione dei dati](luis-concept-data-extraction.md)


 
