---
title: Il servizio importazione/esportazione del database SQL di Azure impiega molto tempo per importare o esportare un database | Microsoft Docs
description: Il servizio importazione/esportazione del database SQL di Azure richiede molto tempo per l'importazione o l'esportazione di un database
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
ms.openlocfilehash: ed5f3d1cd505270eb91c9cfbd6fb5c38b908f33d
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2019
ms.locfileid: "71974461"
---
# <a name="azure-sql-database-importexport-service-takes-a-long-time-to-import-or-export-a-database"></a>Il servizio importazione/esportazione del database SQL di Azure richiede molto tempo per l'importazione o l'esportazione di un database

Quando si usa il servizio di importazione/esportazione del database SQL di Azure, è possibile notare che talvolta il completamento del processo potrebbe richiedere molto tempo. Questo articolo fornisce informazioni aggiuntive sulle possibili cause dei ritardi e dei metodi alternativi che è possibile usare per aggirare questi problemi.

## <a name="azure-sql-database-importexport-service"></a>Servizio di importazione/esportazione del database SQL di Azure

Il servizio di importazione/esportazione del database SQL di Azure è un servizio Web basato su REST eseguito in ogni Microsoft Azure data center. Si tratta del servizio che viene chiamato quando si usano le opzioni [Import database](https://docs.microsoft.com/azure/sql-database/sql-database-import#import-from-a-bacpac-file-in-the-azure-portal) o [Export](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-azure-portal) per spostare il database SQL nel portale di Microsoft Azure. Il servizio fornisce un servizio di accodamento delle richieste gratuito e un servizio di calcolo gratuito per eseguire importazioni ed esportazioni da un database SQL di Microsoft Azure per Microsoft Azure archiviazione BLOB (Binary Large Object).

Le operazioni di importazione ed esportazione non sono un backup fisico tradizionale del database, ma un backup logico del database che utilizza un formato BACPAC speciale. Questo formato logico di BACPAC consente di evitare di dover usare un formato fisico che può variare tra le versioni di SQL Server e il database SQL. Pertanto, è possibile utilizzarlo per ripristinare in modo sicuro il database in un database SQL e in un database SQL Server.

## <a name="what-causes-the-process-to-take-a-long-time"></a>Il processo richiede molto tempo

Il servizio di importazione/esportazione del database SQL di Azure fornisce un numero limitato di macchine virtuali (VM) di calcolo per area per elaborare le operazioni di importazione ed esportazione. La macchina virtuale di calcolo è ospitata per area per assicurarsi che l'importazione o l'esportazione eviti i ritardi e gli addebiti per la larghezza di banda tra aree. Quindi, se vengono eseguite troppe richieste contemporaneamente nella stessa area, si verificano ritardi significativi nell'elaborazione delle operazioni. Il tempo necessario per completare le richieste può variare da pochi secondi a diverse ore.

> [!NOTE]
> Se una richiesta non viene elaborata entro quattro giorni, il servizio annulla automaticamente la richiesta.

## <a name="recommended-solutions"></a>Soluzioni consigliate

Se le esportazioni di database vengono usate solo per il ripristino da eliminazioni accidentali dei dati, tutte le edizioni di database SQL Server di Azure forniscono funzionalità di ripristino self-service da backup generati dal sistema. Tuttavia, se sono necessarie le esportazioni per altri motivi e se sono necessarie prestazioni di importazione o esportazione costantemente più veloci o più prevedibili, è opportuno considerare le opzioni seguenti:

* [Esportare in un file BACPAC tramite l'utilità SqlPackage](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-sqlpackage-utility)
* [Esportare in un file BACPAC usando SQL Server Management Studio (SSMS)](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-sql-server-management-studio-ssms)
* Eseguire l'importazione o l'esportazione BACPAC direttamente nel codice usando l'API Microsoft® SQL Server® Data-Tier Application Framework (DacFx). Per ulteriori informazioni, vedere
  * [Esportare un'applicazione livello dati](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
  * [Spazio dei nomi Microsoft. SqlServer. Dac](https://docs.microsoft.com/dotnet/api/microsoft.sqlserver.dac)
  * [Scarica DACFx](https://www.microsoft.com/download/details.aspx?id=55713)

## <a name="considerations-when-exporting-or-importing-an-azure-sql-database"></a>Considerazioni sull'esportazione o l'importazione di un database SQL di Azure

* Tutti i metodi descritti in questo articolo usano la quota DTU, causando una limitazione del servizio SQLDB di Azure. È possibile [visualizzare le statistiche DTU per il database nel portale di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview#monitor-database-performance). Se il database sta raggiungendo i limiti delle risorse, [aggiornare il livello di servizio](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources) per aggiungere altre risorse.
* Le applicazioni client, ad esempio l'utilità SqlPackage o l'applicazione DAC personalizzata, dovrebbero essere idealmente eseguite da una macchina virtuale (VM) nella stessa area del database SQL, altrimenti potrebbero verificarsi problemi di prestazioni dovuti alla latenza di rete.
* L'esportazione di tabelle di grandi dimensioni senza indici cluster può essere molto lenta o addirittura causare un errore. Ciò è dovuto al fatto che la tabella non può essere suddivisa ed esportata in parallelo e deve essere esportata in una singola transazione, causando la lentezza e potenziali errori durante l'esportazione, soprattutto per le tabelle di grandi dimensioni. 


## <a name="related-documents"></a>Documenti correlati

[Considerazioni sull'esportazione di un database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-export#considerations-when-exporting-an-azure-sql-database)
