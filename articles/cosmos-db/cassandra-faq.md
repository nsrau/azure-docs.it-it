---
title: Domande frequenti sull'API di Azure Cosmos DB per Cassandra.Frequently asked questions on Azure Cosmos DB API for Cassandra.
description: Risposte alle domande frequenti sull'API di Azure Cosmos DB per Cassandra.Get answers to frequently asked questions about Azure Cosmos DB API for Cassandra.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: thvankra
ms.openlocfilehash: 416f0c5f995a101298e84c81317c7d39fb5d43f8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727385"
---
# <a name="frequently-asked-questions-about-the-azure-cosmos-db-api-for-cassandra"></a>Domande frequenti sull'API di Azure Cosmos DB per Cassandra

## <a name="what-are-some-key-differences-between-apache-cassandra-and-cassandra-api"></a>Quali sono alcune differenze chiave tra Apache Cassandra e Cassandra API?

- Apache Cassandra consiglia un limite di 100 MB sulla dimensione di una chiave di partizione. L'API Cassandra consente fino a 10 GB per partizione.
- Apache Cassandra consente di disabilitare i commit durevoli, ovvero saltare la scrittura nel log di commit e passare direttamente a Memtable(s). Ciò può causare la perdita di dati se il nodo si arresta prima che Memtables venga scaricato in SStables su disco. Cosmos DB esegue sempre impegni durevoli in modo da non avere mai la perdita di dati.
- Apache Cassandra può vedere una diminuzione delle prestazioni se il carico di lavoro comporta molte sostitute e/o eliminazioni. Il motivo è tombstones che il carico di lavoro di lettura deve ignorare per recuperare i dati più recenti. L'API Cassandra non vedrà una riduzione delle prestazioni di lettura quando il carico di lavoro presenta molte opzioni di sostituzione e/o eliminazione.
- Durante gli scenari di carico di lavoro con sostituzione elevata, la compattazione deve essere eseguita per unire le sSTables su disco (è necessaria l'unione perché le scritture di Apache Cassandra sono solo aggiunte, pertanto più aggiornamenti vengono archiviati come singole voci SSTable che devono essere unite periodicamente). Ciò può anche portare a una riduzione delle prestazioni di lettura durante la compattazione. Ciò non si verifica nell'API Cassandra in quanto non implementa la compattazione.
- L'impostazione di un fattore di replica pari a 1 è possibile con Apache Cassandra. Tuttavia, porta a una bassa disponibilità se l'unico nodo con i dati scende. Questo non è un problema con azure Cosmos DB Cassandra API perché c'è sempre un fattore di replica di 4 (quorum di 3).
- L'aggiunta/rimozione di nodi in Apache Cassandra richiede un grande intervento manuale, ma anche una CPU elevata sul nuovo nodo mentre i nodi esistenti spostano alcuni dei relativi intervalli di token nel nuovo nodo. Questo è lo stesso quando si rimuove la creazione di autorizzazioni per un nodo esistente. Tuttavia, la scalabilità orizzontale viene eseguita senza problemi sotto il cofano nell'API Cassandra di Azure Cosmos DB, senza problemi osservati nel servizio/applicazione.
- Non è necessario impostare num_tokens su ogni nodo del cluster come in Apache Cassandra. I nodi e gli intervalli di token sono completamente gestiti da Cosmos DB.
- Azure Cosmos DB Cassandra API is fully managed so you don't require the nodetool commands such as repair, decommission etc. that are used in Apache Cassandra.

## <a name="other-frequently-asked-questions"></a>Altre domande frequenti

### <a name="what-is-the-protocol-version-supported-by-azure-cosmos-db-cassandra-api-is-there-a-plan-to-support-other-protocols"></a>Qual è la versione del protocollo supportata dall'API Cassandra di Azure Cosmos DB? È previsto il supporto per altri protocolli?

L'API Cassandra di Azure Cosmos supporta CQL versione 3.x.Azure Cosmos DB Cassandra API supports CQL version 3.x. La sua compatibilità CQL si basa sul repository pubblico [Apache Cassandra GitHub](https://github.com/apache/cassandra/blob/trunk/doc/cql3/CQL.textile). In caso di feedback sul supporto di altri protocolli, segnalarlo sul sito del [feedback User Voice](https://feedback.azure.com/forums/263030-azure-cosmos-db) o inviare un messaggio di posta elettronica all'indirizzo [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com).

### <a name="why-is-choosing-a-throughput-for-a-table-a-requirement"></a>Perché la scelta di una velocità effettiva per una tabella rappresenta un requisito?

Azure Cosmos DB configura la velocità effettiva predefinita per il contenitore in base alla posizione in cui è stata creata la tabella, ovvero nel portale o in CQL.
Azure Cosmos DB offre garanzie di prestazioni e latenza, con limiti superiori nelle operazioni. Questa garanzia è possibile quando il motore può applicare la governance nelle operazioni del tenant. La configurazione della velocità effettiva assicura la velocità effettiva e la latenza garantite, perché la piattaforma riserva tale capacità e garantisce l'esito positivo delle operazioni.
È possibile [modificare in modo elastico](manage-scale-cassandra.md) la velocità effettiva per trarre vantaggio dalla stagionalità dell'applicazione e risparmiare sui costi.

Il concetto di velocità effettiva è illustrato nell'articolo [Unità richiesta in Azure Cosmos DB](request-units.md). La velocità effettiva per una tabella viene distribuita equamente tra le partizioni fisiche sottostanti.

### <a name="what-is-the-default-rus-of-table-when-created-through-cql-what-if-i-need-to-change-it"></a>Qual è la UR/s predefinita di una tabella creata tramite CQL? In che modo la si può modificare, se necessario?

Azure Cosmos DB usa le unità richiesta al secondo (UR/s) come misura per fornire velocità effettiva. Le tabelle create con CQL hanno 400 UR. È possibile modificare le UR dal portale.

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

Azure Cosmos DB offre garanzie di prestazioni e latenza, con limiti superiori nelle operazioni. Questa garanzia è possibile quando il motore può applicare la governance nelle operazioni del tenant. Ciò è possibile in base alla configurazione della velocità effettiva, che assicura la velocità effettiva e la latenza garantite, perché la piattaforma riserva tale capacità e garantisce l'esito positivo delle operazioni.
Quando si supera questa capacità, viene visualizzato un messaggio di errore di overload a indicare che è stata superata la capacità.
0x1001 Overloaded: the request cannot be processed because "Request Rate is large" (0x1001 In overload: impossibile elaborare la richiesta perché "La frequenza delle richieste è troppo elevata"). A questo punto è essenziale esaminare le operazioni che, unitamente al loro volume, determinano questo problema. Le metriche del portale consentono di avere un'idea della capacità utilizzata che supera la capacità di cui è stato effettuato il provisioning. È poi necessario verificare che la capacità venga usata pressoché equamente tra tutte le partizioni sottostanti. Se si nota che la maggior parte della velocità effettiva viene usata da una partizione, si è in presenza di un'asimmetria del carico di lavoro.

Sono disponibili metriche che indicano in che modo la velocità effettiva viene usata in intervalli di ore, giorni e sette giorni, tra le partizioni o in modo aggregato. Per altre informazioni, vedere [Eseguire il monitoraggio e il debug con le metriche in Azure Cosmos DB](use-metrics.md).

I log di diagnostica sono illustrati nell'articolo [Registrazione diagnostica di Azure Cosmos DB](logging.md).

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>La chiave primaria corrisponde al concetto di chiave di partizione di Azure Cosmos DB?

Sì, la chiave di partizione viene usata per collocare l'entità nella posizione corretta. In Azure Cosmos DB consente di individuare la partizione logica corretta archiviata in una partizione fisica. Il concetto di partizionamento è illustrato in dettaglio nell'articolo [Partizionamento e ridimensionamento in Azure Cosmos DB](partition-data.md). Il fulcro della questione è che a oggi una partizione logica non deve superare il limite di 10 GB.

### <a name="what-happens-when-i-get-a-quota-full-notification-indicating-that-a-partition-is-full"></a>Cosa accade se viene visualizzata una notifica di raggiungimento della quota che indica che la partizione è piena?

Azure Cosmos DB è un sistema basato su contratti di servizio che offre scalabilità illimitata, con garanzie di latenza, velocità effettiva, disponibilità e coerenza. Questo tipo di archiviazione illimitata si basa sulla scalabilità orizzontale dei dati usando il partizionamento come concetto chiave. Il concetto di partizionamento è illustrato in dettaglio nell'articolo [Partizionamento e ridimensionamento in Azure Cosmos DB](partition-data.md).

È necessario rispettare il limite di 10 GB sul numero di entità o elementi per partizione logica. Per assicurare la scalabilità dell'applicazione, è consigliabile *non* creare una partizione critica archiviando tutte le informazioni in una partizione ed eseguendo query su di essa. Questo errore può verificarsi solo in presenza di dati asimmetrici, ovvero con un numero elevato di dati per una chiave di partizione (più di 10&nbsp;GB). La distribuzione dei dati è disponibile nel portale di archiviazione. Un modo per correggere l'errore consiste nel creare nuovamente la tabella e scegliere una chiave primaria (di partizione) granulare, che consente una migliore distribuzione dei dati.

### <a name="is-it-possible-to-use-cassandra-api-as-key-value-store-with-millions-or-billions-of-individual-partition-keys"></a>È possibile usare l'API Cassandra come archivio di valori/chiavi con milioni o miliardi di singole chiavi di partizione?

Azure Cosmos DB può archiviare un numero illimitato di dati grazie alla scalabilità orizzontale di archiviazione, in modo indipendente dalla velocità effettiva. Sì, è comunque possibile usare l'API Cassandra per archiviare e recuperare chiavi e valori specificando la chiave primaria o di partizione corretta. Queste singole chiavi ottengono una specifica partizione logica e si trovano al di sopra della partizione fisica.

### <a name="is-it-possible-to-create-more-than-one-table-with-apache-cassandra-api-of-azure-cosmos-db"></a>È possibile creare più tabelle con l'API Apache Cassandra di Azure Cosmos DB?

Sì, è possibile creare più di una tabella con l'API Apache Cassandra. Ogni tabella viene trattata come una singola unità per velocità effettiva e archiviazione.

### <a name="is-it-possible-to-create-more-than-one-table-in-succession"></a>È possibile creare più di una tabella in successione?

Azure Cosmos DB è un sistema basato su risorse per attività a livello di dati e di controllo. I contenitori come le raccolte e le tabelle sono entità di runtime sottoposte a provisioning per una determinata capacità di velocità effettiva. La creazione di questi contenitori in rapida successione non è un'attività prevista e potrebbe essere limitata. Se si hanno test che creano/eliminano tabelle immediatamente, provare a distanziarli.

### <a name="what-is-maximum-number-of-tables-that-can-be-created"></a>Qual è il numero massimo di tabelle che è possibile creare?

Non c'è alcun limite fisico sul numero [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) di tabelle, inviare un'e-mail a se si dispone di un gran numero di tabelle (dove la dimensione totale costante va oltre 10 TB di dati) che devono essere creati da soliti 10s o 100s.

### <a name="what-is-the-maximum--of-keyspace-that-we-can-create"></a>Qual è il numero massimo di keyspace che è possibile creare?

Non esiste alcun limite fisico al numero di spazi chiave in [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) quanto si tratta di contenitori di metadati, inviare un messaggio di posta elettronica se si dispone di un numero elevato di spazi chiave per qualche motivo.

### <a name="is-it-possible-to-bring-in-lot-of-data-after-starting-from-normal-table"></a>È possibile inserire una quantità elevata di dati dopo l'avvio da una tabella normale?

Sì, presupponendo partizioni distribuite uniformemente, la capacità di archiviazione viene gestita automaticamente e aumenta man mano che si esegue il push di più dati. È quindi possibile importare tranquillamente la quantità di dati necessaria senza la gestione e il provisioning dei nodi e così via. Tuttavia, se si prevede una grande crescita immediata dei dati, è più opportuno eseguire direttamente [il provisioning per la velocità effettiva prevista](set-throughput.md) anziché iniziare a rilasciarla e aumentarla immediatamente.

### <a name="is-it-possible-to-supply-yaml-file-settings-to-configure-apache-casssandra-api-of-azure-cosmos-db-behavior"></a>È possibile fornire le impostazioni del file yaml per configurare il comportamento dell'API Apache Cassandra di Azure Cosmos DB?

L'API Apache Cassandra di Azure Cosmos DB è un servizio di piattaforma. Fornisce compatibilità a livello di protocollo per l'esecuzione di operazioni. Nasconde la complessità della gestione, del monitoraggio e della configurazione. Gli utenti e gli sviluppatori non devono preoccuparsi di disponibilità, rimozioni definitive, cache delle chiavi, cache delle righe, filtri Bloom e di molte altre impostazioni. L'API Apache Cassandra di Azure Cosmos DB fornisce le prestazioni di lettura e di scrittura necessarie, senza l'overhead della configurazione e della gestione.

### <a name="will-apache-cassandra-api-for-azure-cosmos-db-support-node-additioncluster-statusnode-status-commands"></a>L'API Apache Cassandra per Azure Cosmos DB supporterà i comandi per aggiunta nodo/stato del cluster/stato del nodo?

L'API Apache Cassandra è un servizio piattaforma che semplifica la pianificazione della capacità e la risposta a richieste di elasticità per la velocità effettiva e le risorse di archiviazione. Con Azure Cosmos DB è possibile effettuare il provisioning della velocità effettiva necessaria. È quindi possibile aumentare o ridurre il numero di unità ogni volta che risulta necessario durante la giornata, senza doversi preoccupare di aggiungere/eliminare nodi o gestirli. Non è quindi necessario usare alcuno strumento di gestione di nodi o cluster.

### <a name="what-happens-with-respect-to-various-config-settings-for-keyspace-creation-like-simplenetwork"></a>Che cosa accade alle diverse impostazioni di configurazione per la creazione del keyspace (semplice/rete)?

Azure Cosmos DB offre la distribuzione globale predefinita per motivi di disponibilità e di bassa latenza. Non è necessario configurare repliche e altro. Il commit di tutte le scritture viene sempre eseguito in modo durevole in qualsiasi area in cui si scrivono fornendo al contempo garanzie di prestazioni.

### <a name="what-happens-with-respect-to-various-settings-for-table-metadata-like-bloom-filter-caching-read-repair-change-gc_grace-compression-memtable_flush_period-and-more"></a>Che cosa accade alle diverse impostazioni per i metadati di tabella come i filtri Bloom, la memorizzazione nella cache, le modifiche di correzione delle operazioni di lettura, gc_grace e memtable_flush_period di compressione e così via?

Azure Cosmos DB offre prestazioni per operazioni di lettura/scrittura e per velocità effettiva senza che sia necessario modificare le impostazioni di configurazione, evitando così il rischio di alterazioni accidentali.

### <a name="is-time-to-live-ttl-supported-for-cassandra-tables"></a>La Durata (TTL) è supportata per le tabelle di Cassandra?

Sì, la Durata (TTL) è supportata.

### <a name="is-it-possible-to-monitor-node-status-replica-status-gc-and-os-parameters-earlier-with-various-tools-what-needs-to-be-monitored-now"></a>È possibile monitorare in anticipo lo stato dei nodi, lo stato delle repliche, gc e i parametri del sistema operativo con vari strumenti? Cosa deve essere monitorato ora?

Azure Cosmos DB è un servizio di piattaforma che consente di incrementare la produttività, senza doversi preoccupare della gestione e del monitoraggio dell'infrastruttura. È sufficiente occuparsi della velocità effettiva disponibile nelle metriche del portale per individuare eventuali limitazioni delle richieste e aumentare o diminuire la velocità effettiva.
Monitorare i [contratti di servizio](monitor-accounts.md).
Usare la [metrica](use-metrics.md) Usare i [log di diagnostica](logging.md).

### <a name="which-client-sdks-can-work-with-apache-cassandra-api-of-azure-cosmos-db"></a>Quali SDK client possono essere usati con l'API Apache Cassandra di Azure Cosmos DB?

I driver client di Apache Cassandra SDK che utilizzano CQLv3 sono stati utilizzati per i programmi client. Se si dispone di altri driver che si utilizzano [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)o se si verificano problemi, inviare la posta a .

### <a name="is-composite-partition-key-supported"></a>La chiave di partizione composta è supportata?

Sì, è possibile usare la sintassi normale per creare una chiave di partizione composta.

### <a name="can-i-use-sstableloader-for-data-loading"></a>Posso usare sstableloader per il caricamento dei dati?

No, sstableloader non è supportato.

### <a name="can-an-on-premises-apache-cassandra-cluster-be-paired-with-azure-cosmos-dbs-cassandra-api"></a>Un cluster Apache Cassandra locale può essere associato all'API Cassandra di Azure Cosmos DB?

Attualmente Azure Cosmos DB offre un'esperienza ottimizzata per l'ambiente cloud, senza l'overhead delle operazioni. Se è necessaria l'associazione, inviare la posta a [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) una descrizione dello scenario. Stiamo lavorando per aiutare ad abbinare il cluster Cassandra locale/diverso cloud all'API Cassandra di Cosomos DB.

### <a name="does-cassandra-api-provide-full-backups"></a>L'API Cassandra fornisce backup completi?

Azure Cosmos DB fornisce attualmente due backup completi gratuiti, creati a intervalli di quattro ore, in tutte le API. In questo modo è possibile evitare di configurare una pianificazione di backup e altri aspetti.
Se si desidera modificare la conservazione e [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) la frequenza, inviare un messaggio di posta elettronica a o generare un caso di supporto. Informazioni sulla funzionalità di backup sono disponibili nell'articolo [Backup online automatico e ripristino con Azure Cosmos DB](../synapse-analytics/sql-data-warehouse/backup-and-restore.md).

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>Come viene gestito il failover dall'account dell'API Cassandra se un'area diventa inattiva?

L'API Cassandra di Azure Cosmos DB sfrutta la piattaforma distribuita a livello globale di Azure Cosmos DB. Affinché l'applicazione possa tollerare i tempi di inattività del data center, abilitare almeno un'altra area per l'account nel portale di Azure Cosmos DB, come illustrato nell'articolo relativo allo [sviluppo con account Azure Cosmos DB tra più aree](high-availability.md). Si può impostare la priorità dell'area usando il portale. Vedere l'articolo relativo allo [sviluppo con account Azure Cosmos DB tra più aree](high-availability.md).

È possibile aggiungere il numero di aree desiderato per l'account e controllare in quale potrà essere effettuato il failover specificando una priorità di failover. Per usare il database, è necessario includere anche un'applicazione. Nel corso di questa operazione, i clienti non riscontrano tempi di inattività.

### <a name="does-the-apache-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>L'API Apache Cassandra indicizza tutti gli attributi di un'entità per impostazione predefinita?

No. L'API Cassandra supporta [gli indici secondari,](cassandra-secondary-index.md)che si comporta in modo molto simile ad Apache Cassandra. Non indicizza ogni attributo per impostazione predefinita.  


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>È possibile usare l'SDK della nuova API Cassandra in locale con l'emulatore?

Sì, questo è supportato. Potete trovare i dettagli di come abilitare questo [qui](local-emulator.md#cassandra-api)


### <a name="how-can-i-migrate-data-from-their-apache-cassandra-clusters-to-cosmos-db"></a>Come è possibile eseguire la migrazione dei dati dai loro cluster Apache Cassandra a Cosmos DB?

Ulteriori informazioni sulle opzioni di migrazione sono disponibili [qui](cassandra-import-data.md).


### <a name="feature-x-of-regular-cassandra-api-isnt-working-as-today-where-can-the-feedback-be-provided"></a>Attualmente la funzionalità x dell'API Cassandra standard non funziona: dove è possibile fornire commenti e suggerimenti?

Fornire feedback sul sito del [feedback User Voice](https://feedback.azure.com/forums/263030-azure-cosmos-db).

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md

## <a name="next-steps"></a>Passaggi successivi

- Introduzione alla [scalabilità elastica di un account API Cassandra di Azure Cosmos DB.](manage-scale-cassandra.md)
