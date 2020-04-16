---
title: Inviare o ricevere eventi da Hub eventi di Azure tramite Java (ultima versione)
description: Questo articolo illustra come creare un'applicazione Java che invia/riceve eventi a/da Hub eventi di Azure usando il pacchetto azure-messaging-eventhubs più recente.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 02/11/2020
ms.author: spelluru
ms.openlocfilehash: 824244c0c3247e5a218c1551dd95de6e1d6e1007
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419222"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-messaging-eventhubs"></a>Usare Java per inviare o ricevere eventi da Hub eventi di Azure (azure-messaging-eventhubs)
Questa guida di avvio rapido illustra come inviare e ricevere eventi da un hub eventi con il pacchetto Java **azure-messaging-eventhubs**.

> [!IMPORTANT]
> In questo argomento di avvio rapido viene usato il nuovo pacchetto **azure-messaging-eventhubs**. Per una guida di avvio rapido sui pacchetti **azure-eventhubs** e **azure-eventhubs-eph** precedenti, vedere [Inviare e ricevere eventi con azure-eventhubs e azure-eventhubs-eph](event-hubs-java-get-started-send.md). 


## <a name="prerequisites"></a>Prerequisiti
Se non si ha familiarità con Hub eventi di Azure, vedere [Panoramica di Hub eventi](event-hubs-about.md) prima di procedere con questa guida di avvio rapido. 

Per completare questa guida introduttiva è necessario soddisfare i prerequisiti seguenti:

- **Sottoscrizione di Microsoft Azure**. Per usare i servizi di Azure, tra cui Hub eventi di Azure, è necessaria una sottoscrizione.  Se non si ha un account Azure, è possibile iscriversi per ottenere una [versione di valutazione gratuita](https://azure.microsoft.com/free/) oppure usare i vantaggi della sottoscrizione di MSDN per la [creazione di un account](https://azure.microsoft.com).
- Ambiente di sviluppo in Java. In questa guida di avvio rapido si usa [Eclipse](https://www.eclipse.org/). È necessario Java Development Kit (JDK) versione 8 o successiva. 
- **Creare uno spazio dei nomi di Hub eventi e un hub eventi**. Il primo passaggio consiste nell'usare il [portale di Azure](https://portal.azure.com) per creare uno spazio dei nomi di tipo Hub eventi e ottenere le credenziali di gestione necessarie all'applicazione per comunicare con l'hub eventi. Per creare uno spazio dei nomi e un hub eventi, seguire la procedura descritta in [questo articolo](event-hubs-create.md). Ottenere quindi la **stringa di connessione allo spazio dei nomi di Hub eventi** seguendo le istruzioni disponibili nell'articolo [Ottenere una stringa di connessione](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). La stringa di connessione sarà necessaria più avanti in questa guida di avvio rapido.

## <a name="send-events"></a>Inviare eventi 
Questa sezione illustra come creare un'applicazione Java per inviare eventi a un hub eventi. 

### <a name="add-reference-to-azure-event-hubs-library"></a>Aggiungere il riferimento alla libreria di Hub eventi di Azure

La libreria client Java per Hub eventi è disponibile nel [repository centrale di Maven](https://search.maven.org/search?q=a:azure-messaging-eventhubs). Per fare riferimento a questa libreria è possibile usare questa dichiarazione di dipendenza all'interno del file di progetto di Maven:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-eventhubs</artifactId>
    <version>5.0.1</version>
</dependency>
```

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Scrivere codice per inviare messaggi all'hub eventi

Per l'esempio seguente, creare prima un nuovo progetto Maven per un'applicazione console/shell nell'ambiente di sviluppo Java preferito. Aggiungere una classe denominata `Sender` e includere il codice seguente nella classe:

```java
import com.azure.messaging.eventhubs.*;
import static java.nio.charset.StandardCharsets.UTF_8;

public class Sender {
       public static void main(String[] args) {
    }
}
```

### <a name="connection-string-and-event-hub"></a>Stringa di connessione e hub eventi
Questo codice usa la stringa di connessione allo spazio dei nomi di Hub eventi e il nome dell'hub eventi per creare un client di Hub eventi. 

```java
String connectionString = "<CONNECTION STRING to EVENT HUBS NAMESPACE>";
String eventHubName = "<EVENT HUB NAME>";
```

### <a name="create-an-event-hubs-producer-client"></a>Creare un client producer di Hub eventi 
Questo codice crea un oggetto client producer usato per produrre/inviare eventi all'hub eventi. 

```java
EventHubProducerClient producer = new EventHubClientBuilder()
    .connectionString(connectionString, eventHubName)
    .buildProducerClient();
```

### <a name="prepare-a-batch-of-events"></a>Preparare un batch di eventi
Questo codice prepara un batch di eventi. 

```java
EventDataBatch batch = producer.createBatch();
batch.tryAdd(new EventData("First event"));
batch.tryAdd(new EventData("Second event"));
batch.tryAdd(new EventData("Third event"));
batch.tryAdd(new EventData("Fourth event"));
batch.tryAdd(new EventData("Fifth event"));
```

### <a name="send-the-batch-of-events-to-the-event-hub"></a>Inviare il batch di eventi all'hub eventi
Questo codice invia il batch di eventi preparato nel passaggio precedente all'hub eventi. Il codice seguente blocca l'operazione di invio. 

```java
producer.send(batch);
```

### <a name="close-and-cleanup"></a>Chiudere e pulire
Questo codice chiude il producer. 

```java
producer.close();
```
### <a name="complete-code-to-send-events"></a>Codice completo l'invio di eventi
Ecco il codice completo per l'invio di eventi all'hub eventi. 

```java
import com.azure.messaging.eventhubs.*;

public class Sender {
    public static void main(String[] args) {
        final String connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
        final String eventHubName = "EVENT HUB NAME";

        // create a producer using the namespace connection string and event hub name
        EventHubProducerClient producer = new EventHubClientBuilder()
            .connectionString(connectionString, eventHubName)
            .buildProducerClient();

        // prepare a batch of events to send to the event hub    
        EventDataBatch batch = producer.createBatch();
        batch.tryAdd(new EventData("First event"));
        batch.tryAdd(new EventData("Second event"));
        batch.tryAdd(new EventData("Third event"));
        batch.tryAdd(new EventData("Fourth event"));
        batch.tryAdd(new EventData("Fifth event"));

        // send the batch of events to the event hub
        producer.send(batch);

        // close the producer
        producer.close();
    }
}
```

Compilare il programma e assicurarsi che non siano presenti errori. Questo programma verrà eseguito dopo l'esecuzione del programma ricevente. 

## <a name="receive-events"></a>Ricevere eventi
Il codice di questa esercitazione si basa sull'[esempio di codice EventProcessorSample in GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs/EventProcessorClientSample.java), che è possibile esaminare per vedere l'applicazione completa in funzione.

> [!NOTE]
> Se il codice viene eseguito in Hub di Azure Stack, questa piattaforma potrebbe supportare una versione diversa dell'SDK di archiviazione BLOB rispetto a quelle disponibili in Azure. Se ad esempio l'esecuzione avviene nell'[hub di Azure Stack versione 2002](https://docs.microsoft.com/azure-stack/user/event-hubs-overview), la versione più recente disponibile per il servizio di archiviazione è 2017-11-09. In questo caso, oltre ai passaggi descritti in questa sezione, sarà anche necessario aggiungere codice destinato alla versione 2017-11-09 dell'API del servizio di archiviazione. Per informazioni su come definire come destinazione una versione specifica dell'API di archiviazione, vedere [questo esempio in GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java). Per altre informazioni sulle versioni del servizio di archiviazione di Azure supportate in Hub di Azure Stack, vedere [Archiviazione nell'hub di Azure Stack: differenze e considerazioni](https://docs.microsoft.com/azure-stack/user/azure-stack-acs-differences).

### <a name="create-a-java-project"></a>Creare un progetto Java

La libreria client Java per hub eventi è disponibile per l'utilizzo in progetti Maven dal [Repository centrale Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22)ed è possibile farvi riferimento utilizzando la seguente dichiarazione di dipendenza all'interno del file di progetto Maven: 

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs</artifactId>
        <version>5.0.1</version>
    </dependency>
</dependencies>
```

1. Usare il codice seguente per creare una nuova classe denominata `Receiver`. Sostituire i segnaposto con i valori usati durante la creazione dell'Hub eventi e dell'account di archiviazione:
   
   ```java
     import com.azure.messaging.eventhubs.*;
     import com.azure.messaging.eventhubs.models.ErrorContext;
     import com.azure.messaging.eventhubs.models.EventContext;
     import java.util.concurrent.TimeUnit;
     import java.util.function.Consumer;
    
     public class Receiver {
    
         final static String connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
         final static String eventHubName = "<EVENT HUB NAME>";
         
         public static void main(String[] args) throws Exception {
    
             // function to process events
             Consumer<EventContext> processEvent = eventContext  -> {
                 System.out.print("Received event: ");
                 // print the body of the event
                 System.out.println(eventContext.getEventData().getBodyAsString());
                 eventContext.updateCheckpoint();
             };
    
             // function to process errors
             Consumer<ErrorContext> processError = errorContext -> {
                 // print the error message
                 System.out.println(errorContext.getThrowable().getMessage());
             };
    
            
             EventProcessorClient eventProcessorClient = new EventProcessorClientBuilder()
                     .connectionString(connectionString, eventHubName)
                     .processEvent(processEvent)
                     .processError(processError)
                     .consumerGroup(EventHubClientBuilder.DEFAULT_CONSUMER_GROUP_NAME)
                     .checkpointStore(new InMemoryCheckpointStore())
                     .buildEventProcessorClient();
    
             System.out.println("Starting event processor");
             eventProcessorClient.start();
    
             System.out.println("Press enter to stop.");
             System.in.read();
    
             System.out.println("Stopping event processor");
             eventProcessorClient.stop();
             System.out.println("Event processor stopped.");
    
             System.out.println("Exiting process");
         }
     }
    ```
    
2. Scaricare il file **InMemoryCheckpointStore.java** da [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs/InMemoryCheckpointStore.java) e aggiungerlo al progetto. 
3. Compilare il programma e assicurarsi che non siano presenti errori. 

## <a name="run-the-applications"></a>Eseguire le applicazioni
1. Eseguire prima l'applicazione **ricevente**.
1. Quindi, eseguire l'applicazione **mittente**. 
1. Nella finestra dell'applicazione **ricevente** verificare che siano visualizzati gli eventi pubblicati dall'applicazione mittente.
1. Premere **INVIO** nella finestra dell'applicazione ricevente per arrestare l'applicazione. 

## <a name="next-steps"></a>Passaggi successivi
Controllare gli [esempi di Java SDK in GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)

