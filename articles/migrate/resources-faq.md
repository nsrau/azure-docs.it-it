---
title: Azure Migrate FAQ
description: Risposte alle domande comuni sul servizio Azure Migrate.Get answers to common questions about the Azure Migrate service.
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: snehaa
ms.openlocfilehash: c85c5c6e11beb0178139dad152f56f420b2ac26f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78926724"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrate: domande comuniAzure Migrate: Common questions

Questo articolo risponde alle domande comuni su Azure Migrate.This article answers common questions about Azure Migrate. In caso di domande dopo aver letto questo articolo, è possibile pubblicarle nel [forum di Azure Migrate.](https://aka.ms/AzureMigrateForum) È inoltre possibile esaminare questi articoli:You can also review these articles:

- Domande [sull'appliance Azure Migrate](common-questions-appliance.md)
- Domande su [scoperta, valutazione e visualizzazione delle dipendenze](common-questions-discovery-assessment.md)

## <a name="what-is-azure-migrate"></a>Cos'è Azure Migrate?

Azure Migrate provides a central hub to track discovery, assessment, and migration of your on-premises apps and workloads and private and public cloud VMs to Azure. L'hub fornisce strumenti di Azure Migrate per la valutazione e la migrazione e offerte ISV di terze parti. [Scopri di più](migrate-services-overview.md).

## <a name="what-can-i-do-with-azure-migrate"></a>Cosa è possibile fare con Azure Migrate?

Usare Azure Migrate per individuare, valutare ed eseguire la migrazione dell'infrastruttura, delle applicazioni e dei dati locali in Azure.Use Azure Migrate to discover, assess, and migrate on-premises infrastructure, applications, and data to Azure. Azure Migrate supporta la valutazione e la migrazione di macchine virtuali VMware locali, macchine virtuali Hyper-V, server fisici, altre macchine virtuali virtualizzate, database, app Web e desktop virtuali. 

## <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Qual è la differenza tra Azure Migrate e Azure Site Recovery?

[Azure Migrate](migrate-services-overview.md) offre un hub centralizzato per la valutazione e la migrazione ad Azure.Azure Migrate provides a centralized hub for assessment and migration to Azure. 

[Azure Site Recovery](../site-recovery/site-recovery-overview.md) è una soluzione di ripristino di emergenza. 

Lo strumento Migrazione di Azure: Migrazione server usa alcune funzionalità di Site Recovery back-end per la migrazione di alcuni computer locali.

## <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Qual è la differenza tra Azure Migrate: Server Assessment e MAP Toolkit?

Server Assessment provides assessment to help with migration readiness, and evaluation of workloads for migration to Azure. [Microsoft Assessment and Planning (MAP) Toolkit](https://www.microsoft.com/download/details.aspx?id=7826) è utile per altre attività, tra cui la pianificazione della migrazione per le versioni più recenti dei sistemi operativi client e server Windows e il monitoraggio dell'utilizzo del software. Per questi scenari, continuare a utilizzare il toolkit MAP.

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>Qual è la differenza tra Server Assessment e Site Recovery Deployment Planner?

Server Assessment è uno strumento di pianificazione della migrazione. Pianificazione distribuzione di Site Recovery è uno strumento di pianificazione del ripristino di emergenza.

Scegli il tuo strumento in base a ciò che vuoi fare:

- **Pianificare**la migrazione locale in Azure: se si prevede di eseguire la migrazione dei server locali in Azure, usare la valutazione del server per la pianificazione della migrazione. Server Assessment valuta i carichi di lavoro locali e fornisce indicazioni e strumenti utili per eseguire la migrazione. Dopo aver pianificato il piano di migrazione, è possibile usare strumenti come Azure Migrate: Migrazione del server per eseguire la migrazione dei computer in Azure.After the migration plan is in place, you can use tools like Azure Migrate: Server Migration to migrate the machines to Azure.
- Pianificare il ripristino di **emergenza in Azure:** se si prevede di configurare il ripristino di emergenza da locale ad Azure con Site Recovery, usare Pianificazione distribuzione di Site Recovery.Plan disaster recovery to Azure : If you plan to set up disaster recovery from on-premises to Azure with Site Recovery, use the Site Recovery Deployment Planner. Deployment Planner fornisce una valutazione approfondita e specifica di Site Recovery dell'ambiente locale ai fini del ripristino di emergenza. Vengono forniti suggerimenti relativi al ripristino di emergenza, ad esempio la replica e il failover.

## <a name="how-does-server-migration-work-with-site-recovery"></a>Come funziona la migrazione del server con Site Recovery?

- Se si usa Azure Migrate: migrazione server per eseguire una migrazione *senza agenti* di macchine virtuali VMware locali, la migrazione è nativa in Azure Migrate e Site Recovery non viene usato.
- Se si usa Azure Migrate: migrazione server per eseguire una migrazione basata su agente di macchine virtuali VMware o se si esegue la migrazione di macchine virtuali Hyper-V o server fisici, Azure Migrate: la migrazione dei server usa il motore di replica di Azure Site Recovery.If you use Azure Migrate: Server Migration to perform an *agent-based* vMs of VMware VMs, or if you migrate Hyper-V VV VMs or physical servers, Azure Migrate: Server Migration uses the Azure Site Recovery replication engine.

## <a name="which-geographies-are-supported"></a>Quali aree geografiche sono supportate?

- **VMware VMs**: esaminare le [aree geografiche supportate](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware) di Azure Migrate per le macchine virtuali VMware.VMware VMs : Review the Azure Migrate supported geographies for VMware VMs.
- **Macchine virtuali Hyper-V:** esaminare le [aree geografiche supportate](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v) di Azure Migrate per le macchine virtuali Hyper-V.Hyper-V V VMs : Review the Azure Migrate supported geographies for Hyper-V VMs.

## <a name="how-do-i-get-started"></a>Come iniziare?

Identificare lo strumento necessario e quindi aggiungere lo strumento a un progetto di Azure Migrate.Identify the tool you need, and then add the tool to an Azure Migrate project. 

Per aggiungere uno strumento ISV o Movere:

1. Per iniziare, ottenere una licenza o iscriversi a una versione di valutazione gratuita, in conformità con i criteri dello strumento. Le licenze per gli strumenti sono conformi al modello di licenza dell'ISV o dello strumento.
2. In ogni strumento è disponibile un'opzione per connettersi ad Azure Migrate. Seguire le istruzioni e la documentazione dello strumento per connettere lo strumento con Azure Migrate.Follow the tool instructions and documentation to connect the tool with Azure Migrate.

È possibile tenere traccia del percorso di migrazione dal progetto Azure Migrate, in Azure e in altri strumenti.

## <a name="how-do-i-delete-a-project"></a>Come si elimina un progetto?

Informazioni su come [eliminare un progetto.](how-to-delete-project.md) 

## <a name="next-steps"></a>Passaggi successivi

Leggere la [panoramica di Azure Migrate](migrate-services-overview.md).
