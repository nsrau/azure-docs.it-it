---
title: Informazioni sull'utilizzo dei ruoli in entità basate sui criteri
titleSuffix: Azure Cognitive Services
description: I ruoli sono sottotipi contestuali denominati di un'entità utilizzati solo nei criteri. Ad esempio, nell'espressione "comprare un biglietto da New York a Londra", New York e Londra corrispondono entrambe a città, ma hanno un significato diverso nell'economia della frase. New York è la città di origine e Londra è la città di destinazione.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 8a92852a2721bd391ddf7c3cf3489b820c4a1400
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2018
ms.locfileid: "51277616"
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


[!INCLUDE[H2 Roles versus hierarchical entities](../../../includes/cognitive-services-luis-hier-roles.md)] 

## <a name="roles-with-prebuilt-entities"></a>Ruoli con entità predefinite

Usare i ruoli con entità predefinite per assegnare un significato a istanze diverse dell'entità predefinita all'interno di un'espressione. 

### <a name="roles-with-datetimev2"></a>Ruoli con datetimeV2

L'entità predefinita datetimeV2 è utilissima per comprendere i diversi modi di indicare data e ora nelle espressioni. È possibile specificare date e intervalli di date in modo diverso rispetto a quanto previsto dall'impostazione predefinita di riconoscimento dell'entità predefinita. 

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come aggiunge [ruoli](luis-how-to-add-entities.md#add-a-role-to-pattern-based-entity)
