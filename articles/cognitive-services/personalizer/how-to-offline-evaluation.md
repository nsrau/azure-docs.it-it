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
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: a8a75601daf36ca21ea56a5930219d7d467f0c85
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69557811"
---
# <a name="how-to-analyze-your-learning-loop-with-an-offline-evaluation"></a>Come analizzare il ciclo di apprendimento con una valutazione offline


Informazioni su come completare una valutazione offline e comprendere i risultati.

Le valutazioni offline consentono di misurare l'efficacia di Personalizza esperienze rispetto al comportamento predefinito dell'applicazione, scoprire quali funzionalità contribuiscono maggiormente alla personalizzazione e individuare nuove impostazioni di apprendimento automatico automaticamente.

Altre informazioni sulle [valutazioni offline](concepts-offline-evaluation.md).


## <a name="prerequisites"></a>Prerequisiti

1. È necessario aver configurato un ciclo di Personalizza esperienze
1. Il ciclo di personalizzazione deve avere una quantità rappresentativa di dati, come Ballpark, recommmend almeno 50.000 eventi nei log per ottenere risultati significativi della valutazione.

Facoltativamente, è anche possibile confrontare e testare i file dei _criteri di valutazione_ esportati in precedenza.

## <a name="steps-to-start-a-new-offline-evaluation"></a>Passaggi per avviare una nuova valutazione offline

1. Individuare la risorsa del ciclo di personalizzazione nel portale di Azure.
1. Passare alla sezione "Evaluation" (Valutazione).
1. Fare clic su New Evaluation (Nuova valutazione)
1. Selezionare una data di inizio e fine per la valutazione offline. Devono essere date nel passato, che specificano l'intervallo di dati da usare nella valutazione. Questi dati devono essere presenti nei log, come specificato nell'impostazione [Conservazione dei dati](how-to-settings.md).
1. Facoltativamente, è possibile caricare i propri criteri di apprendimento. 
1. Specificare se Personalizza esperienze deve creare un criterio di apprendimento ottimizzato basato sul comportamento dell'utente osservato in questo periodo di tempo.
1. Avviare la valutazione

## <a name="results"></a>Risultati

L'esecuzione delle valutazioni può richiedere molto tempo, a seconda della quantità di dati da elaborare, il numero di criteri di apprendimento da confrontare e dall'eventualità in cui sia stata richiesta l'ottimizzazione.

Una volta completate, i risultati visualizzati sono simili ai seguenti:

1. Confronti di criteri di apprendimento, tra cui:
    * **Criteri online**: i criteri di apprendimento correnti usati in Personalizza esperienze
    * **Baseline**: valore predefinito dell'applicazione (come determinato dalla prima azione inviata nelle chiamate di classificazione)
    * **Criteri casuali**: un comportamento di classificazione immaginario che restituisce sempre una scelta casuale di azioni da quelle fornite.
    * **Criteri personalizzati**: ulteriori criteri di apprendimento caricati all'avvio della valutazione.
    * **Criteri ottimizzati**: se la valutazione è stata avviata con l'opzione di individuare un criterio ottimizzato, anch'esso verrà confrontato e sarà possibile scaricarlo o renderlo il criterio di apprendimento online, sostituendo quello corrente.

1. Efficacia delle [funzionalità](concepts-features.md) per azioni e contesto.


## <a name="more-information"></a>Altre informazioni

* Informazioni sul [funzionamento delle valutazioni offline](concepts-offline-evaluation.md).
