---
title: Finalità predefinite
titleSuffix: Azure Cognitive Services
description: LUIS include un set di finalità predefinite per aggiungere rapidamente scenari di conversazione utente comuni.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/24/2019
ms.author: diberry
ms.openlocfilehash: abdbd0672fbb29f3936e4bda6f71e41c0f287329
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55221220"
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
