---
title: Funzionalità in anteprima di Analisi di flusso di Azure
description: Questo articolo elenca le funzionalità di Analisi di flusso di Azure attualmente in anteprima.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.openlocfilehash: 9b721ab614bf1797604fe342de117c78ba703f96
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2018
ms.locfileid: "53557598"
---
# <a name="azure-stream-analytics-preview-features"></a>Funzionalità in anteprima di Analisi di flusso di Azure

Questo articolo riepiloga tutte le funzionalità attualmente in anteprima per Analisi di flusso di Azure. Non è consigliabile usare le funzionalità in anteprima in un ambiente di produzione.

## <a name="public-previews"></a>Anteprime pubbliche

Nell'anteprima pubblica sono disponibili le funzionalità seguenti. È possibile sfruttare queste funzionalità adesso, ma non usarle nell'ambiente di produzione.

### <a name="integration-with-azure-machine-learning"></a>Integrazione con Azure Machine Learning

È possibile per ridimensionare i processi di Analisi di flusso con funzioni di Machine Learning (ML). Per altre informazioni su come è possibile usare le funzioni ML nel processo di Analisi di flusso, vedere [Ridimensionare il processo di Analisi di flusso con funzioni di Azure Machine Learning](stream-analytics-scale-with-machine-learning-functions.md). Per uno scenario reale, vedere [Analisi del sentiment con Analisi di flusso di Azure e Azure Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).

### <a name="session-windows"></a>Finestre delle sessioni

Analisi di flusso offre supporto nativo per le funzioni delle finestre, consentendo agli sviluppatori di creare processi di elaborazione dei flussi complessi con il minimo sforzo. Le [finestre delle sessioni](https://msdn.microsoft.com/azure/stream-analytics/reference/session-window-azure-stream-analytics) raggruppano gli eventi che arrivano a intervalli di tempo simili, filtrando i periodi di tempo nei quali non sono presenti dati. Per altre informazioni sulle funzioni delle finestre, vedere [Introduzione alle funzioni delle finestre di Analisi di flusso](stream-analytics-window-functions.md).

### <a name="blob-output-partitioning-by-custom-time"></a>Partizionamento dell'output BLOB per ora personalizzata

Analisi di flusso di Azure può eseguire l'output nell'archivio BLOB in base agli attributi temporali personalizzati. Per altre informazioni, vedere [Modelli di percorso di data/ora personalizzati per l'output di archiviazione BLOB di Analisi di flusso di Azure](stream-analytics-custom-path-patterns-blob-storage-output.md).

### <a name="javascript-user-defined-aggregate"></a>Aggregazione JavaScript definita dall'utente

Analisi di flusso di Azure supporta le aggregazioni definite dall'utente scritte in JavaScript, che consentono di implementare la logica di business con stato complessa. Per informazioni su come usare le aggregazioni definite dall'utente, vedere il documento [Aggregazioni JavaScript definite dall'utente in Analisi di flusso di Azure](stream-analytics-javascript-user-defined-aggregates.md). 

### <a name="live-data-testing-in-visual-studio"></a>Test dei dati live in Visual Studio

Gli strumenti di Visual Studio per Analisi di flusso di Azure migliorano la funzionalità di test locale che consente di testare le query nei flussi di eventi live da origini cloud come l'hub eventi o l'hub IoT. Per informazioni, vedere [Testare i dati live in locale usando gli strumenti di Analisi di flusso di Azure per Visual Studio](stream-analytics-live-data-local-testing.md).

### <a name="net-user-defined-functions-on-iot-edge"></a>Funzioni .NET definite dall'utente in IoT Edge

Con le funzioni .NET Standard definite dall'utente, è possibile eseguire il codice .NET Standard come parte della pipeline di streaming. È possibile creare semplici classi C# o importare librerie e progetti completi. In Visual Studio è supportata l'esperienza completa di creazione e debug. Per altre informazioni, vedere [Sviluppare funzioni .NET Standard definite dall'utente per i processi di Analisi di flusso di Azure in IoT Edge](stream-analytics-edge-csharp-udf-methods.md).

## <a name="private-previews"></a>Anteprime private

Nell'anteprima privata sono disponibili le funzionalità seguenti. Per accedere a queste anteprime, visitare la pagina di [iscrizione](https://aka.ms/ASApreview1) all'anteprima privata di Analisi di flusso di Azure.

### <a name="anomaly-detection"></a>Anomaly Detection

In Analisi di flusso di Azure sono stati introdotti nuovi modelli di apprendimento automatico con il supporto per il rilevamento di *picchi* e *flessioni*, oltre che per il rilevamento di tendenze bidirezionali, positive lente e negative lente.

### <a name="c-custom-deserializer-for-azure-stream-analytics-on-iot-edge"></a>Deserializzatore personalizzato C# per Analisi di flusso di Azure in IoT Edge

Gli sviluppatori possono ora implementare deserializzatori personalizzati in C# per deserializzare gli eventi ricevuti da Analisi di flusso di Azure. Gli esempi di formati che possono essere deserializzati includono Parquet, Protobuf, XML o qualsiasi altro formato binario.

### <a name="blob-output-partitioning-by-custom-attribute"></a>Partizionamento dell'output BLOB per attributo personalizzato

È ora possibile partizionare l'output di Analisi di flusso di Azure nell'archivio BLOB in base a una colonna della query.

### <a name="managed-identities-for-azure-resources-authentication-to-azure-data-lake-storage"></a>Identità gestite per l'autenticazione delle risorse di Azure in Azure Data Lake Storage

È ora possibile rendere operative le pipeline in tempo reale con le identità gestite per l'autenticazione basata sulle risorse di Azure durante la scrittura in Azure Data Lake Storage Gen1 e poter così creare processi a livello di codice. Per altre informazioni, vedere [Usare le identità gestite per le risorse di Azure per l'autenticazione dei processi di Analisi di flusso di Azure per l'output in Azure Data Lake Storage Gen1](stream-analytics-managed-identities-adls.md).

## <a name="next-steps"></a>Passaggi successivi

* [Eight new features in Azure Stream Analytics (Otto nuove funzionalità in Analisi di flusso di Azure)](https://azure.microsoft.com/blog/eight-new-features-in-azure-stream-analytics/)

* [4 new features now available in Azure Stream Analytics (Quattro nuove funzionalità disponibili in Analisi di flusso di Azure)](https://azure.microsoft.com/blog/4-new-features-now-available-in-azure-stream-analytics/)
