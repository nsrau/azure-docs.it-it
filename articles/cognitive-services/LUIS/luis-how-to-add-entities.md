---
title: Aggiungi entità
titleSuffix: Language Understanding - Azure Cognitive Services
description: Creare entità per estrarre i dati della chiave da espressioni utente nelle App LUIS (Language Understanding).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 0044cbc9e6142989a57e79de5fd1e78e999bb5e1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60196142"
---
# <a name="create-entities-without-utterances"></a>Creare entità senza espressioni

L'entità rappresenta una parola o una frase all'interno dell'espressione che si intende estrarre. Un'entità rappresenta una classe come una raccolta di oggetti simili (luoghi, cose, persone, eventi o concetti). Le entità descrivono le informazioni rilevanti per la finalità e sono talvolta essenziali affinché l'app svolga la sua attività. È possibile creare entità quando si aggiunge un utterance a un intent o distanti tra loro da (prima o dopo) aggiunta di emissione di un suono da un intent.

È possibile aggiungere, modificare o eliminare entità nell'app LUIS tramite l'**elenco di entità** nella pagina **Entities** (Entità). LUIS offre due tipi principali di entità: le [entità predefinite](luis-reference-prebuilt-entities.md) e le [entità personalizzate](luis-concept-entity-types.md#types-of-entities).

Dopo aver creata un'entità apprese macchina, è necessario contrassegnare tale entità in tutte le utterance di esempio di tutti gli Intent in che si trova.

<a name="add-prebuilt-entity"></a>

## <a name="add-a-prebuilt-entity-to-your-app"></a>Aggiungere un'entità predefinita all'app

Le entità predefinite più comuni aggiunte a un'applicazione sono *number* e *datetimeV2*. 

1. Nella sezione **Build** (Compila) della propria app selezionare **Entities** (Entità) nel pannello a sinistra.
 
1. Nella pagina **Entities** (Entità) fare clic su **Add prebuilt entities** (Aggiungi entità predefinite).

1. Nella finestra di dialogo **Add prebuilt entities** (Aggiungi entità predefinite) selezionare le entità predefinite **number** e **datetimeV2**. Al termine selezionare **Done** (Fine).

    ![Schermata della finestra di dialogo di aggiunta entità predefinite](./media/add-entities/list-of-prebuilt-entities.png)

<a name="add-simple-entities"></a>

## <a name="add-simple-entities-for-single-concepts"></a>Aggiungere le entità semplici per i concetti singoli

Un'entità semplice descrive un singolo concetto. Usare la procedura seguente per creare un'entità che estrae i nomi dei reparti aziendali, ad esempio *Human resources* o *Operations*.   

1. Nella sezione **Build** (Compila) dell'app fare clic su **Entities** (Entità) nel pannello a sinistra e quindi selezionare **Create new entity** (Crea nuova entità).

1. Nella finestra di dialogo popup digitare `Location` nella casella **Entity name** (Nome entità), selezionare **Simple** (Semplice) nell'elenco **Entity type** (Tipo di entità) e quindi selezionare **Done** (Fine).

    Dopo avere creato questa entità, passare a tutte le finalità con espressioni di esempio contenenti l'entità. Selezionare il testo nell'espressione di esempio e contrassegnarlo come entità. 

    Un [elenco di frasi](luis-concept-feature.md) viene in genere usato per aumentare il segnale di un'entità semplice.

<a name="add-regular-expression-entities"></a>

## <a name="add-regular-expression-entities-for-highly-structured-concepts"></a>Aggiungere le entità di espressione regolare per informazioni sui concetti altamente strutturati

Un'entità di espressione regolare viene usata per estrarre dati dall'espressione in base all'espressione regolare specificata. 

1. Nel pannello di spostamento a sinistra dell'app selezionare **Entities** (Entità) e quindi selezionare **Create new entity** (Crea nuova entità).

1. Nella finestra di dialogo popup immettere `Human resources form name` nella casella **Entity name** (Nome entità), selezionare **Regular expression** (Espressione regolare) nell'elenco **Entity type** (Tipo di entità), immettere l'espressione regolare `hrf-[0-9]{6}` e quindi selezionare **Done** (Fine). 

    Questa espressione regolare corrisponde al carattere letterale `hrf-`, quindi 6 cifre per rappresentare un form numero per un modulo di gestione delle risorse umane.

## <a name="add-hierarchical-entities"></a>Aggiungere entità gerarchiche

Un'entità gerarchica è una categoria di entità con apprendimento su base contestuale e correlate per concetto. Nell'esempio seguente, l'entità contiene ubicazioni di origine e destinazione. 

Nell'emissione `Move John Smith from Seattle to Cairo`, Seattle è l'origine, mentre Il Cairo è la destinazione. Ogni ubicazione è diversa a livello di contesto e viene appresa dall'ordine e dalla scelta delle parole nell'espressione.

Per aggiungere entità gerarchiche, seguire questa procedura: 

1. Nel pannello di spostamento a sinistra dell'app selezionare **Entities** (Entità) e quindi selezionare **Create new entity** (Crea nuova entità).

1. Nella finestra di dialogo popup digitare `Location` nella casella **Entity name** (Nome entità) e quindi selezionare **Hierarchical** (Gerarchica) nell'elenco **Entity type** (Tipo di entità).

    ![Aggiungere un'entità gerarchica](./media/add-entities/hier-location-entity-creation.png)

1. Selezionare **Add Child** (Aggiungi figlio) e quindi digitare `Origin` nella casella **Child #1** (Figlio n. 1). 

1. Selezionare **Add Child** (Aggiungi figlio) e quindi digitare `Destination` nella casella **Child #2** (Figlio n. 2). Selezionare **Operazione completata**.

    >[!CAUTION]
    >I nomi delle entità figlio devono essere univoci per tutte le entità in una singola app. Due diverse entità gerarchiche non possono contenere entità figlio con lo stesso nome. 

    Dopo avere creato questa entità, passare a tutte le finalità con espressioni di esempio contenenti l'entità. Selezionare il testo nell'espressione di esempio e contrassegnarlo come entità. 

<a name="add-composite-entities"></a>

## <a name="add-composite-entities-to-group-into-a-parent-child-relationship"></a>Aggiungere le entità composte da raggruppare in una relazione padre-figlio

È possibile definire relazioni tra entità di tipi diversi creando un'entità composita. Nell'esempio seguente l'entità contiene un'espressione regolare e un'entità predefinita del nome.  

Nell'espressione `Send hrf-123456 to John Smith` il testo `hrf-123456` viene associato a un'[espressione regolare](#add-regular-expression-entities) delle risorse umane e `John Smith` viene estratto con l'entità predefinita personName. Ogni entità è parte di un'entità padre più ampia. 

1. Nel pannello di spostamento a sinistra dell'app selezionare **Entities** (Entità) nella sezione **Build** (Compila) e quindi selezionare **Add prebuilt entity** (Aggiungi entità predefinita).

1. Aggiungere l'entità predefinita **PersonName**. Per istruzioni, vedere [Aggiungere entità predefinite](#add-prebuilt-entity). 

1. Nel pannello di spostamento a sinistra selezionare **Entities** (Entità) e quindi selezionare **Create new entity** (Crea nuova entità).

1. Nella finestra di dialogo popup digitare `SendHrForm` nella casella **Entity name** (Nome entità) e quindi selezionare **Composite** (Composita) nell'elenco **Entity type** (Tipo di entità).

1. Selezionare **Add Child** (Aggiungi figlio) per aggiungere un nuovo figlio.

1. Nella casella **Child #1** (Figlio n. 1) selezionare l'entità **number** nell'elenco.

1. Nella casella **Child #2** (Figlio n. 2) selezionare l'entità **Human resources form name** nell'elenco. 

1. Selezionare **Operazione completata**.

<a name="add-pattern-any-entities"></a>

## <a name="add-patternany-entities-to-capture-free-form-entities"></a>Aggiungere le entità Pattern.any per acquisire le entità in formato libero

Le entità [Pattern.any](luis-concept-entity-types.md) sono valide solo nei [criteri](luis-how-to-model-intent-pattern.md) e non nelle finalità. Questo tipo di entità consente a LUIS di trovare la fine di entità di lunghezza e scelta delle parole variabili. Poiché questa entità viene usata in un criterio, LUIS sa dove si trova la fine dell'entità nel modello dell'espressione.

Se un'app ha una finalità `FindHumanResourcesForm`, il titolo del modulo estratto può interferire con la previsione della finalità. Per chiarire quali parole si trovano nel titolo del modulo, usare un'entità Pattern.any in un criterio. La stima di LUIS inizia con l'espressione. Viene eseguito un controllo per verificare la corrispondenza con entità e, se queste ultime vengono individuate, il criterio viene controllato e associato. 

Nell'espressione `Where is Request relocation from employee new to the company on the server?` il titolo del modulo è fuorviante perché dal contesto non risulta ovvio dove termini il titolo e dove inizi il resto dell'espressione. I titoli possono essere costituiti da parole in qualsiasi ordine, ad esempio una parola singola, frasi complesse con segni di punteggiatura e parole con ordine senza senso. Un criterio consente di creare un'entità in cui sia possibile estrarre l'entità completa ed esatta. Dopo che il titolo è stato individuato, la finalità `FindHumanResourcesForm` viene prevista perché si tratta della finalità del criterio.

1. Nella sezione **Build** (Compila) fare clic su **Entities** (Entità) nel pannello a sinistra e quindi selezionare **Create new entity** (Crea nuova entità).

1. Nella finestra di dialogo **Add Entity** (Aggiungi entità) immettere `HumanResourcesFormTitle` nella casella **Entity name** (Nome entità) e selezionare **Pattern.any** come **Entity type** (Tipo di entità).

    Per usare l'entità pattern.any, aggiungere un criterio nella pagina **Patterns** (Criteri) nella sezione **Improve app performance** (Migliora prestazioni app) con la sintassi con parentesi graffe corretta, ad esempio `Where is **{HumanResourcesFormTitle}** on the server?`.

    Se si rileva che il criterio, quando include Pattern.any, estrae le entità in modo errato, usare un [elenco esplicito](luis-concept-patterns.md#explicit-lists) per risolvere il problema. 

<a name="add-a-role-to-pattern-based-entity"></a>

## <a name="add-a-role-to-distinguish-different-contexts"></a>Aggiungere un ruolo per distinguere i diversi contesti

Un ruolo è un sottotipo denominato in base al contesto. È disponibile in tutte le entità incluse le entità predefinite e non-machine-appreso. 

Usando lo stesso esempio dell'entità gerarchica delle città di origine e di destinazione, la differenza è che viene denominato Origin un ruolo invece di un elemento figlio gerarchico. 

La sintassi per un ruolo è **{Entityname:Rolename}** dove il nome dell'entità è seguito da due punti e quindi dal nome del ruolo. Ad esempio: `Move {personName} from {LocationUsingRoles:Origin} to {LocationUsingRoles:Destination}`.

1. Nella sezione **Build** (Compila) selezionare **Entities** (Entità) nel pannello a sinistra.

1. Selezionare **Create new entity** (Crea nuova entità). Immettere il nome di `LocationUsingRoles`. Selezionare il tipo **Simple** (Semplice) e quindi selezionare **Done** (Fatto). 

1. Selezionare **Entities** (Entità) nel pannello a sinistra, quindi selezionare la nuova entità **LocationUsingRoles** creata nel passaggio precedente.

1. Nella casella di testo **Role name** (Nome ruolo) immettere il nome del ruolo `Origin` e quindi premere INVIO. Aggiungere un secondo nome del ruolo di `Destination`. 

    ![Schermata dell'aggiunta del ruolo Origin all'entità Location](./media/add-entities/roles-enter-role-name-text.png)

<a name="add-list-entities"></a>

## <a name="add-list-entities-for-exact-matches"></a>Aggiungere l'elenco di entità per la corrispondenza esatta

Le entità elenco rappresentano un set fisso e chiuso di parole correlate. 

In un'app per la gestione delle risorse umane è possibile avere un elenco di tutti i reparti insieme ai sinonimi dei reparti. Non è necessario conoscere tutti i valori quando si crea l'entità. È possibile aggiungerne altri dopo aver esaminato le espressioni reali degli utenti con i sinonimi.

1. Nella sezione **Build** (Compila) fare clic su **Entities** (Entità) nel pannello a sinistra e quindi selezionare **Create new entity** (Crea nuova entità).

1. Nella finestra di dialogo **Add Entity** (Aggiungi entità) digitare `Department` nella casella **Entity name** (Nome entità) e selezionare **List** (Elenco) come **Entity type** (Tipo di entità). Selezionare **Operazione completata**.
  
1. Nella pagina delle entità elenco è possibile aggiungere nomi normalizzati. Nella casella di testo **Values** (Valori) immettere un nome di reparto per l'elenco, ad esempio `HumanResources`, quindi premere INVIO. 

1. A destra del valore normalizzato immettere i sinonimi, premendo INVIO dopo ogni elemento.

1. Se si desiderano più elementi normalizzati per l'elenco, selezionare **Recommend** (Consigliato) per visualizzare le opzioni del [dizionario semantico](luis-glossary.md#semantic-dictionary).

    ![Screenshot della selezione della funzionalità consigliata per visualizzare le opzioni](./media/add-entities/hr-list-2.png)


1. Selezionare un elemento nell'elenco consigliato per aggiungerlo come valore normalizzato o selezionare **Add all** (Aggiungi tutti) per aggiungere tutti gli elementi. 
    È possibile importare valori in un'entità elenco esistente usando il formato JSON seguente:

    ```JSON
    [
        {
            "canonicalForm": "Blue",
            "list": [
                "navy",
                "royal",
                "baby"
            ]
        },
        {
            "canonicalForm": "Green",
            "list": [
                "kelly",
                "forest",
                "avacado"
            ]
        }
    ]  
    ```

<a name="change-entity-type"></a>

## <a name="do-not-change-entity-type"></a>Non modificare il tipo di entità

LUIS non consente di modificare il tipo di entità perché non conoscere gli elementi da aggiungere o rimuovere per costruire tale entità. Per modificare il tipo, è preferibile creare una nuova entità del tipo corretto con un nome leggermente diverso. Dopo aver creato l'entità, in ogni espressione rimuovere il vecchio nome dell'entità con etichetta e aggiungere quello nuovo. Dopo che tutte le espressioni sono stata nuovamente etichettate, eliminare l'entità precedente. 

<a name="create-a-pattern-from-an-utterance"></a>

## <a name="create-a-pattern-from-an-example-utterance"></a>Creare un modello da un utterance di esempio

Vedi [Aggiungi un criterio da un'espressione esistente nella pagina delle entità o finalità](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).

## <a name="train-your-app-after-changing-model-with-entities"></a>Training dell'app dopo la modifica del modello con entità

Dopo aver aggiunto, modificato o rimosso un'entità, [eseguire il training](luis-how-to-train.md) e [pubblicare](luis-how-to-publish-app.md) l'app affinché le modifiche siano effettive per le query di endpoint. 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle entità predefinite, vedere il progetto [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text). 

Per informazioni sul modo in cui l'entità viene visualizzata nella risposta alla query di endpoint JSON, vedere [Estrazione dei dati](luis-concept-data-extraction.md)

Dopo aver aggiunto finalità, espressioni ed entità, è disponibile un'app LUIS di base. Informazioni su com [eseguire il training](luis-how-to-train.md), [testare](luis-interactive-test.md) e [pubblicare](luis-how-to-publish-app.md) l'app.
 
