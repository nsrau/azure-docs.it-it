---
title: 'Esercitazione 5: entità pattern.any per testo in formato libero'
titleSuffix: Azure Cognitive Services
description: Usare l'entità pattern.any per estrarre dati da espressioni formattate in modo corretto e in cui la fine dei dati potrebbe essere facilmente confusa con le ultime parole dell'espressione.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 9038947ad20f63638af1830ca660de8cac802d9b
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2018
ms.locfileid: "51282091"
---
# <a name="tutorial-5-extract-free-form-data"></a>Esercitazione 5: estrarre dati in formato libero

Questa esercitazione illustra come usare l'entità pattern.any per estrarre dati da espressioni formattate in modo corretto e in cui la fine dei dati potrebbe essere facilmente confusa con le ultime parole dell'espressione. 

L'entità pattern.any consente di trovare i dati in formato libero nel caso in cui la formulazione dell'entità renda difficile determinare la fine dell'entità dal resto dell'espressione. 

Questa app delle risorse umane aiuta i dipendenti a trovare i moduli aziendali. 

|Espressione|
|--|
|Dove si trova **HRF-123456**?|
|Chi ha creato **HRF-123234**?|
|**HRF-456098** viene pubblicato in francese?|

Tuttavia, ogni modulo contiene sia un nome formattato, usato nella tabella precedente, nonché un nome descrittivo, ad esempio `Request relocation from employee new to the company 2018 version 5`. 

Le espressioni con il nome descrittivo hanno l'aspetto seguente:

|Espressione|
|--|
|Dove si trova la **Richiesta di trasferimento del nuovo dipendente dell'azienda 2018 versione 5**?|
|Chi ha creato la **"Richiesta di trasferimento del nuovo dipendente dell'azienda 2018 versione 5"**?|
|La **Richiesta di trasferimento del nuovo dipendente dell'azienda 2018 versione 5** viene pubblicata in francese?|

La lunghezza variabile include parole che potrebbero confondere LUIS circa la fine dell'entità. Usando un'entità Pattern.any in un criterio è possibile specificare l'inizio e la fine del nome del modulo in modo che LUIS lo estragga correttamente.

|Espressione modello di esempio|
|--|
|Dov'è {NomeModulo}[?]|
|Chi ha creato {NomeModulo}[?]|
|{NomeModulo} viene pubblicato in francese[?]|

**In questa esercitazione si apprenderà come:**

> [!div class="checklist"]
> * Usare l'app di esercitazione esistente
> * Aggiungere espressioni di esempio alla finalità esistente
> * Creare l'entità pattern.any
> * Creare criteri
> * Eseguire il training
> * Eseguire il test del nuovo criterio

[!INCLUDE[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Usare l'app esistente
Continuare con l'app creata nell'ultima esercitazione denominata **HumanResources**. 

Se non si dispone dell'app HumanResources dell'esercitazione precedente, usare la procedura seguente:

1.  Scaricare e salvare il [file JSON dell'app](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-roles-HumanResources.json).

2. Importare il file JSON in una nuova app.

3. Nella scheda **Versioni** della sezione **Gestisci**, clonare la versione e denominarla `patt-any`. La clonazione è un ottimo modo per provare le diverse funzionalità di LUIS senza modificare la versione originale. Poiché viene usato come parte della route dell'URL, il nome della versione non può contenere caratteri non validi per un URL.

## <a name="add-example-utterances"></a>Aggiungere espressioni di esempio 
Se è difficile creare e assegnare un'etichetta all'entità FormName, rimuovere l'entità keyPhrase predefinita. 

1. Selezionare **Build** (Compila) dalla barra di spostamento in alto, quindi selezionare **Intents** (Finalità) dalla barra di spostamento a sinistra.

2. Selezionare **FindForm** dall'elenco delle finalità.

3. Aggiungere alcune espressioni di esempio:

    |Espressione di esempio|
    |--|
    |Dove è il modulo **Cosa fare quando scoppia un incendio nel laboratorio** e chi deve firmare dopo la lettura?|
    |Dove si trova la **Richiesta di trasferimento del nuovo dipendente dell'azienda**?|
    |Chi ha creato "**Richieste per la salute e il benessere nel campus principale**" e qual è la versione più recente?|
    |Sto cercando il modulo denominato "**Richiesta di spostamento di uffici incluse le risorse fisiche**". |

    Senza un'entità Pattern.any, sarebbe difficile per LUIS capire dove finisce il titolo del modulo a causa delle numerose variazioni dei nomi di modulo.

## <a name="create-a-patternany-entity"></a>Creare un'entità pattern.any
L'entità Pattern.any estrae entità di lunghezza variabile. Funziona solo in un criterio perché il criterio contrassegna l'inizio e la fine dell'entità. Se si rileva che il criterio, quando include Pattern.any, estrae le entità in modo errato, usare un [elenco esplicito](luis-concept-patterns.md#explicit-lists) per risolvere il problema. 

1. Selezionare **Entities** (Entità) nel riquadro di spostamento sinistro.

2. Selezionare **Crea una nuova entità**, immettere il nome `FormName` e selezionare **Pattern.any** come tipo. Selezionare **Operazione completata**. 

    Non è possibile assegnare un'etichetta all'entità nella finalità perché Pattern.any è valido solo in un criterio. 

    Se si vuole che i dati estratti includano altre entità, ad esempio numero o datetimeV2, è necessario creare un'entità composita che includa Pattern.any, nonché un numero e datetimeV2.

## <a name="add-a-pattern-that-uses-the-patternany"></a>Aggiungere un criterio che usa Pattern.any

1. Selezionare **Patterns** (Criteri) dal riquadro di spostamento sinistro.

2. Selezionare la finalità **FindForm**.

3. Immettere le espressioni modello seguenti che usano la nuova entità:

    |Espressioni modello|
    |--|
    |Dov'è il modulo ["]{FormName}["] e chi lo deve firmare dopo che è stato letto[?]|
    |Dov'è ["]{FormName}["] sul server[?]|
    |Chi ha creato ["]{FormName}["] e qual è la versione corrente[?]|
    |Sto cercando il modulo denominato ["]{FormName}["][.]|

    Se si intende tenere conto delle variazioni del modulo, ad esempio le virgolette singole anziché le virgolette doppie o un punto invece di un punto interrogativo, creare un nuovo criterio per ogni variante.

4. Se è stata rimossa l'entità keyPhrase, aggiungerla nuovamente all'app. 

## <a name="train-the-luis-app"></a>Eseguire il training dell'app di Language Understanding

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-the-new-pattern-for-free-form-data-extraction"></a>Testare il nuovo criterio per l'estrazione dei dati in formato libero
1. Selezionare **Test** nella barra superiore per aprire il pannello test. 

2. Immettere l'espressione seguente: 

    `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

3. Selezionare **Inspect** (Ispeziona) sotto il risultato per visualizzare i risultati del test per entità e finalità.

    Prima viene trovata l'entità `FormName`, poi il criterio a determinare la finalità. Se nel risultato di un test le entità non vengono rilevate e di conseguenza il criterio non viene trovato, è necessario aggiungere un maggior numero di espressioni di esempio sulla finalità (non il criterio).

4. Chiudere il pannello test selezionando il pulsante **Test** nella barra di spostamento superiore.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state aggiunte espressioni di esempio a una finalità esistente, quindi è stata creata una nuova entità pattern.any per il nome del modulo. È quindi stato creato un criterio per la finalità esistente con le nuove espressioni di esempio e la nuova entità. Il test interattivo ha mostrato che il criterio e la sua finalità sono stati stimati perché l'entità è stata trovata. 

> [!div class="nextstepaction"]
> [Informazioni su come usare i ruoli con un criterio](luis-tutorial-pattern-roles.md)