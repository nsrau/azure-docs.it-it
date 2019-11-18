---
title: Esaminare le espressioni utente-LUIS
titleSuffix: Azure Cognitive Services
description: Esaminare le espressioni acquisite da Active Learning per selezionare le entità Intent e Mark per le espressioni Read-World; accettare le modifiche, eseguire il training e pubblicare.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 67953f552b5b2bcdd7d13253548227e57dab8548
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132665"
---
# <a name="how-to-improve-the-luis-app-by-reviewing-endpoint-utterances"></a>Come migliorare l'app LUIS esaminando gli enunciati degli endpoint

Il processo di revisione degli enunciati degli endpoint per le stime corrette è denominato [apprendimento attivo](luis-concept-review-endpoint-utterances.md). L'apprendimento attivo acquisisce le query dell'endpoint e seleziona le espressioni dell'endpoint dell'utente che non sono sicure. Si esaminano queste espressioni per selezionare le entità Intent e Mark per queste espressioni di lettura. Accettare queste modifiche nelle espressioni di esempio, quindi eseguire il training e la pubblicazione. LUIS identifica quindi le espressioni in modo più accurato.

Se sono presenti molti utenti che contribuiscono a un'app LUIS, 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="enable-active-learning"></a>Abilita apprendimento attivo

Per abilitare l'apprendimento attivo, è necessario registrare le query utente. Questa operazione viene eseguita chiamando la [query dell'endpoint](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) con il `log=true` parametro e il valore QueryString.

## <a name="correct-intent-predictions-to-align-utterances"></a>Correggere le stime di finalità per allineare le espressioni

Ogni espressione ha una finalità consigliata che è visualizzata nella colonna **Finalità allineata**. 

> [!div class="mx-imgBorder"]
> [![rivedere le espressioni dell'endpoint che LUIS non è sicuro](./media/label-suggested-utterances/review-endpoint-utterances.png)](./media/label-suggested-utterances/review-endpoint-utterances.png#lightbox)

Se si accetta tale scopo, selezionare il segno di spunta. Se non concordi con il suggerimento, seleziona la finalità corretta dall'elenco a discesa della finalità allineata, quindi seleziona il segno di spunta a destra della finalità allineata. Dopo aver selezionato il segno di spunta, l'espressione viene spostata nella finalità ed è stata rimossa dall'elenco **Verifica espressioni endpoint** . 

> [!TIP]
> È importante passare alla pagina dei dettagli dello scopo per esaminare e correggere le stime delle entità da tutte le espressioni di esempio dall'elenco **Verifica espressioni endpoint** .

## <a name="delete-utterance"></a>Eliminare le espressioni

Ogni espressione può essere eliminata dall'elenco di riesame. Una volta eliminate, queste non verranno più visualizzate nell'elenco. Questo vale anche se l'utente inserisce la stessa espressione dall'endpoint. 

Se non si è certi che sia necessario eliminare l'espressione, spostarla nella finalità None oppure creare un nuovo scopo, ad esempio `miscellaneous` e spostare l'espressione a tale scopo. 

## <a name="disable-active-learning"></a>Disabilitare l'apprendimento attivo

Per disabilitare l'apprendimento attivo, non registrare le query utente. Questa operazione viene eseguita impostando la [query dell'endpoint](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) con il `log=false` parametro e il valore QueryString oppure non usando il valore QueryString perché il valore predefinito è false.

## <a name="next-steps"></a>Passaggi successivi

Per verificare come le prestazioni migliorino dopo l'assegnazione dell'etichetta alle espressioni consigliate, è possibile accedere alla console di test selezionando **Testa** nel riquadro superiore. Per istruzioni su come testare l'applicazione usando la console di test, vedere [Eseguire il training e testare l'applicazione](luis-interactive-test.md).
