---
title: Backup e ripristino di Azure SQL Data Warehouse - Snapshot e ridondanza geografica | Microsoft Docs
description: Informazioni su come eseguire il backup e il ripristino in Azure SQL Data Warehouse. Usare i backup del data warehouse per ripristinare il data warehouse a un punto di ripristino nell'area primaria. Usare i backup con ridondanza geografica per ripristinare in un'area geografica diversa.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/30/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 90544e182eb25f53232cee9a4dd0c05bd25508a3
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68988480"
---
# <a name="backup-and-restore-in-azure-sql-data-warehouse"></a>Backup e ripristino in Azure SQL Data Warehouse

Informazioni su come usare il backup e il ripristino in Azure SQL Data Warehouse. Usare i punti di ripristino del data warehouse per ripristinare o copiare il data warehouse in uno stato precedente nell'area primaria. Usare i backup con ridondanza geografica del data warehouse per eseguire il ripristino in un'altra area geografica.

## <a name="what-is-a-data-warehouse-snapshot"></a>Che cos'è uno snapshot di data warehouse

Uno *snapshot del data warehouse* crea un punto di ripristino che è possibile usare per ripristinare o copiare il data warehouse a uno stato precedente.  Poiché SQL Data Warehouse è un sistema distribuito, uno snapshot del data warehouse è costituito da molti file che si trovano in Archiviazione di Azure. Gli snapshot acquisiscono le modifiche incrementali dai dati archiviati nel data warehouse.

Un *ripristino del data warehouse* consiste in un nuovo data warehouse che viene creato da un punto di ripristino di un data warehouse esistente o eliminato. Il ripristino del data warehouse è un elemento essenziale di qualsiasi strategia di continuità aziendale e ripristino di emergenza perché ricrea i dati dopo un caso di danneggiamento o eliminazione accidentale. Il data warehouse è anche un meccanismo molto utile per creare copie del data warehouse per scopi di sviluppo o test.  SQL Data Warehouse le tariffe di ripristino possono variare a seconda delle dimensioni del database e della posizione del data warehouse di origine e di destinazione. In media, nella stessa area, le frequenze di ripristino in genere importano circa 20 minuti. 

## <a name="automatic-restore-points"></a>Punti di ripristino automatici

Gli snapshot sono una funzionalità incorporata del servizio che crea punti di ripristino. Non è necessario abilitare questa funzionalità. I punti di ripristino automatici non possono attualmente essere eliminati dagli utenti nei casi in cui il servizio li usa per mantenere i contratti di servizio per il ripristino.

SQL Data Warehouse acquisisce snapshot del data warehouse nel corso della giornata creando punti di ripristino che restano disponibili per sette giorni. Questo periodo di conservazione non può essere modificato. SQL Data Warehouse supporta un obiettivo del punto di ripristino (RPO) di otto ore. È possibile ripristinare il data warehouse nell'area primaria da uno qualsiasi degli snapshot acquisiti negli ultimi sette giorni.

Per vedere quando è stato eseguito l'ultimo snapshot, eseguire questa query su SQL Data Warehouse online.

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs
order by run_id desc
;
```

## <a name="user-defined-restore-points"></a>Punti di ripristino definiti dall'utente

Questa funzionalità consente di attivare manualmente gli snapshot per creare punti di ripristino del data warehouse prima e dopo modifiche di grandi dimensioni. Questa funzionalità garantisce che i punti di ripristino siano logicamente coerenti, che offre protezione aggiuntiva dei dati in caso di interruzioni del carico di lavoro o errori utente per tempi di ripristino rapidi. I punti di ripristino definiti dall'utente sono disponibili per sette giorni e vengono eliminati automaticamente per conto dell'utente. Non è possibile modificare il periodo di conservazione dei punti di ripristino definiti dall'utente. Poiché in qualsiasi momento sono garantiti **42 punti di ripristino definiti dall'utente**, è necessario [eliminarli](https://go.microsoft.com/fwlink/?linkid=875299) prima di creare un nuovo punto di ripristino. È possibile attivare gli snapshot per creare punti di ripristino definiti dall'utente tramite [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint#examples) o il portale di Azure.

> [!NOTE]
> Se occorre creare punti di ripristino che durino più di 7 giorni, votare questa funzionalità [qui](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/35114410-user-defined-retention-periods-for-restore-points). È anche possibile creare un punto di ripristino definito dall'utente ed eseguire il ripristino dal punto di ripristino appena creato in un nuovo data warehouse. Dopo avere eseguito il ripristino, il data warehouse diventa disponibile online e può essere messo in pausa per un periodo imprecisato al fine di risparmiare sui costi. Il database messo in pausa comporta costi di archiviazione alla frequenza dell'archiviazione Premium di Azure. Nel momento in cui occorre una copia attiva del data warehouse ripristinato, la ripresa dell'esecuzione del data warehouse richiede solo alcuni minuti.

### <a name="restore-point-retention"></a>Conservazione del punto di ripristino

Di seguito sono elencati i dettagli relativi ai periodi di conservazione dei punti di ripristino:

1. SQL Data Warehouse elimina un punto di ripristino quando viene raggiunto il rispettivo limite di 7 giorni per il periodo di conservazione **e** quando sono presenti almeno 42 punti di ripristino totali, inclusi i punti di ripristino definiti dall'utente e automatici
2. Non vengono creati snapshot quando un data warehouse è in pausa
3. La durata di un punto di ripristino viene misurata in base ai giorni di calendario assoluti a partire dal momento della creazione del punto di ripristino, inclusi i periodi in cui il data warehouse è in pausa
4. In qualsiasi momento viene garantito che un data warehouse è in grado di archiviare fino a 42 punti di ripristino definiti dall'utente e 42 punti di ripristino automatici, purché tali punti di ripristino non abbiano raggiunto il limite di 7 giorni per il periodo di conservazione
5. Se viene creato uno snapshot, il data warehouse rimane in pausa per più di 7 giorni e quindi viene riattivato, è possibile che il punto di ripristino venga conservato fino al raggiungimento di 42 punti di ripristino totali, inclusi i punti di ripristino definiti dall'utente e automatici

### <a name="snapshot-retention-when-a-data-warehouse-is-dropped"></a>Conservazione degli snapshot quando un data warehouse viene rilasciato

Quando si elimina un data warehouse, SQL Data Warehouse Crea uno snapshot finale e lo conserva per sette giorni. È possibile ripristinare il data warehouse al punto di ripristino finale creato al momento dell'eliminazione.

> [!IMPORTANT]
> Se si elimina un'istanza logica del server SQL, vengono eliminati anche tutti i database appartenenti all'istanza e non sarà possibile recuperarli. Non è possibile ripristinare un server eliminato.

## <a name="geo-backups-and-disaster-recovery"></a>Backup geografico e ripristino di emergenza

SQL Data Warehouse esegue un backup geografico una volta al giorno su un [data center abbinato](../best-practices-availability-paired-regions.md). L'obiettivo del punto di ripristino per un ripristino geografico è di 24 ore. È possibile ripristinare il backup geografico in un server in qualsiasi altra area in cui sia supportato SQL Data Warehouse. Un backup geografico assicura la possibilità di ripristinare un data warehouse nel caso in cui non si possa accedere ai punti di ripristino nell'area primaria.

I backup geografici sono attivi per impostazione predefinita. Se il data warehouse è di prima generazione, è possibile [scegliere di disattivarli](/powershell/module/az.sql/set-azsqldatabasegeobackuppolicy). Non è possibile scegliere di disattivare i backup geografici di seconda generazione, in quanto la protezione dei dati è una garanzia integrata.

> [!NOTE]
> Se occorre un RPO più breve per i backup geografici, votare questa funzionalità [qui](https://feedback.azure.com/forums/307516-sql-data-warehouse). È anche possibile creare un punto di ripristino definito dall'utente ed eseguire il ripristino dal punto di ripristino appena creato in un nuovo data warehouse in un'area diversa. Dopo avere eseguito il ripristino, il data warehouse diventa disponibile online e può essere messo in pausa per un periodo imprecisato al fine di risparmiare sui costi. Il database messo in pausa comporta costi di archiviazione alla frequenza dell'archiviazione Premium di Azure. occorre una copia attiva del data warehouse, la ripresa dell'esecuzione del data warehouse dovrebbe richiedere solo alcuni minuti.

## <a name="backup-and-restore-costs"></a>Costi di backup e di ripristino

Nella fattura di Azure è inclusa una voce per l'archiviazione e una voce per l'archiviazione con ripristino di emergenza. Il costo dell'archiviazione è il costo totale per l'archiviazione dei dati nell'area primaria insieme alle modifiche incrementali acquisite dagli snapshot. Per una spiegazione più dettagliata su come vengono addebitati gli snapshot, vedere [Understanding how Snapshots Accrue Charges (Informazioni sui costi aggiuntivi degli snapshot)](https://docs.microsoft.com/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges?redirectedfrom=MSDN#snapshot-billing-scenarios). L'addebito per la ridondanza geografica copre il costo per l'archiviazione dei backup geografici.  

Il costo totale per il data warehouse primario e i sette giorni di modifiche agli snapshot viene arrotondato al TB più vicino. Se ad esempio il data warehouse è 1,5 TB e gli snapshot usano 100 GB, verranno fatturati 2 TB di dati in base alle tariffe di Archiviazione Premium di Azure.

Se si usa l'archiviazione con ridondanza geografica, sarà addebitato un costo di archiviazione separato. L'archiviazione con ridondanza geografica è fatturata in base alla tariffa Standard per l'Archiviazione con ridondanza geografica e accesso in lettura.

Per ulteriori informazioni sui prezzi di SQL Data Warehouse, vedere [prezzi SQL Data Warehouse]. Non viene addebitato alcun costo per l'uscita dei dati durante il ripristino tra le aree.

## <a name="restoring-from-restore-points"></a>Ripristino dai punti di ripristino

Ogni snapshot crea un punto di ripristino che rappresenta l'ora di inizio dello snapshot. Per ripristinare un data warehouse, scegliere un punto di ripristino ed eseguire un comando di ripristino.  

È possibile mantenere il data warehouse ripristinato e quello corrente, oppure eliminare uno di questi. Se si desidera sostituire il data warehouse corrente con il data warehouse ripristinato, è possibile rinominarlo usando [ALTER DATABASE (Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse) con l'opzione MODIFY NAME.

Per ripristinare un data warehouse, vedere [Ripristinare Azure SQL Data Warehouse (portale)](sql-data-warehouse-restore-database-portal.md), [Ripristinare Azure SQL Data Warehouse (PowerShell)](sql-data-warehouse-restore-database-powershell.md) o [Ripristinare un'istanza di Azure SQL Data Warehouse con le API REST](sql-data-warehouse-restore-database-rest-api.md).

Per ripristinare un data warehouse eliminato o sospeso, è possibile [creare un ticket di supporto](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="cross-subscription-restore"></a>Ripristino tra sottoscrizioni

Se è necessario eseguire il ripristino direttamente tra le sottoscrizioni, votare questa funzionalità [qui](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/36256231-enable-support-for-cross-subscription-restore). Ripristinare un server logico diverso e [' spostare '](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources) il server tra le sottoscrizioni per eseguire un ripristino tra sottoscrizioni. 

## <a name="geo-redundant-restore"></a>Ripristino con ridondanza geografica

È possibile [ripristinare il data warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-restore-from-geo-backup#restore-from-an-azure-geographical-region-through-powershell) in qualsiasi area che supporta Azure SQL Data Warehouse con il livello di prestazioni scelto.

> [!NOTE]
> Per eseguire un ripristino con ridondanza geografica, è necessario non avere rifiutato esplicitamente questa funzionalità.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla pianificazione del ripristino di emergenza, vedere [Panoramica sulla continuità aziendale](../sql-database/sql-database-business-continuity.md)
