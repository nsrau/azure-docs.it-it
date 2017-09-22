---
title: Domande frequenti su Azure Cosmos DB | Microsoft Docs
description: "Risposte alle domande frequenti su Azure Cosmos DB, un servizio di database multimodello distribuito a livello globale. Informazioni su capacità, livelli di prestazioni e scalabilità."
keywords: Domande sui database - Domande frequenti, documentdb, azure, Microsoft azure
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: b68d1831-35f9-443d-a0ac-dad0c89f245b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2017
ms.author: mimig
ms.translationtype: HT
ms.sourcegitcommit: 9b7316a5bffbd689bdb26e9524129ceed06606d5
ms.openlocfilehash: e8ac4bef5af7a98857278b82304bb58f1ac6e342
ms.contentlocale: it-it
ms.lasthandoff: 09/08/2017

---
# <a name="azure-cosmos-db-faq"></a>Domande frequenti su Azure Cosmos DB
## <a name="azure-cosmos-db-fundamentals"></a>Nozioni fondamentali su Azure Cosmos DB
### <a name="what-is-azure-cosmos-db"></a>Che cos'è Azure Cosmos DB?
Azure Cosmos DB è un servizio di database multimodello con replica globale che offre avanzate funzionalità di query sui dati privi di schema e consente di ottenere prestazioni configurabili e affidabili e un rapido sviluppo. Tutti questi vantaggi sono garantiti da una piattaforma gestita supportata dalla potenza e dalla copertura di Microsoft Azure. 

Azure Cosmos DB è la soluzione ideale per applicazioni Web, per dispositivi mobili, di gioco e IoT in cui velocità effettiva prevedibile, disponibilità elevata, bassa latenza e un modello di dati privo di schema sono requisiti chiave. Offre flessibilità dello schema e indicizzazione avanzata e include il supporto di transazioni su più documenti con JavaScript integrato. 

Per altre domande sui database, risposte e istruzioni per la distribuzione e l'uso di questo servizio, vedere la pagina [Documentazione di Azure Cosmos DB](https://azure.microsoft.com/documentation/services/cosmos-db/).

### <a name="what-happened-to-documentdb"></a>Che cosa è successo a DocumentDB?
L'API DocumentDB è una delle API e uno dei modelli di dati supportati per Azure Cosmos DB. Azure Cosmos DB supporta l'utente anche con l'API Graph (anteprima), l'API Table (anteprima) e l'API MongoDB. Per altre informazioni, vedere [Domande di clienti di DocumentDB](#moving-to-cosmos-db).

### <a name="how-do-i-get-to-my-documentdb-account-in-the-azure-portal"></a>Come si accede al proprio account DocumentDB nel portale di Azure?
Nel portale di Azure fare clic sull'icona di Azure Cosmos DB nel riquadro sinistro. Se in precedenza si aveva un account DocumentDB, adesso si ha un account Azure Cosmos DB senza alcun cambiamento nella fatturazione.

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Quali sono i casi d'uso tipici per Azure Cosmos DB?
Azure Cosmos DB è la scelta ideale per le nuove applicazioni Web, per dispositivi mobili, di gioco e IoT in cui scalabilità automatica, prestazioni prevedibili, tempi di risposta rapidi nell'ordine di millisecondi e la possibilità di eseguire query su dati privi di schema sono importanti. Azure Cosmos DB si presta allo sviluppo rapido e al supporto dell'iterazione continua dei modelli di dati delle applicazioni. Le applicazioni che gestiscono contenuto e dati generati dall'utente rappresentano [casi d'uso comuni di Azure Cosmos DB](use-cases.md). 

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Come vengono offerte prestazioni prevedibili in Azure Cosmos DB?
Un'[unità richiesta](request-units.md) (UR) è l'unità di misura della velocità effettiva in Azure Cosmos DB. Una velocità effettiva di 1 UR corrisponde alla velocità effettiva dell'operazione GET su un documento da 1 KB. Ogni operazione in Azure Cosmos DB, incluse letture, scritture, query SQL ed esecuzioni di stored procedure, ha un valore di UR deterministico basato sulla velocità effettiva necessaria per completare l'operazione. Invece di considerare CPU, I/O e memoria e il modo in cui ognuno di essi influisce sulla velocità effettiva dell'applicazione, è possibile ragionare in termini di singola misura di UR.

Ogni contenitore di Azure Cosmos DB può essere riservato con la velocità effettiva di provisioning in termini di UR di velocità effettiva al secondo. Per applicazioni di qualsiasi dimensione, è possibile effettuare un benchmark delle singole richieste per misurarne i valori di UR ed effettuare il provisioning di un contenitore per gestire il totale delle unità richiesta tra tutte le richieste. È anche possibile aumentare o ridurre la velocità effettiva del contenitore con il variare delle esigenze dell'applicazione. Per altre informazioni sulle unità richiesta e su come determinare le esigenze a livello di contenitore, vedere [Stima delle esigenze di velocità effettiva](request-units.md#estimating-throughput-needs) e provare il [calcolatore della velocità effettiva](https://www.documentdb.com/capacityplanner). Il termine *contenitore* fa qui riferimento a una raccolta dell'API DocumentDB, un grafo dell'API Graph, una raccolta dell'API MongoDB e una tabella dell'API Table. 

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>In che modo Azure Cosmos DB supporta diversi modelli di dati, ad esempio chiave/valore, colonne, documenti e grafi?

I modelli di dati chiave/valore (tabella), colonne, documenti e grafi sono tutti supportati a livello nativo grazie alla progettazione ARS (atom, record e sequenze) su cui si basa Azure Cosmos DB. È facilmente possibile eseguire il mapping e la proiezione di atom, record e sequenze in diversi modelli di dati. Le API per un subset di modelli sono già disponibili (API DocumentDB, MongoDB, Table e Graph) e altre, specifiche di modelli di dati aggiuntivi, saranno disponibili in futuro.

Azure Cosmos DB ha un motore di indicizzazione indipendente dallo schema che può indicizzare automaticamente tutti i dati inseriti senza richiedere schemi o indici secondari agli sviluppatori. Il motore si basa su un set di layout di indice logici (invertiti, colonne, albero) che separano il layout di archiviazione dai sottosistemi di elaborazione di indici e query. Cosmos DB riesce anche a supportare un set di API e protocolli di trasmissione in modalità estendibile e a convertirli nel modello di dati principale (1) e nei layout di indice logici (2), supportando in modo esclusivo più modelli di dati a livello nativo.

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Azure Cosmos DB è conforme alla normativa HIPAA?
Sì, Azure Cosmos DB è conforme alla normativa HIPAA. La normativa HIPAA stabilisce i requisiti per l'uso, la divulgazione e la protezione delle informazioni sanitarie personali sensibili. Per altre informazioni, visitare il [Centro protezione Microsoft](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Quali sono i limiti di archiviazione di Azure Cosmos DB?
In Azure Cosmos DB non esiste alcun limite alla quantità totale di dati che può essere archiviata in un contenitore.

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Quali sono i limiti di velocità effettiva di Azure Cosmos DB?
In Azure Cosmos DB non esiste alcun limite alla velocità effettiva totale che può essere supportata da un contenitore. Lo scopo primario è distribuire il carico di lavoro in modo abbastanza uniforme tra un numero sufficientemente elevato di chiavi di partizione.

### <a name="how-much-does-azure-cosmos-db-cost"></a>Quanto costa Azure Cosmos DB?
Per informazioni dettagliate, vedere la pagina [Prezzi di Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/). Gli addebiti per l'utilizzo di Azure Cosmos DB sono determinati dal numero di contenitori sottoposti a provisioning, dal numero di ore in cui i contenitori sono stati online e dalla velocità effettiva di provisioning per ogni contenitore. Il termine *contenitori* fa qui riferimento a raccolte dell'API DocumentDB, grafi dell'API Graph, raccolte dell'API MongoDB e tabelle dell'API Table. 

### <a name="is-a-free-account-available"></a>È disponibile un account gratuito?
Sì, è possibile ottenere un account gratuito per un tempo limitato, senza alcun impegno. Per iscriversi, visitare la pagina [Prova gratuitamente Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/). Per altre informazioni, vedere [Domande frequenti su Azure Cosmos DB](#try-cosmos-db).

I nuovi utenti possono iscriversi per ottenere un [account gratuito di Azure](https://azure.microsoft.com/free/)che è valido 30 giorni e include un credito per provare tutti i servizi di Azure. Se si ha una sottoscrizione di Visual Studio, si ha anche diritto a [crediti Azure gratuiti](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) da usare per qualsiasi servizio di Azure. 

È anche possibile usare l'[emulatore di Azure Cosmos DB](local-emulator.md) per sviluppare e testare gratuitamente l'applicazione in locale, senza creare una sottoscrizione di Azure. Quando si è soddisfatti del funzionamento dell'applicazione nell'emulatore di Azure Cosmos DB, è possibile iniziare a usare l'account Azure Cosmos DB nel cloud.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Come è possibile ottenere informazioni e supporto aggiuntivi per Azure Cosmos DB?
Per ottenere informazioni o supporto, visitare il sito [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-cosmosdb) o il [forum MSDN](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=AzureDocumentDB) oppure programmare una chat diretta con il team di progettazione di Azure Cosmos DB inviando un messaggio di posta elettronica all'indirizzo [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com). 

<a id="try-cosmos-db"></a>
## <a name="try-azure-cosmos-db-subscriptions"></a>Provare gratuitamente Azure Cosmos DB

È ora possibile provare Azure Cosmos DB per un tempo limitato senza alcuna sottoscrizione, gratuitamente e senza impegno. Per registrarsi a una sottoscrizione di prova di Azure Cosmos DB, andare alla pagina [Prova gratuitamente Azure DB Cosmos](https://azure.microsoft.com/try/cosmosdb/). Questa sottoscrizione differisce dalla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/free/) e può essere usata in aggiunta alla versione di valutazione gratuita di Azure o a una sottoscrizione a pagamento di Azure. 

Le sottoscrizioni di prova di Azure Cosmos DB vengono visualizzate nel portale di Azure accanto alle altre sottoscrizioni associate al proprio ID utente. 

Le condizioni seguenti si applicano alle sottoscrizioni di prova di Azure Cosmos DB:

* Un contenitore per sottoscrizione per account SQL (API DocumentDB), Gremlin (API Graph) e Tabella.
* Fino a 3 raccolte per sottoscrizione per gli account MongoDB.
* 10 GB di capacità di archiviazione.
* La replica globale è disponibile nelle seguenti [aree di Azure](https://azure.microsoft.com/regions/): Stati Uniti centrali, Europa settentrionale e Asia sudorientale
* Velocità effettiva massima di 5.000 UR/sec.
* Le sottoscrizioni scadono dopo 24 ore e possono essere estese fino a un massimo di 48 ore totali.
* Non è possibile creare ticket di supporto di Azure per gli account di prova di Azure Cosmos DB. È tuttavia previsto il supporto per i sottoscrittori con piani di supporto attivi. 

## <a name="set-up-azure-cosmos-db"></a>Configurazione di Azure Cosmos DB
### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Come ci si iscrive ad Azure Cosmos DB?
Azure Cosmos DB è disponibile nel portale di Azure. Per prima cosa, iscriversi per ottenere una sottoscrizione di Azure. Dopo aver effettuato l'iscrizione, è possibile aggiungere un account API DocumentDB, API Graph (anteprima), API Table (anteprima) o API MongoDB alla sottoscrizione di Azure.

### <a name="what-is-a-master-key"></a>Che cos'è una chiave master?
Una chiave master è un token di sicurezza per l'accesso a tutte le risorse di un account. Gli utenti con la chiave hanno accesso in lettura e scrittura a tutte le risorse nell'account del database. Distribuire le chiavi master con cautela. La chiave master primaria e la chiave master secondaria sono disponibili nel pannello **Chiavi** del [portale di Azure][azure-portal]. Per altre informazioni sulle chiavi, vedere [Visualizzare, copiare e rigenerare le chiavi di accesso](manage-account.md#keys).

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>Su quali aree è possibile impostare PreferredLocations? 
Il valore di PreferredLocations può essere impostato su qualsiasi area di Azure in cui è disponibile Cosmos DB. Per un elenco delle aree disponibili, vedere [Aree di Azure](https://azure.microsoft.com/regions/).

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Di quali aspetti è consigliabile tenere conto quando si distribuiscono dati nel mondo tramite i data center di Azure? 
Azure Cosmos DB è presente in tutte le aree di Azure, come specificato nella pagina [Aree di Azure](https://azure.microsoft.com/regions/). Dato che si tratta del servizio di base, in ogni nuovo data center è disponibile Azure Cosmos DB. 

Quando si imposta un'area, tenere presente che Azure Cosmos DB rispetta i cloud sovrani e per enti pubblici. Di conseguenza, se si crea un account in un'area sovrana non è possibile eseguire la replica all'esterno di tale area. Analogamente, non è possibile abilitare la replica in altre località sovrane da un account esterno. 

## <a name="develop-against-the-documentdb-api"></a>Sviluppo con l'API DocumentDB

### <a name="how-do-i-start-developing-against-the-documentdb-api"></a>Come si inizia a eseguire lo sviluppo con l'API DocumentDB?
L'API Microsoft DocumentDB è disponibile nel [portale di Azure][azure-portal]. Per prima cosa, è necessario iscriversi per ottenere una sottoscrizione di Azure. Dopo aver ottenuto una sottoscrizione di Azure, è possibile aggiungere un contenitore dell'API DocumentDB alla sottoscrizione di Azure. Per istruzioni sull'aggiunta di un account Azure Cosmos DB, vedere [Creare un account di database](create-documentdb-dotnet.md#create-account). Se in passato si aveva un account DocumentDB, adesso si ha un account Azure Cosmos DB. 

[SDK](documentdb-sdk-dotnet.md) per .NET, Python, Node.js, JavaScript e Java. Gli sviluppatori possono anche usare le [API HTTP RESTful](/rest/api/documentdb/) per interagire con le risorse di Azure Cosmos DB da un'ampia gamma di piattaforme e linguaggi.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>È possibile accedere a esempi già pronti per iniziare rapidamente?
In GitHub sono disponibili esempi per gli SDK [.NET](documentdb-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](documentdb-nodejs-samples.md) e [Python](documentdb-python-samples.md) per l'API DocumentDB.


### <a name="does-the-documentdb-api-database-support-schema-free-data"></a>Il database dell'API DocumentDB supporta dati privi di schema?
Sì. L'API DocumentDB consente alle applicazioni di archiviare documenti JSON arbitrari senza hint o definizioni dello schema. I dati sono immediatamente disponibili per l'esecuzione di query tramite l'interfaccia di query SQL di Azure Cosmos DB.  

### <a name="does-the-documentdb-api-support-acid-transactions"></a>L'API DocumentDB supporta le transazioni ACID?
Sì. L'API DocumentDB supporta le transazioni tra documenti espresse come trigger e stored procedure JavaScript. L'ambito delle transazioni è limitato a una singola partizione in ogni raccolta e le transazioni vengono eseguite con semantica ACID in modalità "tutto o niente", isolate da altro codice e altre richieste utente in esecuzione contemporaneamente. Se vengono generate eccezioni tramite l'esecuzione sul lato server del codice dell'applicazione JavaScript, viene eseguito il rollback dell'intera transazione. Per altre informazioni sulle transazioni, vedere [Transazioni del programma del database](programming.md#database-program-transactions).

### <a name="what-is-a-collection"></a>Che cos'è una raccolta?
Una raccolta è un gruppo di documenti con la logica dell'applicazione JavaScript associata. Una raccolta è un'entità fatturabile, in cui il [costo](performance-levels.md) è determinato dalla velocità effettiva e dallo spazio di archiviazione usato. Le raccolte possono estendersi su una o più partizioni o server e possono essere ridimensionate per gestire volumi praticamente illimitati di archiviazione o velocità effettiva.

Le raccolte sono le entità di fatturazione anche per Azure Cosmos DB. Ogni raccolta viene fatturata su base oraria a seconda della velocità effettiva di provisioning e dello spazio di archiviazione usato. Per altre informazioni, vedere [Prezzi di Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/). 

### <a name="how-do-i-create-a-database"></a>Come si crea un database?
È possibile creare database usando il [portale di Azure](https://portal.azure.com), come descritto in [Aggiungere una raccolta](create-documentdb-dotnet.md#create-collection), uno degli [SDK di Azure Cosmos DB](documentdb-sdk-dotnet.md) o le [API REST](/rest/api/documentdb/). 

### <a name="how-do-i-set-up-users-and-permissions"></a>Come è possibile configurare gli utenti e le autorizzazioni?
È possibile creare utenti e autorizzazioni usando uno degli [SDK per l'API di Cosmos DB](documentdb-sdk-dotnet.md) o le [API REST](/rest/api/documentdb/).  

### <a name="does-the-documentdb-api-support-sql"></a>L'API DocumentDB supporta SQL?
Il linguaggio di query SQL è un sottoinsieme ottimizzato delle funzionalità di query supportate da SQL. Il linguaggio di query SQL di Azure Cosmos DB offre operatori gerarchici e relazionali avanzati ed estendibilità tramite funzioni definite dall'utente basate su JavaScript. La grammatica JSON consente la modellazione di documenti JSON come alberi con nodi con etichetta, che vengono usati sia dalle tecniche di indicizzazione automatica di Azure Cosmos DB che dal dialetto di query SQL di Azure Cosmos DB. Per informazioni sull'uso della grammatica SQL, vedere l'articolo relativo alle [query in DocumentDB][query].

### <a name="does-the-documentdb-api-support-sql-aggregation-functions"></a>L'API DocumentDB supporta le funzioni di aggregazione SQL?
L'API DocumentDB supporta l'aggregazione a bassa latenza su qualsiasi scala con le funzioni di aggregazione `COUNT`, `MIN`, `MAX`, `AVG` e `SUM` tramite la grammatica SQL. Per altre informazioni, vedere [Funzioni di aggregazione](documentdb-sql-query.md#Aggregates).

### <a name="how-does-the-documentdb-api-provide-concurrency"></a>Come viene offerta la concorrenza nell'API DocumentDB?
L'API DocumentDB supporta il controllo della concorrenza ottimistica tramite tag di entità HTTP (ETag). Ogni risorsa dell'API DocumentDB ha un ETag, che viene impostato nel server ogni volta che un documento viene aggiornato. In tutti i messaggi di risposta sono inclusi l'intestazione ETag e il valore corrente. Gli ETag possono essere usati con l'intestazione If-Match per consentire al server di stabilire se una risorsa deve essere aggiornata. Il valore di If-Match è il valore ETag con cui eseguire il confronto. Se il valore ETag corrisponde al valore ETag del server, la risorsa verrà aggiornata. Se l'ETag non è aggiornato, il server rifiuta l'operazione con un codice di risposta di tipo "HTTP 412 - Precondizione non riuscita". Il client recupera quindi nuovamente la risorsa per acquisire il relativo valore ETag aggiornato. Inoltre, gli ETag possono essere usati con l'intestazione If-None-Match per stabilire se è necessario ripetere il recupero di una risorsa.

Per applicare la concorrenza ottimistica in .NET, usare la classe [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) . Per un esempio .NET, vedere [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) nell'esempio DocumentManagement in GitHub.

### <a name="how-do-i-perform-transactions-in-the-documentdb-api"></a>Come si eseguono le transazioni nell'API DocumentDB?
L'API DocumentDB supporta transazioni integrate con il linguaggio tramite trigger e stored procedure JavaScript. Tutte le operazioni su database negli script vengono eseguite con isolamento dello snapshot. In caso di raccolta con partizione singola, l'ambito dell'esecuzione è limitato alla raccolta. Se la raccolta è partizionata, l'ambito dell'esecuzione è limitato ai documenti con lo stesso valore di chiave di partizione all'interno della raccolta. Viene acquisito uno snapshot delle versioni (ETag) del documento all'inizio della transazione e il commit della transazione viene eseguito solo se lo script ha esito positivo. Se JavaScript genera un errore, viene eseguito il rollback della transazione. Per altre informazioni, vedere [Programmazione JavaScript lato server per Azure Cosmos DB](programming.md).

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>Come è possibile eseguire inserimenti in blocco in Cosmos DB?
È possibile eseguire l'inserimento in blocco di documenti in Azure Cosmos DB in uno dei due modi seguenti:

* Strumento di migrazione dei dati, descritto in [Strumento di migrazione dei database per Azure Cosmos DB](import-data.md).
* Stored procedure, descritte in [Programmazione JavaScript lato server per Azure Cosmos DB](programming.md).

### <a name="does-the-documentdb-api-support-resource-link-caching"></a>L'API DocumentDB supporta la memorizzazione nella cache dei collegamenti alle risorse?
Sì. Dato che Azure Cosmos DB è un servizio RESTful, i collegamenti alle risorse sono immutabili e possono essere memorizzati nella cache. I client dell'API DocumentDB possono specificare un'intestazione "If-None-Match" per le letture su qualsiasi raccolta o documento di tipo risorsa e quindi aggiornare le copie locali in seguito alla modifica della versione del server.

### <a name="is-a-local-instance-of-documentdb-api-available"></a>È disponibile un'istanza locale dell'API DocumentDB?
Sì. L'[emulatore di Azure Cosmos DB](local-emulator.md) offre un'emulazione estremamente fedele del servizio Cosmos DB. Supporta le stesse funzionalità di Azure Cosmos DB, incluso il supporto per la creazione e l'esecuzione di query su documenti JSON, il provisioning e il ridimensionamento delle raccolte e l'esecuzione di stored procedure e trigger. È possibile sviluppare e testare le applicazioni usando l'emulatore di Azure Cosmos DB e distribuirle in Azure su scala globale apportando una singola modifica di configurazione all'endpoint di connessione per Azure Cosmos DB.

## <a name="develop-against-the-api-for-mongodb"></a>Sviluppo con l'API per MongoDB
### <a name="what-is-the-azure-cosmos-db-api-for-mongodb"></a>Che cos'è l'API di Azure Cosmos DB per MongoDB?
L'API di Azure Cosmos DB per MongoDB è un livello di compatibilità che consente alle applicazioni di comunicare in modo facile e trasparente con il motore di database nativo di Azure Cosmos DB usando le API e i driver di Apache MongoDB supportati dalla community. Gli sviluppatori possono ora usare le catene di strumenti e le competenze esistenti di MongoDB per creare applicazioni che sfruttano Azure Cosmos DB. Gli sviluppatori possono trarre vantaggio dalle funzionalità specifiche di Azure Cosmos DB, tra cui l'indicizzazione automatica, la manutenzione di backup, i contratti di servizio con supporto finanziario e così via.

### <a name="how-do-i-connect-to-my-api-for-mongodb-database"></a>Come ci si connette al database dell'API per MongoDB?
Il modo più veloce per connettersi all'API di Azure Cosmos DB per MongoDB consiste nel passare al [portale di Azure](https://portal.azure.com). Accedere al proprio account e quindi fare clic su **Avvio rapido** nel menu di spostamento a sinistra. Avvio rapido è l'approccio ottimale per ottenere i frammenti di codice per la connessione al database. 

Azure Cosmos DB applica standard e requisiti di sicurezza rigidi. Gli account Azure Cosmos DB richiedono l'autenticazione e la comunicazione sicura tramite SSL. Assicurarsi quindi di usare TLSv1.2.

Per altre informazioni, vedere l'articolo su come [connettersi al database dell'API per MongoDB](connect-mongodb-account.md).

### <a name="are-there-additional-error-codes-for-an-api-for-mongodb-database"></a>Sono disponibili codici errore aggiuntivi per un database dell'API per MongoDB?
Oltre ai codici errore comuni di MongoDB, l'API per MongoDB include codici errore specifici:


| Tipi di errore               | Codice  | Descrizione  | Soluzione  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Il numero totale di unità richiesta utilizzate ha superato il livello di unità richiesta di cui è stato effettuato il provisioning per la raccolta ed è stata applicata la limitazione. | Valutare la possibilità di ridimensionare la velocità effettiva della raccolta dal portale di Azure o provare di nuovo. |
| ExceededMemoryLimit | 16501 | In quanto servizio multi-tenant, l'operazione ha superato il limite di allocazione di memoria del client. | Ridurre l'ambito dell'operazione tramite criteri di query più restrittivi oppure contattare il supporto tecnico dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). <br><br>Esempio: *&nbsp;&nbsp;&nbsp;&nbsp;db.getCollection('users').aggregate([<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$match: {name: "Andy"}}, <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$sort: {age: -1}}<br>&nbsp;&nbsp;&nbsp;&nbsp;])*) |

## <a name="develop-with-the-table-api-preview"></a>Sviluppo con l'API Table (anteprima)

### <a name="terms"></a>Termini 
L'API Table (anteprima) di Azure Cosmos DB fa riferimento all'offerta Premium di Azure Cosmos DB per il supporto di tabelle annunciata in occasione di Build 2017. 

L'SDK per tabelle standard è l'SDK per tabelle di Archiviazione di Azure esistente. 

### <a name="how-can-i-use-the-new-table-api-preview-offering"></a>Come si può usare la nuova offerta dell'API Table (anteprima)? 
L'API Table di Azure Cosmos DB è disponibile nel [portale di Azure][azure-portal]. Per prima cosa, è necessario iscriversi per ottenere una sottoscrizione di Azure. Dopo aver effettuato l'iscrizione, è possibile aggiungere un account API Table di Azure Cosmos DB alla sottoscrizione di Azure e quindi aggiungere tabelle all'account. 

Nel periodo di anteprima, durante il quale sono disponibili [SDK](../cosmos-db/table-sdk-dotnet.md) per .NET, vedere la guida introduttiva all'[API Table](../cosmos-db/create-table-dotnet.md) per iniziare.

### <a name="do-i-need-a-new-sdk-to-use-the-table-api-preview"></a>È necessario un nuovo SDK per usare l'API Table (anteprima)? 
Sì. In NuGet è disponibile [Windows Azure Storage Premium Table (Preview) SDK](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable). Per altre informazioni, vedere la pagina relativa a [download e note sulla versione dell'API Table .NET di Azure Cosmos DB](https://github.com/Microsoft/azure-docs-pr/cosmos-db/table-sdk-dotnet.md). 

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Come si forniscono commenti e suggerimenti sull'SDK o sui bug?
È possibile condividere commenti e suggerimenti in uno dei modi seguenti:

* [UserVoice](https://feedback.azure.com/forums/599062-azure-cosmos-db-table-api)
* [Forum MSDN](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=AzureDocumentDB)
* [Stackoverflow](http://stackoverflow.com/questions/tagged/azure-cosmosdb)

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api-preview"></a>Quale stringa di connessione è necessario usare per connettersi all'API Table (anteprima)?
La stringa di connessione è:
```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountNameFromDocumentDB>.documents.azure.com
```
È possibile ottenere la stringa di connessione nella pagina Chiavi del portale di Azure. 

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-new-table-api-preview"></a>Come si sostituiscono le impostazioni di configurazione per le opzioni relative alle richieste nella nuova API Table (anteprima)?
Per informazioni sulle impostazioni di configurazione, vedere [Funzionalità di Azure Cosmos DB](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities). È possibile modificare le impostazioni aggiungendole nella sezione appSettings di app.config nell'applicazione client.

    <appSettings>
        <add key="TableConsistencyLevel" value="Eventual|Strong|Session|BoundedStaleness|ConsistentPrefix"/>
        <add key="TableThroughput" value="<PositiveIntegerValue"/>
        <add key="TableIndexingPolicy" value="<jsonindexdefn>"/>
        <add key="TableUseGatewayMode" value="True|False"/>
        <add key="TablePreferredLocations" value="Location1|Location2|Location3|Location4>"/>....
    </appSettings>


### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-standard-table-sdk"></a>Sono stati introdotti cambiamenti per i clienti che usano l'SDK per tabelle standard esistente?
Nessuno. Non è stato introdotto alcun cambiamento per i clienti nuovi o esistenti che usano l'SDK per tabelle standard esistente. 

### <a name="how-do-i-view-table-data-that-is-stored-in-azure-cosmos-db-for-use-with-the-table-api-review"></a>Come si visualizzano i dati di tabella archiviati in Azure Cosmos DB da usare con l'API Table (anteprima)? 
Per esplorare i dati si può usare il portale di Azure. È anche possibile usare il codice dell'API Table (anteprima) o gli strumenti illustrati nella risposta successiva. 

### <a name="which-tools-work-with-the-table-api-preview"></a>Quali strumenti è possibile usare con l'API Table (anteprima)? 
È possibile usare la versione precedente di Azure Explorer (0.8.9).

La nuova API Table (anteprima) è supportata dagli strumenti sufficientemente flessibili da accettare una stringa di connessione nel formato specificato in precedenza. Per un elenco degli strumenti di tabella, vedere la pagina [Strumenti client di Archiviazione di Azure](../storage/common/storage-explorers.md). 

### <a name="do-powershell-or-azure-cli-work-with-the-new-table-api-preview"></a>È possibile usare PowerShell o l'interfaccia della riga di comando di Azure con la nuova API Table (anteprima)?
È prevista l'aggiunta del supporto di PowerShell e dell'interfaccia della riga di comando di Azure per l'API Table (anteprima). 

### <a name="is-the-concurrency-on-operations-controlled"></a>La concorrenza nelle operazioni è controllata?
Sì. La concorrenza ottimistica è supportata tramite l'uso del meccanismo degli ETag. 

### <a name="is-the-odata-query-model-supported-for-entities"></a>È supportato il modello di query OData per le entità? 
Sì. L'API Table (anteprima) supporta query OData e query LINQ. 

### <a name="can-i-connect-to-the-standard-azure-table-and-the-new-premium-table-api-preview-side-by-side-in-the-same-application"></a>È supportata la connessione affiancata alla tabella standard di Azure e alla nuova API Table (anteprima) Premium nella stessa applicazione? 
Sì. È possibile connettersi creando due istanze separate di CloudTableClient, ognuna delle quali punta a un URI specifico tramite la stringa di connessione.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-new-offering"></a>Come si esegue la migrazione di un'applicazione di archiviazione tabelle di Azure esistente a questa nuova offerta?
Per sfruttare la nuova offerta dell'API Table con dati di archivi tabelle esistenti, contattare [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com). 

### <a name="what-is-the-roadmap-for-this-service-and-when-will-you-offer-other-standard-table-api-functionality"></a>Qual è la roadmap per questo servizio e quando verranno offerte altre funzionalità standard dell'API Table?
È prevista l'aggiunta del supporto per i token di firma di accesso condiviso, il contesto del servizio, le statistiche, la crittografia lato client, l'analisi e altre funzionalità nel corso del processo verso la disponibilità generale. È possibile fornire commenti e suggerimenti in [UserVoice](https://feedback.azure.com/forums/599062-azure-cosmos-db-table-api). 

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>Come si espandono le dimensioni di archiviazione per questo servizio, se ad esempio *n* GB di dati iniziali crescono nel tempo fino a 1 TB? 
Azure Cosmos DB è progettato per offrire spazio di archiviazione illimitato tramite scalabilità orizzontale. Il servizio può monitorare e aumentare efficacemente lo spazio di archiviazione. 

### <a name="how-do-i-monitor-the-table-api-preview-offering"></a>Come si monitora l'offerta dell'API Table (anteprima)?
È possibile usare il riquadro **Metriche** dell'API Table (anteprima) per monitorare le richieste e l'utilizzo dello spazio di archiviazione. 

### <a name="how-do-i-calculate-the-throughput-i-require"></a>Come si calcola la velocità effettiva necessaria?
È possibile usare lo strumento di stima della capacità per calcolare il valore di TableThroughput necessario per le operazioni. Per altre informazioni, vedere [Estimate Request Units and Data Storage](https://www.documentdb.com/capacityplanner) (Stimare le unità richiesta e l'archiviazione dei dati). In generale, è possibile rappresentare un'entità in formato JSON e specificare i numeri relativi alle operazioni. 

### <a name="can-i-use-the-new-table-api-preview-sdk-locally-with-the-emulator"></a>È possibile usare il nuovo SDK dell'API Table (anteprima) in locale con l'emulatore?
Sì. Quando si usa il nuovo SDK, è possibile usare l'API Table (anteprima) con l'emulatore locale. Per scaricare il nuovo emulatore, vedere [Use the Azure Cosmos DB Emulator for local development and testing](local-emulator.md) (Usare l'emulatore di Azure Cosmos DB per sviluppo e test in locale). Il valore di StorageConnectionString in app.config deve essere:

```
DefaultEndpointsProtocol=https;AccountName=localhost;AccountKey=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==;TableEndpoint=https://localhost:8081`. 
```

### <a name="can-my-existing-application-work-with-the-table-api-preview"></a>È possibile usare un'applicazione esistente con l'API Table (anteprima)? 
La superficie di attacco della nuova API Table (anteprima) è compatibile con l'SDK per tabelle standard di Azure esistente nelle operazioni di creazione, eliminazione, aggiornamento e query dell'API .NET. Verificare di avere una chiave di riga, perché l'API Table (anteprima) richiede sia una chiave di partizione che una chiave di riga. È prevista anche l'aggiunta di maggiore supporto a livello di SDK nel corso del processo verso la disponibilità generale di questa offerta di servizio.

### <a name="do-i-need-to-migrate-my-existing-azure-table-based-applications-to-the-new-sdk-if-i-do-not-want-to-use-the-table-api-preview-features"></a>È necessario eseguire la migrazione delle applicazioni basate su tabelle di Azure esistenti al nuovo SDK se non si vogliono usare le funzionalità dell'API Table (anteprima)?
No. È possibile creare e usare asset tabella standard esistenti senza interruzioni di alcun tipo. Se non si usa la nuova API Table (anteprima), tuttavia, non è possibile usufruire dell'indicizzazione automatica, dell'opzione di coerenza aggiuntiva o della distribuzione globale. 

### <a name="how-do-i-add-replication-of-the-data-in-the-premium-table-api-preview-across-multiple-regions-of-azure"></a>Come si aggiunge la replica in più aree di Azure dei dati dell'API Table (anteprima) Premium?
È possibile usare le [impostazioni di replica globale](tutorial-global-distribution-documentdb.md#portal) del portale di Azure Cosmos DB per aggiungere le aree idonee per l'applicazione. Per sviluppare un'applicazione distribuita a livello globale, è consigliabile anche aggiungere l'applicazione con il valore di PreferredLocation impostato sull'area locale, per garantire una bassa latenza di lettura. 

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-premium-table-api-preview"></a>Come si modifica l'area di scrittura primaria per l'account nell'API Table (anteprima) Premium?
È possibile usare il riquadro del portale relativo alla replica globale di Azure Cosmos DB per aggiungere un'area e quindi effettuare il failover nell'area necessaria. Per istruzioni, vedere l'articolo relativo allo [sviluppo con account Azure Cosmos DB tra più aree](regional-failover.md). 

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>Come si configurano le aree di lettura preferite per una bassa latenza quando si distribuiscono i dati? 
Per facilitare la lettura dalla località locale, usare la chiave PreferredLocation nel file app.config. Per le applicazioni esistenti, l'API Table (anteprima) genera un errore in caso di impostazione di LocationMode. Rimuovere tale codice, perché l'API Table (anteprima) Premium acquisisce queste informazioni dal file app.config. Per altre informazioni, vedere [Funzionalità di Azure Cosmos DB](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities).

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api-preview"></a>Come funzionano i livelli di coerenza nell'API Table (anteprima)? 
Azure Cosmos DB offre compromessi ben ponderati tra coerenza, disponibilità e latenza. Azure Cosmos DB offre cinque livelli di coerenza per gli sviluppatori che usano l'API Table (anteprima), consentendo così di scegliere il modello di coerenza appropriato a livello di tabella ed effettuare singole richieste durante l'esecuzione di query sui dati. Quando un client si connette, può specificare un livello di coerenza. È possibile modificare il livello tramite l'impostazione del valore della chiave TableConsistencyLevel in app.config. 

L'API Table (anteprima) offre letture a bassa latenza con la "lettura delle proprie scritture", con la coerenza di sessione come impostazione predefinita. Per altre informazioni, vedere l'articolo relativo ai [livelli di coerenza](consistency-levels.md). 

Per impostazione predefinita, l'archivio tabelle di Azure offre coerenza assoluta all'interno di un'area e coerenza finale nelle località secondarie. 

### <a name="does-azure-cosmos-db-offer-more-consistency-levels-than-standard-tables"></a>Azure Cosmos DB offre più livelli di coerenza rispetto alle tabelle standard?
Sì. Per informazioni su come trarre vantaggio dalla natura distribuita di Azure Cosmos DB, vedere l'articolo relativo ai [livelli di coerenza](consistency-levels.md). Grazie alle garanzie associate ai livelli di coerenza, possono essere usati in tutta sicurezza. Per altre informazioni, vedere [Funzionalità di Azure Cosmos DB](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities).

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>Quando è abilitata la distribuzione globale, quanto tempo è necessario per replicare i dati?
Viene eseguito il commit permanente dei dati nell'area locale e ne viene effettuato immediatamente il push nelle altre aree, nell'arco di millisecondi. Questa replica dipende solo dal tempo di round trip del data center. Per altre informazioni sulla funzionalità di distribuzione globale di Azure Cosmos DB, vedere l'articolo relativo ad [Azure Cosmos DB come servizio di database distribuito a livello globale in Azure](distribute-data-globally.md).

### <a name="can-the-read-request-consistency-level-be-changed"></a>È possibile modificare il livello di coerenza delle richieste di lettura?
Con Azure Cosmos DB, è possibile impostare il livello di coerenza a livello di contenitore (nella tabella). Usando l'SDK, si può modificare il livello specificando il valore della chiave TableConsistencyLevel nel file app.config. I valori possibili sono: Strong, Bounded Staleness, Session, Consistent Prefix ed Eventual. Per altre informazioni, vedere [Livelli di coerenza dei dati ottimizzabili in Azure Cosmos DB](consistency-levels.md). Il concetto chiave è che non è possibile impostare un livello di coerenza delle richieste superiore rispetto all'impostazione della tabella. Ad esempio, non è possibile impostare il livello di coerenza Eventual per la tabella e il livello di coerenza Strong per le richieste. 

### <a name="how-does-the-premium-table-api-preview-account-handle-failover-if-a-region-goes-down"></a>Come viene gestito il failover dall'account API Table (anteprima) Premium se un'area diventa inattiva? 
L'API Table (anteprima) Premium usa la piattaforma distribuita a livello globale di Azure Cosmos DB. Affinché l'applicazione possa tollerare i tempi di inattività del data center, abilitare almeno un'altra area per l'account nel portale di Azure Cosmos DB, come illustrato nell'articolo relativo allo [sviluppo con account Azure Cosmos DB tra più aree](regional-failover.md). Si può impostare la priorità dell'area usando il portale. Vedere l'articolo relativo allo [sviluppo con account Azure Cosmos DB tra più aree](regional-failover.md). 

È possibile aggiungere il numero di aree desiderato per l'account e controllare in quale potrà essere effettuato il failover specificando una priorità di failover. Per usare il database, è necessario includere anche un'applicazione. Nel corso di questa operazione, i clienti non riscontrano tempi di inattività. L'SDK client supporta l'hosting automatico, ovvero può rilevare l'area inattiva ed effettuare automaticamente il failover nella nuova area.

### <a name="is-the-premium-table-api-preview-enabled-for-backups"></a>L'API Table (anteprima) Premium è abilitata per il backup?
L'API Table (anteprima) Premium usa la piattaforma di Azure Cosmos DB per il backup. I backup vengono eseguiti automaticamente. Per altre informazioni, vedere [Backup online automatico e ripristino con Azure Cosmos DB](online-backup-and-restore.md).

 
### <a name="does-the-table-api-preview-index-all-attributes-of-an-entity-by-default"></a>L'API Table (anteprima) indicizza tutti gli attributi di un'entità per impostazione predefinita?
Sì. Per impostazione predefinita vengono indicizzati tutti gli attributi di un'entità. Per altre informazioni, vedere l'articolo relativo ai [criteri di indicizzazione di Azure Cosmos DB](indexing-policies.md). 

### <a name="does-this-mean-i-do-not-have-to-create-multiple-indexes-to-satisfy-the-queries"></a>Per soddisfare le query è quindi necessario creare più indici? 
Sì. Azure Cosmos DB offre l'indicizzazione automatica di tutti gli attributi senza definizione di schema. Questa automazione consente agli sviluppatori di concentrarsi sull'applicazione anziché sulla creazione e sulla gestione degli indici. Per altre informazioni, vedere l'articolo relativo ai [criteri di indicizzazione di Azure Cosmos DB](indexing-policies.md).

### <a name="can-i-change-the-indexing-policy"></a>È possibile modificare i criteri di indicizzazione?
Sì. È possibile modificare i criteri di indicizzazione specificando la definizione di indice. Per altre informazioni, vedere [Funzionalità di Azure Cosmos DB](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities). È necessario eseguire correttamente la codifica e l'escape delle impostazioni. 

Nel formato di stringa JSON nel file app.config:
```
{
  "indexingMode": "consistent",
  "automatic": true,
  "includedPaths": [
    {
      "path": "/somepath",
      "indexes": [
        {
          "kind": "Range",
          "dataType": "Number",
          "precision": -1
        },
        {
          "kind": "Range",
          "dataType": "String",
          "precision": -1
        } 
      ]
    }
  ],
  "excludedPaths": 
[
 {
      "path": "/anotherpath"
 }
]
}
```

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>Come piattaforma, Azure Cosmos DB offre numerose funzionalità, come l'ordinamento, le aggregazioni, la gerarchia e così via. Queste funzionalità verranno aggiunte all'API Table? 
Nella versione di anteprima, l'API Table offre le stesse funzionalità di query dell'archivio tabelle di Azure. Azure Cosmos DB supporta anche l'ordinamento, le aggregazioni, le query geospaziali, la gerarchia e un'ampia gamma di funzioni predefinite. Le funzionalità aggiuntive verranno incluse nell'API Table in un aggiornamento futuro del servizio. Per altre informazioni, vedere [Query SQL per l'API di DocumentDB di Azure Cosmos DB](../documentdb/documentdb-sql-query.md).
 
### <a name="when-should-i-change-tablethroughput-for-the-table-api-preview"></a>Quando è consigliabile modificare TableThroughput per l'API Table (anteprima)?
È consigliabile modificare TableThroughput quando si verifica una delle condizioni seguenti:
* Si esegue un'operazione di estrazione, trasformazione e caricamento (ETL) di dati o si vuole caricare una grande quantità di dati in breve tempo. 
* È necessaria una velocità effettiva superiore dal contenitore al back-end. Ad esempio, si rileva che la velocità effettiva usata è superiore alla velocità effettiva di provisioning e viene applicata la limitazione. Per altre informazioni, vedere [Impostare la velocità effettiva per i contenitori di Azure Cosmos DB](set-throughput.md).

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-preview-table"></a>È possibile aumentare o ridurre la velocità effettiva di una tabella dell'API Table (anteprima)? 
Sì. È possibile usare il riquadro relativo alla scalabilità del portale di Azure Cosmos DB per ridimensionare la velocità effettiva. Per altre informazioni, vedere l'articolo su come [impostare la velocità effettiva](set-throughput.md).

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>Per le tabelle appena sottoposte a provisioning viene impostato un valore predefinito di TableThroughput?
Sì. Se non si sostituisce il valore di TableThroughput in app.config e non si usa un contenitore già creato in Azure Cosmos DB, il servizio crea una tabella con velocità effettiva di 400.
 
### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-standard-table-api"></a>I prezzi per i clienti esistenti dell'API Table standard verranno modificati?
No. Il prezzo per i clienti esistenti dell'API Table standard non verrà modificato. 

### <a name="how-is-the-price-calculated-for-the-table-api-preview"></a>Come viene calcolato il prezzo per l'API Table (anteprima)? 
Il prezzo dipende dal valore di TableThroughput allocato. 

### <a name="how-do-i-handle-any-throttling-on-the-tables-in-table-api-preview-offering"></a>Come si gestisce l'eventuale limitazione nelle tabelle dell'offerta dell'API Table (anteprima)? 
Se la frequenza delle richieste supera la capacità della velocità effettiva di provisioning per il contenitore sottostante, verrà restituito un errore e l'SDK ripeterà la chiamata applicando i criteri di ripetizione dei tentativi.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-premium-table-api-preview-offering-of-azure-cosmos-db"></a>Perché è necessario scegliere una velocità effettiva oltre alla chiave di partizione e alla chiave di riga per sfruttare l'offerta dell'API Table (anteprima) Premium di Azure Cosmos DB?
Azure Cosmos DB imposta una velocità effettiva predefinita per il contenitore, se non se ne specifica una nel file app.config. 

Azure Cosmos DB offre garanzie di prestazioni e latenza, con limiti superiori nelle operazioni. Questa garanzia è possibile quando il motore può applicare la governance nelle operazioni del tenant. L'impostazione di TableThroughput assicura la velocità effettiva e la latenza garantite, perché la piattaforma riserva tale capacità e garantisce l'esito positivo delle operazioni. 

È possibile usare la specifica della velocità effettiva modificandola in modo elastico per trarre vantaggio dalla stagionalità dell'applicazione, soddisfare le esigenze in termini di velocità effettiva e ridurre i costi.

### <a name="azure-storage-sdk-has-been-very-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-new-azure-cosmos-db-offering-seems-to-be-charging-me-even-though-i-have-not-performed-a-single-transaction-or-stored-anything-can-you-please-explain"></a>Azure Storage SDK può essere molto economico se si eseguono raramente query, perché si paga solo per l'archiviazione dei dati. Con la nuova offerta Azure Cosmos DB sembra che vengano effettuati addebiti anche se non si esegue alcuna transazione o non si archiviano dati. Perché?

Azure Cosmos DB è progettato come un sistema basato su contratti di servizio distribuito a livello globale con garanzie di disponibilità, latenza e velocità effettiva. La velocità effettiva riservata in Azure Cosmos DB è garantita, a differenza della velocità effettiva di altri sistemi. Azure Cosmos DB offre funzionalità aggiuntive che sono state richieste dai clienti, come gli indici secondari e la distribuzione globale. Durante il periodo di anteprima è disponibile un modello ottimizzato per la velocità effettiva ed è prevista infine l'introduzione di un modello ottimizzato per l'archiviazione per soddisfare le esigenze dei clienti. 

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-table-storage-with-the-table-api-preview-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Quando si inseriscono dati nell'archivio tabelle, non viene mai visualizzata una notifica di raggiungimento della quota che indica che la partizione è piena. Con l'API Table (anteprima) viene visualizzato questo messaggio. Questa offerta impone limitazioni e la modifica dell'applicazione esistente?

Azure Cosmos DB è un sistema basato su contratti di servizio che offre scalabilità illimitata, con garanzie di latenza, velocità effettiva, disponibilità e coerenza. Per assicurarsi le prestazioni Premium garantite, verificare che l'indice e le dimensioni dei dati siano gestibili e scalabili. Il limite di 10 GB per il numero di entità o elementi per chiave di partizione ha lo scopo di assicurare prestazioni di ricerca e di query elevate. Per assicurare la scalabilità dell'applicazione anche per Archiviazione di Azure, è consigliabile *non* creare una partizione critica archiviando tutte le informazioni in una partizione ed eseguendo query su di essa. 

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-new-table-api-preview"></a>La chiave di partizione e la chiave di riga sono ancora necessarie con la nuova API Table (anteprima)? 
Sì. Dato che la superficie di attacco dell'API Table (anteprima) è simile a quella di Table Storage SDK, la chiave di partizione offre un modo efficiente per distribuire i dati. La chiave di riga è univoca all'interno della partizione. La chiave di riga deve essere presente e non può essere Null come nell'SDK standard. La chiave di riga è lunga 255 byte, mentre la chiave di partizione ha una lunghezza di 100 byte, che verrà presto aumentata a 1 KB. 

### <a name="what-are-the-error-messages-for-the-table-api-preview"></a>Quali sono i messaggi di errore per l'API Table (anteprima)?
Dato che questa anteprima è compatibile con la tabella standard, la maggior parte degli errori sarà mappata agli errori della tabella standard. 

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api-preview"></a>Perché quando si prova a creare numerose tabelle una dopo l'altra nell'API Table (anteprima) viene applicata la limitazione?
Azure Cosmos DB è un sistema basato su contratti di servizio che offre garanzie di latenza, velocità effettiva, disponibilità e coerenza. Dato che è un sistema con provisioning, riserva risorse per garantire tali requisiti. La rapida frequenza di creazione delle tabelle viene rilevata e limitata. È consigliabile esaminare la frequenza di creazione di tabelle e ridurla a meno di 5 al minuto. Tenere presente che l'API Table (anteprima) è un sistema con provisioning. Il relativo pagamento ha inizio nel momento in cui ne viene effettuato il provisioning. 

## <a name="develop-against-the-graph-api-preview"></a>Sviluppo con l'API Graph (anteprima)
### <a name="how-can-i-apply-the-functionality-of-graph-api-preview-to-azure-cosmos-db"></a>Come si possono applicare le funzionalità dell'API Graph (anteprima) ad Azure Cosmos DB?
Per applicare le funzionalità dell'API Graph (anteprima) è possibile usare una libreria di estensioni, denominata Microsoft Azure Graphs e disponibile in NuGet. 

### <a name="it-looks-like-you-support-the-gremlin-graph-traversal-language-do-you-plan-to-add-more-forms-of-query"></a>È supportato il linguaggio per l'attraversamento di grafi Gremlin. È prevista l'aggiunta di altre forme di query?
Sì. È prevista in futuro l'aggiunta di altri meccanismi di query. 

### <a name="how-can-i-use-the-new-graph-api-preview-offering"></a>Come si può usare la nuova offerta dell'API Graph (anteprima)? 
Per iniziare, vedere la guida introduttiva all'[API Graph](../cosmos-db/create-graph-dotnet.md).

<a id="moving-to-cosmos-db"></a>
## <a name="questions-from-documentdb-customers"></a>Domande di clienti di DocumentDB
### <a name="why-are-you-moving-to-azure-cosmos-db"></a>Perché viene eseguito il passaggio ad Azure Cosmos DB? 

Azure Cosmos DB è il prossimo passo decisivo nell'ambito dei database cloud distribuiti a livello globale su larga scala. I clienti di DocumentDB hanno ora accesso al sistema e alle funzionalità all'avanguardia offerti da Azure Cosmos DB.

Azure Cosmos DB è stato introdotto come "progetto Florence" nel 2010 per far fronte ai problemi critici affrontati dagli sviluppatori nella creazione di applicazioni su larga scala all'interno di Microsoft. Dato che i problemi relativi alla creazione di app distribuite a livello globale non sono stati riscontrati solo da Microsoft, nel 2015 è stata resa disponibile per gli sviluppatori di Azure la prima generazione di questa tecnologia, ovvero Azure DocumentDB. 

Da quel momento sono state aggiunte nuove importanti funzionalità. Il risultato è Azure Cosmos DB. Nell'ambito di questo rilascio, i clienti di DocumentDB, con i relativi dati, diventano in modo automatico e trasparente clienti di Azure Cosmos DB. Queste funzionalità riguardano il motore di database principale, ma anche distribuzione globale, scalabilità elastica e contratti di servizio completi, leader del settore. In particolare, il motore di database di Azure Cosmos DB è stato sviluppato per ottenere un mapping efficiente di tutti i più comuni modelli di dati, sistemi di tipi e API al modello di dati sottostante di Azure Cosmos DB. 

Il risultato di questo miglioramento più evidente per gli sviluppatori è attualmente il nuovo supporto per le [API di archiviazione tabelle](../cosmos-db/table-introduction.md) e [Gremlin](../cosmos-db/graph-introduction.md). Questo, però, è solo l'inizio. È prevista l'aggiunta nel corso del tempo di altre API popolari e modelli di dati più recenti con ulteriori miglioramenti in termini di prestazioni e di archiviazione su scala globale. 

È importante sottolineare che il [dialetto SQL](../documentdb/documentdb-sql-query.md) di DocumentDB è sempre stato solo una delle tante API che possono essere supportate da Azure Cosmos DB sottostante. Per gli sviluppatori che usano un servizio completamente gestito come Azure Cosmos DB, l'unica interfaccia per il servizio sono le API esposte dal servizio. Non cambia nulla per i clienti esistenti di DocumentDB. In Azure Cosmos DB è disponibile esattamente la stessa API SQL offerta da DocumentDB. Ora (e in futuro) è possibile accedere ad altre funzionalità prima non accessibili. 

Un altro risultato evidente dei miglioramenti apportati è l'ampia base per la scalabilità globale ed elastica della velocità effettiva e dello spazio di archiviazione, Sono stati apportati diversi miglioramenti al sottosistema di distribuzione globale. Una delle molte funzionalità per gli sviluppatori è il modello di coerenza del prefisso, che porta a cinque il numero di modelli di coerenza ben definiti. Diverse funzionalità ancora più interessanti verranno rilasciate non appena saranno definitive. 

### <a name="what-do-i-need-to-do-to-ensure-that-my-documentdb-resources-continue-to-run-on-azure-cosmos-db"></a>Che cosa è necessario fare per assicurarsi che le risorse di DocumentDB continuino a essere eseguite in Azure Cosmos DB?

Non è necessario apportare alcuna modifica. Le risorse di DocumentDB sono ora risorse di Azure Cosmos DB e non si sono verificate interruzioni del servizio al momento del passaggio.

### <a name="what-changes-do-i-need-to-make-for-my-app-to-work-with-azure-cosmos-db"></a>Quali modifiche è necessario apportare all'app perché funzioni con Azure Cosmos DB?

Non è necessario apportare alcuna modifica. Classi, spazi dei nomi e nomi di pacchetti NuGet non sono stati modificati. Come sempre, è consigliabile mantenere aggiornati gli SDK per poter sfruttare i vantaggi delle funzionalità e dei miglioramenti più recenti. 

### <a name="whats-changed-in-the-azure-portal"></a>Che cosa è cambiato nel portale di Azure?

DocumentDB non è più visualizzato nel portale come servizio di Azure. Al suo posto si trova una nuova icona di Azure Cosmos DB, come illustrato nell'immagine seguente. Tutte le raccolte sono disponibili come prima ed è ancora possibile ridimensionare la velocità effettiva, modificare i livelli di coerenza e monitorare i contratti di servizio. Sono state migliorate le funzionalità di Esplora dati (anteprima). È ora possibile visualizzare e modificare i documenti, creare ed eseguire query e usare stored procedure, trigger e funzioni definite dall'utente da una sola pagina, come illustrato nell'immagine seguente: 

![Pannello Raccolte di Azure Cosmos DB](./media/faq/cosmos-db-data-explorer.png)

### <a name="are-there-changes-to-pricing"></a>I prezzi sono stati modificati?

No, il costo per eseguire l'app in Azure Cosmos DB è lo stesso di prima.

### <a name="are-there-changes-to-the-slas"></a>I contratti di servizio sono stati modificati?

No, i contratti di servizio per disponibilità, coerenza, latenza e velocità effettiva non sono stati modificati e vengono ancora visualizzati nel portale. Per altre informazioni, vedere [Contratto di Servizio per Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/).
   
![App elenco attività con dati di esempio](./media/faq/azure-cosmosdb-portal-metrics-slas.png)


[azure-portal]: https://portal.azure.com
[query]: documentdb-sql-query.md

