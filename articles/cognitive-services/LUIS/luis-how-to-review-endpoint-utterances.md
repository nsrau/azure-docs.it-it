---
title: Esaminare le espressioni utente-LUIS
titleSuffix: Azure Cognitive Services
description: L'apprendimento attivo acquisisce le query dell'endpoint e seleziona le espressioni dell'endpoint dell'utente che non sono sicure. Si esaminano queste espressioni per selezionare le entità Intent e Mark per queste espressioni di lettura. Accettare queste modifiche nelle espressioni di esempio, quindi eseguire il training e la pubblicazione. LUIS identifica quindi le espressioni in modo più accurato.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: d5652857f2f35e392d3f512001044fd06bc0a0c9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499080"
---
# <a name="how-to-review-endpoint-utterances-in-luis-portal-for-active-learning"></a>Come esaminare le espressioni di endpoint nel portale LUIS per l'apprendimento attivo

L' [apprendimento attivo](luis-concept-review-endpoint-utterances.md) acquisisce le query dell'endpoint e seleziona le espressioni dell'endpoint dell'utente che non sono sicure. Si esaminano queste espressioni per selezionare le entità Intent e Mark per queste espressioni di lettura. Accettare queste modifiche nelle espressioni di esempio, quindi eseguire il training e la pubblicazione. LUIS identifica quindi le espressioni in modo più accurato.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="enable-active-learning"></a>Abilita apprendimento attivo

Per abilitare l'apprendimento attivo, registrare le query utente. Questa operazione viene eseguita impostando la [query dell'endpoint](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) con il `log=true` parametro e il valore QueryString.

## <a name="disable-active-learning"></a>Disabilitare l'apprendimento attivo

Per disabilitare l'apprendimento attivo, non registrare le query utente. Questa operazione viene eseguita impostando la [query dell'endpoint](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) con il `log=false` parametro e il valore QueryString.

## <a name="filter-utterances"></a>Filtrare le espressioni

1. Apri l'app (ad esempio, TravelAgent) selezionando il relativo nome nella pagina **App personali**, quindi seleziona **Compila** nella barra superiore.

1. Nella sezione **Migliora le prestazioni dell'applicazione**, seleziona **Esamina espressioni di endpoint**.

1. Nella pagina **Esamina espressioni di endpoint**, seleziona la casella di testo **Filtra l'elenco per finalità o entità**. Questo elenco a discesa include tutti e le finalità nella sezione **FINALITÁ** e tutte le entità in **ENTITÁ**.

    ![Filtro delle espressioni](./media/label-suggested-utterances/filter.png)

1. Seleziona una categoria (finalità o entità) dall'elenco a discesa, quindi esamina le espressioni.

    ![Espressioni di finalità](./media/label-suggested-utterances/intent-utterances.png)

## <a name="label-entities"></a>Etichettare le entità
LUIS sostituisce i token di entità (parole) con i nomi delle entità evidenziate in blu. Se un'espressione ha entità senza etichetta, è necessario etichettarle nell'espressione. 

1. Seleziona la parola/le parole nell'espressione. 

1. Seleziona un'entità dall'elenco.

    ![Etichettare l'entità](./media/label-suggested-utterances/label-entity.png)

## <a name="align-single-utterance"></a>Allineare una singola espressione

Ogni espressione ha una finalità consigliata che è visualizzata nella colonna **Finalità allineata**. 

1. Se accetti tale suggerimento, seleziona il segno di spunta.

    ![Mantenere la finalità allineata](./media/label-suggested-utterances/align-intent-check.png)

1. Se non concordi con il suggerimento, seleziona la finalità corretta dall'elenco a discesa della finalità allineata, quindi seleziona il segno di spunta a destra della finalità allineata. 

    ![Allineare la finalità](./media/label-suggested-utterances/align-intent.png)

1. Dopo aver selezionato il segno di spunta, l'espressione viene rimossa dall'elenco. 

## <a name="align-several-utterances"></a>Allineare diverse espressioni

Per allineare diverse espressioni, seleziona la casella a sinistra dell'espressione, quindi seleziona il pulsante **Aggiungi selezionata**. 

![Allinea diverse](./media/label-suggested-utterances/add-selected.png)

## <a name="verify-aligned-intent"></a>Verifica la finalità allineata

È possibile verificare se l'espressione è stata allineata con la finalità corretta andando alla pagina **Finalità**. Potrai quindi selezionare il nome della finalità ed esaminare le espressioni. L'espressione in **Esamina espressioni di endpoint** è presente nell'elenco.

## <a name="delete-utterance"></a>Eliminare le espressioni

Ogni espressione può essere eliminata dall'elenco di riesame. Una volta eliminate, queste non verranno più visualizzate nell'elenco. Questo vale anche se l'utente inserisce la stessa espressione dall'endpoint. 

Se non sei sicuro di voler eliminare l'espressione, spostala nella finalità Nessuna o crea una nuova finalità, ad esempio "varie" e sposta l'espressione in tale finalità. 

## <a name="delete-several-utterances"></a>Eliminare diverse espressioni

Per eliminare diverse espressioni, seleziona ciascun elemento e scegli il cestino a destra del pulsante **Aggiungi selezionata**.

![Elimina diverse](./media/label-suggested-utterances/delete-several.png)


## <a name="next-steps"></a>Passaggi successivi

Per verificare come le prestazioni migliorino dopo l'assegnazione dell'etichetta alle espressioni consigliate, è possibile accedere alla console di test selezionando **Testa** nel riquadro superiore. Per istruzioni su come testare l'applicazione usando la console di test, vedere [Eseguire il training e testare l'applicazione](luis-interactive-test.md).
