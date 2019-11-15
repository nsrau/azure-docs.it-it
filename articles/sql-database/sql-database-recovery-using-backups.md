---
title: Ripristinare un database da un backup
description: Informazioni sul ripristino temporizzato, che consente di eseguire il rollback di un database SQL di Azure per un massimo di 35 giorni.
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
ms.openlocfilehash: 1c8717614ec59ef210c7340f70ddedd7f7f86f88
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091964"
---
# <a name="recover-an-azure-sql-database-by-using-automated-database-backups"></a>Ripristinare un database SQL di Azure usando i backup automatici del database

Per impostazione predefinita, i backup del database SQL di Azure vengono archiviati in un archivio BLOB con replica geografica (tipo di archiviazione RA-GRS). Le opzioni seguenti sono disponibili per il ripristino del database usando i [backup automatici del database](sql-database-automated-backups.md). È possibile:

- Consente di creare un nuovo database nello stesso server di database SQL, ripristinato fino a un punto nel tempo specificato entro il periodo di memorizzazione.
- Creare un database nello stesso server di database SQL, ripristinato fino all'ora di eliminazione per un database eliminato.
- Creare un nuovo database in qualsiasi server di database SQL nella stessa area, ripristinato fino al punto dei backup più recenti.
- Creare un nuovo database in qualsiasi server di database SQL in un'altra area, ripristinato fino al punto dei backup replicati più recenti.

Se è stata configurata la [conservazione a lungo termine del backup](sql-database-long-term-retention.md), è anche possibile creare un nuovo database da qualsiasi backup di conservazione a lungo termine in qualsiasi server di database SQL.

> [!IMPORTANT]
> Non è possibile sovrascrivere un database esistente durante il ripristino.

Quando si usano i livelli di servizio standard o Premium, il ripristino del database potrebbe comportare un costo di archiviazione aggiuntivo. Il costo aggiuntivo si verifica quando le dimensioni massime del database ripristinato sono maggiori della quantità di spazio di archiviazione inclusa con il livello di servizio e il livello di prestazioni del database di destinazione. Per i dettagli dei prezzi delle risorse di archiviazione aggiuntive, vedere la [pagina dei prezzi del database SQL](https://azure.microsoft.com/pricing/details/sql-database/). Se la quantità effettiva di spazio usato è inferiore alla quantità di spazio di archiviazione inclusa, è possibile evitare questo costo aggiuntivo impostando le dimensioni massime del database sulla quantità inclusa.

## <a name="recovery-time"></a>Tempo di ripristino

Il tempo di recupero per il ripristino di un database tramite i backup automatici del database è influenzato da diversi fattori:

- Dimensioni del database.
- Dimensioni di calcolo del database.
- Numero di log delle transazioni necessari.
- Quantità di attività che deve essere rieseguita per ripristinare il punto di ripristino.
- Larghezza di banda della rete se il ripristino si trova in un'area diversa.
- Il numero di richieste simultanee di ripristino in corso di elaborazione nell'area di destinazione.

Per un database di grandi dimensioni o molto attive, il ripristino potrebbe richiedere diverse ore. Se si verifica un'interruzione prolungata in un'area, è possibile che venga avviato un numero elevato di richieste di ripristino geografico per il ripristino di emergenza. Quando sono presenti molte richieste, il tempo di ripristino per i singoli database può aumentare. Per la maggior parte dei database, il ripristino richiede meno di 12 ore.

Per una singola sottoscrizione, esistono alcune limitazioni al numero di richieste di ripristino simultanee. Queste limitazioni si applicano a qualsiasi combinazione di ripristini temporizzati, ripristini geografici e ripristini dal backup con conservazione a lungo termine.

| | **Numero massimo di richieste simultanee elaborate** | **Numero massimo di richieste simultanee inviate** |
| :--- | --: | --: |
|Database singolo (per sottoscrizione)|10|60|
|Pool elastico (per pool)|4|200|
||||

Non esiste un metodo incorporato per ripristinare l'intero server. Per un esempio di come eseguire questa attività, vedere [database SQL di Azure: ripristino completo del server](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666).

> [!IMPORTANT]
> Per eseguire il ripristino tramite backup automatici, è necessario essere un membro del ruolo Collaboratore SQL Server nella sottoscrizione o essere il proprietario della sottoscrizione. Per ulteriori informazioni, vedere [RBAC: ruoli predefiniti](../role-based-access-control/built-in-roles.md). È possibile eseguire il ripristino usando il portale di Azure, PowerShell o l'API REST. Non è possibile usare Transact-SQL.

## <a name="point-in-time-restore"></a>Ripristino temporizzato

È possibile ripristinare un database autonomo, in pool o di istanza a un punto precedente nel tempo usando il portale di Azure, [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)o l' [API REST](https://docs.microsoft.com/rest/api/sql/databases). La richiesta può specificare qualsiasi livello di servizio o dimensione di calcolo per il database ripristinato. Assicurarsi di disporre di risorse sufficienti sul server in cui si sta ripristinando il database. Al termine, il ripristino crea un nuovo database nello stesso server del database originale. Il database ripristinato viene addebitato in base alle tariffe normali, in base al livello di servizio e alle dimensioni di calcolo. Non vengono addebitati costi fino al completamento del ripristino del database.

In genere si ripristina un database a un punto precedente per scopi di ripristino. È possibile considerare il database ripristinato come una sostituzione per il database originale o usarlo come origine dati per aggiornare il database originale.

- **Sostituzione del database**

  Se si desidera che il database ripristinato sia una sostituzione per il database originale, è necessario specificare le dimensioni di calcolo e il livello di servizio del database originale. È quindi possibile rinominare il database originale e assegnare al database ripristinato il nome originale usando il comando [ALTER database](/sql/t-sql/statements/alter-database-azure-sql-database) in T-SQL.

- **Ripristino dei dati**

  Se si prevede di recuperare dati dal database ripristinato in caso di errore di un utente o di un'applicazione, è necessario scrivere ed eseguire uno script di ripristino dei dati che estrae i dati dal database ripristinato e si applica al database originale. Anche se il completamento dell'operazione di ripristino può richiedere molto tempo, il database in fase di ripristino è visibile nell'elenco dei database per tutto il processo. Se si elimina il database durante il ripristino, l'operazione di ripristino verrà annullata e non verrà addebitato alcun addebito per il database che non ha completato il ripristino.
  
### <a name="point-in-time-restore-by-using-azure-portal"></a>Ripristino temporizzato tramite portale di Azure

È possibile ripristinare un singolo database SQL o istanza a un punto nel tempo dal pannello panoramica del database che si desidera ripristinare nel portale di Azure.

#### <a name="single-azure-sql-database"></a>Singolo database SQL di Azure

Per ripristinare un database singolo o in pool a un punto nel tempo usando il portale di Azure, aprire la pagina Panoramica database e selezionare **Ripristina** sulla barra degli strumenti. Scegliere l'origine di backup e selezionare il punto di backup temporizzato da cui verrà creato un nuovo database. 

  ![Screenshot delle opzioni di ripristino del database](./media/sql-database-recovery-using-backups/pitr-backup-sql-database-annotated.png)

#### <a name="managed-instance-database"></a>Database istanza gestita

Per ripristinare un database di istanza gestita a un punto nel tempo tramite il portale di Azure, aprire la pagina Panoramica database e selezionare **Ripristina** sulla barra degli strumenti. Scegliere il punto di backup temporizzato da cui verrà creato un nuovo database. 

  ![Screenshot delle opzioni di ripristino del database](./media/sql-database-recovery-using-backups/pitr-backup-managed-instance-annotated.png)

> [!TIP]
> Per ripristinare a livello di codice un database da un backup, vedere esecuzione di un [ripristino a livello di codice tramite backup automatici](sql-database-recovery-using-backups.md).

## <a name="deleted-database-restore"></a>Ripristino di un database eliminato

È possibile ripristinare un database eliminato al momento dell'eliminazione o a un punto precedente nel tempo, nello stesso server di database SQL o nella stessa istanza gestita. A tale scopo, è possibile usare il portale di Azure, [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)o [Rest (createMode = Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate). Per ripristinare un database eliminato, creare un nuovo database dal backup.

> [!IMPORTANT]
> Se si elimina un server di database SQL di Azure o un'istanza gestita, verranno eliminati anche tutti i relativi database e non sarà possibile recuperarli. Non è possibile ripristinare un server o un'istanza gestita eliminata.

### <a name="deleted-database-restore-by-using-the-azure-portal"></a>Ripristino del database eliminato tramite il portale di Azure

È possibile ripristinare i database eliminati dal portale di Azure dalla risorsa server e istanza.

#### <a name="single-azure-sql-database"></a>Singolo database SQL di Azure

Per ripristinare un database eliminato singolo o in pool usando il portale di Azure, aprire la pagina Panoramica Server e selezionare **database eliminati**. Selezionare un database eliminato che si desidera ripristinare e digitare il nome del nuovo database che verrà creato con i dati ripristinati dal backup.

  ![Screenshot del ripristino eliminato del database SQL di Azure](./media/sql-database-recovery-using-backups/restore-deleted-sql-database-annotated.png)

#### <a name="managed-instance-database"></a>Database istanza gestita

Per ripristinare un database gestito utilizzando il portale di Azure, aprire la pagina Panoramica istanza gestita e selezionare **database eliminati**. Selezionare un database eliminato che si desidera ripristinare e digitare il nome del nuovo database che verrà creato con i dati ripristinati dal backup.

  ![Schermata del ripristino del database dell'istanza SQL di Azure](./media/sql-database-recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="deleted-database-restore-by-using-powershell"></a>Ripristino del database eliminato tramite PowerShell

Usare gli script di esempio seguenti per ripristinare un database eliminato per il database SQL di Azure e un'istanza gestita usando PowerShell.

#### <a name="single-azure-sql-database"></a>Singolo database SQL di Azure

Per uno script di PowerShell di esempio che illustra come ripristinare un database SQL di Azure eliminato, vedere [ripristinare un database SQL tramite PowerShell](scripts/sql-database-restore-database-powershell.md).

#### <a name="managed-instance-database"></a>Database istanza gestita

Per uno script di PowerShell di esempio che illustra come ripristinare un database di istanza eliminato, vedere [ripristinare un database eliminato in un'istanza gestita tramite PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../recreate-dropped-database-on-azure-sql-managed-instance). 

> [!TIP]
> Per ripristinare a livello di codice un database eliminato, vedere esecuzione di un [ripristino a livello di codice tramite backup automatici](sql-database-recovery-using-backups.md).

## <a name="geo-restore"></a>Ripristino geografico

Il ripristino geografico consente di ripristinare un database SQL presente su un server in qualsiasi area di Azure usando gli ultimi backup con replica geografica. Il ripristino geografico USA come origine un backup con replica geografica. È possibile richiedere il ripristino geografico anche se il database o il Data Center è inaccessibile a causa di un'interruzione del servizio.

Il ripristino geografico è l'opzione di ripristino predefinita quando il database non è disponibile a causa di un evento imprevisto nell'area di hosting. È possibile ripristinare il database in un server in un'altra area. Esiste un ritardo tra il momento in cui un backup viene creato e quando ne viene eseguita la replica geografica in un BLOB di Azure in un'area diversa. Di conseguenza, il database ripristinato può essere fino a un'ora dopo il database originale. Nella figura seguente viene illustrato un ripristino del database dall'ultimo backup disponibile in un'altra area.

![Rappresentazione grafica del ripristino geografico](./media/sql-database-geo-restore/geo-restore-2.png)

### <a name="geo-restore-by-using-the-azure-portal"></a>Ripristino geografico tramite il portale di Azure

Dal portale di Azure si crea un nuovo database istanza singola o gestita e si seleziona un backup disponibile per il ripristino geografico. Il database appena creato contiene i dati di backup con ripristino geografico.

#### <a name="single-azure-sql-database"></a>Singolo database SQL di Azure

Per eseguire il ripristino geografico di un database SQL singolo dal portale di Azure nell'area e nel server di propria scelta, seguire questa procedura:

1. Dal **Dashboard**selezionare **Aggiungi** > **Crea database SQL**. Nella scheda informazioni di **base** immettere le informazioni necessarie.
2. Selezionare **Impostazioni aggiuntive**.
3. Per **Usa dati esistenti**, selezionare **backup**.
4. Per il **backup**, selezionare un backup dall'elenco dei backup disponibili per il ripristino geografico.

    ![Screenshot delle opzioni create database SQL](./media/sql-database-recovery-using-backups/geo-restore-azure-sql-database-list-annotated.png)

Completare il processo di creazione di un nuovo database dal backup. Quando si crea il singolo database SQL di Azure, esso contiene il backup del ripristino geografico ripristinato.

#### <a name="managed-instance-database"></a>Database istanza gestita

Per ripristinare il ripristino geografico di un database di istanza gestita dal portale di Azure a un'istanza gestita esistente in un'area di propria scelta, selezionare un'istanza gestita in cui si desidera ripristinare un database. Seguire questa procedura:

1. Selezionare **nuovo database**.
2. Digitare un nome di database desiderato.
3. In **Usa dati esistenti**selezionare **backup**.
4. Selezionare un backup dall'elenco dei backup disponibili per il ripristino geografico.

    ![Screenshot delle nuove opzioni di database](./media/sql-database-recovery-using-backups/geo-restore-sql-managed-instance-list-annotated.png)

Completare il processo di creazione di un nuovo database. Quando si crea il database dell'istanza, esso contiene il backup del ripristino geografico ripristinato.

### <a name="geo-restore-by-using-powershell"></a>Ripristino geografico tramite PowerShell

#### <a name="single-azure-sql-database"></a>Singolo database SQL di Azure

Per uno script di PowerShell che illustra come eseguire il ripristino geografico per un singolo database SQL, vedere [usare PowerShell per ripristinare un database singolo di Azure SQL a un momento precedente](scripts/sql-database-restore-database-powershell.md).

#### <a name="managed-instance-database"></a>Database istanza gestita

Per uno script di PowerShell che illustra come eseguire il ripristino geografico per un database di istanza gestita, vedere [usare PowerShell per ripristinare un database di istanza gestita in un'altra area geografica](scripts/sql-managed-instance-restore-geo-backup.md).

### <a name="geo-restore-considerations"></a>Considerazioni sul ripristino geografico

Non è possibile eseguire un ripristino temporizzato in un database di replica geografica secondaria. Questa operazione può essere eseguita solo su un database primario. Per informazioni dettagliate sull'uso del ripristino geografico in caso di un'interruzione del servizio, vedere [Ripristinare un database SQL di Azure o eseguire il failover in un database secondario](sql-database-disaster-recovery.md).

> [!IMPORTANT]
> Il ripristino geografico è la soluzione di ripristino di emergenza più semplice disponibile nel database SQL. Si basa sui backup con replica geografica creati automaticamente con l'obiettivo del punto di ripristino (RPO) uguale a 1 ora e il tempo di recupero stimato di un massimo di 12 ore. Non garantisce che l'area di destinazione abbia la capacità di ripristinare i database dopo un'interruzione a livello di area, perché è probabile che si verifichi un forte aumento della domanda. Se l'applicazione usa database relativamente piccoli e non è cruciale per l'azienda, il ripristino geografico è una soluzione di ripristino di emergenza appropriata. Per le applicazioni critiche per l'azienda che richiedono database di grandi dimensioni e che devono garantire la continuità aziendale, usare i [gruppi di failover automatico](sql-database-auto-failover-group.md). Offre un obiettivo molto più basso per RPO e tempo di ripristino e la capacità è sempre garantita. Per altre informazioni sulle opzioni di continuità aziendale, vedere [Panoramica sulla continuità aziendale](sql-database-business-continuity.md).

## <a name="programmatically-performing-recovery-by-using-automated-backups"></a>Esecuzione del ripristino a livello di codice tramite backup automatici

È anche possibile usare Azure PowerShell o l'API REST per il ripristino. Le tabelle seguenti descrivono il set di comandi disponibili.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell è ancora supportato dal database SQL di Azure, ma tutte le attività di sviluppo future sono per il modulo AZ. SQL. Per questi cmdlet, vedere [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nel modulo AZ e nei moduli AzureRm sono molto simili.

#### <a name="single-azure-sql-database"></a>Singolo database SQL di Azure

Per ripristinare un database autonomo o in pool, vedere [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase).

  | Cmdlet | DESCRIZIONE |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Ottiene uno o più database. |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Ottiene un database eliminato che è possibile ripristinare. |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |Ottiene una copia di backup con ridondanza geografica di un database. |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |Ripristina un database SQL. |

  > [!TIP]
  > Per uno script di PowerShell di esempio che illustra come eseguire un ripristino temporizzato di un database, vedere [ripristinare un database SQL tramite PowerShell](scripts/sql-database-restore-database-powershell.md).

#### <a name="managed-instance-database"></a>Database istanza gestita

Per ripristinare un database di istanza gestita, vedere [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase).

  | Cmdlet | DESCRIZIONE |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |Ottiene una o più istanze gestite. |
  | [Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | Ottiene un database dell'istanza. |
  | [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |Ripristina un database dell'istanza. |

### <a name="rest-api"></a>API REST

Per ripristinare un database singolo o in pool usando l'API REST:

| API | DESCRIZIONE |
| --- | --- |
| [REST (createMode=Recovery)](https://docs.microsoft.com/rest/api/sql/databases) |Ripristina un database. |
| [Get Create or Update Database Status](https://docs.microsoft.com/rest/api/sql/operations) |Restituisce lo stato durante un'operazione di ripristino. |

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

#### <a name="single-azure-sql-database"></a>Singolo database SQL di Azure

Per ripristinare un database singolo o in pool usando l'interfaccia della riga di comando di Azure, vedere [AZ SQL DB Restore](/cli/azure/sql/db#az-sql-db-restore).

#### <a name="managed-instance-database"></a>Database istanza gestita

Per ripristinare un database di istanza gestita usando l'interfaccia della riga di comando di Azure, vedere [AZ SQL MidB Restore](/cli/azure/sql/midb#az-sql-midb-restore).

## <a name="summary"></a>summary

I backup automatici proteggono i database da errori dell'utente e delle applicazioni, dall'eliminazione accidentale e da interruzioni prolungate. Questa funzionalità incorporata è disponibile per tutti i livelli di servizio e le dimensioni di calcolo.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
- [Backup automatici del database SQL](sql-database-automated-backups.md)
- [Conservazione a lungo termine](sql-database-long-term-retention.md)
- Per altre informazioni su opzioni di ripristino più veloci, vedere [Replica geografica attiva](sql-database-active-geo-replication.md) o [Gruppi di failover automatico](sql-database-auto-failover-group.md).
