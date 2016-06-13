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
   ms.date="05/27/2016"
   ms.author="jgao"/>

# Effettuare il provisioning di cluster HBase in Rete virtuale di Azure

Questo articolo illustra come creare cluster HBase di Azure HDInsight in [Rete virtuale di Azure][1].

> [AZURE.IMPORTANT] I passaggi descritti in questo documento usano il portale di Azure classico. Per la creazione di nuovi servizi, Microsoft non consiglia di usare il portale classico. Per una spiegazione dei vantaggi del portale di Azure, vedere [Portale di Microsoft Azure](https://azure.microsoft.com/features/azure-portal/).
>
> Questo documento include anche informazioni sull'uso di Azure PowerShell. I frammenti di codice indicati si basano su comandi che usano Azure Service Management (ASM) per lavorare con HDInsight e sono __deprecati__. e verranno rimossi dall'1 gennaio 2017.
>
>Per una versione di questo documento che usa il portale di Azure insieme a frammenti di codice di PowerShell che impiegano Azure Resource Manager (ARM), vedere le informazioni su come [eseguire il provisioning di cluster HBase in Rete virtuale di Azure](hdinsight-hbase-provision-vnet.md).

Grazie all'integrazione con la rete virtuale, i cluster HBase possono essere distribuiti nella stessa rete virtuale delle applicazioni, consentendo così alle applicazioni di comunicare direttamente con HBase. Questo approccio offre i vantaggi seguenti:

- Connettività diretta dell'applicazione Web con i nodi del cluster HBase, che consente le comunicazioni tramite le API RPC (Remote Procedure Call) Java di HBase.
- Miglioramento delle prestazioni, poiché il traffico non deve attraversare più gateway e servizi di bilanciamento del carico.
- Possibilità di elaborare le informazioni sensibili in modo più sicuro, senza esporre un endpoint pubblico.

##Prerequisiti
Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Workstation con Azure PowerShell**. Vedere [Installare e usare Azure PowerShell](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/). Per istruzioni, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md). Per eseguire script di Azure PowerShell, è necessario eseguire Azure PowerShell come amministratore e impostare i criteri di esecuzione su *RemoteSigned*. Vedere [Uso del cmdlet Set-ExecutionPolicy][2].

	Prima di eseguire script PowerShell, assicurarsi di essere connessi alla sottoscrizione di Azure usando il seguente cmdlet:

		Add-AzureAccount

	Se si dispone di più sottoscrizioni di Azure, usare il seguente cmdlet per impostare la sottoscrizione corrente:

		Select-AzureSubscription <AzureSubscriptionName>


##Effettuare il provisioning di un cluster HBase in una rete virtuale

Per poter effettuare il provisioning di un cluster HBase, è necessario disporre di una rete virtuale di Azure.

**Per creare una rete virtuale usando il portale di Azure classico**

1. Accedere al [portale di Azure classico][azure-portal].
2. Fare clic su **NUOVO** nell'angolo in basso a sinistra, scegliere **SERVIZI DI RETE**, quindi **RETE VIRTUALE** e infine **CREAZIONE RAPIDA**.
3. Digitare o selezionare i valori seguenti:

	- **Nome**: nome della rete virtuale.
	- **Spazio di indirizzi**: scegliere uno spazio di indirizzi per la rete virtuale con dimensioni sufficienti per fornire indirizzi per tutti i nodi del cluster. In caso contrario, il provisioning avrà esito negativo. Per completare questa esercitazione, è possibile scegliere una qualsiasi delle tre opzioni.
	- **Numero massimo VM**: scegliere uno dei valori disponibili per il numero massimo di macchine virtuali. Questo valore determinerà il numero di host (VM) che è possibile creare nello spazio di indirizzi. Per completare questa esercitazione, è sufficiente specificare **4096 [CIDR: /20]**.
	- **Percorso**: il percorso deve essere uguale a quello del cluster HBase che sarà creato.
	- **Server DNS**: questa esercitazione usa un server DNS (Domain Name System) interno fornito da Azure. È quindi possibile scegliere **Nessuno**. Sono supportate anche configurazioni di rete più avanzate con server DNS personalizzati. Per istruzioni dettagliate, vedere [Risoluzione dei nomi (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
4. Fare clic su **CREA RETE VIRTUALE** nell'angolo inferiore destro. Il nome della rete virtuale sarà visualizzato nell'elenco. Attendere l'impostazione del valore **Creata** nella colonna Stato.
5. Nel riquadro principale fare clic sulla rete virtuale appena creata.
6. Fare clic su **DASHBOARD** nella parte superiore della pagina.
7. In **riepilogo rapido** annotare l'ID della rete virtuale, che sarà necessario per il provisioning del cluster HBase.
8. Fare clic su **CONFIGURA** nella parte superiore della pagina.
9. Nella parte inferiore della pagina il nome predefinito per la subnet è **Subnet-1**. Se si vuole, è possibile rinominare la subnet o aggiungere una nuova subnet per il cluster HBase. Annotare il nome della subnet, poiché sarà necessario durante il provisioning del cluster.
10. Verificare il valore di **CIDR (CONTEGGIO INDIRIZZI)** per la subnet che sarà usata per il cluster. Il conteggio di indirizzi deve essere superiore al numero di nodi di lavoro più sette (gateway: 2, nodo head: 2, zookeeper: 3). Ad esempio, se è necessario un cluster HBase da 10 nodi, il conteggio di indirizzi per la subnet dovrà essere maggiore di 17 (10+7). In caso contrario, la distribuzione avrà esito negativo.
11. Fare clic su **Salva** nella parte inferiore della pagina, se i valori della subnet sono stati aggiornati.


**Per aggiungere una macchina virtuale del server DNS alla rete virtuale**

Sebbene il server DNS sia generalmente facoltativo, in alcuni casi risulta necessario. La procedura è documentata nell'articolo [Configurare DNS tra due reti virtuali di Azure][hdinsight-hbase-replication-dns]. Sostanzialmente, è necessario eseguire i passaggi seguenti:

1. Aggiungere una macchina virtuale di Azure alla rete virtuale
2. Impostare un indirizzo IP statico per la macchina virtuale
3. Aggiungere il ruolo del server DNS alla macchina virtuale
4. Assegnare il server DNS alla rete virtuale


**Per creare un account di archiviazione di Azure e un contenitore di archiviazione BLOB che potranno essere usati dal cluster:**

> [AZURE.NOTE] I cluster HDInsight usano l'archiviazione BLOB di Azure per l'archiviazione dei dati. Per altre informazioni, vedere [Usare l'archiviazione BLOB di Azure con Hadoop in HDInsight](hdinsight-hadoop-use-blob-storage.md). Saranno necessari un account di archiviazione e un contenitore di archiviazione BLOB. Il percorso dell'account di archiviazione deve corrispondere al percorso della rete virtuale e al percorso del cluster.

Come altri cluster HDInsight, anche il cluster HBase richiede un account di archiviazione di Azure e un contenitore di archiviazione BLOB come file system predefinito. Il percorso dell'account di archiviazione deve corrispondere al percorso della rete virtuale e al percorso del cluster. Per altre informazioni, vedere [Usare l'archiviazione BLOB di Azure con Hadoop in HDInsight][hdinsight-storage]. Quando si effettua il provisioning di un cluster HBase, è possibile scegliere se crearne di nuovi o usarne di esistenti. Questa procedura mostra come creare un account di archiviazione e un contenitore di archiviazione BLOB tramite il portale di Azure classico.

1. Accedere al [portale di Azure classico][azure-portal].
2. Fare clic su **NUOVO** nell'angolo inferiore sinistro, selezionare **SERVIZI DATI**, quindi **ARCHIVIAZIONE** e infine **CREAZIONE RAPIDA**.

3. Digitare o selezionare i valori seguenti:

	- **URL**: nome dell'account di archiviazione.
	- **PERCORSO**: percorso dell'account di archiviazione. Assicurarsi che corrisponda al percorso della rete virtuale. I gruppi di affinità non sono supportati.
	- **REPLICA**: per finalità di testing, usare **Localmente ridondante** per ridurre i costi.

4. Fare clic su **CREA ACCOUNT DI ARCHIVIAZIONE**. Il nuovo account di archiviazione verrà incluso nell'elenco di archiviazione.
5. Attendere che il valore di **STATO** per il nuovo account di archiviazione sia modificato in **Online**.
6. Fare clic sul nuovo account di archiviazione nell'elenco per selezionarlo.
7. Fare clic su **MANAGE ACCESS KEYS** nella parte inferiore della pagina.
8. Prendere nota del nome dell'account di archiviazione e della chiave di accesso primaria o di quella secondaria (funzionano entrambe le chiavi). Sarà necessario usarli più avanti nell'esercitazione.
9. Nella parte superiore della pagina fare clic su **CONTENITORE**.
10. Fare clic su **AGGIUNGI** nella parte inferiore della pagina.
11. Immettere il nome del contenitore. Il contenitore sarà usato come contenitore predefinito per il cluster HBase. Per impostazione predefinita, il nome del contenitore corrisponde al nome del cluster. Lasciare il campo **ACCESSO** impostato su **Privato**.  
12. Fare clic sul segno di spunta per creare il contenitore.

**Per effettuare il provisioning di un cluster HBase usando il portale di Azure classico**

> [AZURE.NOTE] Per informazioni su come effettuare il provisioning di un nuovo cluster HBase usando Azure PowerShell, vedere [Effettuare il provisioning di un cluster HBase tramite Azure PowerShell](#powershell).

1. Accedere al [portale di Azure classico][azure-portal].

2. Fare clic su **NUOVO** nell'angolo inferiore sinistro, selezionare **SERVIZI DATI**, quindi **HDINSIGHT** e infine fare clic su **CREAZIONE PERSONALIZZATA**.

3. Immettere un nome di cluster, selezionare HBase come tipo di cluster e Windows Server 2012 come sistema operativo, selezionare la versione di HDInsight desiderata e fare clic sul pulsante a destra.

	![Specificare dettagli per il cluster HBase][img-provision-cluster-page1]


	> [AZURE.NOTE] Per un cluster HBase, Windows Server è l'unica opzione disponibile tra i sistemi operativi.

4. Nella pagina **Configura cluster** immettere o selezionare quanto segue:

	![Specificare dettagli per il cluster HBase](./media/hdinsight-hbase-provision-vnet/hbasewizard2.png)

	<table border='1'>
		<tr><th>Proprietà</th><th>Valore</th></tr>
		<tr><td>Nodi dati</td><td>Selezionare il numero di dati che si desidera distribuire. Ai fini di test, creare un cluster a singolo nodo. <br />Il limite relativo alle dimensioni del cluster dipende dalla sottoscrizione di Azure. Per aumentare il limite, contattare il team del supporto fatturazione di Azure.</td></tr>
		<tr><td>Area/Rete virtuale</td><td><p>Selezionare un'area o una rete virtuale di Azure, se ne è già stata creata una. Per questa esercitazione, selezionare la rete creata in precedenza e selezionare quindi una subnet corrispondente. Il nome predefinito è <b>Subnet-1</b>.</p></td></tr>
		<tr><td>Dimensione nodo head</td><td><p>Selezionare una dimensione di macchina virtuale per il nodo head.</p></td></tr>
		<tr><td>Dimensione nodo dati</td><td><p>Selezionare una dimensione di macchina virtuale per il nodo dati.</p></td></tr>
		<tr><td>Dimensione Zookeeper</td><td><p>Selezionare una dimensione di macchina virtuale per il nodo Zookeeper.</p></td></tr>
	</table>

	>[AZURE.NOTE] Il costo può variare in base alla scelta delle macchine virtuali. HDInsight usa tutte macchine virtuali di livello standard per i nodi del cluster. Per informazioni sul modo in cui le dimensioni delle macchine virtuali influiscono sui prezzi, vedere <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">Prezzi di HDInsight</a>.

	Fare clic sul pulsante a destra.

5. Immettere il nome utente e la password di Hadoop da usare per il cluster, quindi fare clic sul pulsante a destra.

	![Specificare l'account di archiviazione per il cluster Hadoop HDInsight](./media/hdinsight-hbase-provision-vnet/hbasewizard3.png)

	<table border='1'>
		<tr><th>Proprietà</th><th>Valore</th></tr>
		<tr><td>Nome utente HTTP</td>
			<td>Specificare il nome utente del cluster HDInsight.</td></tr>
		<tr><td>Password HTTP/Conferma password</td>
			<td>Specificare la password utente del cluster HDInsight.</td></tr>
		<tr><td>Abilita Desktop remoto per il cluster</td>
			<td>Selezionare questa casella di controllo per specificare un nome utente, una password e una data di scadenza per un desktop remoto in grado di accedere in remoto ai nodi del cluster al termine del provisioning. È possibile abilitare Desktop remoto anche in un secondo momento, al termine del provisioning del cluster. Per istruzioni, vedere <a href="hdinsight-administer-use-management-portal/#rdp" target="_blank">Connettersi a cluster HDInsight tramite RDP</a>.</td></tr>
	</table>

6. Nella pagina **Account di archiviazione** specificare i valori seguenti:

    ![Specificare l'account di archiviazione per il cluster Hadoop HDInsight](./media/hdinsight-hbase-provision-vnet/hbasewizard4.png)

	<table border='1'>
		<tr><th>Proprietà</th><th>Valore</th></tr>
		<tr><td>Account di archiviazione</td>
			<td>Specificare l'account di archiviazione di Azure che verrà usato come file system predefinito per il cluster HDInsight. È possibile scegliere una delle tre opzioni seguenti:
			<ul>
				<li><strong>Usare l'archiviazione esistente</strong></li>
				<li><strong>Crea nuova archiviazione</strong></li>
				<li><strong>Usare l'archiviazione da un'altra sottoscrizione</strong></li>
			</ul>
			</td></tr>
		<tr><td>Nome account</td>
			<td><ul>
				<li>Se si sceglie di usare l'archiviazione esistente, per il campo <strong>Nome account</strong> selezionare un account di archiviazione esistente. Nella casella di riepilogo vengono elencati solo gli account di archiviazione che si trovano nello stesso data center in cui si è scelto di effettuare il provisioning del cluster.</li>
				<li>Se si sceglie l'opzione <strong>Crea nuova archiviazione</strong> o <strong>Usare l'archiviazione da un'altra sottoscrizione</strong> è necessario indicare il nome dell'account di archiviazione.</li>
			</ul></td></tr>
		<tr><td>Chiave account</td>
			<td>Se si sceglie l'opzione <strong>Usare l'archiviazione da un'altra sottoscrizione</strong>, specificare la chiave dell'account relativa a tale account di archiviazione.</td></tr>
		<tr><td>Contenitore predefinito</td>
			<td><p>Specificare il contenitore predefinito nell'account di archiviazione usato come file system predefinito per il cluster HDInsight. Se si sceglie <strong>Usare l'archiviazione esistente</strong> per il campo <strong>Account di archiviazione</strong> e nell'account non è presente alcun contenitore, per impostazione predefinita il contenitore verrà creato con lo stesso nome del cluster. Se esiste già un contenitore con il nome del cluster, al nome del contenitore verrà aggiunto un numero di sequenza. Ad esempio, mycontainer1, mycontainer2 e così via. Tuttavia, se in un account di archiviazione esistente è presente un contenitore con un nome diverso da quello del cluster specificato, si potrà usare anche tale contenitore.</p>
	        <p>Se si è scelto di creare una nuova risorsa di archiviazione da un'altra sottoscrizione di Azure è necessario specificare il nome del contenitore predefinito.</p>
	    </td></tr>
		<tr><td>Account di archiviazione aggiuntivi</td>
			<td>Se necessario, specificare account di archiviazione aggiuntivi per il cluster. HDInsight supporta più account di archiviazione. Un cluster può usare un numero illimitato di account di archiviazione aggiuntivi. Se tuttavia si crea un cluster tramite il portale di Azure classico, esiste un limite di sette dovuto ai vincoli dell'interfaccia utente. Per ogni nuovo account di archiviazione specificato viene aggiunta un'altra pagina <strong>Account di archiviazione</strong> alla procedura guidata, in cui è possibile specificare le informazioni sull'account. Nella schermata precedente, ad esempio, non è stato selezionato un account di archiviazione aggiuntivo e, quindi, alla procedura guidata non è stata aggiunta alcuna nuova pagina.</td></tr>
	</table>

	Fare clic sulla freccia destra.

7. Nella pagina **Azioni script** selezionare il segno di spunta nell'angolo inferiore destro. Non fare clic sul pulsante che consente di **aggiungere azioni di script**, poiché questa esercitazione non richiede una configurazione cluster personalizzata.

	![Configurare l'azione di script per personalizzare un cluster HBase di HDInsight][img-provision-cluster-page5]

	> [AZURE.NOTE] Questa pagina consente di personalizzare il cluster durante la procedura di configurazione. Per altre informazioni, vedere [Personalizzare cluster HDInsight mediante le azioni script](hdinsight-hadoop-customize-cluster.md).

Per iniziare a lavorare con il nuovo cluster HBase, è possibile usare le procedure disponibili in [Introduzione a HBase con Hadoop in HDInsight](hdinsight-hbase-tutorial-get-started.md).

##Eseguire la connessione al cluster HBase con provisioning nella rete virtuale tramite le API RPC Java di HBase

1.	Effettuare il provisioning di una macchina virtuale IaaS (Infrastructure as a Service ) nella stessa rete virtuale di Azure e nella stessa subnet. In questo modo, sia la macchina virtuale che il cluster HBase useranno lo stesso server DNS interno per risolvere i nomi degli host. Per ottenere questo risultato, è necessario scegliere l'opzione **Da raccolta** e selezionare la rete virtuale anziché un data center. Per istruzioni, vedere [Creazione di una macchina virtuale che esegue Windows Server](../virtual-machines/virtual-machines-windows-hero-tutorial.md). È sufficiente un'immagine standard di Windows Server 2012 con una VM di dimensioni ridotte.

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

	> [AZURE.NOTE] Per ottenere il suffisso DNS, è anche possibile usare Desktop remoto per connettersi al cluster HBase (si verrà connessi al nodo head) ed eseguire **ipconfig** da un prompt dei comandi. Per istruzioni sull'abilitazione di RDP (Remote Desktop Protocol) e sulla connessione al cluster tramite RDP, vedere [Gestire cluster Hadoop in HDInsight tramite il portale di Azure classico][hdinsight-admin-portal].
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

##Effettuare il provisioning di un cluster HBase tramite Azure PowerShell

**Per effettuare il provisioning di un cluster HBase tramite Azure PowerShell**

1. Aprire Azure PowerShell Integrated Scripting Environment (ISE).
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
4. Per convalidare il cluster, è possibile verificarlo dal portale di Azure classico oppure eseguire il cmdlet di Azure PowerShell seguente nel riquadro in basso.

	Get-AzureHDInsightCluster

##Passaggi successivi

In questa esercitazione si è appreso come effettuare il provisioning di un cluster HBase. Per ulteriori informazioni, vedere:

- [Introduzione all'uso di HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
- [Configurare la replica di HBase in HDInsight](hdinsight-hbase-geo-replication.md)
- [Effettuare il provisioning di cluster Hadoop in HDInsight](hdinsight-provision-clusters.md)
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

[azure-portal]: https://management.windowsazure.com
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md#rdp

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx


[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter


[powershell-install]: ../powershell-install-configure.md


[hdinsight-customize-cluster]: hdinsight-hadoop-customize-cluster.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage-powershell]: hdinsight-hadoop-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-DNS.md

[img-dns-surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
[img-primary-dns-suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png
[img-provision-cluster-page1]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png "Dettagli di provisioning per il nuovo cluster HBase"
[img-provision-cluster-page5]: ./media/hdinsight-hbase-provision-vnet/hbasewizard5.png "Usare azioni di script per personalizzare un cluster?"

<!---HONumber=AcomDC_0601_2016-->