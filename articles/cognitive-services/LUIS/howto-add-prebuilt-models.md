---
title: Modelli predefiniti per Language Understanding
titleSuffix: Azure Cognitive Services
description: LUIS include un set di modelli predefiniti per aggiungere rapidamente scenari utente comuni e di conversazione.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: 0a733aff14b71c6378971c0b72a9ca1f3cd4cfc3
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013540"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>Aggiungere modelli predefiniti per scenari di utilizzo comuni 

LUIS include un set di modelli predefiniti per aggiungere rapidamente scenari utente comuni e di conversazione. Si tratta di un modo rapido e semplice per aggiungere funzionalità all'applicazione client di conversazione senza dover progettare i modelli per tali capacità. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-a-prebuilt-domain"></a>Aggiungere un dominio predefinito

1. Nella pagina **App personali** selezionare l'app. L'app viene aperta nella sezione **Build** (Compila) dell'app. 

1. Selezionare **domini predefiniti** dalla barra degli strumenti a sinistra. 

1. Trovare il dominio da aggiungere all'app e quindi fare clic sul pulsante **Aggiungi dominio** .

    > [!div class="mx-imgBorder"]
    > ![aggiunta del dominio predefinito del calendario](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

## <a name="add-a-prebuilt-intent"></a>Aggiungere una finalità predefinita

1. Nella pagina **App personali** selezionare l'app. L'app viene aperta nella sezione **Build** (Compila) dell'app. 

1. Nella pagina **Intent** , selezionare **Aggiungi finalità del dominio predefinito** dalla barra degli strumenti sopra l'elenco Intent. 

1. Selezionare la finalità **Utilities.Cancel** nella finestra di dialogo popup. 

    > [!div class="mx-imgBorder"]
    > ![Aggiungi finalità precompilata](./media/luis-prebuilt-domains/add-prebuilt-domain-intents.png)

1. Selezionare il pulsante **Done** (Fine).

## <a name="add-a-prebuilt-entity"></a>Aggiungere un'entità predefinita

1. Aprire l'app facendo clic sul relativo nome nella pagina **App personali** e selezionare **Entità** nel riquadro sinistro. 

1. Nella pagina **Entità** fare clic su **Aggiungi entità predefinite**.

1. Nella finestra di dialogo **Aggiungi entità predefinite** selezionare l'entità precompilata. 

    > [!div class="mx-imgBorder"]
    > ![finestra di dialogo Aggiungi entità predefinita](./media/luis-prebuilt-domains/add-prebuilt-entity.png)

1. Selezionare **Done**(Fine). Dopo aver aggiunto l'entità, non è necessario eseguire il training dell'app. 

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>Pubblicazione per la visualizzazione del modello predefinito dall'endpoint di stima

Il modo più semplice per visualizzare il valore di un modello predefinito consiste nell'eseguire una query dall'endpoint pubblicato. 

## <a name="entities-containing-a-prebuilt-entity-token"></a>Entità contenenti un token di entità predefinito
 
Se si ha un'entità appresa dal computer che è vincolata da un'entità precompilata, aggiungere un sottocomponente all'entità appresa dal computer, quindi aggiungere un vincolo di un'entità precompilata.

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Compilare il modello da CSV con le API REST](./luis-tutorial-node-import-utterances-csv.md)
