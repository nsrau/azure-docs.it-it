<properties 
    pageTitle="Esempi di DocumentDB per MongoDB | Microsoft Azure" 
    description="È possibile trovare esempi del supporto del protocollo di DocumentDB per MongoDB." 
    keywords="esempi di mongodb"
    services="documentdb" 
    authors="AndrewHoh" 
    manager="jhubbard" 
    editor="" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/23/2016" 
    ms.author="anhoh"/>


# <a name="documentdb-protocol-support-for-mongodb-examples"></a>Esempi di supporto del protocollo DocumentDB per MongoDB
Per usare questi esempi, è necessario seguire questa procedura:

- [Creare](documentdb-create-mongodb-account.md) un account Azure DocumentDB con supporto del protocollo per MongoDB.
- Recuperare l'account DocumentDB con supporto del protocollo per le informazioni sulla [stringa di connessione](documentdb-connect-mongodb-account.md) di MongoDB.

## <a name="get-started-with-a-sample-asp.net-mvc-task-list-application"></a>Iniziare a usare un'applicazione di elenco attività ASP.NET MVC di esempio

È possibile usare l'esercitazione [Creare un'app Web in Azure che si connette a MongoDB in esecuzione su una macchina virtuale](../app-service-web/web-sites-dotnet-store-data-mongodb-vm.md) , con modifiche minime, per configurare rapidamente un'applicazione MongoDB, in locale o pubblicata in un'app Web di Azure, che si connette a un account DocumentDB con supporto del protocollo per MongoDB.  

1. Seguire l'esercitazione, con una modifica.  Sostituire il codice del file Dal.cs con il codice seguente:
    
        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
        using MyTaskListApp.Models;
        using MongoDB.Driver;
        using MongoDB.Bson;
        using System.Configuration;
        using System.Security.Authentication;

        namespace MyTaskListApp
        {
            public class Dal : IDisposable
            {
                //private MongoServer mongoServer = null;
                private bool disposed = false;

                // To do: update the connection string with the DNS name
                // or IP address of your server. 
                //For example, "mongodb://testlinux.cloudapp.net
                private string connectionString = "mongodb://localhost:27017";
                private string userName = "<your user name>";
                private string host = "<your host>";
                private string password = "<your password>";

                // This sample uses a database named "Tasks" and a 
                //collection named "TasksList".  The database and collection 
                //will be automatically created if they don't already exist.
                private string dbName = "Tasks";
                private string collectionName = "TasksList";

                // Default constructor.        
                public Dal()
                {
                }

                // Gets all Task items from the MongoDB server.        
                public List<MyTask> GetAllTasks()
                {
                    try
                    {
                        var collection = GetTasksCollection();
                        return collection.Find(new BsonDocument()).ToList();
                    }
                    catch (MongoConnectionException)
                    {
                        return new List<MyTask>();
                    }
                }

                // Creates a Task and inserts it into the collection in MongoDB.
                public void CreateTask(MyTask task)
                {
                    var collection = GetTasksCollectionForEdit();
                    try
                    {
                        collection.InsertOne(task);
                    }
                    catch (MongoCommandException ex)
                    {
                        string msg = ex.Message;
                    }
                }
        
                private IMongoCollection<MyTask> GetTasksCollection()
                {
                    MongoClientSettings settings = new MongoClientSettings();
                    settings.Server = new MongoServerAddress(host, 10250);
                    settings.UseSsl = true;
                    settings.SslSettings = new SslSettings();
                    settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;
        
                    MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
                    MongoIdentityEvidence evidence = new PasswordEvidence(password);
        
                    settings.Credentials = new List<MongoCredential>()
                    {
                        new MongoCredential("SCRAM-SHA-1", identity, evidence)
                    };

                    MongoClient client = new MongoClient(settings);
                    var database = client.GetDatabase(dbName);
                    var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                    return todoTaskCollection;
                }
        
                private IMongoCollection<MyTask> GetTasksCollectionForEdit()
                {
                    MongoClientSettings settings = new MongoClientSettings();
                    settings.Server = new MongoServerAddress(host, 10250);
                    settings.UseSsl = true;
                    settings.SslSettings = new SslSettings();
                    settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;
        
                    MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
                    MongoIdentityEvidence evidence = new PasswordEvidence(password);
        
                    settings.Credentials = new List<MongoCredential>()
                    {
                        new MongoCredential("SCRAM-SHA-1", identity, evidence)
                    };
                    MongoClient client = new MongoClient(settings);
                    var database = client.GetDatabase(dbName);
                    var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                    return todoTaskCollection;
                }

                # region IDisposable
        
                public void Dispose()
                {
                    this.Dispose(true);
                    GC.SuppressFinalize(this);
                }

                protected virtual void Dispose(bool disposing)
                {
                    if (!this.disposed)
                    {
                        if (disposing)
                        {
                        }
                    }

                    this.disposed = true;
                }

                # endregion
            }
        }

2.  Modificare le variabili seguenti nel file Dal.cs in base alle impostazioni dell'account:

        private string userName = "<your user name>";
        private string host = "<your host>";
        private string password = "<your password>";

3. Usare l'app.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [usare MongoChef](documentdb-mongodb-mongochef.md) con un account DocumentDB con supporto del protocollo per MongoDB.

 



<!--HONumber=Oct16_HO2-->


