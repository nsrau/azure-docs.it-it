---
title: Eseguire la migrazione a un'entità appresa dal computer V3
titleSuffix: Azure Cognitive Services
description: La creazione di V3 fornisce un nuovo tipo di entità, l'entità appresa dal computer, oltre alla possibilità di aggiungere relazioni all'entità appresa dal computer e ad altre entità o funzionalità dell'applicazione.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: diberry
ms.openlocfilehash: b5dbcd9033d9a41e43ea907d043e0c0486b236db
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75563834"
---
# <a name="migrate-to-v3-authoring-entity"></a>Eseguire la migrazione all'entità di creazione V3

La creazione di V3 fornisce un nuovo tipo di entità, l'entità appresa dal computer, oltre alla possibilità di aggiungere relazioni all'entità appresa dal computer e ad altre entità o funzionalità dell'applicazione.

## <a name="entities-are-decomposable-in-v3"></a>Le entità sono decomponibili in V3

Le entità create con le API di creazione di V3, usando le [API](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview) o il [portale di anteprima](https://preview.luis.ai/), consentono di compilare un modello di entità a più livelli con un elemento padre e figlio. L'elemento padre è noto come **entità appresa dal computer** e gli elementi figlio sono noti come **sottocomponenti** dell'entità appresa dal computer.

Ogni sottocomponente è anche un'entità appresa dal computer, ma con le opzioni di configurazione aggiunte di vincoli e descrittori.

* I **vincoli** sono regole di corrispondenza esatte del testo che garantiscono che un'entità venga estratta quando corrisponde a una regola. La regola è definita da un'entità corrispondente esatta del testo, attualmente: un'entità [predefinita](luis-reference-prebuilt-entities.md), un' [entità di espressione regolare](reference-entity-regular-expression.md)o un' [entità di elenco](reference-entity-list.md).
* I **descrittori** sono [funzionalità](luis-concept-feature.md), ad esempio elenchi di frasi o entità, che vengono usate per indicare chiaramente l'entità.

La creazione di V3 fornisce un nuovo tipo di entità, l'entità appresa dal computer, oltre alla possibilità di aggiungere relazioni all'entità appresa dal computer e ad altre entità o funzionalità dell'applicazione.

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
    * Caratteristiche
        * Elenco di frasi
* È necessario eseguire la migrazione degli elementi seguenti **con le modifiche**:
    * Entità
        * Entità gerarchica
        * Entità composita
    * Roles: i ruoli possono essere applicati solo a un'entità con apprendimento automatico (padre). I ruoli non possono essere applicati ai sottocomponenti
    * Test e modelli batch che usano le entità gerarchiche e composite

Quando si progetta il piano di migrazione, lasciare il tempo necessario per rivedere le entità apprese dal computer finale, dopo la migrazione di tutte le entità gerarchiche e composite. Mentre è possibile eseguire una migrazione diretta, dopo aver apportato la modifica ed esaminato i risultati dei test di batch e la stima JSON, il JSON più unificato può comportare la modifica in modo che le informazioni finali inviate all'app sul lato client siano organizzate in modo diverso. Questa operazione è simile al refactoring del codice e deve essere trattata con lo stesso processo di revisione dell'organizzazione.

Se non si dispone di test batch per il modello V2 e si esegue la migrazione dei test batch al modello V3 come parte della migrazione, non sarà possibile convalidare il modo in cui la migrazione influirà sui risultati della stima dell'endpoint.

## <a name="migrating-from-v2-entities"></a>Migrazione da entità V2

Quando si inizia a passare al modello di creazione e modifica V3, è necessario considerare come spostarsi nell'entità appresa dal computer e nei relativi sottocomponenti, inclusi i vincoli e i descrittori.

Nella tabella seguente sono riportate le entità di cui è necessario eseguire la migrazione da V2 a una progettazione di entità V3.

|V2 tipo di entità di creazione|Tipo di entità authoring V3|Esempio|
|--|--|--|
|Entità composita|Entità appresa dal computer|[Ulteriori informazioni](#migrate-v2-composite-entity)|
|Entità gerarchica|Ruolo dell'entità Machine-Learned|[Ulteriori informazioni](#migrate-v2-hierarchical-entity)|

## <a name="migrate-v2-composite-entity"></a>Esegui migrazione dell'entità composita V2

Ogni elemento figlio del composto V2 deve essere rappresentato con un sottocomponente dell'entità Machine-Learn V3. Se l'elemento figlio composito è un'entità predefinita, un'espressione regolare o un elenco, questo attributo deve essere applicato come **vincolo** sul sottocomponente che rappresenta l'elemento figlio.

Considerazioni sulla pianificazione della migrazione di un'entità composita a un'entità appresa dal computer:
* Le entità figlio non possono essere utilizzate nei modelli
* Le entità figlio non sono più condivise
* Le entità figlio devono essere etichettate se non sono state apprese da computer

### <a name="existing-descriptors"></a>Descrittori esistenti

Qualsiasi elenco di frasi usato per aumentare le parole nell'entità composita deve essere applicato come descrittore all'entità (padre) appresa dal computer, all'entità del sottocomponente (figlio) o allo scopo (se l'elenco di frasi si applica solo a uno scopo). Pianificare l'aggiunta del descrittore all'entità che dovrebbe migliorare significativamente. Non aggiungere il descrittore in modo generico all'entità Machine-learned (Parent), se aumenterà significativamente la stima di un sottocomponente (figlio).

### <a name="new-descriptors"></a>Nuovi descrittori

Nella creazione di V3, aggiungere un passaggio di pianificazione per valutare le entità come possibili descrittori per tutte le entità e gli Intent.

### <a name="example-entity"></a>Entità di esempio

Questa entità è solo un esempio. La migrazione delle entità potrebbe richiedere altre considerazioni.

Prendere in considerazione un composto V2 per modificare `order` una pizza che usa:
* datetimeV2 predefiniti per i tempi di recapito
* elenco di frasi per migliorare determinate parole, ad esempio Pizza, torta, crosta e topping
* elencare l'entità per rilevare i superping, ad esempio funghi, olive, peperoni.

Un enunciato di esempio per questa entità è:

`Change the toppings on my pie to mushrooms and delivery it 30 minutes later`

Nella tabella seguente viene illustrata la migrazione:

|Modelli v2|Modelli V3|
|--|--|
|Entità componente padre denominata`Order`|Entità padre-Machine-Learn denominata`Order`|
|DatetimeV2 precompilata figlio|* Eseguire la migrazione di un'entità predefinita a una nuova app.<br>* Aggiungere un vincolo nell'elemento padre per datetimeV2 predefiniti.|
|Entità elenco elementi figlio per i superping|* Eseguire la migrazione dell'entità elenco alla nuova app.<br>* Aggiungere quindi un vincolo nell'elemento padre per l'entità List.|


## <a name="migrate-v2-hierarchical-entity"></a>Migrare entità gerarchica V2

Nella creazione V2 è stata fornita un'entità gerarchica prima dei ruoli esistenti in LUIS. Entrambi hanno servito lo stesso scopo dell'estrazione delle entità in base all'utilizzo del contesto. Se si dispone di entità gerarchiche, è possibile considerarle come entità semplici con ruoli.

Nella creazione di V3:
* Un ruolo può essere applicato nell'entità di apprendimento automatico (padre).
* Non è possibile applicare un ruolo a tutti i sottocomponenti.

Questa entità è solo un esempio. La migrazione delle entità potrebbe richiedere altre considerazioni.

Prendere in considerazione un'entità gerarchica V2 per la `order`modifica di una pizza:
* dove ogni elemento figlio determina un topping originale o il topping finale

Un enunciato di esempio per questa entità è:

`Change the topping from mushrooms to olives`

Nella tabella seguente viene illustrata la migrazione:

|Modelli v2|Modelli V3|
|--|--|
|Entità componente padre denominata`Order`|Entità padre-Machine-Learn denominata`Order`|
|Entità figlio-gerarchica con topping iniziale e finale della pizza|* Aggiungere un ruolo `Order` a per ogni topping.|

## <a name="next-steps"></a>Passaggi successivi

* [Risorse per sviluppatori](developer-reference-resource.md)
