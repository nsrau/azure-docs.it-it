<properties 
	pageTitle="Ordinamento dei dati di DocumentDB tramite Order By | Azure" 
	description="Come utilizzare ORDER BY nelle query di DocumentDB in LINQ ed SQL e come specificare un criterio di indicizzazione per le query ORDER BY." 
	services="documentdb" 
	authors="arramac" 
	manager="jhubbard" 
	editor="cgronlun" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="arramac"/>

# Ordinamento dei dati di DocumentDB tramite Order By
Microsoft Azure DocumentDB supporta l'esecuzione di query di documenti mediante SQL su documenti JSON. È possibile ordinare i risultati della query utilizzando la clausola ORDER BY in istruzioni di query SQL.

Dopo la lettura di questo articolo, si potrà rispondere alle domande seguenti:

- Come si eseguono query con Order By?
- Come si configura un criterio di indicizzazione per Order By?
- Novità successive

Vengono inoltre forniti [esempi](#samples) e [domande frequenti](#faq).

Per informazioni complete sull'esecuzione di query SQL, vedere l’[esercitazione sulle query di DocumentDB](documentdb-sql-query.md).

## Come eseguire query con Order By?
Come in SQL ANSI, è ora possibile includere una clausola Order By facoltativa nelle istruzioni SQL quando si eseguono query di DocumentDB. La clausola può includere un argomento ASC/DESC facoltativo per specificare l'ordine in cui i risultati devono essere recuperati.

### Ordinamento tramite SQL
Ad esempio, ecco una query per recuperare i libri in ordine decrescente di PublishTimestamp.

    SELECT * 
    FROM Books 
    ORDER BY Books.PublishTimestamp DESC

### Ordinamento tramite SQL con filtro
È possibile ordinare utilizzando qualsiasi proprietà annidata all'interno di documenti, ad esempio Books.ShippingDetails.Weight, ed è possibile specificare filtri aggiuntivi nella clausola WHERE in combinazione con Order By come in questo esempio:

    SELECT * 
    FROM Books 
	WHERE Books.SalePrice > 4000
    ORDER BY Books.ShippingDetails.Weight

### Ordinamento tramite il provider LINQ per .NET
Utilizzando .NET SDK versione 1.2.0 e successive, è anche possibile utilizzare la clausola OrderBy() o OrderByDescending() all'interno di query LINQ come in questo esempio:

    foreach (Book book in client.CreateDocumentQuery<Book>(booksCollection.SelfLink)
        .OrderBy(b => b.PublishTimestamp)) 
    {
        // Iterate through books
    }

### Ordinamento con paging tramite .NET SDK
Utilizzando il supporto nativo di paging all'interno degli SDK di DocumentDB, è possibile recuperare una pagina di risultati alla volta come nel seguente frammento di codice .NET. Nell’esempio sono stati recuperati fino a 10 risultati alla volta utilizzando FeedOptions.MaxItemCount e l'interfaccia IDocumentQuery.

    var booksQuery = client.CreateDocumentQuery<Book>(
        booksCollection.SelfLink,
        "SELECT * FROM Books ORDER BY Books.PublishTimestamp DESC"
        new FeedOptions { MaxItemCount = 10 })
      .AsDocumentQuery();
            
    while (booksQuery.HasMoreResults) 
    {
        foreach(Book book in await booksQuery.ExecuteNextAsync<Book>())
        {
            // Iterate through books
        }
    }

DocumentDB supporta l'ordinamento su tipi numerici (non stringhe) e solo per una singola proprietà Order By per query in questa versione di anteprima della funzionalità. Vedere [Novità successive](#Whats_coming_next) per ulteriori dettagli.

## Configurare un criterio di indicizzazione per Order By
Per eseguire query Order By, è necessario:

- Indicizzare percorsi specifici all'interno di documenti con precisione massima, oppure 
- Indicizzare *tutti* i percorsi in modo ricorsivo per l'intera raccolta con precisione massima 

La precisione massima (rappresentata come precisione pari a -1 nel file di configurazione JSON) utilizza un numero variabile di byte che dipende dal valore che si sta indicizzando. Di conseguenza:

- Le proprietà con i valori numerici più grandi come nel caso di epoch timestamp, avranno un elevato sovraccarico dell’indice. 
- Le proprietà con valori numerici più piccoli (enumerazioni, zeri, codici postali, età e così via) avranno un sovraccarico minimo dell’indice.

Per ulteriori informazioni vedere [Criteri di indicizzazione di DocumentDB](documentdb-indexing-policies.md).

### Indicizzazione per Order By su tutte le proprietà numeriche
Ecco come creare una raccolta con l'indicizzazione per Order By su qualsiasi proprietà (numerica).
                   

    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IndexingPath {
            IndexType = IndexType.Range, 
            Path = "/",
            NumericPrecision = -1 });

    await client.CreateDocumentCollectionAsync(databaseLink, 
        booksCollection);  

### Indicizzazione per Order By per una singola proprietà
Ecco come creare una raccolta per l’indicizzazione per Order By rispetto alla proprietà PublishTimestamp.

    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IndexingPath {
            IndexType = IndexType.Range,
            Path = "/"PublishTimestamp"/?",
            NumericPrecision = -1
        });

    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IndexingPath {
            Path = "/"
        });


## Esempi
Esaminare il [progetto di esempio Github](https://github.com/Azure/azure-documentdb-net/tree/master/samples/orderby) che illustra come utilizzare Order By e come creare criteri di indicizzazione e paging tramite Order By. Gli esempi sono open source e si consiglia di inviare richieste pull con contributi che potrebbero essere utili ad altri sviluppatori DocumentDB. Per informazioni su come contribuire, fare riferimento alle [linee guida specifiche](https://github.com/Azure/azure-documentdb-net/blob/master/Contributing.md).

## Novità successive

Aggiornamenti futuri del servizio espanderanno il supporto Order By introdotto qui. Sono in corso di sviluppo le seguenti aggiunte e le priorità di rilascio saranno stabilite in base al feedback degli utenti:

- Criteri di indicizzazione dinamica: possibilità di modificare il criterio di indicizzazione dopo la creazione della raccolta
- Indici di intervalli stringhe: indice per supportare le query di intervallo (>, <,>, =, < =) con valori stringa. Per supportare questa opzione, verrà introdotto un nuovo schema più completo per l'indicizzazione dei criteri.
- Supporto per la stringa Order By nella query di DocumentDB.
- Possibilità di aggiornare il criterio di indicizzazione tramite il portale di anteprima di Azure.
- Supporto per gli indici composti per un uso più efficiente di Order By e Order By su più proprietà.

## Domande frequenti

**Quali piattaforme/versioni di SDK supporta l'ordinamento?**

Poiché la clausola Order By è un aggiornamento sul lato server, non è necessario scaricare una nuova versione di SDK per utilizzare questa funzionalità. Tutte le piattaforme e versioni di SDK, incluso SDK JavaScript sul lato server, possono utilizzare Order By utilizzando stringhe di query SQL. Se si utilizza LINQ, è necessario scaricare la versione 1.2.0 o successiva da Nuget.

**Qual è il consumo di richiesta di unità (RU) previsto delle query Order By?**

Poiché Order By utilizza l'indice di DocumentDB per le ricerche, il numero di unità di richiesta utilizzate dalle query Order By sarà simile alle query equivalenti senza Order By. Come qualsiasi altra operazione in DocumentDB, il numero di unità di richiesta dipende dalle dimensioni/forme dei documenti, nonché dalla complessità della query.


**Qual è il sovraccarico di indicizzazione previsto per Order By?**

Il sovraccarico di indicizzazione sarà proporzionale al numero di proprietà numeriche. Nel peggiore dei casi, il sovraccarico dell’indice sarà pari al 100% dei dati. Non esiste alcuna differenza di sovraccarico della velocità effettiva (unità di richiesta) tra indicizzazione di intervallo/Order By e indicizzazione hash predefinita.

**Questa modifica influisce sulle query senza Order By?**

Non sono state introdotte modifiche nel funzionamento delle query senza Order By. Prima del rilascio di questa funzionalità, tutte le query di DocumentDB restituivano risultati in ordine ResourceId (_rid). Con Order By, le query verranno restituite naturalmente nell'ordine specificato di valori. Nelle query Order By _rid verrà utilizzato come sequenza di ordinamento secondaria quando esistono più documenti con lo stesso valore.

**Come si esegue una query sui dati esistenti in DocumentDB tramite Order By?**

Questa funzionalità sarà supportata con il miglioramento dei criteri di indicizzazione dinamica indicati nella sezione [Novità successive](what's-coming-next). Attualmente per ottenere questo scopo è necessario esportare i dati e reimportarli in una nuova raccolta di DocumentDB creata con un indice di intervallo/Order By. Lo strumento di importazione di DocumentDB può essere utilizzato per migrare i dati tra le raccolte.

**Quali sono le limitazioni attuali di Order By?**

Order By può essere specificata solo su una proprietà numerica e solo in caso di intervallo indicizzato con precisione massima (-1). Order By è supportata solo sulle raccolte di documenti.

Non è possibile usare Order By:
 
- Con proprietà di stringa (sarà possibile a breve).
- Con proprietà di stringa interne come id, _rid e _self (sarà possibile a breve).- Con proprietà derivate dal risultato di un join all'interno del documento (sarà possibile a breve).
- Con proprietà multiple stringa (sarà possibile a breve).
- Con proprietà calcolate, ad esempio il risultato di un'espressione o una funzione definita dall'utente/incorporata.
- Con le query su database, raccolte, utenti, autorizzazioni o allegati.

## Passaggi successivi

Esaminare il [progetto di esempio Github](https://github.com/Azure/azure-documentdb-net/tree/master/samples/orderby) e iniziare a ordinare i dati.

## Riferimenti
* [Nozioni di base sulle query di DocumentDB](documentdb-sql-query.md)
* [Nozioni di base sui criteri di indicizzazione di DocumentDB](documentdb-indexing-policies.md)
* [Riferimento SQL di DocumentDB](https://msdn.microsoft.com/library/azure/dn782250.aspx)
* [Esempi di Order By di DocumentDB](https://github.com/Azure/azure-documentdb-net/tree/master/samples/orderby)
 

<!---HONumber=62-->