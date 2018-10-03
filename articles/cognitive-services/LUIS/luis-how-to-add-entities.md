---
title: Aggiungere entità in app LUIS
titleSuffix: Azure Cognitive Services
description: Aggiungere entità (dati chiave nel dominio dell'applicazione) in app LUIS (Language Understanding).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: e82955da24e127e5536c2e40ad2cccf07c5fa173
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "47032003"
---
# <a name="manage-entities"></a>Gestire le entità
Dopo aver identificato le [finalità](luis-concept-intent.md) dell'app, è necessario [etichettare espressioni di esempio](luis-concept-utterance.md) con [entità](luis-concept-entity-types.md). Le entità sono le parti importanti di un comando o di una domanda e possono essere essenziali affinché l'app del cliente esegua l'attività prevista. 

È possibile aggiungere, modificare o eliminare le entità nella tua app tramite l'**elenco di entità** nella pagina **Entities** (Entità). LUIS offre due tipi principali di entità: [entità predefinite](luis-reference-prebuilt-entities.md) ed entità personalizzate.

Le sezioni seguenti sono disponibili solo in un'app LUIS nella sezione **Build** (Compila). Il collegamento **Build** (Compila) è presente nella barra di spostamento superiore. Nella sezione **Build** (Compila) selezionare **Entities** (Entità) nel menu di spostamento a sinistra. Dopo che un'entità è stata aggiunta all'applicazione, se l'entità è appresa in modo automatico, è possibile [etichettarla](luis-how-to-add-example-utterances.md) all'interno dell'espressione. Dopo che l'app viene sottoposta a training e pubblicata, è possibile ricevere i dati di entità [estratti](luis-concept-data-extraction.md) dalla stima. 

## <a name="add-prebuilt-entity"></a>Aggiungere entità predefinite
Le entità predefinite sono stabilite nel progetto [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text). Le entità predefinite più comuni aggiunte a un'applicazione sono *number* e *datetimeV2*. 

1. Nella sezione **Build** (Compila) della propria app fare clic su **Entities** (Entità) nel pannello a sinistra.
 
2. Nella pagina **Entities** (Entità) fare clic su **Manage prebuilt entities** (Gestisci entità predefinite).

3. Nella finestra di dialogo **Add or remove prebuilt entities** (Aggiungi o rimuovi entità predefinite) selezionare le entità predefinite **number** e **datetimeV2**. Al termine selezionare **Done** (Fine).

    ![Schermata della finestra di dialogo di aggiunta entità predefinite](./media/add-entities/list-of-prebuilt-entities.png)

    Per altre informazioni sull'estrazione dell'entità predefinita dalla risposta alla query JSON sull'endpoint, vedere [Estrazione di dati](luis-concept-data-extraction.md#prebuilt-entity-data).

## <a name="add-simple-entities"></a>Aggiungere entità semplici
Un'entità semplice è un'entità generica che descrive un singolo concetto. 

1. Nella sezione **Build** (Compila) dell'app fare clic su **Entities** (Entità) nel pannello a sinistra e quindi selezionare **Create new entity** (Crea nuova entità).

2. Nella finestra di dialogo popup digitare `Airline` nella casella **Entity name** (Nome entità), selezionare **Simple** (Semplice) nell'elenco **Entity type** (Tipo di entità) e quindi selezionare **Done** (Fine).

    ![Schermata della finestra di dialogo per la creazione dell'entità semplice Airline](./media/add-entities/create-simple-airline-entity.png)

    Per altre informazioni sull'estrazione dell'entità semplice dalla risposta alla query JSON sull'endpoint, vedere [Estrazione di dati](luis-concept-data-extraction.md#simple-entity-data). Per altre informazioni sull'uso delle entità semplici, seguire la relativa [guida introduttiva](luis-quickstart-primary-and-secondary-data.md).

## <a name="add-regular-expression-entities"></a>Aggiungere entità di espressione regolare
Un'entità di espressione regolare viene usata per estrarre dati dall'espressione in base all'espressione regolare specificata. 

1. Nel pannello di spostamento a sinistra dell'app selezionare **Entities** (Entità) e quindi selezionare **Create new entity** (Crea nuova entità).

2. Nella finestra di dialogo popup immettere `AirFrance Flight` nella casella **Entity name** (Nome entità), selezionare **Regular expression** (Espressione regolare) nell'elenco **Entity type** (Tipo di entità), immettere l'espressione regolare `AFR[0-9]{3,4}` e quindi selezionare **Done** (Fine). 

    L'espressione regolare AirFrance Flight si aspetta tre caratteri, ovvero `AFR`, quindi 3 o 4 cifre. Le cifre possono essere un qualsiasi numero compreso tra 0 e 9. L'espressione regolare corrisponde ai numeri di volo AirFrance, ad esempio AFR101", "ARF1302" e "AFR5006". Per altre informazioni sull'estrazione dell'entità dalla risposta alla query JSON sull'endpoint, vedere [Estrazione di dati](luis-concept-data-extraction.md).

    ![Immagine della finestra di dialogo per creare l'entità di espressione regolare](./media/add-entities/regex-entity-create-dialog.png)

    Per altre informazioni sull'estrazione di entità di espressione regolare dalla risposta alla query JSON sull'endpoint, vedere [Estrazione di dati](luis-concept-data-extraction.md#regular-expression-entity-data). Per altre informazioni sull'uso di un'entità di espressione regolare, vedere la [guida introduttiva](luis-quickstart-intents-regex-entity.md).

## <a name="add-hierarchical-entities"></a>Aggiungere entità gerarchiche
Un'entità gerarchica è una categoria di entità con apprendimento su base contestuale e correlate per concetto. Nell'esempio seguente, l'entità contiene ubicazioni di origine e destinazione. 

Nell'emissione `Book 2 tickets from Seattle to Cairo`, Seattle è l'origine, mentre Il Cairo è la destinazione. Ogni ubicazione è diversa a livello di contesto e viene appresa dall'ordine e dalla scelta delle parole nell'espressione.

Per aggiungere entità gerarchiche, seguire questa procedura: 

1. Nel pannello di spostamento a sinistra dell'app selezionare **Entities** (Entità) e quindi selezionare **Create new entity** (Crea nuova entità).

2. Nella finestra di dialogo popup digitare `Location` nella casella **Entity name** (Nome entità) e quindi selezionare **Hierarchical** (Gerarchica) nell'elenco **Entity type** (Tipo di entità).

    ![Aggiungere un'entità gerarchica](./media/add-entities/hier-location-entity-creation.png)

3. Selezionare **Add Child** (Aggiungi figlio) e quindi digitare "Origine" nella casella **Child #1** (Figlio n. 1). 

4. Selezionare **Add Child** (Aggiungi figlio) e quindi digitare "Destinazione" nella casella **Child #2** (Figlio n. 2). Selezionare **Operazione completata**.
5. 
    >[!NOTE]
    >Per eliminare un figlio, selezionare l'icona del Cestino accanto all'elemento stesso.

    >[!CAUTION]
    >I nomi delle entità figlio devono essere univoci per tutte le entità in una singola app. Due diverse entità gerarchiche non possono contenere entità figlio con lo stesso nome. 

    Per altre informazioni sull'estrazione dell'entità gerarchica dalla risposta alla query JSON sull'endpoint, vedere [Estrazione di dati](luis-concept-data-extraction.md#hierarchical-entity-data). Per altre informazioni sull'uso delle entità gerarchiche, seguire la relativa [guida introduttiva](luis-quickstart-intent-and-hier-entity.md).

## <a name="add-composite-entities"></a>Aggiungere entità composita
È possibile definire relazioni tra diverse entità esistenti creando un'entità composita. Nell'esempio seguente l'entità contiene il numero di biglietti e i luoghi di origine e di destinazione. 

Nell'espressione `Book 2 tickets from Seattle to Cairo` il numero 2 viene associato a un'entità predefinita, Seattle è il luogo di origine e Il Cairo è il luogo di destinazione. Dopo la creazione dell'entità composta, ogni entità è parte di un'entità padre più grande.

1. Nell'app aggiungere l'entità predefinita **number**. Per istruzioni, vedere [Aggiungere entità predefinite](#add-prebuilt-entity). 

2. Aggiungere l'entità gerarchica `Location`, inclusi i sottotipi: `origin`, `destination`. Per istruzioni, vedere [Aggiungere entità gerarchiche](#add-hierarchical-entities). 

3. Nel pannello di spostamento a sinistra selezionare **Entities** (Entità) e quindi selezionare **Create new entity** (Crea nuova entità).

4. Nella finestra di dialogo popup digitare `TicketsOrder` nella casella **Entity name** (Nome entità) e quindi selezionare **Composite** (Composita) **Entity type** nell'elenco (Tipo di entità).

5. Selezionare **Add Child** (Aggiungi figlio) per aggiungere un nuovo figlio.

6. Nella casella **Child #1** (Figlio n. 1) selezionare l'entità **number** nell'elenco.

7. Nella casella **Child #2** (Figlio n. 2) selezionare l'entità **Location::Origin** nell'elenco. 

8. Nella casella **Child #3** (Figlio n. 3) selezionare l'entità **Location::Destination** nell'elenco. 

9. Selezionare **Operazione completata**.

    ![Immagine della finestra di dialogo per creare un'entità composita](./media/add-entities/ticketsorder-composite-entity.png)

    >[!NOTE]
    >Per eliminare un figlio, selezionare il pulsante del Cestino accanto all'elemento.

    Per altre informazioni sull'estrazione dell'entità composita dalla risposta alla query JSON sull'endpoint, vedere [Estrazione di dati](luis-concept-data-extraction.md#composite-entity-data). Per altre informazioni sull'uso di un'entità composita, seguire la relativa [esercitazione](luis-tutorial-composite-entity.md).


## <a name="add-patternany-entities"></a>Aggiungere entità Pattern.any
Le entità [Pattern.any](luis-concept-entity-types.md) sono valide solo in [criteri](luis-how-to-model-intent-pattern.md). Questo tipo di entità consente a LUIS di trovare la fine di entità di lunghezza e scelta delle parole variabili. Poiché questa entità viene usata in un criterio, LUIS conosce il punto finale dell'entità nell'espressione.

Se un'app ha una finalità `FindBookInfo`, il titolo del libro può interferire con la stima della finalità. Per chiarire quali parole si trovano nel titolo del libro, usare un'entità Pattern.any in un criterio. La stima di LUIS inizia con l'espressione. Viene eseguito un controllo per verificare la corrispondenza con entità e, se queste ultime vengono individuate, il criterio viene controllato e associato. 

Nell'espressione `Who wrote the book Ask and when was it published?` il titolo del libro, Ask, è fuorviante perché dal conteso non risulta ovvio dove termini il titolo e dove inizi il resto dell'espressione. I titoli di libro possono essere costituiti da parole in qualsiasi ordine, ad esempio una parola singola, frasi complesse con segni di punteggiatura e parole con ordine senza senso. Un criterio consente di creare un'entità in cui sia possibile estrarre l'entità completa ed esatta. Dopo che il tiolo del libro è stato individuato, la finalità `FindBookInfo` viene prevista perché si tratta della finalità per il criterio.

1. Nella sezione **Build** (Compila) dell'app fare clic su **Entities** (Entità) nel pannello a sinistra e quindi selezionare **Create new entity** (Crea nuova entità).

2. Nella finestra di dialogo **Add Entity** (Aggiungi entità) digitare `BookTitle` nella casella **Entity name** (Nome entità) e selezionare **Pattern.any** come **Entity type** (Tipo di entità).
 
    ![Schermata della creazione di un'entità pattern.any](./media/add-entities/create-pattern-any-entity.png)

    Per usare l'entità pattern.any, aggiungere un criterio nella pagina **Patterns** (Criteri) nella sezione **Improve app performance** (Migliora prestazioni app) con la sintassi con parentesi graffe corretta, ad esempio "Per **{BookTitle}** chi è l'autore?".

    Per altre informazioni sull'estrazione dell'entità Pattern.any dalla risposta alla query JSON sull'endpoint, vedere [Estrazione di dati](luis-concept-data-extraction.md#patternany-entity-data). Per altre informazioni su come usare un'entità Pattern.any, provare a eseguire l'esercitazione relativa ai [criteri](luis-tutorial-pattern.md).

Se si rileva che il criterio, quando include Pattern.any, estrae le entità in modo errato, usare un [elenco esplicito](luis-concept-patterns.md#explicit-lists) per risolvere il problema. 

## <a name="add-role-to-pattern-based-entity"></a>Aggiungere un ruolo a un'entità basata su criteri
Un ruolo è un sottotipo denominato di un'entità basato sul contesto. I ruoli possono essere paragonati a un'entità [gerarchica](#add-hierarchical-entities), anche se vengono usati solo in [criteri](luis-how-to-model-intent-pattern.md). 

Per un biglietto di aereo, ad esempio, sono presenti una *città di origine* e una *città di destinazione*, ma entrambe sono città. LUIS determina che entrambe sono città e può determinare le città di origine e destinazione in base al contesto dell'ordine e della scelta delle parole. 

La sintassi per un ruolo è **{Entity name:Role name}** dove il nome dell'entità è seguito da due punti e quindi dal nome del ruolo. Si ha, ad esempio, "Prenotare un biglietto da {Location:Origin} a {Location:Destination}".

1. Nella sezione **Build** (Compila) della propria app selezionare **Entities** (Entità) nel pannello a sinistra.

2. Selezionare **Create new entity** (Crea nuova entità). Immettere il nome di `Location`. Selezionare il tipo **Simple** (Semplice) e quindi selezionare **Done** (Fatto).

3. Selezionare **Entities** (Entità) nel pannello a sinistra, quindi selezionare la nuova entità **Location** creata nel passaggio 2.

4. Nella casella di testo **Role name** (Nome ruolo) immettere il nome del ruolo `Origin` e quindi premere INVIO. Aggiungere un secondo nome del ruolo di `Destination`. Per un viaggio aereo, ad esempio, sono presenti una città di origine e una di destinazione. I due ruoli sono "Origin" e "Destination".

    ![Schermata dell'aggiunta del ruolo Origin all'entità Location](./media/add-entities/roles-enter-role-name-text.png)

    Per altre informazioni sull'estrazione di ruoli dalla risposta alla query JSON sull'endpoint, vedere [Estrazione di dati](luis-concept-data-extraction.md). Per altre informazioni su come usare un'entità Pattern.any, provare a eseguire l'esercitazione relativa ai criteri.

## <a name="add-list-entities"></a>Aggiungere entità elenco
Le entità elenco rappresentano un set di parole correlate fisso e chiuso nel sistema. 

Per un'entità di elenco di una serie di bevande, è possibile avere due valori normalizzati: acqua e bibita. Ciascun nome normalizzato ha dei sinonimi. I sinonimi vengono indicati in inglese. Per l'acqua, i sinonimi sono H20, gas, flat (liscia). Per bibita, i sinonimi sono fruit (fruit), cola, ginger. Non è necessario conoscere tutti i valori quando si crea l'entità. È possibile aggiungerne altri dopo aver esaminato le espressioni reali degli utenti con i sinonimi.

|Nome normalizzato|Sinonimi|
|--|--|
|Acqua|H20, gas, flat|
|Bevanda|Fruit, cola, ginger|

1. Nella sezione **Build** (Compila) dell'app fare clic su **Entities** (Entità) nel pannello a sinistra e quindi selezionare **Create new entity** (Crea nuova entità).

2. Nella finestra di dialogo **Add Entity** (Aggiungi entità) digitare `Drinks` nella casella **Entity name** (Nome entità) e selezionare **List** (Elenco) come **Entity type** (Tipo di entità). Selezionare **Operazione completata**.
 
    ![Immagine della finestra di dialogo per la creazione dell'entità elenco Drinks](./media/add-entities/menu-list-dialog.png)
  
3.  Nella pagina delle entità elenco è possibile aggiungere nomi normalizzati. Nella casella di testo **Values** (Valori) immettere un elemento per l'elenco, ad esempio `Water` per l'elenco di bevande e quindi premere Invio. 

    ![Schermata dell'entità elenco Drinks con il valore normalizzato water nella casella di testo](./media/add-entities/entity-list-normalized-name.png)

4. A destra del valore normalizzato **water**, immettere i sinonimi `h20`, `flat` e `gas`, premendo INVIO sulla tastiera dopo ogni elemento.

    ![Schermata dell'entità elenco Drink con sinonimi per il valore water evidenziati](./media/add-entities/menu-list-synonyms.png)

5. Se si desiderano più elementi normalizzati per l'elenco, selezionare **Recommend** (Consigliato) per visualizzare le opzioni del [dizionario semantico](luis-glossary.md#semantic-dictionary).

    ![Schermata dell'entità elenco Drink con gli elementi consigliati evidenziati](./media/add-entities/entity-list-recommended-list.png)

6. Selezionare un elemento nell'elenco consigliato per aggiungerlo come valore normalizzato o selezionare **Add all** (Aggiungi tutti) per aggiungere tutti gli elementi. 

    ![Schermata dell'entità elenco Drink con l'elemento consigliato beer e il pulsante Add all (Aggiungi tutti) evidenziato](./media/add-entities/list-entity-add-suggestions.png)

    Per altre informazioni sull'estrazione di entità elenco dalla risposta alla query JSON sull'endpoint, vedere [Estrazione di dati](luis-concept-data-extraction.md#list-entity-data). Per altre informazioni sull'uso di un'entità elenco, seguire la [guida introduttiva](luis-quickstart-intent-and-list-entity.md).

## <a name="import-list-entity-values"></a>Importare valori delle entità elenco
È possibile importare valori in un'entità elenco esistente.

 1. Nella pagina dell'entità elenco selezionare **Import Lists** (Importa elenchi).

 2. Nella finestra di dialogo **Import New Entries** (Importa nuove entità) selezionare **Choose File** (Scegli file) e quindi selezionare il file JSON che include l'elenco.

    ![Schermata della finestra di dialogo popup per l'importazione dei valori dell'entità elenco](./media/add-entities/menu-list-import-json-dialog-with-file.png)

    >[!NOTE]
    >LUIS importa solo i file con estensione "Json".

 3. Per altre informazioni sulla sintassi di elenchi in JSON, selezionare **Learn about supported list syntax** (Informazioni sulla sintassi di elenchi supportata) per espandere la finestra di dialogo e visualizzare un esempio di sintassi consentita. Per comprimere la finestra di dialogo e nascondere la sintassi, selezionare nuovamente il titolo del collegamento.

 4. Selezionare **Operazione completata**.

    Un esempio di codice JSON valido per un'entità elenco **Colors** viene visualizzato nel codice in formato JSON seguente:

    ```
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

## <a name="edit-entity-name"></a>Modificare il nome di entità
1. Nella pagina **Entities** (Entità) selezionare l'entità nell'elenco. Viene visualizzata la pagina **Entity** (Entità).

2. Nella pagina **Entity** (Entità) selezionare l'icona di modifica accanto al nome dell'entità per modificarlo. Il tipo di entità non può essere modificato. 

## <a name="delete-entity"></a>Eliminare entità

Nella pagina **Entity** (Entità) selezionare il pulsante **Delete Entity** (Elimina entità). Fare clic su **OK** nel messaggio di conferma per confermare l'eliminazione.
 
![Schermata della pagina dell'entità Location con il pulsante di eliminazione entità evidenziato](./media/add-entities/entity-delete.png)

>[!NOTE]
>* Se si elimina un'entità gerarchica,vengono eliminate anche le relative entità figlio.
>* L'eliminazione di un'entità composita comporta l'eliminazione solo di tale entità e l'interruzione della relazione tra entità, ma non l'eliminazione delle entità che la costituiscono.

## <a name="changing-entity-type"></a>Modifica del tipo di entità
LUIS non consente di modificare il tipo di entità perché non conoscere gli elementi da aggiungere o rimuovere per costruire tale entità. Per modificare il tipo, è preferibile creare una nuova entità del tipo corretto con un nome leggermente diverso. Dopo aver creato l'entità, in ogni espressione rimuovere il vecchio nome dell'entità con etichetta e aggiungere quello nuovo. Dopo che tutte le espressioni sono stata nuovamente etichettate, eliminare l'entità precedente. 

## <a name="create-a-pattern-from-an-utterance"></a>Creare un criterio a partire da un'espressione
Vedi [Aggiungi un criterio da un'espressione esistente nella pagina delle entità o finalità](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).

## <a name="search-utterances"></a>Espressioni di ricerca
È possibile cercare e filtrare espressioni tramite l'icona della lente di ingrandimento nella barra degli strumenti. 

## <a name="train-your-app-after-changing-model-with-entities"></a>Training dell'app dopo la modifica del modello con entità
Dopo aver aggiunto, modificato o rimosso un'entità, [eseguire il training](luis-how-to-train.md) e [pubblicare](luis-how-to-publish-app.md) l'app affinché le modifiche siano effettive per le query di endpoint. 

## <a name="next-steps"></a>Passaggi successivi
Dopo aver aggiunto finalità, espressioni ed entità, è disponibile un'app LUIS di base. Informazioni su com [eseguire il training](luis-how-to-train.md), [testare](luis-interactive-test.md) e [pubblicare](luis-how-to-publish-app.md) l'app.
 
