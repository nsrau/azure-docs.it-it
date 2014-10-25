<properties title="HDInsight Release Notes" pageTitle="HDInsight Release Notes | Azure" description="HDInsight release notes." metaKeywords="hdinsight, hadoop, hdinsight hadoop, hadoop azure, release notes" services="HDInsight" solutions="" documentationCenter="" editor="cgronlun" manager="paulettm"  authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev"></tags>

# Note sulla versione di Microsoft HDInsight

## Note per la versione rilasciata il 21/8/2014

-   È stata aggiunta la nuova configurazione WebHCat (HIVE-7155) seguente, che imposta il limita di memoria predefinito per un processo controller Templeton a 1 GB (il valore predefinito precedente era 512 MB):

    -   templeton.mapper.memory.mb (=1024)
    -   Questa modifica risolve l'errore seguente che si verificava in alcune query Hive a causa dei limiti di memoria più bassi: "Container is running beyond physical memory limits".
    -   Per tornare alle impostazioni predefinite precedenti, è possibile impostare questo valore di configurazione su 512 mediante PowerShell SDK al momento della creazione del cluster usando il comando seguente:

            Add-AzureHDInsightConfigValues -Core @{"templeton.mapper.memory.mb"="512";}

-   Il nome host del ruolo zookeeper è stato modificato in zookeeper. Questo influisce sulla risoluzione dei nomi nel cluster, ma non sulle API REST esterne. Se sono presenti componenti che usano il nome host zookeepernode sarà necessario aggiornarli in modo che usino il nuovo nome. I nuovi nomi per i tre nodi zookeeper sono:

    -   zookeeper0
    -   zookeeper1
    -   zookeeper2
-   La matrice di supporto delle versioni di HBase è stata aggiornata. Per i carichi di lavoro HBase di produzione è supportato solo HDInsight 3.1 (HBase versione 0.98). In futuro la versione 3.0 disponibile in anteprima non sarà supportata. Durante il periodo di transizione, i clienti potranno comunque creare cluster della versione 3.0.

## Note per la versione rilasciata il 28/7/2014

-   **Disponibilità di HDInsight in nuove aree geografiche**: con questa versione la presenza geografica di HDInsight è stata estesa a tre nuove aree. I clienti di HDInsight possono ora creare cluster in queste aree.
    -   Asia orientale
    -   Stati Uniti centro-settentrionali e
    -   Stati Uniti centro-meridionali.


-   In questa versione, 
HDInsight v1.6 (HDP1.1, Hadoop 1.0.3) e HDInsight v2.1 (HDP1.3, Hadoop 1.2) verranno rimossi dal portale di gestione di Azure. È possibile continuare a creare cluster Hadoop per queste versioni usando i cmdlet di HDInsight PowerShell ([New-AzureHDInsightCluster][New-AzureHDInsightCluster]) oppure usando [HDInsight SDK][HDInsight SDK]. Per altre informazioni, fare riferimento alla pagina relativa al [controllo delle versioni del componente HDInsight][controllo delle versioni del componente HDInsight].
-   Modifiche a Hortonworks Data Platform (HDP) in questa versione:

| HDP             | Modifiche                                                    |
|-----------------|--------------------------------------------------------------|
| HDP 1.3/HDI 2.1 | Nessuna modifica                                             |
| HDP 2.0/HDI 3.0 | Nessuna modifica                                             |
| HDP 2.1/HDI 3.1 | zookeeper: ['3.4.5.2.1.3.0-1948'] -\> ['3.4.5.2.1.3.2-0002'] |

## Note per la versione rilasciata il 24/6/2014

In questa versione sono disponibili numerosi miglioramenti al servizio HDInsight:

-   **Disponibilità di HDP 2.1**: HDInsight 3.1, che contiene HDP 2.1, è ora disponibile a livello generale e rappresenta la versione predefinita per i nuovi cluster.
-   **HBase - Miglioramenti nel portale di gestione di Azure**: I cluster HBase sono disponibili in anteprima. Ora è possibile creare cluster HBase dal portale con 3 clic.

![][]

HBase consente di creare numerosi carichi di lavoro in tempo reale su HDInsight, da siti Web interattivi da usare con grandi set di dati a servizi che archiviano dati di sensori e telemetria da milioni di endpoint. Il passaggio successivo consiste nell'analizzare i dati di questi carichi di lavoro tramite processi Hadoop e questo è immediatamente possibile in HDInsight mediante le esperienze disponibili, ad esempio PowerShell e il dashboard del cluster Hive.

### Apache Mahout è ora preinstallato in HDInsight 3.1

[Mahout][Mahout] è preinstallato nei cluster Hadoop di HDInsight 3.1. In questo modo, è possibile eseguire processi Mahout senza dover configurare ulteriormente i cluster. Ad esempio, è possibile accedere in remoto a un cluster Hadoop usando il protocollo RDP (Remote Desktop Protocol) e senza ulteriori passaggi eseguire il comando di Mahout Hello world:

        mahout org.apache.mahout.classifier.df.tools.Describe -p /user/hdp/glass.data -f /user/hdp/glass.info -d I 9 N L  

        mahout org.apache.mahout.classifier.df.BreimanExample -d /user/hdp/glass.data -ds /user/hdp/glass.info -i 10 -t 100

Per una spiegazione completa della procedura, vedere la documentazione relativa all'[esempio Breiman][esempio Breiman] nel sito Web di Apache Mahout.

### Le query Hive possono usare Tez in HDinsight 3.1

Hive 0.13 è ora disponibile in HDInsight 3.1 e può eseguire query mediante Tez, consentendo notevoli miglioramenti delle prestazioni.
Tez non è abilitato per impostazione predefinita per le query Hive. Per usarlo, è necessario acconsentire esplicitamente. È possibile abilitare Tez eseguendo il frammento di codice seguente:

        set hive.execution.engine=tez;
        select sc_status, count(*), histogram_numeric(sc_bytes,5) from website_logs_orc_local group by sc_status;

Hortonworks ha pubblicato un elenco dettagliato dei miglioramenti delle prestazioni delle query Hive con Tez risultanti dall'esecuzione di benchmark standard. Per informazioni dettagliate, vedere l'articolo relativo al [benchmarking di Apache Hive 13 per la soluzione Hadoop di livello enterprise][benchmarking di Apache Hive 13 per la soluzione Hadoop di livello enterprise].

Per altri dettagli ullsull'uso di Hive con Tez, vedere la [pagina Wiki relativa a Hive su Tez][pagina Wiki relativa a Hive su Tez].

### Disponibilità globale

Con il rilascio di Azure HDInsight in Hadoop 2.2, Microsoft ha reso HDInsight disponibile in tutte le principali aree geografiche di Azure. In particolare, sono stati collegati i data center dell'Europa occidentale e dell'Asia sudorientale. Ciò consente ai clienti di individuare i cluster in un data center chiuso e potenzialmente situato in una zona con analoghi requisiti di conformità.

### Modifiche di rilievo

**Sintassi del prefisso**:
nei cluster HDInsight 3.0 e 3.1 è supportata solo la sintassi "wasb://". La sintassi asv:// precedente è supportata nei cluster HDInsight 2.1 e 1.6, ma non è supportata nei cluster HDInsight 3.0 e non sarà supportata nelle versioni successive. Ciò vuol dire che i processi inviati a un cluster HDInsight 3.0 o 3.1 che usano esplicitamente la sintassi "asv://" avranno esito negativo. Sarà pertanto necessario usare la sintassi wasb://. Avrà inoltre esito negativo qualsiasi processo inviato a un cluster HDInsight 3.0 o 3.1 e creato con un metastore esistente che contiene riferimenti espliciti a risorse che usano la sintassi asv://. Sarà necessario ricreare i metastore in modo da fare riferimento alle risorse usando la sintassi wasb://.

**Porte**: Le porte usate dal servizio HDInsight sono state cambiate. I numeri di porta usati in precedenza erano compresi nell'intervallo di porte temporanee del sistema operativo Windows. Per le comunicazioni di breve durata basate su IP, le porte vengono allocate automaticamente da un intervallo di porte temporanee predefinito. Il nuovo set di numeri di porta per il servizio Hortonworks Data Platform (HDP) sono ora esterne a questo intervallo, per evitare conflitti con le porte usate dai servizi in esecuzione sul nodo head. I nuovi numeri di porta non dovrebbero provocare modifiche di rilievo. I nuovi numeri in uso sono i seguenti:

**HDInsight 1.6 (HDP 1.1)**

<table border="1">

<tr>
<th>
Nome

</th>
<th>
Valore

</th>
</tr>

<tr>
<td>
dfs.http.address

</td>
<td>
namenodehost:30070

</td>
</tr>

<tr>
<td>
dfs.datanode.address

</td>
<td>
0.0.0.0:30010

</td>
</tr>

<tr>
<td>
dfs.datanode.http.address

</td>
<td>
0.0.0.0:30075

</td>
</tr>

<tr>
<td>
dfs.datanode.ipc.address

</td>
<td>
0.0.0.0:30020

</td>
</tr>

<tr>
<td>
dfs.secondary.http.address

</td>
<td>
0.0.0.0:30090

</td>
</tr>

<tr>
<td>
mapred.job.tracker.http.address

</td>
<td>
jobtrackerhost:30030

</td>
</tr>

<tr>
<td>
mapred.task.tracker.http.address

</td>
<td>
0.0.0.0:30060

</td>
</tr>

<tr>
<td>
mapreduce.history.server.http.address

</td>
<td>
0.0.0.0:31111

</td>
</tr>

<tr>
<td>
templeton.port

</td>
<td>
30111

</td>
</tr>

</table>

</p>
**HDInsight 3.0 e 3.1 (HDP 2.0 e 2.1)**

<table border="1">

<tr>
<th>
Nome

</th>
<th>
Valore

</th>
</tr>

<tr>
<td>
dfs.namenode.http-address

</td>
<td>
namenodehost:30070

</td>
</tr>

<tr>
<td>
dfs.namenode.https-address

</td>
<td>
headnodehost:30470

</td>
</tr>

<tr>
<td>
dfs.datanode.address

</td>
<td>
0.0.0.0:30010

</td>
</tr>

<tr>
<td>
dfs.datanode.http.address

</td>
<td>
0.0.0.0:30075

</td>
</tr>

<tr>
<td>
dfs.datanode.ipc.address

</td>
<td>
0.0.0.0:30020

</td>
</tr>

<tr>
<td>
dfs.namenode.secondary.http-address

</td>
<td>
0.0.0.0:30090

</td>
</tr>

<tr>
<td>
yarn.nodemanager.webapp.address

</td>
<td>
0.0.0.0:30060

</td>
</tr>

<tr>
<td>
templeton.port

</td>
<td>
30111

</td>
</tr>

</table>

</p>
### Dipendenze

In HDInsight 3.x (HDP2.x) sono state aggiunte le dipendenze seguenti:

-   guice-servlet
-   optiq-core
-   javax.inject
-   activation
-   jsr305
-   geronimo-jaspic\_1.0\_spec
-   jul-to-slf4j
-   java-xmlbuilder
-   ant
-   commons-compiler
-   jdo-api
-   commons-math3
-   paranamer
-   jaxb-impl
-   stringtemplate
-   eigenbase-xom
-   jersey-servlet
-   commons-exec
-   jaxb-api
-   jetty-all-server
-   janino
-   xercesImpl
-   optiq-avatica
-   jta
-   eigenbase-properties
-   groovy-all
-   hamcrest-core
-   mail
-   linq4j
-   jpam
-   jersey-client
-   aopalliance
-   geronimo-annotation\_1.0\_spec
-   ant-launcher
-   jersey-guice
-   xml-apis
-   stax-api
-   asm-commons
-   asm-tree
-   wadl
-   geronimo-jta\_1.1\_spec
-   guice
-   leveldbjni-all
-   velocity
-   jettison
-   snappy-java
-   jetty-all
-   commons-dbcp

Le dipendenze seguenti non esistono più in HDInsight 3.x (HDP2.x):

-   jdeb
-   kfs
-   sqlline
-   ivy
-   aspectjrt
-   json
-   core
-   jdo2-api
-   avro-mapred
-   datanucleus-enhancer
-   jsp
-   commons-logging-api
-   commons-math
-   JavaEWAH
-   aspectjtools
-   javolution
-   hdfsproxy
-   hbase
-   snappy

### Modifiche della versione

Tra HDInsight 2.x (HDP1.x) e HDInsight 3.x (HDP2.x) sono state apportate le seguenti modifiche della versione:

-   metrics-core: ['2.1.2'] -\> ['3.0.0']
-   derbynet: ['10.4.2.0'] -\> ['10.10.1.1']
-   datanucleus: ['rdbms-3.0.8'] -\> ['rdbms-3.2.9']
-   jasper-compiler: ['5.5.12'] -\> ['5.5.23']
-   log4j: ['1.2.15', '1.2.16'] -\> ['1.2.16', '1.2.17']
-   derbyclient: ['10.4.2.0'] -\> ['10.10.1.1']
-   httpcore: ['4.2.4'] -\> ['4.2.5']
-   hsqldb: ['1.8.0.10'] -\> ['2.0.0']
-   jets3t: ['0.6.1'] -\> ['0.9.0']
-   protobuf-java: ['2.4.1'] -\> ['2.5.0']
-   derby: ['10.4.2.0'] -\> ['10.10.1.1']
-   jasper: ['runtime-5.5.12'] -\> ['runtime-5.5.23']
-   commons-daemon: ['1.0.1'] -\> ['1.0.13']
-   datanucleus-core: ['3.0.9'] -\> ['3.2.10']
-   datanucleus-api-jdo: ['3.0.7'] -\> ['3.2.6']
-   zookeeper: ['3.4.5.1.3.9.0-01320'] -\> ['3.4.5.2.1.3.0-1948']
-   bonecp: ['0.7.1.RELEASE'] -\> ['0.8.0.RELEASE']

### Driver

Il driver JDBC per SQL Server viene usato internamente da HDInsight e non viene usato per operazioni esterne. Se si desidera connettersi a HDInsight mediante ODBC, usare Microsoft Hive ODBC Driver. Per altre informazioni sull'uso di Hive ODBC, vedere [Connessione di Excel a HDInsight mediante Microsoft Hive ODBC Driver][connect-excel-with-hive-ODBC].

### Correzioni di bug

In questa versione, le versioni seguenti di HDInsight (Hortonworks Data Platform, HDP) sono state aggiornate con svariate correzioni di bug:

-   HDInsight 2.1 (HDP 1.3)
-   HDInsight 3.0 (HDP 2.0)
-   HDInsight 3.1 (HDP 2.1)

## Note sulla versione di Hortonworks

Le note sulla versione relative alle piattaforme HDP usate dalle versioni del cluster HDInsight sono disponibili nelle pagine seguenti.

-   Il cluster HDInsight versione 3.1 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 2.1][Hortonworks Data Platform 2.1].Si tratta del cluster Hadoop predefinito creato quando si usa il portale di Azure HDInsight.

-   Il cluster HDInsight versione 3.0 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 2.0][Hortonworks Data Platform 2.0].

-   Il cluster HDInsight versione 2.1 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 1.3][Hortonworks Data Platform 1.3].

-   Il cluster HDInsight versione 1.6 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 1.1][Hortonworks Data Platform 1.1].

  [New-AzureHDInsightCluster]: http://msdn.microsoft.com/en-us/library/dn593744.aspx
  [HDInsight SDK]: http://msdn.microsoft.com/en-us/library/azure/dn469975.aspx
  [controllo delle versioni del componente HDInsight]: http://azure.microsoft.com/it-it/documentation/articles/hdinsight-component-versioning/
  []: http://i.imgur.com/cmOl5fM.png
  [Mahout]: http://hortonworks.com/hadoop/mahout/
  [esempio Breiman]: https://mahout.apache.org/users/classification/breiman-example.html
  [benchmarking di Apache Hive 13 per la soluzione Hadoop di livello enterprise]: http://hortonworks.com/blog/benchmarking-apache-hive-13-enterprise-hadoop/
  [pagina Wiki relativa a Hive su Tez]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
  [Hortonworks Data Platform 2.1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html
  [Hortonworks Data Platform 2.0]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html
  [Hortonworks Data Platform 1.3]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html
  [Hortonworks Data Platform 1.1]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html
