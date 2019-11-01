---
title: Autenticazione & Apache Kafka crittografia SSL-Azure HDInsight
description: Configurare la crittografia SSL per la comunicazione tra i client Kafka e i broker Kafka, oltre che tra i broker Kafka. Configurare l'autenticazione SSL dei client.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: hrasheed
ms.openlocfilehash: 5dd698b28a01ed251492cf34e9da2dda4d0c2580
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241987"
---
# <a name="set-up-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Configurare la crittografia e l'autenticazione Secure Sockets Layer (SSL) per Apache Kafka in Azure HDInsight

Questo articolo illustra come configurare la crittografia SSL tra Apache Kafka client e i broker di Apache Kafka. Viene inoltre illustrato come configurare l'autenticazione dei client (talvolta definita SSL bidirezionale).

> [!Important]
> Sono disponibili due client che è possibile usare per le applicazioni Kafka: un client Java e un client console. Solo il client Java `ProducerConsumer.java` può utilizzare SSL per la produzione e l'utilizzo. Il client di produzione console `console-producer.sh` non funziona con SSL.

## <a name="apache-kafka-broker-setup"></a>Installazione di Apache Kafka broker

La configurazione del broker SSL Kafka utilizzerà quattro macchine virtuali del cluster HDInsight nel modo seguente:

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
> Nei frammenti di codice seguenti wnX è un'abbreviazione per uno dei tre nodi di lavoro e deve essere sostituito con `wn0`, `wn1` o `wn2` in base alle esigenze. è necessario sostituire `WorkerNode0_Name` e `HeadNode0_Name` con i nomi dei rispettivi computer, ad esempio `wn0-abcxyz` o `hn0-abcxyz`.

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

## <a name="update-kafka-configuration-to-use-ssl-and-restart-brokers"></a>Aggiornare la configurazione Kafka per usare SSL e riavviare i broker

A questo punto è stato configurato ogni broker Kafka con un archivio chiavi e trustStore e sono stati importati i certificati corretti. A questo punto, modificare le proprietà di configurazione Kafka correlate tramite Ambari e quindi riavviare i broker Kafka.

Per completare la configurazione, seguire questa procedura:

1. Accedere al portale di Azure e selezionare il cluster Apache Kafka di Azure HDInsight.
1. Passare all'interfaccia utente di Ambari facendo clic su **Home Ambari** in **Dashboard cluster**.
1. In **Kafka Broker** (Broker Kafka) impostare la proprietà **listeners** su `PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. In **Advanced kafka-broker** (Kafka-broker avanzato) impostare la proprietà **security.inter.broker.protocol** su `SSL`

    ![Modifica delle proprietà di configurazione SSL di Kafka in Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. In **Custom kafka-broker** (Kafka-broker personalizzato) impostare la proprietà **ssl.client.auth** su `required`. Questo passaggio è necessario solo se si configurano l'autenticazione e la crittografia.

    ![Modifica delle proprietà di configurazione SSL di Kafka in Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. In **Advanced Kafka-ENV** aggiungere le righe seguenti alla fine della proprietà del **modello Kafka-ENV** .

    ```config
    # Needed to configure IP address advertising
    ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks
    ssl.keystore.password=MyServerPassword123
    ssl.key.password=MyServerPassword123
    ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks
    ssl.truststore.password=MyServerPassword123
    ```

    ![Modifica della proprietà del modello Kafka-ENV in Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env.png)

1. Riavviare tutti i broker Kafka.
1. Avviare il client di amministrazione con le opzioni Producer e consumer per verificare che i producer e i consumer stiano lavorando sulla porta 9093.

## <a name="client-setup-with-authentication"></a>Configurazione del client (con autenticazione)

> [!Note]
> I passaggi seguenti sono necessari solo se si configurano sia la crittografia **che** l'autenticazione SSL. Se si intende configurare solo la crittografia, procedere a [Configurazione del client senza autenticazione](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication)

Per completare la configurazione del client, attenersi alla procedura seguente:

1. Accedere al computer client (HN1).
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

Se non è necessaria l'autenticazione, i passaggi per configurare solo la crittografia SSL sono:

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
