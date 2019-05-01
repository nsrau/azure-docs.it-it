---
title: Configurare la crittografia e l'autenticazione SSL per Apache Kafka in Azure HDInsight
description: Configurare la crittografia SSL per la comunicazione tra client e broker Kafka, nonché tra broker Kafka. Configurare l'autenticazione SSL dei client.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: hrasheed
ms.openlocfilehash: 9d8d5e57d0dd7d7022e65a061360c8450848fb4b
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64682908"
---
# <a name="setup-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Configurare la crittografia e l'autenticazione SSL (Secure Sockets Layer) per Apache Kafka in Azure HDInsight

Questo articolo descrive come configurare la crittografia SSL tra i client Apache Kafka e i broker Apache Kafka. Descrive inoltre la procedura per configurare l'autenticazione dei client (nota a volte come SSL bidirezionale).

## <a name="server-setup"></a>Configurazione del server

Il primo passaggio consiste nel creare un archivio chiavi e archivio attendibilità su ogni broker Kafka. Dopo che questi vengono creati, importare i certificati di autorità di certificazione (CA) e broker in questi archivi.

> [!Note] 
> Per questa guida verranno usati certificati autofirmati, ma la soluzione più sicura consiste nell'usare certificati emessi da CA attendibili.

Eseguire le operazioni seguenti per completare la configurazione del server:

1. Creare una cartella denominata ssl ed esportare la password del server come variabile di ambiente. Nella parte restante di questa guida, sostituire `<server_password>` con la password dell'amministratore effettiva per il server.
1. Successivamente, creare un archivio chiavi Java (kafka.server.keystore.jks) e un certificato della CA.
1. Creare quindi una richiesta di firma per ottenere il certificato creato nel passaggio precedente firmato dalla CA.
1. A questo punto, inviare la richiesta di firma alla CA e ottenere questo certificato firmato. Dato che si sta usando un certificato autofirmato, il certificato viene firmato con la CA interna usando il comando `openssl`.
1. Creare un archivio attendibilità e importare il certificato della CA.
1. Importare il certificato della CA pubblica nell'archivio chiavi.
1. Importare il certificato firmato nell'archivio chiavi.

I comandi per il completamento di questi passaggi vengono visualizzati nel frammento di codice seguente.

```bash
export SRVPASS=<server_password>
mkdir ssl
cd ssl

# Create a java keystore (kafka.server.keystore.jks) and a CA certificate.

keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass $SRVPASS -keypass $SRVPASS -dname "CN=wn0-umakaf.xvbseke35rbuddm4fyvhm2vz2h.cx.internal.cloudapp.net" -storetype pkcs12

# Create a signing request to get the certificate created in the previous step signed by the CA.

keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass $SRVPASS -keypass $SRVPASS

# Send the signing request to the CA and get this certificate signed.

openssl x509 -req -CA ca-cert -CAkey ca-key -in cert-file -out cert-signed -days 365 -CAcreateserial -passin pass:$SRVPASS

# Create a trust store and import the certificate of the CA.

keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt

# Import the public CA certificate into the keystore.

keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt

# Import the signed certificate into the keystore.

keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt

# The output should say "Certificate reply was added to keystore"
```

L'importazione del certificato firmato nell'archivio chiavi è il passaggio finale necessario per configurare l'archivio attendibilità e l'archivio chiavi per un broker Kafka.

## <a name="update-kafka-configuration-to-use-ssl-and-restart-brokers"></a>Aggiornare la configurazione Kafka per usare SSL e riavviare i broker

Ogni broker Kafka è stato configurato con un archivio chiavi e un archivio attendibilità e sono stati importati i certificati corretti.  A questo punto, modificare le proprietà di configurazione Kafka correlate tramite Ambari e quindi riavviare i broker Kafka. 

Per completare la configurazione, seguire questa procedura:

1. Accedere al portale di Azure e selezionare il cluster Apache Kafka di Azure HDInsight.
1. Passare all'interfaccia utente di Ambari facendo clic su **Home Ambari** in **Dashboard cluster**.
1. In **Kafka Broker** (Broker Kafka) impostare la proprietà **listeners** su `PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. In **Advanced kafka-broker** (Kafka-broker avanzato) impostare la proprietà **security.inter.broker.protocol** su `SSL`

    ![Modifica delle proprietà di configurazione SSL di Kafka in Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. In **Custom kafka-broker** (Kafka-broker personalizzato) impostare la proprietà **ssl.client.auth** su `required`. Questo passaggio è necessario solo se si configura l'autenticazione oltre alla crittografia.

    ![Modifica delle proprietà di configurazione SSL di Kafka in Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. Aggiungere le proprietà di configurazione al file Kafka `server.properties` per annunciare gli indirizzi IP anziché il nome di dominio completo (FQDN).

    ```bash
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092,SSL://$IP_ADDRESS:9093" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.password=<server_password>" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.key.password=<server_password>" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.password=<server_password>" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. Per verificare che le modifiche precedenti siano state apportate correttamente, è possibile controllare facoltativamente che le righe seguenti siano presenti nel file Kafka `server.properties`.

    ```bash
    advertised.listeners=PLAINTEXT://10.0.0.11:9092,SSL://10.0.0.11:9093
    ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks
    ssl.keystore.password=<server_password>
    ssl.key.password=<server_password>
    ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks
    ssl.truststore.password=<server_password>
    ```

1. Riavviare tutti i broker Kafka.

## <a name="client-setup-with-authentication"></a>Configurazione del client (con autenticazione)

> [!Note]
> I passaggi seguenti sono necessari solo se si configurano sia la crittografia **che** l'autenticazione SSL. Se si intende configurare solo la crittografia, procedere a [Configurazione del client senza autenticazione](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication)

Eseguire i passaggi seguenti per completare la configurazione del client:

1. Accedere al computer client (hn1).
1. Esportare la password del client. Sostituire `<client_password>` con la password dell'amministratore effettivo nel computer client Kafka.
1. Creare un archivio chiavi Java e ottenere un certificato firmato per il broker. Copiare quindi il certificato nella macchina virtuale in cui è in esecuzione la CA.
1. Passare al computer della CA (wn0) per firmare il certificato client.
1. Nel computer client (hn1) passare alla cartella `~/ssl`. Copiare il certificato firmato nel computer client.

```bash
export CLIPASS=<client_password>
cd ssl

# Create a java keystore and get a signed certificate for the broker. Then copy the certificate to the VM where the CA is running.

keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass $CLIPASS -keypass $CLIPASS -dname "CN=mylaptop1" -alias my-local-pc1 -storetype pkcs12

keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -alias my-local-pc1 -storepass $CLIPASS -keypass $CLIPASS

# Copy the cert to the vm where the CA is
scp client-cert-sign-request3 sshuser@wn0-umakaf:~/tmp1/client-cert-sign-request

# Switch to the CA machine (wn0) to sign the client certificate.
cd ssl
openssl x509 -req -CA ca-cert -CAkey ca-key -in /tmp1/client-cert-sign-request -out /tmp1/client-cert-signed -days 365 -CAcreateserial -passin pass:<server_password>

# Return to the client machine (hn1), navigate to ~/ssl folder and copy signed cert to client machine
scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/tmp1/client-cert-signed

# Import CA cert to trust store
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

# Import CA cert to key store
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

# Import signed client (cert client-cert-signed1) to keystore
keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -alias my-local-pc1 -storepass $CLIPASS -keypass $CLIPASS -noprompt
```

Infine, vedere il file `client-ssl-auth.properties` con il comando `cat client-ssl-auth.properties`. Dovrebbe includere le righe seguenti:

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=<client_password>
ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
ssl.keystore.password=<client_password>
ssl.key.password=<client_password>
```

## <a name="client-setup-without-authentication"></a>Configurazione del client (senza autenticazione)

Se non è necessaria l’autenticazione, i passaggi per configurare solo la crittografia SSL sono:

1. Accedere al computer client (hn1) e passare alla cartella `~/ssl`.
1. Esportare la password del client. Sostituire `<client_password>` con la password dell'amministratore effettivo nel computer client Kafka.
1. Copiare il certificato firmato nel computer client dal computer della CA (wn0).
1. Importare il certificato della CA all'archivio attendibilità
1. Importare il certificato della CA all'archivio chiavi

Questi passaggi sono mostrati nel frammento di codice seguente.

```bash
export CLIPASS=<client_password>
cd ssl

# Copy signed cert to client machine
scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert .

# Import CA cert to truststore
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

# Import CA cert to keystore
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file cert-signed -storepass $CLIPASS -keypass $CLIPASS -noprompt
```

Infine, vedere il file `client-ssl-auth.properties` con il comando`cat client-ssl-auth.properties`. Dovrebbe includere le righe seguenti:

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=<client_password>
```

## <a name="next-steps"></a>Passaggi successivi

* [Che cosa è Apache Kafka in HDInsight?](apache-kafka-introduction.md)
