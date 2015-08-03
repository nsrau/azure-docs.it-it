<properties 
	pageTitle="Compilare un servizio con un archivio dati non relazionale | Servizi mobili di Azure" 
	description="Informazioni su come usare un archivio di dati non relazionali, ad esempio MongoDB o Archiviazione tabelle di Azure, con il servizio mobile basato su .NET" 
	services="mobile-services" 
	documentationCenter="" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="mollybos"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/24/2015" 
	ms.author="mahender"/>

# Creare un servizio mobile back-end .NET che usa MongoDB invece di un database SQL per l'archiviazione

Questo argomento illustra come usare un archivio dati non relazionale per il servizio mobile back-end .NET. In questa esercitazione si modificherà il progetto introduttivo di Servizi mobili per usare MongoDB anziché il database SQL di Azure predefinito come archivio dati.

Per questa esercitazione è prima di tutto necessario completare l'esercitazione [Introduzione a Servizi mobili] o [Aggiungere Servizi mobili a un'app esistente]. Sarà anche necessario aggiungere il servizio MongoLab alla sottoscrizione.

## <a name="create-store"></a>Creare l'archivio non relazionale di MongoLab

1. Nel [portale di gestione di Azure] fare clic su **Nuovo** e quindi su **Marketplace**.

2. Fare clic sul componente aggiuntivo **MongoLab** e completare la procedura guidata per ottenere un account MongoLab.

	Per altre informazioni su MongoLab, vedere la [pagina del componente aggiuntivo MongoLab].

2. Dopo la configurazione dell'account fare clic su **Informazioni di connessione** e copiare la stringa di connessione.

3. Nel servizio mobile fare clic sulla scheda **Configura**, scorrere fino a **Stringhe di connessione** e immettere una nuova stringa di connessione con **Nome** pari a `MongoConnectionString` **Valore** corrispondente alla connessione MongoDB, quindi fare clic su **Salva**.

	![Aggiungere la stringa di connessione di MongoDB](./media/mobile-services-dotnet-backend-use-non-relational-data-store/mongo-connection-string.png)

	La stringa di connessione dell'account di archiviazione è archiviata in formato crittografato nelle impostazioni dell'app. È possibile accedere alla stringa in qualsiasi controller di tabella in fase di esecuzione.

8. In Esplora soluzioni in Visual Studio aprire il file Web.config per il progetto di servizio mobile, quindi aggiungere la nuova stringa di connessione seguente:

		<add name="MongoConnectionString" connectionString="<MONGODB_CONNECTION_STRING>" />

9. Sostituire il segnaposto `<MONGODB_CONNECTION_STRING>` con la stringa di connessione di MongoDB.

	Il servizio mobile usa la stringa di connessione quando è in esecuzione sul computer locale, consentendo in questo modo di testare il codice prima di pubblicarlo. Durante l'esecuzione in Azure, il servizio mobile usa invece il valore della stringa di connessione impostato nel portale e ignora la stringa di connessione nel progetto.

## <a name="modify-service"></a>Modificare i tipi di dati e i controller di tabella

1. Installare il pacchetto NuGet **WindowsAzure.MobileServices.Backend.Mongo**.

2. Modificare **TodoItem** in modo che derivi da **DocumentData** anziché da **EntityData**.

        public class TodoItem : DocumentData
        {
            public string Text { get; set; }

            public bool Complete { get; set; }
        }

3. In **TodoItemController** sostituire il metodo **Initialize** con il codice seguente:

        protected override async void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);
            string connectionStringName = "MongoConnectionString";
            string databaseName = "<YOUR-DATABASE-NAME>";
            string collectionName = "todoItems";
            DomainManager = new MongoDomainManager<TodoItem>(connectionStringName, databaseName, 
				collectionName, Request, Services);
        }

4. Nel codice del metodo **Initialize** sostituire `<YOUR-DATABASE-NAME>` con il nome scelto quando si è eseguito il provisioning del componente aggiuntivo MongoLab.

È ora possibile testare l'app.

## <a name="test-application"></a>Testare l'applicazione

1. (Facoltativo) Ripubblicare il progetto di back-end .NET del servizio mobile.

	È anche possibile testare localmente il servizio mobile prima di pubblicare il progetto back-end .NET in Azure. Indipendentemente dall'esecuzione del test in locale o in Azure, il servizio mobile userà MongoDB per l'archiviazione.

4. Mediante il pulsante **Prova adesso** nella pagina iniziale, come illustrato in precedenza, o mediante un'app client connessa all'app per dispositivi mobili, eseguire query sugli elementi nel database.
 
	Notare che non verrà visualizzato alcun elemento archiviato precedentemente nel database SQL dall'esercitazione relativa alla guida introduttiva.

	>[AZURE.NOTE]Quando si usa il pulsante**Prova adesso** per avviare le pagine delle API della Guida, specificare la chiave dell'applicazione come password (con un nome utente vuoto).

3. Creare un nuovo elemento.

	Il comportamento dell'app e del servizio mobile non dovrebbe cambiare, ma i dati vengono ora archiviati nell'archivio non relazionale, invece che nel database SQL.

##Passaggi successivi

Vista la semplicità dell'uso dell'archiviazione tabelle con il back-end .NET, è possibile prendere in considerazione l'esplorazione di altre opzioni di archiviazione back-end:

+ [Creare un servizio mobile back-end .NET che usa l'archiviazione tabelle invece di un database SQL](mobile-services-dotnet-backend-store-data-table-storage.md)</br>Analogamente all'esercitazione appena completata, questo argomento illustra come usare un archivio dati non relazionale per il servizio mobile. In questa esercitazione si modificherà il progetto introduttivo di Servizi mobili per usare l'archiviazione di Azure anziché il database SQL come archivio dati.
 
+ [Connettersi a un'istanza di SQL Server locale da un servizio mobile di Azure mediante Connessioni ibride](mobile-services-dotnet-backend-hybrid-connections-get-started.md)</br>Connessioni ibride permette al servizio mobile di connettersi in modo sicuro agli asset locali. In questo modo, è possibile rendere accessibili i dati locali ai client mobili mediante Azure. Sono supportate tutte le risorse che usano una porta TCP statica, compresi Microsoft SQL Server, MySQL, le API Web HTTP e la maggior parte dei servizi Web personalizzati.

+ [Caricare immagini in Archiviazione di Azure mediante Servizi mobili](mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage.md)</br>Illustra come estendere il progetto di esempio TodoList per permettere di caricare immagini dall'app nell'archivio BLOB di Azure.


<!-- Anchors. -->
[Create a non-relational store]: #create-store
[Modify data and controllers]: #modify-service
[Test the application]: #test-application


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-use-non-relational-data-store/create-mongo-lab.png
[1]: ./media/mobile-services-dotnet-backend-use-non-relational-data-store/mongo-connection-string.png


<!-- URLs. -->
[Introduzione a Servizi mobili]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Aggiungere Servizi mobili a un'app esistente]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md
[portale di gestione di Azure]: https://manage.windowsazure.com/
[What is the Table Service]: ../storage-dotnet-how-to-use-tables.md#what-is
[pagina del componente aggiuntivo MongoLab]: /gallery/store/mongolab/mongolab
 

<!---HONumber=July15_HO4-->