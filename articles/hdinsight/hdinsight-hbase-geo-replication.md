<properties 
   pageTitle="Configurare la replica di HBase tra due data center | Microsoft Azure" 
   description="Informazioni su come configurare la replica di HBase tra due data center e sui casi di utilizzo per la replica del cluster." 
   services="hdinsight,virtual-network" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="07/25/2016"
   ms.author="jgao"/>

# Configurare la replica geografica di HBase in HDInsight

> [AZURE.SELECTOR]
- [Configurare la connettività VPN](../hdinsight-hbase-geo-replication-configure-VNETs.md)
- [Configurare DNS](hdinsight-hbase-geo-replication-configure-DNS.md)
- [Configurare la replica di HBase](hdinsight-hbase-geo-replication.md)
 
Informazioni su come configurare la replica di HBase in due data center. Alcuni casi di utilizzo per la replica di cluster includono:

- Backup e ripristino di emergenza
- Aggregazione dei dati
- Distribuzione dei dati geografici
- Inserimento di dati online combinato con analisi di dati offline

La replica di cluster usa una metodologia con push dell'origine. Un cluster HBase può essere un'origine, una destinazione o può soddisfare entrambi i ruoli in una sola volta. La replica è asincrona e l'obiettivo della replica è la coerenza finale. Quando l'origine riceve una modifica a una famiglia di colonne con la replica abilitata, tale modifica viene propagata a tutti i cluster di destinazione. Quando i dati vengono replicati da un cluster a un altro, viene tenuta traccia del cluster di origine e di tutti i cluster che hanno già usato i dati per evitare i cicli di replica. Per altre informazioni, in questa esercitazione si configurerà una replica origine-destinazione. Per altre topologie di cluster, vedere la [guida di riferimento relativa a Apache HBase](http://hbase.apache.org/book.html#_cluster_replication).

Questa è la terza parte della serie:

- [Configurare una connessione VPN tra due reti virtuali di Azure][hdinsight-hbase-replication-vnet]
- [Configurare il server DNS tra due reti virtuali di Azure][hdinsight-hbase-replication-dns]
- Configurare la replica geografica di HBase (questa esercitazione)

Il diagramma seguente illustra le due reti virtuali e la connettività di rete creata negli articoli su come [configurare una connettività VPN tra due reti virtuali][hdinsight-hbase-geo-replication-vnet] e su come [configurare DNS tra reti virtuali][hdinsight-hbase-replication-dns]\:

![Grafico della rete virtuale di replica di HBase in HDInsight][img-vnet-diagram]

## <a id="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Workstation con Azure PowerShell**.

    Per eseguire script di PowerShell, è necessario eseguire Azure PowerShell come amministratore e impostare i criteri di esecuzione su *RemoteSigned*. Vedere Uso del cmdlet Set-ExecutionPolicy.
	
	[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

- **Due reti virtuali di Azure con connettività VPN e con DNS configurato**. Per istruzioni, vedere l'articolo [Configurare una connessione VPN tra due reti virtuali di Azure][hdinsight-hbase-replication-vnet] e [Configurare DNS tra due reti virtuali di Azure][hdinsight-hbase-replication-dns].


	Prima di eseguire script PowerShell, assicurarsi di essere connessi alla sottoscrizione di Azure utilizzando il seguente cmdlet:

		Add-AzureAccount

	Se si dispone di più sottoscrizioni di Azure, usare il seguente cmdlet per impostare la sottoscrizione corrente:

		Select-AzureSubscription <AzureSubscriptionName>



## Provisioning di cluster HBase in HDInsight

Nell'articolo su come [configurare una connessione VPN tra due reti virtuali di Azure][hdinsight-hbase-replication-vnet], è stata creata una rete virtuale in un data center in Europa e una rete virtuale in un data center negli Stati Uniti. Le due reti virtuali sono connesse tramite VPN. In questa sessione verrà eseguito il provisioning di un cluster HBase in ogni rete virtuale. Più avanti in questa esercitazione si farà in modo che uno dei cluster HBase replichi il cluster di HBase.

Il portale di Azure classico non supporta il provisioning di cluster HDInsight con le opzioni di configurazione personalizzate. Ad esempio, impostare *hbase.replication* su *true*. Se si imposta il valore nel file di configurazione al termine del provisioning di un cluster, si perderà l'impostazione dopo aver ricreato l'immagine del cluster. Per altre informazioni, vedere [Provisioning di cluster Hadoop in HDInsight][hdinsight-provision]. Una delle opzioni di provisioning del cluster HDInsight con opzioni personalizzate è l'uso di Azure PowerShell.


**Per eseguire il provisioning di un cluster HBase in Contoso-VNet-EU**

1. Dalla workstation aprire Windows PowerShell ISE.
2. Impostare le variabili all'inizio dello script e quindi eseguire lo script.

		# create hbase cluster with replication enabled
		
		$azureSubscriptionName = "[AzureSubscriptionName]"
		
		$hbaseClusterName = "Contoso-HBase-EU" # This is the HBase cluster name to be used.
		$hbaseClusterSize = 1   # You must provision a cluster that contains at least 3 nodes for high availability of the HBase services.
		$hadoopUserLogin = "[HadoopUserName]"
		$hadoopUserpw = "[HadoopUserPassword]"
		
		$vNetName = "Contoso-VNet-EU"  # This name must match your Europe virtual network name.
		$subNetName = 'Subnet-1' # This name must match your Europe virtual network subnet name.  The default name is "Subnet-1".
		
		$storageAccountName = 'ContosoStoreEU'  # The script will create this storage account for you.  The storage account name doesn't support hyphens. 
		$storageAccountName = $storageAccountName.ToLower() #Storage account name must be in lower case.
		$blobContainerName = $hbaseClusterName.ToLower()  #Use the cluster name as the default container name.
		
		#connect to your Azure subscription
		Add-AzureAccount 
		Select-AzureSubscription $azureSubscriptionName
		
		# Create a storage account used by the HBase cluster
		$location = Get-AzureVNetSite -VNetName $vNetName | %{$_.Location} # use the virtual network location
		New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location
		
		# Create a blob container used by the HBase cluster
		$storageAccountKey = Get-AzureStorageKey -StorageAccountName $storageAccountName | %{$_.Primary}
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		New-AzureStorageContainer -Name $blobContainerName -Context $storageContext
		
		# Create provision configuration object
		$hbaseConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightHBaseConfiguration'
		    
		$hbaseConfigValues.Configuration = @{ "hbase.replication"="true" } #this modifies the hbase-site.xml file
		
		# retrieve vnet id based on vnetname
		$vNetID = Get-AzureVNetSite -VNetName $vNetName | %{$_.id}
		
		$config = New-AzureHDInsightClusterConfig `
		                -ClusterSizeInNodes $hbaseClusterSize `
		                -ClusterType HBase `
		                -VirtualNetworkId $vNetID `
		                -SubnetName $subNetName `
		            | Set-AzureHDInsightDefaultStorage `
		                -StorageAccountName $storageAccountName `
		                -StorageAccountKey $storageAccountKey `
		                -StorageContainerName $blobContainerName `
		            | Add-AzureHDInsightConfigValues `
		                -HBase $hbaseConfigValues
		
		# provision HDInsight cluster
		$hadoopUserPassword = ConvertTo-SecureString -String $hadoopUserpw -AsPlainText -Force     
		$credential = New-Object System.Management.Automation.PSCredential($hadoopUserLogin,$hadoopUserPassword)
		
		$config | New-AzureHDInsightCluster -Name $hbaseClusterName -Location $location -Credential $credential



**Per eseguire un cluster HBase in Contoso-VNet-EU**

- Usare lo stesso script con i valori seguenti:

		$hbaseClusterName = "Contoso-HBase-US" # This is the HBase cluster name to be used.
		$vNetName = "Contoso-VNet-US"  # This name must match your Europe virtual network name.
		$storageAccountName = 'ContosoStoreUS'	

	Poiché è già collegato al proprio account Azure, non è più necessario eseguire i cmdlet seguenti:

		Add-AzureAccount 
		Select-AzureSubscription $azureSubscriptionName




## Configurare il server d'inoltro condizionale DNS

In [Configurare il server DNS tra reti virtuali][hdinsight-hbase-replication-dns], sono stati configurati i server DNS per le due reti. I cluster HBase dispongono di suffissi di dominio diversi. Pertanto è necessario configurare i server di inoltro condizionali DNS aggiuntivi.

Per configurare il server d'inoltro condizionale, è necessario conoscere i suffissi di dominio dei due cluster HBase.

**Per trovare i suffissi di dominio dei due cluster HBase**

1. Effettuare una connessione RDP in **Contoso-HBase-EU**. Per istruzioni, vedere [Gestire cluster Hadoop in HDInsight tramite il portale di Azure classico][hdinsight-manage-portal]. È effettivamente headnode0 del cluster.
2. Aprire una console Windows PowerShell o un prompt dei comandi.
3. Eseguire **ipconfig** e annotare il **suffisso DNS specifico della connessione**.
4. Non chiudere la sessione RDP. Sarà necessario in seguito per verificare la risoluzione dei nomi di dominio.
5. Ripetere gli stessi passaggi per scoprire il **suffisso DNS specifico della connessione** di **Contoso-HBase-US**.


**Per configurare i server di inoltro DNS**
 
1.	Effettuare una connessione RDP in **Contoso-DNS-EU**.
2.	Fare clic sul tasto di Windows in basso a sinistra.
2.	Fare clic su **Strumenti di amministrazione**.
3.	Fare clic su **DNS**.
4.	Nel riquadro sinistro espandere **DSN**, **Contoso-DNS-EU**.
5.	Fare clic con il pulsante destro del mouse su **Server d'inoltro condizionali** e quindi fare clic su **Nuovo server d'inoltro condizionale**.
5.	Immettere le seguenti informazioni:
	- **Dominio DNS**: immettere il suffisso DNS di Contoso-HBase-US. Ad esempio: Contoso-HBase-US.f5.internal.cloudapp.net.
	- **Indirizzi IP dei server master**: immettere 10.2.0.4, ovvero l'indirizzo IP di Contoso-DNS-US. Verificare l'indirizzo IP. Il server DNS può avere un indirizzo IP diverso.
6.	Premere **INVIO** e quindi fare clic su **OK**. A questo punto sarà possibile risolvere l'indirizzo IP di Contoso-DNS-US da Contoso-DNS-EU.
7.	Ripetere i passaggi per aggiungere un server di inoltro condizionale DNS al servizio DNS nella macchina virtuale di Contoso-DNS-US con i valori seguenti:
	- **Dominio DNS**: immettere il suffisso DNS di Contoso-HBase-EU.
	- **Indirizzi IP dei server master**: immettere 10.2.0.4, ovvero l'indirizzo IP di Contoso-DNS-EU.

**Per testare la risoluzione dei nomi di dominio**

1. Passare alla finestra RDP di Contoso-HBase-EU.
2. Aprire un prompt dei comandi.
3. Eseguire il comando ping:

		ping headnode0.[DNS suffix of Contoso-HBase-US]

	Il protocollo ICM è attivato per i nodi di lavoro dei cluster HBase

4. Non chiudere la sessione RDP. Sarà necessario usarla più avanti nell'esercitazione.
5. Ripetere gli stessi passaggi per eseguire il ping di headnode0 di Contoso-HBase-EU da Contoso-HBase-US.

>[AZURE.IMPORTANT] Il DNS deve funzionare prima di poter procedere con i passaggi successivi.

## Abilitare la replica tra le tabelle di HBase

A questo punto è possibile creare una tabella HBase di esempio, abilitare la replica e quindi testarla con alcuni dati. Si userà una tabella di esempio con due famiglie di colonne: Personale e Ufficio.

In questa esercitazione il cluster HBase dell'Europa sarà il cluster di origine e il cluster HBase degli Stati Uniti sarà quello di destinazione.

Creare tabelle HBase con gli stessi nomi e le famiglie di colonne nei cluster di origine e di destinazione, in modo che il cluster di destinazione sappia dove archiviare i dati ricevuti. Per altre informazioni sull'uso della shell HBase, vedere l'[introduzione all'uso di Apache HBase in HDInsight][hdinsight-hbase-get-started].

**Per creare una tabella HBase in Contoso-HBase-EU**

1. Passare alla finestra RDP di **Contoso-HBase-EU**.
2. Dal desktop fare clic su **Hadoop Command Line**.
2. Passare alla directory home di HBase:

		cd %HBASE_HOME%\bin
3. Aprire la shell HBase:

		hbase shell
4. Creare una tabella HBase:

		create 'Contacts', 'Personal', 'Office'
5. Non chiudere la sessione RDP né la finestra della riga di comando di Hadoop. Sarà necessario usarle più avanti nell'esercitazione.
	
**Per creare una tabella HBase in Contoso-HBase-US**

- Ripetere gli stessi passaggi per creare la stessa tabella in Contoso-HBase-US.


**Per aggiungere Contoso-HBase-US come peer di replica**

1. Passare alla finestra RDP di **Contoso-HBase\_EU**.
2. Dalla finestra della shell di HBase, aggiungere il cluster di destinazione (Contoso-HBase-US) come peer, ad esempio:

		add_peer '1', 'zookeeper0.contoso-hbase-us.d4.internal.cloudapp.net,zookeeper1.contoso-hbase-us.d4.internal.cloudapp.net,zookeeper2.contoso-hbase-us.d4.internal.cloudapp.net:2181:/hbase'

	Nell'esempio riportato il suffisso del dominio è *contoso-hbase-us.d4.internal.cloudapp.net*. È necessario aggiornarlo in modo che corrisponda con il suffisso del dominio del cluster HBase US. Verificare che non vi sia alcun spazio tra i nomi host.

**Per configurare ogni famiglia di colonna da replicare nel cluster di origine**

1. Dalla finestra della shell di HBase della sessione RDP di **Contso-HBase-EU** configurare ogni famiglia di colonne da replicare:

		disable 'Contacts'
		alter 'Contacts', {NAME => 'Personal', REPLICATION_SCOPE => '1'}
		alter 'Contacts', {NAME => 'Office', REPLICATION_SCOPE => '1'}
		enable 'Contacts'

**Per caricare in massa i dati nella tabella HBase**

Un file di dati di esempio è stato caricato in un contenitore Blob di Azure pubblico con il seguente URL:

		wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

Il contenuto del file è il seguente:

		8396	Calvin Raji	230-555-0191	5415 San Gabriel Dr.
		16600	Karen Wu	646-555-0113	9265 La Paz
		4324	Karl Xie	508-555-0163	4912 La Vuelta
		16891	Jonathan Jackson	674-555-0110	40 Ellis St.
		3273	Miguel Miller	397-555-0155	6696 Anchor Drive
		3588	Osarumwense Agbonile	592-555-0152	1873 Lion Circle
		10272	Julia Lee	870-555-0110	3148 Rose Street
		4868	Jose Hayes	599-555-0171	793 Crawford Street
		4761	Caleb Alexander	670-555-0141	4775 Kentucky Dr.
		16443	Terry Chander	998-555-0171	771 Northridge Drive

È possibile caricare lo stesso file di dati nel cluster HBase e importare i dati da tale posizione.

1. Passare alla finestra RDP di **Contoso-HBase-EU**.
2. Dal desktop fare clic su **Hadoop Command Line**.
3. Passare alla directory home di HBase:

		cd %HBASE_HOME%\bin

4. Caricare i dati:

		hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:HomePhone, Office:Address" -Dimporttsv.bulk.output=/tmpOutput Contacts wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

		hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /tmpOutput Contacts


## Verificare che la replica di dati sia in esecuzione

È possibile verificare che la replica sia in esecuzione eseguendo la scansione delle tabelle di entrambi i cluster con i seguenti comandi della shell di HBase:

		Scan 'Contacts'


## Passaggi successivi

In questa esercitazione si è appreso come configurare la replica di HBase in due data center. Per altre informazioni su HDInsight e HBase, vedere:

- [Pagina del servizio relativa a HDInsight](https://azure.microsoft.com/services/hdinsight/)
- [Documentazione relativa a HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/)
- [Introduzione ad Apache HBase in HDInsight][hdinsight-hbase-get-started]
- [Panoramica su HBase di HDInsight][hdinsight-hbase-overview]
- [Provisioning di cluster HBase nella rete virtuale di Azure][hdinsight-hbase-provision-vnet]
- [Analisi dei sentimenti Twitter in tempo reale con HBase][hdinsight-hbase-twitter-sentiment]
- [Analisi dei dati dei sensori con Storm e HBase in HDInsight (Hadoop)][hdinsight-sensor-data]

[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-VNets.md
[hdinsight-hbase-geo-replication-dns]: ../hdinsight-hbase-geo-replication-configure-VNet.md


[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication/HDInsight.HBase.Replication.Network.diagram.png

[powershell-install]: powershell-install-configure.md
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-hbase-replication-vnet]: hdinsight-hbase-geo-replication-configure-VNets.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-DNS.md
[hdinsight-hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[hdinsight-sensor-data]: hdinsight-storm-sensor-data-analysis.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md

<!---HONumber=AcomDC_0914_2016-->