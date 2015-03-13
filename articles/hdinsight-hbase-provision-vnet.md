<properties 
	pageTitle="Effettuare il provisioning di cluster HBase in Rete virtuale di Azure | Azure" 
	description="Informazioni su come creare cluster HDInsight in Rete virtuale di Azure." 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/19/2014" 
	ms.author="jgao"/>

# Provisioning di cluster HBase in Rete virtuale di Azure

Informazioni su come creare cluster HBase di HDInsight in [Rete virtuale di Azure][1]. 

Grazie all'integrazione con la rete virtuale, i cluster HBase possono essere distribuiti nella stessa rete virtuale delle applicazioni, permettendo così alle applicazioni di comunicare direttamente con HBase. Questo approccio offre i vantaggi seguenti:

- Connettività diretta dell'applicazione Web con i nodi del cluster HBase, che permette le comunicazioni tramite le API RPC Java HBase.
- Miglioramento delle prestazioni, poiché il traffico non deve attraversare più gateway e servizi di bilanciamento del carico. 
- Elaborazione di informazioni sensibili in modo più sicuro, senza esporre un endpoint pubblico.


##Contenuto dell'articolo

- [Prerequisiti](#prerequisites)
- [Effettuare il provisioning di cluster HBase in una rete virtuale](#hbaseprovision)
- [Effettuare la connessione al cluster HBase con provisioning nella rete virtuale tramite le API RPC Java HBase](#connect)
- [Effettuare il provisioning di un cluster HBase tramite Azure PowerShell](#powershell)
- [Passaggi successivi](#nextsteps)

##<a id="prerequisites"></a>Prerequisiti
Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- **Una sottoscrizione di Azure**. Azure è una piattaforma basata su sottoscrizione. Per altre informazioni su come ottenere una sottoscrizione, vedere [Opzioni di acquisto][azure-purchase-options], [Offerte per i membri][azure-member-offers] oppure [Versione di valutazione gratuita][azure-free-trial].

- **Una workstation in cui sia stato installato e configurato Azure PowerShell**. Per le istruzioni, vedere [Installare e configurare Azure PowerShell][powershell-install]. Per eseguire script di PowerShell, è necessario eseguire Azure PowerShell come amministratore e impostare i criteri di esecuzione su *RemoteSigned*. Vedere [Uso del cmdlet Set-ExecutionPolicy][2].

	Prima di eseguire script PowerShell, assicurarsi di essere connessi alla sottoscrizione di Azure usando il seguente cmdlet:

		Add-AzureAccount

	Se si dispone di più sottoscrizioni di Azure, usare il seguente cmdlet per impostare la sottoscrizione corrente:

		Select-AzureSubscription <AzureSubscriptionName>


##<a id="hbaseprovision"></a>Effettuare il provisioning di un cluster HBase in una rete virtuale. 

**Per creare una rete virtuale usando il portale di gestione:**

1. Accedere al [portale di gestione di Azure][azure-portal].
2. Fare clic su **NUOVO** nell'angolo in basso a sinistra, scegliere **SERVIZI DI RETE**, quindi **RETE VIRTUALE** e infine **CREAZIONE RAPIDA**.
3. Digitare o selezionare i valori seguenti:

	- **Nome**: nome della rete virtuale.
	- **Spazio di indirizzi**: scegliere uno spazio di indirizzi per la rete virtuali con dimensioni sufficienti per fornire indirizzi per tutti i nodi del cluster. In caso contrario, il provisioning avrà esito negativo. Per completare questa esercitazione, è possibile scegliere una qualsiasi delle tre opzioni. 
	- **Numero massimo VM**: scegliere uno dei valori disponibili per il numero massimo di VM. Questo valore determinerà il numero di host (VM) che è possibile creare nello spazio di indirizzi. Per completare questa esercitazione, è sufficiente specificare **4096 [CIDR: /20]**. 
	- **Percorso**: il percorso deve essere uguale a quello del cluster HBase che sarà creato.
	- **Server DNS**: questo articolo usa un server DNS interno fornito da Azure. È quindi possibile scegliere **Nessuno**. Sono supportate anche configurazioni di rete più avanzate con server DNS personalizzati. Per istruzioni dettagliate, vedere [Risoluzione dei nomi (DNS)](http://msdn.microsoft.com/library/azure/jj156088.aspx).
4. Fare clic su **CREA RETE VIRTUALE**. Il nome della rete virtuale sarà visualizzato nell'elenco. Attendere l'impostazione del valore **Creata** nella colonna Stato.
5. Nel riquadro principale fare clic sulla rete virtuale appena creata.
6. Fare clic su **DASHBOARD** nella parte superiore della pagina.
7. In **riepilogo rapido** annotare l'**ID RETE VIRTUALE**. Sarà necessario per il provisioning del cluster HBase.
8. Fare clic su **CONFIGURA** nella parte superiore della pagina.
9. Nella parte inferiore della pagina il nome predefinito per la subnet è **Subnet-1**. Se si vuole, è possibile rinominare la subnet o aggiungere una nuova subnet per il cluster HBase. Annotare il nome della subnet, poiché sarà necessario durante il provisioning del cluster.
10. Verificare il valore di **CIDR (CONTEGGIO INDIRIZZI)** per la subnet che sarà usata per il cluster. Il conteggio di indirizzi deve essere superiore al numero di nodi di lavoro più sette (Gateway: 2, Nodo head: 2, Zookeeper: 3). Ad esempio, se è necessario un cluster HBase da 10 nodi, il conteggio di indirizzi per la subnet dovrà essere maggiore di 17 (10+7). In caso contrario, la distribuzione avrà esito negativo.

	> [AZURE.NOTE] È consigliabile designare una singola subnet per un cluster. 

11. Fare clic su **Salva** nella parte inferiore della pagina, se i valori della subnet sono stati aggiornati.



> [AZURE.NOTE] I cluster HDInsight usano l'archiviazione BLOB di Azure per l'archiviazione dei dati. Per altre informazioni, vedere [Usare l'archiviazione BLOB di Azure con Hadoop in HDInsight][hdinsight-storage]. Saranno necessari un account di archiviazione e un contenitore di archiviazione BLOB. Il percorso dell'account di archiviazione deve corrispondere al percorso della rete virtuale e al percorso del cluster.

**Per creare un account di archiviazione di Azure e un contenitore di archiviazione BLOB:**

1. Accedere al [portale di gestione di Azure][azure-portal].
2. Fare clic su **NUOVO** nell'angolo inferiore sinistro, selezionare **SERVIZI DATI**, quindi **ARCHIVIAZIONE** e infine **CREAZIONE RAPIDA**.

3. Digitare o selezionare i valori seguenti:

	- **URL**: nome dell'account di archiviazione.
	- **PERCORSO**: percorso dell'account di archiviazione. Assicurarsi che corrisponda al percorso della rete virtuale. I gruppi di affinità non sono supportati.
	- **REPLICA**: per finalità di testing, usare **Con ridondanza locale** per ridurre i costi.

4. Fare clic su **CREA ACCOUNT DI ARCHIVIAZIONE**.  Il nuovo account di archiviazione verrà incluso nell'elenco di archiviazione. 
5. Attendere che il valore di **STATO** per il nuovo account di archiviazione diventi **Online**.
6. Fare clic sul nuovo account di archiviazione nell'elenco per selezionarlo.
7. Fare clic su **GESTISCI CHIAVI DI ACCESSO** nella parte inferiore della pagina.
8. Prendere nota dei valori di **NOME ACCOUNT DI ARCHIVIAZIONE** e **CHIAVE DI ACCESSO PRIMARIA** o **CHIAVE DI ACCESSO SECONDARIA**.  È possibile usare indifferentemente una delle due chiavi.  Sarà necessario usarli più avanti nell'esercitazione.
9. Nella parte superiore della pagina fare clic su **CONTENITORE**.
10. Fare clic su **AGGIUNGI** nella parte inferiore della pagina.
11. Immettere il nome del contenitore.  Il contenitore sarà usato come contenitore predefinito per il cluster HBase. Per impostazione predefinita, il nome del contenitore corrisponde al nome del cluster. Lasciare il campo **ACCESSO** impostato su **Privato**.  
12. Fare clic sull'icona del segno di spunta per creare il contenitore.

**Per effettuare il provisioning di un cluster HBase tramite il portale di Azure:**

> [AZURE.NOTE] Per informazioni su come eseguire il provisioning di un nuovo cluster HBase usando PowerShell, vedere [Effettuare il provisioning di un cluster HBase tramite Azure PowerShell)]#powershell).

1. Accedere al [portale di gestione di Azure][azure-portal].

2. Fare clic su **NUOVO** nell'angolo inferiore sinistro, selezionare **SERVIZI DATI**, quindi **HDINSIGHT** e infine **CREAZIONE PERSONALIZZATA**.

3. Immettere un valore in NOME DEL CLUSTER, selezionare Hbase come TIPO DI CLUSTER e selezionare la versione di HDINSIGHT.

	![Provide details for the HBase cluster][img-provision-cluster-page1]

	Fare clic sul pulsante a destra.

4. Immettere o selezionare i valori seguenti:

	- **NODI DATI**: immettere il numero di nodi dati nel cluster HBase. 
	- **AREA/RETE VIRTUALE**: selezionare la rete virtuale di Azure creata.
	- **SUBNET DELLA RETE VIRTUALE**: selezionare una subnet. Il nome predefinito è **Subnet-1**.

	Fare clic sul pulsante a destra.

5. Immettere **NOME UTENTE** e **PASSWORD** dell'utente Hadoop da usare per questo cluster, quindi fare clic sul pulsante a destra.
6. Scegliere se definire un nuovo account di archiviazione oppure usarne uno esistente come predefinito per il cluster, quindi fare clic sul pulsante a destra.

7. Nella pagina **Azioni script** fare clic su **aggiungi azione script** per specificare i dettagli relativi allo script da eseguire per personalizzare un cluster, ad esempio quello da creare. È ad esempio possibile usare Azione script per personalizzare un cluster da installare. <a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Apache Spark</a>. Per altre informazioni, vedere [Personalizzare cluster HDInsight mediante Script Action][hdinsight-customize-cluster]. 
	
	![Configure Script Action to customize an HDInsight HBase cluster][img-provision-cluster-page5]

	<table border='1'>
		<tr><th>Proprietà</th><th>Valore</th></tr>
		<tr><td>Nome</td>
			<td>Specificare un nome per l'azione script.</td></tr>
		<tr><td>URI script</td>
			<td>Specificare l'URI dello script da richiamare per personalizzare il cluster.</td></tr>
		<tr><td>Tipo di nodo</td>
			<td>Specifica i nodi in cui viene eseguito lo script di personalizzazione. È possibile scegliere <b>Tutti i nodi</b>, <b>Solo nodi head</b> o <b>Solo nodi di lavoro</b>.
		<tr><td>Parametri</td>
			<td>Specificare i parametri, se richiesti dallo script.</td></tr>
	</table>

	È possibile aggiungere altre azioni script per installare più componenti nel cluster. Dopo aver aggiunto gli script, fare clic sul segno di spunta per avviare il provisioning del cluster.


Per iniziare a lavorare con il nuovo cluster HBase, è possibile usare le procedure disponibili in [Introduzione all'uso di HBase con Hadoop in HDInsight][hbase-get-started].

##<a id="connect"></a>Effettuare la connessione al cluster HBase con provisioning nella rete virtuale tramite le API RPC Java HBase

1.	Eseguire il provisioning di una macchina virtuale IaaS nella stessa rete virtuale di Azure e nella stessa subnet. In questo modo, sia la macchina virtuale che il cluster HBase useranno lo stesso server DNS interno per risolvere i nomi degli host. Per ottenere questo risultato, è necessario scegliere l'opzione Da raccolta e selezionare la rete virtuale invece di un data center. Per le istruzioni, vedere [Creare una macchina virtuale che esegue Windows Server][vm-create]. È sufficiente un'immagine standard di Windows Server 2012 con una VM di dimensioni ridotte.
	
2.	Quando si usa un'applicazione Java per connettersi a HBase da remoto, è necessario usare il nome di dominio completo (FQDN). Per determinare quest'ultimo, è necessario ottenere il suffisso DNS specifico della connessione del cluster HBase. Per eseguire questa operazione, usare Curl per eseguire query in Ambari, oppure Desktop remoto per connettersi al cluster.

	* **Curl** - Usare il comando seguente:

			curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

		Nei dati JSON restituiti, trovare la voce "host_name". Questa conterrà il nome di dominio completo (FQDN) per i nodi nel cluster. Ad esempio:

			...
			"host_name": "wordkernode0.<clustername>.b1.cloudapp.net
			...

		La porzione del nome di dominio che inizia con il nome del cluster è il suffisso DNS. Ad esempio, mycluster.b1.cloudapp.net.

	* **PowerShell** - Usare lo script di PowerShell seguente per registrare la funzione **Get-ClusterDetail**, che può essere usata per restituire il suffisso DNS.

			function Get-ClusterDetail(
			    [String]
			    [Parameter( Position=0, Mandatory=$true )]
			    $ClusterDnsName,
				[String]
			    [Parameter( Position=1, Mandatory=$true )]
			    $Username,
				[String]
			    [Parameter( Position=2, Mandatory=$true )]
			    $Password,
			    [String]
			    [Parameter( Position=3, Mandatory=$true )]
			    $PropertyName
				)
			{
			<#
			    .SYNOPSIS 
			     Displays information to facilitate HDInsight cluster to cluster scinario within same virtual network.
				.Description
				 This command shows following 4 properties of an HDInsight cluster.
				 1. ZookeeperQuorum (only support HBase type cluster)
					Shows the value of hbase property "hbase.zookeeper.quorum".
				 2. ZookeeperClientPort (only support HBase type cluster)
					Shows the value of hbase property "hbase.zookeeper.property.clientPort".
				 3. HBaseRestServers (only support HBase type cluster)
					Shows a list of host FQDNs which run HBase rest server.
				 4. FQDNSuffix (support all type cluster)
					Shows FQDN suffix of hosts in the cluster.
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
			     This command shows the value of hbase property "hbase.zookeeper.quorum".
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
			     This command shows the value of hbase property "hbase.zookeeper.property.clientPort".
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
			     This command shows a list of host FQDNs which run HBase rest server.
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
			     This command shows FQDN suffix of hosts in the cluster.
			#>
			
				$DnsSuffix = ".azurehdinsight.net"
				
				$ClusterFQDN = $ClusterDnsName + $DnsSuffix
				$webclient = new-object System.Net.WebClient
				$webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)
			
				if($PropertyName -eq "ZookeeperQuorum")
				{
					$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
					$Response = $webclient.DownloadString($Url)
					$JsonObject = $Response | ConvertFrom-Json
					Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'
				}
				if($PropertyName -eq "ZookeeperClientPort")
				{
					$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.property.clientPort"
					$Response = $webclient.DownloadString($Url)
					$JsonObject = $Response | ConvertFrom-Json
					Write-host $JsonObject.items[0].properties.'hbase.zookeeper.property.clientPort'
				}
				if($PropertyName -eq "HBaseRestServers")
				{
					$Url1 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.rest.port"
					$Response1 = $webclient.DownloadString($Url1)
					$JsonObject1 = $Response1 | ConvertFrom-Json
					$PortNumber = $JsonObject1.items[0].properties.'hbase.rest.port'
					
					$Url2 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/hbase/components/hbrest"
					$Response2 = $webclient.DownloadString($Url2)
					$JsonObject2 = $Response2 | ConvertFrom-Json
					foreach ($host_component in $JsonObject2.host_components)
					{
						$ConnectionString = $host_component.HostRoles.host_name + ":" + $PortNumber
						Write-host $ConnectionString
					}
				}
				if($PropertyName -eq "FQDNSuffix")
				{
					$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/yarn/components/resourcemanager"
					$Response = $webclient.DownloadString($Url)
					$JsonObject = $Response | ConvertFrom-Json
					$FQDN = $JsonObject.host_components[0].HostRoles.host_name
					$pos = $FQDN.IndexOf(".")
					$Suffix = $FQDN.Substring($pos + 1)
					Write-host $Suffix
				}
			}

		Dopo l'esecuzione dello script di PowerShell, usare il comando seguente per restituire il suffisso DNS, usando la funzione Get-ClusterDetail. Quando si usa il comando, specificare il nome del cluster HBase di HDInsight e il nome e la password dell'amministratore.

			Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>

		Questo restituirà il suffisso DNS. Ad esempio, **yourclustername.b4.internal.cloudapp.net**.

	> [AZURE.NOTE] Per ottenere il suffisso DNS, è anche possibile usare Desktop remoto per connettere il cluster HBase (si verrà connessi al nodo head) ed eseguire **ipconfig** da un prompt dei comandi. Per istruzioni sull'abilitazione di RDP e sulla connessione al cluster tramite RDP, vedere [Gestire cluster Hadoop in HDInsight tramite il portale di gestione di Azure][hdinsight-admin-portal].
	>
	> ![hdinsight.hbase.dns.surffix][img-dns-surffix]


<!-- 
3.	Cambiare la configurazione del suffisso DNS primario della macchina virtuale. Ciò permette alla macchina virtuale di risolvere automaticamente il nome host del cluster HBase, senza specificare esplicitamente il suffisso. Ad esempio, il nome host *workernode0* sarà risolto correttamente nel workernode0 del cluster HBase. 

	Per modificare la configurazione:

	1. Effettuare una connessione RDP alla VM. 
	2. Aprire l'**Editor Criteri di gruppo locali**. Il file eseguibile è gpedit.msc.
	3. Espandere **Configurazione computer**, quindi **Modelli amministrativi**, **Rete** e infine fare clic su **Client DNS**. 
	- Impostare **Suffisso DNS primario** sul valore ottenuto nel passaggio 2: 

		![hdinsight.hbase.primary.dns.suffix][img-primary-dns-suffix]
	4. Fare clic su **OK**. 
	5. Riavviare la macchina virtuale.
-->

Per verificare che la macchina virtuale possa comunicare con il cluster HBase, usare il comando  `ping headnode0.<dns suffix>` dalla macchina virtuale. Ad esempio, ping headnode0.mycluster.b1.cloudapp.net

Per usare queste informazioni in un'applicazione Java e creare un'applicazione, è possibile seguire la procedura descritta in [Usare Maven per compilare applicazioni Java che usano HBase con HDInsight (Hadoop)](azure.microsoft.com/it-it/documentation/articles/hdinsight-hbase-build-java-maven/). Per fare in modo che l'applicazione si connetta a un server HBase remoto, modificare il file **hbase-site.xml** in questo esempio, in modo che usi il nome di dominio completo (FQDN) per ZooKeeper. Ad esempio:

	<property>
    	<name>hbase.zookeeper.quorum</name>
    	<value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
	</property>

> [AZURE.NOTE] Per altre informazioni sulla risoluzione dei nomi in Reti virtuali di Azure, comprese quelle relative all'uso del proprio server DNS, vedere [Risoluzione dei nomi (DNS)](http://msdn.microsoft.com/library/azure/jj156088.aspx).

##<a id="powershell"></a>Effettuare il provisioning di un cluster HBase tramite Azure PowerShell

**Per effettuare il provisioning di un cluster HBase tramite Azure PowerShell:**

1. Aprire PowerShell ISE.
2. Copiare e incollare il codice seguente nel riquadro dello script.

		$hbaseClusterName = "<HBaseClusterName>"
		$hadoopUserName = "<HBaseClusterUsername>"
		$hadoopUserPassword = "<HBaseClusterUserPassword>"
		$location = "<HBaseClusterLocation>"  #i.e. "West US"
		$clusterSize = <HBaseClusterSize>  
		$vnetID = "<AzureVirtualNetworkID>"
		$subNetName = "<AzureVirtualNetworkSubNetName>"
		$storageAccountName = "<AzureStorageAccountName>" # Do not use the full name here
		$storageAccountKey = "<AzureStorageAccountKey>"
		$storageContainerName = "<AzureBlobStorageContainer>"
		
		$password = ConvertTo-SecureString $hadoopUserPassword -AsPlainText -Force
		$creds = New-Object System.Management.Automation.PSCredential ($hadoopUserName, $password) 
		
		New-AzureHDInsightCluster -Name $hbaseClusterName `
		                          -ClusterType HBase `
		                          -Version 3.1 `
		                          -Location $location `
		                          -ClusterSizeInNodes $clusterSize `
		                          -Credential $creds `
		                          -VirtualNetworkId $vnetID `
		                          -SubnetName $subNetName `
		                          -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
		                          -DefaultStorageAccountKey $storageAccountKey `
		                          -DefaultStorageContainerName $storageContainerName


3. Fare clic su **Esegui script** o premere **F5**.
4. Per convalidare il cluster, è possibile verificarlo dal portale di gestione oppure eseguire il cmdlet di PowerShell seguente nel riquadro in basso:

	Get-AzureHDInsightCluster 

##<a id="nextsteps"></a>Passaggi successivi

In questa esercitazione si è appreso come eseguire il provisioning di un cluster HBase. Per altre informazioni, vedere:

- [Introduzione all'uso di Azure HDInsight][hdinsight-get-started]
- [Effettuare il provisioning di cluster Hadoop in HDInsight][hdinsight-provision] 
- [Introduzione all'uso di HBase con Hadoop in HDInsight][hbase-get-started]
- [Analisi dei sentimenti Twitter con HBase in HDInsight][hbase-twitter-sentiment]
- [Panoramica di Rete virtuale][vnet-overview].


[1]: http://azure.microsoft.com/services/virtual-network/
[2]: http://technet.microsoft.com/library/ee176961.aspx
[3]: http://technet.microsoft.com/library/hh847889.aspx

[hbase-get-started]: ../hdinsight-hbase-get-started/
[hbase-twitter-sentiment]: ../hdinsight-hbase-twitter-sentiment/
[vnet-overview]: http://msdn.microsoft.com/library/azure/jj156007.aspx
[vm-create]: ../virtual-machines-windows-tutorial/

[azure-portal]: https://manage.windowsazure.com
[azure-create-storageaccount]: ../storage-create-storage-account/ 
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-admin-portal]: ../hdinsight-administer-use-management-portal/#rdp

[hdinsight-powershell-reference]: http://msdn.microsoft.com/library/windowsazure/dn479228.aspx


[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter


[powershell-install]: ../install-configure-powershell


[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-storage-powershell]: ../hdinsight-use-blob-storage/#powershell
[hdinsight-analyze-flight-delay-data]: ../hdinsight-analyze-flight-delay-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-use-sqoop]: ../hdinsight-use-sqoop/
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query/
[hdinsight-hive-odbc]: ../hdinsight-connect-excel-hive-ODBC-driver/

[img-dns-surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
[img-primary-dns-suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png
[img-provision-cluster-page1]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png "Provision details for the new HBase cluster"
[img-provision-cluster-page5]: ./media/hdinsight-hbase-provision-vnet/hbasewizard5.png "Use Script Action to customize an HBase cluster"
<!--HONumber=42-->
