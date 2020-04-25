---
title: Domande frequenti sulla API Cassandra per Azure Cosmos DB
description: Risposte alle domande frequenti sulla API Cassandra per Azure Cosmos DB.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: thvankra
ms.openlocfilehash: 2d6cae3a7a41eae05783d3bcc12ec2bfe8220c4c
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82148314"
---
# <a name="frequently-asked-questions-about-the-cassandra-api-for-azure-cosmos-db"></a>Domande frequenti sulla API Cassandra per Azure Cosmos DB

## <a name="what-are-some-key-differences-between-apache-cassandra-and-the-cassandra-api"></a>Quali sono alcune delle differenze principali tra Apache Cassandra e la API Cassandra?

- Apache Cassandra consiglia un limite di 100 MB per le dimensioni di una chiave di partizione. Il API Cassandra per Azure Cosmos DB consente un massimo di 10 GB per partizione.
- Apache Cassandra consente di disabilitare i commit durevoli. È possibile ignorare la scrittura nel log di commit e passare direttamente a memtables. Questo può causare una perdita di dati se il nodo si arresta prima che memtables venga scaricato su SSTables su disco. Azure Cosmos DB esegue sempre commit durevoli per prevenire la perdita di dati.
- Apache Cassandra può visualizzare prestazioni ridotte se il carico di lavoro prevede molte sostituzioni o eliminazioni. Il motivo è la rimozione definitiva che il carico di lavoro di lettura deve ignorare per recuperare i dati più recenti. Il API Cassandra non vedrà le prestazioni di lettura ridotte quando il carico di lavoro include molte sostituzioni o eliminazioni.
- Durante gli scenari di carichi di lavoro di sostituzione elevati, è necessario eseguire la compattazione per unire SSTables su disco. È necessaria un'Unione perché le scritture di Apache Cassandra sono solo Append. Più aggiornamenti vengono archiviati come singole voci SSTable che devono essere unite periodicamente. Questa situazione può anche causare una riduzione delle prestazioni di lettura durante la compattazione. Questo effetto sulle prestazioni non si verifica nell'API Cassandra perché l'API non implementa la compattazione.
- L'impostazione di un fattore di replica pari a 1 è possibile con Apache Cassandra. Tuttavia, comporta una disponibilità ridotta se l'unico nodo con i dati diventa inattivo. Questo non è un problema con la API Cassandra per Azure Cosmos DB perché esiste sempre un fattore di replica di 4 (quorum di 3).
- L'aggiunta o la rimozione di nodi in Apache Cassandra richiede un intervento manuale, oltre a un utilizzo elevato della CPU nel nuovo nodo, mentre i nodi esistenti spostano alcuni degli intervalli di token nel nuovo nodo. Questa situazione è identica quando si rimuove un nodo esistente. Tuttavia, il API Cassandra viene scalato in orizzontale senza problemi osservati nel servizio o nell'applicazione.
- Non è necessario impostare **num_tokens** in ogni nodo del cluster come in Apache Cassandra. Azure Cosmos DB gestisce completamente i nodi e gli intervalli di token.
- Il API Cassandra è completamente gestito. Non sono necessari i comandi **nodetool** , ad esempio riparazione e rimozione delle autorizzazioni, usati in Apache Cassandra.

## <a name="other-frequently-asked-questions"></a>Altre domande frequenti

### <a name="what-protocol-version-does-the-cassandra-api-support"></a>Quale versione del protocollo supporta il API Cassandra?

Il API Cassandra per Azure Cosmos DB supporta CQL versione 3. x. La compatibilità con CQL è basata sul repository di GitHub pubblico di [Apache Cassandra](https://github.com/apache/cassandra/blob/trunk/doc/cql3/CQL.textile). Per commenti e suggerimenti su come supportare altri protocolli, inviare un messaggio di posta elettronica tramite il [feedback vocale dell'utente](https://feedback.azure.com/forums/263030-azure-cosmos-db) o inviare un messaggio di posta elettronica a [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com).

### <a name="why-is-choosing-throughput-for-a-table-a-requirement"></a>Perché è necessario scegliere la velocità effettiva per una tabella?

Azure Cosmos DB imposta la velocità effettiva predefinita per il contenitore in base alla posizione in cui viene creata la tabella: portale di Azure o CQL.

Azure Cosmos DB offre garanzie di prestazioni e latenza, con limiti superiori nelle operazioni. Queste garanzie sono possibili quando il motore può applicare la governance nelle operazioni del tenant. La configurazione della velocità effettiva assicura la velocità effettiva e la latenza garantite, perché la piattaforma riserva tale capacità e garantisce l'esito positivo delle operazioni.
È possibile [modificare in modo elastico la velocità effettiva](manage-scale-cassandra.md) per trarre vantaggio dalla stagionalità dell'applicazione e ridurre i costi.

Il concetto di velocità effettiva è illustrato nell'articolo [Unità richiesta in Azure Cosmos DB](request-units.md). La velocità effettiva per una tabella è equamente distribuita tra le partizioni fisiche sottostanti.

### <a name="what-is-the-throughput-of-a-table-thats-created-through-cql"></a>Qual è la velocità effettiva di una tabella creata tramite CQL?

Azure Cosmos DB USA unità richiesta al secondo (UR/sec) come valuta per fornire la velocità effettiva. Per impostazione predefinita, le tabelle create tramite CQL hanno 400 ur. È possibile modificare le UR dalla portale di Azure.

CQL

```shell
CREATE TABLE keyspaceName.tablename (user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=1200
```

.NET

```csharp
int provisionedThroughput = 400;
var simpleStatement = new SimpleStatement($"CREATE TABLE {keyspaceName}.{tableName} (user_id int PRIMARY KEY, lastname text)");
var outgoingPayload = new Dictionary<string, byte[]>();
outgoingPayload["cosmosdb_provisioned_throughput"] = Encoding.UTF8.GetBytes(provisionedThroughput.ToString());
simpleStatement.SetOutgoingPayload(outgoingPayload);
```

### <a name="what-happens-when-throughput-is-used-up"></a>Cosa accade quando viene superata la velocità effettiva?

Azure Cosmos DB offre garanzie di prestazioni e latenza, con limiti superiori nelle operazioni. Queste garanzie sono possibili quando il motore può applicare la governance nelle operazioni del tenant. La configurazione della velocità effettiva assicura la velocità effettiva e la latenza garantite, perché la piattaforma riserva tale capacità e garantisce l'esito positivo delle operazioni.

Quando si supera questa capacità, viene ricevuto il messaggio di errore seguente che indica che la capacità è stata usata:

**0x1001 di overload: la richiesta non può essere elaborata perché la "frequenza delle richieste è grande"** 

È essenziale vedere quali operazioni (e il relativo volume) causano questo problema. È possibile ottenere un'idea della capacità utilizzata che supera la capacità con provisioning con le metriche sul portale di Azure. Quindi, è necessario assicurarsi che la capacità venga utilizzata quasi equamente tra tutte le partizioni sottostanti. Se si nota che una partizione sta consumando la maggior parte della velocità effettiva, è possibile che si disponga di un'inclinazione del carico di lavoro.

Sono disponibili metriche che mostrano come viene usata la velocità effettiva in ore, in giorni e per sette giorni, tra le partizioni o in modo aggregato. Per altre informazioni, vedere [Eseguire il monitoraggio e il debug con le metriche in Azure Cosmos DB](use-metrics.md).

I log di diagnostica sono illustrati nell'articolo [Registrazione diagnostica di Azure Cosmos DB](logging.md).

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>La chiave primaria corrisponde al concetto di chiave di partizione di Azure Cosmos DB?

Sì, la chiave di partizione viene usata per inserire l'entità nella posizione corretta. In Azure Cosmos DB viene usato per trovare la partizione logica corretta archiviata in una partizione fisica. Il concetto di partizionamento è illustrato in dettaglio nell'articolo [Partizionamento e ridimensionamento in Azure Cosmos DB](partition-data.md). Il punto di vista essenziale è che una partizione logica non deve superare il limite di 10 GB.

### <a name="what-happens-when-i-get-a-notification-that-a-partition-is-full"></a>Cosa accade quando si riceve una notifica che la partizione è piena?

Azure Cosmos DB è un sistema basato su contratto di servizio (SLA). Offre scalabilità illimitata, con garanzie di latenza, velocità effettiva, disponibilità e coerenza. Questa archiviazione illimitata è basata sulla scalabilità orizzontale orizzontale dei dati, usando il partizionamento come concetto chiave. Il concetto di partizionamento è illustrato in dettaglio nell'articolo [Partizionamento e ridimensionamento in Azure Cosmos DB](partition-data.md).

È necessario rispettare il limite di 10 GB per il numero di entità o elementi per partizione logica. Per assicurare la scalabilità dell'applicazione, è consigliabile *non* creare una partizione critica archiviando tutte le informazioni in una partizione ed eseguendo query su di essa. Questo errore può essere presente solo se i dati sono sbilanciati, ovvero se si dispone di una quantità elevata di dati per una chiave di partizione&nbsp;(oltre 10 GB). È possibile trovare la distribuzione dei dati tramite il portale di archiviazione. Per correggere l'errore, è necessario ricreare la tabella e scegliere una chiave primaria granulare (chiave di partizione), che consente una migliore distribuzione dei dati.

### <a name="can-i-use-the-cassandra-api-as-a-key-value-store-with-millions-or-billions-of-partition-keys"></a>È possibile usare il API Cassandra come archivio di valori chiave con milioni o miliardi di chiavi di partizione?

Azure Cosmos DB può archiviare un numero illimitato di dati grazie alla scalabilità orizzontale di archiviazione, Questa archiviazione è indipendente dalla velocità effettiva. Sì, è sempre possibile usare il API Cassandra solo per archiviare e recuperare le chiavi e i valori specificando la chiave primaria/partizione corretta. Queste singole chiavi ottengono la propria partizione logica e si trovano in cima a una partizione fisica senza problemi.

### <a name="can-i-create-more-than-one-table-with-the-cassandra-api"></a>È possibile creare più di una tabella con la API Cassandra?

Sì, è possibile creare più di una tabella con la API Cassandra. Ogni tabella viene trattata come una singola unità per velocità effettiva e archiviazione.

### <a name="can-i-create-more-than-one-table-in-succession"></a>È possibile creare più di una tabella in successione?

Azure Cosmos DB è un sistema governato dalle risorse per le attività del piano di dati e di controllo. I contenitori, ad esempio le raccolte e le tabelle, sono entità di runtime di cui viene eseguito il provisioning per una determinata capacità di velocità effettiva. La creazione di questi contenitori in rapida successione non è un'attività prevista e potrebbe essere limitata. Se sono presenti test che eliminano o creano immediatamente tabelle, provare a distanziarli.

### <a name="what-is-the-maximum-number-of-tables-that-i-can-create"></a>Qual è il numero massimo di tabelle che è possibile creare?

Non esiste alcun limite fisico al numero di tabelle. Se si dispone di un numero elevato di tabelle, in cui la dimensione totale costante supera i 10 TB di dati, che devono essere creati, non i soli dieci o centinaia, inviare un [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)messaggio di posta elettronica a.

### <a name="what-is-the-maximum-number-of-keyspaces-that-i-can-create"></a>Qual è il numero massimo di spazi di dati che è possibile creare?

Non esiste alcun limite fisico per il numero di spazi di elementi, perché si tratta di contenitori di metadati. Se si dispone di un numero elevato di spazi, inviare un messaggio di [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)posta elettronica a.

### <a name="can-i-bring-in-a-lot-of-data-after-starting-from-a-normal-table"></a>È possibile importare una grande quantità di dati dopo l'avvio da una tabella normale?

Sì. Presupponendo partizioni distribuite in modo uniforme, la capacità di archiviazione viene gestita automaticamente e aumenta man mano che si esegue il push di più dati. È quindi possibile importare con fiducia il maggior numero di dati necessari senza dover gestire e provisioning di nodi e altro ancora. Tuttavia, se si prevede una crescita immediata dei dati, è più sensato eseguire direttamente [il provisioning per la velocità effettiva prevista](set-throughput.md) anziché iniziare con un livello immediatamente inferiore.

### <a name="can-i-use-yaml-file-settings-to-configure-api-behavior"></a>È possibile usare le impostazioni dei file YAML per configurare il comportamento dell'API?

Il API Cassandra per Azure Cosmos DB fornisce la compatibilità a livello di protocollo per l'esecuzione di operazioni. Nasconde la complessità della gestione, del monitoraggio e della configurazione. Gli sviluppatori e gli utenti non devono preoccuparsi di disponibilità, rimozioni definitive, cache delle chiavi, cache di riga, filtro Bloom e numerose altre impostazioni. Il API Cassandra è incentrato sul fornire le prestazioni di lettura e scrittura necessarie senza il sovraccarico della configurazione e della gestione.

### <a name="will-the-cassandra-api-support-node-addition-cluster-status-and-node-status-commands"></a>I comandi per l'aggiunta del nodo, lo stato del cluster e lo stato del nodo API Cassandra supporteranno?

Il API Cassandra semplifica la pianificazione della capacità e risponde alle richieste di elasticità per la velocità effettiva e l'archiviazione. Con Azure Cosmos DB, viene effettuato il provisioning della velocità effettiva necessaria. È quindi possibile aumentare o ridurre le prestazioni di un numero qualsiasi di volte al giorno, senza preoccuparsi di aggiungere, eliminare o gestire i nodi. Non è necessario usare gli strumenti per la gestione dei nodi e dei cluster.

### <a name="what-happens-with-various-configuration-settings-for-keyspace-creation-like-simplenetwork"></a>Che cosa accade con diverse impostazioni di configurazione per la creazione di spazi di spazio dei dati come Simple/Network?

Azure Cosmos DB fornisce la distribuzione globale predefinita per motivi di disponibilità e bassa latenza. Non è necessario configurare repliche o altri elementi. Le Scritture sono sempre vincolate al quorum in qualsiasi area in cui si scrive, garantendo al tempo stesso le garanzie di prestazioni.

### <a name="what-happens-with-various-settings-for-table-metadata"></a>Cosa accade con diverse impostazioni per i metadati della tabella?

Azure Cosmos DB offre garanzie di prestazioni per letture, Scritture e velocità effettiva. Non è quindi necessario preoccuparsi di toccare le impostazioni di configurazione e di modificarle accidentalmente. Queste impostazioni includono il filtro Bloom, la memorizzazione nella cache, la possibilità di ripristino di lettura, gc_grace e la compressione memtable_flush_period.

### <a name="is-time-to-live-supported-for-cassandra-tables"></a>La durata è supportata per le tabelle Cassandra?

Sì, la Durata (TTL) è supportata.

### <a name="how-can-i-monitor-infrastructure-along-with-throughput"></a>Come è possibile monitorare l'infrastruttura insieme alla velocità effettiva?

Azure Cosmos DB è un servizio di piattaforma che consente di incrementare la produttività, senza doversi preoccupare della gestione e del monitoraggio dell'infrastruttura. Ad esempio, non è necessario monitorare lo stato dei nodi, lo stato della replica, GC e i parametri del sistema operativo in precedenza con diversi strumenti. È sufficiente occuparsi della velocità effettiva disponibile nelle metriche del portale per verificare se la velocità effettiva è limitata e quindi aumentare o ridurre la velocità effettiva. È possibile scegliere:

- Monitorare i [contratti](monitor-accounts.md) di contratto
- Usare le [metriche](use-metrics.md)
- Usare i [log di diagnostica](logging.md)

### <a name="which-client-sdks-can-work-with-the-cassandra-api"></a>Quali SDK client possono funzionare con la API Cassandra?

I driver client di Apache Cassandra SDK che usano CQLv3 sono stati usati per i programmi client. Se sono presenti altri driver usati o se si verificano problemi, inviare un messaggio di posta [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)elettronica a.

### <a name="are-composite-partition-keys-supported"></a>Sono supportate chiavi di partizione composite?

Sì, è possibile usare la sintassi normale per creare chiavi di partizione composite.

### <a name="can-i-use-sstableloader-for-data-loading"></a>È possibile usare sstableloader per il caricamento dei dati?

No, sstableloader non è supportato.

### <a name="can-i-pair-an-on-premises-apache-cassandra-cluster-with-the-cassandra-api"></a>È possibile associare un cluster Apache Cassandra locale al API Cassandra?

Attualmente, Azure Cosmos DB offre un'esperienza ottimizzata per un ambiente cloud senza l'overhead delle operazioni. Se è necessario associare, inviare un messaggio di [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) posta elettronica a con una descrizione dello scenario. Stiamo lavorando a un'offerta per aiutare a abbinare il cluster Cassandra locale o cloud alla API Cassandra per Azure Cosmos DB.

### <a name="does-the-cassandra-api-provide-full-backups"></a>Il API Cassandra fornisce backup completi?

Azure Cosmos DB offre due backup completi gratuiti eseguiti a intervalli di quattro ore in tutte le API. Quindi non è necessario configurare una pianificazione di backup. 

Se si desidera modificare la conservazione e la frequenza, inviare un [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) messaggio di posta elettronica o generare un caso di supporto. Informazioni sulla funzionalità di backup sono disponibili nell'articolo [Backup online automatico e ripristino con Azure Cosmos DB](../synapse-analytics/sql-data-warehouse/backup-and-restore.md).

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>Come viene gestito il failover dall'account dell'API Cassandra se un'area diventa inattiva?

Il API Cassandra prende in prestito dalla piattaforma distribuita a livello globale di Azure Cosmos DB. Per assicurarsi che l'applicazione possa tollerare i tempi di inattività del Data Center, abilitare almeno un'altra area per l'account nel portale di Azure. Per ulteriori informazioni, vedere [disponibilità elevata con Azure Cosmos DB](high-availability.md).

È possibile aggiungere il numero di aree desiderato per l'account e controllare in quale potrà essere effettuato il failover specificando una priorità di failover. Per usare il database, è necessario includere anche un'applicazione. Nel corso di questa operazione, i clienti non riscontrano tempi di inattività.

### <a name="does-the-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>Il API Cassandra indicizza tutti gli attributi di un'entità per impostazione predefinita?

No. Il API Cassandra supporta gli [indici secondari](cassandra-secondary-index.md), che si comportano in modo analogo ad Apache Cassandra. Per impostazione predefinita, l'API non indicizza ogni attributo.  


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>È possibile usare l'SDK della nuova API Cassandra in locale con l'emulatore?

Sì, è possibile. Per informazioni dettagliate su come abilitarlo, vedere l'articolo [usare l'emulatore di Azure Cosmos per lo sviluppo e il test locali](local-emulator.md#cassandra-api) .


### <a name="how-can-i-migrate-data-from-apache-cassandra-clusters-to-azure-cosmos-db"></a>Come è possibile eseguire la migrazione dei dati da cluster Apache Cassandra a Azure Cosmos DB?

Per informazioni sulle opzioni di migrazione, vedere l'esercitazione [eseguire la migrazione dei dati a un account API Cassandra in Azure Cosmos DB](cassandra-import-data.md) .


### <a name="where-can-i-give-feedback-on-cassandra-api-features"></a>Dove è possibile inviare commenti e suggerimenti sulle funzionalità di API Cassandra?

Fornire feedback sul sito del [feedback User Voice](https://feedback.azure.com/forums/263030-azure-cosmos-db).

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md

## <a name="next-steps"></a>Passaggi successivi

- Introduzione alla [scalabilità elastica di un account Azure Cosmos DB API Cassandra](manage-scale-cassandra.md).
