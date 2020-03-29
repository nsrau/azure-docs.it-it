---
title: Eseguire il migrazione all'entità appresa dal computer V3Migrate to V3 machine-learned entity
titleSuffix: Azure Cognitive Services
description: La creazione V3 fornisce un nuovo tipo di entità, l'entità appresa dal computer, insieme alla possibilità di aggiungere relazioni all'entità appresa dal computer e ad altre entità o funzionalità dell'applicazione.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: diberry
ms.openlocfilehash: b5dbcd9033d9a41e43ea907d043e0c0486b236db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75563834"
---
# <a name="migrate-to-v3-authoring-entity"></a>Eseguire la migrazione all'entità di creazione di V3Migrate to V3 Authoring entity

La creazione V3 fornisce un nuovo tipo di entità, l'entità appresa dal computer, insieme alla possibilità di aggiungere relazioni all'entità appresa dal computer e ad altre entità o funzionalità dell'applicazione.

## <a name="entities-are-decomposable-in-v3"></a>Le entità sono scomponibili in V3

Le entità create con le API di creazione V3, tramite le [API](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview) o con il portale di [anteprima,](https://preview.luis.ai/)consentono di creare un modello di entità a più livelli con un elemento padre e un elemento figlio. L'elemento padre è noto come **entità appresa** dalla macchina e gli elementi figlio sono noti come **sottocomponenti** dell'entità appresa dalla macchina.

Ogni sottocomponente è anche un'entità appresa dal computer, ma con le opzioni di configurazione aggiunte di vincoli e descrittori.

* **I vincoli** sono regole di corrispondenza del testo esatte che garantiscono che un'entità venga estratta quando corrisponde a una regola. La regola è definita da un'entità di corrispondenza di testo esatto, attualmente: [un'entità predefinita,](luis-reference-prebuilt-entities.md) [un'entità di espressione regolare](reference-entity-regular-expression.md)o [un'entità di elenco](reference-entity-list.md).
* **I descrittori** sono [funzionalità,](luis-concept-feature.md)ad esempio elenchi di frasi o entità, utilizzate per indicare fortemente l'entità.

La creazione V3 fornisce un nuovo tipo di entità, l'entità appresa dal computer, insieme alla possibilità di aggiungere relazioni all'entità appresa dal computer e ad altre entità o funzionalità dell'applicazione.

## <a name="how-do-these-new-relationships-compare-to-v2-authoring"></a>Confronto tra queste nuove relazioni e la creazione V2How do these new relationships compare to V2 authoring

La creazione V2 forniva entità gerarchiche e composite insieme a ruoli e funzionalità per eseguire questa stessa attività. Poiché le entità, le funzionalità e i ruoli non erano esplicitamente correlati tra loro, era difficile comprendere in che modo LUIS implicasse le relazioni durante la stima.

Con V3, la relazione è esplicita e progettata dagli autori dell'app. Ciò consente, in qualità di autore dell'app, di:

* Vedere visivamente come LUIS sta prevedendo queste relazioni, nelle espressioni di esempio
* Verificare queste relazioni con il riquadro di [test interattivo](luis-interactive-test.md) o nell'endpointTest for these relationships either with the interactive test pane or at the endpoint
* Usare queste relazioni nell'applicazione client, tramite un oggetto .json ben strutturato, denominato e annidatoUse these relationships in the client application, via a well structured, named, nested [.json object](reference-entity-machine-learned-entity.md)

## <a name="planning"></a>Pianificazione

Quando si esegue la migrazione, considerare quanto segue nel piano di migrazione:When you migrate, consider the following in your migration plan:

* Eseguire il backup dell'app LUIS ed eseguire la migrazione in un'app separata. La disponibilità di un'app V2 e V3 contemporaneamente consente di convalidare le modifiche necessarie e l'impatto sui risultati della stima.
* Acquisire le metriche di successo della stima corrente
* Acquisire le informazioni correnti del dashboard come snapshot dello stato dell'app
* Esaminare le finalità, le entità, gli elenchi di frasi, i modelli e i test batch esistentiReview existing intents, entities, phrase lists, patterns, and batch tests
* È possibile eseguire la migrazione senza **modifiche**agli elementi seguenti:
    * Finalità
    * Entità
        * Entità di espressione regolare
        * Entità elenco
    * Funzionalità
        * Elenco di frasi
* È necessario eseguire la migrazione dei seguenti elementi **con le modifiche:**
    * Entità
        * Entità gerarchica
        * Entità composita
    * Ruoli: i ruoli possono essere applicati solo a un'entità appresa dal computer (padre). I ruoli non possono essere applicati ai sottocomponenti
    * Test e modelli batch che utilizzano le entità gerarchiche e composite

Quando si progetta il piano di migrazione, lasciare il tempo necessario per esaminare le entità apprese dal computer finali, dopo la migrazione di tutte le entità gerarchiche e composite. Mentre una migrazione diretta funzionerà, dopo aver apportato la modifica e rivedere i risultati dei test batch e la stima JSON, il JSON più unificato può portare ad apportare modifiche in modo che le informazioni finali recapitate all'app lato client siano organizzate in modo diverso. È simile al refactoring del codice e deve essere trattato con lo stesso processo di revisione dell'organizzazione.

Se non si dispone di test batch per il modello V2 ed è possibile eseguire la migrazione dei test batch al modello V3 come parte della migrazione, non sarà possibile convalidare l'impatto della migrazione sui risultati della stima dell'endpoint.

## <a name="migrating-from-v2-entities"></a>Migrazione da entità V2

Quando si inizia a passare al modello di creazione V3, è necessario considerare come passare all'entità appresa dal computer e ai relativi sottocomponenti, inclusi i vincoli e i descrittori.

Nella tabella seguente vengono notate le entità che devono eseguire la migrazione da un progetto di entità V2 a una struttura di entità V3.

|Tipo di entità di creazione V2|Tipo di entità di creazione V3|Esempio|
|--|--|--|
|Entità composita|Entità appresa dalla macchina|[Ulteriori informazioni](#migrate-v2-composite-entity)|
|Entità gerarchica|Ruolo dell'entità appresa automaticamente|[Ulteriori informazioni](#migrate-v2-hierarchical-entity)|

## <a name="migrate-v2-composite-entity"></a>Eseguire la migrazione dell'entità composita V2Migrate V2 Composite entity

Ogni elemento figlio del composito V2 deve essere rappresentato con un sottocomponente dell'entità v3 appresa dal computer. Se l'elemento figlio composito è un'espressione regolare predefinita, un'espressione regolare o un'entità elenco, questo deve essere applicato come **vincolo** al sottocomponente che rappresenta l'elemento figlio.

Considerazioni sulla pianificazione della migrazione di un'entità composita a un'entità appresa dal computer:Considerations when planning to migrate a composite entity to a machine-learned entity:
* Le entità figlio non possono essere utilizzate nei modelli
* Le entità figlio non sono più condivise
* Le entità figlio devono essere etichettate se erano

### <a name="existing-descriptors"></a>Descrittori esistenti

Qualsiasi elenco di frasi utilizzato per aumentare le parole nell'entità composita deve essere applicato come descrittore all'entità appresa dal computer (padre), all'entità del sottocomponente (figlio) o all'intento (se l'elenco di frasi si applica solo a una finalità). Pianificare l'aggiunta del descrittore all'entità che dovrebbe aumentare in modo più significativo. Non aggiungere il descrittore in modo generico all'entità appresa dal computer (padre), se aumenterà in modo più significativo la previsione di un sottocomponente (figlio).

### <a name="new-descriptors"></a>Nuovi descrittori

Nella creazione V3, aggiungere un passaggio di pianificazione per valutare le entità come descrittori possibili per tutte le entità e le finalità.

### <a name="example-entity"></a>Entità di esempio

Questa entità è solo un esempio. La migrazione di entità può richiedere altre considerazioni.

Si consideri un composito V2 per la modifica di una pizza che utilizza:Consider a V2 composite for modifying a pizza `order` that uses:
* precostruito datetimeV2 per il tempo di consegna
* elenco di frasi per aumentare alcune parole come pizza, torta, crosta e topping
* entità elenco per rilevare condimenti come funghi, olive, peperoni.

Un esempio di espressione per questa entità è:An example utterance for this entity is:

`Change the toppings on my pie to mushrooms and delivery it 30 minutes later`

Nella tabella seguente viene illustrata la migrazione:

|Modelli V2|Modelli V3|
|--|--|
|Parent - Entità componente denominata`Order`|Padre - Entità appresa dal computer denominata`Order`|
|Bambino - Precostruito datetimeV2|- Eseguire la migrazione di entità predefinite a una nuova app.<br>Aggiungere vincolo all'elemento padre per datetimeV2 predefinito.|
|Figlio - entità elenco per condimenti|- Eseguire la migrazione dell'entità elenco alla nuova app.<br>Aggiungere quindi un vincolo sull'elemento padre per l'entità elenco.|


## <a name="migrate-v2-hierarchical-entity"></a>Eseguire la migrazione dell'entità gerarchica V2Migrate V2 Hierarchical entity

Nella creazione V2, un'entità gerarchica è stata fornita prima dei ruoli esistenti in LUIS. Entrambi hanno lo stesso scopo di estrarre le entità in base all'utilizzo del contesto. Se si dispone di entità gerarchiche, è possibile considerarle come entità semplici con ruoli.

Nella creazione V3:
* Un ruolo può essere applicato all'entità appresa dal computer (padre).
* Un ruolo non può essere applicato ad alcun sottocomponente.

Questa entità è solo un esempio. La migrazione di entità può richiedere altre considerazioni.

Si consideri un'entità gerarchica V2 per la modifica di una pizza: `order`
* dove ogni bambino determina un condimento originale o il condimento finale

Un esempio di espressione per questa entità è:An example utterance for this entity is:

`Change the topping from mushrooms to olives`

Nella tabella seguente viene illustrata la migrazione:

|Modelli V2|Modelli V3|
|--|--|
|Parent - Entità componente denominata`Order`|Padre - Entità appresa dal computer denominata`Order`|
|Bambino - Entità gerarchica con topping pizza originale e finale|Aggiungi ruolo `Order` a per ogni topping.|

## <a name="next-steps"></a>Passaggi successivi

* [Risorse per sviluppatori](developer-reference-resource.md)
