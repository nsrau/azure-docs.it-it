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
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 259ea23c05f0c0a138ad54b6efd11aad2061cf7a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500226"
---
# <a name="train-your-active-version-of-the-luis-app"></a>Eseguire il training della versione attiva dell'app LUIS 

Il training è il processo di apprendimento per l'app Language Understanding (LUIS) al fine di migliorare la comprensione del linguaggio naturale. Eseguire il training dell'app LUIS dopo gli aggiornamenti al modello, ad esempio aggiunta, modifica, aggiunta di etichette o eliminazione di entità, finalità o espressioni. 

Il training e il [test](luis-concept-test.md) di un'app costituiscono un processo iterativo. Dopo aver eseguito il training dell'app LUIS, testarla con espressioni di esempio per verificare se le finalità e le entità vengono riconosciute correttamente. Se così non fosse, aggiornare l'app LUIS ed eseguire nuovamente il training e il test. 

Il training viene applicato alla versione attiva nel portale LUIS. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="how-to-train-interactively"></a>Come eseguire il training in modo interattivo

Per avviare il processo iterativo nel [portale LUIS](https://www.luis.ai), è prima necessario eseguire il training dell'app LUIS almeno una volta. Prima del training, assicurarsi che ogni finalità abbia almeno un'espressione.

1. Accedere all'app selezionando il relativo nome nella pagina **App personali**. 

2. Nell'app selezionare **Train** (Esegui training) nel riquadro superiore. 

3. Una volta completato il training, viene visualizzata una barra di notifica verde nella parte superiore del browser.

<!-- The following note refers to what might cause the error message "Training failed: FewLabels for model: <ModelName>" -->

>[!NOTE]
>Se nell'app ci sono una o più finalità che non includono espressioni di esempio, non è possibile eseguire il training. Aggiungere espressioni per tutte le finalità. Per altre informazioni, vedere [Add example utterances](luis-how-to-add-example-utterances.md) (Aggiungere espressioni di esempio).

## <a name="training-date-and-time"></a>Data e ora di training

La data e l'ora di training sono GMT + 2. 

## <a name="train-with-all-data"></a>Eseguire il training con tutti i dati

Il training usa una piccola percentuale di campionamento negativo. 

Se si desidera utilizzare tutti i dati anziché il piccolo campionamento negativo, utilizzare l' [API](#version-settings-api-use-of-usealltrainingdata).

<!--

 or the [LUIS portal setting](#luis-portal-setting-to-use-all-training-data)

### LUIS portal setting to use all training data

!!!IGNITE


-->

### <a name="version-settings-api-use-of-usealltrainingdata"></a>Impostazioni della versione utilizzo dell'API di UseAllTrainingData

usare l' [API impostazioni versione](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) con la `UseAllTrainingData` impostata su true per disattivare questa funzionalità. 

## <a name="unnecessary-training"></a>Training non necessario

Non è necessario eseguire il training dopo ogni singola modifica. È opportuno eseguire il training dopo l'applicazione di un gruppo di modifiche al modello, quando il passaggio da eseguire successivamente è il test o la pubblicazione. Se non è necessario testare o pubblicare il modello, il training non è necessario. 

## <a name="training-with-the-rest-apis"></a>Training con le API REST

Il training nel portale LUIS consiste nell'eseguire un unico passaggio, ovvero fare clic sul pulsante **Train** (Esegui training). Il training con le API REST è invece un processo in due passaggi. Il primo passaggio consiste nel [richiedere il training](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) con HTTP POST. Il secondo consiste invece nel richiedere lo [stato del training](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) con HTTP Get. 

Per sapere quando il training è completo, è necessario eseguire il polling dello stato finché il training non risulta completato per tutti i modelli. 

## <a name="next-steps"></a>Passaggi successivi

* [Etichettare le espressioni suggerite con LUIS](luis-how-to-review-endpoint-utterances.md) 
* [Usare le funzionalità per migliorare le prestazioni dell'app LUIS](luis-how-to-add-features.md) 
