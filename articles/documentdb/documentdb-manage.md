<properties 
	pageTitle="Gestire la capacità e le prestazioni di DocumentDB | Azure" 
	description="Informazioni su come aumentare o ridurre in modo flessibile le dimensioni di DocumentDB per soddisfare le esigenze dell'applicazione in termini di capacità." 
	services="documentdb" 
	authors="mimig1, johnfmacintyre" 
	manager="jhubbard" 
	editor="cgronlun" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/17/2015" 
	ms.author="mimig"/>

#Gestire la capacità e le prestazioni di DocumentDB
DocumentDB è un servizio database NoSQL orientato ai documenti, scalabile e completamente gestito.  Grazie a DocumentDB, non sarà necessario affittare macchine virtuali, distribuire software, monitorare database o preoccuparsi del ripristino di emergenza. DocumentDB è gestito e monitorato costantemente da tecnici Microsoft, in modo da offrire disponibilità, prestazioni e protezione dei dati di elevata qualità.  

È possibile iniziare a usare DocumentDB creando un account di database tramite il [portale di gestione in anteprima di Microsoft Azure](https://portal.azure.com/). DocumentDB è offerto in unità di capacità (CU, Capacity Unit), organizzabili in stack, di risorse di archiviazione e velocità effettiva basate su unità SSD. È possibile aumentare o ridurre le dimensioni di DocumentDB in modo flessibile per soddisfare le esigenze dell'applicazione a livello di prestazioni e archiviazione. 

Ogni unità di capacità include una quota di raccolte per l'archiviazione di dati dei documenti, risorse di archiviazione con provisioning e velocità effettiva con provisioning sotto forma di unità di richiesta al secondo. In caso di modifica dei requisiti di capacità dell'applicazione, sarà possibile aumentare o ridurre la capacità di cui è stato effettuato il provisioning nell'account di database. La capacità con provisioning in un account di database è disponibile per tutti i database e tutte le raccolte esistenti o creati nell'account.

> [AZURE.NOTE] È possibile aumentare o ridurre le dimensioni di ogni raccolta in memoria e velocità effettiva fino al valore [limite](documentdb-limits.md) massimo supportato. La velocità effettiva verrà distribuita uniformemente a tutte le raccolte fino al limite massimo per ogni raccolta.

Quando l'applicazione supera i livelli delle prestazioni per una o più raccolte, le richieste saranno limitate in base a ogni raccolta. Ciò significa che alcune richieste di applicazione possono avere esito positivo mentre altre possono essere limitate.

##<a name="DBaccount"></a>Account di database e risorse amministrative
Gli utenti che hanno sottoscritto Azure possono eseguire il provisioning di uno o più account di database di DocumentDB. Ogni account di database è associato a una quota di risorse di amministrazione, inclusi database, utenti e autorizzazioni. Queste risorse sono soggette a [limiti e quote](documentdb-limits.md). Se sono necessarie altre risorse amministrative, contattare il supporto.   

##<a name="DBstorage"></a> Database con archiviazione illimitata dei documenti
Un singolo database di DocumentDB può includere una quantità praticamente illimitata di archiviazione documenti, partizionata in base alle raccolte. Le raccolte costituiscono i domini transazione per i documenti incluse nelle raccolte stesse. Un database di DocumentDB è flessibile per impostazione predefinita e può includere da pochi GB a, potenzialmente, terabyte di archiviazione documenti e velocità effettiva con provisioning basate su SSD. A differenza del database RDBMS tradizionale, l'ambito di un database in DocumentDB non è limitato a una singola macchina.   

Con DocumentDB, in caso di crescita delle esigenze di scalabilità dell'applicazione, sarà possibile creare più raccolte o più database o entrambi. Molte applicazioni prodotte direttamente da Microsoft usano DocumentDB su scala consumer, creando database DocumentDB di dimensioni estremamente elevate, ognuno dei quali include migliaia di raccolte con terabyte di archiviazione documenti. È possibile aumentare o ridurre le dimensioni di un database aggiungendo o rimuovendo raccolte per soddisfare i requisiti di scalabilità dell'applicazione. Il numero di raccolte che è possibile creare in un database dipende dalla disponibilità dell'offerta e dalla quantità di unità di capacità acquistate. Le risorse di archiviazione e la velocità effettiva basate su SSD di cui è eseguito automaticamente il provisioning possono essere distribuite nelle diverse raccolte del database nell'account di database. 

##<a name="DBCollections"></a>Raccolte di database
Ogni database di DocumentDB può includere una o più raccolte. Una raccolta fornisce l'ambito per l'archiviazione documenti e l'esecuzione di query. Una raccolta costituisce anche un dominio di transazione per tutti i documenti inclusi nella raccolta stessa. È possibile creare il numero di raccolte necessario per soddisfare i requisiti dell'applicazione a livello di scalabilità. Per creare le raccolte, è prima di tutto necessario acquistare una o più unità di capacità. Ogni unità di capacità include una quota di raccolte. Se si raggiunge la quota di raccolte per l'account, sarà possibile acquistare unità di capacità aggiuntive.  

>[AZURE.NOTE] Ogni raccolta supporta l'archiviazione fino a 10 GB di dati del documento. 

##<a name="ProvStorage"></a>Risorse di archiviazione e velocità effettiva con provisioning sotto forma di unità di capacità
È possibile eseguire il provisioning di unità organizzabili in stack di archiviazione documenti e velocità effettiva basate su SSD sotto forma di unità di capacità. È possibile aumentare o ridurre la capacità di DocumentDB in modo flessibile con prestazioni prevedibili tramite l'acquisto di altre unità di capacità, per soddisfare le esigenze dell'applicazione a livello di scalabilità di lettura, archiviazione e velocità effettiva.  
 
Ogni unità di capacità include tre raccolte flessibili, 10 GB di archiviazione documenti con provisioning basata su SSD e 2000 unità di richiesta (RU, Request Unit) di velocità effettiva con provisioning. La capacità relativa a risorse di archiviazione e velocità effettiva con provisioning associata a un'unità di capacità è distribuita nelle raccolte di DocumentDB create.   

##<a name="ProvThroughput"></a>Unità di richiesta e operazioni di database
DocumentDB permette un'ampia gamma di operazioni di database, incluse le query relazionali e gerarchiche con funzioni UDF, stored procedure e trigger operative nei documenti in una raccolta di database. Il costo di elaborazione associato a ognuna di queste operazioni dipende da CPU, I/O e memoria necessari per il completamento dell'operazione. Invece di occuparsi della pianificazione e della gestione delle risorse hardware, sarà possibile usare un'unità di richiesta come misura singola per le risorse necessarie per eseguire diverse operazioni di database e rispondere a una richiesta dell'applicazione.

Il provisioning delle unità di richiesta è eseguito per ogni account di database in base al numero di unità di capacità acquistato. Nell'anteprima di DocumentDB, a ogni raccolta creata viene assegnato un limite di unità di richiesta. L'allocazione viene calcolata allocando in modo uniforme tutte le unità di richiesta dalle unità di capacità acquistate in base al numero di raccolte create all'interno di un account. Ad esempio, per un account con 2 unità di capacità acquistate e 4 raccolte create, a ogni raccolta vengono assegnate 2 unità di capacità x 2.000 unità di richiesta / 4 raccolte per un'allocazione di 1.000 unità di richiesta per raccolta. Quando Azure DocumentDB è disponibile a livello generale, il servizio non distribuirà più le unità di richiesta tra le raccolte all'interno di un account. Le unità di richiesta verranno acquistate e assegnate a raccolte specifiche in base ai livelli di prestazioni. Per altre informazioni, vedere [Livelli di prestazioni in DocumentDB](documentdb-performance-levels.md). 

>[AZURE.NOTE] Durante l'anteprima di Azure DocumentDB, a ogni raccolta non possono essere allocate più di 2.000 unità di richiesta. 

Il consumo delle unità di richiesta è valutato in base alla frequenza al secondo. Le applicazioni che superano il livello di unità di richiesta con provisioning previsto per il relativo account saranno limitate fino al ritorno del livello sotto il valore riservato per ogni raccolta. Se l'applicazione necessita di un livello superiore di velocità effettiva, sarà possibile acquistare unità di capacità aggiuntive.

Un'unità di richiesta è una misura normalizzata del costo di elaborazione della richiesta. Una singola unità di richiesta rappresenta la capacità di elaborazione necessaria per leggere un singolo documento JSON da 1 KB costituito da 10 valori di proprietà univoci. L'addebito delle unità di richiesta presuppone un livello di coerenza impostato sul valore predefinito "Sessione" e che tutti i documenti siano indicizzati automaticamente. Una richiesta di inserimento, sostituzione o eliminazione dello stesso documento utilizzerà più potenza di elaborazione del servizio e pertanto più unità di richiesta. Ogni risposta della richiesta dal servizio include un'intestazione personalizzata (x-ms-request-charge) che misura le unità di richiesta usate per la richiesta. Questa intestazione è accessibile anche tramite gli SDK. In .Net SDK, RequestCharge è una proprietà dell'oggetto ResourceResponse.

Ci sono diversi fattori che influiscono sulle unità di richiesta usate per un'operazione in un account di database DocumentDB. Questi fattori includono:

- Dimensioni del documento: con l'aumento delle dimensioni del documento aumentano anche le unità usate per leggere o scrivere i dati.
- Numero delle proprietà: presupponendo l'indicizzazione predefinita di tutte le proprietà, le unità usate per scrivere un documento aumenteranno con l'aumento del numero delle proprietà.
- Coerenza dei dati: se si usano i livelli di coerenza dei dati Assoluta o Obsolescenza associata, verranno usate ulteriori unità per leggere i documenti.
- Proprietà indicizzate: i criteri di indicizzazione in ogni raccolta determinano le proprietà che vengono indicizzate per impostazione predefinita. È possibile ridurre l'utilizzo di unità di richiesta limitando il numero di proprietà indicizzate.
- Indicizzazione del documento: per impostazione predefinita ogni documento viene indicizzato automaticamente, ma se si sceglie di non indicizzare alcuni documenti si utilizzeranno meno unità di richiesta.

Query, stored procedure e trigger utilizzeranno le unità di richiesta in base alla complessità delle operazioni eseguite. Quando si sviluppa l'applicazione, controllare l'intestazione per l'addebito delle richieste per comprendere meglio il modo in cui ciascuna operazione usa la capacità delle unità di richiesta.

##<a name="Collections"></a>Raccolte e velocità effettiva con provisioning
La velocità effettiva con provisioning per l'account di database è allocata in modo uniforme in tutte le raccolte, fino al livello massimo di velocità effettiva (unità di richiesta) per una singola raccolta. Se, ad esempio, si acquista una singola unità di capacità e si crea una singola raccolta, tutta la velocità effettiva con provisioning per l'unità di capacità sarà disponibile per la raccolta. Se si crea una raccolta aggiuntiva, la velocità effettiva con provisioning sarà allocata in modo uniforme e ogni raccolta riceverà la metà della velocità effettiva con provisioning.  

##<a name="Consistency"></a>Scelta del livello di coerenza e velocità effettiva
La scelta del livello di coerenza predefinita influisce sulla velocità effettiva e sulla latenza.  È possibile impostare il livello di coerenza predefinito sia a livello di codice che tramite il portale di Azure. È anche possibile eseguire l'override del livello di coerenza per singole richieste. Per impostazione predefinita, il livello di coerenza corrisponde a quello della sessione che fornisce letture/scritture costanti e legge le garanzie di scrittura fornite. La coerenza delle sessioni è ideale per applicazioni incentrate sugli utenti e offre un compromesso ideale tra coerenza e prestazioni.    

Per istruzioni sulla modifica del livello di coerenza nel portale di gestione dell'anteprima di Azure, vedere Procedura: Gestire le impostazioni di coerenza di DocumentDB in [Come gestire un account DocumentDB](documentdb-manage-account.md).

##<a name="IndexOverhead"></a>Archiviazione documenti con provisioning e sovraccarico dell'indice
A ogni acquisto di unità di capacità viene eseguito il provisioning dell'account con 10 GB di archiviazione documenti basata su SSD. I 10 GB di archiviazione documenti includono i documenti e l'archiviazione per l'indice. Per impostazione predefinita, una raccolta DocumentDB è configurata per l'indicizzazione automatica di tutti i documenti, senza richiedere esplicitamente indici o schemi secondari. In base all'uso in produzione di applicazioni produttore su scala consumer che usano DocumentDB, il sovraccarico tipico dell'indice è compreso tra il 2 e il 20%. La tecnologia di indicizzazione usata da DocumentDB assicura che, indipendentemente dai valori delle proprietà, il sovraccarico dell'indice non supererà l'80% delle dimensioni dei documenti con impostazioni predefinite.  

Per impostazione predefinita, tutti i documenti sono indicizzati automaticamente da DocumentDB. Se, tuttavia, si vuole ottimizzare il sovraccarico dell'indice, è possibile scegliere di rimuovere determinati documenti dall'indicizzazione al momento dell'inserimento o della sostituzione di un documento. È possibile configurare una raccolta di DocumentDB in modo da escludere dall'indicizzazione tutti i documenti nella raccolta. È anche possibile configurare una raccolta DocumentDB in modo che indicizzi in modo selettivo solo determinate proprietà o alcuni percorsi con caratteri jolly dei documenti JSON.  L'esclusione di proprietà o documenti migliora anche la velocità effettiva di scrittura, permettendo quindi di usare meno unità di richiesta.   
 
##<a name="NextSteps"></a>Passaggi successivi
Per istruzioni sulla gestione dell'account di DocumentDB nel portale di anteprima di Azure, vedere Procedura: Gestire le impostazioni di coerenza di DocumentDB in [Come gestire un account DocumentDB](documentdb-manage-account.md).

Per istruzioni su come monitorare i livelli di prestazioni nel portale di anteprima di Azure, vedere [Monitorare un account DocumentDB](documentdb-monitor-accounts.md).

<!--HONumber=49--> 