---
title: Domande frequenti sulle diverse API in Azure Cosmos DB
description: Risposte alle domande frequenti su Azure Cosmos DB, un servizio di database multimodello distribuito a livello globale. Informazioni su capacità, livelli di prestazioni e scalabilità.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 4bd29ce3bf2cc7cd69f86dbf172d3cd9a2044e79
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91570358"
---
# <a name="frequently-asked-questions-about-different-apis-in-azure-cosmos-db"></a>Domande frequenti sulle diverse API in Azure Cosmos DB

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Quali sono i casi d'uso tipici per Azure Cosmos DB?

Azure Cosmos DB è la scelta ideale per le nuove applicazioni Web, per dispositivi mobili, di gioco e IoT in cui scalabilità automatica, prestazioni prevedibili, tempi di risposta rapidi nell'ordine di millisecondi e la possibilità di eseguire query su dati privi di schema sono importanti. Azure Cosmos DB si presta allo sviluppo rapido e al supporto dell'iterazione continua dei modelli di dati delle applicazioni. Le applicazioni che gestiscono contenuto e dati generati dall'utente rappresentano [casi d'uso comuni di Azure Cosmos DB](use-cases.md).

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Come vengono offerte prestazioni prevedibili in Azure Cosmos DB?

Un'[unità richiesta](request-units.md) (UR) è l'unità di misura della velocità effettiva in Azure Cosmos DB. Una velocità effettiva di 1 UR corrisponde alla velocità effettiva dell'operazione GET su un documento da 1 KB. Ogni operazione in Azure Cosmos DB, incluse letture, scritture, query SQL ed esecuzioni di stored procedure, ha un valore di UR deterministico basato sulla velocità effettiva necessaria per completare l'operazione. Invece di considerare CPU, I/O e memoria e il modo in cui ognuno di essi influisce sulla velocità effettiva dell'applicazione, è possibile ragionare in termini di singola misura di UR.

Ogni contenitore di Azure Cosmos può essere configurato con provisioning della velocità effettiva in termini di UR di velocità effettiva al secondo. Per applicazioni di qualsiasi dimensione, è possibile effettuare un benchmark delle singole richieste per misurarne i valori di UR ed effettuare il provisioning di un contenitore per gestire il totale delle unità richiesta tra tutte le richieste. È anche possibile aumentare o ridurre la velocità effettiva del contenitore con il variare delle esigenze dell'applicazione. Per altre informazioni sulle unità richiesta e su come determinare le esigenze a livello di contenitore, provare il [calcolatore della velocità effettiva](https://www.documentdb.com/capacityplanner).

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>In che modo Azure Cosmos DB supporta diversi modelli di dati, ad esempio chiave/valore, colonne, documenti e grafi?

I modelli di dati chiave/valore (tabella), colonne, documenti e grafi sono tutti supportati a livello nativo grazie alla progettazione ARS (atom, record e sequenze) su cui si basa Azure Cosmos DB. È facilmente possibile eseguire il mapping e la proiezione di atom, record e sequenze in diversi modelli di dati. Le API per un subset di modelli sono già disponibili (SQL, MongoDB, Tabella e Gremlin) e altre, specifiche di modelli di dati aggiuntivi, saranno disponibili in futuro.

Azure Cosmos DB ha un motore di indicizzazione indipendente dallo schema che può indicizzare automaticamente tutti i dati inseriti senza richiedere schemi o indici secondari agli sviluppatori. Il motore si basa su un set di layout di indice logici (invertiti, colonne, albero) che separano il layout di archiviazione dai sottosistemi di elaborazione di indici e query. Cosmos DB supporta anche un set di API e protocolli di collegamento in modalità estendibile e consente di convertirli in modo efficiente nel modello di dati principale (1) e nei layout di indice logici (2), supportando in modo esclusivo più modelli di dati a livello nativo.

### <a name="can-i-use-multiple-apis-to-access-my-data"></a>È possibile usare più API per accedere ai dati personali?

Azure Cosmos DB è il servizio di database di Microsoft multimodello distribuito a livello globale. Anche se la modalità multimodello indica che Azure Cosmos DB supporta più API e più modelli di dati, le diverse API usano formati di dati diversi per il protocollo di archiviazione e collegamento. Ad esempio, SQL usa JSON, MongoDB usa BSON, Tabella usa EDM, Cassandra usa CQL, Gremlin usa il formato JSON. Di conseguenza, è consigliabile usare la stessa API per tutti gli accessi ai dati in un determinato account.

Ogni API opera in modo indipendente, ad eccezione delle API Gremlin e SQL, che sono intercambiabili.

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Azure Cosmos DB è conforme alla normativa HIPAA?

Sì, Azure Cosmos DB è conforme alla normativa HIPAA. La normativa HIPAA stabilisce i requisiti per l'uso, la divulgazione e la protezione delle informazioni sanitarie personali sensibili. Per altre informazioni, visitare il [Centro protezione Microsoft](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Quali sono i limiti di archiviazione di Azure Cosmos DB?

In Azure Cosmos DB non esiste alcun limite alla quantità totale di dati che può essere archiviata in un contenitore.

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Quali sono i limiti di velocità effettiva di Azure Cosmos DB?

In Azure Cosmos DB non esiste alcun limite alla velocità effettiva totale che può essere supportata da un contenitore. Lo scopo primario è distribuire il carico di lavoro in modo abbastanza uniforme tra un numero sufficientemente elevato di chiavi di partizione.

### <a name="are-direct-and-gateway-connectivity-modes-encrypted"></a>Le modalità di connettività Diretta e Gateway sono crittografate?

Sì, entrambe le modalità sono completamente crittografate.

### <a name="how-much-does-azure-cosmos-db-cost"></a>Quanto costa Azure Cosmos DB?

Per informazioni dettagliate, vedere la pagina [Prezzi di Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/). Gli addebiti per l'utilizzo di Azure Cosmos DB sono determinati dal numero di contenitori sottoposti a provisioning, dal numero di ore in cui i contenitori sono stati online e dalla velocità effettiva di provisioning per ogni contenitore.

### <a name="is-a-free-account-available"></a>È disponibile un account gratuito?

Sì, è possibile ottenere un account gratuito per un tempo limitato, senza alcun impegno. Per iscriversi, visitare la pagina [Prova gratuitamente Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/). Per altre informazioni, vedere [Domande frequenti su Azure Cosmos DB](#try-cosmos-db).

I nuovi utenti possono iscriversi per ottenere un [account gratuito di Azure](https://azure.microsoft.com/free/), che è valido 30 giorni e include un credito per provare tutti i servizi di Azure. Se si ha una sottoscrizione di Visual Studio, si ha anche diritto a [crediti Azure gratuiti](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) da usare per qualsiasi servizio di Azure.

È anche possibile usare l'[emulatore di Azure Cosmos DB](local-emulator.md) per sviluppare e testare gratuitamente l'applicazione in locale, senza creare una sottoscrizione di Azure. Quando si è soddisfatti del funzionamento dell'applicazione nell'emulatore di Azure Cosmos DB, è possibile iniziare a usare l'account Azure Cosmos DB nel cloud.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Come è possibile ottenere informazioni e supporto aggiuntivi per Azure Cosmos DB?

In caso di domande tecniche, inviare un messaggio a uno di questi due forum di domande e risposte:

* [Pagina delle domande di Domande e risposte Microsoft](https://docs.microsoft.com/answers/topics/azure-cosmos-db.html)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Stack Overflow è ideale per domande relative alla programmazione. Assicurarsi che la domanda sia [pertinente](https://stackoverflow.com/help/on-topic) e [specificare il numero massimo possibile di dettagli, in modo che la domanda sia chiara e sia possibile fornire una risposta](https://stackoverflow.com/help/how-to-ask).

Per richiedere nuove funzionalità, creare una nuova richiesta sul sito di [User Voice](https://feedback.azure.com/forums/263030-azure-cosmos-db).

Per risolvere un problema relativo all'account, inviare una [richiesta di supporto](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) nel portale di Azure.

## <a name="try-azure-cosmos-db-subscriptions"></a><a id="try-cosmos-db"></a>Provare gratuitamente Azure Cosmos DB

È ora possibile provare Azure Cosmos DB per un tempo limitato senza alcuna sottoscrizione, gratuitamente e senza impegno. Per registrarsi a una sottoscrizione di prova di Azure Cosmos DB, andare alla pagina [Prova gratuitamente Azure DB Cosmos](https://azure.microsoft.com/try/cosmosdb/) e usare qualsiasi account Microsoft personale. Questa sottoscrizione differisce dalla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/free/) e può essere usata insieme alla versione di valutazione gratuita di Azure o a una sottoscrizione a pagamento di Azure.

Le sottoscrizioni di prova di Azure Cosmos DB vengono visualizzate nel portale di Azure accanto alle altre sottoscrizioni associate al proprio ID utente.

Le condizioni seguenti si applicano alle sottoscrizioni di prova di Azure Cosmos DB:

* L'accesso all'account può essere concesso agli account Microsoft personali. Evitare di usare account o account Azure Active Directory (Azure AD) appartenenti ai tenant Azure AD aziendali, è possibile che siano presenti limitazioni che potrebbero bloccare la concessione dell'accesso.
* Un [contenitore con provisioning della velocità effettiva](./set-throughput.md#set-throughput-on-a-container) per ogni sottoscrizione per gli account SQL, API Gremlin e Tabella.
* Fino a tre [raccolte con provisioning della velocità effettiva](./set-throughput.md#set-throughput-on-a-container) per ogni sottoscrizione per gli account MongoDB.
* Un solo [database con provisioning della velocità effettiva](./set-throughput.md#set-throughput-on-a-database) per ogni sottoscrizione. I database con provisioning della velocità effettiva possono contenere un numero qualsiasi di contenitori.
* 10 GB di capacità di archiviazione.
* La replica globale è attualmente disponibile nelle [aree di Azure](https://azure.microsoft.com/regions/) seguenti: Stati Uniti centrali, Europa settentrionale e Asia sud-orientale
* La velocità effettiva massima è di 5.000 UR/sec quando viene effettuato il provisioning a livello di contenitore.
* La velocità effettiva massima è di 20.000 UR/sec quando viene effettuato il provisioning a livello di database.
* Le sottoscrizioni scadono dopo 30 giorni e possono essere prorogate fino a un massimo di 31 giorni in totale. Dopo la scadenza, le informazioni archiviate vengono eliminate.
* Non è possibile creare ticket di supporto di Azure per gli account di prova di Azure Cosmos DB. È tuttavia previsto il supporto per i sottoscrittori con piani di supporto esistenti.

## <a name="set-up-azure-cosmos-db"></a>Configurazione di Azure Cosmos DB

### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Come ci si iscrive ad Azure Cosmos DB?

Azure Cosmos DB è disponibile nel portale di Azure. Per prima cosa, iscriversi per ottenere una sottoscrizione di Azure. Dopo aver effettuato l'iscrizione, è possibile aggiungere un account Azure Cosmos DB alla sottoscrizione di Azure.

### <a name="what-is-a-primary-key"></a>Che cos'è una chiave primaria?

Una chiave primaria è un token di sicurezza per accedere a tutte le risorse per un account. Gli utenti con la chiave hanno accesso in lettura e scrittura a tutte le risorse nell'account del database. Prestare attenzione quando si distribuiscono le chiavi primarie. La chiave primaria primaria e la chiave primaria secondaria sono disponibili nel pannello **chiavi** del [portale di Azure][azure-portal]. Per altre informazioni sulle chiavi, vedere [Visualizzare, copiare e rigenerare le chiavi di accesso](manage-with-cli.md#list-account-keys).

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>Su quali aree è possibile impostare PreferredLocations?

Il valore di PreferredLocations può essere impostato su qualsiasi area di Azure in cui è disponibile Cosmos DB. Per un elenco delle aree disponibili, vedere [Aree di Azure](https://azure.microsoft.com/regions/).

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Di quali aspetti è consigliabile tenere conto quando si distribuiscono dati nel mondo tramite i data center di Azure?

Azure Cosmos DB è presente in tutte le aree di Azure, come specificato nella pagina [Aree di Azure](https://azure.microsoft.com/regions/). Dato che si tratta del servizio di base, in ogni nuovo data center è disponibile Azure Cosmos DB.

Quando si imposta un'area, tenere presente che Azure Cosmos DB rispetta i cloud sovrani e per enti pubblici. Di conseguenza, se si crea un account in un'[area sovrana](https://azure.microsoft.com/global-infrastructure/) non è possibile eseguire la replica all'[esterno di tale area](https://azure.microsoft.com/global-infrastructure/). Analogamente, non è possibile abilitare la replica in altre località sovrane da un account esterno.

### <a name="is-it-possible-to-switch-from-container-level-throughput-provisioning-to-database-level-throughput-provisioning-or-vice-versa"></a>È possibile passare dal provisioning della velocità effettiva a livello di contenitore al provisioning della velocità effettiva a livello di database? O viceversa

Il provisioning della velocità effettiva a livello di contenitore e di database costituisce due offerte separate e il passaggio tra le due richiede la migrazione dei dati dall'origine alla destinazione. È quindi necessario creare un nuovo database o un nuovo contenitore e quindi eseguire la migrazione dei dati tramite la [libreria di esecuzione bulk](bulk-executor-overview.md) oppure [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

### <a name="does-azure-cosmosdb-support-time-series-analysis"></a>Azure CosmosDB supporta l'analisi delle serie temporali?

Sì, Azure CosmosDB supporta l'analisi delle serie temporali. Ecco un esempio per il [modello di serie temporali](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/samples/Patterns). Questo esempio mostra come usare il feed di modifiche per creare visualizzazioni aggregate sui dati delle serie temporali. È possibile estendere questo approccio usando Spark Streaming o un altro strumento di elaborazione di dati di flusso.

## <a name="what-are-the-azure-cosmos-db-service-quotas-and-throughput-limits"></a>Quote del servizio e limiti di velocità effettiva di Azure Cosmos DB

Per altre informazioni vedere gli articoli sulle [quote del servizio](concepts-limits.md) e i [limiti di velocità effettiva per contenitore e database](set-throughput.md#comparison-of-models) di Azure Cosmos DB.

## <a name="frequently-asked-questions-about-sql-api"></a><a id="sql-api-faq"></a>Domande frequenti relative al linguaggio API SQL

### <a name="how-do-i-start-developing-against-the-sql-api"></a>Come si inizia a eseguire lo sviluppo con l'API SQL?

Per prima cosa, è necessario iscriversi per ottenere una sottoscrizione di Azure. Dopo aver ottenuto una sottoscrizione di Azure, è possibile aggiungere un contenitore dell'API SQL alla sottoscrizione di Azure. Per istruzioni sull'aggiunta di un account Azure Cosmos DB, vedere [Creare un account Azure Cosmos](create-sql-api-dotnet.md#create-account).

[SDK](sql-api-sdk-dotnet.md) per .NET, Python, Node.js, JavaScript e Java. Gli sviluppatori possono anche usare le [API HTTP RESTful](/rest/api/cosmos-db/) per interagire con le risorse di Azure Cosmos DB da un'ampia gamma di piattaforme e linguaggi.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>È possibile accedere a esempi già pronti per iniziare rapidamente?

In GitHub sono disponibili esempi per gli SDK [.NET](sql-api-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](sql-api-nodejs-samples.md) e [Python](sql-api-python-samples.md) per l'API SQL.

### <a name="does-the-sql-api-database-support-schema-free-data"></a>Il database dell'API SQL supporta dati privi di schema?

Sì. L'API SQL consente alle applicazioni di archiviare documenti JSON arbitrari senza hint o definizioni dello schema. I dati sono immediatamente disponibili per l'esecuzione di query tramite l'interfaccia di query SQL di Azure Cosmos DB.

### <a name="does-the-sql-api-support-acid-transactions"></a>L'API SQL supporta le transazioni ACID?

Sì. L'API SQL supporta le transazioni tra documenti espresse come trigger e stored procedure JavaScript. L'ambito delle transazioni è limitato a una singola partizione in ogni contenitore e le transazioni vengono eseguite con semantica ACID in modalità "tutto o niente", isolate dall'altro codice e le altre richieste utente eseguite contemporaneamente. Se vengono generate eccezioni tramite l'esecuzione sul lato server del codice dell'applicazione JavaScript, viene eseguito il rollback dell'intera transazione. 

### <a name="what-is-a-container"></a>Che cos'è un contenitore?

Un contenitore è un gruppo di documenti con la logica dell'applicazione JavaScript associata. Un contenitore è un'entità fatturabile, in cui il [costo](performance-levels.md) è determinato dalla velocità effettiva e dallo spazio di archiviazione utilizzato. I contenitori possono estendersi su una o più partizioni o server e possono essere ridimensionati per gestire volumi praticamente illimitati di archiviazione o velocità effettiva.

* Per l'API SQL, la risorsa viene chiamata contenitore.
* Per gli account dell'API Cosmos DB per MongoDB viene eseguito il mapping di un contenitore a una raccolta.
* Per gli account dell'API Tabella e Cassandra viene eseguito il mapping di un contenitore a una tabella.
* Per gli account dell'API Gremlin viene eseguito il mapping di un contenitore a un grafico.

I contenitori sono anche le entità di fatturazione per Azure Cosmos DB. Ogni contenitore viene fatturato su base oraria a seconda della velocità effettiva di provisioning e dello spazio di archiviazione utilizzato. Per altre informazioni, vedere [Prezzi di Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

### <a name="how-do-i-create-a-database"></a>Come si crea un database?

È possibile creare database usando il [portale di Azure](https://portal.azure.com), come descritto in [Aggiungere un contenitore](create-sql-api-java.md#add-a-container), uno degli [SDK di Azure Cosmos DB](sql-api-sdk-dotnet.md) o le [API REST](/rest/api/cosmos-db/).

### <a name="how-do-i-set-up-users-and-permissions"></a>Come è possibile configurare gli utenti e le autorizzazioni?

È possibile creare utenti e autorizzazioni usando uno degli [SDK per l'API di Cosmos DB](sql-api-sdk-dotnet.md) o le [API REST](/rest/api/cosmos-db/).

### <a name="does-the-sql-api-support-sql"></a>L'API SQL supporta SQL?

Il linguaggio di query SQL supportato dagli account dell'API SQL è un sottoinsieme ottimizzato delle funzionalità di query supportate da SQL Server. Il linguaggio di query SQL di Azure Cosmos DB offre operatori gerarchici e relazionali avanzati ed estendibilità tramite funzioni definite dall'utente basate su JavaScript. La grammatica JSON consente la modellazione di documenti JSON come alberi con nodi con etichetta, che vengono usati sia dalle tecniche di indicizzazione automatica di Azure Cosmos DB che dal dialetto di query SQL di Azure Cosmos DB. Per informazioni sull'uso della grammatica SQL, vedere l'articolo [Query SQL][query].

### <a name="does-the-sql-api-support-sql-aggregation-functions"></a>L'API SQL supporta le funzioni di aggregazione SQL?

L'API SQL supporta l'aggregazione a bassa latenza su qualsiasi scala con le funzioni di aggregazione `COUNT`, `MIN`, `MAX`, `AVG` e `SUM` tramite la grammatica SQL. Per altre informazioni, vedere [Funzioni di aggregazione](sql-query-aggregates.md).

### <a name="how-does-the-sql-api-provide-concurrency"></a>Come viene offerta la concorrenza nell'API SQL?

L'API SQL supporta il controllo della concorrenza ottimistica tramite tag di entità HTTP o ETag. Ogni risorsa dell'API SQL ha un ETag, che viene impostato nel server ogni volta che un documento viene aggiornato. In tutti i messaggi di risposta sono inclusi l'intestazione ETag e il valore corrente. Gli ETag possono essere usati con l'intestazione If-Match per consentire al server di stabilire se una risorsa deve essere aggiornata. Il valore di If-Match è il valore ETag con cui eseguire il confronto. Se il valore ETag corrisponde al valore ETag del server, la risorsa verrà aggiornata. Se l'ETag non è aggiornato, il server rifiuta l'operazione con un codice di risposta di tipo "HTTP 412 - Precondizione non riuscita". Il client recupera quindi nuovamente la risorsa per acquisire il relativo valore ETag aggiornato. Inoltre, gli ETag possono essere usati con l'intestazione If-None-Match per stabilire se è necessario ripetere il recupero di una risorsa.

Per applicare la concorrenza ottimistica in .NET, usare la classe [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx). Per un esempio .NET, vedere [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) nell'esempio DocumentManagement in GitHub.

### <a name="how-do-i-perform-transactions-in-the-sql-api"></a>Come si eseguono le transazioni nell'API SQL?

L'API SQL supporta le transazioni Language Integrated tramite trigger e stored procedure JavaScript. Tutte le operazioni su database negli script vengono eseguite con isolamento dello snapshot. In caso di contenitore con partizione singola, l'ambito dell'esecuzione è limitato al contenitore. Se il contenitore è partizionato, l'ambito dell'esecuzione è limitato ai documenti con lo stesso valore di chiave di partizione all'interno del contenitore. Viene acquisito uno snapshot delle versioni (ETag) del documento all'inizio della transazione e il commit della transazione viene eseguito solo se lo script ha esito positivo. Se JavaScript genera un errore, viene eseguito il rollback della transazione. Per altre informazioni, vedere [Programmazione JavaScript lato server per Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>Come è possibile eseguire inserimenti in blocco in Cosmos DB?

È possibile eseguire l'inserimento in blocco di documenti in Azure Cosmos DB in uno dei modi seguenti:

* Lo strumento BulkExecutor, descritto in [Uso della libreria .NET BulkExecutor ](bulk-executor-dot-net.md) e [Uso della libreria Java BulkExecutor](bulk-executor-java.md)
* Strumento di migrazione dei dati, descritto in [Strumento di migrazione dei database per Azure Cosmos DB](import-data.md).
* Stored procedure, descritte in [Programmazione JavaScript lato server per Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="does-the-sql-api-support-resource-link-caching"></a>L'API SQL supporta la memorizzazione nella cache dei collegamenti alle risorse?

Sì. Dato che Azure Cosmos DB è un servizio RESTful, i collegamenti alle risorse sono immutabili e possono essere memorizzati nella cache. I client dell'API SQL possono specificare un'intestazione "If-None-Match" per le letture su qualsiasi contenitore o documento di tipo risorsa e quindi aggiornare le copie locali in seguito alla modifica della versione del server.

### <a name="is-a-local-instance-of-sql-api-available"></a>È disponibile un'istanza locale dell'API SQL?

Sì. L'[emulatore di Azure Cosmos DB](local-emulator.md) offre un'emulazione estremamente fedele del servizio Cosmos DB. Supporta le stesse funzionalità di Azure Cosmos DB, incluso il supporto per la creazione e l'esecuzione di query su documenti JSON, il provisioning e il ridimensionamento delle raccolte e l'esecuzione di stored procedure e trigger. È possibile sviluppare e testare le applicazioni usando l'emulatore di Azure Cosmos DB e distribuirle in Azure su scala globale apportando una singola modifica di configurazione all'endpoint di connessione per Azure Cosmos DB.

### <a name="why-are-long-floating-point-values-in-a-document-rounded-when-viewed-from-data-explorer-in-the-portal"></a>Perché i valori a virgola mobile lunghi in un documento appaiono arrotondati se visualizzati da Esplora dati nel portale?

Si tratta di una limitazione di JavaScript. JavaScript usa numeri in formato a virgola mobile a precisione doppia in base a quanto specificato in IEEE 754 e può rappresentare in modo sicuro solo numeri compresi tra -(2<sup>53</sup> - 1) and 2<sup>53</sup>-1, ad esempio 9007199254740991.

### <a name="where-are-permissions-allowed-in-the-object-hierarchy"></a>Quali sono le autorizzazioni consentite nella gerarchia degli oggetti?

La creazione di autorizzazioni tramite ResourceToken è consentita a livello di contenitore e dei relativi discendenti, ad esempio documenti e allegati. Di conseguenza, la creazione di un'autorizzazione a livello di database o di account non è attualmente consentita.

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulle domande frequenti in altre API, vedere:

* Domande frequenti sull'[API Azure Cosmos DB per MongoDB](mongodb-api-faq.md)
* Domande frequenti sull'[API Gremlin in Azure Cosmos DB](gremlin-api-faq.md)
* Domande frequenti sull'[API Cassandra in Azure Cosmos DB](cassandra-faq.md)
* Domande frequenti sull'[API Tabella in Azure Cosmos DB](table-api-faq.md)
