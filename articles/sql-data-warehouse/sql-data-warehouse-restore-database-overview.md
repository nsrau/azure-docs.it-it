---
title: Ripristinare un'istanza di Azure Data Warehouse - Ridondanza geografica e locale | Documentazione Microsoft
description: Panoramica delle opzioni di ripristino del database per ripristinare un database in Azure SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: 3e01c65c-6708-4fd7-82f5-4e1b5f61d304
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: backup-restore
ms.date: 12/06/2017
ms.author: barbkess
ms.openlocfilehash: abf8f0b1005aec71812dc8ebfd12fe65250d7a0e
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="sql-data-warehouse-restore"></a>Ripristino di SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Panoramica][Overview]
> * [Portale][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
> 
> 

SQL Data Warehouse offre ripristini locali e geografici come parte delle sue funzionalità di ripristino di emergenza del data warehouse. È possibile i backup del dati warehouse per ripristinare il data warehouse a un punto di ripristino nell'area primaria o usare i backup con ridondanza geografica per ripristinarlo a un'area geografica diversa. Questo articolo illustra le specifiche del ripristino di un data warehouse.

## <a name="what-is-a-data-warehouse-restore"></a>Cos'è un ripristino del data warehouse?
Un ripristino del data warehouse consiste in un nuovo data warehouse creato da un backup di un data warehouse esistente o eliminato. Il data warehouse ripristinato ricrea il data warehouse di backup in un momento specifico. Poiché SQL Data Warehouse è un sistema distribuito, il ripristino del data warehouse viene creato da molti file di backup archiviati nei blob di Azure. 

Il ripristino del database è un elemento essenziale di qualsiasi strategia di continuità aziendale e di ripristino di emergenza perché ricrea i dati dopo un caso di danneggiamento o eliminazione accidentale.

Per altre informazioni, vedere:

* [Backup di SQL Data Warehouse](sql-data-warehouse-backups.md)
* [Panoramica sulla continuità aziendale](../sql-database/sql-database-business-continuity.md)

## <a name="data-warehouse-restore-points"></a>Punti di ripristino del data warehouse
Come vantaggio dell'uso di Archiviazione Premium di Azure, SQL Data Warehouse usa gli snapshot dei BLOB di Archiviazione di Azure per eseguire il backup del data warehouse primario. Ogni snapshot ha un punto di ripristino che rappresenta l'ora di inizio dello snapshot. Per ripristinare un data warehouse, scegliere un punto di ripristino ed eseguire un comando di ripristino.  

SQL Data Warehouse ripristina sempre il backup in un nuovo data warehouse. È possibile mantenere il data warehouse ripristinato e quello corrente, oppure eliminare uno di questi. Se si desidera sostituire il data warehouse corrente con il data warehouse ripristinato, è possibile rinominarlo.

Se è necessario ripristinare un data warehouse eliminate o messo in pausa, è possibile [creare un ticket di supporto](sql-data-warehouse-get-started-create-support-ticket.md). 

<!-- 
### Can I restore a deleted data warehouse?

Yes, you can restore the last available restore point.

Yes, for the next seven calendar days. When you delete a data warehouse, SQL Data Warehouse actually keeps the data warehouse and its snapshots for seven days just in case you need the data. After seven days, you won't be able to restore to any of the restore points. -->

## <a name="geo-redundant-restore"></a>Ripristino con ridondanza geografica
È possibile ripristinare il data warehouse in qualsiasi area che supporta Azure SQL Data Warehouse con il livello di prestazioni scelto. Si noti che le capacità DWU 9000 e 18000 non sono supportate in tutte le aree durante l'anteprima.

> [!NOTE]
> Per eseguire un ripristino con ridondanza geografica, è necessario non avere rifiutato esplicitamente questa funzionalità.
> 
> 

## <a name="restore-timeline"></a>Sequenza temporale del ripristino
È possibile ripristinare un database a qualsiasi punto di ripristino disponibile degli ultimi sette giorni. Gli snapshot vengono eseguiti ogni quattro-otto ore e sono disponibili per sette giorni. Quando uno snapshot supera i sette giorni di vita, scade e il relativo punto di ripristino non è più disponibile.

## <a name="restore-costs"></a>Costi di ripristino
Il costo di archiviazione per il data warehouse ripristinato viene fatturato alla tariffa di archiviazione Premium di Azure. 

Se si mette in pausa un data warehouse ripristinato, l'archiviazione viene fatturata alla tariffa di archiviazione Premium di Azure. Il vantaggio con la messa in pausa è che le risorse di elaborazione DWU non vengono fatturate.

Per altre informazioni sui prezzi di SQL Data Warehouse, vedere [Prezzi di SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="uses-for-restore"></a>Usi della funzionalità di ripristino
L'uso primario per il ripristino del data warehouse consiste nel recuperare i dati dopo averli perduti o danneggiati accidentalmente.

Inoltre, è possibile usare la funzionalità di ripristino del data warehouse per conservare una copia di backup per più di sette giorni. Dopo aver ripristinato la copia di backup, il data warehouse diventa disponibile online e può essere messo in pausa per un periodo imprecisato al fine di risparmiare sui costi. Il database messo in pausa comporta costi di archiviazione alla frequenza dell'archiviazione Premium di Azure. 

## <a name="related-topics"></a>Argomenti correlati
### <a name="scenarios"></a>Scenari
* Per una panoramica sulla continuità aziendale, vedere [Panoramica sulla continuità aziendale](../sql-database/sql-database-business-continuity.md)

<!-- ### Tasks -->

Per eseguire il ripristino di un data warehouse, eseguire il ripristino con:

* Portale di Azure (vedere [Ripristinare un data warehouse con il portale di Azure](sql-data-warehouse-restore-database-portal.md))
* Cmdlet di PowerShell )vedere [Ripristinare un data warehouse con i cmdlet di PowerShell](sql-data-warehouse-restore-database-powershell.md))
* API REST (vedere [Ripristinare un data warehouse con le API REST](sql-data-warehouse-restore-database-rest-api.md))

<!-- ### Tutorials -->

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->


<!--Other Web references-->
