---
title: Funzionalità in anteprima di Analisi di flusso di Azure
description: Questo articolo elenca le funzionalità di Analisi di flusso di Azure attualmente in anteprima.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 3e99263f6bf472c256e1747b8567249bbd62a445
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969626"
---
# <a name="azure-stream-analytics-preview-features"></a>Funzionalità in anteprima di Analisi di flusso di Azure

Questo articolo riepiloga tutte le funzionalità attualmente in anteprima per Analisi di flusso di Azure. Non è consigliabile usare le funzionalità in anteprima in un ambiente di produzione.

## <a name="public-previews"></a>Anteprime pubbliche

Nell'anteprima pubblica sono disponibili le funzionalità seguenti. È possibile sfruttare queste funzionalità adesso, ma non usarle nell'ambiente di produzione.

### <a name="online-scaling"></a>Scalabilità online

Con il ridimensionamento online, non è necessario interrompere il processo se è necessario modificare l'allocazione DELLA disfunzione su. È possibile aumentare o ridurre la capacità SU di un processo in esecuzione senza doverlo arrestare. Questo si basa sulla promessa del cliente di pipeline mission-critical a esecuzione prolungata che Analisi di flusso offre oggi. Per altre informazioni, vedere [Configurare le unità](stream-analytics-streaming-unit-consumption.md#configure-stream-analytics-streaming-units-sus)di streaming di Analisi di flusso di Azure.For more information, see Configure Azure Stream Analytics Streaming Units .

### <a name="c-custom-de-serializers"></a>Deserializzatori personalizzati di C
Gli sviluppatori possono sfruttare la potenza di Analisi di flusso di Azure per elaborare i dati in Protobuf, XML o qualsiasi formato personalizzato. È possibile implementare deserializzatori personalizzati in C, che possono quindi essere usati per deserializzare gli eventi ricevuti da Analisi di flusso di Azure.You can implement [custom de-serializers](custom-deserializer-examples.md) in C'è, which can then be used to de-serialize events received by Azure Stream Analytics.

### <a name="extensibility-with-c-custom-code"></a>Estensibilità con il codice personalizzato di C

Gli sviluppatori che creano moduli di Analisi di flusso nel cloud o in IoT Edge possono scrivere o riutilizzare funzioni personalizzate di C e richiamarli direttamente nella query tramite [funzioni definite dall'utente.](stream-analytics-edge-csharp-udf-methods.md)


### <a name="debug-query-steps-in-visual-studio"></a>Debug query steps in Visual Studio

È possibile visualizzare facilmente in anteprima il set di righe intermedio in un diagramma di dati quando si esegue il test locale negli strumenti di Analisi di flusso di Azure per Visual Studio.You can easily preview the intermediate row set on a data diagram when doing local testing in Azure Stream Analytics tools for Visual Studio. 

### <a name="local-testing-with-live-data-in-visual-studio-code"></a>Test locali con dati in tempo reale nel codice di Visual StudioLocal testing with live data in Visual Studio Code

È possibile testare le query sui dati attivi nel computer locale prima di inviare il processo ad Azure.You can test your queries against live data on your local machine before submit the job to Azure. Ogni iterazione di test richiede in media meno di due o tre secondi, determinando un processo di sviluppo molto efficiente.

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Visual Studio Code per Analisi di flusso di Azure

I processi di Analisi di flusso di Azure possono essere creati in Visual Studio Code. Vedere il nostro [codice VS per iniziare esercitazione](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code).


### <a name="integration-with-azure-machine-learning"></a>Integrazione con Azure Machine Learning

È possibile per ridimensionare i processi di Analisi di flusso con funzioni di Machine Learning (ML). Per altre informazioni su come è possibile usare le funzioni ML nel processo di Analisi di flusso, vedere [Ridimensionare il processo di Analisi di flusso con funzioni di Azure Machine Learning](stream-analytics-scale-with-machine-learning-functions.md). Per uno scenario reale, vedere [Analisi del sentiment con Analisi di flusso di Azure e Azure Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).


### <a name="live-data-testing-in-visual-studio"></a>Test dei dati live in Visual Studio

Gli strumenti di Visual Studio per Analisi di flusso di Azure migliorano la funzionalità di test locale che consente di testare le query nei flussi di eventi live da origini cloud come l'hub eventi o l'hub IoT. Per informazioni, vedere [Testare i dati live in locale usando gli strumenti di Analisi di flusso di Azure per Visual Studio](stream-analytics-live-data-local-testing.md).


### <a name="net-user-defined-functions-on-iot-edge"></a>Funzioni .NET definite dall'utente in IoT Edge

Con le funzioni .NET Standard definite dall'utente, è possibile eseguire il codice .NET Standard come parte della pipeline di streaming. È possibile creare semplici classi C# o importare librerie e progetti completi. In Visual Studio è supportata l'esperienza completa di creazione e debug. Per altre informazioni, vedere [Sviluppare funzioni .NET Standard definite dall'utente per i processi di Analisi di flusso di Azure in IoT Edge](stream-analytics-edge-csharp-udf-methods.md).

## <a name="other-previews"></a>Altre anteprime

Le seguenti funzioni sono disponibili anche in anteprima su richiesta.

### <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Punteggio ad alte prestazioni in tempo reale con modelli di Machine Learning personalizzati gestiti da Azure Machine Learning

Azure Stream Analytics supporta punteggi ad alte prestazioni e in tempo reale sfruttando modelli di Machine Learning pre-addestrati personalizzati gestiti da Azure Machine Learning e ospitati nel servizio Azure Kubernetes (AKS) o nelle istanze del contenitore di Azure (ACI), usando un flusso di lavoro che non richiede la scrittura di codice. [Registrati](https://aka.ms/asapreview1) per l'anteprima

### <a name="support-for-azure-stack"></a>Supporto per Azure StackSupport for Azure Stack
Questa funzionalità abilitata nel runtime di Azure IoT Edge sfrutta le funzionalità personalizzate di Azure Stack, ad esempio il supporto nativo per gli input e gli output locali in esecuzione in Azure Stack (ad esempio Hub eventi, Hub IoT, Archiviazione BLOB). Questa nuova integrazione consente di creare architetture ibride in grado di analizzare i dati in prossimità della posizione in cui vengono generati, riducendo la latenza e massimizzando le informazioni dettagliate.
Questa funzionalità consente di creare architetture ibride in grado di analizzare i dati vicino alla posizione in cui vengono generati, riducendo la latenza e massimizzando le informazioni dettagliate. Devi [iscriverti](https://aka.ms/asapreview1) a questa anteprima.
