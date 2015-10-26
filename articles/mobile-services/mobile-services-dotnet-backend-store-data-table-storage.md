<properties 
	pageTitle="Creare un servizio mobile back-end .NET che usa l'archiviazione tabelle | Servizi mobili di Azure" 
	description="Informazioni su come usare l'archiviazione tabelle di Azure con il servizio mobile back-end .NET." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="09/14/2015" 
	ms.author="glenga"/>

# Creare un servizio mobile back-end .NET che usa l'archiviazione tabelle

Questo argomento illustra come usare un archivio dati non relazionale per il servizio mobile back-end .NET. In questa esercitazione verrà modificato il progetto introduttivo dei Servizi mobili di Azure, in modo da usare l'archiviazione tabelle di Azure invece dell'archivio dati predefinito del database SQL di Azure.

Prima di iniziare questa esercitazione, è necessario avere completato l'esercitazione [Introduzione a Servizi per dispositivi mobili]. Sarà necessario anche un account di archiviazione di Azure.

##Configurare l'archiviazione tabelle di Azure nel servizio mobile back-end .NET

È prima di tutto necessario configurare il servizio mobile e il progetto di codice back-end .NET per la connessione all'archiviazione di Azure.

1. In **Esplora soluzioni** in Visual Studio fare clic con il pulsante destro del mouse sul progetto back-end .NET e quindi scegliere **Gestisci pacchetti NuGet**.

2. Nel riquadro sinistro selezionare la categoria **Online**, selezionare **Solo stabili**, cercare **MobileServices**, fare clic su **Installa** nel pacchetto **Microsoft Azure Mobile Services .NET Backend Azure Storage Extension** e quindi accettare il contratto di licenza.

  	![](./media/mobile-services-dotnet-backend-store-data-table-storage/mobile-add-storage-nuget-package-dotnet.png)

  	Il supporto per i servizi di archiviazione di Azure verrà aggiunto al progetto del servizio mobile back-end .NET.

3. Se non è ancora stato creato un account di archiviazione, vedere [Come creare un account di archiviazione](../storage-create-storage-account.md).

4. Nel portale di gestione fare clic su **Archiviazione**, sull'account di archiviazione e quindi su **Gestisci chiavi**.

5. Prendere nota dei valori di **Nome account di archiviazione** e **Chiave di accesso**.
 
6. Nel servizio mobile fare clic sulla scheda **Configura**, scorrere fino a **Stringhe di connessione** e immettere una nuova stringa di connessione con **Nome** pari a `StorageConnectionString` e un **Valore** che corrisponde alla stringa di connessione dell'account di archiviazione nel formato seguente.

		DefaultEndpointsProtocol=https;AccountName=<ACCOUNT_NAME>;AccountKey=<ACCESS_KEY>;

	![](./media/mobile-services-dotnet-backend-store-data-table-storage/mobile-blob-storage-app-settings.png)

7. Nella stringa precedente sostituire i valori di `<ACCOUNT_NAME>` e `<ACCESS_KEY>` con i valori disponibili nel portale, quindi fare clic su **Salva**.

	La stringa di connessione dell'account di archiviazione è archiviata in formato crittografato nelle impostazioni dell'app. È possibile accedere alla stringa in qualsiasi controller di tabella in fase di esecuzione.

8. In Esplora soluzioni in Visual Studio aprire il file Web.config per il progetto di servizio mobile, quindi aggiungere la nuova stringa di connessione seguente:

		<add name="StorageConnectionString" connectionString="<STORAGE_CONNECTION_STRING>" />

9. Sostituire il segnaposto `<STORAGE_CONNECTION_STRING>` con la stringa di connessione del passaggio 6.

	Il servizio mobile usa la stringa di connessione quando è in esecuzione sul computer locale, consentendo in questo modo di testare il codice prima di pubblicarlo. Durante l'esecuzione in Azure, il servizio mobile usa invece il valore della stringa di connessione impostato nel portale e ignora la stringa di connessione nel progetto.

## <a name="modify-service"></a>Modificare i tipi di dati e i controller di tabella

Poiché il progetto introduttivo TodoList è stato progettato per l'uso con un database SQL Database con Entity Framework, sarà necessario apportare alcuni aggiornamenti al progetto per potere usare l'archiviazione tabelle.

1. Modificare il tipo di dati **TodoItem** in modo che derivi da **StorageData** invece di **EntityData**, come illustrato di seguito.

	    public class TodoItem : StorageData
	    {
	        public string Text { get; set; }
	        public bool Complete { get; set; }
	    }

	>[AZURE.NOTE]Il tipo **StorageData** ha una proprietà ID che richiede una chiave composta, ovvero una stringa con formato *partitionId*,*rowValue*.

2. In **TodoItemController** aggiungere l'istruzione using seguente.

		using System.Web.Http.OData.Query;
		using System.Collections.Generic;

3. Sostituire il metodo **Initialize** di **TodoItemController** con il codice seguente.

        protected override void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);

            // Create a new Azure Storage domain manager using the stored 
            // connection string and the name of the table exposed by the controller.
            string connectionStringName = "StorageConnectionString";
            var tableName = controllerContext.ControllerDescriptor.ControllerName.ToLowerInvariant();
            DomainManager = new StorageDomainManager<TodoItem>(connectionStringName, 
                tableName, Request, Services);          
        }

	Verrà creato un nuovo account di gestione del dominio di archiviazione per il controller richiesto mediante la stringa di connessione dell'account di archiviazione.

3. Sostituire il metodo **GetAllTodoItems** esistente con il codice seguente.

		public Task<IEnumerable<TodoItem>> GetAllTodoItems(ODataQueryOptions options)
        {
            // Call QueryAsync, passing the supplied query options.
            return DomainManager.QueryAsync(options);
        } 

	A differenza di un database SQL, questa versione non restituisce IQueryable<TEntity>, quindi è possibile associare il risultato ma non inserirlo in una query.

## Aggiornare l'app client

È necessario apportare una modifica sul lato client per potere usare l'app introduttiva con il back-end .NET mediante l'archiviazione tabelle. Ciò è dovuto alla chiave composta prevista dal provider di archiviazione tabelle.

1. Aprire il file del codice client che include il codice di accesso ai dati e trovare il metodo in cui è stata eseguita l'operazione di inserimento.

2. Aggiornare l'istanza di TodoItem aggiunta per impostare esplicitamente il campo ID sul formato stringa `<partitionID>,<rowValue>`.

	Questo è un esempio di impostazione dell'ID in un'app C#, in cui la parte relativa alla partizione è fissa e la parte relativa alla riga è basata sul GUID.

		 todoItem.Id = string.Format("partition,{0}", Guid.NewGuid());

È ora possibile testare l'app.

## <a name="test-application"></a>Testare l'applicazione

1. (Facoltativo) Ripubblicare il progetto di back-end .NET del servizio mobile. 
	
	È anche possibile testare localmente il servizio mobile prima di pubblicare il progetto back-end .NET in Azure. Indipendentemente dall'esecuzione del test in locale o in Azure, il servizio mobile userà l'archiviazione tabelle di Azure.

4. Eseguire l'app client introduttiva connessa al servizio mobile.

	Si noti che non vengono visualizzati gli elementi aggiunti in precedenza mediante l'esercitazione introduttiva. Ciò dipende dal fatto che l'archivio tabelle è attualmente vuoto.

5. Aggiungere nuovi elementi per generare le modifiche del database.
 
	Il comportamento dell'app e del servizio mobile non dovrebbe cambiare, ma i dati vengono ora archiviati nell'archivio non relazionale, invece che nel database SQL.

##Passaggi successivi

Vista la semplicità dell'uso dell'archiviazione tabelle con il back-end .NET, è possibile prendere in considerazione l'esplorazione di altre opzioni di archiviazione back-end:

+ [Connettersi a un'istanza di SQL Server locale da un servizio mobile di Azure mediante Connessioni ibride](mobile-services-dotnet-backend-hybrid-connections-get-started.md)</br>Connessioni ibride permette al servizio mobile di connettersi in modo sicuro agli asset locali. In questo modo, è possibile rendere accessibili i dati locali ai client mobili mediante Azure. Sono supportate tutte le risorse che usano una porta TCP statica, compresi Microsoft SQL Server, MySQL, le API Web HTTP e la maggior parte dei servizi Web personalizzati.

+ [Caricare immagini in Archiviazione di Azure mediante Servizi mobili](mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage.md)</br>Illustra come estendere il progetto di esempio TodoList per permettere di caricare immagini dall'app nell'archivio BLOB di Azure.

<!-- Anchors. -->
[Create a non-relational store]: #create-store
[Modify data and controllers]: #modify-service
[Test the application]: #test-application


<!-- Images. -->


<!-- URLs. -->
[Introduzione a Servizi per dispositivi mobili]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Azure Management Portal]: https://manage.windowsazure.com/
[What is the Table Service]: ../storage-dotnet-how-to-use-tables.md#what-is
[MongoLab Add-on Page]: /gallery/store/mongolab/mongolab
 

<!---HONumber=Oct15_HO3-->