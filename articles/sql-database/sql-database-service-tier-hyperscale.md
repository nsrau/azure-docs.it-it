---
title: Panoramica dell'iperscalabilità del database SQL di Azure | Documentazione Microsoft
description: Questo articolo descrive il livello di servizio con iperscalabilità nel modello di acquisto basato su vCore nel database SQL di Azure e ne illustra le differenze rispetto ai livelli di servizio Utilizzo generico e Business critical.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 25936fa1156dea4beff6e593646d0468a4687f36
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55476180"
---
# <a name="hyperscale-service-tier-preview-for-up-to-100-tb"></a>Livello di servizio con iperscalabilità (anteprima) per database fino a 100 TB

Il Database SQL di Azure si basa sull'architettura del motore di database di SQL Server che viene rettificata per l'ambiente cloud per garantire la disponibilità del 99,99% anche in caso di errori dell'infrastruttura. Esistono tre modelli di architettura usati nel database SQL di Azure:

- Utilizzo generico/Standard 
- Business critical/Premium
- Hyperscale

Il livello di servizio con iperscalabilità nel database SQL di Azure è il livello di servizio più recente nel modello di acquisto basato su vCore. Questo livello di servizio è un altamente scalabile per le prestazioni di archiviazione e calcolo, e sfrutta l'architettura di Azure per scalare orizzontalmente le risorse di archiviazione e di calcolo per un database SQL di Azure sostanzialmente oltre i limiti disponibili per i livelli di utilizzo generico e business critical.

> [!IMPORTANT]
> Il livello di servizio con iperscalabilità è attualmente in anteprima pubblica ed è limitato ad alcune aree di Azure. Per l'elenco completo delle aree, vedere la sezione [Aree disponibili](#available-regions). Non è ancora consigliabile eseguire carichi di lavoro in database con iperscalabilità. Non è possibile aggiornare un database con iperscalabilità ad altri livelli di servizio. A scopo di test, è consigliabile creare una copia del database corrente e aggiornare la copia al livello di servizio con iperscalabilità.
> [!NOTE]
> Per informazioni dettagliate sui livelli di servizio Utilizzo generico e Business critical nel modello di acquisto basato su vCore, vedere i livelli di servizio [Utilizzo generico](sql-database-service-tier-general-purpose.md) e [Business critical](sql-database-service-tier-business-critical.md). Per un confronto tra il modello di acquisto basato su vCore e quello basato su DTU, vedere [Modelli di acquisto e risorse del database SQL di Azure](sql-database-service-tiers.md).
> [!IMPORTANT]
> Il livello di servizio con iperscalabilità è attualmente disponibile in anteprima pubblica. Non è ancora consigliabile eseguire carichi di lavoro in database con iperscalabilità. Non è possibile aggiornare un database con iperscalabilità ad altri livelli di servizio. A scopo di test, è consigliabile creare una copia del database corrente e aggiornare la copia al livello di servizio con iperscalabilità.

## <a name="what-are-the-hyperscale-capabilities"></a>Funzionalità del livello di servizio con iperscalabilità

Il livello di servizio con iperscalabilità nel database SQL di Azure offre le seguenti funzionalità aggiuntive:

- Supporto per database di dimensioni massime di 100 TB
- Backup dei database quasi immediati (basati su snapshot di file archiviati nell'archivio BLOB di Azure) indipendentemente dalle dimensioni senza alcun impatto delle operazioni di I/O sulle risorse di calcolo   
- Ripristino dei database (basati su snapshot di file) in pochi minuti anziché in ore o giorni (non è un'operazione di dimensionamento dei dati)
- Prestazioni complessive più elevate grazie alla maggiore velocità effettiva dei log e ai tempi di esecuzione di commit delle transazioni più veloci, indipendentemente dai volumi di dati
- Rapida scalabilità orizzontale: è possibile effettuare il provisioning di uno o più nodi di sola lettura per l'offload del carico di lavoro di lettura e per l'uso come hot standby
- Rapida scalabilità orizzontale: in un tempo costante è possibile aumentare le risorse di calcolo per supportare ingenti carichi di lavoro secondo necessità, e quindi ridurre nuovamente le risorse di calcolo quando non necessarie.

Il livello di servizio con iperscalabilità elimina molti dei limiti pratici che generalmente caratterizzano i database cloud. Se la maggior parte dei database sono limitati dalle risorse disponibili in un singolo nodo, i database nel livello di servizio con iperscalabilità non presentano limiti di questo tipo. Grazie all'architettura di archiviazione flessibile, lo spazio di archiviazione aumenta in base alle esigenze. Infatti, i database con iperscalabilità non vengono creati con un limite definito per la dimensione massima. Un database con iperscalabilità può espandersi in base alle esigenze, e la fatturazione avviene solo in base alla capacità in uso. Per i carichi di lavoro con intense attività di lettura, il livello di servizio con iperscalabilità consente la rapida scalabilità orizzontale effettuando il provisioning di repliche in lettura aggiuntive in base alle necessità per l'offload dei carichi di lavoro di lettura.

Inoltre, il tempo necessario per creare i backup dei database oppure aumentare o diminuire le prestazioni non è più associato al volume dei dati presenti nel database. È possibile eseguire il backup dei database con iperscalabilità praticamente istantaneamente. È anche possibile ridimensionare un database aumentando o diminuendo la capacità di decine di terabyte in pochi minuti. Questa funzionalità consente di non essere vincolati alle scelte di configurazione iniziali.

Per altre informazioni sulle dimensioni di calcolo per il livello di servizio con iperscalabilità, vedere [Caratteristiche del livello di servizio](sql-database-service-tiers-vcore.md#service-tier-characteristics).

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Destinazione d'uso del livello di servizio con iperscalabilità

Il livello di servizio con iperscalabilità è destinato principalmente ai clienti che dispongono di database di grandi dimensioni in sede e che vogliono modernizzare le proprie applicazioni passando al cloud, oppure per i clienti che giù dispongono del cloud ma sono limitati dalle dimensioni massime del database (1-4 TB). È destinato anche ai clienti che sono alla ricerca di alte prestazioni e scalabilità elevata per l'archiviazione e il calcolo.

Il livello di servizio con iperscalabilità supporta tutti i carichi di lavoro di Microsoft SQL Server ma è ottimizzato principalmente per OLTP. Il livello di servizio con iperscalabilità supporta anche carichi di lavoro ibridi e analitici (data mart).

> [!IMPORTANT]
> I pool elastici non supportano il livello di servizio con iperscalabilità.

## <a name="hyperscale-pricing-model"></a>Modello di prezzi del livello di servizio con iperscalabilità

Il livello di servizio con iperscalabilità è disponibile solo nel [modello vCore](sql-database-service-tiers-vcore.md). Per allinearsi alla nuova architettura, il modello di prezzi è leggermente diverso da quello del livello di servizio Utilizzo generico o Business critical:

- **Calcolo**:

  Il prezzo dell'unità di calcolo del livello di servizio con iperscalabilità è per replica. Il prezzo del [Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/) viene applicato automaticamente alle repliche con scalabilità in lettura. Per impostazione predefinita, in anteprima pubblica vengono create due repliche per database con iperscalabilità.

- **Archiviazione**:

  Non è necessario specificare le dimensioni massime dei dati durante la configurazione di un database con iperscalabilità. Nel livello con iperscalabilità vengono addebitate le risorse di archiviazione per il database in base all'utilizzo effettivo. Le risorse di archiviazione vengono allocate dinamicamente tra 5 GB e 100 TB, in incrementi di 1 GB.  

Per altre informazioni sui prezzi del livello con iperscalabilità, vedere [Prezzi di Database SQL di Azure](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="distributed-functions-architecture"></a>Architettura con funzioni distribuite

A differenza dei motori di database tradizionali che centralizzano tutte le funzioni di gestione dati in un unico percorso o processo (perfino i cosiddetti database distribuiti per la produzione dispongono attualmente di più copie di un motore dati monolitico), un database con iperscalabilità separa il motore di elaborazione query, in cui far divergere la semantica dei vari motori di dati, dai componenti che offrono archiviazione a lungo termine e la durabilità dei dati. In questo modo, la capacità di archiviazione può essere facilmente aumentata secondo necessità (l'obiettivo iniziale è 100 TB). Dato che le repliche di sola lettura condividono gli stessi componenti di calcolo, nessuna copia dei dati è necessaria per creare rapidamente una nuova replica leggibile. Nella versione di anteprima è supportata solo 1 replica di sola lettura.

Il diagramma seguente illustra i diversi tipi di nodi in un database con iperscalabilità:

![architettura](./media/sql-database-hyperscale/hyperscale-architecture.png)

Un database con iperscalabilità contiene i seguenti tipi diversi di nodi:

### <a name="compute-node"></a>Nodo di calcolo

Il nodo di calcolo è dove si trova il motore relazionale e dove si verificano tutti i gli elementi del linguaggio, dell'elaborazione delle query e così via. Tutte le interazioni dell'utente con un database con iperscalabilità vengono eseguite tramite questi nodi di calcolo. I nodi di calcolo presentano cache basae su SSD (con etichetta RBPEX - estensione del pool di buffer resiliente nel diagramma precedente) per ridurre al minimo il numero di round trip di rete necessari per recuperare una pagina di dati. È presente un nodo di calcolo primario in cui vengono elaborati tutti i carichi di lavoro di lettura/scrittura e le transazioni. Sono presenti uno o più nodi di calcolo secondari che fungono da nodi di hot standby per finalità di failover, oltre a fungere da nodi di calcolo di sola lettura per l'offload di carichi di lavoro di lettura (se si desidera questa funzionalità).

### <a name="page-server-node"></a>Nodo server di pagina

I servers di pagina sono sistemi che rappresentano un motore di archiviazione con scalabilità orizzontale.  Ogni server di pagina è responsabile di un subset delle pagine nel database.  Nominalmente, ogni server di pagina controlla 1 terabyte di dati. Nessun dato è condiviso su più di un server di pagina (al di là delle repliche mantenute per ridondanza e disponibilità). Il compito di un server di pagina consiste nel fornire su richiesta le pagine del database ai nodi di calcolo e nel mantenere le pagine aggiornate man mano che le transazioni aggiornano i dati. I server di pagina vengono tenuti aggiornati riproducendo i record di log dal servizio di log. Per migliorare le prestazioni, i server di pagina gestiscono anche le cache basate su SSD. L'archiviazione a lungo termine delle pagine di dati viene mantenuta in Archiviazione di Azure per garantire maggiore affidabilità.

### <a name="log-service-node"></a>Nodo del servizio di log

Il nodo del servizio di log accetta i record di log dal nodo di calcolo primario, li rende persistenti in una cache durevole e inoltra i record del log ai restanti nodi di calcolo (in modo che possano aggiornare le cache) nonché ai server di pagina pertinenti, in modo che i dati possano essere qui aggiornati. In questo modo, tutte le modifiche ai dati dal nodo di calcolo primario vengono propagate tramite il servizio di log a tutti i nodi di calcolo secondari e ai server di pagina. Infine, i record di log vengono inviati all'archiviazione a lungo termine in Archiviazione di Azure, ovvero un repository di archiviazione infinito. Questo meccanismo elimina la necessità di troncamento frequente dei log. Il servizio di log include inoltre la cache locale per velocizzare l'accesso.

### <a name="azure-storage-node"></a>Nodo di archiviazione di Azure

Il nodo di archiviazione di Azure è la destinazione finale dei dati dal server di pagina. Questo archivio viene usato per scopi di backup così come per la replica tra aree di Azure. I backup sono costituiti da snapshot di file di dati. Le operazioni di ripristino sono veloci grazie a questi snapshot e i dati possono essere ripristinati in qualsiasi punto nel tempo.

## <a name="backup-and-restore"></a>Backup e ripristino

I backup sono snapshot di file di base e di conseguenza sono quasi istantanei. La separazione tra calcolo e archiviazione consente di portare backup/operazione di ripristino a livello di archiviazione per ridurre il carico di elaborazione nel nodo di calcolo primario. Di conseguenza, il backup di un database di grandi dimensioni non compromette le prestazioni del nodo di calcolo primario. Analogamente, le operazioni di ripristino vengono eseguite tramite la copia di snapshot di file e di conseguenza non costituiscono un'operazione di dimensionamento dei dati. Per i ripristini nello stesso account di archiviazione, l'operazione di ripristino è veloce.

## <a name="scale-and-performance-advantages"></a>Vantaggi di scalabilità e prestazioni

Con la possibilità di accelerare/diminuore la velocità dei nodi di calcolo di sola lettura aggiuntivi, l'architettura con iperscalabilità offre significative funzionalità di scalabilità di lettura e consente inoltre di liberare il nodo di calcolo primario per la gestione di più richieste di scrittura. Inoltre, i nodi di calcolo possono essere aumentati o diminuiti rapidamente grazie all'architettura di archiviazione condivisa dell'architettura con iperscalabilità.

## <a name="create-a-hyperscale-database"></a>Creare un database con iperscalabilità

È possibile creare un database con iperscalabilità usando il [portale di Azure](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current), [Powershell](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabase) o l'[interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create). I database con iperscalabilità sono disponibili solo con il [modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md).

Il comando T-SQL seguente crea un database con iperscalabilità. Nell'istruzione `CREATE DATABASE` è necessario specificare sia l'edizione che l'obiettivo del servizio.

```sql
-- Create a HyperScale Database
CREATE DATABASE [HyperScaleDB1] (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen4_4');
GO
```

## <a name="migrate-an-existing-azure-sql-database-to-the-hyperscale-service-tier"></a>Eseguire la migrazione di un database SQL di Azure esistente al livello di servizio con iperscalabilità

È possibile spostare i database SQL di Azure esistenti nel livello di servizio con iperscalabilità usando il [portale di Azure](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current), [Powershell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase) o l'[interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update). In anteprima pubblica questo spostamento è una migrazione unidirezionale. Non è possibile spostare i database dal livello di servizio con iperscalabilità in un altro livello di servizio. È consigliabile creare una copia dei database di produzione ed eseguire la migrazione al livello di servizio con iperscalabilità per il modello di verifica.

Il comando T-SQL seguente sposta un database nel livello di servizio con iperscalabilità. Nell'istruzione `ALTER DATABASE` è necessario specificare sia l'edizione che l'obiettivo del servizio.

```sql
-- Alter a database to make it a HyperScale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen4_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>Connettersi a una replica scalabilità in lettura di un database con iperscalabilità

Nei database con iperscalabilità l'argomento `ApplicationIntent` nella stringa di connessione fornita dal client indica se la connessione viene instradata alla replica in scrittura o a una replica secondaria di sola lettura. Se `ApplicationIntent` è impostato su `READONLY` e il database non ha una replica secondaria, la connessione viene indirizzata alla replica primaria con il comportamento predefinito `ReadWrite`.

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="available-regions"></a>Aree disponibili

Il livello di servizio con iperscalabilità è attualmente in anteprima pubblica ed è limitato alle aree di Azure seguenti: EastUS1, EastUS2, WestUS2, CentralUS, NorthCentralUS, WestEurope, NorthEurope, UKWest, AustraliaEast, AustraliaSouthEast, SouthEastAsia, JapanEast, KoreaCentral

## <a name="known-limitations"></a>Limitazioni note

| Problema | DESCRIZIONE |
| :---- | :--------- |
| Il riquadro Gestisci backup per un server di database SQL non mostra i database con iperscalabilità, che vengono filtrati da SQL Server->  | Il livello di servizio con iperscalabilità gestisce i backup diversamente, pertanto le impostazioni di conservazione a lungo termine e conservazione dei backup temporizzata non sono valide. Di conseguenza i database con iperscalabilità non compaiono nel riquadro Gestisci backup. |
| Ripristino temporizzato | Dopo la migrazione di un database nel livello di servizio con iperscalabilità, il ripristino in un tempo precedente alla migrazione non è supportato.|
| Se le dimensioni di un database aumentano durante la migrazione a causa di un carico di lavoro inattivo, superando il limite di 1 TB per file, la migrazione non riesce. | Soluzioni: <br> - Se possibile, eseguire la migrazione del database in un momento in cui non è in esecuzione un carico di lavoro di aggiornamento.<br> - Riprovare a eseguire la migrazione, che riuscirà se durante il processo non viene superato il limite di 1 TB.|
| Istanza gestita non è attualmente supportata | Attualmente non supportato |
| La migrazione al livello di servizio con iperscalabilità è attualmente un'operazione unidirezionale | Dopo aver completato la migrazione di un database a questo livello di servizio, non è possibile eseguirne la migrazione direttamente a un livello di servizio senza iperscalabilità. Attualmente l'unico modo per eseguire la migrazione di un database dal livello di servizio con iperscalabilità a un livello di servizio diverso consiste nell'esportarlo/importarlo usando un file BACPAC.|
| La migrazione di database con oggetti in memoria non è attualmente supportata | Gli oggetti in memoria devono essere eliminati e ricreati come oggetti non in memoria prima di poter eseguire la migrazione di un database al livello di servizio con iperscalabilità.|
| La modifica del rilevamento dei dati non è attualmente supportata. | Non sarà possibile usare la modifica del rilevamento dei dati per i database con iperscalabilità.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sull'iperscalabilità, vedere le [domande frequenti sull'iperscalabilità](sql-database-service-tier-hyperscale-faq.md).
- Per informazioni sui livelli di servizio, vedere [Livelli di servizio](sql-database-service-tiers.md)
- Per informazioni sui limiti a livello di server e sottoscrizione, vedere [Overview of resource limits on a SQL Database server](sql-database-resource-limits-database-server.md) (Panoramica dei limiti delle risorse in un server di database SQL).
- Per informazioni sui limiti del modello di acquisto per un database singolo, vedere [Limiti del modello di acquisto basato su vCore per il database SQL di Azure per un database singolo](sql-database-vcore-resource-limits-single-databases.md).
- Per un elenco di confronto delle funzionalità, vedere [Confronto tra le funzionalità: database SQL di Azure e SQL Server](sql-database-features.md).
