---
title: Criteri di indicizzazione di Azure Cosmos DB | Microsoft Docs
description: Informazioni sull'indicizzazione in Azure Cosmos DB. Informazioni su come configurare e modificare i criteri di indicizzazione per l'indicizzazione automatica e per ottenere prestazioni migliori.
keywords: funzionamento dell'indicizzazione, indicizzazione automatica, indicizzazione del database
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: monicar
ms.assetid: d5e8f338-605d-4dff-8a61-7505d5fc46d7
ms.service: cosmos-db
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 08/17/2017
ms.author: arramac
ms.openlocfilehash: b09f5323f0378721412baade9be9926ebd0c171e
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="how-does-azure-cosmos-db-index-data"></a>Come vengono indicizzati i dati da Azure Cosmos DB?

Per impostazione predefinita, tutti i dati di Azure Cosmos DB sono indicizzati. Benché per molti clienti sia utile lasciare ad Azure Cosmos DB la gestione automatica di tutti gli aspetti dell'indicizzazione, è possibile specificare *criteri di indicizzazione* personalizzati per le raccolte durante la creazione in Azure Cosmos DB. I criteri di indicizzazione in Azure Cosmos DB sono più flessibili e potenti rispetto agli indici secondari disponibili in altre piattaforme di database. In Azure Cosmos DB è possibile progettare e personalizzare la forma dell'indice senza sacrificare la flessibilità dello schema. 

Per informazioni sul funzionamento dell'indicizzazione in Azure Cosmos DB, è importante ricordare che la gestione dei criteri di indicizzazione permette di raggiungere un buon equilibrio tra overhead di archiviazione dell'indice, velocità effettiva di scrittura e di query e coerenza delle query.  

In questo articolo vengono esaminati i criteri di indicizzazione di Azure Cosmos DB, le modalità di personalizzazione di questi criteri e i compromessi associati. 

Alla fine della lettura, si avranno le risposte alle domande seguenti:

* Come è possibile ignorare le proprietà da includere o escludere dall'indicizzazione?
* Come è possibile configurare l'indice di eventuali aggiornamenti?
* Come si configura l'indicizzazione per eseguire query ORDER BY o di intervallo?
* Come è possibile apportare modifiche ai criteri di indicizzazione di una raccolta?
* Come confrontare archiviazione e prestazioni dei diversi criteri di indicizzazione?

## Personalizzare i criteri di indicizzazione di una raccolta <a id="CustomizingIndexingPolicy"></a>  
È possibile personalizzare alcuni aspetti per ottenere il compromesso desiderato tra archiviazione, prestazioni di scrittura e query e coerenza delle query sostituendo i criteri di indicizzazione predefiniti in una raccolta di Azure Cosmos DB. È possibile configurare gli aspetti seguenti:

* **Includere o escludere documenti e percorsi nell'indice**. È possibile escludere o includere documenti specifici nell'indice quando si inseriscono o sostituiscono i documenti nella raccolta. È anche possibile includere o escludere proprietà JSON specifiche, denominate anche *percorsi*, per l'indicizzazione tra documenti inclusi in un indice. I percorsi includono caratteri jolly.
* **Configurare diversi tipi di indice**. Per ogni percorso incluso è possibile specificare il tipo di indice richiesto dal percorso per una raccolta. È possibile specificare il tipo di indice in base ai dati del percorso, al carico di lavoro delle query previsto e alla "precisione" numerica/di stringa.
* **Configurare le modalità di aggiornamento dell'indice**. Azure Cosmos DB supporta tre modalità di indicizzazione: coerente, differita e nessuna indicizzazione. È possibile configurare le modalità di indicizzazione tramite i criteri di indicizzazione in una raccolta di Azure Cosmos DB. 

Il frammento di codice Microsoft .NET seguente mostra come impostare criteri di indicizzazione personalizzati durante la creazione di una raccolta. In questo esempio i criteri vengono impostati con un indice di intervallo per stringhe e numeri con la precisione massima. È possibile usare questi criteri per eseguire query ORDER BY su stringhe.

    DocumentCollection collection = new DocumentCollection { Id = "myCollection" };

    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), collection);   


> [!NOTE]
> Lo schema JSON per i criteri di indicizzazione è stato modificato con la nuova API REST versione 2015-06-03. Con questa versione, lo schema JSON per i criteri di indicizzazione supporta indici di intervallo di stringhe. .NET SDK 1.2.0 e Java, Python e Node.js SDK 1.1.0 supportano il nuovo schema di criteri. Le versioni precedenti dell'SDK usano l'API REST versione 2015-04-08. Queste versioni supportano lo schema precedente per i criteri di indicizzazione.
> 
> Per impostazione predefinita, Azure Cosmos DB indicizza tutte le proprietà stringa nei documenti in modo coerente con un indice hash. Indicizza tutte le proprietà numeriche nei documenti in modo coerente con un indice di intervallo.  
> 
> 

### <a name="customize-the-indexing-policy-in-the-portal"></a>Personalizzare i criteri di indicizzazione nel portale

È possibile modificare i criteri di indicizzazione di una raccolta nel portale di Azure: 

1. Nel portale passare all'account Azure Cosmos DB e quindi selezionare la raccolta. 
2. Nel menu di spostamento a sinistra scegliere **Impostazioni** e quindi **Criteri di indicizzazione**. 
3. In **Criteri di indicizzazione** modificare i criteri di indicizzazione e quindi selezionare **OK**. 

### Modalità di indicizzazione del database <a id="indexing-modes"></a>  
Azure Cosmos DB supporta tre modalità di indicizzazione che è possibile configurare tramite i criteri di indicizzazione in una raccolta di Azure Cosmos DB: coerente, differita e nessuna indicizzazione.

**Coerente**: se i criteri di una raccolta di Azure Cosmos DB sono coerenti, le query su una determinata raccolta di Azure Cosmos DB seguono lo stesso livello di coerenza specificato per le letture punto (assoluta, con obsolescenza associata, di sessione o finale). L'indice viene aggiornato in modo sincrono come parte dell'aggiornamento del documento, ad esempio come parte di un'operazione di inserimento, sostituzione, aggiornamento ed eliminazione di un documento in una raccolta di Azure Cosmos DB.

L'indicizzazione coerente supporta query coerenti al costo di una possibile riduzione della velocità effettiva di scrittura. Questa riduzione è una funzione dei percorsi univoci che devono essere indicizzati e del "livello di coerenza". La modalità di indicizzazione coerente è progettata per carichi di lavoro "scrivi rapidamente, esegui query immediatamente".

**Differita**: l'indice viene aggiornato in modo asincrono quando una raccolta di Azure Cosmos DB è inattiva, ovvero quando la capacità di velocità effettiva della raccolta non viene usata completamente per gestire le richieste utente. La modalità di indicizzazione differita può essere adatta per carichi di lavoro con inserimento immediato ed esecuzione di query successiva che richiedono l'inserimento del documento. Si potrebbero ottenere risultati incoerenti perché i dati vengono inseriti e indicizzati lentamente. Questo significa che le query COUNT o i risultati di query specifici possono non essere coerenti o ripetibili in determinati momenti. 

L'indice è in genere in modalità di recupero con dati inseriti. Con l'indicizzazione differita, le modifiche di durata (TTL) comportano l'eliminazione e la nuova creazione dell'indice. Questo comportamento rende incoerenti la query COUNT e i risultati delle query per un periodo di tempo. Per questo motivo, la maggior parte degli account Azure Cosmos DB deve usare la modalità di indicizzazione coerente.

**Nessuna indicizzazione**: a una raccolta con modalità nessuna indicizzazione non è associato alcun indice. Questa modalità viene usata in genere se Azure Cosmos DB funge da archivio di coppie chiave/valore e l'accesso ai documenti avviene solo tramite il rispettivo ID proprietà. 

> [!NOTE]
> La configurazione dei criteri di indicizzazione con nessuna indicizzazione ha l'effetto collaterale di eliminare tutti gli indici esistenti. Usare questa modalità se i modelli di accesso richiedono solo un ID o un collegamento automatico.
> 
> 

Nella tabella seguente viene illustrata la coerenza per le query basata sulla modalità di indicizzazione (Coerente e Differita) configurata per la raccolta e il livello di coerenza specificato per la richiesta di query. Questo si applica alle query eseguite tramite qualsiasi interfaccia: API REST, SDK o all'interno di stored procedure e trigger. 

|Consistency|Modalità di indicizzazione: coerente|Modalità di indicizzazione: differita|
|---|---|---|
|Assoluta|Assoluta|Finale|
|Obsolescenza associata|Obsolescenza associata|Finale|
|sessione|sessione|Finale|
|Finale|Finale|Finale|

Azure Cosmos DB restituisce un errore per le query eseguite su raccolte in modalità nessuna indicizzazione. Le query possono comunque essere eseguite come analisi tramite l'intestazione **x-ms-documentdb-enable-scan** esplicita nell'API REST o l'opzione di richiesta **EnableScanInQuery** usando .NET SDK. Alcune funzionalità di query, come ORDER BY, non sono supportate come analisi con **EnableScanInQuery**.

La tabella seguente indica la coerenza per le query in base alla modalità di indicizzazione (coerente, differita e nessuna indicizzazione) quando è specificato **EnableScanInQuery**.

|Consistency|Modalità di indicizzazione: coerente|Modalità di indicizzazione: differita|Modalità di indicizzazione: nessuna|
|---|---|---|---|
|Assoluta|Assoluta|Finale|Assoluta|
|Obsolescenza associata|Obsolescenza associata|Finale|Obsolescenza associata|
|sessione|sessione|Finale|sessione|
|Finale|Finale|Finale|Finale|

L'esempio di codice seguente mostra come creare una raccolta di Azure Cosmos DB usando .NET SDK con indicizzazione coerente in tutti gli inserimenti di documenti.

     // Default collection creates a Hash index for all string fields and a Range index for all numeric    
     // fields. Hash indexes are compact and offer efficient performance for equality queries.

     var collection = new DocumentCollection { Id ="defaultCollection" };

     collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;

     collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("mydb"), collection);


### <a name="index-paths"></a>Percorsi di indice
Azure Cosmos DB modella i documenti JSON e l'indice come alberi. È possibile usare i criteri per i percorsi all'interno dell'albero. All'interno dei documenti è possibile scegliere i percorsi da includere o escludere nell'indicizzazione. Questo permette di migliorare le prestazioni di scrittura e ridurre lo spazio di archiviazione dell'indice per gli scenari in cui i modelli di query sono già noti.

I percorsi di indice iniziano con la radice (/) e terminano in genere con il carattere jolly ? come operatore. Questo operatore indica che per il prefisso esistono più valori possibili. Ad esempio, per usare SELECT * FROM Families F WHERE F.familyName = "Andersen", è necessario includere un percorso di indice per /"familyName"/? nei criteri di indicizzazione della raccolta.

I percorsi di indice possono anche usare il carattere jolly \* per specificare un comportamento ricorsivo al di sotto del prefisso. È ad esempio possibile usare /payload/* per escludere dall'indicizzazione qualsiasi elemento al di sotto della proprietà payload.

Di seguito sono indicati i modelli comuni per la definizione di percorsi di indice:

| path                | Descrizione/Caso d'uso                                                                                                                                                                                                                                                                                         |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| /                   | Percorso predefinito per la raccolta. Ricorsivo e applicabile all'intero albero del documento.                                                                                                                                                                                                                                   |
| /prop/?             | Percorso di indice necessario per gestire query come la seguente (rispettivamente con tipi hash e di intervallo):<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop > 5<br><br>SELECT FROM collection c ORDER BY c.prop                                                                       |
| /prop/*             | Percorso di indice per tutti i percorsi al di sotto dell'etichetta specificata. Funziona con le query seguenti<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop.subprop > 5<br><br>SELECT FROM collection c WHERE c.prop.subprop.nextprop = "value"<br><br>SELECT FROM collection c ORDER BY c.prop         |
| /props/[]/?         | Percorso di indice che consente di servire iterazioni e query JOIN su matrici di valori scalari, come ["a", "b", "c"]:<br><br>SELECT tag FROM tag IN collection.props WHERE tag = "value"<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag > 5                                                                         |
| /props/[]/subprop/? | Percorso di indice che consente di servire iterazioni e query JOIN su matrici di oggetti, come [{subprop: "a"}, {subprop: "b"}]:<br><br>SELECT tag FROM tag IN collection.props WHERE tag.subprop = "value"<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag.subprop = "value"                                  |
| /prop/subprop/?     | Percorso di indice necessario per gestire le query (rispettivamente con tipi hash e di intervallo):<br><br>SELECT FROM collection c WHERE c.prop.subprop = "value"<br><br>SELECT FROM collection c WHERE c.prop.subprop > 5                                                                                                                    |

> [!NOTE]
> Quando si impostano percorsi di indice personalizzati, è necessario specificare la regola di indicizzazione predefinita per l'intero albero del documento, indicato dal percorso speciale "/*". 
> 
> 

L'esempio seguente configura un percorso specifico con indice di intervallo e un valore di precisione personalizzato pari a 20 byte:

    var collection = new DocumentCollection { Id = "rangeSinglePathCollection" };    

    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/Title/?", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = 20 } } 
            });

    // Default for everything else
    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*" ,
            Indexes = new Collection<Index> {
                new HashIndex(DataType.String) { Precision = 3 }, 
                new RangeIndex(DataType.Number) { Precision = -1 } 
            }
        });

    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), pathRange);


### <a name="index-data-types-kinds-and-precisions"></a>Tipi di dati, tipologie e valori di precisione degli indici
Per configurare i criteri di indicizzazione per un percorso, sono disponibili più opzioni. È possibile specificare una o più definizioni di indicizzazione per ogni percorso:

* **Tipo di dati**: stringa, numero, Point, Polygon o LineString (può contenere solo una voce per tipo di dati per percorso).
* **Tipo di indice**: hash (query di uguaglianza), intervallo (query di uguaglianza, di intervallo o ORDER BY) o spaziale (query spaziali).
* **Precisione**: per un indice hash, varia da 1 a 8 per stringhe e numeri. Il valore predefinito è 3. Per un indice di intervallo, questo valore può essere -1 (precisione massima). Può variare tra 1 e 100 (precisione massima) per i valori stringa o numerici.

#### <a name="index-kind"></a>Tipologia di indice
Azure Cosmos DB supporta i tipi di indice hash e di intervallo per ogni percorso che può essere configurato per tipi di dati stringa o numerici o per entrambi.

* **Hash** supporta query di uguaglianza efficienti e query JOIN. Nella maggior parte dei casi, gli indici hash non richiedono una precisione superiore al valore predefinito di 3 byte. I dati possono essere di tipo stringa o numerico.
* **Intervallo** supporta query di uguaglianza efficienti, query di intervallo (con >, <, >=, <=, !=) e query ORDER BY. Per impostazione predefinita, anche le query ORDER BY richiedono la precisione di indice massima (-1). I dati possono essere di tipo stringa o numerico.

Azure Cosmos DB supporta anche il tipo di indice spaziale per ogni percorso che può essere specificato per il tipo di dati Point, Polygon o LineString. Il valore nel percorso specificato deve essere un frammento GeoJSON valido come `{"type": "Point", "coordinates": [0.0, 10.0]}`.

* **Spatial** supporta query spaziali (within e distance) efficienti. Il tipo di dati può essere Point, Polygon o LineString.

> [!NOTE]
> Azure Cosmos DB supporta l'indicizzazione automatica dei tipi di dati Point, Polygon e LineString.
> 
> 

Di seguito sono elencati i tipi di indice supportati e gli esempi di query che possono essere utilizzati per rispondere:

| Tipologia di indice | Descrizione/Caso d'uso                                                                                                                                                                                                                                                                                                                                                                                                              |
| ---------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Hash       | Hash over /prop/? (or /) può essere usato per servire in modo efficiente le query seguenti:<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>Hash over /props/[]/? (or / or /props/) può essere usato per servire in modo efficiente le query seguenti:<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag = 5                                                                                                                       |
| Range      | Range over /prop/? (or /) può essere usato per servire in modo efficiente le query seguenti:<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop > 5<br><br>SELECT FROM collection c ORDER BY c.prop                                                                                                                                                                                                              |
| Spatial     | Range over /prop/? (or /) può essere usato per servire in modo efficiente le query seguenti:<br><br>SELECT FROM collection c<br><br>WHERE ST_DISTANCE(c.prop, {"type": "Point", "coordinates": [0.0, 10.0]}) < 40<br><br>SELECT FROM collection c WHERE ST_WITHIN(c.prop, {"type": "Polygon", ... }) --con indicizzazione su punti abilitata<br><br>SELECT FROM collection c WHERE ST_WITHIN({"type": "Point", ... }, c.prop) --con indicizzazione su poligoni abilitata              |

Per impostazione predefinita, viene restituito un errore per le query con operatori di intervallo, ad esempio >=, se non esiste alcun indice di intervallo (di qualsiasi precisione) per segnalare che potrebbe essere necessaria un'analisi per gestire la query. Le query di intervallo possono essere eseguite senza un indice di intervallo usando l'intestazione **x-ms-documentdb-enable-scan** nell'API REST o l'opzione di richiesta **EnableScanInQuery** usando .NET SDK. Se la query include altri filtri in base ai quali Azure Cosmos DB può usare l'indice per filtrare, non viene restituito alcun errore.

Le stesse regole sono valide per le query spaziali. Per impostazione predefinita, viene restituito un errore per le query spaziali se non esiste alcun indice spaziale e non sono presenti altri filtri che possono essere serviti dall'indice. Queste query possono essere eseguite come analisi usando **x-ms-documentdb-enable-scan** o **EnableScanInQuery**.

#### <a name="index-precision"></a>Precisione indice
È possibile usare la precisione dell'indice per ottenere un buon compromesso tra l'overhead di archiviazione dell'indice e le prestazioni delle query. Per i numeri, è consigliabile usare la configurazione di precisione predefinita -1 (precisione massima). Poiché i numeri sono a 8 byte in JSON, questo valore equivale a una configurazione di 8 byte. Selezionando un valore inferiore per la precisione, ad esempio da 1 a 7, i valori compresi in alcuni intervalli vengono associati alla stessa voce di indice. Per questo motivo, viene ridotto lo spazio di archiviazione dell'indice, ma l'esecuzione delle query potrebbe dover elaborare più documenti. Di conseguenza, viene utilizzata una velocità effettiva maggiore nelle unità richiesta.

La configurazione di precisione dell'indice ha un'applicazione più pratica con gli intervalli di stringhe. Poiché le stringhe possono avere qualsiasi lunghezza arbitraria, la scelta della precisione dell'indice può influire sulle prestazioni delle query di intervallo di stringhe. Può anche influire sulla quantità di spazio di archiviazione dell'indice necessaria. Gli indici di intervallo di stringhe possono essere configurati con valori compresi tra 1 e 100 o pari a -1 (precisione massima). Se si vuole eseguire query ORDER BY su proprietà stringa, è necessario specificare una precisione pari a -1 per i percorsi corrispondenti.

Gli indici spaziali usano sempre la precisione dell'indice predefinita per tutti i tipi (Point, LineString e Polygon). Non è possibile eseguire l'override della precisione dell'indice predefinita per indici spaziali. 

L'esempio seguente mostra come aumentare la precisione per gli indici di intervallo in una raccolta tramite .NET SDK. 

**Creare una raccolta con una precisione di indice personalizzata**

    var rangeDefault = new DocumentCollection { Id = "rangeCollection" };

    // Override the default policy for strings to Range indexing and "max" (-1) precision
    rangeDefault.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), rangeDefault);   


> [!NOTE]
> Azure Cosmos DB restituisce un errore quando una query usa ORDER BY ma non ha un indice di intervallo sul percorso sottoposto a query con la precisione massima. 
> 
> 

Analogamente, è possibile escludere completamente i percorsi dall'indicizzazione. L'esempio seguente mostra come escludere un'intera sezione dei documenti (*sottoalbero*) dall'indicizzazione usando il carattere jolly \* come operatore.

    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*" });

    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);



## <a name="opt-in-and-opt-out-of-indexing"></a>Consenso o rifiuto esplicito dell'indicizzazione
È possibile specificare se la raccolta deve indicizzare automaticamente tutti i documenti. Per impostazione predefinita, tutti i documenti vengono indicizzati automaticamente, ma è possibile disattivare l'indicizzazione automatica. Quando l'indicizzazione è disattivata, i documenti sono accessibili solo tramite i rispettivi collegamenti automatici o tramite query usando l'ID documento.

Con l'indicizzazione automatica disattivata, è comunque possibile aggiungere in modo selettivo solo documenti specifici all'indice. Al contrario, è possibile lasciare attiva l'indicizzazione automatica e scegliere di escludere selettivamente documenti specifici. Le configurazioni di attivazione o disattivazione dell'indicizzazione sono utili in presenza di un solo subset di documenti su cui eseguire query.

L'esempio seguente mostra come includere in modo esplicito un documento usando [.NET SDK per l'API SQL](https://docs.microsoft.com/azure/cosmos-db/sql-api-sdk-dotnet) e la proprietà [RequestOptions.IndexingDirective](http://msdn.microsoft.com/library/microsoft.azure.documents.client.requestoptions.indexingdirective.aspx).

    // If you want to override the default collection behavior to either
    // exclude (or include) a document in indexing,
    // use the RequestOptions.IndexingDirective property.
    client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"),
        new { id = "AndersenFamily", isRegistered = true },
        new RequestOptions { IndexingDirective = IndexingDirective.Include });

## <a name="modify-the-indexing-policy-of-a-collection"></a>Modificare i criteri di indicizzazione di una raccolta
In Azure Cosmos DB è possibile apportare modifiche in tempo reale ai criteri di indicizzazione di una raccolta. Una modifica dei criteri di indicizzazione in una raccolta di Azure Cosmos DB può comportare una modifica nella forma dell'indice. La modifica influisce sui percorsi che possono essere indicizzati, sulla loro precisione e sul modello di coerenza dell'indice stesso. Una modifica nei criteri di indicizzazione richiede una trasformazione dell'indice precedente in uno nuovo. 

**Trasformazioni degli indici online**

![Funzionamento dell'indicizzazione - Trasformazioni di indici online di Azure Cosmos DB](./media/indexing-policies/index-transformations.png)

Le trasformazioni degli indici vengono eseguite online. Questo significa che i documenti indicizzati in base ai vecchi criteri vengono trasformati in modo efficiente in base ai nuovi criteri *senza modificare la disponibilità di scrittura o la velocità effettiva di provisioning* della raccolta. La coerenza delle operazioni di lettura e scrittura eseguite tramite l'API REST, gli SDK o all'interno di stored procedure e trigger non cambia durante la trasformazione dell'indice. Quando si apporta una modifica ai criteri di indicizzazione, non si verificano cali di prestazioni o tempo di inattività per le app.

Tuttavia, durante la fase di trasformazione dell’indice, le query sono alla fine coerenti indipendentemente dalla configurazione della modalità indicizzazione (coerente o differita). Questo si applica alle query eseguite usando qualsiasi interfaccia: SDK, API REST o all'interno di stored procedure e trigger. Come per l'indicizzazione differita, la trasformazione dell'indice viene eseguita in modo asincrono in background nelle repliche usando le risorse di riserva disponibili per una replica specifica. 

Le trasformazioni degli indici vengono anche eseguite sul posto. Azure Cosmos DB non conserva due copie dell'indice e scambia l'indice precedente con quello nuovo. Questo significa che non è necessario né viene usato ulteriore spazio su disco nelle raccolte durante la trasformazione dell'indice.

Quando si modificano i criteri di indicizzazione, vengono applicate modifiche per passare dall'indice precedente a quello nuovo prevalentemente in base alle configurazioni delle modalità di indicizzazione. Le configurazioni delle modalità di indicizzazione svolgono un ruolo più importante rispetto ad altri valori come i percorsi inclusi/esclusi, i tipi di indice e i valori di precisione. 

Se i criteri precedenti e quelli nuovi usano entrambi l'indicizzazione coerente, Azure Cosmos DB esegue una trasformazione dell'indice online. Non è possibile applicare un'altra modifica ai criteri di indicizzazione in modalità di indicizzazione coerente mentre è in corso la trasformazione. È tuttavia possibile passare all'indicizzazione differita o a nessuna indicizzazione mentre è in corso una trasformazione: 

* Quando si passa alla modalità differita, la modifica ai criteri di indicizzazione ha effetto immediatamente. Azure Cosmos DB inizia a ricreare l'indice in modo asincrono. 
* Quando si passa alla modalità nessuna indicizzazione, l'indice viene eliminato immediatamente. Il passaggio a nessuna indicizzazione è utile quando si vuole annullare una trasformazione in corso e iniziare da capo con criteri di indicizzazione diversi. 

Il frammento di codice seguente mostra come modificare i criteri di indicizzazione di una raccolta per passare dalla modalità di indicizzazione coerente a quella differita. Se si usa .NET SDK, è possibile avviare una modifica dei criteri di indicizzazione usando il nuovo metodo **ReplaceDocumentCollectionAsync**.

**Modificare i criteri di indicizzazione per passare dalla modalità di indicizzazione coerente a quella di indicizzazione differita**

    // Switch to Lazy indexing mode.
    Console.WriteLine("Changing from Default to Lazy IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.Lazy;

    await client.ReplaceDocumentCollectionAsync(collection);

**Tenere traccia dell'avanzamento di una trasformazione dell'indice**

È possibile tenere traccia della percentuale di avanzamento della trasformazione dell'indice in indice coerente usando la proprietà di risposta **IndexTransformationProgress** da una chiamata **ReadDocumentCollectionAsync**. Altri SDK e l'API REST supportano metodi e proprietà equivalenti per apportare modifiche ai criteri di indicizzazione. È possibile controllare lo stato di avanzamento di una trasformazione dell'indice in indice coerente chiamando **ReadDocumentCollectionAsync**: 

    long smallWaitTimeMilliseconds = 1000;
    long progress = 0;

    while (progress < 100)
    {
        ResourceResponse<DocumentCollection> collectionReadResponse = await client.ReadDocumentCollectionAsync(
            UriFactory.CreateDocumentCollectionUri("db", "coll"));

        progress = collectionReadResponse.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromMilliseconds(smallWaitTimeMilliseconds));
    }

> [!NOTE]
> * La proprietà **IndexTransformationProgress** è applicabile solo quando l'indice viene trasformato in indice coerente. Usare la proprietà **ResourceResponse.LazyIndexingProgress** per tenere traccia delle trasformazioni in indice differito.
> * Le proprietà **IndexTransformationProgress** e **LazyIndexingProgress** vengono popolate solo per una raccolta non partizionata, ovvero una raccolta creata senza una chiave di partizione.
>

È possibile eliminare l'indice per una raccolta passando alla modalità di indicizzazione nessuna. Può rivelarsi uno strumento operativo utile se si vuole annullare una trasformazione in corso e avviarne immediatamente una nuova.

**Eliminare l'indice per una raccolta**

    // Switch to Lazy indexing mode.
    Console.WriteLine("Dropping index by changing to to the None IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.None;

    await client.ReplaceDocumentCollectionAsync(collection);

Quando è necessario apportare modifiche ai criteri di indicizzazione per le raccolte di Azure Cosmos DB? I seguenti casi rappresentano la maggior parte dei casi di utilizzo:

* Restituire risultati coerenti durante il normale funzionamento, ma tornare alla modalità di indicizzazione differita durante le importazioni di dati in blocco.
* Iniziare a usare nuove funzionalità di indicizzazione nelle raccolte di Azure Cosmos DB correnti. Ad esempio, è possibile usare query geospaziali, che richiedono il tipo di indice spaziale, oppure query di intervallo di stringhe/ORDER BY, che richiedono il tipo di indice di intervallo di stringhe.
* Selezionare a mano le proprietà da indicizzare e modificarle nel tempo.
* Ottimizzare la precisione di indicizzazione per migliorare le prestazioni delle query o ridurre l'archiviazione utilizzata.

> [!NOTE]
> Per modificare i criteri di indicizzazione tramite **ReplaceDocumentCollectionAsync**, è necessario usare .NET SDK versione 1.3.0 o successive.
> 
> Perché la trasformazione dell'indice venga completata correttamente, assicurarsi che vi sia sufficiente spazio di archiviazione disponibile nella raccolta. Se la raccolta raggiunge la quota di archiviazione, la trasformazione dell'indice viene sospesa. La trasformazione dell'indice viene ripresa automaticamente quando è disponibile spazio di archiviazione, ad esempio se si eliminano alcuni documenti.
> 
> 

## <a name="performance-tuning"></a>Ottimizzazione delle prestazioni
Le API SQL forniscono informazioni sulle metriche delle prestazioni, ad esempio lo spazio di archiviazione usato per gli indici e il costo della velocità effettiva (unità richiesta) per ogni operazione. Queste informazioni possono essere usate per confrontare diversi criteri di indicizzazione e per l'ottimizzazione delle prestazioni.

Per controllare la quota di archiviazione e l'utilizzo di una raccolta, eseguire una richiesta **HEAD** o **GET** sulla risorsa della raccolta. Esaminare quindi le intestazioni **x-ms-request-quota** e **x-ms-request-usage**. In .NET SDK le proprietà [DocumentSizeQuota](http://msdn.microsoft.com/library/dn850325.aspx) e [DocumentSizeUsage](http://msdn.microsoft.com/library/azure/dn850324.aspx) di [ResourceResponse<T\>](http://msdn.microsoft.com/library/dn799209.aspx) contengono questi valori corrispondenti.

     // Measure the document size usage (which includes the index size) against   
     // different policies.
     ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));  
     Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);


Per valutare l'overhead di indicizzazione di ogni operazione di scrittura (creazione, aggiornamento o eliminazione), esaminare l'intestazione **x-ms-request-charge** o la proprietà equivalente [RequestCharge](http://msdn.microsoft.com/library/dn799099.aspx) in [ResourceResponse<T\>](http://msdn.microsoft.com/library/dn799209.aspx) in .NET SDK per determinare il numero di unità richiesta usate da queste operazioni.

     // Measure the performance (request units) of writes.     
     ResourceResponse<Document> response = await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), myDocument);              
     Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);

     // Measure the performance (request units) of queries.    
     IDocumentQuery<dynamic> queryable =  client.CreateDocumentQuery(UriFactory.CreateDocumentCollectionUri("db", "coll"), queryString).AsDocumentQuery();

     double totalRequestCharge = 0;
     while (queryable.HasMoreResults)
     {
        FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
        Console.WriteLine("Query batch consumed {0} request units",queryResponse.RequestCharge);
        totalRequestCharge += queryResponse.RequestCharge;
     }

     Console.WriteLine("Query consumed {0} request units in total", totalRequestCharge);

## <a name="changes-to-the-indexing-policy-specification"></a>Modifiche per la specifica di criteri di indicizzazione
Il 7 luglio 2015 è stata introdotta una modifica nello schema per i criteri di indicizzazione con l'API REST versione 2015-06-03. Le relative classi nelle versioni SDK hanno nuove implementazioni per garantire la corrispondenza con lo schema. 

Nella specifica JSON sono state implementate le modifiche seguenti:

* I criteri di indicizzazione supportano gli indici di intervallo per le stringhe.
* Ogni percorso può avere più definizioni di indice. Può averne una per ogni tipo di dati.
* La precisione di indicizzazione supporta i valori compresi tra 1 e 8 per i numeri, tra 1 e 100 per le stringhe e -1 (precisione massima).
* I segmenti di percorso non richiedono le virgolette doppie per l'escape di ogni percorso. Ad esempio, è possibile aggiungere un percorso per **/title/?** invece di **/"title"/?**.
* Il percorso radice che rappresenta "tutti i percorsi" può essere rappresentato come **/\*** (oltre a **/**).

Se si dispone di codice per il provisioning delle raccolte con criteri di indicizzazione personalizzati scritto con .NET SDK versione 1.1.0 o precedente, per poter passare all'SDK versione 1.2.0 sarà necessario modificare il codice dell'applicazione per gestire queste modifiche. Se non si dispone di codice per la configurazione di criteri di indicizzazione o si prevede di continuare a usare una versione precedente dell'SDK, non è richiesta alcuna modifica.

Per un confronto pratico, ecco un esempio di un criterio di indicizzazione personalizzato scritto tramite l'API REST versione 2015-06-03, seguito dallo stesso criterio di indicizzazione scritto con la precedente API REST versione 2015-04-08.

**Codice JSON del criterio di indicizzazione corrente (API REST versione 2015-06-03)**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "includedPaths":[
          {
             "path":"/*",
             "indexes":[
                {
                   "kind":"Hash",
                   "dataType":"String",
                   "precision":3
                },
                {
                   "kind":"Hash",
                   "dataType":"Number",
                   "precision":7
                }
             ]
          }
       ],
       "ExcludedPaths":[
          {
             "path":"/nonIndexedContent/*"
          }
       ]
    }


**Codice JSON del criterio di indicizzazione precedente (API REST versione 2015-04-08)**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "IncludedPaths":[
          {
             "IndexType":"Hash",
             "Path":"/",
             "NumericPrecision":7,
             "StringPrecision":3
          }
       ],
       "ExcludedPaths":[
          "/\"nonIndexedContent\"/*"
       ]
    }


## <a name="next-steps"></a>Passaggi successivi
Per alcuni esempi di gestione dei criteri di indicizzazione e per informazioni sul linguaggio di query di Azure Cosmos DB, vedere i collegamenti seguenti:

* [Esempi di codice di gestione degli indici con .NET per l'API SQL](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/IndexManagement/Program.cs)
* [SQL API REST collection operations](https://msdn.microsoft.com/library/azure/dn782195.aspx) (Operazioni di raccolta dell'API SQL tramite REST)
* [Query con SQL](sql-api-sql-query.md)

