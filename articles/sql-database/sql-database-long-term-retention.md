---
title: Conservazione del backup a lungo termine
description: Informazioni sul supporto di Database SQL di Azure per l'archiviazione di backup completi per un totale di 10 anni tramite criteri di conservazione a lungo termine.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 05/18/2019
ms.openlocfilehash: 6fc5fab9ae61c8c8ade9260b32078ffa430b077f
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83771401"
---
# <a name="azure-sql-database-long-term-retention"></a>Conservazione a lungo termine del database SQL di Azure

Molte applicazioni sono vincolate da ragioni normative, di conformità o altri scopi aziendali che richiedono di conservare i backup del database oltre i 7-35 giorni offerti dai [backup automatici](sql-database-automated-backups.md) del database SQL di Azure. La funzionalità di conservazione a lungo termine consente di archiviare i backup completi dei database SQL specificati nell'archiviazione BLOB con ridondanza geografica e accesso in lettura per un totale di 10 anni. È quindi possibile ripristinare qualsiasi backup come nuovo database. Per altre informazioni sulla ridondanza di Archiviazione di Azure, vedere [Ridondanza dell'archiviazione](../storage/common/storage-redundancy.md). 

La conservazione a lungo termine può essere abilitata per database singoli e in pool ed è disponibile in anteprima pubblica limitata per le istanze gestite di Database SQL di Azure. 

> [!NOTE]
> È possibile usare processi di SQL Agent per pianificare [backup dei database di sola copia](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) come alternativa alla conservazione a lungo termine oltre 35 giorni.


## <a name="how-sql-database-long-term-retention-works"></a>Funzionamento della conservazione a lungo termine del database SQL

La conservazione dei backup a lungo termine consente di sfruttare i backup di database completi che vengono [creati automaticamente](sql-database-automated-backups.md) per abilitare il recupero temporizzato. Se è configurato un criterio di conservazione a lungo termine, questi backup vengono copiati in BLOB diversi. La copia è un processo in background che non ha alcun effetto sulle prestazioni del carico di lavoro del database. Il criterio di conservazione a lungo termine per ogni database SQL può anche specificare la frequenza con cui vengono creati i backup.

Per abilitare la conservazione a lungo termine, è possibile definire un criterio usando una combinazione di quattro parametri: conservazione backup settimanale (W), conservazione backup mensile (M), conservazione backup annuale (Y) e settimana dell'anno (WeekOfYear). Se si specifica W, verrà copiato nella risorsa di archiviazione a lungo termine un backup ogni settimana. Se si specifica M, il primo backup di ogni mese verrà copiato nell'archiviazione a lungo termine. Se si specifica Y, verrà copiato nella risorsa di archiviazione a lungo termine un backup nella settimana specificata dal valore WeekOfYear. Se il criterio WeekOfYear viene configurato nel passato, il primo backup per la conservazione a lungo termine verrà creato l'anno successivo. Ogni backup di conservazione a lungo termine verrà mantenuto nell'archiviazione a lungo termine in base ai parametri dei criteri configurati durante la creazione.

> [!NOTE]
> Qualsiasi modifica apportata ai criteri di conservazione a lungo termine si applica solo ai backup futuri. Se ad esempio si modifica la conservazione backup settimanale, la conservazione backup mensile (M) o la conservazione backup annuale (Y), la nuova impostazione di conservazione verrà applicata solo ai nuovi backup. La conservazione dei backup esistenti non verrà modificata. Se si intende eliminare i backup di conservazione a lungo termine obsoleti prima della scadenza del periodo di conservazione, sarà necessario [eliminarli manualmente](https://docs.microsoft.com/azure/sql-database/sql-database-long-term-backup-retention-configure#delete-ltr-backups).
> 

Esempi di criteri di conservazione a lungo termine:

-  W = 0, M = 0, Y = 5, WeekOfYear = 3

   Il terzo backup completo di ogni anno verrà conservato per cinque anni.
   
- W = 0, M = 3, Y = 0

   Il primo backup completo di ogni mese verrà conservato per tre mesi.

- W=12, M=0, Y=0

   Ogni backup completo settimanale verrà conservato per 12 settimane.

- W=6, M=12, Y=10, WeekOfYear=16

   Ogni backup completo settimanale verrà conservato per sei settimane, ad eccezione del primo backup completo di ogni mese, che verrà conservato per 12 mesi, e del backup completo eseguito la sedicesima settimana dell'anno, che verrà conservato per 10 anni. 

La tabella seguente illustra la cadenza e la scadenza dei backup a lungo termine per i criteri seguenti:

W=12 settimane (84 giorni), M=12 mesi (365 giorni), Y=10 anni (3650 giorni), WeekOfYear=15 (settimana dopo il 15 aprile)

   ![esempio di conservazione a lungo termine](./media/sql-database-long-term-retention/ltr-example.png)



Se si modifica il criterio precedente e si imposta W=0 (nessun backup settimanale), la cadenza delle copie di backup cambierà, come illustrato dalle date evidenziate nella tabella precedente. Lo spazio di archiviazione necessario per conservare questi backup si ridurrebbe di conseguenza. 

> [!IMPORTANT]
> La tempistica dei singoli backup di conservazione a lungo termine è controllata da Database SQL di Azure. Non è possibile creare manualmente un backup di conservazione a lungo termine o controllarne la tempistica della creazione. Dopo aver configurato un criterio di conservazione a lungo termine, la visualizzazione del primo backup nell'elenco di quelli disponibili può richiedere fino a 7 giorni.  
> 

## <a name="geo-replication-and-long-term-backup-retention"></a>Conservazione backup a lungo termine e replica geografica

Se si usano i gruppi di failover o di replica geografica attivi come soluzione di continuità aziendale, è necessario prepararsi per eventuali failover e configurare gli stessi criteri di conservazione a lungo termine nel database di replica geografica secondaria. Il costo dell'archiviazione per la conservazione a lungo termine non aumenterà, perché non vengono generati backup dai siti secondari. Solo quando il database secondario diventa primario viene creato il backup. In questo modo verrà garantita la generazione ininterrotta di backup di conservazione a lungo termine nel momento in cui viene attivato il failover e il database primario passa all'area secondaria. 

> [!NOTE]
> Quando viene ripristinato il database primario originale dopo l'interruzione del servizio che ha causato il failover, questo diventerà un nuovo database secondario. Pertanto, la creazione del backup non riprenderà e i criteri di conservazione a lungo termine esistenti non saranno attivi fino a quando il database diventerà nuovamente primario. 

## <a name="managed-instance-support"></a>Supporto di istanza gestita

L'uso della conservazione dei backup a lungo termine con istanze gestite di Database SQL di Azure presenta le limitazioni seguenti:

- **Anteprima pubblica limitata**: questa anteprima è riservata esclusivamente alle sottoscrizioni con contratto Enterprise e CSP ed è soggetta a disponibilità limitata.  
- [**Solo PowerShell**](sql-database-managed-instance-long-term-backup-retention-configure.md): il supporto per il portale di Azure non è attualmente disponibile. La conservazione a lungo termine deve essere abilitata tramite PowerShell. 

Per richiedere la registrazione, creare un [ticket di supporto di Azure](https://azure.microsoft.com/support/create-ticket/). Selezionare problema tecnico come tipo di richiesta, Istanza gestita di Database SQL come servizio e **backup, ripristino e continuità aziendale/conservazione dei backup a lungo termine** come tipo di problema. Nella richiesta indicare che si desidera registrarsi all'anteprima pubblica limitata della conservazione a lungo termine per l'istanza gestita.

## <a name="configure-long-term-backup-retention"></a>Configurare la conservazione del backup a lungo termine

Per informazioni su come configurare la conservazione a lungo termine con il portale di Azure o PowerShell, vedere [Gestire la conservazione dei backup a lungo termine di Database SQL di Azure](sql-database-long-term-backup-retention-configure.md).

## <a name="restore-database-from-ltr-backup"></a>Ripristinare il database dal backup di conservazione a lungo termine

Per ripristinare un database dalla risorsa di archiviazione per la conservazione a lungo termine, selezionare un backup specifico in base al relativo timestamp. Il database può essere ripristinato in qualsiasi server esistente all'interno della stessa sottoscrizione del database originale. Per informazioni su come ripristinare il database dal backup di conservazione a lungo termine, tramite il portale di Azure o PowerShell, vedere [Gestire la conservazione dei backup a lungo termine di Database SQL di Azure](sql-database-long-term-backup-retention-configure.md). Nella richiesta indicare che si desidera registrarsi all'anteprima pubblica limitata della conservazione a lungo termine per l'istanza gestita.

## <a name="next-steps"></a>Passaggi successivi

Poiché i backup dei database proteggono i dati da danneggiamenti o eliminazioni accidentali, sono una parte essenziale di qualsiasi strategia di continuità aziendale e ripristino di emergenza. Per informazioni sulle altre soluzioni di continuità aziendale del database SQL, vedere [Panoramica della continuità aziendale](sql-database-business-continuity.md).
