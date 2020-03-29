---
title: Modelli predefiniti per la comprensione della lingua
titleSuffix: Azure Cognitive Services
description: LUIS include un set di modelli predefiniti per aggiungere rapidamente scenari utente comuni e conversazionali.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: 0a733aff14b71c6378971c0b72a9ca1f3cd4cfc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74013540"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>Aggiungere modelli predefiniti per scenari di utilizzo comuniAdd prebuilt models for common usage scenarios 

LUIS include un set di modelli predefiniti per aggiungere rapidamente scenari utente comuni e conversazionali. Questo è un modo semplice e veloce per aggiungere capacità alla tua applicazione client conversazionale senza dover progettare i modelli per tali abilità. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-a-prebuilt-domain"></a>Aggiungere un dominio predefinito

1. Nella pagina **App personali** selezionare l'app. L'app viene aperta nella sezione **Build** (Compila) dell'app. 

1. Selezionare **Domini predefiniti** dalla barra degli strumenti a sinistra. 

1. Trova il dominio che vuoi aggiungere all'app, quindi seleziona il pulsante **Aggiungi dominio.**

    > [!div class="mx-imgBorder"]
    > ![Aggiunta del dominio predefinito Calendar](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

## <a name="add-a-prebuilt-intent"></a>Aggiungere una finalità predefinita

1. Nella pagina **App personali** selezionare l'app. L'app viene aperta nella sezione **Build** (Compila) dell'app. 

1. Nella pagina **Finalità** selezionare **Aggiungi finalità** di dominio predefinita dalla barra degli strumenti sopra l'elenco delle finalità. 

1. Selezionare la finalità **Utilities.Cancel** nella finestra di dialogo popup. 

    > [!div class="mx-imgBorder"]
    > ![Aggiunta della finalità predefinita](./media/luis-prebuilt-domains/add-prebuilt-domain-intents.png)

1. Selezionare il pulsante **Done** (Fine).

## <a name="add-a-prebuilt-entity"></a>Aggiungere un'entità predefinita

1. Aprire l'app facendo clic sul relativo nome nella pagina **App personali** e selezionare **Entità** nel riquadro sinistro. 

1. Nella pagina **Entità** fare clic su **Aggiungi entità predefinite**.

1. Nella finestra di dialogo **Aggiungi entità predefinite** selezionare l'entità predefinita. 

    > [!div class="mx-imgBorder"]
    > ![Finestra di dialogo Add prebuilt entities (Aggiungi entità predefinite)](./media/luis-prebuilt-domains/add-prebuilt-entity.png)

1. Selezionare **Fatto**. Dopo aver aggiunto l'entità, non è necessario eseguire il training dell'app. 

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>Pubblicare per visualizzare il modello predefinito dall'endpoint di stima

Il modo più semplice per visualizzare il valore di un modello predefinito consiste nell'eseguire una query dall'endpoint pubblicato. 

## <a name="entities-containing-a-prebuilt-entity-token"></a>Entità contenenti un token di entità predefinito
 
Se si dispone di un'entità appresa dal computer vincolata da un'entità predefinita, aggiungere un sottocomponente all'entità appresa dal computer, quindi aggiungere un vincolo di un'entità predefinita.

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Creare un modello da .csv con API REST](./luis-tutorial-node-import-utterances-csv.md)
