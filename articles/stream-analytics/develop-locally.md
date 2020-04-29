---
title: Sviluppare ed eseguire il debug di processi di analisi di flusso di Azure localmente
description: Informazioni su come sviluppare e testare i processi di analisi di flusso di Azure nel computer locale prima di eseguirli in portale di Azure.
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 03/31/2020
ms.service: stream-analytics
ms.openlocfilehash: 736fce1d4b347e36ad5c10ca89ad0627104a0232
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80879843"
---
# <a name="develop-and-debug-azure-stream-analytics-jobs-locally"></a>Sviluppare ed eseguire il debug di processi di analisi di flusso di Azure localmente

Sebbene sia possibile creare e testare i processi di analisi di flusso di Azure nell'portale di Azure, molti sviluppatori preferiscono un'esperienza di sviluppo locale. Analisi di flusso semplifica l'uso dell'editor di codice e degli strumenti di sviluppo preferiti per creare e testare processi con flussi di eventi live dall'hub eventi di Azure, dall'hub Internet e dall'archiviazione BLOB usando un runtime locale a nodo singolo completamente funzionante. È anche possibile inviare processi ad Azure direttamente dall'ambiente di sviluppo locale.

## <a name="local-development-environments"></a>Ambienti di sviluppo locale

Il modo in cui si sviluppano i processi di analisi di flusso nel computer locale dipende dalle preferenze degli strumenti e dalla disponibilità delle funzionalità. Vedere [confronto delle funzionalità di analisi di flusso di Azure](feature-comparison.md) per vedere quali funzionalità sono supportate per ogni ambiente di sviluppo.

Gli ambienti nella tabella seguente supportano lo sviluppo locale:

|Ambiente                              |Descrizione    |
|-----------------------------------------|------------|
|[Visual Studio Code](visual-studio-code-explore-jobs.md)| L' [estensione degli strumenti di analisi di flusso di Azure](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa) per Visual Studio Code consente di creare, gestire e testare il processo di analisi di flusso in locale e nel cloud con IntelliSense avanzato e il controllo del codice sorgente nativo. Supporta lo sviluppo in Linux, MacOS e Windows. Per altre informazioni, vedere [creare un processo di analisi di flusso di Azure in Visual Studio Code](quick-create-vs-code.md).|
|[Visual Studio 2019](stream-analytics-tools-for-visual-studio-install.md) |Gli strumenti di analisi di flusso fanno parte dei carichi di lavoro di sviluppo e archiviazione dei dati e di elaborazione di Azure in Visual Studio. È possibile usare Visual Studio per scrivere funzioni e deserializzatori C# personalizzati definiti dall'utente. Per altre informazioni, vedere [creare un processo di analisi di flusso di Azure con Visual Studio](stream-analytics-quick-create-vs.md).|
|[Prompt dei comandi o terminal](stream-analytics-tools-for-visual-studio-cicd.md)|Il pacchetto NuGet CI/CD di analisi di flusso di Azure offre strumenti per la compilazione di progetti Visual Studio, test locali in un computer arbitrario. Il pacchetto NPM di analisi di flusso CI/CD di Azure fornisce strumenti per Visual Studio Code build di progetto (che genera un modello di Azure Resource Manager) in un computer arbitrario.|

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire test locali delle query di Analisi di flusso con dati di esempio con Visual Studio Code](visual-studio-code-local-run.md)
* [Testare le query di analisi di flusso in locale rispetto all'input del flusso Live usando Visual Studio Code](visual-studio-code-local-run-live-input.md)
* [Eseguire test locali delle query di Analisi di flusso con Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Testare i dati live in locale usando gli strumenti di Analisi di flusso di Azure per Visual Studio](stream-analytics-live-data-local-testing.md)
