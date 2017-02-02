---
title: Introduzione all&quot;archiviazione code di Azure e ai servizi connessi di Visual Studio (ASP.NET) | Documentazione Microsoft
description: Informazioni su come iniziare a usare l&quot;archiviazione code di Azure in un progetto ASP.NET in Visual Studio dopo aver eseguito la connessione a un account di archiviazione con i servizi connessi di Visual Studio
services: storage
documentationcenter: 
author: TomArcher
manager: douge
editor: 
ms.assetid: 94ca3413-5497-433f-abbe-836f83a9de72
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: f58c2b522f81dc2dc86f0d2c6bc4872504cf7377
ms.openlocfilehash: 70875287e79aaf49e1b8802cf2953cd5381b97f4


---
# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services-aspnet"></a>Introduzione all'archiviazione code di Azure e ai servizi connessi di Visual Studio (ASP.NET)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Panoramica

L'archiviazione code di Azure è un servizio per l'archiviazione di quantità elevate di dati non strutturati a cui è possibile accedere tramite HTTP o HTTPS. La dimensione massima di un singolo messaggio della coda è di 64 KB e una coda può contenere un numero illimitato di messaggi, entro i limiti della capacità complessiva di un account di archiviazione.

Questo articolo descrive come gestire a livello di codice le entità di archiviazione code di Azure ed eseguire attività comuni, come la creazione di una coda, nonché l'aggiunta, la modifica, la lettura e la rimozione di messaggi in una coda.

> [!NOTE]
> 
> Le sezioni di codice di questo articolo presuppongono che sia già stata effettuata la connessione a un account di archiviazione di Azure mediante servizi connessi. Per configurare i servizi connessi, aprire Esplora soluzioni di Visual Studio, fare clic con il pulsante destro del mouse sul progetto e scegliere l'opzione **Aggiungi-> Servizio connesso**. Seguire le istruzioni nella finestra di dialogo per connettersi all'account di archiviazione di Azure desiderato.      

## <a name="create-a-queue"></a>Creare una coda

I passaggi seguenti illustrano come creare una coda a livello di codice. In un'app MVC ASP.NET il codice verrà inserito in un controller.

1. Aggiungere le direttive *using* seguenti:
   
        using Microsoft.Azure;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. Ottenere un oggetto **CloudStorageAccount** che rappresenta le informazioni sull'account di archiviazione. Utilizzare il codice seguente per ottenere la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure. Sostituire *<storage-account-name>* con il nome dell'account di archiviazione di Azure a cui si sta eseguendo l'accesso.

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Ottenere un oggetto **CloudQueueClient** che rappresenta un client del servizio di accodamento.

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. Ottenere un oggetto **CloudQueue** che rappresenta un riferimento al nome della coda desiderata. Sostituire *<queue-name>* con il nome della coda da creare.

        CloudQueue queue = queueClient.GetQueueReference(<queue-name>);

5. Chiamare il metodo **CloudQueue.CreateIfNotExists** per creare la coda se non esiste. 

        queue.CreateIfNotExists();


## <a name="add-a-message-to-a-queue"></a>Aggiungere un messaggio a una coda

I passaggi seguenti illustrano come aggiungere un messaggio a una coda a livello di codice. In un'app MVC ASP.NET il codice verrà inserito in un controller.

1. Aggiungere le direttive *using* seguenti:
   
        using Microsoft.Azure;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. Ottenere un oggetto **CloudStorageAccount** che rappresenta le informazioni sull'account di archiviazione. Utilizzare il codice seguente per ottenere la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure. Sostituire *<storage-account-name>* con il nome dell'account di archiviazione di Azure a cui si sta eseguendo l'accesso.

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Ottenere un oggetto **CloudQueueClient** che rappresenta un client del servizio di accodamento.

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. Ottenere un oggetto **CloudQueue** che rappresenta un riferimento al nome della coda desiderata. Sostituire *<queue-name>* con il nome della coda a cui si vuole aggiungere il messaggio.

        CloudQueue queue = queueClient.GetQueueReference(<queue-name>);

5. Creare l'oggetto **CloudQueueMessage** che rappresenta il messaggio da aggiungere alla coda. È possibile creare un oggetto **CloudQueueMessage** da una stringa in formato UTF-8 o da una matrice di byte. Sostituire *<queue-message>* con il messaggio che si vuole aggiungere.

        CloudQueueMessage message = new CloudQueueMessage(<queue-message>);

6. Chiamare il metodo **CloudQueue.AddMessage** per aggiungere il messaggio alla coda.

        queue.AddMessage(message);

## <a name="read-a-message-from-a-queue-without-removing-it"></a>Leggere un messaggio da una coda senza rimuoverlo

I passaggi seguenti illustrano come visualizzare un messaggio in coda a livello di codice (leggere il primo messaggio senza rimuoverlo). In un'app MVC ASP.NET il codice verrà inserito in un controller. 

1. Aggiungere le direttive *using* seguenti:
   
        using Microsoft.Azure;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. Ottenere un oggetto **CloudStorageAccount** che rappresenta le informazioni sull'account di archiviazione. Utilizzare il codice seguente per ottenere la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure. Sostituire *<storage-account-name>* con il nome dell'account di archiviazione di Azure a cui si sta eseguendo l'accesso.

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Ottenere un oggetto **CloudQueueClient** che rappresenta un client del servizio di accodamento.

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. Ottenere un oggetto **CloudQueue** che rappresenta un riferimento alla coda. Sostituire *<queue-name>* con il nome della coda da cui si vuole leggere un messaggio.

        CloudQueue queue = queueClient.GetQueueReference(<queue-name>);

5. Chiamare il metodo **CloudQueue.PeekMessage** per leggere il primo messaggio di una coda senza rimuoverlo dalla coda.

        CloudQueueMessage message = queue.PeekMessage();

6. Accedere al valore dell'oggetto **CloudQueueMessage** usando la proprietà **CloudQueueMessage.AsBytes** o **CloudQueueMessage.AsString**.

        string messageAsString = message.AsString;
        byte[] messageAsBytes = message.AsBytes;

## <a name="read-and-remove-a-message-from-a-queue"></a>Leggere e rimuovere un messaggio da una coda

I passaggi seguenti illustrano come leggere un messaggio in una coda a livello di codice e poi eliminarlo. In un'app MVC ASP.NET il codice verrà inserito in un controller. 

1. Aggiungere le direttive *using* seguenti:
   
        using Microsoft.Azure;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. Ottenere un oggetto **CloudStorageAccount** che rappresenta le informazioni sull'account di archiviazione. Utilizzare il codice seguente per ottenere la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure. Sostituire *<storage-account-name>* con il nome dell'account di archiviazione di Azure a cui si sta eseguendo l'accesso.

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Ottenere un oggetto **CloudQueueClient** che rappresenta un client del servizio di accodamento.

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. Ottenere un oggetto **CloudQueue** che rappresenta un riferimento alla coda. Sostituire *<queue-name>* con il nome della coda da cui si vuole leggere un messaggio.

        CloudQueue queue = queueClient.GetQueueReference(<queue-name>);

5. Chiamare il metodo **CloudQueue.GetMessage** per leggere il primo messaggio nella coda. Il metodo **CloudQueue.GetMessage** rende invisibile il messaggio per 30 secondi (impostazione predefinita) per qualsiasi altro codice che legge i messaggi, in modo che nessun altro codice possa modificare o eliminare il messaggio mentre lo si sta elaborando. Per modificare l'intervallo di tempo per cui il messaggio rimane invisibile, modificare il parametro **visibilityTimeout** passato al metodo **CloudQueue.GetMessage**.

        // This message will be invisible to other code for 30 seconds.
        CloudQueueMessage message = queue.GetMessage();     

6. Chiamare il metodo **CloudQueueMessage.Delete** per eliminare il messaggio dalla coda.

        queue.DeleteMessage(message);

## <a name="get-the-queue-length"></a>Recuperare la lunghezza della coda

I passaggi seguenti illustrano come ottenere la lunghezza della coda (numero di messaggi) a livello di codice. In un'app MVC ASP.NET il codice verrà inserito in un controller. 

1. Aggiungere le direttive *using* seguenti:
   
        using Microsoft.Azure;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. Ottenere un oggetto **CloudStorageAccount** che rappresenta le informazioni sull'account di archiviazione. Utilizzare il codice seguente per ottenere la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure. Sostituire *<storage-account-name>* con il nome dell'account di archiviazione di Azure a cui si sta eseguendo l'accesso.

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Ottenere un oggetto **CloudQueueClient** che rappresenta un client del servizio di accodamento.

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. Ottenere un oggetto **CloudQueue** che rappresenta un riferimento alla coda. Sostituire *<queue-name>* con il nome della coda di cui si vuole recuperare la lunghezza.

        CloudQueue queue = queueClient.GetQueueReference(<queue-name>);

5. Chiamare il metodo **CloudQueue.FetchAttributes** per recuperare gli attributi della coda, che includono anche la lunghezza. 

        queue.FetchAttributes();

6. Accedere alla proprietà **CloudQueue.ApproximateMessageCount** per ottenere la lunghezza della coda.
 
        int? nMessages = queue.ApproximateMessageCount;

## <a name="delete-a-queue"></a>Eliminare una coda
I passaggi seguenti illustrano come eliminare una coda a livello di codice. 

1. Aggiungere le direttive *using* seguenti:
   
        using Microsoft.Azure;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. Ottenere un oggetto **CloudStorageAccount** che rappresenta le informazioni sull'account di archiviazione. Utilizzare il codice seguente per ottenere la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure. Sostituire *<storage-account-name>* con il nome dell'account di archiviazione di Azure a cui si sta eseguendo l'accesso.

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Ottenere un oggetto **CloudQueueClient** che rappresenta un client del servizio di accodamento.

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. Ottenere un oggetto **CloudQueue** che rappresenta un riferimento alla coda. Sostituire *<queue-name>* con il nome della coda di cui si vuole recuperare la lunghezza.

        CloudQueue queue = queueClient.GetQueueReference(<queue-name>);

5. Chiamare il metodo **CloudQueue.Delete** per eliminare la coda rappresentata dall'oggetto **CloudQueue**.

        messageQueue.Delete();

## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]




<!--HONumber=Dec16_HO2-->


