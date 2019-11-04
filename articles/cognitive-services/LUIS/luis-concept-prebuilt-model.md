---
title: Modelli predefiniti-LUIS
titleSuffix: Azure Cognitive Services
description: I modelli predefiniti includono domini, finalità, espressioni ed entità. È possibile avviare l'app con un dominio predefinito o aggiungere all'app un dominio pertinente in un secondo momento.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: d9cb86c1c19649052e4796fd0a8909ce08381d55
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73487591"
---
# <a name="prebuilt-models"></a>Modelli predefiniti

I modelli predefiniti includono domini, finalità, espressioni ed entità. È possibile avviare l'app con un modello predefinito o aggiungere un modello pertinente all'app in un secondo momento. 

## <a name="types-of-prebuilt-models"></a>Tipi di modelli predefiniti

LUIS fornisce tre tipi di modelli predefiniti. Ogni modello può essere aggiunto all'app in qualsiasi momento. 

|Tipo di modello|Include|
|--|--|
|[Dominio](luis-reference-prebuilt-domains.md)|Finalità, espressioni, entità|
|Finalità|Finalità, espressioni|
|[Entità](luis-reference-prebuilt-entities.md)|Solo entità| 

## <a name="prebuilt-domains"></a>Domini predefiniti

Language Understanding (LUIS) fornisce *domini predefiniti*, ovvero modelli di [Intent](luis-how-to-add-intents.md) ed [entità](luis-concept-entity-types.md) che interagiscono tra loro per domini o categorie comuni di applicazioni client. 

I domini predefiniti sono stati sottoposti a training e possono essere aggiunti all'app LUIS. Gli Intent e le entità di un dominio predefinito sono completamente personalizzabili dopo essere stati aggiunti all'app. 

> [!TIP]
> Le finalità e le entità in un dominio predefinito hanno una maggiore efficacia quando operano in modo collaborativo. È consigliabile combinare le finalità e le entità provenienti dallo stesso dominio, quando possibile.
> Il dominio predefinito Utilities include finalità che è possibile personalizzare per l'uso in qualsiasi dominio. Ad esempio, puoi aggiungere `Utilities.Repeat` alla tua app ed eseguirvi il training per riconoscere qualsiasi azione che l'utente vuole ripetere nell'applicazione. 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Modifica del comportamento delle finalità di dominio predefinito

È possibile che un dominio predefinito contenga una finalità simile a un'altra di cui vuoi disporre nell'app LUIS, ma che vuoi operi in modo diverso. Il dominio predefinito, ad esempio, fornisce un `MakeReservation` finalità per la prenotazione di un ristorante, ma si vuole **che l'app** usi tale scopo per effettuare le prenotazioni degli alberghi. In tal caso, è possibile modificare il comportamento di tale finalità aggiungendo espressioni di esempio allo scopo di effettuare prenotazioni di Hotel. Quindi ripetere il training dell'app. 

È possibile consultare l'elenco completo dei domini predefiniti nei [riferimenti dei domini predefiniti](./luis-reference-prebuilt-domains.md).

## <a name="prebuilt-intents"></a>Finalità predefinite

LUIS fornisce Intent predefiniti e le relative espressioni, dai domini predefiniti. È possibile aggiungere le finalità senza aggiungere l'intero dominio. L'aggiunta di un preventivo è il processo di aggiunta di una finalità e delle relative espressioni all'app. È possibile modificare sia il nome della finalità e che l'elenco delle espressioni.  

## <a name="prebuilt-entities"></a>Entità predefinite

LUIS include un insieme di entità predefinite per il riconoscimento di tipi comuni di informazioni, ad esempio date, ore, numeri, misurazioni e valuta. Il supporto dell'entità predefinita varia a seconda delle impostazioni cultura dell'app LUIS. Per un elenco completo delle entità predefinite supportate da LUIS, incluso il supporto per le impostazioni cultura, consultare il [riferimento all'entità predefinita](./luis-reference-prebuilt-entities.md).

Quando si include un'entità predefinita nell'applicazione, le relative stime vengono incluse nell'applicazione pubblicata. Si esegue il training preliminare del comportamento delle entità predefinite. **Non è possibile** applicare modifiche a questo comportamento. 

> [!NOTE]
> **builtin.datetime** è deprecata. Viene sostituita da [**builtin.datetimeV2**](luis-reference-prebuilt-datetimev2.md), che consente il riconoscimento degli intervalli di date e ora, nonché il riconoscimento migliorato di date e ore ambigue.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [aggiungere entità predefinite](luis-prebuilt-entities.md) all'app.
