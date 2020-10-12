---
title: Serie di migrazione di contoso | Microsoft Docs
description: Collegamenti agli scenari di migrazione di esempio contoso per la migrazione ad Azure.
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: raynew
ms.openlocfilehash: f9f7b3b64cf91019ed4e40d5d52b859419b74836
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86107632"
---
# <a name="contoso-migration-series"></a>Serie di migrazione Contoso


È presente una serie di articoli che illustrano in che modo l'organizzazione fittizia Contoso esegue la migrazione dell'infrastruttura locale al cloud [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) . 

La serie include scenari in cui viene illustrato come configurare una migrazione dell'infrastruttura e come eseguire tipi diversi di migrazioni. Gli scenari crescono in modo più complesso durante l'avanzamento. Negli articoli viene illustrato il modo in cui la società Contoso gestisce la migrazione, ma vengono fornite istruzioni generali e puntatori.

## <a name="migration-articles"></a>Articoli sulla migrazione

Nella tabella seguente sono riepilogati gli articoli della serie.  

- Ogni scenario di migrazione è determinato da requisiti aziendali leggermente diversi, che determinano la strategia di migrazione.
- Per ogni scenario di distribuzione vengono fornite informazioni sui driver e gli obiettivi aziendali, un'architettura proposta, i passaggi per eseguire la migrazione e i consigli per la pulizia e i passaggi successivi al termine della migrazione.


**Articolo** | **Dettagli** 
--- | --- 
[Articolo 1: Panoramica](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | Panoramica della serie di articoli, della strategia di migrazione di Contoso e delle app di esempio usate nella serie. 
[Articolo 2: Distribuire l'infrastruttura di Azure](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | Contoso prepara l'infrastruttura locale e l'infrastruttura di Azure per la migrazione. La stessa infrastruttura viene usata per tutti gli articoli della serie. 
[Articolo 3: Valutare le risorse locali per la migrazione in Azure](/azure/cloud-adoption-framework/migrate/azure-migration-guide/assess?tabs=Tools)  | Contoso esegue una valutazione dell'app SmartHotel360 locale in esecuzione su VMware. Valuta le macchine virtuali dell'app con il servizio Azure Migrate e il database SQL Server dell'app con Data Migration Assistant.
[Articolo 4: riospitare un'app in una macchina virtuale di Azure e SQL Istanza gestita](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | Contoso esegue una migrazione ad Azure in modalità lift-and-shift per la propria app SmartHotel360 locale. Contoso esegue la migrazione della VM front-end dell'app usando [Azure migrate](./migrate-services-overview.md). Contoso esegue la migrazione del database dell'app a un Istanza gestita SQL, usando il [servizio migrazione del database di Azure](../dms/dms-overview.md).
[Articolo 5: Eseguire il rehosting di un'app in VM di Azure](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | Contoso esegue la migrazione delle VM dell'app SmartHotel360 in macchine virtuali di Azure usando il servizio Azure Migrate. 
[Articolo 6: Eseguire il rehosting di un'app in macchine virtuali di Azure e in un gruppo di disponibilità AlwaysOn di SQL Server](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) | Contoso esegue la migrazione dell'app SmartHotel360. Contoso USA Azure Migrate per eseguire la migrazione delle VM dell'app. e il Servizio Migrazione del database per la migrazione del database dell'app in un cluster di SQL Server protetto da un gruppo di disponibilità AlwaysOn. 
[Articolo 7: Eseguire il rehosting di un'app Linux in VM di Azure](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | Contoso completa una migrazione in modalità Lift-and-Shift dell'app osTicket Linux alle VM di Azure, usando Azure Migrate.
[Articolo 8: Eseguire il rehosting di un'app Linux in macchine virtuali di Azure e in Database di Azure per MySQL](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Contoso esegue la migrazione dell'app osTicket Linux in macchine virtuali di Azure usando Azure Migrate. Esegue la migrazione del database dell'app al database di Azure per MySQ, usando il servizio migrazione del database di Azure (include un'opzione alternativa con MySQL Workbench).
[Articolo 9: Eseguire il refactoring di un'app in un'app Web di Azure e un database SQL di Azure](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | Contoso migra l'app SmartHotel360 in un'app Web di Azure ed esegue la migrazione del database dell'app al database SQL di Azure usando il servizio migrazione del database di Azure.
[Articolo 10: effettuare il refactoring di un'app di Windows con app Azure Services e SQL Istanza gestita](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql-managed-instance) | Contoso esegue la migrazione di un'app basata su Windows locale a un'app Web di Azure ed esegue la migrazione del database dell'app a un Istanza gestita SQL di Azure, usando il servizio migrazione del database di Azure.
[Articolo 11: effettuare il refactoring di un'app Linux in un'app Web di Azure e in un database di Azure per MySQL](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-linux-app-service-mysql) | Contoso migra l'app Linux osTicket in un'app Web di Azure in più aree di Azure, usando Gestione traffico di Azure, integrato con GitHub per il recapito continuo. Contoso esegue la migrazione del database dell'app in un'istanza di Database di Azure per MySQL. 
[Articolo 12: effettuare il refactoring Team Foundation Server su Azure DevOps Services](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | Contoso esegue la migrazione della propria distribuzione di Team Foundation Server in locale in Azure DevOps Services in Azure.
[Articolo 13: Ricompilare un'app in Azure](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | Contoso ricompila l'app SmartHotel usando una gamma di funzionalità e servizi di Azure, tra cui app Azure servizio, Azure Kubernetes Service (AKS), funzioni di Azure, servizi cognitivi di Azure e Azure Cosmos DB.
[Articolo 14: Passare a una migrazione completa in Azure](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | Dopo aver provato alcune combinazioni di migrazioni, Contoso si prepara a passare a una migrazione completa in Azure.



## <a name="next-steps"></a>Passaggi successivi

- [Scopri di più sulla](/azure/architecture/cloud-adoption/migrate/) migrazione cloud.
- Informazioni sulle strategie di migrazione per altri scenari (coppie di origine/destinazione) nella [Guida alla migrazione del database](https://datamigration.microsoft.com/).
