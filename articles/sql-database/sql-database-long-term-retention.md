---
title: Conservazione del backup a lungo termine
description: Informazioni su come il database SQL di Azure supporta l'archiviazione di backup completi del database per un massimo di 10 anni tramite i criteri di conservazione a lungo termine.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 05/18/2019
ms.openlocfilehash: d015eea21bcfa499d6751e024a882a7316b7f1a5
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380751"
---
# <a name="azure-sql-database-long-term-retention"></a>Conservazione a lungo termine del database SQL di Azure

Molte applicazioni sono vincolate da ragioni normative, di conformità o altri scopi aziendali che richiedono di conservare i backup del database oltre i 7-35 giorni offerti dai [backup automatici](sql-database-automated-backups.md) del database SQL di Azure. Utilizzando la funzionalità di conservazione a lungo termine (LTR), è possibile archiviare i backup completi del database SQL specificati nell'archivio BLOB di Azure con archiviazione con ridondanza geografica di accesso in lettura per un massimo di 10 anni. È quindi possibile ripristinare qualsiasi backup come nuovo database. Per altre informazioni sulla ridondanza di Archiviazione di Azure, vedere [Ridondanza di Archiviazione di Azure.For](../storage/common/storage-redundancy.md)more information about Azure Storage redundancy, see Azure Storage redundancy . 

La conservazione a lungo termine può essere abilitata per i database singoli e in pool ed è in anteprima pubblica limitata per le istanze gestite del database SQL di Azure.Long time retention can be enabled for single and pooled databases, and is in limited public preview for Azure SQL Database managed instances. 

> [!NOTE]
> È possibile usare processi di SQL Agent per pianificare [backup dei database di sola copia](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) come alternativa alla conservazione a lungo termine oltre 35 giorni.


## <a name="how-sql-database-long-term-retention-works"></a>Funzionamento della conservazione a lungo termine del database SQL

La conservazione dei backup a lungo termine consente di sfruttare i backup di database completi che vengono [creati automaticamente](sql-database-automated-backups.md) per abilitare il recupero temporizzato. Se è configurato un criterio LTR, questi backup vengono copiati in BLOB diversi per l'archiviazione a lungo termine. La copia è un processo in background che non ha alcun impatto sulle prestazioni sul carico di lavoro del database. I criteri LTR per ogni database SQL possono inoltre specificare la frequenza con cui vengono creati i backup LTR.

Per abilitare LTR, è possibile definire un criterio utilizzando una combinazione di quattro parametri: conservazione settimanale del backup (W), conservazione mensile dei backup (M), conservazione del backup annuale (Y) e settimana dell'anno (WeekOfYear). Se si specifica W, verrà copiato nella risorsa di archiviazione a lungo termine un backup ogni settimana. Se si specifica M, il primo backup di ogni mese verrà copiato nell'archivio a lungo termine. Se si specifica Y, verrà copiato nella risorsa di archiviazione a lungo termine un backup nella settimana specificata dal valore WeekOfYear. Se l'oggetto WeekOfYear specificato è nell'intervallo precedente quando il criterio è configurato, il primo backup LTR verrà creato nell'anno successivo. Ogni backup verrà mantenuto nell'archivio a lungo termine in base ai parametri dei criteri configurati al momento della creazione del backup LTR.

> [!NOTE]
> Qualsiasi modifica ai criteri LTR si applica solo ai backup futuri. Ad esempio, se la conservazione settimanale dei backup (W), la conservazione mensile dei backup (M) o la conservazione annuale dei backup (Y) viene modificata, la nuova impostazione di conservazione verrà applicata solo ai nuovi backup. La conservazione dei backup esistenti non verrà modificata. Se l'intenzione è quella di eliminare i vecchi backup LTR prima della scadenza del periodo di conservazione, sarà necessario [eliminare manualmente i backup.](https://docs.microsoft.com/azure/sql-database/sql-database-long-term-backup-retention-configure#delete-ltr-backups)
> 

Esempi della politica LTR:

-  W = 0, M = 0, Y = 5, WeekOfYear = 3

   Il terzo backup completo di ogni anno sarà conservato per cinque anni.
   
- W = 0, M = 3, Y = 0

   Il primo backup completo di ogni mese sarà conservato per tre mesi.

- W=12, M=0, Y=0

   Ogni backup completo settimanale verrà conservato per 12 settimane.

- W=6, M=12, Y=10, WeekOfYear=16

   Ogni backup completo settimanale verrà conservato per sei settimane. ad eccezione del primo backup completo di ogni mese, che verrà conservato per 12 mesi, e del backup completo eseguito la sedicesima settimana dell'anno, che verrà conservato per 10 anni. 

La tabella seguente illustra la cadenza e la scadenza dei backup a lungo termine per i criteri seguenti:

W=12 settimane (84 giorni), M=12 mesi (365 giorni), Y=10 anni (3650 giorni), WeekOfYear=15 (settimana dopo il 15 aprile)

   ![esempio di conservazione a lungo termine](./media/sql-database-long-term-retention/ltr-example.png)



Se si modifica il criterio precedente e si imposta W-0 (nessun backup settimanale), la cadenza delle copie di backup cambierà come illustrato nella tabella precedente in base alle date evidenziate. Lo spazio di archiviazione necessario per conservare questi backup si ridurrebbe di conseguenza. 

> [!IMPORTANT]
> The timing of the individual LTR backups is controlled by Azure SQL Database. Non è possibile creare manualmente un backup LTR o controllare la temporizzazione della creazione del backup. Dopo aver configurato un criterio LTR, potrebbero essere necessarie fino a 7 giorni prima che il primo backup LTR venga visualizzato nell'elenco dei backup disponibili.  
> 

## <a name="geo-replication-and-long-term-backup-retention"></a>Conservazione backup a lungo termine e replica geografica

Se si utilizzano gruppi di replica geografica o di failover attivi come soluzione di continuità aziendale, è necessario prepararsi per eventuali failover e configurare gli stessi criteri LTR nel database geo-secondario. Il costo di archiviazione LTR non aumenterà in quanto i backup non vengono generati dai database secondari. Solo quando il database secondario diventa primario viene creato il backup. Garantisce la generazione non interrotta dei backup LTR quando viene attivato il failover e il database primario viene spostato nell'area secondaria. 

> [!NOTE]
> Quando il database primario originale esegue il ripristino da un'interruzione che ha causato il failover, diventerà un nuovo database secondario. Pertanto, la creazione del backup non riprenderà e i criteri di conservazione a lungo termine esistenti non saranno attivi fino a quando il database diventerà nuovamente primario. 

## <a name="managed-instance-support"></a>Supporto di istanza gestita

L'utilizzo della conservazione dei backup a lungo termine con un'istanza gestita di un database SQL di Azure presenta le limitazioni seguenti:Using long-term backup retention with an Azure SQL Database managed instances has the following limitations:

- **Anteprima pubblica limitata:** questa anteprima è disponibile solo per gli abbonamenti EA e CSP ed è soggetta a disponibilità limitata.  
- [**Solo PowerShell:**](sql-database-managed-instance-long-term-backup-retention-configure.md) attualmente non è supportato il portale di Azure.Only PowerShell- There is currently no Azure portal support. LTR must be enabled using PowerShell. 

Per richiedere la registrazione, creare un ticket di supporto di [Azure](https://azure.microsoft.com/support/create-ticket/) nell'argomento di supporto **Backup, ripristino e continuità aziendale/Conservazione**backup a lungo termine .


## <a name="configure-long-term-backup-retention"></a>Configurare la conservazione del backup a lungo termine

Per informazioni su come configurare la conservazione a lungo termine usando il portale di Azure o PowerShell, vedere Gestire la conservazione dei backup a lungo termine del database SQL di Azure.To learn how to configure long-term retention using the Azure portal or PowerShell, see [Manage Azure SQL Database long-term backup retention](sql-database-long-term-backup-retention-configure.md).

## <a name="restore-database-from-ltr-backup"></a>Ripristinare il database dal backup LTR

Per ripristinare un database dalla risorsa di archiviazione per la conservazione a lungo termine, selezionare un backup specifico in base al relativo timestamp. Il database può essere ripristinato in qualsiasi server esistente all'interno della stessa sottoscrizione del database originale. Per informazioni su come ripristinare il database da un backup LTR usando il portale di Azure o PowerShell, vedere Gestire la conservazione dei backup a lungo termine del database SQL di Azure.To learn how to restore your database from an LTR backup, using the Azure portal, or PowerShell, see [Manage Azure SQL Database long-term backup retention](sql-database-long-term-backup-retention-configure.md).

## <a name="next-steps"></a>Passaggi successivi

Poiché i backup dei database proteggono i dati da danneggiamenti o eliminazioni accidentali, sono una parte essenziale di qualsiasi strategia di continuità aziendale e ripristino di emergenza. Per informazioni sulle altre soluzioni di continuità aziendale del database SQL, vedere [Panoramica della continuità aziendale](sql-database-business-continuity.md).
