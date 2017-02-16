---
title: Criteri di indicizzazione DocumentDB | Documentazione Microsoft
description: Comprendere il funzionamento dell&quot;indicizzazione in DocumentDB e imparare a configurare e modificare i criteri di indicizzazione. Configurare il criterio di indicizzazione in DocumentDB per l&quot;indicizzazione automatica e prestazioni superiori.
keywords: 'Come funzionano: indicizzazione, indicizzazione automatica, indicizzazione del database, documentdb, azure, Microsoft azure'
services: documentdb
documentationcenter: 
author: arramac
manager: jhubbard
editor: monicar
ms.assetid: d5e8f338-605d-4dff-8a61-7505d5fc46d7
ms.service: documentdb
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 12/22/2016
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 7f5e33b7f80e3c1e1e3e66b3cab879a5bc30e823
ms.openlocfilehash: 6941a0a5e108617ebb16354edc9917392b7cb786


---
# <a name="documentdb-indexing-policies"></a>Criteri di indicizzazione di DocumentDB
Anche se molti clienti sono felici del fatto che Azure DocumentDB gestisca automaticamente tutti gli aspetti dell'indicizzazione, DocumentDB supporta anche l'impostazione di **criteri d'indicizzazione** personalizzati per le raccolte durante la creazione. I criteri di indicizzazione in DocumentDB sono più flessibili e potenti rispetto agli indici secondari disponibili in altre piattaforme database, perché consentono di progettare e personalizzare la forma dell'indice senza sacrificare la flessibilità dello schema. Per apprendere come eseguire l’indicizzazione in DocumentDB, è necessario comprendere che con la gestione dei criteri di indicizzazione è  possibile rendere accurati compromessi tra archiviazione dell'indice, scrittura e velocità effettiva di query e la coerenza delle query.  

In questo articolo vengono presentati i criteri di indicizzazione di DocumentDB, il procedimento per personalizzare i criteri di indicizzazione e i compromessi associati. 

Dopo la lettura di questo articolo, si potrà rispondere alle domande seguenti:

* Come è possibile ignorare le proprietà da includere o escludere dall'indicizzazione?
* Come è possibile configurare l'indice di eventuali aggiornamenti?
* Come è possibile configurare l'indicizzazione per eseguire query Order By o intervallo?
* Come è possibile apportare modifiche ai criteri di indicizzazione di una raccolta?
* Come confrontare archiviazione e prestazioni dei diversi criteri di indicizzazione?

## <a name="a-idcustomizingindexingpolicya-customizing-the-indexing-policy-of-a-collection"></a><a id="CustomizingIndexingPolicy"></a> Personalizzazione dei criteri di indicizzazione di una raccolta
Gli sviluppatori possono personalizzare i compromessi tra archiviazione, prestazioni delle query/scrittura e coerenza delle query, ignorando i criteri di indicizzazione predefiniti in una raccolta di DocumentDB e configurando i seguenti aspetti.

* **Includi/escludi documenti e percorsi da/a indice**. Gli sviluppatori possono scegliere determinati documenti da escludere o includere nell'indice al momento del loro inserimento o sostituzione nella raccolta. Gli sviluppatori possono anche scegliere di includere o escludere determinate proprietà JSON, anche note come percorsi, inclusi i modelli con caratteri jolly, da indicizzare in documenti che sono inclusi in un indice.
* **Configurazione di vari tipi di indice**. Per ognuno dei percorsi inclusi, gli sviluppatori possono inoltre specificare il tipo di indice richiesto su una raccolta in base ai loro dati, al carico di lavoro previsto della query e alla "precisione" numerica/stringa per ogni percorso.
* **Configurazione delle modalità di aggiornamento dell’indice**. DocumentDB supporta tre modalità di indicizzazione che possono essere configurate tramite criteri di indicizzazione in una raccolta di DocumentDB: Coerente, Differita e Nessuna. 

Il frammento di codice .NET seguente mostra come impostare criteri di indicizzazione personalizzati durante la creazione di una raccolta. Qui vengono impostati i criteri con l'indice di tipo Range per stringhe e numeri con la precisione massima. Questi criteri consentono di eseguire query orderby sulle stringhe.

    DocumentCollection collection = new DocumentCollection { Id = "myCollection" };

    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), collection);   


> [!NOTE]
> Lo schema JSON per il criterio di indicizzazione è stato modificato con la versione dell'API REST 2015-06-03 per supportare gli indici di tipo Range sulle stringhe. .NET SDK 1.2.0 e Java, Python e Node.js SDK 1.1.0 supportano il nuovo schema di criteri. SDK precedenti usano l'API REST versione 2015-04-08 e supportano lo schema precedente dei criteri di indicizzazione.
> 
> Per impostazione predefinita, DocumentDB indicizza tutte le proprietà delle stringhe nei documenti in maniera coerente con un indice Hash e le proprietà numeriche con un indice Range.  
> 
> 

### <a name="database-indexing-modes"></a>Modalità di indicizzazione del database
DocumentDB supporta tre modalità di indicizzazione che possono essere configurate tramite criteri di indicizzazione in una raccolta di DocumentDB – Coerente, Differita e Nessuna.

**Coerente**: se i criteri di una raccolta di DocumentDB vengono designati come "coerenti", le query su una determinata raccolta DocumentDB seguono lo stesso livello di coerenza specificato per le letture punto (assoluta, obsoleta associata, sessione o finale). L'indice viene aggiornato in modo sincrono come parte dell'aggiornamento del documento (ad esempio inserisci, sostituisci, aggiorna e cancella di un documento in una raccolta di DocumentDB).  L'indicizzazione coerente supporta query coerenti al costo della riduzione nella velocità effettiva di scrittura. Questa riduzione è una funzione di percorsi univoci che devono essere indicizzati e il "livello di coerenza". La modalità di indicizzazione coerente è progettata per carichi di lavoro "scrivi rapidamente, esegui query immediatamente".

**Differita**: per consentire la velocità massima effettiva dell'inserimento del documento, una raccolta di DocumentDB può essere configurata con coerenza differita, per la quale le query alla fine sono comunque coerenti. L'indice viene aggiornato in modo asincrono quando una raccolta di DocumentDB è inattiva ad esempio quando la capacità di velocità effettiva della raccolta non è utilizzata completamente per rispondere alle richieste dell’utente. Per carichi di lavoro “inserisci ora, esegui la query più tardi” che richiedono l’inserimento del documento senza alcun impedimento, la modalità di indicizzazione “differita” è più adatta.

**Nessuna**: una raccolta contrassegnata con la modalità "Nessuna" non include indici associati. Si usa in genere se DocumentDB è utilizzato come un archivio di chiavi-valori e i documenti sono accessibili solo con la relativa proprietà ID. 

> [!NOTE]
> La configurazione dei criteri di indicizzazione con la modalità "nessuna" ha l'effetto collaterale di eliminare eventuali indici esistenti. Utilizzare questa opzione se i modelli di accesso richiedono solo "id" e/o "self-link".
> 
> 

L'esempio seguente mostra come creare una raccolta di DocumentDB usando .NET SDK con indicizzazione automatica coerente in tutti gli inserimenti di documenti.

Nella tabella seguente viene illustrata la coerenza per le query basata sulla modalità di indicizzazione (Coerente e Differita) configurata per la raccolta e il livello di coerenza specificato per la richiesta di query. Questo si applica alle query eseguite utilizzando qualsiasi interfaccia SDK, API REST, o stored procedure e trigger. 

|Coerenza|Modalità di indicizzazione: coerente|Modalità di indicizzazione: differita|
|---|---|---|
|Assoluta|Assoluta|Finale|
|Obsolescenza associata|Obsolescenza associata|Finale|
|Sessione|Sessione|Finale|
|Finale|Finale|Finale|

DocumentDB restituisce un errore per le query eseguite su raccolte con la modalità di indicizzazione Nessuna. È ancora possibile eseguire query come analisi tramite l'intestazione `x-ms-documentdb-enable-scan` esplicita nell'API REST o l'opzione relativa alla richiesta `EnableScanInQuery` con .NET SDK. Alcune funzionalità di query, ad esempio ORDER BY, non sono supportati come analisi con `EnableScanInQuery`.

Nella tabella seguente viene illustrata la coerenza per le query in base alla modalità di indicizzazione (Coerente, Differita e Nessuna) quando viene specificato EnableScanInQuery.

|Coerenza|Modalità di indicizzazione: coerente|Modalità di indicizzazione: differita|Modalità di indicizzazione: nessuna|
|---|---|---|---|
|Assoluta|Assoluta|Finale|Assoluta|
|Obsolescenza associata|Obsolescenza associata|Finale|Obsolescenza associata|
|Sessione|Sessione|Finale|Sessione|
|Finale|Finale|Finale|Finale|

L'esempio seguente di codice mostra come creare una raccolta di DocumentDB usando .NET SDK con indicizzazione automatica coerente in tutti gli inserimenti di documenti.

     // Default collection creates a hash index for all string fields and a range index for all numeric    
     // fields. Hash indexes are compact and offer efficient performance for equality queries.

     var collection = new DocumentCollection { Id ="defaultCollection" };

     collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;

     collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("mydb"), collection);


### <a name="index-paths"></a>Percorsi di indice
DocumentDB modella i documenti JSON e l'indice come strutture ad albero e consente di ottimizzare i criteri per i percorsi all'interno della struttura. È possibile trovare ulteriori informazioni in [Introduzione all'indicizzazione DocumentDB](documentdb-indexing.md). All'interno dei documenti è possibile scegliere i percorsi da includere o escludere dall'indicizzazione. Questo consente di migliorare le prestazioni di scrittura e ridurre lo spazio di archiviazione dell'indice per gli scenari in cui i modelli di query sono già noti.

I percorsi di indice iniziano con la radice (/) e terminano in genere con il carattere jolly ?, che indica la possibilità di molteplici valori per il prefisso. Ad esempio, per usare SELECT * FROM Families F WHERE F.familyName = "Andersen", è necessario includere un percorso di indice per /"familyName"/? nei criteri di indicizzazione della raccolta.

I percorsi di indice possono anche usare il carattere jolly * per specificare un comportamento ricorsivo al di sotto del prefisso. Ad esempio, /payload/* può essere usato per escludere dall'indicizzazione qualsiasi elemento al di sotto della proprietà payload.

Di seguito sono indicati i modelli comuni per la definizione di percorsi di indice:

| Path                | Descrizione/Caso d'uso                                                                                                                                                                                                                                                                                         |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| /                   | Percorso predefinito per la raccolta. Ricorsivo e applicabile all'intera struttura ad albero del documento.                                                                                                                                                                                                                                   |
| /prop/?             | Percorso di indice che consente di servire query come le seguenti (rispettivamente con i tipi Hash e Range):<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop > 5<br><br>SELECT FROM collection c ORDER BY c.prop                                                                       |
| /prop/              | Percorso di indice per tutti i percorsi al di sotto dell'etichetta specificata. Funziona con le query seguenti<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop.subprop > 5<br><br>SELECT FROM collection c WHERE c.prop.subprop.nextprop = "value"<br><br>SELECT FROM collection c ORDER BY c.prop         |
| /props/[]/?         | Percorso di indice che consente di servire iterazioni e query JOIN su matrici di valori scalari, come ["a", "b", "c"]:<br><br>SELECT tag FROM tag IN collection.props WHERE tag = "value"<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag > 5                                                                         |
| /props/[]/subprop/? | Percorso di indice che consente di servire iterazioni e query JOIN su matrici di oggetti, come [{subprop: "a"}, {subprop: "b"}]:<br><br>SELECT tag FROM tag IN collection.props WHERE tag.subprop = "value"<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag.subprop = "value"                                  |
| /prop/subprop/?     | Percorso di indice che consente di servire query come le seguenti (rispettivamente con i tipi Hash e Range):<br><br>SELECT FROM collection c WHERE c.prop.subprop = "value"<br><br>SELECT FROM collection c WHERE c.prop.subprop > 5                                                                                                                    |

> [!NOTE]
> Quando si impostano percorsi di indice personalizzati, è necessario specificare la regola di indicizzazione predefinita per l'intera struttura ad albero del documento indicata dal percorso speciale "/*". 
> 
> 

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

    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), pathRange);


### <a name="index-data-types-kinds-and-precisions"></a>Tipi di dati, tipologie e precisioni degli indici
Dopo avere visto come specificare i percorsi, verranno esaminate le opzioni che è possibile usare per configurare i criteri di indicizzazione per un percorso. È possibile specificare una o più definizioni di indicizzazione per ogni percorso:

* Tipo di dati: **String**, **Number**, **Point**, **Polygon** o **LineString**; può contenere una sola voce per tipo di dati per percorso.
* Tipologia indice: **Hash** (query di uguaglianza), **Range** (query di uguaglianza, intervallo o orderby) o **Spatial** (query spaziali) 
* Precisione: da 1 a 8 o -1 (precisione massima) per i numeri, da 1 a 100 (precisione massima) per le stringhe

#### <a name="index-kind"></a>Tipologia di indice
DocumentDB supporta i tipi di indice Hash e Range per ogni percorso (che può essere configurato per stringhe, numeri o entrambi).

* **Hash** supporta query di uguaglianza efficienti e query JOIN. Nella maggior parte dei casi, gli indici di questo tipo non richiedono una precisione superiore a quella predefinita di 3 byte. DataType può essere una stringa o un numero.
* **Range** supporta query di uguaglianza efficienti, query di intervallo (con >, <, >=, <=, !=) e query orderby. Per impostazione predefinita, anche le query orderby richiedono la precisione di indice massima (-1). DataType può essere una stringa o un numero.

DocumentDB supporta anche il tipo di indice Spatial per ogni percorso che è possibile specificare per i tipi di dati Point, Polygon o LineString. Il valore nel percorso specificato deve essere un frammento GeoJSON valido come `{"type": "Point", "coordinates": [0.0, 10.0]}`.

* **Spatial** supporta query spaziali (within e distance) efficienti. DataType può essere Point, Polygon o LineString.

> [!NOTE]
> DocumentDB supporta l'indicizzazione automatica di tipi di dati Points, Polygons e LineStrings.
> 
> 

Di seguito sono elencati i tipi di indice supportati e gli esempi di query che possono essere utilizzati per rispondere:

| Tipologia di indice | Descrizione/Caso d'uso                                                                                                                                                                                                                                                                                                                                                                                                              |
| ---------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Hash       | Hash over /prop/? (or /) può essere usato per servire in modo efficiente le query seguenti:<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>Hash over /props/[]/? (or / or /props/) può essere usato per servire in modo efficiente le query seguenti:<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag = 5                                                                                                                       |
| Range      | Range over /prop/? (or /) può essere usato per servire in modo efficiente le query seguenti:<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop > 5<br><br>SELECT FROM collection c ORDER BY c.prop                                                                                                                                                                                                              |
| Spatial     | Range over /prop/? (or /) può essere usato per servire in modo efficiente le query seguenti:<br><br>SELECT FROM collection c<br><br>WHERE ST_DISTANCE(c.prop, {"type": "Point", "coordinates": [0.0, 10.0]}) < 40<br><br>SELECT FROM collection c WHERE ST_WITHIN(c.prop, {"type": "Polygon", ... }) --con indicizzazione su punti abilitata<br><br>SELECT FROM collection c WHERE ST_WITHIN({"type": "Point", ... }, c.prop) --con indicizzazione su poligoni abilitata              |

Per impostazione predefinita, viene restituito un errore per le query con operatori intervallo, ad esempio >= se non esiste alcun indice di intervallo (di qualsiasi precisione) per segnalare che un'analisi potrebbe essere necessaria per soddisfare la query. Le query di intervallo possono essere eseguite senza un indice di intervallo usando l'intestazione x-ms-documentdb-enable-scans nell'API REST o nell'opzione della richiesta EnableScanInQuery usando .NET SDK. Se sono disponibili altri i filtri nella query che DocumentDB può utilizzare l'indice per filtrare i dati, quindi non verrà restituito alcun errore.

Le stesse regole sono valide per le query spaziali. Per impostazione predefinita, viene restituito un errore per le query spaziali se non esiste alcun indice spaziale e non sono presenti altri filtri che possono essere serviti dall'indice. Possono essere eseguite come una scansione con x-ms-documentdb-enable-scan/EnableScanInQuery.

#### <a name="index-precision"></a>Precisione indice
La precisione indice consente di raggiungere un compromesso tra archiviazione indice e prestazioni delle query. Per i numeri, si consiglia di usare la configurazione di precisione predefinita -1 ("massimo"). Poiché i numeri sono a 8 byte in JSON, questo valore equivale a una configurazione a 8 byte. Selezionando un valore più basso per la precisione, ad esempio da 1 a 7, i valori compresi in alcuni intervalli eseguono il mapping alla stessa voce di indice. Quindi verrà ridotto lo spazio di archiviazione dell'indice, ma l'esecuzione delle query potrebbe dover elaborare più documenti e di conseguenza utilizzare una velocità effettiva maggiore, ad esempio unità di richiesta.

La configurazione di precisione dell'indice ha un'applicazione più pratica con gli intervalli di stringhe. Poiché le stringhe possono avere qualsiasi lunghezza arbitraria, la scelta della precisione indice può compromettere le prestazioni delle query di intervallo di stringhe e influire sulla quantità di spazio di archiviazione indice necessario. Gli indici di intervallo di stringhe possono essere configurati con valori compresi tra 1 e 100 o con il valore di precisione "massima" (-1). Se si desidera eseguire query Order By su proprietà della stringa, è necessario specificare una precisione pari a -1 per i percorsi corrispondenti.

Gli indici spaziali usano sempre la precisione dell'indice predefinito per tutti i tipi (Points, LineStrings e Polygons) e non possono essere sottoposti a override. 

L'esempio seguente mostra come aumentare la precisione per gli indici Range di una raccolta mediante .NET SDK. 

**Creare una raccolta con una precisione di indice personalizzata**

    var rangeDefault = new DocumentCollection { Id = "rangeCollection" };

    // Override the default policy for Strings to range indexing and "max" (-1) precision
    rangeDefault.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), rangeDefault);   


> [!NOTE]
> Quando una query usa Order By, ma non dispone di un indice di intervallo con il percorso di query con la precisione massima, DocumentDB restituisce un errore. 
> 
> 

Allo stesso modo i percorsi possono essere completamente esclusi dall'indicizzazione. Nell'esempio seguente viene illustrato come escludere un'intera sezione dei documenti (detta anche sottoalbero) dall'indicizzazione usando il carattere jolly "*".

    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");

    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);



## <a name="opting-in-and-opting-out-of-indexing"></a>Acconsentire e escludere l’indicizzazione
È possibile specificare se la raccolta deve indicizzare automaticamente tutti i documenti. Per impostazione predefinita, tutti i documenti vengono indicizzati automaticamente, ma è possibile scegliere di disattivare tale funzionalità. Quando l'indicizzazione è disattivata, i documenti sono accessibili solo tramite i relativi self link o le query con ID.

Con l'indicizzazione automatica disattivata, è comunque possibile aggiungere in modo selettivo solo documenti specifici all'indice. In alternativa, è possibile lasciare attivata l'indicizzazione automatica e scegliere di escludere in modo selettivo solo documenti specifici. Le configurazioni di attivazione o disattivazione dell'indicizzazione sono utili quando si ha un solo subset di documenti su cui eseguire query.

L'esempio seguente mostra come includere un documento in modo esplicito usando [DocumentDB .NET SDK](https://github.com/Azure/azure-documentdb-java) e la proprietà [RequestOptions.IndexingDirective](http://msdn.microsoft.com/library/microsoft.azure.documents.client.requestoptions.indexingdirective.aspx).

    // If you want to override the default collection behavior to either
    // exclude (or include) a Document from indexing,
    // use the RequestOptions.IndexingDirective property.
    client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"),
        new { id = "AndersenFamily", isRegistered = true },
        new RequestOptions { IndexingDirective = IndexingDirective.Include });

## <a name="modifying-the-indexing-policy-of-a-collection"></a>Modifica del criterio di indicizzazione di una raccolta
DocumentDB consente di apportare modifiche ai criteri di indicizzazione di una raccolta in tempo reale. Una modifica dei criteri di indicizzazione in una raccolta di DocumentDB può provocare una modifica nella forma dell’indice tra cui i percorsi che possono essere indicizzati, nonché il modello di coerenza dell'indice stesso. Una modifica dei criteri di indicizzazione, richiede quindi  una trasformazione del vecchio indice in uno nuovo. 

**Trasformazioni di indici online**

![Funzionamento dell’indicizzazione - trasformazioni di indici online di DocumentDB](media/documentdb-indexing-policies/index-transformations.png)

Le trasformazioni di indice vengono effettuate online, vale a dire che i documenti indicizzati per i criteri precedenti vengono trasformati in modo efficiente per il nuovo criterio **senza modificare la disponibilità di scrittura o la velocità effettiva di provisioning** della raccolta. La coerenza delle operazioni di lettura e scrittura effettuate utilizzando l'API REST, SDK o le stored procedure e trigger non influisce nella trasformazione di indice. Ciò significa che non vi è alcun calo delle prestazioni o tempi di inattività delle applicazioni quando si modifica un criterio di indicizzazione.

Tuttavia, durante la fase di trasformazione dell’indice, le query sono alla fine coerenti indipendentemente dalla configurazione della modalità indicizzazione (coerente o differita). Questo si applica alle query eseguite utilizzando qualsiasi interfaccia SDK, API REST, o all'interno di stored procedure e trigger. Come con l'indicizzazione differita, la trasformazione dell’indice viene eseguita in modo asincrono nelle repliche utilizzando le risorse di riserva disponibili per una determinata replica. 

Le trasformazioni di indice vengono eseguite anche **in situ** (sul posto), ad esempio DocumentDB non mantenere due copie dell'indice e scambia l'indice precedente con quello nuovo. Ciò significa che non è necessario ne consumato ulteriore spazio su disco nelle raccolte durante la trasformazione dell’indice.

Quando si modifica il criterio di indicizzazione, il modo in cui vengono applicate le modifiche per spostare dall'indice precedente al nuovo dipende principalmente dalle configurazioni di modalità di indicizzazione più altri valori come percorsi inclusi/esclusi, tipi di indice e precisione. Se entrambi i criteri (vecchio e nuovo) utilizzano l'indicizzazione coerente, DocumentDB esegue una trasformazione di indici online. Non è possibile applicare un'altra modifica criteri con indicizzazione in modalità coerente durante il corso della trasformazione.

È tuttavia possibile spostarsi alla modalità di indicizzazione differita o nessuna di indicizzazione durante il corso di una trasformazione. 

* Quando ci si sposta nella modalità differita, la modifica dei criteri dell'indice viene resa effettiva immediatamente e DocumentDB ricrea l'indice in modo asincrono. 
* Quando ci si sposta alla modalità nessuna, l'indice viene eliminato immediatamente. Lo spostamento verso la modalità nessuna è utile quando si desidera annullare una trasformazione in corso e avviare l’aggiornamento con criteri diversi di indicizzazione. 

Se si usa .NET SDK, è possibile avviare una modifica dei criteri di un'indicizzazione dei criteri usando il nuovo metodo **ReplaceDocumentCollectionAsync** e rilevando la percentuale dello stato di avanzamento della trasformazione dell'indice usando la proprietà di risposta **IndexTransformationProgress** da una chiamata **ReadDocumentCollectionAsync**. Altri SDK e l'API REST supportano metodi e proprietà equivalenti per apportare modifiche ai criteri di indicizzazione.

Di seguito è riportato un frammento di codice che illustra come modificare i criteri indicizzazione di una raccolta dalla modalità di indicizzazione coerente a differita.

**Modificare i criteri di indicizzazione da coerente a differita**

    // Switch to lazy indexing.
    Console.WriteLine("Changing from Default to Lazy IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.Lazy;

    await client.ReplaceDocumentCollectionAsync(collection);


È possibile controllare lo stato di avanzamento di una trasformazione dell’indice chiamando ReadDocumentCollectionAsync, ad esempio, come illustrato di seguito.

**Tenere traccia dell'avanzamento della trasformazione di indice**

    long smallWaitTimeMilliseconds = 1000;
    long progress = 0;

    while (progress < 100)
    {
        ResourceResponse<DocumentCollection> collectionReadResponse = await client.ReadDocumentCollectionAsync(
            UriFactory.CreateDocumentCollectionUri("db", "coll"));

        progress = collectionReadResponse.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromMilliseconds(smallWaitTimeMilliseconds));
    }

È possibile eliminare l'indice per una raccolta passando alla modalità di indicizzazione nessuna. Potrebbe trattarsi di uno strumento operativo utile se si desidera annullare una trasformazione in corso e avviarne immediatamente un'altra.

**Eliminazione dell'indice per una raccolta**

    // Switch to lazy indexing.
    Console.WriteLine("Dropping index by changing to to the None IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.None;

    await client.ReplaceDocumentCollectionAsync(collection);

Quando si vogliono effettuare modifiche ai criteri di indicizzazione per le raccolte di DocumentDB? I seguenti casi rappresentano la maggior parte dei casi di utilizzo:

* Fornire risultati coerenti durante il normale funzionamento, ma reimpostata l’indicizzazione differita durante le importazioni di dati di massa
* Iniziare a utilizzare nuove funzionalità sulle raccolte attuali DocumentDB, ad esempio, come le query geospaziali che richiedono il tipo di indice Spatial o query di intervallo di stringa e Order By che richiedono il tipo di indice Range di stringa
* Selezionare a mano le proprietà da indicizzare e modificarle nel tempo
* Ottimizzare la precisione dell'indicizzazione per migliorare le prestazioni delle query o ridurre l'archiviazione utilizzata

> [!NOTE]
> Per modificare i criteri di indicizzazione mediante ReplaceDocumentCollectionAsync, è necessaria la versione > = 1.3.0 di .NET SDK
> 
> Affinché la trasformazione di indice venga completata correttamente, è necessario assicurarsi che vi sia sufficiente spazio di archiviazione disponibile nella raccolta. Se la raccolta raggiunge la quota di archiviazione, la trasformazione di indice viene sospesa. La trasformazione di indice verrà ripresa automaticamente quando lo spazio di archiviazione sarà disponibile, ad esempio, se si eliminano alcuni documenti.
> 
> 

## <a name="performance-tuning"></a>Ottimizzazione delle prestazioni
Le API per DocumentDB forniscono informazioni sulle metriche delle prestazioni, ad esempio le risorse di archiviazione dell'indice usate e il costo della velocità effettiva (unità di richiesta) per ogni operazione. Queste informazioni possono essere usate per confrontare diversi criteri di indicizzazione e per l'ottimizzazione delle prestazioni.

Per controllare la quota di archiviazione e utilizzo di una raccolta, eseguire una richiesta HEAD o GET sulla risorsa della raccolta ed esaminare le intestazioni x-ms-request-quota e x-ms-request-usage. In .NET SDK le proprietà [DocumentSizeQuota](http://msdn.microsoft.com/library/dn850325.aspx) e [DocumentSizeUsage](http://msdn.microsoft.com/library/azure/dn850324.aspx) di [ResourceResponse<T\>](http://msdn.microsoft.com/library/dn799209.aspx) contengono questi valori corrispondenti.

     // Measure the document size usage (which includes the index size) against   
     // different policies.
     ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));  
     Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);


Per valutare l'overhead di indicizzazione di ogni operazione di scrittura (create, update o delete), esaminare l'intestazione x-ms-request-charge o l'equivalente proprietà [RequestCharge](http://msdn.microsoft.com/library/dn799099.aspx) di [ResourceResponse<T>\>](http://msdn.microsoft.com/library/dn799209.aspx) in .NET SDK per determinare il numero di unità di richiesta usate da queste operazioni.

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
Il 7 luglio 2015 è stata introdotta una modifica nello schema del criterio di indicizzazione con l'API REST versione 2015-06-03. Le relative classi nelle versioni SDK hanno nuove implementazioni per garantire la corrispondenza con lo schema. 

Nella specifica JSON sono state implementate le modifiche seguenti:

* Il criterio di indicizzazione supporta gli indici di intervallo per le stringhe
* Ogni percorso può avere più definizioni di indice, uno per ogni tipo di dati
* La precisione di indicizzazione supporta valori da 1 a 8 per i numeri, da 1 a 100 per le stringhe e -1 (precisione massima)
* I segmenti di percorsi non richiedono le virgolette doppie per l'escape di ogni percorso. Ad esempio, è possibile aggiungere un percorso per /title/? invece che /"title"/?
* Il percorso radice che rappresenta "tutti i percorsi" può essere rappresentato come /* (oltre che /)

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
          "/\"nonIndexedContent\"/*"
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

## <a name="next-steps"></a>Passaggi successivi
Seguire i collegamenti seguenti per esempi di gestione dei criteri di indicizzazione e per ottenere altre informazioni sul linguaggio di query di DocumentDB.

1. [Esempi di codice di gestione degli indici .NET di DocumentDB](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/IndexManagement/Program.cs)
2. [Operazione di raccolta API REST di DocumentDB](https://msdn.microsoft.com/library/azure/dn782195.aspx)
3. [Eseguire query con DocumentDB SQL](documentdb-sql-query.md)




<!--HONumber=Jan17_HO2-->


