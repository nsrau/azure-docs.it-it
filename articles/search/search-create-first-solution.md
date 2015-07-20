<properties 
	pageTitle="Creare la prima soluzione di ricerca con Ricerca di Azure" 
	description="Creare la prima soluzione di ricerca con Ricerca di Azure" 
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
	ms.date="07/08/2015" 
	ms.author="heidist"/>

# Creare la prima soluzione di ricerca con Ricerca di Azure

In questo esempio viene illustrata un'applicazione di ricerca per la società produttrice di biciclette Adventure Works. Al completamento di questa esercitazione, si otterrà un catalogo prodotti online con un'esperienza di ricerca avanzata, che include l'esplorazione in base a facet, più opzioni di ordinamento dei risultati della ricerca e suggerimenti di completamento automatico delle query.

   ![][7]

La demo introduce le funzionalità di Ricerca di Azure con alcune esercitazioni guidate:

+	Creare un indice di Ricerca di Azure.
+	Caricare i documenti (dati) nell'indice di Ricerca di Azure da un database di SQL Server.
+	Compilare un'applicazione MVC4 di ASP.NET che usa Ricerca di Azure per:
	+	Cercare e visualizzare i risultati di un indice di Ricerca di Azure
	+	Mostrare suggerimenti di completamento automatico nella casella di ricerca durante l'immissione del termine di ricerca
	+	Filtrare i risultati della ricerca usando l'esplorazione in base a facet


<a id="sub-1"></a>
## Prerequisiti

+	Un [sottoscrizione Azure](../includes/free-trial-note.md). Se non si è pronti per iscriversi a una sottoscrizione di valutazione, è possibile ignorare questa esercitazione e scegliere invece [provare Azure App Service](https://tryappservice.azure.com/). Questa opzione alternativa non consente di Ricerca di Azure con un'app Web ASP.NET gratuitamente - un'ora per ogni sessione - è necessaria la sottoscrizione.
+	Visual Studio 2012 o versione successiva con ASP.NET MVC 4 e SQL Server installati. Se non è già installato il software, è possibile scaricare le versioni Express gratuite: [Visual Studio 2013 Express](http://www.visualstudio.com/products/visual-studio-express-vs.aspx) e [Microsoft SQL Server 2014 Express](http://msdn.microsoft.com/evalcenter/dn434042.aspx).
+	Un servizio di Ricerca di Azure. È necessario disporre del nome del servizio di ricerca e della chiave amministratore. Vedere [creare un servizio di Ricerca di Azure nel portale di](search-create-service-portal.md) per informazioni dettagliate.
+	[Progetto demo di Ricerca di Azure in Adventure Works su CodePlex](http://go.microsoft.com/fwlink/p/?LinkID=510972). Nella scheda Source fare clic su **Download** per scaricare un file compresso della soluzione. 

    ![][12]


Questa soluzione contiene due progetti:

+	**CatalogIndex** crea un indice di Ricerca di Azure e carica i dati da un database di SQL Server incluso nel progetto.
+	**AdventureWorksWeb** è un'applicazione basata su MVC4 che interroga l'indice di Ricerca di Azure. In questa esercitazione si presuppone che l'utente abbia una conoscenza pratica di ASP.NET MVC.

<a id="sub-2"></a>
## Creare un indice di Ricerca di Azure

In questa procedura verrà usato il progetto **CatalogIndex** per creare un nuovo indice di Ricerca di Azure denominato "catalog", basato sui dati contenuti nel database di SQL Server AdventureWorks.

1.	In Visual Studio, aprire la soluzione demo sulla Ricerca di Azure denominata **AdventureWorksCatalog.sln**.  
2.	Fare clic con il pulsante destro del mouse su **CatalogIndexer** in Esplora soluzioni e selezionare **Imposta come progetto di avvio**, in modo che venga eseguita questa applicazione invece del progetto **AdventureWorksWeb** quando si preme **F5**.
3.	Aprire **App.config** in questo progetto e aggiornare i valori di "SearchServiceName" e "SearchServiceApiKey" in base a quelli del servizio di Ricerca di Azure. Per il nome del servizio di ricerca, se il servizio è "mysearch.search.windows.net", immettere "mysearch".
4.	Facoltativamente, App.config include una voce per "SourceSqlConnectionString" che presuppone SQL Server 2014 Express LocalDB (Server=(LocalDB)\\v11.0). Se si usa un'edizione differente di SQL Server, aggiornare il nome del server di conseguenza. Ad esempio, se si ha un'istanza predefinita locale, è possibile usare (local) oppure localhost.
5.	Salvare **App.config**.
6.	Premere **F5** per avviare il progetto.

Aprire un prompt dei comandi con il testo seguente: "Creazione index..."

Dopo qualche istante, l'operazione dovrebbe completarsi con il testo: "Complete. Press <enter> to continue:"

   ![][8]

Premere **INVIO** per chiudere l'applicazione. A questo punto, è stato creato correttamente l'indice di Ricerca di Azure.

> [AZURE.NOTE]Se si verificano errori come "Valore non valido per la chiave 'attachdbfilename'" o un altro errore di allegato al database, potrebbero verificarsi conflitti di Controllo dell'account utente. Ai fini di questa dimostrazione, risolvere questi errori effettuando le seguenti operazioni: copiare la soluzione in una cartella nuova o esistente (ad esempio Temp) che fornisce l'accesso agli utenti autenticati. Utilizzare **Esegui come amministratore** per avviare Visual Studio. Aprire la soluzione, compilarlo e premere **F5** per creare l'indice.

Per verificare la creazione dell'indice e il caricamento del documento, passare al dashboard del servizio di ricerca nel [portale di gestione di Azure](https://portal.azure.com). In Utilizzo, il numero dell'indice dovrebbe essere aumentato di uno e dovrebbero essere disponibili 294 documenti, uno per ogni prodotto presente nel database.

Fare clic sul riquadro **Indici** per visualizzare l'elenco degli indici, che verrà espanso in modo da visualizzare il nuovo numero di indici e documenti. Si noti che è possibile avere fino a tre indici a livello di prezzo Free. Se si dispone già di tre indici, è necessario eliminare uno per liberare spazio per quelli nuovi.

   ![][9]


<a id="sub-3"></a>
## Esplorare CatalogIndexer

Di seguito viene illustrato nel dettaglio il progetto **CatalogIndexer** per comprenderne il funzionamento.

1.	Aprire **Program.cs** da Esplora soluzioni e visitare il `Main(string[] args)` (funzione).

    Si noti come questa funzione crea un Uri chiamato `_serviceURI` basato su servizio di Ricerca di Azure specifico e quindi crea un HttpClient chiamato `_httpClient` che utilizza la chiave dell'API per l'autenticazione per questo servizio di ricerca.

2.	`ChangeEnumeratorSql` e `ChangeSet` vengono utilizzati per enumerare i dati dalla tabella Products del database AdventureWorks di SQL Server.

3.	In base ai dati raccolti da questa tabella `ApplyChanges` viene quindi chiamato per applicare tali dati per l'indice di Ricerca di Azure.

4.	Passare a `ApplyChanges` nello stesso file. Si noti come questa funzione Elimina l'indice se esiste già (`DeleteCatalogIndex`) e quindi crea un nuovo indice denominato "catalogo" (`CreateCatalogIndex`).

5.	Spostare il `CreateCatalogIndex` funzione e notare come l'indice viene creato con uno schema corrispondente alle colonne della tabella Products in SQL Server. Ogni campo presenta un tipo (ad esempio, `Edm.String` o `Edm.Double`) e gli attributi che definiscono quali questi campi vengono utilizzati per. Per informazioni più dettagliate su questi attributi, consultare la [documentazione relativa alle API REST di Ricerca di Azure](http://msdn.microsoft.com/library/azure/dn798935.aspx).

6.	Tornare al `ApplyChanges` (funzione). Si noti come questa funzione scorre tutti i dati in modifiche enumerate `ChangeSet`. Invece di applicare le modifiche una alla volta, vengono raggruppate in batch di 1000 e quindi applicate al servizio di ricerca. Si tratta di una procedura molto più efficiente rispetto all'applicazione dei singoli documenti.

Dopo aver visto come creare e popolare un indice usando i dati relazionali di SQL Server, verrà ora illustrato come compilare un catalogo prodotti che usi i dati della ricerca.

<a id="sub-4"></a>
## Compilare un'applicazione MVC4 usando Ricerca di Azure

In questo passaggio verrà compilata ed eseguita l'applicazione di ricerca in un browser Web.

1.	In Visual Studio, aprire la soluzione demo di Ricerca di Azure chiamata **AdventureWorksCatalog.sln**.  

2.	In Esplora soluzioni fare clic con il pulsante destro del mouse su **AdventureWorksWeb** e selezionare **Imposta come progetto di avvio**.

3.	Aprire **Web.config** in questo progetto e aggiornare i valori di "SearchServiceName" e "SearchServiceApiKey" in base a quelli del servizio di Ricerca di Azure. Per il nome del servizio di ricerca, se il servizio è "mysearch.search.windows.net", immettere "mysearch".

4.	Salvare **Web.config**.

5.	Premere **F5** per avviare il progetto. Se si verifica un errore di compilazione, seguire questi consigli sulla [risoluzione dei problemi](#err-mvc).

    ![][10]

6.	Lasciare vuota la casella di ricerca, quindi fare clic su **Cerca** per restituire tutti e 294 prodotti.

7.	Notare l'elenco di facet lungo il lato sinistro. Provare a fare clic su uno dei facet. La ricerca viene rieseguita, ma stavolta aggiungendo il facet scelto per filtrare i risultati. È possibile aggiungere un punto di interruzione sulla prima riga nel **HomeController.cs** `Search` funzione per eseguire le istruzioni (F11) ogni riga.

7.	Immettere un termine di ricerca, ad esempio "mountain bike". Durante la digitazione, notare l'elenco a discesa di query suggerite.

    ![][11]

Di seguito è riportata nuovamente la schermata illustrata all'inizio di questa demo, come promemoria di ciò che è stato trattato finora. Nelle sezioni precedenti si è discusso di esplorazione in base a facet (a sinistra), numero di documenti nella parte superiore della pagina, suggerimenti e opzioni di ordinamento in base a pertinenza, elenco o prezzo.

   ![][7]


<a id="sub-5"></a>
## Esplorare AdventureWorksWeb

Il progetto AdventureWorksWeb dimostra come sia possibile usare ASP.NET MVC 4 per interagire con Ricerca di Azure da un'applicazione Web. In questa sezione vengono descritte le diverse sezioni del codice dell'applicazione.

1.	In Esplora soluzioni, espandere **AdventureWorksWeb** | **Controller** e aprire **HomeController.cs**

    Si tratta del controller MVC che gestisce l'interazione dalla visualizzazione Index. Nella parte superiore del controller, si noterà un riferimento a `CatalogSearch _catalogSearch` che consente di creare un oggetto di connessione HttpClient al servizio di Ricerca di Azure. Il codice per questo `CatalogSearch` si trova in **CatalogSeach.cs**.

2. All'interno del file **HomeController.cs** esistono due funzioni principali:

	**Ricerca**: quando l'utente fa clic sul pulsante di ricerca o si sceglie un facet, questa funzione viene chiamata per recuperare i risultati e inviarle tornare alla visualizzazione di indice da visualizzare.

	**Suggerisci**: come l'utente digita nella casella di ricerca, questa funzione viene chiamata per restituire un elenco di suggerimenti in base al contenuto nell'indice di Ricerca di Azure.

Verranno ora esaminate queste due funzioni più dettagliatamente.

3.	In **HomeController.cs** `Search` funzione, si noterà una chiamata a `_catalogSearch.Search`, che include l'oggetto di connessione al servizio di Ricerca di Azure. Quando si chiama la funzione Cerca presente in **CatalogSearch.cs**, sarà possibile notare che usa questi parametri per creare una query di Ricerca di Azure. I risultati della query vengono archiviati in un oggetto JSON denominato `result` e quindi passato nuovamente al `Index` visualizzazione in cui i risultati vengono analizzati e visualizzati nella pagina web.

4.	Aprire **Index.cshtml** sotto Visualizzazioni | Home. Si noterà il codice usato per analizzare questi risultati.

5.	Arrestare l'applicazione se è ancora in esecuzione e aprire il file **Index.cshtml** sotto Visualizzazioni | Home. Al termine di questo file, si vedrà una funzione JavaScript che utilizza `JQuery $(function ())`. Tale funzione viene chiamata al caricamento di una pagina. Viene usata la funzione di completamento automatico JQuery e collegata la funzione come callback dalla casella di testo di ricerca, identificata come "q". Ogni volta che un utente digita nella casella di testo, viene chiamata la funzione suggerimenti automatici che a sua volta chiama /home/suggest con quella immessa. `/home/suggest` è un riferimento alla funzione **HomeController.cs** chiamato `Suggest`.

6.	Aprire **HomeController.cs** e passare alla funzione Suggest. Questo codice è molto simile alla funzione di ricerca che utilizza il `_catalogSearch` oggetto per chiamare una funzione **CatalogSearch.cs** chiamato `Suggest`. Invece di effettuare una query di ricerca, la `Suggest` funzione effettua una chiamata al [API suggerimenti](http://msdn.microsoft.com/library/azure/dn798936.aspx). che usa i termini immessi nella casella di testo e crea un elenco di potenziali suggerimenti. I valori vengono restituiti al file **Index.cshtml** che vengono automaticamente elencati nella casella di ricerca come opzioni di completamento automatico.

A questo punto ci si potrebbe chiedere in che modo Ricerca di Azure individua i campi in base ai quali creare i suggerimenti: la risposta è nella fase di creazione dell'indice. Nella `CreateCatalogIndex` funzione all'interno del file Program.cs del **CatalogIndexer** del progetto, è presente un attributo denominato `Suggestions`. Ogni volta che questo attributo è impostato su `True`, significa che Ricerca di Azure possibile utilizzarlo come un campo per il recupero di suggerimenti

Di seguito verrà illustrato come procedere.

7.	Compilare di nuovo l'applicazione e quindi premere **F5** per eseguirla.

8.	Nella casella di ricerca digitare la parola "Road". Dopo un secondo verrà visualizzato un elenco di elementi sotto la casella di testo con i suggerimenti che un utente potrebbe scegliere potenzialmente.

È possibile aggiungere un punto di interruzione il `Suggest` nell'ambito di **HomeController.cs** e scorrere (F11) di ogni chiamata effettuata per compilare l'elenco di suggerimenti.

La demo è terminata. Sono stati descritti i principali aspetti relativi alla compilazione di un'applicazione ASP.NET MVC4 usando Ricerca di Azure.


<a id="err-mvc"></a>
## Come comportarsi in caso di visualizzazione del messaggio "Could not load file or assembly 'System.Web.Mvc"

Durante la compilazione di AdventureWorksWeb, se viene visualizzato il messaggio "Could not load file or assembly 'System.Web.Mvc, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' or one of its dependencies", provare a risolvere il problema con la seguente procedura.

1. Aprire la Console di gestione pacchetti: **strumenti** | **Gestione pacchetti NuGet** | **Console di gestione pacchetti**
2. Nel prompt PM>, immettere "Update-package -reinstall Microsoft.AspNet.Mvc"
3. Alla richiesta di ricaricare il file, scegliere **Sì a tutti**.
4. Ricompilare la soluzione e premere nuovamente **F5**.


<a id="next-steps"></a>
## Passaggi successivi

Per approfondire l'argomento, è possibile considerare l'aggiunta di una pagina di informazioni dettagliate che venga visualizzata quando un utente fa clic su uno dei risultati della ricerca. Per prepararsi, eseguire le operazioni seguenti:

+	Documentarsi sull'[API di ricerca](http://msdn.microsoft.com/library/azure/dn798929.aspx)che consente di eseguire una query di Ricerca di Azure per recuperare un documento specifico (ad esempio è possibile passare l'ID del prodotto).
+	Provare ad aggiungere una nuova funzione nel file **HomeController.cs** chiamato Details. Aggiungere una corrispondente visualizzazione **Details.cshtml** che riceve i risultati di questa ricerca visualizzandoli.
+	Consulta questo ulteriore esempio di codice e i video sulla ricerca geospaziali: [Channel 9: Ricerca di Azure e i dati geospaziali](http://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data) e [CodePlex: esempio di Ricerca di Azure GeoSearch](http://azuresearchgeospatial.codeplex.com)

È anche possibile rivedere l'[API REST Ricerca di Azure](http://msdn.microsoft.com/library/azure/dn798935.aspx) su MSDN.


<!--Anchors-->
[Prerequisites]: #sub-1
[Create an Azure Search index]: #sub-2
[Explore CatalogIndexer]: #sub-3
[Build an MVC4 Application using Azure Search]: #sub-4
[Explore AdventureWorksWeb]: #sub-5
[Next steps]: #next-steps


<!--Image references-->
[7]: ./media/search-create-first-solution/AzureSearch_Create1_WebApp.PNG
[8]: ./media/search-create-first-solution/AzureSearch_Create1_ConsoleMsg.PNG
[9]: ./media/search-create-first-solution/AzureSearch_Create1_DashboardIndexes.PNG
[10]: ./media/search-create-first-solution/AzureSearch_Create1_WebAppEmpty.PNG
[11]: ./media/search-create-first-solution/AzureSearch_Create1_Suggestions.PNG
[12]: ./media/search-create-first-solution/AzureSearch_Create1_CodeplexDownload.PNG

<!---HONumber=July15_HO2-->