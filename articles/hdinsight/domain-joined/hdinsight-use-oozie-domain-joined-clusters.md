---
title: Proteggere i flussi di lavoro di Apache Oozie con Enterprise Security Package - Azure HDInsight
description: Proteggere i flussi di lavoro di Apache Oozie con Enterprise Security Package di Azure HDInsight. Informazioni su come definire un flusso di lavoro Oozie e inviare un processo Oozie.
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: mamccrea
ms.custom: hdinsightactive,seodec18
ms.topic: conceptual
ms.date: 02/15/2019
ms.openlocfilehash: 86cb6f6a18cb799574ae9badc0f02144b3a6e1d7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60535948"
---
# <a name="run-apache-oozie-in-hdinsight-hadoop-clusters-with-enterprise-security-package"></a>Eseguire Apache Oozie nei cluster HDInsight Hadoop con Enterprise Security Package

Apache Oozie è un sistema di coordinamento dei flussi di lavoro che consente di gestire i processi Apache Hadoop. Oozie è integrato con lo stack Hadoop e supporta i processi seguenti:
- Apache MapReduce
- Apache Pig
- Apache Hive
- Apache Sqoop

Oozie può anche essere usato per pianificare processi specifici di un sistema, come programmi Java o script della shell.

## <a name="prerequisite"></a>Prerequisito

- Un cluster Azure HDInsight Hadoop con Enterprise Security Package (ESP). Vedere [Configurare i cluster HDInsight con ESP](./apache-domain-joined-configure-using-azure-adds.md).

    > [!NOTE]  
    > Per istruzioni dettagliate sull'uso di Oozie in cluster non ESP, vedere [Usare Oozie con Hadoop per definire ed eseguire un flusso di lavoro in Azure HDInsight basato su Linux](../hdinsight-use-oozie-linux-mac.md).

## <a name="connect-to-an-esp-cluster"></a>Connettersi a un cluster ESP

Per altre informazioni su SSH (Secure Shell), vedere [Connettersi a HDInsight (Hadoop) con SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Connettersi al cluster HDInsight usando SSH:  
   ```bash
   ssh [DomainUserName]@<clustername>-ssh.azurehdinsight.net
   ```

2. Per verificare se l'autenticazione Kerberos ha avuto esito positivo, usare il comando `klist`. In caso contrario, usare `kinit` per avviare l'autenticazione Kerberos.

3. Accedere al gateway di HDInsight per registrare il token OAuth necessario per accedere ad Azure Data Lake Storage:   
     ```bash
     curl -I -u [DomainUserName@Domain.com]:[DomainUserPassword] https://<clustername>.azurehdinsight.net
     ```

    Un codice di risposta con stato **200 OK** indica la registrazione. Se si riceve una risposta di accesso non autorizzato, ad esempio 401, controllare il nome utente e la password.

## <a name="define-the-workflow"></a>Definire il flusso di lavoro
Le definizioni dei flussi di lavoro di Oozie sono scritte nel linguaggio Apache hPDL (Hadoop Process Definition Language). hPDL è un linguaggio di definizione di processi XML. Seguire questa procedura per definire il flusso di lavoro:

1. Configurare l'area di lavoro dell'utente del dominio:
   ```bash
   hdfs dfs -mkdir /user/<DomainUser>
   cd /home/<DomainUserPath>
   cp /usr/hdp/<ClusterVersion>/oozie/doc/oozie-examples.tar.gz .
   tar -xvf oozie-examples.tar.gz
   hdfs dfs -put examples /user/<DomainUser>/
   ```
   Sostituire `DomainUser` con il nome utente di dominio. 
   Sostituire `DomainUserPath` con il percorso della directory principale per l'utente di dominio. 
   Sostituire `ClusterVersion` con la versione HDP (Hortonworks Data Platform) del cluster in uso.

2. Usare l'istruzione seguente per creare e modificare un nuovo file:
   ```bash
   nano workflow.xml
   ```

3. All'apertura dell'editor nano, immettere il codice XML seguente come contenuto del file:
   ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <workflow-app xmlns="uri:oozie:workflow:0.4" name="map-reduce-wf">
       <credentials>
          <credential name="metastore_token" type="hcat">
             <property>
                <name>hcat.metastore.uri</name>
                <value>thrift://hn0-<clustername>.<Domain>.com:9083</value>
             </property>
             <property>
                <name>hcat.metastore.principal</name>
                <value>hive/_HOST@<Domain>.COM</value>
             </property>
          </credential>
          <credential name="hs2-creds" type="hive2">
             <property>
                <name>hive2.server.principal</name>
                <value>${jdbcPrincipal}</value>
             </property>
             <property>
                <name>hive2.jdbc.url</name>
                <value>${jdbcURL}</value>
             </property>
          </credential>
       </credentials>
       <start to="mr-test" />
       <action name="mr-test">
          <map-reduce>
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <prepare>
                <delete path="${nameNode}/user/${wf:user()}/examples/output-data/mrresult" />
             </prepare>
             <configuration>
                <property>
                   <name>mapred.job.queue.name</name>
                   <value>${queueName}</value>
                </property>
                <property>
                   <name>mapred.mapper.class</name>
                   <value>org.apache.oozie.example.SampleMapper</value>
                </property>
                <property>
                   <name>mapred.reducer.class</name>
                   <value>org.apache.oozie.example.SampleReducer</value>
                </property>
                <property>
                   <name>mapred.map.tasks</name>
                   <value>1</value>
                </property>
                <property>
                   <name>mapred.input.dir</name>
                   <value>/user/${wf:user()}/${examplesRoot}/input-data/text</value>
                </property>
                <property>
                   <name>mapred.output.dir</name>
                   <value>/user/${wf:user()}/${examplesRoot}/output-data/mrresult</value>
                </property>
             </configuration>
          </map-reduce>
          <ok to="myHive2" />
          <error to="fail" />
       </action>
       <action name="myHive2" cred="hs2-creds">
          <hive2 xmlns="uri:oozie:hive2-action:0.2">
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <jdbc-url>${jdbcURL}</jdbc-url>
             <script>${hiveScript2}</script>
             <param>hiveOutputDirectory2=${hiveOutputDirectory2}</param>
          </hive2>
          <ok to="myHive" />
          <error to="fail" />
       </action>
       <action name="myHive" cred="metastore_token">
          <hive xmlns="uri:oozie:hive-action:0.2">
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <configuration>
                <property>
                   <name>mapred.job.queue.name</name>
                   <value>${queueName}</value>
                </property>
             </configuration>
             <script>${hiveScript1}</script>
             <param>hiveOutputDirectory1=${hiveOutputDirectory1}</param>
          </hive>
          <ok to="end" />
          <error to="fail" />
       </action>
       <kill name="fail">
          <message>Oozie job failed, error message[${wf:errorMessage(wf:lastErrorNode())}]</message>
       </kill>
       <end name="end" />
    </workflow-app>
   ```
4. Sostituire `clustername` con il nome del cluster. 

5. Per salvare il file, selezionare CTRL+X. Immettere `Y`. Selezionare quindi **INVIO**.

    Il flusso di lavoro è suddiviso in due parti.
   * **Sezione relativa alle credenziali.** Questa sezione include le credenziali usate per l'autenticazione delle azioni di Oozie:

     Questo esempio usa l'autenticazione per le azioni di Hive. Per altre informazioni, vedere [Action Authentication](https://oozie.apache.org/docs/4.2.0/DG_ActionAuthentication.html) (Autenticazione delle azioni).

     Il servizio credenziali consente alle azioni di Oozie di rappresentare l'utente per l'accesso ai servizi Hadoop.

   * **Sezione relativa alle azioni.** Questa sezione include tre azioni, ovvero map-reduce, Hive server 2 e Hive server 1:

     - L'azione map-reduce esegue un esempio da un pacchetto Oozie per map-reduce che restituisce il numero di parole aggregate.

     - Le azioni Hive server 2 e Hive server 1 eseguono una query su una tabella Hive di esempio fornita con HDInsight.

     Le azioni Hive usano le credenziali definite nella sezione relativa alle credenziali per l'autenticazione usando la parola chiave `cred` nell'elemento action.

6. Usare il comando seguente per copiare il file `workflow.xml` in `/user/<domainuser>/examples/apps/map-reduce/workflow.xml`:
     ```bash
    hdfs dfs -put workflow.xml /user/<domainuser>/examples/apps/map-reduce/workflow.xml
     ```

7. Sostituire `domainuser` con il nome utente per il dominio.

## <a name="define-the-properties-file-for-the-oozie-job"></a>Definire il file di proprietà per il processo Oozie

1. Usare l'istruzione seguente per creare e modificare un nuovo file per le proprietà dei processi:

   ```bash
   nano job.properties
   ```

2. All'apertura dell'editor nano, usare il codice XML seguente come contenuto del file:

   ```bash
       nameNode=adl://home
       jobTracker=headnodehost:8050
       queueName=default
       examplesRoot=examples
       oozie.wf.application.path=${nameNode}/user/[domainuser]/examples/apps/map-reduce/workflow.xml
       hiveScript1=${nameNode}/user/${user.name}/countrowshive1.hql
       hiveScript2=${nameNode}/user/${user.name}/countrowshive2.hql
       oozie.use.system.libpath=true
       user.name=[domainuser]
       jdbcPrincipal=hive/hn0-<ClusterShortName>.<Domain>.com@<Domain>.COM
       jdbcURL=[jdbcurlvalue]
       hiveOutputDirectory1=${nameNode}/user/${user.name}/hiveresult1
       hiveOutputDirectory2=${nameNode}/user/${user.name}/hiveresult2
   ```

   * Usare l'URI `adl://home` per la proprietà `nameNode` se Azure Data Lake Storage Gen1 è la risorsa di archiviazione del cluster principale. Se si usa Archiviazione BLOB di Azure, cambiare l'URI in `wasb://home`. Se si usa Azure Data Lake Storage Gen2, cambiare l'URI in `abfs://home`.
   * Sostituire `domainuser` con il nome utente per il dominio.  
   * Sostituire `ClusterShortName` con il nome breve del cluster. Se ad esempio il nome del cluster è https:// *[collegamento di esempio]* sechadoopcontoso.azurehdisnight.net, il valore `clustershortname` corrisponde ai primi sei caratteri del cluster: **sechad**.  
   * Sostituire `jdbcurlvalue` con l'URL JDBC dalla configurazione di Hive. Ad esempio: jdbc:hive2://headnodehost:10001/;transportMode=http.      
   * Per salvare il file, selezionare CTRL+X, immettere `Y` e quindi selezionare **INVIO**.

   Questo file di proprietà deve essere presente in locale durante l'esecuzione di processi Oozie.

## <a name="create-custom-hive-scripts-for-oozie-jobs"></a>Creare script di Hive personalizzati per i processi Oozie

È possibile creare i due script di Hive per Hive server 1 e Hive server 2 come illustrato nelle sezioni seguenti.

### <a name="hive-server-1-file"></a>File Hive server 1

1.  Creare e modificare un file per le azioni Hive server 1:
    ```bash
    nano countrowshive1.hql
    ```

2.  Creare lo script:
    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemake from hivesampletable limit 2;
    ```

3.  Salvare il file in Apache Hadoop Distributed File System (HDFS):
    ```bash
    hdfs dfs -put countrowshive1.hql countrowshive1.hql
    ```

### <a name="hive-server-2-file"></a>File Hive server 2

1.  Creare e modificare un file per le azioni Hive server 2:
    ```bash
    nano countrowshive2.hql
    ```

2.  Creare lo script:
    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemodel from hivesampletable limit 2;
    ```

3.  Salvare il file in HDFS:
    ```bash
    hdfs dfs -put countrowshive2.hql countrowshive2.hql
    ```

## <a name="submit-oozie-jobs"></a>Inviare processi Oozie

L'invio di processi Oozie per cluster ESP è analogo all'invio di processi Oozie in cluster non ESP.

Per altre informazioni, vedere [Usare Oozie con Hadoop per definire ed eseguire un flusso di lavoro in Azure HDInsight basato su Linux](../hdinsight-use-oozie-linux-mac.md).

## <a name="results-from-an-oozie-job-submission"></a>Risultati dell'invio di un processo Oozie
I processi Oozie vengono eseguiti per l'utente. I log di controllo di Apache Hadoop YARN e Apache Ranger mostrano quindi che i processi vengono eseguiti come utente rappresentato. L'output dell'interfaccia della riga di comando di un processo Oozie è analogo al codice seguente:



```bash
    Job ID : 0000015-180626011240801-oozie-oozi-W
    ------------------------------------------------------------------------------------------------
    Workflow Name : map-reduce-wf
    App Path      : adl://home/user/alicetest/examples/apps/map-reduce/wf.xml
    Status        : SUCCEEDED
    Run           : 0
    User          : alicetest
    Group         : -
    Created       : 2018-06-26 19:25 GMT
    Started       : 2018-06-26 19:25 GMT
    Last Modified : 2018-06-26 19:30 GMT
    Ended         : 2018-06-26 19:30 GMT
    CoordAction ID: -
    
    Actions
    ------------------------------------------------------------------------------------------------
    ID                      Status  Ext ID          ExtStatus   ErrCode
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@:start:    OK  -           OK      -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@mr-test    OK  job_1529975666160_0051  SUCCEEDED   -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@myHive2    OK  job_1529975666160_0053  SUCCEEDED   -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@myHive OK  job_1529975666160_0055  SUCCEEDED   -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@end    OK  -           OK      -
    -----------------------------------------------------------------------------------------------
```

I log di controllo di Ranger per le azioni Hive server 2 mostrano Oozie che esegue l'azione per l'utente. Le visualizzazioni Ranger e YARN risultano visibili solo all'amministratore del cluster.

## <a name="configure-user-authorization-in-oozie"></a>Configurare l'autorizzazione utente in Oozie

Oozie include una configurazione di autorizzazione utente specifica che può impedire agli utenti di arrestare o eliminare i processi di altri utenti. Per abilitare questa configurazione, impostare `oozie.service.AuthorizationService.security.enabled` su `true`. 

Per altre informazioni, vedere [Apache Oozie Installation and Configuration](https://oozie.apache.org/docs/3.2.0-incubating/AG_Install.html) (Installazione e configurazione di Apache Oozie).

Per componenti come Hive server 1 in cui il plug-in Ranger non è disponibile o non è supportato, è possibile usare solo l'autorizzazione HDFS con granularità grossolana. L'autorizzazione con granularità fine è disponibile solo tramite i plug-in Ranger.

## <a name="get-the-oozie-web-ui"></a>Ottenere l'interfaccia utente Web di Oozie

L'interfaccia utente Web di Oozie fornisce una visualizzazione basata sul Web dello stato dei processi Oozie nel cluster. Per ottenere l'interfaccia utente Web, procedere come segue nei cluster ESP:

1. Aggiungere un [nodo perimetrale](../hdinsight-apps-use-edge-node.md) e abilitare l'[autenticazione Kerberos SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Seguire i passaggi indicati nell'[interfaccia utente Web di Oozie](../hdinsight-use-oozie-linux-mac.md) per abilitare il tunneling SSH nel nodo perimetrale e accedere all'interfaccia utente Web.

## <a name="next-steps"></a>Passaggi successivi
* [Usare Apache Oozie con Apache Hadoop per definire ed eseguire un flusso di lavoro in Azure HDInsight basato su Linux](../hdinsight-use-oozie-linux-mac.md).
* [Connettersi a HDInsight (Hadoop) con SSH](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
