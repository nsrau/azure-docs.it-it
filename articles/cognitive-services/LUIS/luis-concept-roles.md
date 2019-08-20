---
title: Ruoli per entità-LUIS
titleSuffix: Azure Cognitive Services
description: I ruoli sono sottotipi contestuali denominati di un'entità utilizzati solo nei criteri. Ad esempio, nell'espressione `buy a ticket from New York to London`, sia New York sia Londra sono città, ma ciascuna assume un significato diverso all'interno della frase. New York è la città di origine e Londra è la città di destinazione.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: b4bd61ea74055a04718d8a9d8d5ccd42671af2ac
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638341"
---
# <a name="entity-roles-for-contextual-subtypes"></a>Ruoli di entità per sottotipi contestuali

I ruoli consentono alle entità di avere sottotipi denominati. Un ruolo può essere usato con qualsiasi tipo di entità predefinita o personalizzata, e può essere usato sia nelle espressioni che nei modelli di esempio. 

<a name="example-role-for-entities"></a>
<a name="roles-with-prebuilt-entities"></a>

## <a name="machine-learned-entity-example-of-roles"></a>Esempio di entità Machine-learned Roles

Nel termine "acquistare un biglietto da **New York** a **Londra**, sia New York che Londra sono città, ma ognuna ha un significato diverso nella frase. New York è la città di origine e Londra è la città di destinazione. 

```
buy a ticket from New York to London
```

I ruoli assegnano un nome a tali differenze:

|Tipo di entità|Nome dell'entità|Role|Scopo|
|--|--|--|--|
|Semplice|Location|origine|luogo in cui parte l'aereo|
|Semplice|Location|destinazione|luogo in cui atterra l'aereo|

## <a name="non-machine-learned-entity-example-of-roles"></a>Esempio di entità non Machine-learned dei ruoli

Nell'espressione "pianificare la riunione da 8 a 9", entrambi i numeri indicano un'ora, ma ogni volta ha un significato diverso nell'espressione. I ruoli forniscono il nome per le differenze. 

```
Schedule the meeting from 8 to 9
```

|Tipo di entità|Nome del ruolo|Value|
|--|--|--|
|DatetimeV2 predefinito|StartTime|8|
|DatetimeV2 predefinito|EndTime|9|

## <a name="are-multiple-entities-in-an-utterance-the-same-thing-as-roles"></a>Più entità in un enunciato sono uguali ai ruoli? 

Più entità possono esistere in un enunciato e possono essere estratte senza usare i ruoli. Se il contesto della frase indica quale versione dell'entità ha un valore, deve essere usato un ruolo. 

### <a name="dont-use-roles-for-duplicates-without-meaning"></a>Non usare i ruoli per i duplicati senza significato

Se l'espressione include un elenco di percorsi, `I want to travel to Seattle, Cairo, and London.`, si tratta di un elenco in cui ogni elemento non ha un significato aggiuntivo. 

### <a name="use-roles-if-duplicates-indicate-meaning"></a>USA ruoli se i duplicati indicano un significato

Se l'espressione include un elenco di percorsi con significato, `I want to travel from Seattle, with a layover in Londen, landing in Cairo.`, questo significato di origine, sosta e destinazione deve essere acquisito con i ruoli.

### <a name="roles-can-indicate-order"></a>I ruoli possono indicare l'ordine

Se l'espressione è cambiata per indicare l'ordine in cui si desidera `I want to first start with Seattle, second London, then third Cairo`estrarre, è possibile estrarre in un paio di modi. È possibile contrassegnare i token che indicano il ruolo, `first start with` `second`,, `third`. È anche possibile usare il **numero ordinale** di entità predefinito e l'entità precompilata **GeographyV2** in un'entità composta per acquisire l'idea dell'ordine e della posizione. 

## <a name="how-are-roles-used-in-example-utterances"></a>Come vengono usati i ruoli nelle espressioni di esempio?

Quando un'entità dispone di un ruolo e l'entità è contrassegnata in un enunciato di esempio, è possibile scegliere di selezionare solo l'entità o di selezionare l'entità e il ruolo. 

Le espressioni di esempio seguenti usano entità e ruoli:

|Visualizzazione token|Visualizzazione entità|
|--|--|
|Sono molto interessante per saperne di più su **Seattle**|Sono interessato a saperne di più su {location}|
|Acquistare un ticket da Seattle a New York|Acquistare un ticket da {location: Origin} in {location: Destination}|

## <a name="how-are-roles-used-in-patterns"></a>In che modo vengono utilizzati i ruoli nei criteri?
Nell'espressione del modello del criterio, i ruoli vengono utilizzati all'interno dell'espressione: 

|Criteri con ruoli di entità|
|--|
|`buy a ticket from {Location:origin} to {Location:destination}`|


## <a name="role-syntax-in-patterns"></a>Sintassi del ruolo nei criteri
L'entità e il ruolo sono racchiusi tra parentesi, `{}`. L'entità e il ruolo sono separati da due punti. 

## <a name="entity-roles-versus-collaborator-roles"></a>Ruoli di entità rispetto ai ruoli del collaboratore

I ruoli dell'entità si applicano al modello di dati dell'app LUIS. I ruoli del [collaboratore](luis-concept-collaborator.md) si applicano ai livelli di accesso alla creazione. 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="next-steps"></a>Passaggi successivi

* Usare un' [esercitazione pratica](tutorial-entity-roles.md) con i ruoli dell'entità con entità non acquisite dal computer
* Informazioni su come aggiunge [ruoli](luis-how-to-add-entities.md#add-a-role-to-pattern-based-entity)
