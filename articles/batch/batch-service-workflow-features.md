---
title: Flusso di lavoro e risorse del servizio Batch
description: Informazioni sulle funzionalità del servizio Batch e sul relativo flusso di lavoro di alto livello dal punto di vista dello sviluppo.
ms.topic: conceptual
ms.date: 05/12/2020
ms.openlocfilehash: 76a0b140f3bea4b07a6de632abbcbc3fd26e582d
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2020
ms.locfileid: "85965213"
---
# <a name="batch-service-workflow-and-resources"></a>Flusso di lavoro e risorse del servizio Batch

Questa panoramica dei componenti di base del servizio Azure Batch illustra il flusso di lavoro di alto livello che gli sviluppatori di Batch possono usare per creare soluzioni di calcolo parallelo su larga scala, oltre a descrivere le risorse principali del servizio usate.

Si usano molte risorse e funzionalità illustrate in questo articolo, sia per sviluppare un'applicazione o un servizio di calcolo distribuito che rilascia chiamate [API REST](/rest/api/batchservice/) dirette che quando si usa uno degli [SDK di Batch](batch-apis-tools.md#batch-service-apis).

> [!TIP]
> Per un'introduzione più generale al servizio Batch, vedere [Panoramica delle funzionalità di Batch per sviluppatori](batch-technical-overview.md). Vedere anche gli [aggiornamenti più recenti per il servizio Batch](https://azure.microsoft.com/updates/?product=batch).

## <a name="basic-workflow"></a>Flusso di lavoro di base

Il flusso di lavoro generale seguente è proprio di quasi tutte le applicazioni e i servizi che usano il servizio Batch per l'elaborazione di carichi di lavoro paralleli:

1. Caricare i **file di dati** da elaborare in un account di [archiviazione di Azure](../storage/index.yml). Batch include il supporto predefinito per accedere all'archivio BLOB di Azure e le attività possono scaricare questi file nei [nodi di calcolo](nodes-and-pools.md#nodes) quando vengono eseguite.
2. Caricare i **file dell'applicazione** che verranno eseguiti dalle attività. Questi file possono essere binari o script con le relative dipendenze e vengono eseguiti dalle attività nei processi. Le attività possono scaricare questi file dall'account di archiviazione oppure è possibile usare la funzionalità [Pacchetti dell'applicazione](nodes-and-pools.md#application-packages) di Batch per la gestione e la distribuzione di applicazioni.
3. Creare un [pool](nodes-and-pools.md#pools) di nodi di calcolo. Quando si crea un pool, si specifica il numero di nodi di calcolo per il pool, le dimensioni e il sistema operativo. Quando ogni attività del processo viene eseguita, viene assegnata per l'esecuzione a uno dei nodi del pool.
4. Creare un [processo](jobs-and-tasks.md#jobs). Un processo gestisce una raccolta di attività. Ogni processo viene associato a un pool specifico in cui verranno eseguite le attività del processo.
5. Aggiungere [attività](jobs-and-tasks.md#tasks) al processo. Ogni attività esegue l'applicazione o lo script caricato per elaborare i file di dati scaricati dall'account di archiviazione. Ogni attività, una volta completata, può caricare l'output in Archiviazione di Azure.
6. Monitorare lo stato del processo e recuperare l'output dell'attività da Archiviazione di Azure.

> [!NOTE]
> Per usare il servizio Batch, è necessario un [account Batch](accounts.md). La maggior parte delle soluzioni Batch usa anche un account di [archiviazione di Azure](../storage/index.yml) associato per il recupero e l'archiviazione dei file.

## <a name="batch-service-resources"></a>Risorse del servizio Batch

Gli argomenti seguenti illustrano le risorse di Batch che rendono possibili scenari di calcolo distribuito.

- [Account Batch e account di archiviazione](accounts.md)
- [Nodi e pool](nodes-and-pools.md)
- [Processi e attività](jobs-and-tasks.md)
- [File e directory](files-and-directories.md)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle [API e gli strumenti di Batch](batch-apis-tools.md) disponibili per la compilazione di soluzioni Batch.
- Apprendere le nozioni di base dello sviluppo di un'applicazione abilitata per Batch con la [libreria client Batch .NET](quick-run-dotnet.md) o con [Python](quick-run-python.md). Queste guide introduttive illustrano un'applicazione di esempio che usa il servizio Batch per eseguire un carico di lavoro su più nodi di calcolo e include l'uso di Archiviazione di Azure per lo staging e il recupero dei file del carico di lavoro.
- Scaricare e installare [Batch Explorer](https://azure.github.io/BatchExplorer/) da usare durante lo sviluppo di soluzioni Batch. Usare Batch Explorer per la creazione, il debug e il monitoraggio delle applicazioni Azure Batch.
- Vedere le risorse della comunità che includono [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-batch), il [repository della community di Batch](https://github.com/Azure/Batch) e il [forum su Azure Batch](/answers/topics/azure-batch.html).
