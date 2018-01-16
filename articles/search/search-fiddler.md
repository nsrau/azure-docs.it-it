---
title: Esplorare le API REST in Fiddler o Postman (REST di Ricerca di Azure) | Microsoft Docs
description: Come usare Fiddler o Postman per inviare richieste HTTP e chiamate alle API REST in Ricerca di Azure.
services: search
documentationcenter: 
author: HeidiSteen
manager: cgronlun
editor: 
ms.assetid: 
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 01/04/2018
ms.author: heidist
ms.openlocfilehash: a9a6bc035ba9f02057e3086a9682b54282da19f3
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/13/2018
---
# <a name="explore-azure-search-rest-apis-using-fiddler-or-postman"></a>Esplorare le API REST di Ricerca di Azure con Fiddler o Postman

Uno dei modi più semplici per esplorare l'[API REST di Ricerca di Azure](https://docs.microsoft.com/rest/api/searchservice) consiste nell'usare Fiddler o Postman per formulare le richieste HTTP e analizzare le risposte. Con gli strumenti corretti e con queste istruzioni è possibile inviare richieste e visualizzare le risposte prima di scrivere codice.

> [!div class="checklist"]
> * Scaricare uno strumento di test delle API Web
> * Ottenere la chiave API e l'endpoint per il servizio di ricerca
> * Configurare le intestazioni delle richieste
> * Creare un indice
> * Caricare un indice
> * Eseguire la ricerca in un indice

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare e quindi [eseguire la registrazione a Ricerca di Azure](search-create-service-portal.md).

## <a name="download-and-install-tools"></a>Scaricare e installare gli strumenti

Gli strumenti seguenti sono spesso usati per lo sviluppo Web, ma se si ha familiarità con un altro strumento, le istruzioni riportate in questo articolo dovrebbero essere comunque valide.

+ [Postman (componente aggiuntivo di Google Chrome)](https://www.getpostman.com/)
+ [Telerik Fiddler](http://www.telerik.com/fiddler)

## <a name="get-the-api-key-and-endpoint"></a>Ottenere la chiave API e l'endpoint

Le chiamate REST richiedono l'URL del servizio e una chiave di accesso per ogni richiesta. Con entrambi gli elementi viene creato un servizio di ricerca, quindi se si è aggiunto Ricerca di Azure alla sottoscrizione, seguire questi passaggi per ottenere le informazioni necessarie:

1. Nel portale di Azure aprire la pagina del servizio di ricerca dal dashboard o [trovare il servizio](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) nell'elenco di servizi.
2. Ottenere l'endpoint in **Panoramica** > **Informazioni di base** > **URL**. Un endpoint di esempio potrebbe essere simile a `https://my-service-name.search.windows.net`.
3. Ottenere la chiave API in **Impostazioni** > **Chiavi**. Ci sono due chiavi amministratore per offrire ridondanza nel caso in cui si voglia eseguire il rollback delle chiavi. Le chiavi amministratore concedono le autorizzazioni di scrittura per il servizio, necessarie per creare e caricare gli indici. Per le operazioni di scrittura è possibile usare la chiave primaria o secondaria.

## <a name="configure-request-headers"></a>Configurare le intestazioni delle richieste

Ogni strumento conserva le informazioni dell'intestazione della richiesta per la sessione, quindi è necessario immettere l'endpoint dell'URL, la versione API, la chiave API e il tipo di contenuto una sola volta.

L'URL completo dovrebbe essere simile all'esempio seguente, con un valore valido per il segnaposto **`my-app`**: `https://my-app.search.windows.net/indexes/hotels?api-version=2016-09-01`

La composizione dell'URL del servizio include gli elementi seguenti:

+ Prefisso HTTPS.
+ URL del servizio, ottenuto dal portale.
+ Risorsa, un'operazione che crea un oggetto nel servizio. In questo passaggio si tratta di un indice denominato hotels.
+ Versione API, una stringa obbligatoria in caratteri minuscoli specificata come "?api-version=2016-09-01" per la versione corrente. Le [versioni API](search-api-versions.md) vengono aggiornate regolarmente. Includendo l'elemento api-version in ogni richiesta, è possibile avere controllo completo sulla versione API usata.  

La composizione dell'intestazione della richiesta include due elementi, il tipo di contenuto e la chiave API, come descritto nella sezione precedente:

         content-type: application/json
         api-key: <placeholder>

### <a name="fiddler"></a>Fiddler

Formulare una richiesta simile allo screenshot seguente. Scegliere **PUT** come verbo. Fiddler aggiunge `User-Agent=Fiddler`. È possibile incollare due intestazioni delle richieste aggiuntive nelle nuove righe al di sotto dell'elemento. Includere il tipo di contenuto e la chiave API per il servizio, usando la chiave di accesso di amministratore per il servizio.

![Intestazione della richiesta Fiddler][1]

> [!Tip]
> È possibile disattivare il traffico Web per nascondere l'attività HTTP estranea non correlata alle attività che si stanno eseguendo. In Fiddler passare al menu **File** e disattivare l'opzione **Capture Traffic** (Acquisisci traffico). 

### <a name="postman"></a>postman

Formulare una richiesta simile allo screenshot seguente. Scegliere **PUT** come verbo. 

![Intestazione della richiesta Postman][6]

## <a name="create-the-index"></a>Creare l'indice

Il corpo della richiesta contiene la definizione di indice. Aggiungendo il corpo della richiesta, si completa la richiesta che produce l'indice.

Oltre al nome dell'indice, il componente più importante nella richiesta è la raccolta fields. La raccolta fields definisce lo schema dell'indice. In ogni campo, specificare il relativo tipo. I campi stringa vengono usati nella ricerca full-text, quindi è consigliabile eseguire il cast dei dati numerici come stringhe se è necessario consentire l'esecuzione di ricerche nel contenuto.

Gli attributi nel campo determinano l'azione consentita. Le API REST consentono numerose azioni per impostazione predefinita. Tutte le stringhe, ad esempio, per impostazione predefinita consentono le ricerche, possono essere recuperate e filtrate e prevedono facet. Spesso è necessario impostare gli attributi solo per disattivare un comportamento. Per altre informazioni sugli attributi, vedere l'articolo relativo all'operazione [Create Index (REST)](https://docs.microsoft.com/rest/api/searchservice/create-index).

          {
         "name": "hotels",  
         "fields": [
           {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
           {"name": "baseRate", "type": "Edm.Double"},
           {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
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

Se viene restituita una risposta HTTP 504, verificare se nell'URL è specificato HTTPS. Se viene visualizzata la risposta HTTP 400 o 404, esaminare il corpo della richiesta per verificare che le operazioni di copia e incolla sono state eseguite correttamente. Una risposta HTTP 403 indica in genere che si è verificato un problema con la chiave API (chiave non valida o problema di sintassi nella specifica della chiave API).

### <a name="fiddler"></a>Fiddler

Copiare la definizione di indice nel corpo della richiesta, in modo analogo allo screenshot seguente, e quindi fare clic su **Execute** (Esegui) in alto a destra per inviare la richiesta completata.

![Corpo della richiesta Fiddler][7]

### <a name="postman"></a>postman

Copiare la definizione di indice nel corpo della richiesta, in modo analogo allo screenshot seguente, e quindi fare clic su **Send** (Invia) in alto a destra per inviare la richiesta completata.

![Corpo della richiesta Postman][8]

## <a name="load-documents"></a>Caricare i documenti

La creazione e il popolamento dell'indice sono passaggi distinti. In Ricerca di Azure l'indice contiene tutti i dati che consentono le ricerche, che è possibile fornire come documenti JSON. Per esaminare l'API per questa operazione, vedere [Add, update, or delete documents (REST)](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) (Aggiungere, aggiornare o eliminare documenti - REST).

+ Modificare il verbo in **POST** per questo passaggio.
+ Modificare l'endpoint per includere `/docs/index`. L'URL completo dovrebbe essere simile a `https://my-app.search.windows.net/indexes/hotels/docs/index?api-version=2016-09-01`
+ Mantenere le intestazioni delle richieste così come sono. 

Il corpo della richiesta contiene quattro documenti da aggiungere all'indice degli hotel.

         {
         "value": [
         {
             "@search.action": "upload",
             "hotelId": "1",
             "baseRate": 199.0,
             "description": "Best hotel in town",
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

### <a name="fiddler"></a>Fiddler

Modificare il verbo in **POST**. Modificare l'URL per includere `/docs/index`. Copiare i documenti nel corpo della richiesta, in modo analogo allo screenshot seguente, e quindi eseguire la richiesta.

![Payload della richiesta Fiddler][9]

### <a name="postman"></a>postman

Modificare il verbo in **POST**. Modificare l'URL per includere `/docs/index`. Copiare i documenti nel corpo della richiesta, in modo analogo allo screenshot seguente, e quindi eseguire la richiesta.

![Payload della richiesta Postman][10]

## <a name="query-the-index"></a>Eseguire una query sull'indice
Ora che l'indice e i documenti sono stati caricati, è possibile eseguire query su di essi. Per altre informazioni su quest'API, vedere l'articolo relativo all'operazione [Search Documents (REST)](https://docs.microsoft.com/rest/api/searchservice/search-documents)  

+ Modificare il verbo in **GET** per questo passaggio.
+ Modificare l'endpoint per includere i parametri di query, comprese le stringhe di ricerca. Un URL di query potrebbe essere simile a `https://my-app.search.windows.net/indexes/hotels/docs?search=motel&$count=true&api-version=2016-09-01`
+ Mantenere le intestazioni delle richieste così come sono

Questa query cerca il termine "motel" e restituisce il numero di documenti nei risultati della ricerca. La richiesta e la risposta dovrebbero essere simili allo screenshot seguente per Postman dopo avere fatto clic su **Send** (Invia). Il codice di stato dovrebbe essere 200.

 ![Risposta alla query Postman][11]

### <a name="tips-for-running-our-sample-queries-in-fiddler"></a>Suggerimenti per l'esecuzione delle query di esempio in Fiddler

La query di esempio seguente è tratta dall'argomento relativo all'[operazione di ricerca nell'indice (API di Ricerca di Azure)](http://msdn.microsoft.com/library/dn798927.aspx). Molte delle query di esempio in questo articolo includono spazi, che non sono consentiti in Fiddler. Sostituire tutti gli spazi con un carattere + prima di incollare la stringa di query ed eseguirla in Fiddler.

**Prima della sostituzione degli spazi (in lastRenovationDate desc):**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2016-09-01

**Dopo la sostituzione degli spazi con + (in lastRenovationDate+desc):**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2016-09-01

## <a name="query-index-properties"></a>Eseguire una query per le proprietà dell'indice
È anche possibile eseguire una query relativa alle informazioni di sistema per ottenere il numero di documenti e informazioni sull'utilizzo dello spazio di archiviazione: `https://my-app.search.windows.net/indexes/hotels/stats?api-version=2016-09-01`

In Postman la richiesta dovrebbe essere simile alla seguente e la risposta include il numero di documenti e lo spazio usato in byte.

 ![Query di sistema Postman][12]

Si noti che la sintassi relativa alla versione API è diversa. Per questa richiesta, usare `?` per aggiungere l'elemento api-version. Il carattere ? separa il percorso dell'URL dalla stringa di query, mentre & separa ogni coppia "nome=valore" nella stringa di query. Per questa query, api-version è il primo e unico elemento nella stringa di query.

Per altre informazioni su quest'API, vedere l'articolo relativo all'operazione [Get Index Statistics (REST)](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics).


### <a name="tips-for-viewing-index-statistic-in-fiddler"></a>Suggerimenti per la visualizzazione delle statistiche di indice in Fiddler

In Fiddler fare clic sulla scheda **Inspectors** (Controlli), quindi sulla scheda **Headers** (Intestazioni) e selezionare il formato JSON. Verrà visualizzato il numero di documenti e la dimensione dello spazio di archiviazione (in KB).

## <a name="next-steps"></a>Passaggi successivi

I client REST sono molto utili per l'esplorazione improvvisata, ma ora che si conosce il funzionamento delle API REST, è possibile precedere con il codice. Per i passaggi successivi, vedere i collegamenti seguenti:

+ [Creare un indice (REST)](search-create-index-rest-api.md)
+ [Importare i dati (REST)](search-import-data-rest-api.md)
+ [Eseguire una ricerca in un indice (REST)](search-query-rest-api.md)

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