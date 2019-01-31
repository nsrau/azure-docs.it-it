---
title: Ruoli per le entità
titleSuffix: Azure Cognitive Services
description: I ruoli sono sottotipi contestuali denominati di un'entità utilizzati solo nei criteri. Ad esempio, nell'espressione `buy a ticket from New York to London`, sia New York sia Londra sono città, ma ciascuna assume un significato diverso all'interno della frase. New York è la città di origine e Londra è la città di destinazione.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/17/2018
ms.author: diberry
ms.openlocfilehash: f5768012a03629190a65dd86d004dc842d99912e
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55215947"
---
# <a name="entity-roles-in-patterns-are-contextual-subtypes"></a>Nei criteri, i ruoli di entità sono sottotipi contestuali
I ruoli sono sottotipi contestuali denominati di un'entità utilizzati solo nei [criteri](luis-concept-patterns.md).

Ad esempio, nell'espressione `buy a ticket from New York to London`, sia New York sia Londra sono città, ma ciascuna assume un significato diverso all'interno della frase. New York è la città di origine e Londra è la città di destinazione. 

I ruoli assegnano un nome a tali differenze:

|Entità|Ruolo|Scopo|
|--|--|--|
|Località|origin|luogo in cui parte l'aereo|
|Località|destination|luogo in cui atterra l'aereo|
|DatetimeV2 predefinito|to|data di fine|
|DatetimeV2 predefinito|from|data di inizio|

## <a name="how-are-roles-used-in-patterns"></a>In che modo vengono utilizzati i ruoli nei criteri?
Nell'espressione del modello del criterio, i ruoli vengono utilizzati all'interno dell'espressione: 

|Criteri con ruoli di entità|
|--|
|`buy a ticket from {Location:origin} to {Location:destination}`|


## <a name="role-syntax-in-patterns"></a>Sintassi del ruolo nei criteri
L'entità e il ruolo sono racchiusi tra parentesi, `{}`. L'entità e il ruolo sono separati da due punti. 


[!INCLUDE [H2 Roles versus hierarchical entities](../../../includes/cognitive-services-luis-hier-roles.md)] 

## <a name="example-role-for-entities"></a>Ruolo di esempio per le entità

Un ruolo è semplicemente la posizione appresa in base al contesto di un'entità all'interno di un'espressione. È più efficace quando l'espressione include più di un'entità di tale tipo. L'esempio più semplice per qualsiasi tipo di entità è la possibilità di distinguere tra posizione di origine e posizione di destinazione. La posizione può essere rappresentata in molti tipi di entità diversi. 

Un caso d'uso di esempio è lo spostamento di un dipendente da un reparto a un altro, in cui ogni reparto è un elemento in un elenco. Ad esempio:  

`Move [PersonName] from [Department:from] to [Department:to]`. 

Nella stima risultante verranno restituite entrambe le entità reparto nella risposta JSON e ognuna includerà il nome del ruolo. 

## <a name="roles-with-prebuilt-entities"></a>Ruoli con entità predefinite

Usare i ruoli con entità predefinite per assegnare un significato a istanze diverse dell'entità predefinita all'interno di un'espressione. 

### <a name="roles-with-datetimev2"></a>Ruoli con datetimeV2

L'entità predefinita datetimeV2 è utilissima per comprendere i diversi modi di indicare data e ora nelle espressioni. È possibile specificare date e intervalli di date in modo diverso rispetto a quanto previsto dall'impostazione predefinita di riconoscimento dell'entità predefinita. 

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come aggiunge [ruoli](luis-how-to-add-entities.md#add-a-role-to-pattern-based-entity)
