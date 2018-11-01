---
title: Eseguire lo streaming in Hub eventi di Azure per Apache Kafka | Microsoft Docs
description: Eseguire lo streaming in Hub eventi usando le API e il protocollo Kafka.
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2018
ms.author: bahariri
ms.openlocfilehash: 18224c3ebf3b5dd9920dcc4b4868ee8bb42e5f91
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50418790"
---
# <a name="stream-into-event-hubs-for-the-apache-kafka"></a>Eseguire lo streaming in Hub eventi per Apache Kafka
Questa guida introduttiva illustra come eseguire lo streaming in un'istanza di Hub eventi abilitata per Kafka senza modificare i client di protocollo o eseguire cluster personalizzati. Si apprenderà come usare i producer e i consumer per comunicare con un'istanza di Hub eventi abilitata per Kafka con solo una modifica della configurazione nelle applicazioni. Hub eventi di Azure supporta [Apache Kafka versione 1.0.](https://kafka.apache.org/10/documentation.html)

> [!NOTE]
> Questo esempio è disponibile su [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java)

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida introduttiva, accertarsi di soddisfare i requisiti seguenti:

* Leggere con attenzione l'articolo [Hub eventi per Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md).
* Una sottoscrizione di Azure. Se non se ne ha una, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks).
* [Scaricare](http://maven.apache.org/download.cgi) e [installare](http://maven.apache.org/install.html) un archivio binario Maven.
* [Git](https://www.git-scm.com/)
* [Uno spazio dei nomi di Hub eventi abilitato per Kafka](event-hubs-create.md)

## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Creare uno spazio dei nomi di Hub eventi con supporto per Kafka

1. Accedere al [portale di Azure][portale di Azure] e fare clic su **Crea una risorsa** nella parte superiore sinistra della schermata.

2. Eseguire la ricerca per gli Hub eventi e selezionare le opzioni illustrate di seguito:
    
    ![Cercare Hub eventi nel portale](./media/event-hubs-create-kafka-enabled/event-hubs-create-event-hubs.png)
 
3. Specificare un nome univoco e abilitare Kafka nello spazio dei nomi. Fare clic su **Create**(Crea).
    
    ![Creare uno spazio dei nomi](./media/event-hubs-create-kafka-enabled/create-kafka-namespace.png)
 
4. Dopo aver creato lo spazio dei nomi, nella scheda **Impostazioni** fare clic su **Criteri di accesso condivisi** per ottenere la stringa di connessione.

    ![Fare clic su Criteri di accesso condivisi](./media/event-hubs-create/create-event-hub7.png)

5. È possibile scegliere il valore predefinito **RootManageSharedAccessKey**, oppure aggiungere un nuovo criterio. Fare clic sul nome del criterio e copiare la stringa di connessione. 
    
    ![Selezionare un criterio](./media/event-hubs-create/create-event-hub8.png)
 
6. Aggiungere questa stringa di connessione alla configurazione dell'applicazione Kafka.

È ora possibile trasmettere gli eventi dalle applicazioni che usano il protocollo Kafka negli Hub eventi.

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>Inviare e ricevere messaggi con Kafka in Hub eventi

1. Clonare gli [Hub eventi di Azure per il repository di Kafka](https://github.com/Azure/azure-event-hubs-for-kafka).

2. Accedere a `azure-event-hubs-for-kafka/quickstart/java/producer`.

3. Aggiornare i dettagli di configurazione per il producer in `src/main/resources/producer.config` come indicato di seguito:

    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```
    
4. Eseguire il codice del producer ed eseguire lo streaming nell'istanza di Hub eventi abilitata per Kafka.
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
    
5. Accedere a `azure-event-hubs-for-kafka/quickstart/java/consumer`.

6. Aggiornare i dettagli di configurazione per il consumer in `src/main/resources/consumer.config` come indicato di seguito:
   
    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```

7. Eseguire il codice del consumer ed eseguire l'elaborazione dall'istanza di Hub eventi abilitata per Kafka usando i client Kafka:

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

Se il cluster Kafka di Hub eventi include eventi, si dovrebbe ora iniziare a riceverli dal consumer.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato illustrato come eseguire lo streaming in un'istanza di Hub eventi abilitata per Kafka senza modificare i client di protocollo o eseguire cluster personalizzati. Per altre informazioni, passare all'esercitazione successiva:

* [Leggere le informazioni su Hub eventi](event-hubs-what-is-event-hubs.md)
* [Leggere le informazioni su Hub eventi per Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Esplorare altri esempi di Hub eventi per Kafka in GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
* Usare [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) per lo [streaming di eventi dall'istanza di Kafka locale all'istanza di Hub eventi abilitata per Kafka nel cloud.](event-hubs-kafka-mirror-maker-tutorial.md)
* Scoprire come eseguire lo streaming negli hub eventi abilitati per Kafka usando [Apache Flink](event-hubs-kafka-flink-tutorial.md) o [Akka Streams](event-hubs-kafka-akka-streams-tutorial.md)
