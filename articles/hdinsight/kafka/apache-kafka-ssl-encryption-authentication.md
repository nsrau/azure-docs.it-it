---
title: Apache Kafka TLS encryption & authentication - Azure HDInsight
description: Impostare la crittografia TLS per la comunicazione tra i client Kafka e broker Kafka, nonché tra i broker Kafka. Configurare l'autenticazione SSL dei client.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 05/01/2019
ms.openlocfilehash: 02b64d77a4fb1af25e1022de3ac8e4775f916d9e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261772"
---
# <a name="set-up-tls-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Configurare la crittografia e l'autenticazione TLS per Apache Kafka in Azure HDInsightSet up TLS encryption and authentication for Apache Kafka in Azure HDInsight

In questo articolo viene illustrato come configurare la crittografia TLS (Transport Layer Security), precedentemente nota come crittografia SSL (Secure Sockets Layer), tra client Apache Kafka e broker Apache Kafka. Viene inoltre illustrato come impostare l'autenticazione dei client (talvolta definito TLS bidirezionale).

> [!Important]
> Esistono due client che è possibile utilizzare per le applicazioni Kafka: un client Java e un client console. Solo il `ProducerConsumer.java` client Java può utilizzare TLS sia per la produzione che per l'utilizzo. Il client `console-producer.sh` di produzione della console non funziona con TLS.

> [!Note]
> Il produttore della console Kafka HDInsight con la versione 1.1 non supporta SSL.

## <a name="apache-kafka-broker-setup"></a>Configurazione del broker Apache Kafka

L'installazione del broker Kafka TLS utilizzerà quattro macchine virtuali del cluster HDInsight nel modo seguente:The Kafka TLS broker setup will use four HDInsight cluster VMs in the following way:

* headnode 0 - Autorità di certificazione (CA)
* nodo lavoratore 0, 1 e 2 - broker

> [!Note] 
> Per questa guida verranno usati certificati autofirmati, ma la soluzione più sicura consiste nell'usare certificati emessi da CA attendibili.

Il riepilogo del processo di installazione del broker è il seguente:

1. I passaggi seguenti vengono ripetuti in ognuno dei tre nodi di lavoro:

    1. Generare un certificato.
    1. Creare una richiesta di firma del certificato.
    1. Inviare la richiesta di firma del certificato all'autorità di certificazione (CA).
    1. Accedere alla CA e firmare la richiesta.
    1. SCP il certificato firmato torna al nodo di lavoro.
    1. SCP il certificato pubblico della CA al nodo di lavoro.

1. Una volta che hai tutti i certificati, inserire i certificati nell'archivio certificati.
1. Vai su Ambari e modifica le configurazioni.

Utilizzare le seguenti istruzioni dettagliate per completare la configurazione del broker:

> [!Important]
> Nei frammenti di codice seguenti wnX è un'abbreviazione di uno `wn0`dei `wn1` `wn2` tre nodi di lavoro e deve essere sostituito con , o in base alle esigenze. `WorkerNode0_Name`e `HeadNode0_Name` devono essere sostituiti con i nomi delle rispettive macchine.

1. Eseguire l'installazione iniziale nel nodo head 0, che per HDInsight ricoprirà il ruolo dell'autorità di certificazione (CA).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Eseguire la stessa configurazione iniziale su ciascuno dei broker (nodi di lavoro 0, 1 e 2).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. In ognuno dei nodi di lavoro eseguire i passaggi seguenti usando il frammento di codice seguente.
    1. Creare un archivio chiavi e popolarlo con un nuovo certificato privato.
    1. Creare una richiesta di firma del certificato.
    1. SCP la richiesta di firma del certificato alla CA (headnode0)

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. Nel computer CA eseguire il comando seguente per creare file ca-cert e ca-key:

    ```bash
    openssl req -new -newkey rsa:4096 -days 365 -x509 -subj "/CN=Kafka-Security-CA" -keyout ca-key -out ca-cert -nodes
    ```

1. Passare al computer CA e firmare tutte le richieste di firma del certificato ricevuto:

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. Inviare i certificati firmati ai nodi di lavoro dalla CA (headnode0).

    ```bash
    scp wn0-cert-signed sshuser@WorkerNode0_Name:~/ssl/cert-signed
    scp wn1-cert-signed sshuser@WorkerNode1_Name:~/ssl/cert-signed
    scp wn2-cert-signed sshuser@WorkerNode2_Name:~/ssl/cert-signed
    ```

1. Inviare il certificato pubblico della CA a ogni nodo di lavoro.

    ```bash
    scp ca-cert sshuser@WorkerNode0_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode1_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode2_Name:~/ssl/ca-cert
    ```

1. In ogni nodo di lavoro aggiungere il certificato pubblico CA all'attendibile store e all'archivio chiavi. Aggiungere quindi il certificato firmato del nodo worker all'archivio chiavi

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-tls-and-restart-brokers"></a>Aggiornare la configurazione Kafka per utilizzare TLS e riavviare i broker

A questo punto è stato impostato ogni broker Kafka con un archivio chiavi e un truststore e sono stati importati i certificati corretti. A questo punto, modificare le proprietà di configurazione Kafka correlate tramite Ambari e quindi riavviare i broker Kafka.

Per completare la configurazione, seguire questa procedura:

1. Accedere al portale di Azure e selezionare il cluster Apache Kafka di Azure HDInsight.
1. Passare all'interfaccia utente di Ambari facendo clic su **Home Ambari** in **Dashboard cluster**.
1. In **Kafka Broker** (Broker Kafka) impostare la proprietà **listeners** su `PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. In **Advanced kafka-broker** (Kafka-broker avanzato) impostare la proprietà **security.inter.broker.protocol** su `SSL`

    ![Modifica delle proprietà di configurazione SSL di Kafka in Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. In **Custom kafka-broker** (Kafka-broker personalizzato) impostare la proprietà **ssl.client.auth** su `required`. Questo passaggio è necessario solo se si configurano l'autenticazione e la crittografia.

    ![Modifica delle proprietà di configurazione SSL di Kafka in Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. Per HDI versione 3.6, andare a Ambari UI e aggiungere le seguenti configurazioni sotto **Advanced kafka-env** e la proprietà **del modello kafka-env.**

    ```bash
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092,SSL://$IP_ADDRESS:9093" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.key.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. Ecco lo screenshot che mostra l'interfaccia utente di configurazione di Ambari con queste modifiche.

    Per HDI versione 3.6:

    ![Modifica della proprietà del modello kafka-env in Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env.png)

    Per HDI versione 4.0:

     ![Modifica della proprietà del modello kafka-env in Ambari four](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env-four.png)

1. Riavviare tutti i broker Kafka.

## <a name="client-setup-without-authentication"></a>Configurazione del client (senza autenticazione)

Se non è necessaria l'autenticazione, il riepilogo dei passaggi per configurare solo la crittografia TLS è:

1. Accedere alla CA (nodo head attivo).
1. Copiare il certificato CA nel computer client dal computer CA (wn0).
1. Accedere al computer client (hn1) e `~/ssl` passare alla cartella.
1. Importare il certificato CA nell'attendibilità.
1. Importare il certificato CA nell'archivio chiavi.

Questi passaggi sono descritti in dettaglio nei frammenti di codice seguenti.

1. Accedere al nodo della CA.

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    ```

1. Copiare il ca-cert nel computer client

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

1. Accedere al computer client (nodo head standby).

    ```bash
    ssh sshuser@HeadNode1_Name
    cd ssl
    ```

1. Importare il certificato CA nell'attendibilità dell'archivio di trust.

    ```bash
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Importare il certificato CA in keystore.
    
    ```bash
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Creare il `client-ssl-auth.properties` file nel computer client (hn1) . Dovrebbe includere le righe seguenti:

    ```config
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ```

1. Avviare il client di amministrazione con opzioni producer e consumer per verificare che sia i produttori che i consumatori stiano lavorando sulla porta 9093. Fare riferimento alla sezione [Verifica](apache-kafka-ssl-encryption-authentication.md#verification) riportata di seguito per i passaggi necessari per verificare la configurazione tramite produttore/consumer della console.

## <a name="client-setup-with-authentication"></a>Configurazione del client (con autenticazione)

> [!Note]
> I passaggi seguenti sono necessari solo se si configurano sia la **crittografia** TLS che l'autenticazione. Se si configura solo la crittografia, vedere [Configurazione client senza autenticazione](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication).

I quattro passaggi seguenti riepilogano le attività necessarie per completare l'installazione del client:

1. Accedere al computer client (nodo head standby).
1. Creare un archivio chiavi Java e ottenere un certificato firmato per il broker. Copiare quindi il certificato nella macchina virtuale in cui è in esecuzione la CA.
1. Passare al computer CA (nodo head attivo) per firmare il certificato client.
1. Passare al computer client (nodo head di `~/ssl` standby) e passare alla cartella. Copiare il certificato firmato nel computer client.

I dettagli di ogni passaggio sono riportati di seguito.

1. Accedere al computer client (nodo head standby).

    ```bash
    ssh sshuser@HeadNode1_Name
    ```

1. Rimuovere qualsiasi directory ssl esistente.

    ```bash
    rm -R ~/ssl
    mkdir ssl
    cd ssl
    ```

1. Creare un keystore java e creare una richiesta di firma del certificato. 

    ```bash
    keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -dname "CN=HEADNODE1_FQDN" -storetype pkcs12
    
    keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -storepass "MyClientPassword123" -keypass "MyClientPassword123"
    ```

1. Copiare la richiesta di firma del certificato nella CA

    ```bash
    scp client-cert-sign-request sshuser@HeadNode0_Name:~/ssl/client-cert-sign-request
    ```

1. Passare al computer CA (nodo head attivo) e firmare il certificato client.

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    openssl x509 -req -CA ca-cert -CAkey ca-key -in ~/ssl/client-cert-sign-request -out ~/ssl/client-cert-signed -days 365 -CAcreateserial -passin pass:MyClientPassword123
    ```

1. Copiare il certificato client firmato dalla CA (nodo head attivo) al computer client.

    ```bash
    scp client-cert-signed sshuser@HeadNode1_Name:~/ssl/client-signed-cert
    ```

1. Copiare il ca-cert nel computer client

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

    1. Accedere al computer client (nodo head di standby) e passare alla directory ssl.

    ```bash
    ssh sshuser@HeadNode1_Name
    cd ssl
    ```

1. Creare l'archivio client con un certificato firmato e importare ca cert nell'archivio chiavi e truststore nel computer client (hn1):

    ```bash
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    
    keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Creare un `client-ssl-auth.properties` file nel computer client (hn1) . Dovrebbe includere le righe seguenti:

    ```bash
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
    ssl.keystore.password=MyClientPassword123
    ssl.key.password=MyClientPassword123
    ```

## <a name="verification"></a>Verifica

Eseguire questi passaggi nel computer client.

> [!Note]
> Se HDInsight 4.0 e Kafka 2.1 sono installati, è possibile usare il producer/consumer della console per verificare la configurazione. In caso contrario, eseguire il producer Kafka sulla porta 9092 e inviare messaggi all'argomento, quindi utilizzare il consumer Kafka sulla porta 9093 che utilizza TLS.

### <a name="kafka-21-or-above"></a>Kafka 2.1 o superiore

1. Creare un argomento se non esiste già.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1. Avviare il produttore `client-ssl-auth.properties` della console e fornire il percorso come file di configurazione per il producer.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9093 --topic topic1 --producer.config ~/ssl/client-ssl-auth.properties
    ```

1. Aprire un'altra connessione ssh al computer client `client-ssl-auth.properties` e avviare il consumer della console e fornire il percorso come file di configurazione per il consumer.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

### <a name="kafka-11"></a>Kafka 1.1

1. Creare un argomento se non esiste già.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE_0>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1. Avviare il produttore della console e fornire il percorso di client-ssl-auth.properties come file di configurazione per il producer.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9092 --topic topic1 
    ```

1. Aprire un'altra connessione ssh al computer client `client-ssl-auth.properties` e avviare il consumer della console e fornire il percorso come file di configurazione per il consumer.

    ```bash
    $ /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

## <a name="next-steps"></a>Passaggi successivi

* [Che cosa è Apache Kafka in HDInsight?](apache-kafka-introduction.md)
