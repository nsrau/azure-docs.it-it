---
redirect_url: https://azure.microsoft.com/services/documentdb/
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: d59ebef3cda36ba048b844f0cd2326fff66b4aa5
ms.openlocfilehash: c5fc68fb25a4cb2166e0c0a72871d16c71397852


---
# <a name="sorting-documentdb-data-using-order-by"></a>Ordinamento dei dati di DocumentDB tramite Order By
Microsoft Azure DocumentDB supporta l'esecuzione di query di documenti mediante SQL su documenti JSON. È possibile ordinare i risultati della query utilizzando la clausola ORDER BY in istruzioni di query SQL.

Dopo la lettura di questo articolo, si potrà rispondere alle domande seguenti: 

* Come si eseguono query con Order By?
* Come si configura un criterio di indicizzazione per Order By?
* Novità successive

Vengono anche forniti [esempi](#samples) e [domande frequenti](#faq).

Per informazioni complete sull'esecuzione di query SQL, vedere l’ [esercitazione sulle query di DocumentDB](documentdb-sql-query.md).

## <a name="how-to-query-with-order-by"></a>Come eseguire query con Order By?
Come in SQL ANSI, è ora possibile includere una clausola Order By facoltativa nelle istruzioni SQL quando si eseguono query di DocumentDB. La clausola può includere un argomento ASC/DESC facoltativo per specificare l'ordine in cui i risultati devono essere recuperati. 

### <a name="ordering-using-sql"></a>Ordinamento tramite SQL
Ad esempio, la query seguente recupera i primi 10 libri in ordine decrescente per titolo. 

    SELECT TOP 10 * 
    FROM Books 
    ORDER BY Books.Title DESC

### <a name="ordering-using-sql-with-filtering"></a>Ordinamento tramite SQL con filtro
È possibile ordinare utilizzando qualsiasi proprietà annidata all'interno di documenti, ad esempio Books.ShippingDetails.Weight, ed è possibile specificare filtri aggiuntivi nella clausola WHERE in combinazione con Order By come in questo esempio:

    SELECT * 
    FROM Books 
    WHERE Books.SalePrice > 4000
    ORDER BY Books.ShippingDetails.Weight

### <a name="ordering-using-the-linq-provider-for-net"></a>Ordinamento tramite il provider LINQ per .NET
Utilizzando .NET SDK versione 1.2.0 e successive, è anche possibile utilizzare la clausola OrderBy() o OrderByDescending() all'interno di query LINQ come in questo esempio:

    foreach (Book book in client.CreateDocumentQuery<Book>(UriFactory.CreateDocumentCollectionUri("db", "books"))
        .OrderBy(b => b.PublishTimestamp)
        .Take(100))
    {
        // Iterate through books
    }

DocumentDB supporta l'ordinamento con una singolo proprietà numerica, stringa o booleana per ogni query, e i tipi di query aggiuntive saranno disponibili a breve. Vedere [Novità successive](#Whats_coming_next) per ulteriori dettagli.

## <a name="configure-an-indexing-policy-for-order-by"></a>Configurare un criterio di indicizzazione per Order By
È importante ricordare che DocumentDB supporta due tipi di indici Hash e intervallo, che possono essere impostati per proprietà/percorsi specifici, tipi di dati (stringhe o numeri) e valori di diversa precisione (precisione massima o un valore di precisione fissa). Poiché DocumentDB utilizza indicizzazione hash come impostazione predefinita, è necessario creare una nuova raccolta con un criterio di indicizzazione personalizzato con intervallo di numeri, stringhe o entrambi, per poter utilizzare Order By. 

> [!NOTE]
> Gli indici dell'intervallo stringa sono stati introdotti il 7 luglio 2015 con API REST versione 2015-06-03. Per creare criteri per Order By su stringhe, è necessario utilizzare SDK versione 1.2.0 di .NET SDK o versione 1.1.0 del Python, Node. js o Java SDK.
> 
> Prima di API REST versione 2015-06-03, il criterio di indicizzazione di raccolta predefinito era hash per le stringhe e numeri. Questo è stato modificato in hash per le stringhe e intervallo per i numeri. 
> 
> 

Per ulteriori informazioni vedere [Criteri di indicizzazione di DocumentDB](documentdb-indexing-policies.md).

### <a name="indexing-for-order-by-against-all-properties"></a>Indicizzazione per Order By su tutte le proprietà numeriche
Ecco come creare una raccolta con "Tutto l’intervallo" che indicizza per Order By su ognuna/tutte le proprietà numerica o stringa visualizzate all'interno dei documenti JSON in esso contenuti. Di seguito viene eseguito l'override del tipo di indice predefinito dei valori stringa su Intervallo, alla precisione massima (-1).

    DocumentCollection books = new DocumentCollection();
    books.Id = "books";
    books.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), books);  

> [!NOTE]
> Si noti che Order By restituirà solo i risultati dei tipi di dati (stringa e numero) indicizzati con un RangeIndex. Ad esempio, se si dispone dell'impostazione predefinita per l'indicizzazione dei criteri che ha solo RangeIndex sui numeri, una clausola Order By su un percorso con i valori stringa non restituirà alcun documento.
> 
> 

### <a name="indexing-for-order-by-for-a-single-property"></a>Indicizzazione per Order By per una singola proprietà
Ecco come creare una raccolta per l’indicizzazione per Order By rispetto alla proprietà Title, con una stringa. Esistono due percorsi, uno per la proprietà Title ("/ titolo /?") con l'indicizzazione di intervallo e l'altro per tutte le altre proprietà con lo schema di indicizzazione predefinito, ovvero hash per le stringhe e intervallo di numeri.                    

    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/Title/?", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = -1 } } 
            });

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), booksCollection);  


## <a name="samples"></a>Esempi
Esaminare il [progetto di esempio Github](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries) che illustra come utilizzare Order By e come creare criteri di indicizzazione e paging tramite Order By. Gli esempi sono open source e si consiglia di inviare richieste pull con contributi che potrebbero essere utili ad altri sviluppatori DocumentDB. Per informazioni su come contribuire, fare riferimento alle [linee guida specifiche](https://github.com/Azure/azure-documentdb-net/blob/master/Contributing.md) .  

## <a name="faq"></a>domande frequenti
**Qual è il consumo di richiesta di unità (RU)  previsto delle query Order By?**

Poiché Order By utilizza l'indice di DocumentDB per le ricerche, il numero di unità di richiesta utilizzate dalle query Order By sarà simile alle query equivalenti senza Order By. Come qualsiasi altra operazione in DocumentDB, il numero di unità di richiesta dipende dalle dimensioni/forme dei documenti, nonché dalla complessità della query. 

**Qual è il sovraccarico di indicizzazione previsto per Order By?**

Il sovraccarico di indicizzazione sarà proporzionale al numero di proprietà numeriche. Nel peggiore dei casi, il sovraccarico dell’indice sarà pari al 100% dei dati. Non esiste alcuna differenza di sovraccarico della velocità effettiva (unità di richiesta) tra indicizzazione di intervallo/Order By e indicizzazione hash predefinita.

**Come si esegue una query sui dati esistenti in DocumentDB tramite Order By?**

Per ordinare i risultati della query tramite Order By, è necessario modificare il criterio di indicizzazione della raccolta per utilizzare un tipo di indice di intervallo rispetto alla proprietà utilizzata per ordinare. Vedere [Modifica dei criteri di indicizzazione](documentdb-indexing-policies.md#modifying-the-indexing-policy-of-a-collection). 

**Quali sono le limitazioni attuali di Order By?**

Order By può essere specificato solo su una proprietà, numerica o stringa se si tratta di intervallo indicizzato con la precisione massima (-1).

Non è possibile usare Order By:

* Con proprietà di stringa interne come id, _rid e _self (sarà possibile a breve).
* Con proprietà derivate dal risultato di un join all'interno del documento (sarà possibile a breve).
* Con proprietà multiple stringa (sarà possibile a breve).
* Order By con le query su database, raccolte, utenti, autorizzazioni o allegati (in arrivo).
* Con proprietà calcolate, ad esempio il risultato di un'espressione o una funzione definita dall'utente/incorporata.

Order By non è attualmente supportato per query tra partizioni nel caso in cui si usi Esplora Query nel portale di Azure.

## <a name="troubleshooting"></a>Risoluzione dei problemi
Se si riceve un messaggio di errore in cui si comunica che Order By non è supportato, verificare che sia in uso una versione di [SDK](documentdb-sdk-dotnet.md) che supporta Order By. 

## <a name="next-steps"></a>Passaggi successivi
Esaminare il [progetto di esempio Github](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries) e iniziare a ordinare i dati. 

## <a name="references"></a>Riferimenti
* [Nozioni di base sulle query di DocumentDB](documentdb-sql-query.md)
* [Nozioni di base sui criteri di indicizzazione di DocumentDB](documentdb-indexing-policies.md)
* [Riferimento SQL di DocumentDB](https://msdn.microsoft.com/library/azure/dn782250.aspx)
* [Esempi di Order By di DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries)




<!--HONumber=Feb17_HO3-->


