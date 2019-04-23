---
title: Configurare Apache Kafka in HDInsight usando Azure PowerShell - Avvio rapido
description: Questa guida di avvio rapido illustra come creare un cluster Apache Kafka in Azure HDInsight tramite Azure PowerShell. Illustra inoltre gli argomenti, i sottoscrittori e i consumer di Kafka.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: mvc
ms.topic: quickstart
ms.date: 04/16/2018
ms.openlocfilehash: c86e5faa212fb6458326e00cba02fbe2ea83c8f7
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58850318"
---
# <a name="quickstart-create-an-apache-kafka-on-hdinsight-cluster"></a>Guida introduttiva: Creare un cluster Apache Kafka in HDInsight

[Apache Kafka](https://kafka.apache.org/) è una piattaforma di streaming open source distribuita. Viene spesso usata come broker di messaggi perché offre funzionalità simili a una coda messaggi di pubblicazione/sottoscrizione. 

Questa guida di avvio rapido illustra come creare un cluster[Apache Kafka](https://kafka.apache.org) tramite Azure PowerShell. Illustra anche come usare le utilità incluse per inviare e ricevere messaggi tramite Kafka.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

> [!IMPORTANT]  
> Possono accedere all'API Kafka solo risorse interne alla stessa rete virtuale. In questa guida di avvio rapido si accede al cluster direttamente usando SSH. Per connettere altri servizi, reti o macchine virtuali a Kafka, è necessario prima di tutto creare una rete virtuale e quindi creare le risorse all'interno della rete.
>
> Per altre informazioni, vedere il documento [Connettersi ad Apache Kafka da una rete locale](apache-kafka-connect-vpn-gateway.md).

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

* Azure PowerShell. Per altre informazioni, vedere il documento [Installare e configurare Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

* Un client SSH. La procedura in questo documento usa SSH per la connessione al cluster.

    Il comando `ssh` è disponibile per impostazione predefinita nei sistemi Linux, Unix e macOS. In Windows 10 usare uno dei metodi seguenti per installare il comando `ssh`:

  * Usare [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart). Cloud Shell include il comando `ssh` e può essere configurata per l'utilizzo di Bash o di PowerShell come ambiente shell.

  * [Installare il sottosistema Windows per Linux](https://docs.microsoft.com/windows/wsl/install-win10). Le distribuzioni Linux disponibili nel Microsoft Store includono il comando `ssh`.

    > [!IMPORTANT]  
    > La procedura descritta in questo documento presuppone che si usi uno dei client SSH sopra menzionati. Se si usa un client SSH diverso e si riscontrano problemi, consultare la documentazione del client SSH.
    >
    > Per altre informazioni, vedere il documento [Connettersi a HDInsight (Hadoop) con SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere alla sottoscrizione di Azure con il cmdlet `Login-AzAccount` e seguire le istruzioni visualizzate.

```powershell
Login-AzAccount
```

## <a name="create-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse di Azure con [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. L'esempio seguente chiede di immettere il nome e il percorso e quindi crea un nuovo gruppo di risorse:

```powershell
$resourceGroup = Read-Input -Prompt "Enter the resource group name"
$location = Read-Input -Prompt "Enter the Azure region to use"

New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-a-storage-account"></a>Creare un account di archiviazione

Mentre Kafka in HDInsight usa dischi gestiti di Azure per archiviare dati Kafka, il cluster usa anche Archiviazione di Microsoft Azure per archiviare informazioni quali i log. Usare [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) per creare un nuovo account di archiviazione.

```powershell
$storageName = Read-Host -Prompt "Enter the storage account name"

New-AzStorageAccount `
        -ResourceGroupName $resourceGroup `
        -Name $storageName `
        -Type Standard_LRS `
        -Location $location
```

HDInsight archivia i dati nell'account di archiviazione in un contenitore blob. Usare [New-AzStorageContainer](/powershell/module/Az.Storage/New-AzStorageContainer) per creare un nuovo contenitore.

```powershell
$containerName = Read-Host -Prompt "Enter the container name"

$storageKey = (Get-AzStorageAccountKey `
                -ResourceGroupName $resourceGroup `
                -Name $storageName)[0].Value
$storageContext = New-AzStorageContext `
                    -StorageAccountName $storageName `
                    -StorageAccountKey $storageKey
New-AzStorageContainer -Name $containerName -Context $storageContext 
```

## <a name="create-an-apache-kafka-cluster"></a>Creare un cluster Apache Kafka

Creare un cluster Apache Kafka in HDInsight con [New-AzHDInsightCluster](/powershell/module/az.HDInsight/New-azHDInsightCluster).

```powershell
# Create a Kafka 1.0 cluster
$clusterName = Read-Host -Prompt "Enter the name of the Kafka cluster"
$httpCredential = Get-Credential -Message "Enter the cluster login credentials" `
    -UserName "admin"
$sshCredentials = Get-Credential -Message "Enter the SSH user credentials"

$numberOfWorkerNodes = "4"
$clusterVersion = "3.6"
$clusterType="Kafka"
$clusterOS="Linux"
$disksPerNode=2

$kafkaConfig = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
$kafkaConfig.Add("kafka", "1.0")

New-AzHDInsightCluster `
        -ResourceGroupName $resourceGroup `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $numberOfWorkerNodes `
        -ClusterType $clusterType `
        -OSType $clusterOS `
        -Version $clusterVersion `
        -ComponentVersion $kafkaConfig `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$storageName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageKey `
        -DefaultStorageContainer $clusterName `
        -SshCredential $sshCredentials `
        -DisksPerWorkerNode $disksPerNode
```

> [!WARNING]  
> La creazione del cluster HDInsight può richiedere fino a 20 minuti.

> [!TIP]  
> Il parametro `-DisksPerWorkerNode` configura la scalabilità di Kafka in HDInsight. Per archiviare i dati, Kafka in HDInsight usa il disco locale delle macchine virtuali nel cluster. Dal momento che in Kafka i processi I/O sono intensivi, viene usato il servizio [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md) per assicurare una velocità effettiva elevata e fornire maggiore spazio di archiviazione per ogni nodo. 
>
> Il tipo di disco gestito può essere __Standard__ (HDD) o __Premium__ (SSD). Il tipo di disco dipende dalla dimensione della VM utilizzata dai nodi del ruolo di lavoro (broker Kafka). I dischi Premium vengono usati con le macchine virtuali serie DS e GS. Tutti gli altri tipi di macchine virtuali usano dischi Standard. È possibile impostare il tipo di VM usando il parametro `-WorkerNodeSize`. Per altre informazioni sui parametri, vedere la documentazione di [New-AzHDInsightCluster](/powershell/module/az.HDInsight/New-azHDInsightCluster).


> [!IMPORTANT]  
> Se si prevede di usare più di 32 nodi del ruolo di lavoro, al momento della creazione del cluster o con il ridimensionamento del cluster dopo la creazione, è necessario usare il parametro `-HeadNodeSize` per specificare una dimensione di VM con almeno 8 core e 14 GB di RAM.
>
> Per altre informazioni sulle dimensioni di nodo e i costi associati, vedere [Prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="connect-to-the-cluster"></a>Connettersi al cluster

1. Per connettersi al nodo head primario del cluster Kafka, usare il comando seguente. Sostituire `sshuser` con il nome utente SSH. Sostituire `mykafka` con il nome del cluster Kafka

    ```bash
    ssh sshuser@mykafka-ssh.azurehdinsight.net
    ```

2. Alla prima connessione al cluster, è possibile che il client SSH mostri un avviso relativo all'impossibilità di confermare l'autenticità dell'host. Al prompt digitare __yes__e quindi premere __INVIO__ per aggiungere l'host all'elenco dei server attendibili del client SSH.

3. Quando richiesto, immettere la password per l'utente SSH.

Dopo avere eseguito la connessione, vengono visualizzate informazioni simili al testo seguente:

```text
Authorized uses only. All activity may be monitored and reported.
Welcome to Ubuntu 16.04.4 LTS (GNU/Linux 4.13.0-1011-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    https://www.ubuntu.com/business/services/cloud

83 packages can be updated.
37 updates are security updates.



Welcome to Kafka on HDInsight.

Last login: Thu Mar 29 13:25:27 2018 from 108.252.109.241
ssuhuser@hn0-mykafk:~$
```

## <a id="getkafkainfo"></a>Ottenere le informazioni sugli host Apache Zookeeper e broker

Quando si usa Kafka, è necessario conoscere gli host *Apache Zookeeper* e *broker*. Questi host vengono usati con l'API Kafka e molte delle utilità offerte con Kafka.

In questa sezione si ottengono le informazioni sull'host dall'API REST Apache Ambari nel cluster.

1. Da una connessione SSH al cluster, usare il comando seguente per installare l'utilità `jq`. Questa utilità consente di analizzare i documenti JSON ed è utile nel recupero delle informazioni sull'host:
   
    ```bash
    sudo apt -y install jq
    ```

2. Per impostare una variabile di ambiente sul nome del cluster, usare il comando seguente:

    ```bash
    read -p "Enter the Kafka on HDInsight cluster name: " CLUSTERNAME
    ```

    Al prompt immettere il nome del cluster Kafka.

3. Per impostare una variabile di ambiente con le informazioni degli host Zookeeper, usare il comando seguente:

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    Al prompt immettere la password dell'account di accesso al cluster (non l'account SSH).

    > [!NOTE]  
    > Questo comando recupera tutti gli host Zookeeper, quindi restituisce solo le prime due voci, per mantenere un certo livello di ridondanza nel caso in cui un host fosse irraggiungibile.

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

    Al prompt immettere la password dell'account di accesso al cluster (non l'account SSH).

6. Usare il comando seguente per verificare che la variabile di ambiente sia impostata correttamente:

    ```bash   
    echo '$KAFKABROKERS='$KAFKABROKERS
    ```

    Questo comando restituisce informazioni simili al testo seguente:
   
    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`

## <a name="manage-apache-kafka-topics"></a>Gestire gli argomenti di Apache Kafka

Kafka archivia i flussi di dati in *argomenti*. Per gestire gli argomenti è possibile usare l'utilità `kafka-topics.sh`.

* **Per creare un argomento** usare il comando seguente nella connessione SSH:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    ```

    Questo comando si connette a Zookeeper usando le informazioni sull'host archiviate in `$KAFKAZKHOSTS` e quindi crea un argomento Kafka denominato **test**. 

    * I dati archiviati in questo argomento vengono divisi in otto partizioni.

    * Ogni partizione è replicata in tre nodi di ruolo di lavoro nel cluster.

        > [!IMPORTANT]  
        > Se il cluster è stato creato in un'area di Azure con tre domini di errore, usare il fattore di replica 3. In caso contrario usare il fattore di replica 4.
        
        Nelle aree con tre domini di errore, il fattore di replica 3 consente di distribuire le repliche tra i domini di errore. Nelle aree con due domini di errore, il fattore di replica 4 distribuisce le repliche uniformemente tra i domini.
        
        Per informazioni sul numero di domini di errore in un'area, vedere il documento [Disponibilità delle macchine virtuali Linux](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

        > [!IMPORTANT]   
        > Kafka non rileva i domini di errore di Azure. Quando si creano le repliche di partizione per gli argomenti, è possibile che le repliche non vengano distribuite in modo corretto per la disponibilità elevata.

        Per garantire la disponibilità elevata, usare lo [strumento per il ribilanciamento delle partizioni Apache Kafka](https://github.com/hdinsight/hdinsight-kafka-tools). Questo strumento deve essere eseguito da una sessione SSH al nodo head del cluster Kafka.

        Per garantire la massima disponibilità dei dati Kafka, è consigliabile ribilanciare le repliche di partizione per l'argomento nelle situazioni seguenti:

        * Quando si crea un nuovo argomento o una nuova partizione

        * Quando si aumentano le prestazioni di un cluster

* **Per elencare gli argomenti** usare il comando seguente:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    Questo comando elenca gli argomenti disponibili nel cluster Kafka.

* **Per eliminare un argomento** usare il comando seguente:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic topicname --zookeeper $KAFKAZKHOSTS
    ```

    Questo comando elimina l'argomento denominato `topicname`.

    > [!WARNING]  
    > Se si elimina l'argomento `test` creato in precedenza, è necessario crearlo di nuovo. Questo argomento si userà in passaggi indicati più avanti in questo documento.

Per altre informazioni sui comandi disponibili con l'utilità `kafka-topics.sh`, usare il comando seguente:

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh
```

## <a name="produce-and-consume-records"></a>Produrre e utilizzare record

Kafka archivia i *record* negli argomenti. I record vengono prodotti da *producer* e usati da *consumer*. I producer e i consumer comunicano con il servizio *broker Kafka*. Ogni nodo del ruolo di lavoro nel cluster HDInsight è un host del broker Kafka.

Seguire questa procedura per archiviare i record nell'argomento test creato in precedenza e quindi leggerli usando un consumer:

1. Per scrivere i record nell'argomento usare l'utilità `kafka-console-producer.sh` dalla connessione SSH:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```
   
    Dopo questo comando viene visualizzata una riga vuota.

2. Digitare un messaggio di testo nella riga vuota e premere INVIO. Digitare invece alcuni messaggi in questo modo e quindi usare **Ctrl + C** per tornare al prompt normale. Ogni riga viene inviata come record distinto all'argomento Kafka.

3. Per scrivere i record dall'argomento usare l'utilità `kafka-console-consumer.sh` dalla connessione SSH:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic test --from-beginning
    ```
   
    Questo comando recupera i record dall'argomento e li visualizza. L'uso di `--from-beginning` indica al consumer di partire dall'inizio del flusso, quindi verranno recuperati tutti i record.

    > [!NOTE]  
    > Se si usa una versione meno recente di Kafka, sostituire `--bootstrap-server $KAFKABROKERS` con `--zookeeper $KAFKAZKHOSTS`.

4. Usare __Ctrl + C__ per arrestare il consumer.

È possibile creare producer e consumer anche a livello di codice. Per un esempio dell'uso di questa API, vedere il documento relativo alle [API Producer e Consumer Apache Kafka con HDInsight](apache-kafka-producer-consumer-api.md).

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, è possibile usare il comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) per rimuovere il gruppo di risorse, l'istanza di HDInsight e tutte le risorse correlate.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

> [!WARNING]  
> La fatturazione del cluster HDInsight inizia dopo la creazione del cluster e si interrompe solo quando questo viene eliminato. La fatturazione avviene con tariffa oraria, perciò si deve sempre eliminare il cluster in uso quando non lo si usa più.
> 
> Se si elimina un cluster Kafka su HDInsight vengono eliminati anche eventuali dati archiviati in Kafka.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Usare Apache Spark con Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
