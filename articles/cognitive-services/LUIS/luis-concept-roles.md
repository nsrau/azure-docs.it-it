---
title: Informazioni sull'utilizzo dei ruoli in entità basate su un criterio - Azure | Microsoft Docs
description: Informazioni sull'utilizzo di un ruolo in un'entità basata su un criterio al fine di assegnare un nome a un sottotipo di entità contestuale.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/08/2018
ms.author: v-geberr;
ms.openlocfilehash: ab6100e33fb767528b87c6afde4c5ef275fc7c81
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35379065"
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

```
buy a ticket from {Location:origin} to {Location:destination}
```

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
