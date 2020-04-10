---
title: Eseguire la migrazione dei carichi di lavoro SSIS locali a SSIS in Azure Data FactoryMigrate on-premises SSIS workloads to SSIS in Azure Data Factory
description: Eseguire la migrazione dei carichi di lavoro SSIS locali a SSIS in ADF.
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
ms.openlocfilehash: 6ad5bb26959916f60973a8c0274e17eee03aa7a1
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991467"
---
# <a name="migrate-on-premises-ssis-workloads-to-ssis-in-adf"></a>Eseguire la migrazione dei carichi di lavoro SSIS locali a SSIS in ADFMigrate on-premises SSIS workloads to SSIS in ADF

## <a name="overview"></a>Panoramica

Quando si esegue la migrazione dei carichi di lavoro del database da SQL Server in locale ai servizi di database di Azure, ovvero il database SQL di Azure o l'istanza gestita del database SQL di Azure, sarà necessario eseguire anche la migrazione dei carichi di lavoro ETL in SQL Server Integration Services (SSIS) come uno dei servizi a valore aggiunto primario.

Azure-SSIS Integration Runtime (IR) in Azure Data Factory (ADF) supporta l'esecuzione di pacchetti SSIS. Dopo aver eseguito il provisioning del runtime di integrazione Azure-SSIS, è possibile usare strumenti familiari, ad esempio SQL Server Data Tools (SSDT)/SQL Server Management Studio (SSMS) e utilità della riga di comando, ad esempio dtinstall/dtutil/dtexec, per distribuire ed eseguire i pacchetti in Azure.Once Azure-SSIS IR is provisioned, you can then use familiar tools, such as SQL Server Data Tools (SSDT)/SQL Server Management Studio (SSMS) and command-line utilities, such as dtinstall/dtutil/dtexec, to deploy and run your packages in Azure. Per altre info, vedi Panoramica di [Azure SSIS lift-and-shift.](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)

Questo articolo evidenzia il processo di migrazione dei carichi di lavoro ETL da SSIS locale a SSIS in ADF. Il processo di migrazione è costituito da due fasi: **Valutazione** e **Migrazione**.

## <a name="assessment"></a>Valutazione

Per stabilire un piano di migrazione completo, una valutazione approfondita consente di identificare i problemi con i pacchetti SSIS di origine che impedirebbero una migrazione corretta.

Data Migration Assistant (DMA) è uno strumento scaricabile gratuitamente per questo scopo che può essere installato ed eseguito localmente. Progetto di valutazione DMA di tipo **Integration Services** può essere creato per valutare i pacchetti SSIS in batch e identificare i problemi di compatibilità che vengono presentati nelle categorie seguenti:

- Migration blockers: These are compatibility issues that block the migration source packages to run on Azure-SSIS IR. DMA fornisce indicazioni per risolvere questi problemi.

- Problemi informativi: sono funzionalità parzialmente supportate o deprecate utilizzate nei pacchetti di origine. DMA fornisce un set completo di consigli, approcci alternativi disponibili in Azure e procedure di attenuazione per risolvere.

### <a name="four-storage-types-for-ssis-packages"></a>Quattro tipi di archiviazione per i pacchetti SSIS

- catalogo SSIS (SSISDB). Questo è stato introdotto con SQL Server 2012 e contiene un set di stored procedure, viste e funzioni con valori di tabella utilizzate per l'utilizzo di progetti/pacchetti SSIS.
- File system.
- Database di sistema di SQL Server (MSDB).
- Archivio pacchetti SSIS. Si tratta di un livello di gestione dei pacchetti in cima a due sottotipi:
  - MSDB, che è un database di sistema in SQL Server utilizzato per archiviare i pacchetti SSIS.
  - File system gestito, ovvero una cartella specifica nel percorso di installazione di SQL ServerSQL Server utilizzata per archiviare i pacchetti SSIS.

DMA supporta attualmente la valutazione batch dei pacchetti archiviati nel **file system,** nell'archivio **pacchetti**e nel **catalogo SSIS** a partire dalla **versione DMA v5.0**.

Ottenere [DMA](https://docs.microsoft.com/sql/dma/dma-overview), ed [eseguire la valutazione del pacchetto con esso](https://docs.microsoft.com/sql/dma/dma-assess-ssis).

## <a name="migration"></a>Migrazione

A seconda dei tipi di [archiviazione](#four-storage-types-for-ssis-packages) dei pacchetti SSIS di origine e della destinazione della migrazione dei carichi di lavoro del database, i passaggi per eseguire la migrazione dei **pacchetti SSIS** e dei processi di SQL Server **Agent** che pianificano le esecuzioni dei pacchetti SSIS possono variare. Esistono due possibili scenari:

- [**Istanza gestita del database SQL** di Azure come destinazione del carico di lavoro del databaseAzure SQL Database managed instance as database workload destination](#azure-sql-database-managed-instance-as-database-workload-destination)
- [**Database SQL** di Azure come destinazione del carico di lavoro del databaseAzure SQL Database as database workload destination](#azure-sql-database-as-database-workload-destination)

### <a name="azure-sql-database-managed-instance-as-database-workload-destination"></a>**Istanza gestita del database SQL** di Azure come destinazione del carico di lavoro del databaseAzure SQL Database managed instance as database workload destination

| **Tipo di archiviazione del pacchetto** |Come eseguire la migrazione batch dei pacchetti SSISHow to batch-migrate SSIS packages|Come eseguire la migrazione batch dei processi SSISHow to batch-migrate SSIS jobs|
|-|-|-|
|Ssisdb|[Eseguire la migrazione di **SSISDBMigrate SSISDB**](scenario-ssis-migration-ssisdb-mi.md)|[Eseguire la migrazione dei processi SSIS all'agente dell'istanza gestita del database SQL di AzureMigrate SSIS jobs to Azure SQL Database managed instance agent](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-azure-sql-database-managed-instance-agent)|
|File system|Ridistribuirli in condivisioni di file/File di Azure tramite dtinstall/dtutil/copia manuale o per mantenere i file system a cui accedere tramite VNet/Self-Hosted IR. Per ulteriori informazioni, vedere [utilità dtutil](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|<li> Eseguire la migrazione con [SSIS Job Migration Wizard in SSMS]. (how-to-migrate-ssis-job-ssms.md) <li>Convertirli in pipeline/attività/trigger ADF tramite il portale script/SSMS/ADF. Per ulteriori informazioni, vedere [Funzionalità di pianificazione di SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|SQL Server (MSDB)|Esportarli in file system/condivisioni file/file di Azure tramite SSMS/dtutil. Per ulteriori informazioni, consultate [Esportazione di pacchetti SSIS.](https://docs.microsoft.com/sql/integration-services/import-and-export-packages-ssis-service)|Convertirli in pipeline/attività/trigger ADF tramite il portale script/SSMS/ADF. Per ulteriori informazioni, vedere [Funzionalità di pianificazione di SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Archivio pacchetti|Esportarli in file system/condivisioni file/file di Azure tramite SSMS/dtutil o ridistribuirli in condivisioni file/File di Azure tramite dtinstall/dtutil/manual copy o conservarli nei file system per accedere tramite VNet/Self-Hosted IR. Per altre info, vedi Utilità dtutil. Per ulteriori informazioni, vedere [utilità dtutil](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|Convertirli in pipeline/attività/trigger ADF tramite il portale script/SSMS/ADF. Per ulteriori informazioni, vedere [Funzionalità di pianificazione di SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

### <a name="azure-sql-database-as-database-workload-destination"></a>**Database SQL** di Azure come destinazione del carico di lavoro del databaseAzure SQL Database as database workload destination

| **Tipo di archiviazione del pacchetto** |Come eseguire la migrazione batch dei pacchetti SSISHow to batch-migrate SSIS packages|Come eseguire la migrazione batch dei processiHow to batch-migrate jobs|
|-|-|-|
|Ssisdb|Ridistribuire in Azure-SSISDB tramite SSDT/SSMS. Per altre informazioni, vedere Distribuzione di pacchetti SSIS in Azure.For more info, see [Deploying SSIS packages in Azure.](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)|Convertirli in pipeline/attività/trigger ADF tramite il portale script/SSMS/ADF. Per ulteriori informazioni, vedere [Funzionalità di pianificazione di SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|File system|Ridistribuirli in condivisioni di file/File di Azure tramite dtinstall/dtutil/copia manuale o per mantenere i file system a cui accedere tramite VNet/Self-Hosted IR. Per ulteriori informazioni, vedere [utilità dtutil](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|<li> Eseguire la migrazione con [SSIS Job Migration Wizard in SSMS]. (how-to-migrate-ssis-job-ssms.md) <li> Convertirli in pipeline/attività/trigger ADF tramite il portale script/SSMS/ADF. Per ulteriori informazioni, vedere [Funzionalità di pianificazione di SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|SQL Server (MSDB)|Esportarli in file system/condivisioni file/file di Azure tramite SSMS/dtutil. Per ulteriori informazioni, consultate [Esportazione di pacchetti SSIS.](https://docs.microsoft.com/sql/integration-services/import-and-export-packages-ssis-service)|Convertirli in pipeline/attività/trigger ADF tramite il portale script/SSMS/ADF. Per ulteriori informazioni, vedere [Funzionalità di pianificazione di SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Archivio pacchetti|Esportarli in file system/condivisioni file/file di Azure tramite SSMS/dtutil o ridistribuirli in condivisioni file/File di Azure tramite dtinstall/dtutil/manual copy o conservarli nei file system per accedere tramite VNet/Self-Hosted IR. Per altre info, vedi Utilità dtutil. Per ulteriori informazioni, vedere [utilità dtutil](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|Convertirli in pipeline/attività/trigger ADF tramite il portale script/SSMS/ADF. Per ulteriori informazioni, vedere [Funzionalità di pianificazione di SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

## <a name="additional-resources"></a>Risorse aggiuntive

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview)
- [Sollevare e trasferire i carichi di lavoro SSIS nel cloud](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview?view=sql-server-2017)
- [Eseguire la migrazione di pacchetti SSIS a un'istanza gestita del database SQL di Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)
- [Ridistribuire i pacchetti nel database SQL di AzureRedeploy packages to Azure SQL Database](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)

## <a name="next-steps"></a>Passaggi successivi

- [Convalidare pacchetti SSIS distribuiti in Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-validate-packages)
- [Eseguire pacchetti SSIS distribuiti in AzureRun SSIS packages deployed in Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
- [Monitorare il runtime di integrazione Azure-SSISMonitor Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)
- [Pianificare le esecuzioni dei pacchetti SSIS in AzureSchedule SSIS package executions in Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
