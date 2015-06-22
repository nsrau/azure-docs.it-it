<properties 
	pageTitle="Creare un'app di ricerca geospaziale mediante Ricerca di Azure" 
	description="Informazioni su come creare un'app di ricerca geospaziale tramite Bing e Ricerca di Azure." 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="01/16/2015" 
	ms.author="heidist"/>

# Creare un'app di ricerca geospaziale mediante Ricerca di Azure

+ [Prerequisiti](#sub-1)
+ [Bing Mappe](#sub-2)
+ [Geocodifica degli indirizzi in C# usando l'API DataFlow di Bing Mappe](#sub-3)
+ [Aggiunta della mappatura a un'applicazione MVC4 usando Ricerca di Azure e Bing Mappe](#sub-4)
+ [Esplorazione di AdventureWorksWebGeo](#sub-5)
+ [Passaggi successivi](#next-steps)

<h2>Panoramica</h2>

Questa esercitazione illustra come aggiungere la ricerca geospaziale alle applicazioni Web usando Ricerca di Azure e Bing Mappe. La ricerca geografica consente di individuare gli elementi desiderati entro una determinata distanza da un punto (ad esempio entro 5 km dall'ubicazione corrente). La funzionalità geospaziale di Ricerca di Azure supporta le tecniche di mappatura più comuni. Se ad esempio si vogliono usare delle forme poligonali in un'app immobiliare per mostrare le proprietà in vendita all'interno di un poligono, è possibile farlo facilmente usando OData o la sintassi di ricerca semplice.

Per altre informazioni, guardare questo filmato di Channel 9 su [Ricerca di Azure e sui dati geospaziali](http://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data)

![][7]

Per creare l'applicazione verrà usato il servizio di mappatura di Bing per geocodificare gli indirizzi caricati da un file CSV e quindi per archiviare i dati risultanti in un indice di ricerca. 

Questa esercitazione si basa sulla [demo Ricerca di Azure - Adventure Works](http://azuresearchadventureworksdemo.codeplex.com). La demo rappresenta un buon punto di partenza per familiarizzarsi con la creazione di un indice e la chiamata dell'API di Ricerca di Azure da un'app Web.  

<h2 id="sub-1">Prerequisiti</h2>

+	Visual Studio 2012 o versione successiva con ASP.NET MVC 4 e SQL Server installati. Se non si ha già il software installato, è possibile scaricare le versioni Express gratuite: [Visual Studio 2013 Express](http://www.visualstudio.com/it-it/products/visual-studio-express-vs.aspx) e [Microsoft SQL Server 2014 Express](http://msdn.microsoft.com/evalcenter/dn434042.aspx).
+	Un servizio Ricerca di Azure. È necessario disporre del nome del servizio di ricerca e della chiave amministratore. Per informazioni, vedere [Introduzione a Ricerca di Azure](search-get-started.md).
+	Un servizio mappe di Bing e una chiave per accedervi. Le istruzioni sono specificate nella sezione successiva.
+	[Esempio di ricerca geografica in Ricerca di Azure su CodePlex](https://azuresearchgeospatial.codeplex.com/). Nella scheda Source fare clic su **Download** per scaricare un file compresso della soluzione. 

    ![][12]

Questa soluzione contiene due progetti:

+	**StoreIndexer** crea un indice di Ricerca di Azure e carica i dati.
+	**AdventureWorksWebGeo** è un'applicazione basata su MVC4 che interroga l'indice di Ricerca di Azure e mostra i punti vendita su una mappa Bing.

[WACOM.INCLUDE [Per completare l'esercitazione, è necessario un account Azure.](../../includes/free-trial-note.md)]


<h2 id="sub-2">Bing Mappe</h2>

L'API di Bing Mappe viene usata per due motivi.

+ **Geocodifica degli indirizzi:** si dispone degli indirizzi (città, stato, codice postale), ma si desidera disporre anche delle coordinate di longitudine e latitudine per effettuare ricerche geospaziali. Per ottenere le coordinate, viene usata l'API DataFlow di Bing Mappe per inviare un batch di indirizzi per la geocodifica. Con un account di prova di Bing è possibile usare un massimo di 50 indirizzi alla volta, quantità sufficiente ai fini di questa esercitazione.

+ **Bing Mappe:** durante l'esecuzione dell'app, si usa Bing Mappe per visualizzare i punti vendita su una mappa Bing.

<h3>Creare un account per Bing Mappe</h3>

1. Accedere al [portale di Bing Mappe](https://www.bingmapsportal.com/) e creare un nuovo account. Immettere i dati richiesti per creare l'account.

2. Dopo aver creato l'account, selezionare **Crea o visualizza chiavi** e immettere i dati richiesti per creare una chiave.  Ai fini di questa demo è possibile scegliere **Codice di valutazione**.

3. Fare clic su **Invia**. Verranno visualizzati i dati del codice per l'applicazione di Bing Mappe. Prendere nota della chiave, che sarà necessaria in seguito.

<h2 id="sub-3">Geocodifica degli indirizzi in C# usando l'API DataFlow di Bing Mappe</h2>

In questo passaggio viene usata l'API DataFlow di Bing Mappe per la geocodifica di alcuni indirizzi di negozi di biciclette sparsi per il mondo. 

I dati provengono da un file CSV chiamato store_locations.csv ubicato nell'origine scaricata in precedenza. Aprendo il file in un editor di testo o in Excel, vengono visualizzati una colonna ID, il nome e l'indirizzo di ciascun negozio.

Segue una spiegazione dettagliata del codice.

1. Aprire la soluzione AdventureWorksGeo in Visual Studio, espandere il progetto **StoreIndexer** in Esplora soluzioni e aprire Program.cs. Poiché la creazione dell'indice è già stata illustrata nella [demo del servizio Ricerca di Azure - Adventure Works](http://azuresearchadventureworksdemo.codeplex.com/), la spiegazione sul funzionamento in Program.cs viene saltata.

2. Passando alla funzione **Main**, si noti come quest'ultima chiama **ApplyStoreData**.  Passare a questa funzione e consultarne il codice.  

3. **ApplyStoreData** carica i dati da un file CSV chiamato "store_locations.csv" trasferendoli in System.Data.DataTable.  

    Questo file contiene tutti i negozi (con i relativi indirizzi) da caricare in Ricerca di Azure.  Mediante l'iterazione in ciascuna riga di questo file, è possibile creare un insieme di **indexOperations**, da inserire in seguito nell'indice di Ricerca di Azure (precedentemente creato nella funzione **CreateStoresIndex()**).  

    In seguito, osservando l'indice, si può notare che il campo **GeoPt** con la longitudine e la latitudine di ciascun negozio è vuoto. Passiamo ora alla fase successiva della funzione **Main**.

5. Passare alla funzione **ExtractAddressInfoToXML()**. Questa funzione estrae i dati sugli indirizzi dal file store_locations.csv file e li carica su un file XML in un formato compatibile con la funzionalità di geocodifica di Bing Mappe. Una volta creato, il file viene inviato per l'elaborazione a DataFlow di Bing Mappe tramite la chiamata della funzione **GeoCoding.CreateJob**.

6. Poiché il processo di geocodifica può richiedere tempo, è presente un loop che chiama **GeoCoding.CheckStatus** ogni 10 secondi per verificare se l'operazione è completa. Una volta terminata l'operazione, i risultati vengono scaricati in una classe di indirizzi tramite la chiamata di **GeoCoding.DownloadResults**.

7. Il passaggio finale consiste nel prelevare questi indirizzi geocodificati per l'invio a Ricerca di Azure. Per scoprire come viene effettuata tale operazione, aprire la funzione **UpdateStoreData**.

  **UpdateStoreData** usa l'azione **@search.action: merge** per aggiornare il campo dell'ubicazione di tipo Edm.GeographyPoint con le coordinate geocodificate di longitudine e latitudine appena scaricate da Bing Mappe. Durante la procedura viene consultato il valore storeId, che rappresenta la chiave univoca del documento nell'indice dei negozi, e i nuovi dati vengono inseriti nel documento esistente.

8. Prima di avviare l'applicazione, aggiungere i dati di Ricerca di Azure e dell'API di Bing Mappe aprendo App.config e aggiornando i valori di "SearchServiceName", "SearchServiceApiKey" e "BingMapsAPI" in base a quelli del servizio Ricerca di Azure e dell'API di Bing Mappe. Per il nome del servizio di ricerca, se il servizio è "mysearch.search.windows.net", immettere "mysearch".

9. Fare clic con il pulsante destro del mouse sul progetto **StoreIndexer** e scegliere **Debug** | **Avvia nuova istanza** per eseguirlo.


<h2 id="sub-4">Aggiunta della mappatura a un'applicazione MVC4 usando Ricerca di Azure e Bing Mappe</h2>

In questo passaggio verrò compilata ed eseguita l'applicazione di ricerca in un browser Web, al fine di cercare i negozi e tracciarli su una mappa Bing.

1.	In Visual Studio aprire la soluzione demo di Ricerca di Azure chiamata AdventureWorksGeo.sln. 
	
2.	In Esplora soluzioni fare clic con il pulsante destro del mouse su **AdventureWorksWebGeo** e scegliere **Imposta come progetto di avvio**.
	
3.	Aprire Web.config in questo progetto e aggiornare i valori di "SearchServiceName", "SearchServiceApiKey" e "BingMapsAPI" in base a quelli del servizio Ricerca di Azure e dell'API di Bing Mappe. Per il nome del servizio di ricerca, se il servizio è "mysearch.search.windows.net", immettere "mysearch".

4.	Salvare Web.config.
	
5.	Premere **F5** per avviare il progetto. Se si verifica un errore di compilazione, seguire questi consigli sulla [risoluzione dei problemi](#err-mvc).

Si noti che i negozi sono visualizzati come punti sulla mappa. Facendo clic su un negozio, viene visualizzato un messaggio popup con i dettagli del negozio. Tutte queste informazioni provengono dall'indice di Ricerca di Azure chiamato "stores" creato nei passaggi precedenti. 


<h2 id="sub-5">Esplorazione di AdventureWorksWebGeo</h2>

Il progetto **AdventureWorksWebGeo** dimostra come ASP.NET MVC 4 possa essere usato per interagire con Ricerca di Azure al fine di compilare un'applicazione di mappatura sfruttando la funzionalità di ricerca geografica. In questa sezione vengono descritte le diverse sezioni del codice dell'applicazione.

1.	In Esplora soluzioni espandere **AdventureWorksWebGeo** | **Controller** e aprire HomeController.cs. La funzione **Index()** viene chiamata in corrispondenza dell'avvio dell'applicazione e del caricamento della pagina di indice. In questa funzione, l'API di Bing Mappe viene caricata da Web.config e trasferita nella vista dell'indice come ViewBag.BingAPI.

2.	Aprire Index.cshtml da **Visualizzazioni** | **Home**.

3.	Questo file segue la normale procedura di aggiunta di Bing Mappe a un'applicazione Web, con alcuni aspetti importanti da segnalare:

+	ViewBag del controller viene usato per caricare le credenziali per la mappa con: credenziali '@ViewBag.BingAPI' 

+	Una volta caricata la mappa, viene eseguito JQuery $.post per la funzione **Search** di HomeController, facendo riferimento a: /home/search

+	La funzione **Search** recupera le ubicazioni dei punti vendita che, a loro volta, vengono trasferite e aggiunte come PushPins alla mappa Bing. 

4.	Aprire HomeController.cs in **Controllers** e cercare la funzione **Search**. Si noti come esegue una chiamata a _storeSearch.Search(lat, lon, 10000). Ciò provoca l'esecuzione di una query per la ricerca di tutti i negozi entro 10.000 km dalla latitudine (lat) e dalla longitudine (lon) specificate. I risultati della query vengono elaborati e inviati nuovamente alla vista di indice dove, a loro volta, vengono elaborati come PushPins e mostrati sulla mappa Bing.

La demo è terminata. Sono stati descritti i principali aspetti relativi alla compilazione di una mappa basata su un'applicazione ASP.NET MVC4 usando Ricerca di Azure.


<h2 id="err-mvc">Come comportarsi in caso di visualizzazione del messaggio "Could not load file or assembly 'System.Web.Mvc"</h2>

Durante la compilazione di AdventureWorksWeb, se viene visualizzato il messaggio "Could not load file or assembly 'System.Web.Mvc, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' or one of its dependencies", provare a risolvere il problema con la seguente procedura.

1. Aprire la console di gestione dei pacchetti: **Strumenti** | **Gestione pacchetti NuGet** | **Console di Gestione pacchetti**
2. Nel prompt PM> immettere "Update-package -reinstall Microsoft.AspNet.Mvc"
3. Alla richiesta di ricaricare il file, scegliere **Sì a tutti**.
4. Ricompilare la soluzione e premere nuovamente **F5**.


<h2 id="next-steps">Passaggi successivi</h2>

Per approfondire l'argomento, è possibile considerare l'aggiunta di ulteriori capacità all'applicazione di mappatura. Ad esempio, potrebbero essere aggiunti elementi analoghi ai seguenti:

+ Casella di ricerca per l'esecuzione di ricerche di negozi specifici.

+ Facet per filtrare per paese o provincia.  

+ Funzionalità di selezione definite dall'utente per specificare le regioni in cui eseguire le ricerche selezionando un'area sulla mappa. In seguito, l'area viene filtrata dalla funzione di Ricerca di Azure usando l'API di geo-convergenza e tracciata sulla mappa.


<!--Anchors-->
[Prerequisiti]: #sub-1
[Bing Mappe]: #sub-2
[Geocodifica degli indirizzi in C# usando l'API DataFlow di Bing Mappe]: #sub-3
[Aggiunta della mappatura a un'applicazione MVC4 usando Ricerca di Azure e Bing Mappe]: #sub-4
[Esplorazione di AdventureWorksWebGeo]: #sub-5
[Passaggi successivi]: #next-steps


<!--Image references-->
[7]: ./media/search-create-geospatial/AzureSearch-geo1-App.PNG
[12]: ./media/search-create-geospatial/AzureSearch_Create2_CodeplexDownload.PNG

<!--HONumber=46--> 
 