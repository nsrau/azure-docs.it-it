---
title: Esercitazione usando i criteri per migliorare le stime LUIS - Azure | Microsoft Docs
titleSuffix: Azure
description: In questa esercitazione usare i criteri per le finalità per migliorare le stime di finalità ed entità LUIS.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr;
ms.openlocfilehash: ff5572366be548132b28e5ce03b9595e7f98128c
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265317"
---
# <a name="tutorial-use-patterns-to-improve-predictions"></a>Esercitazione: Usare i criteri per migliorare le stime

In questa esercitazione usare i criteri per le finalità per migliorare le stime di finalità ed entità LUIS.  

> [!div class="checklist"]
* Come capire che un criterio potrebbe essere utile per l'app
* Come creare un criterio 
* Come usare entità predefinite e personalizzate in un criterio 
* Come verificare i miglioramenti di stima del criterio
* Come aggiungere un ruolo a un'entità per trovare entità basate su contesto
* Come aggiungere Pattern.any per trovare entità in formato libero

Per questo articolo, è necessario disporre di un account [LUIS][LUIS] gratuito per creare l'applicazione LUIS.

## <a name="import-humanresources-app"></a>Importare l'app HumanResources
Questa esercitazione importa un'app HumanResources. L'app dispone di tre finalità: None, GetEmployeeOrgChart, GetEmployeeBenefits. L'app dispone di due entità: Prebuilt number (numero predefinito) e Employee (Dipendente). L'entità Employee (Dipendente) è un'entità semplice per estrarre il nome di un dipendente. 

1. Creare un nuovo file di app LUIS e nominarlo `HumanResources.json`. 

2. Copiare la definizione dell'app seguente nel file:

   [!code-json[Add the LUIS model](~/samples-luis/documentation-samples/tutorial-patterns/HumanResources.json?range=1-164 "Add the LUIS model")]

3. Nella pagina **Apps** (App) LUIS selezionare **Import new app** (Importa nuova app). 

4. Nella finestra di dialogo **Import new app** (Importa nuova app) selezionare il file `HumanResources.json` creato al passaggio 1.

5. Selezionare la finalità **GetEmployeeOrgChart**, quindi passare da **Entities view** (Visualizzazione entità) a **Tokens view** (Visualizzazione token). Sono elencate diverse espressioni di esempio. Ogni espressione contiene un nome, ovvero un'entità Employee (Dipendente). Si noti che ogni nome è diverso e che la disposizione delle parole varia a seconda dell'espressione. Questa diversità consente a LUIS di apprendere un'ampia gamma di espressioni.

    ![Schermata della pagina Intent (Finalità) con la visualizzazione Entities (Entità) attivata](media/luis-tutorial-pattern/utterances-token-view.png)

6. Selezionare **Train** (Esegui il training) nella barra di spostamento superiore per eseguire il training dell'app. Attendere la barra verde che segnala il completamento dell'operazione.

7. Selezionare **Test** nel pannello superiore. Immettere `Who does Patti Owens report to?` e selezionare invio. Selezionare **Inspect** (Ispeziona) sotto l'espressione per visualizzare ulteriori informazioni sul test.
    
    Il nome del dipendente, Patti Owens, non è ancora stato usato in un'espressione di esempio. Questo test ha lo scopo di capire fino a che punto LUIS abbia appreso che questa espressione è destinata alla finalità `GetEmployeeOrgChart` e che l'entità Employee (Dipendente) sia `Patti Owens`. Il risultato deve essere inferiore al 50% (0,50) per la finalità `GetEmployeeOrgChart`. Sebbene la finalità sia corretta, il punteggio è basso. L'entità Employee (Dipendente) è correttamente identificata come `Patti Owens`. I criteri aumentano questo punteggio di stima iniziale. 

    ![Schermata del pannello Test](media/luis-tutorial-pattern/original-test.png)

8. Chiudere il pannello test selezionando il pulsante **Test** nella barra di spostamento superiore. 

## <a name="patterns-teach-luis-common-utterances-with-fewer-examples"></a>I criteri insegnano a LUIS espressioni comuni con un minor numero di esempi
Data la natura del dominio risorse umane, esistono alcuni modi comuni di porre domande sulle relazioni dei dipendenti all'interno delle organizzazioni. Ad esempio: 

```
Who does Mike Jones report to?
Who reports to Mike Jones? 
```

Queste espressioni sono troppo simili per determinare l'univocità contestuale di ciascuna senza fornire molte espressioni di esempio. Aggiungendo un criterio per una finalità, LUIS apprende criteri di espressioni comuni per una finalità senza fornire molte espressioni di esempio. 

Le espressioni modello di esempio per questa finalità includono:

```
Who does {Employee} report to?
Who reports to {Employee}? 
```

Il criterio è una combinazione di corrispondenza a espressioni regolari e apprendimento automatico. Fornire alcuni esempi di espressioni modello per LUIS per apprendere il criterio. Questi esempi, insieme alle espressioni della finalità, offrono a LUIS una migliore comprensione delle espressioni appropriate per la finalità e della posizione, all'interno dell'espressione, in cui si trova l'entità. <!--A pattern is specific to an intent. You can't duplicate the same pattern on another intent. That would confuse LUIS, which lowers the prediction score. -->

## <a name="add-the-template-utterances"></a>Aggiungere espressioni modello

1. Nel riquadro di spostamento sinistro, sotto **Improve app performance** (Migliora le prestazioni dell'app), selezionare **Patterns** (Criteri).

2. Selezionare la finalità **GetEmployeeOrgChart**, quindi immettere le espressioni modello seguenti, una alla volta, selezionando invio dopo ognuna:

    ```
    Does {Employee} have {number} subordinates?
    Does {Employee} have {number} direct reports?
    Who does {Employee} report to?
    Who reports to {Employee}?
    Who is {Employee}'s manager?
    Who are {Employee}'s subordinates?
    ```

    La sintassi `{Employee}` contrassegna la posizione dell'entità nell'espressione modello e specifica di quale entità si tratta. 

    ![Schermata di immissione di espressioni modello per la finalità](./media/luis-tutorial-pattern/enter-pattern.png)

3. Selezionare **Train** (Esegui il training) nella barra di spostamento superiore. Attendere la barra verde che segnala il completamento dell'operazione.

4. Selezionare **Test** nel pannello superiore. Immettere `Who does Patti Owens report to?` nella casella di testo. Selezionare Invio. Si tratta della stessa espressione testata nella sezione precedente. Il risultato dovrebbe essere superiore per la finalità `GetEmployeeOrgChart`. 

    Il punteggio è ora migliore. LUIS ha appreso il criterio rilevante per la finalità senza fornire molti esempi.

    ![Schermata del pannello Test con punteggio alto](./media/luis-tutorial-pattern/high-score.png)

    Prima viene trovata l'entità, poi il criterio a indicare la finalità. Se nel risultato del test l'entità non viene rilevata e di conseguenza il criterio non viene trovato, è necessario aggiungere un maggior numero di espressioni di esempio sulla finalità (non il criterio). 

5. Chiudere il pannello test selezionando il pulsante **Test** nella barra di spostamento superiore.

## <a name="use-an-entity-with-a-role-in-a-pattern"></a>Usare un'entità con un ruolo in un criterio
L'app LUIS viene usata per spostare i dipendenti da una posizione a un'altra. Un'espressione di esempio è `Move Bob Jones from Seattle to Los Colinas`. Ogni posizione nell'espressione ha un significato diverso. Seattle è la posizione di origine e Los Colinas è la posizione di destinazione per lo spostamento. Per poter distinguere le posizioni nel criterio, nelle sezioni seguenti si crea un'entità semplice per la posizione con due ruoli: origine e destinazione. 

### <a name="create-a-new-intent-for-moving-people-and-assets"></a>Creare una nuova finalità per spostare persone e asset
Creare una nuova finalità per qualsiasi espressione che riguardi lo spostamento di persone o asset.

1. Selezionare **Intents** (Finalità) dal riquadro di spostamento sinistro
2. Selezionare **Create new intent** (Crea nuova finalità)
3. Assegnare un nome alla nuova finalità `MoveAssetsOrPeople`
4. Aggiungere espressioni di esempio:

    ```
    Move Bob Jones from Seattle to Los Colinas
    Move Dave Cooper from Redmond to Seattle
    Move Jim Smith from Toronto to Vancouver
    Move Jill Benson from Boston to London
    Move Travis Hinton from Portland to Orlando
    ```
    ![Schermata dell'espressione di esempio per la finalità MoveAssetsOrPeople](./media/luis-tutorial-pattern/intent-moveasserts-example-utt.png)

    Lo scopo delle espressioni di esempio è fornire un numero sufficiente di esempi. Se più avanti nel test, l'entità posizione non viene rilevata e di conseguenza non viene rilevato neanche il criterio, tornare a questo passaggio e aggiungere altri esempi. Eseguire nuovamente il training e il test. 

5. Contrassegnare le entità nelle espressioni di esempio con l'entità Employee (Dipendente) selezionando il nome e il cognome in un'espressione, quindi selezionare l'entità Employee (Dipendente) nell'elenco.

    ![Schermata delle espressioni in MoveAssetsOrPeople contrassegnate con l'entità Employee (Dipendente)](./media/luis-tutorial-pattern/intent-moveasserts-employee.png)

6. Selezionare il testo `portland` nell'espressione `move travis hinton from portland to orlando`. Nella finestra di dialogo popup immettere il nome della nuova entità `Location` e selezionare **Create new entity** (Crea nuova entità). Scegliere il tipo di entità **Simple** (Semplice) e selezionare **Done** (Fine).

    ![Schermata della creazione della nuova entità posizione](./media/luis-tutorial-pattern/create-new-location-entity.png)

    Contrassegnare il resto dei nomi di posizione nelle espressioni. 

    ![Schermata di tutte le entità contrassegnate](./media/luis-tutorial-pattern/moveasset-all-entities-labeled.png)

    Il criterio della scelta e dell'ordine delle parole è ovvio nell'immagine precedente. Se **non** si usano i criteri e le espressioni sulla finalità presentano un criterio ovvio, valutare la possibilità di usarli. 

    Se si prevede un'ampia gamma di espressioni, anziché un criterio, le espressioni di esempio sono quelle errate. In questo caso, sarebbe preferibile disporre di espressioni diverse nella scelta dei termini o delle parole, nella lunghezza e nel posizionamento delle entità. 

<!--TBD: what guidance to move from hier entities to patterns with roles -->
<!--    The [Hierarchical entity quickstart](luis-quickstart-intent-and-hier-entity.md) uses the  same idea of location but uses child entities to find origin and destination locations. 
-->
### <a name="add-role-to-location-entity"></a>Aggiungere un ruolo all'entità posizione 
I ruoli possono essere usati solo per i criteri. Aggiungere i ruoli origine e destinazione all'entità posizione. 

1. Selezionare **Entities** (Entità) nel riquadro di spostamento sinistro, quindi **Location** (Posizione) dall'elenco di entità.

2. Aggiungere i ruoli `Origin` e `Destination` all'entità.

    ![Schermata della nuova entità con ruoli](./media/luis-tutorial-pattern/location-entity.png)

    I ruoli non vengono contrassegnati nella pagina della finalità MoveAssetsOrPeople perché i ruoli non esistono nelle espressioni della finalità. Esistono solo nelle espressioni modello del criterio. 

### <a name="add-template-utterances-that-uses-location-and-destination-roles"></a>Aggiungere espressioni modello che usino i ruoli posizione e destinazione
Aggiungere espressioni modello che usino la nuova entità.

1. Selezionare **Patterns** (Criteri) dal riquadro di spostamento sinistro.

2. Selezionare la finalità **MoveAssetsOrPeople**.

3. Immettere una nuova espressione modello usando la nuova entità `Move {Employee} from {Location:Origin} to {Location:Destination}`. La sintassi per un'entità e il ruolo all'interno di un'espressione modello è `{entity:role}`.

    ![Schermata della nuova entità con ruoli](./media/luis-tutorial-pattern/pattern-moveassets.png)

4. Eseguire il training dell'app per finalità, entità e criterio nuovi.

### <a name="test-the-new-pattern-for-role-data-extraction"></a>Testare il nuovo criterio per l'estrazione dei dati del ruolo
Convalidare il nuovo criterio con un test.

1. Selezionare **Test** nel pannello superiore. 
2. Immettere l'espressione `Move Tammi Carlson from Bellingham to Winthrop`.
3. Selezionare **Inspect** (Ispeziona) sotto il risultato per visualizzare i risultati del test per entità e finalità.

    ![Schermata della nuova entità con ruoli](./media/luis-tutorial-pattern/test-with-roles.png)

    Prima vengono trovate le entità, poi il criterio a indicare la finalità. Se nel risultato di un test le entità non vengono rilevate e di conseguenza il criterio non viene trovato, è necessario aggiungere un maggior numero di espressioni di esempio sulla finalità (non il criterio). 

4. Chiudere il pannello test selezionando il pulsante **Test** nella barra di spostamento superiore.

## <a name="use-a-patternany-entity-to-find-free-form-entities-in-a-pattern"></a>Usare un'entità Pattern.any per trovare entità in formato libero in un criterio
Questa app HumanResources aiuta i dipendenti a trovare i moduli aziendali. Molti dei moduli dispongono di titoli di diversa lunghezza. La lunghezza variabile include frasi che potrebbero confondere LUIS circa la fine del nome del modulo. Usando un'entità **Pattern.any** in un criterio è possibile specificare l'inizio e la fine del nome del modulo in modo che LUIS lo estragga correttamente. 

### <a name="create-a-new-intent-for-the-form"></a>Creare una nuova finalità per il modulo
Creare una nuova finalità per le espressioni che cercano i moduli.

1. Selezionare **Intents** (Finalità) dal riquadro di spostamento sinistro.

2. Selezionare **Create new intent** (Crea nuova finalità).

3. Immettere il nome della nuova finalità `FindForm`.

4. Aggiungere un'espressione di esempio.

    ```
    `Where is the form What to do when a fire breaks out in the Lab and who needs to sign it after I read it?`
    ```

    ![Schermata della nuova entità con ruoli](./media/luis-tutorial-pattern/intent-findform.png)

    Il titolo del modulo è `What to do when a fire breaks out in the Lab`. L'espressione chiede la posizione del modulo e chi deve firmarlo confermando il dipendente che lo legge. Senza un'entità Pattern.any, sarebbe difficile capire dove finisce il titolo del modulo ed estrarre il titolo del modulo come un'entità dell'espressione.

### <a name="create-a-patternany-entity-for-the-form-title"></a>Creare un'entità Pattern.any per il titolo del modulo
L'entità Pattern.any consente entità di diversa lunghezza. Funziona solo in un criterio perché il criterio contrassegna l'inizio e la fine dell'entità. Se si rileva che il criterio, quando include Pattern.any, estrae le entità in modo errato, usare un [elenco esplicito](luis-concept-patterns.md#explicit-lists) per risolvere il problema. 

1. Selezionare **Entities** (Entità) nel riquadro di spostamento sinistro.

2. Selezionare **Create new entity** (Crea nuova entità). 

3. Assegnare un nome all'entità `FormName` con il tipo **Pattern.any**. Per questa specifica esercitazione, non è necessario aggiungere ruoli all'entità.

    ![Immagine della finestra di dialogo per nome entità e tipo entità](./media/luis-tutorial-pattern/create-entity-pattern-any.png)

### <a name="add-a-pattern-that-uses-the-patternany"></a>Aggiungere un criterio che usa Pattern.any

1. Selezionare **Patterns** (Criteri) dal riquadro di spostamento sinistro.

2. Selezionare la finalità **FindForm**.

3. Immettere una nuova espressione modello usando la nuova entità `Where is the form {FormName} and who needs to sign it after I read it?`

    ![Schermata dell'espressione modello usando l'entità pattern.any](./media/luis-tutorial-pattern/pattern.any-template-utterance.png)

4. Eseguire il training dell'app per finalità, entità e criterio nuovi.

### <a name="test-the-new-pattern-for-free-form-data-extraction"></a>Testare il nuovo criterio per l'estrazione dei dati in formato libero
1. Selezionare **Test** nella barra superiore per aprire il pannello test. 

2. Immettere l'espressione `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`.

3. Selezionare **Inspect** (Ispeziona) sotto il risultato per visualizzare i risultati del test per entità e finalità.

    ![Schermata dell'espressione modello usando l'entità pattern.any](./media/luis-tutorial-pattern/test-pattern.any-results.png)

    Prima viene trovata l'entità, poi il criterio a indicare la finalità. Se nel risultato di un test le entità non vengono rilevate e di conseguenza il criterio non viene trovato, è necessario aggiungere un maggior numero di espressioni di esempio sulla finalità (non il criterio).

4. Chiudere il pannello test selezionando il pulsante **Test** nella barra di spostamento superiore.

## <a name="clean-up-resources"></a>Pulire le risorse
Quando non è più necessaria, eliminare l'app LUIS. A tale scopo, selezionare il menu con i puntini di sospensione (...) a destra del nome nell'elenco di app, quindi selezionare **Delete** (Elimina). Nella finestra di dialogo popup **Delete app?** (Eliminare l'app?) selezionare **OK**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Usare l'elenco di frasi per migliorare la stima](luis-tutorial-interchangeable-phrase-list.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions