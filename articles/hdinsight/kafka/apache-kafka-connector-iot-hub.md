---
title: Usare Apache Kafka in HDInsight con l'hub IoT
description: Informazioni su come usare Apache Kafka in HDInsight con l'hub IoT. Il progetto Kafka Connect Azure IoT Hub specifica un connettore di origine e un connettore sink per Kafka. Il connettore di origine può leggere i dati dall'hub IoT mentre il connettore sink scrive nell'hub IoT.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 5559d243573ea04400007cdce0e71009dc91e27a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446448"
---
# <a name="use-apache-kafka-on-hdinsight-with-azure-iot-hub"></a>Usare Apache Kafka in HDInsight con l'hub IoT

Informazioni su come usare il connettore di [Apache Kafka Connect Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) per spostare dati tra Apache Kafka in HDInsight e l'hub IoT di Azure. In questo documento sono incluse informazioni su come eseguire il connettore dell'hub IoT da un nodo perimetrale del cluster.

L'API Kafka Connect consente di implementare i connettori che eseguono in modo continuativo il pull dei dati in Kafka o eseguono il push dei dati da Kafka a un altro sistema. [Apache Kafka Connect Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) è un connettore che esegue il pull dei dati dall'hub IoT di Azure a Kafka. Può anche eseguire il push dei dati da Kafka all'hub IoT. 

Quando si esegue il pull dall'hub IoT, si usa un connettore di __origine__. Quando si esegue il push all'hub IoT, si usa un connettore __sink__. Il connettore hub IoT offre sia il connettore di origine che il connettore sink.

Nel diagramma seguente viene illustrato il flusso di dati tra l'hub IoT e Kafka in HDInsight quando si usa il connettore.

![Immagine che illustra i dati che vanno dall'hub IoT a Kafka tramite il connettore](./media/apache-kafka-connector-iot-hub/iot-hub-kafka-connector-hdinsight.png)

Per altre informazioni sull'API Connect, vedere [https://kafka.apache.org/documentation/#connect](https://kafka.apache.org/documentation/#connect).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

* Un cluster Kafka in HDInsight. Per altre informazioni, vedere il documento [Creare un cluster Kafka in HDInsight](apache-kafka-get-started.md).

* Un nodo perimetrale nel cluster Kafka. Per altre informazioni, vedere il documento [Usare i nodi perimetrali vuoti sui cluster Hadoop in HDInsight](../hdinsight-apps-use-edge-node.md).

* Un hub IoT. Per questo articolo, consiglio il [simulatore online connettere Raspberry Pi all'IoT Hub Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-web-simulator-get-started) documento.

* Un client SSH. La procedura in questo documento usa SSH per la connessione al cluster. Per altre informazioni, vedere il documento [Connettersi a HDInsight (Hadoop) con SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="install-the-connector"></a>Installare il connettore

1. Scaricare la versione più recente di Kafka Connect per l'hub IoT da [https://github.com/Azure/toketi-kafka-connect-iothub/releases/](https://github.com/Azure/toketi-kafka-connect-iothub/releases).

2. Per caricare il file con estensione jar nel nodo perimetrale di Kafka nel cluster HDInsight, usare il comando seguente:

    > [!NOTE]  
    > Sostituire `sshuser` con l'account utente SSH del cluster HDInsight. Sostituire `new-edgenode` con il nome del nodo perimetrale. Sostituire `clustername` con il nome del cluster. Per altre informazioni sull'endpoint SSH per il nodo perimetrale, vedere il documento [Usare i nodi perimetrali vuoti sui cluster Hadoop in HDInsigth](../hdinsight-apps-use-edge-node.md#access-an-edge-node).

    ```bash
    scp kafka-connect-iothub-assembly*.jar sshuser@new-edgenode.clustername-ssh.azurehdinsight.net:
    ```

3. Una volta completata la copia del file, connettersi al nodo perimetrale mediante SSH:

    ```bash
    ssh sshuser@new-edgenode.clustername-ssh.azurehdinsight.net
    ```

    Per altre informazioni, vedere il documento [Connettersi a HDInsight (Hadoop) con SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

4. Per installare il connettore nella directory `libs` di Kafka, usare il comando seguente:

    ```bash
    sudo mv kafka-connect-iothub-assembly*.jar /usr/hdp/current/kafka-broker/libs/
    ```

> [!TIP]  
> Se si verificano problemi con il resto della procedura illustrata in questo documento quando si usa un file con estensione jar preesistente, provare a compilare il pacchetto dall'origine.
>
> Per compilare il connettore, è necessario avere un ambiente di sviluppo Scala con lo [strumento di compilazione Scala](https://www.scala-sbt.org/).
>
> 1. Scaricare l'origine per il connettore da [https://github.com/Azure/toketi-kafka-connect-iothub/](https://github.com/Azure/toketi-kafka-connect-iothub/) nell'ambiente di sviluppo.
>
> 2. A un prompt dei comandi nella directory del progetto, usare il comando seguente per compilare il progetto e includerlo in un pacchetto:
>
>    ```bash
>    sbt assembly
>    ```
>
>    Questo comando crea un file denominato `kafka-connect-iothub-assembly_2.11-0.6.jar` nella directory `target/scala-2.11` del progetto.

## <a name="configure-apache-kafka"></a>Configurare Apache Kafka

Da una connessione SSH al nodo perimetrale, attenersi alla procedura seguente per configurare Kafka per eseguire il connettore in modalità autonoma:

1. Salvare il nome del cluster in una variabile. L'uso di una variabile semplifica l'operazione di copia/incolla degli altri comandi della sezione:

    ```bash
    read -p "Enter the Kafka on HDInsight cluster name: " CLUSTERNAME
    ```

2. Installare l'utilità `jq`. Questa utilità semplifica l'elaborazione dei documenti con estensione json restituiti dalle query Ambari:

    ```bash
    sudo apt -y install jq
    ```

3. Ottenere l'indirizzo dei broker Kafka. Nel cluster possono esserci molti broker, ma è sufficiente fare riferimento a uno o due di essi. Per ottenere l'indirizzo di due host di broker, usare il comando seguente:

    ```bash
    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $KAFKABROKERS
    ```

    Quando richiesto, immettere la password dell'account (admin) di accesso al cluster. Il valore restituito è simile al testo seguente:

    `wn0-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092,wn1-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092`

4. Ottenere l'indirizzo dei nodi Apache ZooKeeper. Esistono diversi nodi Zookeeper nel cluster, ma è sufficiente fare riferimento a uno o due di essi. Per ottenere l'indirizzo di due nodi Zookeeper, usare il comando seguente:

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

5. Quando si esegue il connettore in modalità autonoma, viene usato il file `/usr/hdp/current/kafka-broker/config/connect-standalone.properties` per comunicare con i broker Kafka. Per modificare il file `connect-standalone.properties`, usare il comando seguente:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-standalone.properties
    ```

    * Per configurare la configurazione autonoma per il nodo perimetrale per trovare i broker Kafka, individuare la riga che inizia con `bootstrap.servers=`. Sostituire il valore `localhost:9092` con gli host dei broker del passaggio precedente.

    * Modificare le righe `key.converter=` e `value.converter=` con i valori seguenti:

        ```ini
        key.converter=org.apache.kafka.connect.storage.StringConverter
        value.converter=org.apache.kafka.connect.storage.StringConverter
        ```

        > [!IMPORTANT]  
        > La modifica consente di eseguire test usando il producer di console incluso in Kafka. Potrebbero essere necessari diversi convertitori per altri producer e consumer. Per informazioni sull'uso di altri valori del convertitore, vedere [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

    * Aggiungere una riga alla fine del file contenente il testo seguente:

        ```text
        consumer.max.poll.records=10
        ```

        > [!TIP]  
        > Questa modifica è finalizzata a evitare i timeout nel connettore sink mediante l'impostazione di un limite di 10 record alla volta. Per altre informazioni, vedere [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

6. Per salvare il file, usare __Ctrl + X__, __Y__ e __INVIO__.

7. Per creare gli argomenti usati dal connettore, usare i comandi seguenti:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotin --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotout --zookeeper $KAFKAZKHOSTS
    ```

    Per verificare che gli argomenti `iotin` e `iotout` esistano, usare il comando seguente:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    L'argomento `iotin` viene usato per ricevere messaggi dall'hub IoT. L'argomento `iotout` viene usato per inviare messaggi all'hub IoT.

## <a name="get-iot-hub-connection-information"></a>Ottenere informazioni di connessione dell'hub IoT

Per recuperare informazioni sull'hub IoT usato dal connettore, attenersi alla procedura seguente:

1. Ottenere l'endpoint compatibile con hub eventi e il nome dell'endpoint compatibile con hub eventi per l'hub IoT. Per ottenere tali informazioni, usare uno dei metodi seguenti:

   * __Dal [portale di Azure](https://portal.azure.com/)__ , attenersi alla procedura seguente:

     1. Passare all'hub IoT e selezionare __Endpoint__.
     2. Da __Endpoint predefiniti__, selezionare __Eventi__.
     3. Da __Proprietà__, copiare il valore dei campi seguenti:

         * __Nome compatibile con l'hub eventi__
         * __Endpoint compatibile con l'hub eventi__
         * __Partitions__

        > [!IMPORTANT]  
        > Il valore dell'endpoint del portale può contenere testo aggiuntivo che non è necessario in questo esempio. Estrarre il testo che corrisponde al criterio `sb://<randomnamespace>.servicebus.windows.net/`.

   * __Dall'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__ , usare il comando seguente:

       ```azure-cli
       az iot hub show --name myhubname --query "{EventHubCompatibleName:properties.eventHubEndpoints.events.path,EventHubCompatibleEndpoint:properties.eventHubEndpoints.events.endpoint,Partitions:properties.eventHubEndpoints.events.partitionCount}"
       ```

       Sostituire `myhubname` con il nome dell'hub IoT. La risposta restituita è simile al testo seguente:

       ```json
       "EventHubCompatibleEndpoint": "sb://ihsuprodbnres006dednamespace.servicebus.windows.net/",
       "EventHubCompatibleName": "iothub-ehub-myhub08-207673-d44b2a856e",
       "Partitions": 2
       ```

2. Ottenere i __criteri di accesso condiviso__ e la __chiave__. Per questo esempio, usare la chiave __service__. Per ottenere tali informazioni, usare uno dei metodi seguenti:

    * __Dal [portale di Azure](https://portal.azure.com/)__ , attenersi alla procedura seguente:

        1. Selezionare __Criteri di accesso condiviso__ e quindi __service__.
        2. Copiare il valore di __Chiave primaria__.
        3. Copiare il valore di __Stringa di connessione - chiave primaria__.

    * __Dall'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__ , usare il comando seguente:

        1. Per ottenere il valore della chiave primaria, usare il comando seguente:

            ```azure-cli
            az iot hub policy show --hub-name myhubname --name service --query "primaryKey"
            ```

            Sostituire `myhubname` con il nome dell'hub IoT. La risposta è la chiave primaria dei criteri `service` per l'hub.

        2. Per ottenere la stringa di connessione per i criteri `service`, usare il comando seguente:

            ```azure-cli
            az iot hub show-connection-string --name myhubname --policy-name service --query "connectionString"
            ```

            Sostituire `myhubname` con il nome dell'hub IoT. La risposta è la stringa di connessione per i criteri `service`.

## <a name="configure-the-source-connection"></a>Configurare la connessione di origine

Per configurare l'origine per l'hub IoT, eseguire le azioni seguenti da una connessione SSH al nodo perimetrale:

1. Creare una copia del file `connect-iot-source.properties` nella directory `/usr/hdp/current/kafka-broker/config/`. Per scaricare il file dal progetto toketi-kafka-connect-iothub, usare il comando seguente:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-source.properties
    ```

2. Per modificare il file `connect-iot-source.properties` e aggiungere le informazioni dell'hub IoT, usare il comando seguente:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

    Nell'editor individuare e modificare le voci seguenti:

   * `Kafka.Topic=PLACEHOLDER`: Sostituire  con `iotin`. I messaggi ricevuti dall'hub IoT vengono salvati nell'argomento `iotin`.
   * `IotHub.EventHubCompatibleName=PLACEHOLDER`: sostituire `PLACEHOLDER` con il nome compatibile con l'hub eventi.
   * `IotHub.EventHubCompatibleEndpoint=PLACEHOLDER`: sostituire `PLACEHOLDER` con l'endpoint compatibile con l'hub eventi.
   * `IotHub.Partitions=PLACEHOLDER`: sostituire `PLACEHOLDER` con il numero di partizioni dei passaggi precedenti.
   * `IotHub.AccessKeyName=PLACEHOLDER`: Sostituire  con `service`.
   * `IotHub.AccessKeyValue=PLACEHOLDER`: sostituire `PLACEHOLDER` con la chiave primaria dei criteri `service`.
   * `IotHub.StartType=PLACEHOLDER`: sostituire `PLACEHOLDER` con una data UTC. Tale data indica il momento in cui il connettore inizia a controllare i messaggi. Il formato della data è `yyyy-mm-ddThh:mm:ssZ`.
   * `BatchSize=100`: Sostituire  con `5`. Questa modifica fa sì che il connettore legga i messaggi in Kafka quando sono presenti cinque nuovi messaggi nell'hub IoT.

     Per una configurazione di esempio, vedere [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md).

3. Per salvare le modifiche, usare __CTRL + X__, __Y__ e quindi __INVIO__.

Per altre informazioni sulla configurazione dell'origine del connettore, vedere [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md).

## <a name="configure-the-sink-connection"></a>Configurare la connessione sink

Per configurare la connessione sink per l'hub IoT, eseguire le azioni seguenti da una connessione SSH al nodo perimetrale:

1. Creare una copia del file `connect-iothub-sink.properties` nella directory `/usr/hdp/current/kafka-broker/config/`. Per scaricare il file dal progetto toketi-kafka-connect-iothub, usare il comando seguente:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-sink.properties
    ```

2. Per modificare il file `connect-iothub-sink.properties` e aggiungere le informazioni dell'hub IoT, usare il comando seguente:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
    ```

    Nell'editor individuare e modificare le voci seguenti:

   * `topics=PLACEHOLDER`: Sostituire  con `iotout`. I messaggi scritti nell'argomento `iotout` vengono inoltrati all'hub IoT.
   * `IotHub.ConnectionString=PLACEHOLDER`: sostituire `PLACEHOLDER` con la stringa di connessione per i criteri `service`.

     Per una configurazione di esempio, vedere [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

3. Per salvare le modifiche, usare __CTRL + X__, __Y__ e quindi __INVIO__.

Per altre informazioni sulla configurazione del sink del connettore, vedere [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

## <a name="start-the-source-connector"></a>Avviare il connettore di origine

Per avviare il connettore di origine, usare il comando seguente da una connessione SSH al nodo perimetrale:

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
```

Una volta avviato il connettore, inviare messaggi all'hub IoT dal proprio dispositivo. Quando il connettore legge i messaggi dall'hub IoT e li archivia nell'argomento di Kafka, registra informazioni nella console:

```text
[2017-08-29 20:15:46,112] INFO Polling for data - Obtained 5 SourceRecords from IotHub (com.microsoft.azure.iot.kafka.co
nnect.IotHubSourceTask:39)
[2017-08-29 20:15:54,106] INFO Finished WorkerSourceTask{id=AzureIotHubConnector-0} commitOffsets successfully in 4 ms (
org.apache.kafka.connect.runtime.WorkerSourceTask:356)
```

> [!NOTE]  
> All'avvio del connettore è possibile che vengano visualizzati diversi avvisi. Tali avvisi non causano problemi alla ricezione dei messaggi dall'hub IoT.

Per arrestare il connettore, usare __CTRL + C__.

## <a name="start-the-sink-connector"></a>Avviare il connettore sink

Per avviare il connettore sink in modalità autonoma, usare il comando seguente da una connessione SSH al nodo perimetrale:

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
```

Quando viene avviata l'esecuzione del connettore, vengono visualizzate informazioni simili al testo seguente:

```text
[2017-08-30 17:49:16,150] INFO Started tasks to send 1 messages to devices. (com.microsoft.azure.iot.kafka.connect.sink.
IotHubSinkTask:47)
[2017-08-30 17:49:16,150] INFO WorkerSinkTask{id=AzureIotHubSinkConnector-0} Committing offsets (org.apache.kafka.connec
t.runtime.WorkerSinkTask:262)
```

> [!NOTE]  
> All'avvio del connettore è possibile che vengano visualizzati diversi avvisi. È possibile ignorare questi avvisi.

Per inviare messaggi tramite il connettore, attenersi alla procedura seguente:

1. Aprire un'altra sessione SSH per il cluster Kafka:

    ```bash
    ssh sshuser@new-edgenode.clustername-ssh.azurehdinsight.net
    ```
2. Per inviare messaggi all'argomento `iotout`, usare il comando seguente:

    > [!WARNING]  
    > Poiché si tratta di una nuova connessione SSH, la variabile `$KAFKABROKERS` non contiene alcuna informazione. Per configurarla, usare uno dei metodi seguenti:
    >
    > * Attenersi ai primi tre passaggi della sezione [Configurare Apache Kafka](#configure-apache-kafka).
    > * Usare `echo $KAFKABROKERS` dalla connessione SSH precedente per ottenere i valori e quindi sostituire `$KAFKABROKERS` nel comando seguente con i valori effettivi.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic iotout
    ```

    Questo comando non restituisce valori al prompt di Bash normale. Invece, invia input da tastiera all'argomento `iotout`.

3. Per inviare un messaggio al dispositivo, incollare un documento con estensione json nella sessione SSH per `kafka-console-producer`.

    > [!IMPORTANT]  
    > È necessario impostare il valore della voce `"deviceId"` sull'ID del dispositivo. Nell'esempio seguente il dispositivo è denominato `fakepi`:

    ```json
    {"messageId":"msg1","message":"Turn On","deviceId":"fakepi"}
    ```

    Lo schema per il documento con estensione json è descritto in modo più dettagliato all'indirizzo [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

    Se si usa un dispositivo simulato Raspberry Pi in esecuzione, il dispositivo registra il messaggio seguente:

    ```text
    Receive message: Turn On
    ```

    Inviare nuovamente il documento con estensione json, ma modificare il valore della voce `"message"`. Il nuovo valore viene registrato dal dispositivo.

Per altre informazioni sull'uso del connettore sink, vedere [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

## <a name="next-steps"></a>Passaggi successivi

In questo documento è stato descritto come usare l'API Apache Kafka Connect per avviare il connettore IoT per Kafka in HDInsight. Per trovare altri modi per lavorare con Kafka, vedere i collegamenti seguenti:

* [Usare Apache Spark con Apache Kafka in HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Usare Apache Storm con Apache Kafka in HDInsight](../hdinsight-apache-storm-with-kafka.md)
