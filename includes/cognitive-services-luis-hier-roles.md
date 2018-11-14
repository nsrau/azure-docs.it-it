---
title: File di inclusione
description: File di inclusione
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: include
ms.custom: include file
ms.date: 10/23/2018
ms.author: diberry
ms.openlocfilehash: 0ad3b360611a12b95568e9a20f13a57c93b2e603
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2018
ms.locfileid: "51208305"
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
