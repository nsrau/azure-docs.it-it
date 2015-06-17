<properties 
	pageTitle="Compilare un servizio con un archivio dati non relazionale - Servizi mobili di Azure" 
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

# Compilare un servizio con MongoDB come archivio dati con il back-end .NET

Questo argomento descrive come usare un archivio dati non relazionale per il servizio mobile. In questa esercitazione si modificherà il progetto di guida introduttiva di Servizi mobili per usare MongoDB anziché SQL come archivio dati.

Questa esercitazione descrive i passaggi per configurare un archivio non relazionale:

1. [Creare un archivio non relazionale]
2. [Modificare dati e controller]
3. [Testare l'applicazione]

Prima di iniziare questa esercitazione, è necessario avere completato l'esercitazione [Introduzione a Servizi mobili] o [Introduzione ai dati].

## <a name="create-store"></a>Creare un archivio non relazionale

1. Nel [portale di gestione di Azure] fare clic su **Nuovo** e selezionare **Archivio**.

2. Selezionare il componente aggiuntivo **MongoLab** ed esplorare la procedura guidata per iscriversi per creare un account. Per altre informazioni su MongoLab, vedere la [pagina del componente aggiuntivo MongoLab].

    ![][0]

2. Dopo la configurazione dell'account selezionare **Informazioni di connessione** e copiare la stringa di connessione.

3. Passare alla sezione Servizi mobili del portale e selezionare la scheda **Configura**.

4. In **Impostazioni app** immettere la stringa di connessione con la chiave "MongoConnectionString" e fare clic su **Salva**.

    ![][1]

2. Aggiungere il codice seguente a `TodoItemController`:

        static bool connectionStringInitialized = false;

        private void InitializeConnectionString(string connectionStringName)
        {
            if (!connectionStringInitialized)
            {
                connectionStringInitialized = true;
                if (!this.Services.Settings.Connections.ContainsKey(connectionStringName))
                {
                    var connectionString = this.Services.Settings[connectionStringName];
                    var connectionSetting = new ConnectionSettings(connectionStringName, connectionString);
                    this.Services.Settings.Connections.Add(connectionStringName, connectionSetting);
                }
            }
        }
    
    Questo codice carica l'impostazione dell'applicazione e indica al servizio mobile di considerarla come una connessione che può essere usata da un metodo `TableController`. Successivamente, si chiamerà questo metodo quando viene richiamato `TodoItemController`.



## <a name="modify-service"></a>Modificare dati e controller

1. Installare il pacchetto NuGet **WindowsAzure.MobileServices.Backend.Mongo**.

2. Modificare `TodoItem` in modo che derivi da `DocumentData` anziché da `EntityData`.

        public class TodoItem : DocumentData
        {
            public string Text { get; set; }

            public bool Complete { get; set; }
        }

3. In `TodoItemController` sostituire il metodo `Initialize` con il codice seguente:

        protected override async void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);
            string connectionStringName = "MongoConnectionString";
            string databaseName = "<YOUR-DATABASE-NAME>";
            string collectionName = "todoItems";
            InitializeConnectionString(connectionStringName);
            DomainManager = new MongoDomainManager<TodoItem>(connectionStringName, databaseName, collectionName, Request, Services);
        }

4. Nel codice per il metodo `Initialize` riportato sopra sostituire **YOUR-DATABASE-NAME** con il nome scelto quando si è eseguito il provisioning del componente aggiuntivo MongoLab.


## <a name="test-application"></a>Testare l'applicazione

1. Ripubblicare il progetto di back-end del servizio mobile.

2. Eseguire l'applicazione client. Notare che non verrà visualizzato alcun elemento archiviato precedentemente nel database SQL dall'esercitazione relativa alla guida introduttiva.

3. Creare un nuovo elemento. Il comportamento dell'app rimane invariato, tranne per il fatto che i dati ora verranno inseriti nell'archivio non relazionale.


<!-- Anchors. -->
[Creare un archivio non relazionale]: #create-store
[Modificare dati e controller]: #modify-service
[Testare l'applicazione]: #test-application


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-use-non-relational-data-store/create-mongo-lab.png
[1]: ./media/mobile-services-dotnet-backend-use-non-relational-data-store/mongo-connection-string.png


<!-- URLs. -->
[Introduzione a Servizi mobili]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Introduzione ai dati]: mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md
[portale di gestione di Azure]: https://manage.windowsazure.com/
[What is the Table Service]: storage/storage-dotnet-how-to-use-tables.md#what-is
[pagina del componente aggiuntivo MongoLab]: /gallery/store/mongolab/mongolab

<!--HONumber=54-->