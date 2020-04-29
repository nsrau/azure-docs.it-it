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
ms.openlocfilehash: 2e35e4eb750aa2244df920111b201d886599eaf6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81419052"
---
# <a name="ssis-migration-with-azure-sql-database-managed-instance-as-the-database-workload-destination"></a>Migrazione SSIS con istanza gestita di database SQL di Azure come destinazione del carico di lavoro del database

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Quando si esegue la migrazione dei carichi di lavoro del database da SQL Server locale all'istanza gestita di database SQL di Azure, è necessario avere familiarità con il [servizio migrazione dati di Azure](https://docs.microsoft.com/azure/dms/dms-overview)(DMS) e le [topologie di rete per le migrazioni di istanze gestite di database SQL di Azure con DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies).

Questo articolo è incentrato sulla migrazione dei pacchetti di SQL Server Integration Service (SSIS) archiviati nel catalogo SSIS (SSISDB) e SQL Server Agent processi che pianificano le esecuzioni del pacchetto SSIS.

## <a name="migrate-ssis-catalog-ssisdb"></a>Eseguire la migrazione del catalogo SSIS (SSISDB)

La migrazione di SSISDB può essere eseguita tramite DMS, come descritto nell'articolo [eseguire la migrazione di pacchetti SSIS in un'istanza gestita di database SQL di Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance).

## <a name="ssis-jobs-to-azure-sql-database-managed-instance-agent"></a>Da processi SSIS a agente istanza gestita di database SQL di Azure

Istanza gestita di database SQL di Azure ha un'utilità di pianificazione nativa di prima classe, come SQL Server Agent in locale.  Poiché uno strumento di migrazione per i processi SSIS non è ancora disponibile, è necessario eseguirne la migrazione da SQL Server Agent locale all'agente di istanza gestita di database SQL di Azure tramite script o copia manuale.

## <a name="additional-resources"></a>Risorse aggiuntive

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Runtime di integrazione Azure-SSIS](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
- [Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/dms-overview)
- [Topologie di rete per le migrazioni di istanze gestite del database SQL di Azure con DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies)
- [Eseguire la migrazione di pacchetti SSIS in un'istanza gestita di database SQL di Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="next-steps"></a>Passaggi successivi

- [Connettersi al catalogo SSIS (SSISDB) in Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Eseguire pacchetti SSIS distribuiti in Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
