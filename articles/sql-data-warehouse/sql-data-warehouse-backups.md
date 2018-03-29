---
title: Backup di Azure SQL Data Warehouse - Snapshot e ridondanza geografica | Documentazione Microsoft
description: Informazioni sui backup dei database predefiniti di SQL Data Warehouse che consentono di ripristinare SQL Data Warehouse di Azure in un punto di ripristino o in un'area geografica diversa.
services: sql-data-warehouse
documentationcenter: ''
author: barbkess
manager: jhubbard
editor: ''
ms.assetid: b5aff094-05b2-4578-acf3-ec456656febd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.custom: backup-restore
ms.date: 10/23/2017
ms.author: jrj;barbkess
ms.openlocfilehash: 159a1d34caba829750da33dbc4ad403fb21cd147
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="backup-and-restore-in-sql-data-warehouse"></a>Backup e ripristino in SQL Data Warehouse
Questo articolo illustra le specifiche dei backup in SQL Data Warehouse. Usare i backup di data warehouse per ripristinare uno snapshot del database nell'area primaria o per ripristinare un backup geografico nell'area geografica abbinata. 

## <a name="what-is-a-data-warehouse-backup"></a>Cos'è un backup di data warehouse?
Un backup di data warehouse è la copia del database che è possibile usare per ripristinare un data warehouse.  Poiché SQL Data Warehouse è un sistema distribuito, un backup di data warehouse è costituito da molti file che si trovano in Archiviazione di Azure. Un backup di data warehouse include sia snapshot del database locale sia backup geografici di tutti i database e i file associati a un data warehouse. 

## <a name="local-snapshot-backups"></a>Backup degli snapshot locali
SQL Data Warehouse acquisisce snapshot del data warehouse durante l'intera giornata. Gli snapshot sono disponibili per sette giorni. SQL Data Warehouse supporta un obiettivo del punto di ripristino (RPO) di otto ore. È possibile ripristinare il data warehouse nell'area primaria a uno qualsiasi degli snapshot acquisiti negli ultimi sette giorni.

Per vedere quando è stato eseguito l'ultimo snapshot, eseguire questa query su SQL Data Warehouse online. 

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs 
order by run_id desc
;
```

## <a name="geo-backups"></a>Backup geografici
SQL Data Warehouse esegue un backup geografico una volta al giorno su un [data center abbinato](../best-practices-availability-paired-regions.md). L'obiettivo del punto di ripristino per un ripristino geografico è di 24 ore. È possibile ripristinare il backup geografico sul server nell'area geografica abbinata. Il backup geografico assicura la possibilità di ripristinare un data warehouse nel caso in cui non si possa accedere agli snapshot nell'area primaria.

I backup geografici sono attivi per impostazione predefinita. Se il data warehouse è ottimizzato per l'elasticità, è possibile [disattivarli](/powershell/module/azurerm.sql/set-azurermsqldatabasegeobackuppolicy) se si vuole. Non è possibile disattivare esplicitamente i backup geografici con il livello di prestazioni ottimizzato per il calcolo.

## <a name="backup-costs"></a>Costi di backup
Si noterà che la fattura di Azure include una voce per Archiviazione Premium di Azure e una voce per l'archiviazione con ridondanza geografica. Il costo di Archiviazione Premium è il costo totale per l'archiviazione dei dati nell'area primaria, che include gli snapshot.  L'addebito per la ridondanza geografica copre il costo per l'archiviazione dei backup geografici.  

Il costo totale per il data warehouse primario e sette giorni di snapshot BLOB di Azure viene arrotondato al TB più vicino. Ad esempio, se il data warehouse è 1,5 TB e gli snapshot usano 100 GB, verranno fatturati 2 TB di dati in base alle tariffe di archiviazione Premium di Azure. 

> [!NOTE]
> Ogni snapshot è inizialmente vuoto e aumenta di dimensione quando si apportano modifiche al data warehouse primario. Tutti gli snapshot aumentano di dimensione quando il data warehouse cambia. Perciò i costi di archiviazione per gli snapshot aumentano in base alla frequenza di modifica.
> 
> 

Se si usa l'archiviazione con ridondanza geografica, sarà addebitato un costo di archiviazione separato. L'archiviazione con ridondanza geografica è fatturata in base alla tariffa Standard per l'Archiviazione con ridondanza geografica e accesso in lettura.

Per altre informazioni sui prezzi di SQL Data Warehouse, vedere [Prezzi di SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="snapshot-retention-when-a-data-warehouse-is-paused"></a>Conservazione degli snapshot quando un data warehouse viene sospeso
Mentre un data warehouse è in pausa SQL Data Warehouse non crea snapshot e non fa scadere gli snapshot. L'età degli snapshot non cambia mentre il data warehouse è in pausa. La conservazione degli snapshot è basata sul numero di giorni in cui il data warehouse rimane in linea e non sui giorni di calendario.

Ad esempio se uno snapshot viene avviato il 1° ottobre alle 16.00 e il data warehouse viene messo in pausa il 3 ottobre alle 16.00, l'età degli snapshot arriva fino a due giorni. Quando il data warehouse ritorna in linea, lo snapshot avrà due giorni. Se il data warehouse torna in linea il 5 ottobre alle 16.00, in quel momento lo snapshot avrà due giorni e gli rimarranno cinque giorni.

Quando il data warehouse ritorna in linea, SQL Data Warehouse avvia nuovi snapshot e fa scadere gli snapshot quando superano i sette giorni di dati.

## <a name="can-i-restore-a-dropped-data-warehouse"></a>È possibile ripristinare un data warehouse eliminato?
Quando si elimina un data warehouse, SQL Data Warehouse Crea uno snapshot finale e lo conserva per sette giorni. È possibile ripristinare il data warehouse al punto di ripristino finale creato al momento dell'eliminazione. 

> [!IMPORTANT]
> Se si elimina un'istanza logica del server SQL, vengono eliminati anche tutti i database appartenenti all'istanza e non sarà possibile recuperarli. Non è possibile ripristinare un server eliminato.
> 

## <a name="next-steps"></a>Passaggi successivi
Per ripristinare un SQL Data Warehouse, vedere [Ripristinare un SQL Data Warehouse](sql-data-warehouse-restore-database-overview.md).

Per una panoramica sulla continuità aziendale, vedere [Panoramica sulla continuità aziendale](../sql-database/sql-database-business-continuity.md)
