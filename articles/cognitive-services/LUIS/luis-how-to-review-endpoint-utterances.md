---
title: Esaminare le espressioni utente - LUISReview user utterances - LUIS
titleSuffix: Azure Cognitive Services
description: Esaminare le espressioni acquisite dall'apprendimento attivo per selezionare le finalità e contrassegnare le entità per le espressioni del mondo di lettura. accettare le modifiche, addestrare e pubblicare.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: diberry
ms.openlocfilehash: 95b7c7446a47fafd26d00b0da4d880786340fcd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219852"
---
# <a name="how-to-improve-the-luis-app-by-reviewing-endpoint-utterances"></a>Come migliorare l'app LUIS esaminando le espressioni endpoint

Il processo di revisione delle espressioni endpoint per le stime corrette è denominato [Apprendimento attivo](luis-concept-review-endpoint-utterances.md). L'apprendimento attivo acquisisce le query endpoint e seleziona le espressioni endpoint dell'utente di cui non è sicuro. Queste espressioni vengono esaminate per selezionare la finalità e contrassegnare le entità per queste espressioni del mondo di lettura. Accettare queste modifiche nelle espressioni di esempio, quindi eseguire il training e la pubblicazione. LUIS identifica quindi le espressioni in modo più accurato.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="enable-active-learning"></a>Consentire l'apprendimento attivo

Per abilitare l'apprendimento attivo, è necessario registrare le query utente. Questa operazione viene eseguita chiamando `log=true` la [query dell'endpoint](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) con il valore e il parametro querystring.

Utilizzare il portale LUIS per creare la query dell'endpoint corretta.

1. Nel [portale LUIS](https://preview.luis.ai/)preview selezionare l'app dall'elenco delle app.
1. Passare alla sezione **Gestisci,** quindi selezionare **Risorse di Azure.**
1. Per la risorsa di stima assegnata, selezionare **Modifica parametri di query**.

    > [!div class="mx-imgBorder"]
    > ![Utilizzare il portale LUIS per salvare i log, necessari per l'apprendimento attivo.](./media/luis-tutorial-review-endpoint-utterances/azure-portal-change-query-url-settings.png)

1. Attiva/disattiva **Salva registri,** quindi salva selezionando **Fine**.

    > [!div class="mx-imgBorder"]
    > ![Utilizzare il portale LUIS per salvare i log, necessari per l'apprendimento attivo.](./media/luis-tutorial-review-endpoint-utterances/luis-portal-manage-azure-resource-save-logs.png)

     Questa azione modifica l'URL di esempio aggiungendo il parametro di stringa di query `log=true`. Copiare e usare l'URL della query di esempio modificato per l'esecuzione di query di previsione nell'endpoint di runtime.

## <a name="correct-intent-predictions-to-align-utterances"></a>Correggere le stime delle finalità per allineare le espressioniCorrect intent predictions to align utterances

Ogni espressione ha una finalità consigliata che è visualizzata nella colonna **Finalità allineata**.

> [!div class="mx-imgBorder"]
> [![Esaminare le espressioni endpoint di cui LUIS non è sicuro](./media/label-suggested-utterances/review-endpoint-utterances.png)](./media/label-suggested-utterances/review-endpoint-utterances.png#lightbox)

Se si è d'accordo con tale finalità, selezionare il segno di spunta. Se non concordi con il suggerimento, seleziona la finalità corretta dall'elenco a discesa della finalità allineata, quindi seleziona il segno di spunta a destra della finalità allineata. Dopo aver selezionato il segno di spunta, l'espressione viene spostata alla finalità e rimossa dall'elenco **Rivedere le espressioni endpoint.**

> [!TIP]
> È importante passare alla pagina Dettagli intento per esaminare e correggere le stime di entità da tutte le espressioni di esempio dall'elenco **Rivedi espressioni endpoint.**

## <a name="delete-utterance"></a>Eliminare le espressioni

Ogni espressione può essere eliminata dall'elenco di riesame. Una volta eliminate, queste non verranno più visualizzate nell'elenco. Questo vale anche se l'utente inserisce la stessa espressione dall'endpoint.

Se non si è sicuri se è necessario eliminare l'espressione, spostarla nella finalità Nessuno oppure creare una nuova finalità, ad `miscellaneous` esempio e spostare l'espressione in tale finalità.

## <a name="disable-active-learning"></a>Disabilitare l'apprendimento attivo

Per disabilitare l'apprendimento attivo, non registrare le query utente. Questa operazione viene eseguita impostando `log=false` la [query dell'endpoint](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) con il parametro querystring e valore o non utilizzando il valore querystring perché il valore predefinito è false.

## <a name="next-steps"></a>Passaggi successivi

Per verificare come le prestazioni migliorino dopo l'assegnazione dell'etichetta alle espressioni consigliate, è possibile accedere alla console di test selezionando **Testa** nel riquadro superiore. Per istruzioni su come testare l'applicazione usando la console di test, vedere [Eseguire il training e testare l'applicazione](luis-interactive-test.md).
