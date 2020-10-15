---
title: Funzionalità in anteprima di Analisi di flusso di Azure
description: Questo articolo elenca le funzionalità di Analisi di flusso di Azure attualmente in anteprima.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 8/07/2020
ms.openlocfilehash: 0020efea69fc684ff6ebebf286718aa2a720910e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90900800"
---
# <a name="azure-stream-analytics-preview-features"></a>Funzionalità in anteprima di Analisi di flusso di Azure

Questo articolo riepiloga tutte le funzionalità attualmente in anteprima per Analisi di flusso di Azure. Non è consigliabile usare le funzionalità in anteprima in un ambiente di produzione.

## <a name="public-previews"></a>Anteprime pubbliche

Nell'anteprima pubblica sono disponibili le funzionalità seguenti. È possibile sfruttare queste funzionalità adesso, ma non usarle nell'ambiente di produzione.

### <a name="authenticate-to-sql-database-output-with-managed-identities"></a>Eseguire l'autenticazione all'output del database SQL con identità gestite

Analisi di flusso di Azure supporta l'[autenticazione con identità gestita](../active-directory/managed-identities-azure-resources/overview.md) per i sink di output del database SQL di Azure. Le identità gestite eliminano le limitazioni dei metodi di autenticazione basati su utente, ad esempio la necessità di ripetere l'autenticazione a causa di modifiche della password. 

### <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Assegnazione di punteggio in tempo reale ad alte prestazioni con modelli di ML personalizzati gestiti da Azure Machine Learning

Analisi di flusso di Azure supporta l'assegnazione di punteggio in tempo reale ad alte prestazioni usando i modelli personalizzati con training preliminare di Machine Learning gestiti da Azure Machine Learning e ospitati nel servizio Azure Kubernetes o in istanze di Azure Container tramite un flusso di lavoro che non richiede la scrittura di codice. [Iscrizione](https://aka.ms/asapreview1) all'anteprima

### <a name="c-custom-de-serializers"></a>Deserializzatori C# personalizzati
Gli sviluppatori possono sfruttare la potenza di Analisi di flusso di Azure per elaborare i dati in Protobuf, XML o in qualsiasi formato personalizzato. È possibile implementare [deserializzatori personalizzati](custom-deserializer-examples.md) in C#, che possono essere successivamente usati per deserializzare gli eventi ricevuti da Analisi di flusso di Azure.

### <a name="extensibility-with-c-custom-code"></a>Estensibilità tramite codice C# personalizzato

Gli sviluppatori che creano moduli di Analisi di flusso di Azure nel cloud o in IoT Edge possono scrivere o riusare funzioni C# personalizzate e richiamarle direttamente nella query tramite [funzioni definite dall'utente](stream-analytics-edge-csharp-udf-methods.md).

### <a name="debug-query-steps-in-visual-studio"></a>Eseguire il debug dei passaggi della query in Visual Studio

È possibile visualizzare facilmente l'anteprima del set di righe in un diagramma di dati quando si esegue il test locale in strumenti di Analisi di flusso di Azure per Visual Studio. 


### <a name="live-data-testing-in-visual-studio"></a>Test dei dati live in Visual Studio

Gli strumenti di Visual Studio per Analisi di flusso di Azure migliorano la funzionalità di test locale che consente di testare le query nei flussi di eventi live da origini cloud come l'hub eventi o l'hub IoT. Per informazioni, vedere [Testare i dati live in locale usando gli strumenti di Analisi di flusso di Azure per Visual Studio](stream-analytics-live-data-local-testing.md).

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Visual Studio Code per Analisi di flusso di Azure

I processi di Analisi di flusso di Azure possono essere creati in Visual Studio Code. Vedere l'[esercitazione introduttiva su VS Code](https://docs.microsoft.com/azure/stream-analytics/quick-create-visual-studio-code).

### <a name="local-testing-with-live-data-in-visual-studio-code"></a>Test locale con dati dinamici in Visual Studio Code

Prima di inviare il processo ad Azure, è possibile testare le query su dati dinamici nel computer locale. Ogni iterazione di test richiede in media meno di due o tre secondi, generando un processo di sviluppo molto efficiente.

## <a name="other-previews"></a>Altre anteprime

Su richiesta sono disponibili anche le anteprime delle funzionalità seguenti.

### <a name="support-for-azure-stack"></a>Supporto per Azure Stack
Questa caratteristica abilitata nel runtime di Azure IoT Edge sfrutta le funzionalità personalizzate di Azure Stack, tra cui il supporto nativo per gli input e gli output locali eseguiti in Azure Stack (ad esempio, Hub eventi, hub IoT, Archiviazione BLOB). Questa nuova integrazione consente di creare architetture ibride in grado di analizzare i dati vicino alla posizione in cui vengono generati, riducendo la latenza e ottimizzando le informazioni.
Questa funzionalità consente di creare architetture ibride in grado di analizzare i dati vicino alla posizione in cui vengono generati, riducendo la latenza e ottimizzando le informazioni. È necessario [iscriversi](https://aka.ms/asapreview1) per l'anteprima.
