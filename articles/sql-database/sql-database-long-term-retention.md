---
title: Archiviare i backup del database SQL di Azure per un massimo di 10 anni | Microsoft Docs
description: Informazioni sul supporto dell'archiviazione dei backup completi del database per un massimo di 10 anni da parte di database SQL di Azure.
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
ms.openlocfilehash: b43097dee6a3b4e8ec762e193dc2faf006ec796c
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567764"
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>Archiviare i backup del database SQL di Azure per un massimo di 10 anni

Molte applicazioni sono vincolate da ragioni normative, di conformità o altri scopi aziendali che richiedono di conservare i backup del database oltre i 7-35 giorni offerti dai [backup automatici](sql-database-automated-backups.md) del database SQL di Azure. La funzionalità di conservazione a lungo termine consente di archiviare i backup completi del database SQL in una risorsa di archiviazione BLOB [RA-GRS](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) fino a un massimo di 10 anni. È quindi possibile ripristinare qualsiasi backup come nuovo database.

> [!NOTE]
> È possibile abilitare la conservazione a lungo termine per database singoli e in pool. Non è ancora disponibile per database dell'istanza in Istanze gestite. È possibile usare processi di SQL Agent per pianificare [backup dei database di sola copia](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) come alternativa alla conservazione a lungo termine oltre 35 giorni.
> 

## <a name="how-sql-database-long-term-retention-works"></a>Funzionamento della conservazione a lungo termine del database SQL

La conservazione dei backup a lungo termine consente di sfruttare i backup di database completi che vengono [creati automaticamente](sql-database-automated-backups.md) per abilitare il recupero temporizzato. Se è configurato un criterio LTR, questi backup vengono copiati in BLOB diversi per l'archiviazione a lungo termine. L'operazione di copia è un processo in background che non ha alcun effetto sulle prestazioni del carico di lavoro del database. I backup di LTR vengono conservati per un periodo di tempo impostato dal criterio LTR. Il criterio LTR per ogni database SQL può anche specificare la frequenza con cui vengono creati i backup di LTR. Per abilitare tale flessibilità è possibile definire i criteri utilizzando una combinazione di quattro parametri: conservazione backup settimanale (W), conservazione backup mensile (M), conservazione backup annuale (Y) e settimana dell'anno (WeekOfYear). Se si specifica W, verrà copiato nella risorsa di archiviazione a lungo termine un backup ogni settimana. Se si specifica M, verrà copiato nella risorsa di archiviazione a lungo termine un backup nella prima settimana di ogni mese. Se si specifica Y, verrà copiato nella risorsa di archiviazione a lungo termine un backup nella settimana specificata dal valore WeekOfYear. Ogni backup verrà conservato nella risorsa di archiviazione a lungo termine per il periodo specificato da questi parametri. Eventuali modifiche del criterio LTR si applicano ai backup futuri. Se, ad esempio, il WeekOfYear specificato si trova nel passato quando il criterio è configurato, il primo backup di LTR verrà creato l'anno successivo. 

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

## <a name="configure-long-term-backup-retention"></a>Configurare la conservazione del backup a lungo termine

Per informazioni su come configurare la conservazione a lungo termine usando il portale di Azure o PowerShell, vedere [gestire la conservazione dei backup a lungo termine del database SQL di Azure](sql-database-long-term-backup-retention-configure.md).

## <a name="restore-database-from-ltr-backup"></a>Ripristinare il database dal backup di LTR

Per ripristinare un database dalla risorsa di archiviazione per la conservazione a lungo termine, selezionare un backup specifico in base al relativo timestamp. Il database può essere ripristinato in qualsiasi server esistente all'interno della stessa sottoscrizione del database originale. Per informazioni su come ripristinare il database da un backup di LTR usando il portale di Azure o PowerShell, vedere [gestire la conservazione dei backup a lungo termine del database SQL di Azure](sql-database-long-term-backup-retention-configure.md).

## <a name="next-steps"></a>Passaggi successivi

Poiché i backup dei database proteggono i dati da danneggiamenti o eliminazioni accidentali, sono una parte essenziale di qualsiasi strategia di continuità aziendale e ripristino di emergenza. Per informazioni sulle altre soluzioni di continuità aziendale del database SQL, vedere [Panoramica della continuità aziendale](sql-database-business-continuity.md).
