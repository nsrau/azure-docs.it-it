---
title: Il servizio di importazione/esportazione richiede molto tempo
description: Il servizio Importazione/Esportazione database SQL di Azure richiede molto tempo per importare o esportare un databaseAzure SQL Database Import/Export service takes a long time to import or export a database
ms.custom: seo-lt-2019
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
manager: dcscontentpm
ms.openlocfilehash: cf2d9b218fe63414af2446b8562d3ba187b2d395
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535766"
---
# <a name="azure-sql-database-importexport-service-takes-a-long-time-to-import-or-export-a-database"></a>Il servizio Importazione/Esportazione database SQL di Azure richiede molto tempo per importare o esportare un databaseAzure SQL Database Import/Export service takes a long time to import or export a database

Quando si usa il servizio Importazione/Esportazione database SQL di Azure, il processo potrebbe richiedere più tempo del previsto. In questo articolo vengono descritte le possibili cause di questo ritardo e i metodi di soluzione alternativa.

## <a name="azure-sql-database-importexport-service"></a>Servizio importazione/esportazione del database SQL di AzureAzure SQL Database Import/Export service

Il servizio Importazione/Esportazione database SQL di Azure è un servizio Web basato su REST che viene eseguito in ogni data center di Azure.The Azure SQL Database Import/Export service is a REST-based web service that runs in every Azure data center. Questo servizio viene chiamato quando si usa l'opzione Importa database o Esporta per spostare il database SQL nel portale di Azure.This service is called when you use either the [Import database](sql-database-import.md#using-azure-portal) or [Export](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-azure-portal) option to move your SQL database in the Azure portal. Il servizio fornisce servizi di accodamento e calcolo delle richieste gratuite per eseguire importazioni ed esportazioni tra un database SQL di Azure e l'archiviazione BLOB di Azure.The service provides free request queuing and compute services to perform imports and exportsbetween an Azure SQL database and Azure Blob storage.

Le operazioni di importazione ed esportazione non rappresentano un backup fisico tradizionale del database, ma un backup logico del database che utilizza uno speciale formato BACPAC. Il formato BACPAC consente di evitare di dover usare un formato fisico che può variare tra le versioni di Microsoft SQL Server e del database SQL di Azure. Pertanto, è possibile utilizzarlo per ripristinare in modo sicuro il database in un database di SQL Server e in un database SQL.

## <a name="what-causes-delays-in-the-process"></a>Quali sono le cause di ritardi nel processo?

Il servizio Importazione/Esportazione database SQL di Azure offre un numero limitato di macchine virtuali di calcolo (VM) per area per elaborare le operazioni di importazione ed esportazione. Le macchine virtuali di calcolo sono ospitate per ogni area per assicurarsi che l'importazione o l'esportazione eviti ritardi e costi della larghezza di banda tra aree. Se vengono effettuate troppe richieste contemporaneamente nella stessa area, possono verificarsi ritardi significativi nell'elaborazione delle operazioni. Il tempo necessario per completare le richieste può variare da pochi secondi a molte ore.

> [!NOTE]
> Se una richiesta non viene elaborata entro quattro giorni, il servizio annulla automaticamente la richiesta.

## <a name="recommended-solutions"></a>Soluzioni consigliate

Se le esportazioni del database vengono usate solo per il ripristino dall'eliminazione accidentale dei dati, tutte le edizioni del database SQL di Azure forniscono funzionalità di ripristino self-service dai backup generati dal sistema. Tuttavia, se queste esportazioni sono necessarie per altri motivi e se sono necessarie prestazioni di importazione/esportazione sempre più veloci o più prevedibili, considerare le opzioni seguenti:

* [Esportare in un file BACPAC utilizzando l'utilità SQLPackage](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-sqlpackage-utility).
* [Esportare in un file BACPAC utilizzando SQL Server Management Studio (SSMS)](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-sql-server-management-studio-ssms).
* Eseguire l'importazione o l'esportazione BACPAC direttamente nel codice utilizzando l'API DacFx (Data-Tier Application Framework) di Microsoft SQL Server. Per altre informazioni, vedere:
  * [Esportare un'applicazione livello datiExport a data-tier application](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
  * [Spazio dei nomi Microsoft.SqlServer.Dac](https://docs.microsoft.com/dotnet/api/microsoft.sqlserver.dac)
  * [Scarica DACFx](https://www.microsoft.com/download/details.aspx?id=55713)

## <a name="things-to-consider-when-you-export-or-import-an-azure-sql-database"></a>Aspetti da considerare quando si esporta o si importa un database SQL di Azure

* Tutti i metodi descritti in questo articolo usano la quota DTU (Database Transaction Unit), che causa la limitazione delle richieste da parte del servizio database SQL di Azure.All the methods discussed in this article use up the Database Transaction Unit (DTU) quota, which causes throttling by the Azure SQL Database service. È possibile [visualizzare le statistiche DTU per il database nel portale](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview#sql-database-resource-monitoring)di Azure. Se il database ha raggiunto i limiti di risorse, [aggiornare il livello](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources) di servizio per aggiungere altre risorse.
* In teoria, è consigliabile eseguire applicazioni client (ad esempio l'utilità sqlpackage o l'applicazione di applicazione livello dati personalizzata) da una macchina virtuale nella stessa area del database SQL. In caso contrario, potrebbero verificarsi problemi di prestazioni correlati alla latenza di rete.
* L'esportazione di tabelle di grandi dimensioni senza indici cluster può essere molto lenta o addirittura causare errori. Questo comportamento si verifica perché la tabella non può essere suddivisa ed esportata in parallelo. Al contrario, deve essere esportato in un'unica transazione e ciò causa un rallentamento delle prestazioni e un potenziale errore durante l'esportazione, in particolare per le tabelle di grandi dimensioni.


## <a name="related-documents"></a>Documenti correlati

[Considerazioni relative all'esportazione di un database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-export#considerations-when-exporting-an-azure-sql-database)
