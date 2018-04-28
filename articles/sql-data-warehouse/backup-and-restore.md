---
title: Backup e ripristino di Azure SQL Data Warehouse - Snapshot e ridondanza geografica | Microsoft Docs
description: Informazioni su come eseguire il backup e il ripristino in Azure SQL Data Warehouse. Usare i backup del data warehouse per ripristinare il data warehouse a un punto di ripristino nell'area primaria. Usare i backup con ridondanza geografica per ripristinare in un'area geografica diversa.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 535c16da137b114704aa9a2e97576ced5e9eba44
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2018
---
# <a name="backup-and-restore-in-azure-sql-data-warehouse"></a>Backup e ripristino in Azure SQL Data Warehouse
Informazioni su come eseguire il backup e il ripristino in Azure SQL Data Warehouse. Usare i backup del data warehouse per ripristinare il data warehouse a un punto di ripristino nell'area primaria. Usare i backup con ridondanza geografica per ripristinare in un'area geografica diversa. 

## <a name="what-is-backup-and-restore"></a>Definizione di backup e ripristino
Un *backup del data warehouse* è la copia del database che è possibile usare per ripristinare un data warehouse.  Poiché SQL Data Warehouse è un sistema distribuito, un backup di data warehouse è costituito da molti file che si trovano in Archiviazione di Azure. Un backup di data warehouse include sia snapshot del database locale sia backup geografici di tutti i database e i file associati a un data warehouse. 

Un *ripristino del data warehouse* consiste in un nuovo data warehouse creato da un backup di un data warehouse esistente o eliminato. Il data warehouse ripristinato ricrea il data warehouse di backup in un momento specifico. Il ripristino del data warehouse è un elemento essenziale di qualsiasi strategia di continuità aziendale e ripristino di emergenza perché ricrea i dati dopo un caso di danneggiamento o eliminazione accidentale.

Le funzionalità di ripristino di emergenza di SQL Data Warehouse prevedono sia ripristini locali che geografici. 

## <a name="local-snapshot-backups"></a>Backup degli snapshot locali
I backup di snapshot locali sono una funzionalità incorporata del servizio,  non è necessario abilitarli. 

SQL Data Warehouse acquisisce snapshot del data warehouse durante l'intera giornata. Gli snapshot sono disponibili per sette giorni. SQL Data Warehouse supporta un obiettivo del punto di ripristino (RPO) di otto ore. È possibile ripristinare il data warehouse nell'area primaria a uno qualsiasi degli snapshot acquisiti negli ultimi sette giorni.

Per vedere quando è stato eseguito l'ultimo snapshot, eseguire questa query su SQL Data Warehouse online. 

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs 
order by run_id desc
;
```

### <a name="snapshot-retention-when-a-data-warehouse-is-paused"></a>Conservazione degli snapshot quando un data warehouse viene sospeso
Mentre un data warehouse è in pausa SQL Data Warehouse non crea snapshot e non fa scadere gli snapshot. L'età degli snapshot non cambia mentre il data warehouse è in pausa. La conservazione degli snapshot è basata sul numero di giorni in cui il data warehouse rimane in linea e non sui giorni di calendario.

Ad esempio se uno snapshot viene avviato il 1° ottobre alle 16.00 e il data warehouse viene messo in pausa il 3 ottobre alle 16.00, l'età degli snapshot arriva fino a due giorni. Quando il data warehouse ritorna in linea, lo snapshot avrà due giorni. Se il data warehouse torna in linea il 5 ottobre alle 16.00, in quel momento lo snapshot avrà due giorni e gli rimarranno cinque giorni.

Quando il data warehouse ritorna in linea, SQL Data Warehouse avvia nuovi snapshot e fa scadere gli snapshot quando superano i sette giorni di dati.

### <a name="snapshot-retention-when-a-data-warehouse-is-dropped"></a>Conservazione degli snapshot quando un data warehouse viene rilasciato
Quando si elimina un data warehouse, SQL Data Warehouse Crea uno snapshot finale e lo conserva per sette giorni. È possibile ripristinare il data warehouse al punto di ripristino finale creato al momento dell'eliminazione. 

> [!IMPORTANT]
> Se si elimina un'istanza logica del server SQL, vengono eliminati anche tutti i database appartenenti all'istanza e non sarà possibile recuperarli. Non è possibile ripristinare un server eliminato.
> 

## <a name="geo-backups"></a>Backup geografici
SQL Data Warehouse esegue un backup geografico una volta al giorno su un [data center abbinato](../best-practices-availability-paired-regions.md). L'obiettivo del punto di ripristino per un ripristino geografico è di 24 ore. È possibile ripristinare il backup geografico sul server nell'area geografica abbinata. Un backup geografico assicura la possibilità di ripristinare un data warehouse nel caso in cui non si possa accedere agli snapshot nell'area primaria.

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

## <a name="restoring-from-restore-points"></a>Ripristino dai punti di ripristino
Ogni snapshot ha un punto di ripristino che rappresenta l'ora di inizio dello snapshot. Per ripristinare un data warehouse, scegliere un punto di ripristino ed eseguire un comando di ripristino.  

SQL Data Warehouse ripristina sempre il backup in un nuovo data warehouse. È possibile mantenere il data warehouse ripristinato e quello corrente, oppure eliminare uno di questi. Se si desidera sostituire il data warehouse corrente con il data warehouse ripristinato, è possibile rinominarlo usando [ALTER DATABASE (Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse) con l'opzione MODIFY NAME. 

Per ripristinare un data warehouse, vedere [Ripristinare un data warehouse tramite il portale di Azure](sql-data-warehouse-restore-database-portal.md), [Ripristinare un data warehouse tramite PowerShell](sql-data-warehouse-restore-database-powershell.md) o [Ripristinare un data warehouse tramite T-SQL](sql-data-warehouse-restore-database-rest-api.md).

Per ripristinare un data warehouse eliminato o sospeso, è possibile [creare un ticket di supporto](sql-data-warehouse-get-started-create-support-ticket.md). 


## <a name="geo-redundant-restore"></a>Ripristino con ridondanza geografica
È possibile ripristinare il data warehouse in qualsiasi area che supporta Azure SQL Data Warehouse con il livello di prestazioni scelto. 

> [!NOTE]
> Per eseguire un ripristino con ridondanza geografica, è necessario non avere rifiutato esplicitamente questa funzionalità.
> 
> 

## <a name="restore-timeline"></a>Sequenza temporale del ripristino
È possibile ripristinare un database a qualsiasi punto di ripristino disponibile degli ultimi sette giorni. Gli snapshot vengono eseguiti ogni quattro-otto ore e sono disponibili per sette giorni. Quando uno snapshot supera i sette giorni di vita, scade e il relativo punto di ripristino non è più disponibile. 

I backup non vengono eseguiti per un data warehouse sospeso. Se il data warehouse viene sospeso per più di sette giorni, non si avranno punti di ripristino. 

## <a name="restore-costs"></a>Costi di ripristino
Il costo di archiviazione per il data warehouse ripristinato viene fatturato alla tariffa di archiviazione Premium di Azure. 

Se si mette in pausa un data warehouse ripristinato, l'archiviazione viene fatturata alla tariffa di archiviazione Premium di Azure. Il vantaggio della sospensione è che le risorse di calcolo non vengono fatturate.

Per altre informazioni sui prezzi di SQL Data Warehouse, vedere [Prezzi di SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="restore-use-cases"></a>Casi d'uso del ripristino
L'uso primario per il ripristino del data warehouse consiste nel recuperare i dati dopo averli perduti o danneggiati accidentalmente. Inoltre, è possibile usare la funzionalità di ripristino del data warehouse per conservare una copia di backup per più di sette giorni. Dopo aver ripristinato la copia di backup, il data warehouse diventa disponibile online e può essere messo in pausa per un periodo imprecisato al fine di risparmiare sui costi. Il database messo in pausa comporta costi di archiviazione alla frequenza dell'archiviazione Premium di Azure. 

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla pianificazione del ripristino di emergenza, vedere [Panoramica sulla continuità aziendale](../sql-database/sql-database-business-continuity.md)
