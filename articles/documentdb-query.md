<properties title="Query with Azure DocumentDB" pageTitle="Query with DocumentDB | Azure" description="DocumentDB's SQL query language supports a subset of ANSI SQL grammar and adds document-oriented support. Queries are served through up-to-date indexes that don't require index management."  metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="bradsev" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="bradsev" />

# Query con Azure DocumentDB

## Motivazione

Il linguaggio di query SQL di DocumentDB supporta un sottoinsieme di grammatica SQL ANSI e aggiunge un supporto orientato ai documenti sotto forma di strutture di dati annidate, matrici e costruzione di oggetti. Le query vengono fornite in modo efficiente tramite gli indici di DocumentDB, che vengono automaticamente mantenuti aggiornati per tutte le proprietà nei documenti senza il sovraccarico dovuto alla gestione degli indici. Gli sviluppatori sono anche in grado di liberate la piena potenza di JavaScript all'interno delle query tramite funzioni definite dall'utente.

## Perché query SQL?

DocumentDB supporta SQL per fornire un'interfaccia semplice e pratica per l'esecuzione di query sui dati. SQL come linguaggio è strutturalmente semplice ma tuttavia potente e viene largamente impiegato dagli sviluppatori di applicazioni. DocumentDB è strettamente vincolato a piattaforme e standard aperti quali HTTP, JSON, JavaScript e ora anche SQL.
L'esecuzione di query SQL è supportata tramite l'API REST così come lo sono .NET, Node.js e gli SDK del client JavaScript. Le query SQL sono anche supportate nelle stored procedure e nei trigger nell'API JavaScript lato server. Oltre alle query SQL, DocumentDB supporta inoltre l'esecuzione di query tramite LINQ in .NET SDK.

# Tipi di dati

In DocumentDB il formato dei dati per documenti e metadati viene espresso in JavaScript Object Notation (JSON). JSON viene largamente impiegato a causa della crescita delle applicazioni JavaScript, nonché grazie alla sua evoluzione in un formato di scambio di dati indipendente dalla piattaforma. Poiché JSON è autodescrittivo, risulta anche particolarmente adatto ai database liberi da schema.

I tipi di dati primitivi supportati nelle query di DocumentDB sono gli stessi di JSON. JSON presenta un semplice sistema di tipi che consiste in:

-   Stringhe
-   Numeri (IEEE754 precisione doppia)
-   Booleani - True e False.
-   Null

È possibile rappresentare tipi di dati più complessi sia in JSON che in DocumentDB tramite la creazione di oggetti annidati usando l'operatore { } e di matrici usando l'operatore [ ].

# Grammatica di query

Usando il linguaggio SQL di DocumentDB, gli utenti possono recuperare dati da documenti JSON usando istruzioni **SELECT**. Questa è un'istruzione SQL di esempio su una raccolta di documenti contenente post su un social network.

     SELECT p.message, p.user.id AS user_id, p.tags[0] AS first_tag 
     FROM posts p 
     WHERE p.type = "Comment"

L'output di una query è una matrice di frammenti di documenti JSON, uno per ogni documento che corrisponde ai vincoli specifici.

    {
      "Documents": [
        {
            "message": "JSON rocks!"
            "user_id": "@documentdb"
            "first_tag": "#documentstore"
        },
        {
            "message": "SQL rocks!"
            "user_id": "@documentdb"
            "first_tag": "#documentstore"
        }
      ]
    }

La grammatica formale completa (BNF) per il linguaggio di query SQL è inclusa alla fine di questo documento.

# Clausola FROM

La clausola **FROM** è in genere il nome della raccolta attuale su cui viene eseguita la query. Ad esempio, quanto segue seleziona tutti i documenti nella raccolta denominata "posts". Se il nome non è corretto, viene restituito un errore all'utente.

    SELECT * 
    FROM posts

In alternativa è possibile usare la parola chiave **ROOT** per fare riferimento alla raccolta in base al nome, poiché nell'SDK o nell'API REST le query possono essere indirizzate all'ambito di una raccolta specifica.

    SELECT * 
    FROM ROOT

È possibile effettuare l'aliasing delle proprietà di output della clausola FROM. Ciò è utile quando si usano più origini FROM con **JOIN**. Questa circostanza viene descritta più avanti.

    SELECT P.* 
    FROM posts P

È anche possibile eseguire query da una proprietà annidata. Se ad esempio i documenti di post contengono un sottodocumento per l'utente che ha creato il post, è possibile eseguire una query usando la sintassi seguente.

    SELECT U.* 
    FROM posts.user U

L'origine dell'istruzione FROM può essere anche una matrice scalare (set ordinato).

    SELECT E
    FROM ["documentdb", "json", "sql"] E

È anche possibile omettere completamente l'istruzione FROM durante la valutazione di espressioni scalari.

`SELECT "documentdb"`

# Clausola WHERE (filtraggio)

Il linguaggio SQL di DocumentDB supporta una clausola **WHERE** opzionale che filtra il set di risultati in base alle condizioni specificate, che devono essere soddisfatte dal documento. L'espressione di filtro può contenere confronti logici con qualsiasi proprietà o percorso annidato nei documenti.

Ad esempio, nelle query è possibile usare qualsiasi messaggio con caratteristiche di proprietà. Il filtraggio viene eseguito in modo efficiente usando l'indicizzazione automatica di DocumentDB.

    SELECT * 
    FROM posts p 
    WHERE p.message = "DocumentDB now supports SQL!"

Le proprietà annidate possono essere usate per il filtraggio usando la notazione con punti. La lunghezza dei percorsi può essere arbitraria a seconda dello schema dei documenti.

    SELECT * 
    FROM posts p 
    WHERE p.user.name = "Azure DocumentDB"

È possibile fare riferimento ai valori individuali all'interno delle matrici usando l'operatore di dereferenziazione della matrice. La rimozione di matrici è supportata tramite la parola chiave **IN** (descritta in seguito).

    SELECT * 
    FROM posts p 
    WHERE p.views[0] = 100

I confronti di intervallo (\>, \<. \>=, \<=, !=) sono supportati per i valori numerici. I numeri possono anche essere confrontati al risultato di un'espressione aritmetica come illustrato di seguito.

    SELECT * 
    FROM posts p 
    WHERE p.views[0] > 100 * 2

Gli operatori logici possono essere combinati usando operatori AND, OR e NOT, proprio come nell'SQL standard. Questi vengono elaborati efficientemente tramite l'intersezione degli indici per le proprietà specificate.

    SELECT * 
    FROM posts p 
    WHERE ((p.user.name = "Azure DocumentDB")
    OR (p.views[0] = 100 AND p.message = "DocumentDB now supports SQL!"))

## Gestione di proprietà null o mancanti

NULL è supportato come parola chiave e le proprietà possono essere confrontate a null come altri elementi scalari.

    SELECT * 
    FROM posts p 
    WHERE p.message IS NULL

Siccome lo schema per i documenti può mutare rapidamente o non essere noto in anticipo, il linguaggio supporta anche operatori speciali per controllare proprietà mancanti, tramite l'operatore ISUNDEFINED, che è simile a **undefined** in JavaScript.

    SELECT * 
    FROM posts p 
    WHERE p.message IS UNDEFINED

> Nota: analogamente alla versione JavaScript, = undefined restituisce sempre false.

# Clausola SELECT (proiezione)

La clausola **SELECT** può essere usata per estrarre certe proprietà dalla query. L'operatore asterisco (\*) restituisce i documenti completi corrispondenti alla query.

    SELECT * 
    FROM posts

I campi specificati possono essere estratti nella clausola SELECT. I campi possono essere atomici (stringhe, numeri, booleani) o complessi (matrici, oggetti JSON). I tipi complessi nella clausola SELECT, come "user" nell'esempio qui sotto, restituiscono l'intero oggetto JSON sotto la proprietà. Le proprietà nella clausola SELECT devono avere nomi o alias univoci.

    SELECT posts.id, posts.user
    FROM posts

> Nota: poiché JSON non è ordinato, le proprietà individuali nel documento non vengono restituite in alcun ordine specifico.

> Nota: i nomi di campo devono essere qualificati in modo esplicito. Poiché i documenti non hanno uno schema fisso, questo è necessario al runtime di query per eseguire correttamente il binding.

Un modo alternativo di accedere alle proprietà è dato dall'uso della sintassi di ricerca nel dizionario come illustrato di seguito:

    SELECT posts.id, posts["user"], posts["from"]
    FROM posts

> Nota: la sintassi di ricerca nel dizionario va usata per eseguire l'escape di nomi di proprietà che usano parole chiave riservate come FROM nell'esempio sopra.

## Valutazione di espressioni

Con le espressioni è possibile usare anche espressioni scalari. Quando non è specificato nessun nome, viene usato un nome generato automaticamente come segnaposto ($1, $2, $3 e così via).

    SELECT ((2 + 11 % 7) – 2)/3
    FROM posts

Le espressioni seguenti possono essere usate all'interno delle espressioni di query SQL. Gli operatori sono fortemente tipizzati, come in SQL. Il segno di uguaglianza (=), ad esempio, esprime uguaglianza forte, il che significa che 5 != "5".

|---------------------------|--------------------------------------------------------------|
| Operatori aritmetici      | +, -, \*, /, % (modulo)                                      |
| Operatori logici          | AND, OR, NOT                                                 |
| Operatori bit per bit     | & (AND bit per bit), | (OR bit per bit), ^ (XOR bit per bit) |
| Operatori di tipo stringa | || (concatenazione)                                          |
| Operatori di confronto    | =, !=, \>, \<, \>=, \<=                                      |

## Trasformazione JSON

Le query SQL possono restituire frammenti JSON e non solo documenti completi. A tale scopo è possibile usare la parola chiave **VALUE**. Il seguente documento, ad esempio, restituirà "2" invece di {"id": "2"}.

    SELECT VALUE posts.id
    FROM posts

È possibile costruire espressioni JSON più complesse come in JavaScript usando gli operatori {} e []. Ciò consente flessibilità di query per trasformare l'aspetto dei set di risultati.

    SELECT {"user_name": p.user.name, "recent_views": [p.views[0], p.views[1]]}
    FROM posts p

> Nota: i valori possono essere qualsiasi espressione valida ma le etichette devono essere valori letterali (stringhe).

# Join e iterazione

Nei database di documenti, i dati cui viene fatto riferimento vengono incorporati come sottodocumenti o array interni. Il linguaggio SQL, pertanto, supporta l'uso di intradocumenti, o "self-join", tramite la parola chiave **JOIN**.

    SELECT p.id, tag 
    FROM posts p 
    JOIN tag IN p.tags

> Nota: i join sono formalmente cross join, ma poiché i dati sono incorporati, essi agiscono come inner join.

Nell'esempio, l'operatore **IN** viene usato per iterare su tutti gli elementi di un'origine. L'origine di una clausola IN può essere un array o un oggetto. Quando usato con un oggetto, l'iteratore valuta ogni proprietà. IN può essere anche usato direttamente come illustrato di seguito. IN può essere usato anche con valori letterali della matrice.

    SELECT tag 
    FROM tag IN posts.tags

È possibile usare più join all'interno di un'istruzione SQL, come illustrato di seguito:

    SELECT p.id, tag, liked
    FROM posts p 
    JOIN tag IN p.tags
    JOIN liked IN p.likes  

# Funzioni definite dall'utente

Le query di DocumentDB supportano estensioni programmabili sotto forma di funzioni JavaScript definite dall'utente. In ogni raccolta è possibile salvare una o più **funzioni definite dall'utente** per la trasformazione di frammenti JSON e fare riferimento a tali funzioni per nome, direttamente negli script.

Ad esempio, usando le funzioni di creazione definite dall'utente, è possibile definire una funzione "regex\_match" come segue:

    function regex_match(input, pattern) {
        getContext().getResponse().setBody(pattern.test(input));
    }

È quindi possibile farvi riferimento nelle query come nell'esempio seguente:

    SELECT *
    FROM posts p
    WHERE regex_match(p.message, "doc.*db") = true

> Nota: le funzioni definite dall'utente hanno accesso unicamente ai parametri di input. All'interno delle funzioni definite dall'utente non sono supportate operazioni di DocumentDB relative all'archiviazione (lettura, scrittura, query e così via).

> Nota: attualmente, all'interno di una query può essere usata una sola funzione definita dall'utente.

Le funzioni definite dall'utente possono elaborare e restituire frammenti JSON e non solo valori letterali. Una funzione "array\_count", ad esempio, può essere definita come segue:

    function array_count(input) {
        getContext().getResponse().setBody(input.length);
    }

La funzione può quindi essere usata nelle query per trovare le dimensioni di una matrice all'interno di un documento:

    SELECT *
    FROM posts p
    WHERE array_count(p.likes) > 5

Le funzioni definite dall'utente possono essere specificate nella clausola SELECT o FROM di una query. Ad esempio:

    SELECT array_count(p.likes) AS count
    FROM posts p

Per altre informazioni sulla creazione e la gestione delle funzioni definite dall'utente, vedere la documentazione relativa alla programmabilità di JavaScript.

# Paging

Ogni esecuzione di query restituisce un batch di risultati in base alle dimensioni in pagine configurate dal client. Per leggere tutti i risultati di una query, le applicazioni devono impaginare ogni set di risultati fino al punto in cui non vi sono più dati da leggere. Per leggere non più di 10 documenti, ad esempio, i client devono modificare le dimensioni relative alle pagine in 10 per limitare il numero massimo di documenti restituiti. Si noti che alcune query possono restituire meno risultati di quanti indicati dalle dimensioni in pagine o addirittura nessun risultato. Per leggere tutti i risultati da una query, i client devono ottenere il batch successivo usando il token di continuazione della risposta fino al suo svuotamento.

    // Fetch pages of results up to 10 at a time. FeedOptions is optional
    DocumentServiceQuery<Database> query = (
        from db in client.CreateDatabaseQuery(new FeedOptions { MaxItemCount = 10 })
        where db.Name == dbName
        select db).AsDocumentServiceQuery();

    while (query.HasMoreResults)
    {
        databases.AddRange(await query.ExecuteNextAsync<Database>());
    }

Ciò non è implementato nella grammatica SQL come parola chiave TOP o LIMIT, perché la funzionalità è già disponibile tramite token di continuazione nell'SDK/API REST del client sottostante. Per altre informazioni ed esempi, fare riferimento alla documentazione relativa alle API REST per i documenti GET o ai metodi ReadFeed negli SDK.

# Comportamento di coerenza delle query

DocumentDB supporta quattro livelli di coerenza ottimizzabili dagli sviluppatori: assoluta, con obsolescenza associata, sessione o finale. Le query offrono le stesse garanzie fornite dai livelli di coerenza. Per impostazione predefinita, per i risultati di una query è garantita la corrispondenza con il livello di coerenza richiesto dal client. L'indice di DocumentDB è strutturato su log e progettato per essere mantenuto sempre aggiornato e coerente con i dati, a prescindere dal volume. Quando i documenti vengono inseriti o aggiornati, questi sono immediatamente disponibili nei risultati delle query.

Per le applicazioni che richiedono coerenza finale, il criterio di indicizzazione può facoltativamente essere configurato per usare l'indicizzazione differita. In questo caso, l'indice viene aggiornato in modo coerente ogni qualvolta la raccolta si trovi in stato di inattività. Per riassumere, di seguito è riportata una tabella dei comportamenti di coerenza delle query con le diverse configurazioni degli account di database.

| Coerenza dei dati      | Criterio di indicizzazione | Comportamento query    |
|------------------------|----------------------------|------------------------|
| Assoluta               | Coerente                   | Assoluta               |
| Obsolescenza associata | Coerente                   | Obsolescenza associata |
| Sessione               | Coerente                   | Sessione               |
| Finale                 | Coerente                   | Finale                 |
| Assoluta               | Differita                  | Finale                 |
| Obsolescenza associata | Differita                  | Finale                 |
| Sessione               | Differita                  | Finale                 |
| Finale                 | Differita                  | Finale                 |

Per altri dettagli, vedere la documentazione sui criteri e sulla configurazione dell'indicizzazione.

# API e SDK

Le query possono essere eseguite mediante l'API REST, gli SDK del client nonché l'API JavaScript lato server, usando la grammatica SQL.

## Query tramite API REST

Le applicazioni possono eseguire il POST di query su raccolte che usano l'API REST. Nelle richieste di query devono essere contenuti gli elementi seguenti:

-   Intestazione x-ms-documentdb-isquery: true per indicare che è presente una query
-   Intestazione Content-Type: applicazione/sql per indicare che viene usato il linguaggio SQL
-   Corpo contenente l'istruzione SELECT
    <!-- -->

    POST .../docs/executeQuery HTTP/1.1
    authorization: ...
    x-ms-continuation:
    x-ms-activity-id: 82342881-769e-4113-a662-a85c7617ed5b
    x-ms-date: Fri, 30 May 2014 22:46:13 GMT
    Match:
    x-docdb-resource-id: 9MEKcum9C2g=
    x-docdb-entity-id:
    x-ms-documentdb-isquery: True
    Cache-Control: no-cache
    x-ms-version: 2014-02-25
    User-Agent: Microsoft.Azure.Documents.Client/1.0.0.0
    Content-Type: application/sql
    Host: ...
    Content-Length: 59
    Expect: 100-continue

    SELECT b.title FROM books b WHERE b.title = 'War and Peace'

> Nota: query non supportata con uso di GET o di stringhe di query.

## Query tramite .NET SDK

.NET SDK supporta l'esecuzione di query con uso del metodo CreateDocumentQuery, che supporta query SQL come stringhe. L'output della query è un'interfaccia IQueryable che può essere elaborata usando LINQ sul lato client.

    IQueryable<dynamic> results = client.CreateDocumentQuery(collectionId).AsSQL<dynamic>(
       "SELECT b.title FROM books b WHERE b.title = 'War and Peace'");

Per altre informazioni, vedere la documentazione di .NET SDK.

## Query all'interno di stored procedure e trigger

Le query SQL sono anche supportate nelle stored procedure e nei trigger nell'API JavaScript lato server.

    collection.queryDocuments(collection.GetSelfLink(),
    "SELECT b.title FROM books b WHERE b.title = 'War and Peace'",
    callback);

Per altre informazioni, vedere la documentazione relativa all'SDK JavaScript lato server.

# Appendice A - Sintassi SQL

Nei diagrammi a ferrovia seguenti viene illustrata la grammatica SQL formale per linguaggio di query di DocumentDB.

![][0]

![][1]

![][2]

![][3]

![][4]

![][5]

![][6]

![][7]

![][8]

![][9]

![][10]

![][11]

![][12]

![][13]

![][14]

![][15]

![][16]

  [0]: ./media/documentdb-query/query1.png
  [1]: ./media/documentdb-query/query2.png
  [2]: ./media/documentdb-query/query3.png
  [3]: ./media/documentdb-query/query4.png
  [4]: ./media/documentdb-query/query5.png
  [5]: ./media/documentdb-query/query6.png
  [6]: ./media/documentdb-query/query7.png
  [7]: ./media/documentdb-query/query8.png
  [8]: ./media/documentdb-query/query9.png
  [9]: ./media/documentdb-query/query10.png
  [10]: ./media/documentdb-query/query11.png
  [11]: ./media/documentdb-query/query12.png
  [12]: ./media/documentdb-query/query13.png
  [13]: ./media/documentdb-query/query14.png
  [14]: ./media/documentdb-query/query15.png
  [15]: ./media/documentdb-query/query16.png
  [16]: ./media/documentdb-query/query17.png
