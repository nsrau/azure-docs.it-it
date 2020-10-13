---
title: Conservazione del backup a lungo termine
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Informazioni sul modo in cui il database SQL di Azure & Istanza gestita SQL di Azure supporta l'archiviazione di backup completi del database per un massimo di 10 anni tramite i criteri di conservazione a lungo termine.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 05/18/2019
ms.openlocfilehash: 83d3bb78ef27af377b0a8c5edf75f658a0ca93e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91450230"
---
# <a name="long-term-retention---azure-sql-database-and-azure-sql-managed-instance"></a>Conservazione a lungo termine: database SQL di Azure e Istanza gestita SQL di Azure

Molte applicazioni hanno scopi normativi, di conformità o altri scopi aziendali che richiedono di conservare i backup dei database oltre i 7-35 giorni forniti dal database SQL di Azure e da Azure SQL Istanza gestita [backup automatici](automated-backups-overview.md). Grazie alla funzionalità di conservazione a lungo termine, è possibile archiviare il database SQL specificato e i backup completi di SQL Istanza gestita nell'archiviazione BLOB di Azure con [ridondanza configurata](automated-backups-overview.md#backup-storage-redundancy) per un massimo di 10 anni. È quindi possibile ripristinare qualsiasi backup come nuovo database.

La conservazione a lungo termine può essere abilitata per il database SQL di Azure ed è in anteprima pubblica limitata per Istanza gestita SQL di Azure. Questo articolo fornisce una panoramica concettuale della conservazione a lungo termine. Per configurare la conservazione a lungo termine, vedere [configurare il database SQL di Azure LTR](long-term-backup-retention-configure.md) e [configurare Azure SQL istanza gestita LTR](../managed-instance/long-term-backup-retention-configure.md). 

> [!NOTE]
> È possibile usare processi di SQL Agent per pianificare [backup dei database di sola copia](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) come alternativa alla conservazione a lungo termine oltre 35 giorni.


## <a name="how-long-term-retention-works"></a>Funzionamento della conservazione a lungo termine
     
La conservazione dei backup a lungo termine consente di sfruttare i backup di database completi che vengono [creati automaticamente](automated-backups-overview.md) per abilitare il recupero temporizzato. Se è configurato un criterio di conservazione a lungo termine, questi backup vengono copiati in BLOB diversi. La copia è un processo in background che non ha alcun effetto sulle prestazioni del carico di lavoro del database. Il criterio LTR per ogni database nel database SQL può specificare anche la frequenza con cui vengono creati i backup di LTR.

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

   ![esempio di conservazione a lungo termine](./media/long-term-retention-overview/ltr-example.png)


Se si modifica il criterio precedente e si imposta W=0 (nessun backup settimanale), la cadenza delle copie di backup cambierà, come illustrato dalle date evidenziate nella tabella precedente. Lo spazio di archiviazione necessario per conservare questi backup si ridurrebbe di conseguenza. 

> [!IMPORTANT]
> Il tempo dei singoli backup di LTR è controllato da Azure. Non è possibile creare manualmente un backup di LTR o controllare l'intervallo di creazione del backup. Dopo aver configurato un criterio di conservazione a lungo termine, la visualizzazione del primo backup nell'elenco di quelli disponibili può richiedere fino a 7 giorni.  


## <a name="geo-replication-and-long-term-backup-retention"></a>Conservazione backup a lungo termine e replica geografica

Se si usa la replica geografica attiva o i gruppi di failover come soluzione di continuità aziendale, è necessario preparare i failover finali e configurare gli stessi criteri di LTR nel database o nell'istanza secondaria. Il costo di archiviazione di LTR non aumenterà perché i backup non vengono generati dai database secondari. I backup vengono creati solo quando il database secondario diventa primario. verranno creati i backup. In questo modo verrà garantita la generazione ininterrotta di backup di conservazione a lungo termine nel momento in cui viene attivato il failover e il database primario passa all'area secondaria. 

> [!NOTE]
> Quando viene ripristinato il database primario originale dopo l'interruzione del servizio che ha causato il failover, questo diventerà un nuovo database secondario. Pertanto, la creazione del backup non riprenderà e i criteri di conservazione a lungo termine esistenti non saranno attivi fino a quando il database diventerà nuovamente primario. 

## <a name="sql-managed-instance-support"></a>Supporto per Istanza gestita di SQL

L'uso della conservazione dei backup a lungo termine con Azure SQL Istanza gestita presenta le limitazioni seguenti:

- **Anteprima pubblica limitata**: questa anteprima è riservata esclusivamente alle sottoscrizioni con contratto Enterprise e CSP ed è soggetta a disponibilità limitata.  
- [**Solo PowerShell**](../managed-instance/long-term-backup-retention-configure.md) : attualmente non è disponibile alcun supporto per portale di Azure. La conservazione a lungo termine deve essere abilitata tramite PowerShell. 

Per richiedere la registrazione, creare un [ticket di supporto di Azure](https://azure.microsoft.com/support/create-ticket/). Per tipo di problema selezionare problema tecnico, per servizio scegliere SQL Istanza gestita e per il tipo di problema selezionare **backup, ripristino e continuità aziendale/conservazione dei backup a lungo termine**. Nella richiesta, indicare se si desidera essere registrati in un'anteprima pubblica limitata di LTR per SQL Istanza gestita.

## <a name="configure-long-term-backup-retention"></a>Configurare la conservazione del backup a lungo termine

È possibile configurare la conservazione dei backup a lungo termine usando il portale di Azure e PowerShell per il database SQL di Azure e PowerShell per Istanza gestita SQL di Azure. Per ripristinare un database dalla risorsa di archiviazione per la conservazione a lungo termine, selezionare un backup specifico in base al relativo timestamp. Il database può essere ripristinato in qualsiasi istanza gestita o server esistente nella stessa sottoscrizione del database originale.

Per informazioni su come configurare la conservazione a lungo termine o ripristinare un database dal backup per il database SQL usando il portale di Azure o PowerShell, vedere [gestire la conservazione dei backup a lungo termine del database SQL di Azure](long-term-backup-retention-configure.md) .

Per informazioni su come configurare la conservazione a lungo termine o ripristinare un database dal backup per SQL Istanza gestita tramite PowerShell, vedere [gestire la conservazione dei backup a lungo termine di Azure sql istanza gestita](../managed-instance/long-term-backup-retention-configure.md).

Per ripristinare un database dalla risorsa di archiviazione per la conservazione a lungo termine, selezionare un backup specifico in base al relativo timestamp. Il database può essere ripristinato in qualsiasi server esistente all'interno della stessa sottoscrizione del database originale. Per informazioni su come ripristinare il database dal backup di conservazione a lungo termine, tramite il portale di Azure o PowerShell, vedere [Gestire la conservazione dei backup a lungo termine di Database SQL di Azure](long-term-backup-retention-configure.md). Nella richiesta, indicare lo stato che si vuole registrare nell'anteprima pubblica limitata di LTR per SQL Istanza gestita.

## <a name="next-steps"></a>Passaggi successivi

Poiché i backup dei database proteggono i dati da danneggiamenti o eliminazioni accidentali, sono una parte essenziale di qualsiasi strategia di continuità aziendale e ripristino di emergenza. Per informazioni sulle altre soluzioni di continuità aziendale del database SQL, vedere [Panoramica della continuità aziendale](business-continuity-high-availability-disaster-recover-hadr-overview.md).
 
