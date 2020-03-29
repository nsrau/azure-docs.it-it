---
title: Migrazione SSIS con l'istanza gestita del database SQL di Azure come destinazione del carico di lavoro del databaseSSSIS migration with Azure SQL Database managed instance as the database workload destination
description: Migrazione SSIS con l'istanza gestita del database SQL di Azure come destinazione del carico di lavoro del database.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 38010e3aaa2d0544dfbfe19135d25250d2b021a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74929788"
---
# <a name="ssis-migration-with-azure-sql-database-managed-instance-as-the-database-workload-destination"></a>Migrazione SSIS con l'istanza gestita del database SQL di Azure come destinazione del carico di lavoro del databaseSSSIS migration with Azure SQL Database managed instance as the database workload destination

Quando si esegue la migrazione dei carichi di lavoro del database da SQL Server in locale all'istanza gestita del database SQL di Azure, è necessario avere familiarità con il servizio DMS [(Data Migration Service)](https://docs.microsoft.com/azure/dms/dms-overview)di Azure e le topologie di rete per le migrazioni delle [istanze gestite del database SQL di Azure tramite DMS.](https://docs.microsoft.com/azure/dms/resource-network-topologies)

Questo articolo è incentrato sulla migrazione dei pacchetti di SQL Server Integration Service (SSIS) archiviati nel catalogo SSIS (SSISDB) e nei processi di SQL Server Agent che pianificano le esecuzioni dei pacchetti SSIS.

## <a name="migrate-ssis-catalog-ssisdb"></a>Eseguire la migrazione del catalogo SSIS (SSISDB)Migrate SSIS catalog (SSISDB)

La migrazione sSISDB può essere eseguita usando DMS, come descritto nell'articolo: [Eseguire la migrazione dei pacchetti SSIS a un'istanza gestita del database SQL di Azure.SSISDB](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)migration can be done using DMS, as described in the article: Migrate SSIS packages to an Azure SQL Database managed instance .

## <a name="ssis-jobs-to-azure-sql-database-managed-instance-agent"></a>Processi SSIS nell'agente dell'istanza gestita del database SQL di AzureSSIS jobs to Azure SQL Database managed instance agent

L'istanza gestita del database SQL di Azure ha un'utilità di pianificazione nativa di prima classe come Agente SQL Server in locale.  Poiché uno strumento di migrazione per i processi SSIS non è ancora disponibile, è necessario eseguirne la migrazione da Agente SQL Server in locale all'agente di istanza gestita del database SQL di Azure tramite script/copia manuale.

## <a name="additional-resources"></a>Risorse aggiuntive

- [Data Factory di AzureAzure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Runtime di integrazione Azure-SSISAzure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
- [Servizio di migrazione del database di AzureAzure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)
- [Topologie di rete per le migrazioni di istanze gestite del database SQL di Azure tramite DMSNetwork topologies for Azure SQL Database managed instance migrations using DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies)
- [Eseguire la migrazione di pacchetti SSIS a un'istanza gestita del database SQL di Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="next-steps"></a>Passaggi successivi

- [Connettersi al catalogo SSIS (SSISDB) in Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Eseguire pacchetti SSIS distribuiti in AzureRun SSIS packages deployed in Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
