---
title: Serie di migrazione Contoso Documenti Microsoft
description: Collegamenti a scenari di migrazione di esempio di Contoso per la migrazione ad Azure.Links to Contoso example migration scenarios, for migration to Azure.
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: raynew
ms.openlocfilehash: c57a9f85e8b12bd4e1e66a4fcd5d08ab5f7b9118
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676322"
---
# <a name="contoso-migration-series"></a>Serie di migrazione Contoso


Abbiamo una serie di articoli che dimostrano come l'organizzazione fittizia Contoso esegue la migrazione dell'infrastruttura locale al cloud di [Microsoft Azure.](https://azure.microsoft.com/overview/what-is-azure/) 

La serie include scenari che illustrano come configurare una migrazione dell'infrastruttura e come eseguire diversi tipi di migrazioni. Gli scenari aumentano di complessità man mano che progrediscono. Gli articoli mostrano come la società Contoso gestisce la migrazione, ma le istruzioni generali e i puntatori vengono forniti in tutto.

## <a name="migration-articles"></a>Articoli sulla migrazione

Nella tabella seguente sono riepilogati gli articoli della serie.  

- Ogni scenario di migrazione è guidato da requisiti aziendali leggermente diversi, che determinano la strategia di migrazione.
- Per ogni scenario di distribuzione vengono fornite informazioni sui driver e gli obiettivi di business, un'architettura proposta, i passaggi per eseguire la migrazione e i suggerimenti per la pulizia e i passaggi successivi dopo il completamento della migrazione.


**Articolo** | **Dettagli** 
--- | --- 
[Articolo 1: Panoramica](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | Panoramica della serie di articoli, della strategia di migrazione di Contoso e delle app di esempio usate nella serie. 
[Articolo 2: Distribuire l'infrastruttura di Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | Contoso prepara l'infrastruttura locale e l'infrastruttura di Azure per la migrazione. La stessa infrastruttura viene usata per tutti gli articoli della serie. 
[Articolo 3: Valutare le risorse locali per la migrazione in Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-migration-guide/assess?tabs=Tools)  | Contoso esegue una valutazione dell'app SmartHotel360 locale in esecuzione su VMware. Valuta le macchine virtuali dell'app con il servizio Azure Migrate e il database SQL Server dell'app con Data Migration Assistant.
[Articolo 4: Eseguire il rehosting di un'app in una macchina virtuale di Azure e in Istanza gestita di database SQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | Contoso esegue una migrazione ad Azure in modalità lift-and-shift per la propria app SmartHotel360 locale. Contoso esegue la migrazione della macchina virtuale front-end dell'app usando [Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-services-overview). Contoso esegue la migrazione del database dell'app in un'istanza gestita del database SQL di Azure usando il servizio Migrazione del database di [Azure.](https://docs.microsoft.com/azure/dms/dms-overview)
[Articolo 5: Eseguire il rehosting di un'app in VM di Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | Contoso esegue la migrazione delle macchine virtuali dell'app SmartHotel360 alle macchine virtuali di Azure usando il servizio Azure Migrate.Contoso migrates its SmartHotel360 app VMs to Azure VMs using the Azure Migrate service. 
[Articolo 6: Eseguire il rehosting di un'app in macchine virtuali di Azure e in un gruppo di disponibilità AlwaysOn di SQL Server](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) | Contoso esegue la migrazione dell'app SmartHotel360. Contoso usa Azure Migrate per eseguire la migrazione delle macchine virtuali dell'app. e il Servizio Migrazione del database per la migrazione del database dell'app in un cluster di SQL Server protetto da un gruppo di disponibilità AlwaysOn. 
[Articolo 7: Eseguire il rehosting di un'app Linux in VM di Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | Contoso completa una migrazione completa dell'app osTicket Linux alle macchine virtuali di Azure, usando Azure Migrate.
[Articolo 8: Eseguire il rehosting di un'app Linux in macchine virtuali di Azure e in Database di Azure per MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Contoso migrates its Linux osTicket app to Azure VMs using Azure Migrate. Esegue la migrazione del database dell'app al database di Azure per MySQ, usando il servizio di migrazione del database di Azure (include un'opzione alternativa tramite MySQL Workbench).
[Articolo 9: Eseguire il refactoring di un'app in un'app Web di Azure e un database SQL di Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | Contoso migrates its SmartHotel360 app to an Azure web app, and migrates the app database to an Azure SQL Server instance, using the Azure Database Migration Service.
[Articolo 10: Refactoring di un'app di Windows usando i servizi app di Azure e l'istanza gestita SQL](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql-managed-instance) | Contoso migrates an on-premises Windows-based app to an Azure web app, and migrates the app database to an Azure SQL Managed Instance, using the Azure Database Migration Service.
[Articolo 11: Refactoring di un'app Linux in un'app Web di Azure e un database di Azure per MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-linux-app-service-mysql) | Contoso esegue la migrazione dell'app osTicket Linux a un'app Web di Azure in più aree di Azure, usando Gestione traffico di Azure, integrata con GitHub per la distribuzione continua. Contoso esegue la migrazione del database dell'app in un'istanza di Database di Azure per MySQL. 
[Articolo 12: Refactoring di Team Foundation Server nei servizi DevOps di Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | Contoso esegue la migrazione della propria distribuzione di Team Foundation Server in locale in Azure DevOps Services in Azure.
[Articolo 13: Ricompilare un'app in Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | Contoso rebuilds its SmartHotel app using a range of Azure capabilities and services, including Azure App Service, Azure Kubernetes Service (AKS), Azure Functions, Azure Cognitive Services, and Azure Cosmos DB.
[Articolo 14: Passare a una migrazione completa in Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | Dopo aver provato alcune combinazioni di migrazioni, Contoso si prepara a passare a una migrazione completa in Azure.



## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sulla](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/) migrazione cloud.
- Per informazioni sulle strategie di migrazione per altri scenari (coppie di origine/destinazione) , vedere la Guida alla [migrazione del database](https://datamigration.microsoft.com/).
