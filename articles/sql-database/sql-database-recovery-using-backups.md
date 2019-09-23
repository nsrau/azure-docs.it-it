---
title: Ripristinare un database SQL di Azure da un backup | Microsoft Docs
description: Informazioni sul ripristino temporizzato, che consente di ripristinare un database SQL di Azure a un momento precedente (fino a 35 giorni).
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 08/27/2019
ms.openlocfilehash: ab0a622dcb72072621e6696d423a1d4d2917bedc
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2019
ms.locfileid: "71178403"
---
# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>Ripristinare un database SQL di Azure mediante i backup automatici del database

Per impostazione predefinita, i backup del database SQL vengono archiviati in Archiviazione BLOB con replica geografica (RA-GRS). Per il ripristino di database tramite [backup automatici](sql-database-automated-backups.md) sono disponibili le opzioni seguenti:

- Creare un nuovo database nello stesso server di database SQL ripristinato a un determinato momento nel tempo entro il periodo di conservazione.
- Creare un database nello stesso server di Database SQL ripristinato in base all'ora di eliminazione per un database eliminato.
- Creare un nuovo database su qualsiasi server di database SQL nella stessa area ripristinato in base ai backup più recenti.
- Creare un nuovo database su qualsiasi server di database SQL in qualsiasi altra area ripristinato in base ai backup replicati più recenti.

Se è stata configurata la [conservazione a lungo termine del backup](sql-database-long-term-retention.md), è anche possibile creare un nuovo database da qualsiasi backup di LTR in qualsiasi server di database SQL.

> [!IMPORTANT]
> Non è possibile sovrascrivere un database esistente durante il ripristino.

Quando si usa un livello di servizio Standard o Premium, un database ripristinato comporta un costo di archiviazione aggiuntivo nei casi seguenti:

- Ripristino di P11 – P15 in S4 S12 o P1 – P6 se le dimensioni massime del database sono maggiori di 500 GB.
- Ripristino di P1–P6 in S4-S12 se le dimensioni massime del database sono maggiori di 250 GB.

Il costo aggiuntivo si verifica quando le dimensioni massime del database ripristinato sono maggiori della quantità di spazio di archiviazione inclusa con il livello di servizio e il livello di prestazioni del database di destinazione. Il provisioning di archiviazione aggiuntivo superiore alla quantità inclusa viene addebitato in eccesso. Per i dettagli dei prezzi delle risorse di archiviazione aggiuntive, vedere la [pagina dei prezzi del database SQL](https://azure.microsoft.com/pricing/details/sql-database/). Se la quantità effettiva di spazio usato è inferiore alla quantità di spazio di archiviazione inclusa, è possibile evitare questo costo aggiuntivo impostando le dimensioni massime del database sulla quantità inclusa.

> [!NOTE]
> [I backup di database automatici](sql-database-automated-backups.md) vengono usati quando si crea un [copia del database](sql-database-copy.md).

## <a name="recovery-time"></a>Tempo di ripristino

Il tempo di ripristino di un database tramite i backup automatici del database dipende da numerosi fattori:

- Le dimensioni del database
- Le dimensioni di calcolo del database
- Il numero dei log delle transazioni interessate
- La quantità di attività che deve essere ripetuta per il ripristino al punto di ripristino
- La larghezza di banda di rete se il ripristino avviene in un'area diversa
- Il numero di richieste simultanee di ripristino in corso di elaborazione nell'area di destinazione

Per un database di grandi dimensioni e/o molto attivi, il ripristino potrebbe richiedere diverse ore. Nel caso di un'interruzione prolungata in un'area, è possibile che in altre aree vengano elaborate molte richieste di ripristino geografico. In presenza di numerose richieste, i tempi di recupero dei database in tale area possono aumentare. Per la maggior parte dei database, il ripristino richiede meno di 12 ore.

Per una singola sottoscrizione, esistono limitazioni al numero di richieste di ripristino simultanee.  Queste limitazioni si applicano a qualsiasi combinazione di ripristini temporizzati, ripristini e ripristini geografici dal backup di conservazione a lungo termine:

| | **Numero massimo di richieste simultanee elaborate** | **Numero massimo di richieste simultanee inviate** |
| :--- | --: | --: |
|Database singolo (per sottoscrizione)|10|60|
|Pool elastico (per pool)|4|200|
||||

Attualmente non è disponibile un metodo incorporato per ripristinare l'intero server. Lo script [Database SQL di Azure: Lo script di](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) ripristino completo del server è un esempio di come è possibile eseguire questa attività.

> [!IMPORTANT]
> Per eseguire il ripristino tramite i backup automatici, è necessario essere un membro del ruolo di collaboratore di SQL Server o proprietario della sottoscrizione - vedere [RBAC: ruoli predefiniti](../role-based-access-control/built-in-roles.md). È possibile eseguire il ripristino usando portale di Azure, PowerShell o l'API REST. Non è possibile usare Transact-SQL.

## <a name="point-in-time-restore"></a>Ripristino temporizzato

È possibile ripristinare un database autonomo, in pool o di istanza a un momento precedente usando portale di Azure, [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)o l' [API REST](https://docs.microsoft.com/rest/api/sql/databases). La richiesta può specificare qualsiasi livello di servizio o dimensione di calcolo per il database ripristinato. Assicurarsi di disporre di risorse sufficienti sul server in cui si sta ripristinando il database. Al termine, verrà creato un nuovo database nello stesso server del database originale. Al database ripristinato verranno addebitate le tariffe normali in base al livello di servizio e alle dimensioni di calcolo. Non è previsto alcun addebito fino al completamento del ripristino del database.

In genere si ripristina un database a un punto precedente per scopi di ripristino. È possibile considerare il database ripristinato come una sostituzione per il database originale o usarlo come dati di origine per aggiornare il database originale.

- **Sostituzione del database**

  Se il database ripristinato è destinato alla sostituzione del database originale, è necessario specificare le dimensioni di calcolo e il livello di servizio del database originale. È quindi possibile rinominare il database originale e assegnare al database ripristinato il nome originale usando il comando [ALTER database](/sql/t-sql/statements/alter-database-azure-sql-database) in T-SQL.

- **Ripristino dei dati**

  Se si prevede di recuperare dati dal database ripristinato in caso di errore di un utente o di un'applicazione, è necessario scrivere ed eseguire uno script di ripristino dei dati che estrae i dati dal database ripristinato e si applica al database originale. Anche se il completamento dell'operazione di ripristino può richiedere molto tempo, il database in fase di ripristino è visibile nell'elenco dei database per tutto il processo. Se si elimina il database durante il ripristino, l'operazione di ripristino verrà annullata e non verrà addebitato alcun addebito per il database che non ha completato il ripristino.

Per ripristinare un singolo database di istanza, in pool o a un punto nel tempo utilizzando portale di Azure, aprire la pagina per il database e fare clic su **Ripristina** sulla barra degli strumenti.

![ripristino a un determinato momento nel tempo](./media/sql-database-recovery-using-backups/point-in-time-recovery.png)

> [!IMPORTANT]
> Per ripristinare a livello di codice un database da un backup, vedere [Esecuzione a livello di codice del ripristino tramite backup automatici](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups)

## <a name="deleted-database-restore"></a>Ripristino di un database eliminato

È possibile ripristinare un database eliminato all'ora di eliminazione o a un punto precedente nel tempo nello stesso server di database SQL usando portale di Azure, [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)o [Rest (createMode =](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)Restore). È possibile [ripristinare un database eliminato in istanza gestita tramite PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../recreate-dropped-database-on-azure-sql-managed-instance). 

> [!TIP]
> Per uno script di PowerShell di esempio che illustra come ripristinare un database eliminato, vedere [Ripristinare un database SQL tramite PowerShell](scripts/sql-database-restore-database-powershell.md).
> [!IMPORTANT]
> Se si elimina un'istanza del server di database SQL di Azure, anche tutti i database relativi vengono eliminati e non possono essere recuperati. Non è attualmente disponibile alcun supporto per il ripristino di un server eliminato.

### <a name="deleted-database-restore-using-the-azure-portal"></a>Ripristino di un database eliminato con il portale di Azure

Per ripristinare un database eliminato utilizzando portale di Azure, aprire la pagina per il server e nell'area operazioni fare clic su **database eliminati**.

![deleted-database-restore-1](./media/sql-database-recovery-using-backups/deleted-database-restore-1.png)

![deleted-database-restore-2](./media/sql-database-recovery-using-backups/deleted-database-restore-2.png)

> [!IMPORTANT]
> Per ripristinare a livello di codice un database eliminato, vedere [Esecuzione a livello di codice del ripristino tramite backup automatici](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups)

## <a name="geo-restore"></a>Ripristino geografico

Il ripristino geografico consente di ripristinare un database SQL presente su un server in qualsiasi area di Azure usando gli ultimi backup con replica geografica. Il ripristino geografico USA come origine un backup con replica geografica. Può essere richiesto anche se il database o il Data Center è inaccessibile a causa di un'interruzione del servizio.

Il ripristino geografico è l'opzione di ripristino predefinita quando il database non è disponibile a causa di un evento imprevisto nell'area di hosting. È possibile ripristinare il database in un server in un'altra area. Esiste un ritardo tra il momento in cui un backup viene creato e quando ne viene eseguita la replica geografica in un BLOB di Azure in un'area diversa. Di conseguenza, il database ripristinato può essere fino a un'ora dopo il database originale. La figura seguente mostra il ripristino del database dall'ultimo backup disponibile in un'altra area.

![Ripristino geografico](./media/sql-database-geo-restore/geo-restore-2.png)

### <a name="geo-restore-using-azure-portal"></a>Ripristino geografico con portale di Azure

Il concetto generale per il ripristino geografico di un database da portale di Azure viene eseguito tramite la creazione di un nuovo database istanza singolo o gestito e la selezione di un backup di ripristino geografico disponibile nella schermata di creazione del database. Il database appena creato conterrà i dati di backup con ripristino geografico.

#### <a name="single-azure-sql-database"></a>Singolo database SQL di Azure

Per eseguire il ripristino geografico di un singolo database SQL di Azure da portale di Azure nell'area e nel server di propria scelta, seguire questa procedura:

1. Fare clic su Aggiungi **+ Aggiungi** nel Marketplace e selezionare **Crea database SQL**, compilare le informazioni necessarie nella **scheda nozioni di base**
2. Selezionare la scheda **Impostazioni aggiuntive**
3. In USA dati esistenti fai clic su **backup**
4. Selezionare un backup dall'elenco a discesa dei backup disponibili per il ripristino geografico

![ripristino geografico di un singolo database SQL di Azure](./media/sql-database-recovery-using-backups/geo-restore-azure-sql-database-list-annotated.png)

Completare il processo di creazione di un nuovo database. Una volta creato il singolo database SQL di Azure, esso conterrà il backup del ripristino geografico ripristinato.

#### <a name="managed-instance-database"></a>Database istanza gestita

Per eseguire il ripristino geografico del database dell'istanza gestita da portale di Azure a un'istanza gestita esistente in un'area di propria scelta, selezionare un'istanza gestita in cui si desidera ripristinare un database e attenersi alla procedura seguente:

1. Fare clic su **+ nuovo database**
2. Digitare un nome di database desiderato
3. In USA l'opzione Seleziona dati esistente **backup**
4. Selezionare un backup dall'elenco a discesa dei backup disponibili per il ripristino geografico

![database istanza gestita per il ripristino geografico](./media/sql-database-recovery-using-backups/geo-restore-sql-managed-instance-list-annotated.png)

Completare il processo di creazione di un nuovo database. Una volta creato, il database dell'istanza conterrà il backup del ripristino geografico ripristinato.

### <a name="geo-restore-using-powershell"></a>Ripristino geografico con PowerShell

#### <a name="single-azure-sql-database"></a>Singolo database SQL di Azure

Per uno script di PowerShell che illustra come eseguire il ripristino geografico per un singolo database SQL di Azure, vedere [usare PowerShell per ripristinare un database singolo di Azure SQL a un momento precedente](scripts/sql-database-restore-database-powershell.md).

#### <a name="managed-instance-database"></a>Database istanza gestita

Per uno script di PowerShell che illustra come eseguire il ripristino geografico per un database di istanza gestita, vedere [usare PowerShell per ripristinare un database di istanza gestita in un'altra area](scripts/sql-managed-instance-restore-geo-backup.md)geografica.

### <a name="geo-restore-considerations"></a>Considerazioni sul ripristino geografico

Il ripristino temporizzato in un database di replica geografica secondaria non è attualmente supportato. Il ripristino temporizzato può essere eseguito solo in un database primario. Per informazioni dettagliate sull'uso del ripristino geografico in caso di un'interruzione del servizio, vedere [Ripristinare un database SQL di Azure o eseguire il failover in un database secondario](sql-database-disaster-recovery.md).

> [!IMPORTANT]
> Il ripristino geografico è la soluzione di ripristino di emergenza più semplice disponibile nel database SQL. Si basa su backup con replica geografica creati automaticamente con RPO = 1 ora e il tempo di recupero stimato di un massimo di 12 ore. Non garantisce che l'area di destinazione abbia la capacità di ripristinare i database dopo una Ourage a livello di area, in quanto è probabile che sia presente un forte aumento della domanda. Per applicazioni non aziendali critiche che usano database di dimensioni relativamente ridotte, il ripristino geografico è una soluzione di ripristino di emergenza appropriata. Per le applicazioni aziendali critiche che usano database di grandi dimensioni e che devono garantire la continuità aziendale, è consigliabile usare i [gruppi di failover automatico](sql-database-auto-failover-group.md). Offre un RPO e RTO molto più bassi e la capacità è sempre garantita. Per altre informazioni sulle opzioni di continuità aziendale, vedere [Panoramica sulla continuità aziendale](sql-database-business-continuity.md).

## <a name="programmatically-performing-recovery-using-automated-backups"></a>Esecuzione a livello di codice del ripristino tramite backup automatici

Come descritto in precedenza, oltre a portale di Azure, il ripristino del database può essere eseguito a livello di programmazione tramite Azure PowerShell o l'API REST. Le tabelle seguenti descrivono il set di comandi disponibili.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell è ancora supportato dal database SQL di Azure, ma tutte le attività di sviluppo future sono per il modulo AZ. SQL. Per questi cmdlet, vedere [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nel modulo AZ e nei moduli AzureRm sono sostanzialmente identici.

- Per ripristinare un database autonomo o in pool, vedere [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase).

  | Cmdlet | Descrizione |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Ottiene uno o più database. |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Ottiene un database eliminato che è possibile ripristinare. |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |Ottiene una copia di backup con ridondanza geografica di un database. |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |Ripristina un database SQL. |

  > [!TIP]
  > Per uno script di PowerShell di esempio che illustra come eseguire un ripristino temporizzato di un database, vedere [Ripristinare un database SQL tramite PowerShell](scripts/sql-database-restore-database-powershell.md).

- Per ripristinare un database di Istanza gestita, vedere [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase).

  | Cmdlet | Descrizione |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |Ottiene una o più istanze gestite. |
  | [Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | Ottiene i database dell'istanza. |
  | [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |Ripristina un database dell'istanza. |

### <a name="rest-api"></a>API REST

Per ripristinare un database singolo o in pool con l'API REST:

| API | Descrizione |
| --- | --- |
| [REST (createMode=Recovery)](https://docs.microsoft.com/rest/api/sql/databases) |Ripristina un database |
| [Get Create or Update Database Status](https://docs.microsoft.com/rest/api/sql/operations) |Restituisce lo stato durante un'operazione di ripristino |

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

- Per ripristinare un database singolo o in pool con l'interfaccia della riga di comando di Azure, vedere [Ripristino az sql db](/cli/azure/sql/db#az-sql-db-restore).
- Per ripristinare un'istanza gestita usando l'interfaccia della riga di comando di Azure, vedere [AZ SQL MidB Restore](/cli/azure/sql/midb#az-sql-midb-restore)

## <a name="summary"></a>Riepilogo

I backup automatici proteggono i database da errori dell'utente e delle applicazioni, dall'eliminazione accidentale e da interruzioni prolungate. Questa funzionalità incorporata è disponibile per tutti i livelli di servizio e le dimensioni di calcolo.

## <a name="next-steps"></a>Passaggi successivi

- Per la panoramica e gli scenari della continuità aziendale, vedere [Continuità aziendale del database SQL di Azure](sql-database-business-continuity.md).
- Per informazioni sui backup automatici del database SQL di Azure, vedere [Backup automatici del database SQL](sql-database-automated-backups.md).
- Per altre informazioni sulla conservazione a lungo termine, vedere [Conservazione a lungo termine](sql-database-long-term-retention.md).
- Per altre informazioni su opzioni di ripristino più veloci, vedere [Replica geografica attiva](sql-database-active-geo-replication.md) o [Gruppi di failover automatico](sql-database-auto-failover-group.md).
