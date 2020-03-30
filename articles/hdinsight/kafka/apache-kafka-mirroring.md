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
ms.openlocfilehash: 45977f52226fac0a3e23455ce9457a721947a8cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425885"
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>Usare MirrorMaker per replicare gli argomenti di Apache Kafka con Kafka in HDInsight

Informazioni su come usare la funzionalità di mirroring di Apache Kafka per replicare gli argomenti in un cluster secondario. Il mirroring può essere eseguito come processo continuo o usato in modo intermittente come metodo di migrazione dei dati da un cluster all'altro.

In questo esempio il mirroring viene usato per replicare argomenti tra due cluster HDInsight. Entrambi i cluster si trovano in reti virtuali diverse in data center diversi.

> [!WARNING]  
> Il mirroring non deve essere considerato un mezzo per ottenere la tolleranza di errore. L'offset degli elementi all'interno di un argomento è diverso tra i cluster primari o secondari, pertanto i client non possono utilizzare i due in modo intercambiabile.
>
> Per preservare la tolleranza di errore è necessario impostare la replica per gli argomenti all'interno del cluster. Per altre informazioni, vedere [Introduzione ad Apache Kafka in HDInsight](apache-kafka-get-started.md).

## <a name="how-apache-kafka-mirroring-works"></a>Funzionamento del mirroring di Apache Kafka

Il mirroring funziona utilizzando lo strumento [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) (parte di Apache Kafka) per utilizzare i record degli argomenti nel cluster primario e quindi creare una copia locale nel cluster secondario. MirrorMaker utilizza uno (o più) *consumer* che leggono dal cluster primario e un *producer* che scrive nel cluster locale (secondario).

La configurazione di mirroring più utile per il ripristino di emergenza utilizza cluster Kafka in diverse aree di Azure.The most useful mirroring setup for disaster recovery utilizes Kafka clusters in different Azure regions. A tale scopo, le reti virtuali in cui risiedono i cluster vengono sottoposte a peering insieme.

Il diagramma seguente illustra il processo di mirroring e il flusso della comunicazione tra i cluster:The following diagram illustrates the mirroring process and how the communication flows between clusters:

![Diagramma del processo di mirroring](./media/apache-kafka-mirroring/kafka-mirroring-vnets2.png)

I cluster primario e secondario possono essere diversi per il numero di nodi e partizioni e anche gli offset all'interno degli argomenti sono diversi. Il mirroring mantiene il valore della chiave usato per il partizionamento, quindi l'ordine dei record viene conservato in base alla chiave.

### <a name="mirroring-across-network-boundaries"></a>Mirroring tra i limiti di rete

Se è necessario eseguire il mirroring di cluster Kafka in reti diverse, si notino le seguenti considerazioni aggiuntive:

* **Gateway:** le reti devono essere in grado di comunicare a livello TCP/IP.

* **Indirizzamento server**: È possibile scegliere di indirizzare i nodi del cluster utilizzando i relativi indirizzi IP o nomi di dominio completi.

    * **Indirizzi IP**: se si configurano i cluster Kafka per l'utilizzo della pubblicità degli indirizzi IP, è possibile procedere con la configurazione del mirroring utilizzando gli indirizzi IP dei nodi broker e dei nodi zookeeper.
    
    * **Nomi di dominio**: Se non si configurano i cluster Kafka per l'annuncio degli indirizzi IP, i cluster devono essere in grado di connettersi tra loro utilizzando nomi di dominio completi (FQDN). Ciò richiede un server DNS (Domain Name System) in ogni rete configurata per inoltrare le richieste alle altre reti. Quando si crea una rete virtuale di Azure, invece di usare il DNS automatico fornito con la rete è necessario specificare un server DNS personalizzato con il relativo indirizzo IP. Dopo aver creato la rete virtuale è necessario creare una macchina virtuale di Azure che usi quell'indirizzo IP, quindi installare e configurare il software DNS sulla macchina stessa.

    > [!WARNING]  
    > Creare e configurare il server DNS personalizzato prima di installare HDInsight nella rete virtuale. Non sono necessarie altre operazioni di configurazione per far sì che HDInsight usi il server DNS configurato per la rete virtuale.

Per altre informazioni sulla connessione di due reti virtuali di Azure, vedere [Configurare una connessione da rete virtuale a rete virtuale](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

## <a name="mirroring-architecture"></a>Architettura di mirroring

Questa architettura include due cluster in gruppi di risorse e reti virtuali diversi: **primario** e **secondario.**

### <a name="creation-steps"></a>Passaggi di creazione

1. Creare due nuovi gruppi di risorse:Create two new resource groups:

    |Gruppo di risorse | Location |
    |---|---|
    | kafka-primario-rg | Stati Uniti centrali |
    | kafka-secondario-rg | Stati Uniti centro-settentrionali |

1. Creare una nuova rete virtuale **kafka-primary-vnet** in **kafka-primary-rg**. Lasciare le impostazioni predefinite.
1. Creare una nuova rete virtuale **kafka-secondary-vnet** in **kafka-secondary-rg,** anche con le impostazioni predefinite.

1. Creare due nuovi cluster Kafka:

    | Nome cluster | Gruppo di risorse | Rete virtuale | Account di archiviazione |
    |---|---|---|---|
    | kafka-primario-cluster | kafka-primario-rg | kafka-principale-vnet | kafkaprimarystorage |
    | kafka-gruppo secondario | kafka-secondario-rg | kafka-secondario-vnet | kafkasecondarystorage |

1. Creare peering di rete virtuale. Questo passaggio creerà due peering: uno da **kafka-primary-vnet** a **kafka-secondary-vnet** e uno indietro da **kafka-secondary-vnet** a **kafka-primary-vnet**.
    1. Selezionare la rete virtuale **kafka-primari-vnet.**
    1. Selezionare **Peerings** in **Settings (Impostazioni)**.
    1. Selezionare **Aggiungi**.
    1. Nella schermata **Aggiungi peering** immettere i dettagli come illustrato nella schermata seguente.

        ![HDInsight Kafka aggiungere il peering vnetHDInsight Kafka add vnet peering](./media/apache-kafka-mirroring/hdi-add-vnet-peering.png)

### <a name="configure-ip-advertising"></a>Configurare la pubblicità IP

Configurare la pubblicità IP per consentire a un client di connettersi utilizzando gli indirizzi IP del broker anziché i nomi di dominio.

1. Passare al dashboard di Ambari `https://PRIMARYCLUSTERNAME.azurehdinsight.net`per il cluster primario: .
1. Selezionare **Servizi** > **Kafka**. CliSelectck la scheda **Configs(Configs).**
1. Aggiungere le seguenti righe di configurazione alla sezione del **modello kafka-env** inferiore. Selezionare **Salva**.

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. Immettere una nota nella schermata **Salva configurazione** e fare clic su **Salva**.
1. Se viene visualizzato un avviso di configurazione, fare clic su **Procedi comunque**.
1. Selezionare **OK** nella pagina **Salva modifiche configurazione**.
1. Selezionare **Riavvia** > **riavvia tutto interessato** nella notifica Riavvia **richiesto.** Selezionare **Conferma riavvio tutto**.

    ![Apache Ambari riavviare tutti gli interessati](./media/apache-kafka-mirroring/ambari-restart-notification.png)

### <a name="configure-kafka-to-listen-on-all-network-interfaces"></a>Configurare Kafka per l'ascolto su tutte le interfacce di rete.
    
1. Rimanere nella scheda **Configurazioni** in **Servizi** > **Kafka**. Nella sezione **Kafka Broker** impostare `PLAINTEXT://0.0.0.0:9092`la proprietà **listeners** su .
1. Selezionare **Salva**.
1. Selezionare **Riavvia**e **Conferma riavvio tutto**.

### <a name="record-broker-ip-addresses-and-zookeeper-addresses-for-primary-cluster"></a>Registrare gli indirizzi IP del broker e gli indirizzi del gestore dello zoo per il cluster primario.

1. Selezionare **Host** nel dashboard di Ambari.
1. Prendere nota degli indirizzi IP per i broker e i custodi dello zoo. I nodi broker hanno **wn** come le prime due lettere del nome host, e i nodi zookeeper hanno **zk** come le prime due lettere del nome host.

    ![Indirizzi IP del nodo di visualizzazione Apache Ambari](./media/apache-kafka-mirroring/view-node-ip-addresses2.png)

1. Ripetere i tre passaggi precedenti per il secondo cluster **kafka-secondary-cluster:** configurare la pubblicità IP, impostare i listener e prendere nota degli indirizzi IP del broker e dello zoo.

## <a name="create-topics"></a>Creare argomenti

1. Connettersi al cluster **primario** utilizzando SSH:

    ```bash
    ssh sshuser@PRIMARYCLUSTER-ssh.azurehdinsight.net
    ```

    Sostituire **sshuser** con il nome utente SSH usato durante la creazione del cluster. Sostituire **PRIMARYCLUSTER** con il nome di base utilizzato durante la creazione del cluster.

    Per altre informazioni, vedere [Usare SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Utilizzare il comando seguente per creare una variabile con gli host Apache zookeeper per il cluster primario. Le stringhe `ZOOKEEPER_IP_ADDRESS1` come devono essere sostituite con gli `10.23.0.11` indirizzi `10.23.0.7`IP effettivi registrati in precedenza, ad esempio e . Se si utilizza la risoluzione FQDN con un server DNS personalizzato, [attenersi](apache-kafka-get-started.md#getkafkainfo) alla seguente procedura per ottenere i nomi di broker e zookeeper.:

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

1. Utilizzare quanto segue per visualizzare le informazioni sull'host di per questo cluster **(principale):**

    ```bash
    echo $PRIMARY_ZKHOSTS
    ```

    Verranno restituite informazioni simili al testo seguente:

    `10.23.0.11:2181,10.23.0.7:2181,10.23.0.9:2181`

    Salvare queste informazioni. Viene utilizzato nella sezione successiva.

## <a name="configure-mirroring"></a>Configurare il mirroring

1. Connettersi al cluster **secondario** utilizzando una sessione SSH diversa:

    ```bash
    ssh sshuser@SECONDARYCLUSTER-ssh.azurehdinsight.net
    ```

    Sostituire **sshuser** con il nome utente SSH usato durante la creazione del cluster. Sostituire **SECONDARYCLUSTER** con il nome utilizzato durante la creazione del cluster.

    Per altre informazioni, vedere [Usare SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Un `consumer.properties` file viene utilizzato per configurare la comunicazione con il cluster **primario.** Per creare il file, usare il comando seguente:

    ```bash
    nano consumer.properties
    ```

    Usare il testo seguente come contenuto del file `consumer.properties`:

    ```yaml
    zookeeper.connect=PRIMARY_ZKHOSTS
    group.id=mirrorgroup
    ```

    Sostituire **PRIMARY_ZKHOSTS** con gli indirizzi IP del cluster **primario.**

    Questo file descrive le informazioni sui consumatori da utilizzare durante la lettura dal cluster Kafka primario. Per altre informazioni sulla configurazione dei consumer, vedere [Consumer Configs](https://kafka.apache.org/documentation#consumerconfigs) (Configurazione di consumer) in kafka.apache.org.

    Per salvare il file, usare **Ctrl + X**, **Y** e **INVIO**.

1. Prima di configurare il producer che comunica con il cluster secondario, impostare una variabile per gli indirizzi IP del broker del cluster **secondario.** Utilizzare i seguenti comandi per creare questa variabile:

    ```bash
    export SECONDARY_BROKERHOSTS='BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092'
    ```

    Il `echo $SECONDARY_BROKERHOSTS` comando deve restituire informazioni simili al testo seguente:

    `10.23.0.14:9092,10.23.0.4:9092,10.23.0.12:9092`

1. Un `producer.properties` file viene utilizzato per comunicare il cluster **secondario.** Per creare il file, usare il comando seguente:

    ```bash
    nano producer.properties
    ```

    Usare il testo seguente come contenuto del file `producer.properties`:

    ```yaml
    bootstrap.servers=SECONDARY_BROKERHOSTS
    compression.type=none
    ```

    Sostituire **SECONDARY_BROKERHOSTS** con gli indirizzi IP del broker utilizzati nel passaggio precedente.

    Per altre informazioni sulla configurazione dei producer, vedere [Producer Configs](https://kafka.apache.org/documentation#producerconfigs) (Configurazione di producer) in kafka.apache.org.

1. Utilizzare i comandi seguenti per creare una variabile di ambiente con gli indirizzi IP degli host di  per il cluster secondario:

    ```bash
    # get the zookeeper hosts for the secondary cluster
    export SECONDARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181,ZOOKEEPER_IP_ADDRESS2:2181,ZOOKEEPER_IP_ADDRESS3:2181'
    ```

1. La configurazione predefinita per Kafka in HDInsight non consente la creazione automatica di argomenti. È necessario scegliere una delle opzioni seguenti prima di avviare il processo di mirroring:

    * **Creare gli argomenti nel cluster secondario:** questa opzione consente inoltre di impostare il numero di partizioni e il fattore di replica.

        È possibile creare argomenti in anticipo usando il comando seguente:

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SECONDARY_ZKHOSTS
        ```

        Sostituire `testtopic` con il nome dell'argomento da creare.

    * **Configurare il cluster per la creazione automatica**degli argomenti: questa opzione consente a MirrorMaker di creare automaticamente gli argomenti, tuttavia può crearli con un numero diverso di partizioni o fattore di replica rispetto all'argomento principale.

        Per configurare il cluster secondario per la creazione automatica degli argomenti, eseguire la procedura seguente:To configure the secondary cluster to automatically create topics, perform these steps:

        1. Passare al dashboard di Ambari `https://SECONDARYCLUSTERNAME.azurehdinsight.net`per il cluster secondario: .
        1. Fare clic su **Servizi** > **Kafka**. Fare clic sulla scheda **Configurazioni** .
        1. Nel campo __Filtro__ immettere `auto.create`un valore di . In questo modo, l'elenco di proprietà verrà filtrato e verrà visualizzata l'impostazione `auto.create.topics.enable`.
        1. Cambiare il valore di `auto.create.topics.enable` impostandolo su true e quindi selezionare __Salva__. Aggiungere una nota, quindi selezionare di nuovo __Salva.__
        1. Selezionare il servizio __Kafka__ , selezionare __Riavvia__, quindi __selezionare Riavvia tutto interessato__. Quando richiesto, selezionare __Conferma riavviare tutti i__file .

        ![kafka abilitare la creazione automatica di argomenti](./media/apache-kafka-mirroring/kafka-enable-auto-create-topics.png)

## <a name="start-mirrormaker"></a>Avviare MirrorMaker

1. Dalla connessione SSH al cluster **secondario,** utilizzare il seguente comando per avviare il processo MirrorMaker:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    I parametri usati in questo esempio sono i seguenti:

    |Parametro |Descrizione |
    |---|---|
    |--consumer.config (informazioni in base al fatto di un'impostazion|specifica il file che contiene le proprietà del consumer. Queste proprietà vengono utilizzate per creare un consumer che legge dal cluster Kafka *primario.*|
    |--producer.config (informazioni in cui è stato possibile utilizzare un nome|specifica il file che contiene le proprietà del producer. Queste proprietà vengono utilizzate per creare un producer che scrive nel cluster Kafka *secondario.*|
    |--whitelist|Elenco di argomenti replicati da MirrorMaker dal cluster primario al database secondario.|
    |--num.streams|numero di thread consumer da creare.|

    Il consumer nel nodo secondario è ora in attesa di ricevere messaggi.

2. Dalla connessione SSH al cluster **primario,** utilizzare il comando seguente per avviare un producer e inviare messaggi all'argomento:

    ```bash
    export PRIMARY_BROKERHOSTS=BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

     Quando si arriva a una riga vuota con un cursore, digitare alcuni messaggi di testo. I messaggi vengono inviati all'argomento nel cluster **primario.** Al termine, usare **Ctrl + C** per chiudere il processo del producer.

3. Dalla connessione SSH al cluster **secondario,** utilizzare **Ctrl e C** per terminare il processo MirrorMaker. Per terminare il processo, potrebbero essere necessari alcuni secondi. Per verificare che i messaggi siano stati replicati nel database secondario, utilizzare il comando seguente:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $SECONDARY_ZKHOSTS --topic testtopic --from-beginning
    ```

    L'elenco degli `testtopic`argomenti include ora , che viene creato quando MirrorMaster rispecchia l'argomento dal cluster primario al secondario. I messaggi recuperati dall'argomento sono gli stessi immessi nel cluster primario.

## <a name="delete-the-cluster"></a>Eliminare il cluster

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

I passaggi in questo documento hanno creato cluster in gruppi di risorse di Azure diversi. Per eliminare tutte le risorse create, è possibile eliminare i due gruppi di risorse creati: **kafka-primary-rg** e **kafka-secondary_rg**. L'eliminazione dei gruppi di risorse rimuove tutte le risorse create seguendo questo documento, inclusi cluster, reti virtuali e account di archiviazione.

## <a name="next-steps"></a>Passaggi successivi

In questo documento è stato descritto come usare [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) per creare la replica di un cluster [Apache Kafka](https://kafka.apache.org/). Per trovare altri modi per lavorare con Kafka, vedere i collegamenti seguenti:

* [Documentazione su Apache Kafka MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) in cwiki.apache.org.
* [Best practice Kafka Mirror Maker](https://community.cloudera.com/t5/Community-Articles/Kafka-Mirror-Maker-Best-Practices/ta-p/249269)
* [Introduzione ad Apache Kafka (anteprima) in HDInsight](apache-kafka-get-started.md)
* [Usare Apache Spark con Apache Kafka su HDInsightUse Apache Spark with Apache Kafka on HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Connettersi ad Apache Kafka tramite una rete virtuale di Azure](apache-kafka-connect-vpn-gateway.md)
