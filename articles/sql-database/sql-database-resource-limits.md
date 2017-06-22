---
title: Limiti delle risorse di Database SQL Azure | Microsoft Docs
description: In questa pagina vengono descritti alcuni limiti di risorse comuni per il Database SQL Azure.
services: sql-database
documentationcenter: na
author: janeng
manager: jhubbard
editor: 
ms.assetid: 884e519f-23bb-4b73-a718-00658629646a
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 05/31/2017
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: 17c4dc6a72328b613f31407aff8b6c9eacd70d9a
ms.openlocfilehash: a7887839b7a3672e824ed6662f6ba5dc4e318fab
ms.contentlocale: it-it
ms.lasthandoff: 05/16/2017


---
# <a name="azure-sql-database-resource-limits"></a>Limiti delle risorse del database SQL di Azure
## <a name="overview"></a>Panoramica
Il database SQL di Azure gestisce le risorse disponibili per un database usando due meccanismi diversi: la **governance delle risorse** e l'**imposizione di limiti**. Questo argomento illustra queste due aree principali relative alla gestione delle risorse.

## <a name="resource-governance"></a>Governance delle risorse
Uno degli obiettivi di progettazione dei piani tariffari Basic, Standard, Premium e Premium RS prevede che il database SQL di Azure si comporti come se il database fosse in esecuzione in un computer dedicato, indipendente dagli altri database. La governance delle risorse emula questo comportamento. Se l'utilizzo delle risorse aggregato raggiunge il numero massimo di risorse disponibili di CPU, memoria, I/O di log e I/O di dati assegnate al database, la governance delle risorse inserisce nella coda le query in esecuzione e assegna le risorse alle query accodate man mano che si liberano.

Come in un computer dedicato, l'utilizzo di tutte le risorse disponibili determina un allungamento dei tempi per le query attualmente in esecuzione che può comportare timeout dei comandi nel client. Nelle applicazioni con una logica di ripetizione dei tentativi aggressiva e nelle applicazioni che eseguono query sul database con frequenza elevata possono essere restituiti messaggi di errore durante il tentativo di eseguire nuove query quando è stato raggiunto il limite di richieste simultanee.

### <a name="recommendations"></a>Consigli:
Monitorare l'utilizzo delle risorse e i tempi di risposta medi delle query quando si sta per raggiungere l'utilizzo massimo di un database. Quando si verificano latenze di query più elevate, sono disponibili tre opzioni:

1. Ridurre il numero di richieste in entrata al database per evitare timeout e l'accumulo delle richieste.
2. Assegnare al database un livello di prestazioni più elevato.
3. Ottimizzare le query per ridurre l'utilizzo delle risorse di ogni query. Per altre informazioni, vedere la sezione Hint/ottimizzazione di query in Linee guida per le prestazioni del database SQL di Azure.

## <a name="enforcement-of-limits"></a>imposizione di limiti
Per le risorse diverse da CPU, memoria, I/O di log e I/O di dati, al raggiungimento dei limiti le nuove richieste vengono negate. Quando un database raggiunge le dimensioni massime configurate, gli inserimenti e gli aggiornamenti che aumentano le dimensioni dei dati hanno esito negativo, mentre le selezioni e le eliminazioni continuano a funzionare. I client visualizzano un [messaggio di errore](sql-database-develop-error-messages.md) a seconda del limite che è stato raggiunto.

Ad esempio, il numero di connessioni a un database SQL e il numero di richieste simultanee che possono essere elaborate sono limitati. Nel database SQL, il numero di connessioni al database può essere maggiore del numero di richieste simultanee per supportare i pool di connessioni. Mentre il numero di connessioni disponibili può essere facilmente controllato dall'applicazione, il numero di richieste parallele è più difficile da stimare e da controllare. In particolare, durante i carichi di picco quando l'applicazione invia troppe richieste o il database raggiunge i relativi limiti e inizia ad accumulare thread di lavoro a causa dei tempi di esecuzione delle query prolungati, è possibile che si verifichino errori.

## <a name="service-tiers-and-performance-levels"></a>Livelli di servizio e livelli di prestazioni
Sono disponibili livelli di servizio e livelli di prestazioni sia per i database singoli che per i pool elastici.

### <a name="single-databases"></a>Database singoli
Per un singolo database, i limiti di un database sono definiti dal livello del servizio del database e dal livello delle prestazioni. La tabella seguente descrive le caratteristiche dei database Basic, Standard, Premium e Premium RS a diversi livelli di prestazioni.

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

> [!IMPORTANT]
> I clienti che scelgono livelli di prestazioni P11 e P15 possono usare fino a 4 TB di spazio di archiviazione incluso senza alcun costo aggiuntivo. L'opzione 4 TB è attualmente disponibile nelle aree seguenti: Stati Uniti orientali 2, Stati Uniti occidentali, US Gov Virginia, Europa occidentale, Germania centrale, Asia sud-orientale, Giappone orientale, Australia orientale, Canada centrale e Canada orientale.
>

### <a name="elastic-pools"></a>Pool elastici
[Pool elastici](sql-database-elastic-pool.md) condividono le risorse tra i database del pool. La tabella seguente descrive le caratteristiche dei pool elastici Basic, Standard, Premium e Premium RS.

[!INCLUDE [SQL DB service tiers table for elastic databases](../../includes/sql-database-service-tiers-table-elastic-pools.md)]

Per una definizione espansa di ogni risorsa elencata nelle tabelle precedenti, vedere le descrizioni in [Limiti e capacità a livello di servizio](sql-database-performance-guidance.md#service-tier-capabilities-and-limits). Per una panoramica dei livelli di servizio, vedere [Livelli di servizio e livelli di prestazioni del database SQL di Azure](sql-database-service-tiers.md).

## <a name="other-sql-database-limits"></a>Altri limiti relativi al database SQL
| Area | Limite | Descrizione |
| --- | --- | --- |
| Database che usano l'esportazione automatizzata per ogni sottoscrizione |10 |L’esportazione automatizzata consente di creare una pianificazione personalizzata per il backup dei database SQL. L'anteprima di questa funzionalità terminerà il 1° marzo 2017.  |
| Database per server |Fino a 5000 |Nei server sono permessi fino a 5000 database per server. |
| DTU per server |45000 |In ogni server sono consentite 45000 DTU per il provisioning di database autonomi e pool elastici. Il numero totale di database autonomi e pool consentito per ogni server è limitato solo dal numero di DTU del server.  

> [!IMPORTANT]
> La funzionalità di esportazione automatizzata di database SQL di Azure, ora disponibile in anteprima, verrà ritirata il 1° marzo 2017. A partire dal 1° dicembre 2016 non è più possibile configurare l'esportazione automatizzata per i database SQL. Tutti i processi di esportazione automatizzata esistenti continueranno a funzionare fino al 1° marzo 2017. Dal 1° dicembre 2016, è possibile usare la [conservazione dei backup a lungo termine](sql-database-long-term-retention.md) o [Automazione di Azure](../automation/automation-intro.md) per archiviare periodicamente i database SQL con PowerShell in base alla pianificazione desiderata. È possibile scaricare uno [script di esempio da GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export).
>


## <a name="resources"></a>Risorse
[Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md)

[Livelli di servizio e livelli di prestazioni del database SQL di Azure](sql-database-service-tiers.md)

[Messaggi di errore per programmi client di Database SQL](sql-database-develop-error-messages.md)

