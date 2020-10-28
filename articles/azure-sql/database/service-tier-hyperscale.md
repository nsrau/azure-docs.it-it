---
title: Che cos'è il livello di servizio iperscalabile?
description: Questo articolo descrive il livello di servizio di iperscalabilità nel modello di acquisto basato su vCore nel database SQL di Azure e spiega in che modo è diverso dai livelli di servizio per utilizzo generico e business critical.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/19/2020
ms.openlocfilehash: ee9bcedea15b039982e73304a25073c85b496635
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92780054"
---
# <a name="hyperscale-service-tier"></a>Livello di servizio Hyperscale

Il Database SQL di Azure si basa sull'architettura del motore di database di SQL Server che viene rettificata per l'ambiente cloud per garantire la disponibilità del 99,99% anche in caso di errori dell'infrastruttura. Esistono tre modelli architetturali usati nel database SQL di Azure:

- Utilizzo generico/Standard
- Hyperscale
- Business critical/Premium

Il livello di servizio Hyperscale nel database SQL di Azure è il livello di servizio più recente nel modello di acquisto basato su vCore. Questo livello di servizio è un altamente scalabile per le prestazioni di archiviazione e calcolo, e sfrutta l'architettura di Azure per scalare orizzontalmente le risorse di archiviazione e di calcolo per un database SQL di Azure sostanzialmente oltre i limiti disponibili per i livelli di utilizzo generico e business critical.

> [!NOTE]
>
> - Per informazioni dettagliate sui livelli di servizio per utilizzo generico e business critical nel modello di acquisto basato su vCore, vedere [per utilizzo generico](service-tier-general-purpose.md) e [business critical](service-tier-business-critical.md) livelli di servizio. Per un confronto tra il modello di acquisto basato su vCore e quello basato su DTU, vedere [Modelli di acquisto e risorse del database SQL di Azure](purchasing-models.md).
> - Il livello di servizio iperscalabile è attualmente disponibile solo per il database SQL di Azure e non per Istanza gestita SQL di Azure.

## <a name="what-are-the-hyperscale-capabilities"></a>Funzionalità del livello di servizio Hyperscale

Il livello di servizio Hyperscale nel database SQL di Azure offre le seguenti funzionalità aggiuntive:

- Supporto per database di dimensioni massime di 100 TB
- Backup di database quasi istantanei (basati su snapshot di file archiviati nell'archivio BLOB di Azure) indipendentemente dalle dimensioni senza effetti di i/o sulle risorse di calcolo  
- Ripristino dei database (basati su snapshot di file) in pochi minuti anziché in ore o giorni (non è un'operazione di dimensionamento dei dati)
- Prestazioni complessive più elevate grazie alla maggiore velocità effettiva dei log e ai tempi di esecuzione di commit delle transazioni più veloci, indipendentemente dai volumi di dati
- Rapida scalabilità orizzontale: è possibile effettuare il provisioning di uno o più nodi di sola lettura per l'offload del carico di lavoro di lettura e per l'uso come hot standby
- Scalabilità verticale rapida: è possibile, in un tempo costante, ridimensionare le risorse di calcolo per gestire carichi di lavoro pesanti quando necessario, quindi ridimensionare le risorse di calcolo quando non sono necessarie.

Il livello di servizio Hyperscale elimina molti dei limiti pratici che generalmente caratterizzano i database cloud. Se la maggior parte dei database sono limitati dalle risorse disponibili in un singolo nodo, i database nel livello di servizio Hyperscale non presentano limiti di questo tipo. Grazie all'architettura di archiviazione flessibile, lo spazio di archiviazione aumenta in base alle esigenze. In realtà, i database iperscalati non vengono creati con una dimensione massima definita. Un database con iperscalabilità aumenta in base alle esigenze e la fatturazione viene addebitata solo per la capacità usata. Per i carichi di lavoro con intense attività di lettura, il livello di servizio Hyperscale consente la rapida scalabilità orizzontale effettuando il provisioning di repliche in lettura aggiuntive in base alle necessità per l'offload dei carichi di lavoro di lettura.

Inoltre, il tempo necessario per creare i backup dei database oppure aumentare o diminuire le prestazioni non è più associato al volume dei dati presenti nel database. È possibile eseguire il backup dei database Hyperscale praticamente istantaneamente. È anche possibile ridimensionare un database aumentando o diminuendo la capacità di decine di terabyte in pochi minuti. Questa funzionalità consente di non essere vincolati alle scelte di configurazione iniziali.

Per altre informazioni sulle dimensioni di calcolo per il livello di servizio Hyperscale, vedere [Caratteristiche del livello di servizio](service-tiers-vcore.md#service-tiers).

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Destinazione d'uso del livello di servizio Hyperscale

Il livello di servizio di iperscalabilità è destinato alla maggior parte dei carichi di lavoro aziendali, poiché offre una grande flessibilità e prestazioni elevate con risorse di calcolo e archiviazione scalabili in modo indipendente. Grazie alla possibilità di ridimensionare automaticamente lo spazio di archiviazione fino a 100 TB, è un'ottima scelta per i clienti che:

- Hanno database di grandi dimensioni in locale e si vuole modernizzare le applicazioni passando al cloud
- Sono già presenti nel cloud e sono limitati dalle limitazioni massime per le dimensioni del database di altri livelli di servizio (1-4 TB)
- Sono disponibili database di dimensioni inferiori, ma è necessario un rapido ridimensionamento verticale e orizzontale di calcolo, prestazioni elevate, backup istantaneo e ripristino rapido dei database.

Il livello di servizio di iperscalabilità supporta un'ampia gamma di carichi di lavoro SQL Server, da OLTP pure a analisi pure, ma è ottimizzato principalmente per i carichi di lavoro OLTP e di elaborazione analitica e di transazione (HTAP).

> [!IMPORTANT]
> I pool elastici non supportano il livello di servizio Hyperscale.

## <a name="hyperscale-pricing-model"></a>Modello di prezzi del livello di servizio Hyperscale

Il livello di servizio Hyperscale è disponibile solo nel [modello vCore](service-tiers-vcore.md). Per allinearsi alla nuova architettura, il modello di prezzi è leggermente diverso da quello del livello di servizio Utilizzo generico o Business critical:

- **Compute** :

  Il prezzo dell'unità di calcolo del livello di servizio Hyperscale è per replica. Il prezzo del [Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/) viene applicato automaticamente alle repliche con scalabilità in lettura. Per impostazione predefinita, viene creata una replica primaria e una replica di sola lettura per database iperscalare.  Gli utenti possono modificare il numero totale di repliche, incluso il database primario da 1-5.

- **Spazio di archiviazione** :

  Non è necessario specificare le dimensioni massime dei dati durante la configurazione di un database Hyperscale. Nel livello iperscalare viene addebitato lo spazio di archiviazione per il database in base all'effettiva allocazione. Lo spazio di archiviazione viene allocato automaticamente tra 40 GB e 100 TB, in incrementi di 10 GB. Se necessario, possono crescere più file di dati contemporaneamente. Un database con iperscalabilità viene creato con una dimensione iniziale di 10 GB e inizia a crescere di 10 GB ogni 10 minuti, fino a raggiungere le dimensioni di 40 GB.

Per altre informazioni sui prezzi di Hyperscale, vedere [Prezzi di Database SQL di Azure](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="distributed-functions-architecture"></a>Architettura con funzioni distribuite

A differenza dei motori di database tradizionali che centralizzano tutte le funzioni di gestione dati in un unico percorso o processo (perfino i cosiddetti database distribuiti per la produzione dispongono attualmente di più copie di un motore dati monolitico), un database Hyperscale separa il motore di elaborazione query, in cui far divergere la semantica dei vari motori di dati, dai componenti che offrono archiviazione a lungo termine e la durabilità dei dati. In questo modo, la capacità di archiviazione può essere facilmente aumentata secondo necessità (l'obiettivo iniziale è 100 TB). Le repliche di sola lettura condividono gli stessi componenti di archiviazione, pertanto non è necessaria alcuna copia di dati per creare una nuova replica leggibile.

Il diagramma seguente illustra i diversi tipi di nodi in un database Hyperscale:

![architettura](./media/service-tier-hyperscale/hyperscale-architecture.png)

Un database con iperscalabilità contiene i diversi tipi di componenti seguenti:

### <a name="compute"></a>Calcolo

Il nodo di calcolo è la posizione in cui risiede il motore relazionale. Questo è il punto in cui si verifica la lingua, la query e l'elaborazione delle transazioni. Tutte le interazioni dell'utente con un database Hyperscale vengono eseguite tramite questi nodi di calcolo. I nodi di calcolo presentano cache basae su SSD (con etichetta RBPEX - estensione del pool di buffer resiliente nel diagramma precedente) per ridurre al minimo il numero di round trip di rete necessari per recuperare una pagina di dati. È presente un nodo di calcolo primario in cui vengono elaborati tutti i carichi di lavoro di lettura/scrittura e le transazioni. Sono presenti uno o più nodi di calcolo secondari che fungono da nodi di hot standby per finalità di failover, oltre a fungere da nodi di calcolo di sola lettura per l'offload di carichi di lavoro di lettura (se si desidera questa funzionalità).

Il motore di database in esecuzione su nodi di calcolo iperscalabile è identico a quello degli altri livelli di servizio del database SQL di Azure. Quando gli utenti interagiscono con il motore di database sui nodi di calcolo su larga scala, la superficie di attacco e il comportamento del motore supportati sono gli stessi di altri livelli di servizio, ad eccezione delle [limitazioni note](#known-limitations).

### <a name="page-server"></a>Server della pagina

I servers di pagina sono sistemi che rappresentano un motore di archiviazione con scalabilità orizzontale.  Ogni server di pagina è responsabile di un subset delle pagine nel database.  Ogni server di pagina controlla nominalmente fino a 128 GB o fino a 1 TB di dati. Nessun dato è condiviso in più di un server di pagina (all'esterno delle repliche del server di paging mantenute per la ridondanza e la disponibilità). Il compito di un server di pagina consiste nel fornire su richiesta le pagine del database ai nodi di calcolo e nel mantenere le pagine aggiornate man mano che le transazioni aggiornano i dati. I server di paging vengono mantenuti aggiornati tramite la riproduzione di record di log dal servizio di log. I server della pagina gestiscono anche le cache basate su SSD per migliorare le prestazioni. L'archiviazione a lungo termine delle pagine di dati viene mantenuta in Archiviazione di Azure per garantire maggiore affidabilità.

### <a name="log-service"></a>Servizio di log

Il servizio di log accetta i record di log dalla replica di calcolo primaria, li rende persistenti in una cache durevole e li trasmette al resto delle repliche di calcolo, in modo che possano aggiornare le cache, nonché i server di pagine pertinenti, in modo che i dati possano essere aggiornati in tale posizione. In questo modo, tutte le modifiche apportate ai dati dalla replica primaria di calcolo vengono propagate tramite il servizio di log a tutte le repliche di calcolo secondarie e ai server di pagine. Infine, i record di log vengono inviati all'archiviazione a lungo termine in archiviazione di Azure, un repository di archiviazione virtualmente infinito. Questo meccanismo Elimina la necessità di un troncamento del log frequente. Il servizio di log dispone anche di memoria locale e cache SSD per velocizzare l'accesso ai record di log.

### <a name="azure-storage"></a>Archiviazione di Azure

Archiviazione di Azure contiene tutti i file di dati in un database. I server di paging mantengono aggiornati i file di dati in archiviazione di Azure. Questa archiviazione viene usata a scopo di backup e per la replica tra le aree di Azure. I backup vengono implementati usando gli snapshot di archiviazione dei file di dati. Le operazioni di ripristino che usano gli snapshot sono veloci indipendentemente dalla dimensione dei dati. I dati possono essere ripristinati in qualsiasi punto nel tempo entro il periodo di conservazione dei backup del database.

## <a name="backup-and-restore"></a>Backup e ripristino

I backup sono basati su snapshot di file e quindi sono quasi istantanei. La separazione tra archiviazione e calcolo consente di eseguire il push dell'operazione di backup/ripristino al livello di archiviazione per ridurre il carico di elaborazione della replica di calcolo primaria. Di conseguenza, il backup del database non influisca sulle prestazioni del nodo di calcolo primario. Analogamente, il recupero temporizzato (ripristino temporizzato) viene eseguito ripristinando gli snapshot di file e, di conseguenza, non è un'operazione di dimensione dati. Il ripristino di un database con iperscalabilità nella stessa area di Azure è un'operazione a tempo costante ed è possibile ripristinare i database di più terabyte in pochi minuti anziché in ore o in giorni. La creazione di nuovi database tramite il ripristino di un backup esistente sfrutta anche questa funzionalità: la creazione di copie di database a scopo di sviluppo o test, anche di database con più terabyte, è fattibile in pochi minuti.

Per il ripristino geografico di database iperscalari, vedere [ripristino di un database con iperscalabilità in un'area diversa](#restoring-a-hyperscale-database-to-a-different-region).

## <a name="scale-and-performance-advantages"></a>Vantaggi di scalabilità e prestazioni

Con la possibilità di accelerare/diminuore la velocità dei nodi di calcolo di sola lettura aggiuntivi, l'architettura Hyperscale offre significative funzionalità di scalabilità di lettura e consente inoltre di liberare il nodo di calcolo primario per la gestione di più richieste di scrittura. Inoltre, i nodi di calcolo possono essere aumentati o diminuiti rapidamente grazie all'architettura di archiviazione condivisa dell'architettura Hyperscale.

## <a name="create-a-hyperscale-database"></a>Creare un database con iperscalabilità

È possibile creare un database con iperscalabilità usando il [portale di Azure](https://portal.azure.com), [T-SQL](/sql/t-sql/statements/create-database-transact-sql), [PowerShell](/powershell/module/azurerm.sql/new-azurermsqldatabase)o l' [interfaccia](/cli/azure/sql/db#az-sql-db-create)della riga di comando. I database con iperscalabilità sono disponibili solo con il [modello di acquisto basato su vCore](service-tiers-vcore.md).

Il comando T-SQL seguente crea un database Hyperscale. Nell'istruzione `CREATE DATABASE` è necessario specificare sia l'edizione che l'obiettivo del servizio. Per un elenco degli obiettivi di servizio validi, vedere i [limiti delle risorse](./resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen4) .

```sql
-- Create a Hyperscale Database
CREATE DATABASE [HyperscaleDB1] (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

Verrà creato un database con iperscalabilità nell'hardware quinta generazione con quattro core.

## <a name="upgrade-existing-database-to-hyperscale"></a>Aggiornare un database esistente a iperscalabilità

È possibile spostare i database esistenti nel database SQL di Azure in modo iperscalabile usando il [portale di Azure](https://portal.azure.com), [T-SQL](/sql/t-sql/statements/alter-database-transact-sql), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase)o l' [interfaccia](/cli/azure/sql/db#az-sql-db-update)della riga di comando. A questo punto, si tratta di una migrazione unidirezionale. Non è possibile spostare i database da iperscala a un altro livello di servizio, ad eccezione dell'esportazione e dell'importazione di dati. Per il modello di prova (verifica), è consigliabile creare una copia dei database di produzione ed eseguire la migrazione della copia a iperscalabilità. La migrazione di un database esistente nel database SQL di Azure al livello iperscalare è un'operazione di dimensionamento dei dati.

Il comando T-SQL seguente sposta un database nel livello di servizio Hyperscale. Nell'istruzione `ALTER DATABASE` è necessario specificare sia l'edizione che l'obiettivo del servizio.

```sql
-- Alter a database to make it a Hyperscale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>Connettersi a una replica scalabilità in lettura di un database Hyperscale

Nei database con iperscalabilità, l' `ApplicationIntent` argomento nella stringa di connessione fornita dal client determina se la connessione viene instradata alla replica di scrittura o a una replica secondaria di sola lettura. Se `ApplicationIntent` impostato su `READONLY` e il database non dispone di una replica secondaria, la connessione verrà indirizzata alla replica primaria e il comportamento predefinito sarà `ReadWrite` .

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Le repliche secondarie con iperscalabilità sono tutte identiche, usando lo stesso obiettivo del livello di servizio della replica primaria. Se è presente più di una replica secondaria, il carico di lavoro viene distribuito tra tutti i database secondari disponibili. Ogni replica secondaria viene aggiornata in modo indipendente. In questo modo, diverse repliche potrebbero avere latenza dei dati diversa rispetto alla replica primaria.

## <a name="database-high-availability-in-hyperscale"></a>Disponibilità elevata del database in iperscalabilità

Come in tutti gli altri livelli di servizio, l'iperscalabilità garantisce durabilità dei dati per le transazioni di cui è stato eseguito il commit indipendentemente dalla disponibilità della replica di calcolo L'entità del tempo di inattività dovuto alla mancata disponibilità della replica primaria dipende dal tipo di failover (pianificato e non pianificato) e dalla presenza di almeno una replica secondaria. In un failover pianificato, ad esempio un evento di manutenzione, il sistema crea la nuova replica primaria prima di avviare un failover oppure utilizza una replica secondaria esistente come destinazione del failover. In un failover non pianificato, ad esempio un errore hardware nella replica primaria, il sistema usa una replica secondaria come destinazione del failover se ne esiste una o crea una nuova replica primaria dal pool di capacità di calcolo disponibile. Nel secondo caso, la durata dei tempi di inattività è più lunga a causa di passaggi aggiuntivi necessari per creare la nuova replica primaria.

Per il contratto di contratto con iperscalabilità, vedere [SLA per database SQL di Azure](https://azure.microsoft.com/support/legal/sla/sql-database/).

## <a name="disaster-recovery-for-hyperscale-databases"></a>Ripristino di emergenza per database iperscalari

### <a name="restoring-a-hyperscale-database-to-a-different-region"></a>Ripristino di un database con iperscalabilità in un'area diversa

Se è necessario ripristinare un database con iperscalabilità nel database SQL di Azure in un'area diversa da quella in cui è attualmente ospitato, come parte di un'operazione di ripristino di emergenza o di un'esercitazione, una rilocazione o qualsiasi altro motivo, il metodo principale consiste nell'eseguire un ripristino geografico del database. Ciò comporta esattamente la stessa procedura usata per ripristinare qualsiasi altro database nel database SQL in un'area diversa:

1. Creare un [Server](logical-servers.md) nell'area di destinazione se non si dispone già di un server appropriato.  Il server deve appartenere alla stessa sottoscrizione del server originale (di origine).
2. Seguire le istruzioni riportate nell'argomento relativo al [ripristino geografico](./recovery-using-backups.md#geo-restore) della pagina relativa al ripristino di un database nel database SQL di Azure da backup automatici.

> [!NOTE]
> Poiché l'origine e la destinazione si trovano in aree separate, il database non può condividere l'archiviazione snapshot con il database di origine come nei ripristini non geografici, che vengono completati molto rapidamente. Nel caso di un ripristino geografico di un database con iperscalabilità, sarà un'operazione di dimensioni dei dati, anche se la destinazione si trova nell'area abbinata dell'archiviazione con replica geografica.  Ciò significa che l'esecuzione di un ripristino geografico può richiedere tempo proporzionale alla dimensione del database da ripristinare.  Se la destinazione è nell'area abbinata, la copia sarà all'interno di un'area, che sarà notevolmente più veloce rispetto a una copia tra aree, ma sarà comunque un'operazione di dimensioni dei dati.

## <a name="available-regions"></a><a name=regions></a>Aree disponibili

Il livello iperscalabile del database SQL di Azure è disponibile in tutte le aree, ma è abilitato per impostazione predefinita nelle seguenti aree elencate di seguito.
Se si vuole creare un database con iperscalabilità in un'area non elencata come supportata, è possibile inviare una richiesta di onboarding tramite portale di Azure. Per istruzioni, vedere la pagina relativa agli [aumenti delle quote di richiesta per il database SQL di Azure](quota-increase-request.md) . Quando si invia la richiesta, attenersi alle linee guida seguenti:

- Usare il tipo di quota del database SQL di [accesso all'area](quota-increase-request.md#region) .
- Nei dettagli del testo aggiungere lo SKU di calcolo/core totali, incluse le repliche leggibili.
- Specificare anche la TB stimata.

Aree abilitate:
- Australia orientale
- Australia sud-orientale
- Australia centrale
- Brasile meridionale
- Canada centrale
- Stati Uniti centrali
- Cina orientale 2
- Cina settentrionale 2
- Asia orientale
- Stati Uniti orientali
- Stati Uniti orientali 2
- Francia centrale
- Germania centro-occidentale
- Giappone orientale
- Giappone occidentale
- Corea centrale
- Corea meridionale
- Stati Uniti centro-settentrionali
- Europa settentrionale
- Norvegia orientale
- Norvegia occidentale
- Sudafrica settentrionale
- Stati Uniti centro-meridionali
- Asia sud-orientale
- Svizzera occidentale
- Regno Unito meridionale
- Regno Unito occidentale
- US DoD (area centrale)
- US DoD (area orientale)
- US Govt Arizona
- US Govt Texas
- Stati Uniti centro-occidentali
- Europa occidentale
- Stati Uniti occidentali
- Stati Uniti occidentali 2

## <a name="known-limitations"></a>Limitazioni note

Queste sono le limitazioni correnti per il livello di servizio con iperscalabilità di GA.  Stiamo lavorando attivamente per rimuovere il maggior numero possibile di queste limitazioni.

| Problema | Descrizione |
| :---- | :--------- |
| Il riquadro Gestisci backup per un server non Mostra database con iperscalabilità. Questi verranno filtrati dalla visualizzazione.  | L'iperscalabilità ha un metodo separato per la gestione dei backup, quindi non si applicano le impostazioni di conservazione dei backup Long-Term e temporizzazione. Di conseguenza, i database con iperscalabilità non vengono visualizzati nel riquadro Gestisci backup.<br><br>Per i database migrati a iperscalare da altri livelli di servizio del database SQL di Azure, i backup pre-migrazione vengono conservati per la durata del periodo di [conservazione dei backup](automated-backups-overview.md#backup-retention) del database di origine. Questi backup possono essere utilizzati per [ripristinare](recovery-using-backups.md#programmatic-recovery-using-automated-backups) il database di origine a un punto nel tempo prima della migrazione.|
| Ripristino temporizzato | Non è possibile ripristinare un database non iperscalabile come database iperscalabile e non è possibile ripristinare un database con iperscalabilità come database non iperscalabile. Per un database non iperscalato di cui è stata eseguita la migrazione a iperscalabile modificando il livello di servizio, eseguire il ripristino fino a un punto nel tempo prima della migrazione e all'interno del periodo di conservazione dei backup del database è possibile [a livello di programmazione](recovery-using-backups.md#programmatic-recovery-using-automated-backups). Il database ripristinato sarà non iperscalabile. |
| Se un database contiene uno o più file di dati di dimensioni superiori a 1 TB, la migrazione non riesce | In alcuni casi, potrebbe essere possibile aggirare questo problema compattando i file di grandi dimensioni in modo che siano inferiori a 1 TB. Se si esegue la migrazione di un database usato durante il processo di migrazione, assicurarsi che non ci siano file con dimensioni maggiori di 1 TB. Utilizzare la query seguente per determinare le dimensioni dei file di database. `SELECT *, name AS file_name, size * 8. / 1024 / 1024 AS file_size_GB FROM sys.database_files WHERE type_desc = 'ROWS'`;|
| Istanza gestita di SQL | Il Istanza gestita SQL di Azure non è attualmente supportato con i database con iperscalabilità. |
| Pool elastici |  I pool elastici non sono attualmente supportati con iperscalabilità.|
| La migrazione al livello di servizio Hyperscale è attualmente un'operazione unidirezionale | Una volta eseguita la migrazione di un database a iperscalabilità, non è possibile eseguirne la migrazione direttamente a un livello di servizio non iperscalabile. Attualmente, l'unico modo per eseguire la migrazione di un database da iperscalabilità a non iperscalare consiste nell'esportare/importare utilizzando un file BACPAC o altre tecnologie di spostamento dei dati (copia bulk, Azure Data Factory, Azure Databricks, SSIS e così via). Esportazione/importazione di BACPAC da portale di Azure, da PowerShell con [New-AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) o [New-AZSQLDATABASEIMPORT](/powershell/module/az.sql/new-azsqldatabaseimport), dall'interfaccia della riga di comando di Azure con il comando [AZ SQL DB Export](/cli/azure/sql/db#az-sql-db-export) e [AZ SQL DB Import](/cli/azure/sql/db#az-sql-db-import)e dall' [API REST](/rest/api/sql/databases%20-%20import%20export) non è supportata. L'importazione/esportazione di BACPAC per database con iperscala più piccoli (fino a 200 GB) è supportata con SSMS e [SqlPackage](/sql/tools/sqlpackage) versione 18,4 e versioni successive. Per i database di dimensioni maggiori, l'esportazione/importazione BACPAC può richiedere molto tempo e potrebbe non riuscire per vari motivi.|
| Migrazione di database con In-Memory oggetti OLTP | L'iperscalabilità supporta un subset di In-Memory oggetti OLTP, inclusi i tipi di tabella ottimizzata per la memoria, le variabili di tabella e i moduli compilati in modo nativo. Tuttavia, quando nel database di cui è in corso la migrazione è presente qualsiasi tipo di In-Memory oggetti OLTP, non è supportata la migrazione dai livelli di servizio Premium e business critical a iperscalabilità. Per eseguire la migrazione di un database di questo tipo a iperscalabilità, è necessario eliminare tutti gli oggetti In-Memory OLTP e le relative dipendenze. Dopo la migrazione del database, è possibile ricreare questi oggetti. Le tabelle ottimizzate per la memoria durevoli e non durevoli non sono attualmente supportate in iperscalabilità e devono essere ricreate come tabelle disco.|
| Replica geografica  | Non è ancora possibile configurare la replica geografica per l'iperscalabilità del database SQL di Azure. |
| Copia del database | La copia del database su iperscala è ora disponibile in anteprima pubblica. |
| Integrazione di Transparent Data Encryption/AKV | La crittografia trasparente del database con Azure Key Vault, comunemente definita Bring-your-own-key o BYOK, è attualmente disponibile in anteprima pubblica. |
| Funzionalità di database intelligenti | Ad eccezione dell'opzione "forza piano", tutte le altre opzioni di ottimizzazione automatica non sono ancora supportate in iperscalabilità: le opzioni potrebbero sembrare abilitate, ma non verranno eseguite raccomandazioni o azioni. |
| Analisi delle prestazioni della query | Le informazioni dettagliate sulle prestazioni delle query non sono attualmente supportate per i database con iperscalabilità. |
| Compatta database | DBCC SHRINKDATABASE o DBCC SHRINKFILE non è attualmente supportato per i database con iperscalabilità. |
| Verifica dell'integrità del database | DBCC CHECKDB non è attualmente supportato per i database con iperscalabilità. È possibile utilizzare DBCC CHECKFILEGROUP e DBCC CHECKTABLE come soluzione alternativa. Per informazioni dettagliate sulla gestione dell'integrità dei dati nel database SQL di Azure, vedere [integrità dei dati nel database SQL di Azure](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/) . |

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni su Hyperscale, vedere le [domande frequenti su Hyperscale](service-tier-hyperscale-frequently-asked-questions-faq.md).
- Per informazioni sui livelli di servizio, vedere [livelli di servizio](purchasing-models.md)
- Per informazioni sui limiti a livello del server e della sottoscrizione, vedere [Cenni preliminari sui limiti delle risorse in un server](resource-limits-logical-server.md) .
- Per informazioni sui limiti del modello di acquisto per un database singolo, vedere [Limiti del modello di acquisto basato su vCore per il database SQL di Azure per un database singolo](resource-limits-vcore-single-databases.md).
- Per un elenco di confronto delle funzionalità, vedere [Confronto tra le funzionalità: database SQL di Azure e SQL Server](features-comparison.md).
