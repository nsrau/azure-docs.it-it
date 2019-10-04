---
title: Confronto tra le funzionalità Azure Stream Analitica
description: Questo articolo mette a confronto le funzionalità supportate per il cloud di Azure Stream Analitica e i processi di IoT Edge nel portale di Azure, Visual Studio e Visual Studio Code.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 22d7ef90ee0cf4d09467516b7bb0664327b7dabe
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509781"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Confronto tra le funzionalità Azure Stream Analitica

Con Azure Stream Analitica, è possibile creare soluzioni di streaming nel cloud e in IoT Edge usando [portale di Azure](stream-analytics-quick-create-portal.md), [Visual Studio](stream-analytics-quick-create-vs.md), e [Visual Studio Code](quick-create-vs-code.md). Le tabelle in questo articolo illustrano le funzionalità supportate per ogni piattaforma per entrambi i tipi di processo.

## <a name="cloud-job-features"></a>Funzionalità di processo cloud


|Funzionalità  |Portale  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Multipiattaforma     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|Creazione di script     |Yes         |Sì         |Yes         |
|Script Intellisense     |L'evidenziazione della sintassi         |L'evidenziazione della sintassi</br>Completamento del codice</br>Indicatore di errore         |L'evidenziazione della sintassi</br>Completamento del codice</br>Indicatore di errore         |
|Definire gli input, output e le configurazioni del processo     |Yes         |Sì         |Yes         |
|Partizionamento output BLOB     |Yes         |Sì         |Yes         |
|Power BI come output     |Yes         |Sì         |No         |
|Dati di riferimento del database SQL     |Yes         |Sì         |Yes         |
|Proprietà messaggio personalizzato     |Yes         |No         |No         |
|Condividi più query di input e output     |No         |Yes         |Yes         |
|UDF di JavaScript e aggregazioni definite dall'utente     |Yes         |Yes         |Solo Windows         |
|Callout di Machine Learning     |Sì, ma la query non può essere testato        |Sì, ma non è possibile testare in locale         |No         |
|Livello di compatibilità     |1.0</br>1.1</br>1.2         |1.0</br>1.1</br>1.2          |1.0</br>1.1</br>1.2          |
|Funzioni predefinite di rilevamento delle anomalie basato su Machine Learning     |Yes         |Sì         |Yes         |
|Funzioni geospaziali predefinite     |Yes         |Sì         |Yes         |
|Test delle query con un file di esempio     |Yes         |Sì         |Yes         |
|Live test locale dei dati     |No         |Sì         |No         |
|Elencare i processi e visualizzare le entità di processo     |Yes         |Sì         |Yes         |
|Esportare un processo in un progetto locale     |No         |Yes         |Yes         |
|Inviare, avviare e arrestare i processi     |Yes         |Sì         |Yes         |
|Controllo del codice sorgente     |No         |Yes         |Yes         |
|Supporto di integrazione continua/recapito Continuo     |Parziale         |Yes         |Yes         |
|Visualizzare le metriche di processo e diagramma     |Yes         |Yes         |Apri nel portale         |
|Visualizzare gli errori di runtime di processo     |Yes         |Sì         |No         |
|Log di diagnostica     |Yes         |No         |No         |


## <a name="iot-edge-job-features"></a>Funzionalità di processi di IoT Edge

|Funzionalità  |Portale  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Creazione dei processi     |Yes         |Sì         |No         |
|Controllo del codice sorgente     |No         |Sì         |No         |
|Esportare un processo in un progetto locale     |No         |Sì         |No         |
|Test delle query con un file di esempio     |Yes         |Sì         |No         |
|Condividi più query di input e output     |No         |Sì         |No         |
|C#FUNZIONE DEFINITA DALL'UTENTE     |No         |Sì         |No         |
|Inviare, avviare e arrestare i processi     |Yes         |Sì         |No         |
|Elencare i processi e visualizzare le entità di processo     |Yes         |Sì         |No         |
|Visualizzare le metriche di processo e diagramma     |Yes         |Parziale         |No         |
|Visualizzare gli errori di runtime di processo     |Yes         |Parziale         |No         |
|Supporto di integrazione continua/recapito Continuo     |No         |No         |No         |


## <a name="next-steps"></a>Passaggi successivi

* [Analisi di flusso di Azure in IoT Edge](stream-analytics-edge.md)
* [Esercitazione: Scrivere un C# funzione definita dall'utente per il processo di Azure Stream Analitica IoT Edge (anteprima)](stream-analytics-edge-csharp-udf.md)
* [Sviluppare processi Stream Analitica IoT Edge usando strumenti di Visual Studio](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Usare Visual Studio per visualizzare i processi di Analisi di flusso di Azure](stream-analytics-vs-tools.md)
* [Esplorare Azure Stream Analitica con Visual Studio Code (anteprima)](vscode-explore-jobs.md)


