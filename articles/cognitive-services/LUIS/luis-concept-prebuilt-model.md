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
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: b88801ded3dea7c7514ff117361feba3e95444ed
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72264381"
---
# <a name="prebuilt-domain-intent-and-entity-models"></a>Modelli predefiniti per dominio, finalità ed entità

I modelli predefiniti includono domini, finalità, espressioni ed entità. È possibile avviare l'app con un dominio predefinito o aggiungere all'app un dominio pertinente in un secondo momento. 

## <a name="types-of-prebuilt-models"></a>Tipi di modelli predefiniti

Esistono tre tipi di modelli predefiniti forniti da LUIS. Ogni modello può essere aggiunto all'app in qualsiasi momento. 

|Tipo di modello|Include|
|--|--|
|Domain|Finalità, espressioni, entità|
|Finalità|Finalità, espressioni|
|Entità|Solo entità| 

## <a name="prebuilt-domains"></a>Domini predefiniti

Language Understanding (LUIS) fornisce *domini predefiniti*, ovvero gruppi predefiniti di [finalità](luis-how-to-add-intents.md) ed [entità](luis-concept-entity-types.md) che cooperano in funzione di domini o di categorie generiche di applicazioni client. 

I domini predefiniti sono stati sottoposti a training e possono essere aggiunti all'app LUIS. Le finalità e le entità in un dominio predefinito sono completamente personalizzabili dopo che sono state aggiunte all'app. 

Se in primo luogo personalizzi un intero dominio predefinito, elimina le finalità e le entità non necessarie alla tua app. Puoi inoltre aggiungere alcune finalità o entità al set già fornito dal dominio predefinito. Ad esempio, se usi il dominio predefinito **Eventi** per un'app di eventi sportivi, puoi aggiungere le entità per squadra. Quando inizi a [fornire espressioni](luis-how-to-add-example-utterances.md) a LUIS, includi i termini specifici per la tua app. LUIS impara a riconoscerli e adatta le finalità e le entità del dominio predefinito alle esigenze dell'applicazione. 

> [!TIP]
> Le finalità e le entità in un dominio predefinito hanno una maggiore efficacia quando operano in modo collaborativo. È consigliabile combinare le finalità e le entità provenienti dallo stesso dominio, quando possibile.
> Il dominio predefinito Utilities include finalità che è possibile personalizzare per l'uso in qualsiasi dominio. Ad esempio, puoi aggiungere `Utilities.Repeat` alla tua app ed eseguirvi il training per riconoscere qualsiasi azione che l'utente vuole ripetere nell'applicazione. 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Modifica del comportamento delle finalità di dominio predefinito

È possibile che un dominio predefinito contenga una finalità simile a un'altra di cui vuoi disporre nell'app LUIS, ma che vuoi operi in modo diverso. Ad esempio, il dominio predefinito **Località** fornisce una `MakeReservation` finalità per l'esecuzione di una prenotazione al ristorante, ma vuoi che l'app usi tale finalità per prenotare alberghi. In tal caso, è possibile modificare il comportamento di tale finalità fornendo a LUIS espressioni sulle prenotazioni di hotel e assegnandovi etichette tramite la `MakeReservation` finalità, quindi è possibile eseguire nuovamente il training di LUIS per riconoscere la `MakeReservation` finalità in una richiesta di prenotazione di un hotel.

È possibile consultare l'elenco completo dei domini predefiniti nei [riferimenti dei domini predefiniti](./luis-reference-prebuilt-domains.md).

## <a name="prebuilt-intents"></a>Finalità predefinite

LUIS offre finalità predefinite e le relative espressioni. È possibile aggiungere le finalità senza aggiungere l'intero dominio. Per aggiunta di una finalità si intende il processo di aggiunta di una finalità e delle relative espressioni. È possibile modificare sia il nome della finalità e che l'elenco delle espressioni.  

## <a name="prebuilt-entities"></a>Entità predefinite

LUIS include un insieme di entità predefinite per il riconoscimento di tipi comuni di informazioni, ad esempio date, ore, numeri, misurazioni e valuta. Il supporto dell'entità predefinita varia a seconda delle impostazioni cultura dell'app LUIS. Per un elenco completo delle entità predefinite supportate da LUIS, incluso il supporto per le impostazioni cultura, consultare il [riferimento all'entità predefinita](./luis-reference-prebuilt-entities.md).

Quando si include un'entità predefinita nell'applicazione, le relative stime vengono incluse nell'applicazione pubblicata. Si esegue il training preliminare del comportamento delle entità predefinite. **Non è possibile** applicare modifiche a questo comportamento. 

> [!NOTE]
> **builtin.datetime** è deprecata. Viene sostituita da [**builtin.datetimeV2**](luis-reference-prebuilt-datetimev2.md), che consente il riconoscimento degli intervalli di date e ora, nonché il riconoscimento migliorato di date e ore ambigue.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [aggiungere entità predefinite](luis-prebuilt-entities.md) all'app.
