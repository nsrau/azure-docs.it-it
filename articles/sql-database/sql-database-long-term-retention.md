---
title: Archiviare i backup del database SQL di Azure per un massimo di 10 anni | Microsoft Docs
description: Informazioni sul supporto dell'archiviazione dei backup completi del database per un massimo di 10 anni da parte di database SQL di Azure.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.date: 05/17/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: ce78201e3f87b9687ced181f90d352d73aa29431
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2018
ms.locfileid: "34366063"
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>Archiviare i backup del database SQL di Azure per un massimo di 10 anni

Molte applicazioni sono vincolate da ragioni normative, di conformità o altri scopi aziendali che richiedono di conservare i backup del database oltre i 7-35 giorni offerti dai [backup automatici](sql-database-automated-backups.md) del database SQL di Azure. La funzionalità di conservazione a lungo termine consente di archiviare i backup completi del database SQL in una risorsa di archiviazione BLOB [RA-GRS](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) fino a un massimo di 10 anni. È quindi possibile ripristinare qualsiasi backup come nuovo database.

> [!IMPORTANT]
> La conservazione a lungo termine è attualmente in fase di anteprima. Per i backup esistenti archiviati nell'insieme di credenziali di Servizi di ripristino di Azure come parte della precedente anteprima della funzionalità viene eseguita la migrazione all'archiviazione di SQL Azure.<!-- and available in the following regions: Australia East, Australia Southeast, Brazil South, Central US, East Asia, East US, East US 2, India Central, India South, Japan East, Japan West, North Central US, North Europe, South Central US, Southeast Asia, West Europe, and West US.-->
>

## <a name="how-sql-database-long-term-retention-works"></a>Funzionamento della conservazione a lungo termine del database SQL

La conservazione dei backup a lungo termine sfrutta i [backup automatici dei database SQL](sql-database-automated-backups.md) creati per il ripristino temporizzato. È possibile configurare criteri di conservazione a lungo termine per ogni database SQL e specificare la frequenza con cui è necessario copiare i backup nell'archiviazione a lungo termine. Per abilitare tale flessibilità, è possibile definire i criteri usando una combinazione di quattro parametri: conservazione backup settimanale (W), conservazione backup mensile (M), conservazione backup annuale (Y) e settimana dell'anno (WeekOfYear). Se si specifica W, verrà copiato nella risorsa di archiviazione a lungo termine un backup ogni settimana. Se si specifica M, verrà copiato nella risorsa di archiviazione a lungo termine un backup nella prima settimana di ogni mese. Se si specifica Y, verrà copiato nella risorsa di archiviazione a lungo termine un backup nella settimana specificata dal valore WeekOfYear. Ogni backup verrà conservato nella risorsa di archiviazione a lungo termine per il periodo specificato da questi parametri. 

Esempi:

-  W = 0, M = 0, Y = 5, WeekOfYear = 3

   Il terzo backup completo di ogni anno verrà conservato per 5 anni.

- W = 0, M = 3, Y = 0

   Il primo backup completo di ogni mese verrà conservato per 3 mesi.

- W=12, M=0, Y=0

   Ogni backup completo settimanale verrà conservato per 12 settimane.

- W=6, M=12, Y=10, WeekOfYear=16

   Ogni backup completo settimanale verrà conservato per 6 settimane, ad eccezione del primo backup completo di ogni mese, che verrà conservato per 12 mesi, e del backup completo eseguito la sedicesima settimana dell'anno, che verrà conservato per 10 anni. 

La tabella seguente illustra la cadenza e la scadenza dei backup a lungo termine per i criteri seguenti:

W=12 settimane (84 giorni), M=12 mesi (365 giorni), Y=10 anni (3650 giorni), WeekOfYear=15 (settimana dopo il 15 aprile)

   ![esempio di conservazione a lungo termine](./media/sql-database-long-term-retention/ltr-example.png)


 
Se fosse necessario modificare i criteri sopra riportati e impostare W=0 (nessun backup settimanale), la cadenza delle copie di backup cambierebbe, come illustrato dalle date evidenziate nella tabella precedente. La quantità di archiviazione necessaria per conservare questi backup si ridurrebbe di conseguenza. 

> [!NOTE]
1. Le copie per la conservazione a lungo termine vengono create dal servizio di archiviazione di Azure in modo che il processo di copia non abbia alcun impatto sulle prestazioni del database esistente.
2. Il criterio si applica ai backup futuri. ad esempio Se il criterio WeekOfYear specificato è nel passato quando il viene configurato, il primo backup per la conservazione a lungo termine verrà creato l'anno successivo. 
3. Per ripristinare un database dalla risorsa di archiviazione per la conservazione a lungo termine, selezionare un backup specifico in base al relativo timestamp.   Il database può essere ripristinato in qualsiasi server esistente all'interno della stessa sottoscrizione del database originale. 
> 

## <a name="configure-long-term-backup-retention"></a>Configurare la conservazione del backup a lungo termine

Per informazioni sulla configurazione della conservazione a lungo termine tramite il portale di Azure o PowerShell, vedere [Configurare la conservazione dei backup a lungo termine](sql-database-long-term-backup-retention-configure.md).

## <a name="next-steps"></a>Passaggi successivi

Poiché i backup dei database proteggono i dati da danneggiamenti o eliminazioni accidentali, sono una parte essenziale di qualsiasi strategia di continuità aziendale e ripristino di emergenza. Per informazioni sulle altre soluzioni di continuità aziendale del database SQL, vedere [Panoramica della continuità aziendale](sql-database-business-continuity.md).
