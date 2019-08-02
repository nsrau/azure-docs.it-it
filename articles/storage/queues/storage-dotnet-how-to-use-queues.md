---
title: Introduzione all'archiviazione code di Azure con .NET-archiviazione di Azure
description: Le code di Azure forniscono una messaggistica asincrona affidabile tra i componenti dell'applicazione. La messaggistica cloud consente di ridimensionare i componenti dell'applicazione in modo indipendente.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/21/2019
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: aa92b72b09ed28b41d85ac7c7605077761657d40
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68721569"
---
# <a name="get-started-with-azure-queue-storage-using-net"></a>Introduzione all'archiviazione code di Azure con .NET

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="overview"></a>Panoramica

L'archivio code di Azure fornisce la messaggistica cloud tra i componenti dell'applicazione. Durante la progettazione di applicazioni scalabili, i componenti dell'applicazione vengono spesso separati, per poter essere scalati in modo indipendente. L'archivio code fornisce la messaggistica asincrona per la comunicazione tra i componenti dell'applicazione, che possono essere eseguiti nel cloud, in un desktop, in un server locale o in un dispositivo mobile. Archiviazione code supporta anche la gestione di attività asincrone e la creazione di flussi di lavoro dei processi.

### <a name="about-this-tutorial"></a>Informazioni sull'esercitazione

Questa esercitazione illustra come scrivere codice .NET per alcuni scenari comuni dell'archiviazione code di Azure. Gli scenari presentati includono creazione ed eliminazione di code, nonché aggiunta, lettura ed eliminazione di messaggi nella coda.

**Tempo previsto per il completamento**: 45 minuti

### <a name="prerequisites"></a>Prerequisiti

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Libreria client comune di archiviazione di Azure per .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)
* [Libreria client della coda di archiviazione di Azure per .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/)
* [Gestione configurazione di Azure per .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)
* Un [account di archiviazione di Azure](../common/storage-quickstart-create-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="set-up-your-development-environment"></a>Configurazione dell'ambiente di sviluppo

Configurare quindi l'ambiente di sviluppo in Visual Studio per poter provare gli esempi di codice di questa guida.

### <a name="create-a-windows-console-application-project"></a>Creare un progetto di applicazione console di Windows

In Visual Studio creare una nuova applicazione console di Windows. La procedura seguente illustra come creare un'applicazione console in Visual Studio 2019. La procedura è simile per le altre versioni di Visual Studio.

1. Selezionare **File** > **Nuovo** > **Progetto**
2. Selezionare le**finestre** della **piattaforma** > 
3. Selezionare **App console (.NET Framework)**
4. Selezionare **Avanti**
5. Nel campo **nome progetto** , immettere un nome per l'applicazione
6. Selezionare **Crea**

Tutti gli esempi di codice in questa esercitazione possono essere aggiunti al metodo **Main ()** del file **Program.cs** dell'applicazione console.

È possibile usare le librerie client di archiviazione di Azure in qualsiasi tipo di applicazione .NET, tra cui un servizio cloud o un'app Web di Azure, nonché applicazioni desktop e per dispositivi mobili. Per semplicità, in questa guida si usa un'applicazione console.

### <a name="use-nuget-to-install-the-required-packages"></a>Usare NuGet per installare i pacchetti necessari

Per completare questa esercitazione, è necessario fare riferimento ai tre pacchetti seguenti nel progetto:

* [Archiviazione di Microsoft Azure libreria client comune per .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/): questo pacchetto fornisce l'accesso a livello di codice alle risorse dati nell'account di archiviazione.
* [Archiviazione di Microsoft Azure libreria di Accodamento per .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/): Questa libreria client consente di utilizzare il Archiviazione di Microsoft Azure Servizio di accodamento per archiviare i messaggi a cui è possibile accedere da un client.
* [Libreria Gestione configurazione di Microsoft Azure per .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/): questo pacchetto fornisce una classe per l'analisi di una stringa di connessione in un file di configurazione, indipendentemente dalla posizione in cui viene eseguita l'applicazione.

È possibile usare NuGet per ottenere questi pacchetti. Attenersi ai passaggi riportati di seguito.

1. Fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Gestisci pacchetti NuGet**.
2. Selezionare **Esplora**
3. Cercare online "Microsoft. Azure. storage. Queue" e selezionare **Installa** per installare la libreria client di archiviazione e le relative dipendenze. Verrà installata anche la libreria Microsoft. Azure. storage. Common, che è una dipendenza della libreria di Accodamento.
4. Cercare online "Microsoft. Azure. ConfigurationManager" e selezionare **Installa** per installare il Configuration Manager di Azure.

> [!NOTE]
> I pacchetti delle librerie client di archiviazione sono inclusi anche in [Azure SDK per .NET](https://azure.microsoft.com/downloads/). Tuttavia, è consigliabile installare anche le librerie client di archiviazione da NuGet per assicurarsi di avere sempre le versioni più recenti.
>
> Le dipendenze ODataLib nelle librerie client di archiviazione per .NET vengono risolte dai pacchetti ODataLib disponibili in NuGet, non da WCF Data Services. È possibile scaricare le librerie ODataLib direttamente oppure farvi riferimento nel progetto del codice tramite NuGet. I pacchetti ODataLib specifici usati dalle librerie client di archiviazione sono [OData](https://nuget.org/packages/Microsoft.Data.OData/), [EDM](https://nuget.org/packages/Microsoft.Data.Edm/)e [Spatial](https://nuget.org/packages/System.Spatial/). Sebbene queste librerie vengano usate dalle classi di archiviazione tabelle di Azure, sono dipendenze necessarie per la programmazione con le librerie client di archiviazione.

### <a name="determine-your-target-environment"></a>Determinare l'ambiente di destinazione

Sono disponibili due opzioni relative all'ambiente per l'esecuzione degli esempi di questa guida:

* È possibile eseguire il codice con un account di archiviazione di Azure nel cloud.
* È possibile eseguire il codice nell'emulatore di archiviazione di Azure. L'emulatore di archiviazione è un ambiente locale che emula un account di archiviazione di Azure nel cloud. L'emulatore è un'opzione gratuita per il test e il debug del codice durante lo sviluppo dell'applicazione. L'emulatore usa un account e una chiave noti. Per altre informazioni, vedere [Usare l'emulatore di archiviazione di Azure per sviluppo e test](../common/storage-use-emulator.md).

Se si specifica come destinazione un account di archiviazione nel cloud, immettere la chiave di accesso primaria per tale account tramite il portale di Azure. Per altre informazioni, vedere [Chiavi di accesso](../common/storage-account-manage.md#access-keys).

> [!NOTE]
> È possibile impostare come destinazione l'emulatore di archiviazione per evitare di incorrere negli eventuali costi associati al servizio Archiviazione di Azure. Se però si sceglie di impostare come destinazione un account di archiviazione di Azure nel cloud, i costi per eseguire questa esercitazione saranno minimi.

### <a name="configure-your-storage-connection-string"></a>Configurare la stringa di connessione di archiviazione

Le librerie client di archiviazione di Azure per .NET supportano l'uso di una stringa di connessione di archiviazione per configurare gli endpoint e le credenziali per l'accesso ai servizi di archiviazione. Per gestire nel modo migliore la stringa di connessione di archiviazione, usare un file di configurazione.

Per altre informazioni sulle stringhe di connessione, vedere [Configurare le stringhe di connessione di archiviazione di Azure](../common/storage-configure-connection-string.md).

> [!NOTE]
> La chiave dell’account di archiviazione è simile alla password radice per l'account di archiviazione. È consigliabile proteggere sempre la chiave dell'account di archiviazione. Evitare di distribuirla ad altri utenti, impostarla come hardcoded o salvarla in un file di testo normale accessibile ad altri. Rigenerare la chiave tramite il portale di Azure se si ritiene che possa essere stata compromessa.

Per configurare la stringa di connessione, aprire il file **app. config** da Esplora soluzioni in Visual Studio. Aggiungere il contenuto dell'elemento **\<appSettings\>** illustrato di seguito. Sostituire *account-name* con il nome dell'account di archiviazione e la *chiave di account* con la chiave di accesso dell'account:

```xml
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
    </appSettings>
</configuration>
```

Ad esempio, l'impostazione di configurazione si presenta simile a:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==" />
```

Per impostare come destinazione l'emulatore di archiviazione, è possibile usare un collegamento con mapping al nome dell'account e alla chiave noti. In questo caso, l'impostazione della stringa di connessione è:

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true;" />
```

### <a name="add-using-directives"></a>Aggiungere le direttive using

Aggiungere le direttive `using` seguenti all'inizio del file `Program.cs`:

```csharp
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage; // Namespace for CloudStorageAccount
using Microsoft.Azure.Storage.Queue; // Namespace for Queue storage types
```

### <a name="copy-your-credentials-from-the-azure-portal"></a>Copiare le credenziali dal portale di Azure

Il codice di esempio deve autorizzare l'accesso all'account di archiviazione. Per eseguire l'autorizzazione, si passano all'applicazione le credenziali dell'account di archiviazione sotto forma di stringa di connessione. Per visualizzare le credenziali dell'account di archiviazione:

1. Passare al [portale di Azure](https://portal.azure.com).
2. Individuare l'account di archiviazione.
3. Nella sezione **Impostazioni** della panoramica dell'account di archiviazione selezionare **Chiavi di accesso**. Verranno visualizzate le chiavi di accesso dell'account, con la stringa di connessione completa per ogni chiave.
4. Trovare il valore **Stringa di connessione** in **key1** e fare clic sul pulsante **Copia** per copiare la stringa di connessione. Il valore della stringa di connessione verrà aggiunto a una variabile di ambiente nel passaggio successivo.

    ![Screenshot che mostra come copiare una stringa di connessione dal portale di Azure](media/storage-dotnet-how-to-use-queues/portal-connection-string.png)

### <a name="parse-the-connection-string"></a>Analizzare la stringa di connessione

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-queue-service-client"></a>Creare il client del servizio di accodamento

La classe [CloudQueueClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueueclient?view=azure-dotnet) consente di recuperare le code archiviate nell'archivio code. Ecco come creare il client del servizio:

```csharp
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
```

A questo punto si è pronti a scrivere codice che legge e scrive i dati nell'archivio code.

## <a name="create-a-queue"></a>Crea una coda

Questo esempio illustra come creare una coda, se non esiste già:

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a container.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();
```

## <a name="insert-a-message-into-a-queue"></a>Inserire un messaggio in una coda

Per inserire un messaggio in una coda esistente, creare innanzitutto un nuovo oggetto [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage?view=azure-dotnet). Quindi, chiamare il metodo [AddMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessage?view=azure-dotnet) . È possibile creare un oggetto **CloudQueueMessage** da una stringa in formato UTF-8 o da una matrice di **byte**. Di seguito è riportato il codice che consente di creare una coda (se non esiste già) e di inserire il messaggio 'Hello, World':

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist.
queue.CreateIfNotExists();

// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
queue.AddMessage(message);
```

## <a name="peek-at-the-next-message"></a>Visualizzare il messaggio successivo

È possibile visualizzare il messaggio successivo di una coda senza rimuoverlo dalla coda chiamando il metodo [PeekMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.peekmessage?view=azure-dotnet) .

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Peek at the next message
CloudQueueMessage peekedMessage = queue.PeekMessage();

// Display message.
Console.WriteLine(peekedMessage.AsString);
```

## <a name="change-the-contents-of-a-queued-message"></a>Cambiare il contenuto di un messaggio in coda

È possibile cambiare il contenuto di un messaggio inserito nella coda. Se il messaggio rappresenta un'attività di lavoro, è possibile utilizzare questa funzionalità per aggiornarne lo stato. Il codice seguente consente di aggiornare il messaggio in coda con nuovo contenuto e di impostarne il timeout di visibilità per prolungarlo di altri 60 secondi. In questo modo lo stato del lavoro associato al messaggio viene salvato e il client ha a disposizione un altro minuto per continuare l'elaborazione del messaggio. È possibile usare questa tecnica per tenere traccia di flussi di lavoro composti da più passaggi nei messaggi in coda, senza la necessità di ricominciare dall'inizio se un passaggio di elaborazione non riesce a causa di errori hardware o software. In genere, è consigliabile mantenere anche un conteggio dei tentativi, in modo da eliminare i messaggi per cui vengono effettuati più di *n* tentativi. In questo modo è possibile evitare che un messaggio attivi un errore dell'applicazione ogni volta che viene elaborato.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the message from the queue and update the message contents.
CloudQueueMessage message = queue.GetMessage();
message.SetMessageContent2("Updated contents.", false);
queue.UpdateMessage(message,
    TimeSpan.FromSeconds(60.0),  // Make it invisible for another 60 seconds.
    MessageUpdateFields.Content | MessageUpdateFields.Visibility);
```

## <a name="de-queue-the-next-message"></a>Rimuovere il messaggio successivo dalla coda

Il codice consente di rimuovere un messaggio da una coda in due passaggi. Chiamando [GetMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage?view=azure-dotnet), si ottiene il messaggio successivo in una coda. Un messaggio restituito da **GetMessage** diventa invisibile a qualsiasi altro codice che legge i messaggi dalla stessa coda. Per impostazione predefinita, il messaggio rimane invisibile per 30 secondi. Per completare la rimozione del messaggio dalla coda, è necessario chiamare anche [DeleteMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage?view=azure-dotnet). Questo processo in due passaggi di rimozione di un messaggio assicura che, qualora l'elaborazione di un messaggio non riesca a causa di errori hardware o software, un'altra istanza del codice sia in grado di ottenere lo stesso messaggio e di riprovare. Il codice chiama **DeleteMessage** immediatamente dopo l'elaborazione del messaggio.

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the next message
CloudQueueMessage retrievedMessage = queue.GetMessage();

//Process the message in less than 30 seconds, and then delete the message
queue.DeleteMessage(retrievedMessage);
```

## <a name="use-async-await-pattern-with-common-queue-storage-apis"></a>Utilizzare il modello Async-Await modello con API comuni di archiviazione coda

In questo esempio viene illustrato come utilizzare il modello Async-Await con API comuni di archiviazione coda. Nell'esempio viene chiamata la versione asincrona di ogni metodo specificato, come indicato dal suffisso *Async* di ciascun metodo. Quando un metodo asincrono viene utilizzato, il modello async-await sospende l'esecuzione locale fino al completamento della chiamata. Questo comportamento consente al thread corrente di eseguire altre attività per evitare colli di bottiglia delle prestazioni e migliora la velocità di risposta complessiva dell'applicazione. Per ulteriori informazioni sull'utilizzo del modello Async-Await in .NET, vedere [Async e Await (C# e Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx)

```csharp
// Create the queue if it doesn't already exist
if(await queue.CreateIfNotExistsAsync())
{
    Console.WriteLine("Queue '{0}' Created", queue.Name);
}
else
{
    Console.WriteLine("Queue '{0}' Exists", queue.Name);
}

// Create a message to put in the queue
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Async enqueue the message
await queue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message
CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Async delete the message
await queue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

## <a name="leverage-additional-options-for-de-queuing-messages"></a>Usufruire di opzioni aggiuntive per rimuovere i messaggi dalla coda

È possibile personalizzare il recupero di messaggi da una coda in due modi. Innanzitutto, è possibile recuperare un batch di messaggi (massimo 32). In secondo luogo, è possibile impostare un timeout di invisibilità più lungo o più breve assegnando al codice più o meno tempo per l'elaborazione completa di ogni messaggio. Nell'esempio di codice seguente viene utilizzato il metodo [GetMessages](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessages?view=azure-dotnet) per recuperare 20 messaggi con una sola chiamata. Quindi, ogni messaggio viene elaborato con un ciclo **foreach** . Per ogni messaggio, inoltre, il timeout di invisibilità viene impostato su cinque minuti. Si noti che i cinque minuti iniziano per tutti i messaggi contemporaneamente, quindi dopo che sono trascorsi cinque minuti dalla chiamata a **GetMessages**, tutti i messaggi che non sono stati eliminati diventano nuovamente visibili.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.
    queue.DeleteMessage(message);
}
```

## <a name="get-the-queue-length"></a>Recuperare la lunghezza della coda

È possibile ottenere una stima sul numero di messaggi presenti in una coda. Il metodo [FetchAttributes](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.fetchattributes?view=azure-dotnet) chiede al servizio di accodamento di recuperare gli attributi della coda, incluso il numero di messaggi. La proprietà [ApproximateMessageCount](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.approximatemessagecount?view=azure-dotnet) restituisce l'ultimo valore recuperato dal metodo **FetchAttributes**, senza chiamare il servizio di accodamento.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Fetch the queue attributes.
queue.FetchAttributes();

// Retrieve the cached approximate message count.
int? cachedMessageCount = queue.ApproximateMessageCount;

// Display number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="delete-a-queue"></a>Eliminare una coda

Per eliminare una coda e tutti i messaggi che contiene, chiamare il metodo [Elimina](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.delete?view=azure-dotnet) sull'oggetto coda.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Delete the queue.
queue.Delete();
```

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base dell'archiviazione di accodamento, visitare i collegamenti seguenti per altre informazioni sulle attività di archiviazione più complesse.

* Per informazioni dettagliate sulle API disponibili, vedere la documentazione di riferimento del servizio di accodamento:
  * [Informazioni di riferimento sulla libreria client di archiviazione per .NET](https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
  * [Informazioni di riferimento sulle API REST](https://msdn.microsoft.com/library/azure/dd179355)
* Per altre informazioni su come semplificare il codice scritto da usare con Archiviazione di Azure, vedere [Informazioni su Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).
* Per ulteriori opzioni di archiviazione dei dati in Azure, consultare altre guide alle funzionalità.
  * [Introduzione all'archiviazione tabelle di Azure con .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md) .
  * [Introduzione all'archivio BLOB di Azure con .NET](../blobs/storage-dotnet-how-to-use-blobs.md) .
  * Per archiviare i dati relazionali, vedere [Connettersi al database SQL tramite .NET (C#)](../../sql-database/sql-database-connect-query-dotnet-core.md).

[Download and install the Azure SDK for .NET]: /develop/net/
[.NET client library reference]: https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
[Creating an Azure Project in Visual Studio]: https://msdn.microsoft.com/library/azure/ee405487.aspx
[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[OData]: https://nuget.org/packages/Microsoft.Data.OData/5.0.2
[Edm]: https://nuget.org/packages/Microsoft.Data.Edm/5.0.2
[Spatial]: https://nuget.org/packages/System.Spatial/5.0.2
