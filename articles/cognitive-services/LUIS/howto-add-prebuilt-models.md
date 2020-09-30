---
title: Modelli predefiniti per Language Understanding
titleSuffix: Azure Cognitive Services
description: LUIS include un set di modelli predefiniti per aggiungere rapidamente scenari utente comuni e di conversazione.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/17/2020
ms.openlocfilehash: 2080139732d71aa5df9a9d4ad13fcc4545128dff
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91541918"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>Aggiungere modelli predefiniti per scenari di utilizzo comuni

LUIS include un set di modelli predefiniti per aggiungere rapidamente scenari utente comuni e di conversazione. Si tratta di un modo rapido e semplice per aggiungere funzionalità all'applicazione client di conversazione senza dover progettare i modelli per tali capacità.

## <a name="add-a-prebuilt-domain"></a>Aggiungere un dominio predefinito

1. Accedere al [portale LUIS](https://www.luis.ai) e quindi selezionare la **sottoscrizione** e la **risorsa di creazione** per vedere le app assegnate a tale risorsa.
1. Per aprire l'app, selezionarne il nome nella pagina **app personali** .

1. Selezionare **domini predefiniti** dalla barra degli strumenti a sinistra.

1. Trovare il dominio da aggiungere all'app e quindi fare clic sul pulsante **Aggiungi dominio** .

    > [!div class="mx-imgBorder"]
    > ![Aggiunta del dominio predefinito Calendar](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

## <a name="add-a-prebuilt-intent"></a>Aggiungere una finalità predefinita

1. Accedere al [portale LUIS](https://www.luis.ai) e quindi selezionare la **sottoscrizione** e la **risorsa di creazione** per vedere le app assegnate a tale risorsa.
1. Per aprire l'app, selezionarne il nome nella pagina **app personali** .

1. Nella pagina **Intent** , selezionare **Aggiungi finalità del dominio predefinito** dalla barra degli strumenti sopra l'elenco Intent.

1. Selezionare un Intent dalla finestra di dialogo popup.

    > [!div class="mx-imgBorder"]
    > ![Aggiunta della finalità predefinita](./media/luis-prebuilt-domains/add-prebuilt-domain-intents.png)

1. Selezionare il pulsante **Done** (Fine).

## <a name="add-a-prebuilt-entity"></a>Aggiungere un'entità predefinita
1. Accedere al [portale LUIS](https://www.luis.ai) e quindi selezionare la **sottoscrizione** e la **risorsa di creazione** per vedere le app assegnate a tale risorsa.
1. Per aprire l'app, selezionarne il nome nella pagina **app personali** .
1. Selezionare **entità** sul lato sinistro.

1. Nella pagina **entità** selezionare **Aggiungi entità precompilata**.

1. Nella finestra di dialogo **Aggiungi entità predefinite** selezionare l'entità precompilata.

    > [!div class="mx-imgBorder"]
    > ![Finestra di dialogo Add prebuilt entities (Aggiungi entità predefinite)](./media/luis-prebuilt-domains/add-prebuilt-entity.png)

1. Selezionare **Fine**. Dopo aver aggiunto l'entità, non è necessario eseguire il training dell'app.

## <a name="add-a-prebuilt-domain-entity"></a>Aggiungere un'entità di dominio predefinita
1. Accedere al [portale LUIS](https://www.luis.ai) e quindi selezionare la **sottoscrizione** e la **risorsa di creazione** per vedere le app assegnate a tale risorsa.
1. Per aprire l'app, selezionarne il nome nella pagina **app personali** .
1. Selezionare **entità** sul lato sinistro.

1. Nella pagina **entità** selezionare **Aggiungi entità di dominio predefinita**.

1. Nella finestra di dialogo **Aggiungi modelli di dominio predefiniti** selezionare l'entità di dominio predefinita.

1. Selezionare **Fine**. Dopo aver aggiunto l'entità, non è necessario eseguire il training dell'app.

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>Pubblicazione per la visualizzazione del modello predefinito dall'endpoint di stima

Il modo più semplice per visualizzare il valore di un modello predefinito consiste nell'eseguire una query dall'endpoint pubblicato.

## <a name="entities-containing-a-prebuilt-entity-token"></a>Entità contenenti un token di entità predefinito

Se si dispone di un'entità di Machine Learning che necessita di una funzionalità obbligatoria di un'entità predefinita, aggiungere una sottoentità all'entità Machine Learning, quindi aggiungere una funzionalità _obbligatoria_ di un'entità precompilata.

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Compilare il modello da CSV con le API REST](./luis-tutorial-node-import-utterances-csv.md)
