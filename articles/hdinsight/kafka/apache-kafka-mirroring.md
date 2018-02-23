---
title: 'Eseguire il mirroring degli argomenti di Apache Kafka: Azure HDInsight| Microsoft Docs'
description: "Informazioni su come usare la funzionalità di mirroring di Apache Kafka per gestire una replica di un cluster Kafka in HDInsight eseguendo il mirroring degli argomenti in un cluster secondario."
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
ms.date: 01/31/2018
ms.author: larryfr
ms.openlocfilehash: 87b5912e7f9244dc1be74ac357200122b194dbdc
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2018
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>Usare MirrorMaker per replicare gli argomenti di Apache Kafka con Kafka in HDInsight

Informazioni su come usare la funzionalità di mirroring di Apache Kafka per replicare gli argomenti in un cluster secondario. Il mirroring può essere eseguito come processo continuo o usato in modo intermittente come metodo di migrazione dei dati da un cluster all'altro.

In questo esempio il mirroring viene usato per replicare argomenti tra due cluster HDInsight. Entrambi i cluster si trovano in una rete virtuale di Azure nella stessa area.

> [!WARNING]
> Il mirroring non deve essere considerato un mezzo per ottenere la tolleranza di errore. Gli offset per gli elementi all'interno di un argomento sono diversi nei cluster di origine e di destinazione, quindi i client non possono usarli in modo intercambiabile.
>
> Per preservare la tolleranza di errore è necessario impostare la replica per gli argomenti all'interno del cluster. Per altre informazioni, vedere [Introduzione a Kafka in HDInsight](apache-kafka-get-started.md).

## <a name="how-kafka-mirroring-works"></a>Funzionamento del mirroring di Kafka

Il mirroring usa lo strumento MirrorMaker (componente di Apache Kafka) per utilizzare i record degli argomenti nel cluster di origine e creare una copia locale nel cluster di destinazione. MirrorMaker usa uno o più *consumer* che leggono dal cluster di origine e un *producer* che scrive nel cluster locale (destinazione).

Il diagramma seguente illustra il processo di mirroring:

![Diagramma del processo di mirroring](./media/apache-kafka-mirroring/kafka-mirroring.png)

Apache Kafka in HDInsight non fornisce l'accesso al servizio Kafka tramite Internet pubblico. I producer o i consumer di Kafka devono trovarsi nella stessa rete virtuale di Azure in cui sono presenti i nodi del cluster Kafka. Per questo esempio, i cluster Kafka di origine e destinazione si trovano entrambi in una rete virtuale di Azure. Il diagramma seguente illustra il flusso delle comunicazioni tra i cluster:

![Diagramma dei cluster Kafka di origine e destinazione in una rete virtuale di Azure](./media/apache-kafka-mirroring/spark-kafka-vnet.png)

I cluster di origine e destinazione possono differire per numero di nodi e partizioni. Anche gli offset negli argomenti differiscono. Il mirroring mantiene il valore della chiave usato per il partizionamento, quindi l'ordine dei record viene conservato in base alla chiave.

### <a name="mirroring-across-network-boundaries"></a>Mirroring tra i limiti di rete

Se è necessario eseguire il mirroring di cluster Kafka in reti diverse, si notino le seguenti considerazioni aggiuntive:

* **Gateway**: le reti devono poter comunicare a livello di TCP/IP.

* **Risoluzione dei nomi**: i cluster Kafka in ogni rete devono potersi connettere tra loro usando nomi host. Potrebbe essere necessario un server DNS (Domain Name System) in ogni rete configurato per l'inoltro delle richieste ad altre reti.

    Quando si crea una rete virtuale di Azure, invece di usare il DNS automatico fornito con la rete è necessario specificare un server DNS personalizzato con il relativo indirizzo IP. Dopo aver creato la rete virtuale è necessario creare una macchina virtuale di Azure che usi quell'indirizzo IP, quindi installare e configurare il software DNS sulla macchina stessa.

    > [!WARNING]
    > Creare e configurare il server DNS personalizzato prima di installare HDInsight nella rete virtuale. Non sono necessarie altre operazioni di configurazione per far sì che HDInsight usi il server DNS configurato per la rete virtuale.

Per altre informazioni sulla connessione di due reti virtuali di Azure, vedere [Configurare una connessione da rete virtuale a rete virtuale](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

## <a name="create-kafka-clusters"></a>Creare cluster Kafka

Anche se è possibile creare manualmente cluster Kafka e una rete virtuale di Azure, è più semplice usare un modello di Azure Resource Manager. Seguire questa procedura per distribuire una rete virtuale di Azure e due cluster Kafka e nella sottoscrizione di Azure.

1. Usare il pulsante seguente per accedere ad Azure e aprire il modello nel portale di Azure.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json" target="_blank"><img src="./media/apache-kafka-mirroring/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Il modello di Azure Resource Manager è disponibile su **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json**.

    > [!WARNING]
    > Per garantire la disponibilità di Kafka in HDInsight, il cluster deve contenere almeno tre nodi del ruolo di lavoro. Questo modello crea un cluster Kafka contenente tre nodi di lavoro.

2. Usare le informazioni seguenti per popolare le voci nel pannello **Distribuzione personalizzata**:
    
    ![Distribuzione personalizzata di HDInsight](./media/apache-kafka-mirroring/parameters.png)
    
    * **Gruppo di risorse**: creare un gruppo o selezionarne uno esistente. Questo gruppo contiene il cluster HDInsight.

    * **Località**: scegliere una località geograficamente vicina.
     
    * **Base Cluster Name** (Nome di base del cluster): questo valore viene usato come nome di base per i cluster Kafka. Se ad esempio si immette **hdi** verranno creati cluster denominati **source-hdi** e **dest-hdi**.

    * **Cluster Login User Name** (Nome utente di accesso del cluster): nome utente amministratore per i cluster Kafka di origine e destinazione.

    * **Cluster Login Password** (Password di accesso del cluster): password dell'utente amministratore per i cluster Kafka di origine e destinazione.

    * **SSH User Name** (Nome utente SSH): utente SSH da creare per i cluster Kafka di origine e destinazione.

    * **SSH Password** (Password SSH): password dell'utente SSH per i cluster Kafka di origine e destinazione.

3. Leggere le **Condizioni** e quindi selezionare **Accetto le condizioni riportate sopra**.

4. Selezionare infine **Aggiungi al dashboard** e quindi **Acquista**. La creazione dei cluster richiede circa 20 minuti.

> [!IMPORTANT]
> I nomi dei cluster HDInsight sono **source-BASENAME** e **dest-BASENAME**, dove BASENAME è il nome specificato per il modello. Questi nomi verranno usati nei passaggi successivi per la connessione ai cluster.

## <a name="create-topics"></a>Creare argomenti

1. Connettersi al cluster di **origine** tramite SSH:

    ```bash
    ssh sshuser@source-BASENAME-ssh.azurehdinsight.net
    ```

    Sostituire **sshuser** con il nome utente SSH usato durante la creazione del cluster. Sostituire **BASENAME** con il nome di base usato durante la creazione del cluster.

    Per altre informazioni, vedere [Usare SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Usare i comandi seguenti per trovare gli host Zookeeper per il cluster di origine:

    ```bash
    # Install jq if it is not installed
    sudo apt -y install jq
    # get the zookeeper hosts for the source cluster
    export SOURCE_ZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    Sostituire `$CLUSTERNAME` con il nome del cluster di origine. Quando richiesto, immettere la password dell'account (admin) di accesso al cluster.

3. Per creare un argomento denominato `testtopic`, usare il comando seguente:

    ```bash
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

    `zk0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181,zk1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181`

    Salvare queste informazioni. Verranno usate nella sezione successiva.

## <a name="configure-mirroring"></a>Configurare il mirroring

1. Connettersi al cluster di **destinazione** con un'altra sessione SSH:

    ```bash
    ssh sshuser@dest-BASENAME-ssh.azurehdinsight.net
    ```

    Sostituire **sshuser** con il nome utente SSH usato durante la creazione del cluster. Sostituire **BASENAME** con il nome di base usato durante la creazione del cluster.

    Per altre informazioni, vedere [Usare SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Un file `consumer.properties` viene usato per configurare la comunicazione con il cluster di **origine**. Per creare il file, usare il comando seguente:

    ```bash
    nano consumer.properties
    ```

    Usare il testo seguente come contenuto del file `consumer.properties`:

    ```yaml
    zookeeper.connect=SOURCE_ZKHOSTS
    group.id=mirrorgroup
    ```

    Sostituire **SOURCE_ZKHOSTS** con le informazioni degli host Zookeeper presenti nel cluster di **origine**.

    Questo file descrive le informazioni sui consumer da usare durante la lettura dal cluster Kafka di origine. Per altre informazioni sulla configurazione dei consumer, vedere [Consumer Configs](https://kafka.apache.org/documentation#consumerconfigs) (Configurazione di consumer) in kafka.apache.org.

    Per salvare il file, usare **Ctrl + X**, **Y** e **INVIO**.

3. Prima di configurare il producer che comunica con il cluster di destinazione è necessario trovare gli host broker per il cluster di **destinazione** stesso. Usare i comandi seguenti per recuperare queste informazioni:

    ```bash
    sudo apt -y install jq
    DEST_BROKERHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $DEST_BROKERHOSTS
    ```

    Sostituire `$CLUSTERNAME` con il nome del cluster di destinazione. Quando richiesto, immettere la password dell'account (admin) di accesso al cluster.

    Il comando `echo` restituisce informazioni simili al testo seguente:

        wn0-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn1-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092

4. Un file `producer.properties` viene usato per comunicare il cluster di __destinazione__. Per creare il file, usare il comando seguente:

    ```bash
    nano producer.properties
    ```

    Usare il testo seguente come contenuto del file `producer.properties`:

    ```yaml
    bootstrap.servers=DEST_BROKERS
    compression.type=none
    ```

    Sostituire **DEST_BROKERS** con le informazioni del broker indicate nel passaggio precedente.

    Per altre informazioni sulla configurazione dei producer, vedere [Producer Configs](https://kafka.apache.org/documentation#producerconfigs) (Configurazione di producer) in kafka.apache.org.

5. Usare i comandi seguenti per trovare gli host Zookeeper per il cluster di destinazione:

    ```bash
    # Install jq if it is not installed
    sudo apt -y install jq
    # get the zookeeper hosts for the source cluster
    export DEST_ZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    Sostituire `$CLUSTERNAME` con il nome del cluster di destinazione. Quando richiesto, immettere la password dell'account (admin) di accesso al cluster.

7. La configurazione predefinita di Kafka in HDInsight non consente la creazione automatica di argomenti. È necessario scegliere una delle opzioni seguenti prima di avviare il processo di mirroring:

    * **Creare gli argomenti nel cluster di destinazione**: questa opzione consente inoltre di impostare il numero di partizioni e il fattore di replica.

        È possibile creare argomenti in anticipo usando il comando seguente:

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $DEST_ZKHOSTS
        ```

        Sostituire `testtopic` con il nome dell'argomento da creare.

    * **Configurare il cluster per la creazione automatica degli argomenti**: questa opzione consente a MirrorMaker di creare automaticamente gli argomenti, che potrebbero però venire creati con un numero di partizioni o un fattore di replica diverso rispetto all'argomento di origine.

        Per configurare il cluster di destinazione per creare automaticamente gli argomenti, seguire questa procedura:

        1. Dal [portale di Azure](https://portal.azure.com) selezionare il cluster Kafka di destinazione.
        2. Dalla panoramica del cluster selezionare __Dashboard cluster__. Selezionare quindi __Dashboard cluster HDInsight__. Quando viene chiesto, eseguire l'autenticazione usando le credenziali di accesso (amministratore) per il cluster.
        3. Selezionare il servizio __Kafka__ nell'elenco a sinistra della pagina.
        4. Selezionare __Configs__ (Configurazioni) nella parte centrale della pagina.
        5. Nel campo __Filtro__ immettere un valore `auto.create`. In questo modo, l'elenco di proprietà verrà filtrato e verrà visualizzata l'impostazione `auto.create.topics.enable`.
        6. Cambiare il valore di `auto.create.topics.enable` impostandolo su true e quindi selezionare __Salva__. Aggiungere una nota e selezionare di nuovo __Salva__.
        7. Selezionare il servizio __Kafka__, selezionare __Riavvia__ e quindi selezionare __Restart all affected__ (Riavvia tutte le istanze interessate). Quando viene chiesto, selezionare __Confirm restart all__ (Conferma riavvio di tutte le istanze).

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

    ```json
    {metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-3, security.protocol=PLAINTEXT}{metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-0, security.protocol=PLAINTEXT}
    metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-kafka.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-2, security.protocol=PLAINTEXT}
    metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-1, security.protocol=PLAINTEXT}
    ```

2. Dalla connessione SSH al cluster di **origine**, usare il comando seguente per avviare un producer e inviare messaggi all'argomento:

    ```bash
    SOURCE_BROKERHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

    Sostituire `$CLUSTERNAME` con il nome del cluster di origine. Quando richiesto, immettere la password dell'account (admin) di accesso al cluster.

     Quando si arriva a una riga vuota con un cursore, digitare alcuni messaggi di testo. Questi messaggi vengono inviati all'argomento nel cluster di **origine**. Al termine, usare **Ctrl + C** per chiudere il processo del producer.

3. Dalla connessione SSH al cluster di **destinazione**, usare **Ctrl + C** per chiudere il processo MirrorMaker. Per terminare il processo, potrebbero essere necessari alcuni secondi. Per verificare che i messaggi siano stati replicati nella destinazione, usare il comando seguente:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $DEST_ZKHOSTS --topic testtopic --from-beginning
    ```

    Sostituire `$CLUSTERNAME` con il nome del cluster di destinazione. Quando richiesto, immettere la password dell'account (admin) di accesso al cluster.

    L'elenco degli argomenti include ora `testtopic`, che viene creato quando MirrorMaster esegue il mirroring dell'argomento dal cluster di origine a quello di destinazione. I messaggi recuperati dall'argomento sono gli stessi immessi nel cluster di origine.

## <a name="delete-the-cluster"></a>Eliminare il cluster

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Le procedure illustrate in questo documento creano entrambi i cluster nello stesso gruppo di risorse di Azure. È quindi possibile eliminare il gruppo di risorse dal portale di Azure. In questo modo vengono rimosse tutte le risorse create seguendo le istruzioni di questo documento, la rete virtuale di Azure e l'account di archiviazione usato dai cluster.

## <a name="next-steps"></a>Passaggi successivi

In questo documento è stato descritto come usare MirrorMaker per creare la replica di un cluster Kafka. Per trovare altri modi per lavorare con Kafka, vedere i collegamenti seguenti:

* [Documentazione su Apache Kafka MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) in cwiki.apache.org.
* [Introduzione ad Apache Kafka (anteprima) in HDInsight](apache-kafka-get-started.md)
* [Use Apache Spark with Kafka on HDInsight](../hdinsight-apache-spark-with-kafka.md) (Usare Apache Spark con Kafka in HDInsight)
* [Usare Apache Storm (anteprima) con Kafka in HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Connect to Kafka through an Azure Virtual Network](apache-kafka-connect-vpn-gateway.md) (Connettersi a Kafka tramite una rete virtuale di Azure)
