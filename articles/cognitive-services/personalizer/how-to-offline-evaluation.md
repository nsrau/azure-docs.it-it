---
title: Come eseguire la valutazione offline-personalizzatore
titleSuffix: Azure Cognitive Services
description: Informazioni su come analizzare il ciclo di apprendimento con una valutazione offline
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: 9d7336fb933ee5e9781d15214fa3e59446aa5b6a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490737"
---
# <a name="analyze-your-learning-loop-with-an-offline-evaluation"></a>Analizzare il ciclo di apprendimento con una valutazione offline

Informazioni su come completare una valutazione offline e comprendere i risultati.

Le valutazioni offline consentono di misurare il modo in cui il Personalizzatore effettivo viene confrontato con il comportamento predefinito dell'applicazione, scoprire quali funzionalità contribuiscono maggiormente alla personalizzazione e individuare automaticamente nuovi valori di machine learning.

Altre informazioni sulle [valutazioni offline](concepts-offline-evaluation.md).


## <a name="prerequisites"></a>Prerequisiti

* Un ciclo di personalizzazione configurato
* Il ciclo di personalizzazione deve avere una quantità rappresentativa di dati, come Ballpark, per ottenere risultati di valutazione significativi è consigliabile usare almeno 50.000 eventi nei log. Facoltativamente, è anche possibile confrontare e testare i file dei _criteri di valutazione_ esportati in precedenza.

## <a name="steps-to-start-a-new-offline-evaluation"></a>Passaggi per avviare una nuova valutazione offline

1. Nella [portale di Azure](https://azure.microsoft.com/free/)individuare la risorsa di personalizzazione.
1. Nella portale di Azure passare alla sezione **valutazioni** e selezionare **Crea valutazione**.
    ![nella portale di Azure, passare alla sezione * * valutazioni * * e selezionare * * Crea valutazione * *.](./media/offline-evaluation/create-new-offline-evaluation.png)
1. Configurare i valori seguenti:

    * Nome della valutazione
    * Data di inizio e di fine. si tratta di date nel passato, che specificano l'intervallo di dati da usare nella valutazione. Questi dati devono essere presenti nei log, come specificato nel valore di [conservazione dei dati](how-to-settings.md) .
    * Individuazione ottimizzazione impostata su **Sì**

    ![Scegliere le impostazioni di valutazione offline](./media/offline-evaluation/create-an-evaluation-form.png)

1. Avviare la valutazione selezionando **OK**. 

## <a name="results"></a>Risultati

L'esecuzione delle valutazioni può richiedere molto tempo, a seconda della quantità di dati da elaborare, il numero di criteri di apprendimento da confrontare e dall'eventualità in cui sia stata richiesta l'ottimizzazione.

Al termine, è possibile selezionare la valutazione dall'elenco di valutazioni. 

I confronti dei criteri di apprendimento includono:

* **Criteri online**: i criteri di apprendimento correnti usati per la personalizzazione
* **Baseline**: il valore predefinito dell'applicazione (come determinato dalla prima azione inviata in Rank Calls),
* **Criteri casuali**: un comportamento di rango immaginario che restituisce sempre una scelta casuale di azioni da quelle fornite.
* **Criteri personalizzati**: i criteri di apprendimento aggiuntivi sono stati caricati all'avvio della valutazione.
* **Criteri ottimizzati**: se la valutazione è stata avviata con l'opzione per individuare un criterio ottimizzato, verrà confrontato anche e sarà possibile scaricarlo o impostarlo come criterio di apprendimento online, sostituendo quello corrente.

![Grafico dei risultati delle impostazioni di valutazione offline](./media/offline-evaluation/evaluation-results.png)

Efficacia delle [funzionalità](concepts-features.md) per azioni e contesto.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sul [funzionamento delle valutazioni offline](concepts-offline-evaluation.md).
