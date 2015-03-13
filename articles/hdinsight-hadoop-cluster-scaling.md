<properties
   pageTitle="Scalabilità del cluster in HDInsight | Azure"
   description="Modificare il numero di nodi dati in un cluster in esecuzione in HDInsight senza eliminare e ricreare il cluster."
   services="hdinsight"
   documentationCenter=""
   authors="bradsev"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="bradsev"/>

#Scalabilità del cluster in HDInsight

La funzionalità di scalabilità del cluster consente di modificare il numero di nodi dati usato da un cluster, in esecuzione in HDInsight, senza dover eliminare e ricreare il cluster. L'operazione può essere eseguita tramite PowerShell, HDInsight SDK o dal portale Azure.

> [WACOM.NOTE] Solo i tipi di cluster Hadoop e Storm sono supportati nella versione corrente. Il supporto per il cluster HBase verrà aggiunto a breve. 

## Informazioni sulle funzionalità
Questa sezione descrive l'impatto della modifica del numero di nodi dati per ogni tipo di cluster supportato da HDInsight:

* Hadoop
* Storm
* HBase 

## Hadoop 

### Aggiungere nodi dati
È possibile aumentare facilmente il numero di nodi dati in un cluster Hadoop in esecuzione senza conseguenze per eventuali processi in sospeso o in esecuzione. È inoltre possibile inviare nuovi processi mentre è in corso l'operazione. Gli errori in un'operazione di scalabilità vengono gestiti in modo che il cluster rimanga sempre in uno stato funzionale.

### Rimuovere nodi dati
Quando un cluster Hadoop viene scalato riducendo il numero di nodi dati, alcuni dei servizi del cluster vengono riavviati. In questo modo, tutti i processi in esecuzione e in attesa daranno esito negativo dopo il completamento dell'operazione di ridimensionamento. È tuttavia possibile inviare nuovamente i processi una volta completata l'operazione.

## Storm
È possibile aggiungere o rimuovere facilmente nodi dati dal cluster Storm mentre è in esecuzione. Tuttavia, dopo il completamento dell'operazione di scalabilità, è necessario bilanciare nuovamente la topologia.

Questa procedura può essere eseguita mediante:

* Interfaccia utente Web di Storm
* Strumento CLI 

Per altre informazioni, fare riferimento alla [documentazione di Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

L'interfaccia utente Web di Storm è disponibile nel cluster HDInsight:

![image1](./media/hdinsight-hadoop-cluster-scaling/StormUI.png)

Di seguito viene fornito un esempio d'uso del comando CLI per ribilanciare la topologia di Storm:

	## Reconfigure the topology "mytopology" to use 5 worker processes,
	## the spout "blue-spout" to use 3 executors and
	## the bolt "yellow-bolt" to use 10 executors.

	$ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

##HBase
L'operazione di scalabilità del cluster non è attualmente supportata per i cluster di tipo HBase.

## Prerequisiti:

* Sono supportati solo i cluster con HDInsight versione 3.1.3 o successive. Se non si è sicuri della versione del cluster, è possibile verificarla dal portale Azure facendo clic sul nome del cluster HDInsight o eseguendo il comando `Get-AzureHDInsightCluster -name <clustername>` da Azure PowerShell.

* Per eseguire l'operazione da PowerShell, è necessario Azure PowerShell versione 0.8.14 o successive. È possibile scaricare la versione più recente di PowerShell dalla sezione degli strumenti da riga di comando nel sito Web dei [download di Azure](http://azure.microsoft.com/ downloads/). È possibile verificare la versione di Azure PowerShell installata con il seguente comando da una finestra di PowerShell: `(get-module Azure).Version`

## Come usare la scalabilità del cluster

### Portale di Azure
La dimensione di un cluster in esecuzione può essere modificata mediante la scheda **Scala** del dashboard del cluster di Azure HDInsight.

![](http://i.imgur.com/u5Mewwx.png)

### PowerShell
Per modificare la dimensione del cluster Hadoop mediante PowerShell, attivare il seguente comando da un computer client:

	Set-AzureHDInsightClusterSize -ClusterSizeInNodes <NewSize> -name <clustername>	

> [WACOM.NOTE] Per usare questo comando, nel computer client deve essere installato Azure PowerShell versione 0.8.14 o successive.

### SDK
Per modificare la dimensione del cluster Hadoop usando HDInsight SDK, usare uno dei seguenti metodi: 

	ChangeClusterSize(string dnsName, string location, int newSize) 

o 

	ChangeClusterSizeAsync(string dnsName, string location, int newSize) 


Le versioni sincrona e asincrona del metodo restituiscono l'oggetto [ClusterDetails](http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterdetails_properties.aspx).

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
	     		string location = "<ClusterLocation>"";
				int newSize = <NewClusterSize>;
	
	            // Get the certificate object from certificate store using the friendly name to identify it
	            X509Store store = new X509Store();
	            store.Open(OpenFlags.ReadOnly);
	            X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certfriendlyname);
	
	            // Create an HDInsightClient object
	            HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
	            var client = HDInsightClient.Connect(creds);
	
	            Console.WriteLine("Rescaling HDInsight cluster ...");
	
	            // Change cluster size
	     		ClusterDetails cluster = client.ChangeClusterSize(clustername, location, newSize);
	            
	            Console.WriteLine("Cluster Rescaled: {0} \n New Cluster Size = {1}", cluster.ConnectionUrl, cluster.ClusterSizeInNodes);
	            Console.WriteLine("Press ENTER to continue.");
	            Console.ReadKey();
	        }
	    }
	}


Per altre informazioni sull'uso di HDInsight .NET SDK, fare riferimento all'argomento relativo al [provision di cluster Hadoop in HDInsight con opzioni personalizzate](http://azure.microsoft.com/ documentation/articles/hdinsight-provision-clusters/).
<!--HONumber=45--> 
