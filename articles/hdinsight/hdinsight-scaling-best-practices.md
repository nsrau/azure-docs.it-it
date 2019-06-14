---
title: Ridimensionare i cluster - Azure HDInsight
description: Ridimensionare un cluster HDInsight di Azure in modo elastico in modo che corrisponda il carico di lavoro.
author: ashishthaps
ms.author: ashish
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: b85277a4238351b6448c2cf29676ae3d8c118385
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077185"
---
# <a name="scale-hdinsight-clusters"></a>Ridimensionare i cluster HDInsight

HDInsight offre elasticità permettendo di aumentare e ridurre il numero di nodi di lavoro nei cluster. Questa flessibilità consente di ridurre un cluster dopo ore o nei fine settimana ed espanderlo durante i picchi delle richieste aziendali.

Se hai l'elaborazione di batch periodici, il cluster HDInsight può essere aumentato pochi minuti prima dell'operazione, in modo che il cluster disponga di sufficiente memoria e potenza della CPU.  Successivamente, al termine dell'elaborazione e quando l'utilizzo diminuisce di nuovo, è possibile ridurre il cluster HDInsight a un numero inferiore di nodi di lavoro.

È possibile ridimensionare un cluster manualmente usando uno dei metodi descritti di seguito oppure usare [scalabilità automatica](hdinsight-autoscale-clusters.md) scalabilità verticale di opzioni per disporre automaticamente del sistema in risposta a CPU, memoria e altre metriche.

> [!NOTE]  
> Sono supportati solo i cluster con HDInsight versione 3.1.3 o successive. Se non si è certi della versione del cluster, è possibile controllare la pagina delle proprietà.

## <a name="utilities-to-scale-clusters"></a>Utilità per ridimensionare i cluster

Microsoft fornisce le utilità seguenti per ridimensionare i cluster:

|Utility | Descrizione|
|---|---|
|[PowerShell Az](https://docs.microsoft.com/powershell/azure)|[Set-AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) - ClusterName \<nome Cluster > - TargetInstanceCount \<NewSize >|
|[PowerShell AzureRM](https://docs.microsoft.com/powershell/azure/azurerm) |[Set-AzureRmHDInsightClusterSize](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) - ClusterName \<nome Cluster > - TargetInstanceCount \<NewSize >|
|[Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)| [ridimensionamento hdinsight AZ](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) -resource-group \<gruppo di risorse >-nome \<nome Cluster >-destinazione-instance-count \<NewSize >|
|[Interfaccia della riga di comando di Azure](hdinsight-administer-use-command-line.md)|ridimensionamento del cluster Azure hdinsight \<clusterName > \<il numero di istanze di destinazione > |
|[Portale di Azure](https://portal.azure.com)|Aprire il riquadro del cluster HDInsight, selezionare **dimensioni del Cluster** nel menu a sinistra, quindi nel riquadro delle dimensioni del Cluster, digitare il numero di nodi di lavoro e selezionare Salva.|  

![Ridimensionare un cluster](./media/hdinsight-scaling-best-practices/scale-cluster-blade.png)

Usando uno di questi metodi, è possibile aumentare o ridurre il cluster HDInsight in pochi minuti.

> [!IMPORTANT]  
> * La CLI di Azure classica è deprecata e deve essere usata solo con il modello di distribuzione classica. Per tutte le altre distribuzioni, usare il [CLI Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).  
> * Il modulo AzureRM di PowerShell è deprecato.  Usare la [modulo di Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) laddove possibile.

## <a name="impact-of-scaling-operations"></a>Impatto delle operazioni di ridimensionamento

Quando si **aggiungere** nodi al cluster in esecuzione HDInsight (scalabilità verticale), tutti i processi in sospeso o in esecuzione non saranno interessati. È possibile inviare nuovi processi in tutta sicurezza durante l'esecuzione del processo di ridimensionamento. Se l'operazione di ridimensionamento non riesce per qualsiasi motivo, l'errore verrà gestito per lasciare il cluster in uno stato funzionale.

Se si **rimuovere** nodi (scalabilità verso il basso), verrà eventuali processi in sospeso o in esecuzione si verificano errori quando viene completata l'operazione di ridimensionamento. Questo errore è dovuto a alcuni dei servizi il riavvio durante il processo di ridimensionamento. È inoltre disponibile un rischio che il cluster può ottenere bloccato in modalità sicura durante l'operazione di ridimensionamento manuale.

Impatto della modifica del numero di nodi dati per ogni tipo di cluster supportato da HDInsight:

* Apache Hadoop

    È possibile aumentare facilmente il numero di nodi del ruolo di lavoro in un cluster Hadoop in esecuzione senza conseguenze per eventuali processi in sospeso o in esecuzione. È inoltre possibile inviare nuovi processi mentre è in corso l'operazione. Gli errori in un'operazione di scalabilità vengono gestiti in modo che il cluster rimanga sempre in uno stato funzionale.

    Quando un cluster Hadoop viene ridimensionato riducendo il numero di nodi dati, alcuni dei servizi del cluster vengono riavviati. A causa di questo comportamento, tutti i processi in esecuzione e in sospeso avranno esito negativo al completamento dell'operazione di ridimensionamento. È tuttavia possibile inviare nuovamente i processi una volta completata l'operazione.

* Apache HBase

    È possibile aggiungere o rimuovere facilmente nodi nel cluster HBase mentre è in esecuzione. I server a livello di area vengono bilanciati automaticamente entro pochi minuti dal completamento dell'operazione di ridimensionamento. È tuttavia possibile anche bilanciare manualmente i server a livello di area accedendo al nodo head del cluster ed eseguendo i comandi seguenti da una finestra del prompt dei comandi:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    Per altre informazioni sull'uso della shell HBase, vedere [Iniziare a usare un esempio di Apache HBase in HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md).

* Apache Storm

    È possibile aggiungere o rimuovere facilmente nodi dati dal cluster Storm mentre è in esecuzione. Tuttavia, dopo il completamento dell'operazione di ridimensionamento, è necessario bilanciare nuovamente la topologia.

    A tale scopo, è possibile scegliere tra due opzioni:

  * Interfaccia utente Web di Storm
  * Interfaccia della riga di comando (CLI)

    Per altri dettagli, vedere la [documentazione di Apache Storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

    L'interfaccia utente Web di Storm è disponibile nel cluster HDInsight:

    ![Ribilanciamento di HDInsight Storm](./media/hdinsight-scaling-best-practices/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Ecco un esempio di comando dell'interfaccia della riga di comando per bilanciare di nuovo la topologia di Storm:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

## <a name="how-to-safely-scale-down-a-cluster"></a>Come ridurre in modo sicuro a un cluster

### <a name="scale-down-a-cluster-with-running-jobs"></a>Ridurre un cluster con l'esecuzione di processi

Per evitare che i processi in esecuzione non riuscire durante un'operazione di riduzione, è possibile eseguire tre operazioni:

1. Attendere i completamento dei processi prima di ridurre il cluster.
1. Terminare manualmente i processi.
1. Inviare di nuovo i processi dopo l'operazione di ridimensionamento è conclusa.

Per visualizzare un elenco di sospeso e i processi in esecuzione, è possibile usare il YARN **Resource Manager UI**, attenendosi alla procedura seguente:

1. Dal [portale di Azure](https://portal.azure.com/), selezionare il cluster.  Per le istruzioni vedere la sezione su come [elencare e visualizzare i cluster](./hdinsight-administer-use-portal-linux.md#showClusters). Il cluster viene aperto in una nuova pagina del portale.
2. Dalla visualizzazione principale, passare a **dashboard Cluster** > **Ambari home**. Immettere le credenziali del cluster.
3. La UI Ambari, selezionare **YARN** nell'elenco dei servizi nel menu a sinistra.  
4. Nella pagina YARN, scegliere **collegamenti rapidi** e passare il mouse sul nodo head attivo, quindi selezionare **ResourceManager UI**.

    ![Interfaccia utente ResourceManager](./media/hdinsight-scaling-best-practices/resourcemanager-ui.png)

È possibile accedere direttamente all'interfaccia utente ResourceManager con `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster`.

Verrà visualizzato un elenco dei processi, insieme allo stato corrente di ognuno. Nella schermata, vi è un processo attualmente in esecuzione:

![Applicazioni nell'interfaccia utente ResourceManager](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

Per terminare manualmente l'applicazione in esecuzione, eseguire il comando seguente dalla shell SSH:

```bash
yarn application -kill <application_id>
```

Ad esempio:

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>Rimangono bloccati in modalità provvisoria

Quando si ridimensiona un cluster, HDInsight utilizza le interfacce di gestione di Apache Ambari per rimuovere innanzitutto i nodi di lavoro aggiuntivo, che la replica dei rispettivi blocchi di HDFS in altri nodi di lavoro in linea. Successivamente, HDInsight in modo sicuro riduce le prestazioni del cluster. HDFS entra in modalità sicura durante l'operazione di ridimensionamento e si suppone che ne esca dopo il ridimensionamento è completato. In alcuni casi, tuttavia, HDFS Ottiene bloccato in modalità sicura durante un'operazione di ridimensionamento a causa di replica correggerà blocco file.

Per impostazione predefinita, HDFS è configurato con un `dfs.replication` impostata su 3, che controlla il numero di copie di ogni blocco di file è disponibile. Ogni copia di un blocco di file viene archiviato in un nodo diverso del cluster.

Quando HDFS rileva che il numero previsto di copie di blocco non è disponibile, HDFS entra in modalità sicura e genera avvisi nel Ambari. Se HDFS entra in modalità sicura per un'operazione di ridimensionamento e quindi non è possibile uscire dalla modalità sicura perché il numero necessario di nodi non viene rilevato per la replica, il cluster può restare bloccato in modalità provvisoria.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Errori di esempio quando la modalità sicura è attiva

```
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```
org.apache.http.conn.HttpHostConnectException: Connect to hn0-clustername.servername.internal.cloudapp.net:10001 [hn0-clustername.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

È possibile esaminare i log dei nodi dei nomi nella cartella `/var/log/hadoop/hdfs/`, in prossimità del momento in cui il cluster è stato ridimensionato, per visualizzare quando è stata attivata la modalità sicura. I file di log sono denominati `Hadoop-hdfs-namenode-hn0-clustername.*`.

La causa principale degli errori è che Hive dipende dai file temporanei in Hadoop Distributed File System durante l'esecuzione di query. Quando Hadoop Distributed File System entra in modalità sicura, Hive non può eseguire le query perché non può scrivere in Hadoop Distributed File System. I file temporanei in Hadoop Distributed File System si trovano nell'unità locale montata nelle singole macchine virtuali dei nodi di lavoro e vengono replicati tra altri nodi di lavoro in corrispondenza di almeno tre repliche.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>Come impedire che HDInsight rimangono bloccati in modalità provvisoria

Esistono diversi modi per impedire che HDInsight venga lasciato in modalità sicura:

* Interrompere tutti i processi Hive prima della riduzione di HDInsight. In alternativa, pianificare il processo di riduzione per evitare conflitti con i processi Hive in esecuzione.
* Pulire manualmente i file temporanei della directory `tmp` di Hive in Hadoop Distributed File System prima della riduzione.
* Ridurre HDInsight solo a tre nodi di lavoro come minimo. Evitare di arrivare a un solo nodo di lavoro.
* Eseguire il comando per disattivare la modalità sicura, se necessario.

Le sezioni seguenti descrivono queste opzioni.

#### <a name="stop-all-hive-jobs"></a>Arrestare tutti i processi Hive

Interrompere tutti i processi Hive prima di eseguire la riduzione a un solo nodo di lavoro. Se il carico di lavoro è pianificato, eseguire la riduzione al termine delle operazioni di Hive.

Arrestare i processi Hive prima del ridimensionamento, consente di ridurre al minimo il numero di file temporanei nella cartella tmp (se presente).

#### <a name="manually-clean-up-hives-scratch-files"></a>Pulire manualmente i file temporanei di Hive

Se sono rimasti file temporanei di Hive, è possibile pulire questi file manualmente prima della riduzione per evitare la modalità sicura.

1. Controllare la posizione da cui viene utilizzata per i file temporanei di Hive esaminando il `hive.exec.scratchdir` proprietà di configurazione. Questo parametro viene impostato all'interno di `/etc/hive/conf/hive-site.xml`:

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. Arrestare i servizi Hive e assicurarsi che tutte le query e i processi siano stati completati.
2. Elencare il contenuto della directory di file temporanei trovato sopra, `hdfs://mycluster/tmp/hive/` per verificare se contiene tutti i file:

    ```bash
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```

    Ecco un esempio di output quando sono presenti file:

    ```output
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

    ```bash
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>HDInsight scalabilità a tre o più nodi di lavoro

Se i cluster di rimanere bloccati in modalità provvisoria frequentemente in caso di ridimensionamento fino a meno di tre nodi di lavoro e i passaggi precedenti non funzionano, è possibile evitare il cluster dovrà modalità provvisoria completamente mantenendo almeno tre nodi di lavoro.

Mantenendo tre nodi di lavoro è più costoso riduzione a un solo nodo di lavoro, ma impedirà il cluster rimangono bloccati in modalità provvisoria.

#### <a name="run-the-command-to-leave-safe-mode"></a>Eseguire il comando per disattivare la modalità sicura

L'opzione finale consiste nell'eseguire il comando di lasciare la modalità provvisoria. Se si sa che il motivo per entrare in modalità sicura di HDFS a causa di replica correggerà file Hive, è possibile eseguire il comando seguente per disattivare la modalità sicura:

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>Ridurre un cluster Apache HBase

Server di area vengono bilanciati automaticamente entro pochi minuti dopo aver completato un'operazione di ridimensionamento. Per bilanciare manualmente i server di area, completare i passaggi seguenti:

1. Connettersi al cluster HDInsight tramite SSH. Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Avviare la shell HBase:

    ```bash
    hbase shell
    ```

3. Usare il comando seguente per bilanciare manualmente i server a livello di area:

    ```bash
    balancer
    ```

## <a name="next-steps"></a>Passaggi successivi

* [Ridimensionare automaticamente i cluster HDInsight di Azure](hdinsight-autoscale-clusters.md)
* [Introduzione ad Azure HDInsight](hadoop/apache-hadoop-introduction.md)
