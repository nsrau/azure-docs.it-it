---
title: Scegliere una soluzione di elaborazione in tempo reale e di flusso in AzureChoose a real-time and stream processing solution on Azure
description: Informazioni su come scegliere la giusta tecnologia di analisi e elaborazione in streaming in tempo reale per creare l'applicazione in Azure.Learn about how to choose the right real-time analytics and streaming processing technology to build your application on Azure.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 2146b1bd782aba5d98729a2d37d956744e469ba1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75860249"
---
# <a name="choose-a-real-time-analytics-and-streaming-processing-technology-on-azure"></a>Scegli una tecnologia di analisi e elaborazione in streaming in tempo reale in Azure

Sono disponibili diversi servizi per l'analisi in tempo reale e l'elaborazione del flusso in Azure.There are several services available for real-time analytics and streaming processing on Azure. In questo articolo vengono fornite le informazioni necessarie per decidere quale tecnologia è la soluzione migliore per l'applicazione.

## <a name="when-to-use-azure-stream-analytics"></a>Quando usare Analisi di flusso di AzureWhen to use Azure Stream Analytics

Azure Stream Analytics is the recommended service for stream analytics on Azure. È pensato per una vasta gamma di scenari che includono, ma non sono limitati a:

* Dashboard per la visualizzazione dei dati
* [Avvisi](stream-analytics-set-up-alerts.md) in tempo reale da schemi o anomalie temporali e spaziali
* ETL (Extract Transform Load)
* [Modello di approvvigionamento di eventi](/azure/architecture/patterns/event-sourcing)
* [IoT Edge](stream-analytics-edge.md)

L'aggiunta di un processo di Analisi di flusso di Azure all'applicazione è il modo più rapido per eseguire analisi di streaming in Azure usando il linguaggio SQL già noto. Azure Stream Analytics è un servizio di processo, quindi non è necessario dedicare tempo alla gestione dei cluster e non è necessario preoccuparsi del tempo di inattività con un contratto di servizio del 99,9% a livello di processo. La fatturazione viene eseguita anche a livello di processo rendendo bassi i costi di avvio (un'unità di streaming), ma scalabile (fino a 192 unità di streaming). È molto più conveniente eseguire alcuni processi di Analisi di flusso rispetto all'esecuzione e alla gestione di un cluster.

Analisi di flusso di Azure offre un'esperienza straordinaria. È possibile sfruttare immediatamente le seguenti funzionalità senza alcuna configurazione aggiuntiva:

* Operatori temporali incorporati, ad esempio [aggregazioni con finestre,](stream-analytics-window-functions.md)join temporali e funzioni analitiche temporali.
* Adattatori [di input](stream-analytics-add-inputs.md) e [output](stream-analytics-define-outputs.md) di Azure nativi
* Supporto per [dati](stream-analytics-use-reference-data.md) di riferimento a modifica lenta (noti anche come tabelle di ricerca), inclusa l'unione con dati di riferimento geospaziali per la geofencing.
* Soluzioni integrate, come [il rilevamento delle anomalie](stream-analytics-machine-learning-anomaly-detection.md)
* Più intervalli di tempo nella stessa queryMultiple time windows in the same query
* Possibilità di comporre più operatori temporali in sequenze arbitrarie.
* Latenza end-to-end inferiore a 100 ms dall'input che arriva agli hub eventi, all'avvio dell'uscita negli hub eventi, incluso il ritardo di rete da e verso gli hub eventi, a una velocità effettiva elevata sostenuta

## <a name="when-to-use-other-technologies"></a>Quando utilizzare altre tecnologie

### <a name="you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c"></a>Si desidera scrivere funzioni definite dall'utente, UDA e deserializzatori personalizzati in un linguaggio diverso da JavaScript o C #

Azure Stream Analytics supporta funzioni definite dall'utente (UDF) o aggregazioni definite dall'utente (UDA) in JavaScript per i processi cloud e in C'è per i processi IoT Edge. Sono supportati anche i deserializzatori definiti dall'utente di C. Se si desidera implementare un deserializzatore, una funzione definita dall'utente o un UDA in altri linguaggi, ad esempio Java o Python, è possibile utilizzare Spark Structured Streaming. È anche possibile eseguire **EventProcessorHost** di Hub eventi nelle proprie macchine virtuali per eseguire un'elaborazione arbitraria del flusso.

### <a name="your-solution-is-in-a-multi-cloud-or-on-premises-environment"></a>La soluzione si trova in un ambiente multi-cloud o locale

Azure Stream Analytics is Microsoft's proprietary technology and is only available on Azure. Se hai bisogno che la tua soluzione sia portabile su cloud o in locale, prendi in considerazione le tecnologie open source come Spark Structured Streaming o Storm.

## <a name="next-steps"></a>Passaggi successivi

* [Creare un processo di Analisi di flusso tramite il portale di AzureCreate a Stream Analytics job by using the Azure portal](stream-analytics-quick-create-portal.md)
* [Creare un processo di Analisi di flusso tramite Azure PowerShellCreate a Stream Analytics job by using Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Creare un processo di Analisi di flusso tramite Visual StudioCreate a Stream Analytics job by using Visual Studio](stream-analytics-quick-create-vs.md)
* [Creare un processo di Analisi di flusso usando il codice di Visual StudioCreate a Stream Analytics job by using Visual Studio Code](quick-create-vs-code.md)
