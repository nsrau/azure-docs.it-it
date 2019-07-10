---
title: 'Guida introduttiva: Creare e caricare un indice ed eseguirvi query con Postman e le API REST - Ricerca di Azure'
description: Informazioni su come chiamare le API REST di Ricerca di Azure con Postman, dati di esempio e definizioni.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 05/16/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: b3c5c08e9862d59481573b501045466d6d691a77
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485553"
---
# <a name="quickstart-create-an-azure-search-index-in-postman-using-rest-apis"></a>Guida introduttiva: Creare un indice di Ricerca di Azure in Postman con le API REST
> [!div class="op_single_selector"]
> * [Postman](search-get-started-postman.md)
> * [C#](search-create-index-dotnet.md)
> * [Python](search-get-started-python.md)
> * [Portale](search-get-started-portal.md)
> * [PowerShell](search-howto-dotnet-sdk.md)
>*

Uno dei modi più semplici per esplorare le [API REST di Ricerca di Azure](https://docs.microsoft.com/rest/api/searchservice) consiste nell'usare Postman o un altro strumento di test Web per formulare le richieste HTTP ed esaminare le risposte. Con gli strumenti corretti e con queste istruzioni è possibile inviare richieste e visualizzare le risposte prima di scrivere codice.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare e quindi [eseguire la registrazione a Ricerca di Azure](search-create-service-portal.md).

## <a name="prerequisites"></a>Prerequisiti

In questa guida di avvio rapido vengono usati i servizi e gli strumenti seguenti. 

+ [Creare un servizio Ricerca di Azure](search-create-service-portal.md) o [trovare un servizio esistente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) nella sottoscrizione corrente. È possibile usare un servizio gratuito per questo avvio rapido. 

+ Per l'invio di richieste a Ricerca di Azure viene usata l'[app desktop Postman](https://www.getpostman.com/) oppure [Telerik Fiddler](https://www.telerik.com/fiddler).

## <a name="get-a-key-and-url"></a>Ottenere una chiave e un URL

Le chiamate REST richiedono l'URL del servizio e una chiave di accesso per ogni richiesta. Con entrambi gli elementi viene creato un servizio di ricerca, quindi se si è aggiunto Ricerca di Azure alla sottoscrizione, seguire questi passaggi per ottenere le informazioni necessarie:

1. [Accedere al portale di Azure](https://portal.azure.com/) e ottenere l'URL nella pagina **Panoramica** del servizio di ricerca. Un endpoint di esempio potrebbe essere simile a `https://mydemo.search.windows.net`.

1. In **Impostazioni** > **Chiavi** ottenere una chiave amministratore per diritti completi sul servizio. Sono disponibili due chiavi amministratore interscambiabili, fornite per continuità aziendale nel caso in cui sia necessario eseguire il rollover di una di esse. È possibile usare la chiave primaria o secondaria nelle richieste per l'aggiunta, la modifica e l'eliminazione di oggetti.

![Ottenere una chiave di accesso e un endpoint HTTP](media/search-get-started-postman/get-url-key.png "Ottenere una chiave di accesso e un endpoint HTTP")

Per ogni richiesta inviata al servizio è necessario specificare una chiave API. La presenza di una chiave valida stabilisce una relazione di trust, in base alle singole richieste, tra l'applicazione che invia la richiesta e il servizio che la gestisce.

## <a name="connect-to-azure-search"></a>Connettersi a Ricerca di Azure

In questa sezione usare lo strumento Web preferito per configurare le connessioni a Ricerca di Azure. Ogni strumento conserva le informazioni dell'intestazione della richiesta per la sessione, quindi è necessario immettere la chiave API e il tipo di contenuto una sola volta.

Per ogni strumento è necessario scegliere un comando (GET, POST, PUT e così via), fornire un endpoint URL e per alcune attività fornire il codice JSON nel corpo della richiesta. Sostituire il nome del servizio di ricerca (YOUR-SEARCH-SERVICE-NAME) con un valore valido. 

    https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2019-05-06

Si notino il prefisso HTTPS, il nome del servizio, il nome di un oggetto (in questo caso la raccolta di indici) e la [versione API](search-api-versions.md). La versione API è una stringa obbligatoria in caratteri minuscoli specificata come `?api-version=2019-05-06` per la versione corrente. Le versioni API vengono aggiornate regolarmente. Includendo l'elemento api-version in ogni richiesta, è possibile avere controllo completo sulla versione API usata.  

La composizione dell'intestazione della richiesta include due elementi, il tipo di contenuto e la chiave API usata per l'autenticazione in Ricerca di Azure. Sostituire la chiave API di amministrazione (YOUR-ADMIN-API-KEY) con un valore valido. 

    api-key: <YOUR-ADMIN-API-KEY>
    Content-Type: application/json

In Postman formulare una richiesta simile allo screenshot seguente. Scegliere **GET** come verbo, specificare l'URL e fare clic su **Send** (Invia). Questo comando si connette a Ricerca di Azure, legge la raccolta di indici e restituisce il codice di stato HTTP 200 se la connessione riesce. Se il servizio ha già indici, la risposta includerà anche le definizioni di indice.

![Intestazione della richiesta Postman][6]

## <a name="1---create-an-index"></a>1 - Creare un indice

In Ricerca di Azure, l'indice viene generalmente creato prima di caricarlo con i dati. Per questa attività viene usata l'[API REST di creazione dell'indice](https://docs.microsoft.com/rest/api/searchservice/create-index). 

L'URL è stato esteso per includere il nome dell'indice `hotels`.

Per eseguire questa operazione in Postman:

1. Modificare il verbo in **PUT**.

2. Copiare questo URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels?api-version=2019-05-06`.

3. Specificare nel corpo della richiesta la definizione di indice mostrata di seguito.

4. Fare clic su **Send**.

![Corpo della richiesta Postman][8]

### <a name="index-definition"></a>Definizione di indice

La raccolta campi (fields) definisce la struttura del documento. Ogni documento deve avere questi campi e ogni campo deve avere un tipo di dati. I campi stringa vengono usati nella ricerca full-text, quindi è consigliabile eseguire il cast dei dati numerici come stringhe se è necessario consentire l'esecuzione di ricerche nel contenuto.

Gli attributi nel campo determinano l'azione consentita. Le API REST consentono numerose azioni per impostazione predefinita. Tutte le stringhe, ad esempio, per impostazione predefinita consentono le ricerche, possono essere recuperate e filtrate e prevedono facet. Spesso è necessario impostare gli attributi solo per disattivare un comportamento.

          {
         "name": "hotels",  
         "fields": [
           {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
           {"name": "baseRate", "type": "Edm.Double"},
           {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
           {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
           {"name": "hotelName", "type": "Edm.String"},
           {"name": "category", "type": "Edm.String"},
           {"name": "tags", "type": "Collection(Edm.String)"},
           {"name": "parkingIncluded", "type": "Edm.Boolean"},
           {"name": "smokingAllowed", "type": "Edm.Boolean"},
           {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
           {"name": "rating", "type": "Edm.Int32"},
           {"name": "location", "type": "Edm.GeographyPoint"}
          ]
         }

Quando si invia questa richiesta, si dovrebbe ottenere una risposta HTTP 201, che indica che l'indice è stato creato correttamente. È possibile verificare questa azione nel portale, ma si noti che, in base agli intervalli di aggiornamento della pagina del portale, potrebbero essere necessari uno o due minuti.

> [!TIP]
> Se viene restituita una risposta HTTP 504, verificare se nell'URL è specificato HTTPS. Se viene visualizzata la risposta HTTP 400 o 404, esaminare il corpo della richiesta per verificare che le operazioni di copia e incolla sono state eseguite correttamente. Una risposta HTTP 403 indica in genere che si è verificato un problema con la chiave API (chiave non valida o problema di sintassi nella specifica della chiave API).

## <a name="2---load-documents"></a>2 - Caricare i documenti

La creazione e il popolamento dell'indice sono passaggi distinti. In Ricerca di Azure l'indice contiene tutti i dati che consentono le ricerche, che è possibile fornire come documenti JSON. Per questa attività viene usata l'[API REST di aggiunta, aggiornamento o eliminazione di documenti](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents). 

L'URL è stato esteso per includere le raccolte `docs` e l'operazione `index`.

Per eseguire questa operazione in Postman:

1. Modificare il verbo in **POST**.

2. Copiare questo URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels/docs/index?api-version=2019-05-06`.

3. Specificare nel corpo della richiesta i documenti JSON mostrati di seguito.

4. Fare clic su **Send**.

![Payload della richiesta Postman][10]

### <a name="json-documents-to-load-into-the-index"></a>Documenti JSON da caricare nell'indice

Il corpo della richiesta contiene quattro documenti da aggiungere all'indice degli hotel.

         {
         "value": [
         {
             "@search.action": "upload",
             "hotelId": "1",
             "baseRate": 199.0,
             "description": "Best hotel in town",
             "description_fr": "Meilleur hôtel en ville",
             "hotelName": "Fancy Stay",
             "category": "Luxury",
             "tags": ["pool", "view", "wifi", "concierge"],
             "parkingIncluded": false,
             "smokingAllowed": false,
             "lastRenovationDate": "2010-06-27T00:00:00Z",
             "rating": 5,
             "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
           },
           {
             "@search.action": "upload",
             "hotelId": "2",
             "baseRate": 79.99,
             "description": "Cheapest hotel in town",
             "description_fr": "Hôtel le moins cher en ville",
             "hotelName": "Roach Motel",
             "category": "Budget",
             "tags": ["motel", "budget"],
             "parkingIncluded": true,
             "smokingAllowed": true,
             "lastRenovationDate": "1982-04-28T00:00:00Z",
             "rating": 1,
             "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
           },
           {
             "@search.action": "upload",
             "hotelId": "3",
             "baseRate": 279.99,
             "description": "Surprisingly expensive",
             "hotelName": "Dew Drop Inn",
             "category": "Bed and Breakfast",
             "tags": ["charming", "quaint"],
             "parkingIncluded": true,
             "smokingAllowed": false,
             "lastRenovationDate": null,
             "rating": 4,
             "location": { "type": "Point", "coordinates": [-122.33207, 47.60621] }
           },
           {
             "@search.action": "upload",
             "hotelId": "4",
             "baseRate": 220.00,
             "description": "This could be the one",
             "hotelName": "A Hotel for Everyone",
             "category": "Basic hotel",
             "tags": ["pool", "wifi"],
             "parkingIncluded": true,
             "smokingAllowed": false,
             "lastRenovationDate": null,
             "rating": 4,
             "location": { "type": "Point", "coordinates": [-122.12151, 47.67399] }
           }
          ]
         }

Entro pochi secondi si dovrebbe visualizzare una risposta HTTP 200 nella sessione. Questo indica che i documenti sono stati creati correttamente. 

Se viene restituito un codice 207, significa che il caricamento di almeno uno dei documenti non è riuscito. Se viene restituito un codice 404, significa che è presente un errore di sintassi nell'intestazione o nel corpo della richiesta. Verificare di aver modificato l'endpoint per includere `/docs/index`.

> [!Tip]
> Per le origini dati selezionate, è possibile scegliere l'approccio alternativo tramite *indicizzatore*, che semplifica e riduce la quantità di codice necessario per l'indicizzazione. Per altre informazioni, vedere [Indexer operations](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) (Operazioni dell'indicizzatore).


## <a name="3---search-an-index"></a>3 - Eseguire la ricerca in un indice

Ora che l'indice e i documenti sono stati caricati, è possibile eseguire query su di essi con l'[API REST di ricerca documenti](https://docs.microsoft.com/rest/api/searchservice/search-documents).

L'URL viene esteso per includere una stringa di query, specificata usando l'operatore di ricerca.

Per eseguire questa operazione in Postman:

1. Cambiare il verbo in **GET**.

2. Copiare questo URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels/docs?search=motel&$count=true&api-version=2019-05-06`.

3. Fare clic su **Send**.

Questa query cerca il termine "motel" e restituisce il numero di documenti nei risultati della ricerca. La richiesta e la risposta dovrebbero essere simili allo screenshot seguente per Postman dopo avere fatto clic su **Send** (Invia). Il codice di stato dovrebbe essere 200.

 ![Risposta alla query Postman][11]

## <a name="get-index-properties"></a>Ottenere le proprietà dell'indice
È anche possibile eseguire una query relativa alle informazioni di sistema per ottenere il numero di documenti e informazioni sull'utilizzo dello spazio di archiviazione: `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels/stats?api-version=2019-05-06`

In Postman la richiesta dovrebbe essere simile alla seguente e la risposta include il numero di documenti e lo spazio usato in byte.

 ![Query di sistema Postman][12]

Si noti che la sintassi relativa alla versione API è diversa. Per questa richiesta, usare `?` per aggiungere l'elemento api-version. `?` separa il percorso dell'URL dalla stringa di query, mentre & separa ogni coppia "nome=valore" nella stringa di query. Per questa query, api-version è il primo e unico elemento nella stringa di query.

Per altre informazioni sull'API, vedere l'articolo relativo all'operazione [Get Index Statistics REST API](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics) (API REST per ottenere statistiche di indice).

## <a name="clean-up"></a>Eseguire la pulizia

Se non è più necessario il servizio di ricerca, il modo più veloce per rilasciare i servizi consiste nell'eliminare il gruppo di risorse contenente il servizio Ricerca di Azure. L'eliminazione del gruppo di risorse in modo permanente elimina tutti gli elementi in esso contenuti, tra cui i servizi e qualsiasi contenuto archiviato. Nel portale, il nome del gruppo di risorse è indicato nella pagina Panoramica di ciascun servizio.

## <a name="next-steps"></a>Passaggi successivi

I client REST sono molto utili per l'esplorazione improvvisata, ma ora che si conosce il funzionamento delle API REST, è possibile precedere con il codice. Per i passaggi successivi, vedere i collegamenti seguenti:

+ [Guida introduttiva: Creare un indice con .NET SDK](search-create-index-dotnet.md)
+ [Guida introduttiva: Creare un indice (REST) con PowerShell](search-create-index-rest-api.md)

<!--Image References-->
[1]: ./media/search-get-started-postman/fiddler-url.png
[2]: ./media/search-get-started-postman/AzureSearch_Fiddler2_PostDocs.png
[3]: ./media/search-get-started-postman/AzureSearch_Fiddler3_Query.png
[4]: ./media/search-get-started-postman/AzureSearch_Fiddler4_QueryResults.png
[5]: ./media/search-get-started-postman/AzureSearch_Fiddler5_QueryStats.png
[6]: ./media/search-get-started-postman/postman-url.png
[7]: ./media/search-get-started-postman/fiddler-request.png
[8]: ./media/search-get-started-postman/postman-request.png
[9]: ./media/search-get-started-postman/fiddler-docs.png
[10]: ./media/search-get-started-postman/postman-docs.png
[11]: ./media/search-get-started-postman/postman-query.png
[12]: ./media/search-get-started-postman/postman-system-query.png
