---
title: Eseguire il training dell'app LUIS
titleSuffix: Azure Cognitive Services
description: Il training è il processo di apprendimento per l'app Language Understanding (LUIS) al fine di migliorare la comprensione del linguaggio naturale. Eseguire il training dell'app LUIS dopo gli aggiornamenti al modello, ad esempio aggiunta, modifica, aggiunta di etichette o eliminazione di entità, finalità o espressioni.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/06/2018
ms.author: diberry
ms.openlocfilehash: 42cff3dd8237598da5aa71ed1a4d6462c5b4c25d
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/07/2018
ms.locfileid: "44049132"
---
# <a name="train-your-luis-app"></a>Eseguire il training dell'app LUIS

Il training è il processo di apprendimento per l'app Language Understanding (LUIS) al fine di migliorare la comprensione del linguaggio naturale. Eseguire il training dell'app LUIS dopo gli aggiornamenti al modello, ad esempio aggiunta, modifica, aggiunta di etichette o eliminazione di entità, finalità o espressioni. 

<!--
When you train a LUIS app by example, LUIS generalizes from the examples you have labeled, and it learns to recognize the relevant intents and entities. This teaches LUIS to improve classification accuracy in the future. -->

Il training e il [test](luis-concept-test.md) di un'app costituiscono un processo iterativo. Dopo aver eseguito il training dell'app LUIS, testarla con espressioni di esempio per verificare se le finalità e le entità vengono riconosciute correttamente. Se così non fosse, aggiornare l'app LUIS ed eseguire nuovamente il training e il test. 

## <a name="train-your-app"></a>Eseguire il training dell'app
Per avviare il processo iterativo, è prima necessario eseguire il training dell'app LUIS almeno una volta. Prima del training, assicurarsi che ogni finalità abbia almeno un'espressione.

1. Accedere all'app selezionando il relativo nome nella pagina **App personali**. 

2. Nell'app selezionare **Train** (Esegui training) nel riquadro superiore. 

3. Una volta completato il training, viene visualizzata una barra di notifica verde nella parte superiore del browser.

<!-- The following note refers to what might cause the error message "Training failed: FewLabels for model: <ModelName>" -->

>[!NOTE]
>Se nell'app ci sono una o più finalità che non includono espressioni di esempio, non è possibile eseguire il training. Aggiungere espressioni per tutte le finalità. Per altre informazioni, vedere [Add example utterances](luis-how-to-add-example-utterances.md) (Aggiungere espressioni di esempio).

## <a name="next-steps"></a>Passaggi successivi

* [Etichettare le espressioni suggerite con LUIS](luis-how-to-review-endoint-utt.md) 
* [Usare le funzionalità per migliorare le prestazioni dell'app LUIS](luis-how-to-add-features.md) 