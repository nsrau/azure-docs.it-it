---
title: 'Azure Cosmos DB: Introduzione all''API SQL | Microsoft Docs'
description: Informazioni su come usare Azure Cosmos DB per archiviare volumi elevati di documenti JSON ed eseguire query su di essi con bassa latenza usando SQL e JavaScript.
keywords: database JSON, database di documenti
services: cosmos-db
author: rafats
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 686cdd2b-704a-4488-921e-8eefb70d5c63
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/22/2017
ms.author: rafats
ms.openlocfilehash: 28c18be81d184766f7b64199c09620238e47cde4
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2017
---
# <a name="introduction-to-azure-cosmos-db-sql-api"></a>Introduzione ad Azure Cosmos DB: API SQL

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

[Azure Cosmos DB](introduction.md) è il servizio di database multimodello distribuito a livello globale di Microsoft per applicazioni cruciali. Azure Cosmos DB offre [distribuzione globale predefinita](distribute-data-globally.md), [scalabilità elastica in termini di archiviazione e velocità effettiva](partition-data.md) ovunque nel mondo, latenze pari a singole unità di millisecondi al 99° percentile, [cinque livelli di coerenza ben definiti](consistency-levels.md) e disponibilità elevata garantita, il tutto supportato da [contratti di servizio leader del settore](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Azure Cosmos DB [indicizza automaticamente i dati](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) senza che sia necessario gestire manualmente indici e schemi. Si tratta di un database multimodello che supporta modelli di dati di documenti, coppie chiave-valore, grafi e colonne.

![API SQL di Azure](./media/sql-api-introduction/cosmosdb-sql-api.png) 

Con l'API SQL, Azure Cosmos DB offre [funzionalità di query SQL](sql-api-sql-query.md) avanzate e già note con latenze sempre basse sui dati JSON senza schema. Questo articolo offre una panoramica dell'API SQL di Azure Cosmos DB e ne illustra l'uso per l'archiviazione di volumi elevati di documenti JSON, l'esecuzione di query con latenze di millisecondi e la semplice evoluzione dello schema. 

## <a name="what-capabilities-and-key-features-does-azure-cosmos-db-offer"></a>Capacità e funzionalità chiave offerte da Azure Cosmos DB
Di seguito sono riportati i vantaggi e le funzionalità chiave offerti da Azure Cosmos DB tramite l'API SQL:

* **Velocità effettiva e archiviazione con scalabilità elastica:** è possibile aumentare o ridurre le prestazioni del database JSON in base alle esigenze dell'applicazione. I dati sono archiviati in unità SSD (Solid State Drive) per garantire livelli di latenza bassi e prevedibili. Azure Cosmos DB supporta contenitori di archiviazione dei dati JSON, chiamati raccolte, che possono raggiungere dimensioni di archiviazione e velocità effettiva con provisioning quasi illimitate. Azure Cosmos DB offre una semplice scalabilità elastica e prestazioni prevedibili in base alla crescita dell'applicazione. 


* **Replica multiarea:** Azure Cosmos DB replica in modo trasparente i dati in tutte le aree associate all'account Azure Cosmos DB, consentendo lo sviluppo di applicazioni che richiedono l'accesso globale ai dati, con il necessario compromesso tra coerenza, disponibilità e prestazioni, tutto con le garanzie corrispondenti. Azure Cosmos DB fornisce il failover trasparente a livello di area con le API multihosting e la possibilità di aumentare o ridurre la velocità effettiva e le risorse di archiviazione in tutto il mondo. Per altre informazioni, vedere [Distribuire i dati a livello globale con Azure Cosmos DB](distribute-data-globally.md).

* **Query ad hoc con sintassi SQL familiare:** è possibile archiviare documenti JSON eterogenei ed eseguire query su di essi usando una sintassi SQL familiare. Azure Cosmos DB da uso di una tecnologia di indicizzazione a simultaneità elevata, priva di blocchi, con registrazione strutturata per indicizzare automaticamente l'intero contenuto dei documenti. Ciò permette l'esecuzione di query avanzate in tempo reale, senza dovere specificare hint di schema, indici secondari o visualizzazioni. Per altre informazioni, vedere [Query in Azure Cosmos DB](sql-api-sql-query.md). 
* **Esecuzione di JavaScript nel database:** è possibile esprimere la logica dell'applicazione sotto forma di stored procedure, trigger e funzioni definite dall'utente (UDF, User Defined Functions) tramite JavaScript standard. Ciò permette alla logica dell'applicazione di operare sui dati, senza preoccuparsi della mancata corrispondenza tra l'applicazione e lo schema di database. L'API SQL offre l'esecuzione transazionale completa della logica dell'applicazione JavaScript direttamente nel motore di database. La profonda integrazione di JavaScript permette l'esecuzione di operazioni INSERT, REPLACE, DELETE e SELECT da un programma JavaScript come transazione isolata. Per altre informazioni vedere la [programmazione lato server SQL](programming.md).

* **Livelli di coerenza perfezionabili:** è possibile scegliere tra cinque livelli di coerenza ben definiti per ottenere un compromesso ottimale tra coerenza e prestazioni. Per query e operazioni di lettura, Azure Cosmos DB offre cinque livelli di coerenza distinti, ovvero avanzata, con decadimento ristretto, sessione, prefisso coerente e futura. Questi livelli di coerenza granulari e ben definiti permettono di ottenere compromessi efficaci tra coerenza, disponibilità e latenza. Per altre informazioni, vedere [Uso dei livelli di coerenza per ottimizzare la disponibilità e le prestazioni](consistency-levels.md).

* **Completamente gestito:** è possibile eliminare la necessità di gestire risorse di database e computer. Questo servizio Microsoft Azure completamente gestito non richiede la gestione di macchine virtuali, la distribuzione e la configurazione di software, la gestione del ridimensionamento o complessi aggiornamenti di livello dati. Il backup di ogni database è eseguito automaticamente e ogni database è protetto da errori nell'area geografica specifica. La possibilità di aggiungere con facilità account e capacità di provisioning di Azure Cosmos DB in base alla necessità permette di concentrarsi sull'applicazione invece che sul funzionamento e sulla gestione del database. 

* **Progettazione aperta:** è possibile iniziare subito a lavorare usando le competenze e gli strumenti esistenti. La programmazione per l'API SQL è semplice, intuitiva e non richiede l'adozione di nuovi strumenti o l'adesione a estensioni personalizzate per JSON o JavaScript. È possibile accedere a tutte le funzionalità del database, incluso CRUD, l'elaborazione di JavaScript e query in una semplice interfaccia HTTP RESTful. L'API SQL accetta i formati, i linguaggi e gli standard esistenti, offrendo al tempo stesso capacità di database di livello elevato.

* **Indicizzazione automatica:** per impostazione predefinita, Azure Cosmos DB indicizza automaticamente tutti i documenti nel database e non prevede né richiede schemi o la creazione di indici secondari. Se non si vuole indicizzare ogni elemento, è anche possibile [rifiutare esplicitamente i percorsi nei file JSON](indexing-policies.md).

* **Supporto del feed delle modifiche:** il feed delle modifiche offre un elenco di documenti in una raccolta di Azure Cosmos DB, nell'ordine in cui questi sono stati modificati. Questo feed può essere usato per restare in ascolto delle modifiche ai dati per replicare i dati, attivare le chiamate API o eseguire l'elaborazione dei flussi negli aggiornamenti. Il feed delle modifiche viene abilitato automaticamente ed è facile da usare: [altre informazioni sul feed delle modifiche](https://docs.microsoft.com/azure/cosmos-db/change-feed). 

## <a name="data-management"></a>Come gestire i dati con l'API SQL
L'API SQL consente di gestire i dati JSON tramite risorse di database ben definite. Queste risorse sono replicate per ottenere disponibilità elevata e sono indirizzabili in modo univoco da parte dei rispettivi URI logici. L'API SQL offre un semplice modello di programmazione RESTful basato su HTTP per tutte le risorse. 


L'account di database di Azure Cosmos DB è uno spazio dei nomi univoco che permette di accedere ad Azure Cosmos DB. Prima di creare un account di database, è necessario avere una sottoscrizione di Azure, che consente di accedere ai diversi servizi di Azure. 

Tutte le risorse disponibili in Azure Cosmos DB sono modellate e archiviate come documenti JSON. Le risorse sono gestite come elementi, ovvero documenti JSON contenenti metadati, e come feed, ovvero raccolte di elementi. Gli insiemi di elementi sono inclusi nei rispettivi feed.

La figura seguente mostra le relazioni tra le risorse di Azure Cosmos DB:

![Relazione gerarchica tra le risorse in Azure Cosmos DB][1] 

Un account di database è costituito da un insieme di database, ognuno dei quali include più raccolte, che possono contenere stored procedure, trigger, UDF, documenti e allegati correlati. Un database include anche utenti associati, ognuno dei quali ha un insieme di autorizzazioni per accedere alle diverse raccolte, stored procedure, trigger, UDF, documenti o allegati disponibili. Mentre i database, gli utenti, le autorizzazioni e le raccolte sono risorse definite dal sistema con schemi noti, i documenti, le stored procedure, i trigger, le funzioni definite dall'utente e gli allegati includono contenuto JSON arbitrario definito dall'utente.  

## <a name="develop"></a> Come sviluppare app con l'API SQL

Azure Cosmos DB espone le risorse tramite le API REST che possono essere chiamate da qualsiasi linguaggio in grado di inviare richieste HTTP/HTTPS. Sono anche disponibili librerie di programmazione per diversi linguaggi comuni per l'API SQL. Le librerie client semplificano molti aspetti dell'uso dell'API, grazie alla gestione di dettagli quali la memorizzazione nella cache degli indirizzi, la gestione delle eccezioni, i tentativi automatici e così via. Le librerie attualmente disponibili riguardano i linguaggi e le piattaforme seguenti:  

| Scaricare | Documentazione |
| --- | --- |
| [.NET SDK](http://go.microsoft.com/fwlink/?LinkID=402989) |[Libreria .NET](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet) |
| [Node.js SDK](http://go.microsoft.com/fwlink/?LinkID=402990) |[Libreria Node.js](http://azure.github.io/azure-documentdb-node/) |
| [SDK per Java](http://go.microsoft.com/fwlink/?LinkID=402380) |[Libreria Java](/java/api/com.microsoft.azure.documentdb) |
| [JavaScript SDK](http://go.microsoft.com/fwlink/?LinkID=402991) |[Libreria JavaScript](http://azure.github.io/azure-documentdb-js/) |
| n/d |[JavaScript SDK lato server](http://azure.github.io/azure-documentdb-js-server/) |
| [Python SDK](https://pypi.python.org/pypi/pydocumentdb) |[Libreria di Python](http://azure.github.io/azure-documentdb-python/) |
| N/D | [API per MongoDB](mongodb-introduction.md)


Usando l'[emulatore di Azure Cosmos DB](local-emulator.md), è possibile sviluppare e testare l'applicazione in locale con l'API SQL, senza creare una sottoscrizione di Azure né sostenere costi. Quando si è soddisfatti del funzionamento dell'applicazione nell'emulatore, è possibile iniziare a usare l'account Azure Cosmos DB nel cloud.

Oltre alle operazioni di base di creazione, lettura, aggiornamento ed eliminazione, l'API SQL offre un'interfaccia di query SQL avanzata per il recupero di documenti JSON e supporto lato server per l'esecuzione transazionale di logica dell'applicazione JavaScript. Le interfacce di esecuzione di query e script sono disponibili tramite tutte le librerie delle piattaforme, oltre alle API REST. 

### <a name="sql-query"></a>Query SQL
Azure Cosmos DB supporta l'esecuzione di query sui documenti tramite un linguaggio SQL, basato sul sistema di tipo JavaScript, ed espressioni con supporto per query relazionali, gerarchiche e spaziali. Il linguaggio di query di Azure Cosmos DB è un'interfaccia semplice ma avanzata per l'esecuzione di query su documenti JSON. Il linguaggio supporta un sottoinsieme della grammatica SQL ANSI e aggiunge un'integrazione profonda di oggetti JavaScript, matrici, costruzione di oggetti e chiamata di funzioni. L'API SQL offre il modello di query senza schema esplicito o hint di indicizzazioni dallo sviluppatore.

È possibile registrare le funzioni definite dall'utente nell'API SQL e farvi riferimento come parte di una query SQL, estendendo così la grammatica in modo da supportare la logica dell'applicazione personalizzata. Queste funzioni UDF sono scritte come programmi JavaScript e sono eseguite nel database. 

Per gli sviluppatori .NET, l'API SQL [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.aspx) offre anche un provider di query LINQ. 

### <a name="transactions-and-javascript-execution"></a>Transazioni ed esecuzione di JavaScript
L'API SQL permette di scrivere logica dell'applicazione sotto forma di programmi con nome, scritti interamente in JavaScript. Questi programmi sono registrati per una raccolta e possono eseguire di operazioni di database nei documenti all'interno di una raccolta specifica. Il codice JavaScript può essere registrato per l'esecuzione come trigger, stored procedure o funzione UDF. I trigger e le stored procedure possono creare, leggere, aggiornare ed eliminare documenti, mentre le funzioni UDF sono eseguite come parte della logica di esecuzione delle query, senza accesso in scrittura alla raccolta.

L'esecuzione di JavaScript in Cosmos DB è modellata sui concetti supportati dai sistemi di database relazionali, in cui JavaScript rappresenta un sostituto attuale di Transact-SQL. Tutta la logica JavaScript è eseguita in una transazione di ambiente ACID con isolamento dello snapshot. Se JavaScript genera un'eccezione durante l'esecuzione, l'intera transazione sarà interrotta.

## <a name="next-steps"></a>Passaggi successivi
Se si ha già un account di Azure, è possibile iniziare a usare Azure Cosmos DB seguendo le [guide introduttive](../cosmos-db/create-sql-api-dotnet.md), che illustrano come creare un account e iniziare a usare Cosmos DB.

[1]: ./media/sql-api-introduction/json-database-resources1.png

