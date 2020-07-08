---
title: L'importazione e l'esportazione di un database richiedono molto tempo
description: Il servizio di importazione/esportazione di database SQL di Azure e Azure SQL Istanza gestita richiede molto tempo per l'importazione o l'esportazione di un database
ms.custom: seo-lt-2019, sqldbrb=1
services: sql-database
ms.service: sql-db-mi
ms.subservice: data-movement
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
ms.openlocfilehash: f98cfcd49806061a969a9227f9ade05f70ce79ff
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/06/2020
ms.locfileid: "85982311"
---
# <a name="azure-sql-database-and-managed-instance-importexport-service-takes-a-long-time-to-import-or-export-a-database"></a>Il database SQL di Azure e Istanza gestita il servizio di importazione/esportazione richiede molto tempo per l'importazione o l'esportazione di un database

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Quando si utilizza il servizio importazione/esportazione, il processo potrebbe richiedere più tempo del previsto. Questo articolo descrive le possibili cause di questo ritardo e dei metodi alternativi.

## <a name="azure-sql-database-importexport-service"></a>Servizio di importazione/esportazione del database SQL di Azure

Il servizio di importazione/esportazione del database SQL di Azure è un servizio Web basato su REST eseguito in ogni data center di Azure. Questo servizio viene chiamato quando si utilizza l'opzione [Importa database](database-import.md#using-azure-portal) o [Esporta](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-azure-portal) per spostare il database nel portale di Azure. Il servizio fornisce servizi di accodamento delle richieste e di calcolo gratuiti per eseguire importazioni ed esportazioni tra il database SQL di Azure e l'archiviazione BLOB di Azure.

Le operazioni di importazione ed esportazione non rappresentano un backup fisico tradizionale del database, bensì un backup logico del database che utilizza un formato BACPAC speciale. Il formato BACPAC consente di evitare di dover usare un formato fisico che può variare tra le versioni di Microsoft SQL Server, il database SQL di Azure e Istanza gestita di Azure SQL.

## <a name="what-causes-delays-in-the-process"></a>Cosa causa i ritardi nel processo?

Il servizio di importazione/esportazione del database SQL di Azure fornisce un numero limitato di macchine virtuali (VM) di calcolo per area per elaborare le operazioni di importazione ed esportazione. Le macchine virtuali di calcolo sono ospitate per ogni area per assicurarsi che l'importazione o l'esportazione eviti i ritardi e gli addebiti per la larghezza di banda tra aree. Se un numero eccessivo di richieste viene effettuato contemporaneamente nella stessa area, è possibile che si verifichino ritardi significativi nell'elaborazione delle operazioni. Il tempo necessario per completare le richieste può variare da pochi secondi a diverse ore.

> [!NOTE]
> Se una richiesta non viene elaborata entro quattro giorni, il servizio annulla automaticamente la richiesta.

## <a name="recommended-solutions"></a>Soluzioni consigliate

Se le esportazioni di database vengono usate solo per il ripristino da eliminazioni accidentali dei dati, tutte le edizioni del database SQL di Azure forniscono funzionalità di ripristino self-service da backup generati dal sistema. Tuttavia, se sono necessarie le esportazioni per altri motivi e se sono necessarie prestazioni di importazione/esportazione costantemente più veloci o più prevedibili, prendere in considerazione le opzioni seguenti:

* [Esportare in un file BACPAC tramite l'utilità SqlPackage](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-sqlpackage-utility).
* [Esportare in un file BACPAC usando SQL Server Management Studio (SSMS)](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-sql-server-management-studio-ssms).
* Eseguire l'importazione o l'esportazione BACPAC direttamente nel codice usando l'API Microsoft SQL Server Data-Tier Application Framework (DacFx). Per altre informazioni, vedere:
  * [Esportare un'applicazione livello dati](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
  * [Spazio dei nomi Microsoft. SqlServer. Dac](https://docs.microsoft.com/dotnet/api/microsoft.sqlserver.dac)
  * [Scarica DACFx](https://www.microsoft.com/download/details.aspx?id=55713)

## <a name="things-to-consider-when-you-export-or-import-a-database"></a>Aspetti da considerare quando si esporta o si importa un database

* Tutti i metodi descritti in questo articolo usano la quota di unità di transazione di database (DTU), che causa la limitazione delle richieste da parte del servizio database SQL di Azure. È possibile [visualizzare le statistiche DTU per il database nel portale di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview#sql-database-resource-monitoring). Se il database ha raggiunto i limiti delle risorse, [aggiornare il livello di servizio](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources) per aggiungere altre risorse.
* Idealmente, è consigliabile eseguire applicazioni client, ad esempio l'utilità SqlPackage o l'applicazione DAC personalizzata, da una macchina virtuale nella stessa area del database. In caso contrario, potrebbero verificarsi problemi di prestazioni correlati alla latenza di rete.
* L'esportazione di tabelle di grandi dimensioni senza indici cluster può essere molto lenta o causare un errore. Questo comportamento si verifica perché la tabella non può essere suddivisa ed esportata in parallelo. Al contrario, deve essere esportato in un'unica transazione e questo causa rallentamento delle prestazioni e dei potenziali errori durante l'esportazione, soprattutto per le tabelle di grandi dimensioni.


## <a name="related-documents"></a>Documenti correlati

[Considerazioni sull'esportazione di un database](https://docs.microsoft.com/azure/sql-database/sql-database-export#considerations-when-exporting-an-azure-sql-database)
