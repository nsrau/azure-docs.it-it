<properties 
	pageTitle="Introduzione a Ricerca di Azure | Microsoft Azure | Servizio di ricerca cloud ospitato" 
	description="Introduzione a Ricerca di Azure, un servizio di ricerca ospitato sul cloud in Microsoft Azure." 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""
    tags="azure-portal"/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="01/11/2016" 
	ms.author="heidist"/>

# Introduzione a Ricerca di Azure

Ricerca di Microsoft Azure è un servizio di ricerca ospitato sul cloud che consente di incorporare funzionalità di ricerca in applicazioni personalizzate. Fornisce il motore di ricerca e l'archiviazione per i dati, è possibile accedere e gestire utilizzando una libreria .NET o un'API REST.

In questo articolo viene presentato con l'API REST di Ricerca di Azure.

Un approccio alternativo per gli sviluppatori .NET consiste nell'utilizzare Search.NET Azure SDK. Vedere [iniziare con la Ricerca di Azure in .NET](search-get-started-dotnet.md) o [come utilizzare la ricerca di Azure .NET SDK](search-howto-dotnet-sdk.md) per informazioni dettagliate.


> [AZURE.NOTE]Per completare questa esercitazione richiede un [sottoscrizione Azure](../includes/free-trial-note.md). Se non si è pronti per iscriversi a una sottoscrizione di valutazione, è possibile ignorare questa esercitazione e scegliere invece [provare Azure App Service](https://tryappservice.azure.com/). Questa opzione alternativa non consente di Ricerca di Azure con un'app Web ASP.NET gratuitamente - un'ora per ogni sessione - è necessaria la sottoscrizione.
 
<a id="sub-1"></a>
## Creare un servizio Ricerca di Azure

In qualità di amministratore, è possibile aggiungere il servizio di ricerca a una sottoscrizione esistente senza costi aggiuntivi se si sceglie il servizio condiviso o a un prezzo scontato se si sceglie la soluzione con risorse dedicate.

I sottoscrittori possono usufruire dell'accesso gratuito al servizio di ricerca che può essere usato ai fini dell'apprendimento, per testare modelli di prova o per progetti di ricerca sviluppo di piccole dimensioni.

Accedere al [Portale di Azure](https://portal.azure.com) utilizzando la sottoscrizione esistente. Per istruzioni dettagliate, vedere [Creare un servizio di Ricerca di Azure nel portale](search-create-service-portal.md).

## Fornire l'URL del servizio e la chiave API

Dopo aver creato il servizio, è possibile tornare alle impostazioni di configurazione per ottenere l'URL e le chiavi API. Per le connessioni al servizio di ricerca è necessario disporre sia dell'URL che di una chiave API per l'autenticazione della chiamata. Ecco come trovare rapidamente questi valori:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nella barra di spostamento, fare clic su **Servizio di ricerca** per elencare tutti i servizi di Ricerca di Azure con provisioning per la sottoscrizione.
3. Selezionare il servizio che si desidera usare.
4.	Nel dashboard del servizio sono disponibili i riquadri per **PROPRIETÀ** e **CHIAVI**, oltre alle informazioni sull'utilizzo che mostrano l'utilizzo delle risorse a colpo d'occhio. 


<a id="sub-3"></a>
## Testare le operazioni del servizio

Come passaggio di convalida, verificare se il servizio è operativo e accessibile da un'applicazione client. Per questa procedura viene usato Fiddler, disponibile come [download gratuito da Telerik](http://www.telerik.com/fiddler) per il rilascio di richieste HTTP e risposte di visualizzazione. Fiddler consente di testare immediatamente l'API senza dover scrivere righe di codice.

Nella procedura seguente verrà creato un indice, verranno caricati i documenti, si eseguirà una query sull'indice e quindi si eseguirà una query sul sistema per ottenere informazioni sul servizio.

### Creare un indice

1. Avviare Fiddler Nel menu File disattivare **Capture Traffic** per nascondere attività HTTP estranee non correlate all'attività corrente. Nella scheda Composer formulare una richiesta con l'aspetto simile al seguente: 

  	![][16]

2. Selezionare **PUT**.

3. Immettere un URL che specifica l'URL del servizio (disponibile nella pagina delle proprietà), gli attributi della richiesta e la versione dell'API. Tenere presente quanto segue:
   + Usare HTTP come prefisso
   + L'attributo della richiesta è "/indexes/hotels". In questo modo si comunica al servizio di ricerca di creare un indice denominato 'hotels'.
   + La versione API è in lettere minuscole, specificata come "?api-version=2015-02-28". Le versioni API sono importanti perché Ricerca di Azure distribuisce aggiornamenti su base regolare. In rare occasioni, un aggiornamento del servizio potrebbe introdurre modifiche significative all'API. Con le versioni API, è possibile continuare a usare la versione esistente ed effettuare l'aggiornamento a una più recente nel momento più opportuno.

    L'URL completo dovrebbe avere un aspetto simile a quello dell'esempio seguente:

         https://my-app.search.windows.net/indexes/hotels?api-version=2015-02-28

4.	Specificare l'intestazione della richiesta sostituendo i valori di host e chiave api con valori validi per il servizio corrente.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

5.	Nel corpo della richiesta incollare i campi che costituiscono la definizione dell'indice.

         {
        "name": "hotels",  
        "fields": [
          {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
          {"name": "baseRate", "type": "Edm.Double"},
          {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
          {"name": "hotelName", "type": "Edm.String", "suggestions": true},
          {"name": "category", "type": "Edm.String"},
          {"name": "tags", "type": "Collection(Edm.String)"},
          {"name": "parkingIncluded", "type": "Edm.Boolean"},
          {"name": "smokingAllowed", "type": "Edm.Boolean"},
          {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
          {"name": "rating", "type": "Edm.Int32"},
          {"name": "location", "type": "Edm.GeographyPoint"}
         ] 
        }

6.	Fare clic su **Execute**.

Entro pochi secondi si dovrebbe visualizzare una risposta HTTP 201 nell'elenco della sessione che indica che la creazione dell'indice è stata eseguita correttamente.

Se viene restituita una risposta HTTP 504, verificare se nell'URL è specificato HTTPS. Se viene visualizzata la risposta HTTP 400 o 404, esaminare il corpo della richiesta per verificare che le operazioni di copia e incolla sono state eseguite correttamente. Una risposta HTTP 403 indica in genere che si è verificato un problema con la chiave API (chiave non valida o problema di sintassi nella specifica della chiave API).

### Caricare i documenti

Nella scheda Composer la richiesta di pubblicazione dei documenti avrà un aspetto simile al seguente. Il corpo della richiesta contiene i dati di ricerca per 4 hotel.

   ![][17]

1. Selezionare **POST**.

2.	Immettere un URL che inizia con HTTPS, seguito dall'URL del servizio, seguito da "/indexes/<'indexname'>/docs/index?api-version=2015-02-28". L'URL completo dovrebbe avere un aspetto simile a quello dell'esempio seguente:

        https://my-app.search.windows.net/indexes/hotels/docs/index?api-version=2015-02-28

3.	L'intestazione della richiesta deve essere come quella precedente. Ricordare che i valori di host e chiave api (lettere minuscole) sono stati sostituiti con valori validi per il servizio corrente.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

4.	Il corpo della richiesta contiene quattro documenti da aggiungere all'indice degli hotel.

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

8.	Fare clic su **Execute**.

Entro pochi secondi si dovrebbe visualizzare una risposta HTTP 200 nella sessione. Questo indica che i documenti sono stati creati correttamente. Se viene restituito un codice 207, significa che il caricamento di almeno uno dei documenti non è riuscito. Se viene restituito un codice 404 significa che è presente un errore di sintassi nell'intestazione o nel corpo della richiesta.

### Eseguire una query sull'indice

Ora che l'indice e i documenti sono stati caricati, è possibile eseguire query su di essi. Nella scheda Composer, un comando GET che esegue una query sul servizio avrà un aspetto simile al seguente:

   ![][18]

1.	Selezionare **GET**.

2.	Immettere un URL che inizia con HTTPS, seguito dall'URL del servizio, seguito da "/indexes/<'nome indice'>/docs?", seguito dai parametri di query. A esempio, usare l'URL seguente, sostituendo il nome host di esempio con un nome host valido per il proprio servizio.

        https://my-app.search.windows.net/indexes/hotels/docs?search=motel&facet=category&facet=rating,values:1|2|3|4|5&api-version=2015-02-28

    Questa query esegue la ricerca del termine "motel" e recupera le categorie facet per le valutazioni.

3.	L'intestazione della richiesta deve essere come quella precedente. Ricordare che i valori di host e chiave api (lettere minuscole) sono stati sostituiti con valori validi per il servizio corrente.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

Il codice della risposta dovrebbe essere 200 e l'output della risposta dovrebbe avere un aspetto simile a quello dell'immagine seguente.
 
   ![][19]

La query di esempio seguente è tratta dall'argomento relativo alle [operazioni sull'indice di ricerca (API Ricerca di Azure)](http://msdn.microsoft.com/library/dn798927.aspx) su MSDN. Molte query di esempio descritte in questo argomento includono gli spazi che non sono consentiti in Fiddler. Sostituire tutti gli spazi con un carattere + prima di incollare la stringa di query ed eseguirla in Fiddler.

**Prima della sostituzione degli spazi:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2015-02-28

**Dopo la sostituzione degli spazi con il carattere +:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2015-02-28
### Eseguire query sul sistema

È possibile eseguire query sul sistema per ottenere il numero di documenti e informazioni sull'utilizzo dello spazio di archiviazione. Nella scheda Composer la richiesta avrà un aspetto simile al seguente e la risposta restituirà un conteggio relativo al numero di documenti e allo spazio utilizzato.

   ![][20]

1.	Selezionare **GET**.

2.	Immettere un URL che include l'URL del servizio, seguito da "/indexes/hotels/stats?api-version=2015-02-28":

        https://my-app.search.windows.net/indexes/hotels/stats?api-version=2015-02-28 

3.	Specificare l'intestazione della richiesta sostituendo i valori di host e chiave api con valori validi per il servizio corrente.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

4.	Lasciare il corpo della richiesta vuoto.

5.	Fare clic su **Execute**. Entro pochi secondi si dovrebbe visualizzare un codice di stato HTTP 200 nell'elenco della sessione. Selezionare la voce inserita per il comando.

6.	Fare clic sulla scheda Inspectors | Headers e selezionare il formato JSON. Verrà visualizzato il numero di documenti e la dimensione dello spazio di archiviazione (in KB).

 	![][21]

<a id="sub-4"></a>
## Esplorare il dashboard del servizio di ricerca

Per un ripasso su dove trovare le pagine di configurazione, eseguire la procedura seguente per passare al dashboard servizi.

1.	Accedere al [Portale di Azure](https://portal.azure.com) utilizzando la sottoscrizione esistente. 
2.	Fare clic su **Home** e quindi fare clic sul riquadro per il servizio di ricerca.

 	![][22]

4.	per aprire il relativo dashboard servizi. Si noti che i comandi **Avvia**, **Arresta** ed **Elimina** si trovano nella parte superiore.

5.	L'URL del servizio è visualizzato nella parte superiore della pagina. Questo URL sarà necessario per connettersi al servizio di Ricerca di Azure.
	
7.	Scegliere il **CHIAVI** icona per visualizzare le chiavi api. Per eseguire l'autenticazione per il servizio è necessaria una chiave amministratore. È possibile usare quella primaria o quella secondaria. Facoltativamente, è possibile creare chiavi di query per l'accesso di sola lettura al servizio.


<!--Next steps and links -->
<a id="next-steps"></a>
## Provare il servizio

Pronti per il passaggio successivo? Fare clic sui collegamenti seguenti per accedere a materiale aggiuntivo in cui viene descritto come creare e gestire applicazioni di ricerca basate su Ricerca di Azure.

- [Creare un esempio di ricerca geografica in Ricerca di Azure](search-create-geospatial.md)

- [Gestire la soluzione di ricerca in Microsoft Azure](search-manage.md)

- [Che cos'è la Ricerca di Azure?](search-what-is-azure-search.md)

- [API REST per il servizio Ricerca di Azure](http://msdn.microsoft.com/library/dn798935.aspx)

- [Ricerca di Azure .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx)

- [Video di Channel 9: Introduzione a Ricerca di Azure](http://channel9.msdn.com/Shows/Data-Exposed/Introduction-To-Azure-Search)

- [Video del Channel 9: Ricerca di Azure e i dati geospaziali](http://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data)

- [Episodio di copertina cloud 152: generare un indice nella Ricerca di Azure](http://channel9.msdn.com/Shows/Cloud+Cover/Cloud-Cover-152-Azure-Search-with-Liam-Cavanagh)

<!--Anchors-->
[Start with the free service]: #sub-1
[Upgrade to standard search]: #sub-2
[Test service operations]: #sub-3
[Explore Search service dashboard]: #sub-4
[Try it out]: #next-steps

<!--Image references-->
[6]: ./media/search-get-started/AzureSearch_Configure1_1_New.PNG
[7]: ./media/search-get-started/AzureSearch_Configure1_2_Everything.PNG
[8]: ./media/search-get-started/Azuresearch_Configure1_3a_Gallery.PNG
[9]: ./media/search-get-started/AzureSearch_Configure1_4_GallerySeeAll.PNG
[10]: ./media/search-get-started/AzureSearch_Configure1_5_SearchTile.PNG
[11]: ./media/search-get-started/AzureSearch_Configure1_6_URL.PNG
[12]: ./media/search-get-started/AzureSearch_Configure1_7a_Free.PNG
[13]: ./media/search-get-started/AzureSearch_Configure1_17_HomeDashboard.PNG
[14]: ./media/search-get-started/AzureSearch_Configure1_9_Standard.PNG
[15]: ./media/search-get-started/AzureSearch_Configure1_10_ScaleUp.PNG
[16]: ./media/search-get-started/AzureSearch_Configure1_11_PUTIndex.PNG
[17]: ./media/search-get-started/AzureSearch_Configure1_12_POSTDocs.PNG
[18]: ./media/search-get-started/AzureSearch_Configure1_13_GETQuery.PNG
[19]: ./media/search-get-started/AzureSearch_Configure1_14_GETQueryResponse.PNG
[20]: ./media/search-get-started/AzureSearch_Configure1_15_Stats.PNG
[21]: ./media/search-get-started/AzureSearch_Configure1_16_StatsResponse.PNG
[22]: ./media/search-get-started/AzureSearch_Configure1_17_HomeDashboard.PNG
[23]: ./media/search-get-started/AzureSearch_Configure1_18_Explore.PNG


<!--Link references-->
[Manage your search solution in Microsoft Azure]: search-manage.md
[Azure Search development workflow]: search-workflow.md
[Create your first azure search solution]: search-create-first-solution.md
[Create a geospatial search app using Azure Search]: search-create-geospatial.md

<!---HONumber=AcomDC_0114_2016-->