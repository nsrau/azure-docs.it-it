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
	ms.date="11/04/2015" 
	ms.author="heidist"/>

# Introduzione a Ricerca di Azure

Ricerca di Microsoft Azure è un servizio di ricerca ospitato sul cloud che consente di incorporare funzionalità di ricerca in applicazioni personalizzate. Fornisce il motore di ricerca e l'archiviazione per i dati, è possibile accedere e gestire utilizzando una libreria .NET o un'API REST.

In questo articolo viene presentato con l'API REST di Ricerca di Azure.

Un approccio alternativo per gli sviluppatori .NET consiste nell'utilizzare Search.NET Azure SDK. Vedere [iniziare con la Ricerca di Azure in .NET](search-get-started-dotnet.md) o [come utilizzare la ricerca di Azure .NET SDK](search-howto-dotnet-sdk.md) per informazioni dettagliate.


> [AZURE.NOTE]Per completare questa esercitazione richiede un [sottoscrizione Azure](../includes/free-trial-note.md). Se non si è pronti per iscriversi a una sottoscrizione di valutazione, è possibile ignorare questa esercitazione e scegliere invece [provare Azure App Service](https://tryappservice.azure.com/). Questa opzione alternativa non consente di Ricerca di Azure con un'app Web ASP.NET gratuitamente - un'ora per ogni sessione - è necessaria la sottoscrizione.
 
<a id="sub-1"></a>
## Iniziare con il servizio gratuito

In qualità di amministratore, è possibile aggiungere il servizio di ricerca a una sottoscrizione esistente senza costi aggiuntivi se si sceglie il servizio condiviso o a un prezzo scontato se si sceglie la soluzione con risorse dedicate.

I sottoscrittori possono usufruire dell'accesso gratuito al servizio di ricerca che può essere usato ai fini dell'apprendimento, per testare modelli di prova o per progetti di ricerca sviluppo di piccole dimensioni. Per iscriversi alla versione gratuita, attenersi alla procedura seguente.

1. Accedere a [Portale di Azure](https://portal.azure.com) utilizzando la sottoscrizione esistente. Si noti che questo URL reindirizzerà l'utente al portale di anteprima. Il portale di anteprima costituisce un requisito. 

2. Nella parte superiore della pagina fare clic su **Save**.
 
  	![][6]

3. Fare clic su **dati + archiviazione** | **Ricerca**.

	- Digitare un nome di servizio in lettere minuscole da usare per l'URL del servizio, evitando l'uso di spazi e non superando il limite di 15 caratteri.

	- Fare clic sulla freccia in **Livello di prezzo** per selezionare un'opzione di prezzo. Scegliere **GRATUITO** e quindi fare clic su **SELEZIONA** nella parte inferiore della pagina. La versione gratuita offre capacità sufficiente per provare le esercitazioni e scrivere codice per i modelli di prova, ma non è consigliata per le applicazioni in ambienti di produzione.

	- Fare clic sulla freccia in **Gruppo di risorse** per selezionare un gruppo esistente o crearne uno nuovo. I gruppi di risorse sono contenitori per i servizi e le risorse usati per uno scopo comune. Ad esempio, se si intende creare un'applicazione di ricerca personalizzata basata su Ricerca di Azure, Siti Web di Azure, il servizio di archiviazione BLOB di Azure, è possibile creare un gruppo di risorse per raggruppare questi servizi nelle pagine di gestione del portale.

	- Fare clic sulla freccia in **Sottoscrizione** se sono presenti più sottoscrizione e si vuole usarne una diversa per questo servizio di ricerca.

	- Fare clic sulla freccia in **Percorso** per scegliere l'area geografica del data center. In questa anteprima è possibile scegliere Stati Uniti occidentali, Stati Uniti orientali, Europa settentrionale e Asia sudorientale. Successivamente, quando altre aree geografiche saranno disponibili online, sarà possibile scegliere l'area più appropriata per il servizio che verrà creato. La distribuzione di risorse tra più data center non è una configurazione supportata per l'anteprima pubblica.

4. Fare clic su **CREA** per eseguire il provisioning del servizio. Si noti che l'opzione **CREA** viene abilitata solo dopo aver compilato tutti i valori obbligatori.

Il servizio verrà creato entro pochi minuti. È possibile tornare alle impostazioni di configurazione per ottenere l'URL o le chiavi API. Per le connessioni al servizio di ricerca è necessario disporre sia dell'URL che di una chiave API per l'autenticazione della chiamata. Ecco come trovare rapidamente questi valori:

14. Andare a **Home** per aprire il dashboard. Fare clic sul servizio di ricerca per aprire il dashboard servizi. 

  	![][13]

15.	Nel dashboard del servizio sono disponibili i riquadri per **PROPRIETÀ** e **CHIAVI**, oltre alle informazioni sull'utilizzo che mostrano l'utilizzo delle risorse a colpo d'occhio.

Passare alla sezione [Testare le operazioni del servizio](#sub-3) per le istruzioni su come connettersi al servizio usando questi valori.

<a id="sub-2"></a>
## Eseguire l'aggiornamento al servizio di ricerca standard

Il servizio di ricerca standard consente di recuperare risorse dedicate che possono essere usate solo dall'utente in un data center di Azure. I carichi di lavoro di ricerca richiedono repliche di archiviazione e del servizio. Quando si effettua l'iscrizione per la ricerca standard, è possibile ottimizzare la configurazione del servizio per usare una maggiore quantità della risorsa più importante per lo scenario corrente.

Le risorse dedicate offrono maggiore scalabilità e prestazioni migliori, ma non funzionalità aggiuntive. Sia la ricerca standard che quella condivisa offrono le stesse funzionalità.

Per usare la ricerca standard, creare un nuovo servizio di ricerca e scegliere il livello di prezzo Standard. Si noti che l'aggiornamento non è un aggiornamento sul posto della versione gratuita. Il passaggio al livello Standard, che include la scalabilità, richiede un nuovo servizio. Sarà necessario ricaricare gli indici e i documenti usati dall'applicazione di ricerca.

La configurazione di risorse dedicate può richiedere fino a 15 minuti o più.

**Passaggio 1: creare un nuovo servizio con livello di prezzo impostato su Standard**

1. Accedere a [Portale di Azure](https://portal.azure.com) utilizzando la sottoscrizione esistente. 

2. Fare clic su **New** nella parte inferiore della pagina.

4. Dalla raccolta, fare clic su **dati + archiviazione** | **Ricerca**.

7. Specificare le impostazioni di configurazione del servizio e quindi fare clic su **Crea**.

8. In **livello di prezzo** per selezionare un'opzione di pagamento. Scegliere **STANDARD** e quindi fare clic su **SELEZIONA** nella parte inferiore della pagina.

**Passaggio 2: Impostare le unità di ricerca in base ai requisiti di scalabilità**

La ricerca standard inizia con una replica e una partizione, ma può essere facilmente ridimensionata a livelli più elevati di risorse.

1.	Dopo avere creato il servizio, tornare al dashboard del servizio e fare clic sul riquadro **Ridimensiona**.

2.	Usare i dispositivi di scorrimento per aggiungere repliche, partizioni o entrambe.

Le repliche e le partizioni aggiuntive vengono fatturate in unità di ricerca. Le unità di ricerca totali per supportare qualsiasi configurazione di risorsa specifica vengono visualizzate nella pagina, durante l'aggiunta delle risorse.

Per informazioni sul prezzo per unità, vedere [Dettagli prezzi](http://go.microsoft.com/fwlink/p/?LinkID=509792). Per assistenza su come configurare le combinazioni di partizione e replica, vedere [Limitazioni e vincoli](search-limits-quotas-capacity.md).

 ![][15]

<a id="sub-3"></a>
## Testare le operazioni del servizio

L'ultimo passaggio della configurazione del servizio di ricerca prevede la conferma che il servizio sia operativo e accessibile da un'applicazione client. Per questa procedura viene usato Fiddler, disponibile come [download gratuito da Telerik](http://www.telerik.com/fiddler) per il rilascio di richieste HTTP e risposte di visualizzazione. Fiddler consente di testare immediatamente l'API senza dover scrivere righe di codice.

La procedura seguente funziona sia per la ricerca condivisa che per la ricerca standard. Nella procedura seguente verrà creato un indice, verranno caricati i documenti, si eseguirà una query sull'indice e quindi si eseguirà una query sul sistema per ottenere informazioni sul servizio.

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

1.	Accedere a [Portale di Azure](https://portal.azure.com) utilizzando la sottoscrizione esistente. 
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

<!---HONumber=Nov15_HO3-->