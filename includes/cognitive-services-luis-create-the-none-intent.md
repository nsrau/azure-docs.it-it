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
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 355fe134939b26c51d6e03368f782845628a6b96
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180455"
---
L'applicazione client deve stabilire se un'espressione non è significativa o appropriata per l'applicazione. La finalità **None** (Nessuna) viene aggiunta a ciascuna applicazione come parte del processo di creazione per determinare se l'applicazione client non può rispondere a un'espressione.

Se LUIS restituisce la finalità **None** per un'espressione, l'applicazione client può chiedere se l'utente intende terminare la conversazione oppure offrire altre indicazioni per continuare la conversazione. 

> [!CAUTION] 
> Non lasciare vuota la finalità **None**. 

1. Selezionare **Intents** (Finalità) dal pannello di sinistra.

2. Selezionare la finalità **None** (Nessuna). Aggiungere tre espressioni che l'utente potrebbe usare, ma che non sono pertinenti per l'app Human Resources:

    | Espressioni di esempio|
    |--|
    |Barking dogs are annoying (I cani che abbaiano sono fastidiosi)|
    |Order a pizza for me (Ordinami una pizza)|
    |Penguins in the ocean (Pinguini nell'oceano)|
