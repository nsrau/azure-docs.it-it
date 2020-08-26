---
title: Come usare l'archiviazione di Accodamento da Java-archiviazione di Azure
description: Informazioni su come usare l'archiviazione di Accodamento per creare ed eliminare code. Informazioni su come inserire, visualizzare, ottenere ed eliminare messaggi con la libreria client di archiviazione di Azure per Java.
author: mhopkins-msft
ms.custom: devx-track-java
ms.author: mhopkins
ms.date: 08/19/2020
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.openlocfilehash: 9c2f8ea55f99ac893dca0fa380729b7cc866812e
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88871285"
---
# <a name="how-to-use-queue-storage-from-java"></a>Come usare l'archiviazione di accodamento da Java

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Panoramica

Questa guida illustra come scrivere codice per scenari comuni usando il servizio di archiviazione di Accodamento di Azure. Gli esempi sono scritti in Java e usano [Azure Storage SDK per Java][Azure Storage SDK for Java]. Gli scenari includono **inserimento**, **visualizzazione**, **recupero**ed **eliminazione** dei messaggi in coda. Viene inoltre analizzato il codice per la **creazione** e l' **eliminazione** di code. Per altre informazioni sulle code, vedere la sezione [Passaggi successivi](#next-steps) .

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Creare un'applicazione Java

# <a name="java-v12"></a>[Java V12](#tab/java)

Prima di tutto, verificare che il sistema di sviluppo soddisfi i prerequisiti elencati nella [libreria client di archiviazione code di Azure per Java V12](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue).

Per creare un'applicazione Java denominata *Queues-How-to-V12*:

1. In una finestra della console (ad esempio, cmd, PowerShell o bash) usare Maven per creare una nuova app console con il nome *Queues-How-to-V12*. Digitare il comando **mvn** seguente per creare un progetto Java "Hello world!" .

   ```bash
    mvn archetype:generate \
        --define interactiveMode=n \
        --define groupId=com.queues.howto \
        --define artifactId=queues-howto-v12 \
        --define archetypeArtifactId=maven-archetype-quickstart \
        --define archetypeVersion=1.4
   ```

   ```powershell
    mvn archetype:generate `
        --define interactiveMode=n `
        --define groupId=com.queues.howto `
        --define artifactId=queues-howto-v12 `
        --define archetypeArtifactId=maven-archetype-quickstart `
        --define archetypeVersion=1.4
    ```

1. L'output della generazione del progetto avrà un aspetto simile al seguente:

    ```console
    [INFO] Scanning for projects...
    [INFO]
    [INFO] ------------------< org.apache.maven:standalone-pom >-------------------
    [INFO] Building Maven Stub Project (No POM) 1
    [INFO] --------------------------------[ pom ]---------------------------------
    [INFO]
    [INFO] >>> maven-archetype-plugin:3.1.2:generate (default-cli) > generate-sources @ standalone-pom >>>
    [INFO]
    [INFO] <<< maven-archetype-plugin:3.1.2:generate (default-cli) < generate-sources @ standalone-pom <<<
    [INFO]
    [INFO]
    [INFO] --- maven-archetype-plugin:3.1.2:generate (default-cli) @ standalone-pom ---
    [INFO] Generating project in Batch mode
    [INFO] ----------------------------------------------------------------------------
    [INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
    [INFO] ----------------------------------------------------------------------------
    [INFO] Parameter: groupId, Value: com.queues.howto
    [INFO] Parameter: artifactId, Value: queues-howto-v12
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.howto
    [INFO] Parameter: packageInPathFormat, Value: com/queues/howto
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.howto
    [INFO] Parameter: groupId, Value: com.queues.howto
    [INFO] Parameter: artifactId, Value: queues-howto-v12
    [INFO] Project created from Archetype in dir: C:\queues\queues-howto-v12
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time:  6.775 s
    [INFO] Finished at: 2020-08-17T15:27:31-07:00
    [INFO] ------------------------------------------------------------------------
        ```

1. Switch to the newly created *queues-howto-v12* directory.

   ```console
   cd queues-howto-v12
   ```

### <a name="install-the-package"></a>Installare il pacchetto

Aprire il file *pom.xml* nell'editor di testo. Aggiungere l'elemento di dipendenza seguente al gruppo di dipendenze.

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-storage-queue</artifactId>
  <version>12.6.0</version>
</dependency>
```

# <a name="java-v8"></a>[Java V8](#tab/java8)

Prima di tutto, verificare che il sistema di sviluppo soddisfi i prerequisiti elencati in [Azure Storage SDK per Java V8](https://github.com/azure/azure-storage-java). Seguire le istruzioni per scaricare e installare le librerie di archiviazione di Azure per Java. È quindi possibile creare un'applicazione Java usando gli esempi in questo articolo.

---

## <a name="configure-your-application-to-access-queue-storage"></a>Configurazione dell'applicazione per l'accesso all'archiviazione di accodamento

Aggiungere le istruzioni import seguenti all'inizio del file Java in cui si desidera usare le API di archiviazione di Azure per accedere alle code:

# <a name="java-v12"></a>[Java V12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ImportStatements":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

```java
// Include the following imports to use queue APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.queue.*;
```

---

## <a name="set-up-an-azure-storage-connection-string"></a>Impostare una stringa di connessione di archiviazione di Azure

Un client di archiviazione di Azure usa una stringa di connessione di archiviazione per accedere ai servizi di gestione dati. Ottenere il nome e la chiave di accesso primaria per l'account di archiviazione elencato nell' [portale di Azure](https://portal.azure.com). Utilizzarli come valori *AccountName* e *AccountKey* nella stringa di connessione. In questo esempio viene illustrato come dichiarare un campo statico per memorizzare la stringa di connessione:

# <a name="java-v12"></a>[Java V12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ConnectionString":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

```java
// Define the connection-string with your values.
final String storageConnectionString =
    "DefaultEndpointsProtocol=https;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```
Questa stringa può essere archiviata nel file di configurazione del servizio denominato *ServiceConfiguration. cscfg*. Per un'app in esecuzione all'interno di un ruolo Microsoft Azure, accedere alla stringa di connessione chiamando **RoleEnvironment. getConfigurationSettings**. Di seguito è riportato un esempio di recupero della stringa di connessione da un elemento **Setting** denominato *StorageConnectionString*:

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

---

Gli esempi seguenti presuppongono che sia presente un oggetto **stringa** contenente la stringa di connessione di archiviazione.

## <a name="how-to-create-a-queue"></a>Procedura: creare una coda

# <a name="java-v12"></a>[Java V12](#tab/java)

Un oggetto **QueueClient** contiene le operazioni per l'interazione con una coda. Il codice seguente crea un oggetto **QueueClient** . Utilizzare l'oggetto **QueueClient** per creare la coda che si desidera utilizzare.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_CreateQueue":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

Un oggetto **CloudQueueClient** consente di ottenere oggetti di riferimento per le code. Il codice seguente consente di creare un oggetto **CloudQueueClient**. (Nota: esistono altri modi per creare oggetti **CloudStorageAccount**. Per altre informazioni, vedere **CloudStorageAccount** nel [Riferimento all'SDK del client di archiviazione di Azure]).

Usare l'oggetto **CloudQueueClient** t per ottenere un riferimento alla coda da usare. È possibile creare la coda se non esiste già.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

   // Create the queue client.
   CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

   // Retrieve a reference to a queue.
   CloudQueue queue = queueClient.getQueueReference("myqueue");

   // Create the queue if it doesn't already exist.
   queue.createIfNotExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-add-a-message-to-a-queue"></a>Procedura: aggiungere un messaggio a una coda

# <a name="java-v12"></a>[Java V12](#tab/java)

Per inserire un messaggio in una coda esistente, chiamare il metodo **SendMessage** . Un messaggio può essere una stringa (in formato UTF-8) o una matrice di byte. Ecco il codice che invia un messaggio stringa alla coda.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_AddMessage":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

Per inserire un messaggio in una coda esistente, creare innanzitutto un nuovo oggetto **CloudQueueMessage**. Chiamare quindi il metodo **AddMessage** . È possibile creare un **CloudQueueMessage** da una stringa (in formato UTF-8) o da una matrice di byte. Ecco il codice che crea una coda (se non esiste) e inserisce il messaggio "Hello, World".

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.createIfNotExists();

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.addMessage(message);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-peek-at-the-next-message"></a>Procedura: visualizzare il messaggio successivo

È possibile visualizzare il messaggio successivo di una coda senza rimuoverlo dalla coda chiamando il metodo **peekMessage**.

# <a name="java-v12"></a>[Java V12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_PeekMessage":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Peek at the next message.
    CloudQueueMessage peekedMessage = queue.peekMessage();

    // Output the message value.
    if (peekedMessage != null)
    {
      System.out.println(peekedMessage.getMessageContentAsString());
   }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Procedura: cambiare il contenuto di un messaggio accodato

È possibile cambiare il contenuto di un messaggio inserito nella coda. Se il messaggio rappresenta un'attività di lavoro, è possibile usare questa funzionalità per aggiornare lo stato. Il codice seguente aggiorna un messaggio della coda con nuovo contenuto e imposta il timeout di visibilità per estendere altri 30 secondi. L'estensione del timeout di visibilità fornisce al client altri 30 secondi per continuare a lavorare sul messaggio. È anche possibile che venga mantenuto un numero di tentativi. Se il messaggio viene ritentato più di *n* volte, è necessario eliminarlo. Questo scenario protegge da un messaggio che attiva un errore dell'applicazione ogni volta che viene elaborato.

# <a name="java-v12"></a>[Java V12](#tab/java)

Nell'esempio di codice seguente viene eseguita la ricerca nella coda dei messaggi, viene individuato il primo contenuto del messaggio corrispondente a una stringa di ricerca, viene modificato il contenuto del messaggio e viene chiuso.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_UpdateSearchMessage":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

Nell'esempio di codice seguente viene eseguita la ricerca nella coda dei messaggi, viene individuato il primo contenuto del messaggio corrispondente a "Hello, World", viene modificato il contenuto del messaggio e viene chiuso.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // The maximum number of messages that can be retrieved is 32.
    final int MAX_NUMBER_OF_MESSAGES_TO_PEEK = 32;

    // Loop through the messages in the queue.
    for (CloudQueueMessage message : queue.retrieveMessages(MAX_NUMBER_OF_MESSAGES_TO_PEEK,1,null,null))
    {
        // Check for a specific string.
        if (message.getMessageContentAsString().equals("Hello, World"))
        {
            // Modify the content of the first matching message.
            message.setMessageContent("Updated contents.");
            // Set it to be visible in 30 seconds.
            EnumSet<MessageUpdateFields> updateFields =
                EnumSet.of(MessageUpdateFields.CONTENT,
                MessageUpdateFields.VISIBILITY);
            // Update the message.
            queue.updateMessage(message, 30, updateFields, null, null);
            break;
        }
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

Nell'esempio di codice seguente viene aggiornato solo il primo messaggio visibile nella coda.

# <a name="java-v12"></a>[Java V12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_UpdateFirstMessage":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue.
    CloudQueueMessage message = queue.retrieveMessage();

    if (message != null)
    {
        // Modify the message content.
        message.setMessageContent("Updated contents.");
        // Set it to be visible in 60 seconds.
        EnumSet<MessageUpdateFields> updateFields =
            EnumSet.of(MessageUpdateFields.CONTENT,
            MessageUpdateFields.VISIBILITY);
        // Update the message.
        queue.updateMessage(message, 60, updateFields, null, null);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-get-the-queue-length"></a>Procedura: recuperare la lunghezza delle code

È possibile ottenere una stima sul numero di messaggi presenti in una coda.

# <a name="java-v12"></a>[Java V12](#tab/java)

Il metodo **GetProperties** richiede l'servizio di Accodamento per diversi valori correnti. Uno dei valori è un conteggio del numero di messaggi presenti in una coda. Il conteggio è solo approssimativo perché i messaggi possono essere aggiunti o rimossi dopo la richiesta. Il metodo **getApproximateMessageCount** restituisce l'ultimo valore recuperato dalla chiamata a **GetProperties**, senza chiamare il servizio di Accodamento.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_GetQueueLength":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

Il metodo **downloadAttributes** richiede l'servizio di Accodamento per diversi valori correnti. Uno dei valori è un conteggio del numero di messaggi presenti in una coda. Il conteggio è solo approssimativo perché i messaggi possono essere aggiunti o rimossi dopo la richiesta. Il metodo **getApproximateMessageCount** restituisce l'ultimo valore recuperato dalla chiamata a **downloadAttributes**, senza chiamare il servizio di accodamento.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

   // Download the approximate message count from the server.
    queue.downloadAttributes();

    // Retrieve the newly cached approximate message count.
    long cachedMessageCount = queue.getApproximateMessageCount();

    // Display the queue length.
    System.out.println(String.format("Queue length: %d", cachedMessageCount));
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-dequeue-the-next-message"></a>Procedura: rimuovere il messaggio successivo dalla coda

# <a name="java-v12"></a>[Java V12](#tab/java)

Il codice consente di rimuovere un messaggio da una coda in due passaggi. Quando si chiama **receiveMessage**, si ottiene il messaggio successivo in una coda. Un messaggio restituito da **receiveMessage** diventa invisibile a qualsiasi altro codice che legge i messaggi da questa coda. Per impostazione predefinita, il messaggio rimane invisibile per 30 secondi. Per completare la rimozione del messaggio dalla coda, è necessario chiamare anche **deleteMessage**. Se il codice non riesce a elaborare un messaggio, questo processo in due passaggi garantisce che sia possibile ottenere lo stesso messaggio e riprovare. Il codice chiama **deleteMessage** subito dopo l'elaborazione del messaggio.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DequeueMessage":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

Il codice consente di rimuovere un messaggio da una coda in due passaggi. Chiamando il metodo **retrieveMessage**, si ottiene il messaggio successivo in una coda. Un messaggio restituito da **retrieveMessage** diventa invisibile a qualsiasi altro codice che legge i messaggi dalla stessa coda. Per impostazione predefinita, il messaggio rimane invisibile per 30 secondi. Per completare la rimozione del messaggio dalla coda, è necessario chiamare anche **deleteMessage**. Se il codice non riesce a elaborare un messaggio, questo processo in due passaggi garantisce che sia possibile ottenere lo stesso messaggio e riprovare. Il codice chiama **deleteMessage** subito dopo l'elaborazione del messaggio.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue.
    CloudQueueMessage retrievedMessage = queue.retrieveMessage();

    if (retrievedMessage != null)
    {
        // Process the message in less than 30 seconds, and then delete the message.
        queue.deleteMessage(retrievedMessage);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="additional-options-for-dequeuing-messages"></a>Opzioni aggiuntive per rimuovere i messaggi dalla coda

Esistono due modi per personalizzare il recupero di messaggi da una coda. Per prima cosa, ottenere un batch di messaggi (fino a 32). In secondo luogo, impostare un timeout di invisibilità più lungo o più breve, consentendo al codice più o meno tempo di elaborare completamente ogni messaggio.

# <a name="java-v12"></a>[Java V12](#tab/java)

Nell'esempio di codice seguente viene usato il metodo **receiveMessages** per ottenere 20 messaggi in una sola chiamata. Elabora quindi ogni messaggio usando un ciclo **for** . Per ogni messaggio, inoltre, il timeout di invisibilità viene impostato su cinque minuti (300 secondi). Il timeout viene avviato per tutti i messaggi contemporaneamente. Quando sono trascorsi cinque minuti dalla chiamata a **receiveMessages**, i messaggi non eliminati diventeranno nuovamente visibili.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DequeueMessages":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

Nell'esempio di codice seguente viene usato il metodo **retrieveMessages** per recuperare 20 messaggi con una sola chiamata. Elabora quindi ogni messaggio usando un ciclo **for** . Per ogni messaggio, inoltre, il timeout di invisibilità viene impostato su cinque minuti (300 secondi). Il timeout viene avviato per tutti i messaggi contemporaneamente. Quando sono trascorsi cinque minuti dalla chiamata a **retrieveMessages**, i messaggi non eliminati diventeranno nuovamente visibili.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
    for (CloudQueueMessage message : queue.retrieveMessages(20, 300, null, null)) {
        // Do processing for all messages in less than 5 minutes,
        // deleting each message after processing.
        queue.deleteMessage(message);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-list-the-queues"></a>Procedura: elencare le code

# <a name="java-v12"></a>[Java V12](#tab/java)

Per ottenere un elenco delle code correnti, chiamare il metodo **QueueServiceClient. listQueues ()** , che restituirà una raccolta di oggetti **coda** .

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ListQueues":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

Per ottenere un elenco delle code correnti, chiamare il metodo **CloudQueueClient.listQueues()** che restituisce una raccolta di oggetti **CloudQueue**.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient =
        storageAccount.createCloudQueueClient();

    // Loop through the collection of queues.
    for (CloudQueue queue : queueClient.listQueues())
    {
        // Output each queue name.
        System.out.println(queue.getName());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-delete-a-queue"></a>Procedura: eliminare una coda

# <a name="java-v12"></a>[Java V12](#tab/java)

Per eliminare una coda e tutti i messaggi che contiene, chiamare il metodo **Delete** nell'oggetto **QueueClient** .

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DeleteMessageQueue":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

Per eliminare una coda e tutti i messaggi in essa contenuti, chiamare il metodo **deleteIfExists** sull'oggetto **CloudQueue**.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Delete the queue if it exists.
    queue.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base dell'archiviazione di accodamento, visitare i collegamenti seguenti per altre informazioni sulle attività di archiviazione più complesse.

* [Azure Storage SDK per Java][Azure Storage SDK for Java]
* [Riferimento all'SDK del client di archiviazione di Azure][Azure Storage Client SDK Reference]
* [API REST dei servizi di archiviazione di Azure][Azure Storage Services REST API]
* [Blog del team di Archiviazione di Azure][Azure Storage Team Blog]

[Azure SDK for Java]: https://github.com/azure/azure-sdk-for-java
[Azure Storage SDK for Java]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage
[Riferimento all'SDK del client di archiviazione di Azure]: https://azure.github.io/azure-sdk-for-java/storage.html
[Azure Storage Services REST API]: https://docs.microsoft.com/rest/api/storageservices/
[Azure Storage Team Blog]: https://techcommunity.microsoft.com/t5/azure-storage/bg-p/AzureStorageBlog
