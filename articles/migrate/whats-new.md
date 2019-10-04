---
title: Novità di Azure Migrate | Microsoft Docs
description: Panoramica del servizio Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 06/10/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 2c3bc596076f3ec4f9d41f0da819ddd386fee63c
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811045"
---
# <a name="whats-new-in-azure-migrate"></a>Novità di Azure Migrate

[Azure Migrate](migrate-services-overview.md) consente di individuare, valutare ed eseguire la migrazione di server, app e dati nel cloud Microsoft Azure. Questo articolo riepiloga le nuove funzionalità in Azure Migrate.



## <a name="azure-migrate-new-version"></a>Nuova versione di Azure Migrate

Una nuova versione di Azure Migrate è stata rilasciata a luglio 2019. 

- **Versione corrente (nuova)** : usare questa versione per creare progetti di Azure Migrate, individuare i computer locali e orchestrare valutazioni e migrazioni. 
- **Versione precedente**: ai clienti che usano la versione precedente di Azure Migrate (è supportata solo la valutazione di macchine virtuali VMware locali), si consiglia di usare ora la versione corrente. Nella versione precedente non è più possibile creare nuovi progetti di Azure Migrate o eseguire nuove individuazioni. È ancora possibile accedere ai progetti esistenti. Per accedere ai progetti esistenti, nel portale di Azure cercare Azure Migrate in Tutti i servizi. Tra le notifiche di Azure Migrate è disponibile un collegamento per accedere ai progetti di Azure Migrate precedenti.


## <a name="azure-migrate-features"></a>Funzionalità di Azure Migrate

La nuova versione di Azure Migrate offre varie nuove funzionalità:


- **Piattaforma di migrazione unificata**: Azure Migrate offre ora un unico portale per centralizzare, gestire e tenere traccia del percorso di migrazione ad Azure, con miglioramenti per il flusso di distribuzione e l'esperienza del portale.
- **Strumenti di valutazione e migrazione**: Azure Migrate offre strumenti nativi e si integra con altri servizi di Azure, oltre che con strumenti di fornitori di software indipendenti (ISV). [Vedere altre informazioni](migrate-services-overview.md#isv-integration) sull'integrazione ISV.
- **Valutazione di Azure Migrate**: lo strumento Valutazione server di Azure Migrate consente di valutare le macchine virtuali VMware e le macchine virtuali Hyper-V per la migrazione ad Azure. È anche possibile valutare la migrazione usando altri servizi di Azure e strumenti ISV.
- **Migrazione con Azure Migrate**: lo strumento Migrazione server di Azure Migrate consente di eseguire la migrazione ad Azure di macchine virtuali VMware locali, macchine virtuali Hyper-V, server fisici, altri server virtualizzati e macchine virtuali del cloud privato/pubblico. È anche possibile eseguire la migrazione ad Azure usando strumenti ISV.
- **Appliance di Azure Migrate**: Azure Migrate distribuisce un'appliance leggera per l'individuazione e la valutazione delle macchine virtuali VMware e delle macchine virtuali Hyper-V locali.
    - Questa appliance viene usata dagli strumenti Valutazione server e Migrazione server di Azure Migrate per la migrazione senza agenti.
    - L'appliance individua continuamente i metadati e i dati sulle prestazioni dei server, ai fini della valutazione e della migrazione.  
- **Migrazione di macchine virtuali VMware**:  Lo strumento Migrazione server di Azure Migrate offre un paio di metodi per la migrazione di macchine virtuali VMware locali ad Azure.  Una migrazione senza agenti usando l'appliance di Azure Migrate e una migrazione basata su agente che usa un'appliance di replica e distribuisce un agente in ogni macchina virtuale di cui si vuole eseguire la migrazione. [Altre informazioni](server-migrate-overview.md)
 - **Valutazione e migrazione dei database**: da Azure Migrate è possibile valutare i database locali per la migrazione ad Azure usando Migration Assistant per il database di Azure. È possibile eseguire la migrazione di database usando Servizio Migrazione del database di Azure.
- **Migrazione di app Web**: è possibile valutare le app Web usando un URL di endpoint pubblico con Servizio app di Azure. Per la migrazione di app .NET interne, è possibile scaricare ed eseguire Migration Assistant per il servizio app. 
- **Data Box**: importazione di grandi quantità di dati offline in Azure usando Azure Data Box in Azure Migrate.


## <a name="next-steps"></a>Passaggi successivi

- Vedere [altre informazioni](https://azure.microsoft.com/pricing/details/azure-migrate/) sui prezzi di Azure Migrate.
- [Vedere le domande frequenti](resources-faq.md) su Azure Migrate.
- Provare le esercitazioni per valutare le [macchine virtuali VMware](tutorial-assess-vmware.md) e le [macchine virtuali Hyper-V](tutorial-assess-hyper-v.md).
