---
title: Valutazione offline-personalizzatore
titleSuffix: Azure Cognitive Services
description: Informazioni su come analizzare il ciclo di apprendimento con una valutazione offline
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: diberry
ms.openlocfilehash: a157b36ad2b4f5a7f2e50a9609d8acd621efd5a8
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70884459"
---
# <a name="analyze-your-learning-loop-with-an-offline-evaluation"></a>Analizzare il ciclo di apprendimento con una valutazione offline

Informazioni su come completare una valutazione offline e comprendere i risultati.

Le valutazioni offline consentono di misurare l'efficacia di Personalizza esperienze rispetto al comportamento predefinito dell'applicazione, scoprire quali funzionalità contribuiscono maggiormente alla personalizzazione e individuare nuove impostazioni di apprendimento automatico automaticamente.

Altre informazioni sulle [valutazioni offline](concepts-offline-evaluation.md).


## <a name="prerequisites"></a>Prerequisiti

* Un ciclo di personalizzazione configurato
* Il ciclo di personalizzazione deve avere una quantità rappresentativa di dati, come Ballpark, per ottenere risultati di valutazione significativi è consigliabile usare almeno 50.000 eventi nei log. Facoltativamente, è anche possibile confrontare e testare i file dei _criteri di valutazione_ esportati in precedenza.

## <a name="steps-to-start-a-new-offline-evaluation"></a>Passaggi per avviare una nuova valutazione offline

1. Nella [portale di Azure](https://azure.microsoft.com/free/)individuare la risorsa di personalizzazione.
1. Nella portale di Azure passare alla sezione **valutazioni** e selezionare **Crea valutazione**.
    ![Nella portale di Azure passare alla sezione * * evaluations * * e selezionare * * Crea Evaluation * *.](./media/offline-evaluation/create-new-offline-evaluation.png)
1. Selezionare le impostazioni seguenti:

    * Nome della valutazione
    * Data di inizio e di fine. si tratta di date nel passato, che specificano l'intervallo di dati da usare nella valutazione. Questi dati devono essere presenti nei log, come specificato nell'impostazione [Conservazione dei dati](how-to-settings.md).
    * Individuazione ottimizzazione impostata su **Sì**

    ![Scegliere le impostazioni di valutazione offline](./media/offline-evaluation/create-an-evaluation-form.png)

1. Avviare la valutazione selezionando **OK**. 

## <a name="results"></a>Risultati

L'esecuzione delle valutazioni può richiedere molto tempo, a seconda della quantità di dati da elaborare, il numero di criteri di apprendimento da confrontare e dall'eventualità in cui sia stata richiesta l'ottimizzazione.

Al termine, è possibile selezionare la valutazione dall'elenco di valutazioni. 

I confronti dei criteri di apprendimento includono:

* **Criteri online**: i criteri di apprendimento correnti usati in Personalizza esperienze
* **Baseline**: valore predefinito dell'applicazione (come determinato dalla prima azione inviata nelle chiamate di classificazione)
* **Criteri casuali**: un comportamento di classificazione immaginario che restituisce sempre una scelta casuale di azioni da quelle fornite.
* **Criteri personalizzati**: ulteriori criteri di apprendimento caricati all'avvio della valutazione.
* **Criteri ottimizzati**: se la valutazione è stata avviata con l'opzione di individuare un criterio ottimizzato, anch'esso verrà confrontato e sarà possibile scaricarlo o renderlo il criterio di apprendimento online, sostituendo quello corrente.

![Grafico dei risultati delle impostazioni di valutazione offline](./media/offline-evaluation/evaluation-results.png)

Efficacia delle [funzionalità](concepts-features.md) per azioni e contesto.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sul [funzionamento delle valutazioni offline](concepts-offline-evaluation.md).
