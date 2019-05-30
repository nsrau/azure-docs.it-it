---
title: Scegliere un analitica in tempo reale e streaming la tecnologia di elaborazione in Azure
description: Informazioni su come scegliere l'analitica a destra in tempo reale e streaming la tecnologia di elaborazione per compilare l'applicazione in Azure.
author: zhongc
ms.author: zhongc
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 85d6ed80da93f90e6dc0feaee7081ee3f36f1bf9
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242697"
---
# <a name="choose-a-real-time-analytics-and-streaming-processing-technology-on-azure"></a>Scegliere un analitica in tempo reale e streaming la tecnologia di elaborazione in Azure

Sono disponibili numerosi servizi per l'elaborazione di streaming in Azure e in tempo reale analitica. Questo articolo fornisce le informazioni necessarie per decidere quale tecnologia è la soluzione ottimale per l'applicazione.

## <a name="when-to-use-azure-stream-analytics"></a>Quando usare Azure Stream Analitica

Azure Stream Analitica è il servizio consigliato per analitica di flusso in Azure. Lo scopo è quello per un'ampia gamma di scenari che includono ma non sono limitati a:

* Dashboard per la visualizzazione dei dati
* In tempo reale [avvisi](stream-analytics-set-up-alerts.md) da modelli temporali e quelli spaziali o le anomalie
* ETL (Extract Transform Load)
* [Modello di origine eventi](/azure/architecture/patterns/event-sourcing)
* [IoT Edge](stream-analytics-edge.md)

Aggiunta di un Analitica Stream Azure processo per l'applicazione è il modo più rapido per iniziare lo streaming analitica di e in esecuzione in Azure usando il linguaggio SQL conosci già. Azure Analitica Stream è un servizio di processo, in modo che non è necessario impiegare la gestione dei cluster di tempo e non è necessario preoccuparsi dei tempi di inattività con un contratto di servizio del 99,9% a livello di processo. Anche la fatturazione viene effettuata a livello di processo rendendo ridotto i costi di avvio (un'unità di Streaming), ma scalabile (fino a 192 unità di Streaming). È molto più conveniente eseguire alcuni processi di Analitica Stream piuttosto che eseguire e gestire un cluster.

Azure Stream Analitica ha un'esperienza avanzata di out-of-the-box. È immediatamente possibile sfruttare le funzionalità seguenti senza alcuna configurazione aggiuntiva:

* Gli operatori temporali incorporati, ad esempio [funzioni di aggregazione](stream-analytics-window-functions.md), join temporali e funzioni di analisi temporale.
* Azure native [input](stream-analytics-add-inputs.md) e [output](stream-analytics-define-outputs.md) schede
* Supporto per a modifica lenta [dati di riferimento](stream-analytics-use-reference-data.md) (noto anche come un tabelle di ricerca), compresa l'unione dei dati di riferimento geospaziali per geofencing.
* Soluzioni, integrate, ad esempio [rilevamento anomalie](stream-analytics-machine-learning-anomaly-detection.md)
* Più finestre temporali nella stessa query
* Capacità di comporre più operatori temporali in sequenze arbitrarie.
* In 100 ms latenza end-to-end da un input che pervengono a hub eventi per l'output di destinazione in hub eventi, tra cui il ritardo di rete da e in hub eventi, a velocità effettiva elevata sostenuta

## <a name="when-to-use-other-technologies"></a>Quando utilizzare altre tecnologie

### <a name="you-need-to-input-from-or-output-to-kafka"></a>È necessario come input o output per Kafka

Azure Stream Analitica non avere un input di Apache Kafka o adattatore di output. Se si dispone di eventi di destinazione o è necessario per l'invio a Kafka e non si dispone di un requisito per eseguire il proprio cluster Kafka, è possibile continuare a usare Stream Analitica per l'invio di eventi a hub eventi usando l'API Kafka di hub eventi senza modificare il mittente dell'evento. Se è necessario eseguire il proprio cluster Kafka, è possibile usare lo Streaming strutturato Spark, che è completamente supportato nei [Azure Databricks](../azure-databricks/index.yml), o Storm su [Azure HDInsight](../hdinsight/storm/apache-storm-tutorial-get-started-linux.md).

### <a name="you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c"></a>Si vuole scrivere funzioni definite dall'utente, aggregazioni definite dall'utente e deserializers personalizzato in un linguaggio diverso da JavaScript oC#

Azure Stream Analitica supporta funzioni definite dall'utente (UDF) o funzioni di aggregazione definita dall'utente (UDA) in JavaScript per i processi cloud e C# per i processi di IoT Edge. C#sono supportati anche deserializers definite dall'utente. Se si desidera implementare un deserializzatore, una funzione definita dall'utente o un'aggregazione definita dall'utente in altri linguaggi, ad esempio Java o Python, è possibile usare lo Streaming strutturato Spark. È anche possibile eseguire gli hub eventi **EventProcessorHost** nelle macchine virtuali per effettuare arbitraria elaborazione di streaming.

### <a name="your-solution-is-in-a-multi-cloud-or-on-premises-environment"></a>La soluzione è in un ambiente multi-cloud o in locale

Azure Stream Analitica è una tecnologia proprietaria di Microsoft ed è disponibile solo in Azure. Se è necessaria la soluzione per garantire la portabilità tra cloud o in locale, prendere in considerazione le tecnologie open source, ad esempio lo Streaming strutturato Spark o Storm.

## <a name="next-steps"></a>Passaggi successivi

* [Creare un processo di Stream Analitica usando il portale di Azure](stream-analytics-quick-create-portal.md)
* [Creare un processo di Stream Analitica usando Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Creare un processo di Stream Analitica con Visual Studio](stream-analytics-quick-create-vs.md)
* [Creare un processo di Stream Analitica usando Visual Studio Code](quick-create-vs-code.md)