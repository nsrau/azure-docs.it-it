---
title: 'Guida introduttiva: Esplorare le API REST in Postman - Ricerca di Azure'
description: Informazioni su come usare Postman per inviare richieste HTTP e chiamate API REST a Ricerca di Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 7db3292bc5f377d9728e42994dd3a437cb59958e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024801"
---
# <a name="quickstart-explore-azure-search-rest-apis-using-postman"></a>Guida introduttiva: esplorare le API REST di Ricerca di Azure con Postman
> [!div class="op_single_selector"]
> * [Postman](search-fiddler.md)
> * [C#](search-create-index-dotnet.md)
> * [Portale](search-get-started-portal.md)
> * [PowerShell](search-howto-dotnet-sdk.md)
>*

Uno dei modi più semplici per esplorare l'[API REST di Ricerca di Azure](https://docs.microsoft.com/rest/api/searchservice) consiste nell'usare Postman o un altro strumento di test Web per formulare le richieste HTTP ed esaminare le risposte. Con gli strumenti corretti e con queste istruzioni è possibile inviare richieste e visualizzare le risposte prima di scrivere codice.

> [!div class="checklist"]
> * Scaricare uno strumento di test delle API Web
> * Ottenere una chiave e URL per il servizio di Ricerca
> * Connettersi a Ricerca di Azure
> * Creare un indice
> * Caricare un indice
> * Eseguire la ricerca in un indice

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare e quindi [eseguire la registrazione a Ricerca di Azure](search-create-service-portal.md).

## <a name="prerequisites"></a>Prerequisiti

In questa guida di avvio rapido vengono usati i servizi e gli strumenti seguenti. 

[Creare un servizio Ricerca di Azure](search-create-service-portal.md) o [trovare un servizio esistente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) nella sottoscrizione corrente. È possibile usare un servizio gratuito per questo avvio rapido. 

Per l'invio di richieste a Ricerca di Azure viene usata l'[app desktop Postman](https://www.getpostman.com/) oppure [Telerik Fiddler](https://www.telerik.com/fiddler).

## <a name="get-a-key-and-url"></a>Ottenere una chiave e un URL

Le chiamate REST richiedono l'URL del servizio e una chiave di accesso per ogni richiesta. Con entrambi gli elementi viene creato un servizio di ricerca, quindi se si è aggiunto Ricerca di Azure alla sottoscrizione, seguire questi passaggi per ottenere le informazioni necessarie:

1. [Accedere al portale di Azure](https://portal.azure.com/) e ottenere l'URL nella pagina **Panoramica** del servizio di ricerca. Un endpoint di esempio potrebbe essere simile a `https://mydemo.search.windows.net`.

1. In **Impostazioni** > **Chiavi** ottenere una chiave amministratore per diritti completi sul servizio. Sono disponibili due chiavi amministratore interscambiabili, fornite per continuità aziendale nel caso in cui sia necessario eseguire il rollover di una di esse. È possibile usare la chiave primaria o secondaria nelle richieste per l'aggiunta, la modifica e l'eliminazione di oggetti.

![Ottenere una chiave di accesso e un endpoint HTTP](media/search-fiddler/get-url-key.png "Ottenere una chiave di accesso e un endpoint HTTP")

Per ogni richiesta inviata al servizio è necessario specificare una chiave API. La presenza di una chiave valida stabilisce una relazione di trust, in base alle singole richieste, tra l'applicazione che invia la richiesta e il servizio che la gestisce.

## <a name="connect-to-azure-search"></a>Connettersi a Ricerca di Azure

In questa sezione usare lo strumento Web preferito per configurare le connessioni a Ricerca di Azure. Ogni strumento conserva le informazioni dell'intestazione della richiesta per la sessione, quindi è necessario immettere la chiave API e il tipo di contenuto una sola volta.

Per ogni strumento è necessario scegliere un comando (GET, POST, PUT e così via), fornire un endpoint URL e per alcune attività fornire il codice JSON nel corpo della richiesta. L'URL completo è simile al seguente:

    https://<placeholder-for-your-service-name>.search.windows.net/indexes?api-version=2019-05-06

Si notino il prefisso HTTPS, il nome del servizio, il nome di un oggetto (in questo caso la raccolta di indici) e la [versione API](search-api-versions.md). La versione API è una stringa obbligatoria in caratteri minuscoli specificata come `?api-version=2019-05-06` per la versione corrente. Le versioni API vengono aggiornate regolarmente. Includendo l'elemento api-version in ogni richiesta, è possibile avere controllo completo sulla versione API usata.  

La composizione dell'intestazione della richiesta include due elementi, il tipo di contenuto e la chiave API usata per l'autenticazione in Ricerca di Azure:

    api-key: <placeholder-api-key-for-your-service>
    Content-Type: application/json

In Postman formulare una richiesta simile allo screenshot seguente. Scegliere **GET** come verbo, specificare l'URL e fare clic su **Send** (Invia). Questo comando si connette a Ricerca di Azure, legge la raccolta di indici e restituisce il codice di stato HTTP 200 se la connessione riesce. Se il servizio ha già indici, la risposta includerà anche le definizioni di indice.

![Intestazione della richiesta Postman][6]

## <a name="1---create-an-index"></a>1 - Creare un indice

In Ricerca di Azure, l'indice viene generalmente creato prima di caricarlo con i dati. Per questa attività viene usata l'API REST di [creazione dell'indice](https://docs.microsoft.com/rest/api/searchservice/create-index). 

L'URL è stato esteso per includere il nome dell'indice `hotel`.

Per eseguire questa operazione in Postman:

1. Modificare il verbo in **PUT**.
2. Copiare in questo URL `https://<placeholder-for-your-service-name>.search.windows.net/indexes/hotel?api-version=2019-05-06`
3. Specificare la definizione di indice mostrata di seguito nel corpo della richiesta
4. Fare clic su **Invia**

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

La creazione e il popolamento dell'indice sono passaggi distinti. In Ricerca di Azure l'indice contiene tutti i dati che consentono le ricerche, che è possibile fornire come documenti JSON. Per questa attività viene usata l'API REST di [aggiunta, aggiornamento o eliminazione di documenti](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents). 

L'URL è stato esteso per includere le raccolte `docs` e l'operazione `index`.

Per eseguire questa operazione in Postman:

1. Modificare il verbo in **POST**.
2. Copiare in questo URL `https://<placeholder-for-your-service-name>.search.windows.net/indexes/hotels/docs/index?api-version=2019-05-06`
3. Specificare i documenti JSON mostrati di seguito nel corpo della richiesta
4. Fare clic su **Invia**

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

Ora che l'indice e i documenti sono stati caricati, è possibile eseguire query su di essi con l'API REST di [ricerca documenti](https://docs.microsoft.com/rest/api/searchservice/search-documents).

L'URL viene esteso per includere una stringa di query, specificata usando l'operatore di ricerca.

Per eseguire questa operazione in Postman:

+ Modificare il verbo in **GET**.
+ Copiare in questo URL `https://<placeholder-for-your-service-name>.search.windows.net/indexes/hotels/docs?search=motel&$count=true&api-version=2019-05-06`
+ Fare clic su **Invia**

Questa query cerca il termine "motel" e restituisce il numero di documenti nei risultati della ricerca. La richiesta e la risposta dovrebbero essere simili allo screenshot seguente per Postman dopo avere fatto clic su **Send** (Invia). Il codice di stato dovrebbe essere 200.

 ![Risposta alla query Postman][11]


## <a name="get-index-properties"></a>Ottenere le proprietà dell'indice
È anche possibile eseguire una query relativa alle informazioni di sistema per ottenere il numero di documenti e informazioni sull'utilizzo dello spazio di archiviazione: `https://mydemo.search.windows.net/indexes/hotels/stats?api-version=2019-05-06`

In Postman la richiesta dovrebbe essere simile alla seguente e la risposta include il numero di documenti e lo spazio usato in byte.

 ![Query di sistema Postman][12]

Si noti che la sintassi relativa alla versione API è diversa. Per questa richiesta, usare `?` per aggiungere l'elemento api-version. `?` separa il percorso dell'URL dalla stringa di query, mentre & separa ogni coppia "nome=valore" nella stringa di query. Per questa query, api-version è il primo e unico elemento nella stringa di query.

Per altre informazioni su quest'API, vedere l'articolo relativo all'operazione [Get Index Statistics (REST)](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics).


## <a name="use-fiddler"></a>Usare Fiddler

Questa sezione è equivalente alle sezioni precedenti, ma contiene schermate e istruzioni relative a Fiddler.

### <a name="connect-to-azure-search"></a>Connettersi a Ricerca di Azure

Formulare una richiesta simile allo screenshot seguente. Scegliere **GET** come verbo. Fiddler aggiunge `User-Agent=Fiddler`. È possibile incollare due intestazioni delle richieste aggiuntive nelle nuove righe al di sotto dell'elemento. Includere il tipo di contenuto e la chiave API per il servizio, usando la chiave di accesso di amministratore per il servizio.

Per la destinazione, copiare in una versione modificata dell'URL: `https://<placeholder-for-your-service-name>.search.windows.net/indexes?api-version=2019-05-06`

![Intestazione della richiesta Fiddler][1]

> [!Tip]
> Disattivare il traffico Web per nascondere l'attività HTTP estranea non correlata. Nel menu **File** di Fiddler disattivare l'opzione **Capture Traffic** (Acquisisci traffico). 

### <a name="1---create-an-index"></a>1 - Creare un indice

Modificare il verbo in **PUT**. Copiare una versione modificata dell'URL: `https://<placeholder-for-your-service-name>.search.windows.net/indexes/hotel?api-version=2019-05-06`. Copiare la definizione di indice specificata in precedenza nel corpo della richiesta. La pagina sarà simile alla schermata seguente. Fare clic su **Execute** (Esegui) in alto a destra per inviare la richiesta completata.

![Corpo della richiesta Fiddler][7]

### <a name="2---load-documents"></a>2 - Caricare i documenti

Modificare il verbo in **POST**. Modificare l'URL per includere `/docs/index`. Copiare i documenti nel corpo della richiesta, in modo analogo allo screenshot seguente, e quindi eseguire la richiesta.

![Payload della richiesta Fiddler][9]

### <a name="tips-for-running-our-sample-queries-in-fiddler"></a>Suggerimenti per l'esecuzione delle query di esempio in Fiddler

La query di esempio seguente è tratta dall'argomento relativo all'[operazione di ricerca nell'indice (API di Ricerca di Azure)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents). Molte delle query di esempio in questo articolo includono spazi, che non sono consentiti in Fiddler. Sostituire tutti gli spazi con un carattere + prima di incollare la stringa di query ed eseguirla in Fiddler.

**Prima della sostituzione degli spazi (in lastRenovationDate desc):**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2019-05-06

**Dopo la sostituzione degli spazi con + (in lastRenovationDate+desc):**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2019-05-06

### <a name="tips-for-viewing-index-statistic-in-fiddler"></a>Suggerimenti per la visualizzazione delle statistiche di indice in Fiddler

In Fiddler fare clic sulla scheda **Inspectors** (Controlli), quindi sulla scheda **Headers** (Intestazioni) e selezionare il formato JSON. Verrà visualizzato il numero di documenti e la dimensione dello spazio di archiviazione (in KB).

## <a name="next-steps"></a>Passaggi successivi

I client REST sono molto utili per l'esplorazione improvvisata, ma ora che si conosce il funzionamento delle API REST, è possibile precedere con il codice. Per i passaggi successivi, vedere i collegamenti seguenti:

+ [Guida introduttiva: Creare un indice con .NET SDK](search-create-index-dotnet.md)
+ [Guida introduttiva: Creare un indice (REST) con PowerShell](search-create-index-rest-api.md)

<!--Image References-->
[1]: ./media/search-fiddler/fiddler-url.png
[2]: ./media/search-fiddler/AzureSearch_Fiddler2_PostDocs.png
[3]: ./media/search-fiddler/AzureSearch_Fiddler3_Query.png
[4]: ./media/search-fiddler/AzureSearch_Fiddler4_QueryResults.png
[5]: ./media/search-fiddler/AzureSearch_Fiddler5_QueryStats.png
[6]: ./media/search-fiddler/postman-url.png
[7]: ./media/search-fiddler/fiddler-request.png
[8]: ./media/search-fiddler/postman-request.png
[9]: ./media/search-fiddler/fiddler-docs.png
[10]: ./media/search-fiddler/postman-docs.png
[11]: ./media/search-fiddler/postman-query.png
[12]: ./media/search-fiddler/postman-system-query.png