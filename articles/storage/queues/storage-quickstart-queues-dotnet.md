---
title: 'Guida introduttiva: Usare .NET per creare una coda in Archiviazione di Azure'
description: Questa guida introduttiva illustra come usare la libreria client di Archiviazione di Azure per .NET per creare una coda e aggiungervi messaggi. Si apprenderà quindi come leggere ed elaborare i messaggi dalla coda.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 02/06/2018
ms.author: tamram
ms.openlocfilehash: f16c4438dfb2feb70dece0b95f8afc701c5a3d66
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59009309"
---
# <a name="quickstart-use-net-to-create-a-queue-in-azure-storage"></a>Guida introduttiva: Usare .NET per creare una coda in Archiviazione di Azure

Questa guida introduttiva illustra come usare la libreria client di Archiviazione di Azure per .NET per creare una coda e aggiungervi messaggi. Si apprenderà quindi come leggere ed elaborare i messaggi dalla coda. 

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Successivamente, scaricare e installare .NET Core 2.0 per il sistema operativo in uso. Se si esegue Windows, è possibile installare Visual Studio e usare .NET Framework, se si preferisce. Si può anche scegliere di installare un editor da usare con il sistema operativo.

### <a name="windows"></a> Windows

- Installare [.NET Core per Windows](https://www.microsoft.com/net/download/windows) o [.NET Framework](https://www.microsoft.com/net/download/windows), incluso in Visual Studio per Windows
- Installare [Visual Studio per Windows](https://www.visualstudio.com/). Se si usa .NET Core, l'installazione di Visual Studio è facoltativa.  

Per informazioni sulla scelta tra .NET Core e .NET Framework, vedere [Scegliere tra .NET Core o .NET Framework per le app server](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server).

### <a name="linux"></a>Linux

- Installare [.NET Core per Linux](https://www.microsoft.com/net/download/linux)
- Facoltativamente, installare [Visual Studio Code](https://www.visualstudio.com/) e l'[estensione C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp&dotnetid=963890049.1518206068)

### <a name="macos"></a>macOS

- Installare [.NET Core per macOS](https://www.microsoft.com/net/download/macos)
- Facoltativamente, installare [Visual Studio per Mac](https://www.visualstudio.com/vs/visual-studio-mac/)

## <a name="download-the-sample-application"></a>Scaricare l'applicazione di esempio

L'applicazione di esempio usata in questa guida rapida è un'applicazione console di base. È possibile esplorare l'applicazione di esempio in [GitHub](https://github.com/Azure-Samples/storage-queues-dotnet-quickstart).

Usare [git](https://git-scm.com/) per scaricare una copia dell'applicazione nell'ambiente di sviluppo. 

```bash
git clone https://github.com/Azure-Samples/storage-queues-dotnet-quickstart.git
```

Questo comando consente di duplicare il repository nella cartella locale git. Per aprire la soluzione di Visual Studio, cercare la cartella *storage-queues-dotnet-quickstart*, aprirla e fare doppio clic su *storage-queues-dotnet-quickstart.sln*. 

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Configurare la stringa di connessione di archiviazione

Per eseguire l'applicazione, è necessario specificare la stringa di connessione per l'account di archiviazione. L'applicazione di esempio legge la stringa di connessione da una variabile di ambiente e la usa per l'autorizzazione delle richieste ad Archiviazione di Azure.

Dopo aver copiato la stringa di connessione, scriverla in una nuova variabile di ambiente nel computer locale che esegue l'applicazione. Per impostare la variabile di ambiente, aprire una finestra della console e seguire le istruzioni per il sistema operativo specifico. Sostituire `<yourconnectionstring>` con la stringa di connessione effettiva:

### <a name="windows"></a> Windows

```cmd
setx storageconnectionstring "<yourconnectionstring>"
```

Dopo l'aggiunta della variabile di ambiente potrebbe essere necessario riavviare eventuali programmi in esecuzione che necessitano di leggere la variabile di ambiente, inclusa la finestra della console. Se ad esempio si usa Visual Studio come editor, riavviare Visual Studio prima di eseguire l'esempio. 

### <a name="linux"></a>Linux

```bash
export storageconnectionstring=<yourconnectionstring>
```

Dopo avere aggiunto la variabile di ambiente, eseguire `source ~/.bashrc` dalla finestra della console per rendere effettive le modifiche.

### <a name="macos"></a>macOS

Modificare il file con estensione bash_profile e aggiungere la variabile di ambiente:

```bash
export STORAGE_CONNECTION_STRING=<yourconnectionstring>
```

Dopo avere aggiunto la variabile di ambiente, eseguire `source .bash_profile` dalla finestra della console per rendere effettive le modifiche.

## <a name="run-the-sample"></a>Eseguire l'esempio

L'applicazione di esempio crea una coda e vi aggiunge un messaggio. L'applicazione per prima cosa visualizza in anteprima il messaggio senza rimuoverlo dalla coda, quindi lo recupera e lo elimina dalla coda.

### <a name="windows"></a> Windows

Se si usa Visual Studio come editor, è possibile premere **F5** per l'esecuzione. 

In caso contrario, passare alla directory dell'applicazione e quindi eseguire l'applicazione con il comando `dotnet run`.

```
dotnet run
```

### <a name="linux"></a>Linux

Passare alla directory dell'applicazione e quindi eseguire l'applicazione con il comando `dotnet run`.

```
dotnet run
```

### <a name="macos"></a>macOS

Passare alla directory dell'applicazione e quindi eseguire l'applicazione con il comando `dotnet run`.

```
dotnet run
```

L'output dell'applicazione di esempio è simile all'esempio seguente:

```
Azure Queues - .NET Quickstart sample

Created queue 'quickstartqueues-3136fe9a-fa52-4b19-a447-8999a847da52'

Added message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7' to queue 'quickstartqueues-3136fe9a-fa52-4b19-a447-8999a847da52'
Message insertion time: 2/7/2019 4:30:46 AM +00:00
Message expiration time: 2/14/2019 4:30:46 AM +00:00

Contents of peeked message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7': Hello, World

Message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7' becomes visible again at 2/7/2019 4:31:16 AM +00:00

Processed and deleted message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7'

Press any key to delete the sample queue.
```

## <a name="understand-the-sample-code"></a>Informazioni sul codice di esempio

Esplorare quindi il codice di esempio per poterne comprendere il funzionamento.

### <a name="try-parsing-the-connection-string"></a>Provare ad analizzare la stringa di connessione

Per prima cosa, l'esempio controlla che la variabile di ambiente contenga una stringa di connessione analizzabile per creare un oggetto [CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount) che punti all'account di archiviazione. Per verificare la validità della stringa di connessione, l'esempio usa il metodo [TryParse](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.tryparse). Se **TryParse** ha esito positivo, inizializza la variabile *storageAccount* e restituisce **true**.

```csharp
// Retrieve the connection string for use with the application. The storage connection string is stored
// in an environment variable called storageconnectionstring, on the machine where the application is running.
// If the environment variable is created after the application is launched in a console or with Visual
// Studio, the shell needs to be closed and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("storageconnectionstring");

// Check whether the connection string can be parsed.
if (CloudStorageAccount.TryParse(storageConnectionString, out storageAccount))
{
    // If the connection string is valid, proceed with calls to Azure Queues here.
    ...    
}
else
{
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add an environment variable named 'storageconnectionstring' with your storage " +
        "connection string as a value.");
}
```

### <a name="create-the-queue"></a>Creare la coda

Per prima cosa, l'esempio crea una coda e vi aggiunge un messaggio. 

```csharp
// Create a queue called 'quickstartqueues' and append a GUID value so that the queue name 
// is unique in your storage account. 
queue = cloudQueueClient.GetQueueReference("quickstartqueues-" + Guid.NewGuid().ToString());
await queue.CreateAsync();

Console.WriteLine("Created queue '{0}'", queue.Name);
Console.WriteLine();
```

### <a name="add-a-message"></a>Aggiungere un messaggio

Successivamente, l'esempio aggiunge un messaggio nella parte posteriore della coda. 

Il messaggio deve essere in un formato che possa essere incluso in una richiesta XML con codifica UTF-8 e può avere una dimensione massima di 64 KB. Se un messaggio contiene dati binari, è consigliabile usare la codifica Base64.

Per impostazione predefinita, la durata massima (TTL) di un messaggio è impostata su 7 giorni. Come durata del messaggio è possibile specificare qualsiasi numero positivo, nonché -1 per indicare che il messaggio non ha scadenza.

```csharp
// Create a message and add it to the queue. Set expiration time to 14 days.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await queue.AddMessageAsync(message, new TimeSpan(14,0,0,0), null, null, null);
Console.WriteLine("Added message '{0}' to queue '{1}'", message.Id, queue.Name);
Console.WriteLine("Message insertion time: {0}", message.InsertionTime.ToString());
Console.WriteLine("Message expiration time: {0}", message.ExpirationTime.ToString());
Console.WriteLine();
```

### <a name="peek-a-message-from-the-queue"></a>Visualizzare in anteprima un messaggio dalla coda

L'esempio illustra come visualizzare in anteprima un messaggio da una coda. Quando si visualizza in anteprima un messaggio, è possibile leggerne il contenuto. Il messaggio resta tuttavia visibile agli altri client, da cui potrà quindi essere successivamente recuperato ed elaborato.

```csharp
// Peek at the message at the front of the queue. Peeking does not alter the message's 
// visibility, so that another client can still retrieve and process it. 
CloudQueueMessage peekedMessage = await queue.PeekMessageAsync();

// Display the ID and contents of the peeked message.
Console.WriteLine("Contents of peeked message '{0}': {1}", peekedMessage.Id, peekedMessage.AsString);
Console.WriteLine();
```

### <a name="dequeue-a-message"></a>Rimuovere un messaggio dalla coda

L'esempio illustra anche come rimuovere un messaggio dalla coda. Quando si rimuove un messaggio dalla coda, si recupera il messaggio dalla parte anteriore della coda rendendolo temporaneamente invisibile agli altri client. Per impostazione predefinita, un messaggio rimane invisibile per 30 secondi. Durante questo periodo di tempo, il codice può elaborare il messaggio. Per completare la rimozione del messaggio dalla coda, si elimina il messaggio subito dopo l'elaborazione evitando così che venga rimosso dalla coda da un altro client.

Se il codice non riesce a elaborare un messaggio a causa di un errore hardware o software, il messaggio torna visibile al termine del periodo di invisibilità. Un altro client potrà recuperare lo stesso messaggio e riprovare.

```csharp
// Retrieve the message at the front of the queue. The message becomes invisible for 
// a specified interval, during which the client attempts to process it.
CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();

// Display the time at which the message will become visible again if it is not deleted.
Console.WriteLine("Message '{0}' becomes visible again at {1}", retrievedMessage.Id, retrievedMessage.NextVisibleTime);
Console.WriteLine();

//Process and delete the message within the period of invisibility.
await queue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Processed and deleted message '{0}'", retrievedMessage.Id);
Console.WriteLine();
```

### <a name="clean-up-resources"></a>Pulire le risorse

L'esempio esegue la pulizia delle risorse create eliminando la coda. L'eliminazione della coda elimina anche gli eventuali messaggi in essa contenuti.

```csharp
Console.WriteLine("Press any key to delete the sample queue.");
Console.ReadLine();
Console.WriteLine("Deleting the queue and any messages it contains...");
Console.WriteLine();
if (queue != null)
{
    await queue.DeleteIfExistsAsync();
}
```

## <a name="resources-for-developing-net-applications-with-queues"></a>Risorse per lo sviluppo di applicazioni .NET con code

Per lo sviluppo .NET con code di Azure, vedere le risorse aggiuntive seguenti:

### <a name="binaries-and-source-code"></a>File binari e codice sorgente

- Scaricare il pacchetto NuGet per la versione più recente della [libreria client .NET](https://www.nuget.org/packages/WindowsAzure.Storage/) per Archiviazione di Azure. 
- Visualizzare il [codice sorgente della libreria client .NET](https://github.com/Azure/azure-storage-net) in GitHub.

### <a name="client-library-reference-and-samples"></a>Informazioni di riferimento ed esempi relativi alla libreria client

- Per altre informazioni sulla libreria client .NET, vedere le [informazioni di riferimento sulle API .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage).
- Esplorare gli [esempi per Archiviazione code](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=queues) scritti con la libreria client .NET.

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato illustrato come aggiungere messaggi a una coda, visualizzarli in anteprima e rimuoverli dalla coda ed elaborarli con .NET. 

> [!div class="nextstepaction"]
> [Comunicazione tra applicazioni con Archiviazione code di Azure](https://docs.microsoft.com/learn/modules/communicate-between-apps-with-azure-queue-storage/index)

- Per altre informazioni su .NET Core, vedere [Get started with .NET in 10 minutes](https://www.microsoft.com/net/learn/get-started/) (Introduzione a .NET in 10 minuti).
