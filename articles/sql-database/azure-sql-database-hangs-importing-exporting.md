---
title: Il servizio importazione/esportazione richiede molto tempo
description: Il servizio importazione/esportazione del database SQL di Azure richiede molto tempo per l'importazione o l'esportazione di un database
ms.custom: seo-lt-2019
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
ms.openlocfilehash: 29edd7da42a5eb4586cd52cc0f913a56e199a5ba
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73808272"
---
# <a name="azure-sql-database-importexport-service-takes-a-long-time-to-import-or-export-a-database"></a>Il servizio importazione/esportazione del database SQL di Azure richiede molto tempo per l'importazione o l'esportazione di un database

Quando si usa il servizio di importazione/esportazione del database SQL di Azure, il processo potrebbe richiedere più tempo del previsto. Questo articolo descrive le possibili cause di questo ritardo e dei metodi alternativi.

## <a name="azure-sql-database-importexport-service"></a>Servizio di importazione/esportazione del database SQL di Azure

Il servizio di importazione/esportazione del database SQL di Azure è un servizio Web basato su REST eseguito in ogni data center di Azure. Questo servizio viene chiamato quando si usa l'opzione [Importa database](https://docs.microsoft.com/azure/sql-database/sql-database-import#import-from-a-bacpac-file-in-the-azure-portal) o [Esporta](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-azure-portal) per spostare il database SQL nel portale di Azure. Il servizio fornisce servizi di accodamento delle richieste e di calcolo gratuiti per eseguire importazioni ed esportazioni tra un database SQL di Azure e l'archiviazione BLOB di Azure.

Le operazioni di importazione ed esportazione non rappresentano un backup fisico tradizionale del database, bensì un backup logico del database che utilizza un formato BACPAC speciale. Il formato BACPAC consente di evitare di dover usare un formato fisico che può variare tra le versioni di Microsoft SQL Server e il database SQL di Azure. Pertanto, è possibile utilizzarlo per ripristinare in modo sicuro il database in un database di SQL Server e in un database SQL.

## <a name="what-causes-delays-in-the-process"></a>Cosa causa i ritardi nel processo?

Il servizio di importazione/esportazione del database SQL di Azure fornisce un numero limitato di macchine virtuali (VM) di calcolo per area per elaborare le operazioni di importazione ed esportazione. Le macchine virtuali di calcolo sono ospitate per ogni area per assicurarsi che l'importazione o l'esportazione eviti i ritardi e gli addebiti per la larghezza di banda tra aree. Se un numero eccessivo di richieste viene effettuato contemporaneamente nella stessa area, è possibile che si verifichino ritardi significativi nell'elaborazione delle operazioni. Il tempo necessario per completare le richieste può variare da pochi secondi a diverse ore.

> [!NOTE]
> Se una richiesta non viene elaborata entro quattro giorni, il servizio annulla automaticamente la richiesta.

## <a name="recommended-solutions"></a>Soluzioni consigliate

Se le esportazioni di database vengono usate solo per il ripristino da eliminazioni accidentali dei dati, tutte le edizioni del database SQL di Azure forniscono funzionalità di ripristino self-service da backup generati dal sistema. Tuttavia, se sono necessarie le esportazioni per altri motivi e se sono necessarie prestazioni di importazione/esportazione costantemente più veloci o più prevedibili, prendere in considerazione le opzioni seguenti:

* [Esportare in un file BACPAC tramite l'utilità SqlPackage](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-sqlpackage-utility).
* [Esportare in un file BACPAC usando SQL Server Management Studio (SSMS)](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-sql-server-management-studio-ssms).
* Eseguire l'importazione o l'esportazione BACPAC direttamente nel codice usando l'API Microsoft SQL Server Data-Tier Application Framework (DacFx). Per ulteriori informazioni, vedere:
  * [Esportare un'applicazione livello dati](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
  * [Spazio dei nomi Microsoft. SqlServer. Dac](https://docs.microsoft.com/dotnet/api/microsoft.sqlserver.dac)
  * [Scarica DACFx](https://www.microsoft.com/download/details.aspx?id=55713)

## <a name="things-to-consider-when-you-export-or-import-an-azure-sql-database"></a>Aspetti da considerare quando si esporta o si importa un database SQL di Azure

* Tutti i metodi descritti in questo articolo usano la quota di unità di transazione di database (DTU), che causa la limitazione delle richieste da parte del servizio database SQL di Azure. È possibile [visualizzare le statistiche DTU per il database nel portale di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview#monitor-database-performance). Se il database ha raggiunto i limiti delle risorse, [aggiornare il livello di servizio](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources) per aggiungere altre risorse.
* Idealmente, è consigliabile eseguire applicazioni client, ad esempio l'utilità SqlPackage o l'applicazione DAC personalizzata, da una macchina virtuale nella stessa area del database SQL. In caso contrario, potrebbero verificarsi problemi di prestazioni correlati alla latenza di rete.
* L'esportazione di tabelle di grandi dimensioni senza indici cluster può essere molto lenta o causare un errore. Questo comportamento si verifica perché la tabella non può essere suddivisa ed esportata in parallelo. Al contrario, deve essere esportato in un'unica transazione e questo causa rallentamento delle prestazioni e dei potenziali errori durante l'esportazione, soprattutto per le tabelle di grandi dimensioni.


## <a name="related-documents"></a>Documenti correlati

[Considerazioni sull'esportazione di un database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-export#considerations-when-exporting-an-azure-sql-database)
