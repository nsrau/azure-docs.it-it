---
title: Configurare il comportamento di apprendimento
description: La modalità apprendista offre la confidenza nel servizio di personalizzazione e le funzionalità di apprendimento automatico e fornisce le metriche a cui il servizio riceve informazioni che possono essere apprese, senza rischiare il traffico in linea.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 05/01/2020
ms.openlocfilehash: 10e98cd2f0ad4793aa43f9bb3316c522b44f1d2c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91303540"
---
# <a name="configure-the-personalizer-learning-behavior"></a>Configurare il comportamento di apprendimento della personalizzazione

La [modalità apprendista](concept-apprentice-mode.md) offre attendibilità e confidenza nel servizio di personalizzazione e nelle funzionalità di apprendimento automatico e garantisce che il servizio sia in grado di inviare informazioni che possono essere apprese da, senza rischiare il traffico in linea.

[!INCLUDE [Important Blue Box - Apprentice mode pricing tier](./includes/important-apprentice-mode.md)]

## <a name="configure-apprentice-mode"></a>Configurare la modalità apprendista

1. Accedere al [portale di Azure](https://portal.azure.com)per la risorsa di personalizzazione.

1. Nella scheda **comportamento di apprendimento** della pagina **configurazione** selezionare **return Baseline Action, Learn As a Apprentice** , quindi selezionare **Save**.

> [!div class="mx-imgBorder"]
> ![Screenshot della configurazione del comportamento di apprendimento in modalità apprendista in portale di Azure](media/settings/configure-learning-behavior-azure-portal.png)

## <a name="changes-to-the-existing-application"></a>Modifiche all'applicazione esistente

L'applicazione esistente non deve modificare il modo in cui vengono attualmente selezionate le azioni da visualizzare o la modalità **di determinazione** del valore da parte dell'applicazione. L'unica modifica all'applicazione potrebbe essere l'ordine delle azioni inviate all'API Rank del personalizzatore. L'azione attualmente visualizzata dall'applicazione viene inviata come _prima azione_ nell'elenco di azioni. L' [API Rank](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) usa questa prima azione per eseguire il training del modello di personalizzazione.

### <a name="configure-your-application-to-call-the-rank-api"></a>Configurare l'applicazione in modo che chiami l'API Rank

Per aggiungere il Personalizzatore all'applicazione, è necessario chiamare le API Rank e Reward.

1. Aggiungere la chiamata all' [API Rank](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) dopo il punto nella logica dell'applicazione esistente in cui si determina l'elenco di azioni e le relative funzionalità. La prima azione nell'elenco di azioni deve essere l'azione selezionata dalla logica esistente.

1. Configurare il codice per visualizzare l'azione associata all' **ID dell'azione Reward**della risposta dell'API di rango.

### <a name="configure-your-application-to-call-reward-api"></a>Configurare l'applicazione per chiamare l'API Reward

1. Usare la logica di business esistente per calcolare la **ricompensa** dell'azione visualizzata. Il valore deve essere compreso tra 0 e 1. Inviare questo premio a Personalizzator usando l' [API Reward](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward). Il valore Reward non è previsto immediatamente e può essere ritardato in un periodo di tempo, a seconda della logica di business.

1. Se non si restituisce il premio entro il **tempo di attesa della ricompensa**configurato, verrà usata la ricompensa predefinita.

## <a name="evaluate-apprentice-mode"></a>Valuta modalità Apprentice

Nella portale di Azure, nella pagina **valutazioni** per la risorsa di personalizzazione, esaminare le prestazioni del **comportamento di apprendimento corrente**.

> [!div class="mx-imgBorder"]
> ![Screenshot della revisione della valutazione del comportamento di apprendimento in modalità apprendista in portale di Azure](media/settings/evaluate-apprentice-mode.png)

La modalità apprendista fornisce le **metriche di valutazione**seguenti:
* **Baseline-ricompensa media**: vantaggi medi dell'impostazione predefinita dell'applicazione (baseline).
* **Personalizzatore-ricompensa media**: è stata potenzialmente raggiunta la media del personalizzatore dei premi totali.
* **Percentuale di successo del premio per gli eventi 1000 più recenti**: rapporto tra la linea di base e la ricompensa della personalizzazione, normalizzato rispetto agli eventi 1000 più recenti.

## <a name="evaluate-apprentice-mode-features"></a>Valutare le funzionalità della modalità Apprentice

Valutare le funzionalità usando una [valutazione offline](how-to-offline-evaluation.md).

## <a name="switch-behavior-to-online-mode"></a>Cambiare il comportamento in modalità online

Quando si determina il training della personalizzazione con una media mobile del 75-85%, il modello è pronto per passare alla modalità online.

Nella finestra di portale di Azure per la risorsa di personalizzazione, nella scheda **comportamento di apprendimento** della pagina **configurazione** selezionare **Restituisci l'azione migliore** , quindi selezionare **Salva**.

Non è necessario apportare alcuna modifica alle chiamate API Rank e Reward.

## <a name="next-steps"></a>Passaggi successivi

* [Gestire il modello e le impostazioni di apprendimento](how-to-manage-model.md)
