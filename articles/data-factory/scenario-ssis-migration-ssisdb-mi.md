---
title: Migrazione SSIS con istanza gestita di database SQL di Azure come destinazione del carico di lavoro del database
description: Migrazione SSIS con istanza gestita di database SQL di Azure come destinazione del carico di lavoro del database.
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
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929788"
---
# <a name="ssis-migration-with-azure-sql-database-managed-instance-as-the-database-workload-destination"></a>Migrazione SSIS con istanza gestita di database SQL di Azure come destinazione del carico di lavoro del database

Quando si esegue la migrazione dei carichi di lavoro del database da SQL Server locale all'istanza gestita di database SQL di Azure, è necessario avere familiarità con il [servizio migrazione dati di Azure](https://docs.microsoft.com/azure/dms/dms-overview)(DMS) e le [topologie di rete per le migrazioni di istanze gestite di database SQL di Azure con DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies).

Questo articolo è incentrato sulla migrazione dei pacchetti di SQL Server Integration Service (SSIS) archiviati nel catalogo SSIS (SSISDB) e SQL Server Agent processi che pianificano le esecuzioni del pacchetto SSIS.

## <a name="migrate-ssis-catalog-ssisdb"></a>Eseguire la migrazione del catalogo SSIS (SSISDB)

La migrazione di SSISDB può essere eseguita tramite DMS, come descritto nell'articolo [eseguire la migrazione di pacchetti SSIS in un'istanza gestita di database SQL di Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance).

## <a name="ssis-jobs-to-azure-sql-database-managed-instance-agent"></a>Da processi SSIS a agente istanza gestita di database SQL di Azure

Istanza gestita di database SQL di Azure ha un'utilità di pianificazione nativa di prima classe, come SQL Server Agent in locale.  Poiché uno strumento di migrazione per i processi SSIS non è ancora disponibile, è necessario eseguirne la migrazione da SQL Server Agent locale all'agente di istanza gestita di database SQL di Azure tramite script o copia manuale.

## <a name="additional-resources"></a>Risorse aggiuntive

- [Data Factory di Azure](https://docs.microsoft.com/azure/data-factory/introduction)
- [Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
- [Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/dms-overview)
- [Topologie di rete per le migrazioni di istanze gestite del database SQL di Azure con DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies)
- [Eseguire la migrazione di pacchetti SSIS in un'istanza gestita di database SQL di Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="next-steps"></a>Passaggi successivi

- [Connettersi al catalogo SSIS (SSISDB) in Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Eseguire pacchetti SSIS distribuiti in Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
