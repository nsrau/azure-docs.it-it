---
title: Connettersi a Kafka in HDInsight tramite le reti virtuali - Azure | Microsoft Docs
description: Informazioni su come connettersi in remoto a Kafka in HDInsight tramite il client kafka-python. La configurazione in questo documento usa HDInsight all&quot;interno di una rete virtuale di Azure. Il client remoto si connette alla rete virtuale tramite un gateway VPN da punto a sito.
services: hdinsight
documentationCenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.devlang: 
ms.custom: hdinsightactive
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/18/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 8c4e33a63f39d22c336efd9d77def098bd4fa0df
ms.openlocfilehash: 5da21de1f7b51a6ca38a3fe443531bab4091dd60
ms.lasthandoff: 04/20/2017


---

# <a name="connect-to-kafka-on-hdinsight-preview-through-an-azure-virtual-network"></a>Connettersi a Kafka in HDInsight (anteprima) tramite una rete virtuale di Azure

Informazioni su come connettersi a Kafka in HDInsight tramite le reti virtuali di Azure. I client di Kafka, produttori e clienti, possono essere eseguiti direttamente in HDInsight o nei sistemi remoti. I client remoti devono connettersi a Kafka in HDInsight tramite una rete virtuale di Azure. Usare le informazioni contenute in questo documento per comprendere le modalità di connessione dei client remoti a HDInsight tramite le reti virtuali di Azure.

> [!IMPORTANT]
> Molte delle configurazioni descritte in questo documento possono essere usate con i client di Windows, macOS o Linux. L'esempio da punto a sito incluso offre tuttavia solo un client VPN per Windows.
>
> Nell'esempio viene anche usato un client di Python ([kafka-python](http://kafka-python.readthedocs.io/en/master/)) per verificare la comunicazione con Kafka in HDInsight.

## <a name="architecture-and-planning"></a>Architettura e pianificazione

I cluster HDInsight sono protetti all'interno della rete virtuale di Azure e consentono solo il traffico SSH e HTTPS in ingresso. Il traffico arriva tramite un gateway pubblico, che non indirizza il traffico dai client Kafka. Per accedere a Kafka da un client remoto, è necessario creare una rete virtuale di Azure che offra un gateway di rete privata virtuale, VPN. Dopo aver configurato la rete virtuale e il gateway, installare HDInsight nella rete virtuale e connettersi a questa tramite il gateway VPN.

![Diagramma di HDInsight all'interno di una rete virtuale di Azure con un client connesso tramite VPN](media/hdinsight-apache-kafka-connect-vpn-gateway/hdinsight-in-virtual-network.png)

L'elenco seguente contiene informazioni sul processo di uso di Kafka in HDInsight con una rete virtuale:

1. Creare una rete virtuale. Per informazioni specifiche sull'uso di HDInsight con le reti virtuali di Azure, vedere il documento [Estendere HDInsight con la rete virtuale di Azure](hdinsight-extend-hadoop-virtual-network.md).

2. (Facoltativo) Creare una macchina virtuale di Azure all'interno della rete virtuale e installare un server DNS personalizzato su di essa. Il server DNS viene usato per abilitare la risoluzione dei nomi per i client remoti in una configurazione da sito a sito o da rete virtuale a rete virtuale. Per altre informazioni, vedere il documento [Risoluzione dei nomi per le macchine virtuali e i servizi cloud](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

3. Creare un gateway VPN per la rete virtuale. Per altre informazioni sulle configurazioni del gateway VPN, vedere il documento [Informazioni sul gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

4. Creare HDInsight all'interno della rete virtuale. Se è stato configurato un server DNS personalizzato per la rete, HDInsight viene configurato automaticamente per poterlo usare.

5. (Facoltativo) Se non si usa un server DNS personalizzato e non si dispone della risoluzione dei nomi tra i client e la rete virtuale, è necessario configurare Kafka per la pubblicità IP. Per altre informazioni, vedere la sezione [Configurare Kafka per la pubblicità IP](#configure-kafka-for-ip-advertising) in questo documento.

## <a name="create-using-powershell"></a>Creare: tramite PowerShell

La procedura descritta in questa sezione consente creare la configurazione seguente tramite [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/):

* Rete virtuale di Azure
* Gateway VPN da punto a sito
* Account di archiviazione di Azure, usato da HDInsight
* Kafka in HDInsight

1. Seguire la procedura illustrata nel documento [Usare i certificati autofirmati per le connessioni da punto a sito](../vpn-gateway/vpn-gateway-certificates-point-to-site.md) per creare i certificati necessari per il gateway.

2. Aprire un prompt di PowerShell e usare il codice seguente per accedere alla sottoscrizione di Azure:

    ```powershell
    Add-AzureRmAccount
    # If you have multiple subscriptions, uncomment to set the subscription
    #Select-AzureRmSubscription -SubscriptionName "name of your subscription"
    ```

3. Usare il codice seguente per creare le variabili che contengono le informazioni di configurazione:

    ```powershell
    # Prompt for generic information
    $resourceGroupName = Read-Host "What is the resource group name?"
    $baseName = Read-Host "What is the base name? It is used to create names for resources, such as 'net-basename' and 'kafka-basename':"
    $location = Read-Host "What Azure Region do you want to create the resources in?"
    $rootCert = Read-Host "What is the file path to the root certificate? It is used to secure the VPN gateway."

    # Prompt for HDInsight credentials
    $adminCreds = Get-Credential -Message "Enter the HTTPS user name and password for the HDInsight cluster" -UserName "admin"
    $sshCreds = Get-Credential -Message "Enter the SSH user name and password for the HDInsight cluster" -UserName "sshuser"

    # Names for Azure resources
    $networkName = "net-$baseName"
    $clusterName = "kafka-$baseName"
    $storageName = "store$baseName" # Can't use dashes in storage names
    $defaultContainerName = $clusterName
    $defaultSubnetName = "default"
    $gatewaySubnetName = "GatewaySubnet"
    $gatewayPublicIpName = "GatewayIp"
    $gatewayIpConfigName = "GatewayConfig"
    $vpnRootCertName = "rootcert"
    $vpnName = "VPNGateway"

    # Network settings
    $networkAddressPrefix = "10.0.0.0/16"
    $defaultSubnetPrefix = "10.0.0.0/24"
    $gatewaySubnetPrefix = "10.0.1.0/24"
    $vpnClientAddressPool = "172.16.201.0/24"

    # HDInsight settings
    $HdiWorkerNodes = 4
    $hdiVersion = "3.5"
    $hdiType = "Kafka"
    ```

4. Usare il codice seguente per creare il gruppo di risorse di Azure e la rete virtuale:

    ```powershell
    # Create the resource group that contains everything
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create the subnet configuration
    $defaultSubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -AddressPrefix $defaultSubnetPrefix
    $gatewaySubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -AddressPrefix $gatewaySubnetPrefix

    # Create the subnet
    New-AzureRmVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AddressPrefix $networkAddressPrefix `
        -Subnet $defaultSubnetConfig, $gatewaySubnetConfig

    # Get the network & subnet that were created
    $network = Get-AzureRmVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName
    $gatewaySubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -VirtualNetwork $network
    $defaultSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -VirtualNetwork $network

    # Set a dynamic public IP address for the gateway subnet
    $gatewayPublicIp = New-AzureRmPublicIpAddress -Name $gatewayPublicIpName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AllocationMethod Dynamic
    $gatewayIpConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $gatewayIpConfigName `
        -Subnet $gatewaySubnet `
        -PublicIpAddress $gatewayPublicIp

    # Get the certificate info
    # Get the full path in case a relative path was passed
    $rootCertFile = Get-ChildItem $rootCert
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($rootCertFile)
    $certBase64 = [System.Convert]::ToBase64String($cert.RawData)
    $p2sRootCert = New-AzureRmVpnClientRootCertificate -Name $vpnRootCertName `
        -PublicCertData $certBase64

    # Create the VPN gateway
    New-AzureRmVirtualNetworkGateway -Name $vpnName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -IpConfigurations $gatewayIpConfig `
        -GatewayType Vpn `
        -VpnType RouteBased `
        -EnableBgp $false `
        -GatewaySku Standard `
        -VpnClientAddressPool $vpnClientAddressPool `
        -VpnClientRootCertificates $p2sRootCert
    ```

    > [!WARNING]
    > Il completamento del processo richiede qualche minuto.

5. Per creare un contenitore BLOB e l'account di archiviazione di Azure, usare il codice seguente:

    ```powershell
    # Create the storage account
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageName `
        -Type Standard_GRS `
        -Location $location

    # Get the storage account keys and create a context
    $defaultStorageKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName `
        -Name $storageName)[0].Value
    $storageContext = New-AzureStorageContext -StorageAccountName $storageName `
        -StorageAccountKey $defaultStorageKey

    # Create the default storage container
    New-AzureStorageContainer -Name $defaultContainerName `
        -Context $storageContext
    ```

6. Per creare il cluster HDInsight usare il codice seguente:

    ```powershell
    # Create the HDInsight cluster
    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $hdiWorkerNodes `
        -ClusterType $hdiType `
        -OSType Linux `
        -Version $hdiVersion `
        -HttpCredential $adminCreds `
        -SshCredential $sshCreds `
        -DefaultStorageAccountName "$storageName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageKey `
        -DefaultStorageContainer $defaultContainerName `
        -VirtualNetworkId $network.Id `
        -SubnetName $defaultSubnet.Id
    ```

  > [!WARNING]
  > Il completamento di questa procedura richiede circa 20 minuti.

8. Usare il cmdlet seguente per recuperare l'URL per il client VPN di Windows per la rete virtuale:

    ```powershell
    Get-AzureRmVpnClientPackage -ResourceGroupName $resourceGroupName `
        -VirtualNetworkGatewayName $vpnName `
        -ProcessorArchitecture Amd64
    ```

    Per scaricare il client VPN di Windows, usare l'URI restituito nel Web browser.

## <a name="configure-kafka-for-ip-advertising"></a>Configurare Kafka per la pubblicità IP

Per impostazione predefinita, Zookeeper restituisce il nome di dominio dei broker di Kafka ai client. Questa configurazione non funziona per il client VPN, poiché non può usare la risoluzione dei nomi per le entità nella rete virtuale. Per configurare Kafka in HDInsight affinché possa creare pubblicità per gli indirizzi IP anziché per i nomi di dominio usare la procedura seguente:

1. Tramite il Web browser, aprire https://CLUSTERNAME.azurehdinsight.net. Sostituire __CLUSTERNAME__ con il nome di Kafka nel cluster HDInsight.

    Quando richiesto, usare il nome utente HTTPS e la password per il cluster. Viene visualizzata l'interfaccia utente di Ambari Web per il cluster.

2. Per visualizzare le informazioni su Kafka, selezionare __Kafka__ dall'elenco a sinistra. 

    ![Elenco di servizio con Kafka evidenziato](./media/hdinsight-apache-kafka-connect-vpn-gateway/select-kafka-service.png)

3. Per visualizzare la configurazione di Kafka, selezionare __Configs__ (Configurazioni) nella parte centrale in alto.

    ![Collegamenti Configs (Configurazioni) per Kafka](./media/hdinsight-apache-kafka-connect-vpn-gateway/select-kafka-config.png)

4. Per trovare la configurazione __kafka-env__, immettere `kafka-env` nel campo __Filtro__ in alto a destra.

    ![Configurazione Kafka per kafka-env](./media/hdinsight-apache-kafka-connect-vpn-gateway/search-for-kafka-env.png)

5. Per configurare Kafka affinché generi pubblicità per gli indirizzi IP, aggiungere il testo seguente nella parte inferiore del campo __kafka-env-template__:

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Per configurare l'interfaccia per cui è in ascolto Kafka immettere `listeners` nel campo __Filtro__ in alto a destra.

7. Per configurare tutte le interfacce di rete per cui Kafka è in ascolto, modificare il valore nel campo __Listener__ su `PLAINTEXT://0.0.0.0:92092`.

8. Per salvare le modifiche alla configurazione usare il pulsante __Salva__. Immettere un messaggio di testo che descrive le modifiche. Selezionare __OK__ dopo aver salvato le modifiche.

    ![Pulsante per salvare la configurazione](./media/hdinsight-apache-kafka-connect-vpn-gateway/save-button.png)

9. Per evitare errori al riavvio di Kafka, usare il pulsante __Service Actions__ (Azioni del servizio) e selezionare __Attiva modalità di manutenzione__. Per completare questa operazione selezionare OK.

    ![Azioni di servizio, con il pulsante di attivazione manutenzione evidenziato](./media/hdinsight-apache-kafka-connect-vpn-gateway/turn-on-maintenance-mode.png)

10. Per riavviare Kafka, utilizzare il pulsante __Riavvia__ e selezionare __Restart All Affected__ (Riavviare tutti gli elementi interessati). Confermare il riavvio, quindi usare il pulsante __OK__ dopo aver completato l'operazione.

    ![Pulsante di riavvio con Restart all affected (Riavviare tutti gli elementi interessati) evidenziato](./media/hdinsight-apache-kafka-connect-vpn-gateway/restart-button.png)

11. Per disabilitare la modalità di manutenzione, usare il pulsante __Service Actions__ (Azioni del servizio) e selezionare __Disattiva modalità di manutenzione__. Per completare questa operazione selezionare **OK**.

## <a name="connect-to-the-vpn-gateway"></a>Connettersi al gateway VPN

Per connettersi al gateway VPN da un __client Windows__, usare la sezione __Connettersi ad Azure__ del documento [Configurare una connessione da punto a sito](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md#a-nameconnectapart-7---connect-to-azure).

## <a name="remote-kafka-client"></a>Client Kafka remoto

Per connettersi a Kafka dal computer client, è necessario usare l'indirizzo IP del broker di Kafka o i nodi Zookeeper, a seconda del client che li richiede. Usare la procedura seguente per recuperare l'indirizzo IP dei broker di Kafka e quindi usarli da un'applicazione Python

1. Per recuperare gli indirizzi IP dei nodi nel cluster usare lo script seguente:

    ```powershell
    # Get the NICs for the HDInsight workernodes (names contain 'workernode').
    $nodes = Get-AzureRmNetworkInterface `
        -ResourceGroupName $resourceGroupName `
        | where-object {$_.Name -like "*workernode*"}

    # Loop through each node and get the IP address
    foreach($node in $nodes) {
        $node.IpConfigurations.PrivateIpAddress
    }
    ```

    Questo script presuppone che `$resourceGroupName` sia il nome del gruppo di risorse di Azure che contiene la rete virtuale. L'output dello script è simile al testo seguente:

        10.0.0.12
        10.0.0.6
        10.0.0.13
        10.0.0.5

    > [!NOTE]
    > Se il client Kafka usa nodi Zookeeper anziché broker di Kafka, sostituire `*workernode*` con `*zookeepernode*` nello script di PowerShell.

    > [!WARNING]
    > Se si modifica la scala del cluster o nodi hanno esito negativo e vengono sostituiti, gli indirizzi IP possono cambiare. Attualmente non è possibile pre-assegnare indirizzi IP specifici per i nodi in un cluster HDInsight.

2. Per installare il client [kafka-python](http://kafka-python.readthedocs.io/) usare il codice seguente:

        pip install kafka-python

3. Per inviare dati a Kafka, usare il seguente codice Python:

  ```python
  from kafka import KafkaProducer
  # Replace the `ip_address` entries with the IP address of your worker nodes
  producer = KafkaProducer(bootstrap_servers=['ip_address1','ip_address2','ip_adderess3','ip_address4'])
  for _ in range(50):
      producer.send('testtopic', b'test message')
  ```

    Sostituire le voci `'ip_address'` con gli indirizzi restituiti dal passaggio 1 in questa sezione.
    
    > [!NOTE]
    > Questo codice invia la stringa `test message` all'argomento `testtopic`. La configurazione predefinita di Kafka in HDInsight consiste nel creare l'argomento se non esiste.

4. Per recuperare i messaggi da Kafka, usare il seguente codice Python:

   ```python
   from kafka import KafkaConsumer
   # Replace the `ip_address` entries with the IP address of your worker nodes
   # Note: auto_offset_reset='earliest' resets the starting offset to the beginning
   #       of the topic
   consumer = KafkaConsumer(bootstrap_servers=['ip_address1','ip_address2','ip_adderess3','ip_address4'],auto_offset_reset='earliest')
   consumer.subscribe(['testtopic'])
   for msg in consumer:
     print (msg)
   ```

    Sostituire le voci `'ip_address'` con gli indirizzi restituiti dal passaggio 1 in questa sezione. L'output contiene il messaggio di prova inviato al produttore nel passaggio precedente.

## <a name="troubleshooting"></a>Risoluzione dei problemi

In caso di problemi di connessione alla rete virtuale o se la connessione a HDInsight avviene attraverso la rete, vedere il documento [Risolvere i problemi relativi al gateway di rete virtuale e alle connessioni](../network-watcher/network-watcher-troubleshoot-manage-powershell.md) per informazioni aggiuntive.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla creazione di una rete virtuale di Azure con gateway VPN da punto a sito, vedere i seguenti documenti:

* [Configurare una connessione da punto a sito a una rete virtuale usando il portale di Azure](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)

* [Configurare una connessione da punto a sito a una rete virtuale usando Azure PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

Per altre informazioni sull'uso della gestione di Kafka in HDInsight, vedere i documenti seguenti:

* [Get started with Kafka on HDInsight](hdinsight-apache-kafka-get-started.md) (Introduzione a Kafka in HDInsight)
* [Usare il mirroring con Kafka in HDInsight](hdinsight-apache-kafka-mirroring.md)

