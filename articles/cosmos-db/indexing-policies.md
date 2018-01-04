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
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="how-does-azure-cosmos-db-index-data"></a>Come vengono indicizzati i dati da Azure Cosmos DB?

Per impostazione predefinita, tutti i dati di Azure Cosmos DB sono indicizzati. Sebbene molti clienti siano corrette consentire di gestire automaticamente tutti gli aspetti dell'indicizzazione DB Cosmos Azure, è possibile specificare un oggetto personalizzato *criteri di indicizzazione* per le raccolte durante la creazione di database di Azure Cosmos. Criteri di indicizzazione nel database di Azure Cosmos sono più flessibili e potenti rispetto agli indici secondari che sono disponibili in altre piattaforme di database. Nel database Cosmos di Azure, è possibile progettare e personalizzare la forma dell'indice senza pregiudicare la flessibilità dello schema. 

Per informazioni su come indicizzazione works in Azure Cosmos DB, è importante comprendere che, quando si gestiscono i criteri di indicizzazione, è possibile rendere con granularità fine dei compromessi tra overhead di archiviazione dell'indice, scrittura e velocità effettiva di query e la coerenza delle query.  

In questo articolo è esaminare Azure Cosmos DB criteri, come personalizzare i criteri di indicizzazione e vantaggi e svantaggi associati di indicizzazione. 

Alla fine della lettura, si avranno le risposte alle domande seguenti:

* Come è possibile ignorare le proprietà da includere o escludere dall'indicizzazione?
* Come è possibile configurare l'indice di eventuali aggiornamenti?
* Come è possibile configurare l'indicizzazione per eseguire le query ORDER BY o intervallo?
* Come è possibile apportare modifiche ai criteri di indicizzazione di una raccolta?
* Come confrontare archiviazione e prestazioni dei diversi criteri di indicizzazione?

## Personalizzare i criteri di indicizzazione di una raccolta<a id="CustomizingIndexingPolicy"></a>  
È possibile personalizzare i compromessi tra archiviazione, scrittura e le prestazioni delle query e la coerenza delle query eseguendo l'override di criteri per una raccolta di Azure Cosmos DB di indicizzazione predefiniti. È possibile configurare i seguenti aspetti:

* **Includere o escludere i documenti e i percorsi da e verso l'indice**. È possibile escludere o includere documenti specifici nell'indice quando si inseriscono o sostituire i documenti nella raccolta. È anche possibile includere o escludere specifiche proprietà JSON, detta anche *percorsi*, che si desidera indicizzare in tutti i documenti inclusi in un indice. I percorsi includono modelli jolly.
* **Configurare i vari tipi di indice**. Per ogni percorso incluso, è possibile specificare il tipo di indice che richiede il percorso per una raccolta. È possibile specificare il tipo di indice in base a dati del percorso, il carico di lavoro di query prevista e numerico o stringa "precisione".
* **Configurare la modalità di aggiornamento indice**. DB Cosmos Azure supporta tre modalità di indicizzazione: coerente, Lazy e nessuna. È possibile configurare le modalità di indicizzazione tramite i criteri di indicizzazione per una raccolta di Azure Cosmos DB. 

Il seguente frammento di codice di Microsoft .NET viene illustrato come impostare un criterio personalizzato di indicizzazione quando si crea una raccolta. In questo esempio, si configurare il criterio con un indice di intervallo per le stringhe e numeri a precisione massima. È possibile utilizzare questo criterio per eseguire query ORDER BY in stringhe.

    DocumentCollection collection = new DocumentCollection { Id = "myCollection" };

    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), collection);   


> [!NOTE]
> Lo schema JSON per i criteri di indicizzazione modificato con la versione dell'API REST versione 2015-06-03. Con tale versione, lo schema JSON per criteri di indicizzazione supporta gli indici dell'intervallo in stringhe. .NET SDK 1.2.0 e Java, Python e Node.js SDK 1.1.0 supportano il nuovo schema di criteri. Le versioni precedenti del SDK di utilizzano l'API REST versione 2015-04-08. Supportano lo schema precedente per criteri di indicizzazione.
> 
> Per impostazione predefinita, Azure Cosmos DB indicizza tutte le proprietà di stringa all'interno dei documenti in modo coerente con un indice Hash. Vengono indicizzate tutte le proprietà numeriche all'interno dei documenti in modo coerente con un indice dell'intervallo.  
> 
> 

### <a name="customize-the-indexing-policy-in-the-portal"></a>Personalizzare i criteri di indicizzazione nel portale

È possibile modificare i criteri di indicizzazione di una raccolta nel portale di Azure: 

1. Nel portale, accedere al proprio account Azure Cosmos DB e quindi selezionare la raccolta. 
2. Nel menu di navigazione a sinistra, selezionare **impostazioni**, quindi selezionare **criteri di indicizzazione**. 
3. In **criteri di indicizzazione**, modificare i criteri di indicizzazione e quindi selezionare **OK**. 

### Modalità di indicizzazione del database<a id="indexing-modes"></a>  
DB Cosmos Azure supporta tre modalità di indicizzazione che è possibile configurare tramite i criteri di indicizzazione per una raccolta di Azure Cosmos DB: coerente, Lazy e nessuna.

**Coerente**: se i criteri della raccolta di un database di Azure Cosmos coerenti, le query in una raccolta specifica di Azure Cosmos DB seguono lo stesso livello di coerenza come specificato per le operazioni di lettura punto (sicura, con obsolescenza sessione o eventuale). L'indice viene aggiornato in modo sincrono come parte dell'aggiornamento del documento (insert, replace, update e delete un documento in una raccolta di Azure Cosmos DB).

L'indicizzazione coerente supporta le query coerente al costo di una possibile riduzione della velocità effettiva di scrittura. La riduzione è una funzione di percorsi univoci che devono essere indicizzati e "livello di coerenza". La modalità di indicizzazione coerente è progettata per carichi di lavoro "scrivi rapidamente, esegui query immediatamente".

**Lazy**: l'indice viene aggiornato in modo asincrono quando una raccolta di Azure Cosmos DB è inattivo, ovvero quando la capacità di velocità effettiva della raccolta non è utilizzata completamente per soddisfare le richieste utente. La modalità di indicizzazione Lazy potrebbe essere adatta per carichi di lavoro "di inserimento a questo punto, query in un secondo momento" che richiedono l'inserimento di documento. Si noti che si potrebbero ottenere risultati incoerenti in quanto indicizzati lenta e caricamento dei dati. Ciò significa che le query di conteggio o una query specifica risultati potrebbero non essere coerenti o repeatable in qualsiasi momento. 

L'indice è in genere in modalità di aggiornamento con i dati acquisiti. Con Lazy l'indicizzazione, durata (TTL) le modifiche l'indice verrà eliminata e ricreata. In questo modo i risultati di query e conteggio incoerente per un periodo di tempo. Per questo motivo, la maggior parte degli account di Azure Cosmos DB deve utilizzare la modalità di indicizzazione coerenza.

**Nessuna**: una raccolta contenente una None modalità indice non dispone di alcun indice associato. Viene in genere utilizzato se Azure Cosmos DB viene utilizzato come un valore di chiave di archiviazione e sono accessibili solo documenti la relativa proprietà ID. 

> [!NOTE]
> Configurazione dei criteri di indicizzazione con nessuno ha l'effetto collaterale di eliminare un indice esistente. Da utilizzare se i modelli di accesso richiedono solo ID o collegamento self link.
> 
> 

Nella tabella seguente viene illustrata la coerenza per le query basata sulla modalità di indicizzazione (Coerente e Differita) configurata per la raccolta e il livello di coerenza specificato per la richiesta di query. Questo vale per le query eseguite utilizzando qualsiasi interfaccia: SDK, API REST o all'interno di stored procedure e trigger. 

|Consistency|La modalità di indicizzazione: coerente|La modalità di indicizzazione: Lazy|
|---|---|---|
|Assoluta|Assoluta|Finale|
|Obsolescenza associata|Obsolescenza associata|Finale|
|sessione|sessione|Finale|
|Finale|Finale|Finale|

DB Cosmos Azure restituisce un errore per le query eseguite su raccolte con un Nessuna modalità di indicizzazione. È possibile eseguire query ancora come analisi tramite la proprietà esplicita **x-ms-documentdb-enable-analisi** intestazione nell'API REST o **EnableScanInQuery** richiesta opzione mediante .NET SDK. Alcune funzionalità di query, ad esempio ORDER BY non sono supportati come analisi con **EnableScanInQuery**.

La tabella seguente illustra la coerenza per le query in base alla modalità di indicizzazione (coerenti, Lazy e nessuna) quando **EnableScanInQuery** specificato.

|Consistency|Modalità di indicizzazione: coerente|Modalità di indicizzazione: differita|Modalità di indicizzazione: nessuna|
|---|---|---|---|
|Assoluta|Assoluta|Finale|Assoluta|
|Obsolescenza associata|Obsolescenza associata|Finale|Obsolescenza associata|
|sessione|sessione|Finale|sessione|
|Finale|Finale|Finale|Finale|

La presentazione di esempio di codice seguente illustrato come creare una raccolta di Azure Cosmos DB mediante .NET SDK con indicizzazione coerente in tutti gli inserimenti di documento.

     // Default collection creates a Hash index for all string fields and a Range index for all numeric    
     // fields. Hash indexes are compact and offer efficient performance for equality queries.

     var collection = new DocumentCollection { Id ="defaultCollection" };

     collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;

     collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("mydb"), collection);


### <a name="index-paths"></a>Percorsi di indice
Azure DB Cosmos modelli di documenti JSON e l'indice come alberi. È possibile ottimizzare i criteri per i percorsi all'interno dell'albero. All'interno dei documenti, è possibile scegliere i percorsi da includere o escludere dall'indicizzazione. Questo può offrire prestazioni di scrittura migliorate e archiviazione dell'indice più basso per gli scenari in cui i modelli di query sono noti in anticipo.

I percorsi di indice iniziano con la radice (/) e terminano in genere con il carattere jolly ?, carattere jolly. Ciò indica che sono presenti più valori possibili per il prefisso. Ad esempio, per usare SELECT * FROM Families F WHERE F.familyName = "Andersen", è necessario includere un percorso di indice per /"familyName"/? nei criteri di indicizzazione della raccolta.

I percorsi di indice possono anche usare il carattere jolly \* per specificare un comportamento ricorsivo al di sotto del prefisso. È ad esempio possibile usare /payload/* per escludere dall'indicizzazione qualsiasi elemento al di sotto della proprietà payload.

Di seguito sono indicati i modelli comuni per la definizione di percorsi di indice:

| path                | Descrizione/Caso d'uso                                                                                                                                                                                                                                                                                         |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| /                   | Percorso predefinito per la raccolta. Ricorsivo e si applica all'albero intero documento.                                                                                                                                                                                                                                   |
| /prop/?             | Percorso di indice richiesto per gestire query simile alla seguente (con tipi Hash o Range, rispettivamente):<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop > 5<br><br>SELECT FROM collection c ORDER BY c.prop                                                                       |
| /prop/*             | Percorso di indice per tutti i percorsi al di sotto dell'etichetta specificata. Funziona con le query seguenti<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop.subprop > 5<br><br>SELECT FROM collection c WHERE c.prop.subprop.nextprop = "value"<br><br>SELECT FROM collection c ORDER BY c.prop         |
| /props/[]/?         | Percorso di indice che consente di servire iterazioni e query JOIN su matrici di valori scalari, come ["a", "b", "c"]:<br><br>SELECT tag FROM tag IN collection.props WHERE tag = "value"<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag > 5                                                                         |
| /props/[]/subprop/? | Percorso di indice che consente di servire iterazioni e query JOIN su matrici di oggetti, come [{subprop: "a"}, {subprop: "b"}]:<br><br>SELECT tag FROM tag IN collection.props WHERE tag.subprop = "value"<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag.subprop = "value"                                  |
| /prop/subprop/?     | Percorso di indice richiesto per le query (con tipi Hash o Range, rispettivamente):<br><br>SELECT FROM collection c WHERE c.prop.subprop = "value"<br><br>SELECT FROM collection c WHERE c.prop.subprop > 5                                                                                                                    |

> [!NOTE]
> Quando si impostano i percorsi di indice personalizzato, verrà richiesto di specificare la regola di indicizzazione predefinita per l'albero intero documento, che è identificato dal percorso speciale "/ *". 
> 
> 

Nell'esempio seguente consente di configurare un percorso specifico con l'indice dell'intervallo e il valore personalizzato di precisione pari a 20 byte:

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


### <a name="index-data-types-kinds-and-precisions"></a>Tipi di dati, tipi di indice e precisione
Sono disponibili più opzioni quando si configurano i criteri di indicizzazione per un percorso. È possibile specificare una o più definizioni di indicizzazione per ogni percorso:

* **Tipo di dati**: stringa, numero, punto, poligono o LineString (può contenere solo una voce per ogni tipo di dati per ogni percorso).
* **Tipo di indice**: Hash (query di uguaglianza), intervallo (uguaglianza, intervallo o query ORDER BY) o spaziale (query spaziali).
* **Precisione**: indice per un Hash, il valore varia da 1 a 8 per le stringhe e numeri. Il valore predefinito è 3. Per un indice dell'intervallo, questo valore può essere -1 (la precisione massima). E può variare tra 1 e 100 (la precisione massima) per valori di stringa o numerici.

#### <a name="index-kind"></a>Tipologia di indice
DB Cosmos Azure supporta indice Hash e i tipi di indice di intervallo per ogni percorso che può essere configurata per i tipi di dati stringa o numero o entrambi.

* **Hash** supporta query di uguaglianza efficienti e query JOIN. Per la maggior parte dei casi, gli indici Hash non necessario una precisione superiore rispetto al valore predefinito di 3 byte. Il tipo di dati può essere stringa o numero.
* **Intervallo** supporta le query di uguaglianza efficiente, le query di intervallo (utilizzando >, <>, =, < =,! =) e le query ORDER BY. Le query ORDER By per impostazione predefinita richiedono anche una precisione di indice massimo (-1). Il tipo di dati può essere stringa o numero.

DB Cosmos Azure supporta anche il tipo di indice spaziale per ogni percorso può essere specificato per i tipi di dati LineString, Polygon o punto. Il valore nel percorso specificato deve essere un frammento GeoJSON valido come `{"type": "Point", "coordinates": [0.0, 10.0]}`.

* **Spatial** supporta query spaziali (within e distance) efficienti. Il tipo di dati può essere LineString, Polygon o punto.

> [!NOTE]
> Azure Cosmos DB supporta l'indicizzazione automatica punto Polygon e LineString di tipi di dati.
> 
> 

Di seguito sono elencati i tipi di indice supportati e gli esempi di query che possono essere utilizzati per rispondere:

| Tipologia di indice | Descrizione/Caso d'uso                                                                                                                                                                                                                                                                                                                                                                                                              |
| ---------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Hash       | Hash over /prop/? (or /) può essere usato per servire in modo efficiente le query seguenti:<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>Hash over /props/[]/? (or / or /props/) può essere usato per servire in modo efficiente le query seguenti:<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag = 5                                                                                                                       |
| Range      | Range over /prop/? (or /) può essere usato per servire in modo efficiente le query seguenti:<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop > 5<br><br>SELECT FROM collection c ORDER BY c.prop                                                                                                                                                                                                              |
| Spatial     | Range over /prop/? (or /) può essere usato per servire in modo efficiente le query seguenti:<br><br>SELECT FROM collection c<br><br>WHERE ST_DISTANCE(c.prop, {"type": "Point", "coordinates": [0.0, 10.0]}) < 40<br><br>SELECT FROM collection c WHERE ST_WITHIN(c.prop, {"type": "Polygon", ... }) --con indicizzazione su punti abilitata<br><br>SELECT FROM collection c WHERE ST_WITHIN({"type": "Point", ... }, c.prop) --con indicizzazione su poligoni abilitata              |

Per impostazione predefinita, viene restituito un errore per l'intervallo di query con operatori come > = Se è presente alcun indice di intervallo (di qualsiasi precisione) per segnalare che un'analisi potrebbero essere necessaria per soddisfare la query. Le query di intervallo possono essere eseguite senza un indice dell'intervallo utilizzando la **x-ms-documentdb-enable-analisi** intestazione nell'API REST o **EnableScanInQuery** richiesta opzione mediante .NET SDK. Se sono presenti i filtri nella query che Azure Cosmos DB è possibile utilizzare l'indice per filtrare i dati, non viene restituito alcun errore.

Le stesse regole sono valide per le query spaziali. Per impostazione predefinita, viene restituito un errore per le query spaziali se non esiste alcun indice spaziale e non sono presenti altri filtri che possono essere serviti dall'indice. Può essere eseguite come un'analisi utilizzando **x-ms-documentdb-enable-analisi** o **EnableScanInQuery**.

#### <a name="index-precision"></a>Precisione indice
È possibile utilizzare una precisione di indice per rendere i compromessi tra overhead archiviazione dell'indice e le prestazioni delle query. Per i numeri, è consigliabile utilizzare la configurazione di precisione predefinito-1 (massimo). Poiché i numeri JSON di 8 byte, questa operazione equivale a una configurazione di 8 byte. Scelta di un valore inferiore per la precisione, ad esempio da 1 a 7, significa che i valori all'interno di alcuni intervalli di eseguire il mapping allo stesso indice voce. Pertanto, si riduce lo spazio di archiviazione dell'indice, ma l'esecuzione di query potrebbe essere necessario elaborare più documenti. Di conseguenza, che consuma maggiore velocità effettiva in unità di richiesta.

La configurazione di precisione dell'indice ha un'applicazione più pratica con gli intervalli di stringhe. Poiché le stringhe possono essere di qualsiasi lunghezza arbitraria, la scelta della precisione dell'indice potrebbe influenzare le prestazioni delle query di intervallo di stringa. Potrebbe influenzare anche la quantità di spazio di archiviazione dell'indice che ha richiesto. Gli indici dell'intervallo di stringa possono essere configurati con 1 a 100 o -1 (massimo). Se si desidera eseguire query ORDER BY per le proprietà della stringa, è necessario specificare una precisione pari a -1 per i percorsi corrispondenti.

Gli indici spaziali utilizzano sempre la precisione di indice predefinita per tutti i tipi (punto LineString e poligono). La precisione di indice predefinita per gli indici spaziali non può essere sottoposto a override. 

Nell'esempio seguente viene illustrato come aumentare la precisione per gli indici di intervallo in una raccolta mediante .NET SDK. 

**Creare una raccolta con una precisione di indice personalizzata**

    var rangeDefault = new DocumentCollection { Id = "rangeCollection" };

    // Override the default policy for strings to Range indexing and "max" (-1) precision
    rangeDefault.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), rangeDefault);   


> [!NOTE]
> DB Cosmos Azure restituisce un errore quando una query utilizza ORDER BY, ma non dispone di un indice di intervallo rispetto al percorso della query con la massima precisione. 
> 
> 

Analogamente, è possibile escludere completamente percorsi dall'indicizzazione. Nell'esempio seguente viene illustrato come escludere un'intera sezione dei documenti (un *sottoalbero*) dall'indicizzazione mediante il \* carattere jolly.

    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*" });

    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);



## <a name="opt-in-and-opt-out-of-indexing"></a>Consente di partecipare e rifiutare esplicitamente l'indicizzazione
È possibile specificare se la raccolta deve indicizzare automaticamente tutti i documenti. Per impostazione predefinita, tutti i documenti vengono indicizzati automaticamente, ma è possibile disattivare l'indicizzazione automatica. Quando l'indicizzazione è disattivata, i documenti possono essere accessibili solo tramite i relativi self link o dalle query utilizzando il documento ID.

Con l'indicizzazione automatica disattivata, è comunque possibile aggiungere in modo selettivo solo documenti specifici all'indice. Al contrario, è possibile lasciare l'indicizzazione in automatico e scegliere in modo selettivo escludere i documenti specifici. L'indicizzazione o disattivare le configurazioni sono utili quando è presente solo un sottoinsieme di documenti che è necessario eseguire una query.

L'esempio seguente viene illustrato come includere un documento in modo esplicito utilizzando il [SQL API .NET SDK](https://docs.microsoft.com/azure/cosmos-db/sql-api-sdk-dotnet) e [RequestOptions.IndexingDirective](http://msdn.microsoft.com/library/microsoft.azure.documents.client.requestoptions.indexingdirective.aspx) proprietà.

    // If you want to override the default collection behavior to either
    // exclude (or include) a document in indexing,
    // use the RequestOptions.IndexingDirective property.
    client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"),
        new { id = "AndersenFamily", isRegistered = true },
        new RequestOptions { IndexingDirective = IndexingDirective.Include });

## <a name="modify-the-indexing-policy-of-a-collection"></a>Modificare i criteri di indicizzazione di una raccolta
Nel database Cosmos di Azure, è possibile apportare modifiche ai criteri di indicizzazione di una raccolta in tempo reale. Una modifica nei criteri per una raccolta di Azure Cosmos DB di indicizzazione comportano una modifica nella forma dell'indice. La modifica interessa i percorsi che è possibile indicizzare, la precisione e il modello di coerenza dell'indice stesso. Una modifica nei criteri di indicizzazione in modo efficace richiede una trasformazione di indice precedente in un nuovo indice. 

**Trasformazioni di indice online**

![Funzionamento dell'indicizzazione - Trasformazioni di indici online di Azure Cosmos DB](./media/indexing-policies/index-transformations.png)

Trasformazioni di indice sono online. Ciò significa che i documenti indicizzati per i criteri precedenti vengono trasformati in modo efficiente per il nuovo criterio *senza influenzare la disponibilità di scrittura o la velocità effettiva provisioning* della raccolta. La coerenza di lettura e scrittura di operazioni eseguite tramite l'API REST, SDK, o dalla stored procedure e trigger non subiscono modifiche durante la trasformazione dell'indice. Non è alcuna riduzione delle prestazioni o tempi di inattività per le app quando si esegue un criterio di indicizzazione di modifica.

Tuttavia, durante la fase di trasformazione dell’indice, le query sono alla fine coerenti indipendentemente dalla configurazione della modalità indicizzazione (coerente o differita). Questo vale anche per le query da tutte le interfacce: REST API, SDK e all'interno di stored procedure e trigger. Come con l'indicizzazione, Lazy indice trasformazione viene eseguita in modo asincrono in background alle repliche utilizzando le risorse di riserva che sono disponibili per una replica specifica. 

Trasformazioni di indice vengono eseguite anche sul posto. DB Cosmos Azure non mantiene due copie dell'indice ed estrarre l'indice precedente con quello nuovo. Ciò significa che nessun ulteriore spazio su disco richiesto o utilizzato in raccolte mentre si verificano trasformazioni di indice.

Quando si modificano i criteri di indicizzazione, le modifiche vengono applicate per spostare dall'indice precedente nella nuova dipende principalmente le configurazioni di modalità di indicizzazione. Le configurazioni della modalità di indicizzazione svolgono un ruolo più grande di altri valori come percorsi inclusi o esclusi, i tipi di indice e precisione. 

Se i criteri nuovi e precedenti Usa indicizzazione coerente, Azure Cosmos DB esegue una trasformazione di un indice online. Non è possibile applicare un'altra modifica criteri di indicizzazione con la modalità di indicizzazione coerente durante la trasformazione non è in corso. Tuttavia, è possibile spostare in Lazy o nessuna modalità di indicizzazione durante una trasformazione è in corso: 

* Quando si sposta in Lazy, la modifica di criteri di indice viene applicata immediatamente. DB Cosmos Azure avvia ricreando l'indice in modo asincrono. 
* Quando si sposta su Nessuno, l'indice viene eliminato immediatamente. Nessuno spostamento è utile quando si desidera annullare una trasformazione in corso e iniziare con un criterio di indicizzazione diversi. 

Frammento di codice seguente viene illustrato come modificare i criteri di indicizzazione di una raccolta dalla modalità di indicizzazione coerente per la modalità di indicizzazione Lazy. Se si utilizza .NET SDK, è possibile avviare una modifica dei criteri di indicizzazione utilizzando il nuovo **ReplaceDocumentCollectionAsync** metodo.

**Modificare i criteri di indicizzazione di consistenza per Lazy**

    // Switch to Lazy indexing mode.
    Console.WriteLine("Changing from Default to Lazy IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.Lazy;

    await client.ReplaceDocumentCollectionAsync(collection);

**Tenere traccia dell'avanzamento della trasformazione di indice**

È possibile monitorare lo stato di percentuale della trasformazione di indice per un indice coerenza con il **IndexTransformationProgress** proprietà risposta da un **ReadDocumentCollectionAsync** chiamare. Altri SDK e l'API REST supportano metodi e proprietà equivalenti per apportare modifiche ai criteri di indicizzazione. È possibile controllare lo stato di avanzamento di una trasformazione di indice per un indice coerenza chiamando **ReadDocumentCollectionAsync**: 

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
> * Il **IndexTransformationProgress** proprietà è applicabile solo quando la trasformazione in un indice coerenza. Utilizzare il **ResourceResponse.LazyIndexingProgress** proprietà per tenere traccia delle trasformazioni in un indice Lazy.
> * Il **IndexTransformationProgress** e **LazyIndexingProgress** le proprietà vengono popolate solo per una raccolta non è partizionata, vale a dire una raccolta che viene creata senza una chiave di partizione.
>

È possibile eliminare l'indice per una raccolta passando alla modalità di indicizzazione nessuna. Potrebbe trattarsi di un utile strumento operativo se si desidera annullare una trasformazione in corso e quindi avviare immediatamente uno nuovo.

**Eliminare l'indice per una raccolta**

    // Switch to Lazy indexing mode.
    Console.WriteLine("Dropping index by changing to to the None IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.None;

    await client.ReplaceDocumentCollectionAsync(collection);

Quando è necessario apportare modifiche ai criteri di indicizzazione per le raccolte di Azure Cosmos DB? I seguenti casi rappresentano la maggior parte dei casi di utilizzo:

* Fornire risultati coerenti durante il normale funzionamento, ma eseguire il fallback alla modalità di indicizzazione Lazy durante l'importazione di dati bulk.
* Iniziare a usare le nuove funzionalità di indicizzazione su raccolte DB Cosmos Azure corrente. Ad esempio, possibile utilizzare la query geospaziali, che richiede il tipo di indice spaziale, o ORDER BY / stringa di query di intervallo, che richiedono la stringa di tipo di intervallo di indice.
* Selezionare manualmente le proprietà da indicizzare e modificarle nel tempo.
* Ottimizzare l'indicizzazione di precisione per migliorare le prestazioni delle query o per ridurre spazio di archiviazione utilizzato.

> [!NOTE]
> Per modificare i criteri di indicizzazione utilizzando **ReplaceDocumentCollectionAsync**, è necessario utilizzare versione 1.3.0 o versioni successive di .NET SDK.
> 
> Per la trasformazione di indice per cui viene completata correttamente, verificare che sia sufficiente spazio di archiviazione disponibile per la raccolta. Se la raccolta raggiunge la quota di archiviazione, la trasformazione di indice è stato sospeso. Trasformazione indice riprende automaticamente quando lo spazio di archiviazione è disponibile, ad esempio, se si eliminano alcuni documenti.
> 
> 

## <a name="performance-tuning"></a>Ottimizzazione delle prestazioni
Le API SQL forniscono informazioni sulle metriche delle prestazioni, ad esempio l'archiviazione dell'indice utilizzato e il costo di velocità effettiva (unità di richiesta) per ogni operazione. È possibile utilizzare queste informazioni per confrontare i vari criteri di indicizzazione e per l'ottimizzazione delle prestazioni.

Per controllare la quota di archiviazione e l'utilizzo di una raccolta, eseguire una **HEAD** o **ottenere** richiesta nei confronti della risorsa della raccolta. Quindi, controllare il **x-ms-richiesta di quota** e **x-ms-richiesta-usage** intestazioni. In .NET SDK le proprietà [DocumentSizeQuota](http://msdn.microsoft.com/library/dn850325.aspx) e [DocumentSizeUsage](http://msdn.microsoft.com/library/azure/dn850324.aspx) di [ResourceResponse<T\>](http://msdn.microsoft.com/library/dn799209.aspx) contengono questi valori corrispondenti.

     // Measure the document size usage (which includes the index size) against   
     // different policies.
     ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));  
     Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);


Per misurare l'overhead di indicizzazione in ogni operazione di scrittura (creare, aggiornare o eliminare), controllare il **addebito x-ms-richiesta** intestazione (o equivalente [RequestCharge](http://msdn.microsoft.com/library/dn799099.aspx) proprietà [ ResourceResponse < T\> ](http://msdn.microsoft.com/library/dn799209.aspx) in .NET SDK) per misurare il numero di unità di richiesta vengono utilizzati da queste operazioni.

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
7 luglio 2015, l'API REST versione 2015-06-03 è stata introdotta una modifica nello schema per criteri di indicizzazione. Le relative classi nelle versioni SDK hanno nuove implementazioni per garantire la corrispondenza con lo schema. 

Nella specifica JSON sono state implementate le modifiche seguenti:

* Criteri di indicizzazione supportano gli indici dell'intervallo per le stringhe.
* Ogni percorso può avere più definizioni di indice. Ciò può avere uno per ogni tipo di dati.
* L'indicizzazione di precisione supporta 1 a 8 per i numeri, tra 1 e 100 per le stringhe e -1 (la precisione massima).
* Segmenti di percorso non richiedono una virgoletta doppia per ogni percorso di escape. Ad esempio, è possibile aggiungere un percorso per   **/titolo /?** invece di **/ "title" /?**.
* Il percorso radice che rappresenta "tutti i percorsi" può essere rappresentato come  **/ \***  (oltre a  **/** ).

Se si dispone di codice che esegue il provisioning di raccolte con un criterio di indicizzazione personalizzato scritto con versione 1.1.0 del SDK .NET o una versione precedente, per spostare in SDK versione 1.2.0, è necessario modificare il codice dell'applicazione per gestire queste modifiche. Se non si dispone di codice che consente di configurare criteri di indicizzazione, o se si prevede di continuare a usare una versione precedente del SDK, non sono necessarie modifiche.

Per un confronto pratico, ecco un esempio di un criterio di indicizzazione personalizzato scritto tramite API REST versione 2015-06-03, aggiungendo gli stessi criteri di indicizzazione scritto utilizzando l'API REST di precedente versione 2015-04-08.

**JSON (API REST versione 2015-06-03) di criteri di indicizzazione corrente**

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


**In precedenza l'indicizzazione JSON (API REST versione 2015-04-08) dei criteri**

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
Per esempi di gestione criteri di indice e per altre informazioni sul linguaggio di query di database Cosmos di Azure, vedere i collegamenti seguenti:

* [Esempi di codice SQL API .NET indice management](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/IndexManagement/Program.cs)
* [Operazioni di raccolta SQL API REST](https://msdn.microsoft.com/library/azure/dn782195.aspx)
* [Query con SQL](sql-api-sql-query.md)

