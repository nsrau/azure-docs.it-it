---
title: 'Avvio rapido: Inserire dati da Kafka in Esplora dati di Azure'
description: Questa guida introduttiva descrive come inserire (caricare) i dati in Esplora dati di Azure da Kafka.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 11/19/2018
ms.openlocfilehash: 180053aa2146d8ab80df832044ac3ee4d45970da
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59047110"
---
# <a name="quickstart-ingest-data-from-kafka-into-azure-data-explorer"></a>Avvio rapido: Inserire dati da Kafka in Esplora dati di Azure
 
Esplora dati di Azure è un servizio di esplorazione dati rapido e a scalabilità elevata per dati di log e di telemetria. Esplora dati di Azure offre l'inserimento (caricamento dei dati) da Kafka. Kafka è una piattaforma di streaming distribuito che consente di creare pipeline di dati in streaming in tempo reale che spostano in modo affidabile i dati tra applicazioni o sistemi.
 
## <a name="prerequisites"></a>Prerequisiti
 
* Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare. 
 
* [Un cluster e un database di test](create-cluster-database-portal.md)
 
* [Un'app di esempio](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/kafka) che genera i dati e li invia a Kafka

* [Visual Studio 2017 versione 15.3.2 o successiva](https://www.visualstudio.com/vs/) per eseguire l'app di esempio
 
## <a name="kafka-connector-setup"></a>Installazione del connettore Kafka

Kafka Connect è uno strumento per lo streaming dei dati scalabile e affidabile tra Apache Kafka e altri sistemi. Rende più semplice definire rapidamente i connettori che gestiscono lo spostamento di grandi raccolte di dati all'interno e all'esterno di Kafka. Kafka Sink di ADX funge da connettore con Kafka.
 
### <a name="bundle"></a>Bundle

Kafka è in grado di caricare un file `.jar` come plug-in che funge da connettore personalizzato. Per generare questo file `.jar`, si clonerà il codice localmente e lo si compilerà usando Maven. 

#### <a name="clone"></a>Clone

```bash
git clone git://github.com:Azure/kafka-sink-azure-kusto.git
cd ./kafka-sink-azure-kusto/kafka/
```

#### <a name="build"></a>Compilare

Compilare in locale con Maven per produrre un file `.jar` completo di dipendenze.

* JDK >= 1.8 [download](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Maven [download](https://maven.apache.org/install.html)
 

All'interno della directory radice *kafka-sink-azure-kusto* eseguire:

```bash
mvn clean compile assembly:single
```

### <a name="deploy"></a>Distribuire 

Caricare il plug-in Kafka. Un esempio di distribuzione che usa Docker è reperibile in [kafka-sink-azure-kusto](https://github.com/Azure/kafka-sink-azure-kusto#deploy)
 

Documentazione dettagliata sui connettori Kafka e la loro distribuzione è reperibile in [Kafka Connect](https://kafka.apache.org/documentation/#connect) 

### <a name="example-configuration"></a>Configurazione di esempio 
 
```config
name=KustoSinkConnector 
connector.class=com.microsoft.azure.kusto.kafka.connect.sink.KustoSinkConnector 
kusto.sink.flush_interval_ms=300000 
key.converter=org.apache.kafka.connect.storage.StringConverter 
value.converter=org.apache.kafka.connect.storage.StringConverter 
tasks.max=1 
topics=testing1 
kusto.tables.topics_mapping=[{'topic': 'testing1','db': 'daniel', 'table': 'TestTable','format': 'json', 'mapping':'TestMapping'}] 
kusto.auth.authority=XXX 
kusto.url=https://ingest-{mycluster}.kusto.windows.net/ 
kusto.auth.appid=XXX 
kusto.auth.appkey=XXX 
kusto.sink.tempdir=/var/tmp/ 
kusto.sink.flush_size=1000
```
 
## <a name="create-a-target-table-in-adx"></a>Creare una tabella di destinazione in ADX
 
Creare una tabella in ADX a cui Kafka può inviare dati. Creare la tabella nel cluster e nel database di cui è stato effettuato il provisioning in **Prerequisiti**.
 
1. Nel portale di Azure passare al cluster e selezionare **Query**.
 
    ![Collegamento all'applicazione di query](media/ingest-data-event-hub/query-explorer-link.png)
 
1. Copiare il comando seguente nella finestra e selezionare **Esegui**.
 
    ```Kusto
    .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
    ```
 
    ![Esecuzione della creazione di query](media/ingest-data-event-hub/run-create-query.png)
 
1. Copiare il comando seguente nella finestra e selezionare **Esegui**.
 
    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.timeStamp","datatype":"datetime"},{"column":"Name","path":"$.name","datatype":"string"},{"column":"Metric","path":"$.metric","datatype":"int"},{"column":"Source","path":"$.source","datatype":"string"}]'
    ```

    Questo comando esegue il mapping dei dati JSON in ingresso ai nomi di colonna e ai tipi di dati della tabella (TestTable).


## <a name="generate-sample-data"></a>Generare i dati di esempio

Ora che il cluster Kafka è connesso a ADX, usare l'[app di esempio](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) scaricata per generare dati.

### <a name="clone"></a>Clone

Clonare l'app di esempio in locale:

```cmd
git clone git://github.com:Azure/azure-kusto-samples-dotnet.git
cd ./azure-kusto-samples-dotnet/kafka/
```

### <a name="run-the-app"></a>Esecuzione dell'app

1. Aprire la soluzione dell'app di esempio in Visual Studio.

1. Nel file `Program.cs` aggiornare la costante `connectionString` assegnandole la stringa di connessione a Kafka.

    ```csharp    
    const string connectionString = @"<YourConnectionString>";
    ```

1. Compilare ed eseguire l'app. L'app invia messaggi al cluster Kafka e visualizza lo stato ogni 10 secondi.

1. Dopo che l'app ha inviato alcuni messaggi, procedere con il passaggio successivo.
 
## <a name="query-and-review-the-data"></a>Eseguire una query ed esaminare i dati

1. Per assicurarsi non si siano verificati errori durante l'inserimento:

    ```Kusto
    .show ingestion failures
    ```

1. Per visualizzare i dati appena inseriti:

    ```Kusto
    TestTable 
    | count
    ```

1. Per visualizzare il contenuto dei messaggi:
 
    ```Kusto
    TestTable
    ```
 
    Il set di risultati dovrebbe essere simile al seguente:
 
    ![Set di risultati dei messaggi](media/ingest-data-event-hub/message-result-set.png)
 
## <a name="next-steps"></a>Passaggi successivi
 
> [!div class="nextstepaction"]
> [Guida introduttiva: Eseguire query sui dati in Esplora dati di Azure](web-query-data.md)
