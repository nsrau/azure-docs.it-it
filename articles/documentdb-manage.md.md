<properties title="Manage DocumentDB capacity and performance" pageTitle="Manage DocumentDB capacity and performance | Azure" description="Learn how you can elastically scale DocumentDB to meet the performance and storage needs of your application." metaKeywords="" services="documentdb" solutions="data-management"  authors="bradsev" manager="jhubbard" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="bradsev"></tags>

# Gestire la capacità e le prestazioni di DocumentDB

DocumentDB è un servizio database NoSQL orientato ai documenti a scalabilità elevata e completamente gestito. Grazie a DocumentDB, non sarà necessario affittare macchine virtuali, distribuire software, monitorare database o preoccuparsi del ripristino di emergenza. DocumentDB è gestito e monitorato costantemente da ingegneri Microsoft, in modo da offrire disponibilità, prestazioni e protezione dei dati di qualità elevata.

È possibile iniziare a usare DocumentDB creando un account di database tramite il portale di Microsoft Azure. DocumentDB è offerto in unità di capacità (CU, Capacity Unit), organizzabili in stack, di risorse di archiviazione e velocità effettiva basate su SSD. È possibile aumentare o ridurre le dimensioni di DocumentDB in modo flessibile per soddisfare le esigenze dell'applicazione a livello di prestazioni e archiviazione.

Ogni unità di capacità include una quota di raccolte flessibili per l'archiviazione di dati dei documenti, risorse di archiviazione con provisioning e velocità effettiva con provisioning sotto forma di unità di richiesta al secondo. In caso di modifica dei requisiti di capacità dell'applicazione, sarà possibile aumentare o ridurre la capacità di cui è stato effettuato il provisioning nell'account di database. La capacità con provisioning in un account di database è disponibile per tutti i database e tutte le raccolte esistenti o creati nell'account.

# Account di database e quota di risorse amministrazione

Gli utenti che hanno sottoscritto Azure possono eseguire il provisioning di uno o più account di database di DocumentDB. Ogni account di database è associato a una quota di risorse di amministrazione, inclusi 100 database, 500.000 utenti e 2.000.000 autorizzazioni.

# Database con archiviazione illimitata dei documenti

Un singolo database di DocumentDB può includere una quantità praticamente illimitata di archiviazione documenti, partizionata in base alle raccolte. Le raccolte costituiscono i domini transazione per i documenti incluse nelle raccolte stesse. Un database di DocumentDB è flessibile per impostazione predefinita e può includere da pochi GB a, potenzialmente, petabyte di archiviazione documenti e velocità effettiva con provisioning basate su SSD. A differenza del database RDBMS tradizionale, l'ambito di un database in DocumentDB non è limitato a una singola macchina.

Con DocumentDB, in caso di incremento delle esigenze di scalabilità dell'applicazione, sarà possibile creare più raccolte o più database o entrambi. Molte applicazioni prodotte direttamente da Microsoft usano DocumentDB su scala consumer, creando database DocumentDB di dimensioni estremamente elevate, ognuno dei quali include migliaia di raccolte con terabyte di archiviazione documenti. È possibile aumentare o ridurre le dimensioni di un database aggiungendo o rimuovendo raccolte per soddisfare i requisiti di scalabilità dell'applicazione. Il numero di raccolte che è possibile creare in un database dipende dalla disponibilità dell'offerta e dalla quantità di unità di capacità acquistate. Le risorse di archiviazione e la velocità effettiva basate su SSD di cui è eseguito automaticamente il provisioning possono essere distribuite nelle diverse raccolte del database nell'account di database.

# Raccolte flessibili

Ogni database di DocumentDB può includere una o più raccolte. Una raccolta fornisce l'ambito per l'archiviazione documenti e l'esecuzione di query. Una raccolta costituisce anche un dominio di transazione per tutti i documenti inclusi nella raccolta stessa. Le raccolte sono flessibili ed è possibile aumentarne o ridurne le risorse di archiviazione e la velocità effettiva. È possibile creare il numero di raccolte necessario per soddisfare i requisiti dell'applicazione a livello di scalabilità. Per creare le raccolte, è prima di tutto necessario acquistare una o più unità di capacità. Ogni unità di capacità include una quota di raccolte. Se si raggiunge la quota di raccolte per l'account, sarà possibile acquistare unità di capacità aggiuntive.

> [AZURE.NOTE] Si noti che nella versione di anteprima le raccolte possono avere dimensioni comprese tra 0 e 10 GB.

# Risorse di archiviazione e velocità effettiva con provisioning sotto forma di unità di capacità

È possibile eseguire il provisioning di unità organizzabili in stack di archiviazione documenti e velocità effettiva basate su SSD sotto forma di unità di capacità. È possibile aumentare o ridurre la capacità di DocumentDB in modo flessibile con prestazioni prevedibili tramite l'acquisto di altre unità di capacità, per soddisfare le esigenze dell'applicazione a livello di scalabilità di lettura, archiviazione e velocità effettiva.

Ogni unità di capacità include tre raccolte flessibili, 10 GB di archiviazione documenti con provisioning basata su SSD e 2000 unità di richiesta (RU, Request Unit) di velocità effettiva con provisioning. La capacità relativa a risorse di archiviazione e velocità effettiva con provisioning associata a un'unità di capacità è distribuita nelle raccolte di DocumentDB create.

# Velocità effettiva con provisioning, unità di richiesta e operazioni di database

A differenza di archivi NoSQL di tipo chiave-valore, che offrono semplici operazioni GET e PUT, DocumentDB permette una gamma più ampia di operazioni di database, incluse le query relazionali e gerarchiche con funzioni UDF, stored procedure e trigger operative nei documenti in una raccolta di database. Il costo associato a ognuna di queste operazioni dipende da CPU, I/O e memoria necessari per il completamento dell'operazione. Invece di occuparsi della pianificazione e della gestione delle risorse hardware, sarà possibile usare un'unità di richiesta come misura singola per le risorse necessarie per eseguire diverse operazioni di database e rispondere a una richiesta dell'applicazione.

Il provisioning delle unità di richiesta è eseguito per ogni account di database in base al numero di unità di capacità acquistato. Il consumo delle unità di richiesta è valutato in base alla frequenza al secondo. Le applicazioni che superano il livello di unità di richiesta con provisioning previsto per il relativo account saranno limitate fino al ritorno del livello sotto il valore riservato per l'account. Se l'applicazione necessita di un livello superiore di velocità effettiva, sarà possibile acquistare unità di capacità aggiuntive.

Nella tabella seguente è disponibile un elenco di diverse operazioni di database e delle unità di richiesta disponibili per ogni unità di capacità. La tabella semplifica la pianificazione del consumo della velocità effettiva associata a un'unità di capacità da parte di una determinata operazione di database. Si presuppone che la dimensione del documento sia pari a 1 KB e che sia costituita da 10 valori di proprietà univoci con livello di coerenza predefinito impostato su "Sessione" e che tutti i documenti siano indicizzati automaticamente da DocumentDB.

| Operazioni di database                                                                               | Numero di operazioni al secondo per unità di capacità |
|------------------------------------------------------------------------------------------------------|-------------------------------------------------------|
| Lettura di un singolo documento                                                                      | 2000                                                  |
| Inserimento/Sostituzione/Eliminazione di un singolo documento                                        | 500                                                   |
| Esecuzione di query su una raccolta con un semplice predicato e restituzione di un singolo documento | 1000                                                  |
| Stored procedure con 50 inserimenti di documenti                                                     | 20                                                    |

Come si può notare dalla tabella, le unità di richiesta usate dalle operazioni di inserimento/sostituzione/eliminazione per documenti indicizzati automaticamente sono pari a circa 4 volte quelle necessarie per la lettura di un documento. Questa tabella è semplicemente un riferimento. In pratica tuttavia:

-   I documenti possono avere dimensioni diverse, non corrispondenti a 1 KB.
-   I documenti possono avere più di 10 proprietà.
-   Il livello di coerenza predefinito può essere impostato su avanzata, con obsolescenza associata o futura.
-   È possibile scegliere di non indicizzare solo alcuni documenti.
-   È possibile scegliere di indicizzare solo alcune proprietà e di non permettere a DocumentDB di indicizzare tutti gli elementi automaticamente.
-   Le query e le stored procedure in uso potrebbero essere molto più complesse.

Per ottenere un numero preciso di unità di richiesta per l'applicazione, è possibile usare un calcolatore di unità di richiesta per calcolare le unità di richiesta usate per le diverse operazioni. È anche possibile esaminare l'intestazione di risposta x-ms-request-charge, che include il numero di unità di richiesta usate per una determinata richiesta.

[Calcolatore di unità di richiesta][]

# Raccolte e velocità effettiva con provisioning

La velocità effettiva con provisoning per l'account di database è allocata in modo uniforme in tutte le raccolte, fino al livello massimo di velocità effettiva (unità di richiesta) per una singola raccolta. Se, ad esempio, si acquista una singola unità di capacità e si crea una singola raccolta, tutta la velocità effettiva con provisioning per l'unità di capacità sarà disponibile per la raccolta. Se si crea una raccolta aggiuntiva, la velocità effettiva con provisioning sarà allocata in modo uniforme e ogni raccolta riceverà la metà della velocità effettiva con provisioning.

# Scelta del livello di coerenza e velocità effettiva

La scelta del livello di coerenza predefinita influisce sulla velocità effettiva e sulla latenza. È possibile impostare il livello di coerenza predefinito sia a livello di codice che tramite il portale di Azure. È anche possibile eseguire l'override del livello di coerenza per singole richieste. Per impostazione predefinita, il livello di coerenza corrisponde a quello della sessione che fornisce letture/scritture costanti e legge le garanzie di scrittura fornite. La coerenza delle sessioni è ideale per applicazioni incentrate sugli utenti e offre un compromesso ideale tra coerenza e prestazioni.

-   I livelli sessione e futura per la coerenza offrono circa 2000 richieste di lettura di documenti e 500 inserimenti/sostituzioni/eliminazioni di documenti.
-   I livelli avanzata e con obsolescenza associata per la coerenza offrono circa 1200 richieste di lettura di documenti e 500 inserimenti/sostituzioni/eliminazioni di documenti. La latenza degli inserimenti/sostituzioni/eliminazioni offerta dal livello con obsolescenza associata è significativamente inferiore rispetto a quella offerta dal livello avanzata.

[Calcolatore di unità di richiesta][]

# Archiviazione documenti con provisioning e sovraccarico dell'indice

A ogni acquisto di unità di capacità viene eseguito il provisioning dell'account con 10 GB di archiviazione documenti basata su SSD. I 10 GB di archiviazione documenti includono i documenti e l'archiviazione per l'indice. Per impostazione predefinita, una raccolta DocumentDB è configurata per l'indicizzazione automatica di tutti i documenti, senza richiedere esplicitamente indici o schemi secondari. In base all'uso in produzione di applicazioni produttore su scala consumer che usano DocumentDB, il sovraccarico tipico dell'indice è compreso tra il 2 e il 20%. La tecnologia di indicizzazione usata da DocumentDB assicura che, indipendentemente dai valori delle proprietà, il sovraccarico dell'indice non supererà l'80% delle dimensioni dei documenti con impostazioni predefinite.

Per impostazione predefinita, tutti i documenti sono indicizzati automaticamente da DocumentDB. Se, tuttavia, si vuole ottimizzare il sovraccarico dell'indice, è possibile scegliere di rimuovere determinati documenti dall'indicizzazione al momento dell'inserimento o della sostituzione di un documento. È possibile configurare una raccolta di DocumentDB in modo da escludere dall'indicizzazione tutti i documenti nella raccolta. È anche possibile configurare una raccolta DocumentDB in modo che indicizzi in modo selettivo solo determinate proprietà o alcuni percorsi con caratteri jolly dei documenti JSON. L'esclusione di proprietà o documenti migliora anche la velocità effettiva di scrittura, permettendo quindi di usare meno unità di richiesta.

[Calcolatore di unità di richiesta][]

  [Calcolatore di unità di richiesta]: http://go.microsoft.com/fwlink/?LinkID=510088
