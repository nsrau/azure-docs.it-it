---
title: Creare cluster HBase in una rete virtuale | Documentazione Microsoft
description: Introduzione all&quot;uso di HBase in Azure HDInsight. Informazioni su come creare cluster HBase di HDInsight in Rete virtuale di Azure.
keywords: 
services: hdinsight,virtual-network
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 8de8e446-f818-4e61-8fad-e9d38421e80d
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/22/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 938abf03191dec10da8d2fabf27c5db2415d6bc5
ms.openlocfilehash: 6dd149808a89ccf7da2989751788e074d5340d5c


---
# <a name="create-hbase-clusters-in-azure-virtual-network"></a>Creare cluster HBase nella rete virtuale di Azure
Informazioni su come creare cluster HBase in Azure HDInsight in una [Rete virtuale di Azure][1].

Grazie all'integrazione con la rete virtuale, i cluster HBase possono essere distribuiti nella stessa rete virtuale delle applicazioni, consentendo così alle applicazioni di comunicare direttamente con HBase. Questo approccio offre i vantaggi seguenti:

* Connettività diretta dell'applicazione Web con i nodi del cluster HBase, che consente le comunicazioni tramite le API RPC (Remote Procedure Call) Java di HBase.
* Miglioramento delle prestazioni, poiché il traffico non deve attraversare più gateway e servizi di bilanciamento del carico.
* Possibilità di elaborare le informazioni sensibili in modo più sicuro, senza esporre un endpoint pubblico.

### <a name="prerequisites"></a>Prerequisiti
Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Workstation con Azure PowerShell**. Vedere [Installare e usare Azure PowerShell](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

## <a name="create-hbase-cluster-into-virtual-network"></a>Creare cluster HBase nella rete virtuale
In questa sezione viene creato un cluster HBase basato su Linux con l'account di archiviazione di Azure dipendente in una rete virtuale di Azure tramite un [modello di Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md). Per altri metodi di creazione di cluster e per informazioni sulle impostazioni, vedere l'articolo sulla [creazione di cluster HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Per altre informazioni sull'uso di un modello per creare cluster Hadoop in HDInsight, vedere l'articolo relativo alla [creazione di cluster Hadoop in HDInsight tramite modelli di Azure Resource Manager](hdinsight-hadoop-create-windows-clusters-arm-templates.md)

> [!NOTE]
> Alcune proprietà sono state impostate come hardcoded nel modello. Ad esempio:
>
> * **Location**: Stati Uniti orientali 2
> * __Cluster version: 3.4
> * **Cluster worker node count**: 4
> * **Default storage account**: stringa univoca
> * **Virtual network name**: &lt;Nome cluster>-vnet
> * **Virtual network address space**: 10.0.0.0/16
> * **Subnet name**: subnet1
> * **Subnet address range**: 10.0.0.0/24
>
> &lt;Cluster Name > viene sostituito con il nome del cluster fornito quando si usa il modello.
>
>

1. Fare clic sull'immagine seguente per aprire il modello nel portale di Azure. Il modello è disponibile tra i [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-linux-vnet/).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hbase-provision-vnet/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Compilare i campi seguenti del pannello **Distribuzione personalizzata**:

   * **Sottoscrizione**: selezionare una sottoscrizione di Azure usata per creare il cluster HDInsight, l'account di archiviazione dipendente e la rete virtuale di Azure.
   * **Gruppo di risorse**: selezionare **Crea nuovo** e assegnare un nome al nuovo gruppo di risorse.
   * **Posizione**: selezionare una posizione per il gruppo di risorse.
   * **ClusterName**: immettere un nome per il cluster Hadoop da creare.
   * **Cluster login name and password**: il nome dell'account di accesso predefinito è **admin**.
   * **SSH username and password**: il nome utente predefinito è **sshuser**.  È possibile rinominarlo.
   * **Accetto le condizioni riportate sopra**: (selezionare)
3. Fare clic su **Acquista**. La creazione di un cluster richiede circa 20 minuti. Dopo aver creato il cluster, è possibile fare clic sul pannello del cluster nel portale per aprirlo.

Al termine dell'esercitazione, è consigliabile eliminare il cluster. Con HDInsight, i dati vengono archiviati in Archiviazione di Azure ed è possibile eliminare tranquillamente un cluster quando non viene usato. Vengono addebitati i costi anche per i cluster HDInsight che non sono in uso. Poiché i costi per il cluster sono decisamente superiori a quelli per l'archiviazione, economicamente ha senso eliminare i cluster quando non vengono usati. Per istruzioni sull'eliminazione di un cluster, vedere [Gestire cluster Hadoop in HDInsight tramite il portale di Azure](hdinsight-administer-use-management-portal.md#delete-clusters).

Per iniziare a lavorare con il nuovo cluster HBase, è possibile usare le procedure disponibili in [Introduzione a HBase con Hadoop in HDInsight](hdinsight-hbase-tutorial-get-started.md).

## <a name="connect-to-the-hbase-cluster-using-hbase-java-rpc-apis"></a>Connettersi al cluster HBase tramite le API RPC Java di HBase
1. Creare una macchina virtuale IaaS (Infrastructure as a Service) nella stessa rete virtuale di Azure e nella stessa subnet. Per le istruzioni su come creare una nuova macchina virtuale IaaS, vedere [Creare una macchina virtuale che esegue Windows Server](../virtual-machines/virtual-machines-windows-hero-tutorial.md). Quando si usa la procedura indicata in questo documento, è necessario inserire quanto segue per la configurazione di rete:

   * **Virtual network**: &lt;Nome cluster>-vnet
   * **Subnet**: subnet1

   > [!IMPORTANT]
   > Sostituire &lt;Nome cluster> con il nome usato durante la creazione del cluster HDInsight nei passaggi precedenti.
   >
   >

   Applicando questi valori, la macchina virtuale viene configurata nella stessa rete virtuale e subnet del cluster HDInsight. Ciò consente la comunicazione bidirezionale diretta. È possibile creare un cluster HDInsight con un nodo perimetrale vuoto. Il nodo perimetrale può essere usato per gestire il cluster.  Per altre informazioni, vedere [Usare nodi perimetrali vuoti in HDInsight](hdinsight-apps-use-edge-node.md).

2. Quando si usa un'applicazione Java per connettersi a HBase da remoto, è necessario usare il nome di dominio completo (FQDN). Per determinare quest'ultimo, è necessario ottenere il suffisso DNS specifico della connessione del cluster HBase. A questo scopo, è possibile usare uno dei metodi seguenti:

   * Usare un Web browser per effettuare una chiamata Ambari:

     Passare a https://&lt;Nome cluster>.azurehdinsight.net/api/v1/clusters/&lt;Nome cluster>/hosts?minimal_response=true. Viene restituito un file JSON con i suffissi DNS.
   * Usare il sito Web Ambari:

     1. Passare a https://&lt;Nome cluster>.azurehdinsight.net.
     2. Scegliere **Host** dal menu in alto.
   * Usare Curl per effettuare chiamate REST:

         curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

     Nei dati JSON (JavaScript Object Notation) restituiti, trovare la voce "host_name". Questa contiene il nome di dominio completo (FQDN) per i nodi nel cluster. Ad esempio:

         ...
         "host_name": "wordkernode0.<clustername>.b1.cloudapp.net
         ...

     La porzione del nome di dominio che inizia con il nome del cluster è il suffisso DNS. Ad esempio, mycluster.b1.cloudapp.net.
   * Uso di Azure PowerShell

     Usare lo script di Azure PowerShell seguente per registrare la funzione **Get-ClusterDetail**, che può essere usata per restituire il suffisso DNS:

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
                 $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/YARN/components/RESOURCEMANAGER"
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
   * Usare il protocollo RDP

     Per ottenere il suffisso DNS, è anche possibile usare Desktop remoto per connettersi al cluster HBase (si verrà connessi al nodo head) ed eseguire **ipconfig** da un prompt dei comandi. Per istruzioni sull'abilitazione di RDP (Remote Desktop Protocol) e sulla connessione al cluster tramite RDP, vedere [Gestire cluster Hadoop in HDInsight tramite il portale di Azure][hdinsight-admin-portal].

     ![hdinsight.hbase.dns.surffix][img-dns-surffix]

<!--
3.    Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

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

Per usare queste informazioni in un'applicazione Java e creare un'applicazione, è possibile seguire i passaggi in [Usare Maven per compilare applicazioni Java che usano HBase con HDInsight (Hadoop)](hdinsight-hbase-build-java-maven.md) . Per fare in modo che l'applicazione si connetta a un server HBase remoto, modificare il file **hbase-site.xml** in questo esempio, in modo che usi il nome di dominio completo (FQDN) per Zookeeper. Ad esempio:

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [!NOTE]
> Per altre informazioni sulla risoluzione dei nomi in reti virtuali di Azure, comprese quelle relative all'uso del proprio server DNS, vedere [Risoluzione dei nomi (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
>
>

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è appreso come creare un cluster HBase. Per altre informazioni, vedere:

* [Introduzione all'uso di HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
* [Usare nodi perimetrali vuoti in HDInsight](hdinsight-apps-use-edge-node.md)
* [Configurare la replica di HBase in HDInsight](hdinsight-hbase-replication.md)
* [Creare cluster Hadoop in HDInsight](hdinsight-provision-clusters.md)
* [Introduzione all'uso di HBase con Hadoop in HDInsight](hdinsight-hbase-tutorial-get-started.md)
* [Analizzare i sentimenti Twitter con HBase in HDInsight](hdinsight-hbase-analyze-twitter-sentiment.md)
* [Panoramica di Rete virtuale][vnet-overview]

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
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx


[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter


[powershell-install]: /powershell/azureps-cmdlets-docs


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
[img-provision-cluster-page5]: ./media/hdinsight-hbase-provision-vnet/hbasewizard5.png "Usare azioni di script per personalizzare un cluster HBase"

[azure-preview-portal]: https://portal.azure.com



<!--HONumber=Dec16_HO4-->


