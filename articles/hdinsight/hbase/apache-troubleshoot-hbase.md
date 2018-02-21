---
title: Risolvere i problemi di HBase con Azure HDInsight | Microsoft Docs
description: Risposte alle domande comuni sull'uso di HBase e Azure HDInsight.
services: hdinsight
documentationcenter: 
author: nitinver
manager: ashitg
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 7/7/2017
ms.author: nitinver
ms.openlocfilehash: cd6315c192ad3c33d43406993b1a3e6bd6ec7e4d
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-hbase-by-using-azure-hdinsight"></a>Risolvere i problemi di HBase con Azure HDInsight

Questo articolo illustra i problemi principali che possono verificarsi quando si usano payload di Apache HBase in Apache Ambari e le relative risoluzioni.

## <a name="how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions"></a>Come eseguire report del comando hbck con più aree non assegnate

Un messaggio di errore comune che può essere visualizzato quando si esegue il comando `hbase hbck` riguarda la presenza di più aree non assegnate o difetti nella catena delle aree.

Nell'interfaccia utente di HBase Master è possibile visualizzare il numero delle aree non bilanciate tra tutti i server di area. Si può quindi eseguire il comando `hbase hbck` per visualizzare i difetti nella catena di aree.

Dato che i difetti possono essere causati dalle aree offline, per prima cosa correggere le assegnazioni. 

Per ripristinare uno stato normale delle aree non assegnate, seguire questa procedura:

1. Accedere al cluster HBase di HDInsight con SSH.
2. Per connettersi con la shell ZooKeeper, eseguire il comando `hbase zkcli`.
3. Eseguire il comando `rmr /hbase/regions-in-transition` o `rmr /hbase-unsecure/regions-in-transition`.
4. Per uscire dalla shell `hbase zkcli`, usare il comando `exit`.
5. Aprire l'interfaccia utente di Apache Ambari e quindi riavviare il servizio HBase Master attivo.
6. Eseguire di nuovo il comando `hbase hbck`, senza opzioni. Controllare l'output del comando per verificare che tutte le aree siano assegnate.


## <a name="how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments"></a>Come risolvere i problemi di timeout con i comandi hbck per le assegnazioni di aree

### <a name="issue"></a>Problema

Un possibile causa dei problemi di timeout quando si usa il comando `hbck` potrebbe essere che diverse aree restano a lungo in stato di transizione. Queste aree vengono visualizzate come offline nell'interfaccia utente di HBase Master. A causa del numero elevato di aree che tentano la transizione, può verificarsi il timeout di HBase Master, che non riuscirà a riportare online tali aree.

### <a name="resolution-steps"></a>Procedura per la risoluzione

1. Accedere al cluster HBase di HDInsight con SSH.
2. Per connettersi con la shell ZooKeeper, eseguire il comando `hbase zkcli`.
3. Eseguire il comando `rmr /hbase/regions-in-transition` o `rmr /hbase-unsecure/regions-in-transition`.
4. Per uscire dalla shell `hbase zkcli`, usare il comando `exit`.
5. Nell'interfaccia utente di Apache Ambari riavviare il servizio HBase Master attivo.
6. Eseguire di nuovo il comando `hbase hbck -fixAssignments`.

## <a name="how-do-i-force-disable-hdfs-safe-mode-in-a-cluster"></a>Come forzare la disabilitazione della modalità sicura di Hadoop Distributed File System in un cluster

### <a name="issue"></a>Problema

Il sistema HDFS (Hadoop Distributed File System) locale è bloccato in modalità sicura nel cluster HDInsight.

### <a name="detailed-description"></a>Descrizione dettagliata

Questo errore potrebbe essere causato da un errore durante l'esecuzione del comando HDFS seguente:

```apache
hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
```

Quando si prova a eseguire il comando, potrebbe essere visualizzato un errore come il seguente:

```apache
hdiuser@hn0-spark2:~$ hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
17/04/05 16:20:52 WARN retry.RetryInvocationHandler: Exception while invoking ClientNamenodeProtocolTranslatorPB.mkdirs over hn0-spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net/10.0.0.22:8020. Not retrying because try once and fail.
org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.hdfs.server.namenode.SafeModeException): Cannot create directory /temp. Name node is in safe mode.
It was turned on manually. Use "hdfs dfsadmin -safemode leave" to turn safe mode off.
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1359)
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.mkdirs(FSNamesystem.java:4010)
        at org.apache.hadoop.hdfs.server.namenode.NameNodeRpcServer.mkdirs(NameNodeRpcServer.java:1102)
        at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolServerSideTranslatorPB.mkdirs(ClientNamenodeProtocolServerSideTranslatorPB.java:630)
        at org.apache.hadoop.hdfs.protocol.proto.ClientNamenodeProtocolProtos$ClientNamenodeProtocol$2.callBlockingMethod(ClientNamenodeProtocolProtos.java)
        at org.apache.hadoop.ipc.ProtobufRpcEngine$Server$ProtoBufRpcInvoker.call(ProtobufRpcEngine.java:640)
        at org.apache.hadoop.ipc.RPC$Server.call(RPC.java:982)
        at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:2313)
        at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:2309)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:422)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1724)
        at org.apache.hadoop.ipc.Server$Handler.run(Server.java:2307)
        at org.apache.hadoop.ipc.Client.getRpcResponse(Client.java:1552)
        at org.apache.hadoop.ipc.Client.call(Client.java:1496)
        at org.apache.hadoop.ipc.Client.call(Client.java:1396)
        at org.apache.hadoop.ipc.ProtobufRpcEngine$Invoker.invoke(ProtobufRpcEngine.java:233)
        at com.sun.proxy.$Proxy10.mkdirs(Unknown Source)
        at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolTranslatorPB.mkdirs(ClientNamenodeProtocolTranslatorPB.java:603)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:498)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invokeMethod(RetryInvocationHandler.java:278)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invoke(RetryInvocationHandler.java:194)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invoke(RetryInvocationHandler.java:176)
        at com.sun.proxy.$Proxy11.mkdirs(Unknown Source)
        at org.apache.hadoop.hdfs.DFSClient.primitiveMkdir(DFSClient.java:3061)
        at org.apache.hadoop.hdfs.DFSClient.mkdirs(DFSClient.java:3031)
        at org.apache.hadoop.hdfs.DistributedFileSystem$24.doCall(DistributedFileSystem.java:1162)
        at org.apache.hadoop.hdfs.DistributedFileSystem$24.doCall(DistributedFileSystem.java:1158)
        at org.apache.hadoop.fs.FileSystemLinkResolver.resolve(FileSystemLinkResolver.java:81)
        at org.apache.hadoop.hdfs.DistributedFileSystem.mkdirsInternal(DistributedFileSystem.java:1158)
        at org.apache.hadoop.hdfs.DistributedFileSystem.mkdirs(DistributedFileSystem.java:1150)
        at org.apache.hadoop.fs.FileSystem.mkdirs(FileSystem.java:1898)
        at org.apache.hadoop.fs.shell.Mkdir.processNonexistentPath(Mkdir.java:76)
        at org.apache.hadoop.fs.shell.Command.processArgument(Command.java:273)
        at org.apache.hadoop.fs.shell.Command.processArguments(Command.java:255)
        at org.apache.hadoop.fs.shell.FsCommand.processRawArguments(FsCommand.java:119)
        at org.apache.hadoop.fs.shell.Command.run(Command.java:165)
        at org.apache.hadoop.fs.FsShell.run(FsShell.java:297)
        at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:76)
        at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:90)
        at org.apache.hadoop.fs.FsShell.main(FsShell.java:350)
mkdir: Cannot create directory /temp. Name node is in safe mode.
```

### <a name="probable-cause"></a>Possibile causa

Il cluster HDInsight è stato ridotto a pochissimi nodi. Il numero di nodi è inferiore o vicino al fattore di replica di HDFS.

### <a name="resolution-steps"></a>Procedura per la risoluzione 

1. Ottenere lo stato di HDFS nel cluster HDInsight eseguendo questi comandi:

   ```apache
   hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
   ```

   ```apache
   hdiuser@hn0-spark2:~$ hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
   Safe mode is ON
   Configured Capacity: 3372381241344 (3.07 TB)
   Present Capacity: 3138625077248 (2.85 TB)
   DFS Remaining: 3102710317056 (2.82 TB)
   DFS Used: 35914760192 (33.45 GB)
   DFS Used%: 1.14%
   Under replicated blocks: 0
   Blocks with corrupt replicas: 0
   Missing blocks: 0
   Missing blocks (with replication factor 1): 0

   -------------------------------------------------
   Live datanodes (8):

   Name: 10.0.0.17:30010 (10.0.0.17)
   Hostname: 10.0.0.17
   Decommission Status : Normal
   Configured Capacity: 421547655168 (392.60 GB)
   DFS Used: 5288128512 (4.92 GB)
   Non DFS Used: 29087272960 (27.09 GB)
   DFS Remaining: 387172253696 (360.58 GB)
   DFS Used%: 1.25%
   DFS Remaining%: 91.85%
   Configured Cache Capacity: 0 (0 B)
   Cache Used: 0 (0 B)
   Cache Remaining: 0 (0 B)
   Cache Used%: 100.00%
   Cache Remaining%: 0.00%
   Xceivers: 2
   Last contact: Wed Apr 05 16:22:00 UTC 2017
   ...

   ```
2. È anche possibile verificare l'integrità di HDFS nel cluster HDInsight usando i comandi seguenti:

   ```apache
   hdiuser@hn0-spark2:~$ hdfs fsck -D "fs.default.name=hdfs://mycluster/" /
   ```

   ```apache
   Connecting to namenode via http://hn0-spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net:30070/fsck?ugi=hdiuser&path=%2F
   FSCK started by hdiuser (auth:SIMPLE) from /10.0.0.22 for path / at Wed Apr 05 16:40:28 UTC 2017
   ....................................................................................................

   ....................................................................................................
   ..................Status: HEALTHY
   Total size:    9330539472 B
   Total dirs:    37
   Total files:   2618
   Total symlinks:                0 (Files currently being written: 2)
   Total blocks (validated):      2535 (avg. block size 3680686 B)
   Minimally replicated blocks:   2535 (100.0 %)
   Over-replicated blocks:        0 (0.0 %)
   Under-replicated blocks:       0 (0.0 %)
   Mis-replicated blocks:         0 (0.0 %)
   Default replication factor:    3
   Average block replication:     3.0
   Corrupt blocks:                0
   Missing replicas:              0 (0.0 %)
   Number of data-nodes:          8
   Number of racks:               1
   FSCK ended at Wed Apr 05 16:40:28 UTC 2017 in 187 milliseconds

   The filesystem under path '/' is HEALTHY
   ```

3. Se si determina che non esistono blocchi mancanti, danneggiati o replicati in modo insufficiente o che questi blocchi possono essere ignorati, eseguire questo comando per sbloccare il nodo dei nomi dalla modalità sicura:

   ```apache
   hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
   ```


## <a name="how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix"></a>Come risolvere i problemi di connettività relativi a JDBC o SQLLine con Apache Phoenix

### <a name="resolution-steps"></a>Procedura per la risoluzione

Per connettersi con Phoenix, è necessario specificare l'indirizzo IP di un nodo ZooKeeper attivo. Verificare che il servizio ZooKeeper a cui sqlline.py prova a connettersi sia operativo.
1. Accedere al cluster HDInsight con SSH.
2. Immettere il comando seguente:
                
   ```apache
           "/usr/hdp/current/phoenix-client/bin/sqlline.py <IP of machine where Active Zookeeper is running"
   ```

   > [!Note] 
   > È possibile ottenere l'indirizzo IP del nodo ZooKeeper attivo nell'interfaccia utente di Ambari. Passare a **HBase** > **Quick Links** > **ZK\* (Active)** > **Zookeeper Info** (HBase > Collegamenti rapidi > ZK - attivo > Info su Zookeeper). 

3. Se sqlline.py si connette a Phoenix senza timeout, eseguire questo comando per convalidare la disponibilità e l'integrità di Phoenix:

   ```apache
           !tables
           !quit
   ```      
4. Se il comando funziona, non sussistono problemi. L'indirizzo IP specificato dall'utente potrebbe essere errato. Se invece il comando resta in sospeso per un periodo di tempo prolungato e quindi visualizza l'errore seguente, procedere al passaggio 5.

   ```apache
           Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings. 
   ```

5. Eseguire questi comandi dal nodo head (hn0) per diagnosticare la condizione della tabella SYSTEM.CATALOG di Phoenix:

   ```apache
            hbase shell
                
           count 'SYSTEM.CATALOG'
   ```

   Il comando dovrebbe restituire un errore simile al seguente: 

   ```apache
           ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189) 
   ```
6. Nell'interfaccia utente di Ambari eseguire questi passaggi per riavviare il servizio HMaster in tutti i nodi ZooKeeper:

    1. Nella sezione **Summary** (Riepilogo) di HBase passare a **HBase** > **Active HBase Master** (HBase Master attivo). 
    2. Nella sezione **Components** (Componenti) riavviare il servizio HBase Master.
    3. Ripetere questi passaggi per tutti i rimanenti servizi **Standby HBase Master** (HBase Master in standby). 

Possono trascorrere fino a cinque minuti prima che il servizio HBase Master si stabilizzi e completi il processo di ripristino. Dopo alcuni minuti, ripetere i comandi sqlline.py per verificare che la tabella SYSTEM.CATALOG sia operativa e che sia possibile eseguirvi query. 

Quando la tabella SYSTEM.CATALOG torna allo stato normale, il problema di connettività a Phoenix dovrebbe essere automaticamente risolto.


## <a name="what-causes-a-master-server-to-fail-to-start"></a>Causa dell'errore di avvio del server master

### <a name="error"></a>Tipi di errore 

Si verifica un errore di ridenominazione atomica.

### <a name="detailed-description"></a>Descrizione dettagliata

Durante il processo di avvio, HMaster completa numerosi passaggi di inizializzazione, tra cui lo spostamento dei dati dalla cartella dei file temporanei (con estensione tmp) alla cartella dati. HMaster verifica anche nella cartella dei log write-ahead se sono presenti server di area che non rispondono e così via. 

Durante l'avvio, HMaster esegue un comando `list` di base su tali cartelle. Se in qualunque momento rileva un file imprevisto in una delle cartelle, HMaster genera un'eccezione e non viene avviato.  

### <a name="probable-cause"></a>Possibile causa

Nei log dei server di area provare a identificare la sequenza temporale della creazione dei file e quindi controllare se si sia verificato l'arresto anomalo di un processo all'incirca nel momento in cui è stato creato il file. Per ottenere assistenza a tale scopo, contattare il supporto HBase. In questo modo saranno disponibili meccanismi più affidabili per evitare di incorrere in questo bug e garantire l'arresto normale dei processi.

### <a name="resolution-steps"></a>Procedura per la risoluzione

Controllare lo stack di chiamate e provare a determinare quale cartella potrebbe causare il problema, ad esempio la cartella dei log write-ahead o quella dei file temporanei. In Cloud Explorer o con comandi HDFS provare quindi a individuare il file problematico, che è in genere un file \*-renamePending.json. \*-renamePending.json è un file journal usato per implementare l'operazione di ridenominazione atomica nel driver WASB. A causa di alcuni bug in questa implementazione, questi file possono essere ancora presenti in seguito ad arresti anomali dei processi e così via. Forzare l'eliminazione del file in Cloud Explorer o con comandi HDFS. 

In questo percorso potrebbe essere talvolta presente anche un file con un nome simile a *$$$.$$$*. Per visualizzare questo file è necessario usare il comando HDFS `ls`. Non viene visualizzato in Cloud Explorer. Per eliminare il file, usare il comando HDFS `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$`.  

Dopo l'esecuzione di questi comandi, HMaster dovrebbe essere avviato immediatamente. 

### <a name="error"></a>Tipi di errore

In *hbase: meta* non è elencato alcun indirizzo del server per l'area xxx.

### <a name="detailed-description"></a>Descrizione dettagliata

In un cluster Linux potrebbe essere visualizzato un messaggio che indica che la tabella *hbase: meta* non è online. L'esecuzione di `hbck` potrebbe segnalare che non è stato possibile trovare la tabella hbase: meta con replicaId 0 in alcuna area. Il problema potrebbe essere che non è stato possibile inizializzare HMaster dopo il riavvio di HBase. Nei log di HMaster viene visualizzato un messaggio che segnala che in hbase: meta non è elencato alcun indirizzo del server per l'area "hbase: backup \<region name\>".  

### <a name="resolution-steps"></a>Procedura per la risoluzione

1. Nella shell di HBase digitare i comandi seguenti, modificando i valori effettivi a seconda dei casi:  

   ```apache
   > scan 'hbase:meta'  
   ```

   ```apache
   > delete 'hbase:meta','hbase:backup <region name>','<column name>'  
   ```

2. Eliminare la voce *hbase: namespace*. Questa voce potrebbe corrispondere all'errore segnalato durante l'analisi della tabella *hbase: namespace*.

3. Per ripristinare lo stato di esecuzione di HBase, nell'interfaccia utente di Ambari riavviare il servizio HMaster attivo.  

4. Nella shell di HBase eseguire questo comando per rendere disponibili tutte le tabelle offline:

   ```apache 
   hbase hbck -ignorePreCheckPermission -fixAssignments 
   ```

### <a name="additional-reading"></a>Informazioni aggiuntive

[Impossibile elaborare la tabella HBase](http://stackoverflow.com/questions/4794092/unable-to-access-hbase-table)


### <a name="error"></a>Tipi di errore

Si verifica il timeout di HMaster con un'eccezione irreversibile di tipo java.io.IOException, che indica un timeout di 300.000 millisecondi in attesa dell'assegnazione della tabella namespace.

### <a name="detailed-description"></a>Descrizione dettagliata

Questo problema potrebbe verificarsi se al riavvio dei servizi HMaster sono presenti diverse tabelle e aree che non sono state scaricate. Il riavvio potrebbe avere esito negativo e verrà visualizzato il messaggio di errore descritto sopra.  

### <a name="probable-cause"></a>Possibile causa

Si tratta di un problema noto relativo al servizio HMaster. Le attività di avvio generali del cluster possono richiedere molto tempo. HMaster si arresta perché la tabella namespace non è ancora stata assegnata. Questo si verifica solo in scenari in cui è presente una grande quantità di dati non scaricati e un timeout di cinque minuti non è sufficiente.
  
### <a name="resolution-steps"></a>Procedura per la risoluzione

1. Nell'interfaccia utente di Ambari passare a **HBase** > **Configs** (Configurazioni). Nel file hbase-site.xml personalizzato aggiungere l'impostazione seguente: 

   ```apache
   Key: hbase.master.namespace.init.timeout Value: 2400000  
   ```

2. Riavviare i servizi necessari (HMaster e possibilmente altri servizi HBase).  


## <a name="what-causes-a-restart-failure-on-a-region-server"></a>Causa di un errore di riavvio in un server di area

### <a name="issue"></a>Problema

È possibile prevenire un errore di avvio in un server di area seguendo le procedure consigliate. Quando si pianifica il riavvio di server di area di HBase, è consigliabile sospendere le attività con carichi di lavoro elevati. Se l'applicazione continua a connettersi ai server di area mentre è in corso l'arresto, l'operazione di riavvio dei server di area verrà rallentata di diversi minuti. È anche opportuno scaricare prima tutte le tabelle. Per informazioni su come scaricare le tabelle, vedere [HDInsight HBase: How to improve the HBase cluster restart time by flushing tables](https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/) (HBase in HDInsight: come migliorare i tempi di riavvio del cluster HBase scaricando le tabelle).

Se l'operazione di riavvio sui server di area di HBase viene avviata dall'interfaccia utente di Ambari, viene visualizzato immediatamente che i server di area sono stati arrestati, ma non vengono riavviati subito. 

Di seguito è riportato che cosa avviene in background: 

1. L'agente Ambari invia una richiesta di arresto al server di area.
2. L'agente Ambari attende per 30 secondi l'arresto normale del server di area. 
3. Se l'applicazione continua a connettersi al server di area, il server non viene arrestato immediatamente. Il timeout di 30 secondi scade prima dell'arresto. 
4. Dopo 30 secondi, l'agente Ambari invia un comando di terminazione forzata (`kill -9`) al server di area. Questa operazione è riportata nel log ambari-agent, disponibile nella directory /var/log/ del rispettivo nodo di lavoro:

   ```apache
           2017-03-21 13:22:09,171 - Execute['/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh --config /usr/hdp/current/hbase-regionserver/conf stop regionserver'] {'only_if': 'ambari-sudo.sh  -H -E t
           est -f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >/dev/null 2>&1', 'on_timeout': '! ( ambari-sudo.sh  -H -E test -
           f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >/dev/null 2>&1 ) || ambari-sudo.sh -H -E kill -9 `ambari-sudo.sh  -H 
           -E cat /var/run/hbase/hbase-hbase-regionserver.pid`', 'timeout': 30, 'user': 'hbase'}
           2017-03-21 13:22:40,268 - Executing '! ( ambari-sudo.sh  -H -E test -f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >
           /dev/null 2>&1 ) || ambari-sudo.sh -H -E kill -9 `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid`'. Reason: Execution of 'ambari-sudo.sh su hbase -l -s /bin/bash -c 'export  
           PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/var/lib/ambari-agent ; /usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh --config /usr/hdp/curre
           nt/hbase-regionserver/conf stop regionserver was killed due timeout after 30 seconds
           2017-03-21 13:22:40,285 - File['/var/run/hbase/hbase-hbase-regionserver.pid'] {'action': ['delete']}
           2017-03-21 13:22:40,285 - Deleting File['/var/run/hbase/hbase-hbase-regionserver.pid']
   ```
A causa dell'arresto improvviso, la porta associata al processo potrebbe non essere rilasciata nonostante il processo del server di area venga arrestato. Questa situazione può generare un'eccezione AddressBindException durante l'avvio del server di area, come illustrato nei log di seguito. È possibile verificare questa condizione nel file region-server.log nella directory /var/log/hbase dei nodi di lavoro in cui l'avvio del server di area ha esito negativo. 

   ```apache

   2017-03-21 13:25:47,061 ERROR [main] regionserver.HRegionServerCommandLine: Region server exiting
   java.lang.RuntimeException: Failed construction of Regionserver: class org.apache.hadoop.hbase.regionserver.HRegionServer
   at org.apache.hadoop.hbase.regionserver.HRegionServer.constructRegionServer(HRegionServer.java:2636)
   at org.apache.hadoop.hbase.regionserver.HRegionServerCommandLine.start(HRegionServerCommandLine.java:64)
   at org.apache.hadoop.hbase.regionserver.HRegionServerCommandLine.run(HRegionServerCommandLine.java:87)
   at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:76)
   at org.apache.hadoop.hbase.util.ServerCommandLine.doMain(ServerCommandLine.java:126)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.main(HRegionServer.java:2651)
        
   Caused by: java.lang.reflect.InvocationTargetException
   at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
   at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:57)
   at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)
   at java.lang.reflect.Constructor.newInstance(Constructor.java:526)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.constructRegionServer(HRegionServer.java:2634)
   ... 5 more
        
   Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
   at org.apache.hadoop.hbase.ipc.RpcServer.bind(RpcServer.java:2497)
   at org.apache.hadoop.hbase.ipc.RpcServer$Listener.<init>(RpcServer.java:580)
   at org.apache.hadoop.hbase.ipc.RpcServer.<init>(RpcServer.java:1982)
   at org.apache.hadoop.hbase.regionserver.RSRpcServices.<init>(RSRpcServices.java:863)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.createRpcServices(HRegionServer.java:632)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.<init>(HRegionServer.java:532)
   ... 10 more
        
   Caused by: java.net.BindException: Address already in use
   at sun.nio.ch.Net.bind0(Native Method)
   at sun.nio.ch.Net.bind(Net.java:463)
   at sun.nio.ch.Net.bind(Net.java:455)
   at sun.nio.ch.ServerSocketChannelImpl.bind(ServerSocketChannelImpl.java:223)
   at sun.nio.ch.ServerSocketAdaptor.bind(ServerSocketAdaptor.java:74)
   at org.apache.hadoop.hbase.ipc.RpcServer.bind(RpcServer.java:2495)
   ... 15 more
   ```

### <a name="resolution-steps"></a>Procedura per la risoluzione

1. Provare a ridurre il carico sui server di area di HBase prima di procedere a un riavvio. 
2. In alternativa, se il passaggio 1 non è stato utile, provare a riavviare manualmente i server di area nei nodi di lavoro con i comandi seguenti:

   ```apache
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"   
   ```

### <a name="see-also"></a>Vedere anche
[Risolvere i problemi usando Azure HDInsight](../../hdinsight/hdinsight-troubleshoot-guide.md)