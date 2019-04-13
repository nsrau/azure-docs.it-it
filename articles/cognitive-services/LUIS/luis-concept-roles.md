---
title: Ruoli per le entità
titleSuffix: Azure Cognitive Services
description: I ruoli sono sottotipi contestuali denominati di un'entità utilizzati solo nei criteri. Ad esempio, nell'espressione `buy a ticket from New York to London`, sia New York sia Londra sono città, ma ciascuna assume un significato diverso all'interno della frase. New York è la città di origine e Londra è la città di destinazione.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 5fa922cb91d34483256faf4dcf70569aa2f17b97
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/12/2019
ms.locfileid: "59522487"
---
# <a name="entity-roles-for-contextual-subtypes"></a>Ruoli di entità per i sottotipi contestuali

I ruoli consentono alle entità a ha assegnato un nome sottotipi. Un ruolo può essere usato con qualsiasi tipo di entità predefiniti o personalizzati e usato in espressioni di esempio e i modelli. 

<a name="example-role-for-entities"></a>
<a name="roles-with-prebuilt-entities"></a>

## <a name="machine-learned-entity-example-of-roles"></a>Entità apprese macchina esempio dei ruoli

Nel utterance "Acquista un ticket da **New York** al **Londra**, New York sia Londra sono città, ma ognuno ha un significato diverso nella frase. New York è la città di origine e Londra è la città di destinazione. 

```
buy a ticket from New York to London
```

I ruoli assegnano un nome a tali differenze:

|Tipo di entità|Nome dell'entità|Ruolo|Scopo|
|--|--|--|--|
|Semplice|Località|origin|luogo in cui parte l'aereo|
|Semplice|Località|destination|luogo in cui atterra l'aereo|

## <a name="non-machine-learned-entity-example-of-roles"></a>Esempio di entità non-machine-appreso dei ruoli

Nel utterance "Pianificare la riunione da 8 a 9", entrambi i numeri indicano un tempo, ma ogni volta che ha un significato diverso nel utterance. Ruoli di specificare il nome per le differenze. 

```
Schedule the meeting from 8 to 9
```

|Tipo di entità|Nome del ruolo|Valore|
|--|--|--|
|DatetimeV2 predefinito|StartTime|8|
|DatetimeV2 predefinito|EndTime|9|

## <a name="are-multiple-entities-in-an-utterance-the-same-thing-as-roles"></a>Sono più entità in un utterance la stessa operazione come ruoli? 

Più entità può esistere in un utterance e può essere estratta senza usare i ruoli. Se il contesto della frase indica con versione dell'entità ha un valore, quindi un ruolo deve essere utilizzato. 

### <a name="dont-use-roles-for-duplicates-without-meaning"></a>Non usare i ruoli per i duplicati senza significato

Se il utterance include un elenco di località, `I want to travel to Seattle, Cairo, and London.`, questo è un elenco in cui ogni elemento non ha un significato aggiuntivo. 

### <a name="use-roles-if-duplicates-indicate-meaning"></a>Usare i ruoli se duplicati indicano significato

Se il utterance include un elenco di località con un significato, `I want to travel from Seattle, with a layover in Londen, landing in Cairo.`, questo significato sosta d'origine e destinazione deve essere acquisito con i ruoli.

### <a name="roles-can-indicate-order"></a>I ruoli possono indicare l'ordine

Se il utterance modificata per indicare l'ordine in cui si desidera estrarre, `I want to first start with Seattle, second London, then third Cairo`, è possibile estrarre in un paio di modi. È possibile contrassegnare i token che indicano il ruolo `first start with`, `second`, `third`. È anche possibile usare entità predefinite **ordinale** e il **GeographyV2** entità predefinite in un'entità composita per acquisire l'idea di ordine e la posizione. 

## <a name="how-are-roles-used-in-example-utterances"></a>Come i ruoli vengono utilizzati in espressioni di esempio?

Quando un'entità dispone di un ruolo e l'entità è contrassegnata in utterance un esempio, è possibile scegliere di selezionare solo le entità, o l'entità e il ruolo. 

Le espressioni di esempio seguenti usano le entità e ruoli:

|Visualizzazione di token|Visualizzazione di entità|
|--|--|
|Mi interessante scoprire di più sulle **Seattle**|Vorrei scoprire di più sulle località {Location}|
|Acquista un ticket da Seattle a New York|Acquistare un ticket da {Location: Origin} per {Location: Destination}|

## <a name="how-are-roles-related-to-hierarchical-entities"></a>Come i ruoli sono correlati alle entità gerarchiche?

I ruoli sono ora disponibili per tutte le entità in espressioni di esempio, nonché l'utilizzo precedente dei modelli. Perché sono disponibili ovunque, sostituiscono la necessità di entità gerarchiche. Nuove entità deve essere creata con i ruoli, invece di usare le entità gerarchiche. 

Entità gerarchiche sarà deprecata.

## <a name="how-are-roles-used-in-patterns"></a>In che modo vengono utilizzati i ruoli nei criteri?
Nell'espressione del modello del criterio, i ruoli vengono utilizzati all'interno dell'espressione: 

|Criteri con ruoli di entità|
|--|
|`buy a ticket from {Location:origin} to {Location:destination}`|


## <a name="role-syntax-in-patterns"></a>Sintassi del ruolo nei criteri
L'entità e il ruolo sono racchiusi tra parentesi, `{}`. L'entità e il ruolo sono separati da due punti. 

## <a name="entity-roles-versus-collaborator-roles"></a>Ruoli di entità e ruoli di collaboratore

I ruoli delle entità si applicano al modello di dati dell'app LUIS. [Collaboratore](luis-concept-collaborator.md) ruoli si applicano ai livelli di accesso di creazione. 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="next-steps"></a>Passaggi successivi

* Usare un [esercitazione pratica](tutorial-entity-roles.md) usando i ruoli di entità con le entità non appreso-computer
* Informazioni su come aggiunge [ruoli](luis-how-to-add-entities.md#add-a-role-to-pattern-based-entity)
