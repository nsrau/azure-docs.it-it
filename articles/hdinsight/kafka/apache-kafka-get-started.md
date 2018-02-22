---
title: 'Iniziare a usare Apache Kafka: Azure HDInsight| Microsoft Docs'
description: Informazioni su come creare un cluster Apache Kafka in Azure HDInsight. Informazioni su come creare argomenti, sottoscrittori e consumer.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 43585abf-bec1-4322-adde-6db21de98d7f
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/18/2018
ms.author: larryfr
ms.openlocfilehash: 639adb2fdc5a7d76c11397b5027199626a0a4016
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2018
---
# <a name="start-with-apache-kafka-on-hdinsight"></a>Iniziare a usare Apache Kafka in HDInsight

Questo articolo illustra come creare e usare un cluster [Apache Kafka](https://kafka.apache.org) in Azure HDInsight. Kafka è una piattaforma di streaming distribuita open source disponibile con HDInsight. Viene spesso usata come broker di messaggi perché mette a disposizione funzionalità simili a una coda messaggi di pubblicazione/sottoscrizione. Kafka viene spesso usato con Apache Spark e Apache Storm.

> [!NOTE]
> Con HDInsight sono attualmente disponibili due versioni di Kafka: 0.9.0 (HDInsight 3.4) e 0.10.0 (HDInsight 3.5 e 3.6). Le procedure in questo documento presuppongono che si usi Kafka in HDInsight 3.6.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="create-a-kafka-cluster"></a>Creare un cluster Kafka

Per creare un cluster Kafka in HDInsight seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com) selezionare **+** per creare una risorsa, quindi **Dati e analisi** e infine **HDInsight**.
   
    ![Creazione di un cluster HDInsight](./media/apache-kafka-get-started/create-hdinsight.png)

2. In **Informazioni di base** immettere le informazioni seguenti.

    * **Nome del cluster**: nome del cluster HDInsight.
    * **Sottoscrizione**: selezionare la sottoscrizione da usare.
    * **Nome utente dell'account di accesso del cluster** e **Password dell'account di accesso del cluster**: account di accesso usato per il cluster su HTTPS. Queste credenziali vengono usate per accedere a servizi quali l'interfaccia utente Web di Ambari o l'API REST.
    * **Secure Shell (SSH) username** (Nome utente SSH): account di accesso usato per il cluster su SSH. Per impostazione predefinita, la password corrisponde alla password di accesso al cluster.
    * **Gruppo di risorse**: il gruppo di risorse nel quale viene creato il cluster.
    * **Posizione**: area di Azure in cui creare il cluster.

        > [!IMPORTANT]
        > Per una disponibilità elevata dei dati, è consigliabile selezionare una posizione (area) contenente __tre domini di errore__. Per altre informazioni, vedere la sezione [Disponibilità elevata dei dati](#data-high-availability).
   
 ![Selezionare la sottoscrizione](./media/apache-kafka-get-started/hdinsight-basic-configuration.png)

3. Selezionare **Tipo di cluster** e quindi impostare i valori seguenti in **Configurazione cluster**.
   
    * **Tipo di cluster**: Kafka
    * **Versione**: Kafka 0.10.0 (HDI 3.6)

    Usare infine il pulsante **Seleziona** per salvare le impostazioni.
     
 ![Selezionare il tipo di cluster](./media/apache-kafka-get-started/set-hdinsight-cluster-type.png)

4. Dopo avere selezionato il tipo di cluster, usare il pulsante __Seleziona__ per impostare il tipo di cluster. Usare quindi il pulsante __Avanti__ per completare la configurazione di base.

5. In **Archiviazione** selezionare o creare un account di archiviazione. Per la procedura illustrata in questo documento, negli altri campi mantenere i valori predefiniti. Usare il pulsante __Avanti__ per salvare la configurazione della risorsa di archiviazione.

    ![Configurare le impostazioni dell'account di archiviazione per HDInsight](./media/apache-kafka-get-started/set-hdinsight-storage-account.png)

6. In __Applicazioni (facoltativo)__ selezionare __Avanti__ per continuare. Non sono richieste applicazioni per questo esempio.

7. In __Dimensioni del cluster__ selezionare __Avanti__ per continuare.

    > [!WARNING]
    > Per garantire la disponibilità di Kafka in HDInsight, il cluster deve contenere almeno tre nodi del ruolo di lavoro. Per altre informazioni, vedere la sezione [Disponibilità elevata dei dati](#data-high-availability).

    ![Configurare le dimensioni del cluster di Kafka](./media/apache-kafka-get-started/kafka-cluster-size.png)

    > [!IMPORTANT]
    > La voce relativa ai **dischi per nodo di lavoro** controlla la scalabilità di Kafka in HDInsight. Kafka in HDInsight usa il disco locale delle macchine virtuali nel cluster. Dal momento che in Kafka i processi I/O sono intensivi, viene usato il servizio [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md) per assicurare una velocità effettiva elevata e fornire maggiore spazio di archiviazione per ogni nodo. Il tipo di disco gestito può essere __Standard__ (HDD) o __Premium__ (SSD). I dischi Premium sono usati con le macchine virtuali serie DS e GS. Tutti gli altri tipi di macchine virtuali usano dischi Standard.

8. In __Impostazioni avanzate__ selezionare __Avanti__ per continuare.

9. Nel **riepilogo** esaminare la configurazione del cluster. Usare i collegamenti __Modifica__ per cambiare eventuali impostazioni non corrette. Usare infine il pulsante __Crea__ per creare il cluster.
   
    ![Riepilogo della configurazione del cluster](./media/apache-kafka-get-started/hdinsight-configuration-summary.png)
   
    > [!NOTE]
    > La creazione del cluster può richiedere fino a 20 minuti.

## <a name="connect-to-the-cluster"></a>Connettersi al cluster

> [!IMPORTANT]
> Per eseguire questa procedura è necessario usare un client SSH. Per altre informazioni, vedere il documento [Connettersi a HDInsight (Hadoop) con SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

Dal client usare SSH per connettersi al cluster:

```ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net```

Sostituire **SSHUSER** con il nome utente SSH specificato durante la creazione del cluster. Sostituire **CLUSTERNAME** con il nome del cluster.

Quando richiesto, immettere la password usata per l'account SSH.

Per altre informazioni, vedere [Usare SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="getkafkainfo"></a>Ottenere le informazioni sugli host Zookeeper e broker

Quando si usa Kafka è necessario conoscere due valori di host, ovvero *Zookeeper* e *broker*. Questi host vengono usati con l'API Kafka e molte delle utilità offerte con Kafka.

Seguire questa procedura per creare le variabili di ambiente che contengono le informazioni sugli host. Queste variabili di ambiente vengono usate nei passaggi di questo documento.

1. Da una connessione SSH al cluster usare questo comando per installare l'utilità `jq`. Questa utilità consente di analizzare documenti JSON e risulta utile nel recupero delle informazioni sugli host broker:
   
    ```bash
    sudo apt -y install jq
    ```

2. Per impostare le variabili di ambiente con le informazioni recuperate da Ambari, usare i comandi seguenti:

    ```bash
    CLUSTERNAME='your cluster name'
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`

    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`

    echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
    echo '$KAFKABROKERS='$KAFKABROKERS
    ```

    > [!IMPORTANT]
    > Impostare `CLUSTERNAME=` sul nome del cluster Kafka. Quando richiesto, immettere la password dell'account (admin) di accesso al cluster.

    Il testo seguente è un esempio del contenuto di `$KAFKAZKHOSTS`:
   
    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`
   
    Il testo seguente è un esempio del contenuto di `$KAFKABROKERS`:
   
    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`

    > [!NOTE]
    > Il comando `cut` viene usato per ridurre l'elenco degli host a due voci. Non è necessario specificare l'elenco completo degli host durante la creazione di un consumer o un producer Kafka.
   
    > [!WARNING]
    > Le informazioni restituite da questa sessione non sono sempre precise. Se si ridimensiona il cluster, verranno aggiunti o rimossi broker. Se si verifica un errore e viene sostituito un nodo, il nome host per il nodo può cambiare.
    >
    > È necessario recuperare le informazioni sugli host Zookeeper e broker poco prima di usarle, per assicurare che siano valide.

## <a name="create-a-topic"></a>Creare un argomento

Kafka archivia i flussi di dati in categorie denominate *argomenti*. Da una connessione SSH a un nodo head del cluster, usare uno script fornito con Kafka per creare un argomento:

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
```

Questo comando si connette a Zookeeper usando le informazioni sull'host archiviate in `$KAFKAZKHOSTS` e quindi crea un argomento Kafka denominato **test**. Per verificare che l'argomento sia stato creato è possibile usare lo script seguente per elencare gli argomenti:

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
```

L'output di questo comando elenca gli argomenti Kafka, incluso l'argomento **test**.

## <a name="produce-and-consume-records"></a>Produrre e utilizzare record

Kafka archivia i *record* negli argomenti. I record vengono prodotti da *producer* e usati da *consumer*. I producer producono i record per i *broker* Kafka. Ogni nodo del ruolo di lavoro nel cluster HDInsight è un broker Kafka.

Seguire questa procedura per archiviare i record nell'argomento test creato in precedenza e quindi leggerli usando un consumer:

1. Dalla sessione SSH, usare uno script fornito con Kafka per scrivere record nell'argomento:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```
   
    Non si torna al prompt dopo questo comando. Digitare invece alcuni messaggi di testo e quindi usare **Ctrl + C** per arrestare l'invio all'argomento. Ogni riga viene inviata come record distinto.

2. Usare uno script fornito con Kafka per leggere record dall'argomento:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic test --from-beginning
    ```
   
    Questo comando recupera i record dall'argomento e li visualizza. L'uso di `--from-beginning` indica al consumer di partire dall'inizio del flusso, quindi verranno recuperati tutti i record.

    > [!NOTE]
    > Se si usa una versione meno recente di Kafka, potrebbe essere necessario sostituire `--bootstrap-server $KAFKABROKERS` con `--zookeeper $KAFKAZKHOSTS`.

3. Usare __Ctrl + C__ per arrestare il consumer.

È possibile creare producer e consumer anche a livello di codice. Per un esempio dell'uso di questa API, vedere il documento relativo alle [API Producer e Consumer Kafka con HDInsight](apache-kafka-producer-consumer-api.md).

## <a name="data-high-availability"></a>Disponibilità elevata dei dati

Ogni area (posizione) di Azure fornisce _domini di errore_. Un dominio di errore è un raggruppamento logico dell'hardware sottostante in un data center di Azure. Ogni dominio di errore condivide una fonte di alimentazione e un commutatore di rete comuni. Le macchine virtuali e i dischi gestiti che implementano i nodi in un cluster HDInsight sono distribuiti tra i domini di errore. Questa architettura limita il potenziale impatto dei guasti dell'hardware fisico.

Per informazioni sul numero di domini di errore in un'area, vedere il documento [Disponibilità delle macchine virtuali Linux](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

> [!IMPORTANT]
> È consigliabile usare un'area di Azure contenente tre domini di errore e un fattore di replica di 3.

Se si deve usare un'area che contiene solo due domini di errore, usare un fattore di replica di 4 per distribuire uniformemente le repliche tra i due domini di errore.

### <a name="kafka-and-fault-domains"></a>Kafka e domini di errore

Kafka non rileva i domini di errore. Quando si creano le repliche di partizione per gli argomenti, è possibile che le repliche non vengano distribuite in modo corretto per la disponibilità elevata. Per garantire la disponibilità elevata, usare lo [strumento per il ribilanciamento delle partizioni Kafka](https://github.com/hdinsight/hdinsight-kafka-tools). Questo strumento deve essere eseguito da una sessione SSH al nodo head del cluster Kafka.

Per garantire la massima disponibilità dei dati Kafka, è consigliabile ribilanciare le repliche di partizione per l'argomento nei momenti seguenti:

* Quando viene creato un nuovo argomento o una nuova partizione

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
