---
title: Configurare i criteri per Kafka in HDInsight con Enterprise Security Package - Azure
description: Informazioni su come configurare i criteri di Apache Ranger per Kafka in HDInsight di Azure con Enterprise Security Package.
services: hdinsight
ms.service: hdinsight
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: tutorial
ms.date: 09/24/2018
ms.openlocfilehash: 48cfba6f62d75470efd27e3a4cdcb995e716798b
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2018
ms.locfileid: "51037142"
---
# <a name="tutorial-configure-kafka-policies-in-hdinsight-with-enterprise-security-package-preview"></a>Esercitazione: Configurare i criteri per Kafka in HDInsight con Enterprise Security Package (anteprima)

Informazioni su come configurare i criteri di Apache Ranger per i cluster Kafka Enterprise Security Package (ESP). Gli ESP cluster sono connessi a un dominio che consente agli utenti di eseguire l'autenticazione con le credenziali di dominio. In questa esercitazione vengono creati due criteri di Ranger per limitare l'accesso a `sales*` e `marketingspend`.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare utenti del dominio
> * Creazione dei criteri di Ranger
> * Creare argomenti in un cluster Kafka
> * Testare i criteri di Ranger

## <a name="before-you-begin"></a>Prima di iniziare

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/).

* Accedere al [portale di Azure](https://portal.azure.com/).

* Creare un [cluster HDInsight Kafka con Enterprise Security Package](apache-domain-joined-configure-using-azure-adds.md).

## <a name="connect-to-apache-ranger-admin-ui"></a>Connettersi all'interfaccia utente di amministrazione di Apache Ranger

1. Da un browser, connettersi all'interfaccia utente di amministrazione di Ranger usando l'URL `https://<ClusterName>.azurehdinsight.net/Ranger/`. Ricordarsi di modificare `<ClusterName>` con il nome del cluster Kafka.

    > [!NOTE] 
    > Le credenziali di Ranger non sono le stesse usate del cluster Hadoop. Per evitare che i browser usino credenziali memorizzate nella cache di Hadoop, usare una nuova finestra del browser InPrivate per connettersi all'interfaccia utente di Ranger Admin.

2. Accedere usando le credenziali di amministratore di Azure Active Directory (AD). Le credenziali di amministratore di Azure AD non sono le stesse del cluster HDInsight o del nodo SSH di Linux HDInsight.

   ![Interfaccia utente di amministrazione di Apache Ranger](./media/apache-domain-joined-run-kafka/apache-ranger-admin-login.png)

## <a name="create-domain-users"></a>Creare utenti del dominio

Visitare [Creare un cluster HDInsight con Enterprise Security Package](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds#create-a-domain-joined-hdinsight-cluster) per informazioni su come creare gli utenti del dominio **sales_user** e **marketing_user**. In uno scenario di produzione, gli utenti del dominio provengono dal tenant di Active Directory.

## <a name="create-ranger-policy"></a>Creare un criterio di Ranger 

Creare un criterio di Ranger per **sales_user** e **marketing_user**.

1. Aprire l’**interfaccia utente di amministrazione di Ranger**.

2. Fare clic su  **\<ClusterName>_kafka** nella sezione **Kafka**. Potrebbe essere presente un criterio preconfigurato.

3. Fare clic su **Aggiungi nuovo criterio**, quindi immettere i valori seguenti:

   |**Impostazione**  |**Valore consigliato**  |
   |---------|---------|
   |Nome criterio  |  hdi sales* policy   |
   |Argomento   |  sales* |
   |Seleziona utente  |  sales_user1 |
   |Autorizzazioni  | publish, consume, create |

   I seguenti caratteri jolly possono essere inclusi nel nome dell'argomento:

   * "*" indica zero o più occorrenze del carattere.
   * "?" indica un carattere singolo.

   ![Creazione di un criterio nell'interfaccia utente di amministrazione di Apache Ranger](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy.png)   

   >[!NOTE] 
   >Attendere alcuni istanti per eseguire la sincronizzazione con Azure AD, se un utente di dominio non viene popolato automaticamente per **Seleziona utente**.

4. Fare clic su **Aggiungi** per salvare il criterio.

5. Fare clic su **Aggiungi nuovo criterio**, quindi immettere i valori seguenti:

   |**Impostazione**  |**Valore consigliato**  |
   |---------|---------|
   |Nome criterio  |  hdi marketing policy   |
   |Argomento   |  marketingspend |
   |Seleziona utente  |  marketing_user1 |
   |Autorizzazioni  | publish, consume, create |

   ![Creazione di un criterio nell'interfaccia utente di amministrazione di Apache Ranger](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy-2.png)  

6. Fare clic su **Aggiungi** per salvare il criterio.

## <a name="create-topics-in-a-kafka-cluster-with-esp"></a>Creare argomenti in un cluster Kafka con ESP

Per creare due argomenti **salesevents** e **marketingspend**:

1. Per aprire una connessione SSH al cluster, usare il comando seguente:

   ```bash
   ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net
   ```

   Sostituire `SSHUSER` con il nome utente SSH del cluster e sostituire `CLUSTERNAME` con il nome del cluster. Quando richiesto, immettere la password per l'account utente SSH. Per altre informazioni sull'uso di `scp` con HDInsight, vedere [Use SSH with HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) (Uso di SSH con HDInsight).

   In uno scenario di produzione, gli utenti del dominio configurati durante la creazione di cluster possono eseguire SSH nel cluster.

2. Per salvare il nome del cluster in una variabile e installare un'utilità di analisi JSON `jq`, usare i comandi seguenti. Quando richiesto, immettere il nome del cluster Kafka.

   ```bash
   sudo apt -y install jq
   read -p 'Enter your Kafka cluster name:' CLUSTERNAME
   ```

3. Per ottenere gli host del broker Kafka e gli host Zookeeper, usare i comandi seguenti. Quando richiesto, immettere la password dell'account amministratore del cluster.

   ```bash
   export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`; \
   export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
   ```
> [!Note]
> Prima di continuare, può essere necessario configurare l'ambiente di sviluppo se non è stato ancora fatto. Sono necessari componenti quali Java JDK, Apache Maven e un client SSH con scp. Per maggiori dettagli, vedere queste [istruzioni di installazione](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer).
1. Scaricare gli [esempi di producer e consumer Apache Kafka aggiunti a dominio](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer).

1. Seguire i passaggi 2 e 3 in **Creare e distribuire l'esempio** in [Esercitazione: Usare le API Apache Kafka Producer e Consumer](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-producer-consumer-api#build-and-deploy-the-example)

1. Eseguire i comandi seguenti:

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create salesevents $KAFKABROKERS
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create marketingspend $KAFKABROKERS
   ```

   >[!NOTE] 
   >Solo il proprietario del processo del servizio di Kafka, come radice, può scrivere negli Znode `/config/topics` di Zookeeper. I criteri di Ranger non vengono applicati quando un utente senza privilegi crea un argomento. Questo perché lo script `kafka-topics.sh` comunica direttamente con Zookeeper per creare l'argomento. Le voci vengono aggiunte ai nodi Zookeeper, mentre gli osservatori lato broker monitorano e creano argomenti corrispondenti. L'autorizzazione non può essere eseguita tramite il plug-in di Ranger e il comando precedente viene eseguito usando `sudo` tramite il broker Kafka.


## <a name="test-the-ranger-policies"></a>Testare i criteri di Ranger

In base ai criteri di Ranger configurati, **sales_user** può produrre o utilizzare argomenti **salesevents** ma non argomenti **marketingspend**. Al contrario, **marketing_user** può produrre o utilizzare argomenti **marketingspend** ma non argomenti **salesevents**.

1. Aprire una nuova connessione SSH al cluster. Eseguire questo comando per accedere come  **sales_user1**:

   ```bash
   ssh sales_user1@CLUSTERNAME-ssh.azurehdinsight.net
   ```

2. Eseguire il comando seguente:

   ```bash
   export KAFKA_OPTS="-Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf"
   ```

3. Usare i nomi di Zookeeper e del broker nella sezione precedente per impostare le variabili di ambiente seguenti:

   ```bash
   export KAFKABROKERS=<brokerlist>:9092 
   ```

   Esempio: `export KAFKABROKERS=wn0-khdicl.contoso.com:9092,wn1-khdicl.contoso.com:9092`

   ```bash
   export KAFKAZKHOSTS=<zklist>:2181
   ```

   Esempio: `export KAFKAZKHOSTS=zk1-khdicl.contoso.com:2181,zk2-khdicl.contoso.com:2181`

4. Verificare che **sales_user1** può eseguire la produzione nell'argomento **salesevents**.
   
   Eseguire il comando seguente per avviare la console-producer per l'argomento **salesevents**:

   ```bash
   /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic salesevents --security-protocol SASL_PLAINTEXT
   ```

   Quindi, digitare alcuni messaggi nella console. Premere **Ctrl + C** per chiudere lai console-producer.

5. Eseguire il comando seguente da utilizzare a partire dall'argomento **salesevents**:

   ```bash
   /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --topic salesevents --security-protocol PLAINTEXTSASL --from-beginning
   ```
 
6. Verificare che i messaggi inseriti nel passaggio precedente vengano visualizzati,e che **sales_user1** non possa eseguire la produzione dell'argomento **marketingspend**.

   Dalla stessa finestra ssh come illustrato in precedenza, eseguire il comando seguente per la produzione nell'argomento **marketingspend**:

   ```bash
   /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic marketingspend --security-protocol SASL_PLAINTEXT
   ```

   Si verifica un errore di autorizzazione che può essere ignorato. 

7. Si noti che **marketing_user1** non può essere utilizzato dall'argomento **salesevents**.

   Ripetere i passaggi da 1 a 3 di cui sopra, ma questa volta come **marketing_user1**.

   Eseguire il comando seguente da utilizzare a partire dall'argomento **salesevents**:

   ```bash
   /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --topic marketingspend --security-protocol PLAINTEXTSASL --from-beginning
   ```

   I messaggi precedenti non possono essere visualizzati.

8. Visualizzare gli eventi di accesso di controllo dall'interfaccia utente di Ranger.

   ![Controllo dei criteri di Ranger dell'interfaccia utente](./media/apache-domain-joined-run-kafka/apache-ranger-admin-audit.png)

## <a name="next-steps"></a>Passaggi successivi

* [Trasferire la propria chiave a Kafka](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok)
* [Introduzione alla sicurezza Hadoop con Enterprise Security Package](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-introduction)
