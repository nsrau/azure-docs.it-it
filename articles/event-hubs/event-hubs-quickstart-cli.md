---
title: "Guida introduttiva di Azure: Creare un hub eventi usando l'interfaccia della riga di comando di Azure | Microsoft Docs"
description: Questa guida introduttiva illustra come creare un hub eventi usando l'interfaccia della riga di comando di Azure e inviare e ricevere eventi usando Java.
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc
ms.date: 06/26/2018
ms.author: shvija
ms.openlocfilehash: 0607c9a65febdb5890eb49808a0fcef582deb37e
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2018
ms.locfileid: "42022789"
---
# <a name="quickstart-create-an-event-hub-using-azure-cli"></a>Guida introduttiva: Creare un hub eventi usando l'interfaccia della riga di comando di Azure

Hub eventi di Azure è una piattaforma di streaming di dati e un servizio di inserimento degli eventi estremamente scalabile, che consente di ricevere ed elaborare milioni di eventi al secondo. Questa Guida introduttiva illustra come creare risorse di Hub eventi usando l'interfaccia della riga di comando di Azure, quindi inviare e ricevere flussi di eventi da un hub eventi usando codice Java.

Per completare questa guida introduttiva è necessaria una sottoscrizione di Azure. Se non se ne ha una, [creare un account gratuito][] prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando di Azure in locale, in questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.4 o successiva. Eseguire `az --version` per controllare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="log-on-to-azure"></a>Accedere ad Azure

I passaggi seguenti non sono necessari se si eseguono i comandi in Cloud Shell. Se si esegue l'interfaccia della riga di comando in locale, attenersi alla procedura seguente per accedere ad Azure e impostare la sottoscrizione corrente:

Eseguire il comando seguente per accedere ad Azure:

```azurecli-interactive
az login
```

Impostare il contesto della sottoscrizione corrente. Sostituire `MyAzureSub` con il nome della sottoscrizione di Azure che si desidera usare:

```azurecli-interactive
az account set --subscription MyAzureSub
``` 

## <a name="provision-resources"></a>Effettuare il provisioning delle risorse

Eseguire i comandi seguenti per il provisioning delle risorse di Hub eventi. Assicurarsi di sostituire i segnaposto `myResourceGroup`, `namespaceName`, `eventHubName` e `storageAccountName` con i valori appropriati:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location eastus

# Create an Event Hubs namespace
az eventhubs namespace create --name namespaceName --resource-group myResourceGroup -l eastus2

# Create an event hub
az eventhubs eventhub create --name eventHubName --resource-group myResourceGroup --namespace-name namespaceName

# Create a general purpose standard storage account
az storage account create --name storageAccountName --resource-group myResourceGroup --location eastus2 --sku Standard_RAGRS --encryption blob

# List the storage account access keys
az storage account keys list --resource-group myResourceGroup --account-name storageAccountName

# Get namespace connection string
az eventhubs namespace authorization-rule keys list --resource-group myResourceGroup --namespace-name namespaceName --name RootManageSharedAccessKey
```

Copiare e incollare la stringa di connessione in un percorso temporaneo, ad esempio Blocco note, per usarla in seguito.

## <a name="stream-into-event-hubs"></a>Trasmettere un flusso a Hub eventi

Il passaggio successivo consiste nel download di codice di esempio che invia flussi di eventi a un hub eventi e riceve tali eventi usando l'host del processore di eventi. In primo luogo, inviare i messaggi:

Clonare il [repository GitHub di Hub eventi](https://github.com/Azure/azure-event-hubs) eseguendo il comando seguente:

```bash
git clone https://github.com/Azure/azure-event-hubs.git
```

Passare alla cartella **SimpleSend**: `\azure-event-hubs\samples\Java\Basic\SimpleSend\src\main\java\com\microsoft\azure\eventhubs\samples\SimpleSend`. Aprire il file SimpleSend.java e sostituire la stringa `"Your Event Hubs namaspace name"` con lo spazio dei nomi di Hub eventi ottenuto nella sezione "Creare uno spazio dei nomi di Hub eventi" di questo articolo.

Sostituire `"Your event hub"` con il nome dell'hub eventi creato nello spazio dei nomi e `"Your policy name"` con il nome dei criteri di accesso condiviso per lo spazio dei nomi. A meno che non si creino nuovi criteri, il valore predefinito è **RootManageSharedAccessKey**. 

Sostituire infine `"Your primary SAS key"` con il valore della chiave di firma di accesso condiviso per il criterio nel passaggio precedente.

### <a name="build-the-application"></a>Compilare l'applicazione 

Passare alla cartella `\azure-event-hubs\samples\Java\Basic\SimpleSend` e immettere il comando di compilazione seguente:

```shell
mvn clean package -DskipTests
```

### <a name="receive"></a>Ricevere

Scaricare l'esempio Event Processor Host (Host processore di eventi) che riceve i messaggi inviati. Passare alla cartella **EventProcessorSample**: `\azure-event-hubs\samples\Java\Basic\EventProcessorSample\src\main\java\com\microsoft\azure\eventhubs\samples\eventprocessorsample`.

Aprire il file EventProcessorSample.java, sostituire il valore `----EventHubsNamespaceName-----` con lo spazio dei nomi di Hub eventi ottenuto nella sezione "Creare uno spazio dei nomi di Hub eventi" di questo articolo. 

Sostituire i valori dell'altra stringa in questo file: sostituire `----EventHubName-----` con il nome dell'hub eventi creato nello spazio dei nomi e `-----SharedAccessSignatureKeyName-----` con il nome dei criteri di accesso condiviso per lo spazio dei nomi. A meno che non si creino nuovi criteri, il valore predefinito è **RootManageSharedAccessKey**.

Sostituire `---SharedAccessSignatureKey----` con il valore della chiave di firma di accesso condiviso per i criteri nel passaggio precedente, sostituire `----AzureStorageConnectionString----` con la stringa di connessione dell'account di archiviazione creata e `----StorageContainerName----` con il nome del contenitore nell'account di archiviazione creato. 

Sostituire infine `----HostNamePrefix----` con il nome dell'account di archiviazione.

### <a name="build-the-receiver"></a>Compilare il ricevitore 

Per compilare l'applicazione ricevente, passare alla cartella `\azure-event-hubs\samples\Java\Basic\EventProcessorSample` e immettere il comando seguente:

```shell
mvn clean package -DskipTests
```

### <a name="run-the-apps"></a>Eseguire le app

Se le build vengono completate correttamente, è possibile inviare e ricevere eventi. Eseguire innanzitutto l'applicazione **SimpleSend** e osservare gli eventi inviati. Per eseguire il programma, passare alla cartella `\azure-event-hubs\samples\Java\Basic\SimpleSend` ed immettere il comando seguente:

```shell
java -jar ./target/simplesend-1.0.0-jar-with-dependencies.jar
```

Successivamente, eseguire l'app **EventProcessorSample** e osservare gli eventi ricevuti. Per eseguire il programma passare alla cartella `\azure-event-hubs\samples\Java\Basic\EventProcessorSample` e immettere il comando seguente:
   
```shell
java -jar ./target/eventprocessorsample-1.0.0-jar-with-dependencies.jar
```

Dopo avere eseguito entrambi i programmi, è possibile controllare la pagina di panoramica del portale di Azure per l'hub eventi per vedere il numero di messaggi in ingresso e in uscita:

![inviare e ricevere](./media/event-hubs-quickstart-cli/ephjava.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Eseguire il comando seguente per rimuovere il gruppo di risorse, lo spazio dei nomi, l'account di archiviazione e tutte le risorse correlate. Sostituire `myResourceGroup` con il nome del gruppo di risorse creato:

```azurecli
az group delete --resource-group myResourceGroup
```

## <a name="understand-the-sample-code"></a>Informazioni sul codice di esempio

Questa sezione contiene altri dettagli sulle operazioni eseguite dal codice di esempio.

### <a name="send"></a>Invio

Nel file SimpleSend.java la maggior parte delle operazioni viene eseguita nel metodo main(). Innanzitutto il codice utilizza un'istanza di `ConnectionStringBuilder` per costruire la stringa di connessione usando i valori definiti dall'utente per il nome dello spazio dei nomi, il nome dell'hub eventi, il nome della chiave di firma di accesso condiviso e la chiave di firma di accesso condiviso stessa:

```java
final ConnectionStringBuilder connStr = new ConnectionStringBuilder()
        .setNamespaceName("Your Event Hubs namespace name")
        .setEventHubName("Your event hub")
        .setSasKeyName("Your policy name")
        .setSasKey("Your primary SAS key");
```

L'oggetto Java che contiene il payload dell'evento viene quindi convertito in Json:

```java
final Gson gson = new GsonBuilder().create();

final PayloadEvent payload = new PayloadEvent(1);
byte[] payloadBytes = gson.toJson(payload).getBytes(Charset.defaultCharset());
EventData sendEvent = EventData.create(payloadBytes);  
```

Il client di Hub eventi viene creato in questa riga di codice:

```java
final EventHubClient ehClient = EventHubClient.createSync(connStr.toString(), executorService);
```

Il blocco try/finally invia un evento round robin a una partizione non specificata:

```java
try {
    for (int i = 0; i < 100; i++) {

        String payload = "Message " + Integer.toString(i);
        //PayloadEvent payload = new PayloadEvent(i);
        byte[] payloadBytes = gson.toJson(payload).getBytes(Charset.defaultCharset());
        EventData sendEvent = EventData.create(payloadBytes);

        // Send - not tied to any partition
        // Event Hubs service will round-robin the events across all EventHubs partitions.
        // This is the recommended & most reliable way to send to EventHubs.
        ehClient.sendSync(sendEvent);
    }

    System.out.println(Instant.now() + ": Send Complete...");
    System.in.read();
} finally {
    ehClient.closeSync();
    executorService.shutdown();
}
```

### <a name="receive"></a>Ricevere 

L'operazione di ricezione avviene nel file EventProcessorSample.java. In primo luogo, dichiara le costanti che mantengono il nome dello spazio dei nomi di Hub eventi e altre credenziali:

```java
String consumerGroupName = "$Default";
String namespaceName = "----NamespaceName----";
String eventHubName = "----EventHubName----";
String sasKeyName = "----SharedAccessSignatureKeyName----";
String sasKey = "----SharedAccessSignatureKey----";
String storageConnectionString = "----AzureStorageConnectionString----";
String storageContainerName = "----StorageContainerName----";
String hostNamePrefix = "----HostNamePrefix----";
```

Come il programma SimpleSend, il codice crea quindi un'istanza di ConnectionStringBuilder per costruire la stringa di connessione:

```java
ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder()
    .setNamespaceName(namespaceName)
    .setEventHubName(eventHubName)
    .setSasKeyName(sasKeyName)
    .setSasKey(sasKey);
```

L'*host processore di eventi* è una classe che semplifica la ricezione di eventi dagli hub eventi gestendo checkpoint persistenti e ricezioni parallele da tali hub. A questo punto, il codice crea un'istanza di `EventProcessorHost`:

```java
EventProcessorHost host = new EventProcessorHost(
    EventProcessorHost.createHostName(hostNamePrefix),
    eventHubName,
    consumerGroupName,
    eventHubConnectionString.toString(),
    storageConnectionString,
    storageContainerName);
```

Dopo la dichiarazione di un codice di gestione errori, l'app definisce la classe `EventProcessor`, un'implementazione dell'interfaccia `IEventProcessor`. Questa classe elabora gli eventi ricevuti:

```java
public static class EventProcessor implements IEventProcessor
{
    private int checkpointBatchingCount = 0;
    ...
```

Il metodo `onEvents()` viene chiamato quando gli eventi vengono ricevuti su questa partizione dell'hub eventi:

```java
@Override
public void onEvents(PartitionContext context, Iterable<EventData> events) throws Exception
{
    System.out.println("SAMPLE: Partition " + context.getPartitionId() + " got event batch");
    int eventCount = 0;
    for (EventData data : events)
    {
        try
        {
         System.out.println("SAMPLE (" + context.getPartitionId() + "," + data.getSystemProperties().getOffset() + "," +
                data.getSystemProperties().getSequenceNumber() + "): " + new String(data.getBytes(), "UTF8"));
             eventCount++;
                
         // Checkpointing persists the current position in the event stream for this partition and means that the next
         // time any host opens an event processor on this event hub+consumer group+partition combination, it will start
         // receiving at the event after this one. Checkpointing is usually not a fast operation, so there is a tradeoff
         // between checkpointing frequently (to minimize the number of events that will be reprocessed after a crash, or
         // if the partition lease is stolen) and checkpointing infrequently (to reduce the impact on event processing
         // performance). Checkpointing every five events is an arbitrary choice for this sample.
         this.checkpointBatchingCount++;
         if ((checkpointBatchingCount % 5) == 0)
         {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " checkpointing at " +
                    data.getSystemProperties().getOffset() + "," + data.getSystemProperties().getSequenceNumber());
            // Checkpoints are created asynchronously. It is important to wait for the result of checkpointing
            // before exiting onEvents or before creating the next checkpoint, to detect errors and to ensure proper ordering.
            context.checkpoint(data).get();
         }
    }
        catch (Exception e)
        {
            System.out.println("Processing failed for an event: " + e.toString());
        }
    }
    System.out.println("SAMPLE: Partition " + context.getPartitionId() + " batch size was " + eventCount + " for host " + context.getOwner());
}
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono stati creati lo spazio dei nomi di Hub eventi e altre risorse necessarie per inviare e ricevere eventi dall'hub eventi. Per altre informazioni, passare all'esercitazione successiva:

> [!div class="nextstepaction"]
> [Visualizzare anomalie dei dati nei flussi di dati di Hub eventi](event-hubs-tutorial-visualize-anomalies.md)

[Creare un account gratuito]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install Azure CLI 2.0]: /cli/azure/install-azure-cli
[az group create]: /cli/azure/group#az-group-create
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
