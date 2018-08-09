---
title: Esercitazione usando l'entità pattern.any per migliorare le stime LUIS - Azure | Microsoft Docs
titleSuffix: Cognitive Services
description: Questa esercitazione usa l'entità pattern.any per migliorare le stime LUIS di finalità ed entità.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: 1587debecd82072c29d4caffc2b81629b1f52b0e
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2018
ms.locfileid: "39527365"
---
# <a name="tutorial-improve-app-with-patternany-entity"></a>Esercitazione: migliorare le app con l'entità pattern.any

Questa esercitazione usa l'entità pattern.any per aumentare le stime di finalità ed entità.  

> [!div class="checklist"]
* Informazioni su quando e come usare pattern.any
* Creare un criterio che usa pattern.any
* Come verificare i miglioramenti di stima

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Prima di iniziare
Se non si ha l'app relativa alle risorse umane dell'esercitazione dei [ruoli del criterio](luis-tutorial-pattern-roles.md), [importare](luis-how-to-start-new-app.md#import-new-app) il codice JSON in una nuova app nel sito Web [LUIS](luis-reference-regions.md#luis-website). L'app da importare è disponibile nel repository GitHub [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-roles-HumanResources.json).

Se si vuole mantenere l'app originale, clonare la versione nella pagina [Settings](luis-how-to-manage-versions.md#clone-a-version) (Impostazioni) assegnando il nome `patt-any`. La clonazione è un ottimo modo per provare le diverse funzionalità di LUIS senza modificare la versione originale. 

## <a name="the-purpose-of-patternany"></a>Lo scopo di pattern.any
L'entità pattern.any consente di trovare i dati in formato libero in cui la formulazione dell'entità rende difficile determinare la fine dell'entità dal resto dell'espressione. 

Questa app delle risorse umane aiuta i dipendenti a trovare i moduli aziendali. All'[esercitazione di espressione regolare](luis-quickstart-intents-regex-entity.md) sono stati aggiunti dei moduli. I nomi di modulo dell'esercitazione hanno usato un'espressione regolare per estrarre un nome di modulo formattato in modo corretto, ad esempio i nomi di modulo in grassetto nella seguente tabella delle espressioni:

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

La lunghezza variabile include frasi che potrebbero confondere LUIS circa la fine dell'entità. Usando un'entità Pattern.any in un criterio è possibile specificare l'inizio e la fine del nome del modulo in modo che LUIS lo estragga correttamente.

**Mentre i criteri consentono di fornire un numero inferiore espressioni di esempio, se le entità non vengono rilevate, il criterio non corrisponde.**

## <a name="add-example-utterances-to-the-existing-intent-findform"></a>Aggiungere espressioni di esempio alla finalità FindForm esistente 
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

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-the-new-pattern-for-free-form-data-extraction"></a>Testare il nuovo criterio per l'estrazione dei dati in formato libero
1. Selezionare **Test** nella barra superiore per aprire il pannello test. 

2. Immettere l'espressione seguente: 

    `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

3. Selezionare **Inspect** (Ispeziona) sotto il risultato per visualizzare i risultati del test per entità e finalità.

    Prima viene trovata l'entità `FormName`, poi il criterio a determinare la finalità. Se nel risultato di un test le entità non vengono rilevate e di conseguenza il criterio non viene trovato, è necessario aggiungere un maggior numero di espressioni di esempio sulla finalità (non il criterio).

4. Chiudere il pannello test selezionando il pulsante **Test** nella barra di spostamento superiore.

## <a name="clean-up-resources"></a>Pulire le risorse

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su come usare i ruoli con un criterio](luis-tutorial-pattern-roles.md)