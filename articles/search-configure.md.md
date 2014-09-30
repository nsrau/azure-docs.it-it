<properties title="Configure Search in the Azure Preview Portal" pageTitle="Configure Search in the Azure Preview Portal" description="Configure Search in the Azure Preview Portal" metaKeywords="" services="Azure Search" solutions="" documentationCenter="" authors="Heidist" videoId="" scriptId="" />

# Configurare il servizio di ricerca nel portale di anteprima di Azure

[WACOM.INCLUDE [Questo articolo usa il portale di anteprima di Azure][]]

Il servizio Ricerca di Microsoft Azure (anteprima pubblica) è disponibile nel nuovo portale di anteprima. In qualità di amministratore, è possibile aggiungere il servizio di ricerca a una sottoscrizione esistente senza costi aggiuntivi se si sceglie il servizio condiviso o a un prezzo scontato se si sceglie la soluzione con risorse dedicate. Questo articolo include le sezioni seguenti:

<!--Table of contents -->

-   [Iniziare con il servizio gratuito][]
-   [Eseguire l'aggiornamento al servizio di ricerca standard][]
-   [Testare le operazioni del servizio][]
-   [Esplorare le pagine di configurazione del servizio di ricerca][]
-   [Provare il servizio][]

## Iniziare con il servizio gratuito

I sottoscrittori possono usufruire dell'accesso gratuito al servizio di ricerca che può essere usato ai fini dell'apprendimento, per testare modelli di prova o per progetti di ricerca sviluppo di piccole dimensioni. Per iscriversi alla versione gratuita, attenersi alla procedura seguente.

1.  Accedere al [portale di anteprima di Azure][] con la sottoscrizione esistente. Si noti che questo URL reindirizzerà l'utente al portale di anteprima.

2.  Fare clic su **New** nella parte inferiore della pagina.

    ![][]

3.  Fare clic su **Tutto** nella parte superiore della pagina.

    ![][1]

4.  In Raccolta fare clic su **Dati, archiviazione, cache e backup**.

    ![][2]

5.  Fare clic su **Visualizza tutto** per espandere l'elenco dei servizi correlati ai dati.

    ![][3]

6.  In Servizi dati fare clic su **Ricerca**.

    ![][4]

7.  Nella parte inferiore della pagina Ricerca fare clic su **CREA**.

8.  Digitare un nome di servizio in lettere minuscole da usare per l'URL del servizio, evitando l'uso di trattini e spazi e non superando il limite di 15 caratteri.

    ![][5]

9.  Fare clic sulla freccia in **Livello di prezzo** per selezionare un'opzione di prezzo. Scegliere **GRATUITO** e quindi fare clic su **SELEZIONA** nella parte inferiore della pagina. La versione gratuita offre capacità sufficiente per provare le esercitazioni e scrivere codice per i modelli di prova, ma non è consigliata per le applicazioni in ambienti di produzione.

    ![][6]

10. Fare clic sulla freccia in **Gruppo di risorse** per selezionare un gruppo esistente o crearne uno nuovo. I gruppi di risorse sono contenitori per i servizi e le risorse usati per uno scopo comune. Ad esempio, se si intende creare un'applicazione di ricerca personalizzata basata su Ricerca di Azure, Siti Web di Azure, il servizio di archiviazione BLOB di Azure, è possibile creare un gruppo di risorse per raggruppare questi servizi nelle pagine di gestione del portale.

11. Fare clic sulla freccia in **Sottoscrizione** se sono presenti più sottoscrizione e si vuole usarne una diversa per questo servizio di ricerca.

12. Fare clic sulla freccia in **Percorso** per scegliere l'area geografica del data center. In questa anteprima è possibile scegliere Stati Uniti occidentali, Stati Uniti orientali, Europa settentrionale e Asia sudorientale. Successivamente, quando altre aree geografiche saranno disponibili online, sarà possibile scegliere l'area più appropriata per il servizio che verrà creato. La distribuzione di risorse tra più data center non è una configurazione supportata per l'anteprima pubblica.

13. Fare clic su **CREA** per eseguire il provisioning del servizio. Si noti che l'opzione **CREA** viene abilitata solo dopo aver compilato tutti i valori obbligatori.

Il servizio verrà creato entro pochi minuti. È possibile tornare alle impostazioni di configurazione per ottenere l'URL o le chiavi API. Per le connessioni al servizio di ricerca è necessario disporre sia dell'URL che di una chiave API per l'autenticazione della chiamata. Ecco come trovare rapidamente questi valori:

1.  Passare a **Sfoglia** | **Tutto** | **Dati, archiviazione, cache e backup** | **Visualizza tutto** | **Servizi di ricerca**, quindi fare clic sul servizio di ricerca desiderato per aprire il dashboard del servizio.

    ![][7]

2.  Nel dashboard del servizio sono disponibili i riquadri per **PROPRIETÀ** e **CHIAVI**, oltre alle informazioni sull'utilizzo che mostrano l'utilizzo delle risorse a colpo d'occhio.

    ![][8]

In **PROPRIETÀ** è visualizzato l'URL del servizio.

In **CHIAVI** sono visualizzate le chiavi API per l'autenticazione.

In **UTILIZZO** sono visualizzati il numero di documenti, le risorse disponibili e i limiti di archiviazione.

Passare alla sezione [Testare le operazioni del servizio][] per le istruzioni su come connettersi al servizio usando questi valori.

## Eseguire l'aggiornamento al servizio di ricerca standard

Il servizio di ricerca standard consente di recuperare risorse dedicate che possono essere usate solo dall'utente in un data center di Azure. I carichi di lavoro di ricerca richiedono repliche di archiviazione e del servizio. Quando si effettua l'iscrizione per la ricerca standard, è possibile ottimizzare la configurazione del servizio per usare una maggiore quantità della risorsa più importante per lo scenario corrente.

Le risorse dedicate offrono maggiore scalabilità e prestazioni migliori, ma non funzionalità aggiuntive. Sia la ricerca standard che quella condivisa offrono le stesse funzionalità.

Per usare la ricerca standard, creare un nuovo servizio di ricerca e scegliere il livello di prezzo Standard. Si noti che l'aggiornamento non è un aggiornamento sul posto della versione gratuita. Il passaggio al livello Standard, che include la scalabilità, richiede un nuovo servizio. Sarà necessario ricaricare gli indici e i documenti usati dall'applicazione di ricerca.

La configurazione di risorse dedicate può richiedere fino a 15 minuti o più.

**Passaggio 1: Creare un nuovo servizio con il livello di prezzo impostato su Standard**

1.  Accedere al [portale di anteprima di Azure][] con la sottoscrizione esistente.

2.  Fare clic su **New** nella parte inferiore della pagina.

3.  Fare clic su **Tutto** nella parte superiore della pagina.

4.  In Raccolta fare clic su **Dati, archiviazione, cache e backup**.

5.  Fare clic su **Visualizza tutto** per espandere l'elenco dei servizi correlati ai dati.

6.  In Servizi dati fare clic su **Ricerca**.

7.  Nella parte inferiore della pagina Ricerca fare clic su **CREA**.

8.  Digitare un nome di servizio in lettere minuscole da usare per l'URL del servizio, evitando l'uso di trattini e spazi e non superando il limite di 15 caratteri.

9.  Fare clic sulla freccia in **Livello di prezzo** per selezionare un'opzione di prezzo. Scegliere **STANDARD** e quindi fare clic su **SELEZIONA** nella parte inferiore della pagina.

![][9]

**Passaggio 2: Impostare le unità di ricerca in base ai requisiti di scalabilità**

La ricerca standard inizia con una replica e una partizione, ma può essere facilmente ridimensionata a livelli più elevati di risorse.

1.  Dopo avere creato il servizio, tornare al dashboard del servizio e fare clic sul riquadro **Ridimensiona**.

2.  Usare i dispositivi di scorrimento per aggiungere repliche, partizioni o entrambe.

Le repliche e le partizioni aggiuntive vengono fatturate in unità di ricerca. Le unità di ricerca totali per supportare qualsiasi configurazione di risorsa specifica vengono visualizzate nella pagina, durante l'aggiunta delle risorse. Per informazioni sul prezzo per unità, vedere [Dettagli prezzi][].

![][10]

## Testare le operazioni del servizio

L'ultimo passaggio della configurazione del servizio di ricerca prevede la conferma che il servizio sia operativo e accessibile da un'applicazione client. Per questa procedura viene usato Fiddler, disponibile come [download gratuito da Telerik][] per il rilascio di richieste HTTP e risposte di visualizzazione. Fiddler consente di testare immediatamente l'API senza dover scrivere righe di codice.

La procedura seguente funziona sia per la ricerca condivisa che per la ricerca standard. Nella procedura seguente verrà creato un indice, verranno caricati i documenti, si eseguirà una query sull'indice e quindi si eseguirà una query sul sistema per ottenere informazioni sul servizio.

### Creare un indice

1.  Avviare Fiddler Nel menu File disattivare **Capture Traffic** per nascondere attività HTTP estranee non correlate all'attività corrente. Nella scheda Composer formulare una richiesta con l'aspetto simile al seguente:

    ![][11]

2.  Selezionare **PUT**.

3.  Immettere un URL che specifica l'URL del servizio (disponibile nella pagina delle proprietà), gli attributi della richiesta e la versione dell'API. Tenere presente quanto segue:
	-   Usare HTTP come prefisso
	-   L'attributo della richiesta è "/indexes/hotels". In questo modo si comunica al servizio di ricerca di creare un indice denominato 'hotels'.
	-   La versione API è in lettere minuscole, specificata come "?api-version=2014-07-31-preview". Le versioni API sono importanti perché Ricerca di Azure distribuisce aggiornamenti su base regolare. In rare occasioni, un aggiornamento del servizio potrebbe introdurre modifiche significative all'API. Con le versioni API, è possibile continuare a usare la versione esistente ed effettuare l'aggiornamento a una più recente nel momento più opportuno.

    L'URL completo dovrebbe avere un aspetto simile a quello dell'esempio seguente:

         https://my-app.search.windows.net/indexes/hotels?api-version=2014-07-31-Preview

1.  Specificare l'intestazione della richiesta sostituendo i valori di host e chiave api (lettere minuscole) con valori validi per il servizio corrente.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

2.  Nel corpo della richiesta incollare i campi che costituiscono la definizione dell'indice.

         {
        "name": "hotels",  
        "fields": [
          {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
          {"name": "baseRate", "type": "Edm.Double"},
          {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "suggestions": true},
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

3.  Fare clic su **Execute**.

Entro pochi secondi si dovrebbe visualizzare una risposta HTTP 204 nell'elenco della sessione che indica che la creazione dell'indice è stata eseguita correttamente.

Se viene restituita una risposta HTTP 504, verificare se nell'URL è specificato HTTPS. Se viene restituita una risposta HTTP 404, verificare la sintassi. Una risposta HTTP 400 indica che si è verificato un problema con la chiave API (chiave non valida o problema di sintassi nella specifica della chiave API).

### Caricare i documenti

Nella scheda Composer la richiesta di pubblicazione dei documenti avrà un aspetto simile al seguente. Il corpo della richiesta contiene i dati di ricerca per 4 hotel.

![][12]

1.  Selezionare **POST**.

2.  Immettere un URL che inizia con HTTPS, seguito dall'URL del servizio, seguito da "/indexes/\<'nome indice'\>/docs/index?api-version=2014-07-31-preview". L'URL completo dovrebbe avere un aspetto simile a quello dell'esempio seguente:

        https://my-app.search.windows.net/indexes/hotels/docs/index?api-version=2014-07-31-Preview

3.  L'intestazione della richiesta deve essere come quella precedente. Ricordare che i valori di host e chiave api (lettere minuscole) sono stati sostituiti con valori validi per il servizio corrente.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

4.  Il corpo della richiesta contiene quattro documenti da aggiungere all'indice degli hotel.

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

5.  Fare clic su **Execute**.

Entro pochi secondi si dovrebbe visualizzare una risposta HTTP 200 nella sessione. Questo indica che i documenti sono stati creati correttamente. Se viene restituito un codice 207, significa che il caricamento di almeno uno dei documenti non è riuscito. Se viene restituito un codice 404 significa che è presente un errore di sintassi nell'intestazione o nel corpo della richiesta.

### Eseguire una query sull'indice

Ora che l'indice e i documenti sono stati caricati, è possibile eseguire query su di essi. Nella scheda Composer, un comando GET che esegue una query sul servizio avrà un aspetto simile al seguente:

![][13]

1.  Selezionare **GET**.

2.  Immettere un URL che inizia con HTTPS, seguito dall'URL del servizio, seguito da "/indexes/\<'nome indice'\>/docs?", seguito dai parametri di query. A esempio, usare l'URL seguente, sostituendo il nome host di esempio con un nome host valido per il proprio servizio.

        https://my-app.search.windows.net/indexes/hotels/docs?search=motel&facet=category&facet=rating,values:1|2|3|4|5&api-version=2014-07-31-Preview

    Questa query esegue la ricerca del termine "motel" e recupera le categorie facet per le valutazioni.

3.  L'intestazione della richiesta deve essere come quella precedente. Ricordare che i valori di host e chiave api (lettere minuscole) sono stati sostituiti con valori validi per il servizio corrente.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

Il codice della risposta dovrebbe essere 200 e l'output della risposta dovrebbe avere un aspetto simile a quello dell'immagine seguente.

![][14]

La query di esempio seguente è tratta dall'argomento relativo alle [operazioni sull'indice di ricerca (API Ricerca di Azure)][] su MSDN. Molte query di esempio descritte in questo argomento includono gli spazi che non sono consentiti in Fiddler. Sostituire tutti gli spazi con un carattere + prima di incollare la stringa di query ed eseguirla in Fiddler.

**Prima della sostituzione degli spazi:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2014-07-31-Preview

**Dopo la sostituzione degli spazi con il carattere +:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2014-07-31-Preview

### Eseguire query sul sistema

È possibile eseguire query sul sistema per ottenere il numero di documenti e informazioni sull'utilizzo dello spazio di archiviazione. Nella scheda Composer la richiesta avrà un aspetto simile al seguente e la risposta restituirà un conteggio relativo al numero di documenti e allo spazio utilizzato.

![][15]

1.  Selezionare **GET**.

2.  Immettere un URL che include l'URL del servizio, seguito da "/indexes/hotels/stats?api-version=2014-07-31-Preview":

        https://my-app.search.windows.net/indexes/hotels/stats?api-version=2014-07-31-Preview 

3.  Specificare l'intestazione della richiesta sostituendo i valori di host e chiave api con valori validi per il servizio corrente.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

4.  Lasciare il corpo della richiesta vuoto.

5.  Fare clic su **Execute**. Entro pochi secondi si dovrebbe visualizzare un codice di stato HTTP 200 nell'elenco della sessione. Selezionare la voce inserita per il comando.

6.  Fare clic sulla scheda Inspectors | Headers e selezionare il formato JSON. Verrà visualizzato il numero di documenti e la dimensione dello spazio di archiviazione (in KB).

    ![][16]

## Esplorare le pagine di configurazione del servizio di ricerca

Per un ripasso su dove trovare le pagine di configurazione, eseguire la procedura seguente per passare al dashboard del servizio.

1.  Accedere al [portale di anteprima di Azure][] con la sottoscrizione esistente.
2.  Fare clic su **Sfoglia** | **Tutto**.

    ![][17]

3.  Nell'elenco fare clic su **Servizi di ricerca**. Verrà visualizzato un elenco dei servizi di ricerca creati per le proprie sottoscrizioni.

4.  Fare clic su un servizio per aprire il dashboard corrispondente. Si noti che i comandi **Avvia**, **Arresta** ed **Elimina** si trovano nella parte superiore. Nel dashboard del servizio sono presenti i riquadri per la visualizzazione di Proprietà e Chiavi, oltre alla Guida introduttiva contenente i collegamenti a informazioni e istruzioni. Scorrere verso il basso per visualizzare l'utilizzo.

5.  Fare clic su **PROPRIETÀ**. La pagina Proprietà verrà aperta sul lato destro. L'URL del servizio è visualizzato nella parte superiore della pagina. Per ottenere le chiavi API usate per eseguire l'autenticazione al servizio, fare clic su **CHIAVI**.

    ![][8]

<!--Next steps and links -->

## Provare il servizio

Pronti per il passaggio successivo? Fare clic sui collegamenti seguenti per accedere a materiale aggiuntivo in cui viene descritto come creare e gestire applicazioni di ricerca basate su Ricerca di Azure.

[Creare la prima soluzione di ricerca con Ricerca di Azure][]

[Gestire la soluzione di ricerca in Microsoft Azure][]

[Panoramica tecnica relativa a Ricerca di Azure][]

[API REST Ricerca di Azure][]

<!--Anchors--> 
<!--Image references--> 
<!--Link references-->

  [Questo articolo usa il portale di anteprima di Azure]: ../includes/preview-portal-note.md
  [Iniziare con il servizio gratuito]: #sub-1
  [Eseguire l'aggiornamento al servizio di ricerca standard]: #sub-2
  [Testare le operazioni del servizio]: #sub-3
  [Esplorare le pagine di configurazione del servizio di ricerca]: #sub-4
  [Provare il servizio]: #next-steps
  [portale di anteprima di Azure]: https://portal.azure.com
  []: ./media/search-configure/AzureSearch_Configure1_1_New.PNG
  [1]: ./media/search-configure/AzureSearch_Configure1_2_Everything.PNG
  [2]: ./media/search-configure/Azuresearch_Configure1_3_Gallery.PNG
  [3]: ./media/search-configure/AzureSearch_Configure1_4_GallerySeeAll.PNG
  [4]: ./media/search-configure/AzureSearch_Configure1_5_DataServicesList.PNG
  [5]: ./media/search-configure/AzureSearch_Configure1_6_URL.PNG
  [6]: ./media/search-configure/AzureSearch_Configure1_7_Free.PNG
  [7]: ./media/search-configure/AzureSearch_Configure1_8_SearchServiceList.PNG
  [8]: ./media/search-configure/AzureSearch_Configure1_18_Explore.PNG
  [9]: ./media/search-configure/AzureSearch_Configure1_9_Standard.PNG
  [Dettagli prezzi]: http://go.microsoft.com/fwlink/p/?LinkID=509792
  [10]: ./media/search-configure/AzureSearch_Configure1_10_ScaleUp.PNG
  [download gratuito da Telerik]: http://www.telerik.com/fiddler
  [11]: ./media/search-configure/AzureSearch_Configure1_11_PUTIndex.PNG
  [12]: ./media/search-configure/AzureSearch_Configure1_12_POSTDocs.PNG
  [13]: ./media/search-configure/AzureSearch_Configure1_13_GETQuery.PNG
  [14]: ./media/search-configure/AzureSearch_Configure1_14_GETQueryResponse.PNG
  [operazioni sull'indice di ricerca (API Ricerca di Azure)]: http://msdn.microsoft.com/en-us/library/dn798927.aspx
  [15]: ./media/search-configure/AzureSearch_Configure1_15_Stats.PNG
  [16]: ./media/search-configure/AzureSearch_Configure1_16_StatsResponse.PNG
  [17]: ./media/search-configure/AzureSearch_Configure1_17_BrowseEverything.PNG
  [Creare la prima soluzione di ricerca con Ricerca di Azure]: ../search-create-first-solution/
  [Gestire la soluzione di ricerca in Microsoft Azure]: ../search-manage/
  [Panoramica tecnica relativa a Ricerca di Azure]: http://msdn.microsoft.com/en-us/library/dn798933.aspx
  [API REST Ricerca di Azure]: http://msdn.microsoft.com/en-us/library/dn798935.aspx
