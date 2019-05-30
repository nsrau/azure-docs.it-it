---
title: Funzionalità in anteprima di Analisi di flusso di Azure
description: Questo articolo elenca le funzionalità di Analisi di flusso di Azure attualmente in anteprima.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: e63937cedf44b1642e091a4744d898a26422be10
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393681"
---
# <a name="azure-stream-analytics-preview-features"></a>Funzionalità in anteprima di Analisi di flusso di Azure

Questo articolo riepiloga tutte le funzionalità attualmente in anteprima per Analisi di flusso di Azure. Non è consigliabile usare le funzionalità in anteprima in un ambiente di produzione.

## <a name="public-previews"></a>Anteprime pubbliche

Nell'anteprima pubblica sono disponibili le funzionalità seguenti. È possibile sfruttare queste funzionalità adesso, ma non usarle nell'ambiente di produzione.

### <a name="visual-studio-code-for-azure-stream-analytics-released-may-2019"></a>Visual Studio Code per Azure Stream Analitica (rilasciata a maggio 2019)

I processi di Analisi di flusso di Azure possono essere creati in Visual Studio Code. Vedere la [esercitazione introduttiva di Visual Studio Code](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code).

### <a name="anomaly-detection"></a>Anomaly Detection

In Analisi di flusso di Azure sono stati introdotti nuovi modelli di apprendimento automatico con il supporto per il rilevamento di *picchi* e *flessioni*, oltre che per il rilevamento di tendenze bidirezionali, positive lente e negative lente. Per altre informazioni, visitare [rilevamento di anomalie in Azure Stream Analitica](stream-analytics-machine-learning-anomaly-detection.md).

### <a name="integration-with-azure-machine-learning"></a>Integrazione con Azure Machine Learning

È possibile per ridimensionare i processi di Analisi di flusso con funzioni di Machine Learning (ML). Per altre informazioni su come è possibile usare le funzioni ML nel processo di Analisi di flusso, vedere [Ridimensionare il processo di Analisi di flusso con funzioni di Azure Machine Learning](stream-analytics-scale-with-machine-learning-functions.md). Per uno scenario reale, vedere [Analisi del sentiment con Analisi di flusso di Azure e Azure Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).

### <a name="javascript-user-defined-aggregate"></a>Aggregazione JavaScript definita dall'utente

Analisi di flusso di Azure supporta le aggregazioni definite dall'utente scritte in JavaScript, che consentono di implementare la logica di business con stato complessa. Per informazioni su come usare le aggregazioni definite dall'utente, vedere il documento [Aggregazioni JavaScript definite dall'utente in Analisi di flusso di Azure](stream-analytics-javascript-user-defined-aggregates.md). 

### <a name="live-data-testing-in-visual-studio"></a>Test dei dati live in Visual Studio

Gli strumenti di Visual Studio per Analisi di flusso di Azure migliorano la funzionalità di test locale che consente di testare le query nei flussi di eventi live da origini cloud come l'hub eventi o l'hub IoT. Per informazioni, vedere [Testare i dati live in locale usando gli strumenti di Analisi di flusso di Azure per Visual Studio](stream-analytics-live-data-local-testing.md).

### <a name="net-user-defined-functions-on-iot-edge"></a>Funzioni .NET definite dall'utente in IoT Edge

Con le funzioni .NET Standard definite dall'utente, è possibile eseguire il codice .NET Standard come parte della pipeline di streaming. È possibile creare semplici classi C# o importare librerie e progetti completi. In Visual Studio è supportata l'esperienza completa di creazione e debug. Per altre informazioni, vedere [Sviluppare funzioni .NET Standard definite dall'utente per i processi di Analisi di flusso di Azure in IoT Edge](stream-analytics-edge-csharp-udf-methods.md).

## <a name="other-previews"></a>Altre anteprime

Le funzionalità seguenti sono anche disponibili in anteprima.

### <a name="c-custom-deserializer-for-azure-stream-analytics-on-iot-edge-and-cloud-announced-may-2019"></a>C#deserializzatore personalizzato per Analitica Stream di Azure in IoT Edge e il Cloud (Announced maggio 2019)

Gli sviluppatori possono implementare deserializers personalizzato in C# per deserializzare gli eventi ricevuti da Azure Stream Analitica. Gli esempi di formati che possono essere deserializzati includono Parquet, Protobuf, XML o qualsiasi altro formato binario. Iscriversi a questa versione di anteprima [qui](https://aka.ms/asapreview1).

### <a name="parquet-output-announced-may-2019"></a>Output parquet (annunciato maggio 2019)
Parquet è un formato orc abilitazione elaborazione efficiente di big data. Restituendo i dati in formato Parquet in un archivio data lake, è possibile sfruttare Azure Stream Analitica per su larga scala power streaming ETL ed eseguire l'elaborazione batch, eseguire il training di algoritmi di machine learning o eseguire query interattive su dati cronologici. Iscriversi a questa versione di anteprima [qui](https://aka.ms/asapreview1).

### <a name="one-click-integration-with-event-hubs-announced-may-2019"></a>Integrazione con hub eventi (Announced maggio 2019) con un clic 
Con questa integrazione, a questo punto sarà possibile visualizzare i dati in ingresso e iniziare a scrivere una query di Stream Analitica con un solo clic dal portale di Hub eventi. Quando la query è pronta, sarà possibile productize, con pochi e iniziare a ottenere informazioni dettagliate in tempo reale. Ciò riduce significativamente i tempi e costi per lo sviluppo di soluzioni di analitica in tempo reale. Iscriversi a questa versione di anteprima [qui](https://aka.ms/asapreview1).

### <a name="support-for-azure-stack-announced-may-2019"></a>Supporto per Azure Stack (annunciato maggio 2019)
Questa funzionalità è abilitata nel runtime di Azure IoT Edge, sfrutta funzionalità personalizzate di Azure Stack, ad esempio il supporto nativo per gli input locali e gli output in esecuzione in Azure Stack (ad esempio hub eventi, IoT Hub, archiviazione Blob). Questa nuova integrazione consente di compilare architetture ibride in grado di analizzare i tuoi dati vicino in cui viene generato, riducendo la latenza e massimizzando insights.
Iscriversi a questa versione di anteprima [qui](https://aka.ms/asapreview1).

