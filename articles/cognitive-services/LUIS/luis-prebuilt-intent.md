---
title: Finalità predefinite per Language Understanding (LUIS)
titleSuffix: Azure Cognitive Services
description: LUIS include un set di finalità predefinite per aggiungere rapidamente scenari di conversazione utente comuni.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 10/18/2018
ms.author: diberry
ms.openlocfilehash: 83cbc9fbe5262700a724148c2b63a2e46c489583
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2018
ms.locfileid: "49651837"
---
# <a name="add-prebuilt-intents-for-common-intents"></a>Aggiungere finalità predefinite per finalità comuni 

LUIS include un set di finalità predefinite disponibili nei domini predefiniti per aggiungere rapidamente finalità ed espressioni comuni. Si tratta di un modo semplice e rapido per aggiungere funzionalità all'app client di conversazione senza dover progettare i modelli per tali funzionalità. 

## <a name="add-a-prebuilt-intent"></a>Aggiungere una finalità predefinita

1. Nella pagina **App personali** selezionare l'app. L'app viene aperta nella sezione **Build** (Compila) dell'app. 

1. Nella pagina **Intents** (Finalità) selezionare **Add prebuilt intent** (Aggiungi finalità predefinita) dalla barra degli strumenti sopra l'elenco delle finalità. 

1. Selezionare la finalità **Utilities.Cancel** nella finestra di dialogo popup. 

    ![Aggiunta della finalità predefinita](./media/luis-prebuilt-intents/prebuilt-intents-ddl.png)

1. Selezionare il pulsante **Done** (Fine).

## <a name="train-and-test"></a>Eseguire il training e il test

1. Dopo avere aggiunto la finalità, eseguire il training dell'app selezionando **Train** (Training) nella barra degli strumenti in alto a destra. 

1. Per testare la nuova finalità, seleziona **Test** nella barra degli strumenti a destra. 

1. Nella casella di testo immettere le espressioni per l'annullamento:

    |Espressione di test|Punteggio di previsione|
    |--|:--|
    |I want to cancel my flight.|0.67|
    |Cancel the purchase.|0.52|
    |Cancel the meeting.|0.56|

    ![Test della finalità predefinita](./media/luis-prebuilt-intents/test.png)

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Entità predefinite](./luis-prebuilt-entities.md)