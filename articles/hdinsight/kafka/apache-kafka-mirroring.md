---
title: 'Eseguire il mirroring degli argomenti di Apache Kafka: Azure HDInsight'
description: Informazioni su come usare la funzionalità di mirroring di Apache Kafka per gestire una replica di un cluster Kafka in HDInsight eseguendo il mirroring degli argomenti in un cluster secondario.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/24/2019
ms.openlocfilehash: bdc393d041bd40fd27493ccc8f3c4f39adfa35b2
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657195"
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>Usare MirrorMaker per replicare gli argomenti di Apache Kafka con Kafka in HDInsight

Informazioni su come usare la funzionalità di mirroring di Apache Kafka per replicare gli argomenti in un cluster secondario. Il mirroring può essere eseguito come processo continuo o usato in modo intermittente come metodo di migrazione dei dati da un cluster all'altro.

In questo esempio il mirroring viene usato per replicare argomenti tra due cluster HDInsight. Entrambi i cluster sono in diverse reti virtuali in diversi Data Center.

> [!WARNING]  
> Il mirroring non deve essere considerato un mezzo per ottenere la tolleranza di errore. L'offset per gli elementi all'interno di un argomento sono diversi tra i cluster primari e secondari, in modo che i client non è possibile usare i due in modo intercambiabile.
>
> Per preservare la tolleranza di errore è necessario impostare la replica per gli argomenti all'interno del cluster. Per altre informazioni, vedere [Introduzione ad Apache Kafka in HDInsight](apache-kafka-get-started.md).

## <a name="how-apache-kafka-mirroring-works"></a>Funzionamento del mirroring di Apache Kafka

Il mirroring Usa la [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) strumento (parte di Apache Kafka) per utilizzare i record degli argomenti nel cluster primario e quindi creare una copia locale nel cluster secondario. MirrorMaker usa uno (o più) *consumatore* che leggono dal cluster primario e una *producer* che scrive nel cluster locale (secondario).

Le impostazioni di mirroring più utili per il ripristino di emergenza Usa cluster Kafka in diverse aree di Azure. A tale scopo, le reti virtuali in cui si trovano i cluster vengono eseguito il peering tra loro.

Il diagramma seguente illustra il processo di mirroring e come i flussi delle comunicazioni tra i cluster:

![Diagramma del processo di mirroring](./media/apache-kafka-mirroring/kafka-mirroring-vnets2.png)

Il cluster primario e secondario può essere diverso nel numero di nodi e partizioni, e gli offset negli argomenti sono anche diversi. Il mirroring mantiene il valore della chiave usato per il partizionamento, quindi l'ordine dei record viene conservato in base alla chiave.

### <a name="mirroring-across-network-boundaries"></a>Mirroring tra i limiti di rete

Se è necessario eseguire il mirroring di cluster Kafka in reti diverse, si notino le seguenti considerazioni aggiuntive:

* **Gateway**: Le reti devono essere in grado di comunicare a livello di TCP/IP.

* **Server addressing**: È possibile scegliere soddisfare i nodi del cluster usando i propri indirizzi IP o nomi di dominio completo.

    * **Indirizzi IP**: Se si configurano i cluster Kafka per l'uso di annunci pubblicitari di indirizzo IP, è possibile procedere con la configurazione del mirroring usando gli indirizzi IP dei nodi broker e nodi zookeeper.
    
    * **I nomi di dominio**: Se non si configura il cluster Kafka per la pubblicità IP indirizzo, il cluster deve essere in grado di connettersi tra loro usando nomi di dominio completi (FQDN). Ciò richiede un server di sistema DNS (Domain Name) in ogni rete configurato per inoltrare le richieste ad altre reti. Quando si crea una rete virtuale di Azure, invece di usare il DNS automatico fornito con la rete è necessario specificare un server DNS personalizzato con il relativo indirizzo IP. Dopo aver creato la rete virtuale è necessario creare una macchina virtuale di Azure che usi quell'indirizzo IP, quindi installare e configurare il software DNS sulla macchina stessa.

    > [!WARNING]  
    > Creare e configurare il server DNS personalizzato prima di installare HDInsight nella rete virtuale. Non sono necessarie altre operazioni di configurazione per far sì che HDInsight usi il server DNS configurato per la rete virtuale.

Per altre informazioni sulla connessione di due reti virtuali di Azure, vedere [Configurare una connessione da rete virtuale a rete virtuale](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

## <a name="mirroring-architecture"></a>Architettura del mirroring

Questa architettura offre due cluster in diversi gruppi di risorse e le reti virtuali: un **primari** e **secondario**.

### <a name="creation-steps"></a>Passaggi per la creazione

1. Creare due nuovi gruppi di risorse:

    |Gruppo di risorse | Location |
    |---|---|
    | kafka-primary-rg | Stati Uniti centrali |
    | kafka-secondary-rg | Stati Uniti centro-settentrionali |

1. Creare una nuova rete virtuale **kafka-primary-vnet** nelle **kafka-primary-rg**. Lasciare le impostazioni predefinite.
1. Creare una nuova rete virtuale **kafka-secondario-vnet** nelle **kafka-secondario-rg**, anche con le impostazioni predefinite.

1. Creare due nuovi cluster Kafka:

    | Nome cluster | Gruppo di risorse | Rete virtuale | Account di archiviazione |
    |---|---|---|---|
    | kafka-primary-cluster | kafka-primary-rg | kafka-primary-vnet | kafkaprimarystorage |
    | kafka-secondary-cluster | kafka-secondary-rg | kafka-secondary-vnet | kafkasecondarystorage |

1. Creare il peering di rete virtuale. Questo passaggio verrà creato il peering di due: uno dalla **kafka-primary-vnet** al **kafka-secondario-vnet** e uno nuovo da **kafka-secondario-vnet** a  **kafka-primary-vnet**.
    1. Selezionare il **kafka-primary-vnet** rete virtuale.
    1. Fare clic su **peering** sotto **impostazioni**.
    1. Fare clic su **Aggiungi**.
    1. Nel **Aggiungi peering** schermata, immettere i dettagli come illustrato nello screenshot seguente.

        ![aggiungere il peering reti virtuali](./media/apache-kafka-mirroring/add-vnet-peering.png)

1. Configurare la pubblicità IP:
    1. Passare al dashboard di Ambari per il cluster primario: `https://PRIMARYCLUSTERNAME.azurehdinsight.net`.
    1. Fare clic su **Services** > **Kafka**. Fare clic sulla scheda **Configurazioni** .
    1. Aggiungere le righe di configurazione seguenti nella parte inferiore **kafka-env modello** sezione. Fare clic su **Save**.
    
        ```
        # Configure Kafka to advertise IP addresses instead of FQDN
        IP_ADDRESS=$(hostname -i)
        echo advertised.listeners=$IP_ADDRESS
        sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
        echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
        ```

    1. Immettere una nota **Salva la configurazione** dello schermo e fare clic su **salvare**.
    1. Se viene richiesto con avviso di configurazione, fare clic su **procedere comunque**.
    1. Fare clic su **accettabile** nel **salvare le modifiche alla configurazione**.
    1. Fare clic su **riavviare** > **riavviare All Affected** nel **Restart Required** notifica. Fare clic su **confermare il riavvio tutti**.

        ![riavviare i nodi di kafka](./media/apache-kafka-mirroring/ambari-restart-notification.png)

1. Configurare Kafka per l'ascolto su tutte le interfacce di rete.
    1. Restare sempre aggiornato riguardo il **Configs** disponibile nella scheda **Services** > **Kafka**. Nel **Broker Kafka** sezione set di **listener** proprietà `PLAINTEXT://0.0.0.0:9092`.
    1. Fare clic su **Save**.
    1. Fare clic su **riavviare**, e **conferma riavvio tutti**.

1. Registrare gli indirizzi IP dei Broker e Zookeeper indirizzi per il cluster primario.
    1. Fare clic su **host** nel dashboard di Ambari.
    1. Prendere nota degli indirizzi IP per il Broker e Zookeeper. I nodi di Service broker hanno **wn** come le prime due lettere del nome host e nodo zookeeper nodi hanno **zk** come le prime due lettere del nome host.

        ![Visualizza indirizzi ip](./media/apache-kafka-mirroring/view-node-ip-addresses2.png)

1. Ripetere i tre passaggi precedenti per il secondo cluster **kafka-secondario-cluster**: configurare la pubblicità IP, impostare i listener e prendere nota degli indirizzi IP di Zookeeper e Broker.

## <a name="create-topics"></a>Creare argomenti

1. Connettere il **primario** del cluster tramite SSH:

    ```bash
    ssh sshuser@PRIMARYCLUSTER-ssh.azurehdinsight.net
    ```

    Sostituire **sshuser** con il nome utente SSH usato durante la creazione del cluster. Sostituire **BASENAME** con il nome di base usato durante la creazione del cluster.

    Per altre informazioni, vedere [Usare SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Usare il comando seguente per creare una variabile con l'host di Apache Zookeeper per il cluster primario. Ad esempio le stringhe `ZOOKEEPER_IP_ADDRESS1` devono essere sostituite con gli indirizzi IP effettivi registrati in precedenza, ad esempio `10.23.0.11` e `10.23.0.7`. Se si usa la risoluzione FQDN con un server DNS personalizzato, seguire [questi passaggi](apache-kafka-get-started.md#getkafkainfo) per ottenere i nomi di Service broker e zookeeper.:

    ```bash
    # get the zookeeper hosts for the primary cluster
    export PRIMARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181, ZOOKEEPER_IP_ADDRESS2:2181, ZOOKEEPER_IP_ADDRESS3:2181'
    ```

3. Per creare un argomento denominato `testtopic`, usare il comando seguente:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $PRIMARY_ZKHOSTS
    ```

3. Usare il comando seguente per verificare che l'argomento sia stato creato:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $PRIMARY_ZKHOSTS
    ```

    La risposta contiene `testtopic`.

4. Usare il comando seguente per visualizzare le informazioni sull'host Zookeeper per questo oggetto (il **primaria**) cluster:

    ```bash
    echo $PRIMARY_ZKHOSTS
    ```

    Verranno restituite informazioni simili al testo seguente:

    `10.23.0.11:2181,10.23.0.7:2181,10.23.0.9:2181`

    Salvare queste informazioni. Verranno usate nella sezione successiva.

## <a name="configure-mirroring"></a>Configurare il mirroring

1. Connettersi al **secondario** usando un'altra sessione SSH del cluster:

    ```bash
    ssh sshuser@SECONDARYCLUSTER-ssh.azurehdinsight.net
    ```

    Sostituire **sshuser** con il nome utente SSH usato durante la creazione del cluster. Sostituire **SECONDARYCLUSTER** con il nome usato durante la creazione del cluster.

    Per altre informazioni, vedere [Usare SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Oggetto `consumer.properties` file viene usato per configurare la comunicazione con il **primaria** cluster. Per creare il file, usare il comando seguente:

    ```bash
    nano consumer.properties
    ```

    Usare il testo seguente come contenuto del file `consumer.properties`:

    ```yaml
    zookeeper.connect=PRIMARY_ZKHOSTS
    group.id=mirrorgroup
    ```

    Sostituire **PRIMARY_ZKHOSTS** con gli indirizzi IP di Zookeeper dalle **primario** cluster.

    Questo file descrive le informazioni sui consumer da usare durante la lettura del cluster Kafka primario. Per altre informazioni sulla configurazione dei consumer, vedere [Consumer Configs](https://kafka.apache.org/documentation#consumerconfigs) (Configurazione di consumer) in kafka.apache.org.

    Per salvare il file, usare **Ctrl + X**, **Y** e **INVIO**.

3. Prima di configurare il producer che comunica con il cluster secondario, di una variabile per gli indirizzi IP di broker di installazione di **secondario** cluster. Usare i comandi seguenti per creare questa variabile:

    ```bash
    export SECONDARY_BROKERHOSTS='BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092'
    ```

    Il comando `echo $SECONDARY_BROKERHOSTS` restituisce informazioni simili al testo seguente:

    `10.23.0.14:9092,10.23.0.4:9092,10.23.0.12:9092`

4. Oggetto `producer.properties` file viene usato per comunicare il **secondario** cluster. Per creare il file, usare il comando seguente:

    ```bash
    nano producer.properties
    ```

    Usare il testo seguente come contenuto del file `producer.properties`:

    ```yaml
    bootstrap.servers=SECONDARY_BROKERHOSTS
    compression.type=none
    ```

    Sostituire **SECONDARY_BROKERHOSTS** con gli indirizzi IP di Service broker usati nel passaggio precedente.

    Per altre informazioni sulla configurazione dei producer, vedere [Producer Configs](https://kafka.apache.org/documentation#producerconfigs) (Configurazione di producer) in kafka.apache.org.

5. Usare i comandi seguenti per creare una variabile di ambiente con gli indirizzi IP degli host Zookeeper per il cluster secondario:

    ```bash
    # get the zookeeper hosts for the secondary cluster
    export SECONDARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181,ZOOKEEPER_IP_ADDRESS2:2181,ZOOKEEPER_IP_ADDRESS3:2181'
    ```

7. La configurazione predefinita di Kafka in HDInsight non consente la creazione automatica di argomenti. È necessario scegliere una delle opzioni seguenti prima di avviare il processo di mirroring:

    * **Creare gli argomenti nel cluster secondario**: questa opzione consente inoltre di impostare il numero di partizioni e il fattore di replica.

        È possibile creare argomenti in anticipo usando il comando seguente:

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SECONDARY_ZKHOSTS
        ```

        Sostituire `testtopic` con il nome dell'argomento da creare.

    * **Configurare il cluster per la creazione automatica degli argomenti**: Questa opzione consente a MirrorMaker di creare automaticamente gli argomenti, tuttavia, è possibile crearli con un numero diverso di partizioni o fattore di replica più l'argomento principale.

        Per configurare il cluster secondario per creare automaticamente gli argomenti, seguire questi passaggi:

        1. Passare al dashboard di Ambari per il cluster secondario: `https://SECONDARYCLUSTERNAME.azurehdinsight.net`.
        1. Fare clic su **Services** > **Kafka**. Fare clic sulla scheda **Configurazioni** .
        5. Nel campo __Filtro__ immettere un valore `auto.create`. In questo modo, l'elenco di proprietà verrà filtrato e verrà visualizzata l'impostazione `auto.create.topics.enable`.
        6. Cambiare il valore di `auto.create.topics.enable` impostandolo su true e quindi selezionare __Salva__. Aggiungere una nota e selezionare di nuovo __Salva__.
        7. Selezionare il servizio __Kafka__, selezionare __Riavvia__ e quindi selezionare __Restart all affected__ (Riavvia tutte le istanze interessate). Quando viene chiesto, selezionare __Confirm restart all__ (Conferma riavvio di tutte le istanze).

        ![configurare la creazione automatica di argomento](./media/apache-kafka-mirroring/kafka-enable-auto-create-topics.png)

## <a name="start-mirrormaker"></a>Avviare MirrorMaker

1. Dalla connessione SSH per il **secondario** del cluster, usare il comando seguente per avviare il processo MirrorMaker:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    I parametri usati in questo esempio sono i seguenti:

    * **--consumer.config**: specifica il file che contiene le proprietà del consumer. Queste proprietà vengono usate per creare un consumer che legge i *primaria* cluster Kafka.

    * **--producer.config**: specifica il file che contiene le proprietà del producer. Queste proprietà vengono usate per creare un producer che scrive il *secondario* cluster Kafka.

    * **--whitelist**: Un elenco di argomenti che vengono replicati da MirrorMaker dal cluster primario a quello secondario.

    * **--num.streams**: numero di thread consumer da creare.

    Il consumer nel nodo secondario è in attesa di ricevere messaggi.

2. Dalla connessione SSH per il **primaria** del cluster, usare il comando seguente per avviare un producer e inviare messaggi all'argomento:

    ```bash
    export PRIMARY_BROKERHOSTS=BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

     Quando si arriva a una riga vuota con un cursore, digitare alcuni messaggi di testo. I messaggi vengono inviati all'argomento di **primaria** cluster. Al termine, usare **Ctrl + C** per chiudere il processo del producer.

3. Dalla connessione SSH per il **secondari** del cluster, usare **Ctrl + C** per terminare il processo MirrorMaker. Per terminare il processo, potrebbero essere necessari alcuni secondi. Per verificare che i messaggi siano stati replicati nel database secondario, usare il comando seguente:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $SECONDARY_ZKHOSTS --topic testtopic --from-beginning
    ```

    L'elenco di argomenti include ora `testtopic`, che viene creato quando mirrormaster esegue il mirroring dell'argomento del cluster primario a quello secondario. I messaggi recuperati dall'argomento sono uguali a quelli che immessi nel cluster primario.

## <a name="delete-the-cluster"></a>Eliminare il cluster

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

I passaggi descritti in questo documento creato i cluster in gruppi di risorse di Azure diversi. Per eliminare tutte le risorse create, è possibile eliminare i gruppi di due risorse: **kafka-primary-rg** e **kafka-secondary_rg**. L'eliminazione di gruppi di risorse consente di rimuovere tutte le risorse create seguendo le istruzioni in questo documento, inclusi cluster, le reti virtuali e gli account di archiviazione.

## <a name="next-steps"></a>Fasi successive

In questo documento è stato descritto come usare [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) per creare la replica di un cluster [Apache Kafka](https://kafka.apache.org/). Per trovare altri modi per lavorare con Kafka, vedere i collegamenti seguenti:

* [Documentazione su Apache Kafka MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) in cwiki.apache.org.
* [Introduzione ad Apache Kafka (anteprima) in HDInsight](apache-kafka-get-started.md)
* [Usare Apache Spark con Apache Kafka in HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Usare Apache Storm con Apache Kafka in HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Connettersi ad Apache Kafka tramite una rete virtuale di Azure](apache-kafka-connect-vpn-gateway.md)
