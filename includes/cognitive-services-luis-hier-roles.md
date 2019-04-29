---
title: File di inclusione
description: File di inclusione
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 10/23/2018
ms.author: diberry
ms.openlocfilehash: 3803bc7f1e0da01fbaa8aa11bc6e8fc5c508b5ae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61074291"
---
## <a name="roles-versus-hierarchical-entities"></a>Ruoli ed entità gerarchiche

Si deve usare un'entità gerarchica oppure un modello con un'entità semplice per i ruoli? 

Dipende. I modelli e le espressioni di esempio sono assimilabili perché rappresentano l'espressione di un utente e sono specifici di una finalità.  

Se le espressioni non hanno un modello chiaro, usare le entità gerarchiche. 

|Entità gerarchiche|Entità semplice con i ruoli|
|--|--|
|Disponibili nelle espressioni e nei modelli di esempio della finalità|Disponibile solo nei modelli|
|Devono avere espressioni di esempio nella finalità con entità figlio etichettate|I ruoli non possono essere etichettati nelle espressioni di esempio nella finalità|
|Potrebbero richiedere **più** espressioni di esempio nella finalità per estrarre l'entità|Potrebbe richiedere **meno** espressioni di esempio nella finalità|
