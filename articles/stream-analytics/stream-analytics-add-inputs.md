---
title: Informazioni sugli input per Analisi di flusso di Azure
description: In questo articolo viene descritta la nozione di input in un processo Analitica di flusso di Azure, confrontando l'input di streaming con l'input di dati di riferimento.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 87e260c97a748807929a0e7021e3efb2ae8f8e7b
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329297"
---
# <a name="understand-inputs-for-azure-stream-analytics"></a>Informazioni sugli input per Analisi di flusso di Azure

I processi di Analisi di flusso di Azure si connettono a uno o più input di dati. Ogni input definisce una connessione a un'origine dati esistente. Analisi di flusso di Azure accetta i dati in ingresso da diversi tipi di origini evento, inclusi hub eventi, hub IoT e archiviazione BLOB. Gli input sono referenziati per nome nella query SQL streaming scritta per ogni processo. Nella query è possibile aggiungere più input per il blending dei dati o confrontare i dati di flusso con una ricerca tra i dati di riferimento e passare i risultati agli output. 

Stream Analitica si integra perfettamente con tre tipi di risorse come input:
- [Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/)
- [Hub IoT Azure](https://azure.microsoft.com/services/iot-hub/) 
- [Archivio BLOB di Azure](https://azure.microsoft.com/services/storage/blobs/) 

Queste risorse di input possono trovarsi nella stessa sottoscrizione di Azure del processo di Stream Analitica o da un'altra sottoscrizione.

È possibile usare la [portale di Azure](stream-analytics-quick-create-portal.md#configure-job-input), [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.streamanalytics/New-azStreamAnalyticsInput), [API .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.inputsoperationsextensions), [API REST](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input), e [Visual Studio](stream-analytics-tools-for-visual-studio-install.md)per creare, modificare e testare gli input del processo Stream Analitica.

## <a name="stream-and-reference-inputs"></a>Input del flusso e di riferimento
Quando sono inviati tramite push a un'origine, i dati vengono utilizzati dal processo di Analisi di flusso ed elaborati in tempo reale. Gli input si dividono in due tipi: input del flusso dei dati e input dei dati di riferimento.

### <a name="data-stream-input"></a>Input del flusso dei dati
Un flusso dei dati è una sequenza non associata di eventi che accadono nel tempo. I processi di Analisi di flusso devono includere almeno un input del flusso dei dati. Gli hub eventi, l'hub IoT e l'archiviazione BLOB sono supportati come origini di input del flusso dei dati. Gli hub eventi vengono usati per raccogliere flussi di eventi da più dispositivi e servizi, ad esempio i feed attività dei social media, le informazioni sulle quotazioni azionarie o i dati di sensori. Gli hub IoT sono ottimizzati per raccogliere dati dai dispositivi connessi in scenari Internet of Things (IoT).  L'archiviazione BLOB può essere usata come origine di input per l'inserimento di dati in blocco come flusso, ad esempio i file di log.  

Per altre informazioni sul flusso dei dati di input, vedere [Trasmettere dati come input in Analisi di flusso](stream-analytics-define-inputs.md)

### <a name="reference-data-input"></a>Input dei dati di riferimento
Analisi di flusso supporta anche l'input noto come *dati di riferimento*. I dati di riferimento possono essere completamente statici o cambiare lentamente. I dati di riferimento vengono usati in genere per l'esecuzione di correlazione e ricerche. È ad esempio possibile unire i dati nell'input del flusso dei dati con quelli inclusi nei dati di riferimento, proprio come si esegue un join SQL per cercare valori statici. Archiviazione Blob di Azure e Database SQL di Azure attualmente sono supportati come origini di input dei dati di riferimento. BLOB di origine dei dati di riferimento hanno un limite di 300 MB di dimensioni, a seconda della complessità della query e allocato alle unità di Streaming (vedere la [limitazione delle dimensioni](stream-analytics-use-reference-data.md#size-limitation) sezione della documentazione di dati di riferimento per altri dettagli).

Per altre informazioni sugli input di dati di riferimento, vedere [Uso dei dati di riferimento per le ricerche in Analisi di flusso](stream-analytics-use-reference-data.md)

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Avvio rapido: Creare un processo di Analisi di flusso tramite il portale di Azure](stream-analytics-quick-create-portal.md)
