---
title: Apache Kafka la crittografia TLS & autenticazione-Azure HDInsight
description: Configurare la crittografia TLS per la comunicazione tra i client Kafka e i broker Kafka, oltre che tra i broker Kafka. Configurare l'autenticazione SSL dei client.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 05/01/2019
ms.openlocfilehash: 9a95970647a26ea80db9f63fb8523c6a65cc5e06
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86082081"
---
# <a name="set-up-tls-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Configurare la crittografia e l'autenticazione TLS per Apache Kafka in Azure HDInsight

Questo articolo illustra come configurare la crittografia Transport Layer Security (TLS), nota in precedenza come crittografia Secure Sockets Layer (SSL) tra client Apache Kafka e broker Apache Kafka. Viene inoltre illustrato come configurare l'autenticazione dei client (talvolta definita TLS bidirezionale).

> [!Important]
> Sono disponibili due client che è possibile usare per le applicazioni Kafka: un client Java e un client console. Solo il client Java `ProducerConsumer.java` può usare TLS per la produzione e l'utilizzo. Il client Producer console non `console-producer.sh` funziona con TLS.

> [!Note]
> Il producer console Kafka HDInsight con versione 1,1 non supporta SSL.

## <a name="apache-kafka-broker-setup"></a>Installazione di Apache Kafka broker

Il programma di installazione di Kafka TLS broker utilizzerà quattro macchine virtuali del cluster HDInsight nel modo seguente:

* nodo Head 0-autorità di certificazione (CA)
* nodo del ruolo di lavoro 0, 1 e 2-broker

> [!Note] 
> Per questa guida verranno usati certificati autofirmati, ma la soluzione più sicura consiste nell'usare certificati emessi da CA attendibili.

Il riepilogo del processo di installazione di Service Broker è il seguente:

1. I passaggi seguenti vengono ripetuti in ognuno dei tre nodi di lavoro:

    1. Generare un certificato.
    1. Creare una richiesta di firma del certificato.
    1. Inviare la richiesta di firma del certificato all'autorità di certificazione (CA).
    1. Accedere alla CA e firmare la richiesta.
    1. SCP il certificato firmato di nuovo nel nodo di lavoro.
    1. SCP il certificato pubblico dell'autorità di certificazione per il nodo di lavoro.

1. Una volta tutti i certificati, inserire i certificati nell'archivio certificati.
1. Passare a Ambari e modificare le configurazioni.

Usare le istruzioni dettagliate seguenti per completare la configurazione di Service Broker:

> [!Important]
> Nei frammenti di codice seguenti wnX è un'abbreviazione per uno dei tre nodi di lavoro e deve essere sostituito con `wn0` `wn1` o `wn2` in base alle esigenze. `WorkerNode0_Name`e `HeadNode0_Name` devono essere sostituiti con i nomi dei rispettivi computer.

1. Eseguire la configurazione iniziale sul nodo Head 0, che per HDInsight riempirà il ruolo dell'autorità di certificazione (CA).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Eseguire la stessa configurazione iniziale in ogni broker (nodi del ruolo di lavoro 0, 1 e 2).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. In ogni nodo del ruolo di lavoro eseguire la procedura seguente usando il frammento di codice riportato di seguito.
    1. Creare un archivio chiavi e popolarlo con un nuovo certificato privato.
    1. Creare una richiesta di firma del certificato.
    1. SCP la richiesta di firma del certificato all'autorità di certificazione (headnode0)

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. Nel computer della CA eseguire il comando seguente per creare i file CA-CERT e CA-Key:

    ```bash
    openssl req -new -newkey rsa:4096 -days 365 -x509 -subj "/CN=Kafka-Security-CA" -keyout ca-key -out ca-cert -nodes
    ```

1. Passare al computer della CA e firmare tutte le richieste di firma del certificato ricevute:

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. Inviare di nuovo i certificati firmati ai nodi di lavoro dalla CA (headnode0).

    ```bash
    scp wn0-cert-signed sshuser@WorkerNode0_Name:~/ssl/cert-signed
    scp wn1-cert-signed sshuser@WorkerNode1_Name:~/ssl/cert-signed
    scp wn2-cert-signed sshuser@WorkerNode2_Name:~/ssl/cert-signed
    ```

1. Inviare il certificato pubblico della CA a ogni nodo del ruolo di lavoro.

    ```bash
    scp ca-cert sshuser@WorkerNode0_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode1_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode2_Name:~/ssl/ca-cert
    ```

1. In ogni nodo del ruolo di lavoro aggiungere il certificato pubblico CAs a trustStore e keystore. Aggiungere quindi il certificato firmato del nodo di lavoro all'archivio chiavi

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-tls-and-restart-brokers"></a>Aggiornare la configurazione di Kafka per l'uso di TLS e riavviare i broker

A questo punto è stato configurato ogni broker Kafka con un archivio chiavi e trustStore e sono stati importati i certificati corretti. A questo punto, modificare le proprietà di configurazione Kafka correlate tramite Ambari e quindi riavviare i broker Kafka.

Per completare la configurazione, seguire questa procedura:

1. Accedere al portale di Azure e selezionare il cluster Apache Kafka di Azure HDInsight.
1. Passare all'interfaccia utente di Ambari facendo clic su **Home Ambari** in **Dashboard cluster**.
1. In **Kafka Broker** (Broker Kafka) impostare la proprietà **listeners** su `PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. In **Advanced kafka-broker** (Kafka-broker avanzato) impostare la proprietà **security.inter.broker.protocol** su `SSL`

    ![Modifica delle proprietà di configurazione SSL di Kafka in Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. In **Custom kafka-broker** (Kafka-broker personalizzato) impostare la proprietà **ssl.client.auth** su `required`. Questo passaggio è necessario solo se si configurano l'autenticazione e la crittografia.

    ![Modifica delle proprietà di configurazione SSL di Kafka in Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. Per HDI versione 3,6, passare all'interfaccia utente di Ambari e aggiungere le configurazioni seguenti in **Advanced Kafka-ENV** e la proprietà del **modello Kafka-ENV** .

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

1. Di seguito è riportata la schermata che mostra l'interfaccia utente di configurazione di Ambari con queste modifiche.

    Per HDI versione 3,6:

    ![Modifica della proprietà del modello Kafka-ENV in Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env.png)

    Per HDI versione 4,0:

     ![Modifica della proprietà del modello Kafka-ENV in Ambari quattro](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env-four.png)

1. Riavviare tutti i broker Kafka.

## <a name="client-setup-without-authentication"></a>Configurazione del client (senza autenticazione)

Se non è necessaria l'autenticazione, il riepilogo dei passaggi per configurare solo la crittografia TLS è:

1. Accedere alla CA (nodo head attivo).
1. Copiare il certificato CA nel computer client dal computer della CA (WN0).
1. Accedere al computer client (HN1) e passare alla `~/ssl` cartella.
1. Importare il certificato CA in trustStore.
1. Importare il certificato CA nell'archivio chiavi.

Questi passaggi sono descritti in dettaglio nei frammenti di codice seguenti.

1. Accedere al nodo CA.

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    ```

1. Copiare il certificato CA nel computer client

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

1. Accedere al computer client (nodo head di standby).

    ```bash
    ssh sshuser@HeadNode1_Name
    cd ssl
    ```

1. Importare il certificato CA in trustStore.

    ```bash
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Importare il certificato CA nell'archivio chiavi.
    
    ```bash
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Creare il file `client-ssl-auth.properties` nel computer client (HN1). Dovrebbe includere le righe seguenti:

    ```config
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ```

1. Avviare il client di amministrazione con le opzioni Producer e consumer per verificare che i producer e i consumer stiano lavorando sulla porta 9093. Per la procedura necessaria per verificare il programma di installazione tramite la console producer/consumer, vedere la sezione di [Verifica](apache-kafka-ssl-encryption-authentication.md#verification) riportata di seguito.

## <a name="client-setup-with-authentication"></a>Configurazione del client (con autenticazione)

> [!Note]
> I passaggi seguenti sono necessari solo se si configurano sia la crittografia TLS **che** l'autenticazione. Se si configura solo la crittografia, vedere [configurazione client senza autenticazione](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication).

I quattro passaggi seguenti riepilogano le attività necessarie per completare la configurazione del client:

1. Accedere al computer client (nodo head di standby).
1. Creare un archivio chiavi Java e ottenere un certificato firmato per il broker. Copiare quindi il certificato nella macchina virtuale in cui è in esecuzione la CA.
1. Passare al computer della CA (nodo head attivo) per firmare il certificato client.
1. Passare al computer client (nodo head standby) e passare alla `~/ssl` cartella. Copiare il certificato firmato nel computer client.

Di seguito sono riportati i dettagli di ogni passaggio.

1. Accedere al computer client (nodo head di standby).

    ```bash
    ssh sshuser@HeadNode1_Name
    ```

1. Rimuovere qualsiasi directory SSL esistente.

    ```bash
    rm -R ~/ssl
    mkdir ssl
    cd ssl
    ```

1. Creare un archivio chiavi Java e creare una richiesta di firma del certificato. 

    ```bash
    keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -dname "CN=HEADNODE1_FQDN" -storetype pkcs12
    
    keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -storepass "MyClientPassword123" -keypass "MyClientPassword123"
    ```

1. Copiare la richiesta di firma del certificato nell'autorità di certificazione

    ```bash
    scp client-cert-sign-request sshuser@HeadNode0_Name:~/ssl/client-cert-sign-request
    ```

1. Passare al computer della CA (nodo head attivo) e firmare il certificato client.

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    openssl x509 -req -CA ca-cert -CAkey ca-key -in ~/ssl/client-cert-sign-request -out ~/ssl/client-cert-signed -days 365 -CAcreateserial -passin pass:MyClientPassword123
    ```

1. Copiare il certificato client firmato dalla CA (nodo head attivo) al computer client.

    ```bash
    scp client-cert-signed sshuser@HeadNode1_Name:~/ssl/client-signed-cert
    ```

1. Copiare il certificato CA nel computer client

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

    1. Accedere al computer client (nodo head standby) e passare alla directory SSL.

    ```bash
    ssh sshuser@HeadNode1_Name
    cd ssl
    ```

1. Creare un archivio client con certificato firmato e importare il certificato CA nell'archivio chiavi e trustStore nel computer client (HN1):

    ```bash
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    
    keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Creare un file `client-ssl-auth.properties` nel computer client (HN1). Dovrebbe includere le righe seguenti:

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
> Se HDInsight 4,0 e Kafka 2,1 è installato, è possibile usare la console producer/consumer per verificare la configurazione. In caso contrario, eseguire il producer Kafka sulla porta 9092 e inviare messaggi all'argomento, quindi usare il consumer Kafka sulla porta 9093 che usa TLS.

### <a name="kafka-21-or-above"></a>Kafka 2,1 o versione successiva

1. Creare un argomento se non esiste già.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1. Avviare Producer console e specificare il percorso di `client-ssl-auth.properties` come file di configurazione per il producer.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9093 --topic topic1 --producer.config ~/ssl/client-ssl-auth.properties
    ```

1. Aprire un'altra connessione SSH al computer client e avviare l'utente della console e fornire il percorso di `client-ssl-auth.properties` come file di configurazione per il consumer.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

### <a name="kafka-11"></a>Kafka 1,1

1. Creare un argomento se non esiste già.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE_0>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1. Avviare Producer console e specificare il percorso di client-SSL-auth. Properties come file di configurazione per il producer.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9092 --topic topic1 
    ```

1. Aprire un'altra connessione SSH al computer client e avviare l'utente della console e fornire il percorso di `client-ssl-auth.properties` come file di configurazione per il consumer.

    ```bash
    $ /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

## <a name="next-steps"></a>Passaggi successivi

* [Che cosa è Apache Kafka in HDInsight?](apache-kafka-introduction.md)
