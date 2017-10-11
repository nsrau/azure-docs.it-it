---
title: Inviare eventi a Hub eventi di Azure usando Java | Microsoft Docs
description: Guida introduttiva all'invio a Hub di eventi con Java
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: core
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: sethm
ms.openlocfilehash: b31771001989e20b88bc8d7bca1afceb58ec197c
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2017
---
# <a name="send-events-to-azure-event-hubs-using-java"></a>Inviare eventi a Hub eventi di Azure usando Java

## <a name="introduction"></a>Introduzione
Hub eventi è un sistema di inserimento a scalabilità elevata, in grado di inserire milioni di eventi al secondo, che permette a un'applicazione di elaborare e analizzare le elevate quantità di dati prodotti dalle applicazioni e dai dispositivi connessi. Dopo la raccolta in un hub eventi, è possibile trasformare e archiviare i dati usando qualsiasi provider di analisi in tempo reale o un cluster di archiviazione.

Per altre informazioni, vedere [Panoramica di Hub eventi][Event Hubs overview].

Questa esercitazione illustra come inviare eventi a un hub eventi usando un'applicazione console in Java. Per ricevere eventi usando la libreria host del processore di eventi di Java, vedere [questo articolo](event-hubs-java-get-started-receive-eph.md) o fare clic sul linguaggio di ricezione appropriato nel sommario a sinistra.

Per completare questa esercitazione, sono necessari gli elementi seguenti:

* Ambiente di sviluppo in Java. Per questa esercitazione si presuppone l'uso di [Eclipse](https://www.eclipse.org/).
* Un account Azure attivo. <br/>Se non si ha un account, è possibile creare un account gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">versione di valutazione gratuita di Azure</a>.

## <a name="send-messages-to-event-hubs"></a>Inviare messaggi all'hub eventi
La libreria client Java per Hub eventi è disponibile per l'uso in progetti Maven dal [repository centrale di Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22). Per fare riferimento a questa libreria è possibile usare questa dichiarazione di dipendenza all'interno del file di progetto di Maven:    

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>{VERSION}</version>
</dependency>
```

Per diversi tipi di ambienti di compilazione è possibile ottenere in modo esplicito i file JAR rilasciati più recenti dal [repository centrale di Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22).  

Per un autore di eventi semplice, importare il pacchetto *com.microsoft.azure.eventhubs* per le classi di client di hub eventi e il pacchetto *com.microsoft.azure.servicebus* per le classi di utilità, ad esempio eccezioni comuni condivise con il client di messaggistica del bus di servizio di Azure. 

Per l'esempio seguente, creare prima un nuovo progetto Maven per un'applicazione console/shell nell'ambiente di sviluppo Java preferito. Denominare la classe `Send`.     

```java
import java.io.IOException;
import java.nio.charset.*;
import java.util.*;
import java.util.concurrent.ExecutionException;

import com.microsoft.azure.eventhubs.*;
import com.microsoft.azure.servicebus.*;

public class Send
{
    public static void main(String[] args) 
            throws ServiceBusException, ExecutionException, InterruptedException, IOException
    {
```

Sostituire lo spazio dei nomi e i nomi dell'hub eventi con i valori utilizzati durante la creazione dell'hub eventi.

```java
    final String namespaceName = "----ServiceBusNamespaceName-----";
    final String eventHubName = "----EventHubName-----";
    final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
    final String sasKey = "---SharedAccessSignatureKey----";
    ConnectionStringBuilder connStr = new ConnectionStringBuilder(namespaceName, eventHubName, sasKeyName, sasKey);
```

A questo punto, creare un evento singolare trasformando una stringa nel relativo corrispettivo con codifica UTF-8. Creare infine una nuova istanza del client di Hub eventi dalla stringa di connessione e inviare il messaggio.   

```java 

    byte[] payloadBytes = "Test AMQP message from JMS".getBytes("UTF-8");
    EventData sendEvent = new EventData(payloadBytes);

    EventHubClient ehClient = EventHubClient.createFromConnectionStringSync(connStr.toString());
    ehClient.sendSync(sendEvent);
    }
}

``` 

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni su Hub eventi visitare i collegamenti seguenti:

* [Receive events using the EventProcessorHost](event-hubs-java-get-started-receive-eph.md) (Ricevere eventi usando EventProcessorHost)
* [Panoramica di Hub eventi][Event Hubs overview]
* [Creare un hub eventi](event-hubs-create.md)
* [Domande frequenti su Hub eventi](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-overview.md