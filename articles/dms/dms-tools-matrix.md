---
title: Matrice di strumenti del servizio migrazione del database di Azure
description: Informazioni sui servizi e gli strumenti disponibili per la migrazione dei database e per il supporto delle diverse fasi del processo di migrazione.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/10/2020
ms.openlocfilehash: 6259f365edeb3c3d94646a4b2ae019d0776dcc23
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75860844"
---
# <a name="services-and-tools-available-for-data-migration-scenarios"></a>Servizi e strumenti disponibili per gli scenari di migrazione dei dati

Questo articolo fornisce una matrice dei servizi e degli strumenti di Microsoft e di terze parti disponibili per aiutare con vari database, scenari di migrazione dei dati e attività specifiche.

Le tabelle seguenti identificano i servizi e gli strumenti che è possibile usare per pianificare correttamente la migrazione dei dati e per completare le varie fasi.

> [!NOTE]
> Nelle tabelle seguenti, gli elementi contrassegnati con un asterisco (*) rappresentano gli strumenti di terze parti.

## <a name="business-justification-phase"></a>Fase di motivazione aziendale

| Origine | Obiettivo | Individuare<br/>Inventario | Destinazione e SKU<br/>Suggerimento | TCO/ROI e<br/>Business case |
| --- | --- | --- | --- | --- |
| SQL Server | Azure SQL DB | [Microsoft Assessment and Planning (MAP) Toolkit](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloud Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [Calcolatore del costo totale di proprietà](https://azure.microsoft.com/pricing/tco/calculator/) |
 SQL Server | Istanza gestita del database SQL di Azure | [Microsoft Assessment and Planning (MAP) Toolkit](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloud Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [Calcolatore del costo totale di proprietà](https://azure.microsoft.com/pricing/tco/calculator/) |
| SQL Server | VM di Azure SQL | [Microsoft Assessment and Planning (MAP) Toolkit](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [Cloud Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [Calcolatore del costo totale di proprietà](https://azure.microsoft.com/pricing/tco/calculator/) |
| SQL Server | SQL DW | [Microsoft Assessment and Planning (MAP) Toolkit](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/)<br/>[Cloudamize*](https://www.cloudamize.com/) |  | [Calcolatore del costo totale di proprietà](https://azure.microsoft.com/pricing/tco/calculator/) |
| Servizi Desktop remoto SQL | Database SQL di Azure, Istanza gestita, Macchina virtuale |  | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [Calcolatore del costo totale di proprietà](https://azure.microsoft.com/pricing/tco/calculator/) |
| Oracle | Database SQL di Azure, Istanza gestita, Macchina virtuale | [Microsoft Assessment and Planning (MAP) Toolkit](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[MigVisor*](https://www.migvisor.com/) |  |
| Oracle | SQL DW | [Microsoft Assessment and Planning (MAP) Toolkit](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Oracle | DATABASE di Azure per PostgreSQL-<br/>Server singolo | [Microsoft Assessment and Planning (MAP) Toolkit](https://msdn.microsoft.com/library/bb977556.aspx)<br/>[Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) |  |  |
| MongoDB | Cosmos DB | [Cloudamize*](https://www.cloudamize.com/) | [Cloudamize*](https://www.cloudamize.com/) |  |
| Cassandra | Cosmos DB |  |  |  |
| MySQL | Database SQL di Azure, Istanza gestita, Macchina virtuale | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Cloud Atlas *](https://www.unifycloud.com/cloud-migration-tool/) | [Calcolatore del costo totale di proprietà](https://azure.microsoft.com/pricing/tco/calculator/) |
| MySQL | Database di Azure per MySQL | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) |  | [Calcolatore del costo totale di proprietà](https://azure.microsoft.com/pricing/tco/calculator/) |
| Servizi Desktop remoto MySQL | Database di Azure per MySQL |  |  | [Calcolatore del costo totale di proprietà](https://azure.microsoft.com/pricing/tco/calculator/) |
| PostgreSQL | DATABASE di Azure per PostgreSQL-<br/>Server singolo | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) |  | [Calcolatore del costo totale di proprietà](https://azure.microsoft.com/pricing/tco/calculator/) |
| Servizi Desktop remoto PostgreSQL | DATABASE di Azure per PostgreSQL-<br/>Server singolo |  |  | [Calcolatore del costo totale di proprietà](https://azure.microsoft.com/pricing/tco/calculator/) |
| DB2 | Database SQL di Azure, Istanza gestita, Macchina virtuale | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Accesso | Database SQL di Azure, Istanza gestita, Macchina virtuale | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Sybase-SAP ASE | Database SQL di Azure, Istanza gestita, Macchina virtuale | [Azure Migrate](https://azure.microsoft.com/services/azure-migrate/) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Sybase-SAP IQ | Database SQL di Azure, Istanza gestita, Macchina virtuale |  |  |  |
| | | | | |

## <a name="pre-migration-phase"></a>Fase di pre-migrazione

| Origine | Obiettivo | Accesso ai dati dell'app<br/>Valutazione livello | Database<br/>Valutazione | Performance<br/>Valutazione |
| --- | --- | --- | --- | --- |
| SQL Server | Azure SQL DB | [Demt](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) / [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloud Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | Istanza gestita del database SQL di Azure | [Demt](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) / [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloud Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | VM di Azure SQL | [Demt](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) / [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloud Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | SQL DW | [DEMT](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) |  |  |
| Servizi Desktop remoto SQL | Database SQL di Azure, Istanza gestita, Macchina virtuale | [Demt](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) / [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [DEA](https://www.microsoft.com/download/details.aspx?id=54090) |
| Oracle | Database SQL di Azure, Istanza gestita, Macchina virtuale | [Demt](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) / [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Oracle | SQL DW | [Demt](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) / [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Oracle | DATABASE di Azure per PostgreSQL-<br/>Server singolo |  | [Ora2Pg*](http://ora2pg.darold.net/start.html) |  |
| MongoDB | Cosmos DB |  | [Cloudamize*](https://www.cloudamize.com/) | [Cloudamize*](https://www.cloudamize.com/) |
| Cassandra | Cosmos DB |  |  |  |
| MySQL | Database SQL di Azure, Istanza gestita, Macchina virtuale | [Demt](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) / [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[Cloud Atlas *](https://www.unifycloud.com/cloud-migration-tool/) |  |
| MySQL | Database di Azure per MySQL |  |  |  |
| Servizi Desktop remoto MySQL | Database di Azure per MySQL |  |  |  |
| PostgreSQL | DATABASE di Azure per PostgreSQL-<br/>Server singolo |  |  |  |
| Servizi Desktop remoto PostgreSQL | DATABASE di Azure per PostgreSQL-<br/>Server singolo |  |  |  |
| DB2 | Database SQL di Azure, Istanza gestita, Macchina virtuale | [Demt](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) / [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Accesso | Database SQL di Azure, Istanza gestita, Macchina virtuale |  | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Sybase-SAP ASE | Database SQL di Azure, Istanza gestita, Macchina virtuale | [Demt](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) / [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |  |
| Sybase-SAP IQ | Database SQL di Azure, Istanza gestita, Macchina virtuale |  | |  |
| | | | | |

## <a name="migration-phase"></a>Fase di migrazione

| Origine | Obiettivo | SCHEMA | Dati<br/>Offline | Dati<br/>Online |
| --- | --- | --- | --- | --- |
| SQL Server | Azure SQL DB | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Cloudamize*](https://www.cloudamize.com/) | [Servizio Migrazione del database](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [Servizio Migrazione del database](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server | Istanza gestita del database SQL di Azure | [Servizio Migrazione del database](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [Servizio Migrazione del database](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/) | [Servizio Migrazione del database](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server | VM di Azure SQL | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Servizio Migrazione del database](https://azure.microsoft.com/services/database-migration/)<br>[Cloudamize*](https://www.cloudamize.com/) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Servizio Migrazione del database](https://azure.microsoft.com/services/database-migration/)<br>[Cloudamize*](https://www.cloudamize.com/) | [Servizio Migrazione del database](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| SQL Server | SQL DW |  |  |  |
| Servizi Desktop remoto SQL | Database SQL di Azure, Istanza gestita, Macchina virtuale | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | [DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)<br/>[Servizio Migrazione del database](https://azure.microsoft.com/services/database-migration/) | [Servizio Migrazione del database](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | Database SQL di Azure, Istanza gestita, Macchina virtuale | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[SharePlex*](https://www.quest.com/products/shareplex/) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017)<br/>[SharePlex*](https://www.quest.com/products/shareplex/) | [Servizio Migrazione del database](https://azure.microsoft.com/services/database-migration/)<br/>[SharePlex*](https://www.quest.com/products/shareplex/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | SQL DW | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [Servizio Migrazione del database](https://azure.microsoft.com/services/database-migration/)<br/>[SharePlex*](https://www.quest.com/products/shareplex/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Oracle | DATABASE di Azure per PostgreSQL-<br/>Server singolo |  |  | [Servizio Migrazione del database](https://azure.microsoft.com/services/database-migration/) |
| MongoDB | Cosmos DB | [Servizio Migrazione del database](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Imanis Data*](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [Servizio Migrazione del database](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Imanis Data*](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [Servizio Migrazione del database](https://azure.microsoft.com/services/database-migration/)<br/>[Cloudamize*](https://www.cloudamize.com/)<br/>[Imanis Data*](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Cassandra | Cosmos DB | [Imanis Data*](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [Imanis Data*](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) | [Imanis Data*](https://www.imanisdata.com/wp-content/uploads/2018/02/Imanis_DS_MongoDB_Azure_FINAL.pdf) |
| MySQL | Database SQL di Azure, Istanza gestita, Macchina virtuale | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| MySQL | Database di Azure per MySQL | [MySQL dump*](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) |  | [Servizio Migrazione del database](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Servizi Desktop remoto MySQL | Database di Azure per MySQL | [MySQL dump*](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) |  | [Servizio Migrazione del database](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| PostgreSQL | DATABASE di Azure per PostgreSQL-<br/>Server singolo | [PG dump*](https://www.postgresql.org/docs/11/static/app-pgdump.html) |  | [Servizio Migrazione del database](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Servizi Desktop remoto PostgreSQL | DATABASE di Azure per PostgreSQL-<br/>Server singolo | [PG dump*](https://www.postgresql.org/docs/11/static/app-pgdump.html) |  | [Servizio Migrazione del database](https://azure.microsoft.com/services/database-migration/)<br/>[Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| DB2 | Database SQL di Azure, Istanza gestita, Macchina virtuale | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Accesso | Database SQL di Azure, Istanza gestita, Macchina virtuale | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) |
| Sybase-SAP ASE | Database SQL di Azure, Istanza gestita, Macchina virtuale | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [SSMA](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant?view=sql-server-2017) | [Attunity*](https://www.attunity.com/products/replicate/)<br/>[Striim*](https://www.striim.com/partners/striim-for-microsoft-azure/) |
| Sybase-SAP IQ | Database SQL di Azure, Istanza gestita, Macchina virtuale | [Pireo *](http://www.ispirer.com/solutions/migration-to-the-microsoft-technology-stack) | [Pireo *](http://www.ispirer.com/solutions/migration-to-the-microsoft-technology-stack) | |
| | | | | |

## <a name="post-migration-phase"></a>Fase di post-migrazione

| Origine | Obiettivo | Ottimizzare |
| --- | --- | --- |
| SQL Server | Azure SQL DB | [Cloud Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | Istanza gestita del database SQL di Azure | [Cloud Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | VM di Azure SQL | [Cloud Atlas *](https://www.unifycloud.com/cloud-migration-tool/)<br/>[Cloudamize*](https://www.cloudamize.com/) |
| SQL Server | SQL DW |  |
| Servizi Desktop remoto SQL | Database SQL di Azure, Istanza gestita, Macchina virtuale |  |
| Oracle | Database SQL di Azure, Istanza gestita, Macchina virtuale |  |
| Oracle | SQL DW |  |
| Oracle | DATABASE di Azure per PostgreSQL-<br/>Server singolo |  |
| MongoDB | Cosmos DB | [Cloudamize*](https://www.cloudamize.com/) |
| Cassandra | Cosmos DB |  |
| MySQL | Database SQL di Azure, Istanza gestita, Macchina virtuale |  |
| MySQL | Database di Azure per MySQL |  |
| Servizi Desktop remoto MySQL | Database di Azure per MySQL |  |
| PostgreSQL | DATABASE di Azure per PostgreSQL-<br/>Server singolo |  |
| Servizi Desktop remoto PostgreSQL | DATABASE di Azure per PostgreSQL-<br/>Server singolo |  |
| DB2 | Database SQL di Azure, Istanza gestita, Macchina virtuale |  |
| Accesso | Database SQL di Azure, Istanza gestita, Macchina virtuale |  |
| Sybase-SAP ASE | Database SQL di Azure, Istanza gestita, Macchina virtuale |  |
| Sybase-SAP IQ | Database SQL di Azure, Istanza gestita, Macchina virtuale |  |
| | | |

## <a name="next-steps"></a>Passaggi successivi

Per una panoramica del Servizio Migrazione del database di Azure, vedere l’articolo [Definizione del Servizio Migrazione del database di Azure](dms-overview.md).
