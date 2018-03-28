---
title: 'Iniziare a usare Apache Kafka: Azure HDInsight| Microsoft Docs'
description: Informazioni su come creare un cluster Apache Kafka in Azure HDInsight. Informazioni su come creare argomenti, sottoscrittori e consumer.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 43585abf-bec1-4322-adde-6db21de98d7f
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/20/2018
ms.author: larryfr
ms.openlocfilehash: 27e6472480dac104de799ebf0e7579a7987f6c4c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="start-with-apache-kafka-on-hdinsight"></a>Iniziare a usare Apache Kafka in HDInsight

Questo articolo illustra come creare e usare un cluster [Apache Kafka](https://kafka.apache.org) in Azure HDInsight. Kafka è una piattaforma di streaming distribuita open source disponibile con HDInsight. Viene spesso usata come broker di messaggi perché mette a disposizione funzionalità simili a una coda messaggi di pubblicazione/sottoscrizione. Kafka viene spesso usato con Apache Spark e Apache Storm per messaggistica, rilevamento di attività, aggregazione di flussi o trasformazione di dati.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="create-a-kafka-cluster"></a>Creare un cluster Kafka

Per creare un cluster Kafka in HDInsight seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com) selezionare **+** per creare una risorsa, quindi **Dati e analisi** e infine **HDInsight**.
   
    ![Creazione di un cluster HDInsight](./media/apache-kafka-get-started/create-hdinsight.png)

2. In **Informazioni di base** immettere le informazioni seguenti.

    * **Nome del cluster**: nome del cluster HDInsight. Il nome deve essere univoco.
    * **Sottoscrizione**: selezionare la sottoscrizione da usare.
    * **Tipo di cluster**: selezionare questa voce e quindi impostare i valori seguenti in **Configurazione cluster**:

        * **Tipo di cluster**: Kafka
        * **Versione**: Kafka 0.10.0 (HDI 3.6)

        Usare il pulsante **Seleziona** per salvare le impostazioni del tipo di cluster.

        ![Selezionare il tipo di cluster](./media/apache-kafka-get-started/set-hdinsight-cluster-type.png)

    * **Nome utente dell'account di accesso del cluster** e **Password dell'account di accesso del cluster**: account di accesso usato per il cluster su HTTPS. Queste credenziali vengono usate per accedere a servizi quali l'interfaccia utente Web di Ambari o l'API REST.
    * **Secure Shell (SSH) username** (Nome utente SSH): account di accesso usato per il cluster su SSH. Per impostazione predefinita, la password corrisponde alla password di accesso al cluster.
    * **Gruppo di risorse**: il gruppo di risorse nel quale viene creato il cluster.
    * **Posizione**: area di Azure in cui creare il cluster.

        > [!IMPORTANT]
        > Per una disponibilità elevata dei dati, è consigliabile selezionare una posizione (area) contenente __tre domini di errore__. Per altre informazioni, vedere la sezione [Disponibilità elevata dei dati](#data-high-availability).
   
 ![Selezionare la sottoscrizione](./media/apache-kafka-get-started/hdinsight-basic-configuration.png)

3. Usare il pulsante __Avanti__ per completare la configurazione di base.

4. In **Archiviazione** selezionare o creare un account di archiviazione. Per la procedura illustrata in questo documento, negli altri campi mantenere i valori predefiniti. Usare il pulsante __Avanti__ per salvare la configurazione della risorsa di archiviazione.

    ![Configurare le impostazioni dell'account di archiviazione per HDInsight](./media/apache-kafka-get-started/set-hdinsight-storage-account.png)

5. In __Applicazioni (facoltativo)__ selezionare __Avanti__ per continuare. Non sono richieste applicazioni per questo esempio.

6. In __Dimensioni del cluster__ selezionare __Avanti__ per continuare.

    > [!WARNING]
    > Per garantire la disponibilità di Kafka in HDInsight, il cluster deve contenere almeno tre nodi del ruolo di lavoro. Per altre informazioni, vedere la sezione [Disponibilità elevata dei dati](#data-high-availability).

    ![Configurare le dimensioni del cluster di Kafka](./media/apache-kafka-get-started/kafka-cluster-size.png)

    > [!IMPORTANT]
    > La voce relativa ai **dischi per nodo di lavoro** configura la scalabilità di Kafka in HDInsight. Kafka in HDInsight usa il disco locale delle macchine virtuali nel cluster. Dal momento che in Kafka i processi I/O sono intensivi, viene usato il servizio [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md) per assicurare una velocità effettiva elevata e fornire maggiore spazio di archiviazione per ogni nodo. Il tipo di disco gestito può essere __Standard__ (HDD) o __Premium__ (SSD). I dischi Premium sono usati con le macchine virtuali serie DS e GS. Tutti gli altri tipi di macchine virtuali usano dischi Standard.

7. In __Impostazioni avanzate__ selezionare __Avanti__ per continuare.

8. Nel **riepilogo** esaminare la configurazione del cluster. Usare i collegamenti __Modifica__ per cambiare eventuali impostazioni non corrette. Usare infine il pulsante __Crea__ per creare il cluster.
   
    ![Riepilogo della configurazione del cluster](./media/apache-kafka-get-started/hdinsight-configuration-summary.png)
   
    > [!NOTE]
    > La creazione del cluster può richiedere fino a 20 minuti.

## <a name="connect-to-the-cluster"></a>Connettersi al cluster

> [!IMPORTANT]
> Per eseguire questa procedura è necessario usare un client SSH. Per altre informazioni, vedere il documento [Connettersi a HDInsight (Hadoop) con SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

Per connettersi al cluster con SSH è necessario specificare il nome dell'account utente SSH e il nome del cluster. Nell'esempio seguente, sostituire `sshuser` e `clustername` con il nome dell'account e del cluster:

```ssh sshuser@clustername-ssh.azurehdinsight.net```

Quando richiesto, immettere la password usata per l'account SSH.

Per altre informazioni, vedere [Usare SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="getkafkainfo"></a>Ottenere le informazioni sugli host Zookeeper e broker

Quando si usa Kafka è necessario conoscere gli host *Zookeeper* e *broker*. Questi host vengono usati con l'API Kafka e molte delle utilità offerte con Kafka.

Seguire questa procedura per creare le variabili di ambiente che contengono le informazioni sugli host:

1. Da una connessione SSH al cluster usare questo comando per installare l'utilità `jq`. Questa utilità consente di analizzare documenti JSON e risulta utile nel recupero delle informazioni sugli host broker:
   
    ```bash
    sudo apt -y install jq
    ```

2. Per impostare una variabile di ambiente sul nome del cluster, usare il comando seguente:

    ```bash
    read -p "Enter the HDInsight cluster name: " CLUSTERNAME
    ```

3. Per impostare una variabile di ambiente con le informazioni degli host Zookeeper, usare il comando seguente:

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    Quando richiesto, immettere la password dell'account di accesso al cluster (amministratore).

4. Usare il comando seguente per verificare che la variabile di ambiente sia impostata correttamente:

    ```bash
     echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
    ```

    Questo comando restituisce informazioni simili al testo seguente:

    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`

5. Per impostare una variabile di ambiente con le informazioni degli host broker, usare il comando seguente:

    ```bash
    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

    Quando richiesto, immettere la password dell'account di accesso al cluster (amministratore).

6. Usare il comando seguente per verificare che la variabile di ambiente sia impostata correttamente:

    ```bash   
    echo '$KAFKABROKERS='$KAFKABROKERS
    ```

    Questo comando restituisce informazioni simili al testo seguente:
   
    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`
   
> [!WARNING]
> Le informazioni restituite da questa sessione non sono sempre precise. Quando si ridimensiona il cluster, vengono aggiunti o rimossi broker. Se si verifica un errore e viene sostituito un nodo, il nome host per il nodo può cambiare.
>
> È necessario recuperare le informazioni degli host Zookeeper e broker poco prima di usarle, per assicurare che siano valide.

## <a name="create-a-topic"></a>Creare un argomento

Kafka archivia i flussi di dati in categorie denominate *argomenti*. Da una connessione SSH a un nodo head del cluster, usare uno script fornito con Kafka per creare un argomento:

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
```

Questo comando si connette a Zookeeper usando le informazioni sull'host archiviate in `$KAFKAZKHOSTS` e quindi crea un argomento Kafka denominato **test**. Per verificare che l'argomento sia stato creato è possibile usare lo script seguente per elencare gli argomenti:

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
```

L'output di questo comando elenca gli argomenti Kafka nel cluster.

## <a name="produce-and-consume-records"></a>Produrre e utilizzare record

Kafka archivia i *record* negli argomenti. I record vengono prodotti da *producer* e usati da *consumer*. I producer producono i record per i *broker* Kafka. Ogni nodo del ruolo di lavoro nel cluster HDInsight è un broker Kafka.

Seguire questa procedura per archiviare i record nell'argomento test creato in precedenza e quindi leggerli usando un consumer:

1. Dalla sessione SSH, usare uno script fornito con Kafka per scrivere record nell'argomento:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```
   
    Dopo questo comando viene visualizzata una riga vuota.

2. Digitare un messaggio di testo nella riga vuota e premere INVIO. Digitare invece alcuni messaggi in questo modo e quindi usare **Ctrl + C** per tornare al prompt normale. Ogni riga viene inviata come record distinto all'argomento Kafka.

3. Usare uno script fornito con Kafka per leggere record dall'argomento:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic test --from-beginning
    ```
   
    Questo comando recupera i record dall'argomento e li visualizza. L'uso di `--from-beginning` indica al consumer di partire dall'inizio del flusso, quindi verranno recuperati tutti i record.

    > [!NOTE]
    > Se si usa una versione meno recente di Kafka, sostituire `--bootstrap-server $KAFKABROKERS` con `--zookeeper $KAFKAZKHOSTS`.

4. Usare __Ctrl + C__ per arrestare il consumer.

È possibile creare producer e consumer anche a livello di codice. Per un esempio dell'uso di questa API, vedere il documento relativo alle [API Producer e Consumer Kafka con HDInsight](apache-kafka-producer-consumer-api.md).

## <a name="data-high-availability"></a>Disponibilità elevata dei dati

Ogni area (posizione) di Azure fornisce _domini di errore_. Un dominio di errore è un raggruppamento logico dell'hardware sottostante in un data center di Azure. Ogni dominio di errore condivide una fonte di alimentazione e un commutatore di rete comuni. Le macchine virtuali e i dischi gestiti che implementano i nodi in un cluster HDInsight sono distribuiti tra i domini di errore. Questa architettura limita il potenziale impatto dei guasti dell'hardware fisico.

Per informazioni sul numero di domini di errore in un'area, vedere il documento [Disponibilità delle macchine virtuali Linux](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

> [!IMPORTANT]
> Se possibile, usare un'area di Azure contenente tre domini di errore e creare argomenti con un fattore di replica di 3.

Se si usa un'area che contiene solo due domini di errore, usare un fattore di replica di 4 per distribuire uniformemente le repliche tra i due domini di errore.

### <a name="kafka-and-fault-domains"></a>Kafka e domini di errore

Kafka non rileva i domini di errore. Quando si creano le repliche di partizione per gli argomenti, è possibile che le repliche non vengano distribuite in modo corretto per la disponibilità elevata. Per garantire la disponibilità elevata, usare lo [strumento per il ribilanciamento delle partizioni Kafka](https://github.com/hdinsight/hdinsight-kafka-tools). Questo strumento deve essere eseguito da una sessione SSH al nodo head del cluster Kafka.

Per garantire la massima disponibilità dei dati Kafka, è consigliabile ribilanciare le repliche di partizione per l'argomento nei momenti seguenti:

* Quando si crea un nuovo argomento o una nuova partizione

* Quando si aumentano le prestazioni di un cluster

## <a name="delete-the-cluster"></a>Eliminare il cluster

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Risolvere problemi

Se si verificano problemi di creazione dei cluster HDInsight, vedere i [requisiti dei controlli di accesso](../hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Passaggi successivi

In questo documento sono state illustrate le nozioni di base per l'uso di Apache Kafka in HDInsight. Per altre informazioni sull'uso di Kafka, vedere:

* [Analizzare i log di Kafka](apache-kafka-log-analytics-operations-management.md)
* [Replicare i dati tra cluster Kafka](apache-kafka-mirroring.md)
* [API Producer e Consumer Kafka con HDInsight](apache-kafka-producer-consumer-api.md)
* [API Kafka Streams con HDInsight](apache-kafka-streams-api.md)
* [Usare lo streaming Apache Spark (DStream) con Kafka in HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Usare lo streaming strutturato Apache Spark con Kafka in HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
* [Usare lo streaming strutturato Apache Spark per spostare dati da Kafka in HDInsight a Cosmos DB](../apache-kafka-spark-structured-streaming-cosmosdb.md)
* [Usare Apache Storm (anteprima) con Kafka in HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Connect to Kafka through an Azure Virtual Network](apache-kafka-connect-vpn-gateway.md) (Connettersi a Kafka tramite una rete virtuale di Azure)
* [Usare Kafka con il servizio contenitore di Azure](apache-kafka-azure-container-services.md)
* [Usare Kafka con le app per le funzioni di Azure](apache-kafka-azure-functions.md)
