---
title: Etichettare espressioni consigliate con LUIS | Microsoft Docs
description: Usare Language Understanding (LUIS) per assegnare un'etichetta a espressioni consigliate e favorire l'apprendimento automatico attivo.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/08/2017
ms.author: diberry
ms.openlocfilehash: 5e195b8ef5aeb35b73c22438980fe2b2e3856977
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224552"
---
# <a name="review-endpoint-utterances"></a>Esaminare le espressioni endpoint

La funzionalità all'avanguardia di LUIS è il [fulcro concettuale](luis-concept-review-endpoint-utterances.md) dell'apprendimento attivo. Una volta contenente le query di endpoint, LUIS usa l'apprendimento attivo per migliorare la qualità dei risultati. Nel processo di apprendimento attivo, LUIS esamina tutte le espressioni di endpoint e seleziona quelle di cui non è sicuro. Se etichetti queste espressioni, vi esegui il training e le pubblichi, LUIS le identificherà in modo più accurato. 

## <a name="filter-utterances"></a>Filtrare le espressioni
1. Apri l'app (ad esempio, TravelAgent) selezionando il relativo nome nella pagina **App personali**, quindi seleziona **Compila** nella barra superiore.

2. Nella sezione **Migliora le prestazioni dell'applicazione**, seleziona **Esamina espressioni di endpoint**.

    ![Esaminare le espressioni](./media/label-suggested-utterances/review.png)

3. Nella pagina **Esamina espressioni di endpoint**, seleziona la casella di testo **Filtra l'elenco per finalità o entità**. Questo elenco a discesa include tutti e le finalità nella sezione **FINALITÁ** e tutte le entità in **ENTITÁ**.

    ![Filtro delle espressioni](./media/label-suggested-utterances/filter.png)

4. Seleziona una categoria (finalità o entità) dall'elenco a discesa, quindi esamina le espressioni.

    ![Espressioni di finalità](./media/label-suggested-utterances/intent-utterances.png)

## <a name="label-entities"></a>Etichettare le entità
LUIS sostituisce i token di entità (parole) con i nomi delle entità evidenziate in blu. Se un'espressione ha entità senza etichetta, è necessario etichettarle nell'espressione. 

1. Seleziona la parola/le parole nell'espressione. 

2. Seleziona un'entità dall'elenco.

    ![Etichettare l'entità](./media/label-suggested-utterances/label-entity.png)

## <a name="align-single-utterance"></a>Allineare una singola espressione

Ogni espressione ha una finalità consigliata che è visualizzata nella colonna **Finalità allineata**. 

1. Se accetti tale suggerimento, seleziona il segno di spunta.

    ![Mantenere la finalità allineata](./media/label-suggested-utterances/align-intent-check.png)

2. Se non concordi con il suggerimento, seleziona la finalità corretta dall'elenco a discesa della finalità allineata, quindi seleziona il segno di spunta a destra della finalità allineata. 

    ![Allineare la finalità](./media/label-suggested-utterances/align-intent.png)

3. Dopo aver selezionato il segno di spunta, l'espressione viene rimossa dall'elenco. 

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
