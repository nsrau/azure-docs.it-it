---
title: Backup & replica per Apache HBase, Phoenix-Azure HDInsight
description: Configurare il backup e la replica per Apache HBase e Apache Phoenix in Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/19/2019
ms.openlocfilehash: fdd43a017e584a07d61d41e1af06d30db2f30ac7
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92542778"
---
# <a name="set-up-backup-and-replication-for-apache-hbase-and-apache-phoenix-on-hdinsight"></a>Configurare il backup e la replica per Apache HBase e Apache Phoenix in HDInsight

Apache HBase supporta diversi approcci per la protezione contro la perdita di dati:

* Copiare la cartella `hbase`
* Esportare e quindi importare
* Copiare le tabelle
* Snapshot
* Replica

> [!NOTE]  
> Apache Phoenix archivia i metadati in tabelle HBase, in modo che ne venga eseguito il backup quando si esegue il backup delle tabelle di catalogo di sistema HBase.

Le sezioni seguenti descrivono lo scenario di utilizzo per ognuno di questi approcci.

## <a name="copy-the-hbase-folder"></a>Copiare la cartella hbase

Con questo approccio si copiano tutti i dati di HBase, senza la possibilità di selezionare un subset di tabelle o famiglie di colonne. Gli approcci successivi offrono un maggiore controllo.

HBase in HDInsight usa l'archivio predefinito selezionato quando si crea il cluster, ovvero BLOB del servizio di archiviazione di Azure oppure Azure Data Lake Storage. In entrambi i casi HBase archivia i file di dati e metadati nel percorso seguente:

`/hbase`

* In un account di archiviazione di Azure la cartella `hbase` si trova nella radice del contenitore BLOB:

  `wasbs://<containername>@<accountname>.blob.core.windows.net/hbase`

* In Azure Data Lake Storage la `hbase` cartella si trova nel percorso radice specificato durante il provisioning di un cluster. Questo percorso radice ha in genere una cartella `clusters` con una sottocartella denominata in base al cluster HDInsight:

  `/clusters/<clusterName>/hbase`

In entrambi i casi, la cartella `hbase` contiene tutti i dati che HBase ha scaricato su disco, ma potrebbe non contenere i dati in memoria. Per assicurarsi che la cartella sia una rappresentazione accurata dei dati HBase, è necessario arrestare il cluster.

Dopo avere eliminato il cluster, è possibile lasciare i dati dove si trovano oppure copiarli in una nuova posizione:

* Creare una nuova istanza di HDInsight che punta alla posizione di archiviazione corrente. La nuova istanza viene creata con tutti i dati esistenti.

* Copiare la cartella `hbase` in un contenitore BLOB del servizio di archiviazione di Azure diverso o in una posizione di Data Lake Storage diversa e quindi avviare un nuovo cluster con i dati. Per Archiviazione di Azure usare [AzCopy](../../storage/common/storage-use-azcopy-v10.md), mentre per Data Lake Storage usare [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md).

## <a name="export-then-import"></a>Esportare e quindi importare

Nel cluster HDInsight di origine usare l' [utilità di esportazione](https://hbase.apache.org/book.html#export) (inclusa in HBase) per esportare i dati da una tabella di origine nell'archivio collegato predefinito. È quindi possibile copiare la cartella esportata nel percorso di archiviazione di destinazione ed eseguire l' [utilità di importazione](https://hbase.apache.org/book.html#import) nel cluster HDInsight di destinazione.

Per esportare i dati della tabella, prima SSH nel nodo head del cluster HDInsight di origine, quindi eseguire il `hbase` comando seguente:

```console
hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>"
```

La directory di esportazione non deve essere già esistente. Il nome della tabella fa distinzione tra maiuscole e minuscole.

Per importare i dati della tabella, connettersi tramite SSH al nodo head del cluster HDInsight di destinazione e quindi eseguire il `hbase` comando seguente:

```console
hbase org.apache.hadoop.hbase.mapreduce.Import "<tableName>" "/<path>/<to>/<export>"
```

La tabella deve essere già esistente.

Specificare il percorso di esportazione completo per l'archivio predefinito o per uno degli archivi collegati. Ad esempio, in Archiviazione di Azure:

`wasbs://<containername>@<accountname>.blob.core.windows.net/<path>`

In Azure Data Lake Storage Gen2 la sintassi è:

`abfs://<containername>@<accountname>.dfs.core.windows.net/<path>`

In Azure Data Lake Storage Gen1 la sintassi è:

`adl://<accountName>.azuredatalakestore.net:443/<path>`

Questo approccio offre granularità a livello di tabella. È anche possibile specificare un intervallo di date per le righe da includere, per eseguire il processo in modo incrementale. Ogni data è espressa in millisecondi dall'epoca Unix.

```console
hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>" <numberOfVersions> <startTimeInMS> <endTimeInMS>
```

Si noti che è necessario specificare il numero di versioni di ogni riga da esportare. Per includere tutte le versioni nell'intervallo di date, impostare `<numberOfVersions>` su un valore maggiore rispetto al numero massimo possibile di versioni di riga, ad esempio 100.000.

## <a name="copy-tables"></a>Copiare le tabelle

L' [utilità CopyTable](https://hbase.apache.org/book.html#copy.table) copia i dati da una tabella di origine, riga per riga, a una tabella di destinazione esistente con lo stesso schema dell'origine. La tabella di destinazione può trovarsi nello stesso cluster o in un cluster HBase diverso. I nomi di tabella fanno distinzione tra maiuscole e minuscole.

Per usare CopyTable in un cluster, connettersi tramite SSH al nodo head del cluster HDInsight di origine e quindi eseguire questo comando `hbase`:

```console
hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> <srcTableName>
```

Per usare CopyTable per copiare una tabella in un cluster diverso, aggiungere l'opzione `peer` con l'indirizzo del cluster di destinazione:

```console
hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> --peer.adr=<destinationAddress> <srcTableName>
```

L'indirizzo di destinazione è costituito dalle tre parti seguenti:

`<destinationAddress> = <ZooKeeperQuorum>:<Port>:<ZnodeParent>`

* `<ZooKeeperQuorum>` è un elenco delimitato da virgole di nodi Apache ZooKeeper, ad esempio:

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net

* Per impostazione predefinita, `<Port>` in HDInsight è 2181 e `<ZnodeParent>` è `/hbase-unsecure`, quindi il valore completo di `<destinationAddress>` è:

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net:2181:/hbase-unsecure

Vedere [Raccogliere manualmente l'elenco di quorum di Apache ZooKeeper](#manually-collect-the-apache-zookeeper-quorum-list) in questo articolo per informazioni dettagliate su come recuperare questi valori per il cluster HDInsight.

L'utilità CopyTable supporta anche i parametri per specificare l'intervallo di tempo delle righe da copiare e per specificare il subset di famiglie di colonne in una tabella da copiare. Per visualizzare l'elenco completo dei parametri supportati da CopyTable, eseguire il comando CopyTable senza parametri:

```console
hbase org.apache.hadoop.hbase.mapreduce.CopyTable
```

CopyTable analizza il contenuto dell'intera tabella di origine che verrà copiato nella tabella di destinazione. Ciò potrebbe ridurre le prestazioni del cluster HBase mentre CopyTable è in esecuzione.

> [!NOTE]  
> Per automatizzare la copia dei dati tra tabelle, vedere lo script `hdi_copy_table.sh` nel repository [Azure HBase Utils](https://github.com/Azure/hbase-utils/tree/master/replication) in GitHub.

### <a name="manually-collect-the-apache-zookeeper-quorum-list"></a>Raccogliere manualmente l'elenco di quorum di Apache ZooKeeper

Quando entrambi i cluster HDInsight sono nella stessa rete virtuale, come descritto in precedenza, la risoluzione dei nomi host interni è automatica. Per usare CopyTable per i cluster HDInsight in due reti virtuali separate connesse da un gateway VPN, è necessario fornire gli indirizzi IP host dei nodi Zookeeper nel quorum.

Per acquisire i nomi host del quorum, eseguire il comando curl seguente:

```console
curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/configurations?type=hbase-site&tag=TOPOLOGY_RESOLVED" | grep "hbase.zookeeper.quorum"
```

Il comando curl recupera un documento JSON contenente le informazioni di configurazione di HBase e il comando grep restituisce solo la voce "hbase.zookeeper.quorum", ad esempio:

```output
"hbase.zookeeper.quorum" : "zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net"
```

Il valore relativo ai nomi host del quorum è l'intera stringa a destra dei due punti.

Per recuperare gli indirizzi IP di questi host, usare il comando curl seguente per ogni host nell'elenco precedente:

```console
curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/hosts/<zookeeperHostFullName>" | grep "ip"
```

In questo comando curl `<zookeeperHostFullName>` è il nome DNS completo di un host ZooKeeper, ad esempio `zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net`. L'output del comando contiene l'indirizzo IP per l'host specificato, ad esempio:

`100    "ip" : "10.0.0.9",`

Dopo aver raccolto gli indirizzi IP per tutti i nodi ZooKeeper nel quorum, ricreare l'indirizzo di destinazione:

`<destinationAddress>  = <Host_1_IP>,<Host_2_IP>,<Host_3_IP>:<Port>:<ZnodeParent>`

In questo esempio:

`<destinationAddress> = 10.0.0.9,10.0.0.8,10.0.0.12:2181:/hbase-unsecure`

## <a name="snapshots"></a>Snapshot

Gli [snapshot](https://hbase.apache.org/book.html#ops.snapshots) consentono di eseguire un backup temporizzato dei dati nell'archivio dati HBase. Gli snapshot comportano un overhead minimo e vengono completati in pochi secondi, perché un'operazione di creazione di snapshot è un'efficace operazione sui metadati che acquisisce i nomi di tutti i file in archivio in un preciso momento. Al momento della creazione di uno snapshot, non vengono copiati dati effettivi. Gli snapshot si basano sulla natura non modificabile dei dati archiviati in HDFS, dove le operazioni di aggiornamento, eliminazione e inserimento sono tutte rappresentate come nuovi dati. È possibile ripristinare ( *clonare* ) uno snapshot nello stesso cluster oppure esportare uno snapshot in un altro cluster.

Per creare uno snapshot, connettersi tramite SSH al nodo head del cluster HBase in HDInsight e avviare la shell `hbase`:

```console
hbase shell
```

All'interno della shell hbase usare il comando snapshot con i nomi della tabella e dello snapshot:

```console
snapshot '<tableName>', '<snapshotName>'
```

Per ripristinare uno snapshot in base al nome nella shell `hbase`, disabilitare prima di tutto la tabella, quindi ripristinare lo snapshot e riabilitare la tabella:

```console
disable '<tableName>'
restore_snapshot '<snapshotName>'
enable '<tableName>'
```

Per ripristinare uno snapshot in una nuova tabella, usare clone_snapshot:

```console
clone_snapshot '<snapshotName>', '<newTableName>'
```

Per esportare uno snapshot in HDFS per consentirne l'uso da parte di un altro cluster, creare prima di tutto lo snapshot come descritto in precedenza e quindi usare l'utilità ExportSnapshot. Eseguire questa utilità dalla sessione SSH nel nodo head, non all'interno della shell `hbase`:

```console
hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot <snapshotName> -copy-to <hdfsHBaseLocation>
```

`<hdfsHBaseLocation>` può essere qualsiasi posizione di archiviazione accessibile dal cluster di origine e deve puntare alla cartella hbase usata dal cluster di destinazione. Se, ad esempio, si ha un account di archiviazione di Azure secondario collegato al cluster di origine e tale account consente l'accesso al contenitore usato dall'archivio predefinito del cluster di destinazione, è possibile usare questo comando:

```console
hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot 'Snapshot1' -copy-to 'wasbs://secondcluster@myaccount.blob.core.windows.net/hbase'
```

Se non si ha un account di archiviazione di Azure secondario collegato al cluster di origine o se il cluster di origine è un cluster locale (o un cluster non HDI), è possibile che si verifichino problemi di autorizzazione quando si tenta di accedere all'account di archiviazione del cluster HDI. Per risolvere questo problema, specificare la chiave per l'account di archiviazione come parametro della riga di comando, come illustrato nell'esempio seguente. È possibile ottenere la chiave per l'account di archiviazione nell'portale di Azure.

```console
hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -Dfs.azure.account.key.myaccount.blob.core.windows.net=mykey -snapshot 'Snapshot1' -copy-to 'wasbs://secondcluster@myaccount.blob.core.windows.net/hbase'
```

Dopo l'esportazione dello snapshot, connettersi tramite SSH al nodo head del cluster di destinazione e ripristinare lo snapshot utilizzando il `restore_snapshot` comando come descritto in precedenza.

Gli snapshot forniscono un backup completo di una tabella al momento dell'esecuzione del comando `snapshot`. Gli snapshot non offrono la possibilità di eseguire snapshot incrementali in base all'intervallo di tempo, né di specificare subset di colonne da includere nello snapshot.

## <a name="replication"></a>Replica

La [replica di HBase](https://hbase.apache.org/book.html#_cluster_replication) esegue automaticamente il push delle transazioni da un cluster di origine a un cluster di destinazione, usando un meccanismo asincrono con un overhead minimo nel cluster di origine. In HDInsight è possibile configurare la replica tra cluster dove:

* I cluster di origine e di destinazione si trovano nella stessa rete virtuale.
* I cluster di origine e di destinazione si trovano in reti virtuali diverse connesse tramite un gateway VPN, ma entrambi i cluster sono nella stessa posizione geografica.
* I cluster di origine e di destinazione si trovano in reti virtuali diverse connesse tramite un gateway VPN, ma ogni cluster è in una posizione geografica diversa.

I passaggi generali per configurare la replica sono i seguenti:

1. Nel cluster di origine creare le tabelle e popolare i dati.
2. Nel cluster di destinazione creare tabelle di destinazione vuote con lo schema della tabella di origine.
3. Registrare il cluster di destinazione come peer per il cluster di origine.
4. Abilitare la replica nelle tabelle di origine desiderate.
5. Copiare i dati esistenti dalle tabelle di origine a quelle di destinazione.
6. La replica copia automaticamente le nuove modifiche ai dati delle tabelle di origine nelle tabelle di destinazione.

Per abilitare la replica in HDInsight, applicare un'azione script al cluster HDInsight di origine in esecuzione. Per una procedura dettagliata relativa all'abilitazione della replica nel cluster o per eseguire esperimenti con la replica nei cluster di esempio creati nelle reti virtuali usando modelli di Azure Resource Manager, vedere [Configurare la replica di Apache HBase](apache-hbase-replication.md). Tale articolo include anche le istruzioni per abilitare la replica dei metadati Phoenix.

## <a name="next-steps"></a>Passaggi successivi

* [Configurare la replica di Apache HBase](apache-hbase-replication.md)
* [Utilizzo dell'utilità di importazione ed esportazione di HBase](/archive/blogs/data_otaku/working-with-the-hbase-import-and-export-utility)