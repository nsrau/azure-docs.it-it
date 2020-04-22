---
title: Confronto tra le funzionalità di Analisi di flusso di AzureAzure Stream Analytics feature comparison
description: Questo articolo confronta le funzionalità supportate per i processi cloud e IoT Edge di Azure nel portale di Azure, in Visual Studio e nel codice di Visual Studio.This article compares the features supported for Azure Stream Analytics cloud and IoT Edge jobs in the Azure portal, Visual Studio, and Visual Studio Code.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: d3b27a50fa86916b71c84b30ecdbf45deb0ec45c
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770764"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Confronto tra le funzionalità di Analisi di flusso di AzureAzure Stream Analytics feature comparison

Con Analisi di flusso di Azure è possibile creare soluzioni di streaming nel cloud e in Edge Edge utilizzando il portale di [Azure,](stream-analytics-quick-create-portal.md) [Visual Studio](stream-analytics-quick-create-vs.md)e Visual [Studio Code](quick-create-vs-code.md). Le tabelle in questo articolo mostrano le funzionalità supportate da ogni piattaforma per entrambi i tipi di processo.

> [!NOTE]
> Gli strumenti Visual Studio e Visual Studio Code non supportano i processi nelle aree NordEst di Cina Orientale, Cina settentrionale, Germania centrale e Germania.

## <a name="cloud-job-features"></a>Funzionalità dei lavori cloud


|Funzionalità  |Portale  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Multipiattaforma     |Mac</br>Linux</br>Windows         |WINDOWS        |Mac</br>Linux</br>Windows          |
|Creazione di script     |Sì         |Sì         |Sì         |
|Script Intellisense     |Evidenziazione della sintassi         |Evidenziazione della sintassi</br>Completamento del codice</br>Indicatore di errore         |Evidenziazione della sintassi</br>Completamento del codice</br>Indicatore di errore         |
|Definire tutti i tipi di input, output e configurazioni di processo     |Sì         |Sì         |Sì         |
|Controllo del codice sorgente     |No         |Sì         |Sì         |
|Supporto CI/CD     |Partial         |Sì         |Sì         |
|Condividere input e output tra più query     |No         |Sì         |Sì         |
|Test di query con un file di esempioQuery testing with a sample file     |Sì         |Sì        |Sì         |
|Test locali dei dati in tempo reale     |No         |Sì       |Sì      |
|Elencare i processi e visualizzare le entità processo     |Sì         |Sì        |Sì         |
|Esportare un processo in un progetto locale     |No         |Sì         |Sì         |
|Inviare, avviare e arrestare i processi     |Sì         |Sì         |Sì         |
|Visualizzare le metriche e il diagramma dei processi     |Sì         |Sì         |Apri nel portale         |
|Visualizzare gli errori di runtime dei processiView job runtime errors     |Sì         |Sì         |No         |
|Log di diagnostica     |Sì         |No         |No         |
|Proprietà personalizzate dei messaggi     |Sì         |Sì         |No       |
|Funzione di codice personalizzato e Deserializer di C|Modalità di sola lettura|Sì|No|
|UDF e UDA JavaScript     |Sì         |Sì         |Solo Windows         |
|Servizio Machine Learning     |Sì        |Sì         |No         |
|Machine Learning Studio     |Sì, ma la query non può essere testata        |Sì |No         |
|Livello di compatibilità     |1.0</br>1.1</br>1.2 (predefinito)         |1.0</br>1.1</br>1.2 (predefinito)           |1.0</br>1.1</br>1.2 (predefinito)           |
|Funzioni di rilevamento anomalie integrate in ML     |Sì         |Sì         |Sì         |
|Funzioni Geospaziali integrate     |Sì         |Sì         |Sì         |



## <a name="iot-edge-job-features"></a>Funzionalità del lavoro ioT Edge

|Funzionalità  |Portale  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Creazione di processi     |Sì         |Sì         |No         |
|Controllo del codice sorgente     |No         |Sì         |No         |
|Esportare un processo in un progetto locale     |No         |Sì         |No         |
|Test di query con un file di esempioQuery testing with a sample file     |Sì         |Sì         |No         |
|Condividere input e output tra più query     |No         |Sì         |No         |
|Funzione definita dall'utente in C#     |No         |Sì         |No         |
|Inviare i processi     |Sì         |Sì         |No         |
|Elencare i processi e visualizzare le entità processo     |Sì         |Sì         |No         |
|Visualizzare le metriche e il diagramma dei processi     |Sì         |Parziale         |No         |
|Visualizzare gli errori di runtime dei processiView job runtime errors     |Sì         |Parziale         |No         |
|Supporto CI/CD     |No         |No         |No         |


## <a name="next-steps"></a>Passaggi successivi

* [Analisi di flusso di Azure in IoT Edge](stream-analytics-edge.md)
* [Esercitazione: Scrivere una funzione definita dall'utente di C'è per il processo di Azure Stream Analytics IoT Edge (anteprima)Tutorial: Write a C' user-defined function for Azure Stream Analytics IoT Edge job (Preview)](stream-analytics-edge-csharp-udf.md)
* [Sviluppare processi di Analisi di flusso di IoT Edge usando gli strumenti di Visual StudioDevelop Stream Analytics IoT Edge jobs using Visual Studio tools](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Usare Visual Studio per visualizzare i processi di Analisi di flusso di Azure](stream-analytics-vs-tools.md)
* [Esplora Analisi di flusso di Azure con il codice di Visual Studio (anteprima)Explore Azure Stream Analytics with Visual Studio Code (Preview)](visual-studio-code-explore-jobs.md)


