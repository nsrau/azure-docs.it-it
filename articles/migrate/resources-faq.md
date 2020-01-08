---
title: Domande frequenti su Azure Migrate
description: Risposte alle domande più comuni sul servizio Azure Migrate.
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: snehaa
ms.openlocfilehash: 8bfa9237d365636c0bdaa3af06c5af23b683231d
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75563540"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrate: domande comuni

Questo articolo risponde alle domande più comuni su Azure Migrate. Se si hanno altre query dopo aver letto questo articolo, pubblicarle nel [forum Azure migrate](https://aka.ms/AzureMigrateForum). Per altre domande, vedere gli articoli seguenti:

- [Domande](common-questions-appliance.md) sull'appliance Azure migrate.
- [Domande](common-questions-discovery-assessment.md) su individuazione, valutazione e visualizzazione delle dipendenze.


## <a name="what-is-azure-migrate"></a>Cos'è Azure Migrate?

Azure Migrate offre un hub centrale per tenere traccia dell'individuazione, della valutazione e della migrazione delle app e dei carichi di lavoro locali e delle macchine virtuali del cloud privato/pubblico in Azure. L'hub fornisce gli strumenti di Azure Migrate per la valutazione e la migrazione, nonché offerte di ISV terzi. [Altre informazioni](migrate-services-overview.md)


## <a name="what-can-i-do-with-azure-migrate"></a>Che cosa è possibile fare con Azure Migrate?

Usare Azure Migrate per individuare, valutare e migrare l'infrastruttura, le applicazioni e i dati locali in Azure. Azure Migrate supporta la valutazione e la migrazione di VM VMware locali, VM Hyper-V, server fisici, altre macchine virtuali virtualizzate, database, app Web e desktop virtuali. 

## <a name="whats-the-difference-between-azure-migrate-and-site-recovery"></a>Qual è la differenza tra Azure Migrate e Site Recovery?

Azure Migrate fornisce un hub centralizzato per la valutazione e la migrazione ad Azure. [Azure Site Recovery](../site-recovery/site-recovery-overview.md) è una soluzione di ripristino di emergenza. Lo strumento di migrazione del server Azure Migrate: USA alcune funzionalità di Site Recovery back-end per la migrazione in modalità Lift-and-Shift di alcuni computer locali.

## <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Qual è la differenza tra Azure Migrate server assessment e MAP Toolkit?

Server Assessment offre una valutazione che aiuta la preparazione alla migrazione e la valutazione dei carichi di lavoro per la migrazione ad Azure. [Microsoft Assessment and Planning (Map) Toolkit](https://www.microsoft.com/download/details.aspx?id=7826) consente di eseguire altre attività, tra cui la pianificazione della migrazione per le versioni più recenti dei sistemi operativi client/server Windows e il rilevamento dell'utilizzo del software. Per questi scenari, continuare a usare MAP Toolkit.

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>Qual è la differenza tra la valutazione del server e la Site Recovery Deployment Planner?

Server assessment è uno strumento di pianificazione della migrazione. Il Site Recovery Deployment Planner è uno strumento di pianificazione del ripristino di emergenza.

- **Pianificare la migrazione locale ad Azure**: se si prevede di eseguire la migrazione dei server locali in Azure, usare la valutazione del server per la pianificazione della migrazione. Valuta i carichi di lavoro locali e fornisce indicazioni e strumenti per semplificare la migrazione. Dopo aver installato il piano di migrazione, è possibile usare gli strumenti, tra cui Azure Migrate migrazione del server, per eseguire la migrazione dei computer in Azure.
- **Pianificare il ripristino di emergenza in Azure**: se si prevede di configurare il ripristino di emergenza da locale ad azure con Site Recovery, usare il Deployment Planner Site Recovery. Il Deployment Planner fornisce una valutazione approfondita Site Recovery specifica dell'ambiente locale allo scopo del ripristino di emergenza. Fornisce consigli sul ripristino di emergenza, ad esempio la replica e il failover.

## <a name="how-does-server-migration-work-with-site-recovery"></a>Funzionamento della migrazione del server con Site Recovery

- Se si usa Azure Migrate: migrazione del server per eseguire una migrazione senza agenti di macchine virtuali VMware locali, la migrazione è nativa per Azure Migrate e Site Recovery non viene usata.
- Se si usa Azure Migrate: migrazione server per eseguire una migrazione basata su agente di macchine virtuali VMware o eseguire la migrazione di macchine virtuali Hyper-V o server fisici, la migrazione del server Azure Migrate usa il motore di replica di Azure Site Recovery.


## <a name="which-geographies-are-supported"></a>Quali aree geografiche sono supportate?

Esaminare le aree geografiche supportate Azure Migrate per le macchine virtuali [VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) e [Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects).

## <a name="how-do-i-get-started"></a>Come posso iniziare a usare la soluzione?

È sufficiente individuare lo strumento necessario e aggiungerlo a un progetto di Azure Migrate. Se si aggiunge uno strumento ISV o Movere:
- Per iniziare, ottenere una licenza o iscriversi per ricevere una versione di valutazione gratuita, in base ai requisiti dello strumento. Le licenze per gli strumenti sono conformi al modello di licenza dell'ISV o dello strumento.
- In ogni strumento è disponibile un'opzione per connettersi ad Azure Migrate. Per connettere lo strumento ad Azure Migrate, seguire le istruzioni e la documentazione per lo strumento.
È possibile tenere traccia centralmente del percorso di migrazione dall'interno del progetto di Azure Migrate, con strumenti di Azure e di altro tipo.

### <a name="how-do-i-delete-a-project"></a>Ricerca per categorie eliminare un progetto?

[Informazioni su come](how-to-delete-project.md) eliminare un progetto. 






## <a name="next-steps"></a>Passaggi successivi
Leggere la [Panoramica di Azure migrate](migrate-services-overview.md).
