---
title: Come usare il callback di rilevamento entità con un modello di Conversation Learner - Servizi cognitivi Microsoft| Microsoft Docs
titleSuffix: Azure
description: Informazioni su come usare il callback di rilevamento entità con un modello di Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: f168018a23d03ffb957da2dd1f67881420a21208
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171104"
---
# <a name="how-to-use-entity-detection-callback"></a>Come usare il callback di rilevamento entità

Questa esercitazione illustra il callback di rilevamento entità e un modello comune per la risoluzione delle entità.

## <a name="video"></a>Video

[![Anteprima esercitazione 10](http://aka.ms/cl-tutorial-10-preview)](http://aka.ms/blis-tutorial-10)

## <a name="requirements"></a>Requisiti
Per questa esercitazione è necessario che il bot `tutorialEntityDetectionCallback` sia in esecuzione.

    npm run tutorial-entity-detection

## <a name="details"></a>Dettagli
Il callback di rilevamento entità consente di usare codice personalizzato per gestire regole business correlate alle entità. In questa demo si useranno callback ed entità a livello di codice per risolvere il nome di città immesso dall'utente in un nome canonico, risolvendo ad esempio "the big apple" in "new york".

### <a name="open-the-demo"></a>Aprire la demo

Nell'elenco del modello, fare clic su Tutorial-10-EntityDetectionCallback. 

### <a name="entities"></a>Entità

Nel modello vengono definite tre entità.

![](../media/tutorial10_entities.PNG)

1. City è un'entità personalizzata che verrà specificata dall'utente come input di testo.
2. CityUnknown è un'entità a livello di codice che verrà popolata dal sistema. copiando l'input utente se il sistema non riconosce di quale città si tratta.
3. CityResolved è la città rilevata dal sistema ed è il nome canonico della città. Ad esempio, "the big apple" verrà risolto in "new york".

### <a name="actions"></a>Azioni

Nel modello vengono definite tre azioni.

![](../media/tutorial10_actions.PNG)

1. La prima azione è "Which city do you want?".
2. La seconda è "I don't know this city, $CityUnknown. Which city do you want?".
3. La terza è "You said $City, and I resolved that to $CityResolved".

### <a name="callback-code"></a>Codice di callback

Esaminare il codice. Il metodo EntityDetectionCallback si trova nel file C:\<percorsoinstallazione>\src\demos\tutorialSessionCallbacks.ts.

![](../media/tutorial10_callbackcode.PNG)

Questa funzione viene chiamata dopo la risoluzione di entità.
 
- Per prima cosa, cancellerà $CityUnknown. L'entità $CityUnknown persiste per un singolo turno, perché viene sempre cancellata all'inizio.
- Si recupera quindi l'elenco delle città che sono state riconosciute. Tentare di risolvere la prima.
- La funzione che la risolve (resolveCity) è definita nel codice precedente e usa un elenco di nomi canonici di città. Trova il nome della città nell'elenco e lo restituisce oppure cerca in "cityMap" e restituisce il nome mappato. Se non riesce a trovare una città, restituisce Null.
- Se la città è stata risolta in un nome, infine, si archivia la città nell'entità $CityKnown. In caso contrario, l'espressione dell'utente viene cancellata e viene popolata l'entità $CityUnknown.

### <a name="train-dialogs"></a>Dialoghi di training

1. Fare clic su Train Dialogs (Dialoghi di training) e quindi su New Train Dialog (Nuovo dialogo di training).
2. Digitare "hello".
3. Fare clic su Score Actions (Punteggio azioni) e selezionare "Which city do you want?".
2. Immettere "new york".
    - Il testo viene riconosciuto come entità city.
5. Fare clic su Score Actions (Punteggio azioni).
    - `City` e `CityResolved` sono state popolate.
6. Selezionare "You said $City, and I resolved that to $CityResolved".
7. Fare clic su Done Teaching (Training completato).

Aggiungere un altro dialogo di esempio:

1. Fare clic su New Train Dialog (Nuovo dialogo di training).
2. Digitare "hello".
3. Fare clic su Score Actions (Punteggio azioni) e selezionare "Which city do you want?".
2. Immettere "big apple".
    - Il testo viene riconosciuto come entità city.
5. Fare clic su Score Actions (Punteggio azioni).
    - `CityResolved` mostra l'effetto del codice in esecuzione.
6. Selezionare "You said $City, and I resolved that to $CityResolved".
7. Fare clic su Done Teaching (Training completato).

Aggiungere un altro dialogo di esempio:

1. Fare clic su New Train Dialog (Nuovo dialogo di training).
2. Digitare "hello".
3. Fare clic su Score Actions (Punteggio azioni) e selezionare "Which city do you want?".
2. Immettere "foo".
    - Questo è un esempio di una città non riconosciuta dal sistema. 
5. Fare clic su Score Actions (Punteggio azioni).
6. Selezionare "I don't know this city, $CityUnknown. Which city do you want?".
7. Immettere "new york".
8. Fare clic su Score Actions (Punteggio azioni).
    - `CityUknown` è stato cancellato e `CityResolved` viene popolato.
6. Selezionare "You said $City, and I resolved that to $CityResolved".
7. Fare clic su Done Teaching (Training completato).

![](../media/tutorial10_bigapple.PNG)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Callback di sessione](./11-session-callbacks.md)
