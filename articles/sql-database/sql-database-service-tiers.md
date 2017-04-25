---
title: 'Prestazioni del database SQL: livelli di servizio | Microsoft Docs'
description: Confrontare i livelli di servizio del database SQL.
keywords: opzioni di database,prestazioni del database
services: sql-database
documentationcenter: 
author: janeng
manager: jhubbard
editor: 
ms.assetid: f5c5c596-cd1e-451f-92a7-b70d4916e974
ms.service: sql-database
ms.custom: resources
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
wms.date: 03/06/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 85b7336958c90b477eea8ea185a69bab2bd87a79
ms.lasthandoff: 04/15/2017


---
# <a name="sql-database-options-and-performance-understand-whats-available-in-each-service-tier"></a>Opzioni e prestazioni disponibili in ogni livello di servizio del database SQL

Il [database SQL di Azure](sql-database-technical-overview.md) offre quattro livelli di servizio: **Basic**, **Standard**, **Premium** e **Premium RS**. Ognuno offre diversi livelli di prestazioni per la gestione di carichi di lavoro differenti. Livelli di prestazioni più elevati offrono risorse aggiuntive progettate per garantire un aumento della velocità effettiva. È possibile cambiare i livelli di servizio e di prestazioni in modo dinamico senza tempi di inattività. I livelli di servizio Basic, Standard, Premium e Premium RS garantiscono un tempo di attività previsto dal contratto di servizio del 99,99% e opzioni di continuità aziendale flessibili, funzionalità di sicurezza e fatturazione su base oraria. Il livello di servizio Premium RS offre gli stessi livelli di prestazioni e le stesse funzionalità di sicurezza e di continuità aziendale del livello Premium, anche se con un contratto di servizio ridotto.

> [!IMPORTANT]
> I database Premium RS vengono eseguiti con un numero inferiore di copie ridondanti rispetto ai database Premium o Standard. In caso di errore del servizio, quindi, potrebbe essere necessario recuperare il database da un backup con un ritardo massimo di 5 minuti.
>

È possibile creare database singoli con risorse dedicate in un livello di servizio, con un [livello di prestazioni](sql-database-service-tiers.md#single-database-service-tiers-and-performance-levels) specifico. È anche possibile creare database in un [pool elastico](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus) in cui le risorse sono condivise tra più database. Le risorse disponibili per i database singoli sono espresse in unità di transazione di database (DTU), quelle per i pool elastici sono espresse in unità di transazione di database elastico (eDTU). Per altre informazioni sulle DTU e le eDTU, vedere [Informazioni sulle DTU](sql-database-what-is-a-dtu.md). 

## <a name="choosing-a-service-tier"></a>Scelta di un piano di servizio
Nella tabella seguente sono riportati esempi dei livelli ottimali adatti ai carichi di lavoro di diverse applicazioni.

| Livello di servizio | Carichi di lavoro di destinazione |
| :--- | --- |
| **Basic** | Più adatto per un database di piccole dimensioni, che supporta in genere una singola operazione attiva in un determinato momento. Ad esempio, database usati per lo sviluppo o i test oppure applicazioni su scala ridotta usate raramente. |
| **Standard** |Opzione per le applicazioni cloud con requisiti di prestazioni I/O bassi o medi, supporto di più query contemporaneamente. Ad esempio, applicazioni web o per gruppi di lavoro. |
| **Premium** | Progettato per volumi di transazioni elevati con alti requisiti di prestazioni I/O che supportano più utenti contemporaneamente. Ad esempio, database che supportano applicazioni mission-critical. |
| **Premium RS** | Progettato per carichi di lavoro con un numero elevato di operazioni di I/O che non richiedono una garanzia di disponibilità massima. Gli esempi includono test di carichi di lavoro ad alte prestazioni e carichi di lavoro di analisi in cui il database non è il sistema di registrazione. |
|||

Prima di tutto è necessario decidere se eseguire un database singolo con una quantità specifica di risorse dedicate o condividere un pool di risorse in un gruppo di database. Fare riferimento alle [considerazioni sul pool elastico](sql-database-elastic-pool-guidance.md). Per decidere il livello di servizio da usare, stabilire innanzitutto le funzionalità minime necessarie del database:

| **Funzionalità del livello di servizio** | **Basic** | **Standard** | **Premium** | **Premium RS**|
| :-- | --: | --: | --: | --: |
| Dimensioni massime del singolo database | 2 GB | 250 GB | 4 TB*  | 500 GB  |
| Spazio di archiviazione totale massimo in un pool elastico | 117 GB | 1200 GB | 750 GB | 750 GB |
| Numero massimo di database per pool | 400  | 400 | 50 | 50 |
| Periodo di conservazione dei backup dei database | 7 giorni | 35 giorni | 35 giorni | 35 giorni |
||||||

> [!IMPORTANT]
> I clienti che scelgono livelli di prestazioni P11 e P15 possono usare fino a 4 TB di spazio di archiviazione incluso senza alcun costo aggiuntivo. L'opzione 4 TB è attualmente in anteprima pubblica nelle aree seguenti: Stati Uniti orientali 2, Stati Uniti occidentali, Europa occidentale, Asia sud-orientale, Giappone orientale, Australia orientale, Canada centrale e Canada orientale. Per altre informazioni, vedere le [limitazioni correnti per l'opzione 4 TB](sql-database-service-tiers.md#current-limitations-of-p11-and-p15-databases-with-4-tb-maxsize).
>

Dopo aver determinato il livello di servizio minimo, è possibile determinare il livello di prestazioni del database, ovvero il numero di DTU. I livelli di prestazioni Standard S2 e S3 sono in molti casi un valido punto di inizio. Per i database con requisiti elevati di CPU o I/O, i livelli di prestazioni Premium sono il punto di partenza ottimale. Premium offre più CPU e inizia a 10 volte più I/O rispetto al livello di prestazioni Standard massimo.

## <a name="single-database-service-tiers-and-performance-levels"></a>Livelli di servizio e di prestazioni per database singoli
Per i database singoli sono disponibili più livelli di prestazioni all'interno di ogni livello di servizio. È possibile scegliere il livello che soddisfa meglio le esigenze del carico di lavoro usando il portale di Azure, [PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md), [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database), C# e l'API REST.  

Indipendentemente dal numero di database ospitati, il database in uso ottiene un set garantito di risorse e le caratteristiche delle prestazioni previste per il database non saranno interessate.

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

> [!NOTE]
> Per una spiegazione dettagliata di tutte le altre righe in questa tabella dei livelli di servizio, vedere [Limiti e funzionalità dei livelli di servizio](sql-database-performance-guidance.md#service-tier-capabilities-and-limits).
> 

## <a name="scaling-up-or-scaling-down-a-single-database"></a>Ridimensionamento di un singolo database

Dopo aver selezionato inizialmente un livello di servizio e di prestazioni, è possibile ridimensionare un singolo database in modo dinamico in base all'esperienza effettiva. Se è necessario ridimensionare, è possibile modificare facilmente i livelli del database usando il portale di Azure, [PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md), [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database), C# e l'API REST. 

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

La modifica del livello di servizio e/o di prestazioni di un database crea una replica del database originale al nuovo livello di prestazioni, quindi passa le connessioni alla replica. Durante il processo non si verificano perdite di dati, tuttavia durante il breve intervallo nel quale si passa alla replica, le connessioni sono disabilitate e può verificarsi il rollback di alcune transazioni in-flight. Questa finestra varia, ma in media è inferiore a 4 secondi e in più del 99% dei casi è inferiore a 30 secondi. Se è presente un elevato numero di transazioni in-flight quando le connessioni sono disabilitate, questa finestra potrebbe essere più lunga.  

La durata dell'intero processo di scalabilità verticale dipende dalla dimensione e dal livello di servizio del database prima e dopo la modifica. Ad esempio, il passaggio di un database di 250 GB al livello di servizio Standard o dal livello di servizio Standard a un altro livello o nell'ambito dello stesso livello di servizio Standard viene completato entro 6 ore. Per un database delle stesse dimensioni in fase di modifica dei livelli di prestazioni all'interno del livello di servizio Premium, il completamento dovrebbe avvenire entro 3 ore.

* Per effettuare il downgrade di un database, la dimensione di quest'ultimo deve essere inferiore alla dimensione massima consentita per il livello del servizio di destinazione. 
* Quando si aggiorna un database con la [replica geografica](sql-database-geo-replication-portal.md) abilitata, è necessario aggiornare i database secondari al livello di prestazioni desiderato prima di aggiornare il database primario.
* Quando si effettua il downgrade da un livello di servizio Premium, è necessario prima terminare tutte le relazioni di replica geografica. È possibile attenersi alla procedura descritta nell'argomento [Ripristinare un database SQL di Azure in seguito a un'interruzione del servizio](sql-database-disaster-recovery.md) per arrestare il processo di replica tra il database primario e i database secondari attivi.
* Le offerte per il ripristino del servizio sono diverse per i vari livelli di servizio. Se si effettua il downgrade è possibile che la capacità di eseguire un ripristino temporizzato o di disporre di un periodo di mantenimento del backup inferiore vengano perse. Per ulteriori informazioni, vedere [Backup e ripristino del database SQL di Azure](sql-database-business-continuity.md).
* Le nuove proprietà del database non vengono applicate finché non sono state completate le modifiche.

## <a name="elastic-pool-service-tiers-and-performance-in-edtus"></a>Livelli di servizio e di prestazioni per pool elastici in eDTU

I pool consentono ai database di condividere e usare risorse DTU senza dover assegnare un livello di prestazioni specifico a ogni database nel pool. Ad esempio, un database singolo in un pool Standard può passare dall'uso di 0 eDTU al valore eDTU massimo per il database impostato quando si configura il pool. I pool consentono a più database con carichi di lavoro diversi di usare in modo efficiente le risorse eDTU disponibili per l'intero pool. Per altre informazioni relative alle considerazioni su prezzi e prestazioni per un pool di database elastici, vedere [Quando usare un pool di database elastici](sql-database-elastic-pool-guidance.md) .

La tabella seguente descrive le caratteristiche dei livelli di servizio del pool.

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-pools.md)]

Ciascun database all'interno di un pool è inoltre conforme alle caratteristiche di database singolo per il livello in questione. Ad esempio, il pool Basic ha un limite di 4800 - 28800 per il numero massimo di sessioni per pool. Un database singolo all'interno del pool Basic ha un limite di 300 sessioni.

## <a name="scaling-up-or-scaling-down-an-elastic-pool"></a>Ridimensionamento di un pool elastico

Dopo aver selezionato inizialmente un livello di servizio e di prestazioni, è possibile ridimensionare un pool elastico in modo dinamico in base all'esperienza effettiva. 

* La modifica del numero minimo di eDTU per database o del numero massimo di eDTU per database in genere viene completata entro cinque minuti.
* Il tempo richiesto per modificare le dimensioni del pool (eDTU) dipende dalla dimensione combinata di tutti i database nel pool. Le modifiche richiedono una media di 90 minuti o meno per 100 GB. Ad esempio, se lo spazio totale di tutti i database nel pool è pari a 200 GB, la latenza prevista per la modifica del numero di eDTU del pool per ogni pool è di 3 ore o meno.

Per informazioni dettagliate, vedere gli articoli relativi alla gestione di un pool elastico [nel portale di Azure](sql-database-elastic-pool-manage-portal.md), [con PowerShell](scripts/sql-database-monitor-and-scale-pool-powershell.md), [con Transact-SQL](sql-database-elastic-pool-manage-tsql.md) oppure [con C#](sql-database-elastic-pool-manage-csharp.md).

## <a name="creating-or-upgrading-to-4tb"></a>Creazione o aggiornamento a 4 TB

Le sezioni seguenti illustrano i dettagli dell'implementazione per l'opzione 4 TB.

### <a name="creating-in-the-azure-portal"></a>Creazione nel portale di Azure

Quando si crea un database con un livello di prestazioni P11 o P15, l'opzione di archiviazione predefinita di 1 TB è già selezionata. Per i database che si trovano in una delle aree supportate, è possibile aumentare lo spazio di archiviazione massimo fino a 4 TB. Per tutte le altre aree, lo spazio di archiviazione non è modificabile. Quando si seleziona l'opzione da 4 TB di spazio di archiviazione incluso, il prezzo non cambia.

### <a name="creating-using-powershell-or-transact-sql"></a>Creazione tramite PowerShell o Transact-SQL

Quando si crea un database con un livello di prestazioni P11 o P15, è possibile impostare il valore delle dimensioni massime su 1 TB, ovvero l'impostazione predefinita, o su 4 TB. Sono validi anche i valori "1024 GB" e "4096 GB". Se si sceglie l'opzione da 4 TB e viene effettuato il provisioning del database in un'area non supportata, il comando di creazione avrà esito negativo e restituirà un errore.

### <a name="upgrading-to-4tb"></a>Aggiornamento a 4 TB 

Per i database P11 e P15 che si trovano in una delle aree supportate, è possibile aumentare lo spazio di archiviazione massimo fino a 4 TB. A tale scopo è possibile usare il portale di Azure, PowerShell o Transact-SQL. L'esempio seguente illustra la modifica delle dimensioni massime con il comando ALTER DATABASE:

 ```sql
ALTER DATABASE <myDatabaseName> 
   MODIFY (MAXSIZE = 4096 GB);
```

È possibile eseguire l'aggiornamento di un database P11 o P15 esistente solo tramite un accesso entità a livello del server o come membri del ruolo del database dbmanager. Se eseguita in un'area supportata, la configurazione verrà aggiornata immediatamente. È possibile verificarlo usando [SELECT DATABASEPROPERTYEX](https://msdn.microsoft.com/library/ms186823.aspx) o esaminando le dimensioni del database nel portale di Azure. Il database rimarrà online durante il processo di aggiornamento. Tuttavia, non sarà possibile usare tutti i 4 TB di memoria fino al completamento dell'aggiornamento dei file del database alle nuove dimensioni massime. Il tempo necessario dipende dalle dimensioni del database in corso di aggiornamento.  

### <a name="error-messages"></a>messaggi di errore
La creazione o l'aggiornamento di un database P11/P15 in un'area non supportata avranno esito negativo, con il seguente messaggio di errore: **P11 and P15 database with up to 4TB of storage are available in US East 2, West US, South East Asia, West Europe, Canada East, Canada Central, Japan East, and Australia East.** (I database P11 e P15 con memoria massima di 4 TB sono disponibili nelle aree Stati Uniti orientali 2, Stati Uniti occidentali, Asia sud-orientale, Europa occidentale, Canada orientale, Canada centrale, Giappone orientale e Australia orientale.)

## <a name="current-limitations-of-p11-and-p15-databases-with-4-tb-maxsize"></a>Limitazioni correnti dei database P11 e P15 con dimensioni massime di 4 TB

- Durante la creazione o l'aggiornamento di un database P11 o P15, è possibile scegliere solamente 1 TB o 4 TB di dimensioni massime. Gli account di archiviazione intermedi non sono attualmente supportati.
- Un database dalle dimensioni massime di 4 TB non potrà essere modificato a 1 TB, anche se la memoria usata non supera quest'ultimo valore. Pertanto, non è possibile eseguire il downgrade da P11 o P15 da 4 TB a P11 o P15 da 1 TB o a un livello di prestazioni minore, ad esempio P1-P6, fino a quando non verranno fornite opzioni di archiviazione aggiuntive per tutti gli altri livelli di prestazioni. Questa restrizione si applica anche agli scenari di ripristino e copia, inclusi gli scenari temporizzati, il ripristino geografico, la conservazione backup a lungo termine e la copia del database. Dopo avere configurato un database con l'opzione da 4 TB, tutte le operazioni di ripristino di tale database dovranno essere P11/P15 con dimensioni massime di 4 TB.
- Per gli scenari di replica geografica attiva:
   - Impostazione di una relazione di replica geografica: se il database primario è P11 o P15, devono esserlo anche i database secondari. I livelli di prestazioni inferiori non verranno accettati come database secondari in quanto non supportano l'opzione da 4 TB.
   - Aggiornamento del database primario in una relazione di replica geografica: portando a 4 TB le dimensioni massime di un database primario, verranno portate a 4 TB anche le dimensioni del database secondario. Entrambi gli aggiornamenti devono avere esito positivo per applicare la modifica al database primario. Si applicano limitazioni per l'opzione da 4 TB (vedere sopra). Se il database secondario si trova in un'area che non supporta l'opzione da 4 TB, il database primario non verrà aggiornato.
- Il servizio di importazione/esportazione per caricare i database P11 o P15 da 4 TB non è supportato. Usare SqlPackage.exe per [importare](sql-database-import.md) ed [esportare](sql-database-export.md) i dati.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni dettagliate sui [pool elastici](sql-database-elastic-pool-guidance.md) e [considerazioni su prezzi e prestazioni dei pool elastici](sql-database-elastic-pool-guidance.md).
* Informazioni su come [Monitorare e gestire un pool di database elastici con il portale di Azure](sql-database-elastic-pool-manage-portal.md) e [Monitorare le prestazioni del database nel database SQL di Azure](sql-database-single-database-monitor.md).
* Dopo aver acquisito familiarità con i livelli del database SQL, provare con un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) per scoprire come [creare il primo database SQL](sql-database-get-started-portal.md).
* Per gli scenari di migrazione usare lo strumento per il [calcolo di DTU](http://dtucalculator.azurewebsites.net/) per simulare il numero di DTU necessario. 


