---
title: Ridimensionare i cluster - Azure HDInsight | Microsoft Docs
description: Ridimensionare un cluster HDInsight in base al carico di lavoro.
services: hdinsight
documentationcenter: 
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/02/2018
ms.author: ashish
ms.openlocfilehash: 7e9ee660c07d6265e55e94cf79ed13334fcb3d16
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2018
---
# <a name="scale-hdinsight-clusters"></a>Ridimensionare i cluster HDInsight

HDInsight offre elasticità permettendo di aumentare e ridurre il numero di nodi di lavoro nei cluster. In questo modo, è possibile ridurre un cluster nelle ore di chiusura o nei fine settimana ed espanderlo durante i picchi delle richieste aziendali.

Se, ad esempio, vengono eseguite attività di elaborazione batch una volta al giorno o una volta al mese, il cluster HDInsight può essere aumentato pochi minuti prima dell'evento pianificato in modo che sia disponibile un'adeguata potenza di calcolo della memoria e della CPU. È possibile automatizzare il ridimensionamento con il cmdlet [`Set–AzureRmHDInsightClusterSize`](hdinsight-administer-use-powershell.md#scale-clusters) di PowerShell.  Successivamente, al termine dell'elaborazione e quando l'utilizzo diminuisce di nuovo, è possibile ridurre il cluster HDInsight a un numero inferiore di nodi di lavoro.

* Per ridimensionare il cluster tramite [PowerShell](hdinsight-administer-use-powershell.md):

    ```powershell
    Set-AzureRmHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
    ```
    
* Per ridimensionare il cluster tramite l'[interfaccia della riga di comando di Azure](hdinsight-administer-use-command-line.md)

    ```
    azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>
    ```
    
* Per ridimensionare il cluster tramite il [portale di Azure](https://portal.azure.com), aprire il riquadro del cluster HDInsight, scegliere **Ridimensiona cluster** dal menu a sinistra, digitare il numero di nodi di lavoro nel riquadro Ridimensiona cluster e quindi selezionare Salva.

    ![Ridimensionare un cluster](./media/hdinsight-scaling-best-practices/scale-cluster-blade.png)

Usando uno di questi metodi, è possibile aumentare o ridurre il cluster HDInsight in pochi minuti.

## <a name="scaling-impacts-on-running-jobs"></a>Impatto del ridimensionamento sui processi in esecuzione

Quando si **aggiungono** nodi al cluster HDInsight in esecuzione, questa operazione non ha alcun impatto sui processi in sospeso o in esecuzione. Inoltre, è possibile inviare nuovi processi in tutta sicurezza durante l'esecuzione del processo di ridimensionamento. Se le operazioni di ridimensionamento non riescono per qualche motivo, l'errore viene gestito normalmente, lasciando il cluster in uno stato funzionale.

Tuttavia, se il cluster viene ridotto tramite la **rimozione** di nodi, qualsiasi processo in sospeso o in esecuzione non riuscirà al termine dell'operazione di ridimensionamento. L'errore è dovuto al riavvio di alcuni servizi durante il processo.

Per risolvere questo problema, è possibile attendere il completamento del processo prima di ridurre il cluster, terminare manualmente i processi o inviare di nuovo i processi al termine dell'operazione di ridimensionamento.

Per visualizzare un elenco dei processi in sospeso e in esecuzione, è possibile usare l'interfaccia utente ResourceManager di YARN seguendo questi passaggi:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nel menu a sinistra scegliere **Sfoglia**, scegliere **Cluster HDInsight** e quindi selezionare il cluster.
3. Nel riquadro del cluster HDInsight selezionare **Dashboard** nel menu superiore per aprire l'interfaccia utente di Ambari. Immettere le credenziali di accesso al cluster.
4. Fare clic su **YARN** nell'elenco dei servizi nel menu a sinistra. Nella pagina YARN selezionare **Collegamenti rapidi** e posizionare il puntatore del mouse sul nodo head attivo e quindi fare clic su **ResourceManager UI** (Interfaccia utente ResourceManager).

    ![Interfaccia utente ResourceManager](./media/hdinsight-scaling-best-practices/resourcemanager-ui.png)

È possibile accedere direttamente all'interfaccia utente ResourceManager con `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster`.

Verrà visualizzato un elenco dei processi, insieme allo stato corrente di ognuno. Lo screenshot mostra un processo attualmente in esecuzione:

![Applicazioni nell'interfaccia utente ResourceManager](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

Per terminare manualmente l'applicazione in esecuzione, eseguire il comando seguente dalla shell SSH:

```bash
yarn application -kill <application_id>
```

Ad esempio: 

```bash
yarn application -kill "application_1499348398273_0003"
```

## <a name="rebalancing-an-hbase-cluster"></a>Ribilanciamento di un cluster HBase

I server a livello di area vengono bilanciati automaticamente entro pochi minuti dal completamento dell'operazione di ridimensionamento. Per bilanciare manualmente i server a livello di area, seguire questa procedura:

1. Connettersi al cluster HDInsight tramite SSH. Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Avviare la shell HBase:

        hbase shell

3. Usare il comando seguente per bilanciare manualmente i server a livello di area:

        balancer

## <a name="scale-down-implications"></a>Implicazioni relative alla riduzione

Come accennato in precedenza, tutti i processi in sospeso e in esecuzione vengono terminati al completamento di un'operazione di riduzione. Tuttavia, la riduzione può avere altre potenziali implicazioni.

## <a name="hdinsight-name-node-stays-in-safe-mode-after-scaling-down"></a>Il nodo dei nomi HDInsight resta in modalità sicura dopo la riduzione

![Ridimensionare un cluster](./media/hdinsight-scaling-best-practices/scale-cluster.png)

Se si riduce il cluster al numero minimo di un nodo di lavoro, come mostrato nell'immagine precedente, Hadoop Distributed File System può restare bloccato in modalità sicura quando i nodi di lavoro vengono riavviati a causa dell'applicazione di patch oppure immediatamente dopo l'operazione di ridimensionamento.

La causa principale di questo comportamento è che Hive usa alcuni file `scratchdir` e, per impostazione predefinita, si aspetta tre repliche di ogni blocco, mentre è possibile una sola replica se il cluster viene ridotto al numero minimo di un nodo di lavoro. Di conseguenza, i file in `scratchdir` diventano *sottoreplicati*. Questo può far sì che Hadoop Distributed File System resti in modalità sicura al riavvio dei servizi dopo l'operazione di ridimensionamento.

Quando viene eseguito un tentativo di riduzione, HDInsight si affida alle interfacce di gestione di Ambari per rimuovere innanzitutto i nodi di lavoro aggiuntivi indesiderati, operazione che comporta la replica dei rispettivi blocchi Hadoop Distributed File System in altri nodi di lavoro online, e quindi ridurre in modo sicuro il cluster. Hadoop Distributed File System passa in modalità sicura durante la finestra di manutenzione e si suppone che ne esca al termine del ridimensionamento. È a questo punto che Hadoop Distributed File System può restare bloccato in modalità sicura.

Hadoop Distributed File System è configurato con l'opzione `dfs.replication` impostata su 3. In questo modo, i blocchi dei file temporanei sono sottoreplicati ogni volta che vi sono meno di tre nodi di lavoro online, perché non sono disponibili le tre copie previste di ogni blocco di file.

È possibile eseguire un comando per disattivare la modalità sicura per Hadoop Distributed File System. Ad esempio, se l'unico motivo per cui la modalità sicura è attiva è che i file temporanei sono sottoreplicati, è possibile disattivare la modalità sicura senza problemi. Questo è dovuto al fatto che i file sottoreplicati sono file temporanei di Hive.

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

Dopo aver disattivato la modalità sicura, è possibile rimuovere manualmente i file temporanei o attendere che sia Hive a pulirli automaticamente.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Errori di esempio quando la modalità sicura è attiva

* H070 Unable to open Hive session. org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.ipc.RetriableException): org.apache.hadoop.hdfs.server.namenode.SafeModeException: **Cannot create directory** /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. **Name node is in safe mode**. The reported blocks 75 needs additional 12 blocks to reach the threshold 0.9900 of total blocks 87. The number of live datanodes 10 has reached the minimum number 0. Safe mode will be turned off automatically once the thresholds have been reached (H070 Impossibile aprire la sessione di Hive. org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.ipc.RetriableException): org.apache.hadoop.hdfs.server.namenode.SafeModeException: Impossibile creare la directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Il nodo dei nomi è in modalità sicura. I 75 blocchi segnalati necessitano di altri 12 blocchi per raggiungere la soglia 0,9900 di 87 blocchi totali. Il numero di 10 nodi dati live ha raggiunto il numero minimo 0. La modalità sicura verrà disattivata automaticamente una volta raggiunte le soglie).

* H100 Unable to submit statement show databases: org.apache.thrift.transport.TTransportException: org.apache.http.conn.HttpHostConnectException: Connect to hn0-clustername.servername.internal.cloudapp.net:10001 [hn0-clustername.servername. internal.cloudapp.net/1.1.1.1] failed: **Connection refused**

* H020 Could not establish connection to hn0-hdisrv.servername.bx.internal.cloudapp.net:10001: org.apache.thrift.transport.TTransportException: Could not create http connection to http://hn0-hdisrv.servername.bx.internal.cloudapp.net:10001/. org.apache.http.conn.HttpHostConnectException: Connect to hn0-hdisrv.servername.bx.internal.cloudapp.net:10001 [hn0-hdisrv.servername.bx.internal.cloudapp.net/10.0.0.28] failed: Connection refused: org.apache.thrift.transport.TTransportException: Could not create http connection to http://hn0-hdisrv.servername.bx.internal.cloudapp.net:10001/. org.apache.http.conn.HttpHostConnectException: Connect to hn0-hdisrv.servername.bx.internal.cloudapp.net:10001 [hn0-hdisrv.servername.bx.internal.cloudapp.net/10.0.0.28] failed: **Connection refused** (H020 Impossibile stabilire la connessione a hn0-hdisrv.servername.bx.internal.cloudapp.net:10001: org.apache.thrift.transport.TTransportException: Impossibile creare la connessione HTTP a http://hn0-hdisrv.servername.bx.internal.cloudapp.net:10001/.org.apache.http.conn.HttpHostConnectException: Connessione a hn0-hdisrv.servername.bx.internal.cloudapp.net:10001 [hn0-hdisrv.servername.bx.internal.cloudapp.net/10.0.0.28] non riuscita: Connessione rifiutata: org.apache.thrift.transport.TTransportException: Impossibile creare la connessione HTTP a http://hn0-hdisrv.servername.bx.internal.cloudapp.net:10001/.org.apache.http.conn.HttpHostConnectException: Connessione a hn0-hdisrv.servername.bx.internal.cloudapp.net:10001 [hn0-hdisrv.servername.bx.internal.cloudapp.net/10.0.0.28] non riuscita: Connessione rifiutata)

* Dai log di Hive: WARN [main]: server.HiveServer2 (HiveServer2.java:startHiveServer2(442)) – Error starting HiveServer2 on attempt 21, will retry in 60 seconds java.lang.RuntimeException: Error applying authorization policy on hive configuration: org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.ipc.RetriableException): org.apache.hadoop.hdfs.server.namenode.SafeModeException: **Cannot create directory** /tmp/hive/hive/70a42b8a-9437-466e-acbe-da90b1614374. **Name node is in safe mode**.
    The reported blocks 0 needs additional 9 blocks to reach the threshold 0.9900 of total blocks 9.
    The number of live datanodes 10 has reached the minimum number 0. **Safe mode will be turned off automatically once the thresholds have been reached**.
    at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1324) (AVVISO [principale]: server.HiveServer2 (HiveServer2.java:startHiveServer2(442)) - Errore a partire da HiveServer2 al tentativo 21, l'operazione verrà ritentata tra 60 secondi java.lang.RuntimeException: Errore durante l'applicazione dei criteri di autorizzazione nella configurazione Hive: org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.ipc.RetriableException): org.apache.hadoop.hdfs.server.namenode.SafeModeException: Impossibile creare la directory /tmp/hive/hive/70a42b8a-9437-466e-acbe-da90b1614374. Il nodo dei nomi è in modalità sicura. Gli 0 blocchi segnalati necessitano di altri 9 blocchi per raggiungere la soglia 0,9900 di 9 blocchi totali. Il numero di 10 nodi dati live ha raggiunto il numero minimo 0. La modalità sicura verrà disattivata automaticamente una volta raggiunte le soglie. In corrispondenza di org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1324))

È possibile esaminare i log dei nodi dei nomi nella cartella `/var/log/hadoop/hdfs/`, in prossimità del momento in cui il cluster è stato ridimensionato, per visualizzare quando è stata attivata la modalità sicura. I file di log sono denominati `Hadoop-hdfs-namenode-hn0-clustername.*`.

La causa principale degli errori è che Hive dipende dai file temporanei in Hadoop Distributed File System durante l'esecuzione di query. Quando Hadoop Distributed File System entra in modalità sicura, Hive non può eseguire le query perché non può scrivere in Hadoop Distributed File System. I file temporanei in Hadoop Distributed File System si trovano nell'unità locale montata nelle singole macchine virtuali dei nodi di lavoro e vengono replicati tra altri nodi di lavoro in corrispondenza di almeno tre repliche.

Il parametro `hive.exec.scratchdir` in Hive è configurato in `/etc/hive/conf/hive-site.xml`:

```xml
<property>
    <name>hive.exec.scratchdir</name>
    <value>hdfs://mycluster/tmp/hive</value>
</property>
```

### <a name="view-the-health-and-state-of-your-hdfs-file-system"></a>Visualizzare l'integrità e lo stato di Hadoop Distributed File System

È possibile visualizzare un report di stato da ogni nodo dei nomi per determinare se i nodi siano in modalità sicura. Per visualizzare il report, usare SSH in ogni nodo head ed eseguire il comando seguente:

```
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode get
```

![Modalità sicura disattivata](./media/hdinsight-scaling-best-practices/safe-mode-off.png)

> [!NOTE]
> L'opzione `-D` è necessaria perché il file system predefinito in HDInsight è Archiviazione di Azure o Azure Data Lake Store. `-D` specifica che i comandi devono essere eseguiti nell'istanza di Hadoop Distributed File System locale.

È quindi possibile visualizzare un report che mostra i dettagli dello stato di Hadoop Distributed File System:

```
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -report
```

Questo comando restituisce l'output seguente in un cluster integro in cui tutti i blocchi vengono replicati al livello previsto:

![Modalità sicura disattivata](./media/hdinsight-scaling-best-practices/report.png)

Hadoop Distributed File System supporta il comando `fsck` per controllare la presenza di incoerenze con diversi file, ad esempio i blocchi mancanti per un file o i blocchi sottoreplicati. Per eseguire il comando `fsck` sui file `scratchdir` (disco dei file temporanei):

```
hdfs fsck -D 'fs.default.name=hdfs://mycluster/' /tmp/hive/hive
```

Quando il comando viene eseguito su un'istanza di Hadoop Distributed File System integra senza blocchi sottoreplicati, l'output visualizzato sarà simile al seguente:

```
Connecting to namenode via http://hn0-scalin.name.bx.internal.cloudapp.net:30070/fsck?ugi=sshuser&path=%2Ftmp%2Fhive%2Fhive
FSCK started by sshuser (auth:SIMPLE) from /10.0.0.21 for path /tmp/hive/hive at Thu Jul 06 20:07:01 UTC 2017
..Status: HEALTHY
 Total size:    53 B
 Total dirs:    5
 Total files:   2
 Total symlinks:                0 (Files currently being written: 2)
 Total blocks (validated):      2 (avg. block size 26 B)
 Minimally replicated blocks:   2 (100.0 %)
 Over-replicated blocks:        0 (0.0 %)
 Under-replicated blocks:       0 (0.0 %)
 Mis-replicated blocks:         0 (0.0 %)
 Default replication factor:    3
 Average block replication:     3.0
 Corrupt blocks:                0
 Missing replicas:              0 (0.0 %)
 Number of data-nodes:          4
 Number of racks:               1
FSCK ended at Thu Jul 06 20:07:01 UTC 2017 in 3 milliseconds


The filesystem under path '/tmp/hive/hive' is HEALTHY
```

Al contrario, quando il comando `fsck` viene eseguito su un'istanza di Hadoop Distributed File System con alcuni blocchi sottoreplicati, l'output sarà simile al seguente:

```
Connecting to namenode via http://hn0-scalin.name.bx.internal.cloudapp.net:30070/fsck?ugi=sshuser&path=%2Ftmp%2Fhive%2Fhive
FSCK started by sshuser (auth:SIMPLE) from /10.0.0.21 for path /tmp/hive/hive at Thu Jul 06 20:13:58 UTC 2017
.
/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info:  Under replicated BP-1867508080-10.0.0.21-1499348422953:blk_1073741826_1002. Target Replicas is 3 but found 1 live replica(s), 0 decommissioned replica(s) and 0 decommissioning replica(s).
.
/tmp/hive/hive/e7c03964-ff3a-4ee1-aa3c-90637a1f4591/inuse.info: CORRUPT blockpool BP-1867508080-10.0.0.21-1499348422953 block blk_1073741825

/tmp/hive/hive/e7c03964-ff3a-4ee1-aa3c-90637a1f4591/inuse.info: MISSING 1 blocks of total size 26 B.Status: CORRUPT
 Total size:    53 B
 Total dirs:    5
 Total files:   2
 Total symlinks:                0 (Files currently being written: 2)
 Total blocks (validated):      2 (avg. block size 26 B)
  ********************************
  UNDER MIN REPL'D BLOCKS:      1 (50.0 %)
  dfs.namenode.replication.min: 1
  CORRUPT FILES:        1
  MISSING BLOCKS:       1
  MISSING SIZE:         26 B
  CORRUPT BLOCKS:       1
  ********************************
 Minimally replicated blocks:   1 (50.0 %)
 Over-replicated blocks:        0 (0.0 %)
 Under-replicated blocks:       1 (50.0 %)
 Mis-replicated blocks:         0 (0.0 %)
 Default replication factor:    3
 Average block replication:     0.5
 Corrupt blocks:                1
 Missing replicas:              2 (33.333332 %)
 Number of data-nodes:          1
 Number of racks:               1
FSCK ended at Thu Jul 06 20:13:58 UTC 2017 in 28 milliseconds


The filesystem under path '/tmp/hive/hive' is CORRUPT
```

È anche possibile visualizzare lo stato di Hadoop Distributed File System nell'interfaccia utente di Ambari selezionando il servizio **Hadoop Distributed File System** a sinistra oppure con `https://<HDInsightClusterName>.azurehdinsight.net/#/main/services/HDFS/summary`.

![Stato di Hadoop Distributed File System in Ambari](./media/hdinsight-scaling-best-practices/ambari-hdfs.png)

Potrebbero essere visualizzati anche uno o più errori critici nei blocchi NameNode attivi o in standby. Per visualizzare l'integrità dei blocchi NameNode, selezionare il collegamento NameNode accanto all'avviso.

![Integrità dei blocchi NameNode](./media/hdinsight-scaling-best-practices/ambari-hdfs-crit.png)

Per pulire i file temporanei, operazione che rimuove gli errori di replica dei blocchi, usare SSH in ogni nodo head ed eseguire il comando seguente:

```
hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
```

> [!NOTE]
> Questo comando può interrompere Hive se alcuni processi sono ancora in esecuzione.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode-due-to-under-replicated-blocks"></a>Come impedire che HDInsight resti bloccato in modalità sicura a causa di blocchi sottoreplicati

Esistono diversi modi per impedire che HDInsight venga lasciato in modalità sicura:

* Interrompere tutti i processi Hive prima della riduzione di HDInsight. In alternativa, pianificare il processo di riduzione per evitare conflitti con i processi Hive in esecuzione.
* Pulire manualmente i file temporanei della directory `tmp` di Hive in Hadoop Distributed File System prima della riduzione.
* Ridurre HDInsight solo a tre nodi di lavoro come minimo. Evitare di arrivare a un solo nodo di lavoro.
* Eseguire il comando per disattivare la modalità sicura, se necessario.

Le sezioni seguenti descrivono queste opzioni.

#### <a name="stop-all-hive-jobs"></a>Arrestare tutti i processi Hive

Interrompere tutti i processi Hive prima di eseguire la riduzione a un solo nodo di lavoro. Se il carico di lavoro è pianificato, eseguire la riduzione al termine delle operazioni di Hive.

In questo modo, sarà possibile ridurre al minimo il numero di file temporanei nella cartella tmp (se presenti).

#### <a name="manually-clean-up-hives-scratch-files"></a>Pulire manualmente i file temporanei di Hive

Se sono rimasti file temporanei di Hive, è possibile pulire questi file manualmente prima della riduzione per evitare la modalità sicura.

1. Arrestare i servizi Hive e assicurarsi che tutte le query e i processi siano stati completati.

2. Elencare il contenuto della directory `hdfs://mycluster/tmp/hive/` per determinare se contiene file:

    ```
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```
    
    Ecco un esempio di output quando sono presenti file:

    ```
    sshuser@hn0-scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

3. Se si è certi che questi file non sono più necessari ad Hive, è possibile rimuoverli. Assicurarsi che Hive non abbia query in esecuzione esaminando la pagina dell'interfaccia utente ResourceManager di YARN.

    Riga di comando di esempio per rimuovere file da Hadoop Distributed File System:

    ```
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```
    
#### <a name="scale--hdinsight-to-three-worker-nodes"></a>Ridurre HDInsight a tre nodi di lavoro

Se il blocco in modalità sicura è un problema persistente e i passaggi precedenti non sono possibili, è possibile evitare questo problema ridimensionando il sistema solo a tre nodi di lavoro. Questa soluzione può non essere ottimale, a causa dei vincoli legati ai costi, rispetto alla riduzione a un solo nodo. Tuttavia, con un solo nodo di lavoro Hadoop Distributed File System non può garantire che per il cluster siano disponibili tre repliche dei dati.

#### <a name="run-the-command-to-leave-safe-mode"></a>Eseguire il comando per disattivare la modalità sicura

L'opzione finale è prestare attenzione alle rare occasioni in cui Hadoop Distributed File System entra in modalità sicura e quindi eseguire il comando per disattivarla. Dopo aver determinato che il motivo per cui Hadoop Distributed File System entra in modalità sicura è dovuto al fatto che i file di Hive sono sottoreplicati, eseguire il comando seguente per disattivare la modalità sicura:

* HDInsight in Linux:

    ```bash
    hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
    ```
    
* HDInsight in Windows:

    ```bash
    hdfs dfsadmin -fs hdfs://headnodehost:9000 -safemode leave
    ```
    
## <a name="next-steps"></a>Passaggi successivi

* [Introduzione ad Azure HDInsight](hadoop/apache-hadoop-introduction.md)
* [Ridimensionare i cluster](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Gestire i cluster HDInsight mediante l'utilizzo dell'interfaccia utente Web Ambari](hdinsight-hadoop-manage-ambari.md)