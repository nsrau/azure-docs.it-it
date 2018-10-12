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
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: dd99218765c873120c4117a3ec1712fe0a605e66
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/07/2018
ms.locfileid: "44164588"
---
**Domanda**: si deve usare un'entità gerarchica oppure un modello con un'entità semplice per i ruoli? 

**Risposta**: dipende. I modelli e le espressioni di esempio sono assimilabili perché rappresentano l'espressione di un utente e sono specifici di una finalità.  

Se le espressioni non hanno un modello chiaro, usare le entità gerarchiche. 

|Entità gerarchiche|Entità semplice con i ruoli|
|--|--|
|devono avere espressioni di esempio con entità figlio etichettate nelle finalità|deve avere espressioni di esempio, i **ruoli non possono essere etichettati nelle finalità**|
|possono essere usate nei modelli|**deve** essere usata nei modelli|
|potrebbero richiedere **più** espressioni di esempio nella finalità|potrebbe richiedere **meno espressioni di esempio nella finalità|