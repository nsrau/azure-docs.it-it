---
title: Limiti delle risorse del server
description: Questo articolo offre una panoramica dei limiti delle risorse del server di database SQL di Azure per database singoli e pool elastici. Offre anche informazioni su cosa accade quando tali limiti delle risorse vengono raggiunti o superati.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
ms.date: 11/14/2019
ms.openlocfilehash: 52e7a3408c231ba8a38fdc22c2fcac65ee26bb82
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082515"
---
# <a name="sql-database-resource-limits-for-azure-sql-database-server"></a>Limiti delle risorse del database SQL per il server di database SQL di Azure

Questo articolo offre una panoramica dei limiti delle risorse del database SQL per un server di database SQL che gestisce database singoli e pool elastici. Offre anche informazioni su cosa accade quando tali limiti delle risorse vengono raggiunti o superati.

> [!NOTE]
> Per i limiti di istanze gestite, consultare [Limiti delle risorse del database SQL per le istanze gestite](sql-database-managed-instance-resource-limits.md).

## <a name="maximum-resource-limits"></a>Limiti massimi delle risorse

| Risorsa | Limite |
| :--- | :--- |
| Database per server | 5000 |
| Numero predefinito di server per sottoscrizione in ogni area | 20 |
| Numero massimo di server per sottoscrizione in ogni area | 200 |  
| Quota DTU/eDTU per server | 54.000 |  
| Quota vCore per server/istanza | 540 |
| N. max pool per server | Limitato dal numero di DTU o vCore. Se ad esempio ogni pool è da 1000 DTU, un server può supportare 54 pool.|
|||

> [!NOTE]
> Per ottenere una quota di DTU/eDTU o vCore maggiore o un numero di server più alto rispetto alla quantità predefinita, è possibile inviare una nuova richiesta di supporto nel portale di Azure per la sottoscrizione con il tipo di problema "Quota". La quota di DTU/eDTU e il limite di database per server limita il numero di pool elastici per ogni server.
> [!IMPORTANT]
> Poiché il numero di database si avvicina al limite per server di database SQL, può verificarsi quanto segue:
>
> - Latenza in aumento nelle query in esecuzione nel database master.  Ciò include le visualizzazioni delle statistiche di utilizzo delle risorse, ad esempio sys.resource_stats.
> - Latenza in aumento nelle operazioni di gestione e nel portale di esecuzione del rendering dei punti di visualizzazione che coinvolgono l'enumerazione dei database nel server.

### <a name="storage-size"></a>Dimensioni della risorsa di archiviazione

- Per le dimensioni di archiviazione delle risorse dei singoli database, fare riferimento ai limiti [delle risorse basate su DTU](sql-database-dtu-resource-limits-single-databases.md) o ai [limiti delle risorse basate su vCore](sql-database-vcore-resource-limits-single-databases.md) per i limiti delle dimensioni di archiviazione per ogni piano tariffario.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>Cosa accade quando vengono raggiunti i limiti delle risorse del database?

### <a name="compute-dtus-and-edtus--vcores"></a>Elaborazione (DTU ed eDTU/vCore)

Quando l'utilizzo del calcolo del database (misurato da DTU e edtu, o VCore) diventa elevato, la latenza delle query aumenta e può addirittura timeout. In queste condizioni, le query possono essere accodate dal servizio e vengono fornite le risorse per l'esecuzione perché la risorsa diventa disponibile.
In caso di uso elevato di risorse di elaborazione, le opzioni di mitigazione includono:

- Aumento delle dimensioni di calcolo del database o del pool elastico per mettere a disposizione del database un numero maggiore di risorse del computer. Vedere [Ridimensionare le risorse del database singolo](sql-database-single-database-scale.md) e [Ridimensionare le risorse del pool elastico](sql-database-elastic-pool-scale.md).
- Ottimizzazione delle query per ridurre l'uso delle risorse di ogni query. Per altre informazioni, vedere la sezione [Hint/ottimizzazione di query](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Archiviazione

Quando la quantità di spazio del database usato raggiunge il limite di dimensioni massime, gli inserimenti e gli aggiornamenti del database che aumentano la dimensione dei dati hanno esito negativo e i clienti ricevono un [messaggio di errore](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md). Le operazioni SELECT e DELETE del database continuano ad avere esito positivo.

In caso di uso elevato di spazio, le opzioni di mitigazione includono:

- Aumento delle dimensioni massime del database o pool elastico o aggiungere più archiviazione. Vedere [Ridimensionare le risorse del database singolo](sql-database-single-database-scale.md) e [Ridimensionare le risorse del pool elastico](sql-database-elastic-pool-scale.md).
- Se il database è in un pool elastico, in alternativa può essere spostato all'esterno del pool in modo che lo spazio di archiviazione non venga condiviso con altri database.
- Compattare un database per recuperare spazio inutilizzato. Per altre informazioni, vedere [Gestire lo spazio file nel database SQL di Azure](sql-database-file-space-management.md)

### <a name="sessions-and-workers-requests"></a>Sessioni e ruoli di lavoro (richieste)

Il numero massimo di sessioni e ruoli di lavoro è determinato dal livello di servizio e dalle dimensioni di calcolo (DTU ed eDTU). Le nuove richieste vengono rifiutate quando vengono raggiunti i limiti delle sessioni o dei ruoli di lavoro e i clienti ricevono un messaggio di errore. Mentre il numero di connessioni disponibili può essere controllato dall'applicazione, il numero di ruoli di lavoro simultanei è spesso più difficile da stimare e da controllare. Ciò vale soprattutto durante i picchi di periodi di carico quando vengono raggiunti i limiti di risorse del database e i ruoli di lavoro si accumulano per via di query a esecuzione prolungata.

In caso di uso elevato di sessioni o ruoli di lavoro, le opzioni di mitigazione includono:

- Aumento del livello di servizio o delle dimensioni di calcolo del database o del pool elastico. Vedere [Ridimensionare le risorse del database singolo](sql-database-single-database-scale.md) e [Ridimensionare le risorse del pool elastico](sql-database-elastic-pool-scale.md).
- Ottimizzazione delle query per ridurre l'uso delle risorse di ogni query, se l'aumento dell'uso di ruoli di lavoro è dovuto a un conflitto delle risorse di elaborazione. Per altre informazioni, vedere la sezione [Hint/ottimizzazione di query](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="transaction-log-rate-governance"></a>Governance della frequenza del log delle transazioni

La gestione della frequenza dei log delle transazioni è un processo nel database SQL di Azure usato per limitare le velocità di inserimento elevate per i carichi di lavoro, ad esempio le compilazioni BULK INSERT, SELECT INTO e index. Questi limiti vengono rilevati e applicati al livello di sottosecondo alla frequenza di generazione dei record di log, limitando la velocità effettiva indipendentemente dal numero di IOs che possono essere emessi sui file di dati.  Le frequenze di generazione del log delle transazioni attualmente si scalano in modo lineare fino a un punto dipendente dall'hardware, con la frequenza massima di log consentita di 96 MB/s con il modello di acquisto vCore.

> [!NOTE]
> Il valore effettivo di IOs per i file di log delle transazioni non è regolato o limitato.

Le frequenze dei log sono impostate in modo che possano essere realizzate e prolungate in diversi scenari, mentre il sistema generale può mantenerne le funzionalità con un effetto ridotto al carico dell'utente. La governance della frequenza dei log garantisce che i backup del log delle transazioni restino entro i contratti di sicurezza di ripristino pubblicati.  Questa governance impedisce anche un backlog eccessivo nelle repliche secondarie.

Quando vengono generati i record di log, ogni operazione viene valutata e valutata per determinare se deve essere ritardata al fine di mantenere una velocità massima di log desiderata (MB/s al secondo). I ritardi non vengono aggiunti quando i record del log vengono scaricati nella risorsa di archiviazione, ma la governance della frequenza dei log viene applicata durante la generazione della velocità del log.

Le velocità effettive di generazione dei log imposte in fase di esecuzione possono anche essere influenzate dai meccanismi di feedback, riducendo temporaneamente le frequenze di log consentite in modo che il sistema possa stabilizzarsi Gestione dello spazio del file di log, evitando che le condizioni di spazio del log e i meccanismi di replica dei gruppi di disponibilità possano ridurre temporaneamente i limiti di sistema complessivi.

Il data shaping di log rate Governor viene esposto tramite i tipi di attesa seguenti (esposti nella DMV [sys. dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) ):

| Tipo di attesa | note |
| :--- | :--- |
| LOG_RATE_GOVERNOR | Limitazione del database |
| POOL_LOG_RATE_GOVERNOR | Limitazione del pool |
| INSTANCE_LOG_RATE_GOVERNOR | Limitazione a livello di istanza |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | Controllo feedback, replica fisica del gruppo di disponibilità in Premium/business critical non in linea |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | Controllo feedback, limitazione delle tariffe per evitare una condizione di spazio del log esaurita |
|||

Quando si verifica un limite di velocità di log che ostacola la scalabilità desiderata, prendere in considerazione le opzioni seguenti:

- Scalabilità verticale a un livello superiore per ottenere la velocità massima di log di 96 MB/s.
- Se i dati caricati sono temporanei, ad esempio i dati di staging in un processo ETL, possono essere caricati in tempdb (con registrazione minima).
- Per gli scenari di analisi, caricare in una tabella analizzata columnstore cluster. In questo modo si riduce la velocità di log necessaria a causa della compressione. Questa tecnica aumenta l'utilizzo della CPU ed è applicabile solo ai set di dati che traggono vantaggio dagli indici columnstore cluster.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sui limiti generici di Azure, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md).
- Per informazioni su DTU ed eDTU, vedere [DTU ed eDTU](sql-database-purchase-models.md#dtu-based-purchasing-model).
- Per informazioni sui limiti di tempdb relativi alle dimensioni, vedere [TempDB nel database SQL di Azure](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).
