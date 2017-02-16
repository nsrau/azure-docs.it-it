---
title: SDK e API .NET di DocumentDB | Microsoft Docs
description: Tutte le informazioni sull&quot;SDK e sull&quot;API .NET, incluse le date di rilascio, le date di ritiro e le modifiche apportate tra le versioni di DocumentDB .NET SDK.
services: documentdb
documentationcenter: .net
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: 8e239217-9085-49f5-b0a7-58d6e6b61949
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/09/2016
ms.author: rnagpal
translationtype: Human Translation
ms.sourcegitcommit: efd154811ebbcf2170ffb001344a954be72b1d92
ms.openlocfilehash: 93b4c7003b4b5461d34a357967b2c089a189c854


---
# <a name="documentdb-apis-and-sdks"></a>SDK e API di DocumentDB
> [!div class="op_single_selector"]
> * [.NET](documentdb-sdk-dotnet.md)
> * [.NET Core](documentdb-sdk-dotnet-core.md)
> * [Node.js](documentdb-sdk-node.md)
> * [Java](documentdb-sdk-java.md)
> * [Python](documentdb-sdk-python.md)
> * [REST](https://docs.microsoft.com/en-us/rest/api/documentdb/)
> * [Provider di risorse REST](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

## <a name="documentdb-net-api-and-sdk"></a>SDK e API di DocumentDB .NET
<table>

<tr><td>**Download dell'SDK**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)</td></tr>

<tr><td>**Documentazione sull'API**</td><td>[Documentazione di riferimento API .NET](https://msdn.microsoft.com/library/azure/dn948556.aspx)</td></tr>

<tr><td>**Esempi**</td><td>[Esempi di codice .NET](documentdb-dotnet-samples.md)</td></tr>

<tr><td>**Introduzione**</td><td>[Introduzione a DocumentDB .NET SDK](documentdb-get-started.md)</td></tr>

<tr><td>**Esercitazione sull'app Web**</td><td>[Sviluppo di applicazioni Web con DocumentDB](documentdb-dotnet-application.md)</td></tr>

<tr><td>**Framework attualmente supportato**</td><td>[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</td></tr>
</table></br>

## <a name="release-notes"></a>Note sulla versione

### <a name="a-name11101110httpswwwnugetorgpackagesmicrosoftazuredocumentdb1110"></a><a name="1.11.0"/>[1.11.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.11.0)
* Supporto per nuove classi e metodi per elaborare il [feed di modifiche](documentdb-change-feed.md) dei documenti in una raccolta.
* Supporto per la continuazione della query tra partizioni e miglioramenti delle prestazioni per le query tra partizioni.
* Aggiunta dei metodi CreateDatabaseIfNotExistsAsync e CreateDocumentCollectionIfNotExistsAsync.
* Supporto di LINQ per le funzioni di sistema: IsDefined, IsNull e IsPrimitive.
* Correzione per l'inserimento automatico nel Cestino degli assembly Microsoft.Azure.Documents.ServiceInterop.dll e DocumentDB.Spatial.Sql.dll nella cartella Cestino dell'applicazione quando si usa il pacchetto Nuget con progetti che includono strumenti project.json.
* Supporto per l'emissione di tracce ETW lato client che possono essere utili in scenari di debug.

### <a name="a-name11001100httpswwwnugetorgpackagesmicrosoftazuredocumentdb1100"></a><a name="1.10.0"/>[1.10.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.10.0)
* Aggiunto il supporto per la connettività diretta delle raccolte partizionate.
* Miglioramento delle prestazioni per il livello di coerenza con obsolescenza associata.
* Aggiunta Polygon e LineString DataTypes quando si specifica il criterio di indicizzazione per la raccolta criteri per le query spaziali di geo-fencing.
* Aggiunto il supporto LINQ per StringEnumConverter, IsoDateTimeConverter e UnixDateTimeConverter durante la traduzione dei predicati.
* Varie correzioni di bug dell'SDK.

### <a name="a-name195195httpswwwnugetorgpackagesmicrosoftazuredocumentdb195"></a><a name="1.9.5"/>[1.9.5](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.5)
* Risolto un problema che causava l'eccezione NotFoundException seguente: la sessione di lettura non è disponibile per il token della sessione di input. In alcuni casi questa eccezione si è verificata durante l'esecuzione di query per l'area di lettura di un account geograficamente distribuito.
* L'esposizione della proprietà ResponseStream nella classe ResourceResponse consente l'accesso diretto al flusso sottostante proveniente da una risposta.

### <a name="a-name194194httpswwwnugetorgpackagesmicrosoftazuredocumentdb194"></a><a name="1.9.4"/>[1.9.4](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.4)
* Modificate le classi ResourceResponse, FeedResponse, StoredProcedureResponse e MediaResponse per l'implementazione dell'interfaccia pubblica corrispondente in modo che possano essere simulate per la distribuzione basata su test (TDD).
* Risolto un problema che causava un'intestazione di chiave di partizione non valida con l'uso di un oggetto JsonSerializerSettings personalizzato per la serializzazione dei dati.

### <a name="a-name193193httpswwwnugetorgpackagesmicrosoftazuredocumentdb193"></a><a name="1.9.3"/>[1.9.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.3)
* Risoluzione di un problema che provocava l'esito negativo con errore delle query con esecuzione prolungata. Al momento il token di autorizzazione non è valido.
* Risoluzione di un problema che rimuoveva l'oggetto SqlParameterCollection originale dalle query top/order-by tra partizioni.

### <a name="a-name192192httpswwwnugetorgpackagesmicrosoftazuredocumentdb192"></a><a name="1.9.2"/>[1.9.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.2)
* Aggiunta del supporto per le query parallele nelle raccolte partizionate.
* Aggiunta del supporto ORDER BY e TOP tra partizioni per le raccolte partizionate.
* Correzione di riferimenti mancanti a DocumentDB.Spatial.Sql.dll e Microsoft.Azure.Documents.ServiceInterop.dll, necessari quando si fa riferimento a un progetto di DocumentDB con riferimento al pacchetto Nuget DocumentDB.
* Correzione della possibilità di usare parametri di tipi diversi per le funzioni definite dall'utente in LINQ. 
* Risoluzione di un bug per gli account replicati a livello globale in cui le chiamate Upsert sono state indirizzate verso posizioni di lettura invece di posizioni di scrittura.
* Aggiunta di metodi mancanti all'interfaccia IDocumentClient: 
  * il metodo UpsertAttachmentAsync che accetta mediaStream e opzioni come parametri
  * il metodo CreateAttachmentAsync che accetta opzioni come parametro
  * il metodo CreateOfferQuery che accetta querySpec come parametro.
* Rivelazione di classi pubbliche esposte nell'interfaccia IDocumentClient.

### <a name="a-name180180httpswwwnugetorgpackagesmicrosoftazuredocumentdb180"></a><a name="1.8.0"/>[1.8.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.8.0)
* Aggiunta del supporto per gli account di database con più aree.
* Aggiunta del supporto per la ripetizione dei tentativi delle richieste limitate.  L'utente può personalizzare il numero di tentativi e il tempo di attesa massimo configurando la proprietà ConnectionPolicy.RetryOptions.
* Aggiunta di una nuova interfaccia IDocumentClient che definisce le firme di tutti i metodi e di tutte le proprietà DocumenClient.  Come parte di questa modifica, anche i metodi di estensione che creano IQueryable e IOrderedQueryable sono stati modificati in metodi sulla stessa classe DocumentClient.
* Aggiunta dell'opzione di configurazione per impostare ServicePoint.ConnectionLimit per un URI dell'endpoint di DocumentDB specifico.  Usare ConnectionPolicy.MaxConnectionLimit per modificare il valore predefinito, impostato su 50.
* IPartitionResolver e la relativa implementazione sono stati deprecati.  Il supporto per IPartitionResolver è ora obsoleto. È consigliabile usare le raccolte partizionate per un'archiviazione e una velocità effettiva superiori.

### <a name="a-name171171httpswwwnugetorgpackagesmicrosoftazuredocumentdb171"></a><a name="1.7.1"/>[1.7.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.7.1)
* Aggiunta di un overload al metodo ExecuteStoredProcedureAsync basato su URI che accetta RequestOptions come parametro.

### <a name="a-name170170httpswwwnugetorgpackagesmicrosoftazuredocumentdb170"></a><a name="1.7.0"/>[1.7.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.7.0)
* Aggiunta del supporto per la durata (TTL) relativa ai documenti.

### <a name="a-name163163httpswwwnugetorgpackagesmicrosoftazuredocumentdb163"></a><a name="1.6.3"/>[1.6.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.6.3)
* Correzione di un bug nel pacchetto Nuget di .NET SDK per creare il pacchetto come parte di una soluzione del servizio cloud di Azure.

### <a name="a-name162162httpswwwnugetorgpackagesmicrosoftazuredocumentdb162"></a><a name="1.6.2"/>[1.6.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.6.2)
* Implementazione delle [raccolte partizionate](documentdb-partition-data.md) e dei [livelli di prestazioni definiti dall'utente](documentdb-performance-levels.md). 

### <a name="a-name153153httpswwwnugetorgpackagesmicrosoftazuredocumentdb153"></a><a name="1.5.3"/>[1.5.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.3)
* **[Correzione]** Se si eseguono query nell'endpoint DocumentDB viene generato il messaggio seguente: "System.Net.Http.HttpRequestException: Errore durante la copia del contenuto in un flusso".

### <a name="a-name152152httpswwwnugetorgpackagesmicrosoftazuredocumentdb152"></a><a name="1.5.2"/>[1.5.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.2)
* Supporto LINQ espanso tra cui nuovi operatori per il paging, espressioni condizionali e confronto di intervalli.
  * Operatore Take per abilitare il comportamento SELECT TOP in LINQ
  * Operatore CompareTo per abilitare i confronti di intervallo di stringa
  * Operatori Conditional (?) e Coalesce (??)
* **[Correzione]** ArgumentOutOfRangeException durante l'unione della proiezione del modello con Where-In nella query linq.  [N. 81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="a-name151151httpswwwnugetorgpackagesmicrosoftazuredocumentdb151"></a><a name="1.5.1"/>[1.5.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.1)
* **[Corretto]** Se Select non è l'ultima espressione il provider LINQ presuppone che non ci sia alcuna proiezione e produce SELECT * in modo non corretto.  [#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="a-name150150httpswwwnugetorgpackagesmicrosoftazuredocumentdb150"></a><a name="1.5.0"/>[1.5.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.0)
* Implementazione di Upsert, aggiunti metodi UpsertXXXAsync
* Miglioramenti delle prestazioni per tutte le richieste
* Supporto del provider LINQ per metodi condizionali, coalesce e CompareTo per le stringhe
* **[Corretto]** Provider LINQ --> Implementa il metodo Contains nell'elenco per generare lo stesso codice SQL generato in IEnumerable e Matrice
* **[Corretto]** BackoffRetryUtility usa nuovamente lo stesso HttpRequestMessage anziché crearne uno nuovo in un tentativo successivo
* **[Obsoleto]** UriFactory.CreateCollection --> Ora deve usare UriFactory.CreateDocumentCollection

### <a name="a-name141141httpswwwnugetorgpackagesmicrosoftazuredocumentdb141"></a><a name="1.4.1"/>[1.4.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.1)
* **[Corretto]** Problemi di localizzazione quando si usano impostazioni cultura diverse dalla lingua inglese, ad esempio nl-NL e così via. 

### <a name="a-name140140httpswwwnugetorgpackagesmicrosoftazuredocumentdb140"></a><a name="1.4.0"/>[1.4.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.0)
* Routing basato su ID
  * Nuovo helper UriFactory per agevolare la costruzione di collegamenti alle risorse basati su ID
  * Nuovi overload su DocumentClient da eseguire nell'URI
* Aggiunti IsValid() and IsValidDetailed() in LINQ per i dati geospaziali
* Migliorato il supporto del provider LINQ
  * **Matematica** : Abs, Acos, Asin, Atan, Ceiling, Cos, Exp, Floor, Log, Log10, Pow, Round, Sign, Sin, Sqrt, Tan, Truncate
  * **Stringa** : Concat, Contains, EndsWith, IndexOf, Count, ToLower, TrimStart, Replace, Reverse, TrimEnd, StartsWith, SubString, ToUpper
  * **Matrice** : Concat, Contains, Count
  * **IN** 

### <a name="a-name130130httpswwwnugetorgpackagesmicrosoftazuredocumentdb130"></a><a name="1.3.0"/>[1.3.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.3.0)
* Aggiunto il supporto per la modifica dei criteri di indicizzazione
  * Nuovo metodo ReplaceDocumentCollectionAsync in DocumentClient
  * Nuova proprietà IndexTransformationProgress in ResourceResponse<T> per tenere traccia dello stato percentuale delle modifiche ai criteri di indice
  * DocumentCollection.IndexingPolicy è ora modificabile
* Aggiunto il supporto per query e indicizzazione spaziali
  * Nuovo spazio dei nomi Microsoft.Azure.Documents.Spatial per la serializzazione/deserializzazione di tipi di dati spaziali come point e polygon
  * Nuova classe SpatialIndex per l'indicizzazione dei dati GeoJSON archiviati in DocumentDB
* **[Corretto]** Query SQL non corretta generata dall'espressione linq [#38](https://github.com/Azure/azure-documentdb-net/issues/38)

### <a name="a-name120120httpswwwnugetorgpackagesmicrosoftazuredocumentdb120"></a><a name="1.2.0"/>[1.2.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.2.0)
* Dipendenza su Newtonsoft.Json 5.0.7 
* Modifiche per il supporto di Order By
  
  * Supporto del provider LINQ per OrderBy() o OrderByDescending()
  * IndexingPolicy per il supporto di Order By 
    
        **NB: Possible breaking change** 
    
        If you have existing code that provisions collections with a custom indexing policy, then your existing code will need to be updated to support the new IndexingPolicy class. If you have no custom indexing policy, then this change does not affect you.

### <a name="a-name110110httpswwwnugetorgpackagesmicrosoftazuredocumentdb110"></a><a name="1.1.0"/>[1.1.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.1.0)
* Supporto per il partizionamento dei dati con IPartitionResolver e le nuove classi HashPartitionResolver e RangePartitionResolver
* Serializzazione di DataContract
* Supporto per GUID nel provider LINQ
* Supporto per UDF in LINQ

### <a name="a-name100100httpswwwnugetorgpackagesmicrosoftazuredocumentdb100"></a><a name="1.0.0"/>[1.0.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.0.0)
* SDK con disponibilità generale

> [!NOTE]
> Tra l'anteprima e la disponibilità generale è stata apportata una modifica al nome del pacchetto NuGet, da **Microsoft.Azure.Documents.Client** a **Microsoft.Azure.DocumentDB**
> <br/>
> 
> 

### <a name="a-name09x-preview09x-previewhttpswwwnugetorgpackagesmicrosoftazuredocumentsclient"></a><a name="0.9.x-preview"/>[0.9.x-anteprima](https://www.nuget.org/packages/Microsoft.Azure.Documents.Client)
* [Obsoleto] Anteprima di SDK

## <a name="release--retirement-dates"></a>Date di rilascio e di ritiro
Microsoft invierà una notifica almeno **12 mesi** prima del ritiro di un SDK per agevolare la transizione a una versione più recente o supportata.

Le nuove caratteristiche e funzionalità e le ottimizzazioni vengono aggiunte solo all'SDK corrente. È quindi consigliabile eseguire sempre l'aggiornamento alla versione più recente dell'SDK quanto prima. 

Qualsiasi richiesta inviata a DocumentDB con un SDK ritirato verrà rifiutata dal servizio.

> [!WARNING]
> Tutte le versioni dell'SDK per .NET di Azure DocumentDB precedenti alla versione **1.0.0** verranno ritirate il **29 febbraio 2016**. 
> 
> 

<br/>

| Versione | Data di rilascio | Data di ritiro |
| --- | --- | --- |
| [1.11.0](#1.11.0) |08 dicembre 2016 |--- |
| [1.10.0](#1.10.0) |27 settembre 2016 |--- |
| [1.9.5](#1.9.5) |1° settembre 2016 |--- |
| [1.9.4](#1.9.4) |24 agosto 2016 |--- |
| [1.9.3](#1.9.3) |15 agosto 2016 |--- |
| [1.9.2](#1.9.2) |23 luglio 2016 |--- |
| 1.9.1 |Funzionalità deprecate |--- |
| 1.9.0 |Funzionalità deprecate |--- |
| [1.8.0](#1.8.0) |14 giugno 2016 |--- |
| [1.7.1](#1.7.1) |06 maggio 2016 |--- |
| [1.7.0](#1.7.0) |26 aprile 2016 |--- |
| [1.6.3](#1.6.3) |08 aprile 2016 |--- |
| [1.6.2](#1.6.2) |29 marzo 2016 |--- |
| [1.5.3](#1.5.3) |19 febbraio 2016 |--- |
| [1.5.2](#1.5.2) |14 dicembre 2015 |--- |
| [1.5.1](#1.5.1) |23 novembre 2015 |--- |
| [1.5.0](#1.5.0) |05 ottobre 2015 |--- |
| [1.4.1](#1.4.1) |25 agosto 2015 |--- |
| [1.4.0](#1.4.0) |13 agosto 2015 |--- |
| [1.3.0](#1.3.0) |05 agosto 2015 |--- |
| [1.2.0](#1.2.0) |06 luglio 2015 |--- |
| [1.1.0](#1.1.0) |30 aprile 2015 |--- |
| [1.0.0](#1.0.0) |08 aprile 2015 |--- |
| [0.9.3-prelease](#0.9.x-preview) |12 marzo 2015 |29 febbraio 2016 |
| [0.9.2-prelease](#0.9.x-preview) |Gennaio 2015 |29 febbraio 2016 |
| [.9.1-prelease](#0.9.x-preview) |13 ottobre 2014 |29 febbraio 2016 |
| [0.9.0-prelease](#0.9.x-preview) |21 agosto 2014 |29 febbraio 2016 |

## <a name="faq"></a>Domande frequenti
[!INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## <a name="see-also"></a>Vedere anche
Per altre informazioni su DocumentDB, vedere la pagina del servizio [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) . 




<!--HONumber=Dec16_HO2-->


