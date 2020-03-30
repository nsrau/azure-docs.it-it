---
title: Ridimensionare i cluster - Azure HDInsight
description: Ridimensionare elasticamente un cluster Apache Hadoop in base al carico di lavoro in Azure HDInsightScale an Apache Hadoop cluster elastically to match your workload in Azure HDInsight
author: ashishthaps
ms.author: ashish
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/26/2020
ms.openlocfilehash: 96a72541255ad0059abe5ad280f1728518dbf68c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234742"
---
# <a name="scale-azure-hdinsight-clusters"></a>Ridimensionare i cluster di Azure HDInsightScale Azure HDInsight clusters

HDInsight offre elasticità permettendo di aumentare e ridurre il numero di nodi di lavoro nei cluster. Questa elasticità consente di ridurre un cluster dopo l'orario di lavoro o nei fine settimana ed espanderlo durante le esigenze aziendali di picco.

Se si dispone di elaborazione batch periodica, il cluster HDInsight può essere aumentato alcuni minuti prima di tale operazione, in modo che il cluster disponga di memoria e potenza della CPU adeguate. Successivamente, al termine dell'elaborazione e quando l'utilizzo diminuisce di nuovo, è possibile ridurre il cluster HDInsight a un numero inferiore di nodi di lavoro.

È possibile ridimensionare un cluster manualmente utilizzando uno dei metodi descritti di seguito oppure usare le opzioni di [scalabilità automatica](hdinsight-autoscale-clusters.md) per fare in modo che il sistema venga ridimensionato automaticamente verso l'alto e verso il basso in risposta a CPU, memoria e altre metriche.

> [!NOTE]  
> Sono supportati solo i cluster con HDInsight versione 3.1.3 o successive. Se non si è certi della versione del cluster, è possibile controllare la pagina delle proprietà.

## <a name="utilities-to-scale-clusters"></a>Utilità per la scalabilità dei cluster

Microsoft fornisce le seguenti utilità per ridimensionare i cluster:

|Utilità | Descrizione|
|---|---|
|[PowerShell Az](https://docs.microsoft.com/powershell/azure)|[Set-AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) -ClusterName \<Nome cluster> \<-TargetInstanceCount NewSize>|
|[PowerShell AzureRM](https://docs.microsoft.com/powershell/azure/azurerm) |[Set-AzureRmHDInsightClusterSize](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) -ClusterName \<Nome cluster> \<-TargetInstanceCount NewSize>|
|[Interfaccia della riga di comando di AzureAzure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)| [az hdinsight resize](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) --resource-group \<Gruppo \<di risorse> --name Nome cluster> --workernode-count \<NewSize>|
|[Interfaccia della riga di comando classica di AzureAzure Classic CLI](hdinsight-administer-use-command-line.md)|Azure hdinsight \<cluster ridimensionare \<clusterName> Target Instance Count> |
|[Portale di Azure](https://portal.azure.com)|Aprire il riquadro del cluster HDInsight, selezionare Dimensioni cluster nel menu a sinistra, quindi nel riquadro Dimensioni cluster digitare il numero di nodi di lavoro e selezionare Salva.Open your HDInsight cluster pane, select **Cluster size** on the left-hand menu, then on the Cluster size pane, type in the number of worker nodes, and select Save.|  

![Opzione cluster di scalabilità del portale di AzureAzure portal scale cluster option](./media/hdinsight-scaling-best-practices/azure-portal-settings-nodes.png)

Usando uno di questi metodi, è possibile aumentare o ridurre il cluster HDInsight in pochi minuti.

> [!IMPORTANT]  
> * L'interfaccia della riga di comando classica di Azure è deprecata e deve essere usata solo con il modello di distribuzione classica. Per tutte le altre distribuzioni, usare l'interfaccia della riga di comando di [Azure.](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)  
> * Il modulo di PowerShell AzureRM è deprecato.  Si prega di utilizzare il [modulo Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) quando possibile.

## <a name="impact-of-scaling-operations"></a>Impatto delle operazioni di ridimensionamento

Quando si **aggiungono** nodi al cluster HDInsight in esecuzione (scalabilità verticale), eventuali processi in sospeso o in esecuzione non saranno interessati. È possibile inviare nuovi processi in tutta sicurezza durante l'esecuzione del processo di ridimensionamento. Se l'operazione di ridimensionamento non riesce per qualsiasi motivo, l'errore verrà gestito per lasciare il cluster in uno stato funzionale.

Se si **rimuovono** nodi (scalabilità verso il basso), tutti i processi in sospeso o in esecuzione avranno esito negativo al completamento dell'operazione di ridimensionamento. Questo errore è dovuto al riavvio di alcuni dei servizi durante il processo di ridimensionamento. Esiste anche il rischio che il cluster possa rimanere bloccato in modalità provvisoria durante un'operazione di ridimensionamento manuale.

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

    ```console
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

## <a name="how-to-safely-scale-down-a-cluster"></a>Come ridurre in modo sicuro un clusterHow to safely down a cluster

### <a name="scale-down-a-cluster-with-running-jobs"></a>Ridimensionare un cluster con processi in esecuzioneScale down a cluster with running jobs

Per evitare che i processi in esecuzione non riescano durante un'operazione di scalabilità verticale, è possibile provare tre cose:To avoid your running jobs fail during a scale down operation, you can try three things:

1. Attendere il completamento dei processi prima di ridurre il cluster.
1. Terminare manualmente i processi.
1. Inviare nuovamente i processi al termine dell'operazione di ridimensionamento.

Per visualizzare un elenco dei processi in sospeso e in esecuzione, è possibile utilizzare l'interfaccia utente di YARN **Resource Manager**, attenendosi alla seguente procedura:

1. Dal [portale di Azure](https://portal.azure.com/)selezionare il cluster.  Per le istruzioni vedere la sezione su come [elencare e visualizzare i cluster](./hdinsight-administer-use-portal-linux.md#showClusters). Il cluster viene aperto in una nuova pagina del portale.
2. Dalla vista principale, accedere a **Dashboard** > **cluster, homepage di Ambari.** Immettere le credenziali del cluster.
3. Dall'interfaccia utente di Ambari, seleziona **YARN** nell'elenco dei servizi nel menu a sinistra.  
4. Nella pagina YARN selezionare **Collegamenti rapidi** e passare il mouse sul nodo head attivo, quindi selezionare **ResourceManager UI**.

    ![Apache Ambari collegamenti rapidi ResourceManager UI](./media/hdinsight-scaling-best-practices/resource-manager-ui1.png)

È possibile accedere direttamente all'interfaccia utente ResourceManager con `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster`.

Verrà visualizzato un elenco dei processi, insieme allo stato corrente di ognuno. Nella schermata, c'è un lavoro attualmente in esecuzione:

![Applicazioni nell'interfaccia utente ResourceManager](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

Per terminare manualmente l'applicazione in esecuzione, eseguire il comando seguente dalla shell SSH:

```bash
yarn application -kill <application_id>
```

Ad esempio:

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>Rimanere bloccati in modalità provvisoria

Quando si riduce un cluster, HDInsight usa le interfacce di gestione Apache Ambari per rimuovere prima le autorizzazioni dei nodi di lavoro aggiuntivi, che replicano i blocchi HDFS in altri nodi di lavoro online. Successivamente, HDInsight ridimensiona in modo sicuro il cluster verso il basso. HDFS passa alla modalità provvisoria durante l'operazione di ridimensionamento e dovrebbe uscire una volta terminato il ridimensionamento. In alcuni casi, tuttavia, HDFS si blocca in modalità provvisoria durante un'operazione di ridimensionamento a causa di un blocco di file sotto-replica.

Per impostazione predefinita, HDFS è configurato con un'impostazione `dfs.replication` pari a 1, che controlla il numero di copie di ogni blocco di file disponibili. Ogni copia di un blocco di file viene archiviata in un nodo diverso del cluster.

Quando HDFS rileva che il numero previsto di copie di blocco non è disponibile, HDFS entra in modalità provvisoria e Ambari genera avvisi. Se HDFS entra in modalità provvisoria per un'operazione di ridimensionamento, ma non è possibile uscire dalla modalità provvisoria perché il numero richiesto di nodi non viene rilevato per la replica, il cluster può bloccarsi in modalità provvisoria.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Errori di esempio quando la modalità sicura è attiva

```output
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```output
org.apache.http.conn.HttpHostConnectException: Connect to active-headnode-name.servername.internal.cloudapp.net:10001 [active-headnode-name.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

È possibile esaminare i log dei nodi dei nomi nella cartella `/var/log/hadoop/hdfs/`, in prossimità del momento in cui il cluster è stato ridimensionato, per visualizzare quando è stata attivata la modalità sicura. I file di log sono denominati `Hadoop-hdfs-namenode-<active-headnode-name>.*`.

La causa principale degli errori è che Hive dipende dai file temporanei in Hadoop Distributed File System durante l'esecuzione di query. Quando Hadoop Distributed File System entra in modalità sicura, Hive non può eseguire le query perché non può scrivere in Hadoop Distributed File System. I file temporanei in Hadoop Distributed File System si trovano nell'unità locale montata nelle singole macchine virtuali dei nodi di lavoro e vengono replicati tra altri nodi di lavoro in corrispondenza di almeno tre repliche.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>Come evitare che HDInsight rimanga bloccato in modalità provvisoriaHow to prevent HDInsight from getting stuck in safe mode

Esistono diversi modi per impedire che HDInsight venga lasciato in modalità sicura:

* Interrompere tutti i processi Hive prima della riduzione di HDInsight. In alternativa, pianificare il processo di riduzione per evitare conflitti con i processi Hive in esecuzione.
* Pulire manualmente i file temporanei della directory `tmp` di Hive in Hadoop Distributed File System prima della riduzione.
* Ridurre HDInsight solo a tre nodi di lavoro come minimo. Evitare di arrivare a un solo nodo di lavoro.
* Eseguire il comando per disattivare la modalità sicura, se necessario.

Le sezioni seguenti descrivono queste opzioni.

#### <a name="stop-all-hive-jobs"></a>Arrestare tutti i processi Hive

Interrompere tutti i processi Hive prima di eseguire la riduzione a un solo nodo di lavoro. Se il carico di lavoro è pianificato, eseguire la riduzione al termine delle operazioni di Hive.

L'arresto dei processi Hive prima del ridimensionamento consente di ridurre al minimo il numero di file di memoria virtuale nella cartella tmp (se presente).

#### <a name="manually-clean-up-hives-scratch-files"></a>Pulire manualmente i file temporanei di Hive

Se sono rimasti file temporanei di Hive, è possibile pulire questi file manualmente prima della riduzione per evitare la modalità sicura.

1. Controllare quale percorso viene utilizzato per i file `hive.exec.scratchdir` temporanei Hive esaminando la proprietà di configurazione. Questo parametro `/etc/hive/conf/hive-site.xml`è impostato all'interno di :

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. Arrestare i servizi Hive e assicurarsi che tutte le query e i processi siano stati completati.
2. Elencare il contenuto della directory `hdfs://mycluster/tmp/hive/` scratch trovata sopra, per vedere se contiene file:

    ```bash
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```

    Ecco un esempio di output quando sono presenti file:

    ```output
    sshuser@scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
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

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>Ridimensionare HDInsight a tre o più nodi di lavoroScale HDInsight to three or more worker nodes

Se i cluster si bloccano spesso in modalità provvisoria quando si riduce a meno di tre nodi di lavoro e i passaggi precedenti non funzionano, è possibile evitare che il cluster passi completamente in modalità provvisoria mantenendo almeno tre nodi di lavoro.

Mantenere tre nodi di lavoro è più costoso della scalabilità verso il basso in un solo nodo di lavoro, ma impedirà al cluster di rimanere bloccato in modalità provvisoria.

### <a name="scale-hdinsight-down-to-one-worker-node"></a>Ridimensionare HDInsight a un nodo di lavoroScale HDInsight down to one worker node

Anche quando il cluster viene ridimensionato a 1 nodo, il nodo di lavoro 0 sopravviverà comunque. Il nodo di lavoro 0 non può mai essere rimosso.

#### <a name="run-the-command-to-leave-safe-mode"></a>Eseguire il comando per disattivare la modalità sicura

L'opzione finale consiste nell'eseguire il comando leave safe mode. Se si sa che il motivo per cui HDFS entra in modalità provvisoria è a causa della sottoreplica del file Hive, è possibile eseguire il comando seguente per uscire dalla modalità provvisoria:

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>Ridurre un cluster Apache HBase

I server di area vengono bilanciati automaticamente entro pochi minuti dal completamento di un'operazione di ridimensionamento. La procedura seguente illustra come bilanciare manualmente i server di area geografica:

1. Connettersi al cluster HDInsight tramite SSH. Per ulteriori informazioni, consultate [Usare SSH con HDInsight.](hdinsight-hadoop-linux-use-ssh-unix.md)

2. Avviare la shell HBase:

    ```bash
    hbase shell
    ```

3. Usare il comando seguente per bilanciare manualmente i server a livello di area:

    ```bash
    balancer
    ```

## <a name="next-steps"></a>Passaggi successivi

* [Ridimensionare automaticamente i cluster Azure HDInsight](hdinsight-autoscale-clusters.md)
* [Introduzione ad Azure HDInsight](hadoop/apache-hadoop-introduction.md)
