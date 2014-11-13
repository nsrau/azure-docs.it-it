<properties urlDisplayName="Provision HBase clusters on Azure Virtual Network" pageTitle="Provisioning di cluster HBase in Rete virtuale di Azure | Azure" metaKeywords="" description="Informazioni su come creare cluster HDInsight in Rete virtuale di Azure." metaCanonical="" services="hdinsight" documentationCenter="" title="Provisioning di cluster HBase in Rete virtuale di Azure" authors="jgao" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="jgao" />

# Provisioning di cluster HBase in Rete virtuale di Azure

Informazioni su come creare cluster HDInsight in Rete virtuale di Azure.

Grazie all'integrazione con la rete virtuale, i cluster HBase possono essere distribuiti nella stessa rete virtuale delle applicazioni, permettendo così alle applicazioni di comunicare direttamente con HBase. Questo approccio offre i vantaggi seguenti:

-   Connettività diretta dell'applicazione Web con i nodi del cluster HBase, che permette le comunicazioni tramite le API RPC Java HBase.
-   Miglioramento delle prestazioni, poiché il traffico non deve attraversare più gateway e servizi di bilanciamento del carico.
-   Elaborazione di informazioni sensibili in modo più sicuro, senza esporre un endpoint pubblico.

## Contenuto dell'articolo

-   [Prerequisiti][Prerequisiti]
-   [Provisioning di cluster HBase in una rete virtuale][Provisioning di cluster HBase in una rete virtuale]
-   [Effettuare la connessione al cluster HBase con provisioning nella rete virtuale tramite le API RPC Java HBase][Effettuare la connessione al cluster HBase con provisioning nella rete virtuale tramite le API RPC Java HBase]
-   [Provisioning di un cluster HBase tramite Azure PowerShell][Provisioning di un cluster HBase tramite Azure PowerShell]
-   [Passaggi successivi][Passaggi successivi]

## <span id="prerequisites"></span></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

-   **Una sottoscrizione di Azure**. Azure è una piattaforma basata su sottoscrizione. Per altre informazioni su come ottenere una sottoscrizione, vedere [Opzioni di acquisto][Opzioni di acquisto], [Offerte per i membri][Offerte per i membri] oppure [Versione di valutazione gratuita][Versione di valutazione gratuita].

-   **Una workstation in cui sia stato installato e configurato Azure PowerShell**. Per istruzioni, vedere [Come installare e configurare Azure PowerShell][Come installare e configurare Azure PowerShell]. Per eseguire script di PowerShell, è necessario eseguire Azure PowerShell come amministratore e impostare i criteri di esecuzione su *RemoteSigned*. Vedere la pagina relativa all'[esecuzione di script di Windows PowerShell][esecuzione di script di Windows PowerShell].

    Prima di eseguire script PowerShell, assicurarsi di essere connessi alla sottoscrizione di Azure usando il seguente cmdlet:

        Add-AzureAccount

    Se si dispone di più sottoscrizioni di Azure, usare il seguente cmdlet per impostare la sottoscrizione corrente:

        Select-AzureSubscription <AzureSubscriptionName>

## <span id="hbaseprovision"></span></a>Effettuare il provisioning di un cluster HBase in una rete virtuale.

**Per creare una rete virtuale usando il portale di gestione:**

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].
2.  Nella parte inferiore della pagina fare clic su **NUOVO**, quindi su **SERVIZI DI RETE**, **RETE VIRTUALE** e infine su **CREAZIONE RAPIDA**.
3.  Digitare o selezionare i valori seguenti:

    -   **Nome**: nome della rete virtuale.
    -   **Spazio di indirizzi**: scegliere uno spazio di indirizzi per la rete virtuali con dimensioni sufficienti per fornire indirizzi per tutti i nodi del cluster. In caso contrario, il provisioning avrà esito negativo.
    -   **Numero massimo VM**: scegliere uno dei valori disponibili per il numero massimo di VM.
    -   **Percorso**: il percorso deve essere uguale a quello del cluster HBase che sarà creato.
    -   **Server DNS**: questo articolo usa un server DNS interno fornito da Azure. È quindi possibile scegliere **Nessuno**. Sono supportate anche configurazioni di rete più avanzate con server DNS personalizzati. Per indicazioni dettagliate, vedere <http://msdn.microsoft.com/library/azure/jj156088.aspx>.

4.  Fare clic su **CREA RETE VIRTUALE**. Il nome della rete virtuale sarà visualizzato nell'elenco. Attendere l'impostazione del valore **Creata** nella colonna Stato.
5.  Nel riquadro principale fare clic sulla rete virtuale appena creata.
6.  Nella parte superiore della pagina fare clic su **DASHBOARD**.
7.  In **riepilogo rapido** annotare l'**ID RETE VIRTUALE**. Sarà necessario per il provisioning del cluster HBase.
8.  Nella parte superiore della pagina fare clic su **CONFIGURA**.
9.  Nella parte inferiore della pagina il nome predefinito per la subnet è **Subnet-1**. Se si vuole, è possibile rinominare la subnet o aggiungere una nuova subnet per il cluster HBase. Annotare il nome della subnet, poiché sarà necessario durante il provisioning del cluster.
10. Verificare il valore di **CIDR (CONTEGGIO INDIRIZZI)** per la subnet che sarà usata per il cluster. Il conteggio di indirizzi deve essere superiore al numero di nodi di lavoro più sette (Gateway: 2, Nodo head: 2, Zookeeper: 3). Ad esempio, se è necessario un cluster HBase da 10 nodi, il conteggio di indirizzi per la subnet dovrà essere maggiore di 17 (10+7). In caso contrario, la distribuzione avrà esito negativo.

    > [WACOM.NOTE] È consigliabile designare una singola subnet per un cluster.

11. Fare clic su **Salva** nella parte inferiore della pagina, se i valori della subnet sono stati aggiornati.

> [WACOM.NOTE] I cluster HDInsight usano l'archiviazione BLOB di Azure per l'archiviazione dei dati. Per altre informazioni, vedere [Usare l'archiviazione BLOB di Azure con Hadoop in HDInsight][Usare l'archiviazione BLOB di Azure con Hadoop in HDInsight]. Saranno necessari un account di archiviazione e un contenitore di archiviazione BLOB. Il percorso dell'account di archiviazione deve corrispondere al percorso della rete virtuale e al percorso del cluster.

**Per creare un account di archiviazione di Azure e un contenitore di archiviazione BLOB:**

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].
2.  Fare clic su **NEW** nell'angolo inferiore sinistro, selezionare **DATA SERVICES**, quindi **STORAGE** e infine **QUICK CREATE**.

3.  Digitare o selezionare i valori seguenti:

    -   **URL**: nome dell'account di archiviazione.
    -   **PERCORSO**: percorso dell'account di archiviazione. Assicurarsi che corrisponda al percorso della rete virtuale. I gruppi di affinità non sono supportati.
    -   **REPLICA**: per finalità di testing, usare **Con ridondanza locale** per ridurre i costi.

4.  Fare clic su **CREATE STORAGE ACCOUNT**. Il nuovo account di archiviazione verrà incluso nell'elenco di archiviazione.
5.  Attendere che il valore di **STATO** per il nuovo account di archiviazione sia modificato in **Online**.
6.  Fare clic sul nuovo account di archiviazione nell'elenco per selezionarlo.
7.  Fare clic su **MANAGE ACCESS KEYS** nella parte inferiore della pagina.
8.  Prendere nota dei valori di **NOME ACCOUNT DI ARCHIVIAZIONE** e **CHIAVE DI ACCESSO PRIMARIA** o **CHIAVE DI ACCESSO SECONDARIA**. È possibile usare indifferentemente una delle due chiavi. Sarà necessario usarli più avanti nell'esercitazione.
9.  Nella parte superiore della pagina fare clic su **CONTENITORE**.
10. Fare clic su **AGGIUNGI** nella parte inferiore della pagina.
11. Immettere il nome del contenitore. Il contenitore sarà usato come contenitore predefinito per il cluster HBase. Per impostazione predefinita, il nome del contenitore corrisponde al nome del cluster. Lasciare impostato il campo **ACCESSO** su **Privato**.
12. Fare clic sull'icona del segno di spunta per creare il contenitore.

**Per eseguire il provisioning di un cluster HBase tramite il portale di Azure:**

> [WACOM.NOTE] Per informazioni su come eseguire il provisioning di un nuovo cluster HBase usando PowerShell, vedere [Provisioning di un cluster HBase tramite Azure PowerShell][Provisioning di un cluster HBase tramite Azure PowerShell].

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].
2.  Fare clic su **NUOVO** nell'angolo inferiore sinistro, selezionare **SERVIZI DATI**, quindi **HDINSIGHT** e infine fare clic su **CREAZIONE PERSONALIZZATA**.
3.  Immettere un **NOME CLUSTER** e selezionare la **VERSIONE DI HDINSIGHT** da usare per il cluster.

    ![Campi nome cluster e versione][Campi nome cluster e versione]

4.  Selezionare il numero di **NODI DATI** da creare per il cluster e l'area o la rete virtuale di Azure (**AREA/RETE VIRTUALE**) da usare per il cluster.

    ![Campi numero di nodi e area][Campi numero di nodi e area]

5.  Immettere il **NOME UTENTE** e la **PASSWORD** dell'amministratore da usare per questo cluster.

    ![Campi nome amministratore e password][Campi nome amministratore e password]

6.  Selezionare se usare un nuovo account di archiviazione o se usarne uno esistente. In caso di account nuovo, immettere il **NOME ACCOUNT** e il **CONTENITORE PREDEFINITO** da creare. Infine, selezionare il segno di spunta per creare il cluster.

    ![Selezione account di archiviazione][Selezione account di archiviazione]

Per iniziare a lavorare con il nuovo cluster HBase, è possibile usare le procedure disponibili in [Introduzione a HBase con Hadoop in HDInsight][Introduzione a HBase con Hadoop in HDInsight].

## <span id="connect"></span></a>Effettuare la connessione al cluster HBase con provisioning nella rete virtuale tramite le API RPC Java HBase

1.  Eseguire il provisioning di una macchina virtuale IaaS nella stessa rete virtuale di Azure e nella stessa subnet. In questo modo, sia la macchina virtuale che il cluster HBase useranno lo stesso server DNS interno per risolvere i nomi degli host. Per ottenere questo risultato, è necessario scegliere l'opzione Da raccolta e selezionare la rete virtuale invece di un data center. Per istruzioni, vedere [Creazione di una macchina virtuale che esegue Windows Server][Creazione di una macchina virtuale che esegue Windows Server]. È sufficiente un'immagine standard di Windows Server 2012 con una VM di dimensioni ridotte.

2.  Quando si usa un'applicazione Java per connettersi a HBase da remoto, è necessario usare il nome di dominio completo (FQDN). Per determinare quest'ultimo, è necessario ottenere il suffisso DNS specifico della connessione del cluster HBase. Per eseguire questa operazione, usare Curl per eseguire query in Ambari, oppure Desktop remoto per connettersi al cluster.

    -   **Curl** - Usare il comando seguente:

            curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

        Nei dati JSON restituiti, trovare la voce "host\_name". Questa conterrà il nome di dominio completo (FQDN) per i nodi nel cluster. Ad esempio:

            ...
            "host_name": "wordkernode0.<clustername>.b1.cloudapp.net
            ...

        La porzione del nome di dominio che inizia con il nome del cluster è il suffisso DNS. Ad esempio, mycluster.b1.cloudapp.net.

    -   **PowerShell** - Usare lo script di PowerShell seguente per registrare la funzione **Get-ClusterDetail**, che può essere usata per restituire il suffisso DNS.

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

    > [WACOM.NOTE] Per ottenere il suffisso DNS, è anche possibile usare Desktop remoto per connettere il cluster HBase (si verrà connessi al nodo head) ed eseguire **ipconfig** da un prompt dei comandi. Per istruzioni sull'abilitazione di RDP e sulla connessione al cluster tramite RDP, vedere [Gestire cluster Hadoop in HDInsight tramite il portale di gestione di Azure][Gestire cluster Hadoop in HDInsight tramite il portale di gestione di Azure].
    >
    > ![hdinsight.hbase.dns.surffix][hdinsight.hbase.dns.surffix]

<!--  3.  Change the Primary DNS Suffix configuration of the virtual machine. This enables virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to the workernode0 of the HBase cluster.       To make the configuration change:      1. RDP into the virtual machine.      2. Open **Local Group Policy Editor**. The executable is gpedit.msc.     3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.      - Set **Primary DNS Suffix** to the value obtained in the step 2:           ![hdinsight.hbase.primary.dns.suffix][img-primary-dns-suffix]     4. Click **OK**.      5. Reboot the virtual machine. -->

Per verificare che la macchina virtuale possa comunicare con il cluster HBase, usare il comando `ping headnode0.<dns suffix>` dalla macchina virtuale. Ad esempio, ping headnode0.mycluster.b1.cloudapp.net

Per usare queste informazioni in un'applicazione Java e creare un'applicazione, è possibile seguire i passaggi in [Usare Maven per compilare applicazioni Java che usano HBase con HDInsight (Hadoop)][Usare Maven per compilare applicazioni Java che usano HBase con HDInsight (Hadoop)]. Per fare in modo che l'applicazione si connetta a un server HBase remoto, modificare il file **hbase-site.xml** in questo esempio, in modo che usi il nome di dominio completo (FQDN) per ZooKeeper. Ad esempio:

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [WACOM.NOTE] Per altre informazioni sulla risoluzione dei nomi in Reti virtuali di Azure, comprese quelle relative all'uso del proprio server DNS, vedere [Risoluzione dei nomi (DNS)][Risoluzione dei nomi (DNS)].

## <span id="powershell"></span></a>Provisioning di un cluster HBase tramite Azure PowerShell:\*\*

1.  Aprire PowerShell ISE.
2.  Copiare e incollare il codice seguente nel riquadro dello script.

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

3.  Fare clic su **Esegui script** o premere **F5**.
4.  Per convalidare il cluster, è possibile verificarlo dal portale di gestione oppure eseguire il cmdlet di PowerShell seguente nel riquadro in basso:

    Get-AzureHDInsightCluster

## <span id="nextsteps"></span></a>Passaggi successivi

In questa esercitazione si è appreso come eseguire il provisioning di un cluster HBase. Per altre informazioni, vedere:

-   [Introduzione all'uso di HDInsight][Introduzione all'uso di HDInsight]
-   [Provisioning di cluster Hadoop in HDInsight][Provisioning di cluster Hadoop in HDInsight]
-   [Introduzione a HBase con Hadoop in HDInsight][Introduzione a HBase con Hadoop in HDInsight]
-   [Analizzare i sentimenti Twitter con HBase in HDInsight][Analizzare i sentimenti Twitter con HBase in HDInsight]
-   [Panoramica di Rete virtuale][Panoramica di Rete virtuale].

  [Prerequisiti]: #prerequisites
  [Provisioning di cluster HBase in una rete virtuale]: #hbaseprovision
  [Effettuare la connessione al cluster HBase con provisioning nella rete virtuale tramite le API RPC Java HBase]: #connect
  [Provisioning di un cluster HBase tramite Azure PowerShell]: #powershell
  [Passaggi successivi]: #nextsteps
  [Opzioni di acquisto]: http://azure.microsoft.com/it-it/pricing/purchase-options/
  [Offerte per i membri]: http://azure.microsoft.com/it-it/pricing/member-offers/
  [Versione di valutazione gratuita]: http://azure.microsoft.com/it-it/pricing/free-trial/
  [Come installare e configurare Azure PowerShell]: ../install-configure-powershell
  [esecuzione di script di Windows PowerShell]: http://technet.microsoft.com/it-it/library/ee176949.aspx
  [portale di gestione di Azure]: https://manage.windowsazure.com
  [Usare l'archiviazione BLOB di Azure con Hadoop in HDInsight]: ../hdinsight-use-blob-storage/
  [Campi nome cluster e versione]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png
  [Campi numero di nodi e area]: ./media/hdinsight-hbase-provision-vnet/hbasewizard2.png
  [Campi nome amministratore e password]: ./media/hdinsight-hbase-provision-vnet/hbasewizard3.png
  [Selezione account di archiviazione]: ./media/hdinsight-hbase-provision-vnet/hbasewizard4.png
  [Introduzione a HBase con Hadoop in HDInsight]: ../hdinsight-hbase-get-started/
  [Creazione di una macchina virtuale che esegue Windows Server]: ../virtual-machines-windows-tutorial/
  [Gestire cluster Hadoop in HDInsight tramite il portale di gestione di Azure]: ../hdinsight-administer-use-management-portal/#rdp
  [hdinsight.hbase.dns.surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
  [Usare Maven per compilare applicazioni Java che usano HBase con HDInsight (Hadoop)]: azure.microsoft.com/it-it/documentation/articles/hdinsight-hbase-build-java-maven/
  [Risoluzione dei nomi (DNS)]: http://msdn.microsoft.com/it-it/library/azure/jj156088.aspx
  [Introduzione all'uso di HDInsight]: ../hdinsight-get-started/
  [Provisioning di cluster Hadoop in HDInsight]: ../hdinsight-provision-clusters/
  [Analizzare i sentimenti Twitter con HBase in HDInsight]: ../hdinsight-hbase-twitter-sentiment/
  [Panoramica di Rete virtuale]: http://msdn.microsoft.com/library/azure/jj156007.aspx
