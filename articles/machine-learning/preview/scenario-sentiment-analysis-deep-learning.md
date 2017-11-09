---
title: Analisi del sentiment tramite tecniche di apprendimento avanzato con Azure Machine Learning | Microsoft Docs
description: Come eseguire l'analisi del sentiment tramite tecniche di apprendimento avanzato con Azure Machine Learning Workbench.
services: machine-learning
documentationcenter: 
author: miprasad
manager: kristin.tolle
editor: miprasad
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2017
ms.author: miprasad
ms.openlocfilehash: 39ae2aa7217b45e8fab77f528b27b77a1b1256bf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="sentiment-analysis-using-deep-learning-with-azure-machine-learning"></a>Analisi del sentiment tramite tecniche di apprendimento avanzato con Azure Machine Learning

L'analisi del sentiment è un'attività ben nota negli scenari di elaborazione del linguaggio naturale. Dato un set di testi, l'obiettivo è determinare la valutazione del testo. L'obiettivo di questa soluzione è usare CNTK come back-end per Keras, una libreria a livello di modello che fornisce blocchi predefiniti di alto livello per lo sviluppo di modelli di apprendimento avanzato, e implementare l'analisi del sentiment da un insieme di recensioni cinematografiche.

La soluzione si trova in https://github.com/Azure/MachineLearningSamples-SentimentAnalysis

## <a name="link-to-the-gallery-github-repository"></a>Collegamento al repository GitHub delle raccolte

Seguire questo collegamento al repository GitHub pubblico:

[https://github.com/Azure/MachineLearningSamples-SentimentAnalysis](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis)

## <a name="use-case-overview"></a>Panoramica del caso d'uso

L'incredibile aumento dei dati e la diffusione dei dispositivi mobili hanno offerto ai clienti molte opportunità per esprimere i propri sentimenti e stati d'animo su qualsiasi elemento in ogni momento. Questa opinione o "sentiment" viene spesso generato tramite canali social sotto forma di recensioni, chat, condivisioni, Mi piace, tweet e così via. Questa valutazione può essere preziosa per le aziende che vogliono migliorare i propri prodotti e servizi, prendere decisioni più informate e promuovere al meglio i marchi.

Per ottenere valore dall'analisi del sentiment, le aziende devono poter eseguire il data mining di vasti archivi di dati di social networking non strutturati per ricavare informazioni di utilità pratica. In questo esempio si svilupperanno modelli di apprendimento avanzato per l'esecuzione dell'analisi del sentiment di un insieme di recensioni cinematografiche con Azure Machine Learning Workbench

## <a name="prerequisites"></a>Prerequisiti

* Un [account di Azure](https://azure.microsoft.com/free/) (sono disponibili versioni di valutazione gratuite).

* Una copia di [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) installata seguendo la [guida introduttiva all'installazione](./quickstart-installation.md) per installare il programma e creare un'area di lavoro.

* Per l'operazionalizzazione, è consigliabile installare ed eseguire in locale un motore Docker. In caso contrario, è possibile usare l'opzione cluster. Tuttavia, l'esecuzione di un servizio contenitore di Azure può essere costosa.

* Questa soluzione presuppone che Azure Machine Learning Workbench sia in esecuzione in Windows 10 con il motore Docker installato in locale. Per un sistema macOS, le istruzioni sono in gran parte uguali.

## <a name="data-description"></a>Descrizione dei dati

In questo esempio viene usato un piccolo set di dati creato manualmente che si trova nella [cartella data](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/tree/master/data).

La prima colonna contiene le recensioni cinematografiche, mentre la seconda include la valutazione corrispondente (0: negativa, 1: positiva). Il set di dati viene usato esclusivamente a scopo dimostrativo, ma in genere per ottenere punteggi affidabili, è necessario un set di dati di grandi dimensioni. Ad esempio, il [problema di classificazione del sentiment delle recensioni cinematografiche di IMDB](https://keras.io/datasets/#datasets ) in Keras è costituito da un set di dati di 25.000 recensioni cinematografiche di IMDB, con etichetta in base alla valutazione (positiva o negativa). Lo scopo di questa esercitazione è mostrare come eseguire l'analisi del sentiment tramite tecniche di apprendimento avanzato usando Azure Machine Learning Workbench.

## <a name="scenario-structure"></a>Struttura dello scenario

La struttura di cartelle è la seguente:

1. Tutto il codice correlato all'analisi del sentiment con Azure Machine Learning Workbench si trova nella cartella radice
2. data: contiene il set di dati usato nella soluzione
3. docs: contiene tutte le esercitazioni pratiche

L'ordine delle esercitazioni pratiche per l'esecuzione della soluzione è il seguente:

| Ordine| File Name | File correlati |
|--|-----------|------|
| 1 | [`SentimentAnalysisDataPreparation.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisDataPreparation.md) | "data/sampleReviews.txt" |
| 2 | [`SentimentAnalysisModelingKerasWithCNTKBackend.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisModelingKerasWithCNTKBackend.md) | "SentimentExtraction.py" |
| 3 | [`SentimentAnalysisModelingDocker.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisModelingDocker.md) | "SentimentExtractionDocker.py" |
| 4 | [`SentimentAnalysisOperationalization.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisOperationalization.md) | "Operaionalization" |

## <a name="conclusion"></a>Conclusioni

In conclusione, questa soluzione presenta l'uso di tecniche di apprendimento avanzato per eseguire l'analisi del sentiment con Azure Machine Learning Workbench. La soluzione presentata è flessibile e supporta l'uso di CNTK/TensorFlow come back-end con Keras. Inoltre, l'operazionalizzazione avviene usando modelli HDF5.
