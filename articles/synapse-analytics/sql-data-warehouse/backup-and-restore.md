---
title: Backup e ripristino-snapshot, con ridondanza geografica
description: Informazioni sul funzionamento del backup e del ripristino nel pool SQL di Azure sinapsi Analytics. Usare i backup per ripristinare il data warehouse a un punto di ripristino nell'area primaria. Usare i backup con ridondanza geografica per ripristinare in un'area geografica diversa.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/04/2020
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019"
ms.openlocfilehash: 1d82c7c22bb5aeb2740884b0d7ede4a4d8f07f86
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80631206"
---
# <a name="backup-and-restore-in-azure-synapse-sql-pool"></a>Eseguire il backup e il ripristino nel pool SQL sinapsi di Azure

Informazioni su come usare il backup e il ripristino nel pool SQL sinapsi di Azure. Usare i punti di ripristino del pool SQL per ripristinare o copiare il data warehouse in uno stato precedente nell'area primaria. Usare i backup con ridondanza geografica del data warehouse per eseguire il ripristino in un'altra area geografica.

## <a name="what-is-a-data-warehouse-snapshot"></a>Che cos'è uno snapshot di data warehouse

Uno *snapshot del data warehouse* crea un punto di ripristino che è possibile usare per ripristinare o copiare il data warehouse a uno stato precedente.  Poiché il pool SQL è un sistema distribuito, un data warehouse snapshot è costituito da molti file che si trovano in archiviazione di Azure. Gli snapshot acquisiscono le modifiche incrementali dai dati archiviati nel data warehouse.

Un *ripristino del data warehouse* consiste in un nuovo data warehouse che viene creato da un punto di ripristino di un data warehouse esistente o eliminato. Il ripristino del data warehouse è un elemento essenziale di qualsiasi strategia di continuità aziendale e ripristino di emergenza perché ricrea i dati dopo un caso di danneggiamento o eliminazione accidentale. Il data warehouse è anche un meccanismo molto utile per creare copie del data warehouse per scopi di sviluppo o test.  Le frequenze di ripristino del pool SQL possono variare a seconda delle dimensioni del database e della posizione del data warehouse di origine e di destinazione.

## <a name="automatic-restore-points"></a>Punti di ripristino automatici

Gli snapshot sono una funzionalità incorporata del servizio che crea punti di ripristino. Non è necessario abilitare questa funzionalità. Tuttavia, il pool SQL deve essere in uno stato attivo per la creazione del punto di ripristino. Se il pool SQL viene sospeso di frequente, i punti di ripristino automatici potrebbero non essere creati, quindi assicurarsi di creare un punto di ripristino definito dall'utente prima di sospendere il pool SQL. I punti di ripristino automatici attualmente non possono essere eliminati dagli utenti perché il servizio utilizza questi punti di ripristino per mantenere i contratti di servizio per il ripristino.

Gli snapshot del data warehouse vengono eseguiti durante la giornata creando punti di ripristino disponibili per sette giorni. Questo periodo di conservazione non può essere modificato. Il pool SQL supporta un obiettivo del punto di ripristino (RPO) di otto ore. È possibile ripristinare il data warehouse nell'area primaria da uno qualsiasi degli snapshot acquisiti negli ultimi sette giorni.

Per vedere quando è stato avviato l'ultimo snapshot, eseguire questa query nel pool SQL online.

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs
order by run_id desc
;
```

## <a name="user-defined-restore-points"></a>Punti di ripristino definiti dall'utente

Questa funzionalità consente di attivare manualmente gli snapshot per creare punti di ripristino del data warehouse prima e dopo modifiche di grandi dimensioni. Questa funzionalità garantisce che i punti di ripristino siano logicamente coerenti, che offre protezione aggiuntiva dei dati in caso di interruzioni del carico di lavoro o errori utente per tempi di ripristino rapidi. I punti di ripristino definiti dall'utente sono disponibili per sette giorni e vengono eliminati automaticamente per conto dell'utente. Non è possibile modificare il periodo di conservazione dei punti di ripristino definiti dall'utente. Poiché in qualsiasi momento sono garantiti **42 punti di ripristino definiti dall'utente**, è necessario [eliminarli](https://go.microsoft.com/fwlink/?linkid=875299) prima di creare un nuovo punto di ripristino. È possibile attivare gli snapshot per creare punti di ripristino definiti dall'utente tramite [PowerShell](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsont#examples) o il portale di Azure.

> [!NOTE]
> Se occorre creare punti di ripristino che durino più di 7 giorni, votare questa funzionalità [qui](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/35114410-user-defined-retention-periods-for-restore-points). È anche possibile creare un punto di ripristino definito dall'utente ed eseguire il ripristino dal punto di ripristino appena creato in un nuovo data warehouse. Una volta ripristinato, il pool SQL è online ed è possibile sospenderlo per un tempo illimitato per risparmiare sui costi di calcolo. Il database messo in pausa comporta costi di archiviazione alla frequenza dell'archiviazione Premium di Azure. Nel momento in cui occorre una copia attiva del data warehouse ripristinato, la ripresa dell'esecuzione del data warehouse richiede solo alcuni minuti.

### <a name="restore-point-retention"></a>Conservazione del punto di ripristino

Di seguito sono elencati i dettagli relativi ai periodi di conservazione dei punti di ripristino:

1. Il pool SQL Elimina un punto di ripristino quando raggiunge il periodo di conservazione di 7 giorni **e** quando sono presenti almeno 42 punti di ripristino totali, inclusi sia definiti dall'utente che automatici.
2. Gli snapshot non vengono presi quando un pool SQL viene sospeso.
3. Il periodo di tempo di un punto di ripristino viene misurato in base ai giorni di calendario assoluti dal momento in cui viene effettuato il punto di ripristino, incluso quando il pool SQL è sospeso.
4. In qualsiasi momento, è garantito che un pool SQL sia in grado di archiviare fino a 42 punti di ripristino definiti dall'utente e 42 punti di ripristino automatici, purché questi punti di ripristino non raggiungano il periodo di conservazione di 7 giorni
5. Se viene utilizzato uno snapshot, il pool SQL viene quindi sospeso per più di 7 giorni e quindi riprende, il punto di ripristino verrà mantenuto fino a 42 punti di ripristino totali (inclusi sia definiti dall'utente che automatici)

### <a name="snapshot-retention-when-a-sql-pool-is-dropped"></a>Conservazione degli snapshot quando viene eliminato un pool SQL

Quando si rilascia un pool SQL, viene creato e salvato uno snapshot finale per sette giorni. È possibile ripristinare il pool SQL al punto di ripristino finale creato in fase di eliminazione. Se il pool SQL viene eliminato in uno stato di sospensione, non viene eseguita alcuna snapshot. In questo scenario, assicurarsi di creare un punto di ripristino definito dall'utente prima di eliminare il pool SQL.

> [!IMPORTANT]
> Se si elimina un'istanza logica del server SQL, vengono eliminati anche tutti i database appartenenti all'istanza e non sarà possibile recuperarli. Non è possibile ripristinare un server eliminato.

## <a name="geo-backups-and-disaster-recovery"></a>Backup geografico e ripristino di emergenza

Un backup geografico viene creato una volta al giorno per un [Data Center abbinato](../../best-practices-availability-paired-regions.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). L'obiettivo del punto di ripristino per un ripristino geografico è di 24 ore. È possibile ripristinare il backup geografico in un server in qualsiasi altra area in cui è supportato il pool SQL. Un backup geografico assicura la possibilità di ripristinare un data warehouse nel caso in cui non si possa accedere ai punti di ripristino nell'area primaria.

> [!NOTE]
> Se occorre un RPO più breve per i backup geografici, votare questa funzionalità [qui](https://feedback.azure.com/forums/307516-sql-data-warehouse). È anche possibile creare un punto di ripristino definito dall'utente ed eseguire il ripristino dal punto di ripristino appena creato in un nuovo data warehouse in un'area diversa. Dopo avere eseguito il ripristino, il data warehouse diventa disponibile online e può essere messo in pausa per un periodo imprecisato al fine di risparmiare sui costi. Il database messo in pausa comporta costi di archiviazione alla frequenza dell'archiviazione Premium di Azure. occorre una copia attiva del data warehouse, la ripresa dell'esecuzione del data warehouse dovrebbe richiedere solo alcuni minuti.

## <a name="backup-and-restore-costs"></a>Costi di backup e di ripristino

Nella fattura di Azure è inclusa una voce per l'archiviazione e una voce per l'archiviazione con ripristino di emergenza. L'addebito per l'archiviazione è il costo totale per l'archiviazione dei dati nell'area primaria insieme alle modifiche incrementali acquisite dagli snapshot. Per una spiegazione più dettagliata su come vengono addebitati gli snapshot, vedere [Understanding how Snapshots Accrue Charges (Informazioni sui costi aggiuntivi degli snapshot)](/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). L'addebito per la ridondanza geografica copre il costo per l'archiviazione dei backup geografici.  

Il costo totale per il data warehouse primario e i sette giorni di modifiche agli snapshot viene arrotondato al TB più vicino. Se ad esempio il data warehouse è 1,5 TB e gli snapshot usano 100 GB, verranno fatturati 2 TB di dati in base alle tariffe di Archiviazione Premium di Azure.

Se si usa l'archiviazione con ridondanza geografica, sarà addebitato un costo di archiviazione separato. L'archiviazione con ridondanza geografica è fatturata in base alla tariffa Standard per l'Archiviazione con ridondanza geografica e accesso in lettura.

Per altre informazioni sui prezzi di Azure sinapsi, vedere [prezzi di sinapsi di Azure](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/). Non viene addebitato alcun costo per l'uscita dei dati durante il ripristino tra le aree.

## <a name="restoring-from-restore-points"></a>Ripristino dai punti di ripristino

Ogni snapshot crea un punto di ripristino che rappresenta l'ora di inizio dello snapshot. Per ripristinare un data warehouse, scegliere un punto di ripristino ed eseguire un comando di ripristino.  

È possibile mantenere il data warehouse ripristinato e quello corrente, oppure eliminare uno di questi. Se si desidera sostituire il data warehouse corrente con il data warehouse ripristinato, è possibile rinominarlo utilizzando [ALTER database (pool SQL)](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) con l'opzione modifica nome.

Per ripristinare un data warehouse, vedere [ripristinare un pool SQL](sql-data-warehouse-restore-points.md#create-user-defined-restore-points-through-the-azure-portal).

Per ripristinare un data warehouse eliminato o sospeso, è possibile [creare un ticket di supporto](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="cross-subscription-restore"></a>Ripristino tra sottoscrizioni

Se è necessario eseguire il ripristino direttamente tra le sottoscrizioni, votare questa funzionalità [qui](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/36256231-enable-support-for-cross-subscription-restore). Ripristinare un server logico diverso e [' spostare '](/azure/azure-resource-manager/resource-group-move-resources?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) il server tra le sottoscrizioni per eseguire un ripristino tra sottoscrizioni.

## <a name="geo-redundant-restore"></a>Ripristino con ridondanza geografica

È possibile [ripristinare il pool SQL](sql-data-warehouse-restore-from-geo-backup.md#restore-from-an-azure-geographical-region-through-powershell) in qualsiasi area che supporta il pool SQL al livello di prestazioni scelto.

> [!NOTE]
> Per eseguire un ripristino con ridondanza geografica, è necessario non avere rifiutato esplicitamente questa funzionalità.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla pianificazione del ripristino di emergenza, vedere [Panoramica sulla continuità aziendale](../../sql-database/sql-database-business-continuity.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
