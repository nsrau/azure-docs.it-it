---
title: Eseguire la migrazione a un'entità di Machine Learning V3
description: La creazione di V3 fornisce un nuovo tipo di entità, l'entità Machine Learning, oltre alla possibilità di aggiungere relazioni all'entità Machine Learning e ad altre entità o funzionalità dell'applicazione.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/08/2020
ms.openlocfilehash: 667226770d25ef1687420b1c13bc71863f987e33
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91324689"
---
# <a name="migrate-to-v3-authoring-entity"></a>Eseguire la migrazione all'entità di creazione V3

La creazione di V3 fornisce un nuovo tipo di entità, l'entità Machine Learning, oltre alla possibilità di aggiungere relazioni all'entità Machine Learning e ad altre entità o funzionalità dell'applicazione.

## <a name="entities-are-decomposable-in-v3"></a>Le entità sono decomponibili in V3

Le entità create con le API di creazione di V3, usando le [API](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview) o il portale, consentono di compilare un modello di entità a più livelli con un elemento padre e figlio. L'elemento padre è noto come **entità di Machine Learning** e gli elementi figlio sono noti come **sottoentità** dell'entità appresa dal computer.

Ogni sottoentità è anche un'entità di Machine Learning, ma con le opzioni di configurazione aggiunte delle funzionalità.

* Le **funzionalità necessarie** sono regole che assicurano che un'entità venga estratta quando corrisponde a una funzionalità. La regola è definita dalla funzionalità obbligatoria per il modello:
    * [Entità predefinita](luis-reference-prebuilt-entities.md)
    * [Entità di espressione regolare](reference-entity-regular-expression.md)
    * [Elencare l'entità](reference-entity-list.md).

## <a name="how-do-these-new-relationships-compare-to-v2-authoring"></a>Confronto tra le nuove relazioni e la creazione V2

V2 creazione di entità gerarchiche e composite fornite insieme a ruoli e funzionalità per eseguire questa stessa attività. Poiché le entità, le funzionalità e i ruoli non erano correlati in modo esplicito tra loro, era difficile capire in che modo LUIS implicava le relazioni durante la stima.

Con V3, la relazione è esplicita e progettata dagli autori dell'app. Questo consente, come autore dell'app, di:

* Vedere visivamente il modo in cui LUIS sta stimando queste relazioni, nelle espressioni di esempio
* Testare queste relazioni con il riquadro di [test interattivo](luis-interactive-test.md) o nell'endpoint
* Usare queste relazioni nell'applicazione client tramite un [oggetto con estensione JSON](reference-entity-machine-learned-entity.md) annidato e ben strutturato

## <a name="planning"></a>Pianificazione

Quando si esegue la migrazione, tenere presente quanto segue nel piano di migrazione:

* Eseguire il backup dell'app LUIS ed eseguire la migrazione in un'app separata. La disponibilità di un'app V2 e V3 allo stesso tempo consente di convalidare le modifiche necessarie e l'effetto sui risultati della stima.
* Acquisisci le metriche di successo delle stime correnti
* Acquisisci le informazioni correnti sul dashboard come snapshot dello stato dell'app
* Esaminare gli Intent, le entità, gli elenchi di frasi, i modelli e i test batch esistenti
* È possibile eseguire la migrazione degli elementi seguenti **senza modifiche**:
    * Finalità
    * Entità
        * Entità di espressione regolare
        * Entità elenco
    * Funzionalità
        * Elenco di frasi
* È necessario eseguire la migrazione degli elementi seguenti **con le modifiche**:
    * Entità
        * Entità gerarchica
        * Entità composita
    * Roles: i ruoli possono essere applicati solo a un'entità di Machine Learning (padre). Non è possibile applicare i ruoli alle sottoentità
    * Test e modelli batch che usano le entità gerarchiche e composite

Quando si progetta il piano di migrazione, lasciare il tempo necessario per rivedere le entità di Machine Learning finali, dopo la migrazione di tutte le entità gerarchiche e composite. Mentre è possibile eseguire una migrazione diretta, dopo aver apportato la modifica ed esaminato i risultati dei test di batch e la stima JSON, il JSON più unificato può comportare la modifica in modo che le informazioni finali inviate all'app sul lato client siano organizzate in modo diverso. Questa operazione è simile al refactoring del codice e deve essere trattata con lo stesso processo di revisione dell'organizzazione.

Se non si dispone di test batch per il modello V2 e si esegue la migrazione dei test batch al modello V3 come parte della migrazione, non sarà possibile convalidare il modo in cui la migrazione influirà sui risultati della stima dell'endpoint.

## <a name="migrating-from-v2-entities"></a>Migrazione da entità V2

Quando si inizia a passare al modello di creazione e modifica V3, è necessario considerare come passare all'entità Machine Learning e alle relative sottoentità e funzionalità.

Nella tabella seguente sono riportate le entità di cui è necessario eseguire la migrazione da V2 a una progettazione di entità V3.

|V2 tipo di entità di creazione|Tipo di entità authoring V3|Esempio|
|--|--|--|
|Entità composita|Entità appresa dal computer|[Ulteriori informazioni](#migrate-v2-composite-entity)|
|Entità gerarchica|ruolo dell'entità Machine Learning|[Ulteriori informazioni](#migrate-v2-hierarchical-entity)|

## <a name="migrate-v2-composite-entity"></a>Esegui migrazione dell'entità composita V2

Ogni elemento figlio del composto V2 deve essere rappresentato con una sottoentità dell'entità Machine Learning V3. Se l'elemento figlio composito è un'entità predefinita, un'espressione regolare o un elenco, è necessario applicarla come funzionalità obbligatoria nella sottoentità.

Considerazioni sulla pianificazione della migrazione di un'entità composita a un'entità di Machine Learning:
* Le entità figlio non possono essere utilizzate nei modelli
* Le entità figlio non sono più condivise
* Le entità figlio devono essere etichettate se non sono state apprese da computer

### <a name="existing-features"></a>Funzionalità esistenti

Qualsiasi elenco di frasi usato per aumentare le parole nell'entità composita deve essere applicato come funzionalità all'entità di Machine Learning (padre), all'entità di sottoentità (figlio) o allo scopo (se l'elenco di frasi si applica solo a uno scopo). Pianificare l'aggiunta della funzionalità all'entità in cui deve essere incrementata significativamente. Non aggiungere la funzionalità in modo generico all'entità di Machine Learning (padre), se aumenterà significativamente la stima di una sottoentità (figlio).

### <a name="new-features"></a>Nuove funzionalità

Nella creazione di V3 aggiungere un passaggio di pianificazione per valutare le entità come possibili funzionalità per tutte le entità e gli Intent.

### <a name="example-entity"></a>Entità di esempio

Questa entità è solo un esempio. La migrazione delle entità potrebbe richiedere altre considerazioni.

Prendere in considerazione un composto V2 per modificare una pizza `order` che usa:
* datetimeV2 predefiniti per i tempi di recapito
* elenco di frasi per migliorare determinate parole, ad esempio Pizza, torta, crosta e topping
* elencare l'entità per rilevare i superping, ad esempio funghi, olive, peperoni.

Un enunciato di esempio per questa entità è:

`Change the toppings on my pie to mushrooms and delivery it 30 minutes later`

Nella tabella seguente viene illustrata la migrazione:

|Modelli v2|Modelli V3|
|--|--|
|Entità componente padre denominata `Order`|Entità padre-Machine-Learning denominata `Order`|
|DatetimeV2 precompilata figlio|* Eseguire la migrazione di un'entità predefinita a una nuova app.<br>* Aggiungere la funzionalità richiesta nell'elemento padre per datetimeV2 predefiniti.|
|Entità elenco elementi figlio per i superping|* Eseguire la migrazione dell'entità elenco alla nuova app.<br>* Aggiungere quindi una funzionalità obbligatoria nell'elemento padre per l'entità List.|


## <a name="migrate-v2-hierarchical-entity"></a>Migrare entità gerarchica V2

Nella creazione V2 è stata fornita un'entità gerarchica prima dei ruoli esistenti in LUIS. Entrambi hanno servito lo stesso scopo dell'estrazione delle entità in base all'utilizzo del contesto. Se si dispone di entità gerarchiche, è possibile considerarle come entità semplici con ruoli.

Nella creazione di V3:
* Un ruolo può essere applicato all'entità di Machine Learning (padre).
* Non è possibile applicare un ruolo ad alcuna sottoentità.

Questa entità è solo un esempio. La migrazione delle entità potrebbe richiedere altre considerazioni.

Prendere in considerazione un'entità gerarchica V2 per la modifica di una pizza `order` :
* dove ogni elemento figlio determina un topping originale o il topping finale

Un enunciato di esempio per questa entità è:

`Change the topping from mushrooms to olives`

Nella tabella seguente viene illustrata la migrazione:

|Modelli v2|Modelli V3|
|--|--|
|Entità componente padre denominata `Order`|Entità padre-Machine-Learning denominata `Order`|
|Entità figlio-gerarchica con topping iniziale e finale della pizza|* Aggiungere un ruolo a `Order` per ogni topping.|

## <a name="api-change-constraint-replaced-with-required-feature"></a>Vincolo di modifica API sostituito con la funzionalità richiesta

Questa modifica è stata apportata nel maggio 2020 della conferenza Build e si applica solo alle API di creazione di V3 in cui un'app usa una funzionalità vincolata. Se si esegue la migrazione dalla creazione V2 alla versione V3 o non sono state usate le funzionalità vincolate V3, ignorare questa sezione.

**Funzionalità** : è possibile richiedere un'entità esistente come funzionalità a un altro modello ed estrarre tale modello solo se viene rilevata l'entità. La funzionalità non è stata modificata, ma l'API e la terminologia sono state modificate.

|Terminologia precedente|Nuova terminologia|
|--|--|
|`constrained feature`<br>`constraint`<br>`instanceOf`|`required feature`<br>`isRequired`|

#### <a name="automatic-migration"></a>Migrazione automatica

A partire dal **19 2020 giugno**non sarà consentito creare vincoli a livello di codice usando l'API di creazione precedente che ha esposto questa funzionalità.

Tutte le funzionalità di vincolo esistenti verranno automaticamente migrate al flag di funzionalità obbligatorio. Non sono necessarie modifiche a livello di codice per l'API di stima e nessuna modifica risultante sulla qualità dell'accuratezza della stima.

#### <a name="luis-portal-changes"></a>Modifiche al portale LUIS

Il portale di anteprima LUIS ha fatto riferimento a questa funzionalità come **vincolo**. Il portale LUIS corrente designa questa funzionalità come **funzionalità obbligatoria**.

#### <a name="previous-authoring-api"></a>API di creazione precedente

Questa funzionalità è stata applicata nell'anteprima creazione dell' **[API figlio crea entità](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5d86cf3c6a25a45529767d77)** come parte della definizione di un'entità, usando la `instanceOf` proprietà dell'elemento figlio di un'entità:

```json
{
    "name" : "dayOfWeek",
    "instanceOf": "datetimeV2",
    "children": [
        {
           "name": "dayNumber",
           "instanceOf": "number",
           "children": []
        }
    ]
}
```

#### <a name="new-authoring-api"></a>Nuova API di creazione

Questa funzionalità viene ora applicata con l' **[API Aggiungi relazione](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5d9dc1781e38aaec1c375f26)** tra le funzionalità dell'entità usando le `featureName` `isRequired` proprietà e. Il valore della `featureName` proprietà è il nome del modello.

```json
{
    "featureName": "YOUR-MODEL-NAME-HERE",
    "isRequired" : true
}
```


## <a name="next-steps"></a>Passaggi successivi

* [Risorse per sviluppatori](developer-reference-resource.md)
