---
title: "Azure CosmosDB: unità richiesta al minuto (UR/m) | Microsoft Docs"
description: "Informazioni su come ridurre i costi utilizzando le unità richiesta al minuto."
services: cosmos-db
documentationcenter: 
author: arnomicrosoft
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/10/2017
ms.author: acomet
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: 0c597fa4afa816f9731edb744ae494b6ef928b9e
ms.contentlocale: it-it
ms.lasthandoff: 06/20/2017


---
# <a name="request-units-per-minute-in-azure-cosmos-db"></a>Unità richiesta al minuto in Azure Cosmos DB

Azure Cosmos DB è progettato per ottenere prestazioni rapide e prevedibili e per eseguire facilmente un ridimensionamento in base allo sviluppo dell'applicazione. È possibile effettuare il provisioning della velocità effettiva in un contenitore Cosmos DB sia con la granularità al secondo che con la granularità al minuto (UR/m). La velocità effettiva con provisioning con granularità al minuto viene usata per gestire gli spike imprevisti nel carico di lavoro che si verificano con la granularità al secondo. 

Questo articolo offre una panoramica di come funziona il provisioning delle unità richiesta al minuto (UR/m). L'obiettivo da raggiungere con il provisioning di UR/m è quello di garantire prestazioni prevedibili con esigenze imprevedibili (soprattutto se è necessario eseguire l'analisi sui dati operativi) e carichi di lavoro di picco. È importante che i clienti possano utilizzare maggiormente la velocità effettiva di cui effettuano il provisioning in modo che possano eseguire rapidamente e senza problemi un ridimensionamento.

Dopo la lettura di questo articolo, si potrà rispondere alle domande seguenti:

* Come funziona un'unità richiesta al minuto?
* Qual è la differenza tra unità richiesta al minuto e unità richiesta al secondo?
* Come effettuare il provisioning di UR/m?
* In quale scenario è opportuno considerare il provisioning di unità richiesta al minuto?
* Come usare le metriche del portale per ottimizzare i costi e le prestazioni?
* Quale tipo di richiesta può utilizzare il budget UR/m?

## <a name="provisioning-request-units-per-minute-rum"></a>Provisioning di unità richiesta al minuto (UR/m)

Quando si effettua il provisioning di Azure Cosmos DB con la granularità al secondo (UR/s), si ha la certezza che la richiesta abbia esito positivo a una bassa latenza se la velocità effettiva non ha superato la capacità di cui è stato effettuato il provisioning entro un secondo. Con le UR/m, la granularità è al minuto con la certezza che la richiesta abbia esito positivo entro un minuto. Rispetto ai sistemi burst, si ha la garanzia che le prestazioni ottenute siano prevedibili e affidabili.

Il funzionamento del provisioning al minuto è semplice:

* Le UR/m vengono fatturate su base oraria e in aggiunta alle UR/s. Per altre informazioni, vedere la [pagina dei prezzi](https://aka.ms/acdbpricing) di Azure Cosmos DB.
* Le UR/m possono essere abilitate a livello di raccolta, tramite SDK (Node.js, Java o .Net) o tramite il portale (includere anche i carichi di lavoro con API MongoDB)
* Quando le UR/m sono abilitate, per ogni 100 UR/s di cui viene effettuato il provisioning, viene effettuato il provisioning anche di 1.000 UR/m (il rapporto è di 10x)
* In un determinato secondo, un'unità richiesta utilizza il provisioning di UR/m solo se durante tale secondo è stato superato il provisioning al secondo
* Al termine del periodo di 60 secondi (UTC), il provisioning al minuto viene ricaricato
* Le UR/m possono essere abilitate solo per le raccolte con un provisioning massimo di 5.000 UR/s per partizione. Se le esigenze di velocità effettiva aumentano e si ha un livello di provisioning per partizione così elevato, si riceverà un messaggio di avviso

Segue un esempio concreto, in cui un cliente può effettuare il provisioning di 10.000 UR/s con 100.000 UR/m, risparmiando il 73% sui costi rispetto al provisioning per picco (a 50.000 UR/sec) in un periodo di 90 secondi in una raccolta con un provisioning di 10.000 UR/s e 100.000 UR/m:

* 1° secondo: il budget per UR/m è impostato su 100.000
* 3° secondo: durante tale secondo il consumo di unità richiesta è stato di 11.010 UR, 1.010 UR oltre il provisioning di UR/s. Vengono quindi dedotte 1.010 UR dal budget per UR/m. 98.990 UR sono disponibili per i successivi 57 secondi nel budget per UR/m
* 29° secondo: durante tale secondo, si è verificato uno spike elevato (più di 4 volte maggiore del provisioning al secondo) e il consumo di unità richiesta è stato di 46.920 UR. 36.920 UR vengono dedotte dal budget per UR/m che è sceso da 92.323 UR (28° secondo) a 55.403 UR (29° secondo)
* 61° secondo: il budget per UR/m viene di nuovo impostato su 100.000 UR.
 
![Grafico che illustra il consumo e il provisioning di Azure Cosmos DB](./media/request-units-per-minute/azure-cosmos-db-request-units-per-minute.png)

## <a name="specifying-request-unit-capacity-with-rum"></a>Specifica della capacità delle unità richiesta con UR/M

Quando si crea una raccolta di Azure Cosmos DB, si specifica il numero di unità richiesta al secondo da riservare per la raccolta. È anche possibile decidere se aggiungere UR al minuto. Questa operazione può essere eseguita tramite il portale o l'SDK. 

### <a name="through-the-portal"></a>Tramite il portale

Per abilitare o disabilitare UR al minuto, è sufficiente un clic durante il provisioning di una raccolta. 

 ![Screenshot che illustra come impostare UR/m nel portale di Azure](./media/request-units-per-minute/azure-cosmos-db-request-unit-per-minute-portal.png)

### <a name="through-the-sdk"></a>Tramite l'SDK
Prima di tutto, è importante notare che UR/m è disponibile solo per gli SDK seguenti:

* .Net 1.14.0
* Java 1.11.0
* Node.js 1.12.0
* Python 2.2.0

Ecco un frammento di codice per la creazione di una raccolta con 3.000 unità richiesta al secondo e 30.000 unità richiesta al minuto usando .NET SDK:

```csharp
// Create a collection with RU/m enabled
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

// Set the throughput to 3,000 request units per second which will give you 30,000 request units per minute as the RU/m budget
await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 3000, OfferEnableRUPerMinuteThroughput = true });
```

Ecco un frammento di codice per la modifica della velocità effettiva di una raccolta fino a 5.000 unità richiesta al secondo senza provisioning di UR al minuto usando .NET SDK:

```csharp
// Get the current offer
Offer offer = client.CreateOfferQuery()
    .Where(r => r.ResourceLink == collection.SelfLink)    
    .AsEnumerable()
    .SingleOrDefault();

// Set the throughput to 5000 request units per second without RU/m enabled (the last parameter to OfferV2 constructor below)
OfferV2 offerV2 = new OfferV2(offer, 5000, false);

// Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offerV2);
```

## <a name="good-fit-scenarios"></a>Scenari ideali

In questa sezione è disponibile una panoramica degli scenari ideali per abilitare le unità richiesta al minuto.

**Ambiente di sviluppo e test:** ideale. Durante la fase di sviluppo, se si testa l'applicazione con carichi di lavoro diversi, UR/m può offrire la flessibilità necessaria. L'[emulatore](local-emulator.md) è invece un ottimo strumento gratuito per testare Azure Cosmos DB. Se tuttavia si preferisce iniziare in un ambiente cloud, UR/m offrirà una flessibilità elevata in termini di prestazioni ad hoc. Sarà possibile dedicare più tempo allo sviluppo, non essendo più necessario preoccuparsi in primo luogo delle prestazioni. È consigliabile iniziare con il provisioning di UR/s minimo e abilitare UR/m.

**Esigenze di granularità al minuto non prevedibili e di picco:** ideale. Risparmio: dal 25 al 75%. Grazie alle UR/m sono stati osservati notevoli miglioramenti per la maggior parte degli scenari di produzione. Se è presente un carico di lavoro IoT in cui si verifica uno spike alcune volte in un minuto, se sono in esecuzione query quando il sistema esegue un inserimento di massa nello stesso momento, sarà necessaria capacità aggiuntiva per gestire le esigenze di picco. È consigliabile ottimizzare le esigenze delle risorse applicando l'approccio graduale indicato più avanti.

 ![Grafico che illustra il consumo di richieste con una granularità di 5 minuti](./media/request-units-per-minute/azure-cosmos-db-request-units-per-minute-consumption.png)
 
 *Figura: benchmark del consumo di UR*

**Massima tranquillità:** ideale. Risparmio: dal 10 al 20%. A volte l'obiettivo è semplicemente quello di lavorare in tranquillità, senza preoccuparsi di potenziali picchi e limitazioni. Questa funzionalità è l'ideale. In questo caso, è consigliabile abilitare RU/m e ridurre leggermente il provisioning al secondo. Questo caso è diverso da quello precedente perché non si cercherà di ottimizzare il provisioning in modo aggressivo. Non si tratta solo di eliminare le limitazioni.

Operazioni critiche con esigenze ad hoc: a volte è consigliabile consentire solo alle operazioni critiche di accedere al budget per le UR/m in modo che il budget non venga utilizzato da operazioni ad hoc o meno importanti. Questa impostazione è facilmente definibile nella sezione seguente.

## <a name="using-the-portal-metrics-to-optimize-cost-and-performance"></a>Uso delle metriche del portale per ottimizzare i costi e le prestazioni

**Nelle prossime settimane, i contenuti sul monitoraggio del consumo al minuto di UR per ottimizzare le esigenze di velocità effettiva verrà integrato e aggiornato.**

Tramite le metriche del portale, è possibile visualizzare la quantità di normali secondi di UR utilizzata rispetto ai minuti di UR. Il monitoraggio di queste metriche consente di ottimizzare il provisioning. 

È consigliabile adottare un approccio graduale all'uso delle UR/m poterne sfruttare meglio i vantaggi. Per ogni passaggio, è consigliabile avere una panoramica del consumo di UR che rappresenti un ciclo completo del carico di lavoro (può trattarsi di giorni, ore o anche settimane) e ottenere informazioni dettagliate sull'utilizzo di ciò di cui viene effettuato il provisioning.

Il principio alla base di questo approccio è quello di far coincidere il più possibile il provisioning della velocità effettiva con un punto di provisioning corrispondente ai criteri delle prestazioni seguenti. 

![Grafico che illustra il consumo di richieste con una granularità di 5 minuti](./media/request-units-per-minute/azure-cosmos-db-request-units-per-minute-adjust-provisioning.png)
 
Per individuare il punto di provisioning ottimale per il carico di lavoro, è necessario conoscere:

* Modelli di consumo: spike prolungati, sporadici o inesistenti? Spike di dimensioni piccole (2 volte la media), medie o grandi (più di 10 volte la media)?
* Percentuale di richieste limitate: la presenza di qualche limitazione è accettabile? Se sì, in quale percentuale? 

Dopo avere identificato gli obiettivi, sarà possibile avvicinarsi al provisioning ottimale.

Per assistere l'utente, vengono date indicazioni generali su come ottimizzare il provisioning in base al consumo di UR/m. Queste indicazioni non si applicano a tutti i tipi di carichi di lavoro, ma si basano sulle informazioni dell'anteprima privata. Tali linee guida potrebbero cambiare nel corso del tempo:

|% utilizzo UR/m|Grado di utilizzo di UR/m|Azioni consigliate per il provisioning|
|---|---|---|
|0-1%|Sottoutilizzo|Ridurre le UR/s per utilizzare più UR/m|
|1-10%|Uso appropriato|Mantenere lo stesso livello di provisioning|
|Più del 10%|Sovrautilizzo|Aumentare le UR/s per ridurre l'uso delle UR/m|

## <a name="select-which-operations-can-consume-the-rum-budget"></a>Selezionare le operazioni che possono utilizzare il budget per le UR/m

A livello di richiesta, è anche possibile abilitare/disabilitare il budget per le UR/m per poter gestire le richieste indipendentemente dal tipo di operazione. Se il normale budget per le UR/sec con provisioning viene esaurito e la richiesta non può utilizzare il budget per le UR/m, la richiesta verrà limitata. Per impostazione predefinita, qualsiasi richiesta viene gestita dal budget per le UR/m se il budget per la velocità effettiva di UR/m è attivato. 

Ecco un frammento di codice per disabilitare il budget per le UR/m usando l'API DocumentDB per le operazioni CRUD e di query.

```csharp
// In order to disable any CRUD request for RU/m, set DisableRUPerMinuteUsage to true in RequestOptions
await client.CreateDocumentAsync(
    UriFactory.CreateDocumentCollectionUri("db", "container"),
    new Document { Id = "Cosmos DB" },
    new RequestOptions { DisableRUPerMinuteUsage = true });
// In order to disable any query request for RU/m, set DisableRUPerMinuteOnRequest to true in RequestOptions
FeedOptions feedOptions = new FeedOptions();
feedOptions.DisableRUPerMinuteUsage = true;
var query = client.CreateDocumentQuery<Book>(
    UriFactory.CreateDocumentCollectionUri("db", "container"),
    "select * from c",feedOptions).AsDocumentQuery();
```

## <a name="next-steps"></a>Passaggi successivi

Questo articolo descrive come funziona il partizionamento in Azure Cosmos DB, come creare raccolte partizionate e come scegliere una chiave di partizione efficace per l'applicazione.

* Eseguire il test delle prestazioni e della scalabilità con Azure Cosmos DB. Per un esempio, vedere [Test delle prestazioni e della scalabilità con Azure Cosmos DB](performance-testing.md).
* Introduzione alla programmazione con gli [SDK](documentdb-sdk-dotnet.md) o l'[API REST](/rest/api/documentdb/).
* Informazioni sulla [velocità effettiva di provisioning in Azure Cosmos DB](request-units.md). 


