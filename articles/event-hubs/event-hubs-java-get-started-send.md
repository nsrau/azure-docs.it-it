---
title: Inviare eventi a Hub eventi di Azure usando Java | Microsoft Docs
description: Guida introduttiva all'invio a Hub di eventi con Java
services: event-hubs
author: sethmanheim
manager: timlt
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 05/30/2018
ms.author: sethm
ms.openlocfilehash: 6d3bf0b8ac5c5bdc7bf3deda21e800fe3cc6be2e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34626412"
---
# <a name="send-events-to-azure-event-hubs-using-java"></a>Inviare eventi a Hub eventi di Azure usando Java

Hub eventi è un sistema di inserimento a scalabilità elevata, in grado di inserire milioni di eventi al secondo, che permette a un'applicazione di elaborare e analizzare le elevate quantità di dati prodotti dalle applicazioni e dai dispositivi connessi. Dopo la raccolta in un hub eventi, è possibile trasformare e archiviare i dati usando qualsiasi provider di analisi in tempo reale o un cluster di archiviazione.

Per altre informazioni, vedere [Panoramica di Hub eventi][Event Hubs overview].

Questa esercitazione illustra come inviare eventi a un hub eventi usando un'applicazione console in Java. Per ricevere eventi usando la libreria host del processore di eventi di Java, vedere [questo articolo](event-hubs-java-get-started-receive-eph.md) o fare clic sul linguaggio di ricezione appropriato nel sommario a sinistra.

## <a name="prerequisites"></a>prerequisiti

Per completare questa esercitazione è necessario soddisfare i prerequisiti seguenti:

* Ambiente di sviluppo in Java. Questa esercitazione usa [Eclipse](https://www.eclipse.org/).
* Un account Azure attivo. Se non si ha una sottoscrizione di Azure, creare un [account gratuito][] prima di iniziare.

Il codice in questa esercitazione si basa sull'[esempio di GitHub SimpleSend](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend), che è possibile esaminare per visualizzare la versione completa dell'applicazione in funzione.

## <a name="send-events-to-event-hubs"></a>Inviare eventi a Hub eventi

La libreria client Java per Hub eventi è disponibile per l'uso in progetti Maven dal [repository centrale di Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22). Per fare riferimento a questa libreria, è possibile usare questa dichiarazione di dipendenza all'interno del file di progetto di Maven. La versione corrente è 1.0.0:    

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>1.0.0</version>
</dependency>
```

Per diversi tipi di ambienti di compilazione è possibile ottenere in modo esplicito i file JAR rilasciati più recenti dal [repository centrale di Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22).  

Per un autore di eventi semplice, importare il pacchetto *com.microsoft.azure.eventhubs* per le classi di client di hub eventi e il pacchetto *com.microsoft.azure.servicebus* per le classi di utilità, ad esempio eccezioni comuni condivise con il client di messaggistica del bus di servizio di Azure. 

### <a name="declare-the-send-class"></a>Dichiarare la classe Send

Per l'esempio seguente, creare prima un nuovo progetto Maven per un'applicazione console/shell nell'ambiente di sviluppo Java preferito. Denominare la classe `Send`:     

```java
package com.microsoft.azure.eventhubs.samples.send;

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
import com.microsoft.azure.eventhubs.EventData;
import com.microsoft.azure.eventhubs.EventHubClient;
import com.microsoft.azure.eventhubs.PartitionSender;
import com.microsoft.azure.eventhubs.EventHubException;

import java.io.IOException;
import java.nio.charset.Charset;
import java.time.Instant;
import java.util.Random;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Executors;
import java.util.concurrent.ExecutorService;

public class Send {

    public static void main(String[] args)
            throws EventHubException, ExecutionException, InterruptedException, IOException {
```

### <a name="construct-connection-string"></a>Costruire la stringa di connessione

Usare la classe ConnectionStringBuilder per costruire un valore di stringa di connessione da passare all'istanza del client di Hub eventi. Sostituire i segnaposto con i valori ottenuti durante la creazione dello spazio dei nomi e dell'hub eventi:

```java
   final ConnectionStringBuilder connStr = new ConnectionStringBuilder()
      .setNamespaceName("----NamespaceName-----")
      .setEventHubName("----EventHubName-----")
      .setSasKeyName("-----SharedAccessSignatureKeyName-----")
      .setSasKey("---SharedAccessSignatureKey----");
```

### <a name="send-events"></a>Inviare eventi

Creare un evento singolare trasformando una stringa nella relativa codifica UTF-8. Creare quindi una nuova istanza del client di Hub eventi dalla stringa di connessione e inviare il messaggio:   

```java 
byte[] payloadBytes = "Test AMQP message from JMS".getBytes("UTF-8");
EventData sendEvent = new EventData(payloadBytes);

final EventHubClient ehClient = EventHubClient.createSync(connStr.toString(), executorService);
ehClient.sendSync(sendEvent);
    
// close the client at the end of your program
ehClient.closeSync();

``` 

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su Hub eventi visitare i collegamenti seguenti:

* [Receive events using the EventProcessorHost](event-hubs-java-get-started-receive-eph.md) (Ricevere eventi usando EventProcessorHost)
* [Panoramica di Hub eventi][Event Hubs overview]
* [Creare un hub eventi](event-hubs-create.md)
* [Domande frequenti su Hub eventi](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-overview.md
[account gratuito]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

