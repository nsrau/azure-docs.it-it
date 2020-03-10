---
title: Novità di Azure Migrate
description: Informazioni sulle novità e gli aggiornamenti recenti del servizio Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 11/30/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 63b6783a2f36d5bc9e84ce8291e7025b27359b6c
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361918"
---
# <a name="whats-new-in-azure-migrate"></a>Novità di Azure Migrate

[Azure Migrate](migrate-services-overview.md) consente di individuare, valutare ed eseguire la migrazione di server locali, app e dati nel cloud Microsoft Azure. Questo articolo riepiloga le nuove funzionalità in Azure Migrate.



## <a name="update-november-2019"></a>Aggiornamento (novembre 2019)

Sono state aggiunte alcune nuove funzionalità in Azure Migrate:

- **Valutazione dei server fisici**: è ora supportata la valutazione dei server fisici locali, oltre alla migrazione dei server fisici già supportata.
- **Valutazione basata sull'importazione**: è ora supportata la valutazione dei computer tramite metadati e dati sulle prestazioni disponibili in un file CSV.
- **Individuazione di applicazioni**: Azure Migrate supporta ora l'individuazione a livello di applicazione di app, ruoli e funzionalità usando l'appliance di Azure migrate. Questa funzionalità è attualmente supportata solo per le macchine virtuali VMware ed è limitata all'individuazione, mentre la valutazione non è supportata al momento. [Altre informazioni](how-to-discover-applications.md)
- **Visualizzazione delle dipendenze senza agente**: non è più necessario installare in modo esplicito gli agenti per la visualizzazione delle dipendenze. Sono ora supportate sia le funzionalità senza agente che quelle basate su agenti.
- **Desktop virtuale**: usare gli strumenti ISV per valutare ed eseguire la migrazione dell'infrastruttura VDI (Virtual Desktop Infrastructure) locale al desktop virtuale di Windows in Azure.
- **App Web**: il servizio app Azure Migration Assistant, usato per la valutazione e la migrazione di app Web, è ora integrato in Azure migrate.

In Azure Migrate sono stati aggiunti nuovi strumenti di valutazione e migrazione:

- **Rackware**: offerta di migrazione cloud.
- **Mover**: valutazione offerta.

[Altre informazioni](migrate-services-overview.md) sull'uso di strumenti e offerte ISV per la valutazione e la migrazione in Azure Migrate.

## <a name="release-version-july-2019"></a>Versione di rilascio (luglio 2019)

La versione corrente di Azure Migrate è stata rilasciata nel mese di luglio 2019.

- **Versione corrente**: usare questa versione per creare progetti di Azure migrate, individuare computer locali e orchestrare valutazioni e migrazioni.
- **Versione precedente**: per i clienti che usano la versione precedente di Azure migrate (è supportata solo la valutazione delle macchine virtuali VMware locali), è ora necessario usare la versione corrente. Nella versione precedente non è più possibile creare nuovi progetti di Azure Migrate o eseguire nuove individuazioni. È ancora possibile accedere ai progetti esistenti. Per effettuare questa operazione nel portale di Azure > **Tutti i servizi**, cercare **Azure Migrate**. Tra le notifiche di Azure Migrate è disponibile un collegamento per accedere ai progetti di Azure Migrate precedenti.


### <a name="azure-migrate-features"></a>Funzionalità di Azure Migrate

La versione corrente di Azure Migrate offre numerose nuove funzionalità:


- **Piattaforma di migrazione unificata**: Azure migrate offre ora un unico portale per centralizzare, gestire e tenere traccia del percorso di migrazione in Azure, con un flusso di distribuzione migliorato e un'esperienza del portale.
- **Strumenti per la valutazione e la migrazione**: Azure migrate fornisce strumenti nativi e si integra con altri servizi di Azure, oltre che con gli strumenti di fornitori di software indipendenti (ISV). [Vedere altre informazioni](migrate-services-overview.md#isv-integration) sull'integrazione ISV.
- **Azure migrate Assessment**: con lo strumento Azure migrate server assessment è possibile valutare le macchine virtuali VMware e Hyper-V per la migrazione ad Azure. È anche possibile valutare la migrazione usando altri servizi di Azure e strumenti ISV.
- **Migrazione di Azure migrate**: con lo strumento di migrazione Azure migrate server è possibile eseguire la migrazione di macchine virtuali VMware locali e macchine virtuali Hyper-V in Azure, nonché server fisici, altri server virtualizzati e macchine virtuali del cloud privato/pubblico. È anche possibile eseguire la migrazione ad Azure usando strumenti ISV.
- **Azure migrate Appliance**: Azure migrate distribuisce un dispositivo leggero per l'individuazione e la valutazione delle macchine virtuali VMware locali e delle VM Hyper-V.
    - Questa appliance viene usata dagli strumenti Valutazione server e Migrazione server di Azure Migrate per la migrazione senza agenti.
    - L'appliance individua continuamente i metadati e i dati sulle prestazioni dei server, ai fini della valutazione e della migrazione.  
- **Migrazione di macchine virtuali VMware**: la migrazione del server Azure migrate offre un paio di metodi per la migrazione di macchine virtuali VMware locali in Azure.  Una migrazione senza agenti usando l'appliance di Azure Migrate e una migrazione basata su agente che usa un'appliance di replica e distribuisce un agente in ogni macchina virtuale di cui si vuole eseguire la migrazione. [Altre informazioni](server-migrate-overview.md)
 - **Valutazione e migrazione di database**: da Azure migrate è possibile valutare i database locali per la migrazione ad Azure usando il Migration Assistant di database di Azure. È possibile eseguire la migrazione di database usando Servizio Migrazione del database di Azure.
- **Migrazione di app Web**: è possibile valutare le app Web usando un URL di endpoint pubblico con il servizio app Azure. Per la migrazione di app .NET interne, è possibile scaricare ed eseguire Migration Assistant per il servizio app.
- **Data Box**: importare grandi quantità di dati offline in Azure usando Azure Data Box in Azure migrate.


## <a name="next-steps"></a>Passaggi successivi

- Vedere [altre informazioni](https://azure.microsoft.com/pricing/details/azure-migrate/) sui prezzi di Azure Migrate.
- [Vedere le domande frequenti](resources-faq.md) su Azure Migrate.
- Provare le esercitazioni per valutare le [macchine virtuali VMware](tutorial-assess-vmware.md) e le [macchine virtuali Hyper-V](tutorial-assess-hyper-v.md).
