---
title: 'Eseguire il mirroring degli argomenti di Apache Kafka: Azure HDInsight'
description: Informazioni su come usare la funzionalità di mirroring di Apache Kafka per gestire una replica di un cluster Kafka in HDInsight eseguendo il mirroring degli argomenti in un cluster secondario.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 2bd25ad823217c5e9260142912a3d2d748b9c15a
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74767706"
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>Usare MirrorMaker per replicare gli argomenti di Apache Kafka con Kafka in HDInsight

Informazioni su come usare la funzionalità di mirroring di Apache Kafka per replicare gli argomenti in un cluster secondario. Il mirroring può essere eseguito come processo continuo o usato in modo intermittente come metodo di migrazione dei dati da un cluster all'altro.

In questo esempio il mirroring viene usato per replicare argomenti tra due cluster HDInsight. Entrambi i cluster si trovano in reti virtuali diverse in data center diversi.

> [!WARNING]  
> Il mirroring non deve essere considerato un mezzo per ottenere la tolleranza di errore. L'offset per gli elementi all'interno di un argomento è diverso tra i cluster primari e secondari, quindi i client non possono usarli in modo intercambiabile.
>
> Per preservare la tolleranza di errore è necessario impostare la replica per gli argomenti all'interno del cluster. Per altre informazioni, vedere [Introduzione ad Apache Kafka in HDInsight](apache-kafka-get-started.md).

## <a name="how-apache-kafka-mirroring-works"></a>Funzionamento del mirroring di Apache Kafka

Il mirroring funziona usando lo strumento [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) (parte di Apache Kafka) per utilizzare i record degli argomenti del cluster primario e quindi creare una copia locale nel cluster secondario. MirrorMaker utilizza uno o più *consumer* che leggono dal cluster primario e un *Producer* che scrive nel cluster locale (secondario).

La configurazione del mirroring più utile per il ripristino di emergenza usa i cluster Kafka in diverse aree di Azure. A tale scopo, le reti virtuali in cui risiedono i cluster vengono sottoposto a peering insieme.

Il diagramma seguente illustra il processo di mirroring e il modo in cui il flusso di comunicazione tra i cluster:

![Diagramma del processo di mirroring](./media/apache-kafka-mirroring/kafka-mirroring-vnets2.png)

I cluster primari e secondari possono essere diversi nel numero di nodi e partizioni e gli offset all'interno degli argomenti sono diversi. Il mirroring mantiene il valore della chiave usato per il partizionamento, quindi l'ordine dei record viene conservato in base alla chiave.

### <a name="mirroring-across-network-boundaries"></a>Mirroring tra i limiti di rete

Se è necessario eseguire il mirroring di cluster Kafka in reti diverse, si notino le seguenti considerazioni aggiuntive:

* **Gateway**: le reti devono essere in grado di comunicare a livello di TCP/IP.

* **Indirizzamento del server**: è possibile scegliere di indirizzare i nodi del cluster usando i rispettivi indirizzi IP o nomi di dominio completi.

    * **Indirizzi IP**: se si configurano i cluster Kafka per l'uso della pubblicità degli indirizzi IP, è possibile procedere con la configurazione del mirroring usando gli indirizzi IP dei nodi broker e Zookeeper.
    
    * **Nomi di dominio**: se non si configurano i cluster Kafka per la pubblicità degli indirizzi IP, è necessario che i cluster siano in grado di connettersi tra loro usando nomi di dominio completi (FQDN). Questa operazione richiede un server Domain Name System (DNS) in ogni rete configurata per l'invio di richieste ad altre reti. Quando si crea una rete virtuale di Azure, invece di usare il DNS automatico fornito con la rete è necessario specificare un server DNS personalizzato con il relativo indirizzo IP. Dopo aver creato la rete virtuale è necessario creare una macchina virtuale di Azure che usi quell'indirizzo IP, quindi installare e configurare il software DNS sulla macchina stessa.

    > [!WARNING]  
    > Creare e configurare il server DNS personalizzato prima di installare HDInsight nella rete virtuale. Non sono necessarie altre operazioni di configurazione per far sì che HDInsight usi il server DNS configurato per la rete virtuale.

Per altre informazioni sulla connessione di due reti virtuali di Azure, vedere [Configurare una connessione da rete virtuale a rete virtuale](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

## <a name="mirroring-architecture"></a>Architettura di mirroring

Questa architettura include due cluster in gruppi di risorse e reti virtuali diversi, ovvero un database **primario** e un database **secondario**.

### <a name="creation-steps"></a>Procedura di creazione

1. Creare due nuovi gruppi di risorse:

    |Gruppo di risorse | Località |
    |---|---|
    | Kafka-primario-RG | Stati Uniti centrali |
    | Kafka-secondario-RG | Stati Uniti centro-settentrionali |

1. Creare una nuova rete virtuale **Kafka-Primary-VNET** in **Kafka-Primary-RG**. Lasciare le impostazioni predefinite.
1. Creare una nuova rete virtuale **Kafka-Secondary-VNET** in **Kafka-Secondary-RG**, anche con le impostazioni predefinite.

1. Creare due nuovi cluster Kafka:

    | Nome cluster | Gruppo di risorse | Rete virtuale | Account di archiviazione |
    |---|---|---|---|
    | Kafka-primario-cluster | Kafka-primario-RG | Kafka-Primary-VNET | kafkaprimarystorage |
    | Kafka-secondario-cluster | Kafka-secondario-RG | Kafka-secondario-VNET | kafkasecondarystorage |

1. Creare peering di rete virtuale. Questo passaggio creerà due peering: uno da **Kafka-Primary-VNET** a **Kafka-Secondary-VNET** e uno indietro da **Kafka-Secondary-VNET** a **Kafka-Primary-VNET**.
    1. Selezionare la rete virtuale **Kafka-Primary-VNET** .
    1. Selezionare **peer** in **Impostazioni**.
    1. Selezionare **Aggiungi**.
    1. Nella schermata **Aggiungi peering** immettere i dettagli, come illustrato nella schermata seguente.

        ![HDInsight Kafka-aggiunta del peering VNET](./media/apache-kafka-mirroring/hdi-add-vnet-peering.png)

1. Configurare la pubblicità IP:
    1. Passare al dashboard di Ambari per il cluster primario: `https://PRIMARYCLUSTERNAME.azurehdinsight.net`.
    1. Selezionare **servizi** > **Kafka**. CliSelectck la scheda **configs (configurazioni** ).
    1. Aggiungere le seguenti righe di configurazione alla sezione del **modello Kafka-ENV** inferiore. Selezionare **Salva**.

        ```
        # Configure Kafka to advertise IP addresses instead of FQDN
        IP_ADDRESS=$(hostname -i)
        echo advertised.listeners=$IP_ADDRESS
        sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
        echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
        ```

    1. Immettere una nota nella schermata **Salva configurazione** e fare clic su **Salva**.
    1. Se viene visualizzato un avviso di configurazione, fare clic su **continua**.
    1. Selezionare **OK** in **Salva modifiche configurazione**.
    1. Selezionare **riavvia** > **Riavvia tutti gli interessati** nella notifica **riavvio richiesto** . Selezionare **Confirm Restart All** (Conferma riavvio di tutti gli elementi).

        ![Riavvio di Apache Ambari tutti interessati](./media/apache-kafka-mirroring/ambari-restart-notification.png)

1. Configurare Kafka per l'ascolto su tutte le interfacce di rete.
    1. Rimanere nella scheda **configs (configurazioni** ) in **Services** > **Kafka**. Nella sezione **Kafka broker** impostare la proprietà **listeners** su `PLAINTEXT://0.0.0.0:9092`.
    1. Selezionare **Salva**.
    1. Selezionare **Riavvia**e **confermare riavvia tutto**.

1. Registrare gli indirizzi IP e gli indirizzi Zookeeper del broker per il cluster primario.
    1. Selezionare **host** nel dashboard di Ambari.
    1. Prendere nota degli indirizzi IP per i broker e Zookeeper. I nodi **broker hanno come** prime due lettere del nome host e i nodi Zookeeper hanno **ZK** come prime due lettere del nome host.

        ![Indirizzi IP del nodo di visualizzazione Apache Ambari](./media/apache-kafka-mirroring/view-node-ip-addresses2.png)

1. Ripetere i tre passaggi precedenti per il secondo cluster **Kafka-Secondary-cluster**: Configure IP Advertising, set Listeners e prendere nota degli indirizzi IP broker e Zookeeper.

## <a name="create-topics"></a>Creare argomenti

1. Connettersi al cluster **primario** tramite SSH:

    ```bash
    ssh sshuser@PRIMARYCLUSTER-ssh.azurehdinsight.net
    ```

    Sostituire **sshuser** con il nome utente SSH usato durante la creazione del cluster. Sostituire **PRIMARYCLUSTER** con il nome di base usato durante la creazione del cluster.

    Per altre informazioni, vedere [Usare SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Usare il comando seguente per creare una variabile con gli host Apache Zookeeper per il cluster primario. Le stringhe come `ZOOKEEPER_IP_ADDRESS1` devono essere sostituite con gli indirizzi IP effettivi registrati in precedenza, ad esempio `10.23.0.11` e `10.23.0.7`. Se si usa la risoluzione FQDN con un server DNS personalizzato, attenersi alla [procedura seguente](apache-kafka-get-started.md#getkafkainfo) per ottenere i nomi broker e Zookeeper.:

    ```bash
    # get the zookeeper hosts for the primary cluster
    export PRIMARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181, ZOOKEEPER_IP_ADDRESS2:2181, ZOOKEEPER_IP_ADDRESS3:2181'
    ```

1. Per creare un argomento denominato `testtopic`, usare il comando seguente:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $PRIMARY_ZKHOSTS
    ```

1. Usare il comando seguente per verificare che l'argomento sia stato creato:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $PRIMARY_ZKHOSTS
    ```

    La risposta contiene `testtopic`.

1. Usare il comando seguente per visualizzare le informazioni sull'host Zookeeper per il cluster ( **primario**):

    ```bash
    echo $PRIMARY_ZKHOSTS
    ```

    Verranno restituite informazioni simili al testo seguente:

    `10.23.0.11:2181,10.23.0.7:2181,10.23.0.9:2181`

    Salvare queste informazioni. Viene usato nella sezione successiva.

## <a name="configure-mirroring"></a>Configurare il mirroring

1. Connettersi al cluster **secondario** usando un'altra sessione SSH:

    ```bash
    ssh sshuser@SECONDARYCLUSTER-ssh.azurehdinsight.net
    ```

    Sostituire **sshuser** con il nome utente SSH usato durante la creazione del cluster. Sostituire **SECONDARYCLUSTER** con il nome usato durante la creazione del cluster.

    Per altre informazioni, vedere [Usare SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Viene usato un file `consumer.properties` per configurare la comunicazione con il cluster **primario** . Per creare il file, usare il comando seguente:

    ```bash
    nano consumer.properties
    ```

    Usare il testo seguente come contenuto del file `consumer.properties`:

    ```yaml
    zookeeper.connect=PRIMARY_ZKHOSTS
    group.id=mirrorgroup
    ```

    Sostituire **PRIMARY_ZKHOSTS** con gli indirizzi IP Zookeeper dal cluster **primario** .

    Questo file descrive le informazioni del consumer da usare durante la lettura dal cluster Kafka primario. Per altre informazioni sulla configurazione dei consumer, vedere [Consumer Configs](https://kafka.apache.org/documentation#consumerconfigs) (Configurazione di consumer) in kafka.apache.org.

    Per salvare il file, usare **Ctrl + X**, **Y** e **INVIO**.

1. Prima di configurare il producer che comunica con il cluster secondario, configurare una variabile per gli indirizzi IP del broker del cluster **secondario** . Usare i comandi seguenti per creare questa variabile:

    ```bash
    export SECONDARY_BROKERHOSTS='BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092'
    ```

    Il comando `echo $SECONDARY_BROKERHOSTS` dovrebbe restituire informazioni simili al testo seguente:

    `10.23.0.14:9092,10.23.0.4:9092,10.23.0.12:9092`

1. Per comunicare il cluster **secondario** viene usato un file `producer.properties`. Per creare il file, usare il comando seguente:

    ```bash
    nano producer.properties
    ```

    Usare il testo seguente come contenuto del file `producer.properties`:

    ```yaml
    bootstrap.servers=SECONDARY_BROKERHOSTS
    compression.type=none
    ```

    Sostituire **SECONDARY_BROKERHOSTS** con gli indirizzi IP del broker usati nel passaggio precedente.

    Per altre informazioni sulla configurazione dei producer, vedere [Producer Configs](https://kafka.apache.org/documentation#producerconfigs) (Configurazione di producer) in kafka.apache.org.

1. Usare i comandi seguenti per creare una variabile di ambiente con gli indirizzi IP degli host Zookeeper per il cluster secondario:

    ```bash
    # get the zookeeper hosts for the secondary cluster
    export SECONDARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181,ZOOKEEPER_IP_ADDRESS2:2181,ZOOKEEPER_IP_ADDRESS3:2181'
    ```

1. La configurazione predefinita di Kafka in HDInsight non consente la creazione automatica di argomenti. È necessario scegliere una delle opzioni seguenti prima di avviare il processo di mirroring:

    * **Creare gli argomenti nel cluster secondario**: questa opzione consente anche di impostare il numero di partizioni e il fattore di replica.

        È possibile creare argomenti in anticipo usando il comando seguente:

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SECONDARY_ZKHOSTS
        ```

        Sostituire `testtopic` con il nome dell'argomento da creare.

    * **Configurare il cluster per la creazione automatica dell'argomento**: questa opzione consente a MirrorMaker di creare automaticamente gli argomenti, tuttavia può crearli con un numero diverso di partizioni o un fattore di replica rispetto all'argomento primario.

        Per configurare il cluster secondario per la creazione automatica di argomenti, seguire questa procedura:

        1. Passare al dashboard di Ambari per il cluster secondario: `https://SECONDARYCLUSTERNAME.azurehdinsight.net`.
        1. Fare clic su **servizi** > **Kafka**. Fare clic sulla scheda **Configurazioni** .
        1. Nel campo __Filtro__ immettere un valore `auto.create`. In questo modo, l'elenco di proprietà verrà filtrato e verrà visualizzata l'impostazione `auto.create.topics.enable`.
        1. Cambiare il valore di `auto.create.topics.enable` impostandolo su true e quindi selezionare __Salva__. Aggiungere una nota e selezionare di nuovo __Salva__.
        1. Selezionare il servizio __Kafka__, selezionare __Riavvia__ e quindi selezionare __Restart all affected__ (Riavvia tutte le istanze interessate). Quando viene chiesto, selezionare __Confirm restart all__ (Conferma riavvio di tutte le istanze).

        ![argomenti relativi alla creazione automatica di Kafka](./media/apache-kafka-mirroring/kafka-enable-auto-create-topics.png)

## <a name="start-mirrormaker"></a>Avviare MirrorMaker

1. Dalla connessione SSH al cluster **secondario** , usare il comando seguente per avviare il processo MirrorMaker:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    I parametri usati in questo esempio sono i seguenti:

    |Parametro |Description |
    |---|---|
    |--consumer. config|specifica il file che contiene le proprietà del consumer. Queste proprietà vengono usate per creare un consumer che legge dal cluster Kafka *primario* .|
    |--Producer. config|specifica il file che contiene le proprietà del producer. Queste proprietà vengono usate per creare un producer che scrive nel cluster Kafka *secondario* .|
    |--whitelist|Un elenco di argomenti che MirrorMaker replica dal cluster primario al database secondario.|
    |--Num. flussi|numero di thread consumer da creare.|

    Il consumer nel nodo secondario è ora in attesa di ricevere messaggi.

2. Dalla connessione SSH al cluster **primario** , usare il comando seguente per avviare un producer e inviare messaggi all'argomento:

    ```bash
    export PRIMARY_BROKERHOSTS=BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

     Quando si arriva a una riga vuota con un cursore, digitare alcuni messaggi di testo. I messaggi vengono inviati all'argomento nel cluster **primario** . Al termine, usare **Ctrl + C** per chiudere il processo del producer.

3. Dalla connessione SSH al cluster **secondario** , usare **CTRL + C** per terminare il processo MirrorMaker. Per terminare il processo, potrebbero essere necessari alcuni secondi. Per verificare che i messaggi siano stati replicati nel database secondario, utilizzare il comando seguente:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $SECONDARY_ZKHOSTS --topic testtopic --from-beginning
    ```

    L'elenco di argomenti include ora `testtopic`, che viene creato quando MirrorMaster esegue il mirroring dell'argomento dal cluster primario al database secondario. I messaggi recuperati dall'argomento sono identici a quelli immessi nel cluster primario.

## <a name="delete-the-cluster"></a>Eliminare il cluster

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

I passaggi descritti in questo documento hanno creato cluster in diversi gruppi di risorse di Azure. Per eliminare tutte le risorse create, è possibile eliminare i due gruppi di risorse creati: **Kafka-Primary-RG** e **Kafka-secondary_rg**. L'eliminazione dei gruppi di risorse consente di rimuovere tutte le risorse create seguendo questo documento, inclusi cluster, reti virtuali e account di archiviazione.

## <a name="next-steps"></a>Passaggi successivi

In questo documento è stato descritto come usare [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) per creare la replica di un cluster [Apache Kafka](https://kafka.apache.org/). Per trovare altri modi per lavorare con Kafka, vedere i collegamenti seguenti:

* [Documentazione su Apache Kafka MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) in cwiki.apache.org.
* [Introduzione ad Apache Kafka (anteprima) in HDInsight](apache-kafka-get-started.md)
* [Usare Apache Spark con Apache Kafka in HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Usare Apache Storm con Apache Kafka in HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Connettersi ad Apache Kafka tramite una rete virtuale di Azure](apache-kafka-connect-vpn-gateway.md)
