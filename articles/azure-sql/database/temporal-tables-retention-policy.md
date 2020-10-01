---
title: Gestire i dati cronologici nelle tabelle temporali
description: Informazioni su come usare criteri di conservazione temporale per tenere sotto controllo i dati cronologici.
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: bonova
ms.author: bonova
ms.reviewer: sstein
ms.date: 09/25/2018
ms.openlocfilehash: 1d68163a9fba3ba3bcd4c0c0f3fb5f442296e781
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2020
ms.locfileid: "91619390"
---
# <a name="manage-historical-data-in-temporal-tables-with-retention-policy"></a>Gestire i dati cronologici nelle tabelle temporali con criteri di conservazione
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Le tabelle temporali possono aumentare le dimensioni del database più delle tabelle normali, soprattutto se si conservano i dati cronologici per un periodo di tempo più lungo. Di conseguenza, i criteri di conservazione per i dati cronologici sono un aspetto importante della pianificazione e della gestione del ciclo di vita di ogni tabella temporale. Le tabelle temporali nel database SQL di Azure e Istanza gestita SQL di Azure sono dotate di un meccanismo di conservazione di facile utilizzo che consente di eseguire questa attività.

La conservazione della cronologia temporale può essere configurata a livello di singola tabella. Ciò consente agli utenti di creare criteri di aging flessibili. L'applicazione della conservazione della cronologia temporale è semplice e richiede l'impostazione di un solo parametro durante la creazione della tabella o la modifica dello schema.

Dopo aver definito i criteri di conservazione, il database SQL di Azure e il Istanza gestita SQL di Azure iniziano a controllare regolarmente se sono presenti righe cronologiche idonee per la pulizia automatica dei dati. L'identificazione di righe corrispondenti e la loro rimozione dalla tabella di cronologia si verificano in modo trasparente nell'attività in background pianificata ed eseguita dal sistema. La condizione cronologica delle righe della tabella viene verificata in base alla colonna che rappresenta la fine del periodo SYSTEM_TIME. Se ad esempio il periodo di conservazione impostato è pari a sei mesi, le righe di tabella idonee per la rimozione soddisfano la condizione seguente:

```sql
ValidTo < DATEADD (MONTH, -6, SYSUTCDATETIME())
```

Nell'esempio precedente si presuppone che la colonna **ValidTo** corrisponda alla fine del periodo di SYSTEM_TIME.

## <a name="how-to-configure-retention-policy"></a>Come si configurano i criteri di conservazione?

Prima di configurare i criteri di conservazione per una tabella temporale, verificare prima di tutto se la conservazione cronologica temporale è abilitata *a livello di database*.

```sql
SELECT is_temporal_history_retention_enabled, name
FROM sys.databases
```

Il flag del database **is_temporal_history_retention_enabled** è ON per impostazione predefinita, ma gli utenti possono modificarlo con l'istruzione ALTER DATABASE. Viene inoltre impostata automaticamente su OFF dopo l'operazione di [ripristino temporizzato](recovery-using-backups.md) . Per impostare la pulizia della cronologia temporale per il database eseguire l'istruzione seguente:

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
```

> [!IMPORTANT]
> È possibile configurare la conservazione per le tabelle temporali anche se **is_temporal_history_retention_enabled** è impostato su OFF. Tuttavia in questo caso la pulizia automatica delle righe obsolete non verrà attivata.

I criteri di conservazione vengono configurati durante la creazione della tabella specificando un valore per il parametro HISTORY_RETENTION_PERIOD:

```sql
CREATE TABLE dbo.WebsiteUserInfo
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH
 (
     SYSTEM_VERSIONING = ON
     (
        HISTORY_TABLE = dbo.WebsiteUserInfoHistory,
        HISTORY_RETENTION_PERIOD = 6 MONTHS
     )
 );
```

Il database SQL di Azure e Istanza gestita SQL di Azure consentono di specificare il periodo di conservazione usando unità di tempo diverse: giorni, settimane, mesi e anni. Se HISTORY_RETENTION_PERIOD viene omesso, viene usata la conservazione INFINITE. È anche possibile usare esplicitamente la parola chiave INFINITE.

In alcuni scenari risulta utile configurare la conservazione dopo la creazione della tabella o per modificare un valore configurato in precedenza. In questi casi usare l'istruzione ALTER TABLE:

```sql
ALTER TABLE dbo.WebsiteUserInfo
SET (SYSTEM_VERSIONING = ON (HISTORY_RETENTION_PERIOD = 9 MONTHS));
```

> [!IMPORTANT]
> Con l'impostazione di SYSTEM_VERSIONING su OFF il valore relativo al periodo di conservazione *non viene mantenuto*. Con l'impostazione di SYSTEM_VERSIONING su ON senza specificare esplicitamente HISTORY_RETENTION_PERIOD si ottiene come risultato un periodo di conservazione INFINITE.

Per esaminare lo stato corrente del criterio di conservazione usare la seguente query, che unisce il flag di abilitazione della conservazione temporale a livello di database con i periodi di conservazione per le singole tabelle:

```sql
SELECT DB.is_temporal_history_retention_enabled,
SCHEMA_NAME(T1.schema_id) AS TemporalTableSchema,
T1.name as TemporalTableName,  SCHEMA_NAME(T2.schema_id) AS HistoryTableSchema,
T2.name as HistoryTableName,T1.history_retention_period,
T1.history_retention_period_unit_desc
FROM sys.tables T1  
OUTER APPLY (select is_temporal_history_retention_enabled from sys.databases
where name = DB_NAME()) AS DB
LEFT JOIN sys.tables T2
ON T1.history_table_id = T2.object_id WHERE T1.temporal_type = 2
```

## <a name="how-ages-rows-are-deleted"></a>Frequenza di eliminazione delle righe

Il processo di pulizia dipende dal layout dell'indice della tabella di cronologia. È importante notare che *solo nelle tabelle di cronologia con un indice cluster (albero B o columnstore) è possibile configurare criteri di conservazione finiti*. Viene creata un'attività in background per eseguire la pulizia dei dati obsoleti per tutte le tabelle temporali con periodo di conservazione finito.
La logica di pulizia per l'indice in cluster rowstore (B-tree) elimina la riga obsoleta in blocchi più piccoli (fino a 10.000) riducendo al minimo la pressione sul log del database e sul sottosistema I/O. Anche se la logica di pulizia usa l'indice albero B richiesto, l'ordine di eliminazione delle righe con durata superiore al periodo di conservazione non può essere garantito con certezza. Di conseguenza *evitare qualsiasi dipendenza dall'ordine di pulizia nelle applicazioni*.

L'attività di pulizia per il columnstore cluster rimuove interi [gruppi di righe](/sql/relational-databases/indexes/columnstore-indexes-overview) in una sola volta (in genere contiene 1 milione di righe ognuna), che è molto efficiente, soprattutto quando vengono generati dati cronologici a velocità elevata.

![Conservazione columnstore cluster](./media/temporal-tables-retention-policy/cciretention.png)

Un'ottima compressione dei dati e una pulizia efficiente dei dati conservati fanno dell'indice columnstore cluster la soluzione ottimale per gli scenari in cui il carico di lavoro genera rapidamente volumi elevati di dati cronologici. Questo modello è tipico per i [carichi di lavoro di elaborazione transazionale intensivi che usano le tabelle temporali](/sql/relational-databases/tables/temporal-table-usage-scenarios) per il rilevamento delle modifiche e il controllo, l'analisi delle tendenze o l'inserimento di dati.

## <a name="index-considerations"></a>Considerazioni sull'indice

L'attività di pulizia per le tabelle con indice rowstore in cluster richiede che l'indice inizi con la colonna che corrisponde alla fine del periodo SYSTEM_TIME. Se questa colonna non esiste, non è possibile configurare un periodo di conservazione finito:

*Il messaggio 13765, livello 16, stato 1 <br> </br> impostazione del periodo di conservazione finito sulla tabella temporale con controllo delle versioni di sistema ' temporalstagetestdb. dbo. WebsiteUserInfo ' non è riuscito perché la tabella di cronologia ' temporalstagetestdb. dbo. WebsiteUserInfoHistory ' non contiene l'indice cluster richiesto. Si consiglia di creare un indice columnstore cluster o albero B a partire dalla colonna che corrisponde alla fine del periodo di SYSTEM_TIME nella tabella di cronologia.*

È importante notare che la tabella di cronologia predefinita creata dal database SQL di Azure e da Azure SQL Istanza gestita dispone già di un indice cluster, che è conforme ai criteri di conservazione. Se si tenta di rimuovere l'indice in una tabella con periodo di conservazione definito, l'operazione ha esito negativo con l'errore seguente:

*Il messaggio 13766, livello 16, stato 1 <br> </br> non è in grado di eliminare l'indice cluster ' WebsiteUserInfoHistory. IX_WebsiteUserInfoHistory ' perché è usato per la pulizia automatica dei dati obsoleti. Se è necessario eliminare l'indice, provare a impostare HISTORY_RETENTION_PERIOD su infinito nella tabella temporale con controllo delle versioni di sistema corrispondente.*

La pulizia dell'indice columnstore in cluster funziona in modo ottimale se vengono inserite righe cronologiche in ordine crescente (ordinate in base alla fine della colonna del periodo); questo viene sempre applicato quando la tabella di cronologia viene popolata esclusivamente dal meccanismo SYSTEM_VERSIONIOING. Se le righe della tabella di cronologia non sono ordinate in base alla fine della colonna del periodo (ad esempio in caso di migrazione dei dati cronologici esistenti), è necessario ricreare un indice columnstore in cluster sull'indice rowstore B-tree ordinato in modo corretto, per ottenere prestazioni ottimali.

Evitare la ricompilazione dell'indice columnstore in cluster nella tabella di cronologia con il periodo di conservazione definito, perché può cambiare l'ordine nei gruppi di righe imposti naturalmente dall'operazione di controllo delle versioni di sistema. Se è necessario ricompilare l'indice columnstore in cluster della tabella di cronologia, crearne uno nuovo al posto dell'indice B-tree conforme, mantenendo l'ordinamento in gruppi di righe necessario per la pulizia dei dati regolare. Lo stesso approccio deve essere adottato se si crea una tabella temporale con la tabella di cronologia esistente che dispone di un indice di colonna in cluster senza ordine dei dati garantito:

```sql
/*Create B-tree ordered by the end of period column*/
CREATE CLUSTERED INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory (ValidTo)
WITH (DROP_EXISTING = ON);
GO
/*Re-create clustered columnstore index*/
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
```

Quando viene configurato il periodo di conservazione definito per la tabella di cronologia con l'indice columnstore in cluster, non è possibile creare indici B-tree non in cluster aggiuntivi nella tabella:

```sql
CREATE NONCLUSTERED INDEX IX_WebHistNCI ON WebsiteUserInfoHistory ([UserName])
```

Un tentativo di eseguire l'istruzione sopra indicata avrà esito negativo con l'errore seguente:

*Il messaggio 13772, livello 16, stato 1 <br> </br> non è in grado di creare un indice non cluster in una tabella di cronologia temporale ' WebsiteUserInfoHistory ' perché contiene un periodo di conservazione finito e un indice columnstore cluster definito.*

## <a name="querying-tables-with-retention-policy"></a>Esecuzione di query su tabelle con criteri di conservazione

Tutte le query sulla tabella temporale consentono di escludere automaticamente tramite filtro le righe cronolgiche definite corrispondenti, per evitare risultati imprevisti e incoerenti, poiché è possibile eliminare righe obsolete tramite l'attività di pulizia, *in qualsiasi momento e in ordine arbitrario*.

Nell'immagine seguente viene illustrato il piano di query per una query semplice:

```sql
SELECT * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME ALL;
```

Il piano di query include un altro filtro applicato alla fine della colonna del periodo (ValidTo) nell'operatore Clustered Index Scan nella tabella di cronologia (evidenziata). Questo esempio presuppone che il periodo di conservazione di un MONTH sia stato impostato nella tabella WebsiteUserInfo.

![Filtro di query di conservazione](./media/temporal-tables-retention-policy/queryexecplanwithretention.png)

Tuttavia, se si esegue una query direttamente in una tabella di cronologia, è possibile vedere le righe precedenti al periodo di conservazione specificato, ma senza alcuna garanzia relativa ai risultati ripetibili. Nell'immagine seguente è mostrato il piano di esecuzione di query per la query nella tabella di cronologia senza altri filtri applicati:

![Esecuzione di query senza filtro di conservazione](./media/temporal-tables-retention-policy/queryexecplanhistorytable.png)

Non affidare la logica di business alla lettura della tabella di cronologia oltre il periodo di conservazione, perché si potrebbero ottenere risultati incoerenti o imprevisti. È consigliabile usare query temporali con la clausola FOR SYSTEM_TIME per l'analisi dei dati nelle tabelle temporali.

## <a name="point-in-time-restore-considerations"></a>Considerazioni sul ripristino temporizzato

Quando si crea un nuovo database attraverso il [ripristino di un database esistente in un punto specifico nel tempo](recovery-using-backups.md), la conservazione temporale è disabilitata a livello di database. (Il flag **is_temporal_history_retention_enabled** è impostato su OFF). Questa funzionalità consente di esaminare tutte le righe di cronologia al momento del ripristino, senza doversi preoccupare che le righe obsolete vengano rimosse prima di procedere con l'esecuzione di query. È possibile usarla per *esaminare i dati cronologici oltre il periodo di conservazione configurato*.

Si supponga che per una tabella temporale sia specificato il periodo di conservazione MONTH. Se il database è stato creato al livello di servizio Premium, è possibile creare una copia del database con lo stato del database risalendo fino a 35 giorni prima. Una tale efficacia consentirebbe di analizzare le righe cronologiche risalenti anche a 65 giorni prima eseguendo una query direttamente nella tabella di cronologia.

Se si desidera attivare la pulizia della conservazione temporale, eseguire l'istruzione Transact-SQL seguente dopo il ripristino temporizzato:

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come usare le tabelle temporali nelle applicazioni, vedere [Introduzione con le tabelle temporali](../temporal-tables.md).

Visitare Channel 9 per ascoltare una [storia di successo dell'implementazione temporale del cliente](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) e guardare una [dimostrazione temporale in tempo reale](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

Per informazioni dettagliate sulle tabelle temporali, vedere [tabelle temporali](/sql/relational-databases/tables/temporal-tables).
