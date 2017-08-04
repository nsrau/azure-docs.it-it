---
title: Risoluzione dei problemi relativi a HBase - Azure HDInsight | Microsoft Docs
description: Risoluzione delle cause dei difetti nella catena delle aree.
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
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 29b1776d6b0c456515738aae3c5fd836c9a0295d
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---

# <a name="hbase-troubleshooting"></a>Risoluzione dei problemi relativi a HBASE

Questo articolo descrive i problemi principali e le rispettive soluzioni per l'uso di payload HBASE in Apache Ambari.

## <a name="how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions"></a>Come si eseguono i report dei comandi hbck con più aree non assegnate?

La presenza di più aree non assegnate o di difetti nella catena delle aree quando l'utente di HBase esegue il comando 'hbase hbck' è un problema comune.

In presenza di questo problema, l'utente nota un conteggio di aree sbilanciato tra tutti i server di area dall'interfaccia utente di HBase Master. L'utente esegue quindi il comando 'hbase hbck' e riscontra difetti nella catena di aree.

L'utente deve prima di tutto correggere le assegnazioni, perché questi difetti possono essere dovuti alle aree offline. 

Seguire i passaggi seguenti per ripristinare lo stato normale delle aree non assegnate:

1. Accedere al cluster HDInsight HBase con SSH.
1. Eseguire il comando 'hbase zkcli' per connettersi alla shell zookeeper.
1. Eseguire il comando 'rmr /hbase/regions-in-transition' o 'rmr /hbase-unsecure/regions-in-transition'.
1. Uscire dalla shell 'hbase zkcli' usando il comando 'exit'.
1. Aprire l'interfaccia utente di Ambari e riavviare il servizio Active HBase Master da Ambari.
1. Eseguire di nuovo il comando 'hbase hbck' (senza altre opzioni).

Controllare l'output del comando nel passaggio 6 e assicurarsi che tutte le aree siano assegnate.

---

## <a name="how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments"></a>Come si risolvono i problemi di timeout dei comandi hbck per le assegnazioni delle aree?

### <a name="probable-cause"></a>Possibile causa

La possibile causa di questo problema può essere dovuta al fatto che diverse aree sono in stato "in transizione" per un lungo periodo. Queste aree possono essere visualizzate come offline dall'interfaccia utente di HBase Master. A causa del numero elevato di aree che tentano la transizione, può verificarsi il timeout di HBase Master, che non sarà in grado di ripristinare lo stato online delle aree.

### <a name="resolution-steps"></a>Procedura per la risoluzione

Di seguito sono indicati i passaggi per correggere il problema di timeout di hbck:

1. Accedere al cluster HDInsight HBase con SSH.
1. Eseguire il comando 'hbase zkcli' per connettersi alla shell zookeeper.
1. Eseguire il comando 'rmr /hbase/regions-in-transition' o 'rmr /hbase-unsecure/regions-in-transition'.
1. Uscire dalla shell 'hbase zkcli' usando il comando 'exit'.
1. Aprire l'interfaccia utente di Ambari e riavviare il servizio Active HBase Master da Ambari.
1. Eseguire di nuovo il comando 'hbase hbck -fixAssignments'. Il timeout non dovrebbe più verificarsi.

## <a name="how-do-i-force-disable-hdfs-safe-mode-in-an-cluster"></a>Come si forza la disabilitazione della modalità sicura di HDFS in un cluster?

### <a name="issue"></a>Problema:

Il sistema HDFS locale è bloccato in modalità sicura nel cluster HDInsight.   

### <a name="detailed-description"></a>Descrizione dettagliata:

Non è possibile eseguire il comando HDFS semplice nel modo seguente:

```apache
hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
```

Durante il tentativo di eseguire il comando precedente, si è verificato questo errore:

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

### <a name="probable-cause"></a>Possibile causa:

Il cluster HDInsight è stato ridotto a un numero minimo di nodi, inferiore o vicino al fattore di replica di HDFS.

### <a name="resolution-steps"></a>Procedura per la risoluzione: 

- Ottenere informazioni sullo stato di HDFS nel cluster HDInsight con i comandi seguenti:

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
- È anche possibile controllare l'integrità di HDFS nel cluster HDInsight con i comandi seguenti:

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

- Se non si riscontrano blocchi mancanti, danneggiati o replicati in modo insufficiente o questi blocchi possono essere ignorati, eseguire il comando seguente per disattivare la modalità sicura per il nodo dei nomi:

```apache
hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
```


## <a name="how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix"></a>Come si risolvono i problemi di connettività relativi a JDBC o sqlline con Apache Phoenix?

### <a name="resolution-steps"></a>Procedura per la risoluzione:

Per connettersi ad Apache Phoenix, è necessario specificare l'indirizzo IP del nodo zookeeper attivo. Assicurarsi che il servizio zookeeper cui sqlline.py tenta di connettersi sia operativo.
1. Effettuare l'accesso al cluster HDInsight con SSH.
1. Provare il comando seguente:
        
```apache
        "/usr/hdp/current/phoenix-client/bin/sqlline.py <IP of machine where Active Zookeeper is running"
```     
    Note: The IP of Active Zooker node can be identified from Ambari UI, by following the links to HBase -> "Quick Links" -> "ZK* (Active)" -> "Zookeeper Info". 

Se sqlline.py si connette ad Apache Phoenix e non si verifica il timeout, eseguire il comando seguente per verificare la disponibilità e l'integrità di Apache Phoenix:

```apache
        !tables
        !quit
```      
- Se il comando precedente funziona, il problema è stato corretto. L'IP fornito dall'utente potrebbe non essere corretto.
   
    Tuttavia, se il comando viene sospeso troppo a lungo e quindi genera l'errore seguente, continuare a seguire la guida alla risoluzione dei problemi di seguito:

```apache
        Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings. 
```

- Eseguire i comandi seguenti dal nodo head (hn0) per determinare la condizione della tabella SYSTEM.CATALOG di Phoenix:

```apache
        hbase shell
        
        count 'SYSTEM.CATALOG'
```        
- Il comando dovrebbe restituire un errore simile al seguente: 

```apache
        ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189) 
```
- Riavviare il servizio HMaster in tutti i nodi zookeeper dall'interfaccia utente di Ambari seguendo questi passaggi:

    a. Passare al collegamento "HBase -> Active HBase Master" nella sezione Summary (Riepilogo) di HBase. 
    a. Nella sezione Components (Componenti) riavviare il servizio HBase Master.
    a. Ripetere i passaggi precedenti per i servizi "Standby HBase Master" rimanenti. 

Possono essere necessari fino a cinque minuti prima che il servizio HBase Master si stabilizzi e completi il ripristino. Dopo alcuni minuti di attesa, ripetere i comandi sqlline.py per assicurarsi che la tabella del catalogo di sistema sia operativa e che sia possibile eseguirvi query. 

Quando la tabella 'SYSTEM.CATALOG' torna allo stato normale, il problema di connettività ad Apache Phoenix dovrebbe essere automaticamente risolto.


## <a name="what-causes-a-master-server-to-fail-to-start"></a>Qual è la causa di un errore di avvio di un server master?

### <a name="error"></a>Errore: 

Errore di ridenominazione atomica

### <a name="detailed-description"></a>Descrizione dettagliata:

Durante il processo di avvio, HMaster esegue numerosi passaggi di inizializzazione, tra cui lo spostamento dei dati dalla cartella dei file temporanei (TMP) alla cartella dei dati ed esamina anche la cartella dei log write-ahead per determinare se vi siano server di area inutilizzati o altri problemi. 

In tutte queste situazioni il servizio esegue un comando 'list' di base nelle cartelle. Se in qualunque momento riscontra un file imprevisto in una delle cartelle, genera un'eccezione e di conseguenza non viene avviato.  

### <a name="probable-cause"></a>Possibile causa:

Provare a identificare la sequenza temporale della creazione dei file e controllare se si sia verificato l'arresto anomalo di un processo nei log dei server di area. Contattare qualcuno del team di HBase per ottenere supporto. In questo modo, saranno disponibili meccanismi più affidabili per evitare di incorrere in questo bug e garantire arresti dei processi corretti.

### <a name="resolution-steps"></a>Procedura per la risoluzione:

In una situazione di questo tipo, provare a controllare lo stack di chiamate e individuare la cartella che potrebbe causare il problema, ad esempio la cartella dei log write-ahead o dei file temporanei. Tramite Cloud Explorer o i comandi hdfs provare quindi a individuare il file problematico, in genere un file *-renamePending.json, ovvero il file journal usato per implementare l'operazione di ridenominazione atomica nel driver WASB. A causa di alcuni bug in questa implementazione, tali file possono essere ignorati in caso di arresto anomalo di un processo o altri problemi. Forzare l'eliminazione anche tramite Cloud Explorer. 

Inoltre, a volte può essere presente un file temporaneo di natura $$$.$$$ in questa posizione, che non può essere individuato con Cloud Explorer, ma solo tramite il comando Is hdfs. È possibile usare il comando hdfs "hdfs dfs -rm /<the path>/\$\$\$.\$\$\$" per eliminare il file.  

Al termine, HMaster dovrebbe avviarsi immediatamente. 

### <a name="error-no-server-address-listed-in"></a>Errore: nessun indirizzo del server elencato 

In hbase: meta non è elencato alcun indirizzo del server per l'area xxx

### <a name="detailed-description"></a>Descrizione dettagliata:

Il cliente ha riscontrato un problema sul proprio cluster Linux, relativo al fatto che la tabella hbase: meta non è online. L'esecuzione di hbck ha segnalato che non è stato possibile trovare la tabella hbase: meta con replicaId 0 in alcuna area. Dopo aver riavviato HBase, il sintomo è risultato essere la mancata inizializzazione di hmaster. Nei log di hmaster viene segnalato che in hbase: meta non è elencato alcun indirizzo del server per l'area hbase: backup <region name>.  

### <a name="resolution-steps"></a>Procedura per la risoluzione:

- Digitare il comando seguente nella shell di HBase, modificando i valori effettivi a seconda dei casi:  

```apache
> scan 'hbase:meta'  
```

```apache
> delete 'hbase:meta','hbase:backup <region name>','<column name>'  
```

- Eliminare la voce di hbase: namespace, in quanto lo stesso errore potrebbe essere segnalato durante l'analisi della tabella hbase: namespace.

- Riavviare Active HMaster dall'interfaccia utente di Ambari per ripristinare lo stato di esecuzione di HBase.  

- Eseguire il comando seguente nella shell di HBase per visualizzare tutte le tabelle offline:

```apache 
hbase hbck -ignorePreCheckPermission -fixAssignments 
```

### <a name="further-reading"></a>Altre informazioni:

[Unable to process HBase table](http://stackoverflow.com/questions/4794092/unable-to-access-hbase-table)


### <a name="error"></a>Errore:

Si verifica il timeout di HMaster con un'eccezione irreversibile come java.io.IOException, che indica il timeout di 300.000 ms in attesa dell'assegnazione della tabella namespace

### <a name="detailed-description"></a>Descrizione dettagliata:

Apparentemente il cliente riscontra questo problema quando molte tabelle e aree non vengono scaricate durante il riavvio dei servizi HMaster. Il riavvio non è riuscito con il messaggio riportato sopra. Non sono stati riscontrati altri errori.  

### <a name="probable-cause"></a>Possibile causa:

Si tratta di un "difetto" noto di HMaster: le attività di avvio generali del cluster possono impiegare molto tempo e HMaster si arresta perché la tabella namespace non è ancora assegnata. Il problema si verifica solo in questo scenario, in cui è presente una grande quantità di dati non scaricati e un timeout di cinque minuti non è sufficiente
  
### <a name="resolution-steps"></a>Procedura per la risoluzione:

- Accedere all'interfaccia utente di Ambari, passare a HBase -> Configs (Configurazioni) e nel file hbase-site.xml personalizzato aggiungere l'impostazione seguente: 

```apache
Key: hbase.master.namespace.init.timeout Value: 2400000  
```

- Riavviare i servizi necessari, in particolare HMaster e possibilmente altri servizi HBase.  



## <a name="what-causes-a-restart-failure-on-a-region-server"></a>Qual è la causa di un errore di riavvio in un server di area?

### <a name="probable-cause"></a>Possibile causa:

Prima di tutto, è possibile prevenire una situazione come questa seguendo alcune procedure consigliate. È consigliabile sospendere l'attività dei carichi di lavoro elevati quando si pianifica il riavvio dei server di area di HBase. Se l'applicazione continua a connettersi ai server di area mentre è in corso l'arresto, l'operazione di riavvio dei server di area verrà rallentata di diversi minuti. Inoltre, è consigliabile per gli utenti scaricare tutte le tabelle seguendo le informazioni contenute in [HDInsight HBase: How to Improve HBase cluster restart time by Flushing tables](https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/) (HDInsight HBase: Come migliorare i tempi di riavvio del cluster HBase scaricando le tabelle) come riferimento.

Se un utente avvia l'operazione di riavvio nei server di area di HBase dall'interfaccia utente di Ambari, noterà immediatamente che i server di area sono stati arrestati, ma non vengono riavviati per un periodo di tempo eccessivo. 

Di seguito ecco cosa avviene in background: 

1. L'agente Ambari invia una richiesta di arresto al server di area.
1. L'agente Ambari attende quindi 30 secondi che il server di area venga arrestato correttamente. 
1. Se l'applicazione del cliente continua a connettersi al server di area, il server non viene arrestato immediatamente e di conseguenza il timeout di 30 secondi scade prima. 
1. Dopo il termine di 30 secondi, l'agente Ambari invia un comando force kill (kill -9) al server di area. È possibile osservare questa operazione nel file ambari-agent.log (in /var/log/directory del rispettivo nodo di lavoro), mostrato di seguito:

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
A causa di questo arresto improvviso, la porta associata al processo potrebbe non essere rilasciata, anche quando il processo del server di area viene terminato. Questa situazione può generare l'eccezione AddressBindException mostrata nei log di seguito durante l'avvio del server di area. È possibile verificare questa operazione nel file region-server.log in /var/log/directory hbase dei nodi di lavoro in cui l'avvio del server di area non riesce. 

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

### <a name="resolution-steps"></a>Procedura per la risoluzione:

In questi casi, è possibile provare la soluzione alternativa seguente: 

- Provare a ridurre il carico sui server di area di HBase prima di procedere a un riavvio. 

- In alternativa, se il passaggio precedente non è stato utile, provare a riavviare manualmente i server di area nei nodi di lavoro con i comandi seguenti:

```apache
      sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
      sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"   
```



