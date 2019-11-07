---
title: Confronto delle funzionalità di analisi di flusso di Azure
description: Questo articolo mette a confronto le funzionalità supportate per i processi cloud e IoT Edge di analisi di flusso di Azure in portale di Azure, Visual Studio e Visual Studio Code.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 4eb19a5b344cc5bda5ecad724daaddf9b0000d7e
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580913"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Confronto delle funzionalità di analisi di flusso di Azure

Con analisi di flusso di Azure è possibile creare soluzioni di streaming nel cloud e in IoT Edge usando [portale di Azure](stream-analytics-quick-create-portal.md), [Visual Studio](stream-analytics-quick-create-vs.md)e [Visual Studio Code](quick-create-vs-code.md). Nelle tabelle di questo articolo vengono illustrate le funzionalità supportate da ogni piattaforma per entrambi i tipi di processo.

## <a name="cloud-job-features"></a>Funzionalità del processo cloud


|Funzionalità  |di Microsoft Azure  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Multipiattaforma     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|Creazione di script     |Sì         |Sì         |Sì         |
|Script IntelliSense     |Evidenziazione della sintassi         |Evidenziazione della sintassi</br>Completamento del codice</br>Marcatore errore         |Evidenziazione della sintassi</br>Completamento del codice</br>Marcatore errore         |
|Definire input, output e configurazioni di processo     |Sì         |Sì         |Sì         |
|Partizionamento dell'output del BLOB     |Sì         |Sì         |Sì         |
|Power BI come output     |Sì         |Sì         |No         |
|Dati di riferimento del database SQL     |Sì         |Sì         |Sì         |
|Proprietà personalizzate del messaggio     |Sì         |No         |No         |
|Condividere input e output tra più query     |No         |Sì         |Sì         |
|UDF e UDA JavaScript     |Sì         |Sì         |Solo Windows         |
|Callout di Machine Learning     |Sì, ma non è possibile testare la query        |Sì, ma non può essere testato localmente         |No         |
|Livello di compatibilità     |1.0</br>1.1</br>1.2         |1.0</br>1.1</br>1.2          |1.0</br>1.1</br>1.2          |
|Funzioni di rilevamento di anomalie basate su ML predefinite     |Sì         |Sì         |Sì         |
|Funzioni geospaziali predefinite     |Sì         |Sì         |Sì         |
|Test di query con un file di esempio     |Sì         |Sì         |Sì         |
|Test locali dei dati in tempo reale     |No         |Sì         |No         |
|Elencare i processi e visualizzare le entità del processo     |Sì         |Sì         |Sì         |
|Esportare un processo in un progetto locale     |No         |Sì         |Sì         |
|Inviare, avviare e arrestare processi     |Sì         |Sì         |Sì         |
|Controllo del codice sorgente     |No         |Sì         |Sì         |
|Supporto CI/CD     |Parziale         |Sì         |Sì         |
|Visualizzare le metriche e il diagramma del processo     |Sì         |Sì         |Apri nel portale         |
|Visualizzare gli errori di runtime del processo     |Sì         |Sì         |No         |
|Log di diagnostica     |Sì         |No         |No         |


## <a name="iot-edge-job-features"></a>Funzionalità del processo di IoT Edge

|Funzionalità  |di Microsoft Azure  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Creazione di processi     |Sì         |Sì         |No         |
|Controllo del codice sorgente     |No         |Sì         |No         |
|Esportare un processo in un progetto locale     |No         |Sì         |No         |
|Test di query con un file di esempio     |Sì         |Sì         |No         |
|Condividere input e output tra più query     |No         |Sì         |No         |
|C#UDF     |No         |Sì         |No         |
|Inviare i processi     |Sì         |Sì         |No         |
|Elencare i processi e visualizzare le entità del processo     |Sì         |Sì         |No         |
|Visualizzare le metriche e il diagramma del processo     |Sì         |Parziale         |No         |
|Visualizzare gli errori di runtime del processo     |Sì         |Parziale         |No         |
|Supporto CI/CD     |No         |No         |No         |


## <a name="next-steps"></a>Passaggi successivi

* [Analisi di flusso di Azure in IoT Edge](stream-analytics-edge.md)
* [Esercitazione: scrivere una C# funzione definita dall'utente per l'analisi di flusso di Azure IOT Edge processo (anteprima)](stream-analytics-edge-csharp-udf.md)
* [Sviluppare analisi di flusso IoT Edge processi usando gli strumenti di Visual Studio](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Usare Visual Studio per visualizzare i processi di Analisi di flusso di Azure](stream-analytics-vs-tools.md)
* [Esplora analisi di flusso di Azure con Visual Studio Code (anteprima)](vscode-explore-jobs.md)


