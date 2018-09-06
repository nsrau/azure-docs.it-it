---
title: Informazioni sugli input per Analisi di flusso di Azure
description: In questo articolo viene descritta la nozione di input in un processo Analitica di flusso di Azure, confrontando l'input di streaming con l'input di dati di riferimento.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/25/2018
ms.openlocfilehash: 4a84e8f7460d3a339be783be6a12353770ced1b8
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2018
ms.locfileid: "43665824"
---
# <a name="understand-inputs-for-azure-stream-analytics"></a>Informazioni sugli input per Analisi di flusso di Azure

I processi di Analisi di flusso di Azure si connettono a uno o più input di dati. Ogni input definisce una connessione a un'origine dati esistente. Analisi di flusso di Azure accetta i dati in ingresso da diversi tipi di origini evento, inclusi hub eventi, hub IoT e archiviazione BLOB. Gli input sono referenziati per nome nella query SQL streaming scritta per ogni processo. Nella query è possibile aggiungere più input per il blending dei dati o confrontare i dati di flusso con una ricerca tra i dati di riferimento e passare i risultati agli output. 

Analisi di flusso di Azure si integra perfettamente con tre tipi di risorse di input:
- [Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/)
- [Hub IoT Azure](https://azure.microsoft.com/services/iot-hub/) 
- [Archivio BLOB di Azure](https://azure.microsoft.com/services/storage/blobs/) 

Queste risorse di input possono trovarsi nella stessa sottoscrizione di Azure del processo di Analisi di flusso o in un'altra sottoscrizione.

È anche possibile usare il [portale di Azure](stream-analytics-quick-create-portal.md#configure-input-to-the-job), [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/New-AzureRmStreamAnalyticsInput), l'[API .Net](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.inputsoperationsextensions), l'[API REST](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input) e [Visual Studio](stream-analytics-tools-for-visual-studio-install.md) per creare, modificare e testare gli input del processo di Analisi di flusso.

## <a name="stream-and-reference-inputs"></a>Input del flusso e di riferimento
Quando sono inviati tramite push a un'origine, i dati vengono utilizzati dal processo di Analisi di flusso ed elaborati in tempo reale. Gli input si dividono in due tipi: input del flusso dei dati e input dei dati di riferimento.

### <a name="data-stream-input"></a>Input del flusso dei dati
Un flusso dei dati è una sequenza non associata di eventi che accadono nel tempo. I processi di Analisi di flusso devono includere almeno un input del flusso dei dati. Gli hub eventi, l'hub IoT e l'archiviazione BLOB sono supportati come origini di input del flusso dei dati. Gli hub eventi vengono usati per raccogliere flussi di eventi da più dispositivi e servizi, ad esempio i feed attività dei social media, le informazioni sulle quotazioni azionarie o i dati di sensori. Gli hub IoT sono ottimizzati per raccogliere dati dai dispositivi connessi in scenari Internet of Things (IoT).  L'archiviazione BLOB può essere usata come origine di input per l'inserimento di dati in blocco come flusso, ad esempio i file di log.  

Per altre informazioni sul flusso dei dati di input, vedere [Trasmettere dati come input in Analisi di flusso](stream-analytics-define-inputs.md)

### <a name="reference-data-input"></a>Input dei dati di riferimento
Analisi di flusso supporta anche l'input noto come *dati di riferimento*. I dati di riferimento possono essere completamente statici o cambiare lentamente. I dati di riferimento vengono usati in genere per l'esecuzione di correlazione e ricerche. È ad esempio possibile unire i dati nell'input del flusso dei dati con quelli inclusi nei dati di riferimento, proprio come si esegue un join SQL per cercare valori statici. L'archiviazione BLOB di Azure è attualmente l'unica origine di input supportata per i dati di riferimento. I BLOB di origine dei dati di riferimento non possono avere una dimensione superiore a 100 MB.

Per altre informazioni sugli input di dati di riferimento, vedere [Uso dei dati di riferimento per le ricerche in Analisi di flusso](stream-analytics-use-reference-data.md)

Questo articolo è un passaggio nel [percorso di apprendimento di analisi di flusso](https://azure.microsoft.com/documentation/learning-paths/stream-analytics/).

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Avvio rapido: creare un processo di Analisi di flusso di Azure tramite il portale di Azure](stream-analytics-quick-create-portal.md)