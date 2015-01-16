<properties title="HDInsight Release Notes" pageTitle="Note sulla versione di HDInsight | Azure" description="Note sulla versione di HDInsight." metaKeywords="hdinsight, hadoop, hdinsight hadoop, hadoop azure, release notes" services="HDInsight" solutions="" documentationCenter="" editor="cgronlun" manager="paulettm"  authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/05/2014" ms.author="bradsev" />


#Note sulla versione di Microsoft HDInsight

## Note per la versione di HDinsight rilasciata il 05/12/2014 ##

I numeri di versione completi per i cluster HDInsight distribuiti con questa versione sono i seguenti:

* HDInsight 	2.1.9.406.1221105	(HDP 1.3.9.0-01351 )
* HDInsight 	3.0.5.406.1221105	(HDP 2.0.9.0-2097)
* HDInsight 	3.1.1.406.1221105	(HDP 2.1.9.0-2196)
* HDInsight SDK N/D

Questa versione include gli aggiornamenti seguenti dei componenti.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Componente</th>
<th>Tipo di cluster</th>
<th>JIRA (se applicabile)</th>
</tr>

<tr>
<td>Correzione di bug: errore intermittente durante l'aggiunta di un numero elevato di partizioni a una tabella in un file DLL di Hive. </td>
<td><p>Se si verifica un errore di connessione intermittente con il database metastore di Hive durante l'aggiunta di molte partizioni a una tabella di Hive, è possibile che si verifichi un errore del file DLL di Hive. Se si verifica questo errore, nel log degli errori di Hive verrà visualizzata l'istruzione seguente: </p><p>"ERROR [main]: ql.Driver (SessionState.java:printError(547)) - FAILED: Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.DDLTask. MetaException(message:java.lang.RuntimeException: commitTransaction was called but openTransactionCalls = 0. This probably indicates that there are unbalanced calls to openTransaction/commitTransaction)"</p></td>
<td>Hive</td>
<td>Hadoop, Hbase</td>
<td>HIVE-482 (Si tratta di JIRA interno, non può essere indicato esternamente. Specificato qui come riferimento).</td>
</tr>

<tr>
<td>Correzione di bug: interruzione occasionale nella console di query di HDInsight</td>
<td>Quando si verifica questo problema, nel log WebHCat verrà visualizzata l'istruzione seguente per il processo di avvio di WebHCat: <p>"org.apache.hive.hcatalog.templeton.CatchallExceptionMapper | org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.yarn.exceptions.YarnRuntimeException): Could not load history file {wasb url to the history file}"</p></td>
<td>WebHCat</td>
<td>Hadoop</td>
<td>HIVE-482 (Si tratta di JIRA interno, non può essere indicato esternamente. Specificato qui come riferimento).</td>
</tr>

<tr>
<td>Correzione di bug: picco occasionale della latenza delle query Hbase</td>
<td>Se si verifica questo problema, gli utenti noteranno un picco occasionale di 3 secondi nella latenza delle query Hbase. </td>
<td>Gateway del cluster HDInsight</td>
<td>HBase</td>
<td>N/D</td>
</tr>

<tr>
<td>Modifiche al nome file Jar HDP</td>
<td>Per i cluster HDI versione 3.0 sono state apportate alcune modifiche ai file jar interni installati da HDP. jetty-6.1.26.jar
 è stato sostituito con jetty-6.1.26.hwx.jar. jetty-util-6.1.26.jar è stato sostituito con jetty-util-6.1.26.hwx.jar. Queste modifiche sono applicabili ai progetti Hadoop, Mahout, WebHCat e Oozie.**</td>
<td>Hadoop, Mahout, WebHCat, Oozie</td>
<td>Hadoop, HBase</td>
<td>N/D</td>
</tr>

</table>
<br>


## Note per la versione di HDinsight rilasciata il 21/11/2014 ##

I numeri di versione completi per i cluster HDInsight distribuiti con questa versione sono i seguenti:

* HDInsight 2.1.9.382.1169709 (nessuna modifica rispetto al 14/11/2014)
* HDInsight 3.0.5.382.1169709 (nessuna modifica rispetto al 14/11/2014)
* HDInsight 3.1.1.382.1169709 (nessuna modifica rispetto al 14/11/2014)
* HDINsight SDK 1.4.0

Questa versione include gli aggiornamenti seguenti dei componenti.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Componente</th>
<th>Tipo di cluster</th>
<th>JIRA (se applicabile)</th>
</tr>

<tr>
<td>Accesso ai registri applicazioni</td>
<td>Possibilità di enumerare a livello di codice le applicazioni eseguite nei cluster e di scaricare i registri rilevanti relativi ad applicazioni o ai contenitori per semplificare il debug di applicazioni che presentano problemi.</td>
<td>SDK</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>

<tr>
<td>Possibilità di specificare un nome di area geografica in IHdInsightClient.DeleteCluster </td>
<td>Azure HDInsight SDK permette ora di specificare un nome di area quando si usa **DeleteCluster**. Ciò permette di sbloccare i clienti che hanno due risorse con lo stesso nome in aree geografiche diverse e non riescono a eliminarle.</td>
<td>SDK</td>
<td>Tutti</td>
<td>N/D</td>
</tr>

<tr>
<td>ClusterDetails.DeploymentId</td>
<td>L'oggetto **ClusterDetails** restituisce ora un campo **DeploymentID** che rappresenta un identificatore univoco del cluster. L'univocità è garantita in tutti i tentativi di creazione di cluster con gli stessi nomi.</td>
<td>SDK</td>
<td>Tutti</td>
<td>N/D</td>
</tr>
</table>
<br>

## Note per la versione di HDInsight ## rilasciata il 14/11/2014

I numeri di versione completi per i cluster HDInsight distribuiti con questa versione sono i seguenti:

* HDInsight 2.1.9.382.1169709
* HDInsight 3.0.5.382.1169709
* HDInsight 3.1.1.382.1169709

Questa versione include le nuove funzionalità, gli aggiornamenti di componenti e le correzioni di bug seguenti.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Componente</th>
<th>Tipo di cluster</th>
<th>JIRA (se applicabile)</th>
</tr>

<tr>
<td>Azione di script (anteprima)</td>
<td>Anteprima della funzionalità di personalizzazione dei cluster, che permette la modifica di cluster Hadoop in modo arbitrario tramite script personalizzati. Grazie a questa nuova funzionalità, gli utenti possono provare a usare e distribuire nei cluster HDInsight in Azure i progetti disponibili nell'ecosistema Apache Hadoop. Questa funzionalità di personalizzazione è disponibile in tutti i tipi di cluster HDInsight, inclusi Hadoop, HBase e Storm.</td>
<td>Nuova funzionalità</td>
<td>Tutti</td>
<td>N/D</td>
</tr>

<tr>
<td>Processi predefiniti per l'analisi dei log relativi a Siti web e Archiviazione di Azure</td>
<td>La console di query di HDInsight include una raccolta introduttiva che supporta soluzioni da usare sui dati dell'utente o su dati di esempio.
<p>Soluzioni utilizzabili sui dati dell'utente:<br>
Sono stati creati processi per alcuni degli scenari più comuni di analisi dei dati, per offrire un punto di partenza per la creazione di soluzioni personalizzate. È possibile usare i propri dati insieme al processo per verificarne il funzionamento. Quando si è pronti, sarà quindi possibile usare le competenze acquisite per creare una soluzione personalizzata, modellata sul processo predefinito.</p>
<p>Soluzioni utilizzabili sui dati di esempio:<br>
Per imparare a usare HDInsight, esaminare nel dettaglio alcuni scenari di base, ad esempio l'analisi di log Web e di dati di sensori. Si imparerà a usare HDInsight per analizzare questi dati e come connettere altre applicazioni e servizi a questi dati. La visualizzazione dei dati tramite la connessione a Microsoft Excel offre un esempio delle potenzialità di questo approccio.</p></td>
<td>Console query</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>

<tr>
<td>Correzione per la perdita di memoria in Templeton</td>
<td>È stata risolta la perdita di memoria in Templeton che ha interessato i clienti con cluster con esecuzioni di lunga durata o che inviavano centinaia di richieste di processi al secondo. Questo problema si presentava sotto forma di errori di tipo 5xx in Templeton e come soluzione alternativa veniva suggerito di riavviare il servizio. Questa soluzione alternativa non è più necessaria.</td>
<td>Templeton</td>
<td>Tutti</td>
<td>https://issues.apache.org/jira/browse/HADOOP-11248</td>
</tr>
</table>
<br>


Nota: per illustrare le nuove funzionalità rese disponibili dalla personalizzazione dei cluster, sono state documentate le procedure che usano azioni di script per installare moduli Spark e R in un cluster. Per altre informazioni, vedere:

* [Installare e usare Spark 1.0 in cluster HDInsight][hdinsight-install-spark]
* [Installare e usare R in cluster Hadoop HDInsight][hdinsight-r-scripts]




## Note per la versione di HDInsight ## rilasciata il 07/11/2014

I numeri di versione completi per i cluster HDInsight distribuiti con questa versione sono i seguenti:

* HDInsight 2.1	2.1.9.374.1153876
* HDInsight 3.0	3.0.5.374.1153876
* HDInsight 3.1	3.1.1.374.1153876

Questa versione include gli aggiornamenti seguenti dei componenti.

<table border="1">
<tr>
<th>Titolo</th>
<th>Descrizione</th>
<th>Componente</th>
<th>Tipo di cluster</th>
<th>JIRA (se applicabile)</th>
</tr>

<tr>
<td>HDP 2.1.7</td>
<td>Questa versione è basata su Hortonworks Data Platform (HDP) 2.1.7. Le note sulla versione per HDP 2.1.7 sono disponibili nel sito Web Hortonworks all'indirizzo http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html.</td>
<td>HDP</td>
<td>Tutti</td>
<td>N/D</td>
</tr>

<tr>
<td>YARN Timeline Server</td>
<td>YARN Timeline Server (noto anche come Generic Application History Server) è stato abilitato per impostazione predefinita. Timeline Server fornisce informazioni generiche sulle applicazioni complete, ad esempio l'ID, il nome, lo stato, l'ora di invio e l'ora di completamento dell'applicazione. <p>Queste informazioni sull'applicazione possono essere recuperate dal nodo head tramite l'accesso all'URI http://headnodehost:8188 oppure tramite l'esecuzione del comando YARN seguente: yarn application -list -appStates ALL.</p> 
<p>È anche possibile recuperare in modo remoto queste applicazioni tramite l'API REST all'indirizzo https://{ClusterDnsName}. azurehdinsight.net/ws/v1/applicationhistory/.</p> 
<p>Per informazioni dettagliate su Timeline Server, vedere la pagina Web all'indirizzo http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html.</p></td>
<td>Servizio, YARN</td>
<td>Hadoop, HBase</td>
<td>N/D</td>
</tr>

<tr>
<td>ID di distribuzione cluster</td>
<td>A partire dalla versione più recente dell'SDK, 1.3.3.1.5426.29232, gli utenti possono accedere agli ID univoci emessi da HDInsight per ogni cluster. Ciò permetterà ai clienti di individuare istanze univoche dei cluster quando si riutilizza un nome DNS in scenari di creazione o eliminazione.</td>
<td>SDK</td>
<td>Tutti</td>
<td>N/D</td>
</tr>
</table>
<br>

* Si noti che il bug che impediva la visualizzazione della versione completa # nel portale o la restituzione da parte dell'SDK o da PowerShell è stato risolto in questa versione. 

## Note per la versione rilasciata il 15/10/2014 ##

Questa versione dell'hotfix ha risolto una perdita di memoria in Templeton che influiva sugli utenti che ne fanno ampio uso. In alcuni casi, tali utenti visualizzavano errori che si manifestavano come codici di errore 500 perché le richieste non avevano memoria sufficiente per l'esecuzione. La soluzione alternativa per questo problema consisteva nel riavviare il servizio Templeton. Il problema è ora stato risolto.


## Note per la versione rilasciata il 07/10/2014 ##

* Quando si usa l'endpoint Ambari, "https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}", il campo *host_name* ora restituisce il nome di dominio completo (FQDN) del nodo invece del solo nome host. Ad esempio, invece di restituire "**headnode0**", si otterrà il nome FQDN "**headnode0.{ClusterDNS}.azurehdinsight.net**". Questa modifica si è resa necessaria per facilitare scenari in cui sia possibile implementare più tipi di cluster, come HBase e Hadoop, in un'unica rete virtuale (VNET). Ciò accade, ad esempio, quando si usa HBase come piattaforma back-end per Hadoop.

* Sono state fornite nuove impostazioni di memoria per l'implementazione predefinita del cluster HDInsight. Le precedenti impostazioni di memoria predefinite non tenevano conto adeguatamente delle indicazioni per il numero di core CPU implementati. Queste nuove impostazioni di memoria dovrebbero offrire valori predefiniti migliori, sulla base delle raccomandazioni di Hortonworks. Per modificare queste impostazioni, consultare la documentazione di riferimento dell'SDK sulla modifica della configurazione del cluster. Le nuove impostazioni di memoria usate dal cluster HDInsight predefinito con 4 core CPU (8 contenitori) sono elencate nella tabella seguente (tra parentesi vengono forniti anche i valori usati prima di questa versione). 
 
<table border="1">
<tr><th>Componente</th><th>Allocazione della memoria</th></tr>
<tr><td> yarn.scheduler.minimum-allocation</td><td>768MB (in precedenza 512MB)</td></tr>
<tr><td> yarn.scheduler.maximum-allocation</td><td>6144MB (invariato)</td></tr>
<tr><td>yarn.nodemanager.resource.memory</td><td>6144MB (invariato)</td></tr>
<tr><td>mapreduce.map.memory</td><td>768MB (in precedenza 512MB)</td></tr>
<tr><td>mapreduce.map.java.opts</td><td>opts=-Xmx512m (in precedenza -Xmx410m)</td></tr>
<tr><td>mapreduce.reduce.memory</td><td>1536MB (in precedenza 1024MB)</td></tr>
<tr><td>mapreduce.reduce.java.opts</td><td>opts=-Xmx1024m (in precedenza -Xmx819m)</td></tr>
<tr><td>yarn.app.mapreduce.am.resource</td><td>768MB (in precedenza 1024MB)</td></tr>
<tr><td>yarn.app.mapreduce.am.command</td><td>opts=-Xmx512m (in precedenza -Xmx819m)</td></tr>
<tr><td>mapreduce.task.io.sort</td><td>256 MB (in precedenza 200 MB)</td></tr>
<tr><td>tez.am.resource.memory</td><td>1536 MB (invariato)</td></tr>

</table><br>

Per altre informazioni sulle impostazioni di configurazione di memoria usate da YARN e da MapReduce su Hortonworks Data Platform usata da HDInsight, vedere l'articolo su come [determinare le impostazioni di configurazione di memoria HDP](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1-latest/bk_installing_manually_book/content/rpm-chap1-11.html). Hortonworks fornisce anche uno strumento per calcolare correttamente le impostazioni di memoria.

Errore di HDInsight PowerShell/SDK: "*Cluster is not configured for Http Services access*":

* Questo errore è un [problema di compatibilità](https://social.msdn.microsoft.com/Forums/azure/en-US/a7de016d-8de1-4385-b89e-d2e7a1a9d927/hdinsight-powershellsdk-error-cluster-is-not-configured-for-http-services-access?forum=hdinsight) noto che può verificarsi a causa di una differenza tra la versione di SDK/PowerShell e la versione del cluster. Per i cluster creati sulla versione 8/15 o successiva esiste il supporto della nuova funzionalità di provisioning nelle reti virtuali, che però non viene interpretata correttamente dalle versioni precedenti di SDK/PowerShell. Il risultato è un errore in alcune operazioni di invio dei processi. Se si usano le API SDK o i cmdlet PowerShell per inviare i processi (**Use-AzureHDInsightCluster**, **Invoke-Hive**), queste operazioni possono non riuscire e generare il messaggio di errore "*Cluster <clustername> is not configured for Http Services access*" o, a seconda dell'operazione, altri messaggi di errore come "*Cannot connect to cluster*".

* Questi problemi di compatibilità sono stati risolti nelle versioni più recenti di HDInsight SDK e di Azure PowerShell. È consigliabile aggiornare HDInsight SDK alla versione 1.3.1.6 o successiva e gli strumenti di Azure PowerShell alla versione 0.8.8 o successiva. È possibile ottenere l'accesso alla versione più recente di HDInsight SDK da [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Gli strumenti di Azure PowerShell sono disponibili in [Come installare e configurare Azure PowerShell](http://azure.microsoft.com/it-it/documentation/articles/install-configure-powershell/).

* Finché la versione del cluster rimane invariata, SDK e PowerShell continueranno a funzionare con i nuovi aggiornamenti. Ad esempio, la versione 3.1 dei cluster sarà sempre compatibile con la versione corrente di SDK/PowerShell 1.3.1.6 e 0.8.8.


## Note per la versione di HDInsight 3.1 rilasciata il 12/09/2014##

* Questa versione è basata su Hortonworks Data Platform (HDP) 2.1.5. Per un elenco dei bug risolti in questa versione, vedere la pagina relativa alle [correzioni disponibili in questa versione](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.5/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.5-fixed.html) nel sito Hortonworks.
* Nella cartella delle librerie pig, il file "avro-mapred-1.7.4.jar" è stato modificato in avro-mapred-1.7.4-hadoop2.jar. Questi file contengono una correzione di bug non essenziale che non causa interruzioni. È consigliabile che i clienti non accettino una dipendenza diretta sul nome dello stesso file JAR in modo da evitare interruzioni quando i file vengono rinominati.


## Note per la versione rilasciata il 21/08/2014 ##

* È stata aggiunta la nuova configurazione WebHCat (HIVE-7155) seguente, che imposta il limita di memoria predefinito per un processo controller Templeton a 1 GB (il valore predefinito precedente era 512 MB):
	
	* templeton.mapper.memory.mb (=1024)
	* Questa modifica risolve l'errore seguente che si verificava in alcune query Hive a causa dei limiti di memoria più bassi: "Container is running beyond physical memory limits".
	* Per tornare alle impostazioni predefinite precedenti, è possibile impostare questo valore di configurazione su 512 mediante PowerShell SDK al momento della creazione del cluster usando il comando seguente:
	
		Add-AzureHDInsightConfigValues -Core @{"templeton.mapper.memory.mb"="512";}


* Il nome host del ruolo zookeeper è stato modificato in zookeeper. Questo influisce sulla risoluzione dei nomi nel cluster, ma non sulle API REST esterne. Se sono presenti componenti che usano il nome host zookeepernode sarà necessario aggiornarli in modo che usino il nuovo nome. I nuovi nomi per i tre nodi zookeeper sono: 
	* zookeeper0 
	* zookeeper1 
	* zookeeper2 
* La matrice di supporto delle versioni di HBase è stata aggiornata. Per i carichi di lavoro HBase di produzione è supportato solo HDInsight 3.1 (HBase versione 0.98). In futuro la versione 3.0 disponibile in anteprima non sarà supportata. Durante il periodo di transizione, i clienti potranno comunque creare cluster della versione 3.0. 

## Note sui cluster creati prima del 15/08/2014 ##

È possibile che si verifichi un errore di HDInsight PowerShell/SDK con il messaggio "Cluster <clustername> is not configured for Http Services access" (o, a seconda dell'operazione, altri messaggi di errore come: "Cannot connect to cluster") a causa di una differenza tra le versioni di SDK/PowerShell e un cluster. Per i cluster creati sulla versione 8/15 o successiva esiste il supporto della nuova funzionalità di provisioning nelle reti virtuali, che però non viene interpretata correttamente dalle versioni precedenti di SDK/PowerShell, dando origine a errori nelle operazioni di invio dei processi. Se si usano le API SDK o i cmdlet PowerShell per inviare i processi, ad esempio Use-AzureHDInsightCluster o Invoke-AzureHDInsightHiveJob, queste operazioni possono non riuscire e generare uno dei messaggi di errore sopra descritti.

Questi problemi di compatibilità sono stati risolti nelle versioni più recenti di SDK e di Azure PowerShell. È consigliabile aggiornare HDInsight SDK alla versione 1.3.1.6 o successiva e gli strumenti di Azure PowerShell alla versione 0.8.8 o successiva. È possibile ottenere l'accesso alla versione più recente di HDInsight SDK da [NuGet](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.HDInsight/). Gli strumenti di Azure PowerShell sono disponibili tramite l'[Installazione guidata piattaforma Web Microsoft](http://go.microsoft.com/?linkid=9811175&clcid=0x409).

Finché la versione del cluster rimane invariata, SDK e PowerShell continueranno a funzionare con i nuovi aggiornamenti. Ad esempio, la versione 3.1 dei cluster sarà sempre compatibile con la versione corrente di SDK/PowerShell 1.3.1.6 e 0.8.8.


## Note per la versione rilasciata il 28/07/2014 ##

* **Disponibilità di HDInsight in nuove aree geografiche**: con questa versione la presenza geografica di HDInsight è stata estesa a tre nuove aree. I clienti di HDInsight possono ora creare cluster in queste aree. 
	* Asia orientale 
	* Stati Uniti centro-settentrionali e 
	* Stati Uniti centro-meridionali. 
* In questa versione, HDInsight v1.6 (HDP1.1, Hadoop 1.0.3) e HDInsight v2.1 (HDP1.3, Hadoop 1.2) verranno rimossi dal portale di gestione di Azure. È possibile continuare a creare cluster Hadoop per queste versioni usando i cmdlet PowerShell per HDInsight ([New-AzureHDInsightCluster](http://msdn.microsoft.com/it-it/library/dn593744.aspx)) o tramite [HDInsight SDK](http://msdn.microsoft.com/it-it/library/azure/dn469975.aspx). Per altre informazioni, vedere la pagina relativa al [controllo delle versioni dei componenti di HDInsight](http://azure.microsoft.com/it-it/documentation/articles/hdinsight-component-versioning/).
* Modifiche a Hortonworks Data Platform (HDP) in questa versione: 

<table border="1">
<tr><th>HDP</th><th>Modifiche</th></tr>
<tr><td>HDP 1.3/HDI 2.1</td><td>Nessuna modifica</td></tr>
<tr><td>HDP 2.0/HDI 3.0</td><td>Nessuna modifica</td></tr>
<tr><td>HDP 2.1/HDI 3.1</td><td>zookeeper: ['3.4.5.2.1.3.0-1948'] -> ['3.4.5.2.1.3.2-0002']</td></tr>


</table><br>

## Note per la versione rilasciata il 24/6/2014 ##

In questa versione sono disponibili numerosi miglioramenti al servizio HDInsight: 

* **Disponibilità di HDP 2.1**: HDInsight 3.1, che contiene HDP 2.1, è ora disponibile a livello generale e rappresenta la versione predefinita per i nuovi cluster.
* **HBase - Miglioramenti nel portale di gestione di Azure**: I cluster HBase sono disponibili in anteprima. Ora è possibile creare cluster HBase dal portale con 3 clic.

![](http://i.imgur.com/cmOl5fM.png)

HBase consente di creare numerosi carichi di lavoro in tempo reale su HDInsight, da siti Web interattivi da usare con grandi set di dati a servizi che archiviano dati di sensori e telemetria da milioni di endpoint. Il passaggio successivo consiste nell'analizzare i dati di questi carichi di lavoro tramite processi Hadoop e questo è immediatamente possibile in HDInsight mediante le esperienze disponibili, ad esempio PowerShell e il dashboard del cluster Hive.

### Apache Mahout è ora preinstallato in HDInsight 3.1 ###

 [Mahout](http://hortonworks.com/hadoop/mahout/) è preinstallato nei cluster Hadoop di HDInsight 3.1. In questo modo, è possibile eseguire processi Mahout senza dover configurare ulteriormente i cluster. Ad esempio, è possibile accedere in remoto a un cluster Hadoop usando il protocollo RDP (Remote Desktop Protocol) e senza ulteriori passaggi eseguire il comando di Mahout Hello world:

		mahout org.apache.mahout.classifier.df.tools.Describe -p /user/hdp/glass.data -f /user/hdp/glass.info -d I 9 N L  

		mahout org.apache.mahout.classifier.df.BreimanExample -d /user/hdp/glass.data -ds /user/hdp/glass.info -i 10 -t 100

Per una spiegazione completa della procedura, vedere la documentazione relativa all'[esempio Breiman](https://mahout.apache.org/users/classification/breiman-example.html) nel sito Web di Apache Mahout. 


### Le query Hive possono usare Tez in HDInsight 3.1 ###

Hive 0.13 è ora disponibile in HDInsight 3.1 e può eseguire query mediante Tez, consentendo notevoli miglioramenti delle prestazioni. 
Tez non è abilitato per impostazione predefinita per le query Hive. Per usarlo, è necessario acconsentire esplicitamente. È possibile abilitare Tez eseguendo il frammento di codice seguente:

		set hive.execution.engine=tez;
		select sc_status, count(*), histogram_numeric(sc_bytes,5) from website_logs_orc_local group by sc_status;

Hortonworks ha pubblicato un elenco dettagliato dei miglioramenti delle prestazioni delle query Hive con Tez risultanti dall'esecuzione di benchmark standard. Per informazioni dettagliate, vedere l'articolo relativo al [benchmarking di Apache Hive 13 per la soluzione Hadoop di livello enterprise](http://hortonworks.com/blog/benchmarking-apache-hive-13-enterprise-hadoop/). 

Per altri dettagli sull'uso di Hive con Tez, vedere la [pagina Wiki relativa a Hive su Tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez).

###Disponibilità globale
Con il rilascio di Azure HDInsight in Hadoop 2.2, Microsoft ha reso HDInsight disponibile in tutte le principali aree geografiche di Azure. In particolare, sono stati collegati i data center dell'Europa occidentale e dell'Asia sudorientale. Ciò consente ai clienti di individuare i cluster in un data center chiuso e potenzialmente situato in una zona con analoghi requisiti di conformità. 


###Operazioni consigliate e sconsigliate tra le versioni dei cluster

**I metastore Oozie usati con un cluster HDInsight 3.1 non sono compatibili con le versioni precedenti dei cluster HDInsight 2.1 e non possono essere usati con questa versione precedente**

Un database metastore Oozie personalizzato, implementato con un cluster HDInsight 3.1, non può essere riutilizzato con un cluster HDInsight 2.1, anche se il metastore è stato originato con un cluster 2.1. Questo scenario non è supportato perché lo schema del metastore viene aggiornato quando usato con un cluster 3.1 e dunque non è più compatibile con il metastore richiesto dai cluster 2.1. Qualsiasi tentativo di riusare un metastore Oozie che è stato usato con un cluster HDInsight 3.1 renderà inutilizzabile il cluster 2.1. 

**Non è possibile condividere i metastore Oozie tra i cluster**
In linea generale e alquanto indipendente, i metastore Oozie vengono collegati a cluster specifici e non possono essere condivisi tra cluster.

###Modifiche di rilievo

**Sintassi del prefisso**:
nei cluster HDInsight 3.0 e 3.1 è supportata solo la sintassi "wasb://". La sintassi asv:// precedente è supportata nei cluster HDInsight 2.1 e 1.6, ma non è supportata nei cluster HDInsight 3.0 e non sarà supportata nelle versioni successive. Ciò vuol dire che i processi inviati a un cluster HDInsight 3.0 o 3.1 che usano esplicitamente la sintassi "asv://" avranno esito negativo. Sarà pertanto necessario usare la sintassi wasb://. Avrà inoltre esito negativo qualsiasi processo inviato a un cluster HDInsight 3.0 o 3.1 e creato con un metastore esistente che contiene riferimenti espliciti a risorse che usano la sintassi asv://. Sarà necessario ricreare i metastore in modo da fare riferimento alle risorse usando la sintassi wasb://. 


**Porte**: Le porte usate dal servizio HDInsight sono state cambiate. I numeri di porta usati in precedenza erano compresi nell'intervallo di porte temporanee del sistema operativo Windows. Per le comunicazioni di breve durata basate su IP, le porte vengono allocate automaticamente da un intervallo di porte temporanee predefinito. Il nuovo set di numeri di porta per il servizio Hortonworks Data Platform (HDP) sono ora esterne a questo intervallo, per evitare conflitti con le porte usate dai servizi in esecuzione sul nodo head. I nuovi numeri di porta non dovrebbero provocare modifiche di rilievo. I nuovi numeri in uso sono i seguenti:

 **HDInsight 1.6 (HDP 1.1)**
<table border="1">
<tr><th>Nome</th><th>Valore</th></tr>
<tr><td>dfs.http.address</td><td>namenodehost:30070</td></tr>
<tr><td>dfs.datanode.address</td><td>0.0.0.0:30010</td></tr>
<tr><td>dfs.datanode.http.address</td><td>0.0.0.0:30075</td></tr>
<tr><td>dfs.datanode.ipc.address</td><td>0.0.0.0:30020</td></tr>
<tr><td>dfs.secondary.http.address</td><td>0.0.0.0:30090</td></tr>
<tr><td>mapred.job.tracker.http.address</td><td>jobtrackerhost:30030</td></tr>
<tr><td>mapred.task.tracker.http.address</td><td>0.0.0.0:30060</td></tr>
<tr><td>mapreduce.history.server.http.address</td><td>0.0.0.0:31111</td></tr>
<tr><td>templeton.port</td><td>30111</td></tr>
</table><br>

 **HDInsight 3.0 e 3.1 (HDP 2.0 e 2.1)**
<table border="1">
<tr><th>Nome</th><th>Valore</th></tr>
<tr><td>dfs.namenode.http-address</td><td>namenodehost:30070</td></tr>
<tr><td>dfs.namenode.https-address</td><td>headnodehost:30470</td></tr>
<tr><td>dfs.datanode.address</td><td>0.0.0.0:30010</td></tr>
<tr><td>dfs.datanode.http.address</td><td>0.0.0.0:30075</td></tr>
<tr><td>dfs.datanode.ipc.address</td><td>0.0.0.0:30020</td></tr>
<tr><td>dfs.namenode.secondary.http-address</td><td>0.0.0.0:30090</td></tr>
<tr><td>yarn.nodemanager.webapp.address</td><td>0.0.0.0:30060</td></tr>
<tr><td>templeton.port</td><td>30111</td></tr>
</table><br>

###Dipendenze 

In HDInsight 3.x (HDP2.x) sono state aggiunte le dipendenze seguenti:

* guice-servlet
* optiq-core
* javax.inject
* activation
* jsr305
* geronimo-jaspic_1.0_spec
* jul-to-slf4j
* java-xmlbuilder
* ant
* commons-compiler
* jdo-api
* commons-math3
* paranamer
* jaxb-impl
* stringtemplate
* eigenbase-xom
* jersey-servlet
* commons-exec
* jaxb-api
* jetty-all-server
* janino
* xercesImpl
* optiq-avatica
* jta
* eigenbase-properties
* groovy-all
* hamcrest-core
* mail
* linq4j
* jpam
* jersey-client
* aopalliance
* geronimo-annotation_1.0_spec
* ant-launcher
* jersey-guice
* xml-apis
* stax-api
* asm-commons
* asm-tree
* wadl
* geronimo-jta_1.1_spec
* guice
* leveldbjni-all
* velocity
* jettison
* snappy-java
* jetty-all
* commons-dbcp

Le dipendenze seguenti non esistono più in HDInsight 3.x (HDP2.x):

* jdeb
* kfs
* sqlline
* ivy
* aspectjrt
* json
* core
* jdo2-api
* avro-mapred
* datanucleus-enhancer
* jsp
* commons-logging-api
* commons-math
* JavaEWAH
* aspectjtools
* javolution
* hdfsproxy
* hbase
* snappy

###Modifiche della versione 

Tra HDInsight 2.x (HDP1.x) e HDInsight 3.x (HDP2.x) sono state apportate le seguenti modifiche della versione:

* metrics-core: ['2.1.2'] -> ['3.0.0']
* derbynet: ['10.4.2.0'] -> ['10.10.1.1']
* datanucleus: ['rdbms-3.0.8'] -> ['rdbms-3.2.9']
* jasper-compiler: ['5.5.12'] -> ['5.5.23']
* log4j: ['1.2.15', '1.2.16'] -> ['1.2.16', '1.2.17']
* derbyclient: ['10.4.2.0'] -> ['10.10.1.1']
* httpcore: ['4.2.4'] -> ['4.2.5']
* hsqldb: ['1.8.0.10'] -> ['2.0.0']
* jets3t: ['0.6.1'] -> ['0.9.0']
* protobuf-java: ['2.4.1'] -> ['2.5.0']
* derby: ['10.4.2.0'] -> ['10.10.1.1']
* jasper: ['runtime-5.5.12'] -> ['runtime-5.5.23']
* commons-daemon: ['1.0.1'] -> ['1.0.13']
* datanucleus-core: ['3.0.9'] -> ['3.2.10']
* datanucleus-api-jdo: ['3.0.7'] -> ['3.2.6']
* zookeeper: ['3.4.5.1.3.9.0-01320'] -> ['3.4.5.2.1.3.0-1948']
* bonecp: ['0.7.1.RELEASE'] -> ['0.8.0.RELEASE']


###Driver
Il driver JDBC per SQL Server viene usato internamente da HDInsight e non viene usato per operazioni esterne. Se si desidera connettersi a HDInsight mediante ODBC, usare Microsoft Hive ODBC Driver. Per informazioni sull'uso di Hive ODBC, vedere [Connessione di Excel a HDInsight mediante Microsoft Hive ODBC Driver][connect-excel-with-hive-ODBC].


### Correzioni di bug ###

In questa versione, le versioni seguenti di HDInsight (Hortonworks Data Platform - HDP) sono state aggiornate con svariate correzioni di bug:

* HDInsight 2.1 (HDP 1.3)
* HDInsight 3.0 (HDP 2.0)
* HDInsight 3.1 (HDP 2.1)


## Note sulla versione di Hortonworks ##

Le note sulla versione relative alle piattaforme HDP usate dalle versioni del cluster HDInsight sono disponibili nelle pagine seguenti.

* Il cluster HDInsight versione 3.1 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 2.1.7][hdp-2-1-7]. Si tratta del cluster Hadoop predefinito creato quando si usa il portale di Azure HDInsight dopo il 07/11/2014. I cluster HDInsight 3.1 creati prima del 07/11/2014 sono basati su [Hortonworks Data Platform 2.1.1][hdp-2-1-1] 

* Il cluster HDInsight versione 3.0 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 2.0][hdp-2-0-8].

* Il cluster HDInsight versione 2.1 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 1.3][hdp-1-3-0]. 

* Il cluster HDInsight versione 1.6 usa una distribuzione Hadoop basata su [Hortonworks Data Platform 1.1][hdp-1-1-0]. 

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html


[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-r-scripts]: ../hdinsight-hadoop-r-scripts/




<!--HONumber=35.1-->
