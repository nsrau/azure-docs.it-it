---
title: Panoramica di Hyperscale per il database SQL di Azure | Microsoft Docs
description: Questo articolo descrive il livello di servizio Hyperscale nel modello di acquisto basato su vCore nel database SQL di Azure e ne illustra le differenze rispetto ai livelli di servizio Utilizzo generico e Business critical.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/01/2019
ms.openlocfilehash: 5a9917010b7301bf70c3bebf68c35d82f4839e0f
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409053"
---
# <a name="hyperscale-service-tier"></a>Livello di servizio Hyperscale

Il Database SQL di Azure si basa sull'architettura del motore di database di SQL Server che viene rettificata per l'ambiente cloud per garantire la disponibilità del 99,99% anche in caso di errori dell'infrastruttura. Esistono tre modelli architetturali usati nel database SQL di Azure:
- Utilizzo generico/Standard 
-  Hyperscale
-  Business critical/Premium

Il livello di servizio Hyperscale nel database SQL di Azure è il livello di servizio più recente nel modello di acquisto basato su vCore. Questo livello di servizio è un altamente scalabile per le prestazioni di archiviazione e calcolo, e sfrutta l'architettura di Azure per scalare orizzontalmente le risorse di archiviazione e di calcolo per un database SQL di Azure sostanzialmente oltre i limiti disponibili per i livelli di utilizzo generico e business critical.

> 
> [!NOTE]
> Per informazioni dettagliate sui livelli di servizio Scopo generale e Business Critical nel modello di acquisto basato su vCore, vedere [General Purpose](sql-database-service-tier-general-purpose.md) and [Business Critical](sql-database-service-tier-business-critical.md) service tiers. Per un confronto tra il modello di acquisto basato su vCore e quello basato su DTU, vedere [Modelli di acquisto e risorse del database SQL di Azure](sql-database-service-tiers.md).


## <a name="what-are-the-hyperscale-capabilities"></a>Funzionalità del livello di servizio Hyperscale

Il livello di servizio Hyperscale nel database SQL di Azure offre le seguenti funzionalità aggiuntive:

- Supporto per database di dimensioni massime di 100 TB
- Backup di database quasi istantanei (basati su snapshot di file archiviati nell'archiviazione BLOB di Azure) indipendentemente dalle dimensioni senza impatto sulle risorse di calcolo  
- Ripristino dei database (basati su snapshot di file) in pochi minuti anziché in ore o giorni (non è un'operazione di dimensionamento dei dati)
- Prestazioni complessive più elevate grazie alla maggiore velocità effettiva dei log e ai tempi di esecuzione di commit delle transazioni più veloci, indipendentemente dai volumi di dati
- Rapida scalabilità orizzontale: è possibile effettuare il provisioning di uno o più nodi di sola lettura per l'offload del carico di lavoro di lettura e per l'uso come hot standby
- Rapida scalabilità orizzontale: in un tempo costante è possibile aumentare le risorse di calcolo per supportare ingenti carichi di lavoro secondo necessità, e quindi ridurre nuovamente le risorse di calcolo quando non necessarie.

Il livello di servizio Hyperscale elimina molti dei limiti pratici che generalmente caratterizzano i database cloud. Se la maggior parte dei database sono limitati dalle risorse disponibili in un singolo nodo, i database nel livello di servizio Hyperscale non presentano limiti di questo tipo. Grazie all'architettura di archiviazione flessibile, lo spazio di archiviazione aumenta in base alle esigenze. Infatti, i database Hyperscale non vengono creati con un limite definito per la dimensione massima. Un database Hyperscale può espandersi in base alle esigenze, e la fatturazione avviene solo in base alla capacità in uso. Per i carichi di lavoro con intense attività di lettura, il livello di servizio Hyperscale consente la rapida scalabilità orizzontale effettuando il provisioning di repliche in lettura aggiuntive in base alle necessità per l'offload dei carichi di lavoro di lettura.

Inoltre, il tempo necessario per creare i backup dei database oppure aumentare o diminuire le prestazioni non è più associato al volume dei dati presenti nel database. È possibile eseguire il backup dei database Hyperscale praticamente istantaneamente. È anche possibile ridimensionare un database aumentando o diminuendo la capacità di decine di terabyte in pochi minuti. Questa funzionalità consente di non essere vincolati alle scelte di configurazione iniziali.

Per altre informazioni sulle dimensioni di calcolo per il livello di servizio Hyperscale, vedere [Caratteristiche del livello di servizio](sql-database-service-tiers-vcore.md#service-tiers).

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Destinazione d'uso del livello di servizio Hyperscale

Il livello di servizio Hyperscale è destinato alla maggior parte dei carichi di lavoro aziendali in quanto offre grande flessibilità e prestazioni elevate con risorse di elaborazione e archiviazione scalabili in modo indipendente. Con la possibilità di scalare automaticamente lo storage fino a 100 TB, è un'ottima scelta per i clienti che:

- Disporre di database di grandi dimensioni in locale e si desidera modernizzare le applicazioni passando al cloud
- Sono già nel cloud e sono limitati dalle restrizioni di dimensione massima del database di altri livelli di servizio (1-4 TB)
- Disporre di database più piccoli, ma richiedono scalabilità di calcolo verticale e orizzontale veloce, prestazioni elevate, backup istantaneo e ripristino rapido del database.

Il livello di servizio Hyperscale supporta un'ampia gamma di carichi di lavoro di SQL Server, da OLTP puro a analisi pura, ma è ottimizzato principalmente per i carichi di lavoro OLTP e HTAP (Hybrid transaction and analytical Processing).

> [!IMPORTANT]
> I pool elastici non supportano il livello di servizio Hyperscale.

## <a name="hyperscale-pricing-model"></a>Modello di prezzi del livello di servizio Hyperscale

Il livello di servizio Hyperscale è disponibile solo nel [modello vCore](sql-database-service-tiers-vcore.md). Per allinearsi alla nuova architettura, il modello di prezzi è leggermente diverso da quello del livello di servizio Utilizzo generico o Business critical:

- **Compute**:

  Il prezzo dell'unità di calcolo del livello di servizio Hyperscale è per replica. Il prezzo del [Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/) viene applicato automaticamente alle repliche con scalabilità in lettura. Per impostazione predefinita, viene creata una replica primaria e una replica di sola lettura per ogni database Hyperscale.We create a primary replica and one read-only replica per Hyperscale database by default.  Gli utenti possono modificare il numero totale di repliche, incluso il database primario da 1 a 5.

- **Spazio di archiviazione**:

  Non è necessario specificare le dimensioni massime dei dati durante la configurazione di un database Hyperscale. Nel livello di hyperscale viene addebitato l'archiviazione per il database in base all'allocazione effettiva. Lo spazio di archiviazione viene allocato automaticamente tra 40 GB e 100 TB, in 10 GB di incrementi di 10 GB. Più file di dati possono crescere contemporaneamente, se necessario. Un database Hyperscale viene creato con una dimensione iniziale di 10 GB e inizia a crescere di 10 GB ogni 10 minuti, fino a raggiungere la dimensione di 40 GB.

Per altre informazioni sui prezzi di Hyperscale, vedere [Prezzi di Database SQL di Azure](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="distributed-functions-architecture"></a>Architettura con funzioni distribuite

A differenza dei motori di database tradizionali che centralizzano tutte le funzioni di gestione dati in un unico percorso o processo (perfino i cosiddetti database distribuiti per la produzione dispongono attualmente di più copie di un motore dati monolitico), un database Hyperscale separa il motore di elaborazione query, in cui far divergere la semantica dei vari motori di dati, dai componenti che offrono archiviazione a lungo termine e la durabilità dei dati. In questo modo, la capacità di archiviazione può essere facilmente aumentata secondo necessità (l'obiettivo iniziale è 100 TB). Le repliche di sola lettura condividono gli stessi componenti di archiviazione, pertanto non è necessaria alcuna copia dei dati per creare una nuova replica leggibile. 

Il diagramma seguente illustra i diversi tipi di nodi in un database Hyperscale:

![architettura](./media/sql-database-hyperscale/hyperscale-architecture.png)

Un database Hyperscale contiene i seguenti tipi diversi di componenti:

### <a name="compute"></a>Calcolo

Il nodo di calcolo è dove si trova il motore relazionale e dove si verificano tutti i gli elementi del linguaggio, dell'elaborazione delle query e così via. Tutte le interazioni dell'utente con un database Hyperscale vengono eseguite tramite questi nodi di calcolo. I nodi di calcolo presentano cache basae su SSD (con etichetta RBPEX - estensione del pool di buffer resiliente nel diagramma precedente) per ridurre al minimo il numero di round trip di rete necessari per recuperare una pagina di dati. È presente un nodo di calcolo primario in cui vengono elaborati tutti i carichi di lavoro di lettura/scrittura e le transazioni. Sono presenti uno o più nodi di calcolo secondari che fungono da nodi di hot standby per finalità di failover, oltre a fungere da nodi di calcolo di sola lettura per l'offload di carichi di lavoro di lettura (se si desidera questa funzionalità).

### <a name="page-server"></a>Server di paging

I servers di pagina sono sistemi che rappresentano un motore di archiviazione con scalabilità orizzontale.  Ogni server di pagina è responsabile di un subset delle pagine nel database.  Nominalmente, ogni server di pagina controlla tra 128 GB e 1 TB di dati. Nessun dato è condiviso su più di un server di pagina (al di là delle repliche mantenute per ridondanza e disponibilità). Il compito di un server di pagina consiste nel fornire su richiesta le pagine del database ai nodi di calcolo e nel mantenere le pagine aggiornate man mano che le transazioni aggiornano i dati. I server di pagina vengono tenuti aggiornati riproducendo i record di log dal servizio di log. Per migliorare le prestazioni, i server di pagina gestiscono anche le cache basate su SSD. L'archiviazione a lungo termine delle pagine di dati viene mantenuta in Archiviazione di Azure per garantire maggiore affidabilità.

### <a name="log-service"></a>Servizio di registrazione

Il servizio di log accetta i record di log dalla replica di calcolo primaria, li rende persistenti in una cache durevole e inoltra i record di log al resto delle repliche di calcolo (in modo che possano aggiornare le cache) e i server di paging pertinenti, in modo che i dati possano essere aggiornati in tale server. In questo modo, tutte le modifiche ai dati dalla replica di calcolo primaria vengono propagate tramite il servizio di log a tutte le repliche di calcolo secondarie e i server di paging. Infine, i record di log vengono inviati all'archiviazione a lungo termine in Archiviazione di Azure, che è un archivio di archiviazione praticamente infinito. Questo meccanismo elimina la necessità di troncamento frequente del log. Il servizio di log dispone anche di cache locale per velocizzare l'accesso ai record di log.

### <a name="azure-storage"></a>Archiviazione di Azure

Archiviazione di Azure contiene tutti i file di dati in un database. I server di paging mantengono aggiornati i file di dati in Archiviazione di Azure.Page servers keep data files in Azure Storage up to date. Questa risorsa di archiviazione viene usata a scopo di backup e per la replica tra aree di Azure.This storage is used for backup purposes, as well as for replication between Azure regions. I backup vengono implementati utilizzando snapshot di archiviazione dei file di dati. Le operazioni di ripristino tramite snapshot sono veloci indipendentemente dalle dimensioni dei dati. I dati possono essere ripristinati in qualsiasi momento entro il periodo di conservazione del backup del database.

## <a name="backup-and-restore"></a>Backup e ripristino

I backup sono basati su snapshot di file e quindi sono quasi istantanei. La separazione di archiviazione e calcolo consente di eseguire il push dell'operazione di backup/ripristino al livello di archiviazione per ridurre il carico di elaborazione nella replica di calcolo primaria. Di conseguenza, il backup del database non influisce sulle prestazioni del nodo di calcolo primario. allo stesso modo, i ripristini vengono eseguiti ripristinando gli snapshot di file e come tali non sono una dimensione dell'operazione sui dati. Il ripristino è un'operazione a tempo costante e anche i database con più terabyte possono essere ripristinati in pochi minuti anziché in ore o giorni. La creazione di nuovi database ripristinando un backup esistente sfrutta anche questa funzionalità: la creazione di copie del database a scopo di sviluppo o test, anche di database di dimensioni terabyte, è fattibile in pochi minuti.

## <a name="scale-and-performance-advantages"></a>Vantaggi di scalabilità e prestazioni

Con la possibilità di accelerare/diminuore la velocità dei nodi di calcolo di sola lettura aggiuntivi, l'architettura Hyperscale offre significative funzionalità di scalabilità di lettura e consente inoltre di liberare il nodo di calcolo primario per la gestione di più richieste di scrittura. Inoltre, i nodi di calcolo possono essere aumentati o diminuiti rapidamente grazie all'architettura di archiviazione condivisa dell'architettura Hyperscale.

## <a name="create-a-hyperscale-database"></a>Creare un database HyperscaleCreate a Hyperscale database

È possibile creare un database Hyperscale usando il portale di [Azure,](https://portal.azure.com) [T-SQL,](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current) [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabase) o [l'interfaccia della riga di comando.](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create) I database di hyperscale sono disponibili solo utilizzando il modello di [acquisto basato su vCore.](sql-database-service-tiers-vcore.md)

Il comando T-SQL seguente crea un database Hyperscale. Nell'istruzione `CREATE DATABASE` è necessario specificare sia l'edizione che l'obiettivo del servizio. Fare riferimento ai [limiti di risorse](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases#hyperscale---provisioned-compute---gen4) per un elenco di obiettivi di servizio validi.

```sql
-- Create a Hyperscale Database
CREATE DATABASE [HyperscaleDB1] (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```
Verrà creato un database Hyperscale su hardware Gen5 con 4 core.

## <a name="migrate-an-existing-azure-sql-database-to-the-hyperscale-service-tier"></a>Eseguire la migrazione di un database SQL di Azure esistente al livello di servizio Hyperscale

È possibile spostare i database SQL di Azure esistenti in Hyperscale usando il portale di [Azure,](https://portal.azure.com) [T-SQL,](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase) o [l'interfaccia della riga di comando.](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update) In questo momento, si tratta di una migrazione unidirezionale. Non è possibile spostare i database da Hyperscale a un altro livello di servizio, se non esportando e importando i dati. Per le prove di concetto (POC), è consigliabile creare una copia dei database di produzione e migrare la copia in Hyperscale. La migrazione di un database SQL di Azure esistente al livello Hyperscale è una dimensione dell'operazione sui dati.

Il comando T-SQL seguente sposta un database nel livello di servizio Hyperscale. Nell'istruzione `ALTER DATABASE` è necessario specificare sia l'edizione che l'obiettivo del servizio.

```sql
-- Alter a database to make it a Hyperscale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>Connettersi a una replica scalabilità in lettura di un database Hyperscale

Nei database di `ApplicationIntent` hyperscale, l'argomento nella stringa di connessione fornita dal client determina se la connessione viene instradata alla replica di scrittura o a una replica secondaria di sola lettura. Se `ApplicationIntent` è impostato su `READONLY` e il database non ha una replica secondaria, la connessione viene indirizzata alla replica primaria con il comportamento predefinito `ReadWrite`.

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Le repliche secondarie hyperscale sono tutte identiche, usando lo stesso obiettivo del livello di servizio della replica primaria. Se sono presenti più repliche secondarie, il carico di lavoro viene distribuito tra tutti i database secondari disponibili. Ogni replica secondaria viene aggiornata in modo indipendente, pertanto repliche diverse potrebbero avere una latenza dei dati diversa rispetto alla replica primaria.

## <a name="database-high-availability-in-hyperscale"></a>Database High Availability in Hyperscale

Come in tutti gli altri livelli di servizio, Hyperscale garantisce la durabilità dei dati per le transazioni di cui è stato eseguito il commit indipendentemente dalla disponibilità della replica di calcolo. L'entità del tempo di inattività dovuto alla non disponibilità della replica primaria dipende dal tipo di failover (pianificato e non pianificato) e dalla presenza di almeno una replica secondaria. In un failover pianificato, ovvero un evento di manutenzione, il sistema crea la nuova replica primaria prima di iniziare un failover oppure usa una replica secondaria esistente come destinazione del failover. In un failover non pianificato, ovvero un errore hardware nella replica primaria, il sistema utilizza una replica secondaria come destinazione del failover, se presente, oppure crea una nuova replica primaria dal pool di capacità di calcolo disponibile. In quest'ultimo caso, la durata dei tempi di inattività è maggiore a causa dei passaggi aggiuntivi necessari per creare la nuova replica primaria.

Per il servizio di archiviazione Hyperscale, vedere [SLA per il database SQL](https://azure.microsoft.com/support/legal/sla/sql-database/)di Azure.For Hyperscale SLA, see SLA for Azure SQL Database .

## <a name="disaster-recovery-for-hyperscale-databases"></a>Ripristino di emergenza per i database di hyperscaleDisaster Recovery for Hyperscale Databases

### <a name="restoring-a-hyperscale-database-to-a-different-geography"></a>Ripristino di un database Hyperscale in un'area geografica diversa
Se è necessario ripristinare un database di hyperscale del database SQL di Azure in un'area diversa da quella in cui è attualmente ospitata, come parte di un'operazione di ripristino di emergenza o di un'operazione di ripristino di emergenza, rilocazione o qualsiasi altro motivo, il metodo principale consiste nell'eseguire un ripristino geografico del database.  Questo comporta esattamente gli stessi passaggi che si utilizzerebbe per ripristinare qualsiasi altro database SQL di AUR ON- in un'area diversa:
1. Creare un server di database SQL nell'area di destinazione, se non si dispone già di un server appropriato.  Questo server deve essere di proprietà della stessa sottoscrizione del server originale (origine).
2. Seguire le istruzioni nell'argomento del [ripristino geografico](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#geo-restore) della pagina sul ripristino dei database SQL di Azure dai backup automatici.

> [!NOTE]
> Poiché l'origine e la destinazione si trovano in aree separate, il database non può condividere l'archiviazione snapshot con il database di origine come nei ripristini non geografici, che vengono completati molto rapidamente. Nel caso di un ripristino geografico di un database Hyperscale, sarà un'operazione di dimensione dei dati, anche se la destinazione si trova nell'area associata dell'archiviazione con replica geografica.  Ciò significa che l'esecuzione di un ripristino geografico richiederà tempo proporzionale alle dimensioni del database da ripristinare.  Se la destinazione si trova nell'area abbinata, la copia sarà all'interno di un'area, che sarà significativamente più veloce di una copia tra aree, ma sarà comunque un'operazione di dimensione dei dati.

## <a name="available-regions"></a><a name=regions></a>Aree disponibili

Il livello di Hyperscale del database SQL di Azure è attualmente disponibile nelle aree seguenti:The Azure SQL Database Hyperscale tier is currently available in the following regions:

- Australia orientale
- Australia sud-orientale
- Brasile meridionale
- Canada centrale
- Stati Uniti centrali
- Cina orientale 2
- Cina settentrionale 2
- Asia orientale
- Stati Uniti orientali
- Stati Orientali 2
- Francia centrale
- Giappone orientale
- Giappone occidentale
- Corea centrale
- Corea meridionale
- Stati Uniti centro-settentrionali
- Europa settentrionale
- Sudafrica settentrionale
- Stati Uniti centro-meridionali
- Asia sud-orientale
- Regno Unito meridionale
- Regno Unito occidentale
- Europa occidentale
- Stati Uniti occidentali
- Stati Uniti occidentali 2

Se si vuole creare un database Hyperscale in un'area non elencata come supportata, è possibile inviare una richiesta di onboarding tramite il portale di Azure.If you want to create Hyperscale database in a region that is not listed as supported, you can send an onboarding request via Azure portal. Per istruzioni, vedere Richiedi aumenti delle quote per il database SQL di [Azure.For](quota-increase-request.md) instructions, see Request quota increases for Azure SQL Database for instructions. Quando invii la tua richiesta, segui le seguenti linee guida:

- Utilizzare il tipo di quota del database SQL [di richiesta di quota.](quota-increase-request.md#other)
- Nei dettagli del testo aggiungere lo SKU di calcolo/core totali, incluse le repliche leggibili.
- Specificare anche la TB stimata.

## <a name="known-limitations"></a>Limitazioni note

Queste sono le limitazioni correnti al livello di servizio Hyperscale a partire da GA.  Stiamo lavorando attivamente per rimuovere il maggior numero possibile di queste limitazioni.

| Problema | Descrizione |
| :---- | :--------- |
| Il riquadro Gestisci backup per un server logico non mostra i database Hyperscale verranno filtrati dal server SQL  | Hyperscale ha un metodo separato per la gestione dei backup e come tale le impostazioni di conservazione dei backup a lungo termine e conservazione dei backup temporizzazione non si applicano / vengono invalidate. Di conseguenza i database Hyperscale non compaiono nel riquadro Gestisci backup. |
| Ripristino temporizzato | È possibile ripristinare un database Hyperscale in un database non Hyperscale, entro il periodo di conservazione del database non Hyperscale.You can restore a Hyperscale database into a non-Hyperscale database, within non-Hyperscale database retention period. Non è possibile ripristinare un database non Hyperscale in un database Hyperscale.|
| Se un database contiene uno o più file di dati di dimensioni superiori a 1 TB, la migrazione non riesce | In alcuni casi, potrebbe essere possibile aggirare questo problema riducendo i file di grandi dimensioni a meno di 1 TB. Se durante il processo di migrazione viene eseguito la migrazione di un database, assicurarsi che nessun file superi 1 TB. Utilizzare la query seguente per determinare le dimensioni dei file di database. `SELECT *, name AS file_name, size * 8. / 1024 / 1024 AS file_size_GB FROM sys.database_files WHERE type_desc = 'ROWS'`;|
| database SQL | Istanza gestita database SQL di Azure non è attualmente supportata con i database Hyperscale.Azure SQL Database Managed Instance is not currently supported with Hyperscale databases. |
| Pool elastici |  I pool elastici non sono attualmente supportati con l'hyperscale del database SQL.|
| La migrazione al livello di servizio Hyperscale è attualmente un'operazione unidirezionale | Dopo aver completato la migrazione di un database a Hyperscale, non è possibile eseguirne la migrazione direttamente a un livello di servizio diverso. Al momento, l'unico modo per eseguire la migrazione di un database da Hyperscale a non Hyperscale consiste nell'esportare/importare usando un file BACPAC o altre tecnologie di spostamento dei dati (copia globale, Azure Data Factory, Azure Databricks, SSIS e così via).|
| Migrazione di database con oggetti in memoria persistenti | Hyperscale supporta solo oggetti In-Memory non persistenti (tipi di tabella, SP e funzioni native).  Le tabelle in memoria persistente e altri oggetti devono essere eliminati e ricreati come oggetti non in memoria prima di eseguire la migrazione di un database al livello di servizio Hyperscale.|
| Rilevamento modifiche | Il rilevamento delle modifiche è attualmente in anteprima pubblica e può essere abilitato in database Hyperscale nuovi o esistenti. |
| Replica geografica  | Non è ancora possibile configurare la replica geografica per l'Hyperscale del database SQL di Azure.You cannot yet configure geo-replication for Azure SQL Database Hyperscale. |
| Copia del database | Non è ancora possibile usare Copia del database per creare un nuovo database in Hyperscale SQL di Azure.You cannot yet use Database Copy to create a new database in Azure SQL Hyperscale. |
| Integrazione TDE/AKV | Transparent Database Encryption using Azure Key Vault (comunemente noto come Bring-Your-Own-Key o BYOK) non è ancora supportato per l'hyperscale del database SQL di Azure, tuttavia TDE con chiavi gestite del servizio è completamente supportato. |
|Funzionalità di database intelligenti | Ad eccezione dell'opzione "Piano di forza", tutte le altre opzioni di ottimizzazione automatica non sono ancora supportate su Hyperscale: le opzioni potrebbero sembrare abilitate, ma non verranno formulate raccomandazioni o azioni. |
|Analisi delle prestazioni della query | Query Performance Insights non è attualmente supportato per i database di Hyperscale.Query Performance Insights is currently not supported for Hyperscale databases. |
| Compatta database | DBCC SHRINKDATABASE o DBCC SHRINKFILE non è attualmente supportato per i database Hyperscale. |
| Controllo dell'integrità del database | DBCC CHECKDB non è attualmente supportato per i database di Hyperscale. Per informazioni dettagliate sulla gestione dell'integrità dei dati nel database SQL di Azure, vedere Integrità dei dati nel database SQL di [Azure.See Data Integrity in Azure SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/) for details on data integrity management in Azure SQL Database. |

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni su Hyperscale, vedere le [domande frequenti su Hyperscale](sql-database-service-tier-hyperscale-faq.md).
- Per informazioni sui livelli di servizio, vedere Livelli di servizioFor information about service [tiers,](sql-database-service-tiers.md) see Service tiers
- Per informazioni sui limiti a livello di server e sottoscrizione, vedere [Panoramica dei limiti delle risorse in un server logico](sql-database-resource-limits-logical-server.md).
- Per informazioni sui limiti del modello di acquisto per un database singolo, vedere [Limiti del modello di acquisto basato su vCore per il database SQL di Azure per un database singolo](sql-database-vcore-resource-limits-single-databases.md).
- Per un elenco di confronto delle funzionalità, vedere [Confronto tra le funzionalità: database SQL di Azure e SQL Server](sql-database-features.md).
