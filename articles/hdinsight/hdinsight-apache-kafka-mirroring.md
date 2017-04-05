---
title: Eseguire il mirroring di cluster Apache Kafka in HDInsight | Documentazione Microsoft
description: "Informazioni su come usare la funzionalità di mirroring di Kafka per mantenere una replica di un cluster Kafka in HDInsight eseguendo il mirroring di argomenti in un cluster secondario."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 015d276e-f678-4f2b-9572-75553c56625b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/13/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 281901045723266128db9069a6f244acb183ae80
ms.lasthandoff: 03/25/2017

---
# <a name="use-mirrormaker-to-create-a-replica-of-a-kafka-on-hdinsight-cluster-preview"></a>Usare MirrorMaker per creare una replica di un cluster Kafka in HDInsight (anteprima)

Apache Kafka include una funzionalità di mirroring che consente di replicare gli argomenti da un cluster Kafka all'altro, ad esempio la replica dei record tra cluster Kafka in aree differenti di Azure.

Il mirroring può essere eseguito come processo continuo o usato in modo intermittente come metodo di migrazione dei dati da un cluster all'altro.

> [!WARNING]
> Il mirroring non deve essere considerato un mezzo per ottenere la tolleranza di errore. Gli offset per gli elementi all'interno di un argomento sono diversi nei cluster di origine e di destinazione, quindi i client non possono usarli in modo intercambiabile.
> 
> Per preservare la tolleranza di errore è necessario impostare la replica per gli argomenti all'interno del cluster. Per altre informazioni, vedere [Introduzione a Kafka in HDInsight](hdinsight-apache-kafka-get-started.md).

## <a name="prerequisites"></a>Prerequisiti

* Una rete virtuale di Azure: i cluster Kafka di origine e destinazione devono poter comunicare direttamente tra loro. HDInsight non espone le API Kafka pubblicamente in Internet, quindi i cluster di origine e di destinazione devono trovarsi nella stessa rete virtuale di Azure.

* Due cluster Kafka: in questo documento viene usato un modello di Azure Resource Manager per creare due cluster Kafka in HDInsight in una rete virtuale di Azure.

## <a name="how-does-mirroring-work"></a>Funzionamento del mirroring

Il mirroring usa lo strumento MirrorMaker (componente di Apache Kafka) per utilizzare i record degli argomenti nel cluster di origine e creare una copia locale nel cluster di destinazione. MirrorMaker usa uno o più *consumer* che leggono dal cluster di origine e un *producer* che scrive nel cluster locale (destinazione).

Il diagramma seguente illustra il processo di mirroring:

![Diagramma del processo di mirroring](./media/hdinsight-apache-kafka-mirroring/kafka-mirroring.png)

I cluster di origine e destinazione possono differire per numero di nodi e partizioni. Anche gli offset negli argomenti differiscono. Il mirroring mantiene il valore della chiave usato per il partizionamento, quindi l'ordine dei record viene conservato in base alla chiave.

### <a name="mirroring-between-networks"></a>Mirroring tra reti

Se è necessario eseguire il mirroring di cluster Kafka in reti diverse, si notino le seguenti considerazioni aggiuntive:

* **Gateway**: le reti devono poter comunicare a livello di TCP/IP.

* **Risoluzione dei nomi**: i cluster Kafka in ogni rete devono potersi connettere tra loro usando nomi host. Potrebbe essere necessario un server DNS (Domain Name System) in ogni rete configurato per l'inoltro delle richieste ad altre reti. 
  
    Quando si crea una rete virtuale di Azure, invece di usare il DNS automatico fornito con la rete è necessario specificare un server DNS personalizzato con il relativo indirizzo IP. Dopo aver creato la rete virtuale è necessario creare una macchina virtuale di Azure che usi quell'indirizzo IP, quindi installare e configurare il software DNS sulla macchina stessa.
  
    > [!WARNING]
    > Creare e configurare il server DNS personalizzato prima di installare HDInsight nella rete virtuale. Non sono necessarie altre operazioni di configurazione per far sì che HDInsight usi il server DNS configurato per la rete virtuale.

Per altre informazioni sulla connessione di due reti virtuali di Azure, vedere [Configurare una connessione da rete virtuale a rete virtuale](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

## <a name="create-kafka-clusters"></a>Creare cluster Kafka

Apache Kafka in HDInsight non fornisce l'accesso al servizio Kafka tramite Internet pubblico. Tutto ciò che comunica con Kafka deve trovarsi nella stessa rete virtuale di Azure dei nodi del cluster Kafka. Per questo esempio, i cluster Kafka di origine e destinazione si trovano entrambi in una rete virtuale di Azure. Il diagramma seguente illustra il flusso delle comunicazioni tra i cluster:

![Diagramma dei cluster Kafka di origine e destinazione in una rete virtuale di Azure](./media/hdinsight-apache-kafka-mirroring/spark-kafka-vnet.png)

> [!NOTE]
> Anche se Kafka è limitato alle comunicazioni all'interno della rete virtuale, è possibile accedere ad altri servizi del cluster tramite Internet, ad esempio SSH e Ambari. Per altre informazioni sulle porte pubbliche disponibili con HDInsight, vedere [Porte e URI usati da HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Anche se è possibile creare manualmente cluster Kafka e una rete virtuale di Azure, è più semplice usare un modello di Azure Resource Manager. Seguire questa procedura per distribuire una rete virtuale di Azure e due cluster Kafka e nella sottoscrizione di Azure.

1. Usare il pulsante seguente per accedere ad Azure e aprire il modello nel portale di Azure.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-mirror-cluster-in-vnet.json" target="_blank"><img src="./media/hdinsight-apache-kafka-mirroring/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Il modello di Azure Resource Manager è disponibile all'indirizzo **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet.json**.

2. Usare le informazioni seguenti per popolare le voci nel pannello **Distribuzione personalizzata**:
    
    ![Distribuzione personalizzata di HDInsight](./media/hdinsight-apache-kafka-mirroring/parameters.png)
    
    * **Gruppo di risorse**: creare un gruppo o selezionarne uno esistente. Questo gruppo contiene il cluster HDInsight.

    * **Località**: scegliere una località geograficamente vicina. La località deve corrispondere a quella indicata nella sezione __IMPOSTAZIONI__.
     
    * **Base Cluster Name** (Nome di base del cluster): questo valore viene usato come nome di base per i cluster Kafka. Se ad esempio si immette **hdi** verranno creati cluster denominati **source-hdi** e **dest-hdi**.

    * **Cluster Login User Name** (Nome utente di accesso del cluster): nome utente amministratore per i cluster Kafka di origine e destinazione.

    * **Cluster Login Password** (Password di accesso del cluster): password dell'utente amministratore per i cluster Kafka di origine e destinazione.

    * **SSH User Name** (Nome utente SSH): utente SSH da creare per i cluster Kafka di origine e destinazione.

    * **SSH Password** (Password SSH): password dell'utente SSH per i cluster Kafka di origine e destinazione.

    * **Location** (Località): area in cui vengono creati i cluster.

3. Leggere le **Condizioni** e quindi selezionare **Accetto le condizioni riportate sopra**.

4. Selezionare infine **Aggiungi al dashboard** e quindi **Acquista**. La creazione dei cluster richiede circa 20 minuti.

Dopo aver creato le risorse, si viene reindirizzati a un pannello del gruppo di risorse che contiene i cluster e il dashboard Web.

![Pannello Gruppo di risorse per la rete virtuale e i cluster](./media/hdinsight-apache-kafka-mirroring/groupblade.png)

> [!IMPORTANT]
> Si noti che i nomi dei cluster HDInsight sono **source-BASENAME** e **dest-BASENAME**, dove BASENAME è il nome specificato per il modello. Questi nomi verranno usati nei passaggi successivi per la connessione ai cluster.

## <a name="create-topics"></a>Creare argomenti

1. Connettersi al cluster di **origine** tramite SSH:
   
        ssh sshuser@source-BASENAME-ssh.azurehdinsight.net
   
    Sostituire **sshuser** con il nome utente SSH usato durante la creazione del cluster. Sostituire **BASENAME** con il nome di base usato durante la creazione del cluster.
   
    Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Usare il comando seguente per trovare gli host Zookeeper, impostare la variabile `SOURCE_ZKHOSTS` e quindi creare nuovi argomenti denominati `testtopic`:
   
    ```bash
    SOURCE_ZKHOSTS=`grep -R zk /etc/hadoop/conf/yarn-site.xml | grep 2181 | grep -oPm1 "(?<=<value>)[^<]+"`
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SOURCE_ZKHOSTS
    ```

3. Usare il comando seguente per verificare che l'argomento sia stato creato:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $SOURCE_ZKHOSTS
    ```
   
    La risposta contiene `testtopic`.

4. Usare il comando seguente per visualizzare le informazioni degli host Zookeeper per questo cluster, ovvero il cluster di **origine**:
   
    ```bash
    echo $SOURCE_ZKHOSTS
    ```
   
 Verranno restituite informazioni simili al testo seguente:
   
       zk0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181,zk1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181,zk6-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181
   
 Salvare queste informazioni. Verranno usate nella sezione successiva.

## <a name="configure-mirroring"></a>Configurare il mirroring

1. Connettersi al cluster di **destinazione** con un'altra sessione SSH:
   
        ssh sshuser@dest-BASENAME-ssh.azurehdinsight.net
   
    Sostituire **sshuser** con il nome utente SSH usato durante la creazione del cluster. Sostituire **BASENAME** con il nome di base usato durante la creazione del cluster.
   
    Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Usare il comando seguente per creare un file `consumer.properties` che descrive come comunicare con il cluster di **origine**:
   
    ```bash
    nano consumer.properties
    ```
   
    Usare il testo seguente come contenuto del file `consumer.properties`:
   
        zookeeper.connect=SOURCE_ZKHOSTS
        group.id=mirrorgroup
   
    Sostituire **SOURCE_ZKHOSTS** con le informazioni degli host Zookeeper presenti nel cluster di **origine**.
   
    Questo file descrive le informazioni sui consumer da usare durante la lettura dal cluster Kafka di origine. Per altre informazioni sulla configurazione dei consumer, vedere [Consumer Configs](https://kafka.apache.org/documentation#consumerconfigs) (Configurazione di consumer) in kafka.apache.org.
   
    Usare **Ctrl + X**, **Y** e INVIO per salvare il file.

3. Prima di configurare il producer che comunica con il cluster di destinazione è necessario trovare gli host broker per il cluster di **destinazione** stesso. Usare i comandi seguenti per recuperare queste informazioni:
   
    ```bash
    sudo apt -y install jq
    DEST_BROKERHOSTS=`sudo bash -c 'ls /var/lib/ambari-agent/data/command-[0-9]*.json' | tail -n 1 | xargs sudo cat | jq -r '["\(.clusterHostInfo.kafka_broker_hosts[]):9092"] | join(",")'`
    echo $DEST_BROKERHOSTS
    ```
   
    Questi comandi restituiscono informazioni simili alle seguenti:
   
        wn0-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn1-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092

4. Usare il comando seguente per creare un file `producer.properties` che descrive come comunicare con il cluster di **destinazione**:

    ```bash
    nano producer.properties
    ```

    Usare il testo seguente come contenuto del file `producer.properties`:
   
        bootstrap.servers=DEST_BROKERS
        compression.type=none
   
    Sostituire **DEST_BROKERS** con le informazioni del broker indicate nel passaggio precedente. 
   
    Per altre informazioni sulla configurazione dei producer, vedere [Producer Configs](https://kafka.apache.org/documentation#producerconfigs) (Configurazione di producer) in kafka.apache.org.

## <a name="start-mirrormaker"></a>Avviare MirrorMaker

1. Dalla connessione SSH al cluster di **destinazione** usare il comando seguente per avviare il processo MirrorMaker:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```
   
    I parametri usati in questo esempio sono i seguenti:
   
    * **--consumer.config**: specifica il file che contiene le proprietà del consumer. Queste proprietà vengono usate per creare un consumer che legge dal cluster Kafka di *origine*.

    * **--producer.config**: specifica il file che contiene le proprietà del producer. Queste proprietà vengono usate per creare un producer che scrive nel cluster Kafka di *destinazione*.

    * **--whitelist**: elenco di argomenti che vengono replicati da MirrorMaker dal cluster di origine alla destinazione.
    
    * **--num.streams**: numero di thread consumer da creare.
     
    All'avvio, MirrorMaker restituisce informazioni simili al testo seguente:
        
    ```{metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-3, security.protocol=PLAINTEXT}{metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-0, security.protocol=PLAINTEXT}  metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-kafka.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-2, security.protocol=PLAINTEXT}  metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-1, security.protocol=PLAINTEXT}
    ```

2. From the SSH connection to the **source** cluster, use the following command to start a producer and send messages to the topic:
    
    ```bash
    sudo apt -y install jq
    SOURCE_BROKERHOSTS=`sudo bash -c 'ls /var/lib/ambari-agent/data/command-[0-9]*.json' | tail -n 1 | xargs sudo cat | jq -r '["\(.clusterHostInfo.kafka_broker_hosts[]):9092"] | join(",")'`
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

 Quando si arriva a una riga vuota con un cursore, digitare alcuni messaggi di testo. Questi vengono inviati all'argomento nel cluster di **origine**. Al termine, usare **Ctrl + C** per chiudere il processo del producer.

3. Dalla connessione SSH al cluster di **destinazione**, usare **Ctrl + C** per chiudere il processo MirrorMaker. Usare quindi i comandi seguenti per verificare che l'argomento `testtopic` sia stato creato e che i dati nell'argomento siano stati replicati al mirror:
    
    ```bash
    DEST_ZKHOSTS=`grep -R zk /etc/hadoop/conf/yarn-site.xml | grep 2181 | grep -oPm1 "(?<=<value>)[^<]+"`
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $DEST_ZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $DEST_ZKHOSTS --topic testtopic --from-beginning
    ```
    
  L'elenco degli argomenti include ora `testtopic`, che viene creato quando MirrorMaster esegue il mirroring dell'argomento dal cluster di origine a quello di destinazione. I messaggi recuperati dall'argomento sono gli stessi immessi nel cluster di origine.

## <a name="delete-the-cluster"></a>Eliminazione del cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Le procedure illustrate in questo documento creano entrambi i cluster nello stesso gruppo di risorse di Azure. È quindi possibile eliminare il gruppo di risorse dal portale di Azure. In questo modo vengono rimosse tutte le risorse create seguendo le istruzioni di questo documento, la rete virtuale di Azure e l'account di archiviazione usato dai cluster.

## <a name="next-steps"></a>Passaggi successivi

In questo documento è stato descritto come usare MirrorMaker per creare la replica di un cluster Kafka. Per trovare altri modi per lavorare con Kafka, vedere i collegamenti seguenti:

* [Documentazione su Apache Kafka MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) in cwiki.apache.org.
* [Introduzione ad Apache Kafka (anteprima) in HDInsight](hdinsight-apache-kafka-get-started.md)
* [Use Apache Spark with Kafka on HDInsight](hdinsight-apache-spark-with-kafka.md) (Usare Apache Spark con Kafka in HDInsight)
* [Usare Apache Storm (anteprima) con Kafka in HDInsight](hdinsight-apache-storm-with-kafka.md)


