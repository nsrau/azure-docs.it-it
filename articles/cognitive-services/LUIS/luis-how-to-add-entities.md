---
title: Aggiungere entità - LUISAdd entities - LUIS
titleSuffix: Azure Cognitive Services
description: Creare entità per estrarre i dati chiave dalle espressioni utente nelle app LUIS (Language Understanding). I dati dell'entità estratti vengono utilizzati dall'applicazione client per eseguire il fullfil delle richieste dei clienti.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220908"
---
# <a name="add-entities-to-extract-data"></a>Aggiungere entità per estrarre i datiAdd entities to extract data 

Creare entità per estrarre i dati chiave dalle espressioni utente nelle app LUIS (Language Understanding). I dati dell'entità estratti vengono utilizzati dall'applicazione client per eseguire il fullfil delle richieste dei clienti.

L'entità rappresenta una parola o una frase all'interno dell'espressione che si intende estrarre. Le entità descrivono le informazioni rilevanti per la finalità e sono talvolta essenziali affinché l'app svolga la sua attività. È possibile creare entità quando si aggiunge un'espressione di esempio a una finalità o separata dall'aggiunta di un'espressione di esempio a una finalità.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="plan-entities-then-create-and-label"></a>Pianificare le entità, quindi creare ed etichettare

Le entità apprese automaticamente possono essere create dalle espressioni di esempio o dalla pagina Entità.Machine-learned entities can be created from the example utterances or created from the **Entities** page. 

In generale, una procedura consigliata consiste nel dedicare del tempo alla pianificazione delle entità prima di creare un'entità appresa dal computer nel portale. Creare quindi l'entità appresa dal computer dall'espressione di esempio con lo stesso dettaglio nei sottocomponenti, nei descrittori e nei vincoli che si conosce al momento. [L'esercitazione sull'entità scomponibile](tutorial-machine-learned-entity.md) illustra come usare questo metodo. 

Durante la pianificazione delle entità, è possibile sapere che sono necessarie entità di corrispondenza del testo (ad esempio entità predefinite, entità di espressioni regolari o entità di elenco). È possibile crearli dalla pagina **Entità** prima che vengano etichettati nelle espressioni di esempio. 

Durante l'etichettatura, è possibile etichettare singole entità e quindi creare fino a un'entità appresa dal computer padre. In alternativa, è possibile iniziare con un'entità appresa dal computer padre e scomporre in entità figlio. 

> [!TIP] 
>Etichettare tutte le parole che possono indicare un'entità, anche se le parole non vengono utilizzate quando vengono estratte nell'applicazione client. 

## <a name="creating-an-entity-before-or-with-labeling"></a>Creazione di un'entità prima o con l'etichettatura

Usare la tabella seguente per comprendere quali entità devono creare o aggiungere ogni entità all'app. 

|Tipo di entità|Dove creare l'entità nel portale LUIS|
|--|--|
|Entità basata su Machine Learning|Dettaglio entità o finalità|
|Entità elenco|Dettaglio entità o finalità|
|Entità di espressione regolare|Entità|
|Entità pattern.any|Entità|
|Entità predefinita|Entità|
|Entità di dominio precompilata|Entità|

È possibile creare tutte le entità dalla pagina **Entità** oppure creare un paio di entità come parte dell'etichettatura dell'entità nell'espressione di esempio nella pagina **Dettagli intento.** È possibile _etichettare_ un'entità solo in un'espressione di esempio dalla pagina **Dettagli intento.** 

## <a name="create-a-machine-learned-entity"></a>Creare un'entità appresa dal computerCreate a machine-learned entity

[!INCLUDE [Create and label entities in machine-learned tutorial](includes/decomposable-tutorial-links.md)]

## <a name="create-a-text-matching-entity"></a>Creare un'entità di corrispondenza del testoCreate a text-matching entity

Utilizzare le entità di corrispondenza del testo per estrarre i dati in diversi modi:Use text-matching entities provide several ways to extract data:

|Entità corrispondenti al testo|Scopo|
|--|--|
|[Entità elenco](#add-list-entities-for-exact-matches)|elenco di nomi canonici insieme a sinonimi come forme alternative|
|Entità di espressione regolare|trovare il testo utilizzando un'entità di espressione regolare|
|[Entità predefinita](tutorial-machine-learned-entity.md#add-prebuilt-number-to-help-extract-data)|tipi di dati comuni, ad esempio numero, e-mail, data|
|Entità di dominio precompilata|trovare la corrispondenza utilizzando domini oggetto selezionati|
|[Pattern.any](#add-a-patternany-entity)| per abbinare le entità che possono essere facilmente confuse con il testo circostante|  

Le entità predefinite funzionano senza fornire dati di training personalizzati. Le altre entità devono fornire dati di training dei clienti (ad esempio gli elementi dell'entità Elenco) o un'espressione (ad esempio un'espressione regolare o pattern.any).

<a name="add-list-entities"></a>

### <a name="how-to-create-a-new-custom-entity"></a>Come creare una nuova entità personalizzata

1. Nel portale LUIS passare alla sezione **Gestisci,** quindi alla pagina **Entità.** 
1. Selezionare **Crea**, quindi selezionare il tipo di entità. 
1. Continuare a configurare l'entità, quindi selezionare **Crea** al termine. 

### <a name="add-list-entities-for-exact-matches"></a>Aggiungere entità elenco per corrispondenze esatte

Le entità elenco rappresentano un set fisso e chiuso di parole correlate. Anche se l'autore può modificare l'elenco, LUIS non aumenta o riduce l'elenco. È anche possibile importare in un'entità elenco esistente utilizzando un [entità elenco .json format(reference-entity-list.md-example-json-to-import-into-list-entity). 

Nell'elenco seguente vengono illustrati il nome canonico e i sinonimi. 

|Colore - nome voce di elenco|Colore - sinonimi|
|--|--|
|Rosso|cremisi, sangue, mela, motore antincendio|
|Blu|cielo, azzurro, cobalto|
|Green|kelly, lime|

Utilizzare la procedura per creare un'entità elenco. Dopo aver creato l'entità elenco, non è necessario etichettare le espressioni di esempio in una finalità. Gli elementi di elenco e i sinonimi vengono confrontati utilizzando testo esatto. 

1. Nella sezione **Compila,** selezionare **Entità** nel riquadro sinistro, quindi selezionare **Crea**.

1. Nella finestra di **dialogo Crea un tipo di** entità `Colors` immettere il nome dell'entità, ad esempio e selezionare **Elenco**.
1. Nella finestra di dialogo **Crea un'entità elenco,** nel **sottoelenco Aggiungi nuovo....** immettere il nome della voce di elenco, ad `Green`esempio , quindi aggiungere sinonimi.

    > [!div class="mx-imgBorder"]
    > ![Creare un elenco di colori come entità elenco nella pagina Dettagli entità.](media/how-to-add-entities/create-list-entity-of-colors.png) 

1. Dopo aver aggiunto voci di elenco e sinonimi, selezionare **Crea**.

    Quando hai finito con un gruppo di modifiche all'app, ricorda di **addestrare** l'app. Non addestrare l'app dopo una singola modifica. 

    > [!NOTE]
    > Questa procedura illustra la creazione e l'etichetta di un'entità elenco da un'espressione di esempio nella pagina **Dettagli intento.** È inoltre possibile creare la stessa entità dalla pagina **Entità.**

## <a name="add-a-role-for-an-entity"></a>Aggiungere un ruolo per un'entitàAdd a role for an entity

Un ruolo è un sottotipo denominato di un'entità, in base al contesto. 

### <a name="add-a-role-to-distinguish-different-contexts"></a>Aggiungere un ruolo per distinguere contesti diversiAdd a role to distinguish different contexts

Nell'espressione seguente sono presenti due posizioni, ognuna delle quali viene `to` specificata `from`semanticamente dalle parole che la circondano, ad esempio e : 

`Pick up the package from Seattle and deliver to New York City.`

In questa procedura, aggiungere `origin` e `destination` ruoli a un'entità geographyV2 predefinita.

1. Nella sezione **Build** (Compila) selezionare **Entities** (Entità) nel pannello a sinistra.

1. Selezionare **: Aggiungi entità predefinita**. Selezionare **geographyV2,** quindi **fatto**. In questo modo viene aggiunta un'entità predefinita all'app.
    
    Se si rileva che il criterio, quando include Pattern.any, estrae le entità in modo errato, usare un [elenco esplicito](reference-pattern-syntax.md#explicit-lists) per risolvere il problema. 

1. Selezionare l'entità geographyV2 predefinita appena aggiunta dall'elenco delle entità della pagina **Entità.** 
1. Per aggiungere un nuovo **+** ruolo, selezionare accanto a **Nessun ruolo aggiunto.**
1. Nella casella di testo **Tipo ruolo...** immettere il nome del ruolo. `Origin` Aggiungere un secondo `Destination` nome di ruolo di quindi immettere. 

    > [!div class="mx-imgBorder"]
    > ![Schermata dell'aggiunta del ruolo Origin all'entità Location](media/how-to-add-entities//add-role-to-prebuilt-geographyv2-entity.png)

    Il ruolo viene aggiunto all'entità predefinita, ma non alle espressioni che utilizzano tale entità. 

### <a name="label-text-with-a-role-in-an-example-utterance"></a>Etichettare il testo con un ruolo in un'espressione di esempioLabel text with a role in an example utterance

1. Passare alla pagina Dettagli finalità, in cui sono disponibili espressioni di esempio che utilizzano il ruolo. 
1. Per etichettare con il ruolo, selezionare l'etichetta dell'entità (linea continua sotto il testo) nell'espressione di esempio, quindi selezionare **Visualizza nella tavolozza entità** dall'elenco a discesa. 

    > [!div class="mx-imgBorder"]
    > ![Screenshot della selezione di Visualizza nella tavolozza delle entità](media/how-to-add-entities/select-text-label-with-entity-palette-for-role.png)   

    La tavolozza delle entità si apre a destra. 

1. Selezionare l'entità, quindi andare nella parte inferiore della tavolozza e selezionare il ruolo. 

    > [!div class="mx-imgBorder"]
    > ![Screenshot della selezione di Visualizza nella tavolozza delle entità](media/how-to-add-entities/select-role-from-entity-palette-entity-inspector.png)

<a name="add-pattern-any-entities"></a>

## <a name="add-a-patternany-entity"></a>Aggiungere un'entità pattern.anyAdd a pattern.any entity

Le entità [Pattern.any](luis-concept-entity-types.md) sono valide solo nei [modelli,](luis-how-to-model-intent-pattern.md)non nelle espressioni di esempio delle finalità. Questo tipo di entità consente a LUIS di trovare la fine di entità di lunghezza e scelta delle parole variabili. Poiché questa entità viene usata in un criterio, LUIS sa dove si trova la fine dell'entità nel modello dell'espressione.

### <a name="steps-to-create-a-patternany-entity"></a>Passaggi per creare un'entità pattern.anySteps to create a pattern.any entity

1. Nella sezione **Compila,** selezionare **Entità** nel riquadro sinistro, quindi selezionare **Crea**.

1. Nella finestra di dialogo Scegli un tipo di entità immettere il nome **dell'entità** nella casella **Nome** e selezionare **Pattern.Any** come **Tipo,** quindi selezionare **Crea**.

    Dopo aver [creato un'espressione](luis-how-to-model-intent-pattern.md) di modello usando questa entità, l'entità viene estratta con un algoritmo combinato di ricerca computer e di corrispondenza del testo. 

### <a name="create-a-pattern-template-utterance-to-use-patternany-entity"></a>Creare un'espressione del modello di modello di modello per usare l'entità pattern.anyCreate a pattern template utterance to use pattern.any entity

Per usare l'entità pattern.any, aggiungere un criterio nella pagina **Patterns** (Criteri) nella sezione **Improve app performance** (Migliora prestazioni app) con la sintassi con parentesi graffe corretta, ad esempio `Where is **{HumanResourcesFormTitle}** on the server?`.

Se si rileva che il criterio, quando include Pattern.any, estrae le entità in modo errato, usare un [elenco esplicito](reference-pattern-syntax.md#explicit-lists) per risolvere il problema. 

## <a name="do-not-change-entity-type"></a>Non modificare il tipo di entità

LUIS non consente di modificare il tipo di entità perché non conoscere gli elementi da aggiungere o rimuovere per costruire tale entità. Per modificare il tipo, è preferibile creare una nuova entità del tipo corretto con un nome leggermente diverso. Dopo aver creato l'entità, in ogni espressione rimuovere il vecchio nome dell'entità con etichetta e aggiungere quello nuovo. Dopo che tutte le espressioni sono stata nuovamente etichettate, eliminare l'entità precedente. 

<a name="create-a-pattern-from-an-utterance"></a>

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"] 
> [Usare modelli predefiniti](howto-add-prebuilt-models.md) 

Altre informazioni su:
* Come [allenarsi](luis-how-to-train.md)
* Come [eseguire il test](luis-interactive-test.md)
* Come [pubblicare](luis-how-to-publish-app.md)
* Modelli:
    * [Concetti](luis-concept-patterns.md)
    * [Sintassi](reference-pattern-syntax.md)
* [Entità predefinite Repository GitHub](https://github.com/Microsoft/Recognizers-Text)
* [Concetti di Estrazione dati](luis-concept-data-extraction.md)


 
