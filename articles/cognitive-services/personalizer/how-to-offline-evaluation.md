---
title: Come eseguire la valutazione offline-personalizzatore
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra come usare la valutazione offline per misurare l'efficacia dell'app e analizzare il ciclo di apprendimento.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: a473085f9c94ca42a75d01b342d60cc33836b096
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88244840"
---
# <a name="analyze-your-learning-loop-with-an-offline-evaluation"></a>Analizzare il ciclo di apprendimento con una valutazione offline

Informazioni su come completare una valutazione offline e comprendere i risultati.

Le valutazioni offline consentono di misurare il modo in cui il Personalizzatore effettivo viene confrontato con il comportamento predefinito dell'applicazione, scoprire quali funzionalità contribuiscono maggiormente alla personalizzazione e individuare automaticamente nuovi valori di machine learning.

Altre informazioni sulle [valutazioni offline](concepts-offline-evaluation.md).

## <a name="prerequisites"></a>Prerequisiti

* Un ciclo di personalizzazione configurato
* Il ciclo di personalizzazione deve avere una quantità rappresentativa di dati, come Ballpark, per ottenere risultati di valutazione significativi è consigliabile usare almeno 50.000 eventi nei log. Facoltativamente, è anche possibile confrontare e testare i file dei _criteri di valutazione_ esportati in precedenza.

## <a name="run-an-offline-evaluation"></a>Eseguire una valutazione offline

1. Nella [portale di Azure](https://azure.microsoft.com/free/cognitive-services)individuare la risorsa di personalizzazione.
1. Nella portale di Azure passare alla sezione **valutazioni** e selezionare **Crea valutazione**.
    ![Nella portale di Azure passare alla sezione * * evaluations * * e selezionare * * Crea Evaluation * *.](./media/offline-evaluation/create-new-offline-evaluation.png)
1. Configurare i valori seguenti:

    * Nome della valutazione.
    * Data di inizio e di fine: date che specificano l'intervallo di dati da usare nella valutazione. Questi dati devono essere presenti nei log, come specificato nel valore di [conservazione dei dati](how-to-settings.md) .
    * L'individuazione dell'ottimizzazione è impostata su **Sì**.

    > [!div class="mx-imgBorder"]
    > ![Scegliere le impostazioni di valutazione offline](./media/offline-evaluation/create-an-evaluation-form.png)

1. Avviare la valutazione selezionando **OK**.

## <a name="review-the-evaluation-results"></a>Esaminare i risultati della valutazione

L'esecuzione delle valutazioni può richiedere molto tempo, a seconda della quantità di dati da elaborare, il numero di criteri di apprendimento da confrontare e dall'eventualità in cui sia stata richiesta l'ottimizzazione.

Al termine, è possibile selezionare la valutazione dall'elenco di valutazioni, quindi selezionare **Confronta il punteggio dell'applicazione con altre impostazioni di apprendimento potenziali**. Selezionare questa funzionalità quando si desidera visualizzare le prestazioni dei criteri di apprendimento correnti rispetto a un nuovo criterio.

1. Esaminare le prestazioni dei [criteri di formazione](concepts-offline-evaluation.md#discovering-the-optimized-learning-policy).

    > [!div class="mx-imgBorder"]
    > [![Esaminare i risultati della valutazione](./media/offline-evaluation/evaluation-results.png)](./media/offline-evaluation/evaluation-results.png#lightbox)

1. Selezionare **applica** per applicare il criterio che migliora il modello più adatto ai dati.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sul [funzionamento delle valutazioni offline](concepts-offline-evaluation.md).
