---
title: Creare reti virtuali per i cluster HDInsight di Azure
description: Informazioni su come creare una rete virtuale di Azure per connettere HDInsight ad altre risorse cloud o risorse nel Data Center.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 2fd4c20e5c1cd0a8e1ee6f7c36d4b4a8b99d37ea
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442012"
---
# <a name="create-virtual-networks-for-azure-hdinsight-clusters"></a>Creare reti virtuali per i cluster HDInsight di Azure

Questo articolo fornisce esempi ed esempi di codice per la creazione e la configurazione di [reti virtuali di Azure](../virtual-network/virtual-networks-overview.md) da usare con i cluster HDInsight di Azure. Vengono presentati esempi dettagliati della creazione di gruppi di sicurezza di rete (gruppi) e della configurazione del DNS. 

Per informazioni generali sull'uso di reti virtuali con Azure HDInsight, vedere [pianificare una rete virtuale per Azure HDInsight](hdinsight-plan-virtual-network-deployment.md).

## <a name="prerequisites-for-code-samples-and-examples"></a>Prerequisiti per esempi di codice ed esempi

Prima di eseguire uno degli esempi di codice in questo articolo, l'unità organizzativa deve comprendere la rete TCP/IP. Se non si ha familiarità con la rete TCP/IP, consultare un utente che prima di apportare modifiche alle reti di produzione.

Altri prerequisiti per gli esempi in questo articolo sono i seguenti:

* Se si usa PowerShell, sarà necessario installare il [modulo AZ](https://docs.microsoft.com/powershell/azure/overview).
* Se si vuole usare l'interfaccia della riga di comando di Azure e non è ancora stata installata, vedere [installare l'interfaccia della](https://docs.microsoft.com/cli/azure/install-azure-cli)riga di comando di Azure.

> [!IMPORTANT]  
> Per istruzioni dettagliate sulla connessione di HDInsight alla rete locale tramite Rete virtuale di Microsoft Azure, vedere il documento [Connettere HDInsight alla rete locale](connect-on-premises-network.md).

## <a id="hdinsight-nsg"></a>Ad esempio: gruppi di sicurezza di rete con HDInsight

Gli esempi in questa sezione illustrano come creare regole per i gruppi di sicurezza di rete che consentano a HDInsight di comunicare con i servizi di gestione di Azure. Prima di usare gli esempi, modificare gli indirizzi IP in modo che corrispondano a quelli per l'area di Azure in uso. È possibile trovare queste informazioni negli [indirizzi IP di gestione di HDInsight](hdinsight-management-ip-addresses.md).

### <a name="azure-resource-management-template"></a>Modello di Resource Manager di Azure

Il modello di Resource Manager seguente crea una rete virtuale che limita il traffico in ingresso, ma consente il traffico dagli indirizzi IP richiesti da HDInsight. Questo modello crea anche un cluster HDInsight nella rete virtuale.

* [Deploy a secured Azure Virtual Network and an HDInsight Hadoop cluster](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/) (Distribuire una rete virtuale di Azure protetta e un cluster Hadoop HDInsight)

### <a name="azure-powershell"></a>Azure PowerShell

Usare lo script di PowerShell seguente per creare una rete virtuale che limita il traffico in ingresso e consente il traffico dagli indirizzi IP per l'area Europa settentrionale.

> [!IMPORTANT]  
> Modificare gli indirizzi IP per `hdirule1` e `hdirule2` in questo esempio in modo che corrispondano all'area di Azure in uso. È possibile trovare queste informazioni sugli [indirizzi IP di gestione di HDInsight](hdinsight-management-ip-addresses.md).

```powershell
$vnetName = "Replace with your virtual network name"
$resourceGroupName = "Replace with the resource group the virtual network is in"
$subnetName = "Replace with the name of the subnet that you plan to use for HDInsight"

# Get the Virtual Network object
$vnet = Get-AzVirtualNetwork `
    -Name $vnetName `
    -ResourceGroupName $resourceGroupName

# Get the region the Virtual network is in.
$location = $vnet.Location

# Get the subnet object
$subnet = $vnet.Subnets | Where-Object Name -eq $subnetName

# Create a Network Security Group.
# And add exemptions for the HDInsight health and management services.
$nsg = New-AzNetworkSecurityGroup `
    -Name "hdisecure" `
    -ResourceGroupName $resourceGroupName `
    -Location $location `
    | Add-AzNetworkSecurityRuleConfig `
        -name "hdirule1" `
        -Description "HDI health and management address 52.164.210.96" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "52.164.210.96" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 300 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule2" `
        -Description "HDI health and management 13.74.153.132" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "13.74.153.132" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 301 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule3" `
        -Description "HDI health and management 168.61.49.99" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.49.99" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 302 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule4" `
        -Description "HDI health and management 23.99.5.239" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "23.99.5.239" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 303 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule5" `
        -Description "HDI health and management 168.61.48.131" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.48.131" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 304 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule6" `
        -Description "HDI health and management 138.91.141.162" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "138.91.141.162" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 305 `
        -Direction Inbound `

# Set the changes to the security group
Set-AzNetworkSecurityGroup -NetworkSecurityGroup $nsg

# Apply the NSG to the subnet
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name $subnetName `
    -AddressPrefix $subnet.AddressPrefix `
    -NetworkSecurityGroup $nsg
$vnet | Set-AzVirtualNetwork
```

Questo esempio illustra come aggiungere le regole per consentire il traffico in ingresso sugli indirizzi IP richiesti. Non contiene regole per limitare l'accesso in ingresso da altre origini. Il codice seguente illustra come abilitare l'accesso SSH da Internet:

```powershell
Get-AzNetworkSecurityGroup -Name hdisecure -ResourceGroupName RESOURCEGROUP |
Add-AzNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 306 -Direction Inbound
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Seguire questa procedura per creare una rete virtuale che limita il traffico in ingresso, ma consente il traffico dagli indirizzi IP richiesti da HDInsight.

1. Usare il comando seguente per creare un nuovo gruppo di sicurezza di rete denominato `hdisecure`. Sostituire `RESOURCEGROUP` con il gruppo di risorse che contiene la rete virtuale di Azure. Sostituire `LOCATION` con il percorso (area) in cui è stato creato il gruppo.

    ```azurecli
    az network nsg create -g RESOURCEGROUP -n hdisecure -l LOCATION
    ```

    Dopo aver creato il gruppo si ricevono informazioni sul nuovo gruppo.

2. Usare le informazioni seguenti per aggiungere regole al nuovo gruppo di sicurezza di rete che ammettono la comunicazione in ingresso sulla porta 443 dal servizio integrità e gestione di Azure HDInsight. Sostituire `RESOURCEGROUP` con il nome del gruppo di risorse che contiene la rete virtuale di Azure.

    > [!IMPORTANT]  
    > Modificare gli indirizzi IP per `hdirule1` e `hdirule2` in questo esempio in modo che corrispondano all'area di Azure in uso. È possibile trovare queste informazioni negli [indirizzi IP di gestione di HDInsight](hdinsight-management-ip-addresses.md).

    ```azurecli
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "52.164.210.96" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "13.74.153.132" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule3 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule6 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 305 --direction "Inbound"
    ```

3. Per recuperare l'identificatore univoco per questo gruppo di sicurezza di rete, usare il comando seguente:

    ```azurecli
    az network nsg show -g RESOURCEGROUP -n hdisecure --query "id"
    ```

    Il comando restituisce un valore simile al testo seguente:

        "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"

4. Usare il comando seguente per applicare il gruppo di sicurezza di rete a una subnet. Sostituire i `GUID` valori `RESOURCEGROUP` e con quelli restituiti nel passaggio precedente. Sostituire `VNETNAME` e`SUBNETNAME` con il nome della rete virtuale e il nome della subnet che si desidera creare.

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUP --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    Dopo il completamento del comando, è possibile installare HDInsight nella rete virtuale.


Questa procedura consente di accedere solo al servizio di gestione e integrità di HDInsight nel cloud di Azure. Qualsiasi altro accesso al cluster HDInsight dall'esterno della rete virtuale è bloccato. Per abilitare l'accesso dall'esterno della rete virtuale è necessario aggiungere altre regole del gruppo di sicurezza di rete.

Il codice seguente illustra come abilitare l'accesso SSH da Internet:

```azurecli
az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n ssh --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 306 --direction "Inbound"
```

## <a id="example-dns"></a> Esempio: Configurazione del DNS

### <a name="name-resolution-between-a-virtual-network-and-a-connected-on-premises-network"></a>Risoluzione dei nomi tra una rete virtuale e una rete locale connessa

Questo esempio si basa sui presupposti seguenti:

* Si dispone di una rete virtuale di Azure che è connessa a una rete locale tramite un gateway VPN.

* Il server DNS personalizzato nella rete virtuale esegue il sistema operativo Linux o Unix.

* Nel server DNS personalizzato è installato [Bind](https://www.isc.org/downloads/bind/).

Nel server DNS personalizzato nella rete virtuale:

1. Usare Azure PowerShell o l'interfaccia della riga di comando di Azure per individuare il suffisso DNS della rete virtuale:

    Sostituire `RESOURCEGROUP` con il nome del gruppo di risorse che contiene la rete virtuale, quindi immettere il comando:

    ```powershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. Nel server DNS personalizzato per la rete virtuale, usare il testo seguente come contenuto del file `/etc/bind/named.conf.local`:

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {168.63.129.16;}; # Azure recursive resolver
    };
    ```

    Sostituire il valore `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` con il suffisso DNS della rete virtuale.

    Questa configurazione indirizza tutte le richieste DNS per il suffisso DNS della rete virtuale al sistema di risoluzione ricorsiva di Azure.

2. Nel server DNS personalizzato per la rete virtuale, usare il testo seguente come contenuto del file `/etc/bind/named.conf.options`:

    ```
    // Clients to accept requests from
    // TODO: Add the IP range of the joined network to this list
    acl goodclients {
        10.0.0.0/16; # IP address range of the virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            # All other requests are sent to the following
            forwarders {
                192.168.0.1; # Replace with the IP address of your on-premises DNS server
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```
    
    * Sostituire il valore `10.0.0.0/16` con l'intervallo di indirizzi IP della rete virtuale. Questa voce consente l'indirizzamento delle richieste di risoluzione dei nomi all'interno di questo intervallo.

    * Aggiungere l'intervallo di indirizzi IP della rete locale alla sezione `acl goodclients { ... }`.  Questa voce consente le richieste di risoluzione dei nomi dalle risorse nella rete locale.
    
    * Sostituire il valore `192.168.0.1` con l'indirizzo IP del server DNS locale. Questa voce indirizza tutte le altre richieste DNS al server DNS locale.

3. Per usare la configurazione, riavviare Bind. Ad esempio `sudo service bind9 restart`.

4. Aggiungere un server d'inoltro condizionale al server DNS locale. Configurare il server d'inoltro condizionale per l'invio di richieste del suffisso DNS del passaggio 1 al server DNS personalizzato.

    > [!NOTE]  
    > Per informazioni dettagliate su come aggiungere un server d'inoltro condizionale, consultare la documentazione del software del DNS.

Dopo avere completato questi passaggi, è possibile connettersi alle risorse in entrambe le reti usando i nomi di dominio completi (FQDN). È ora possibile installare HDInsight nella rete virtuale.

### <a name="name-resolution-between-two-connected-virtual-networks"></a>Risoluzione dei nomi tra due reti virtuali connesse

Questo esempio si basa sui presupposti seguenti:

* Si dispone di due reti virtuali di Azure connesse tramite gateway VPN o peering.

* Il server DNS personalizzato in entrambe le reti esegue il sistema operativo Linux o Unix.

* Nei server DNS personalizzati è installato [Bind](https://www.isc.org/downloads/bind/).

1. Usare Azure PowerShell o l'interfaccia della riga di comando di Azure per individuare il suffisso DNS di entrambe le reti virtuali:

    Sostituire `RESOURCEGROUP` con il nome del gruppo di risorse che contiene la rete virtuale, quindi immettere il comando:

    ```powershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. Usare il testo seguente come contenuto del file `/etc/bind/named.config.local` nel server DNS personalizzato. Apportare questa modifica al server DNS personalizzato in entrambe le reti virtuali.

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The IP address of the DNS server in the other virtual network
    };
    ```

    Sostituire il valore `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` con il suffisso DNS dell'__altra__ rete virtuale. Questa voce indirizza le richieste per il suffisso DNS della rete remota al DNS personalizzato di quella rete.

3. Nei server DNS personalizzati in entrambe le reti virtuali, usare il testo seguente come contenuto del file `/etc/bind/named.conf.options`:

    ```
    // Clients to accept requests from
    acl goodclients {
        10.1.0.0/16; # The IP address range of one virtual network
        10.0.0.0/16; # The IP address range of the other virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            forwarders {
            168.63.129.16;   # Azure recursive resolver         
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```
    
   Sostituire i valori `10.0.0.0/16` e `10.1.0.0/16` all'interno degli intervalli di indirizzi IP delle reti virtuali. Questa voce consente alle risorse in ogni rete di eseguire richieste dei server DNS.

    Tutte le richieste che non riguardano i suffissi DNS delle reti virtuali (ad esempio microsoft.com) vengono gestite dal sistema di risoluzione ricorsiva di Azure.

4. Per usare la configurazione, riavviare Bind. Ad esempio `sudo service bind9 restart` su entrambi i server DNS.

Dopo avere completato questi passaggi, è possibile connettersi alle risorse nella rete virtuale usando i nomi di dominio completi (FQDN). È ora possibile installare HDInsight nella rete virtuale.

## <a name="next-steps"></a>Passaggi successivi

* Per un esempio completo di configurazione di HDInsight per la connessione a una rete locale, vedere [Connettere HDInsight alla rete locale](./connect-on-premises-network.md).
* Per la configurazione di cluster Apache HBase in reti virtuali di Azure, vedere [creare cluster Apache HBase in HDInsight in rete virtuale di Azure](hbase/apache-hbase-provision-vnet.md).
* Per la configurazione della replica geografica di Apache HBase, vedere [Configurare la replica di cluster Apache HBase nelle reti virtuali di Azure](hbase/apache-hbase-replication.md).
* Per altre informazioni sulle reti virtuali di Azure, vedere la [panoramica sulle reti virtuali di Azure](../virtual-network/virtual-networks-overview.md).

* Per altre informazioni sui gruppi di sicurezza di rete, vedere [Gruppi di sicurezza di rete](../virtual-network/security-overview.md).

* Per altre informazioni sulle route definite dall'utente, vedere [Route definite dall'utente e inoltro IP](../virtual-network/virtual-networks-udr-overview.md).
