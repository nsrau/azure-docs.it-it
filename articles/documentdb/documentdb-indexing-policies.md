<properties 
    pageTitle="Criteri di indicizzazione di DocumentDB | Azure" 
    description="Comprendere il funzionamento dell'indicizzazione in DocumentDB e imparare a configurare e modificare i criteri di indicizzazione." 
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
    ms.date="07/19/2015" 
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

Ad esempio, la proprietà JSON `{"headquarters": "Belgium"}` nell'esempio precedente corrisponde al percorso `/headquarters/Belgium`. La matrice JSON `{"exports": [{"city": “Moscow"}, {"city": Athens"}]}` corrisponde ai percorsi `/exports/[]/city/Moscow` e `/exports/[]/city/Athens`.

>[AZURE.NOTE]La rappresentazione di percorso attenua il confine tra struttura/schema e valori di istanza nei documenti, consentendo a DocumentDB di essere effettivamente privo di schema.

In DocumentDB i documenti sono organizzati in raccolte su cui è possibile eseguire query SQL o che è possibile elaborare nell'ambito di una singola transazione. Ogni raccolta può essere configurata con specifici criteri di indicizzazione espressi in termini di percorsi. Nella sezione seguente si esaminerà come configurare il comportamento dell'indicizzazione di una raccolta di DocumentDB.

## Configurazione dei criteri di indicizzazione di una raccolta

Per ogni raccolta DocumentDB, è possibile configurare le opzioni seguenti:

- Modalità di indicizzazione: **Consistent**, **Lazy** (per gli aggiornamenti asincroni) o **None** (solo accesso basato su "id")
- Percorsi inclusi ed esclusi: scegliere i percorsi all'interno di JSON da includere e da escludere
- Tipo indicizzato: **Hash** (per le query di uguaglianza), **Range** (per le query di uguaglianza, intervallo e orderby con una capacità di archiviazione maggiore)
- Precisione dell'indice: da 1 a 8 o massima (-1) come compromesso tra esigenze di archiviazione e prestazioni
- Automatica: **true** o **false** per abilitare oppure **manual** (consenso esplicito con ogni inserimento)

Il frammento di codice .NET seguente mostra come impostare criteri di indicizzazione personalizzati durante la creazione di una raccolta. Qui vengono impostati i criteri con l'indice di tipo Range per stringhe e numeri con la precisione massima. Questi criteri consentono di eseguire query orderby sulle stringhe.

    var collection = new DocumentCollection { Id = "myCollection" };
    
    collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
    
    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = -1 }, 
                new RangeIndex(DataType.Number) { Precision = -1 }
            }
        });

    await client.CreateDocumentCollectionAsync(database.SelfLink, collection);   


>[AZURE.NOTE]Lo schema JSON per il criterio di indicizzazione è stato modificato con la versione dell'API REST 2015-06-03 per supportare gli indici di tipo Range sulle stringhe. .NET SDK 1.2.0 e Java, Python e Node.js SDK 1.1.0 supportano il nuovo schema di criteri. SDK precedenti usano l'API REST versione 2015-04-08 e supportano lo schema precedente dei criteri di indicizzazione.
>
>Per impostazione predefinita, DocumentDB indicizza tutte le proprietà delle stringhe nei documenti in maniera coerente con un indice Hash e le proprietà numeriche con un indice Range.

### Modalità di indicizzazione

È possibile scegliere tra aggiornamenti dell'indice sincroni (**Consistent**), asincroni (**Lazy**) o nessuno (**None**). Per impostazione predefinita, l'indice viene aggiornato in modo sincrono in ogni azione di inserimento, sostituzione o eliminazione eseguita su un documento della raccolta. Questo consente alle query di rispettare lo stesso livello di coerenza delle letture di documenti, senza che l'indice debba recuperare alcun ritardo.

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

### Percorsi di indice

All'interno dei documenti è possibile scegliere i percorsi da includere o escludere dall'indicizzazione. Questo consente di migliorare le prestazioni di scrittura e ridurre lo spazio di archiviazione dell'indice per gli scenari in cui i modelli di query sono già noti.

I percorsi di indice iniziano con la radice (/) e terminano in genere con il carattere jolly ?, che indica la possibilità di molteplici valori per il prefisso. Ad esempio, per usare SELECT * FROM Families F WHERE F.familyName = "Andersen", è necessario includere un percorso di indice per /"familyName"/? nei criteri di indicizzazione della raccolta.

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
                <p>
                    SELECT * FROM collection c ORDER BY c.prop
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
                <p>
                    SELECT * FROM collection c ORDER BY c.prop
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
                <p>
                    SELECT * FROM collection c ORDER BY c.prop.subprop
                </p>                
            </td>
        </tr>
    </tbody>
</table>

>[AZURE.NOTE]Quando si impostano percorsi di indice personalizzati, è necessario specificare la regola di indicizzazione predefinita per l'intera struttura ad albero del documento indicata dal percorso speciale "/*".

L'esempio seguente configura un percorso specifico con indicizzazione Range e un valore di precisione personalizzato di 20 byte:

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
        
    collection = await client.CreateDocumentCollectionAsync(database.SelfLink, pathRange);

### Tipi di dati, tipologie e precisioni degli indici

Dopo avere visto come specificare i percorsi, verranno esaminate le opzioni che è possibile usare per configurare i criteri di indicizzazione per un percorso. È possibile specificare una o più definizioni di indicizzazione per ogni percorso:

- Tipo di dati: **String** o **Number** (può contenere una sola voce per tipo di dati per percorso)
- Tipologia indice: **Hash** (query di uguaglianza) o **Range** (query di uguaglianza, intervallo o orderby)
- Precisione: da 1 a 8 o -1 (precisione massima) per i numeri, da 1 a 100 (precisione massima) per le stringhe

#### Tipologia di indice

DocumentDB supporta due tipologie di indice per ogni coppia percorso e tipo di dati.

- **Hash** supporta query di uguaglianza efficienti. Nella maggior parte dei casi, gli indici di questo tipo non richiedono una precisione superiore a quella predefinita di 3 byte.
- **Range** supporta query di uguaglianza efficienti, query di intervallo (con >, <, >=, <=, !=) e query orderby. Per impostazione predefinita, anche le query orderby richiedono la precisione di indice massima (-1).

#### Precisione indice

La precisione indice consente di raggiungere un compromesso tra l'overhead di archiviazione indice e le prestazioni delle query. Per i numeri, si consiglia di usare la configurazione di precisione predefinita -1. Poiché i numeri sono a 8 byte in JSON, questo valore equivale a una configurazione a 8 byte. Selezionando un valore più basso per la precisione, ad esempio da 1 a 7, i valori compresi in alcuni intervalli eseguono il mapping alla stessa voce di indice. Quindi verrà ridotto lo spazio di archiviazione dell'indice, ma l'esecuzione delle query potrebbe dover elaborare più documenti e di conseguenza utilizzare una velocità effettiva maggiore, ad esempio unità di richiesta.

La configurazione di precisione indice è più utile con gli intervalli di stringhe. Poiché le stringhe possono avere qualsiasi lunghezza arbitraria, la scelta della precisione indice può compromettere le prestazioni delle query di intervallo di stringhe e influire sulla quantità di spazio di archiviazione indice necessario. Gli indici di intervallo di stringhe possono essere configurati con valori compresi tra 1 e 100 o con il valore di precisione massima (-1). Se è necessaria una query orderby sulle stringhe, è necessario specificarlo nel percorso specificato (-1).

L'esempio seguente mostra come aumentare la precisione per gli indici Range di una raccolta mediante .NET SDK. Si noti che viene usato il percorso predefinito "/*".

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


> [AZURE.NOTE]Quando una query usa Order By, ma non dispone di un indice di intervallo con il percorso di query con la precisione massima, DocumentDB restituisce un errore.
>
> Viene restituito un errore per le query con operatori di intervallo come > = se non esiste alcun indice di intervallo (di qualsiasi precisione), ma possono essere usati se sono presenti altri filtri che possono essere gestiti dall'indice.
> 
> Le query di intervallo possono essere eseguite senza un indice di intervallo usando l'intestazione x-ms-documentdb-enable-scans nell'API REST o nell'opzione della richiesta EnableScanInQuery usando .NET SDK.

Allo stesso modo i percorsi possono essere completamente esclusi dall'indicizzazione. Nell'esempio seguente viene illustrato come escludere un'intera sezione dei documenti (detta anche sottoalbero) dall'indicizzazione usando il carattere jolly "*".

    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
    
    collection = await client.CreateDocumentCollectionAsync(database.SelfLink, excluded);


### Indicizzazione automatica

È possibile specificare se la raccolta deve indicizzare automaticamente tutti i documenti. Per impostazione predefinita, tutti i documenti vengono indicizzati automaticamente, ma è possibile scegliere di disattivare tale funzionalità. Quando l'indicizzazione è disattivata, i documenti sono accessibili solo tramite i relativi self link o le query con ID.

Con l'indicizzazione automatica disattivata, è comunque possibile aggiungere in modo selettivo solo documenti specifici all'indice. In alternativa, è possibile lasciare attivata l'indicizzazione automatica e scegliere di escludere in modo selettivo solo documenti specifici. Le configurazioni di attivazione o disattivazione dell'indicizzazione sono utili quando si ha un solo subset di documenti su cui eseguire query.

L'esempio seguente mostra come includere un documento in modo esplicito usando [DocumentDB .NET SDK](https://github.com/Azure/azure-documentdb-java) e la proprietà [RequestOptions.IndexingDirective](http://msdn.microsoft.com/library/microsoft.azure.documents.client.requestoptions.indexingdirective.aspx).

    // If you want to override the default collection behavior to either
    // exclude (or include) a Document from indexing,
    // use the RequestOptions.IndexingDirective property.
    client.CreateDocumentAsync(defaultCollection.SelfLink,
        new { id = "AndersenFamily", isRegistered = true },
        new RequestOptions { IndexingDirective = IndexingDirective.Include });

## Ottimizzazione delle prestazioni

Le API per DocumentDB forniscono informazioni sulle metriche delle prestazioni, ad esempio le risorse di archiviazione dell'indice usate e il costo della velocità effettiva (unità di richiesta) per ogni operazione. Queste informazioni possono essere usate per confrontare diversi criteri di indicizzazione e per l'ottimizzazione delle prestazioni.

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
Il 7 luglio 2015 è stata introdotta una modifica nello schema del criterio di indicizzazione con l'API REST versione 2015-06-03. Le relative classi nelle versioni SDK hanno nuove implementazioni per garantire la corrispondenza con lo schema.

Nella specifica JSON sono state implementate le modifiche seguenti:

- Il criterio di indicizzazione supporta gli indici di intervallo per le stringhe
- Ogni percorso può avere più definizioni di indice, uno per ogni tipo di dati
- La precisione di indicizzazione supporta valori da 1 a 8 per i numeri, da 1 a 100 per le stringhe e -1 (precisione massima)
- I segmenti di percorsi non richiedono le virgolette doppie per l'escape di ogni percorso. Ad esempio, è possibile aggiungere un percorso per /title/? invece che /"title"/?
- Il percorso radice che rappresenta "tutti i percorsi" può essere rappresentato come /* (oltre che /)

Se si dispone di codice che esegue il provisioning delle raccolte con un criterio di indicizzazione personalizzato scritto con .NET SDK versione 1.1.0 o precedente, sarà necessario modificare il codice dell'applicazione per gestire queste modifiche e poter passare a SDK versione 1.2.0. Se non si dispone di codice che configura il criterio di indicizzazione o si prevede di continuare a usare una versione precedente dell'SDK, non è richiesta alcuna modifica.

Per un rapido confronto, ecco un criterio di indicizzazione personalizzato di esempio scritto usando l'API REST versione 2015-06-03 e la versione precedente 2015-04-08.

**JSON criterio di indicizzazione precedente**

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

**JSON criterio di indicizzazione corrente**

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

 

<!---HONumber=July15_HO4-->