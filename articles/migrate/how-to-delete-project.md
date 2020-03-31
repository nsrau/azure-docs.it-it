---
title: Eliminare un progetto di Azure Migrate
description: Descrive come creare un progetto di Azure Migrate e aggiungere uno strumento di valutazione/migrazione.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/22/2019
ms.author: raynew
ms.openlocfilehash: 55842d36cddb2a7851ff5bd7002c20e9873158f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73512730"
---
# <a name="delete-an-azure-migrate-project"></a>Eliminare un progetto di Azure Migrate

Questo articolo descrive come eliminare un progetto [di Azure Migrate.This](migrate-overview.md) article describes how to delete an Azure Migrate project.


## <a name="before-you-start"></a>Prima di iniziare

Prima di eliminare un progetto:

- Quando si elimina un progetto, il progetto e i metadati del computer individuati vengono eliminati.
- Se è stata collegata un'area di lavoro di Log Analytics allo strumento di valutazione del server per l'analisi delle dipendenze, decidere se si desidera eliminare l'area di lavoro. 
    - L'area di lavoro non viene eliminata automaticamente. Eliminarlo manualmente.
    - Verificare per cosa viene utilizzata un'area di lavoro prima di eliminarla. La stessa area di lavoro di Log Analytics può essere utilizzata per più scenari.
    - Prima di eliminare il progetto, è possibile trovare un collegamento all'area di lavoro in **Azure Migrate - Server** > **Azure Migrate - Valutazione del**server , in Area di **lavoro OMS**.
    - Per eliminare un'area di lavoro dopo l'eliminazione di un progetto, individuare l'area di lavoro nel gruppo di risorse pertinente e seguire [queste istruzioni.](../azure-monitor/platform/delete-workspace.md)


## <a name="delete-a-project"></a>Eliminare un progetto


1. Nel portale di Azure aprire il gruppo di risorse in cui è stato creato il progetto.
2. Nella pagina Del gruppo di risorse selezionare **Mostra tipi nascosti.**
3. Selezionare il progetto e le risorse associate che si desidera eliminare.
    - Il tipo di risorsa per i progetti di Azure Migrate è **Microsoft.Migrate/migrateprojects**.
    - Nella sezione successiva esaminare le risorse create per l'individuazione, la valutazione e la migrazione in un progetto di Azure Migrate.In the next section, review the resources created for discovery, assessment, and migration in an Azure Migrate project.
    - Se il gruppo di risorse contiene solo il progetto di Azure Migrate, è possibile eliminare l'intero gruppo di risorse.
    - Se si vuole eliminare un progetto dalla versione precedente di Azure Migrate, i passaggi sono gli stessi. Il tipo di risorsa per questi progetti è Progetto di **migrazione**.


## <a name="created-resources"></a>Risorse create

Queste tabelle riepilogano le risorse create per l'individuazione, la valutazione e la migrazione in un progetto di Azure Migrate.These tables summarize the resources created for discovery, assessment, and migration in an Azure Migrate project.

> [!NOTE]
> Eliminare l'insieme di credenziali delle chiavi con cautela perché potrebbe contenere chiavi di sicurezza.

### <a name="vmwarephysical-server"></a>VMware/server fisico

**Risorsa** | **Tipo**
--- | ---
"Nomeaccessorio"kv | Insieme di credenziali delle chiavi
Sito "NomeAccessorio" | Microsoft.OffAzure/VMwareSiti
"NomeProgetto" | Microsoft.Migrate/migrateprojects
Progetto "NomeProgetto" | Microsoft.Migrate/assessmentProjects
"NomeProgetto"rsvault | Insieme di credenziali dei servizi di ripristino
"NomeProgetto"-MigrateVault- | Insieme di credenziali dei servizi di ripristino
migrateappligwsa | Account di archiviazione
migrateapplilsa | Account di archiviazione
migrateapplicsa | Account di archiviazione
migrateapplikv | Insieme di credenziali delle chiavi
migrateapplisbns16041 | Spazio dei nomi del bus di servizio

### <a name="hyper-v-vm"></a>Macchina virtuale Hyper-V 

**Risorsa** | **Tipo**
--- | ---
"NomeProgetto" | Microsoft.Migrate/migrateprojects
Progetto "NomeProgetto" | Microsoft.Migrate/assessmentProjects
HyperV | Insieme di credenziali delle chiavi
HyperV - Sito | Microsoft.OffAzure/HyperVSites
"NomeProgetto"-MigrateVault- | Insieme di credenziali dei servizi di ripristino


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come aggiungere ulteriori strumenti di [valutazione](how-to-assess.md) e [migrazione.](how-to-migrate.md) 
