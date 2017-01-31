---
title: Introduzione all&quot;archiviazione tabelle di Azure e a Servizi connessi di Visual Studio (ASP.NET) | Documentazione Microsoft
description: Informazioni su come iniziare a usare il servizio di archiviazione tabelle in un progetto ASP.NET in Visual Studio dopo aver eseguito la connessione a un account di archiviazione con Servizi connessi di Visual Studio
services: storage
documentationcenter: 
author: TomArcher
manager: douge
editor: 
ms.assetid: af81a326-18f4-4449-bc0d-e96fba27c1f8
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 07f827e13e5f01a373e69b90e8a5a0c72081acd0
ms.openlocfilehash: 24cfb3217dbadba3a086a8b0251efe5ab0173d1f


---
# <a name="get-started-with-azure-table-storage-and-visual-studio-connected-services-aspnet"></a>Introduzione all'archiviazione tabelle di Azure e a Servizi connessi di Visual Studio (ASP.NET)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Panoramica

L’archiviazione tabelle di Azure consente di archiviare grandi quantità di dati strutturati. Il servizio è un datastore NoSQL che accetta chiamate autenticate dall'interno e dall'esterno del cloud di Azure. Le tabelle di Azure sono ideali per l'archiviazione di dati strutturati non relazionali.

Questo articolo descrive come gestire a livello di codice le entità di archiviazione tabelle di Azure, eseguire attività comuni, come la creazione e l'eliminazione di una tabella, nonché usare le entità tabella. 

> [!NOTE]
> 
> Le sezioni di codice di questo articolo presuppongono che sia già stata effettuata la connessione a un account di archiviazione di Azure mediante Servizi connessi. Per configurare Servizi connessi aprire Esplora soluzioni di Visual Studio, fare clic sul progetto con il pulsante destro del mouse e selezionare l'opzione **Aggiungi-> Servizio connesso**. Seguire le istruzioni visualizzate nella finestra di dialogo per connettersi all'account di archiviazione di Azure desiderato.      

## <a name="create-a-table-in-code"></a>Creazione di una tabella in codice

I passaggi seguenti illustrano come creare una tabella a livello di codice. In un'app MVC ASP.NET il codice verrà inserito in un controller.

1. Aggiungere le seguenti direttive *using*:

         using Microsoft.Azure;
         using Microsoft.WindowsAzure.Storage;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Table;

2. Ottenere un oggetto **CloudStorageAccount** che rappresenta le informazioni sull'account di archiviazione. Utilizzare il codice seguente per ottenere la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure. Cambiare *<storage-account-name>* nel nome dell'account di archiviazione di Azure a cui si sta eseguendo l'accesso.

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Ottenere un oggetto **CloudTableClient** che rappresenta un client del servizio tabelle.

        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();


4. Ottenere un oggetto **CloudTable** che rappresenta un riferimento al nome della tabella desiderata. Cambiare *<table-name>* nel nome della tabella che si vuole creare.

        CloudTable table = tableClient.GetTableReference(<table-name>);

5. Chiamare il metodo **CloudTable.CreateIfNotExists** per creare la tabella se non esiste.   
   
        table.CreateIfNotExists();

## <a name="add-an-entity-to-a-table"></a>Aggiungere un'entità a una tabella

I passaggi seguenti illustrano come aggiungere un'entità a una tabella a livello di codice. In un'app MVC ASP.NET il codice verrà inserito in un controller. 

1. Aggiungere le seguenti direttive *using*:

         using Microsoft.Azure;
         using Microsoft.WindowsAzure.Storage;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Table;

2. Ottenere un oggetto **CloudStorageAccount** che rappresenta le informazioni sull'account di archiviazione. Utilizzare il codice seguente per ottenere la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure. Cambiare *<storage-account-name>* nel nome dell'account di archiviazione di Azure a cui si sta eseguendo l'accesso.

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Ottenere un oggetto **CloudTableClient** che rappresenta un client del servizio tabelle.

        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();


4. Ottenere un oggetto **CloudTable** che rappresenta un riferimento al nome della tabella desiderata. Cambiare *<table-name>* nel nome della tabella a cui aggiungere l'entità.

        CloudTable table = tableClient.GetTableReference(<table-name>);

5. Per aggiungere un'entità a una tabella si definisce una classe derivata da **TableEntity**. Il codice seguente permette di definire una classe di entità denominata **CustomerEntity** che usa il nome e il cognome del cliente rispettivamente come chiave di riga e chiave di partizione.

        public class CustomerEntity : TableEntity
        {
            public CustomerEntity(string lastName, string firstName)
            {
                this.PartitionKey = lastName;
                this.RowKey = firstName;
            }
    
            public CustomerEntity() { }
    
            public string Email { get; set; }
        }

6. Creare un'istanza dell'entità.

        CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
        customer1.Email = "Walter@contoso.com";

7. Creare l'oggetto **TableOperation** che inserisce l'entità cliente.

        TableOperation insertOperation = TableOperation.Insert(customer1);

8. Eseguire l'operazione di inserimento chiamando il metodo **CloudTable.Execute**. È possibile verificare il risultato dell'operazione controllando la proprietà **TableResult.HttpStatusCode**. Il codice di stato 2xx indica che l'azione richiesta dal client è stata elaborata correttamente. Ad esempio, l'inserimento di nuove entità genera il codice di stato HTTP 204 indicante che l'operazione è stata elaborata correttamente e il server non ha restituito alcun contenuto.

        TableResult result = table.Execute(insertOperation);

        // Inspect result.HttpStatusCode for success/failure.

## <a name="add-a-batch-of-entities-to-a-table"></a>Aggiungere un batch di entità a una tabella

Oltre ad aggiungere entità a una tabella una alla volta, è possibile aggiungere entità anche in batch. Questo riduce il numero di round trip tra il codice e il servizio tabelle di Azure. I passaggi seguenti illustrano come aggiungere più entità a una tabella a livello di codice con una singola operazione. In un'app MVC ASP.NET il codice verrà inserito in un controller.

1. Aggiungere le seguenti direttive *using*:

         using Microsoft.Azure;
         using Microsoft.WindowsAzure.Storage;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Table;

2. Ottenere un oggetto **CloudStorageAccount** che rappresenta le informazioni sull'account di archiviazione. Utilizzare il codice seguente per ottenere la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure. Cambiare *<storage-account-name>* nel nome dell'account di archiviazione di Azure a cui si sta eseguendo l'accesso.

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Ottenere un oggetto **CloudTableClient** che rappresenta un client del servizio tabelle.

        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();


4. Ottenere un oggetto **CloudTable** che rappresenta un riferimento al nome della tabella desiderata. Cambiare *<table-name>* nel nome della tabella a cui aggiungere le entità.

        CloudTable table = tableClient.GetTableReference(<table-name>);

5. Per aggiungere un'entità a una tabella si definisce una classe derivata da **TableEntity**. Il codice seguente permette di definire una classe di entità denominata **CustomerEntity** che usa il nome e il cognome del cliente rispettivamente come chiave di riga e chiave di partizione.

        public class CustomerEntity : TableEntity
        {
            public CustomerEntity(string lastName, string firstName)
            {
                this.PartitionKey = lastName;
                this.RowKey = firstName;
            }
    
            public CustomerEntity() { }
    
            public string Email { get; set; }
        }

6. Creare istanze delle entità.

        CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
        customer1.Email = "Jeff@contoso.com";
    
        CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
        customer2.Email = "Ben@contoso.com";

7. Ottenere un oggetto **TableBatchOperation**.

        TableBatchOperation batchOperation = new TableBatchOperation();

8. Aggiungere le entità all'operazione di inserimento in batch.

        batchOperation.Insert(customer1);
        batchOperation.Insert(customer2);

9. Eseguire l'operazione di inserimento in batch chiamando il metodo **CloudTable.ExecuteBatch**. Il metodo **CloudTable.ExecuteBatch** restituisce un elenco di oggetti **TableResult**. È possibile verificare il risultato dell'operazione di inserimento in batch controllando la proprietà **TableResult.HttpStatusCode** di ciascun oggetto **TableResult** dell'elenco. Il codice di stato 2xx indica che l'azione richiesta dal client è stata elaborata correttamente. Ad esempio, l'inserimento di nuove entità genera il codice di stato HTTP 204 indicante che l'operazione è stata elaborata correttamente e il server non ha restituito alcun contenuto.
    
        IList<TableResult> results = table.ExecuteBatch(batchOperation);

        // Inspect the HttpStatusCode property of each TableResult object
        // in the results list for success/failure.

## <a name="get-a-single-entity"></a>Ottenere una singola entità

I passaggi seguenti illustrano come ottenere un'entità da una tabella a livello di codice. In un'app MVC ASP.NET il codice verrà inserito in un controller. 

> [!NOTE]
> 
> Il codice di questa sezione fa riferimento alla classe **CustomerEntity** e ai dati presentati nella sezione [Aggiungere un batch di entità a una tabella](#add-a-batch-of-entities-to-a-table). 

1. Aggiungere le seguenti direttive *using*:

         using Microsoft.Azure;
         using Microsoft.WindowsAzure.Storage;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Table;

2. Ottenere un oggetto **CloudStorageAccount** che rappresenta le informazioni sull'account di archiviazione. Utilizzare il codice seguente per ottenere la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure. Cambiare *<storage-account-name>* nel nome dell'account di archiviazione di Azure a cui si sta eseguendo l'accesso.

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Ottenere un oggetto **CloudTableClient** che rappresenta un client del servizio tabelle.

        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();


4. Ottenere un oggetto **CloudTable** che rappresenta un riferimento al nome della tabella desiderata. Cambiare *<table-name>* nel nome della tabella a cui aggiungere le entità.

        CloudTable table = tableClient.GetTableReference(<table-name>);

5. Creare un oggetto operazione di recupero che accetta un oggetto entità derivato da **TableEntity**. Il primo parametro è il *partitionKey* e il secondo è il *rowKey*. Usando la classe **CustomerEntity** e i dati presentati nella sezione [Aggiungere un batch di entità a una tabella](#add-a-batch-of-entities-to-a-table), il frammento di codice seguente esegue una query sulla tabella per un **CustomerEntity** il cui valore *partitionKey* sia "Smith" e il cui valore *rowKey* sia "Ben".  

        TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

6. Eseguire l'operazione di recupero.   

        TableResult retrievedResult = table.Execute(retrieveOperation);

7. Verificare il risultato dell'operazione controllando la proprietà **TableOperation.HttpStatusCode** in cui il codice di stato 200 indica che l'azione richiesta dal client è stata elaborata correttamente. È anche possibile esaminare la proprietà **TableResult.Result** che (se l'operazione ha esito positivo) conterrà l'entità restituita.

        CustomerEntity customer = null;

        if (retrievedResult.HttpStatusCode == 200 && retrievedResult.Result != null)
        {
            // Process the customer entity.
            customer = retrievedResult.Result as CustomerEntity;
        }

## <a name="get-all-entities-in-a-partition"></a>Recuperare tutte le entità di una partizione

I passaggi seguenti illustrano come ottenere tutte le entità da una partizione a livello di codice. In un'app MVC ASP.NET il codice verrà inserito in un controller. 

> [!NOTE]
> 
> Il codice di questa sezione fa riferimento alla classe **CustomerEntity** e ai dati presentati nella sezione [Aggiungere un batch di entità a una tabella](#add-a-batch-of-entities-to-a-table). 

1. Aggiungere le seguenti direttive *using*:

         using Microsoft.Azure;
         using Microsoft.WindowsAzure.Storage;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Table;

2. Ottenere un oggetto **CloudStorageAccount** che rappresenta le informazioni sull'account di archiviazione. Utilizzare il codice seguente per ottenere la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure. Cambiare *<storage-account-name>* nel nome dell'account di archiviazione di Azure a cui si sta eseguendo l'accesso.

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Ottenere un oggetto **CloudTableClient** che rappresenta un client del servizio tabelle.

        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();


4. Ottenere un oggetto **CloudTable** che rappresenta un riferimento al nome della tabella desiderata. Cambiare *<table-name>* nel nome della tabella a cui aggiungere le entità.

        CloudTable table = tableClient.GetTableReference(<table-name>);

5. Creare un'istanza di un oggetto **TableQuery** specificando la query nella clausola **Where**. Usando la classe **CustomerEntity** e i dati presentati nella sezione [Aggiungere un batch di entità a una tabella](#add-a-batch-of-entities-to-a-table), il frammento di codice seguente esegue una query sulla tabella per tutte le entità in cui il valore **PartitionKey** sia "Smith".

        TableQuery<CustomerEntity> query = 
            new TableQuery<CustomerEntity>()
            .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

6. All'interno di un ciclo chiamare il metodo **CloudTable.ExecuteQuerySegmented** passando l'oggetto query istanziato nel passaggio precedente.  Il metodo **CloudTable.ExecuteQuerySegmented** restituisce un oggetto **TableContinuationToken** che, quando è **null**, indica che non ci sono più entità da recuperare. All'interno del ciclo usare un altro ciclo per eseguire un'iterazione sulle entità restituite.

        TableContinuationToken token = null;
        do
        {
            TableQuerySegment<CustomerEntity>resultSegment = table.ExecuteQuerySegmented(query, token);
            token = resultSegment.ContinuationToken;

            foreach (CustomerEntity customer in resultSegment.Results)
            {
                // Process customer entity.
            }
        } while (token != null);

## <a name="delete-an-entity"></a>Eliminare un'entità

La procedura seguente illustra come cercare e quindi eliminare un'entità.

1. Aggiungere le seguenti direttive *using*:

         using Microsoft.Azure;
         using Microsoft.WindowsAzure.Storage;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Table;

2. Ottenere un oggetto **CloudStorageAccount** che rappresenta le informazioni sull'account di archiviazione. Utilizzare il codice seguente per ottenere la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure. Cambiare *<storage-account-name>* nel nome dell'account di archiviazione di Azure a cui si sta eseguendo l'accesso.

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Ottenere un oggetto **CloudTableClient** che rappresenta un client del servizio tabelle.

        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();


4. Ottenere un oggetto **CloudTable** che rappresenta un riferimento al nome della tabella desiderata. Cambiare *<table-name>* nel nome della tabella a cui aggiungere le entità.

        CloudTable table = tableClient.GetTableReference(<table-name>);

5. Creare un oggetto operazione di recupero che accetta un oggetto entità derivato da **TableEntity**. Il primo parametro è il *partitionKey* e il secondo è il *rowKey*. Usando la classe **CustomerEntity** e i dati presentati nella sezione [Aggiungere un batch di entità a una tabella](#add-a-batch-of-entities-to-a-table), il frammento di codice seguente esegue una query sulla tabella per un **CustomerEntity** il cui valore *partitionKey* sia "Smith" e il cui valore *rowKey* sia "Ben".  

        TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

6. Eseguire l'operazione di recupero.   

        TableResult retrievedResult = table.Execute(retrieveOperation);

7. Verificare il risultato dell'operazione controllando la proprietà **TableOperation.HttpStatusCode** in cui il codice di stato 200 indica che l'azione richiesta dal client è stata elaborata correttamente. È anche possibile esaminare la proprietà **TableResult.Result** che (se l'operazione ha esito positivo) conterrà l'entità restituita. All'interno dell'istruzione condizionale per verificare che l'operazione ha avuto esito positivo, creare un'operazione di eliminazione (passando l'entità restituita dalla query) ed eseguire l'operazione di eliminazione.

        if (retrievedResult.HttpStatusCode == 200 && retrievedResult.Result != null)
        {
            CustomerEntity customer = retrievedResult.Result as CustomerEntity;

            // Create the delete operation.
            TableOperation deleteOperation = TableOperation.Delete(customer);

            // Execute the delete operation.
            table.Execute(deleteOperation);
        }

## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]




<!--HONumber=Dec16_HO2-->


