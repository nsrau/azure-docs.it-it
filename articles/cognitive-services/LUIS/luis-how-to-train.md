---
title: Train app-LUIS
titleSuffix: Azure Cognitive Services
description: Il training è il processo di appredimento per la propria versione applicazione Language Understanding (LUIS) al fine di migliorare la comprensione del linguaggio naturale. Eseguire il training dell'app LUIS dopo gli aggiornamenti al modello, ad esempio aggiunta, modifica, aggiunta di etichette o eliminazione di entità, finalità o espressioni.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 1da8ab3015730c6b3e1962301a34b1ad43b1aad6
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74143684"
---
# <a name="train-your-active-version-of-the-luis-app"></a>Eseguire il training della versione attiva dell'app LUIS 

Il training è il processo di apprendimento per l'app Language Understanding (LUIS) al fine di migliorare la comprensione del linguaggio naturale. Eseguire il training dell'app LUIS dopo gli aggiornamenti al modello, ad esempio aggiunta, modifica, aggiunta di etichette o eliminazione di entità, finalità o espressioni. 

Il training e il [test](luis-concept-test.md) di un'app costituiscono un processo iterativo. Dopo aver eseguito il training dell'app LUIS, testarla con espressioni di esempio per verificare se le finalità e le entità vengono riconosciute correttamente. Se così non fosse, aggiornare l'app LUIS ed eseguire nuovamente il training e il test. 

Il training viene applicato alla versione attiva nel portale LUIS. 

## <a name="how-to-train-interactively"></a>Come eseguire il training in modo interattivo

Per avviare il processo iterativo nel [portale LUIS](https://www.luis.ai), è prima necessario eseguire il training dell'app LUIS almeno una volta. Prima del training, assicurarsi che ogni finalità abbia almeno un'espressione.

1. Accedere all'app selezionando il relativo nome nella pagina **My Apps** (App personali). 

1. Nell'app selezionare **Train** (Esegui training) nel riquadro superiore. 

1. Al termine del training, viene visualizzata una notifica nella parte superiore del browser.

## <a name="training-date-and-time"></a>Data e ora di training

La data e l'ora di training sono GMT + 2. 

## <a name="train-with-all-data"></a>Eseguire il training con tutti i dati

Il training usa una piccola percentuale di campionamento negativo. Se si desidera utilizzare tutti i dati anziché il piccolo campionamento negativo, utilizzare l' [API](#version-settings-api-use-of-usealltrainingdata).

### <a name="version-settings-api-use-of-usealltrainingdata"></a>Impostazioni della versione utilizzo dell'API di UseAllTrainingData

usare l' [API impostazioni versione](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) con la `UseAllTrainingData` impostata su true per disattivare questa funzionalità. 

## <a name="unnecessary-training"></a>Training non necessario

Non è necessario eseguire il training dopo ogni singola modifica. È opportuno eseguire il training dopo l'applicazione di un gruppo di modifiche al modello, quando il passaggio da eseguire successivamente è il test o la pubblicazione. Se non è necessario testare o pubblicare il modello, il training non è necessario. 

## <a name="training-with-the-rest-apis"></a>Training con le API REST

Il training nel portale LUIS consiste nell'eseguire un unico passaggio, ovvero fare clic sul pulsante **Train** (Esegui training). Il training con le API REST è invece un processo in due passaggi. Il primo passaggio consiste nel [richiedere il training](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) con HTTP POST. Il secondo consiste invece nel richiedere lo [stato del training](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) con HTTP Get. 

Per sapere quando il training è completo, è necessario eseguire il polling dello stato finché il training non risulta completato per tutti i modelli. 

## <a name="next-steps"></a>Passaggi successivi

* [Test interattivi](luis-interactive-test.md)
* [Test batch](luis-how-to-batch-test.md)
