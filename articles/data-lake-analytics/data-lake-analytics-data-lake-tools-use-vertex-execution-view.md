---
title: Usare la visualizzazione esecuzioni vertici in Azure Data Lake Tools per Visual Studio | Microsoft Docs
description: Informazioni su come usare la visualizzazione esecuzioni vertici per esaminare i processi di Data Lake Analytics.
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 5366d852-e7d6-44cf-a88c-e9f52f15f7df
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/13/2016
ms.author: jgao
ms.openlocfilehash: b788e7bc8ded86ebd49cc0be73e5b4e1bcbeaba3
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="use-the-vertex-execution-view-in-data-lake-tools-for-visual-studio"></a>Usare la visualizzazione esecuzioni vertici in Azure Data Lake Tools per Visual Studio
Informazioni su come usare la visualizzazione esecuzioni vertici per esaminare i processi di Data Lake Analytics.

## <a name="prerequisites"></a>Prerequisiti

È necessario conoscere i concetti di base dell'uso di Strumenti Data Lake per Visual Studio per sviluppare script U-SQL.  Vedere [Esercitazione: Sviluppare script U-SQL tramite Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).

## <a name="open-the-vertex-execution-view"></a>Aprire la visualizzazione esecuzioni vertici
Aprire un processo U-SQL in Strumenti Data Lake per Visual Studio. Fare clic su **Vista esecuzione vertici** nell'angolo in basso a sinistra. È possibile che venga chiesto di caricare prima i profili e l'operazione può richiedere alcuni minuti in base alla connettività di rete.

![Visualizzazione esecuzioni vertici di Data Lake Analytics Tools](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-open-vertex-execution-view.png)

## <a name="understand-vertex-execution-view"></a>Informazioni sulla visualizzazione esecuzioni vertici
La visualizzazione esecuzioni vertici comprende tre parti:

![Visualizzazione esecuzioni vertici di Data Lake Analytics Tools](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view.png)

**Selettore vertice** a sinistra consente di selezionare i vertici in base alle funzionalità, ad esempio i primi 10 dati letti, o di scegliere in base alla fase. Uno dei filtri di uso più comune è quello per visualizzare i **vertici sul percorso critico**. **Percorso critico** è la catena di vertici più lunga di un processo U-SQL. Conoscere il percorso critico è utile per ottimizzare i processi controllando il vertice che richiede maggiore tempo.
  
![Visualizzazione esecuzioni vertici di Data Lake Analytics Tools](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane2.png)

Il riquadro in alto al centro mostra lo **stato di esecuzione di tutti i vertici**.
  
![Visualizzazione esecuzioni vertici di Data Lake Analytics Tools](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane3.png)

Il riquadro in basso al centro mostra le informazioni su ogni vertice:
* Process Name (Nome processo): il nome dell'istanza del vertice, costituito da parti differenti in NomeFase | NomeVertice | IstanzaEsecuzioneVertice. Ad esempio, il vertice SV7_Split[62].v1 indica la seconda istanza in esecuzione (.v1, l'indice inizia da 0) del vertice numero 62 nella fase SV7_Split.
* Totale dati letti/scritti: i dati letti/scritti da questo vertice.
* State/Exit Status (Stato/Stato uscita): lo stato finale quando il vertice viene terminato.
* Exit Code/Failure Type (Codice uscita/Tipo di errore): l'errore in caso di non riuscita del vertice.
* Creation Reason (Motivo creazione): il motivo per cui il vertice è stato creato.
* Resource Latency/Process Latency/PN Queue Latency: (Latenza risorsa/Latenza processo/Latenza coda elaborazione dati): il tempo in cui il vertice rimane di attesa delle risorse, per elaborare i dati e che rimane in coda.
* Process/Creator GUID (GUID processo/creatore): il GUID per il vertice in esecuzione corrente o per il relativo creatore.
* Version (Versione): il numero di istanza del vertice in esecuzione; il sistema potrebbe pianificare nuove istanze di un vertice per diversi motivi, ad esempio in caso di failover, ridondanza di calcolo e così via.
* Version Created Time (Data e ora creazione versione).
* Process Create Start Time/Process Queued Time/Process Start Time/Process Complete Time (Ora inizio creazione processo/Ora inserimento in coda processo/Ora inizio processo/Ora completamento processo): il momento in cui è iniziato il processo di creazione del vertice o di inserimento in coda del vertice, è iniziato o è stato completato il processo del vertice specifico.

## <a name="next-steps"></a>Passaggi successivi
* Per registrare informazioni di diagnostica, vedere [Accessing diagnostics logs for Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md)
* Per visualizzare una query più complessa, vedere [Analizzare i log del sito Web mediante Analisi Data Lake di Azure](data-lake-analytics-analyze-weblogs.md).
* Per visualizzare i dettagli del processo, vedere [Usare Job Browser e Job View (Visualizzazione processo) per i processi di Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md)
