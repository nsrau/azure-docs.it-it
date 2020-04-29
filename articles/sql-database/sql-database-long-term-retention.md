---
title: Conservazione del backup a lungo termine
description: Informazioni sul modo in cui il database SQL di Azure supporta l'archiviazione di backup completi del database per un massimo di 10 anni tramite i criteri di conservazione a lungo termine.
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
ms.openlocfilehash: e85c8c0990a2659dba1f254a8f1aa7c7be7852eb
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82508089"
---
# <a name="azure-sql-database-long-term-retention"></a>Conservazione a lungo termine del database SQL di Azure

Molte applicazioni sono vincolate da ragioni normative, di conformità o altri scopi aziendali che richiedono di conservare i backup del database oltre i 7-35 giorni offerti dai [backup automatici](sql-database-automated-backups.md) del database SQL di Azure. Usando la funzionalità di conservazione a lungo termine, è possibile archiviare i backup completi del database SQL specificati nell'archivio BLOB di Azure con archiviazione con ridondanza geografica e accesso in lettura per un massimo di 10 anni. È quindi possibile ripristinare qualsiasi backup come nuovo database. Per altre informazioni sulla ridondanza di archiviazione di Azure, vedere [ridondanza di archiviazione di Azure](../storage/common/storage-redundancy.md). 

La conservazione a lungo termine può essere abilitata per i database singoli e in pool ed è in anteprima pubblica limitata per le istanze gestite del database SQL di Azure. 

> [!NOTE]
> È possibile usare processi di SQL Agent per pianificare [backup dei database di sola copia](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) come alternativa alla conservazione a lungo termine oltre 35 giorni.


## <a name="how-sql-database-long-term-retention-works"></a>Funzionamento della conservazione a lungo termine del database SQL

La conservazione dei backup a lungo termine consente di sfruttare i backup di database completi che vengono [creati automaticamente](sql-database-automated-backups.md) per abilitare il recupero temporizzato. Se è configurato un criterio LTR, questi backup vengono copiati in BLOB diversi per l'archiviazione a lungo termine. La copia è un processo in background che non ha alcun effetto sulle prestazioni del carico di lavoro del database. Il criterio LTR per ogni database SQL può anche specificare la frequenza con cui vengono creati i backup di LTR.

Per abilitare LTR, è possibile definire un criterio utilizzando una combinazione di quattro parametri: conservazione backup settimanale (W), conservazione backup mensile (M), conservazione backup annuale (Y) e settimana dell'anno (WeekOfYear). Se si specifica W, verrà copiato nella risorsa di archiviazione a lungo termine un backup ogni settimana. Se si specifica M, il primo backup di ogni mese verrà copiato nell'archiviazione a lungo termine. Se si specifica Y, verrà copiato nella risorsa di archiviazione a lungo termine un backup nella settimana specificata dal valore WeekOfYear. Se il WeekOfYear specificato si trova nel passato quando il criterio è configurato, il primo backup di LTR verrà creato nell'anno seguente. Ogni backup verrà mantenuto nell'archiviazione a lungo termine in base ai parametri dei criteri configurati durante la creazione del backup di LTR.

> [!NOTE]
> Qualsiasi modifica ai criteri LTR si applica solo ai backup futuri. Se ad esempio si modifica la conservazione dei backup settimanali, la conservazione mensile dei backup (M) o la conservazione annuale dei backup (Y), la nuova impostazione di conservazione verrà applicata solo ai nuovi backup. La conservazione dei backup esistenti non verrà modificata. Se si intende eliminare i backup di LTR obsoleti prima della scadenza del periodo di conservazione, sarà necessario [eliminare manualmente i backup](https://docs.microsoft.com/azure/sql-database/sql-database-long-term-backup-retention-configure#delete-ltr-backups).
> 

Esempi di criteri di LTR:

-  W = 0, M = 0, Y = 5, WeekOfYear = 3

   Il terzo backup completo di ogni anno verrà mantenuto per cinque anni.
   
- W = 0, M = 3, Y = 0

   Il primo backup completo di ogni mese verrà mantenuto per tre mesi.

- W=12, M=0, Y=0

   Ogni backup completo settimanale verrà conservato per 12 settimane.

- W=6, M=12, Y=10, WeekOfYear=16

   Ogni backup completo settimanale verrà mantenuto per sei settimane. ad eccezione del primo backup completo di ogni mese, che verrà conservato per 12 mesi, e del backup completo eseguito la sedicesima settimana dell'anno, che verrà conservato per 10 anni. 

La tabella seguente illustra la cadenza e la scadenza dei backup a lungo termine per i criteri seguenti:

W=12 settimane (84 giorni), M=12 mesi (365 giorni), Y=10 anni (3650 giorni), WeekOfYear=15 (settimana dopo il 15 aprile)

   ![esempio di conservazione a lungo termine](./media/sql-database-long-term-retention/ltr-example.png)



Se si modificano i criteri precedenti e si imposta W = 0 (nessun backup settimanale), la cadenza delle copie di backup cambierà come illustrato nella tabella precedente dalle date evidenziate. Lo spazio di archiviazione necessario per conservare questi backup si ridurrebbe di conseguenza. 

> [!IMPORTANT]
> La tempistica dei singoli backup di LTR è controllata dal database SQL di Azure. Non è possibile creare manualmente un backup di LTR o controllare l'intervallo di creazione del backup. Dopo aver configurato un criterio LTR, potrebbero essere importati fino a 7 giorni prima che il primo backup di LTR venga visualizzato nell'elenco dei backup disponibili.  
> 

## <a name="geo-replication-and-long-term-backup-retention"></a>Conservazione backup a lungo termine e replica geografica

Se si usa la replica geografica attiva o i gruppi di failover come soluzione di continuità aziendale, è necessario prepararsi per eventuali failover e configurare gli stessi criteri di LTR nel database di replica geografica secondaria. Il costo di archiviazione di LTR non aumenterà in quanto i backup non verranno generati dai database secondari. Solo quando il database secondario diventa primario viene creato il backup. Assicura la generazione senza interruzioni dei backup di LTR quando viene attivato il failover e il database primario si sposta nell'area secondaria. 

> [!NOTE]
> Quando il database primario originale viene ripristinato da un'interruzione che ha causato il failover, diventerà un nuovo database secondario. Pertanto, la creazione del backup non riprenderà e i criteri di conservazione a lungo termine esistenti non saranno attivi fino a quando il database diventerà nuovamente primario. 

## <a name="managed-instance-support"></a>Supporto di istanza gestita

L'uso della conservazione dei backup a lungo termine con istanze gestite di un database SQL di Azure presenta le limitazioni seguenti:

- **Anteprima pubblica limitata** : questa anteprima è disponibile solo per le sottoscrizioni EA e CSP ed è soggetta a disponibilità limitata.  
- [**Solo PowerShell**](sql-database-managed-instance-long-term-backup-retention-configure.md) : attualmente non è disponibile alcun supporto portale di Azure. Il LTR deve essere abilitato tramite PowerShell. 

Per richiedere la registrazione, creare un [ticket di supporto di Azure](https://azure.microsoft.com/support/create-ticket/). Per tipo di problema selezionare problema tecnico, per servizio scegliere Istanza gestita di database SQL e per il tipo di problema selezionare **backup, ripristino e continuità aziendale/conservazione dei backup a lungo termine**. Nella richiesta, indicare se si desidera essere registrati in un'anteprima pubblica limitata di LTR per l'istanza gestita.

## <a name="configure-long-term-backup-retention"></a>Configurare la conservazione del backup a lungo termine

Per informazioni su come configurare la conservazione a lungo termine usando il portale di Azure o PowerShell, vedere [gestire la conservazione dei backup a lungo termine del database SQL di Azure](sql-database-long-term-backup-retention-configure.md).

## <a name="restore-database-from-ltr-backup"></a>Ripristinare il database dal backup di LTR

Per ripristinare un database dalla risorsa di archiviazione per la conservazione a lungo termine, selezionare un backup specifico in base al relativo timestamp. Il database può essere ripristinato in qualsiasi server esistente all'interno della stessa sottoscrizione del database originale. Per informazioni su come ripristinare il database da un backup di LTR, usando il portale di Azure o PowerShell, vedere [gestire la conservazione dei backup a lungo termine del database SQL di Azure](sql-database-long-term-backup-retention-configure.md). Nella richiesta, indicare lo stato che si vuole registrare nell'anteprima pubblica limitata di LTR per istanza gestita.

## <a name="next-steps"></a>Passaggi successivi

Poiché i backup dei database proteggono i dati da danneggiamenti o eliminazioni accidentali, sono una parte essenziale di qualsiasi strategia di continuità aziendale e ripristino di emergenza. Per informazioni sulle altre soluzioni di continuità aziendale del database SQL, vedere [Panoramica della continuità aziendale](sql-database-business-continuity.md).
