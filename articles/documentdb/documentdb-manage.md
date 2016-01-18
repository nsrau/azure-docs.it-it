<properties 
	pageTitle="Gestione della capacità di DocumentDB | Microsoft Azure" 
	description="Informazioni su come aumentare o ridurre le dimensioni di DocumentDB per soddisfare le esigenze dell'applicazione in termini di capacità." 
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="cgronlun" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/28/2015" 
	ms.author="mimig"/>

# Per gestire le esigenze relative alla capacità di DocumentDB
DocumentDB è un servizio database NoSQL orientato ai documenti, scalabile e completamente gestito. Grazie a DocumentDB, non sarà necessario affittare macchine virtuali, distribuire software, monitorare database o preoccuparsi del ripristino di emergenza. DocumentDB è gestito e monitorato costantemente da tecnici Microsoft, in modo da offrire disponibilità, prestazioni e protezione dei dati di elevata qualità.

È possibile iniziare a usare DocumentDB [creando un account database](documentdb-create-account.md) tramite il [portale di Azure](https://portal.azure.com/). DocumentDB è offerto in unità di risorse di archiviazione e velocità effettiva basate su unità SSD. Queste unità sono sottoposte a provisioning mediante la creazione di raccolte di database all'interno dell'account di database. Ogni raccolta include 10 GB di archiviazione del database con una velocità effettiva riservata. Se i requisiti di velocità effettiva dell'applicazione cambiano, è possibile modificare dinamicamente questa impostazione configurando il [livello di prestazioni](documentdb-performance-levels.md) per ogni raccolta.

Quando l'applicazione supera i livelli delle prestazioni per una o più raccolte, le richieste saranno limitate in base a ogni raccolta. Ciò significa che alcune richieste di applicazione possono avere esito positivo mentre altre possono essere limitate.

## Account di database e risorse amministrative
Gli utenti che hanno sottoscritto Azure possono eseguire il provisioning di uno o più account di database di DocumentDB. Ogni account database è associato a una quota di risorse amministrative, inclusi database, utenti e autorizzazioni. Queste risorse sono soggette a [limiti e quote](documentdb-limits.md). Se sono necessarie altre risorse amministrative, contattare il [supporto tecnico](documentdb-increase-limits.md).

## Database con archiviazione illimitata dei documenti
Un singolo database di DocumentDB può includere una quantità praticamente illimitata di archiviazione documenti, partizionata in base alle raccolte. Le raccolte costituiscono i domini transazione per i documenti incluse nelle raccolte stesse. Un database di DocumentDB è flessibile per quanto riguarda le dimensioni e può includere da pochi GB a, potenzialmente, terabyte di archiviazione documenti e velocità effettiva con provisioning basate su SSD. A differenza del database RDBMS tradizionale, l'ambito di un database in DocumentDB non è limitato a una singola macchina.

Con DocumentDB, in caso di esigenza di scalabilità delle applicazioni, sarà possibile creare più raccolte o più database o entrambi. Molte applicazioni prodotte direttamente da Microsoft usano DocumentDB su scala consumer, creando database DocumentDB di dimensioni estremamente elevate, ognuno dei quali include centinaia o migliaia di raccolte con terabyte di archiviazione documenti. È possibile aumentare o ridurre le dimensioni di un database aggiungendo o rimuovendo raccolte per soddisfare i requisiti di scalabilità dell'applicazione.

## Raccolte di database
Ogni database di DocumentDB può includere una o più raccolte. Le raccolte fungono da partizioni di dati a disponibilità elevata per l'elaborazione e archiviazione dei documenti. Ogni raccolta è in grado di archiviare documenti con schemi eterogenei. Le funzionalità di indicizzazione automatica e query di DocumentDB consentono di filtrare e recuperare documenti facilmente. Una raccolta fornisce l'ambito per l'archiviazione documenti e l'esecuzione di query. Una raccolta costituisce anche un dominio di transazione per tutti i documenti inclusi nella raccolta stessa. La velocità effettiva delle raccolte viene allocata in base al livello delle prestazioni specificato. Questa impostazione può essere modificata in modo dinamico tramite il portale di Azure o uno degli SDK.

È possibile creare qualunque numero di raccolte necessario per soddisfare i requisiti di archiviazione e scalabilità della velocità effettiva dell'applicazione. È possibile creare le raccolte tramite il [portale di Azure](https://portal.azure.com/) o uno degli [SDK di DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx).

>[AZURE.NOTE]Ogni raccolta supporta l'archiviazione fino a 10 GB di dati del documento.
 
## Unità di richiesta e operazioni di database
DocumentDB permette un'ampia gamma di operazioni di database, incluse le query relazionali e gerarchiche con funzioni UDF, stored procedure e trigger operative nei documenti in una raccolta di database. Il costo di elaborazione associato a ognuna di queste operazioni dipende da CPU, I/O e memoria necessari per il completamento dell'operazione. Invece di occuparsi della pianificazione e della gestione delle risorse hardware, sarà possibile usare un'unità di richiesta come misura singola per le risorse necessarie per eseguire diverse operazioni di database e rispondere a una richiesta dell'applicazione.

Le unità di richiesta sono sottoposte a provisioning e assegnate in base al livello di prestazioni impostato per una raccolta. Per ogni raccolta viene designato un livello di prestazioni al momento della creazione. Questo livello di prestazioni determina la capacità di elaborazione di una raccolta in unità di richiesta al secondo. È possibile regolare i livelli di prestazioni per tutta la durata di una raccolta per adattarsi alle esigenze di elaborazione mutevoli e accedere ai modelli dell'applicazione. Per altre informazioni, vedere [Livelli di prestazioni in DocumentDB](documentdb-performance-levels.md).

>[AZURE.IMPORTANT]Le raccolte sono entità fatturabili. Il costo è determinato dal livello di prestazioni assegnato alla raccolta.

Il consumo delle unità di richiesta è valutato in base alla frequenza al secondo. Per le applicazioni che superano il livello di unità di richiesta con provisioning per una raccolta, le richieste a tale raccolta saranno limitate fino al ritorno del livello sotto il valore riservato. Se l'applicazione richiede un livello di velocità effettiva superiore, è possibile modificare il livello di prestazioni delle raccolte esistenti o distribuire le richieste delle applicazioni tra nuove raccolte.

Un'unità di richiesta è una misura normalizzata del costo di elaborazione della richiesta. Una singola unità di richiesta rappresenta la capacità di elaborazione necessaria per leggere un singolo documento JSON da 1 KB costituito da 10 valori di proprietà univoci. L'addebito delle unità di richiesta presuppone un livello di coerenza impostato sul valore predefinito "Sessione" e che tutti i documenti siano indicizzati automaticamente. Una richiesta di inserimento, sostituzione o eliminazione dello stesso documento utilizzerà più potenza di elaborazione del servizio e pertanto più unità di richiesta. Ogni risposta dal servizio include un'intestazione personalizzata (x-ms-request-charge) che misura le unità di richiesta usate per la richiesta. Questa intestazione è accessibile anche tramite gli [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx). In .NET SDK, RequestCharge è una proprietà dell'oggetto ResourceResponse.

>[AZURE.NOTE]La base di 1 unità di una richiesta per un documento da 1 KB corrisponde a un'operazione GET semplice dal collegamento self del documento.

Ci sono diversi fattori che influiscono sulle unità di richiesta utilizzate per un'operazione in un account di database DocumentDB. Questi fattori includono:

- Dimensioni del documento. Con l'aumento delle dimensioni del documento aumentano anche le unità utilizzate per leggere o scrivere i dati.
- Numero delle proprietà. presupponendo l'indicizzazione predefinita di tutte le proprietà, le unità utilizzate per scrivere un documento aumenteranno con l'aumento del numero delle proprietà.
- Coerenza dei dati. se si usano i livelli di coerenza dei dati Assoluta o Obsolescenza associata, verranno utilizzate ulteriori unità per leggere i documenti.
- Proprietà indicizzate. I criteri di indicizzazione in ogni raccolta determinano le proprietà che vengono indicizzate per impostazione predefinita. È possibile ridurre l'utilizzo di unità di richiesta limitando il numero di proprietà indicizzate. 
- Indicizzazione del documento. Per impostazione predefinita ogni documento viene indicizzato automaticamente, ma se si sceglie di non indicizzare alcuni documenti si utilizzeranno meno unità di richiesta.

Query, stored procedure e trigger utilizzeranno le unità di richiesta in base alla complessità delle operazioni eseguite. Quando si sviluppa l'applicazione, controllare l'intestazione per l'addebito delle richieste per comprendere meglio il modo in cui ciascuna operazione usa la capacità delle unità di richiesta.

## Scelta del livello di coerenza e velocità effettiva
La scelta del livello di coerenza predefinita influisce sulla velocità effettiva e sulla latenza. È possibile impostare il livello di coerenza predefinito sia a livello di codice che tramite il portale di Azure. È anche possibile eseguire l'override del livello di coerenza per singole richieste. Per impostazione predefinita, il livello di coerenza corrisponde a quello della sessione che fornisce letture/scritture costanti e legge le garanzie di scrittura fornite. La coerenza delle sessioni è ideale per applicazioni incentrate sugli utenti e offre un compromesso ideale tra coerenza e prestazioni.

Per istruzioni sulla modifica del livello di coerenza nel portale di Azure, vedere [Come gestire un account DocumentDB](documentdb-manage-account.md#consistency). In alternativa, per altre informazioni sui livelli di coerenza, vedere [Uso dei livelli di coerenza](documentdb-consistency-levels.md).

## Archiviazione documenti con provisioning e sovraccarico dell'indice
A ogni raccolta creata, viene eseguito il provisioning dell'account con 10 GB di archiviazione documenti basata su SSD. I 10 GB di archiviazione documenti includono i documenti e l'archiviazione per l'indice. Per impostazione predefinita, una raccolta DocumentDB è configurata per l'indicizzazione automatica di tutti i documenti, senza richiedere esplicitamente indici o schemi secondari. In base alle applicazioni che utilizzano DocumentDB, il sovraccarico tipico dell’indice è compreso tra 2-20%. La tecnologia di indicizzazione usata da DocumentDB assicura che, indipendentemente dai valori delle proprietà, il sovraccarico dell'indice non supererà l'80% delle dimensioni dei documenti con impostazioni predefinite.

Per impostazione predefinita, tutti i documenti sono indicizzati automaticamente da DocumentDB. Se tuttavia si vuole ottimizzare il sovraccarico dell'indice, è possibile scegliere di rimuovere determinati documenti dall'indicizzazione al momento dell'inserimento o della sostituzione di un documento, come descritto in [Criteri di indicizzazione di DocumentDB](documentdb-indexing-policies.md). È possibile configurare una raccolta di DocumentDB in modo da escludere dall'indicizzazione tutti i documenti nella raccolta. È anche possibile configurare una raccolta DocumentDB in modo che indicizzi in modo selettivo solo determinate proprietà o alcuni percorsi con caratteri jolly dei documenti JSON, come descritto in [Configurazione dei criteri di indicizzazione di una raccolta](documentdb-indexing-policies.md#configuring-the-indexing-policy-of-a-collection). L'esclusione di proprietà o documenti migliora anche la velocità effettiva di scrittura, permettendo quindi di usare meno unità di richiesta.
 
## Passaggi successivi
Per istruzioni su come monitorare i livelli di prestazioni nel portale di Azure, vedere [Monitorare un account DocumentDB](documentdb-monitor-accounts.md).

Per altre informazioni sulla scelta dei livelli di prestazioni delle raccolte, vedere [Livelli di prestazioni in DocumentDB](documentdb-performance-levels.md).
 

<!---HONumber=AcomDC_0107_2016-->