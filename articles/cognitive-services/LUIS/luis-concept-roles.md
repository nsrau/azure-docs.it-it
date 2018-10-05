---
title: Informazioni sull'utilizzo dei ruoli in entità basate sui criteri
titleSuffix: Azure Cognitive Services
description: I ruoli sono sottotipi contestuali denominati di un'entità utilizzati solo nei criteri. Ad esempio, nell'espressione "comprare un biglietto da New York a Londra", New York e Londra corrispondono entrambe a città, ma hanno un significato diverso nell'economia della frase. New York è la città di origine e Londra è la città di destinazione.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 033e5e5e054b0a29961ad60e72b1466b51d1df0c
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "47035199"
---
# <a name="entity-roles-in-patterns-are-contextual-subtypes"></a>Nei criteri, i ruoli di entità sono sottotipi contestuali
I ruoli sono sottotipi contestuali denominati di un'entità utilizzati solo nei [criteri](luis-concept-patterns.md).

Ad esempio, nell'espressione `buy a ticket from New York to London`, sia New York sia Londra sono città, ma ciascuna assume un significato diverso all'interno della frase. New York è la città di origine e Londra è la città di destinazione. 

I ruoli assegnano un nome a tali differenze:

|Entità|Ruolo|Scopo|
|--|--|--|
|Località|origin|luogo in cui parte l'aereo|
|Località|destination|luogo in cui atterra l'aereo|

## <a name="how-are-roles-used-in-patterns"></a>In che modo vengono utilizzati i ruoli nei criteri?
Nell'espressione del modello del criterio, i ruoli vengono utilizzati all'interno dell'espressione: 

|Criteri con ruoli di entità|
|--|
|`buy a ticket from {Location:origin} to {Location:destination}`|


## <a name="role-syntax-in-patterns"></a>Sintassi del ruolo nei criteri
L'entità e il ruolo sono racchiusi tra parentesi, `{}`. L'entità e il ruolo sono separati da due punti. 

## <a name="roles-versus-hierarchical-entities"></a>Ruoli ed entità gerarchiche
Le entità gerarchiche forniscono le stesse informazioni contestuali dei ruoli, ma solamente nelle espressioni delle **finalità**. Analogamente, i ruoli forniscono le stesse informazioni contestuali delle entità gerarchiche, ma solamente nei **criteri**.

|Apprendimento contestuale|Usato in|
|--|--|
|Entità gerarchiche|Finalità|
|roles|Criteri|

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come aggiunge [ruoli](luis-how-to-add-entities.md#add-role-to-pattern-based-entity)
