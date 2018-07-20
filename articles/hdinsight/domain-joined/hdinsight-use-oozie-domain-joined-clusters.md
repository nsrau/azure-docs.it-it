---
title: Flussi di lavoro Oozie di Hadoop in cluster HDInsight aggiunti a un dominio
description: Usare Oozie di Hadoop in Enterprise Security Package nell'ambiente HDInsight aggiunto a un dominio basato su Linux. Informazioni su come definire un flusso di lavoro Oozie e inviare un processo Oozie.
services: hdinsight
author: omidm1
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d7603471-5076-43d1-8b9a-dbc4e366ce5d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: omidm
ms.openlocfilehash: 928f6adbb348683a110f7da9b20efaae998290ca
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972214"
---
#<a name="run-apache-oozie-in-domain-joined-hdinsight-hadoop-clusters"></a>Eseguire Apache Oozie in cluster Hadoop di HDInsight aggiunti al dominio
Oozie è un sistema di coordinamento dei flussi di lavoro che consente di gestire i processi Hadoop. Oozie è integrato con lo stack Hadoop e supporta i processi seguenti:
- Apache MapReduce
- Apache Pig
- Apache Hive
- Apache Sqoop

Oozie può anche essere usato per pianificare processi specifici di un sistema, come programmi Java o script della shell.

##<a name="prerequisites"></a>Prerequisiti:
- Cluster Hadoop di HDInsight aggiunto a un dominio. Vedere [Configurare cluster HDInsight aggiunti al dominio](./apache-domain-joined-configure-using-azure-adds.md)

    > [!NOTE]
    > Per istruzioni dettagliate sull'uso di Oozie in cluster non aggiunti a un dominio, vedere [qui](../hdinsight-use-oozie-linux-mac.md)

##<a name="connecting-to-domain-joined-cluster"></a>Connessione a un cluster aggiunto a un dominio
Per altre informazioni su SSH, vedere [Autenticazione: HDInsight aggiunto al dominio](../hdinsight-hadoop-linux-use-ssh-unix.md).
- Connettersi al cluster HDInsight usando SSH:
     ```bash
    ssh [DomainUserName]@<clustername>-ssh.azurehdinsight.net
    ```
Per verificare se l'autenticazione Kerberos ha avuto esito positivo, usare il comando `klist`. In caso contrario, usare `kinit` per avviare l'autenticazione Kerberos.

- Accedere al gateway di HDInsight per registrare il token oAuth necessario per l'accesso a Azure Data Lake Store.
     ```bash
     curl -I -u [DomainUserName@Domain.com]:[DomainUserPassword] https://<clustername>.azurehdinsight.net
     ```

    Un codice di risposta con stato _200 OK_ indica la registrazione. Se si riceve una risposta di accesso non autorizzato (401), verificare il nome utente e la password.

## <a name="define-the-workflow"></a>Definire il flusso di lavoro
Le definizioni del flusso di lavoro di Oozie sono scritte in un linguaggio di definizione dei processi XML denominato hPDL (Hadoop Process Definition Language). Usare i passaggi seguenti per definire il flusso di lavoro:

-   Configurazione dell'area di lavoro di un utente di dominio:
 ```bash
hdfs dfs -mkdir /user/<DomainUser>
cd /home/<DomainUserPath>
cp /usr/hdp/<ClusterVersion>/oozie/doc/oozie-examples.tar.gz .
tar -xvf oozie-examples.tar.gz
hdfs dfs -put examples /user/<DomainUser>/
 ```
Sostituire `DomainUser` con il nome utente di dominio. Sostituire `DomainUserPath` con il percorso della directory principale per l'utente di dominio. Sostituire `ClusterVersion` con la versione HDP del cluster.

-   Usare l'istruzione seguente per creare e modificare un nuovo file:
 ```bash
nano workflow.xml
 ```

- All'apertura dell'editor nano, immettere il codice XML seguente come contenuto del file:
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
Sostituire `clustername` con il nome del cluster. 

Per salvare il file, selezionare CTRL+X, immettere `Y` e quindi selezionare **INVIO**.

Il flusso di lavoro è suddiviso in due parti.
*   Sezione delle credenziali: vengono accettate le credenziali usate per autenticare le azioni di Oozie.

    In questo esempio viene usata l'autenticazione per le azioni di Hive. Per altre informazioni, vedere [qui]( https://oozie.apache.org/docs/4.2.0/DG_ActionAuthentication.html).

    Il servizio credenziali consente alle azioni di Oozie di rappresentare l'utente per l'accesso ai servizi Hadoop.

*   Sezione delle azioni: sono presenti tre azioni, ovvero map-reduce, hive server 2 e hive server 1.

    L'azione map-reduce esegue un esempio del pacchetto di Oozie per map-reduce che restituisce il numero di parole aggregate.

    Le azioni hive server 2 e hive server 1 eseguono una semplice query sulla tabella hivesample disponibile in HDInsight.

    Le azioni hive usano le credenziali definite nella sezione relativa per eseguire l'autenticazione usando la parola chiave `cred` nell'elemento azione.

- Usare il comando seguente per copiare il file `workflow.xml` in `/user/<domainuser>/examples/apps/map-reduce/workflow.xml`:
     ```bash
    hdfs dfs -put workflow.xml /user/<domainuser>/examples/apps/map-reduce/workflow.xml
     ```

    Sostituire `domainuser` con il nome utente per il dominio.

## <a name="define-the-properties-file-for-the-oozie-job"></a>Definire il file di proprietà per il processo Oozie

1.  Usare l'istruzione seguente per creare e modificare un nuovo file per le proprietà dei processi:
     ```bash
    nano job.properties
     ```

2.   All'apertura dell'editor nano, usare il codice XML seguente come contenuto del file:

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
    

   * Sostituire `domainuser` con il nome utente per il dominio.
   * Sostituire `ClusterShortName` con un nome breve per il cluster. Se il nome del cluster è https://sechadoopcontoso.azurehdisnight.net, l'elemento `clustershortname` è costituito dalle prime sei lettere per il cluster, ovvero sechad.
   * Sostituire `jdbcurlvalue` con l'URL JDBC dalla configurazione di hive. Esempio: jdbc:hive2://headnodehost:10001/;transportMode=http .
    
   * Per salvare il file, selezionare CTRL+X, immettere `Y` e quindi selezionare **INVIO**.

   * Questo file di proprietà deve essere presente in locale durante l'esecuzione di processi Oozie.

## <a name="creating-custom-hive-scripts-for-the-oozie-job"></a>Creazione di script hive personalizzati per il processo Oozie
I 2 script hive per hive server 1 e hive server 2 possono essere creati nel modo indicato di seguito.
-   File hive server 1:
1.  Usare l'istruzione seguente per creare e modificare un file per l'azione hive server 1:
    ```bash
    nano countrowshive1.hql
    ```

2.  Creare lo script
    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemake from hivesampletable limit 2;
    ```

3.  Salvare il file in Hdfs
    ```bash
    hdfs dfs -put countrowshive1.hql countrowshive1.hql
    ```

-   File hive server 2:
1.  Usare l'istruzione seguente per creare e modificare un campo per l'azione hive server 2:
    ```bash
    nano countrowshive2.hql
    ```

2.  Creare lo script
    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemodel from hivesampletable limit 2;
    ```

3.  Salvare il file in Hdfs
    ```bash
    hdfs dfs -put countrowshive2.hql countrowshive2.hql
    ```

## <a name="submission-of-oozie-jobs"></a>Invio dei processi Oozie
L'invio di processi Oozie per i cluster aggiunti a un dominio è analoga all'invio di processi Oozie in cluster non aggiunti a un dominio.

Per altre informazioni, vedere [Inviare e gestire il processo](../hdinsight-use-oozie-linux-mac.md).

## <a name="results-from-oozie-job-submission"></a>Risultati dell'invio del processo Oozie
Poiché i processi Oozie vengono eseguiti per conto dell'utente, entrambi i log di controllo Yarn e Ranger mostrano i processi in esecuzione come utente rappresentato. L'output della riga di comando del processo Oozie è analogo al seguente:


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

*    I log di controllo Ranger per le azioni hive server 2 mostrano Oozie che esegue l'azione per conto dell'utente. La vista Ranger e Yarn è visibile solo per l'amministratore del cluster.

## <a name="configuration-of-user-authorization-in-oozie"></a>Configurazione dell'autorizzazione utente in Oozie
Oozie dispone di una configurazione di autorizzazione utente autonoma, che può impedire agli utenti l'arresto e la terminazione dei processi di altri utenti. Per abilitare questa opzione, impostare `oozie.service.AuthorizationService.security.enabled` su `true`. 

Per altre informazioni, vedere la sezione della documentazione di Oozie sulla [configurazione dell'autorizzazione utente]( https://oozie.apache.org/docs/3.2.0-incubating/AG_Install.html).

Per componenti come hive server 1 in cui il plug-in Ranger non è disponibile o supportato, è possibile implementare solo un'autorizzazione con granularità grossolana. L'autorizzazione con granularità maggiore è disponibile solo tramite i plug-in Ranger.

## <a name="oozie-web-ui"></a>Interfaccia utente Web di Oozie
L'interfaccia utente Web di Oozie fornisce una visualizzazione basata sul Web dello stato dei processi Oozie nel cluster. In cluster aggiunti a un dominio è necessario eseguire queste operazioni:

1. Aggiungere un [nodo perimetrale](../hdinsight-apps-use-edge-node.md) e abilitare l'[autenticazione Kerberos SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)

2. Seguire i passaggi indicati nell'[interfaccia utente Web di Oozie](../hdinsight-use-oozie-linux-mac.md) per abilitare il tunneling SSH nel nodo perimetrale e accedere all'interfaccia utente Web.

## <a name="next-steps"></a>Passaggi successivi
* [Usare Oozie con Hadoop per definire ed eseguire un flusso di lavoro in Azure HDInsight basato su Linux](../hdinsight-use-oozie-linux-mac.md)
* [Usare il coordinatore Oozie basato sul tempo](../hdinsight-use-oozie-coordinator-time.md)
* [Eseguire query Hive usando SSH nei cluster HDInsight aggiunti a un dominio](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
