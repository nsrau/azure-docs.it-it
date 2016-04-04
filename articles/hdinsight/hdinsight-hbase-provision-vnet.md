<properties
	pageTitle="Effettuare il provisioning di cluster HBase in una rete virtuale | Microsoft Azure"
	description="Introduzione all'uso di HBase in Azure HDInsight. Informazioni su come creare cluster HBase di HDInsight in Rete virtuale di Azure."
	keywords=""
	services="hdinsight,virtual-network"
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
   ms.date="02/17/2016"
   ms.author="jgao"/>

# Creare cluster HBase nella rete virtuale di Azure 

Questo articolo illustra come creare cluster HBase di Azure HDInsight in [Rete virtuale di Azure][1].

Grazie all'integrazione con la rete virtuale, i cluster HBase possono essere distribuiti nella stessa rete virtuale delle applicazioni, consentendo così alle applicazioni di comunicare direttamente con HBase. Questo approccio offre i vantaggi seguenti:

- Connettività diretta dell'applicazione Web con i nodi del cluster HBase, che consente le comunicazioni tramite le API RPC (Remote Procedure Call) Java di HBase.
- Miglioramento delle prestazioni, poiché il traffico non deve attraversare più gateway e servizi di bilanciamento del carico.
- Possibilità di elaborare le informazioni sensibili in modo più sicuro, senza esporre un endpoint pubblico.

###Prerequisiti
Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Workstation con Azure PowerShell**. Vedere [Installare e usare Azure PowerShell](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

## Creare cluster HBase nella rete virtuale

In questa sezione si creerà un cluster HBase basato su Linux in HDInsight usando il [modello di Gestione risorse di Azure](../resource-group-template-deploy.md). Per questa esercitazione non è necessario conoscere il modello di Gestione risorse di Azure. Per altri metodi di creazione di cluster e per informazioni sulle impostazioni, vedere l'articolo sulla [creazione di cluster HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Per altre informazioni sull'uso di modelli di Gestione risorse di Azure per creare cluster Hadoop in HDInsight, vedere l'articolo relativo alla [creazione di cluster Hadoop in HDInsight tramite modelli di Gestione risorse di Azure](hdinsight-hadoop-create-windows-clusters-arm-templates.md).

1. Fare clic sull'immagine seguente per aprire un modello di Gestione risorse di Azure nel portale di Azure. Il modello di Gestione risorse di Azure è disponibile in un contenitore BLOB pubblico. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-cluster-in-vnet.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/it-IT/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Nel pannello **Parametri** immettere le informazioni seguenti:

    - **ClusterName**: immettere un nome per il cluster Hadoop che verrà creato.
    - **Cluster login name and password**: il nome dell'account di accesso predefinito è **admin**.
    - **SSH username and password**: il nome utente predefinito è **sshuser**. È possibile rinominarlo. 

    Molte proprietà sono state hardcoded nel modello. Ad esempio:
    
    - Location: Stati Uniti orientali
    - Cluster worker node count: 4
    - Default storage account: <Cluster Name>store
    - Virtual network name: <Cluster Name>-vnet
    - Virtual network address space: 10.0.0.0/16
    - Subnet name: predefinito
    - Subnet address range: 10.0.0.0/24

3. Fare clic su **OK** per salvare i parametri.
4. Nel pannello **Distribuzione personalizzata** fare clic sulla casella a discesa **Gruppo di risorse** e quindi scegliere **Nuovo** per creare un nuovo gruppo di risorse. Il gruppo di risorse è un contenitore che raggruppa il cluster, l'account di archiviazione dipendente e altre risorse collegate.
5. Fare clic su **Note legali** e quindi su **Crea**.
6. Fare clic su **Create**. Verrà visualizzato un nuovo riquadro denominato **Invio della distribuzione per Distribuzione modello**. La creazione di un cluster richiede circa 20 minuti. Dopo aver creato il cluster, è possibile fare clic sul pannello del cluster nel portale per aprirlo.

Al termine dell'esercitazione, è consigliabile eliminare il cluster. Con HDInsight, i dati vengono archiviati in Archiviazione di Azure ed è possibile eliminare tranquillamente un cluster quando non viene usato. Vengono addebitati i costi anche per i cluster HDInsight che non sono in uso. Poiché i costi per il cluster sono decisamente superiori a quelli per l'archiviazione, economicamente ha senso eliminare i cluster quando non vengono usati. Per istruzioni sull'eliminazione del cluster, vedere [Gestire cluster Hadoop in HDInsight tramite il portale di Azure](hdinsight-administer-use-management-portal.md#delete-clusters).

Per iniziare a lavorare con il nuovo cluster HBase, è possibile usare le procedure disponibili in [Introduzione a HBase con Hadoop in HDInsight](hdinsight-hbase-tutorial-get-started.md).

##Connettersi al cluster HBase tramite le API RPC Java di HBase

1.	Creare una macchina virtuale IaaS (Infrastructure as a Service ) nella stessa rete virtuale di Azure e nella stessa subnet. In questo modo, sia la macchina virtuale che il cluster HBase useranno lo stesso server DNS interno per risolvere i nomi degli host. Per ottenere questo risultato, è necessario scegliere l'opzione **Da raccolta** e selezionare la rete virtuale anziché un data center. Per istruzioni, vedere [Creazione di una macchina virtuale che esegue Windows Server](../virtual-machines/virtual-machines-windows-hero-tutorial.md). È sufficiente un'immagine standard di Windows Server 2012 con una VM di dimensioni ridotte.

2.	Quando si usa un'applicazione Java per connettersi a HBase da remoto, è necessario usare il nome di dominio completo (FQDN). Per determinare quest'ultimo, è necessario ottenere il suffisso DNS specifico della connessione del cluster HBase. A questo scopo, usare Curl per eseguire query in Ambari oppure Desktop remoto per connettersi al cluster.

	* **Curl** - Usare il comando seguente:

			curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

		Nei dati JSON (JavaScript Object Notation) restituiti, trovare la voce "host\_name". Questa conterrà il nome di dominio completo (FQDN) per i nodi nel cluster. Ad esempio:

			...
			"host_name": "wordkernode0.<clustername>.b1.cloudapp.net
			...

		La porzione del nome di dominio che inizia con il nome del cluster è il suffisso DNS. Ad esempio, mycluster.b1.cloudapp.net.

	* **Azure PowerShell** - Usare lo script di Azure PowerShell seguente per registrare la funzione **Get-ClusterDetail**, che può essere usata per restituire il suffisso DNS.

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
			     Displays information to facilitate an HDInsight cluster-to-cluster scenario within the same virtual network.
				.Description
				 This command shows the following 4 properties of an HDInsight cluster:
				 1. ZookeeperQuorum (supports only HBase type cluster)
					Shows the value of HBase property "hbase.zookeeper.quorum".
				 2. ZookeeperClientPort (supports only HBase type cluster)
					Shows the value of HBase property "hbase.zookeeper.property.clientPort".
				 3. HBaseRestServers (supports only HBase type cluster)
					Shows a list of host FQDNs that run the HBase REST server.
				 4. FQDNSuffix (supports all cluster types)
					Shows the FQDN suffix of hosts in the cluster.
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
			     This command shows the value of HBase property "hbase.zookeeper.quorum".
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
			     This command shows the value of HBase property "hbase.zookeeper.property.clientPort".
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
			     This command shows a list of host FQDNs that run the HBase REST server.
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
			     This command shows the FQDN suffix of hosts in the cluster.
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

		Dopo l'esecuzione dello script di Azure PowerShell, usare il comando seguente per restituire il suffisso DNS tramite la funzione **Get-ClusterDetail**. Quando si usa il comando, specificare il nome del cluster HBase di HDInsight e il nome e la password dell'amministratore.

			Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>

		Questo restituirà il suffisso DNS. Ad esempio, **yourclustername.b4.internal.cloudapp.net**.

	> [AZURE.NOTE] Per ottenere il suffisso DNS, è anche possibile usare Desktop remoto per connettersi al cluster HBase (si verrà connessi al nodo head) ed eseguire **ipconfig** da un prompt dei comandi. Per istruzioni sull'abilitazione di RDP (Remote Desktop Protocol) e sulla connessione al cluster tramite RDP, vedere [Gestire cluster Hadoop in HDInsight tramite il portale di gestione di Azure][hdinsight-admin-portal].
	>
	> ![hdinsight.hbase.dns.surffix][img-dns-surffix]


<!--
3.	Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

	To make the configuration change:

	1. RDP into the virtual machine.
	2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
	3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
	- Set **Primary DNS Suffix** to the value obtained in step 2:

		![hdinsight.hbase.primary.dns.suffix][img-primary-dns-suffix]
	4. Click **OK**.
	5. Reboot the virtual machine.
-->

Per verificare che la macchina virtuale possa comunicare con il cluster HBase, usare il comando `ping headnode0.<dns suffix>` dalla macchina virtuale. Ad esempio, eseguire il ping di headnode0.mycluster.b1.cloudapp.net

Per usare queste informazioni in un'applicazione Java e creare un'applicazione, è possibile seguire i passaggi in [Usare Maven per compilare applicazioni Java che usano HBase con HDInsight (Hadoop)](hdinsight-hbase-build-java-maven.md). Per fare in modo che l'applicazione si connetta a un server HBase remoto, modificare il file **hbase-site.xml** in questo esempio, in modo che usi il nome di dominio completo (FQDN) per Zookeeper. Ad esempio:

	<property>
    	<name>hbase.zookeeper.quorum</name>
    	<value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
	</property>

> [AZURE.NOTE] Per altre informazioni sulla risoluzione dei nomi in reti virtuali di Azure, comprese quelle relative all'uso del proprio server DNS, vedere [Risoluzione dei nomi (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

##Passaggi successivi

In questa esercitazione si è appreso come creare un cluster HBase. Per ulteriori informazioni, vedere:

- [Introduzione all'uso di HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
- [Configurare la replica di HBase in HDInsight](hdinsight-hbase-geo-replication.md)
- [Creare cluster Hadoop in HDInsight](hdinsight-provision-clusters.md)
- [Introduzione all'uso di HBase con Hadoop in HDInsight](hdinsight-hbase-tutorial-get-started.md)
- [Analizzare i sentimenti Twitter con HBase in HDInsight](hdinsight-hbase-analyze-twitter-sentiment.md)
- [Panoramica di Rete virtuale.][vnet-overview]


[1]: http://azure.microsoft.com/services/virtual-network/
[2]: http://technet.microsoft.com/library/ee176961.aspx
[3]: http://technet.microsoft.com/library/hh847889.aspx

[hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[vnet-overview]: ../virtual-network/virtual-networks-overview.md
[vm-create]: ../virtual-machines/virtual-machines-windows-hero-tutorial.md

[azure-portal]: https://portal.azure.com
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md#rdp

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx


[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter


[powershell-install]: powershell-install-configure.md


[hdinsight-customize-cluster]: hdinsight-hadoop-customize-cluster.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage-powershell]: ../hdinsight-hadoop-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-DNS.md

[img-dns-surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
[img-primary-dns-suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png
[img-provision-cluster-page1]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png "Dettagli di provisioning per il nuovo cluster HBase"
[img-provision-cluster-page5]: ./media/hdinsight-hbase-provision-vnet/hbasewizard5.png "Usare azioni di script per personalizzare un cluster?"

[azure-preview-portal]: https://portal.azure.com

<!---HONumber=AcomDC_0323_2016-->