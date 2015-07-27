<properties 
    pageTitle="Criteri di indicizzazione di DocumentDB | Azure" 
    description="Comprendere il funzionamento dell'indicizzazione in DocumentDB e imparare a configurare i criteri di indicizzazione." 
    services="documentdb" 
    documentationCenter="" 
    authors="mimig1" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="documentdb" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="07/06/2015" 
    ms.author="mimig"/>


# Criteri di indicizzazione di DocumentDB

DocumentDB è un database effettivamente privo di schema. Non presuppone né richiede schemi per i documenti JSON che indicizza. Questa caratteristica consente di definire rapidamente modelli di dati di applicazioni ed eseguirne l'iterazione. DocumentDB indicizza automaticamente tutte le proprietà dei documenti aggiunti a una raccolta in modo da renderle disponibili per l'esecuzione di query. L'indicizzazione automatica permette anche di archiviare tipi di documenti eterogenei.

L'indicizzazione automatica dei documenti è consentita da tecniche di manutenzione degli indici ottimizzate per la scrittura, prive di blocchi e strutturate con log. DocumentDB supporta un volume consistente di scritture veloci e al tempo stesso l'esecuzione di query coerenti.

Il sottosistema di indicizzazione di DocumentDB è progettato per supportare:

-  Query efficienti, altamente gerarchiche e relazionali senza definizioni di schema o indice.
-  Risultati di query coerenti durante la gestione di un volume sostenuto delle operazioni di scrittura. Per i carichi di lavoro elevati di velocità effettiva della scrittura con query coerenti, l'indice viene aggiornato in maniera incrementale, efficiente e online, gestendo al tempo stesso un volume elevato di scritture.
- Efficienza di archiviazione. Per conseguire l'efficienza dei costi, le risorse di archiviazione su disco dell'indice sono vincolate e prevedibili.
- Multi-tenancy. Gli aggiornamenti dell'indice vengono eseguiti entro i limiti delle risorse di sistema allocate per la raccolta di DocumentDB. 

Per la maggior parte delle applicazioni, è possibile usare i criteri di indicizzazione automatica predefiniti poiché consentono la massima flessibilità e compromessi efficaci tra prestazioni ed efficienza di archiviazione. Tuttavia, la definizione di criteri di indicizzazione personalizzati consente di ottenere compromessi più ragionati tra prestazioni delle query, prestazioni di scrittura e costi di archiviazione dell'indice.

Ad esempio, se si escludono dall'indicizzazione determinati documenti o percorsi all'interno di documenti, è possibile ridurre sia lo spazio di archiviazione usato per l'indicizzazione sia i tempi di inserimento per la manutenzione dell'indice. È possibile cambiare il tipo di indice in modo che risulti più adatto alle query di intervallo oppure aumentare la precisione dell'indice in byte per migliorare le prestazioni delle query. Questo articolo presenta le varie opzioni di configurazione dell'indicizzazione disponibili in DocumentDB e descrive come personalizzare i criteri di indicizzazione per i carichi di lavoro.

Dopo la lettura di questo articolo, si potrà rispondere alle domande seguenti:

- In che modo DocumentDB supporta l'indicizzazione di tutte le proprietà per impostazione predefinita?
- Come è possibile ignorare le proprietà da includere o escludere dall'indicizzazione?
- Come è possibile configurare l'indice di eventuali aggiornamenti?
- Come è possibile configurare l'indicizzazione per eseguire query Order By o intervallo?


## Come funziona l'indicizzazione di DocumentDB

L'indicizzazione di DocumentDB sfrutta il fatto che la grammatica JSON consente la **rappresentazione dei documenti come strutture ad albero**. Per rappresentare un documento JSON come struttura ad albero, è necessario creare un nodo principale fittizio come elemento padre per il resto dei nodi sottostanti nel documento. Ogni etichetta che include gli indici di matrice in un documento JSON diventa un nodo dell'albero. La figura seguente illustra un documento JSON di esempio e la rappresentazione ad albero corrispondente.

![Criteri di indicizzazione](media/documentdb-indexing-policies/image001.png)

Ad esempio, la proprietà JSON {"headquarters": "Belgium"} nell'esempio precedente corrisponde al percorso /"headquarters"/"Belgium". La matrice JSON {"exports": [{"city": “Moscow"}, {"city": Athens"}]} corrisponde ai percorsi /"exports"/0/"city"/"Moscow" e /"exports"/1/"city"/"Athens".

>[AZURE.NOTE]La rappresentazione di percorso attenua il confine tra struttura/schema e valori di istanza nei documenti, consentendo a DocumentDB di essere effettivamente privo di schema.

In DocumentDB i documenti sono organizzati in raccolte su cui è possibile eseguire query SQL o che è possibile elaborare nell'ambito di una singola transazione. Ogni raccolta può essere configurata con specifici criteri di indicizzazione espressi in termini di percorsi. Nella sezione seguente si esaminerà come configurare il comportamento dell'indicizzazione di una raccolta di DocumentDB.

## Configurazione dei criteri di indicizzazione di una raccolta

L'esempio seguente mostra come impostare criteri di indicizzazione personalizzati durante la creazione di una raccolta usando l'API REST di DocumentDB. L'esempio mostra i criteri di indicizzazione espressi in termini di percorsi, tipi di indice e precisioni.

    POST https://<REST URI>/colls HTTP/1.1
    Accept: application/json 

    {
       "id":"customIndexCollection",
       "indexingPolicy":{
          "automatic":true,
          "indexingMode":"Consistent",
          "includedPaths":[
             {
                "path":"/*",
                "indexes":[
    
                ]
             }
          ],
          "excludedPaths":[
             {
                "path":"/nonIndexedContent/*"
             }
          ]
       }
    }
     ...


     HTTP/1.1 201 Created

>[AZURE.NOTE]Lo schema JSON per il criterio di indicizzazione è stato modificato con la versione dell'API REST versione 2015-06-03 per supportare gli indici dell'intervallo stringhe. .NET SDK 1.2.0 e Java, Python e Node. js SDK 1.1.0 supportano il nuovo schema di criteri. SDK precedenti utilizzano l'API REST versione 2015-04-08 e supportano lo schema precedente dei criteri di indicizzazione.
>
>I criteri di indicizzazione di una raccolta devono essere specificati al momento della creazione. La modifica di tali criteri dopo la creazione della raccolta non è attualmente consentita, ma sarà supportata in una futura versione di DocumentDB.
>
>Per impostazione predefinita, DocumentDB indicizza tutti i percorsi nei documenti in maniera coerente con un indice Hash. Il percorso di Timestamp (\\_ts) interno viene archiviato con un indice Range.

### Indicizzazione automatica

È possibile specificare se la raccolta deve indicizzare automaticamente tutti i documenti. Per impostazione predefinita, tutti i documenti vengono indicizzati automaticamente, ma è possibile scegliere di disattivare tale funzionalità. Quando l'indicizzazione è disattivata, i documenti sono accessibili solo tramite i relativi self link o le query con ID.

Con l'indicizzazione automatica disattivata, è comunque possibile aggiungere in modo selettivo solo documenti specifici all'indice. In alternativa, è possibile lasciare attivata l'indicizzazione automatica e scegliere di escludere in modo selettivo solo documenti specifici. Le configurazioni di attivazione o disattivazione dell'indicizzazione sono utili quando si ha un solo subset di documenti su cui eseguire query.

È possibile configurare i criteri predefiniti impostando su true o false il valore per la proprietà automatica. Per ignorare un singolo documento, è possibile impostare l'intestazione della richiesta x-ms-indexingdirective durante l'inserimento o la sostituzione di un documento.

L'esempio seguente mostra come includere un documento in modo esplicito usando [DocumentDB .NET SDK](https://github.com/Azure/azure-documentdb-java) e la proprietà [RequestOptions.IndexingDirective](http://msdn.microsoft.com/library/microsoft.azure.documents.client.requestoptions.indexingdirective.aspx).

    // If you want to override the default collection behavior to either
    // exclude (or include) a Document from indexing,
    // use the RequestOptions.IndexingDirective property.
    client.CreateDocumentAsync(defaultCollection.SelfLink,
        new { id = "AndersenFamily", isRegistered = true },
        new RequestOptions { IndexingDirective = IndexingDirective.Include });
        


### Modalità di indicizzazione

È possibile scegliere tra aggiornamenti dell'indice sincroni (**coerenti**) e asincroni (**differiti**). Per impostazione predefinita, l'indice viene aggiornato in modo sincrono in ogni azione di inserimento, sostituzione o eliminazione eseguita su un documento della raccolta. Questo consente alle query di rispettare lo stesso livello di coerenza delle letture di documenti, senza che l'indice debba recuperare alcun ritardo.

Benché DocumentDB sia ottimizzato per la scrittura e supporti volumi elevati di scritture di documenti, oltre a offrire la manutenzione sincrona dell'indice, è possibile configurare determinate raccolte per l'aggiornamento differito dell'indice. L'indicizzazione differita è ideale per gli scenari in cui i dati vengono scritti in burst e si desidera ammortizzare il lavoro necessario per indicizzare il contenuto dell'indice in un periodo di tempo più lungo. Questo consente di usare in modo efficiente la velocità effettiva con provisioning e di servire le richieste di scrittura nei momenti di massima attività con latenza minima. Con l'indicizzazione differita attivata, i risultati delle query risulteranno comunque coerenti indipendentemente dal livello di coerenza configurato per l'account di database.

L'esempio seguente mostra come creare una raccolta di DocumentDB usando .NET SDK con indicizzazione automatica coerente in tutti gli inserimenti di documenti.


     // Default collection creates a hash index for all string and numeric    
     // fields. Hash indexes are compact and offer efficient
     // performance for equality queries.
     
     var collection = new DocumentCollection { Id ="defaultCollection" };
     
     // Optional. Override Automatic to false for opt-in indexing of documents.
     collection.IndexingPolicy.Automatic = true;
     
     // Optional. Set IndexingMode to Lazy for bulk import/read heavy        
     // collections. Queries might return stale results with Lazy indexing.
     collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
     
     collection = await client.CreateDocumentCollectionAsync(database.SelfLink, collection);

### Tipi di indice e precisione

Il tipo o lo schema usato per le voci di indice ha un impatto diretto sulle prestazioni e le risorse di archiviazione dell'indice. Per uno schema con precisione più elevata, le query sono in genere più veloci, ma i costi di archiviazione dell'indice sono maggiori. Se si sceglie una precisione inferiore, è possibile che debbano essere elaborati più documenti durante l'esecuzione di query, ma i costi di archiviazione saranno inferiori.

La precisione dell'indice per i valori di qualsiasi percorso può essere configurata entro un intervallo da 1 a 8 oppure può essere impostata su -1 per indicare che il percorso deve usare la precisione massima necessaria. Ogni percorso può essere configurato con una matrice di indici, uno per ogni tipo di dati (stringa e numero) e specifica la precisione per ognuno.

Sono supportati due tipi di indice: Hash e Range. Se si sceglie un tipo di indice **Hash**, è possibile eseguire query di uguaglianza efficienti. Nella maggior parte dei casi, gli indici di questo tipo non richiedono una precisione superiore a quella predefinita di 3 byte.

Se sceglie un tipo di indice **Range**, è possibile eseguire query di intervallo (con >, <, >=, <=, !=), oltre a query di tipo **Order By**. Per impostazione predefinita, le query di tipo Order By richiedono anche che l'indice dell'intervallo venga creato con la precisione di indice massima (-1), in modo da garantire l'ordine totale dei risultati.

Per i percorsi con ampi intervalli di valori, è consigliabile usare una precisione maggiore, ad esempio 6 byte. Un caso comune in cui è richiesto un indice Range con maggiore precisione è rappresentato da timestamp archiviati come valore Epoch.

Se non sono richieste query di intervallo particolarmente efficienti, l'impostazione predefinita degli indici Hash offre un compromesso ottimale tra archiviazione e prestazioni. Si noti che per supportare la clausola order by o le query di intervallo, è necessario specificare un criterio di indice personalizzato/non predefinito.

L'esempio seguente mostra come aumentare la precisione per gli indici Range di una raccolta mediante .NET SDK. Nell'esempio viene usato un percorso speciale "/" che verrà descritto nella sezione successiva.

    var rangeDefault = new DocumentCollection { Id = "rangeCollection" };
    
    rangeDefault.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = -1 }, 
                new RangeIndex(DataType.Number) { Precision = -1 }
            }
        });

    await client.CreateDocumentCollectionAsync(database.SelfLink, rangeDefault);   


### Percorsi di indice

All'interno dei documenti è possibile scegliere i percorsi da includere o escludere dall'indicizzazione. Questo consente di migliorare le prestazioni di scrittura e ridurre lo spazio di archiviazione dell'indice per gli scenari in cui i modelli di query sono già noti.

I percorsi di indice iniziano con la radice (/) e terminano in genere con il carattere jolly ?, che indica la possibilità di molteplici valori per il prefisso. Ad esempio, per servire SELECT * FROM Families F WHERE F.familyName = "Andersen", è necessario includere un percorso di indice per /"familyName"/? nei criteri di indicizzazione della raccolta.

I percorsi di indice possono anche usare il carattere jolly * per specificare un comportamento ricorsivo al di sotto del prefisso. Ad esempio, /"payload"/* può essere usato per escludere dall'indicizzazione qualsiasi elemento al di sotto della proprietà payload.

Di seguito sono indicati i modelli comuni per la definizione di percorsi di indice:

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top">
                <p>
                    <strong>Percorso</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    <strong>Descrizione/Caso d'uso</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /*
                </p>
            </td>
            <td valign="top">
                <p>
                    Percorso predefinito per la raccolta. Ricorsivo e applicabile all'intera struttura ad albero del documento.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /prop/?
                </p>
            </td>
            <td valign="top">
                <p>
                    Percorso di indice che consente di servire query come le seguenti (rispettivamente con i tipi Hash e Range):
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop = "value"
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop > 5
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /prop/*
                </p>
            </td>
            <td valign="top">
                <p>
                    Percorso di indice per tutti i percorsi al di sotto dell'etichetta specificata. Funziona con le query seguenti
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop = "value"
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop > 5
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop.nextprop = "value"
                </p>

            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /props/[]/?
                </p>
            </td>
            <td valign="top">
                <p>
                    Percorso di indice che consente di servire iterazioni e query JOIN su matrici di valori scalari, come ["a", "b", "c"]:
                </p>
                <p>
                    SELECT tag FROM tag IN collection.props WHERE tag = "value"
                </p>
                <p>
                    SELECT tag FROM collection c JOIN tag IN c.props WHERE tag > 5
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /props/[]/subprop/?
                </p>
            </td>
            <td valign="top">
                <p>
                    Percorso di indice che consente di servire iterazioni e query JOIN su matrici di oggetti, come [{subprop: "a"}, {subprop: "b"}]:
                </p>
                <p>
                    SELECT tag FROM tag IN collection.props WHERE tag.subprop = "value"
                </p>
                <p>
                    SELECT tag FROM collection c JOIN tag IN c.props WHERE tag.subprop = "value"
                </p>
            </td>
        </tr>        
        <tr>
            <td valign="top">
                <p>
                    /prop/subprop/?
                </p>
            </td>
            <td valign="top">
                <p>
                    Percorso di indice che consente di servire query come le seguenti (rispettivamente con i tipi Hash e Range):
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop = "value"
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop > 5
                </p>
            </td>
        </tr>
    </tbody>
</table>

>[AZURE.NOTE]Durante l'impostazione dei percorsi indice personalizzata, è necessario specificare la regola di indicizzazione predefiniti per l'albero intero documento identificato dal percorso speciale "/ *".

Nell'esempio seguente viene configurato un percorso specifico con l'indicizzazione di intervallo e un valore di precisione personalizzato di 20 byte:

    var collection = new DocumentCollection { Id = "rangeSinglePathCollection" };    
    
    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/Title/?", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = 20 } } 
            });

    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*" 
        });
        
    collection = await client.CreateDocumentCollectionAsync(database.SelfLink, pathRange);


Quando una query utilizza Order By, ma non dispone di un indice di intervallo con il percorso di query con la precisione massima, DocumentDB restituisce un errore. Viene restituito un errore per le query con operatori di intervallo come > = Se non esiste alcun indice di intervallo (di qualsiasi precisione), ma quelli possono essere serviti se sono presenti altri filtri che possono essere serviti dall'indice. Le query di intervallo possono essere eseguite senza un indice di intervallo utilizzando l'intestazione x-ms-documentdb-enable-analisi nell'opzione richiesta EnableScanInQuery utilizzando .NET SDK o l'API REST.

Allo stesso modo percorsi possono essere completamente esclusi dall'indicizzazione. Nell'esempio seguente viene illustrato come escludere un'intera sezione dei documenti (anche noto come una sotto-struttura ad albero) dall'indicizzazione utilizzando il "*" con caratteri jolly.

    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
    
    collection = await client.CreateDocumentCollectionAsync(database.SelfLink, excluded);


## Ottimizzazione delle prestazioni

Quando si valutano le diverse configurazioni dei criteri di indicizzazione, è consigliabile valutare le implicazioni di archiviazione e velocità effettiva dei criteri tramite le API di DocumentDB.

Per controllare la quota di archiviazione e utilizzo di una raccolta, eseguire una richiesta HEAD o GET sulla risorsa della raccolta ed esaminare le intestazioni x-ms-request-quota e x-ms-request-usage. In.NET SDK, le proprietà [DocumentSizeQuota](http://msdn.microsoft.com/library/dn850325.aspx) e [DocumentSizeUsage](http://msdn.microsoft.com/library/azure/dn850324.aspx) di [ResourceResponse<T>](http://msdn.microsoft.com/library/dn799209.aspx) contengono questi valori corrispondenti.

     // Measure the document size usage (which includes the index size) against   
     // different policies.        
     ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(collectionSelfLink);  
     Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);


Per valutare l'overhead di indicizzazione di ogni operazione di scrittura (create, update o delete), esaminare l'intestazione x-ms-request-charge (o l'equivalente proprietà [RequestCharge](http://msdn.microsoft.com/library/dn799099.aspx) di [ResourceResponse<T>](http://msdn.microsoft.com/library/dn799209.aspx) in .NET SDK) per determinare il numero di unità di richiesta usate da queste operazioni.

     // Measure the performance (request units) of writes.     
     ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);              
     Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
     
     // Measure the performance (request units) of queries.    
     IDocumentQuery<dynamic> queryable =  client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();                                  
     double totalRequestCharge = 0;
     while (queryable.HasMoreResults)
     {
        FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
        Console.WriteLine("Query batch consumed {0} request units",queryResponse.RequestCharge);
        totalRequestCharge += queryResponse.RequestCharge;
     }
     
     Console.WriteLine("Query consumed {0} request units in total", totalRequestCharge);

## Modifiche per la specifica di criteri di indicizzazione
È stata introdotta una modifica nello schema per il criterio di indicizzazione su 7 luglio 2015 con API REST versione 2015-06-03. Le classi corrispondenti nelle versioni di SDK sono nuove implementazioni affinché corrisponda allo schema.

Nella specifica JSON sono state implementate le modifiche seguenti:

- Indicizzazione di criterio supporta gli indici dell'intervallo per le stringhe
- Ogni percorso può avere più definizioni di indice, uno per ogni tipo di dati
- L'indicizzazione precisione supporta 1-8 per numeri, 1-100 per le stringhe di e -1 (la precisione massima)
- Segmenti di percorsi non richiedono una virgoletta doppia per ogni percorso di escape. Ad esempio, è possibile aggiungere un percorso per/titolo /? anziché / "title" /?
- Il percorso radice che rappresenta "tutti i percorsi" può essere rappresentato come / * (oltre a /)

Se si dispone di codice che raccolte disposizioni con un criterio di indicizzazione personalizzato scritto con versione 1.1.0 di .NET SDK o meno recenti, sarà necessario modificare il codice dell'applicazione per gestire queste modifiche per spostare in SDK versione 1.2.0. Se non si dispone di codice che configura il criterio di indicizzazione, o intende continuare a utilizzare una versione precedente di SDK, è richiesta alcuna modifica.

Ecco alcuni esempi che mostrano le differenze tra la versione dell'API 2015-06-03 e la versione precedente 2015-04-08.

**Criterio di indicizzazione precedente JSON**

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
          "/"nonIndexedContent"/*"
       ]
    }

**Criterio di indicizzazione corrente JSON**

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

## Passaggi successivi

Seguire i collegamenti seguenti per esempi di gestione dei criteri di indicizzazione e per ottenere altre informazioni sul linguaggio di query di DocumentDB.

1.	[Esempi di codice di gestione degli indici .NET di DocumentDB](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/IndexManagement/Program.cs)
2.	[Operazione di raccolta API REST di DocumentDB](https://msdn.microsoft.com/library/azure/dn782195.aspx)
3.	[Eseguire query con DocumentDB SQL](documentdb-sql-query.md)

 

<!---HONumber=July15_HO3-->