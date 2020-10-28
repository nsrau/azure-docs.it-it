---
title: Eseguire la migrazione di carichi di lavoro SQL Server Integration Services (SSIS) locali a SSIS in Azure Data Factory (ADF)
description: Eseguire la migrazione di carichi di lavoro SSIS locali a SSIS in ADF.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 9/3/2019
ms.openlocfilehash: ef4b01e38a60d6770ba476988fab934ada0bc631
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92635695"
---
# <a name="migrate-on-premises-ssis-workloads-to-ssis-in-adf"></a>Eseguire la migrazione di carichi di lavoro SSIS locali a SSIS in ADF

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Panoramica

Quando si esegue la migrazione dei carichi di lavoro del database da SQL Server locale ai servizi di database di Azure, in particolare il database SQL di Azure o Istanza gestita SQL di Azure, è necessario eseguire la migrazione dei carichi di lavoro ETL in SQL Server Integration Services (SSIS) come uno dei servizi primari aggiunti a valore.

Azure-SSIS Integration Runtime (IR) in Azure Data Factory (ADF) supporta l'esecuzione di pacchetti SSIS. Una volta eseguito il provisioning di Azure-SSIS IR, è possibile usare strumenti familiari, ad esempio SQL Server Data Tools (SSDT)/SQL Server Management Studio (SSMS) e utilità della riga di comando, ad esempio dtinstall/dtutil/dtexec, per distribuire ed eseguire i pacchetti in Azure. Per altre informazioni, vedere [Panoramica di Azure SSIS Lift-and-Shift](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

Questo articolo evidenzia il processo di migrazione dei carichi di lavoro ETL da SSIS locale a SSIS in ADF. Il processo di migrazione è costituito da due fasi: **Valutazione** e **Migrazione** .

## <a name="assessment"></a>Valutazione

Per definire un piano di migrazione completo, una valutazione completa consente di identificare i problemi relativi ai pacchetti SSIS di origine che potrebbero impedire una migrazione corretta.

Data Migration Assistant (DMA) è uno strumento scaricabile gratuitamente per questo scopo, che può essere installato ed eseguito in locale. È possibile creare un progetto di valutazione DMA di tipo **Integration Services** per valutare i pacchetti SSIS in batch e identificare i problemi di compatibilità presentati nelle categorie seguenti:

- Blocchi di migrazione: problemi di compatibilità che bloccano l'esecuzione dei pacchetti di origine della migrazione in Azure-SSIS IR. DMA fornisce indicazioni utili per risolvere questi problemi.

- Problemi informativi: funzionalità parzialmente supportate o deprecate utilizzate nei pacchetti di origine. DMA fornisce un set completo di indicazioni, approcci alternativi disponibili in Azure e procedure di mitigazione per la risoluzione.

### <a name="four-storage-types-for-ssis-packages"></a>Quattro tipi di archiviazione per i pacchetti SSIS

- Catalogo SSIS (SSISDB). Introdotta con SQL Server 2012 e contiene un set di stored procedure, viste e funzioni con valori di tabella utilizzate per l'utilizzo di progetti/pacchetti SSIS.
- File system.
- Database di sistema SQL Server (MSDB).
- Archivio pacchetti SSIS. Livello di gestione dei pacchetti su due sottotipi:
  - MSDB, ovvero un database di sistema in SQL Server usato per archiviare i pacchetti SSIS.
  - File system gestito, ovvero una cartella specifica nel percorso di installazione SQL Server usato per archiviare i pacchetti SSIS.

DMA supporta attualmente la valutazione batch dei pacchetti archiviati nel **file System** , nell' **Archivio pacchetti** e nel **Catalogo SSIS** a partire da **DMA versione v 5.0** .

Ottieni [DMA](/sql/dma/dma-overview)ed [Esegui la valutazione del pacchetto](/sql/dma/dma-assess-ssis).

## <a name="migration"></a>Migrazione

A seconda dei [tipi di archiviazione](#four-storage-types-for-ssis-packages) dei pacchetti SSIS di origine e della destinazione di migrazione dei carichi di lavoro del database, i passaggi per eseguire la migrazione di  **pacchetti SSIS** e **SQL Server Agent processi** che pianificano le esecuzioni di pacchetti SSIS possono variare. Esistono due possibili scenari:

- [**Istanza gestita SQL di Azure** come destinazione del carico di lavoro del database](#azure-sql-managed-instance-as-database-workload-destination)
- [**Database SQL di Azure** come destinazione del carico di lavoro del database](#azure-sql-database-as-database-workload-destination)

È anche un modo pratico per usare [gli strumenti DevOps di SSIS](/sql/integration-services/devops/ssis-devops-overview)per eseguire la ridistribuzione dei pacchetti batch nella destinazione della migrazione.  

### <a name="azure-sql-managed-instance-as-database-workload-destination"></a>**Istanza gestita SQL di Azure** come destinazione del carico di lavoro del database

| **Tipo di archiviazione del pacchetto** |Come eseguire la migrazione in batch di pacchetti SSIS|Come eseguire la migrazione in batch dei processi SSIS|
|-|-|-|
|SSISDB|[Eseguire la migrazione di **SSISDB**](scenario-ssis-migration-ssisdb-mi.md)|<li>[Eseguire la migrazione di processi SSIS ad Azure SQL Istanza gestita Agent](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-sql-managed-instance-agent) <li>Convertirli in pipeline/attività/trigger di ADF tramite script/SSMS/ADF Portal. Per altre informazioni, vedere [funzionalità di pianificazione di SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|File system|Ridistribuirli in condivisioni file/File di Azure tramite dtinstall/dtutil/copia manuale o per restare nei file System per accedere tramite il runtime di integrazione self-hosted o VNet. Per altre informazioni, vedere [dtutil Utility](/sql/integration-services/dtutil-utility).|<li>[Eseguire la migrazione di processi SSIS ad Azure SQL Istanza gestita Agent](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-sql-managed-instance-agent) <li> Eseguire la migrazione con [migrazione guidata processi SSIS in SSMS](how-to-migrate-ssis-job-ssms.md) <li>Convertirli in pipeline/attività/trigger di ADF tramite script/SSMS/ADF Portal. Per altre informazioni, vedere [funzionalità di pianificazione di SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|SQL Server (MSDB)|Esportarli in file System/condivisioni file/File di Azure tramite SSMS/dtutil. Per altre informazioni, vedere [esportazione di pacchetti SSIS](/sql/integration-services/service/package-management-ssis-service#import-and-export-packages).|Convertirli in pipeline/attività/trigger di ADF tramite script/SSMS/ADF Portal. Per altre informazioni, vedere [funzionalità di pianificazione di SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Archivio pacchetti|Esportarli nell'archivio pacchetti tramite SSMS/dtutil o ridistribuirli nell'archivio pacchetti tramite dtinstall/dtutil/copia manuale. Per altre informazioni, vedere [gestire i pacchetti con Azure-SSIS Integration Runtime archivio pacchetti](azure-ssis-integration-runtime-package-store.md).|<li>[Eseguire la migrazione di processi SSIS ad Azure SQL Istanza gestita Agent](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-sql-managed-instance-agent) <li> Convertirli in pipeline/attività/trigger di ADF tramite script/SSMS/ADF Portal. Per altre informazioni, vedere [funzionalità di pianificazione di SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

### <a name="azure-sql-database-as-database-workload-destination"></a>**Database SQL di Azure** come destinazione del carico di lavoro del database

| **Tipo di archiviazione del pacchetto** |Come eseguire la migrazione in batch di pacchetti SSIS|Come eseguire la migrazione in batch dei processi|
|-|-|-|
|SSISDB|Eseguire nuovamente la distribuzione in Azure-SSISDB tramite SSDT/SSMS. Per altre informazioni, vedere [distribuzione di pacchetti SSIS in Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial).|Convertirli in pipeline/attività/trigger di ADF tramite script/SSMS/ADF Portal. Per altre informazioni, vedere [funzionalità di pianificazione di SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|File system|Ridistribuirli in condivisioni file/File di Azure tramite dtinstall/dtutil/copia manuale o per restare nei file System per accedere tramite il runtime di integrazione self-hosted o VNet. Per altre informazioni, vedere [dtutil Utility](/sql/integration-services/dtutil-utility).|<li> Eseguire la migrazione con [migrazione guidata processi SSIS in SSMS](how-to-migrate-ssis-job-ssms.md) <li> Convertirli in pipeline/attività/trigger di ADF tramite script/SSMS/ADF Portal. Per altre informazioni, vedere [funzionalità di pianificazione di SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|SQL Server (MSDB)|Esportarli in file System/condivisioni file/File di Azure tramite SSMS/dtutil. Per altre informazioni, vedere [esportazione di pacchetti SSIS](/sql/integration-services/service/package-management-ssis-service#import-and-export-packages).|Convertirli in pipeline/attività/trigger di ADF tramite script/SSMS/ADF Portal. Per altre informazioni, vedere [funzionalità di pianificazione di SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Archivio pacchetti|Esportarli in file System/condivisioni file/File di Azure tramite SSMS/dtutil o ridistribuirli in condivisioni file/File di Azure tramite dtinstall/dtutil/copia manuale o mantenerli in file System per accedere tramite il runtime di integrazione self-hosted o VNet. Per altre informazioni, vedere dtutil Utility. Per altre informazioni, vedere [dtutil Utility](/sql/integration-services/dtutil-utility).|Convertirli in pipeline/attività/trigger di ADF tramite script/SSMS/ADF Portal. Per altre informazioni, vedere [funzionalità di pianificazione di SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

## <a name="additional-resources"></a>Risorse aggiuntive

- [Azure Data Factory](./introduction.md)
- [Database Migration Assistant](/sql/dma/dma-overview)
- [Lift-and-Shift dei carichi di lavoro SSIS nel cloud](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)
- [Strumenti DevOps SSIS](/sql/integration-services/devops/ssis-devops-overview)
- [Eseguire la migrazione di pacchetti SSIS a Istanza gestita di SQL di Azure](../dms/how-to-migrate-ssis-packages-managed-instance.md)
- [Ridistribuire i pacchetti nel database SQL di Azure](../dms/how-to-migrate-ssis-packages.md)

- [Accesso ai dati locali da Azure-SSIS Integration Runtime](https://techcommunity.microsoft.com/t5/sql-server-integration-services/vnet-or-no-vnet-secure-data-access-from-ssis-in-azure-data/ba-p/1062056)
- [Personalizzare l'installazione di Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md)
- [Accedere ad archivi dati e condivisioni file con l'autenticazione di Windows da pacchetti SSIS in Azure](ssis-azure-connect-with-windows-auth.md)
- [Usa autenticazione identità gestita](/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)
- [Usare l'Insieme di credenziali delle chiavi di Azure](store-credentials-in-key-vault.md)
- [Configurare il runtime di integrazione Azure-SSIS per garantire prestazioni elevate](configure-azure-ssis-integration-runtime-performance.md)
- [Come avviare e arrestare Azure-SSIS Integration Runtime in base a una pianificazione](how-to-schedule-azure-ssis-integration-runtime.md)

## <a name="next-steps"></a>Passaggi successivi

- [Convalidare pacchetti SSIS distribuiti in Azure](/sql/integration-services/lift-shift/ssis-azure-validate-packages)
- [Eseguire pacchetti SSIS distribuiti in Azure](/sql/integration-services/lift-shift/ssis-azure-run-packages)
- [Azure-SSIS Integration Runtime di monitoraggio](./monitor-integration-runtime.md#azure-ssis-integration-runtime)
- [Pianificare le esecuzioni di pacchetti SSIS in Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)