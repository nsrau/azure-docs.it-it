---
title: Limiti delle risorse del server di database SQL di Azure | Microsoft Docs
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
manager: craigg
ms.date: 04/18/2019
ms.openlocfilehash: 4e4c0a6cd25587b33c06526b57e6acdbebb69c8b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445638"
---
# <a name="sql-database-resource-limits-for-azure-sql-database-server"></a>Limiti delle risorse del database SQL per il server di database SQL di Azure

Questo articolo offre una panoramica dei limiti delle risorse del database SQL per un server di database SQL che gestisce database singoli e pool elastici. Offre anche informazioni su cosa accade quando tali limiti delle risorse vengono raggiunti o superati.

> [!NOTE]
> Per i limiti di istanze gestite, consultare [Limiti delle risorse del database SQL per le istanze gestite](sql-database-managed-instance-resource-limits.md).

## <a name="maximum-resource-limits"></a>Limiti massimi delle risorse

| Resource | Limite |
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
> - Latenza in aumento nelle query in esecuzione nel database master.  Ciò include le visualizzazioni delle statistiche di utilizzo delle risorse, ad esempio sys.resource_stats.
> - Latenza in aumento nelle operazioni di gestione e nel portale di esecuzione del rendering dei punti di visualizzazione che coinvolgono l'enumerazione dei database nel server.

### <a name="storage-size"></a>Dimensioni della risorsa di archiviazione
- Per i database singoli rources consultare uno [limiti delle risorse basate su DTU](sql-database-dtu-resource-limits-single-databases.md) oppure [limiti delle risorse basate su vCore](sql-database-vcore-resource-limits-single-databases.md) per i limiti delle dimensioni di archiviazione per ogni piano tariffario.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>Cosa accade quando vengono raggiunti i limiti delle risorse del database?

### <a name="compute-dtus-and-edtus--vcores"></a>Elaborazione (DTU ed eDTU/vCore)

Quando l'uso delle risorse di elaborazione del database (misurato in base a DTU ed eDTU o VCore) diventa elevato, la latenza delle query aumenta e può anche verificarsi un timeout. In queste condizioni le query possono essere messe in coda dal servizio e vengono rese disponibili alcune risorse per l'esecuzione.
In caso di uso elevato di risorse di elaborazione, le opzioni di mitigazione includono:

- Aumento delle dimensioni di calcolo del database o del pool elastico per mettere a disposizione del database un numero maggiore di risorse del computer. Vedere [Ridimensionare le risorse del database singolo](sql-database-single-database-scale.md) e [Ridimensionare le risorse del pool elastico](sql-database-elastic-pool-scale.md).
- Ottimizzazione delle query per ridurre l'uso delle risorse di ogni query. Per altre informazioni, vedere la sezione [Hint/ottimizzazione di query](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Archiviazione

Quando la quantità di spazio del database usato raggiunge il limite di dimensioni massime, gli inserimenti e gli aggiornamenti del database che aumentano la dimensione dei dati hanno esito negativo e i clienti ricevono un [messaggio di errore](sql-database-develop-error-messages.md). Le operazioni SELECT e DELETE del database continuano ad avere esito positivo.

In caso di uso elevato di spazio, le opzioni di mitigazione includono:

- Aumento delle dimensioni massime del database o pool elastico o aggiungere più archiviazione. Vedere [Ridimensionare le risorse del database singolo](sql-database-single-database-scale.md) e [Ridimensionare le risorse del pool elastico](sql-database-elastic-pool-scale.md).
- Se il database è in un pool elastico, in alternativa può essere spostato all'esterno del pool in modo che lo spazio di archiviazione non venga condiviso con altri database.
- Compattare un database per recuperare spazio inutilizzato. Per altre informazioni, vedere [Gestire lo spazio file nel database SQL di Azure](sql-database-file-space-management.md)

### <a name="sessions-and-workers-requests"></a>Sessioni e ruoli di lavoro (richieste)

Il numero massimo di sessioni e ruoli di lavoro è determinato dal livello di servizio e dalle dimensioni di calcolo (DTU ed eDTU). Le nuove richieste vengono rifiutate quando vengono raggiunti i limiti delle sessioni o dei ruoli di lavoro e i clienti ricevono un messaggio di errore. Mentre il numero di connessioni disponibili può essere controllato dall'applicazione, il numero di ruoli di lavoro simultanei è spesso più difficile da stimare e da controllare. Ciò vale soprattutto durante i picchi di periodi di carico quando vengono raggiunti i limiti di risorse del database e i ruoli di lavoro si accumulano per via di query a esecuzione prolungata.

In caso di uso elevato di sessioni o ruoli di lavoro, le opzioni di mitigazione includono:

- Aumento del livello di servizio o delle dimensioni di calcolo del database o del pool elastico. Vedere [Ridimensionare le risorse del database singolo](sql-database-single-database-scale.md) e [Ridimensionare le risorse del pool elastico](sql-database-elastic-pool-scale.md).
- Ottimizzazione delle query per ridurre l'uso delle risorse di ogni query, se l'aumento dell'uso di ruoli di lavoro è dovuto a un conflitto delle risorse di elaborazione. Per altre informazioni, vedere la sezione [Hint/ottimizzazione di query](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="transaction-log-rate-governance"></a>Governance delle velocità di Log delle transazioni 
Governance delle velocità di log delle transazioni è un processo in Azure SQL Database usate per limitare i tassi di inserimento elevati per carichi di lavoro, ad esempio bulk insert, SELECT INTO, e le compilazioni di indici. Questi limiti vengono registrati e applicati a livello di frazioni di secondo per la frequenza di generazione di record di log, limitazione della velocità effettiva indipendentemente dal numero IOs può essere emesso per i file di dati.  Frequenza di generazione del log delle transazioni attualmente una scalabilità lineare fino a un punto che dipende dall'hardware, con il log massimo consentita da 96 MB/s con il modello di acquisto di Vcore per velocità. 

> [!NOTE]
> IOs fisico effettivo nei file di log delle transazioni non sono regolate o limitate. 

Tassi di log vengono impostate in modo che possano essere ottenute e mantenute in una vasta gamma di scenari, mentre tutto il sistema può gestire le funzionalità con un impatto ridotto a icona per il carico utente. Log frequenza governance assicura che i backup vengano mantenute all'interno di recuperabilità pubblicata i contratti di servizio il log delle transazioni.  La governance impedisce anche un backlog eccessivo sulle repliche secondarie.

Quando vengono generati i record del log, ogni operazione viene valutata e valutato per indica se deve essere ritardato per mantenere una frequenza massima del registro desiderato (MB/s al secondo). I ritardi non vengono aggiunti quando i record del log vengono scaricati da archiviazione, piuttosto log frequenza governance viene applicata durante la generazione della velocità del log stesso.

La generazione di log effettivo tassi di imposte in fase di esecuzione potrebbero anche essere influenzate da meccanismi di commenti e suggerimenti, ridurre temporaneamente le frequenze consentite per il log in modo che il sistema può stabilizzarsi. Gestione dello spazio file di log, evitando in esecuzione in condizioni di spazio di log e il gruppo di disponibilità di meccanismi di replica può ridurre temporaneamente i limiti di sistema complessivo. 

La conformazione del traffico di log frequenza governor viene esposto tramite i seguenti tipi di attesa (esposto nel [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) DMV):

| Tipo di attesa | Note |
| :--- | :--- |
| LOG_RATE_GOVERNOR | La limitazione del database |
| POOL_LOG_RATE_GOVERNOR | Limitazione di pool |
| INSTANCE_LOG_RATE_GOVERNOR | La limitazione a livello di istanza |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | Controllo di commenti e suggerimenti, replica fisico di gruppo di disponibilità in Premium/Business Critical non bastano |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | Controllo di commenti e suggerimenti, limitazione della velocità per evitare di log dello spazio insufficiente |
|||

In presenza di un limite di frequenza di log che è intralciano la scalabilità desiderata, prendere in considerazione le opzioni seguenti:
- Aumentare le prestazioni a un livello di dimensioni maggiori per ottenere la velocità massima di 96 MB/s log. 
- Se i dati in fase di caricamento sono temporanei, ad esempio gestione temporanea dei dati in un processo ETL, può essere caricato in tempdb (che è a registrazione minima). 
- Per gli scenari analitici, caricare in una tabella columnstore cluster coperto. In questo modo si riduce la velocità necessaria log a causa della compressione. Questa tecnica comporta un aumento utilizzo della CPU ed è applicabile solo ai set di dati che traggono vantaggio dagli indici columnstore cluster. 

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sui limiti generici di Azure, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md).
- Per informazioni su DTU ed eDTU, vedere [DTU ed eDTU](sql-database-purchase-models.md#dtu-based-purchasing-model).
- Per informazioni sui limiti di tempdb relativi alle dimensioni, vedere [TempDB nel database SQL di Azure](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).
