<properties
   pageTitle="Scalabilità del cluster in HDInsight | Azure"
   description="Modificare il numero di nodi dati in un cluster in esecuzione in HDInsight senza eliminare e ricreare il cluster."
   services="hdinsight"
   documentationCenter=""
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="04/02/2015"
   ms.author="jgao"/>

#Scalabilità del cluster in HDInsight

La funzionalità di scalabilità del cluster consente di modificare il numero di nodi dati usati da un cluster in esecuzione in Azure HDInsight senza dover eliminare e ricreare il cluster. L'operazione può essere eseguita tramite Azure PowerShell, HDInsight SDK o il portale Azure.

## Informazioni sulle funzionalità
Questa sezione descrive l'impatto della modifica del numero di nodi dati per ogni tipo di cluster supportato da HDInsight:

* Hadoop
* Storm
* HBase 

## Hadoop 

### Aggiunta di nodi dati
È possibile aumentare facilmente il numero di nodi dati in un cluster Hadoop in esecuzione senza conseguenze per eventuali processi in sospeso o in esecuzione. È inoltre possibile inviare nuovi processi mentre è in corso l'operazione. Gli errori in un'operazione di scalabilità vengono gestiti in modo che il cluster rimanga sempre in uno stato funzionale.

### Rimozione di nodi dati
Quando un cluster Hadoop viene ridimensionato riducendo il numero di nodi dati, alcuni dei servizi del cluster vengono riavviati. In questo modo, tutti i processi in esecuzione e in attesa daranno esito negativo dopo il completamento dell'operazione di ridimensionamento. È tuttavia possibile inviare nuovamente i processi una volta completata l'operazione.

## Storm
È possibile aggiungere o rimuovere facilmente nodi dati dal cluster Storm mentre è in esecuzione. Tuttavia, dopo il completamento dell'operazione di ridimensionamento, è necessario bilanciare nuovamente la topologia.

A tale scopo, è possibile scegliere tra due opzioni:

* Interfaccia utente Web di Storm
* Interfaccia della riga di comando (CLI) 

Per altre informazioni, fare riferimento alla [documentazione su Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

L'interfaccia utente Web di Storm è disponibile nel cluster HDInsight:

![image1](./media/hdinsight-hadoop-cluster-scaling/StormUI.png)

Di seguito viene fornito un esempio d'uso del comando CLI per ribilanciare la topologia di Storm:

	## Reconfigure the topology "mytopology" to use 5 worker processes,
	## the spout "blue-spout" to use 3 executors, and
	## the bolt "yellow-bolt" to use 10 executors

	$ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

##HBase
È possibile aggiungere o rimuovere facilmente nodi nel cluster HBase mentre è in esecuzione. I server a livello di area vengono bilanciati automaticamente entro pochi minuti dal completamento dell'operazione di ridimensionamento. È tuttavia possibile anche bilanciare manualmente i server a livello di area accedendo al nodo head del cluster ed eseguendo i comandi seguenti da una finestra del prompt dei comandi:

	>pushd %HBASE_HOME%\bin
	>hbase shell
	>balancer

## Prerequisiti

* Sono supportati solo i cluster con HDInsight versione 3.1.3 o successive. Se non si è sicuri della versione del cluster, è possibile verificarla dal portale di Azure facendo clic sul nome del cluster HDInsight o eseguendo il comando `Get-AzureHDInsightCluster –name <clustername>` da Azure PowerShell.

* Per eseguire l'operazione da Azure PowerShell, è necessario disporre di Azure PowerShell versione 0.8.14 o successive. È possibile scaricare la versione più recente di Azure PowerShell dalla sezione **Strumenti da riga di comando** nel sito Web dei [download di Azure](http://azure.microsoft.com/downloads/). È possibile verificare la versione di Azure PowerShell installata usando il comando seguente da una finestra di Azure PowerShell: `(get-module Azure).Version`

## Come usare la scalabilità del cluster

### Portale di Azure
Le dimensioni di un cluster in esecuzione possono essere modificate mediante la scheda **SCALABILITÀ** del dashboard del cluster Azure HDInsight.

![](http://i.imgur.com/u5Mewwx.png)

### Azure PowerShell
Per modificare le dimensioni del cluster Hadoop mediante Azure PowerShell, eseguire il comando seguente da un computer client:

	Set-AzureHDInsightClusterSize -ClusterSizeInNodes <NewSize> -name <clustername>	

> [AZURE.NOTE]Per usare questo comando, nel computer client deve essere installato Azure PowerShell versione 0.8.14 o successive.

### SDK
Per modificare le dimensioni del cluster Hadoop mediante HDInsight SDK, usare uno dei metodi seguenti:

	ChangeClusterSize(string dnsName, string location, int newSize) 

oppure

	ChangeClusterSizeAsync(string dnsName, string location, int newSize) 


Entrambe le versioni, sincrona e asincrona, di questo metodo restituiscono un oggetto [ClusterDetails](http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterdetails_properties.aspx).

Di seguito sono riportati alcuni esempi di codice che illustrano come usare la versione sincrona del metodo:

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.Threading.Tasks;
	using System.Security.Cryptography.X509Certificates;
	using Microsoft.WindowsAzure.Management.HDInsight;
	using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;

	namespace HDInsightClusterScaling
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            // Friendly name for the certificate your created earlier  
	            string certfriendlyname = "<CertificateFriendlyName>";     
	            string subscriptionid = "<SubscriptionID>";
	            string clustername = "<ClusterDNSName>";
	     		string location = "<ClusterLocation>”";
				int newSize = <NewClusterSize>;
	
	            // Get the certificate object from certificate store by using the friendly name to identify it
	            X509Store store = new X509Store();
	            store.Open(OpenFlags.ReadOnly);
	            X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certfriendlyname);
	
	            // Create an HDInsightClient object
	            HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
	            var client = HDInsightClient.Connect(creds);
	
	            Console.WriteLine("Rescaling HDInsight cluster ...");
	
	            // Change the cluster size
	     		ClusterDetails cluster = client.ChangeClusterSize(clustername, location, newSize);
	            
	            Console.WriteLine("Cluster Rescaled: {0} \n New Cluster Size = {1}", cluster.ConnectionUrl, cluster.ClusterSizeInNodes);
	            Console.WriteLine("Press ENTER to continue.");
	            Console.ReadKey();
	        }
	    }
	}


Per altre informazioni sull'uso di HDInsight .NET SDK, vedere [Effettuare il provisioning di cluster Hadoop in HDInsight con opzioni personalizzate](hdinsight-provision-clusters.md).

<!--HONumber=54-->