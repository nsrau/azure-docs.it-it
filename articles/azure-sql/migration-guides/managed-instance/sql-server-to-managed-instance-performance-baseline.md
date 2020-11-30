---
title: SQL Server a SQL Istanza gestita-analisi delle prestazioni
description: Informazioni su come creare e confrontare una baseline delle prestazioni quando si esegue la migrazione dei database di SQL Server al Istanza gestita SQL di Azure.
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: mokabiru
ms.date: 11/06/2020
ms.openlocfilehash: c47e4c1278f222feac35a2c6ab0b067c916c0217
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96326846"
---
# <a name="migration-performance-sql-server-to-sql-managed-instance-performance-analysis"></a>Prestazioni della migrazione: analisi delle prestazioni da SQL Server a SQL Istanza gestita
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

Creare una baseline per le prestazioni per confrontare le prestazioni del carico di lavoro in un Istanza gestita SQL con il carico di lavoro originale in esecuzione in SQL Server. 

## <a name="create-a-baseline"></a>Creazione di una linea di base

Idealmente, le prestazioni sono simili o migliori dopo la migrazione, quindi è importante misurare e registrare i valori delle prestazioni di base nell'origine, quindi confrontarli con l'ambiente di destinazione. Una baseline per le prestazioni è un set di parametri che definiscono il carico di lavoro medio nell'origine. 

Selezionare un set di query importanti per e rappresentativo del carico di lavoro aziendale. Misurare e documentare la durata minima/media/massima e l'utilizzo della CPU per queste query, nonché le metriche delle prestazioni nel server di origine, ad esempio utilizzo della CPU medio/massimo, latenza di i/o del disco media/massima, velocità effettiva, IOPS, permanenza media/massima delle pagine e dimensioni medie massime del tempdb. 

Le risorse seguenti consentono di definire una linea di base per le prestazioni: 

   - [Monitorare l'utilizzo della CPU ](https://techcommunity.microsoft.com/t5/azure-sql-database/monitor-cpu-usage-on-sql-server-and-azure-sql/ba-p/680777#M131)
   - [Monitorare l'utilizzo](/sql/relational-databases/performance-monitor/monitor-memory-usage)   della memoria e determinano la quantità di memoria usata da diversi componenti, ad esempio pool di buffer, cache dei piani, pool di archivio colonne, [OLTP in memoria](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage)e così via. Inoltre, è necessario trovare i valori medi e massimi del contatore delle prestazioni della memoria permanenza presunta delle pagine. 
   - Monitorare l'utilizzo dei dati di i/o su disco [sys.dm_io_virtual_file_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)nell'istanza di SQL Server di origine utilizzando i    [contatori delle prestazioni](/sql/relational-databases/performance-monitor/monitor-disk-usage)o della sys.dm_io_virtual_file_stats. 
   - Monitorare il carico di lavoro e le prestazioni delle query esaminando viste a gestione dinamica (o Query Store se si esegue la migrazione da SQL Server 2016 e versioni successive). Identificare la durata media e l'utilizzo della CPU delle query più importanti nel carico di lavoro. 

Eventuali problemi di prestazioni nel SQL Server di origine devono essere risolti prima della migrazione. La migrazione di problemi noti a qualsiasi nuovo sistema può causare risultati imprevisti e invalidare qualsiasi confronto delle prestazioni. 


## <a name="compare-performance"></a>Confrontare le prestazioni 

Dopo aver definito una linea di base, confrontare le prestazioni del carico di lavoro simili sul Istanza gestita SQL di destinazione. Per maggiore precisione, è importante che l'ambiente SQL Istanza gestita sia paragonabile al più possibile l'ambiente SQL Server. 

Esistono differenze nell'infrastruttura di SQL Istanza gestita che rendono esattamente improbabile la corrispondenza delle prestazioni. Alcune query potrebbero essere eseguite più velocemente del previsto, mentre altre potrebbero risultare più lente. Lo scopo di questo confronto consiste nel verificare che le prestazioni del carico di lavoro nell'istanza gestita soddisfino le prestazioni in SQL Server (in media) e per identificare eventuali query critiche con prestazioni che non corrispondono alle prestazioni originali. 

È probabile che il confronto delle prestazioni provochi i risultati seguenti: 

- Le prestazioni dei carichi di lavoro nell'istanza gestita sono allineate o migliori rispetto alle prestazioni del carico di lavoro nel SQL Server di origine. In questo caso, è stato confermato che la migrazione è stata completata correttamente. 

- La maggior parte dei parametri delle prestazioni e delle query nel carico di lavoro viene eseguita come previsto, con alcune eccezioni che comportano un calo delle prestazioni. In questo caso, identificare le differenze e la loro importanza. Se sono presenti alcune query importanti con prestazioni ridotte, verificare se i piani SQL sottostanti sono stati modificati o se le query raggiungono i limiti delle risorse. È possibile ovviare a questo problema applicando alcuni suggerimenti sulle query critiche (ad esempio, modificare il livello di compatibilità e la stima di cardinalità legacy), direttamente o usando le guide di piano. Verificare che le statistiche e gli indici siano aggiornati e equivalenti in entrambi gli ambienti. 

- La maggior parte delle query è più lenta in un'istanza gestita rispetto all'istanza di SQL Server di origine. In questo caso, provare a identificare le cause principali della differenza, ad esempio il [raggiungimento di un limite di risorse](../../managed-instance/resource-limits.md#service-tier-characteristics) , ad esempio i limiti di velocità di i/o, memoria o di log dell'istanza. Se non sono presenti limiti per le risorse che determinano la differenza, provare a modificare il livello di compatibilità del database o modificare le impostazioni del database, ad esempio la stima della cardinalità legacy, e rieseguire il test. Esaminare le indicazioni fornite dall'istanza gestita o dalle viste Query Store per identificare le query con prestazioni regressioni. 

SQL Istanza gestita dispone di una funzionalità di correzione automatica dei piani, abilitata per impostazione predefinita. Questa funzionalità garantisce che le query che hanno funzionato correttamente in passato non peggiorano in futuro. Se questa funzionalità non è abilitata, eseguire il carico di lavoro con le impostazioni precedenti in modo che SQL Istanza gestita possa apprendere la baseline delle prestazioni. Quindi, abilitare la funzionalità ed eseguire di nuovo il carico di lavoro con le nuove impostazioni. 

Apportare modifiche ai parametri del test o eseguire l'aggiornamento a livelli di servizio superiori per raggiungere la configurazione ottimale per le prestazioni del carico di lavoro adatte alle proprie esigenze. 

## <a name="monitor-performance"></a>Monitorare le prestazioni 

SQL Istanza gestita offre strumenti avanzati per il monitoraggio e la risoluzione dei problemi ed è consigliabile usarli per monitorare le prestazioni dell'istanza. Di seguito sono riportate alcune delle metriche principali da monitorare: 

- Utilizzo della CPU nell'istanza di per determinare se il numero di vcore di cui è stato effettuato il provisioning è la corrispondenza corretta per il carico di lavoro. 
- Permanenza presunta della pagina nell'istanza gestita per determinare se è necessaria [memoria aggiuntiva](https://techcommunity.microsoft.com/t5/azure-sql-database/do-you-need-more-memory-on-azure-sql-managed-instance/ba-p/563444).
-  Statistiche come INSTANCE_LOG_GOVERNOR o PAGEIOLATCH che identificano i problemi di i/o di archiviazione, soprattutto nel livello di per utilizzo generico, in cui potrebbe essere necessario pre-allocare i file per ottenere migliori prestazioni di i/o. 


## <a name="considerations"></a>Considerazioni  

Quando si confrontano le prestazioni, tenere presente quanto segue: 

- Impostazioni corrispondenti tra origine e destinazione. Verificare che varie impostazioni di istanza, database e tempdb siano equivalenti tra i due ambienti. Le differenze di configurazione, i livelli di compatibilità, le impostazioni di crittografia, i flag di traccia e così via possono comparire le prestazioni. 

- L'archiviazione viene configurata in base alle [procedure consigliate](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525). Ad esempio, per per utilizzo generico, potrebbe essere necessario pre-allocare le dimensioni dei file per migliorare le prestazioni. 

- Sono presenti [differenze di ambiente principali](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) che possono causare differenze nelle prestazioni tra un'istanza gestita e SQL Server. Identificare i rischi rilevanti per l'ambiente che possono contribuire a un problema di prestazioni. 

- L'archivio query e l'ottimizzazione automatica devono essere abilitati nella Istanza gestita SQL perché consentono di misurare le prestazioni del carico di lavoro e di ridurre automaticamente i potenziali problemi di prestazioni. 



## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sull'ottimizzazione del nuovo ambiente di Istanza gestita SQL di Azure, vedere le risorse seguenti: 

- [Come identificare il motivo per cui le prestazioni dei carichi di lavoro in Azure SQL Istanza gestita sono diverse da SQL Server?](https://medium.com/azure-sqldb-managed-instance/what-to-do-when-azure-sql-managed-instance-is-slower-than-sql-server-dd39942aaadd)
- [Principali cause delle differenze di prestazioni tra SQL Istanza gestita e SQL Server](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)
- [Procedure consigliate e considerazioni sulle prestazioni di archiviazione per Istanza gestita SQL di Azure (per utilizzo generico)](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525)
- [Il monitoraggio delle prestazioni in tempo reale per Istanza gestita SQL di Azure (archiviato è la destinazione prevista?)](/archive/blogs/sqlcat/real-time-performance-monitoring-for-azure-sql-database-managed-instance)