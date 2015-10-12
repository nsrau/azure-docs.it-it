<properties 
	pageTitle="Personalizzazione dell'indicizzatore di Ricerca di Azure" 
	description="Informazioni su come personalizzare le impostazioni e i criteri degli indicizzatori di Ricerca di Azure" 
	services="search" 
	documentationCenter="" 
	authors="chaosrealm" 
	manager="pablocas" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="09/29/2015" 
	ms.author="eugenesh"/>

#Personalizzazione dell'indicizzatore di Ricerca di Azure

La configurazione di un indicizzatore in Ricerca di Azure consente di rinominare i campi tra un'origine dati e un indice di destinazione, trasformare stringhe da una tabella di database in insiemi di stringhe, passare i criteri di rilevamento di modifiche in un'origine dati, codificare in URL le chiavi del documento che contengono caratteri URL non sicuri e tollerare errori di indicizzazione di alcuni documenti.

Se non si ha familiarità con gli indicizzatori di Ricerca di Azure, può essere utile leggere prima gli articoli seguenti:

- [Connessione del database SQL di Azure a Ricerca di Azure tramite gli indicizzatori](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [Connessione di DocumentDB con Ricerca di Azure tramite indicizzatori](../documentdb/documentdb-search-indexer.md)
- [.NET SDK con supporto per gli indicizzatori](https://msdn.microsoft.com/library/dn951165.aspx) o 
- [Informazioni di riferimento sull'API REST di Azure](https://msdn.microsoft.com/library/azure/dn946891.aspx)

##Rinominare i campi tra un'origine dati e un indice di destinazione##

I **mapping dei campi** sono proprietà che riconciliano le differenze tra le definizioni di campo. Gli esempi più comuni si trovano nei nomi di campo che violano le regole di denominazione di Ricerca di Azure. Si consideri una tabella di origine in cui uno o più nomi di campo iniziano con un carattere di sottolineatura, ad esempio `_id`. Ricerca di Azure non ammette i nomi di campo con un carattere di sottolineatura iniziale e quindi il campo deve essere rinominato.

L'esempio seguente illustra come aggiornare un indicizzatore in modo da includere un mapping di campo che trasforma il campo `_id` dell'origine dati nel campo `id` nell'indice di destinazione:

	PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]
    {
        "dataSourceName" : "mydatasource",
        "targetIndexName" : "myindex",
        "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ] 
    } 

**NOTA:** per usare i mapping dei campi è necessario usare un'API di anteprima versione 2015-02-28-Preview.

È possibile specificare più mapping dei campi.

	"fieldMappings" : [ 
		{ "sourceFieldName" : "_id", "targetFieldName" : "id" },
        { "sourceFieldName" : "_timestamp", "targetFieldName" : "timestamp" },
	 ]

Per i nomi dei campi di origine e destinazione non viene fatta distinzione tra maiuscole e minuscole.

##Trasformare le stringhe di una tabella di database in raccolte di stringhe##

Il mapping dei campi può essere usato anche per trasformare i valori del campo di origine mediante le *funzioni di mapping*.

Una funzione di questo tipo, `jsonArrayToStringCollection`, analizza un campo contenente una stringa formattata come matrice JSON e lo trasforma in un campo Collection(Edm.String) nell'indice di destinazione. È destinata in particolare all'uso con l'indicizzatore di SQL Azure, poiché SQL non ha un tipo di dati nativo per le raccolte. È possibile usare questa funzione come descritto di seguito:

	"fieldMappings" : [ { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } } ] 

Ad esempio, se il campo di origine contiene la stringa `["red", "white", "blue"]`, il campo di destinazione di tipo `Collection(Edm.String)` verrà popolato con i tre valori `"red"`, `"white"` e `"blue"`.

Si noti che la proprietà `targetFieldName` è facoltativa. Se omessa, viene usato il valore `sourceFieldName`.

##Cambiare i criteri di rilevamento delle modifiche in un'origine dati##
  
In Ricerca di Azure la decisione relativa ai criteri di rilevamento delle modifiche da adottare dipende in gran parte dalle funzionalità supportate dall'origine dati e dallo schema dati. Nel corso del tempo, soprattutto in caso di aggiornamento o migrazione dei database, si può decidere di passare a un altro tipo di criteri di rilevamento delle modifiche. Ad esempio, questa operazione può essere opportuna se si è aggiornato il database SQL di Azure a una versione più recente che supporta il rilevamento delle modifiche integrato e si desidera passare dai criteri con limite massimo a quelli integrati, oppure se si è scelto di usare una colonna diversa come limite massimo.

Se si chiama semplicemente l'API REST PUT datasource per aggiornare l'origine dati, è possibile che venga restituita una risposta 400 con un messaggio di errore simile al seguente:


	"Change detection policy cannot be changed for data source '…' because indexer '…' references this data source and has a non-empty change tracking state. You can use Reset API to reset the indexer's change tracking state, and retry this call."

 Probabilmente ci si chiederà cosa significa e come risolvere il problema. Questo errore si verifica perché Ricerca di Azure mantiene lo stato interno associato ai criteri di rilevamento delle modifiche. Quando i criteri vengono cambiati, lo stato esistente non risulta più valido poiché non è applicabile ai nuovi criteri. Questo significa che l'indicizzatore deve iniziare da zero l'indicizzazione dell'origine dati usando i nuovi criteri, con possibili implicazioni per l'utente (ad esempio, carico aggiuntivo sul database o costi supplementari della larghezza di banda di rete). Per questo motivo, Ricerca di Azure chiede di chiamare l'[API Reset Indexer](https://msdn.microsoft.com/library/azure/dn946897.aspx) per reimpostare lo stato associato ai criteri correnti di rilevamento delle modifiche e consentire così la sostituzione dei criteri con una normale chiamata a PUT datasource. Naturalmente, Ricerca di Azure può eseguire questa operazione in modo automatico, ma è importante riconoscere esplicitamente di aver compreso le implicazioni chiamando l'API Reset.

##Applicare la codifica URL alle chiavi dei documenti contenenti caratteri non sicuri per l'URL##

Ricerca di Azure richiede l'uso di caratteri sicuri per l'URL all'interno di un campo della chiave di un documento poiché gli utenti devono essere in grado di cercare documenti utilizzando le proprie chiavi. Cosa accade quando i documenti da indicizzare contengono caratteri non sicuri per l'URL nel campo della chiave? Se si indicizzano i documenti personalmente, usando un SDK client o l'API REST, è possibile applicare la codifica URL alle chiavi. Con gli indicizzatori, è possibile indicare a Ricerca di Azure di applicare la codifica URL alle chiavi impostando il parametro **base64EncodeKeys** su `true` durante la creazione o l'aggiornamento dell'indicizzatore:

    PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]
    {
        "dataSourceName" : "mydatasource",
        "targetIndexName" : "myindex",
        "parameters" : { "base64EncodeKeys": true }
    }

Per informazioni dettagliate sulla codifica, vedere questo [articolo di MSDN](http://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx).

NOTA: per eseguire ricerche o applicare filtri su valori di chiave, si dovrà codificare in modo analogo le chiavi usate nelle richieste, in modo che la richiesta corrisponda al valore codificato nell'indice di ricerca.


##Tollerare gli errori di indicizzazione di alcuni documenti##

Per impostazione predefinita, un indicizzatore di Ricerca di Azure arresta l'indicizzazione non appena un singolo documento non viene indicizzato. A seconda dello scenario, è possibile decidere di tollerare alcuni errori, ad esempio se si reindicizza ripetutamente l'intera origine dati. Ricerca di Azure fornisce due parametri di indicizzatore per ottimizzare questo comportamento:

- **maxFailedItems**: numero di elementi per cui l'indicizzazione può avere esito negativo prima che l'indicizzatore venga considerato in errore. Il valore predefinito è 0.
- **maxFailedItems**: numero di elementi per cui l'indicizzazione può avere esito negativo in un singolo batch prima che l'indicizzatore venga considerato in errore. Il valore predefinito è 0.

È possibile modificare questi valori in qualsiasi momento specificando uno di questi parametri, o entrambi, durante la creazione o l'aggiornamento dell'indicizzatore:

	PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
	Content-Type: application/json
	api-key: [admin key]
    {
        "dataSourceName" : "mydatasource",
        "targetIndexName" : "myindex",
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5 }
    }

Anche se si sceglie di tollerare alcuni errori, le informazioni sui documenti per cui l'indicizzazione ha avuto esito negativo vengono restituite dall'[API Get Indexer Status](https://msdn.microsoft.com/library/azure/dn946884.aspx).

A completamento di questo articolo, si consiglia di inviare commenti o suggerimenti per nuove idee di contenuto tramite tweet, usando l'hashtag #AzureSearch, o dalla [pagina del forum UserVoice](http://feedback.azure.com/forums/263029-azure-search).
 

<!---HONumber=Oct15_HO1-->