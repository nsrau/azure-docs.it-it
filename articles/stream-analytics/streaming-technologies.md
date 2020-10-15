---
title: Scegliere una soluzione di elaborazione dei flussi in tempo reale in Azure
description: Scopri come scegliere la tecnologia di elaborazione del flusso e dell'analisi in tempo reale per compilare l'applicazione in Azure.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: a204aacc0061091e0a273581e766bb669d191f0b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90903702"
---
# <a name="choose-a-real-time-analytics-and-streaming-processing-technology-on-azure"></a>Scegliere una tecnologia di elaborazione del flusso e analisi in tempo reale in Azure

Sono disponibili diversi servizi per l'analisi in tempo reale e l'elaborazione di flussi in Azure. Questo articolo fornisce le informazioni necessarie per scegliere la tecnologia più adatta per l'applicazione.

## <a name="when-to-use-azure-stream-analytics"></a>Quando usare analisi di flusso di Azure

Analisi di flusso di Azure è il servizio consigliato per l'analisi di flusso in Azure. È concepita per un'ampia gamma di scenari che includono ma non sono limitati a:

* Dashboard per la visualizzazione dei dati
* [Avvisi](stream-analytics-set-up-alerts.md) in tempo reale da modelli temporali e spaziali o anomalie
* ETL (Extract Transform Load)
* [Modello di origine eventi](/azure/architecture/patterns/event-sourcing)
* [IoT Edge](stream-analytics-edge.md)

L'aggiunta di un processo di analisi di flusso di Azure all'applicazione rappresenta il modo più rapido per ottenere analisi di flusso in esecuzione in Azure, usando il linguaggio SQL già noto. Analisi di flusso di Azure è un servizio di lavoro, quindi non è necessario dedicare tempo alla gestione dei cluster e non è necessario preoccuparsi del tempo di inattività con un contratto di servizio del 99,9% a livello di processo. La fatturazione viene eseguita anche a livello di processo, rendendo bassi i costi di avvio (un'unità di streaming), ma scalabile (fino a 192 unità di streaming). È molto più conveniente eseguire alcuni processi di analisi di flusso anziché eseguire e gestire un cluster.

Analisi di flusso di Azure offre un'esperienza predefinita completa. È possibile sfruttare immediatamente le funzionalità seguenti senza alcuna configurazione aggiuntiva:

* Operatori temporali incorporati, ad esempio [aggregazioni con finestra](stream-analytics-window-functions.md), join temporali e funzioni analitiche temporali.
* Adattatori di [input](stream-analytics-add-inputs.md) e [output](stream-analytics-define-outputs.md) di Azure nativi
* Supporto per [i dati di riferimento](stream-analytics-use-reference-data.md) a modifica lenta (noti anche come tabelle di ricerca), incluso l'Unione con i dati di riferimento geospaziali per la geoschermatura.
* Soluzioni integrate, ad esempio il [rilevamento delle anomalie](stream-analytics-machine-learning-anomaly-detection.md)
* Più finestre temporali nella stessa query
* Possibilità di comporre più operatori temporali in sequenze arbitrarie.
* Alla latenza end-to-end di 100-ms dall'input in arrivo all'hub eventi, per l'output di destinazione in hub eventi, incluso il ritardo di rete da e verso hub eventi, a velocità effettiva elevata sostenuta

## <a name="when-to-use-other-technologies"></a>Quando utilizzare altre tecnologie

### <a name="you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c"></a>Si desidera scrivere funzioni definite dall'utente, Uda e deserializzatori personalizzati in un linguaggio diverso da JavaScript o C #

Analisi di flusso di Azure supporta funzioni definite dall'utente (UDF) o aggregazioni definite dall'utente (UDA) in JavaScript per i processi cloud e C# per IoT Edge processi. Sono supportati anche i deserializzatori definiti dall'utente C#. Se si vuole implementare un deserializzatore, una funzione definita dall'utente o un'aggregazione definita dall'utente in altri linguaggi, ad esempio Java o Python, è possibile usare lo streaming strutturato Spark. È anche possibile eseguire l'hub eventi **EventProcessorHost** nelle proprie macchine virtuali per eseguire l'elaborazione di flussi arbitrari.

### <a name="your-solution-is-in-a-multi-cloud-or-on-premises-environment"></a>La soluzione si trova in un ambiente con più cloud o in locale

Analisi di flusso di Azure è la tecnologia proprietaria di Microsoft ed è disponibile solo in Azure. Se è necessario che la soluzione sia portabile tra cloud o in locale, prendere in considerazione tecnologie open source, ad esempio Spark Structured streaming o Storm.

## <a name="next-steps"></a>Passaggi successivi

* [Creare un processo di analisi di flusso usando il portale di Azure](stream-analytics-quick-create-portal.md)
* [Creare un processo di analisi di flusso usando Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Creare un processo di analisi di flusso con Visual Studio](stream-analytics-quick-create-vs.md)
* [Creare un processo di analisi di flusso usando Visual Studio Code](quick-create-visual-studio-code.md)
