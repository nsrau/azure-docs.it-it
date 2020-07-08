---
title: Gestire l'account
description: Informazioni su ciò che comprende un account Azure Batch
ms.topic: how-to
ms.date: 03/30/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6ef87fab099c6ff7402b351664ee15a198d69a4b
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83722967"
---
# <a name="manage-your-batch-account"></a>Gestire l'account Batch

Un account Batch è un'entità identificata in modo univoco all'interno del servizio Batch. Tutte le operazioni di elaborazione sono associata a un account Batch.

È possibile creare un account Batch di Azure usando il [portale di Azure](batch-account-create-portal.md) o a livello di codice, ad esempio con la [libreria di gestione .NET per Batch](batch-management-dotnet.md). Quando si crea l'account, è possibile associare un account di archiviazione di Azure per l'archiviazione di applicazioni e dati di input e output correlati al processo.

È possibile eseguire più carichi di lavoro Batch in un solo account Batch o distribuire i carichi di lavoro tra gli account Batch nella stessa sottoscrizione, ma in aree di Azure diverse.

## <a name="components-of-the-batch-account"></a>Componenti dell'account Batch

L'account Batch consente di eseguire in modo efficiente processi batch paralleli e HPC (High Performance Computing) su larga scala in Azure. All'interno dell'account è possibile gestire:

- Applicazioni in esecuzione

- Allocazione di pool e nodi all'interno di pool

- Numero e tipi di attività 

- Input e output dei dati. Non è necessario installare software aggiuntivo per gestire le attività.

- Quando si crea l'account Batch, viene richiesto di assegnarvi un nome. Questo nome è l'ID e, dopo che viene assegnato, non può essere modificato.

- Per modificare il nome di un account, è necessario eliminarlo e creare un nuovo account Batch.

- L'account viene creato nella sottoscrizione che si vuole usare.

- Usare l'account per identificare e recuperare le chiavi primarie e secondarie da qualsiasi account Batch all'interno della sottoscrizione.

- Nell'account vengono conservate le informazioni relative all'allocazione del pool e alle quote principali.  

- L'account contiene informazioni sulla posizione.

- L'account identifica l'account di archiviazione.

## <a name="next-steps"></a>Passaggi successivi

- Creare un account Batch usando il [portale di Azure](batch-account-create-portal.md).
- Creare un account Batch a livello di codice, ad esempio con la [libreria di gestione .NET per Batch](batch-management-dotnet.md).
- [Configurare o disabilitare l'accesso remoto ai nodi di calcolo in un pool di Azure Batch](pool-endpoint-configuration.md).
- [Eseguire attività di preparazione e rilascio del processo in nodi di calcolo di Batch](batch-job-prep-release.md)

