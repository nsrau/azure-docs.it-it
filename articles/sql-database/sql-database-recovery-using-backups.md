---
title: Ripristinare un database SQL di Azure da un backup | Microsoft Docs
description: Informazioni sul ripristino temporizzato, che consente di ripristinare un database SQL di Azure a un momento precedente (fino a 35 giorni).
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: monicar
ms.assetid: fd1d334d-a035-4a55-9446-d1cf750d9cf7
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/25/2017
ms.author: carlrab
ms.translationtype: HT
ms.sourcegitcommit: 48dfc0fa4c9ad28c4c64c96ae2fc8a16cd63865c
ms.openlocfilehash: df6e4bba9290c6129c9cba1440bb0c903aacc3c8
ms.contentlocale: it-it
ms.lasthandoff: 08/30/2017

---
# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>Ripristinare un database SQL di Azure mediante i backup automatici del database
Il database SQL prevede queste opzioni per il ripristino del database mediante [backup automatici del database](sql-database-automated-backups.md) e [backup nella conservazione a lungo termine](sql-database-long-term-retention.md). È possibile ripristinare un backup di database in:

* Un nuovo database nello stesso server logico ripristinato in una temporizzazione specificata entro il periodo di conservazione. 
* Un database nello stesso server logico ripristinato all'ora di eliminazione per un database eliminato.
* Un nuovo database in qualsiasi server logico in qualsiasi area ripristinato ai backup giornalieri più recenti nell'archiviazione BLOB con replica geografica (RA-GRS).

> [!IMPORTANT]
> Non è possibile sovrascrivere un database esistente durante il ripristino.
>

Un database ripristinato comporta un costo di archiviazione aggiuntivo nei segeunti casi: 
- Ripristino di P11 – P15 in S4 S12 o P1 – P6 se le dimensioni massime del database sono maggiori di 500 GB.
- Ripristino di P1–P6 o PRS1–PRS6 in S4-S12 se le dimensioni massime del database sono maggiori di 250 GB.

Viene addebitato un costo aggiuntivo perché la dimensione massima del database ripristinato è superiore alla dimensione di archiviazione inclusa per il livello di prestazioni; vengono applicati costi aggiuntivi per l'archiviazione aggiuntiva di cui viene eseguito il provisioning che supera la dimensione inclusa.  Per i dettagli dei prezzi delle risorse di archiviazione aggiuntive, vedere la [pagina dei prezzi del database SQL](https://azure.microsoft.com/pricing/details/sql-database/).  Se la quantità effettiva di spazio usato è inferiore alla quantità inclusa di risorse di archiviazione, questo costo aggiuntivo può essere evitato riducendo le dimensioni massime del database fino alla quantità inclusa. Per altre informazioni sulle dimensioni di archiviazione del database e la modifica delle dimensioni massime del database, vedere [limiti delle risorse per il database singolo](sql-database-resource-limits.md#single-database-storage-sizes-and-performance-levels).  

> [!NOTE]
> [I backup di database automatici](sql-database-automated-backups.md) vengono usati quando si crea un [copia del database](sql-database-copy.md). 
>


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

È possibile ripristinare un database esistente a un punto specifico nel tempo come un nuovo database nello stesso server logico tramite il portale di Azure, [PowerShell](https://docs.microsoft.com/en-us/powershell/module/azurerm.sql/restore-azurermsqldatabase) o l'[API REST](https://msdn.microsoft.com/library/azure/mt163685.aspx). 

> [!TIP]
> Per uno script di PowerShell di esempio che illustra come eseguire un ripristino temporizzato di un database, vedere [Ripristinare un database SQL tramite PowerShell](scripts/sql-database-restore-database-powershell.md).
>

Il database può essere ripristinato a qualsiasi livello di prestazioni o di servizio, come database singolo o in un pool elastico. Assicurarsi di disporre di risorse sufficienti sul server logico o nel pool elastico in cui si sta ripristinando il database. Al termine, il database ripristinato è un normale database online completamente accessibile alle tariffe normali in base al livello di prestazioni e di servizio. Non è previsto alcun addebito fino al completamento del ripristino del database.

In genere si ripristina un database a un punto precedente per scopi di ripristino. Quando si esegue questa operazione, è possibile considerare il database ripristinato come una sostituzione per il database originale o usarlo per recuperare i dati e quindi aggiornare il database originale. 

* ***Sostituzione del database***: se il database ripristinato è da intendersi come una sostituzione per il database originale, è necessario verificare che il livello di prestazioni e/o il livello di servizio sia appropriato e ridimensionare il database se necessario. È possibile rinominare il database originale e quindi assegnare al database ripristinato il nome originale usando il comando ALTER DATABASE in T-SQL. 
* ***Ripristino dei dati***: se si prevede di recuperare dati dal database ripristinato in caso di errore dell'applicazione o dell'utente, è necessario scrivere ed eseguire gli script di ripristino per estrarre i dati dal database ripristinato e inserirli nel database originale. Anche se il completamento dell'operazione di ripristino può richiedere molto tempo, il database in fase di ripristino è visibile nell'elenco dei database per tutto il processo. Se si elimina il database durante il ripristino, l'operazione verrà annullata e non verrà addebitata per il database il cui ripristino non è stato completato. 

### <a name="azure-portal"></a>Portale di Azure

Per eseguire il ripristino a un determinato momento nel tempo tramite il portale di Azure, aprire la pagina per il database e fare clic su **Ripristina** nella barra degli strumenti.

![ripristino a un determinato momento nel tempo](./media/sql-database-recovery-using-backups/point-in-time-recovery.png)

## <a name="deleted-database-restore"></a>Ripristino di un database eliminato
Il ripristino di un database eliminato consente di ripristinare un database all'ora di eliminazione per un database eliminato sullo stesso server logico, usando il portale di Azure, [PowerShell](https://docs.microsoft.com/en-us/powershell/module/azurerm.sql/restore-azurermsqldatabase) o l'[API REST (createMode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx). 

> [!TIP]
> Per uno script di PowerShell di esempio che illustra come ripristinare un database eliminato, vedere [Ripristinare un database SQL tramite PowerShell](scripts/sql-database-restore-database-powershell.md).
>

> [!IMPORTANT]
> Se si elimina un'istanza del server di database SQL di Azure, anche tutti i database relativi vengono eliminati e non possono essere recuperati. Non è attualmente disponibile alcun supporto per il ripristino di un server eliminato.
> 

### <a name="azure-portal"></a>Portale di Azure

Per ripristinare un database eliminato durante il [periodo di conservazione](sql-database-service-tiers.md) tramite il portale di Azure, aprire la pagina per il server e nell'area Operazioni fare clic su **Database eliminati**.

![deleted-database-restore-1](./media/sql-database-recovery-using-backups/deleted-database-restore-1.png)


![deleted-database-restore-2](./media/sql-database-recovery-using-backups/deleted-database-restore-2.png)

## <a name="geo-restore"></a>Ripristino geografico
Il ripristino geografico consente di ripristinare un database SQL presente su un server in qualsiasi area di Azure usando gli ultimi backup completi e differenziali con replica geografica. Il ripristino geografico usa un backup con ridondanza geografica come origine e può essere usato per ripristinare un database anche se il database o il data center è inaccessibile a causa di un'interruzione del servizio. 

Il ripristino geografico è l'opzione di ripristino predefinita quando il database non è disponibile a causa di un evento imprevisto nell'area in cui è ospitato. Se si verifica un evento imprevisto su larga scala che determina la mancata disponibilità dell'applicazione di database, è possibile ripristinare un database dai backup con replica geografica in un server in un'altra area. Esiste un ritardo tra il momento in cui un backup differenziale viene creato e quando ne viene eseguita la replica geografica in un BLOB di Azure in un'area diversa. Questo ritardo può essere al massimo di un'ora, quindi, in caso di emergenza, può verificarsi una perdita massima di un'ora di dati. La figura seguente mostra il ripristino del database dall'ultimo backup disponibile in un'altra area.

![Ripristino geografico](./media/sql-database-geo-restore/geo-restore-2.png)

> [!TIP]
> Per uno script di PowerShell di esempio che illustra come eseguire un ripristino geografico, vedere [Ripristinare un database SQL tramite PowerShell](scripts/sql-database-restore-database-powershell.md).
> 

Il ripristino temporizzato in un database di replica geografica secondaria non è attualmente supportato. Il ripristino temporizzato può essere eseguito solo in un database primario. Per informazioni dettagliate sull'uso del ripristino geografico in caso di un'interruzione del servizio, vedere [Ripristinare un database SQL di Azure o eseguire il failover in un database secondario](sql-database-disaster-recovery.md).

> [!IMPORTANT]
> Il ripristino da backup è la più semplice delle soluzioni di ripristino di emergenza disponibili nel database SQL con il massimo valore per RPO e tempo di recupero stimato. Per soluzioni che usano i database Basic, il ripristino geografico è spesso una soluzione ragionevole di ripristino di emergenza con un ERT di 12 ore. Per soluzioni che usano i database Standard o Premium di dimensioni maggiori, che richiedono tempi di ripristino inferiori, è consigliabile usare la [replica geografica attiva](sql-database-geo-replication-overview.md). La replica geografica attiva offre un obiettivo del punto di ripristino e un tempo di recupero stimato decisamente inferiori perché richiede solo l'avvio di un failover in un database secondario con replica continua. Per altre informazioni sulle opzioni di continuità aziendale, vedere [Overview of business continuity](sql-database-business-continuity.md) (Panoramica sulla continuità aziendale).
> 

### <a name="azure-portal"></a>Portale di Azure

Per geo-ripristinare un database durante il [periodo di conservazione](sql-database-service-tiers.md) tramite il portale di Azure, aprire la pagina Database SQL e quindi fare clic su **Aggiungi**. Nel casella di testo **Seleziona origine** selezionare **Backup**. Specificare il backup da cui eseguire il ripristino nell'area e nel server di propria scelta. 

## <a name="programmatically-performing-recovery-using-automated-backups"></a>Esecuzione a livello di codice del ripristino tramite backup automatici
Come indicato in precedenza, oltre al portale di Azure, il ripristino di database può essere eseguito a livello di codice usando Azure PowerShell o l'API REST. Le tabelle seguenti descrivono il set di comandi disponibili.

### <a name="powershell"></a>PowerShell
| Cmdlet | Descrizione |
| --- | --- |
| [Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase) |Ottiene uno o più database. |
| [Get-AzureRMSqlDeletedDatabaseBackup](/powershell/module/azurerm.sql/get-azurermsqldeleteddatabasebackup) | Ottiene un database eliminato che è possibile ripristinare. |
| [Get-AzureRmSqlDatabaseGeoBackup](/powershell/module/azurerm.sql/get-azurermsqldatabasegeobackup) |Ottiene una copia di backup con ridondanza geografica di un database. |
| [Restore-AzureRmSqlDatabase](/powershell/module/azurerm.sql/restore-azurermsqldatabase) |Ripristina un database SQL. |
|  | |

### <a name="rest-api"></a>API REST
| API | Descrizione |
| --- | --- |
| [REST (createMode=Recovery)](https://msdn.microsoft.com/library/azure/mt163685.aspx) |Ripristina un database |
| [Get Create or Update Database Status](https://msdn.microsoft.com/library/azure/mt643934.aspx) |Restituisce lo stato durante un'operazione di ripristino |
|  | |

## <a name="summary"></a>Riepilogo
I backup automatici proteggono i database da errori dell'utente e delle applicazioni, dall'eliminazione accidentale e da interruzioni prolungate. Questa funzionalità incorporata è disponibile per tutti i livelli di servizio e di prestazioni. 

## <a name="next-steps"></a>Passaggi successivi
* Per la panoramica e gli scenari della continuità aziendale, vedere [Continuità aziendale del database SQL di Azure](sql-database-business-continuity.md)
* Per informazioni sui backup automatici del database SQL di Azure, vedere [Backup automatici del database SQL](sql-database-automated-backups.md)
* Per altre informazioni, vedere l'articolo sulla [conservazione dei backup a lungo termine](sql-database-long-term-retention.md)
* Per la configurazione, la gestione e il ripristino dalla conservazione a lungo termine di backup automatici in un insieme di credenziali di Servizi di ripristino di Azure tramite il portale di Azure, vedere l'articolo [Configurare e ripristinare dalla conservazione dei backup a lungo termine del database SQL di Azure](sql-database-long-term-backup-retention-configure.md). 
* Per altre informazioni sulle opzioni di ripristino più veloci, vedere [Panoramica: Replica geografica attiva per il database SQL di Azure](sql-database-geo-replication-overview.md).  

