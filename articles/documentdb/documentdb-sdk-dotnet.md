<properties 
	pageTitle="DocumentDB .NET SDK | Microsoft Azure" 
	description="Tutte le informazioni su .NET SDK, incluse le date di rilascio, le date di ritiro e le modifiche apportate tra le versioni di DocumentDB .NET SDK." 
	services="documentdb" 
	documentationCenter=".net" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/16/2015" 
	ms.author="ryancraw"/>

# DocumentDB SDK

> [AZURE.SELECTOR]
- [.NET SDK](documentdb-sdk-dotnet.md)
- [Node.js SDK](documentdb-sdk-node.md)
- [Java SDK](documentdb-sdk-java.md)
- [Python SDK](documentdb-sdk-python.md)

##DocumentDB .NET SDK

<table> <tr><td>**Download**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)</td></tr> <tr><td>**Documentazione**</td><td>[Documentazione di riferimento di .NET SDK](https://msdn.microsoft.com/library/azure/dn948556.aspx)</td></tr> <tr><td>**Esempi**</td><td>[Esempi di codice .NET](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)</td></tr> <tr><td>**Introduzione**</td><td>[Introduzione a DocumentDB .NET SDK](documentdb-get-started.md)</td></tr> <tr><td>**Framework attualmente supportato**</td><td>[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</td></tr> </table></br>

## Note sulla versione

### <a name="1.5.1"/>[1\.5.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.1)
 - **[Corretto]** Se Select non è l'ultima espressione il provider LINQ presuppone che non ci sia alcuna proiezione e produce SELECT * in modo non corretto. [#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="1.5.0"/>[1\.5.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.0)
 - Implementazione di Upsert, aggiunti metodi UpsertXXXAsync
 - Miglioramenti delle prestazioni per tutte le richieste
 - Supporto del provider LINQ per metodi condizionali, coalesce e CompareTo per le stringhe
 - **[Corretto]** Provider LINQ --> Implementa il metodo Contains nell'elenco per generare lo stesso codice SQL generato in IEnumerable e nella matrice
 - **[Corretto]** BackoffRetryUtility usa nuovamente lo stesso HttpRequestMessage anziché crearne uno nuovo in un tentativo successivo
 - **[Obsoleto]** UriFactory.CreateCollection --> Ora deve usare UriFactory.CreateDocumentCollection
 
### <a name="1.4.1"/>[1\.4.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.1)
 - **[Corretto]** Problemi di localizzazione quando si usano impostazioni cultura diverse dalla lingua inglese, ad esempio nl-NL e così via. 
 
### <a name="1.4.0"/>[1\.4.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.0)
  - Routing basato su ID
    - Nuovo helper UriFactory per agevolare la costruzione di collegamenti alle risorse basati su ID
    - Nuovi overload su DocumentClient da eseguire nell'URI
  - Aggiunti IsValid() and IsValidDetailed() in LINQ per i dati geospaziali
  - Migliorato il supporto del provider LINQ
    - **Matematica**: Abs, Acos, Asin, Atan, Ceiling, Cos, Exp, Floor, Log, Log10, Pow, Round, Sign, Sin, Sqrt, Tan, Truncate
    - **Stringa**: Concat, Contains, EndsWith, IndexOf, Count, ToLower, TrimStart, Replace, Reverse, TrimEnd, StartsWith, SubString, ToUpper
    - **Matrice**: Concat, Contains, Count
    - Operatore **IN**

### <a name="1.3.0"/>[1\.3.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.3.0)
  - Aggiunto il supporto per la modifica dei criteri di indicizzazione
    - Nuovo metodo ReplaceDocumentCollectionAsync in DocumentClient
    - Nuova proprietà IndexTransformationProgress in ResourceResponse<T> per tenere traccia dello stato percentuale delle modifiche ai criteri di indice
    - DocumentCollection.IndexingPolicy è ora modificabile
  - Aggiunto il supporto per query e indicizzazione spaziali
    - Nuovo spazio dei nomi Microsoft.Azure.Documents.Spatial per la serializzazione/deserializzazione di tipi di dati spaziali come point e polygon
    - Nuova classe SpatialIndex per l'indicizzazione dei dati GeoJSON archiviati in DocumentDB
  - **[Corretto]** Query SQL non corretta generata dall'espressione linq [#38](https://github.com/Azure/azure-documentdb-net/issues/38)

### <a name="1.2.0"/>[1\.2.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.2.0)
- Dipendenza su Newtonsoft.Json 5.0.7 
- Modifiche per il supporto di Order By
  - Supporto del provider LINQ per OrderBy() o OrderByDescending()
  - IndexingPolicy per il supporto di Order By 
  
		**NB: Possible breaking change** 
  
    	If you have existing code that provisions collections with a custom indexing policy, then your existing code will need to be updated to support the new IndexingPolicy class. If you have no custom indexing policy, then this change does not affect you.

### <a name="1.1.0"/>[1\.1.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.1.0)
- Supporto per il partizionamento dei dati con IPartitionResolver e le nuove classi HashPartitionResolver e RangePartitionResolver
- Serializzazione di DataContract
- Supporto per GUID nel provider LINQ
- Supporto per UDF in LINQ

### <a name="1.0.0"/>[1\.0.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.0.0)
- SDK con disponibilità generale

> [AZURE.NOTE]Tra l'anteprima e la disponibilità generale è stata apportata una modifica al nome del pacchetto NuGet, da **Microsoft.Azure.Documents.Client** a **Microsoft.Azure.DocumentDB** <br/>.


### <a name="0.9.x-preview"/>[0\.9.x-anteprima](https://www.nuget.org/packages/Microsoft.Azure.Documents.Client)
- [Obsoleto] Anteprima di SDK

## Date di rilascio e di ritiro
Microsoft invierà una notifica almeno **12 mesi** prima del ritiro di un SDK per agevolare la transizione a una versione più recente o supportata.

Le nuove caratteristiche e funzionalità e le ottimizzazioni vengono aggiunte solo all'SDK corrente, è quindi consigliabile eseguire sempre l'aggiornamento alla versione più recente dell'SDK quanto prima.

Qualsiasi richiesta inviata a DocumentDB con un SDK ritirato verrà rifiutata dal servizio.

> [AZURE.WARNING]Tutte le versioni dell'SDK per .NET di Azure DocumentDB precedenti alla versione **1.0.0** verranno ritirate il **29 febbraio 2016**.
 
<br/>
 
| Versione | Data di rilascio | Data di ritiro 
| ---	  | ---	         | ---
| [1\.5.1](#1.5.1) | 23 novembre 2015 |--- | [1\.5.0](#1.5.0) | 05 ottobre 2015 |--- | [1\.4.1](#1.4.1) | 25 agosto 2015 |--- | [1\.4.0](#1.4.0) | 13 agosto 2015 |--- | [1\.3.0](#1.3.0) | 05 agosto 2015 |--- | [1\.2.0](#1.2.0) | 06 luglio 2015 |--- | [1\.1.0](#1.1.0) | 30 aprile 2015 |--- | [1\.0.0](#1.0.0) | 08 aprile 2015 |--- | [0\.9.3-versione non definitiva](#0.9.x-preview) | 12 marzo 2015 | 29 febbraio 2016 | [0\.9.2-versione non definitiva](#0.9.x-preview) | gennaio 2015 | 29 febbraio 2016 | [.9.1-versione non definitiva](#0.9.x-preview) | 13 ottobre 2014 | 29 febbraio 2016 | [0\.9.0-versione non definitiva](#0.9.x-preview) | 21 agosto 2014 | 29 febbraio 2016

## Domande frequenti
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## Vedere anche

Per altre informazioni su DocumentDB, vedere la pagina del servizio [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/).

<!---HONumber=AcomDC_1125_2015-->