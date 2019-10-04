---
title: Ridimensionare i cluster - Azure HDInsight
description: Ridimensionare un cluster Apache Hadoop in modo elastico in base al carico di lavoro in Azure HDInsight
author: ashishthaps
ms.author: ashish
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: 4a1d835ebe47ec36bb839da8dcbcd107ffcb9c4c
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71161969"
---
# <a name="scale-azure-hdinsight-clusters"></a>Ridimensionare i cluster HDInsight di Azure

HDInsight offre elasticità permettendo di aumentare e ridurre il numero di nodi di lavoro nei cluster. Questa elasticità consente di compattare un cluster dopo ore o nei fine settimana ed espanderlo durante le esigenze aziendali di picco.

Se si dispone di un'elaborazione batch periodica, il cluster HDInsight può essere ridimensionato di alcuni minuti prima di tale operazione, in modo che il cluster disponga di una potenza di memoria e di CPU adeguata.  Successivamente, al termine dell'elaborazione e quando l'utilizzo diminuisce di nuovo, è possibile ridurre il cluster HDInsight a un numero inferiore di nodi di lavoro.

È possibile ridimensionare manualmente un cluster usando uno dei metodi descritti di seguito oppure usare le opzioni di [scalabilità](hdinsight-autoscale-clusters.md) automatica per aumentare e ridurre le prestazioni del sistema in risposta alla CPU, alla memoria e ad altre metriche.

> [!NOTE]  
> Sono supportati solo i cluster con HDInsight versione 3.1.3 o successive. Se non si è certi della versione del cluster, è possibile controllare la pagina delle proprietà.

## <a name="utilities-to-scale-clusters"></a>Utilità per la scalabilità di cluster

Microsoft fornisce le utilità seguenti per la scalabilità dei cluster:

|Utilità | Descrizione|
|---|---|
|[PowerShell AZ](https://docs.microsoft.com/powershell/azure)|[Set-AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) -clustername \<nome cluster >-TargetInstanceCount \<NewSize >|
|[AzureRM di PowerShell](https://docs.microsoft.com/powershell/azure/azurerm) |[Set-AzureRmHDInsightClusterSize](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) -clustername \<nome cluster >-TargetInstanceCount \<NewSize >|
|[Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)| [AZ HDInsight Resize](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) --Resource-Group \<Resource Group >--nome \<cluster nome >--Target-Instance-Count \<NewSize >|
|[Interfaccia della riga di comando di Azure](hdinsight-administer-use-command-line.md)|il numero di istanze \<di destinazione del \<cluster HDInsight di Azure clustername > > |
|[Portale di Azure](https://portal.azure.com)|Aprire il riquadro del cluster HDInsight, selezionare **dimensioni del cluster** nel menu a sinistra, quindi nel riquadro Dimensioni del cluster digitare il numero di nodi del ruolo di lavoro e selezionare Salva.|  

![Opzione del cluster portale di Azure scale](./media/hdinsight-scaling-best-practices/scale-cluster-blade1.png)

Usando uno di questi metodi, è possibile aumentare o ridurre il cluster HDInsight in pochi minuti.

> [!IMPORTANT]  
> * L'interfaccia della riga di comando di Azure classico è deprecata e deve essere usata solo con il modello di distribuzione classica. Per tutte le altre distribuzioni, usare l'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).  
> * Il modulo AzureRM di PowerShell è deprecato.  Usare il [modulo AZ](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) quando possibile.

## <a name="impact-of-scaling-operations"></a>Effetti delle operazioni di ridimensionamento

Quando si **aggiungono** nodi al cluster HDInsight in esecuzione (scalabilità verticale), eventuali processi in sospeso o in esecuzione non saranno interessati. È possibile inviare nuovi processi in tutta sicurezza durante l'esecuzione del processo di ridimensionamento. Se l'operazione di ridimensionamento ha esito negativo per qualsiasi motivo, l'errore verrà gestito in modo da lasciare il cluster in uno stato funzionale.

Se si **rimuovono** i nodi (riduzione), eventuali processi in sospeso o in esecuzione avranno esito negativo al termine dell'operazione di ridimensionamento. Questo errore è dovuto al riavvio di alcuni servizi durante il processo di ridimensionamento. Esiste anche il rischio che il cluster si blocchi in modalità provvisoria durante un'operazione di ridimensionamento manuale.

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

## <a name="how-to-safely-scale-down-a-cluster"></a>Come ridimensionare in modo sicuro un cluster

### <a name="scale-down-a-cluster-with-running-jobs"></a>Ridimensionare un cluster con processi in esecuzione

Per evitare che i processi in esecuzione abbiano esito negativo durante un'operazione di ridimensionamento, è possibile provare tre elementi:

1. Attendere il completamento dei processi prima di ridimensionare il cluster.
1. Terminare manualmente i processi.
1. Inviare nuovamente i processi dopo la conclusione dell'operazione di ridimensionamento.

Per visualizzare un elenco dei processi in sospeso e in esecuzione, è possibile usare l' **interfaccia utente**di Yarn gestione risorse, seguendo questa procedura:

1. Dal [portale di Azure](https://portal.azure.com/)selezionare il cluster.  Per le istruzioni vedere la sezione su come [elencare e visualizzare i cluster](./hdinsight-administer-use-portal-linux.md#showClusters). Il cluster viene aperto in una nuova pagina del portale.
2. Dalla visualizzazione principale passare a **cluster Dashboards** > **Ambari Home**. Immettere le credenziali del cluster.
3. Dall'interfaccia utente di Ambariri selezionare **Yarn** nell'elenco dei servizi nel menu a sinistra.  
4. Dalla pagina YARN selezionare **Quick Links (collegamenti rapidi** ) e passare il mouse sul nodo head attivo e quindi selezionare **ResourceManager UI (interfaccia utente di ResourceManager**).

    ![Interfaccia utente di ResourceManager per i collegamenti rapidi di Apache Ambari](./media/hdinsight-scaling-best-practices/resource-manager-ui1.png)

È possibile accedere direttamente all'interfaccia utente ResourceManager con `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster`.

Verrà visualizzato un elenco dei processi, insieme allo stato corrente di ognuno. Nello screenshot c'è un processo attualmente in esecuzione:

![Applicazioni nell'interfaccia utente ResourceManager](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

Per terminare manualmente l'applicazione in esecuzione, eseguire il comando seguente dalla shell SSH:

```bash
yarn application -kill <application_id>
```

Esempio:

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>Rimanere bloccati in modalità provvisoria

Quando si riduce un cluster, HDInsight usa le interfacce di gestione Apache Ambari per rimuovere prima le autorizzazioni dei nodi del ruolo di lavoro aggiuntivi, che replicano i blocchi HDFS in altri nodi di lavoro online. Successivamente, HDInsight ridimensiona il cluster in modo sicuro. HDFS passa alla modalità provvisoria durante l'operazione di ridimensionamento e dovrebbe uscire al termine del ridimensionamento. In alcuni casi, tuttavia, HDFS viene bloccato in modalità sicura durante un'operazione di ridimensionamento a causa del blocco di file in fase di replica.

Per impostazione predefinita, HDFS è configurato con `dfs.replication` l'impostazione 3, che controlla il numero di copie di ogni blocco di file disponibili. Ogni copia di un blocco di file è archiviata in un nodo diverso del cluster.

Quando HDFS rileva che il numero previsto di copie di blocco non è disponibile, HDFS entra in modalità provvisoria e Ambari genera avvisi. Se HDFS passa alla modalità provvisoria per un'operazione di ridimensionamento, ma non è possibile uscire dalla modalità sicura perché il numero di nodi necessario non viene rilevato per la replica, il cluster può rimanere bloccato in modalità provvisoria.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Errori di esempio quando la modalità sicura è attiva

```
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```
org.apache.http.conn.HttpHostConnectException: Connect to hn0-clustername.servername.internal.cloudapp.net:10001 [hn0-clustername.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

È possibile esaminare i log dei nodi dei nomi nella cartella `/var/log/hadoop/hdfs/`, in prossimità del momento in cui il cluster è stato ridimensionato, per visualizzare quando è stata attivata la modalità sicura. I file di log sono denominati `Hadoop-hdfs-namenode-hn0-clustername.*`.

La causa principale degli errori è che Hive dipende dai file temporanei in Hadoop Distributed File System durante l'esecuzione di query. Quando Hadoop Distributed File System entra in modalità sicura, Hive non può eseguire le query perché non può scrivere in Hadoop Distributed File System. I file temporanei in Hadoop Distributed File System si trovano nell'unità locale montata nelle singole macchine virtuali dei nodi di lavoro e vengono replicati tra altri nodi di lavoro in corrispondenza di almeno tre repliche.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>Come impedire a HDInsight di rimanere bloccati in modalità provvisoria

Esistono diversi modi per impedire che HDInsight venga lasciato in modalità sicura:

* Interrompere tutti i processi Hive prima della riduzione di HDInsight. In alternativa, pianificare il processo di riduzione per evitare conflitti con i processi Hive in esecuzione.
* Pulire manualmente i file temporanei della directory `tmp` di Hive in Hadoop Distributed File System prima della riduzione.
* Ridurre HDInsight solo a tre nodi di lavoro come minimo. Evitare di arrivare a un solo nodo di lavoro.
* Eseguire il comando per disattivare la modalità sicura, se necessario.

Le sezioni seguenti descrivono queste opzioni.

#### <a name="stop-all-hive-jobs"></a>Arrestare tutti i processi Hive

Interrompere tutti i processi Hive prima di eseguire la riduzione a un solo nodo di lavoro. Se il carico di lavoro è pianificato, eseguire la riduzione al termine delle operazioni di Hive.

L'arresto dei processi hive prima del ridimensionamento consente di ridurre al minimo il numero di file temporanei nella cartella tmp (se presente).

#### <a name="manually-clean-up-hives-scratch-files"></a>Pulire manualmente i file temporanei di Hive

Se sono rimasti file temporanei di Hive, è possibile pulire questi file manualmente prima della riduzione per evitare la modalità sicura.

1. Verificare quale percorso viene usato per i file temporanei hive esaminando la `hive.exec.scratchdir` proprietà di configurazione. Questo parametro è impostato in `/etc/hive/conf/hive-site.xml`:

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. Arrestare i servizi Hive e assicurarsi che tutte le query e i processi siano stati completati.
2. Elencare il contenuto della directory dei file temporanei `hdfs://mycluster/tmp/hive/` trovata sopra per verificare se contiene file:

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

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>Ridimensionare HDInsight a tre o più nodi del ruolo di lavoro

Se i cluster si bloccano spesso in modalità provvisoria quando si esegue il ridimensionamento a meno di tre nodi di lavoro e i passaggi precedenti non funzionano, è possibile evitare che il cluster si trovi in modalità provvisoria mantenendo almeno tre nodi di lavoro.

La conservazione di tre nodi di lavoro è più costosa rispetto alla scalabilità verticale di un solo nodo di lavoro, ma impedisce il blocco del cluster in modalità provvisoria.

#### <a name="run-the-command-to-leave-safe-mode"></a>Eseguire il comando per disattivare la modalità sicura

L'ultima opzione consiste nell'eseguire il comando lascia modalità provvisoria. Se si è certi che il motivo per cui si entra in modalità provvisoria è a causa di un file hive sottoreplica, è possibile eseguire il comando seguente per uscire dalla modalità sicura:

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>Ridimensionare un cluster Apache HBase

I server di area vengono bilanciati automaticamente entro pochi minuti dopo il completamento di un'operazione di ridimensionamento. Per bilanciare manualmente i server di area, attenersi alla procedura seguente:

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
