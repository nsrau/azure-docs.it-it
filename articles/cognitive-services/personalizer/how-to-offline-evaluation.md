---
title: Come eseguire la valutazione offline - Personalizer
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra come usare la valutazione offline per misurare l'efficacia dell'app e analizzare il ciclo di apprendimento.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: diberry
ms.openlocfilehash: ce85c2d264b2b4849a4a36ed757150292fdf39f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77622796"
---
# <a name="analyze-your-learning-loop-with-an-offline-evaluation"></a>Analizza il tuo ciclo di apprendimento con una valutazione offline

Informazioni su come completare una valutazione offline e comprendere i risultati.

Le valutazioni offline consentono di misurare l'efficacia del programma Personalizer rispetto al comportamento predefinito dell'applicazione, di apprendere le funzionalità che contribuiscono maggiormente alla personalizzazione e di individuare automaticamente i nuovi valori di apprendimento automatico.

Altre informazioni sulle [valutazioni offline](concepts-offline-evaluation.md).

## <a name="prerequisites"></a>Prerequisiti

* Un ciclo Personalizer configurato
* Il ciclo Personalizer deve avere una quantità rappresentativa di dati - come un campo da baseball consigliamo almeno 50.000 eventi nei suoi registri per risultati di valutazione significativi. Facoltativamente, è anche possibile confrontare e testare i file dei _criteri di valutazione_ esportati in precedenza.

## <a name="run-an-offline-evaluation"></a>Eseguire una valutazione offline

1. Nel [portale](https://azure.microsoft.com/free/)di Azure individuare la risorsa Personalizer.In the Azure portal , locate your Personalizer resource.
1. Nel portale di Azure passare alla sezione **Valutazioni** e selezionare **Crea valutazione**.
    ![Nel portale di Azure passare alla sezione relativa alle valutazioni e selezionare crea la valutazione.](./media/offline-evaluation/create-new-offline-evaluation.png)
1. Configurare i valori seguenti:

    * Un nome di valutazione.
    * Data di inizio e di fine: si tratta di date che specificano l'intervallo di dati da utilizzare nella valutazione. Questi dati devono essere presenti nei registri, come specificato nel valore [conservazione dei dati.](how-to-settings.md)
    * Ottimizzazione individuazione impostata su **yes**.

    > [!div class="mx-imgBorder"]
    > ![Scegliere le impostazioni di valutazione offline](./media/offline-evaluation/create-an-evaluation-form.png)

1. Avviare la valutazione selezionando **Ok**.

## <a name="review-the-evaluation-results"></a>Esaminare i risultati della valutazione

L'esecuzione delle valutazioni può richiedere molto tempo, a seconda della quantità di dati da elaborare, il numero di criteri di apprendimento da confrontare e dall'eventualità in cui sia stata richiesta l'ottimizzazione.

Al termine, è possibile selezionare la valutazione dall'elenco delle valutazioni, quindi selezionare **Confronta il punteggio dell'applicazione con altre potenziali impostazioni**di apprendimento . Selezionare questa funzionalità quando si desidera visualizzare le prestazioni dei criteri di apprendimento correnti rispetto a un nuovo criterio.

1. Esaminare le prestazioni delle [politiche](concepts-offline-evaluation.md#discovering-the-optimized-learning-policy)di apprendimento .

    > [!div class="mx-imgBorder"]
    > [![Esaminare i risultati della valutazioneReview evaluation results](./media/offline-evaluation/evaluation-results.png)](./media/offline-evaluation/evaluation-results.png#lightbox)

1. Selezionare **Applica** per applicare il criterio che migliora il modello più adatto ai dati.

## <a name="next-steps"></a>Passaggi successivi

* Ulteriori informazioni sul [funzionamento delle valutazioni offline](concepts-offline-evaluation.md).
