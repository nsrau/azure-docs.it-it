---
title: includere file
description: includere file
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.custom: include file
ms.date: 02/14/2020
ms.subservice: language-understanding
ms.openlocfilehash: 1509d175979bb65c467424db5de967f56825a3f9
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91545244"
---
L'applicazione client deve stabilire se un'espressione non è significativa o appropriata per l'applicazione. La finalità **None** (Nessuna) viene aggiunta a ciascuna applicazione come parte del processo di creazione per determinare se l'applicazione client non deve rispondere a un'espressione.

Se LUIS restituisce la finalità **None** per un'espressione, l'applicazione client può chiedere se l'utente intende terminare la conversazione oppure offrire altre indicazioni per continuare la conversazione.

Se si lascia vuota la finalità **None**, un'espressione che deve essere stimata all'esterno del dominio soggetto sarà stimata in una delle finalità del dominio soggetto esistente. Ciò determinerà operazioni non corrette basate su una stima errata da parte dell'applicazione client, ad esempio un chatbot.

1. Selezionare **Intents** (Finalità) dal pannello di sinistra.

1. Selezionare la finalità **None** (Nessuna). Aggiungere tre espressioni che l'utente potrebbe usare, ma che non sono pertinenti per l'app per l'ordinazione di pizze:

    |Espressioni di esempio `None`|
    |--|
    |`Barking dogs are annoying`|
    |`Penguins in the ocean`|

    Questi esempi non devono usare parole che ci si aspetta nel dominio soggetto, ad esempio `pizza`, `cheese`, `crust`, `pickup` `deliver`.