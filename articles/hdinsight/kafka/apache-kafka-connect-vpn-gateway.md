---
title: Connettersi a Kafka tramite le reti virtuali - Azure HDInsight
description: Informazioni su come connettersi direttamente a Kafka in HDInsight tramite una rete virtuale di Azure. Informazioni su come connettersi a Kafka dai client di sviluppo tramite un gateway VPN o dai client nella rete locale tramite un dispositivo gateway VPN.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 6807a14bb7b46389abbc4c5a44e7699c79e29dff
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361614"
---
# <a name="connect-to-apache-kafka-on-hdinsight-through-an-azure-virtual-network"></a>Connettersi ad Apache Kafka in HDInsight tramite una rete virtuale di Azure

Informazioni su come connettersi direttamente ad Apache Kafka in HDInsight tramite una rete virtuale di Azure. Questo documento contiene informazioni sulla connessione a Kafka con le configurazioni seguenti:

* Dalle risorse in una rete locale. La connessione viene stabilita tramite un dispositivo VPN, un software o un hardware, nella rete locale.
* Da un ambiente di sviluppo tramite un client software VPN.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="architecture-and-planning"></a>Architettura e pianificazione

HDInsight non consente la connessione diretta a Kafka nella rete Internet pubblica. I client di Kafka, produttori e clienti, invece, devono usare uno dei seguenti metodi di connessione:

* Eseguire il client nella stessa rete virtuale di Kafka in HDInsight. Questa configurazione viene usata nel documento [Iniziare a usare Apache Kafka in HDInsight](apache-kafka-get-started.md). Il client viene eseguito direttamente nei nodi del cluster di HDInsight o in un'altra macchina virtuale nella stessa rete.

* Collegare una rete privata, ad esempio la rete locale, alla rete virtuale. Questa configurazione consente ai client nella rete locale per lavorare direttamente con Kafka. Per abilitare questa configurazione, eseguire le attività seguenti:

  1. Creare una rete virtuale.
  2. Creare un gateway VPN che usi una configurazione da sito a sito. La configurazione usata in questo documento si connette a un dispositivo gateway VPN nella rete locale.
  3. Creare un server DNS nella rete virtuale.
  4. Configurare l'inoltro tra il server DNS in ogni rete.
  5. Creare un cluster Kafka in HDInsight nella rete virtuale.

     Per altre informazioni, vedere la sezione [Connettersi ad Apache Kafka da una rete locale](#on-premises). 

* Connettere i computer individualmente alla rete virtuale usando un gateway VPN e il client VPN. Per abilitare questa configurazione, eseguire le attività seguenti:

  1. Creare una rete virtuale.
  2. Creare un gateway VPN che usi una configurazione da punto a sito. Questa configurazione può essere usata sia con i client Windows che con i client MacOS.
  3. Creare un cluster Kafka in HDInsight nella rete virtuale.
  4. Configurare Kafka per la pubblicità IP. Questa configurazione consente al client di connettersi usando l'indirizzo IP broker anziché i nomi di dominio.
  5. Scaricare e usare il client VPN nel sistema di sviluppo.

     Per altre informazioni, vedere la sezione [Connettersi ad Apache Kafka con un client VPN](#vpnclient).

     > [!WARNING]  
     > Questa configurazione è consigliata solo per scopi di sviluppo a causa delle limitazioni seguenti:
     >
     > * Ogni client deve connettersi usando un client software VPN.
     > * Il client VPN non passa le richieste di risoluzione dei nomi alla rete virtuale, quindi per comunicare con Kafka è necessario usare gli indirizzi IP. La comunicazione tramite IP richiede una configurazione aggiuntiva nel cluster Kafka.

Per altre informazioni sull'uso di HDInsight in una rete virtuale, vedere [Estendere le funzionalità di HDInsight usando Rete virtuale di Azure](../hdinsight-extend-hadoop-virtual-network.md).

## <a id="on-premises"></a> Connettersi ad Apache Kafka da una rete locale

Per creare un cluster Kafka che comunichi con la rete locale, seguire i passaggi descritti nel documento [Connect HDInsight to your on-premises network](./../connect-on-premises-network.md) (Connettere HDInsight alla rete locale).

> [!IMPORTANT]  
> Quando si crea il cluster HDInsight, selezionare il tipo di cluster __Kafka__.

Questi passaggi creano la configurazione seguente:

* Rete virtuale di Azure
* Gateway VPN da sito a sito
* Account di archiviazione di Azure, usato da HDInsight
* Kafka in HDInsight

Per verificare che un client Kafka riesce a connettersi al cluster da locale, usare la procedura descritta nella sezione [Esempio: client Python](#python-client).

## <a id="vpnclient"></a> Connettersi ad Apache Kafka con un client VPN

Usare la procedura descritta in questa sezione per creare la configurazione seguente:

* Rete virtuale di Azure
* Gateway VPN da punto a sito
* Account di archiviazione di Azure, usato da HDInsight
* Kafka in HDInsight

1. Seguire la procedura nel documento [Usare i certificati autofirmati per le connessioni da punto a sito](../../vpn-gateway/vpn-gateway-certificates-point-to-site.md). Questo documento consente di creare i certificati necessari per il gateway.

2. Aprire un prompt di PowerShell e usare il codice seguente per accedere alla sottoscrizione di Azure:

    ```powershell
    Connect-AzAccount
    # If you have multiple subscriptions, uncomment to set the subscription
    #Select-AzSubscription -SubscriptionName "name of your subscription"
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
    $hdiVersion = "3.6"
    $hdiType = "Kafka"
    ```

4. Usare il codice seguente per creare il gruppo di risorse di Azure e la rete virtuale:

    ```powershell
    # Create the resource group that contains everything
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create the subnet configuration
    $defaultSubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -AddressPrefix $defaultSubnetPrefix
    $gatewaySubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -AddressPrefix $gatewaySubnetPrefix

    # Create the subnet
    New-AzVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AddressPrefix $networkAddressPrefix `
        -Subnet $defaultSubnetConfig, $gatewaySubnetConfig

    # Get the network & subnet that were created
    $network = Get-AzVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName
    $gatewaySubnet = Get-AzVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -VirtualNetwork $network
    $defaultSubnet = Get-AzVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -VirtualNetwork $network

    # Set a dynamic public IP address for the gateway subnet
    $gatewayPublicIp = New-AzPublicIpAddress -Name $gatewayPublicIpName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AllocationMethod Dynamic
    $gatewayIpConfig = New-AzVirtualNetworkGatewayIpConfig -Name $gatewayIpConfigName `
        -Subnet $gatewaySubnet `
        -PublicIpAddress $gatewayPublicIp

    # Get the certificate info
    # Get the full path in case a relative path was passed
    $rootCertFile = Get-ChildItem $rootCert
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($rootCertFile)
    $certBase64 = [System.Convert]::ToBase64String($cert.RawData)
    $p2sRootCert = New-AzVpnClientRootCertificate -Name $vpnRootCertName `
        -PublicCertData $certBase64

    # Create the VPN gateway
    New-AzVirtualNetworkGateway -Name $vpnName `
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
    New-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageName `
        -Type Standard_GRS `
        -Location $location

    # Get the storage account keys and create a context
    $defaultStorageKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName `
        -Name $storageName)[0].Value
    $storageContext = New-AzStorageContext -StorageAccountName $storageName `
        -StorageAccountKey $defaultStorageKey

    # Create the default storage container
    New-AzStorageContainer -Name $defaultContainerName `
        -Context $storageContext
    ```

6. Per creare il cluster HDInsight usare il codice seguente:

    ```powershell
    # Create the HDInsight cluster
    New-AzHDInsightCluster `
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
        -DisksPerWorkerNode 2 `
        -VirtualNetworkId $network.Id `
        -SubnetName $defaultSubnet.Id
    ```

   > [!WARNING]  
   > Il completamento di questo processo richiede circa 15 minuti.

### <a name="configure-kafka-for-ip-advertising"></a>Configurare Kafka per la pubblicità IP

Per impostazione predefinita, Apache ZooKeeper restituisce il nome di dominio dei broker di Kafka ai client. Questa configurazione non funziona con il client software VPN, poiché non può usare la risoluzione dei nomi per le entità nella rete virtuale. Per questa configurazione usare la procedura seguente per configurare Kafka in HDInsight affinché possa creare pubblicità per gli indirizzi IP anziché per i nomi di dominio:

1. Usando un Web browser, passare a https://CLUSTERNAME.azurehdinsight.net. Sostituire __CLUSTERNAME__ con il nome di Kafka nel cluster HDInsight.

    Quando richiesto, usare il nome utente HTTPS e la password per il cluster. Viene visualizzata l'interfaccia utente di Ambari Web per il cluster.

2. Per visualizzare le informazioni su Kafka, selezionare __Kafka__ dall'elenco a sinistra.

    ![Elenco di servizio con Kafka evidenziato](./media/apache-kafka-connect-vpn-gateway/select-kafka-service.png)

3. Per visualizzare la configurazione di Kafka, selezionare __Configs__ (Configurazioni) nella parte centrale in alto.

    ![Collegamenti Configs (Configurazioni) per Kafka](./media/apache-kafka-connect-vpn-gateway/select-kafka-config.png)

4. Per trovare la configurazione __kafka-env__, immettere `kafka-env` nel campo __Filtro__ in alto a destra.

    ![Configurazione Kafka per kafka-env](./media/apache-kafka-connect-vpn-gateway/search-for-kafka-env.png)

5. Per configurare Kafka affinché generi pubblicità per gli indirizzi IP, aggiungere il testo seguente nella parte inferiore del campo __kafka-env-template__:

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Per configurare l'interfaccia per cui è in ascolto Kafka immettere `listeners` nel campo __Filtro__ in alto a destra.

7. Per configurare tutte le interfacce di rete per cui Kafka è in ascolto, modificare il valore nel campo __Listener__ su `PLAINTEXT://0.0.0.0:9092`.

8. Per salvare le modifiche alla configurazione usare il pulsante __Salva__. Immettere un messaggio di testo che descrive le modifiche. Selezionare __OK__ dopo aver salvato le modifiche.

    ![Pulsante per salvare la configurazione](./media/apache-kafka-connect-vpn-gateway/save-button.png)

9. Per evitare errori al riavvio di Kafka, usare il pulsante __Service Actions__ (Azioni del servizio) e selezionare __Attiva modalità di manutenzione__. Per completare questa operazione selezionare OK.

    ![Azioni di servizio, con il pulsante di attivazione manutenzione evidenziato](./media/apache-kafka-connect-vpn-gateway/turn-on-maintenance-mode.png)

10. Per riavviare Kafka, utilizzare il pulsante __Riavvia__ e selezionare __Restart All Affected__ (Riavviare tutti gli elementi interessati). Confermare il riavvio, quindi usare il pulsante __OK__ dopo aver completato l'operazione.

    ![Pulsante di riavvio con Restart all affected (Riavviare tutti gli elementi interessati) evidenziato](./media/apache-kafka-connect-vpn-gateway/restart-button.png)

11. Per disabilitare la modalità di manutenzione, usare il pulsante __Service Actions__ (Azioni del servizio) e selezionare __Disattiva modalità di manutenzione__. Per completare questa operazione selezionare **OK**.

### <a name="connect-to-the-vpn-gateway"></a>Connettersi al gateway VPN

Per connettersi al gateway VPN, usare la sezione __Connettersi ad Azure__ del documento [Configurare una connessione da punto a sito](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md#connect).

## <a id="python-client"></a> Esempio: client Python

Per convalidare la connettività a Kafka, usare la procedura seguente per creare ed eseguire un produttore e un utente di Python:

1. Per recuperare il nome di dominio completo e gli indirizzi IP dei nodi nel cluster Kafka, usare uno dei metodi seguenti:

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

    $nodes = @()
    foreach($nic in $clusterNICs) {
        $node = new-object System.Object
        $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
        $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
        $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
        $nodes += $node
    }
    $nodes | sort-object Type
    ```

    ```azurecli
    az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

    Questo script presuppone che `$resourceGroupName` sia il nome del gruppo di risorse di Azure che contiene la rete virtuale.

    Salvare le informazioni restituite da usare nei passaggi successivi.

2. Per installare il client [kafka-python](https://kafka-python.readthedocs.io/) usare il codice seguente:

        pip install kafka-python

3. Per inviare dati a Kafka, usare il seguente codice Python:

   ```python
   from kafka import KafkaProducer
   # Replace the `ip_address` entries with the IP address of your worker nodes
   # NOTE: you don't need the full list of worker nodes, just one or two.
   producer = KafkaProducer(bootstrap_servers=['kafka_broker_1','kafka_broker_2'])
   for _ in range(50):
      producer.send('testtopic', b'test message')
   ```

    Sostituire le voci `'kafka_broker'` con gli indirizzi restituiti dal passaggio 1 in questa sezione:

   * Se si usa un __client VPN del software__, sostituire le voci `kafka_broker` con l'indirizzo IP dei nodi di lavoro.

   * Se è __abilitata la risoluzione dei nomi tramite un server DNS personalizzato__, sostituire le voci `kafka_broker` con il nome di dominio completo dei nodi di lavoro.

     > [!NOTE]
     > Questo codice invia la stringa `test message` all'argomento `testtopic`. La configurazione predefinita di Kafka in HDInsight consiste nel creare l'argomento se non esiste.

4. Per recuperare i messaggi da Kafka, usare il seguente codice Python:

   ```python
   from kafka import KafkaConsumer
   # Replace the `ip_address` entries with the IP address of your worker nodes
   # Again, you only need one or two, not the full list.
   # Note: auto_offset_reset='earliest' resets the starting offset to the beginning
   #       of the topic
   consumer = KafkaConsumer(bootstrap_servers=['kafka_broker_1','kafka_broker_2'],auto_offset_reset='earliest')
   consumer.subscribe(['testtopic'])
   for msg in consumer:
     print (msg)
   ```

    Sostituire le voci `'kafka_broker'` con gli indirizzi restituiti dal passaggio 1 in questa sezione:

    * Se si usa un __client VPN del software__, sostituire le voci `kafka_broker` con l'indirizzo IP dei nodi di lavoro.

    * Se è __abilitata la risoluzione dei nomi tramite un server DNS personalizzato__, sostituire le voci `kafka_broker` con il nome di dominio completo dei nodi di lavoro.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso di HDInsight con le reti virtuali, vedere il documento [Estendere le funzionalità di HDInsight usando Rete virtuale di Azure](../hdinsight-extend-hadoop-virtual-network.md).

Per altre informazioni sulla creazione di una rete virtuale di Azure con gateway VPN da punto a sito, vedere i seguenti documenti:

* [Configurare una connessione da punto a sito a una rete virtuale usando il portale di Azure](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)

* [Configurare una connessione da punto a sito a una rete virtuale usando Azure PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

Per altre informazioni sull'uso della gestione di Apache Kafka in HDInsight, vedere i documenti seguenti:

* [Introduzione ad Apache Kafka (anteprima) in HDInsight](apache-kafka-get-started.md)
* [Usare il mirroring con Apache Kafka in HDInsight](apache-kafka-mirroring.md)
