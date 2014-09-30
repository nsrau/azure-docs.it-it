<properties linkid="manage-services-hdinsight-hbase-provision-on-vnet" urlDisplayName="Provision HBase clusters on Azure Virtual Network" pageTitle="Provision HBase clusters on Azure Virtual Network | Azure" metaKeywords="" description="Learn how to create HDInsight clusters on Azure Virtual Network." metaCanonical="" services="hdinsight" documentationCenter="" title="Provision HBase clusters on Azure Virtual Network" authors="jgao" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="jgao"></tags>

# Provisioning di cluster HBase in Rete virtuale di Azure

Informazioni su come creare cluster HDInsight in Rete virtuale di Azure.

Grazie all'integrazione con la rete virtuale, i cluster HBase possono essere distribuiti nella stessa rete virtuale delle applicazioni, permettendo così alle applicazioni di comunicare direttamente con HBase. Questo approccio offre i vantaggi seguenti:

-   Connettività diretta dell'applicazione Web con i nodi del cluster HBase, che permette le comunicazioni tramite le API RPC Java HBase.
-   Miglioramento delle prestazioni, poiché il traffico non deve attraversare più gateway e servizi di bilanciamento del carico.
-   Elaborazione di informazioni sensibili in modo più sicuro, senza esporre un endpoint pubblico.

> [WACOM.NOTE]È attualmente possibile eseguire il provisioning solo dei cluster HBase in Rete virtuale di Azure. I cluster Hadoop non sono supportati.

## Contenuto dell'articolo

-   [Prerequisiti][]
-   [Provisioning di cluster HBase in una rete virtuale][]
-   [Effettuare la connessione al cluster HBase con provisioning nella rete virtuale tramite le API RPC Java HBase][]
-   [Passaggi successivi][]

## <span id="prerequisites"></span></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

-   **Una sottoscrizione di Azure**. Azure è una piattaforma basata su sottoscrizione. Per altre informazioni su come ottenere una sottoscrizione, vedere [Opzioni di acquisto][], [Offerte per i membri][] oppure [Versione di valutazione gratuita][].

-   **Una workstation in cui sia stato installato e configurato Azure PowerShell**. Per istruzioni, vedere [Come installare e configurare Azure PowerShell][]. Per eseguire script di PowerShell, è necessario eseguire Azure PowerShell come amministratore e impostare i criteri di esecuzione su *RemoteSigned*. Vedere la pagina relativa all'[esecuzione di script di Windows PowerShell][].

    Prima di eseguire script PowerShell, assicurarsi di essere connessi alla sottoscrizione di Azure utilizzando il seguente cmdlet:

        Add-AzureAccount

    Se si dispone di più sottoscrizioni di Azure, utilizzare il seguente cmdlet per impostare la sottoscrizione corrente:

        Select-AzureSubscription <AzureSubscriptionName>

## <span id="hbaseprovision"></span></a>Effettuare il provisioning di un cluster HBase in una rete virtuale.

**Per creare una rete virtuale usando il portale di gestione:**

1.  Accedere al [portale di gestione di Azure][].
2.  Nella parte inferiore della pagina fare clic su **NUOVO**, quindi su **SERVIZI DI RETE**, **RETE VIRTUALE** e infine su **CREAZIONE RAPIDA**.
3.  Digitare o selezionare i valori seguenti:

    -   **Name**: nome della rete virtuale.
    -   **Spazio di indirizzi**: scegliere uno spazio di indirizzi per la rete virtuali con dimensioni sufficienti per fornire indirizzi per tutti i nodi del cluster. In caso contrario, il provisioning avrà esito negativo.
    -   **Numero massimo VM**: scegliere uno dei valori disponibili per il numero massimo di VM.
    -   **Percorso**: il percorso deve essere uguale a quello del cluster HBase che sarà creato.
    -   **Server DNS**: questo articolo usa un server DNS interno fornito da Azure. È quindi possibile scegliere **Nessuno**. Sono supportate anche configurazioni di rete più avanzate con server DNS personalizzati. Per indicazioni dettagliate, vedere [][]<http://msdn.microsoft.com/library/azure/jj156088.aspx></a>.

4.  Fare clic su **CREA RETE VIRTUALE**. Il nome della rete virtuale sarà visualizzato nell'elenco. Attendere l'impostazione del valore **Creata** nella colonna Stato.
5.  Nel riquadro principale fare clic sulla rete virtuale appena creata.
6.  Nella parte superiore della pagina fare clic su **DASHBOARD**.
7.  In **riepilogo rapido** annotare l'**ID RETE VIRTUALE**. Sarà necessario per il provisioning del cluster HBase.
8.  Nella parte superiore della pagina fare clic su **CONFIGURA**.
9.  Nella parte inferiore della pagina il nome predefinito per la subnet è **Subnet-1**. Se si vuole, è possibile rinominare la subnet o aggiungere una nuova subnet per il cluster HBase. Annotare il nome della subnet, poiché sarà necessario durante il provisioning del cluster.
10. Verificare il valore di **CIDR (CONTEGGIO INDIRIZZI)** per la subnet che sarà usata per il cluster. Il conteggio di indirizzi deve essere superiore al numero di nodi di lavoro più sette (Gateway: 2, Nodo head: 2, Zookeeper: 3). Ad esempio, se è necessario un cluster HBase da 10 nodi, il conteggio di indirizzi per la subnet dovrà essere maggiore di 17 (10+7). In caso contrario, la distribuzione avrà esito negativo.

    > [WACOM.NOTE] È consigliabile designare una singola subnet per un cluster.

11. Fare clic su **Salva** nella parte inferiore della pagina, se i valori della subnet sono stati aggiornati.

> [WACOM.NOTE] I cluster HDInsight usano l'archiviazione BLOB di Azure Blob per l'archiviazione dei dati. Per altre informazioni, vedere [Usare l'archiviazione BLOB di Azure con Hadoop in HDInsight][]. Saranno necessari un account di archiviazione e un contenitore di archiviazione BLOB. Il percorso dell'account di archiviazione deve corrispondere al percorso della rete virtuale e al percorso del cluster.

**Per creare un account di archiviazione di Azure e un contenitore di archiviazione BLOB:**

1.  Accedere al [portale di gestione di Azure][].
2.  Fare clic su **NEW** nell'angolo inferiore sinistro, selezionare **DATA SERVICES**, quindi **STORAGE** e infine **QUICK CREATE**.

3.  Digitare o selezionare i valori seguenti:

    -   **URL**: nome dell'account di archiviazione.
    -   **PERCORSO**: percorso dell'account di archiviazione. Assicurarsi che corrisponda al percorso della rete virtuale. I gruppi di affinità non sono supportati.
    -   **REPLICA**: per finalità di testing, usare **Con ridondanza locale** per ridurre i costi.

4.  Fare clic su **CREATE STORAGE ACCOUNT**. Il nuovo account di archiviazione verrà incluso nell'elenco di archiviazione.
5.  Attendere che il valore di **STATO** per il nuovo account di archiviazione sia modificato in **Online**.
6.  Fare clic sul nuovo account di archiviazione nell'elenco per selezionarlo.
7.  Fare clic su **MANAGE ACCESS KEYS** nella parte inferiore della pagina.
8.  Prendere nota dei valori di **NOME ACCOUNT DI ARCHIVIAZIONE** e **CHIAVE DI ACCESSO PRIMARIA** o **CHIAVE DI ACCESSO SECONDARIA**. È possibile usare indifferentemente una delle due chiavi. Sarà necessario utilizzarli più avanti nell'esercitazione.
9.  Nella parte superiore della pagina fare clic su **CONTENITORE**.
10. Fare clic su **AGGIUNGI** nella parte inferiore della pagina..
11. Immettere il nome del contenitore. Il contenitore sarà usato come contenitore predefinito per il cluster HBase. Per impostazione predefinita, il nome del contenitore corrisponde al nome del cluster. Lasciare impostato il campo **ACCESSO** su **Privato**.
12. Fare clic sull'icona del segno di spunta per creare il contenitore.

**Per eseguire il provisioning di un cluster HBase tramite Azure PowerShell:**

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

Per testare il nuovo cluster HBase, è possibile usare le procedure disponibili in [Introduzione a HBase con Hadoop in HDInsight][].

## <span id="connect"></span></a>Effettuare la connessione al cluster HBase con provisioning nella rete virtuale tramite le API RPC Java HBase

1.  Eseguire il provisioning di una macchina virtuale IaaS nella stessa rete virtuale di Azure e nella stessa subnet. In questo modo, sia la macchina virtuale che il cluster HBase useranno lo stesso server DNS interno per risolvere i nomi degli host. Per ottenere questo risultato, è necessario scegliere l'opzione Da raccolta e selezionare la rete virtuale invece di un data center. Per istruzioni, vedere [Creazione di una macchina virtuale che esegue Windows Server][]. È sufficiente un'immagine standard di Windows Server 2012 con una VM di dimensioni ridotte.

2.  Ottenere il suffisso DNS specifico della connessione per il cluster HBase. Per ottenere questo risultato, effettuare una connessione RDP al cluster HBase (si sarà connessi al nodo head) ed eseguire **ipconfig** da un prompt dei comandi. Per istruzioni sull'abilitazione di RDP e sulla connessione al cluster tramite RDP, vedere [Gestire cluster Hadoop in HDInsight tramite il portale di gestione di Azure][].

    ![hdinsight.hbase.dns.surffix][]

3.  Cambiare la configurazione del suffisso DNS primario della macchina virtuale. Ciò permette alla macchina virtuale di risolvere automaticamente il nome host del cluster HBase, senza specificare esplicitamente il suffisso. Ad esempio, il nome host *workernode0* sarà risolto correttamente nel workernode0 del cluster HBase.
    Per modificare la configurazione:

    1.  Effettuare una connessione RDP alla VM.
    2.  Aprire l'**Editor Criteri di gruppo locali**. Il file eseguibile è gpedit.msc.
    3.  Espandere **Configurazione computer**, quindi **Modelli amministrativi**, **Rete** e infine fare clic su **Client DNS**.
    4.  Impostare **Suffisso DNS primario** sul valore ottenuto nel passaggio 2:

        ![hdinsight.hbase.primary.dns.suffix][]

    1.  Fare clic su **OK**.
    2.  Riavviare la macchina virtuale.

    La VM è ora pronta per comunicare con il cluster HBase. Per testare la connessione, eseguire "ping headnode0" dalla macchina virtuale.

## <span id="nextsteps"></span></a>Passaggi successivi

In questa esercitazione si è appreso come eseguire il provisioning di un cluster HBase. Per ulteriori informazioni, vedere:

-   [Introduzione all'utilizzo di HDInsight][]
-   [Provisioning di cluster Hadoop in HDInsight][]
-   [Introduzione a HBase con Hadoop in HDInsight][]
-   [Analizzare i sentimenti Twitter con HBase in HDInsight][]
-   [Panoramica di Rete virtuale][].

  [Prerequisiti]: #prerequisites
  [Provisioning di cluster HBase in una rete virtuale]: #hbaseprovision
  [Effettuare la connessione al cluster HBase con provisioning nella rete virtuale tramite le API RPC Java HBase]: #connect
  [Passaggi successivi]: #nextsteps
  [Opzioni di acquisto]: http://azure.microsoft.com/en-us/pricing/purchase-options/
  [Offerte per i membri]: http://azure.microsoft.com/en-us/pricing/member-offers/
  [Versione di valutazione gratuita]: http://azure.microsoft.com/en-us/pricing/free-trial/
  [Come installare e configurare Azure PowerShell]: ../install-configure-powershell
  [esecuzione di script di Windows PowerShell]: http://technet.microsoft.com/en-us/library/ee176949.aspx
  [portale di gestione di Azure]: https://manage.windowsazure.com
  []: http://msdn.microsoft.com/library/azure/jj156088.aspx
  [Usare l'archiviazione BLOB di Azure con Hadoop in HDInsight]: ../hdinsight-use-blob-storage/
  [Introduzione a HBase con Hadoop in HDInsight]: ../hdinsight-hbase-get-started/
  [Creazione di una macchina virtuale che esegue Windows Server]: ../virtual-machines-windows-tutorial/
  [Gestire cluster Hadoop in HDInsight tramite il portale di gestione di Azure]: ../hdinsight-administer-use-management-portal/#rdp
  [hdinsight.hbase.dns.surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
  [hdinsight.hbase.primary.dns.suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png
  [Introduzione all'utilizzo di HDInsight]: ../hdinsight-get-started/
  [Provisioning di cluster Hadoop in HDInsight]: ../hdinsight-provision-clusters/
  [Analizzare i sentimenti Twitter con HBase in HDInsight]: ../hdinsight-hbase-twitter-sentiment/
  [Panoramica di Rete virtuale]: http://msdn.microsoft.com/library/azure/jj156007.aspx
