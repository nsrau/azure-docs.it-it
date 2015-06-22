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
	ms.date="01/16/2015" 
	ms.author="heidist"/>

# Creare la prima soluzione di ricerca con Ricerca di Azure

+ [Prerequisiti](#sub-1)
+ [Creare un indice di Ricerca di Azure](#sub-2)
+ [Esplorare CatalogIndexer](#sub-3)
+ [Compilare un'applicazione MVC4 usando Ricerca di Azure](#sub-4)
+ [Esplorare AdventureWorksWeb](#sub-5)
+ [Passaggi successivi](#next-steps)

<h2>Panoramica</h2>

<p>In questo esempio viene illustrata un'applicazione di ricerca per la società produttrice di biciclette Adventure Works. Al completamento di questa esercitazione, si otterrà un catalogo prodotti online con un'esperienza di ricerca avanzata, che include l'esplorazione in base a facet, più opzioni di ordinamento dei risultati della ricerca e suggerimenti di completamento automatico delle query.

   ![][7]

La demo introduce le funzionalità di Ricerca di Azure con alcune esercitazioni guidate:

+	Creare un indice di Ricerca di Azure.
+	Caricare i documenti (dati) nell'indice di Ricerca di Azure da un database di SQL Server.
+	Compilare un'applicazione MVC4 di ASP.NET che usa Ricerca di Azure per:
	+	Cercare e visualizzare i risultati di un indice di Ricerca di Azure
	+	Mostrare suggerimenti di completamento automatico nella casella di ricerca durante l'immissione del termine di ricerca
	+	Filtrare i risultati della ricerca usando l'esplorazione in base a facet


<h2 id="sub-1">Prerequisiti</h2>

+	Visual Studio 2012 o versione successiva con ASP.NET MVC 4 e SQL Server installati. Se non si ha già il software installato, è possibile scaricare le versioni Express gratuite: [Visual Studio 2013 Express](http://www.visualstudio.com/it-it/products/visual-studio-express-vs.aspx) e [Microsoft SQL Server 2014 Express](http://msdn.microsoft.com/evalcenter/dn434042.aspx).
+	Un servizio di Ricerca di Azure. È necessario disporre del nome del servizio di ricerca e della chiave amministratore. Vedere [Introduzione a Ricerca di Azure](search-get-started.md) per informazioni dettagliate.
+	[Progetto demo di Ricerca di Azure in Adventure Works su CodePlex](http://go.microsoft.com/fwlink/p/?LinkID=510972). Nella scheda Source fare clic su **Download** per scaricare un file compresso della soluzione. 

    ![][12]


Questa soluzione contiene due progetti:

+	**CatalogIndex** crea un indice di Ricerca di Azure e carica i dati da un database di SQL Server incluso nel progetto.
+	**AdventureWorksWeb** è un'applicazione basata su MVC4 che interroga l'indice di Ricerca di Azure. In questa esercitazione si presuppone che l'utente abbia una conoscenza pratica di ASP.NET MVC.

[WACOM.INCLUDE [You need an Azure account to complete this tutorial:](../../includes/free-trial-note.md)]


<h2 id="sub-2">Creare un indice di Ricerca di Azure</h2>

In questa procedura verrà usato il progetto **CatalogIndex** per creare un nuovo indice di Ricerca di Azure denominato "catalog", basato sui dati contenuti in un database AdventureWorks di SQL Server.

1.	In Visual Studio aprire la soluzione demo di Ricerca di Azure chiamata **AdventureWorksCatalog.sln**.  
2.	Fare clic con il pulsante destro del mouse su **CatalogIndexer** in Esplora soluzioni e scegliere **Imposta come progetto di avvio**, in modo che venga eseguita questa applicazione invece del progetto **AdventureWorksWeb** quando si preme **F5**.
3.	Aprire **App.config** in questo progetto e aggiornare i valori di "SearchServiceName" e "SearchServiceApiKey" in base a quelli del servizio di Ricerca di Azure. Per il nome del servizio di ricerca, se il servizio è "mysearch.search.windows.net", immettere "mysearch".
4.	Facoltativamente, App.config include una voce per "SourceSqlConnectionString" che presuppone SQL Server 2014 Express LocalDB (Server=(LocalDB)\v11.0). Se si usa un'edizione differente di SQL Server, aggiornare il nome del server di conseguenza. Ad esempio, se si ha un'istanza predefinita locale, è possibile usare (local) oppure localhost.
5.	Salvare **App.config**.
6.	Premere **F5** per avviare il progetto.

Si aprirà un prompt dei comandi con il testo seguente: "Creating index..."

Dopo pochi secondi, verrà completato con il testo: "Complete.  Press <enter> to continue:"

   ![][8]

Premere **INVIO** per chiudere l'applicazione. A questo punto, è stato creato correttamente l'indice di Ricerca di Azure. 

> [WACOM.NOTE] Se si verificano errori come "Valore non valido per la chiave 'attachdbfilename'" o un altro errore di allegato al database, potrebbero verificarsi conflitti di Controllo dell'account utente. Ai fini di questa dimostrazione, risolvere questi errori effettuando le operazioni seguenti: 
> Copiare la soluzione in una cartella nuova o esistente (ad esempio Temp) che fornisce l'accesso agli utenti autenticati. 
> Usare **Esegui come amministratore** per avviare Visual Studio. 
> Aprire la soluzione, compilarla e premere **F5** per creare l'indice.

Per verificare la creazione dell'indice e il caricamento dei documenti, passare al dashboard del servizio di ricerca nel [portale di anteprima di Azure](https://portal.azure.com). In Utilizzo, il numero dell'indice dovrebbe essere aumentato di uno e dovrebbero essere disponibili 294 documenti, uno per ogni prodotto presente nel database.

Fare clic sul riquadro **Indici** per visualizzare l'elenco degli indici, che verrà espanso in modo da visualizzare il nuovo numero di indici e documenti.

   ![][9]


<h2 id="sub-3">Esplorare CatalogIndexer</h2>

Di seguito viene descritto in dettaglio il progetto **CatalogIndexer** per illustrarne il funzionamento.

1.	Aprire **Program.cs** da Esplora soluzioni e passare alla funzione `Main(string[] args)`.

    Si noti che questa funzione crea un URI chiamato `_serviceURI` basato su uno specifico servizio di Ricerca di Azure e quindi crea un HttpClient chiamato `_httpClient` che usa la chiave API per eseguire l'autenticazione su questo servizio di ricerca.

2.	`ChangeEnumeratorSql` e `ChangeSet` vengono usati per enumerare i dati dalla tabella Products nel database AdventureWorks di SQL Server. 

3.	In base ai dati raccolti da questa tabella, viene quindi chiamato `ApplyChanges` per applicare questi dati all'indice di Ricerca di Azure.

4.	Passare ad `ApplyChanges` nello stesso file. Notare che questa funzione elimina l'indice, se già esistente (`DeleteCatalogIndex`), creandone uno nuovo denominato "catalog" (`CreateCatalogIndex`).  

5.	Passare alla funzione `CreateCatalogIndex` e notare che l'indice viene creato con uno schema corrispondente alle colonne presenti nella tabella Products in SQL Server. Ogni campo ha un Tipo (cioè `Edm.String` o `Edm.Double`) oltre agli attributi che ne definiscono lo scopo. Per informazioni più dettagliate su questi attributi, consultare la [documentazione relativa alle API REST di Ricerca di Azure](http://msdn.microsoft.com/library/azure/dn798935.aspx).

6.	Tornare alla funzione `ApplyChanges`. Notare che questa funzione esegue il ciclo di tutti i dati nel `ChangeSet` delle modifiche enumerate. Invece di applicare le modifiche una alla volta, vengono raggruppate in batch di 1000 e quindi applicate al servizio di ricerca. Si tratta di una procedura molto più efficiente rispetto all'applicazione dei singoli documenti.

Dopo aver visto come creare e popolare un indice usando i dati relazionali di SQL Server, verrà ora illustrato come compilare un catalogo prodotti che usi i dati della ricerca.


<h2 id="sub-4">Compilare un'applicazione MVC4 usando Ricerca di Azure</h2>

In questo passaggio verrà compilata ed eseguita l'applicazione di ricerca in un browser Web.

1.	In Visual Studio aprire la soluzione demo di Ricerca di Azure chiamata **AdventureWorksCatalog.sln**.  

2.	In Esplora soluzioni fare clic con il pulsante destro del mouse su **AdventureWorksWeb** e scegliere **Imposta come progetto di avvio**.

3.	Aprire **Web.config** in questo progetto e aggiornare i valori di "SearchServiceName" e "SearchServiceApiKey" in base a quelli del servizio di Ricerca di Azure. Per il nome del servizio di ricerca, se il servizio è "mysearch.search.windows.net", immettere "mysearch".

4.	Salvare **Web.config**.

5.	Premere **F5** per avviare il progetto. Eseguire questa procedura per la [risoluzione dei problemi](#err-mvc) se si verifica un errore di compilazione. 

    ![][10]

6.	Lasciare vuota la casella di ricerca, quindi fare clic su **Cerca** per restituire tutti i 294 prodotti.

7.	Notare l'elenco di facet lungo il lato sinistro. Provare a fare clic su uno dei facet. La ricerca viene rieseguita, ma stavolta aggiungendo il facet scelto per filtrare i risultati. Può essere opportuno aggiungere un punto di interruzione alla prima riga nella funzione `Search` di **HomeController.cs** per analizzare ogni riga (F11).

7.	Immettere un termine di ricerca, ad esempio "mountain bike". Durante la digitazione, notare l'elenco a discesa di query suggerite.

    ![][11]

Di seguito è riportata nuovamente la schermata illustrata all'inizio di questa demo, come promemoria di ciò che è stato trattato finora. Nelle sezioni precedenti si è discusso di esplorazione in base a facet (a sinistra), numero di documenti nella parte superiore della pagina, suggerimenti e opzioni di ordinamento in base a pertinenza, elenco o prezzo.

   ![][7]


<h2 id="sub-5">Esplorare AdventureWorksWeb</h2>

Il progetto AdventureWorksWeb dimostra come sia possibile usare ASP.NET MVC 4 per interagire con Ricerca di Azure da un'applicazione Web. In questa sezione vengono descritte le diverse sezioni del codice dell'applicazione.

1.	In Esplora soluzioni espandere **AdventureWorksWeb** | **Controller** e aprire **HomeController.cs**.

    Si tratta del controller MVC che gestisce l'interazione dalla visualizzazione Index.  Nella parte superiore del controller si noterà un riferimento a `CatalogSearch _catalogSearch`, che crea un oggetto connessione HttpClient a Ricerca di Azure. Il codice relativo a `CatalogSearch` si trova nel file **CatalogSeach.cs**

2. All'interno di **HomeController.cs** sono presenti due funzioni principali:

	**Search**: quando l'utente fa clic sul pulsante Cerca oppure sceglie un facet, viene chiamata questa funzione per recuperare i risultati e inviarli nuovamente alla visualizzazione Index.

	**Suggest**: quando l'utente digita dei caratteri nella casella di ricerca, viene chiamata questa funzione per restituire un elenco di suggerimenti basati sul contenuto dell'indice di Ricerca di Azure.

Verranno ora esaminate queste due funzioni più dettagliatamente.  

3.	Nella funzione `Search` di **HomeController.cs** si noterà una chiamata a `_catalogSearch.Search`, che include l'oggetto connessione a Ricerca di Azure. Quando si chiama la funzione Search presente in **CatalogSearch.cs**, si noterà che usa questi parametri per creare una query di Ricerca di Azure. I risultati della query vengono archiviati in un oggetto JSON chiamato `result` e quindi passati nuovamente alla visualizzazione `Index`, dove i risultati vengono analizzati e visualizzati nella pagina Web.

4.	Aprire **Index.cshtml** in Visualizzazioni | Home. Si noterà il codice usato per analizzare questi risultati.

5.	Arrestare l'applicazione se è ancora in esecuzione e aprire il file **Index.cshtml** in Visualizzazioni | Home.  Al termine di questo file è presente una funzione JavaScript che usa `JQuery $(function ())`. Tale funzione viene chiamata al caricamento di una pagina. Viene usata la funzione di completamento automatico JQuery e collegata la funzione come callback dalla casella di testo di ricerca, identificata come "q". Ogni volta che si digitano dei caratteri nella casella di testo, viene chiamata la funzione di suggerimento automatico che a sua volta chiama /home/suggest con il contenuto digitato.  `/home/suggest` è un riferimento alla funzione in **HomeController.cs** chiamata `Suggest`.

6.	Aprire **HomeController.cs** e passare alla funzione Suggest. Questo codice è molto simile alla funzione Search che usa l'oggetto `_catalogSearch` per chiamare una funzione in **CatalogSearch.cs** chiamata `Suggest`. Invece di eseguire una query di ricerca, la funzione `Suggest` esegue una chiamata all'[API Suggestions](http://msdn.microsoft.com/library/azure/dn798936.aspx). che usa i termini immessi nella casella di testo e crea un elenco di potenziali suggerimenti. I valori vengono restituiti al file **Index.cshtml** e vengono automaticamente elencati nella casella di ricerca come opzioni di completamento automatico.

A questo punto ci si potrebbe chiedere in che modo Ricerca di Azure individua i campi in base ai quali creare i suggerimenti: la risposta è nella fase di creazione dell'indice. Nella funzione `CreateCatalogIndex` all'interno del file Program.cs del progetto **CatalogIndexer** è presente un attributo chiamato `Suggestions`.  Ogni volta che questo attributo viene impostato su `True`, Ricerca di Azure può usarlo come campo per recuperare suggerimenti.

Di seguito verrà illustrato come procedere.  

7.	Compilare di nuovo l'applicazione e quindi premere **F5** per eseguirla.

8.	Nella casella di ricerca digitare la parola "Road".  Dopo un secondo verrà visualizzato un elenco di elementi sotto la casella di testo con i suggerimenti che un utente potrebbe scegliere potenzialmente.  

Può essere opportuno aggiungere un punto di interruzione alla funzione `Suggest` nel file **HomeController.cs** ed esaminare (F11) ogni chiamata eseguita per creare l'elenco dei suggerimenti.

La demo è terminata. Sono stati descritti i principali aspetti relativi alla compilazione di un'applicazione ASP.NET MVC4 usando Ricerca di Azure.


<h2 id="err-mvc">Come comportarsi in caso di visualizzazione del messaggio "Could not load file or assembly 'System.Web.Mvc"</h2>

Durante la compilazione di AdventureWorksWeb, se viene visualizzato il messaggio "Could not load file or assembly 'System.Web.Mvc, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' or one of its dependencies", provare a risolvere il problema con la seguente procedura.

1. Aprire la console di gestione dei pacchetti: **Strumenti** | **Gestione pacchetti NuGet** | **Console di Gestione pacchetti**
2. Nel prompt PM>, immettere "Update-package -reinstall Microsoft.AspNet.Mvc"
3. Alla richiesta di ricaricare il file, scegliere **Sì per tutti**.
4. Ricompilare la soluzione e premere nuovamente **F5**.


<h2 id="next-steps">Passaggi successivi</h2>

Per approfondire l'argomento, è possibile considerare l'aggiunta di una pagina di informazioni dettagliate che venga visualizzata quando un utente fa clic su uno dei risultati della ricerca. Per prepararsi, eseguire le operazioni seguenti:

+	Documentarsi sull'[API di ricerca](http://msdn.microsoft.com/library/azure/dn798929.aspx) che consente di eseguire una query di Ricerca di Azure per recuperare un documento specifico, ad esempio passando l'ID del prodotto.
+	Provare ad aggiungere una nuova funzione nel file **HomeController.cs** chiamato Details. Aggiungere una visualizzazione **Details.cshtml** corrispondente che riceve i risultati di questa ricerca visualizzandoli.
+	Consultare questo ulteriore esempio di codice e un video sulla ricerca geospaziale: [Channel 9 - Ricerca di Azure e dati geospaziali](http://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data) and [CodePlex: Esempio di ricerca geografica in Ricerca di Azure](http://azuresearchgeospatial.codeplex.com)

È anche possibile rivedere l'[API REST di Ricerca di Azure](http://msdn.microsoft.com/library/azure/dn798935.aspx) su MSDN.


<!--Anchors-->
[Prerequisiti]: #sub-1
[Creare un indice di Ricerca di Azure]: #sub-2
[Esplorare CatalogIndexer]: #sub-3
[Compilare un'applicazione MVC4 usando Ricerca di Azure]: #sub-4
[Esplorare AdventureWorksWeb]: #sub-5
[Passaggi successivi]: #next-steps


<!--Image references-->
[7]: ./media/search-create-first-solution/AzureSearch_Create1_WebApp.PNG
[8]: ./media/search-create-first-solution/AzureSearch_Create1_ConsoleMsg.PNG
[9]: ./media/search-create-first-solution/AzureSearch_Create1_DashboardIndexes.PNG
[10]: ./media/search-create-first-solution/AzureSearch_Create1_WebAppEmpty.PNG
[11]: ./media/search-create-first-solution/AzureSearch_Create1_Suggestions.PNG
[12]: ./media/search-create-first-solution/AzureSearch_Create1_CodeplexDownload.PNG

<!--HONumber=35.2-->

<!--HONumber=46--> 
 