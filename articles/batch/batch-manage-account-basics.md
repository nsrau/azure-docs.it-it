---
title: Gestire l'account - Batch di Azure Documenti Microsoft
description: Informazioni su cosa comprende un account Azure BatchLearn what comprises an Azure Batch account
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a4df2ec0439e659fd8dc1448c6209c9718114791
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479791"
---
# <a name="manage-your-batch-account"></a>Gestire l'account Batch

Un account Batch è un'entità identificata in modo univoco all'interno del servizio Batch. Tutte le operazioni di elaborazione sono associata a un account Batch.

È possibile creare un account Batch di Azure usando il [portale di Azure](batch-account-create-portal.md) o a livello di codice, ad esempio con la [libreria di gestione .NET per Batch](batch-management-dotnet.md). Quando si crea l'account, è possibile associare un account di archiviazione di Azure per l'archiviazione di applicazioni e dati di input e output correlati al processo.

È possibile eseguire più carichi di lavoro Batch in un solo account Batch o distribuire i carichi di lavoro tra gli account Batch nella stessa sottoscrizione, ma in aree di Azure diverse.

## <a name="components-of-the-batch-account"></a>Componenti dell'account Batch

L'account Batch consente di eseguire processi batch di calcolo paralleli e ad alte prestazioni (HPC) su larga scala in modo efficiente in Azure.The Batch account enables you to run large-scale parallel and high-performance computing (HPC) batch jobs efficiently in Azure. All'interno dell'account che gestisci:

- applicazioni in esecuzione

- l'allocazione di pool e nodi all'interno di pool

- il numero e i tipi di attività 

- l'input e l'output dei dati. Non è necessario installare software aggiuntivo per gestire le attività.

- Quando si crea l'account Batch, viene chiesto di assegnargli un nome. Questo nome è il suo ID e una volta assegnato non può essere modificato.

- Per modificare il nome di un account, è necessario eliminarlo e creare un nuovo account Batch.

- L'account viene creato all'interno della sottoscrizione che si desidera utilizzare.

- Utilizzare l'account per identificare e recuperare le chiavi dell'account primario e secondario da qualsiasi account Batch all'interno della sottoscrizione.

- L'account gestisce le informazioni sull'allocazione del pool e sulle quote principali.  

- L'account contiene informazioni sulla posizione.

- L'account identifica l'account di archiviazione.

## <a name="next-steps"></a>Passaggi successivi

- Creare un account Batch usando il portale di Azure.Create a Batch account using the [Azure portal](batch-account-create-portal.md).
- Creare un account Batch a livello di programmazione, ad esempio con la [libreria .NET di gestione Batch](batch-management-dotnet.md).
- [Configurare o disabilitare l'accesso remoto per calcolare i nodi in un pool Batch](pool-endpoint-configuration.md)di Azure.
- [Eseguire attività di preparazione e rilascio del processo in nodi di calcolo di Batch](batch-job-prep-release.md)

