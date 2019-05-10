---
title: Configurare la crittografia SSL e autenticazione per Apache Kafka in HDInsight di Azure
description: Configurare la crittografia SSL per la comunicazione tra i client di Kafka e Broker di Kafka anche tra Broker Kafka. Configurare l'autenticazione SSL del client.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: hrasheed
ms.openlocfilehash: 5d567074a0038915cc43a585b34c9c71ccf3eb1b
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65464987"
---
# <a name="set-up-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Configurare la crittografia Secure Sockets Layer (SSL) e l'autenticazione per Apache Kafka in HDInsight di Azure

Questo articolo illustra come impostare la crittografia SSL tra i client di Apache Kafka e Broker di Apache Kafka. Viene inoltre illustrato come configurare l'autenticazione dei client (talvolta detto SSL bidirezionale).

> [!Important]
> Esistono due client che è possibile usare per le applicazioni di Kafka: un client Java e un client console. Solo il client Java `ProducerConsumer.java` può utilizzare SSL per entrambi producono e usano. Il client di producer di console `console-producer.sh` non funziona con SSL.

## <a name="apache-kafka-broker-setup"></a>Programma di installazione di Apache Kafka Broker

Il programma di installazione di Service broker di Kafka SSL userà quattro macchine virtuali del cluster HDInsight nel modo seguente:

* nodo head 0 - autorità di certificazione (CA)
* nodo di lavoro, 0, 1 e 2 - Broker

> [!Note] 
> Per questa guida verranno usati certificati autofirmati, ma la soluzione più sicura consiste nell'usare certificati emessi da CA attendibili.

Il riepilogo del processo di installazione di Service broker è come segue:

1. I seguenti passaggi vengono ripetuti in ognuno dei nodi di tre lavoro:

    1. Generare un certificato.
    1. Creare un certificato di firma richiesta.
    1. Inviare il certificato di firma richiesta per l'autorità di certificazione (CA).
    1. Accedere all'autorità di certificazione e firmare la richiesta.
    1. SCP cui certificato firmato di nuovo al nodo del ruolo di lavoro.
    1. SCP il certificato pubblico dell'autorità di certificazione per il nodo di lavoro.

1. Dopo avere tutti i certificati, inseriti i certificati nell'archivio del certificato.
1. Passare ad Ambari e modificare le configurazioni.

Utilizzare le istruzioni dettagliate seguenti per completare l'installazione di Service broker:

> [!Important]
> Nei frammenti di codice seguente è un'abbreviazione per uno dei nodi di tre lavoro wnX e deve essere sostituito con `wn0`, `wn1` o `wn2` come appropriato. `WorkerNode0_Name` e `HeadNode0_Name` deve essere sostituito con i nomi dei rispettivi computer, come `wn0-abcxyz` o `hn0-abcxyz`.

1. Eseguire l'installazione iniziale nel nodo head 0, che per HDInsight compilerà il ruolo di autorità di certificazione (CA).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Eseguire la stessa configurazione iniziale in ognuno dei Broker (nodi di lavoro 0, 1 e 2).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. In ognuno dei nodi di lavoro, eseguire i passaggi seguenti usando il frammento di codice riportato di seguito.
    1. Creare un archivio chiavi e popolarla con un nuovo certificato privato.
    1. Creare un richiesta di firma del certificato.
    1. SCP la richiesta di firma del certificato all'autorità di certificazione (nodo head 0)

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. Passare al computer della CA e firmare tutti i file del certificato ricevuto richieste di firma:

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. Inviare i certificati firmati dall'autorità di certificazione (headnode0) i nodi di lavoro.

    ```bash
    scp wn0-cert-signed sshuser@WorkerNode0_Name:~/ssl/cert-signed
    scp wn1-cert-signed sshuser@WorkerNode1_Name:~/ssl/cert-signed
    scp wn2-cert-signed sshuser@WorkerNode2_Name:~/ssl/cert-signed
    ```

1. Inviare il certificato pubblico dell'autorità di certificazione per ogni nodo di lavoro.

    ```bash
    scp ca-cert sshuser@WorkerNode0_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode1_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode2_Name:~/ssl/ca-cert
    ```

1. In ogni nodo di lavoro, aggiungere il certificato pubblico CA per il file truststore e dell'archivio chiavi. Quindi aggiungere il certificato autofirmato del nodo di lavoro dell'archivio chiavi

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-ssl-and-restart-brokers"></a>Aggiornare la configurazione Kafka per usare SSL e riavviare i broker

Ora avere impostato ogni broker Kafka con un archivio chiavi e del file truststore e importare i certificati corretti. A questo punto, modificare le proprietà di configurazione Kafka correlate tramite Ambari e quindi riavviare i broker Kafka.

Per completare la configurazione, seguire questa procedura:

1. Accedere al portale di Azure e selezionare il cluster Apache Kafka di Azure HDInsight.
1. Passare all'interfaccia utente di Ambari facendo clic su **Home Ambari** in **Dashboard cluster**.
1. In **Kafka Broker** (Broker Kafka) impostare la proprietà **listeners** su `PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. In **Advanced kafka-broker** (Kafka-broker avanzato) impostare la proprietà **security.inter.broker.protocol** su `SSL`

    ![Modifica delle proprietà di configurazione SSL di Kafka in Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. In **Custom kafka-broker** (Kafka-broker personalizzato) impostare la proprietà **ssl.client.auth** su `required`. Questo passaggio è obbligatorio se si configura l'autenticazione e crittografia.

    ![Modifica delle proprietà di configurazione SSL di Kafka in Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. Aggiungere le proprietà di configurazione al file Kafka `server.properties` per annunciare gli indirizzi IP anziché il nome di dominio completo (FQDN).

    ```bash
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

1. Per verificare che le modifiche precedenti siano state apportate correttamente, è possibile controllare facoltativamente che le righe seguenti siano presenti nel file Kafka `server.properties`.

    ```bash
    advertised.listeners=PLAINTEXT://10.0.0.11:9092,SSL://10.0.0.11:9093
    ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks
    ssl.keystore.password=MyServerPassword123
    ssl.key.password=MyServerPassword123
    ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks
    ssl.truststore.password=MyServerPassword123
    ```

1. Riavviare tutti i broker Kafka.
1. Avviare il client di amministrazione con opzioni di producer e consumer per verificare che i producer e consumer funzionino sulla porta 9093.

## <a name="client-setup-with-authentication"></a>Configurazione del client (con autenticazione)

> [!Note]
> I passaggi seguenti sono necessari solo se si configurano sia la crittografia **che** l'autenticazione SSL. Se si intende configurare solo la crittografia, procedere a [Configurazione del client senza autenticazione](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication)

Completare i passaggi seguenti per completare l'installazione del client:

1. Accedere al computer client (hn1).
1. Creare un archivio chiavi Java e ottenere un certificato firmato per il broker. Copiare quindi il certificato nella macchina virtuale in cui è in esecuzione la CA.
1. Passare al computer della CA (hn0) per firmare il certificato client.
1. Nel computer client (hn1) passare alla cartella `~/ssl`. Copiare il certificato firmato nel computer client.

```bash
cd ssl

# Create a java keystore and get a signed certificate for the broker. Then copy the certificate to the VM where the CA is running.

keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -dname "CN=mylaptop1" -alias my-local-pc1 -storetype pkcs12

keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -alias my-local-pc1 -storepass "MyClientPassword123" -keypass "MyClientPassword123"

# Copy the cert to the CA
scp client-cert-sign-request3 sshuser@HeadNode0_Name:~/tmp1/client-cert-sign-request

# Switch to the CA machine (hn0) to sign the client certificate.
cd ssl
openssl x509 -req -CA ca-cert -CAkey ca-key -in /tmp1/client-cert-sign-request -out /tmp1/client-cert-signed -days 365 -CAcreateserial -passin pass:MyServerPassword123

# Return to the client machine (hn1), navigate to ~/ssl folder and copy signed cert from the CA (hn0) to client machine
scp -i ~/kafka-security.pem sshuser@HeadNode0_Name:/tmp1/client-cert-signed

# Import CA cert to trust store
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import CA cert to key store
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import signed client (cert client-cert-signed1) to keystore
keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -alias my-local-pc1 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
```

Infine, vedere il file `client-ssl-auth.properties` con il comando `cat client-ssl-auth.properties`. Dovrebbe includere le righe seguenti:

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=MyClientPassword123
ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
ssl.keystore.password=MyClientPassword123
ssl.key.password=MyClientPassword123
```

## <a name="client-setup-without-authentication"></a>Configurazione del client (senza autenticazione)

Se non sono necessarie l'autenticazione, i passaggi per configurare solo la crittografia SSL sono:

1. Accedere al computer client (hn1) e passare alla cartella `~/ssl`.
1. Copiare il certificato firmato nel computer client dal computer della CA (wn0).
1. Importare il certificato della CA all'archivio attendibilità
1. Importare il certificato della CA all'archivio chiavi

Questi passaggi sono mostrati nel frammento di codice seguente.

```bash
cd ssl

# Copy signed cert to client machine
scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert .

# Import CA cert to truststore
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import CA cert to keystore
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file cert-signed -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
```

Infine, vedere il file `client-ssl-auth.properties` con il comando`cat client-ssl-auth.properties`. Dovrebbe includere le righe seguenti:

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=MyClientPassword123
```

## <a name="next-steps"></a>Passaggi successivi

* [Che cosa è Apache Kafka in HDInsight?](apache-kafka-introduction.md)
