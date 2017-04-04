---
title: Backup di Azure SQL Data Warehouse - Snapshot e ridondanza geografica | Documentazione Microsoft
description: Informazioni sui backup dei database predefiniti di SQL Data Warehouse che consentono di ripristinare SQL Data Warehouse di Azure in un punto di ripristino o in un&quot;area geografica diversa.
services: sql-data-warehouse
documentationcenter: 
author: lakshmi1812
manager: jhubbard
editor: 
ms.assetid: b5aff094-05b2-4578-acf3-ec456656febd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.custom: backup-restore
ms.date: 10/31/2016
ms.author: lakshmir;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 43ab6a2f71ab51c50847b1ba5249f51c48e03fea
ms.openlocfilehash: 94b92f05af30734de727a12fd99271aa9769723a
ms.lasthandoff: 01/24/2017


---
# <a name="sql-data-warehouse-backups"></a>Backup di SQL Data Warehouse
SQL Data Warehouse offre il backup sia locale che geografico come parte delle sue funzionalità di backup del data warehouse. Queste includono gli snapshot dei BLOB di Archiviazione di Azure e l'archiviazione con ridondanza geografica. È possibile i backup del dati warehouse per ripristinare il data warehouse a un punto di ripristino nell'area primaria o per ripristinarlo a un'area geografica diversa. Questo articolo illustra le specifiche dei backup in SQL Data Warehouse.

## <a name="what-is-a-data-warehouse-backup"></a>Cos'è un backup di data warehouse?
Un backup di data warehouse è costituito dai dati che è possibile usare per ripristinare un data warehouse a un momento specifico.  Poiché SQL Data Warehouse è un sistema distribuito, un backup di data warehouse è costituito da molti file archiviati in BLOB di Azure. 

I backup dei database sono una parte essenziale di qualsiasi strategia di continuità aziendale e ripristino di emergenza, perché proteggono i dati dal danneggiamento o dall'eliminazione accidentale. Per altre informazioni, vedere [Panoramica sulla continuità aziendale](../sql-database/sql-database-business-continuity.md).

## <a name="data-redundancy"></a>Ridondanza dei dati
SQL Data Warehouse protegge i dati archiviandoli in Archiviazione Premium di Azure con ridondanza locale. Questa funzionalità di Archiviazione di Azure archivia più copie sincrone dei dati nel data center locale per garantire una protezione trasparente degli stessi in caso di problemi localizzati. La ridondanza dei dati garantisce che i dati non subiscano l'impatto di problemi dell'infrastruttura, ad esempio errori del disco. La ridondanza dei dati assicura la continuità aziendale con un'infrastruttura a tolleranza di errore e disponibilità elevata.

Per altre informazioni su:

* Archiviazione Premium di Azure, vedere [Introduzione ad Archiviazione Premium di Azure](../storage/storage-premium-storage.md).
* Archiviazione con ridondanza locale, vedere [Replica di Archiviazione di Azure](../storage/storage-redundancy.md#locally-redundant-storage).

## <a name="azure-storage-blob-snapshots"></a>Snapshot dei BLOB di Archiviazione di Azure
Come vantaggio dell'uso di Archiviazione Premium di Azure, SQL Data Warehouse usa gli snapshot dei BLOB di Archiviazione di Azure per eseguire il backup del data warehouse in locale. È possibile ripristinare un data warehouse a un punto di ripristino di uno snapshot. Gli snapshot vengono eseguiti come minimo ogni quattro-otto ore e sono disponibili per sette giorni.  

Per altre informazioni su:

* Snapshot BLOB di Azure, vedere [Creare uno snapshot BLOB](../storage/storage-blob-snapshots.md).

## <a name="geo-redundant-backups"></a>Backup con ridondanza geografica
Ogni 24 ore SQL Data Warehouse archivia il data warehouse completo nella risorsa di archiviazione standard. Il data warehouse completo viene creato in base all'ora dell'ultimo snapshot. La risorsa di archiviazione standard appartiene a un account di archiviazione con ridondanza geografica con accesso in lettura (RA-GRS). La funzionalità Archiviazione con ridondanza geografica e accesso in lettura di Archiviazione di Azure replica i file di backup in un [data center associato](../best-practices-availability-paired-regions.md). La replica geografica assicura la possibilità di ripristinare un data warehouse nel caso in cui non si possa accedere agli snapshot nell'area primaria. 

Questa funzionalità è attivata per impostazione predefinita. Se non si desidera usare backup con ridondanza geografica, è possibile [rifiutare esplicitamente] (https://docs.microsoft.com/powershell/resourcemanager/Azurerm.sql/v2.1.0/Set-AzureRmSqlDatabaseGeoBackupPolicy?redirectedfrom=msdn). 

> [!NOTE]
> In Archiviazione di Azure il termine *replica* fa riferimento alla copia dei file da una località a un'altra. La *replica di database* di SQL fa riferimento a più database secondari sincronizzati con un database primario. 
> 
> 

Per altre informazioni su:

* Archiviazione con ridondanza geografica, vedere [Replica di Archiviazione di Azure](../storage/storage-redundancy.md).
* Archiviazione con ridondanza geografica e accesso in lettura, vedere [Archiviazione con ridondanza geografica e accesso in lettura](../storage/storage-redundancy.md#read-access-geo-redundant-storage).

## <a name="data-warehouse-backup-schedule-and-retention-period"></a>Pianificazione dei backup del data warehouse e periodo di conservazione
SQL Data Warehouse crea snapshot nei data warehouse online ogni quattro-otto ore e mantiene ogni snapshot per sette giorni. È possibile ripristinare il database online a uno dei punti di ripristino degli ultimi sette giorni. 

Per vedere quando è stato eseguito l'ultimo snapshot, eseguire questa query su SQL Data Warehouse online. 

```sql
select top 1 *
from sys.pdw_loader_backup_runs 
order by run_id desc;
```

Se si desidera mantenere uno snapshot per più di sette giorni, è possibile ripristinare un punto di ripristino come nuovo data warehouse. Dopo aver completato il ripristino, SQL Data Warehouse avvia la creazione di snapshot nel nuovo data warehouse. Se non si apportano modifiche al nuovo data warehouse, gli snapshot rimangono vuoti e pertanto il costo dello snapshot è minimo. È anche possibile mettere in pausa il database per impedire a SQL Data Warehouse di creare snapshot.

### <a name="what-happens-to-my-backup-retention-while-my-data-warehouse-is-paused"></a>Cosa accade alla conservazione dei backup mentre il data warehouse è in pausa?
Mentre un data warehouse è in pausa SQL Data Warehouse non crea snapshot e non fa scadere gli snapshot. L'età degli snapshot non cambia mentre il data warehouse è in pausa. La conservazione degli snapshot è basata sul numero di giorni in cui il data warehouse rimane in linea e non sui giorni di calendario.

Ad esempio se uno snapshot viene avviato il 1° ottobre alle 16.00 e il data warehouse viene messo in pausa il 3 ottobre alle 16.00, lo snapshot ha due giorni. In qualsiasi momento il data warehouse ritorni in linea, lo snapshot avrà due giorni. Se il data warehouse torna in linea il 5 ottobre alle 16.00, in quel momento lo snapshot avrà due giorni e gli rimarranno cinque giorni.

Quando il data warehouse ritorna in linea, SQL Data Warehouse avvia nuovi snapshot e fa scadere gli snapshot quando superano i sette giorni di dati.

### <a name="how-long-is-the-retention-period-for-a-dropped-data-warehouse"></a>Quanto dura il periodo di conservazione di un data warehouse rimosso?
Quando un data warehouse viene eliminato, il data warehouse e gli snapshot vengono conservati per sette giorni e poi vengono rimossi. È possibile ripristinare il data warehouse a qualsiasi punto di ripristino salvato.

> [!IMPORTANT]
> Se si elimina un'istanza logica del server SQL, vengono eliminati anche tutti i database appartenenti all'istanza e non sarà possibile recuperarli. Non è possibile ripristinare un server eliminato.
> 
> 

## <a name="data-warehouse-backup-costs"></a>Costi di backup del data warehouse
Il costo totale per il data warehouse primario e sette giorni di snapshot BLOB di Azure viene arrotondato al TB più vicino. Ad esempio, se il data warehouse è 1,5 TB e gli snapshot usano 100 GB, verranno fatturati 2 TB di dati in base alle tariffe di archiviazione Premium di Azure. 

> [!NOTE]
> Ogni snapshot è inizialmente vuoto e aumenta di dimensione quando si apportano modifiche al data warehouse primario. Tutti gli snapshot aumentano di dimensione quando il data warehouse cambia. Perciò i costi di archiviazione per gli snapshot aumentano in base alla frequenza di modifica.
> 
> 

Se si usa l'archiviazione con ridondanza geografica, sarà addebitato un costo di archiviazione separato. L'archiviazione con ridondanza geografica è fatturata in base alla tariffa Standard per l'Archiviazione con ridondanza geografica e accesso in lettura.

Per altre informazioni sui prezzi di SQL Data Warehouse, vedere [Prezzi di SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="using-database-backups"></a>Uso dei backup di database
Il principale utilizzo per i backup di SQL Data Warehouse è il ripristino del data warehouse a uno dei punti di ripristino entro il periodo di conservazione.  

* Per ripristinare un SQL Data Warehouse, vedere [Ripristinare un SQL Data Warehouse](sql-data-warehouse-restore-database-overview.md).

## <a name="related-topics"></a>Argomenti correlati
### <a name="scenarios"></a>Scenari
* Per una panoramica sulla continuità aziendale, vedere [Panoramica sulla continuità aziendale](../sql-database/sql-database-business-continuity.md)

<!-- ### Tasks -->

* Per ripristinare un data warehouse, vedere [Ripristinare un SQL Data Warehouse](sql-data-warehouse-restore-database-overview.md)

<!-- ### Tutorials -->


