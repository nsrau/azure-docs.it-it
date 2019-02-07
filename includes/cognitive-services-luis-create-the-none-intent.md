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
ms.openlocfilehash: 08328b3c872ce9e8a25db6075b00e0cee2fdf4fd
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55480478"
---
L'applicazione client deve stabilire se un'espressione non è significativa o appropriata per l'applicazione. La finalità **None** (Nessuna) viene aggiunta a ciascuna applicazione come parte del processo di creazione per determinare se l'applicazione client non può rispondere a un'espressione.

Se LUIS restituisce la finalità **None** per un'espressione, l'applicazione client può chiedere se l'utente intende terminare la conversazione oppure offrire altre indicazioni per continuare la conversazione. 

> [!CAUTION] 
> Non lasciare vuota la finalità **None**. 

1. Selezionare **Intents** (Finalità) dal pannello di sinistra.

2. Selezionare la finalità **None** (Nessuna). Aggiungere tre espressioni che l'utente potrebbe usare, ma che non sono pertinenti per l'app Human Resources:

    | Espressioni di esempio|
    |--|
    |Barking dogs are annoying|
    |Order a pizza for me|
    |Penguins in the ocean|
