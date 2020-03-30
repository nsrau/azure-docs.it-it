---
title: Ripristinare un database da un backup
description: Informazioni sul ripristino temporizzato, che consente di eseguire il rollback di un database SQL di Azure fino a 35 giorni.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
ms.date: 09/26/2019
ms.openlocfilehash: b98331a9cdb359aeefac5db1546f3a15b54010ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268743"
---
# <a name="recover-an-azure-sql-database-by-using-automated-database-backups"></a>Ripristinare un database SQL di Azure usando backup automatici del databaseRecover an Azure SQL database by using automated database backups

Per impostazione predefinita, i backup del database SQL di Azure vengono archiviati nell'archiviazione BLOB con replica geografica (tipo di archiviazione RA-GRS). Le opzioni seguenti sono disponibili per il ripristino del database utilizzando [backup automatici del database](sql-database-automated-backups.md). È possibile:

- Creare un nuovo database nello stesso server di database SQL, ripristinato a un punto nel tempo specificato all'interno del periodo di conservazione.
- Creare un database nello stesso server di database SQL, ripristinato all'ora di eliminazione per un database eliminato.
- Creare un nuovo database in qualsiasi server di database SQL nella stessa area, ripristinato al punto dei backup più recenti.
- Creare un nuovo database in qualsiasi server di database SQL in qualsiasi altra area, ripristinato al punto dei backup replicati più recenti.

Se è stato configurato il [backup di conservazione a lungo termine](sql-database-long-term-retention.md), è anche possibile creare un nuovo database da qualsiasi backup di conservazione a lungo termine in qualsiasi server di database SQL.

> [!IMPORTANT]
> Non è possibile sovrascrivere un database esistente durante il ripristino.

Quando si usano i livelli di servizio Standard o Premium, il ripristino del database potrebbe comportare un costo di archiviazione aggiuntivo. Il costo aggiuntivo viene sostenuto quando la dimensione massima del database ripristinato è maggiore della quantità di spazio di archiviazione inclusa nel livello di servizio e nel livello di prestazioni del database di destinazione. Per i dettagli dei prezzi delle risorse di archiviazione aggiuntive, vedere la [pagina dei prezzi del database SQL](https://azure.microsoft.com/pricing/details/sql-database/). Se la quantità effettiva di spazio utilizzato è inferiore alla quantità di spazio di archiviazione incluso, è possibile evitare questo costo aggiuntivo impostando la dimensione massima del database sulla quantità inclusa.

## <a name="recovery-time"></a>Tempo di ripristino

Il tempo di ripristino per il ripristino di un database utilizzando backup automatici del database è influenzato da diversi fattori:

- Dimensioni del database.
- Dimensioni di calcolo del database.
- Numero di registri delle transazioni coinvolti.
- Quantità di attività che deve essere riprodotta per il ripristino nel punto di ripristino.
- Larghezza di banda di rete se il ripristino si trova in un'area diversa.
- Il numero di richieste simultanee di ripristino in corso di elaborazione nell'area di destinazione.

Per un database di grandi dimensioni o molto attivo, il ripristino potrebbe richiedere diverse ore. Se si verifica un'interruzione prolungata in un'area, è possibile che venga avviato un numero elevato di richieste di ripristino geografico per il ripristino di emergenza. Quando sono presenti molte richieste, il tempo di ripristino per i singoli database può aumentare. Per la maggior parte dei database, il ripristino richiede meno di 12 ore.

Per una singola sottoscrizione, esistono limitazioni sul numero di richieste di ripristino simultanee. Queste limitazioni si applicano a qualsiasi combinazione di ripristini, ripristini e ripristini geografici dal backup di conservazione a lungo termine.

| | **Numero massimo di richieste simultanee elaborate** | **Numero massimo di richieste simultanee inviate** |
| :--- | --: | --: |
|Database singolo (per sottoscrizione)|10|60|
|Pool elastico (per pool)|4|200|
||||

Non esiste un metodo incorporato per ripristinare l'intero server. Per un esempio di come eseguire questa attività, vedere Database SQL di [Azure: ripristino completo del server](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666).

> [!IMPORTANT]
> Per eseguire il ripristino tramite backup automatici, è necessario essere membri del ruolo di collaboratore di SQL Server nella sottoscrizione o essere il proprietario della sottoscrizione. Per ulteriori informazioni, vedere [RBAC: ruoli predefiniti](../role-based-access-control/built-in-roles.md). È possibile eseguire il ripristino usando il portale di Azure, PowerShell o l'API REST. Non è possibile utilizzare Transact-SQLTransact-SQL .You can't use Transact-SQLTransact-SQL.

## <a name="point-in-time-restore"></a>Ripristino temporizzato

È possibile ripristinare un database autonomo, in pool o dell'istanza in un momento precedente usando il portale di Azure, [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)o [l'API REST.](https://docs.microsoft.com/rest/api/sql/databases) La richiesta può specificare qualsiasi livello di servizio o dimensione di calcolo per il database ripristinato. Assicurarsi di disporre di risorse sufficienti nel server in cui si sta ripristinando il database. Al termine, il ripristino crea un nuovo database nello stesso server del database originale. Il database ripristinato viene addebitato a tariffe normali, in base al livello di servizio e alle dimensioni di calcolo. Non si incorre in addebiti fino al completamento del ripristino del database.

In genere si ripristina un database a un punto precedente per scopi di ripristino. È possibile considerare il database ripristinato come una sostituzione per il database originale o utilizzarlo come origine dati per aggiornare il database originale.

- **Sostituzione del database**

  Se si desidera che il database ripristinato sostituisca il database originale, è necessario specificare le dimensioni di calcolo e il livello di servizio del database originale. È quindi possibile rinominare il database originale e assegnare al database ripristinato il nome originale utilizzando il comando [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) in T-SQL.

- **Recupero dei dati**

  Se si prevede di recuperare i dati dal database ripristinato per il ripristino da un errore dell'utente o dell'applicazione, è necessario scrivere ed eseguire uno script di recupero dati che estrae i dati dal database ripristinato e si applica al database originale. Anche se il completamento dell'operazione di ripristino può richiedere molto tempo, il database in fase di ripristino è visibile nell'elenco dei database per tutto il processo. Se si elimina il database durante il ripristino, l'operazione di ripristino verrà annullata e non verrà addebitato alcun importo per il database che non ha completato il ripristino.
  
### <a name="point-in-time-restore-by-using-azure-portal"></a>Ripristino temporizzato tramite il portale di AzurePoint-in-time restore by using Azure portal

È possibile ripristinare un singolo database SQL o un database dell'istanza a un punto nel tempo dal pannello Panoramica del database che si vuole ripristinare nel portale di Azure.You can recover a single SQL database or instance database to point in time from the overview blade of the database you want to restore in the Azure portal.

#### <a name="single-azure-sql-database"></a>Singolo database SQL di Azure

Per ripristinare un database singolo o in pool a un punto nel tempo tramite il portale di Azure, aprire la pagina di panoramica del database e selezionare **Ripristina** sulla barra degli strumenti. Scegliere l'origine di backup e selezionare il punto di backup temportemporale da cui verrà creato un nuovo database. 

  ![Screenshot delle opzioni di ripristino del database](./media/sql-database-recovery-using-backups/pitr-backup-sql-database-annotated.png)

#### <a name="managed-instance-database"></a>Database dell'istanza gestita

Per ripristinare un database dell'istanza gestita a un punto nel tempo tramite il portale di Azure, aprire la pagina di panoramica del database e selezionare **Ripristina** sulla barra degli strumenti. Scegliere il punto di backup temportemporale da cui verrà creato un nuovo database. 

  ![Screenshot delle opzioni di ripristino del database](./media/sql-database-recovery-using-backups/pitr-backup-managed-instance-annotated.png)

> [!TIP]
> Per ripristinare un database da un backup a livello di codice, vedere Esecuzione del ripristino a [livello di codice mediante backup automatici](sql-database-recovery-using-backups.md).

## <a name="deleted-database-restore"></a>Ripristino di un database eliminato

È possibile ripristinare un database eliminato nell'ora di eliminazione o in un momento precedente nello stesso server di database SQL o nella stessa istanza gestita. A tale scopo, è possibile eseguire questa operazione tramite il portale di Azure, [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)o [REST (createMode-Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate). Per ripristinare un database eliminato, creare un nuovo database dal backup.

> [!IMPORTANT]
> Se si elimina un server di database SQL di Azure o un'istanza gestita, vengono eliminati anche tutti i relativi database e non possono essere recuperati. Non è possibile ripristinare un server eliminato o un'istanza gestita.

### <a name="deleted-database-restore-by-using-the-azure-portal"></a>Eliminazione del ripristino del database tramite il portale di AzureDeleted database restore by using the Azure portal

I database eliminati vengono ripristinati dal portale di Azure dal server e dalla risorsa istanza.

#### <a name="single-azure-sql-database"></a>Singolo database SQL di Azure

Per ripristinare un database eliminato singolo o in pool all'ora di eliminazione tramite il portale di Azure, aprire la pagina di panoramica del server e selezionare **Database eliminati**. Selezionare un database eliminato che si desidera ripristinare e digitare il nome del nuovo database che verrà creato con i dati ripristinati dal backup.

  ![Screenshot del ripristino del database SQL di Azure eliminato](./media/sql-database-recovery-using-backups/restore-deleted-sql-database-annotated.png)

#### <a name="managed-instance-database"></a>Database dell'istanza gestita

Per ripristinare un database gestito tramite il portale di Azure, aprire la pagina Panoramica dell'istanza gestita e selezionare **Database eliminati.** Selezionare un database eliminato che si desidera ripristinare e digitare il nome del nuovo database che verrà creato con i dati ripristinati dal backup.

  ![Screenshot del ripristino del database dell'istanza SQL di Azure eliminato](./media/sql-database-recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="deleted-database-restore-by-using-powershell"></a>Eliminato il ripristino del database tramite PowerShellDeleted database restore by using PowerShell

Usare gli script di esempio seguenti per ripristinare un database eliminato per il database SQL di Azure e un'istanza gestita tramite PowerShell.Use the following sample scripts to restore a deleted database for Azure SQL Database and a managed instance by using PowerShell.

#### <a name="single-azure-sql-database"></a>Singolo database SQL di Azure

Per uno script di PowerShell di esempio che illustra come ripristinare un database SQL di Azure eliminato, vedere [Ripristinare un database SQL tramite PowerShell.For](scripts/sql-database-restore-database-powershell.md)a sample PowerShell script showing how to restore a deleted Azure SQL database, see Restore a SQL database using PowerShell.

#### <a name="managed-instance-database"></a>Database dell'istanza gestita

Per uno script di PowerShell di esempio che mostra come ripristinare un database dell'istanza eliminata, vedere Ripristinare il [database eliminato nell'istanza gestita tramite PowerShellFor](sql-database-managed-instance-point-in-time-restore.md#restore-a-deleted-database) a sample PowerShell script showing how to restore a deleted instance database, see Restore deleted database on managed instance using PowerShell

> [!TIP]
> Per ripristinare a livello di codice un database eliminato, vedere Esecuzione del ripristino a [livello di codice mediante backup automatici](sql-database-recovery-using-backups.md).

## <a name="geo-restore"></a>Ripristino geografico

Il ripristino geografico consente di ripristinare un database SQL presente su un server in qualsiasi area di Azure usando gli ultimi backup con replica geografica. Il ripristino geografico utilizza un backup con replica geografica come origine. È possibile richiedere il ripristino geografico anche se il database o il data center non è accessibile a causa di un'interruzione.

Il ripristino geografico è l'opzione di ripristino predefinita quando il database non è disponibile a causa di un evento imprevisto nell'area di hosting. È possibile ripristinare il database in un server in qualsiasi altra area. Esiste un ritardo tra il momento in cui un backup viene creato e quando ne viene eseguita la replica geografica in un BLOB di Azure in un'area diversa. Di conseguenza, il database ripristinato può trovarsi fino a un'ora dopo il database originale. Nella figura seguente viene illustrato un ripristino del database dall'ultimo backup disponibile in un'altra area.

![Grafica del geo-ripristino](./media/sql-database-geo-restore/geo-restore-2.png)

### <a name="geo-restore-by-using-the-azure-portal"></a>Ripristino geografico tramite il portale di AzureGeo-restore by using the Azure portal

Dal portale di Azure si crea un nuovo database di istanze singole o gestite e si seleziona un backup di ripristino geografico disponibile. Il database appena creato contiene i dati di backup ripristinati geograficamente.

#### <a name="single-azure-sql-database"></a>Singolo database SQL di Azure

Per eseguire il ripristino geografico di un singolo database SQL dal portale di Azure nell'area e nel server di propria scelta, attenersi alla seguente procedura:

1. In **Dashboard**selezionare **Aggiungi** > **database SQL**. Nella scheda **Nozioni di base** immettere le informazioni richieste.
2. Seleziona **Impostazioni aggiuntive**.
3. Per **Usa dati esistenti**, selezionare **Backup**.
4. In **Backup**, selezionare un backup dall'elenco dei backup di ripristino geografico disponibili.

    ![Screenshot delle opzioni Crea database SQL](./media/sql-database-recovery-using-backups/geo-restore-azure-sql-database-list-annotated.png)

Completare il processo di creazione di un nuovo database dal backup. Quando si crea il singolo database SQL di Azure, questo contiene il backup di ripristino geografico ripristinato.

#### <a name="managed-instance-database"></a>Database dell'istanza gestita

Per ripristinare geograficamente un database di istanze gestite dal portale di Azure a un'istanza gestita esistente in un'area di propria scelta, selezionare un'istanza gestita in cui si vuole ripristinare un database. A tale scopo, seguire questa procedura:

1. Selezionare **Nuovo database**.
2. Digitare il nome di database desiderato.
3. In **Usa dati esistenti**selezionare **Backup**.
4. Selezionare un backup dall'elenco dei backup di ripristino geografico disponibili.

    ![Schermata delle opzioni Nuovo database](./media/sql-database-recovery-using-backups/geo-restore-sql-managed-instance-list-annotated.png)

Completare il processo di creazione di un nuovo database. Quando si crea il database dell'istanza, esso contiene il backup di ripristino geografico ripristinato.

### <a name="geo-restore-by-using-powershell"></a>Ripristino geografico tramite PowerShellGeo-restore by using PowerShell

#### <a name="single-azure-sql-database"></a>Singolo database SQL di Azure

Per uno script di PowerShell che illustra come eseguire il ripristino geografico per un singolo database SQL, vedere [Usare PowerShell per ripristinare un singolo database SQL](scripts/sql-database-restore-database-powershell.md)di Azure in un momento precedente.

#### <a name="managed-instance-database"></a>Database dell'istanza gestita

Per uno script di PowerShell che illustra come eseguire il ripristino geografico per un database dell'istanza gestita, vedere [Usare PowerShell per ripristinare un database dell'istanza gestita in un'altra area geografica.](scripts/sql-managed-instance-restore-geo-backup.md)

### <a name="geo-restore-considerations"></a>Considerazioni sul ripristino geografico

Non è possibile eseguire un ripristino temporizzato in un database secondario geografico. È possibile eseguire questa operazione solo in un database primario. Per informazioni dettagliate sull'utilizzo del geo-ripristino per il ripristino da un'interruzione, vedere [Ripristino da un'interruzione](sql-database-disaster-recovery.md).

> [!IMPORTANT]
> Il ripristino geografico è la soluzione di ripristino di emergenza più semplice disponibile nel database SQL. Si basa su backup con replica geografica creati automaticamente con obiettivo del punto di ripristino (RPO) pari a 1 ora e il tempo di ripristino stimato fino a 12 ore. Non garantisce che l'area di destinazione avrà la capacità di ripristinare i database dopo un'interruzione a livello di area, perché è probabile un forte aumento della domanda. Se l'applicazione utilizza database relativamente piccoli e non è fondamentale per l'azienda, il ripristino geografico è una soluzione di ripristino di emergenza appropriata. Per le applicazioni business-critical che richiedono database di grandi dimensioni e devono garantire la continuità aziendale, utilizzare i gruppi di [failover automatico.](sql-database-auto-failover-group.md) Offre un RPO e un obiettivo di tempo di recupero molto più bassi e la capacità è sempre garantita. Per altre informazioni sulle opzioni di continuità aziendale, vedere [Panoramica sulla continuità aziendale](sql-database-business-continuity.md).

## <a name="programmatically-performing-recovery-by-using-automated-backups"></a>Esecuzione di ripristino a livello di codice tramite backup automaticiProgrammatically performing recovery by using automated backups

È anche possibile usare Azure PowerShell o l'API REST per il ripristino. Le tabelle seguenti descrivono il set di comandi disponibili.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo di PowerShell Azure Resource Manager è ancora supportato dal database SQL di Azure, ma tutto lo sviluppo futuro è per il modulo Az.Sql.The PowerShell Azure Resource Manager module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. Per questi cmdlet, vedere [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nel modulo Az e nei moduli di AzureRm sono in larga misura identici.

#### <a name="single-azure-sql-database"></a>Singolo database SQL di Azure

Per ripristinare un database autonomo o in pool, vedere [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase).

  | Cmdlet | Descrizione |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Ottiene uno o più database. |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Ottiene un database eliminato che è possibile ripristinare. |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |Ottiene una copia di backup con ridondanza geografica di un database. |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |Ripristina un database SQL. |

  > [!TIP]
  > Per uno script di PowerShell di esempio che illustra come eseguire un ripristino temporizzato di un database, vedere [Ripristinare un database SQL tramite PowerShell.For](scripts/sql-database-restore-database-powershell.md)a sample PowerShell script that shows how to perform a point-in-time restore of a database, see Restore a SQL database using PowerShell .

#### <a name="managed-instance-database"></a>Database dell'istanza gestita

Per ripristinare un database dell'istanza gestita, vedere [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase).

  | Cmdlet | Descrizione |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |Ottiene una o più istanze gestite. |
  | [Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | Ottiene un database dell'istanza. |
  | [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |Ripristina un database dell'istanza. |

### <a name="rest-api"></a>API REST

Per ripristinare un database singolo o in pool tramite l'API REST:To restore a single or pooled database by using the REST API:

| API | Descrizione |
| --- | --- |
| [REST (createMode=Recovery)](https://docs.microsoft.com/rest/api/sql/databases) |Ripristina un database. |
| [Get Create or Update Database Status](https://docs.microsoft.com/rest/api/sql/operations) |Restituisce lo stato durante un'operazione di ripristino. |

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

#### <a name="single-azure-sql-database"></a>Singolo database SQL di Azure

Per ripristinare un database singolo o in pool tramite l'interfaccia della riga di comando di Azure, vedere [az sql db restore](/cli/azure/sql/db#az-sql-db-restore).

#### <a name="managed-instance-database"></a>Database dell'istanza gestita

Per ripristinare un database dell'istanza gestita tramite l'interfaccia della riga di comando di Azure, vedere [az sql midb restore](/cli/azure/sql/midb#az-sql-midb-restore).

## <a name="summary"></a>Riepilogo

I backup automatici proteggono i database da errori dell'utente e delle applicazioni, dall'eliminazione accidentale e da interruzioni prolungate. Questa funzionalità incorporata è disponibile per tutti i livelli di servizio e le dimensioni di calcolo.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
- [Backup automatici del database SQL](sql-database-automated-backups.md)
- [Conservazione a lungo termine](sql-database-long-term-retention.md)
- Per altre informazioni su opzioni di ripristino più veloci, vedere [Replica geografica attiva](sql-database-active-geo-replication.md) o [Gruppi di failover automatico](sql-database-auto-failover-group.md).
