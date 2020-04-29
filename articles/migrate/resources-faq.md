---
title: Domande frequenti Azure Migrate
description: Risposte alle domande più comuni sul servizio Azure Migrate.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 9b71888b284fd9cc125def4758d1e3800a92acf2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81530318"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrate: domande comuni

Questo articolo risponde alle domande più comuni su Azure Migrate. Per eventuali domande dopo aver letto questo articolo, è possibile pubblicarle nel [forum Azure migrate](https://aka.ms/AzureMigrateForum). È anche possibile esaminare gli articoli seguenti:

- Domande sull' [appliance Azure migrate](common-questions-appliance.md)
- Domande su [individuazione, valutazione e visualizzazione delle dipendenze](common-questions-discovery-assessment.md)

## <a name="what-is-azure-migrate"></a>Cos'è Azure Migrate?

Azure Migrate offre un hub centrale per tenere traccia dell'individuazione, della valutazione e della migrazione delle app e dei carichi di lavoro locali e delle macchine virtuali del cloud pubblico e privato in Azure. L'hub fornisce strumenti Azure Migrate per la valutazione e la migrazione e offerte ISV di terze parti. [Altre informazioni](migrate-services-overview.md)

## <a name="what-can-i-do-with-azure-migrate"></a>Che cosa è possibile fare con Azure Migrate?

Usare Azure Migrate per individuare, valutare e migrare l'infrastruttura, le applicazioni e i dati locali in Azure. Azure Migrate supporta la valutazione e la migrazione di VM VMware locali, VM Hyper-V, server fisici, altre macchine virtuali virtualizzate, database, app Web e desktop virtuali. 

## <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Qual è la differenza tra Azure Migrate e Azure Site Recovery?

[Azure migrate](migrate-services-overview.md) fornisce un hub centralizzato per la valutazione e la migrazione ad Azure. 

[Azure Site Recovery](../site-recovery/site-recovery-overview.md) è una soluzione di ripristino di emergenza. 

Lo strumento di migrazione del server Azure Migrate: USA alcune funzionalità di Site Recovery back-end per la migrazione in modalità Lift-and-Shift di alcuni computer locali.

## <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Qual è la differenza tra Azure Migrate: server assessment e MAP Toolkit?

Server Assessment offre una valutazione che aiuta la preparazione alla migrazione e la valutazione dei carichi di lavoro per la migrazione ad Azure. [Microsoft Assessment and Planning (Map) Toolkit](https://www.microsoft.com/download/details.aspx?id=7826) consente di eseguire altre attività, tra cui la pianificazione della migrazione per le versioni più recenti dei sistemi operativi client e server Windows e il rilevamento dell'utilizzo del software. Per questi scenari, continuare a usare MAP Toolkit.

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>Qual è la differenza tra la valutazione del server e la Site Recovery Deployment Planner?

Server assessment è uno strumento di pianificazione della migrazione. Il Site Recovery Deployment Planner è uno strumento di pianificazione del ripristino di emergenza.

Scegliere lo strumento in base alle operazioni che si desidera eseguire:

- **Pianificare la migrazione locale ad Azure**: se si prevede di eseguire la migrazione dei server locali in Azure, usare la valutazione del server per la pianificazione della migrazione. Server Assessment valuta i carichi di lavoro locali e fornisce indicazioni e strumenti per semplificare la migrazione. Dopo aver installato il piano di migrazione, è possibile usare strumenti come Azure Migrate: migrazione del server per eseguire la migrazione dei computer in Azure.
- **Pianificare il ripristino di emergenza in Azure**: se si prevede di configurare il ripristino di emergenza da locale ad azure con Site Recovery, usare il Deployment Planner Site Recovery. Il Deployment Planner fornisce una valutazione approfondita Site Recovery specifica dell'ambiente locale allo scopo del ripristino di emergenza. Fornisce consigli correlati al ripristino di emergenza, ad esempio la replica e il failover.

## <a name="how-does-server-migration-work-with-site-recovery"></a>Funzionamento della migrazione del server con Site Recovery

- Se si usa Azure Migrate: migrazione del server per eseguire una migrazione senza *agenti* di macchine virtuali VMware locali, la migrazione è nativa per Azure Migrate e Site Recovery non viene usata.
- Se si usa Azure Migrate: migrazione server per eseguire una migrazione *basata su agente* di macchine virtuali VMware o se si esegue la migrazione di macchine virtuali Hyper-V o server fisici, Azure migrate: la migrazione del server usa il motore di replica di Azure Site Recovery.

## <a name="which-geographies-are-supported"></a>Quali aree geografiche sono supportate?

Esaminare le aree geografiche supportate per i cloud [pubblici](migrate-support-matrix.md#supported-geographies-public-cloud) e [governativi](migrate-support-matrix.md#supported-geographies-azure-government).

## <a name="how-do-i-get-started"></a>Come iniziare

Identificare lo strumento necessario, quindi aggiungere lo strumento a un progetto Azure Migrate. 

Per aggiungere uno strumento ISV o Mover:

1. Per iniziare, ottenere una licenza o iscriversi per una versione di valutazione gratuita, in base ai criteri degli strumenti. Le licenze per gli strumenti sono conformi al modello di licenza dell'ISV o dello strumento.
2. In ogni strumento è disponibile un'opzione per connettersi ad Azure Migrate. Seguire le istruzioni e la documentazione dello strumento per connettere lo strumento con Azure Migrate.

È possibile tenere traccia del percorso di migrazione dall'interno del Azure Migrate progetto, in Azure e in altri strumenti.

## <a name="how-do-i-delete-a-project"></a>Ricerca per categorie eliminare un progetto?

Informazioni su come [eliminare un progetto](how-to-delete-project.md). 

## <a name="next-steps"></a>Passaggi successivi

Leggere la [Panoramica di Azure migrate](migrate-services-overview.md).
