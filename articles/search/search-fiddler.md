---
title: Come utilizzare Fiddler per valutare e testare le API REST di Ricerca di Azure | Documentazione di Microsoft
description: "Utilizzare Fiddler per un approccio senza scrittura di codice alla verifica della disponibilità di Ricerca di Azure e alla valutazione delle API REST."
services: search
documentationcenter: 
author: HeidiSteen
manager: mblythe
editor: 
ms.assetid: 790e5779-c6a3-4a07-9d1e-d6739e6b87d2
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 10/17/2016
ms.author: heidist
translationtype: Human Translation
ms.sourcegitcommit: 6ff31940f3a4e7557e0caf3d9d3740590be3bc04
ms.openlocfilehash: 12f5a22fa65d84682e5792bcbe88b67986540498


---
# <a name="use-fiddler-to-evaluate-and-test-azure-search-rest-apis"></a>Utilizzare Fiddler per valutare e testare le API REST di Ricerca di Azure
> [!div class="op_single_selector"]
> * [Panoramica](search-query-overview.md)
> * [Esplora ricerche](search-explorer.md)
> * [Fiddler](search-fiddler.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
> 
> 

Questo articolo illustra come usare Fiddler, disponibile come [download gratuito da Telerik](http://www.telerik.com/fiddler), per eseguire richieste HTTP e visualizzare risposte attraverso l'API REST di Ricerca di Azure, senza dover scrivere codice. Ricerca di Azure è un servizio di ricerca cloud ospitato in Microsoft Azure e completamente gestito, facilmente programmabile con le API REST e .NET. Le API REST del servizio Ricerca di Azure sono documentate in [MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx).

Nei seguenti passaggi verrà creato un indice, verranno caricati i documenti, si eseguirà una query sull'indice e quindi si eseguirà una query sul sistema per ottenere informazioni sul servizio.

Per completare questi passaggi, è necessario disporre del servizio Ricerca di Azure e di `api-key`. Vedere [Creare un servizio di Ricerca di Azure nel portale](search-create-service-portal.md) per istruzioni su come iniziare.

## <a name="create-an-index"></a>Creare un indice
1. Avviare Fiddler Nel menu **File** disattivare **Capture Traffic** (Acquisisci traffico) per nascondere attività HTTP estranee non correlate all'attività corrente.
2. Nella scheda **Composer** formulare una richiesta simile alla schermata seguente.
   
      ![][1]
3. Selezionare **PUT**.
4. Immettere un URL che specifichi l'URL del servizio (disponibile nella pagina delle proprietà), gli attributi della richiesta e la versione dell'API. Tenere presente quanto segue:
   
   * Usare HTTPS come prefisso.
   * L'attributo della richiesta è "/indexes/hotels". In questo modo si comunica al servizio di ricerca di creare un indice denominato 'hotels'.
   * La versione API è in lettere minuscole, specificata come "?api-version=2015-02-28". Le versioni API sono importanti perché Ricerca di Azure distribuisce aggiornamenti su base regolare. In rare occasioni, un aggiornamento del servizio potrebbe introdurre modifiche significative all'API. Per questo motivo, Ricerca di Azure richiede la versione dell'API in ogni richiesta per garantire il controllo completo sulla versione usata.
     
     L'URL completo dovrebbe avere un aspetto simile a quello dell'esempio seguente.
     
             https://my-app.search.windows.net/indexes/hotels?api-version=2015-02-28
5. Specificare l'intestazione della richiesta sostituendo i valori di host e chiave api con valori validi per il servizio corrente.
   
         User-Agent: Fiddler
         host: my-app.search.windows.net
         content-type: application/json
         api-key: 1111222233334444
6. Nel corpo della richiesta incollare i campi che costituiscono la definizione dell'indice.
   
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
7. Fare clic su **Execute**.

Entro pochi secondi si dovrebbe visualizzare una risposta HTTP 201 nell'elenco della sessione che indica che la creazione dell'indice è stata eseguita correttamente.

Se viene restituita una risposta HTTP 504, verificare se nell'URL è specificato HTTPS. Se viene visualizzata la risposta HTTP 400 o 404, esaminare il corpo della richiesta per verificare che le operazioni di copia e incolla sono state eseguite correttamente. Una risposta HTTP 403 indica in genere che si è verificato un problema con la chiave API (chiave non valida o problema di sintassi nella specifica della chiave API).

## <a name="load-documents"></a>Caricare i documenti
Nella scheda **Composer** la richiesta di pubblicazione dei documenti avrà un aspetto simile al seguente. Il corpo della richiesta contiene i dati di ricerca per 4 hotel.

   ![][2]

1. Selezionare **POST**.
2. Immettere un URL che inizia con HTTPS, seguito dall'URL del servizio, seguito da "/indexes/<'indexname'>/docs/index?api-version=2015-02-28". L'URL completo dovrebbe avere un aspetto simile a quello dell'esempio seguente.
   
         https://my-app.search.windows.net/indexes/hotels/docs/index?api-version=2015-02-28
3. L'intestazione della richiesta deve essere come quella precedente. Ricordare che i valori di host e chiave api (lettere minuscole) sono stati sostituiti con valori validi per il servizio corrente.
   
         User-Agent: Fiddler
         host: my-app.search.windows.net
         content-type: application/json
         api-key: 1111222233334444
4. Il corpo della richiesta contiene quattro documenti da aggiungere all'indice degli hotel.
   
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
5. Fare clic su **Execute**.

Entro pochi secondi si dovrebbe visualizzare una risposta HTTP 200 nella sessione. Questo indica che i documenti sono stati creati correttamente. Se viene restituito un codice 207, significa che il caricamento di almeno uno dei documenti non è riuscito. Se viene restituito un codice 404 significa che è presente un errore di sintassi nell'intestazione o nel corpo della richiesta.

## <a name="query-the-index"></a>Eseguire una query sull'indice
Ora che l'indice e i documenti sono stati caricati, è possibile eseguire query su di essi.  Nella scheda **Composer** (Compositore) un comando **GET** che esegue una query sul servizio avrà un aspetto simile allo screenshot seguente.

   ![][3]

1. Selezionare **GET**.
2. Immettere un URL che inizia con HTTPS, seguito dall'URL del servizio, seguito da "/indexes/<'nome indice'>/docs?", seguito dai parametri di query. A esempio, usare l'URL seguente, sostituendo il nome host di esempio con un nome host valido per il proprio servizio.
   
         https://my-app.search.windows.net/indexes/hotels/docs?search=motel&facet=category&facet=rating,values:1|2|3|4|5&api-version=2015-02-28
   
   Questa query esegue la ricerca del termine "motel" e recupera le categorie facet per le valutazioni.
3. L'intestazione della richiesta deve essere come quella precedente. Ricordare che i valori di host e chiave api (lettere minuscole) sono stati sostituiti con valori validi per il servizio corrente.
   
         User-Agent: Fiddler
         host: my-app.search.windows.net
         content-type: application/json
         api-key: 1111222233334444

Il codice della risposta dovrebbe essere 200 e l'output della risposta dovrebbe avere un aspetto simile a quello della seguente schermata.

   ![][4]

La query di esempio seguente è tratta dall'argomento relativo alle [operazioni sull'indice di ricerca (API Ricerca di Azure)](http://msdn.microsoft.com/library/dn798927.aspx) su MSDN. Molte delle query di esempio descritte in questo argomento includono spazi, che non sono consentiti in Fiddler. Sostituire tutti gli spazi con un carattere + prima di incollare la stringa di query ed eseguirla in Fiddler.

**Prima della sostituzione degli spazi:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2015-02-28

**Dopo la sostituzione degli spazi con il carattere +:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2015-02-28

## <a name="query-the-system"></a>Eseguire query sul sistema
È possibile eseguire query sul sistema per ottenere il numero di documenti e informazioni sull'utilizzo dello spazio di archiviazione. Nella scheda **Composer** la richiesta avrà un aspetto simile al seguente e la risposta restituirà un conteggio relativo al numero di documenti e allo spazio utilizzato.

 ![][5]

1. Selezionare **GET**.
2. Immettere un URL che include l'URL del servizio, seguito da "/indexes/hotels/stats?api-version=2015-02-28":
   
         https://my-app.search.windows.net/indexes/hotels/stats?api-version=2015-02-28
3. Specificare l'intestazione della richiesta sostituendo i valori di host e chiave api con valori validi per il servizio corrente.
   
         User-Agent: Fiddler
         host: my-app.search.windows.net
         content-type: application/json
         api-key: 1111222233334444
4. Lasciare il corpo della richiesta vuoto.
5. Fare clic su **Execute**. Entro pochi secondi si dovrebbe visualizzare un codice di stato HTTP 200 nell'elenco della sessione. Selezionare la voce inserita per il comando.
6. Fare clic sulla scheda **Inspectors** (Controlli), sulla scheda **Headers** (Intestazioni) e quindi selezionare il formato JSON. Verrà visualizzato il numero di documenti e la dimensione dello spazio di archiviazione (in KB).

## <a name="next-steps"></a>Passaggi successivi
Vedere [Gestire il servizio di ricerca in Microsoft Azure](search-manage.md) per un approccio senza scrittura di codice alla gestione e all'uso di Ricerca di Azure.

<!--Image References-->
[1]: ./media/search-fiddler/AzureSearch_Fiddler1_PutIndex.png
[2]: ./media/search-fiddler/AzureSearch_Fiddler2_PostDocs.png
[3]: ./media/search-fiddler/AzureSearch_Fiddler3_Query.png
[4]: ./media/search-fiddler/AzureSearch_Fiddler4_QueryResults.png
[5]: ./media/search-fiddler/AzureSearch_Fiddler5_QueryStats.png



<!--HONumber=Nov16_HO2-->


