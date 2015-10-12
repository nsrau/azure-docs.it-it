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
   ms.date="08/12/2015"
   ms.author="jgao"/>

# Effettuare il provisioning di cluster HBase in Rete virtuale di Azure 

Questo articolo illustra come creare cluster HBase di Azure HDInsight in [Rete virtuale di Azure][1].

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [Provisioning di cluster HBase nella rete virtuale di Azure](hdinsight-hbase-provision-vnet-v1.md)

Grazie all'integrazione con la rete virtuale, i cluster HBase possono essere distribuiti nella stessa rete virtuale delle applicazioni, consentendo così alle applicazioni di comunicare direttamente con HBase. Questo approccio offre i vantaggi seguenti:

- Connettività diretta dell'applicazione Web con i nodi del cluster HBase, che consente le comunicazioni tramite le API RPC (Remote Procedure Call) Java di HBase.
- Miglioramento delle prestazioni, poiché il traffico non deve attraversare più gateway e servizi di bilanciamento del carico.
- Possibilità di elaborare le informazioni sensibili in modo più sicuro, senza esporre un endpoint pubblico.

##Prerequisiti
Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Workstation con Azure PowerShell**. Vedere [Installare e usare Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/). Per istruzioni, vedere [Come installare e configurare Azure PowerShell](../install-configure-powershell.md). Per eseguire script di Azure PowerShell, è necessario eseguire Azure PowerShell come amministratore e impostare i criteri di esecuzione su *RemoteSigned*. Vedere [Uso del cmdlet Set-ExecutionPolicy][2].

	Prima di eseguire script PowerShell, assicurarsi di essere connessi alla sottoscrizione di Azure usando il seguente cmdlet:

		Add-AzureAccount

	Se si dispone di più sottoscrizioni di Azure, usare il seguente cmdlet per impostare la sottoscrizione corrente:

		Select-AzureSubscription <AzureSubscriptionName>


## Effettuare il provisioning di un cluster HBase in una rete virtuale

Le applicazioni sono in genere costituite da molti componenti. In questa esercitazione sono presenti:

- una rete virtuale di Azure
- un Account di archiviazione di Azure
- un Cluster HBase di HDInsight di Azure
- (facoltativo) una macchina virtuale Azure che funge da server DNS

Gestione risorse di Azure consente di lavorare con le risorse dell'applicazione come gruppo. È quindi possibile distribuire, aggiornare o eliminare tutte le risorse per l'applicazione mediante un'unica operazione coordinata. È possibile descrivere le risorse del gruppo in un modello JSON per la distribuzione e quindi usare tale modello per ambienti diversi, ad esempio di testing, gestione temporanea e produzione. È possibile chiarire la fatturazione per l'organizzazione visualizzando i costi per l'intero gruppo.

**Per creare un gruppo di risorse**

1. Accedere al [portale di anteprima di Azure](https://portal.azure.com).
2. Fare clic su **NUOVO**, fare clic su **Gestione**, quindi fare clic su **Gruppo di risorse**.
3. Digitare o selezionare i valori seguenti:

	- **Nome gruppo di risorse**: immettere un nome per il gruppo di risorse.
	- **Sottoscrizione**: selezionare la sottoscrizione di Azure utilizzata per questo gruppo di risorse.
	- **Percorso gruppo di risorse**: selezionare un data center di Azure. Questo percorso non deve necessariamente corrispondere al percorso del cluster HDInsight.

4. Fare clic su **Crea**.

Per poter effettuare il provisioning di un cluster HBase, è necessario disporre di una rete virtuale di Azure.

**Per creare una rete virtuale usando il portale di Azure**

1. Accedere al [portale di anteprima](https://portal.azure.com).
2. Fare clic su **NUOVO**, fare clic su **Rete**, quindi fare clic su **Rete virtuale**.
3. In **Seleziona un modello di distribuzione**, selezionare **Classico**, quindi fare clic su **Crea**.

	> [AZURE.NOTE]Non è possibile utilizzare una rete virtuale di Azure v1 (classica) con HDInsight. La rete virtuale deve essere v2 (Gestione risorse di Azure) per poter essere elencata come opzione durante il processo di creazione di cluster HDInsight nel portale di anteprima di Azure o possa essere usata durante la creazione di un cluster mediante l'interfaccia della riga di comando di Azure o Azure PowerShell.
> 
> Se si dispone di risorse in una rete v1 e si desidera rendere HDInsight direttamente accessibile a tali risorse attraverso una rete virtuale, vedere [Connessione di reti virtuali classiche a nuove reti virtuali](../virtual-network/virtual-networks-arm-asm-s2s.md) per informazioni su come connettersi una rete virtuale v2 a una rete virtuale v1. Una volta stabilita la connessione, è possibile creare il cluster HDInsight nella rete virtuale v2.

4. Digitare o selezionare i valori seguenti:

	- **Nome**: nome della rete virtuale.
	- **Spazio degli indirizzi**: scegliere uno spazio degli indirizzi per la rete virtuale di dimensioni sufficienti per fornire indirizzi per tutti i nodi del cluster. In caso contrario, il provisioning avrà esito negativo. Per questa esercitazione, è possibile utilizzare i valori predefiniti. Fare clic su **OK** per salvare le modifiche.
	- **Gruppo di risorse**: selezionare il gruppo di risorse creato in precedenza nell'esercitazione.
	- In **Sottoscrizione** selezionare la sottoscrizione di Azure che si desidera utilizzare per rete virtuale.
	- **Percorso**: il percorso deve essere uguale a quello del cluster HBase che sarà creato.

5. Fare clic su **Crea**.

Per impostazione predefinita, la rete virtuale utilizza un server Domain Name System (DNS) interno fornito da Azure. Sono supportate anche configurazioni di rete più avanzate con server DNS personalizzati. Per istruzioni dettagliate, vedere [Risoluzione dei nomi (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

**(Facoltativo) Per aggiungere una macchina virtuale del server DNS alla rete virtuale**

Sebbene il server DNS sia generalmente facoltativo, in alcuni casi risulta necessario. La procedura è documentata nell'articolo [Configurare DNS tra due reti virtuali di Azure][hdinsight-hbase-replication-dns]. Sostanzialmente, è necessario eseguire i passaggi seguenti:

1. Aggiungere una macchina virtuale di Azure alla rete virtuale
2. Impostare un indirizzo IP statico per la macchina virtuale
3. Aggiungere il ruolo del server DNS alla macchina virtuale
4. Assegnare il server DNS alla rete virtuale

**Per effettuare il provisioning di un cluster HBase usando il portale di Azure**

> [AZURE.NOTE]Per informazioni su come effettuare il provisioning di un nuovo cluster HBase usando Azure PowerShell, vedere [Effettuare il provisioning di un cluster HBase tramite Azure PowerShell](#powershell).


**Per creare un cluster HDInsight**

1. Accedere al [portale di anteprima di Azure](https://portal.azure.com).
2. Fare clic su **NUOVO**, su **Analisi dei dati**, quindi su **HDInsight**.

    ![Creazione di un nuovo cluster nel portale di anteprima di Azure](./media/hdinsight-provision-clusters/HDI.CreateCluster.1.png "Creazione di un nuovo cluster nel portale di Anteprima di Azure")

3. Digitare o selezionare i valori seguenti:

  - **Nome del cluster**: Immettere un nome per il cluster. Un segno di spunta verde verrà visualizzato accanto al nome del cluster, se disponibile.
  - **Tipo di cluster**: selezionare **HBase**.
  - **Sistema operativo cluster**: selezionare **Windows Server 2012 R2 Datacenter**.
  - **Sottoscrizione**: selezionare la sottoscrizione di Azure che verrà usata per effettuare il provisioning del cluster.
  - **Gruppo di risorse**: selezionare il gruppo di risorse creato in precedenza nell'esercitazione.
  - **Credenziali**: configurare il nome utente e la password per l'utente di Hadoop (utente HTTP). Se si abilita desktop remoto per il cluster, è necessario configurare il nome utente di desktop remoto utente e password e la data di scadenza dell’account. Fare clic su **Seleziona** in basso per salvare le modifiche.
  - **Origine dati**: selezionare un nuovo o selezionare un account di archiviazione di Azure esistente da utilizzare come file system predefinito per il cluster. Per impostazione predefinita, il nome del contenitore corrisponde al nome del cluster. Il percorso dell'account di archiviazione determina anche il percorso del cluster.
  - **Piano tariffario del nodo**: A scopo di valutazione o di formazione, selezionare 1 nodo area per ridurre al minimo il costo.

  	- **Metodo di selezione**: impostare questa proprietà su **Da tutte le sottoscrizioni** per consentire l'esplorazione di account di archiviazione da tutte le sottoscrizioni. Impostare questa proprietà su **Tasto di scelta** se si desidera immettere il **Nome di archiviazione** e il **Tasto di scelta** di un account di archiviazione esistente.
  	- **Seleziona account di archiviazione / Crea nuovo**: fare clic su **Seleziona account di archiviazione** per cercare e selezionare un account di archiviazione esistente da associare al cluster. Fare clic su **Crea nuovo** per creare un nuovo account di archiviazione. Utilizzare il campo che viene visualizzato per immettere il nome dell'account di archiviazione. Se il nome è disponibile, verrà visualizzato un segno di spunta verde.
    - **Scegli contenitore predefinito**: utilizzare questa opzione per immettere il nome del contenitore predefinito da utilizzare per il cluster. È possibile immettere qualsiasi nome, è consigliabile utilizzare lo stesso nome del cluster in modo che sia facilmente intuibile che il contenitore viene utilizzato per tale cluster specifico.
  	- **Percorso**: l'area geografica dove si trova o dove verrà creato l'account di archiviazione. Questo percorso determina il percorso del cluster. Il cluster e l'account di archiviazione predefinito devono trovarsi nello stesso data center di Azure.

  - **Livelli di prezzi nodo**: Impostare il numero di nodi del ruolo di lavoro necessari per il cluster. Verrà visualizzato il costo stimato del cluster all'interno del pannello.
	- **Configurazione facoltativa**: per questa esercitazione, è sufficiente configurare **Rete virtuale**. Selezionare la rete virtuale creata in precedenza nell'esercitazione. Assicurarsi di selezionare anche una subnet.

4. Fare clic su **Crea**.


Per iniziare a lavorare con il nuovo cluster HBase, è possibile usare le procedure disponibili in [Introduzione a HBase con Hadoop in HDInsight](../hdinsight-hbase-get-started.md).

##Eseguire la connessione al cluster HBase con provisioning nella rete virtuale tramite le API RPC Java di HBase

1.	Effettuare il provisioning di una macchina virtuale IaaS (Infrastructure as a Service ) nella stessa rete virtuale di Azure e nella stessa subnet. In questo modo, sia la macchina virtuale che il cluster HBase useranno lo stesso server DNS interno per risolvere i nomi degli host. Per ottenere questo risultato, è necessario scegliere l'opzione **Da raccolta** e selezionare la rete virtuale anziché un data center. Per istruzioni, vedere [Creazione di una macchina virtuale che esegue Windows Server](../virtual-machines-windows-tutorial.md). È sufficiente un'immagine standard di Windows Server 2012 con una VM di dimensioni ridotte.

2.	Quando si usa un'applicazione Java per connettersi a HBase da remoto, è necessario usare il nome di dominio completo (FQDN). Per determinare quest'ultimo, è necessario ottenere il suffisso DNS specifico della connessione del cluster HBase. A questo scopo, usare Curl per eseguire query in Ambari oppure Desktop remoto per connettersi al cluster.

	* **Curl** - Usare il comando seguente:

			curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

		Nei dati JSON (JavaScript Object Notation) restituiti, trovare la voce "host_name". Questa conterrà il nome di dominio completo (FQDN) per i nodi nel cluster. Ad esempio:

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

	> [AZURE.NOTE]Per ottenere il suffisso DNS, è anche possibile usare Desktop remoto per connettersi al cluster HBase (si verrà connessi al nodo head) ed eseguire **ipconfig** da un prompt dei comandi. Per istruzioni sull'abilitazione di RDP (Remote Desktop Protocol) e sulla connessione al cluster tramite RDP, vedere [Gestire cluster Hadoop in HDInsight tramite il portale di gestione di Azure][hdinsight-admin-portal].
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

> [AZURE.NOTE]Per altre informazioni sulla risoluzione dei nomi in reti virtuali di Azure, comprese quelle relative all'uso del proprio server DNS, vedere [Risoluzione dei nomi (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

##Effettuare il provisioning di un cluster HBase tramite Azure PowerShell

**Per effettuare il provisioning di un cluster HBase tramite Azure PowerShell**

1. Aprire Azure PowerShell Integrated Scripting Environment (ISE).
2. Copiare e incollare il codice seguente nel riquadro dello script.

		$hbaseClusterName = "<HBaseClusterName>"
		$hadoopUserName = "<HBaseClusterUsername>"
		$hadoopUserPassword = "<HBaseClusterUserPassword>"
		$location = "<HBaseClusterLocation>"  #i.e. "West US"
		$clusterSize = <HBaseClusterSize>  
		$resourceGroup = "<AzureResourceGroupName>"
		$vnetID = "<AzureVirtualNetworkID>"
		$subNetName = "<AzureVirtualNetworkSubNetName>"
		$storageAccountName = "<AzureStorageAccountName>" # Do not use the full name here
		$storageAccountKey = "<AzureStorageAccountKey>"
		$storageContainerName = "<AzureBlobStorageContainer>"

		$password = ConvertTo-SecureString $hadoopUserPassword -AsPlainText -Force
		$creds = New-Object System.Management.Automation.PSCredential ($hadoopUserName, $password)

		New-AzureHDInsightCluster -ResourceGroupName $resourceGroup `
		                          -ClusterName $hbaseClusterName `
				                    	-ClusterType HBase `
				                    	-Location $location `
				                    	-ClusterSizeInNodes $clusterSize `
		                          -HttpCredential $creds `
				                    	-VirtualNetworkId $vnetID `
				                    	-SubnetName $subNetName `
				                    	-DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
				                    	-DefaultStorageAccountKey $storageAccountKey `
		                          -DefaultStorageContainer $storageContainerName


3. Fare clic su **Esegui script** o premere **F5**.
4. Per convalidare il cluster, è possibile verificarlo dal portale di Azure oppure eseguire il cmdlet di Azure PowerShell seguente nel riquadro in basso.

	Get-AzureHDInsightCluster

##Passaggi successivi

In questa esercitazione si è appreso come effettuare il provisioning di un cluster HBase. Per ulteriori informazioni, vedere:

- [Introduzione all'uso di HDInsight](../hdinsight-get-started.md)
- [Configurare la replica di HBase in HDInsight](hdinsight-hbase-geo-replication.md)
- [Effettuare il provisioning di cluster Hadoop in HDInsight](hdinsight-provision-clusters.md)
- [Introduzione all'uso di HBase con Hadoop in HDInsight](../hdinsight-hbase-get-started.md)
- [Analizzare i sentimenti Twitter con HBase in HDInsight](../hdinsight-hbase-twitter-sentiment.md)
- [Panoramica di Rete virtuale][vnet-overview].


[1]: http://azure.microsoft.com/services/virtual-network/
[2]: http://technet.microsoft.com/library/ee176961.aspx
[3]: http://technet.microsoft.com/library/hh847889.aspx

[hbase-get-started]: ../hdinsight-hbase-get-started.md
[hbase-twitter-sentiment]: ../hdinsight-hbase-twitter-sentiment.md
[vnet-overview]: ../virtual-network/virtual-networks-overview.md
[vm-create]: ../virtual-machines-windows-tutorial.md

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


[powershell-install]: ../install-configure-powershell.md


[hdinsight-customize-cluster]: hdinsight-hadoop-customize-cluster.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-storage-powershell]: ../hdinsight-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-DNS.md

[img-dns-surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
[img-primary-dns-suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png
[img-provision-cluster-page1]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png "Dettagli di provisioning per il nuovo cluster HBase"
[img-provision-cluster-page5]: ./media/hdinsight-hbase-provision-vnet/hbasewizard5.png "Usare azioni di script per personalizzare un cluster?"

[azure-preview-portal]: https://portal.azure.com

<!---HONumber=Oct15_HO1-->