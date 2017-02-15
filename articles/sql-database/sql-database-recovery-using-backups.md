---
title: "Continuità aziendale cloud - Ripristino di un database eliminato - Database SQL | Documentazione Microsoft"
description: Informazioni sul ripristino temporizzato, che consente di ripristinare un database SQL di Azure a un momento precedente (fino a 35 giorni).
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: fd1d334d-a035-4a55-9446-d1cf750d9cf7
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/11/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 239d009a1fc7273a50d335a0d55d61f414d99b11
ms.openlocfilehash: e333c306d85d6eb571c3c1990188e67b18c8a6b1


---
# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>Ripristinare un database SQL di Azure mediante i backup automatici del database
Il database SQL offre tre opzioni di ripristino dei database con i [backup automatici del database SQL](sql-database-automated-backups.md). È possibile ripristinare un database dai backup avviati dal servizio durante il relativo [periodo di conservazione](sql-database-service-tiers.md) in:

* Un nuovo database nello stesso server logico ripristinato in una temporizzazione specificata entro il periodo di conservazione. 
* Un database nello stesso server logico ripristinato all'ora di eliminazione per un database eliminato.
* Un nuovo database in qualsiasi server logico in qualsiasi area ripristinato ai backup giornalieri più recenti nell'archiviazione BLOB con replica geografica (RA-GRS).

> [!TIP]
> Per un'esercitazione, vedere [Introduzione a Backup e ripristino per la protezione dei dati e il ripristino](sql-database-get-started-backup-recovery.md)
>

È inoltre possibile usare [backup automatici del database SQL](sql-database-automated-backups.md) per creare una [copia del database](sql-database-copy.md) in qualsiasi server logico in un'area coerente da un punto di vista transazionale con il database SQL corrente. È possibile usare la copia del database ed [esportare in un file BACPAC](sql-database-export.md) per archiviare una copia di un database coerente da un punto di vista transazionale per l'archiviazione a lungo termine oltre il periodo di conservazione o trasferire una copia del database in un'istanza locale o un'istanza VM di Azure di SQL Server.

> [!IMPORTANT]
> È possibile configurare il server logico in cui archiviare i backup settimanali con [conservazione dei backup a lungo termine](sql-database-long-term-retention.md).

## <a name="recovery-time"></a>Tempo di ripristino
Il tempo di ripristino di un database tramite i backup automatici del database dipende da numerosi fattori: 

* Le dimensioni del database
* Il livello di prestazioni del database
* Il numero dei log delle transazioni interessate
* La quantità di attività che deve essere ripetuta per il ripristino al punto di ripristino
* La larghezza di banda di rete se il ripristino avviene in un'area diversa 
* Il numero di richieste simultanee di ripristino in corso di elaborazione nell'area di destinazione. 
  
  Per un database molto grande e/o attivo il ripristino potrebbe richiedere diverse ore. Nel caso di un'interruzione prolungata in un'area, è possibile che in altre aree vengano elaborate molte richieste di ripristino geografico. In presenza di numerose richieste, i tempi di recupero dei database in tale area possono aumentare. Per la maggior parte dei database, il ripristino richiede al massimo 12 ore.
  
  Non è disponibile una funzionalità incorporata per il ripristino in blocco. Lo script [Database SQL di Azure: Full Server Recovery](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) è un esempio di un modo per eseguire questa operazione.

> [!IMPORTANT]
> Per eseguire il ripristino tramite i backup automatici, è necessario essere un membro del ruolo di collaboratore di SQL Server o proprietario della sottoscrizione. Per il ripristino, è possibile usare il portale di Azure, PowerShell o l'API REST. Non è possibile usare Transact-SQL. 
> 

## <a name="point-in-time-restore"></a>Ripristino temporizzato
La funzionalità di ripristino temporizzato consente di ripristinare un database esistente come database nuovo a un punto precedente nel tempo sullo stesso server logico, usando i [backup automatici del database SQL](sql-database-automated-backups.md). Non è possibile sovrascrivere il database esistente. È possibile eseguire il ripristino a un punto precedente nel tempo usando il [portale di Azure](sql-database-point-in-time-restore.md), [PowerShell](sql-database-point-in-time-restore.md) o l'[API REST](https://msdn.microsoft.com/library/azure/mt163685.aspx).


Il database può essere ripristinato a qualsiasi livello di prestazioni o pool elastico. Verificare di disporre di una quota DTU sufficiente per il server logico o il pool elastico. Tenere presente che il ripristino crea un nuovo database e che il livello di prestazioni e il livello di servizio del database ripristinato potrebbero essere diversi dallo stato corrente del database attivo. Al termine, il database ripristinato è un normale database online completamente accessibile alle tariffe normali in base al livello di prestazioni e al livello di servizio. Non è previsto alcun addebito fino al completamento del ripristino del database.

In genere si ripristina un database a un punto precedente per scopi di ripristino. Quando si esegue questa operazione, è possibile considerare il database ripristinato come una sostituzione per il database originale o usarlo per recuperare i dati e quindi aggiornare il database originale. 

* ***Sostituzione del database***: se il database ripristinato è da intendersi come una sostituzione per il database originale, è necessario verificare che il livello di prestazioni e/o il livello di servizio sia appropriato e ridimensionare il database se necessario. È possibile rinominare il database originale e quindi assegnare al database ripristinato il nome originale usando il comando ALTER DATABASE in T-SQL. 
* ***Ripristino dei dati***: se si prevede di recuperare dati dal database ripristinato in caso di errore dell'applicazione o dell'utente, è necessario scrivere ed eseguire gli script di ripristino per estrarre i dati dal database ripristinato e inserirli nel database originale. Anche se il completamento dell'operazione di ripristino può richiedere molto tempo, il database in fase di ripristino sarà visibile nell'elenco dei database per tutto il tempo. Se si elimina il database durante il ripristino l'operazione verrà annullata e non verrà addebitata per il database il cui ripristino non è stato completato. 

Per informazioni dettagliate sull'uso del ripristino temporizzato in caso di errori dell'utente e dell'applicazione, vedere [Ripristino temporizzato](sql-database-recovery-using-backups.md#point-in-time-restore)

## <a name="deleted-database-restore"></a>Ripristino di un database eliminato
Il ripristino di un database eliminato consente di ripristinare un database all'ora di eliminazione per un database eliminato sullo stesso server logico, usando i [backup automatici del database SQL](sql-database-automated-backups.md). 

> [!IMPORTANT]
> Se si elimina un'istanza del server di database SQL di Azure, anche tutti i database relativi vengono eliminati e non possono essere recuperati. Non è attualmente disponibile alcun supporto per il ripristino di un server eliminato.
> 
> 

È possibile usare lo stesso nome o un nuovo nome di database per il database ripristinato. È possibile usare il [portale di Azure](sql-database-restore-deleted-database-portal.md), [PowerShell](sql-database-restore-deleted-database-powershell.md) o [REST (createMode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx). 


## <a name="geo-restore"></a>Ripristino geografico
Il ripristino geografico consente di ripristinare un database SQL presente su un server in qualsiasi area di Azure usando l'ultimo [backup giornaliero automatico](sql-database-automated-backups.md)con replica geografica. Il ripristino geografico usa un backup con ridondanza geografica come origine e può essere usato per ripristinare un database anche se il database o il data center è inaccessibile a causa di un guasto. È possibile usare il [portale di Azure](sql-database-geo-restore-portal.md), [PowerShell](sql-database-geo-restore-powershell.md) o [REST (createMode=Recovery)](https://msdn.microsoft.com/library/azure/mt163685.aspx). 


Il ripristino geografico è l'opzione di ripristino predefinita quando il database non è disponibile a causa di un evento imprevisto nell'area in cui è ospitato. Se si verifica un evento imprevisto su larga scala che determina la mancata disponibilità dell'applicazione di database, è possibile usare il ripristino geografico per ripristinare un database dal backup più recente in un server in un'altra area. Tutti i backup sono con replica geografica ed è possibile si verifichi un ritardo che tra l'acquisizione del backup e la replica geografica nel BLOB di Azure in un'area diversa. Questo ritardo può essere al massimo di un'ora, quindi in caso di emergenza può verificarsi una perdita di dati relativi al massimo a un'ora, con valore RPO fino a un'ora. Di seguito è mostrato il ripristino del database dall'ultimo backup giornaliero.

![Ripristino geografico](./media/sql-database-geo-restore/geo-restore-2.png)

Per informazioni dettagliate sull'uso del ripristino geografico in caso di un'interruzione del servizio, vedere [Ripristinare un database SQL di Azure o eseguire il failover in un database secondario](sql-database-disaster-recovery.md)

> [!IMPORTANT]
> Il ripristino geografico, disponibile con tutti i livelli di servizio, è la più semplice delle soluzioni di ripristino di emergenza disponibili nel database SQL con il massimo valore per RPO e tempo di recupero stimato. Per i database di base con una dimensione massima di 2 GB, il ripristino geografico rappresenta una soluzione ragionevole per il ripristino di emergenza con un tempo di recupero stimato di 12 ore. Per i database Standard o Premium di dimensioni maggiori, se è auspicabile una riduzione dei tempi di ripristino o per limitare le probabilità di perdita di dati, è consigliabile usare la replica geografica attiva. La replica geografica attiva offre un obiettivo del punto di ripristino e un tempo di recupero stimato decisamente inferiori perché richiede solo l'avvio di un failover in un database secondario con replica continua. Per informazioni dettagliate, vedere [Panoramica: Replica geografica attiva per il database SQL di Azure](sql-database-geo-replication-overview.md).
> 
> 

## <a name="programmatically-performing-recovery-using-automated-backups"></a>Esecuzione a livello di codice del ripristino tramite backup automatici
Come indicato in precedenza, oltre al portale di Azure, il ripristino di database può essere eseguito a livello di codice usando Azure PowerShell e l'API REST. Le tabelle seguenti descrivono il set di comandi disponibili.

### <a name="powershell"></a>PowerShell
| Cmdlet | Descrizione |
| --- | --- |
| [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/en-us/library/azure/mt603648.aspx) |Ottiene uno o più database. |
| [Get-AzureRMSqlDeletedDatabaseBackup](https://msdn.microsoft.com/en-us/library/azure/mt693387.aspx) |Ottiene un database eliminato che è possibile ripristinare. |
| [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388.aspx) |Ottiene una copia di backup con ridondanza geografica di un database. |
| [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx) |Ripristina un database SQL. |
|  | |

### <a name="rest-api"></a>API REST
| API | Descrizione |
| --- | --- |
| [REST (createMode=Recovery)](https://msdn.microsoft.com/library/azure/mt163685.aspx) |Ripristina un database |
| [Get Create or Update Database Status](https://msdn.microsoft.com/library/azure/mt643934.aspx) |Restituisce lo stato durante un'operazione di ripristino |
|  | |

## <a name="summary"></a>Riepilogo
I backup automatici proteggono i database da errori dell'utente e delle applicazioni, dall'eliminazione accidentale e da interruzioni prolungate. Questa soluzione a costo zero e senza l'intervento dell'amministratore è disponibile con tutti i database SQL. 

## <a name="next-steps"></a>Passaggi successivi
* Per la panoramica e gli scenari della continuità aziendale, vedere [Continuità aziendale del database SQL di Azure](sql-database-business-continuity.md)
* Per informazioni sui backup automatici del database SQL di Azure, vedere [Backup automatici del database SQL](sql-database-automated-backups.md)
* Per configurare la conservazione a lungo termine dei backup automatici nell'insieme di credenziali di Servizi di ripristino di Azure, vedere [Configurare la conservazione a lungo termine dei backup](sql-database-configure-long-term-retention.md)
* Per altre informazioni sulle opzioni di ripristino più veloci, vedere [Panoramica: Replica geografica attiva per il database SQL di Azure](sql-database-geo-replication-overview.md)  
* Per altre informazioni sull'uso dei backup automatici per l'archiviazione, vedere [Copiare un database SQL di Azure](sql-database-copy.md)



<!--HONumber=Dec16_HO2-->


