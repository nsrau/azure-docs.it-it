---
title: Apache Kafka con registro dello schema Confluent in Azure HDInsight
description: Distribuire un cluster Kafka gestito da HDInsight con un nodo perimetrale all'interno di una rete virtuale e quindi installare il registro schemi Confluent sul nodo perimetrale.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: bb46bc18469638416ff76f84516498e0076c85fd
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2020
ms.locfileid: "95500323"
---
# <a name="apache-kafka-with-confluent-schema-registry-in-azure-hdinsight"></a>Apache Kafka con registro dello schema Confluent in Azure HDInsight

Il registro dello schema Kafka fornisce i serializzatori che si collegano ai client Apache Kafka che gestiscono l'archiviazione e il recupero dello schema dei messaggi per i messaggi Kafka inviati nel formato avro. Si trattava di un progetto OSS di confluenti, ma ora è sotto la [licenza community Confluent](https://www.confluent.io/blog/license-changes-confluent-platform/). Il registro dello schema offre inoltre i seguenti scopi:

* Archiviare e recuperare schemi per Producer e consumer.
* Applicare la compatibilità/full indietro/avanti negli argomenti.
* Ridurre le dimensioni del payload inviato a Kafka.

In un cluster Kafka gestito da HDInsight, il registro dello schema viene in genere distribuito in un nodo perimetrale per consentire la separazione dei calcoli dai nodi head.

Di seguito è riportata un'architettura rappresentativa del modo in cui viene distribuito il registro dello schema in un cluster HDInsight. Il registro dello schema espone in modo nativo un'API REST per le operazioni su di esso.  I producer e i consumer possono interagire con il registro dello schema dall'interno di VNet o tramite il [proxy Rest Kafka](rest-proxy.md).

![Registro dello schema di HDInsight Kafka](./media/schema-registry/pic1.png)

## <a name="deploy-an-hdinsight-managed-kafka-with-confluent-schema-registry"></a>Distribuire un HDInsight gestito Kafka con registro dello schema Confluent

In questa sezione viene distribuito un cluster Kafka gestito da HDInsight con un nodo perimetrale all'interno di una rete virtuale e quindi viene installato il registro di sistema dello schema Confluent sul nodo perimetrale.  

1. Selezionare il pulsante **Distribuisci in Azure** seguente per accedere ad Azure e aprire il modello di Resource Manager.

    [![Distribuzione in Azure](./media/schema-registry/hdi-deploy-to-azure1.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Farnabganguly%2FKafkaschemaregistry%2Fmaster%2Fazuredeploy.json)

1. Nel modello di distribuzione personalizzata compilare i campi come descritto di seguito:

    |Proprietà |Descrizione |
    |---|---|
    |Subscription|Nell'elenco a discesa selezionare la sottoscrizione di Azure che viene usata per il cluster.|
    |Resource group|Nell'elenco a discesa selezionare il gruppo di risorse esistente oppure selezionare **Crea nuovo**.|
    |Region|Nell'elenco a discesa selezionare un'area in cui viene creato il cluster.|
    |Cluster Name|Immettere un nome univoco globale. In alternativa, lasciare invariato per utilizzare il nome predefinito.|
    |Nome utente dell'account di accesso del cluster|Specificare il nome utente. Il valore predefinito è **admin**.|
    |Password di accesso al cluster|Specificare la password.|
    |Nome utente SSH|Specificare il nome utente. il valore predefinito è **sshuser**.|
    |Password SSH|Specificare la password.|

    Lasciare gli altri campi così come sono. Selezionare **Rivedi e crea** per continuare.

1. Esaminare i dettagli della distribuzione, quindi selezionare **Crea** per inizializzare la distribuzione. Il completamento della distribuzione può richiedere 45 minuti.

## <a name="configure-the-confluent-schema-registry"></a>Configurare il registro di sistema dello schema Confluent

In questa sezione viene configurato il registro di sistema di schema Kafka confluenti installato nel nodo perimetrale. Il registro di sistema dello schema Confluent si trova in  `/etc/schema-registry/schema-registry.properties` e i meccanismi per avviare e arrestare i file eseguibili del servizio si trovano nella  `/usr/bin/` cartella.

Il registro dello schema deve essere a conoscenza del servizio Zookeeper che può interagire con il cluster HDInsight Kafka. Seguire questa procedura per ottenere i dettagli del quorum Zookeeper.

1. Usare il [comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) per connettersi al nodo perimetrale del cluster. Modificare il comando seguente sostituendo CLUSTERNAME con il nome del cluster in uso e quindi immettere il comando:

    ```cmd
    ssh sshuser@schema-registry.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Configurare la variabile di password. Sostituire PASSWORD con la password di accesso del cluster, quindi immettere il comando:

    ```bash
    export password='PASSWORD'
    ```

1. Estrarre il nome del cluster con l'uso corretto di maiuscole e minuscole. Eseguire il comando seguente:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
    ```

1. Estrarre gli host Kafka Zookeeper. Eseguire il comando seguente:

    ```bash
    export KAFKAZKHOSTS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2);

    echo $KAFKAZKHOSTS
    ```

    Prendere nota di questo valore perché verrà usato in un secondo momento.

1. Estrarre gli host del broker Kafka. Eseguire il comando seguente:

    ```bash
    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);

    echo $KAFKABROKERS
    ```

1. Aprire i file delle proprietà del registro di sistema in modalità di modifica. Eseguire il comando seguente:

    ```bash
    sudo nano /etc/schema-registry/schema-registry.properties
    ```

    1. Aggiornare il valore di `kafkastore.connection.url` con la stringa Zookeeper identificata in precedenza.
    1. Aggiornare il valore per `debug` a `true` .

    Il file delle proprietà ha ora un aspetto simile al seguente:  

    ```
    listeners=http://0.0.0.0:8081
    kafkastore.connection.url=zk1-ag4kaf.q2hwzr1xkxjuvobkaagmjjkhta.gx.internal.cloudapp.net:2181,zk2-ag4kaf.q2hwzr1xkxjuvobkaagmjjkhta.gx.internal.cloudapp.net:2181
    kafkastore.topic=_schemas
    debug=true
    ```

1. Per salvare il file, usare **Ctrl + X**, **Y** e **INVIO**.

1. Avviare il registro dello schema e puntare a usare il file delle proprietà del registro di sistema aggiornato. Eseguire i comandi seguenti:

    ```bash
    cd /bin
    sudo schema-registry-start /etc/schema-registry/schema-registry.properties
    ```

1. Con il registro dello schema in esecuzione in una sessione SSH, avviare un'altra finestra SSH.  Registrare una nuova versione di uno schema nel soggetto "Kafka-Key" e prendere nota dell'output:

    ```cmd
    curl -X POST -i -H "Content-Type: application/vnd.schemaregistry.v1+json" \
        --data '{"schema": "{\"type\": \"string\"}"}'
    ```

    ```output
       HTTP/1.1 200 OK
    Date: Sun, 22 Mar 2020 16:33:04 GMT
    Content-Type: application/vnd.schemaregistry.v1+json
    Content-Length: 9
    Server: Jetty(9.2.24.v20180105)
    ```

1. Registrare una nuova versione di uno schema nel soggetto "Kafka-value" e prendere nota dell'output:

    ```cmd
    curl -X POST -i -H "Content-Type: application/vnd.schemaregistry.v1+json" \
        --data '{"schema": "{\"type\": \"string\"}"}' \
    ```

    ```output
    HTTP/1.1 200 OK
    Date: Sun, 22 Mar 2020 16:34:18 GMT
    Content-Type: application/vnd.schemaregistry.v1+json
    Content-Length: 9
    Server: Jetty(9.2.24.v20180105)
    ```

1. Elencare tutti gli argomenti e controllare l'output:

    ```cmd
    curl -X GET -i -H "Content-Type: application/vnd.schemaregistry.v1+json" \
        http://localhost:8081/subjects
    ```

    ```output
    HTTP/1.1 200 OK
    Date: Sun, 22 Mar 2020 16:34:39 GMT
    Content-Type: application/vnd.schemaregistry.v1+json
    Content-Length: 27
    Server: Jetty(9.2.24.v20180105)

    ["Kafka-value","Kafka-key"]
    ```

Si consiglia di provare altri [comandi avanzati elencati qui](https://docs.confluent.io/1.0/schema-registry/docs/intro.html#quickstart).

## <a name="send-and-consume-avro-data-from-kafka-using-schema-registry"></a>Inviare e usare i dati avro da Kafka usando il registro schemi

In questa sezione i dati verranno letti dall'input standard e scritti in un argomento Kafka in un formato. Verranno quindi letti i dati dagli argomenti Kafka usando un consumer con un formattatore avro per trasformare i dati in un formato leggibile.

1. Creare un argomento Kafka `agkafkaschemareg` .

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic agkafkaschemareg --zookeeper $KAFKAZKHOSTS
    ```

1. Usare il **Producer console Kafka avro** per creare uno schema, assegnare lo schema all'argomento e iniziare a inviare i dati all'argomento in formato avro. Assicurarsi che l'argomento Kafka nel passaggio precedente sia stato creato correttamente e che **$KAFKABROKERS** disponga di un valore.

    Lo schema che si sta inviando è una coppia chiave-valore

    ```
    Value
    {
      "type": "record",
      "name": "example_schema",
      "namespace": "com.example",
      "fields": [
        {
          "name": "cust_id",
          "type": "int",
          "doc": "Id of the customer account"
        },
        {
          "name": "year",
          "type": "int",
          "doc": "year of expense"
        },
        {
          "name": "expenses",
          "type": {
            "type": "array",
            "items": "float"
          },
          "doc": "Expenses for the year"
        }
      ],
      "doc:": "A basic schema for storing messages"
    }
    ```

    Usare il comando seguente per avviare il **Producer della console Kafka avro**:

    ```bash
    /usr/bin/kafka-avro-console-producer     --broker-list $KAFKABROKERS     --topic agkafkaschemareg     --property parse.key=true --property key.schema='{"type" : "int", "name" : "id"}'     --property value.schema='{ "type" : "record", "name" : "example_schema", "namespace" : "com.example", "fields" : [ { "name" : "cust_id", "type" : "int", "doc" : "Id of the customer account" }, { "name" : "year", "type" : "int", "doc" : "year of expense" }, { "name" : "expenses", "type" : {"type": "array", "items": "float"}, "doc" : "Expenses for the year" } ], "doc:" : "A basic schema for storing messages" }'
    ```

1. Quando il Producer è pronto ad accettare i messaggi, iniziare a inviare i messaggi nel formato di schema avro predefinito. Usare il tasto TAB per creare spaziatura tra la chiave e il valore.

    ```
    1 TAB {"cust_id":1313131, "year":2012, "expenses":[1313.13, 2424.24]}
    2 TAB {"cust_id":3535353, "year":2011, "expenses":[761.35, 92.18, 14.41]}
    3 TAB {"cust_id":7979797, "year":2011, "expenses":[4489.00]}
    ```

1. Provare a immettere dati casuali non di schema nel Producer della console per vedere come il Producer consente ora tutti i dati che non sono conformi allo schema avro predefinito.

    ```
    1       {"cust_id":1313131, "year":2012, "expenses":[1313.13, 2424.24]}
    2       {"cust_id":1313131,"cust_age":34 "year":2012, "expenses":[1313.13, 2424.24,34.212]}
    org.apache.kafka.common.errors.SerializationException: Error deserializing json {"cust_id":1313131,"cust_age":34 "year":2012, "expenses":[1313.13, 2424.24,34.212]} to Avro of schema {"type":"record","name":"example_schema","namespace":"com.example","fields":[{"name":"cust_id","type":"int","doc":"Id of the customer account"},{"name":"year","type":"int","doc":"year of expense"},{"name":"expenses","type":{"type":"array","items":"float"},"doc":"Expenses for the year"}],"doc:":"A basic schema for storing messages"}
    Caused by: org.codehaus.jackson.JsonParseException: Unexpected character ('"' (code 34)): was expecting comma to separate OBJECT entries
     at [Source: java.io.StringReader@3561c410; line: 1, column: 35]
            at org.codehaus.jackson.JsonParser._constructError(JsonParser.java:1433)
            at org.codehaus.jackson.impl.JsonParserMinimalBase._reportError(JsonParserMinimalBase.java:521)
            at org.codehaus.jackson.impl.JsonParserMinimalBase._reportUnexpectedChar(JsonParserMinimalBase.java:442)
            at org.codehaus.jackson.impl.ReaderBasedParser.nextToken(ReaderBasedParser.java:406)
            at org.apache.avro.io.JsonDecoder.getVaueAsTree(JsonDecoder.java:549)
            at org.apache.avro.io.JsonDecoder.doAction(JsonDecoder.java:474)
            at org.apache.avro.io.parsing.Parser.advance(Parser.java:88)
            at org.apache.avro.io.JsonDecoder.advance(JsonDecoder.java:139)
            at org.apache.avro.io.JsonDecoder.readInt(JsonDecoder.java:166)
            at org.apache.avro.io.ValidatingDecoder.readInt(ValidatingDecoder.java:83)
            at org.apache.avro.generic.GenericDatumReader.readInt(GenericDatumReader.java:511)
            at org.apache.avro.generic.GenericDatumReader.readWithoutConversion(GenericDatumReader.java:182)
            at org.apache.avro.generic.GenericDatumReader.read(GenericDatumReader.java:152)
            at org.apache.avro.generic.GenericDatumReader.readField(GenericDatumReader.java:240)
            at org.apache.avro.generic.GenericDatumReader.readRecord(GenericDatumReader.java:230)
            at org.apache.avro.generic.GenericDatumReader.readWithoutConversion(GenericDatumReader.java:174)
            at org.apache.avro.generic.GenericDatumReader.read(GenericDatumReader.java:152)
            at org.apache.avro.generic.GenericDatumReader.read(GenericDatumReader.java:144)
            at io.confluent.kafka.formatter.AvroMessageReader.jsonToAvro(AvroMessageReader.java:213)
            at io.confluent.kafka.formatter.AvroMessageReader.readMessage(AvroMessageReader.java:200)
            at kafka.tools.ConsoleProducer$.main(ConsoleProducer.scala:59)
            at kafka.tools.ConsoleProducer.main(ConsoleProducer.scala)
    
    ```

1. In una schermata diversa, avviare il consumer della console Kafka avro

    ```bash
    sudo /usr/bin/kafka-avro-console-consumer --bootstrap-server $KAFKABROKERS --topic agkafkaschemareg --from-beginning
    ```

    Si dovrebbe iniziare a vedere l'output seguente:

    ```output
    {"cust_id":1313131,"year":2012,"expenses":[1313.13,2424.24]}
    {"cust_id":7979797,"year":2011,"expenses":[4489.0]}
    {"cust_id":3535353,"year":2011,"expenses":[761.35,92.18,14.41]}
    ```

## <a name="next-steps"></a>Passaggi successivi

* [Usare le API Producer e consumer Apache Kafka](apache-kafka-producer-consumer-api.md)