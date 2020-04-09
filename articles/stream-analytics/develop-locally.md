---
title: Sviluppare ed eseguire il debug di processi di Analisi di flusso di Azure in localeDevelop and debug Azure Stream Analytics jobs locally
description: Informazioni su come sviluppare e testare i processi di Analisi di flusso di Azure nel computer locale prima di eseguirli nel portale di Azure.Learn how to develop and test Azure Stream Analytics jobs on your local computer before you run them in Azure portal.
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 03/31/2020
ms.service: stream-analytics
ms.openlocfilehash: 736fce1d4b347e36ad5c10ca89ad0627104a0232
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879843"
---
# <a name="develop-and-debug-azure-stream-analytics-jobs-locally"></a>Sviluppare ed eseguire il debug di processi di Analisi di flusso di Azure in localeDevelop and debug Azure Stream Analytics jobs locally

Anche se è possibile creare e testare i processi di Analisi di flusso di Azure nel portale di Azure, molti sviluppatori preferiscono un'esperienza di sviluppo locale. Analisi di flusso semplifica l'uso dell'editor di codice e degli strumenti di sviluppo preferiti per creare e testare processi con flussi di eventi live da Hub eventi di Azure, Hub IoT e Archiviazione BLOB usando un runtime locale a nodo singolo completamente funzionante. È anche possibile inviare processi ad Azure direttamente dall'ambiente di sviluppo locale.

## <a name="local-development-environments"></a>Ambienti di sviluppo locale

Il modo in cui sviluppi i processi di Analisi di flusso nel computer locale dipende dalle preferenze degli strumenti e dalla disponibilità delle funzionalità. Vedere [Confronto tra le funzionalità](feature-comparison.md) di Analisi di flusso di Azure per vedere quali funzionalità sono supportate per ogni ambiente di sviluppo.

Gli ambienti nella tabella seguente supportano lo sviluppo locale:

|Environment                              |Descrizione    |
|-----------------------------------------|------------|
|[Visual Studio Code](visual-studio-code-explore-jobs.md)| [L'estensione Strumenti di Analisi di flusso](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa) di Azure per Visual Studio Code consente di creare, gestire e testare il processo di analisi di flusso sia in locale che nel cloud con IntelliSense avanzato e il controllo del codice sorgente nativo. Supporta lo sviluppo su Linux, MacOS e Windows. Per altre informazioni, vedere [Creare un processo di Analisi di flusso](quick-create-vs-code.md)di Azure in Codice di Visual Studio .|
|[Visual Studio 2019](stream-analytics-tools-for-visual-studio-install.md) |Strumenti di analisi di flusso fa parte dei carichi di lavoro di sviluppo e archiviazione e elaborazione dei dati di Azure in Visual Studio.Stream Analytics Tools is part of the Azure development and Data storage and processing workloads in Visual Studio. È possibile utilizzare Visual Studio per scrivere funzioni personalizzate definite dall'utente di C, e deserializzatori. Per altre informazioni, vedere Creare un processo di Analisi di flusso di [Azure usando Visual Studio.](stream-analytics-quick-create-vs.md)|
|[Prompt dei comandi o terminal](stream-analytics-tools-for-visual-studio-cicd.md)|Il pacchetto CI/CD NuGet di Azure Stream Analytics fornisce strumenti per la compilazione del progetto di Visual Studio, test locali in un computer arbitrario. Il pacchetto CI/CD npm di Azure Stream Analytics fornisce strumenti per le compilazioni del progetto di codice di Visual Studio (che genera un modello di Azure Resource Manager) in un computer arbitrario.|

## <a name="next-steps"></a>Passaggi successivi

* [TestStream Analytics esegue query localmente con dati di esempio usando il codice di Visual StudioTest Stream Analytics queries locally with sample data using Visual Studio Code](visual-studio-code-local-run.md)
* [Testare le query di Analisi di flusso in locale sull'input del flusso live usando il codice di Visual StudioTest Stream Analytics queries locally against live stream input by using Visual Studio Code](visual-studio-code-local-run-live-input.md)
* [Eseguire test locali delle query di Analisi di flusso con Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Testare i dati live in locale usando gli strumenti di Analisi di flusso di Azure per Visual Studio](stream-analytics-live-data-local-testing.md)
