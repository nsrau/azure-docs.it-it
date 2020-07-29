---
title: Migrazione SSIS con SQL di Azure Istanza gestita come destinazione del carico di lavoro del database
description: Migrazione SSIS con SQL di Azure Istanza gestita come destinazione del carico di lavoro del database.
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
ms.openlocfilehash: c91731d70d5db59ecf5daa726fd42ee42c58f51e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84116306"
---
# <a name="ssis-migration-with-azure-sql-managed-instance-as-the-database-workload-destination"></a>Migrazione SSIS con SQL di Azure Istanza gestita come destinazione del carico di lavoro del database

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Quando si esegue la migrazione dei carichi di lavoro del database da un'istanza di SQL Server al Istanza gestita SQL di Azure, è necessario avere familiarità con il [servizio di migrazione dei dati di Azure](https://docs.microsoft.com/azure/dms/dms-overview)e le [topologie di rete per le migrazioni SQL istanza gestita con DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies).

Questo articolo è incentrato sulla migrazione dei pacchetti di SQL Server Integration Service (SSIS) archiviati nel catalogo SSIS (SSISDB) e SQL Server Agent processi che pianificano le esecuzioni del pacchetto SSIS.

## <a name="migrate-ssis-catalog-ssisdb"></a>Eseguire la migrazione del catalogo SSIS (SSISDB)

La migrazione di SSISDB può essere eseguita tramite DMS, come descritto nell'articolo [eseguire la migrazione di pacchetti SSIS a SQL istanza gestita](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance).

## <a name="ssis-jobs-to-sql-managed-instance-agent"></a>Processi SSIS a SQL Istanza gestita Agent

SQL Istanza gestita dispone di un'utilità di pianificazione nativa di prima classe esattamente come SQL Server Agent locale.  Poiché uno strumento di migrazione per i processi SSIS non è ancora disponibile, è necessario eseguirne la migrazione da SQL Server Agent locale a SQL Istanza gestita Agent tramite script/copia manuale.

## <a name="additional-resources"></a>Risorse aggiuntive

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Runtime di integrazione Azure-SSIS](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
- [Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/dms-overview)
- [Topologie di rete per le migrazioni di SQL Istanza gestita tramite DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies)
- [Eseguire la migrazione di pacchetti SSIS a un Istanza gestita SQL](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="next-steps"></a>Passaggi successivi

- [Connettersi al catalogo SSIS (SSISDB) in Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Eseguire pacchetti SSIS distribuiti in Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
