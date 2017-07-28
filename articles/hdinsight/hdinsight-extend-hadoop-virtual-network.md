---
title: Estendere HDInsight con Rete virtuale - Azure | Microsoft Docs
description: Informazioni su come usare Rete virtuale di Azure per la connessione di HDInsight ad altre risorse cloud o risorse nel proprio data center
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 37b9b600-d7f8-4cb1-a04a-0b3a827c6dcc
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/30/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: c0091f48518174f9aa0bc8022d1cd22694f8f8e7
ms.contentlocale: it-it
ms.lasthandoff: 07/01/2017


---
# <a name="extend-hdinsight-capabilities-by-using-azure-virtual-network"></a>Estendere le funzionalità di HDInsight usando Rete virtuale di Azure

Informazioni su come usare le reti virtuali di Azure con HDInsight per abilitare gli scenari seguenti:

* Limitare l'accesso a HDInsight. Ad esempio, impedire il traffico in ingresso da internet.

* Accedere direttamente ai servizi in HDInsight a cui non è possibile avere accesso tramite Internet. Ad esempio, lavorare direttamente con i Broker Kafka o usare l'API Java HBase.

* Connettere direttamente i servizi ad HDInsight. Ad esempio, per importare o esportare dati in un Server SQL all'interno del centro dati, usare Oozie.

* Creare soluzioni che interessano più cluster HDInsight. Ad esempio, per analizzare i dati archiviati in Kafka usare Spark o Storm.

## <a name="prerequisites"></a>Prerequisiti

* Interfaccia della riga di comando di Azure 2.0: per altre informazioni, vedere [Installare e configurare l'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2).

* Azure PowerShell: per altre informazioni, vedere [Installare e configurare Azure PowerShell](/powershell/azure/overview).

> [!NOTE]
> Per eseguire la procedura descritta in questo documento, è necessaria la versione più recente dell'interfaccia della riga di comando di Azure e di Azure PowerShell. Se si usa una versione precedente, i comandi possono essere diversi. Per ottenere risultati ottimali, usare i collegamenti precedenti per installare le versioni più recenti.

## <a id="whatis"></a>Che cos'è Rete virtuale di Azure

[Rete virtuale di Azure](https://azure.microsoft.com/documentation/services/virtual-network/) permette di creare una rete sicura e persistente che contiene le risorse necessarie per la propria soluzione.

Di seguito vi è un elenco di considerazioni per quando si usa HDInsight in una rete virtuale:

* __Reti virtuali classiche e di gestione risorse__: Usare la seguente tabella per determinare il tipo di rete per l'uso basato sul sistema operativo del cluster HDInsight:

    | Sistema operativo HDInsight | Rete virtuale classica | Rete virtuale di Resource Manager |
    | ---- | ---- | ---- |
    | Linux | no | sì |
    | Windows | sì | no |

    Per accedere alle risorse in una rete virtuale incompatibile, unire le due reti. Per altre informazioni sulla connessione tra reti virtuali classiche e reti di Azure Resource Manager, vedere [Connettere le reti virtuali classiche alle reti virtuali di Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

* __DNS personalizzato__: se è necessario abilitare la risoluzione dei nomi tra HDInsight e le risorse della rete locale, è necessario usare un server DNS personalizzato. Non è necessario un server DNS personalizzato per accedere alle risorse che sono pubblicamente disponibili su Internet.

    Per altre informazioni sull'uso di un server DNS personalizzato, vedere [Risoluzione dei nomi per le macchine virtuali e le istanze del ruolo](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

* __Tunneling forzato__: HDInsight non supporta il tunneling forzato.

* __Rete virtuale limitata__: è possibile installare HDInsight in una rete virtuale che limita il traffico in ingresso e in uscita. È necessario consentire l'accesso a determinati indirizzi IP per l'area di Azure che si sta usando.

    * __Gruppi di sicurezza di rete__: se si usano gruppi di sicurezza di rete, è necessario consentire l'accesso illimitato a più indirizzi IP di Azure. Per l'elenco di indirizzi IP, vedere la sezione [Indirizzi IP richiesti](#hdinsight-ip).

        Per altre informazioni, vedere la sezione [Gruppi di sicurezza di rete](#using-network-security-groups).

    * __Route definite dall'utente__: se si usano le route definite dall'utente, è necessario definire le route per vari indirizzi IP di Azure. Per l'elenco di indirizzi IP, vedere la sezione [Indirizzi IP richiesti](#hdinsight-ip).

        Per altre informazioni, vedere la sezione [Route definite dall'utente](#user-defined-routes).

    * __Appliance virtuale di rete__: se si usa un firewall dell'appliance virtuale, vedere la sezione [Firewall dell'appliance virtuale](#virtual-appliance-firewall) per un elenco di porte che devono essere consentite attraverso il firewall.

### <a name="connect-cloud-resources-together-in-a-private-network-cloud-only"></a>Connettere le risorse cloud tra loro in una rete privata (solo cloud)

![Diagramma di una configurazione solo cloud](media/hdinsight-extend-hadoop-virtual-network/cloud-only.png)

L'uso di Rete virtuale per collegare servizi di Azure ad Azure HDInsight rende possibili gli scenari seguenti:

* **Richiamo di servizi o processi di HDInsight** da siti Web o servizi di Azure in esecuzione in macchine virtuali di Azure.

* **Trasferimento diretto di dati** tra HDInsight e il database SQL di Azure, SQL Server o un'altra soluzione di archiviazione dei dati in esecuzione in una macchina virtuale.

* **Combinazione di più server HDInsight** in un'unica soluzione. Sono disponibili vari tipi di cluster HDInsight, corrispondenti al carico di lavoro o alla tecnologia per cui è ottimizzato il cluster. Non è disponibile alcun metodo supportato per creare un cluster che combini più tipi, ad esempio Storm e HBase in un cluster. L'uso di una rete virtuale consente a più cluster di comunicare direttamente tra loro.

### <a name="connect-cloud-resources-to-a-local-datacenter-network"></a>Connettere le risorse cloud a una rete di Data Center locale

La configurazione da sito a sito permette di connettere più risorse del centro dati alla rete virtuale di Azure. La connessione può essere eseguita con un dispositivo VPN hardware o con il servizio Routing e Accesso remoto.

![Diagramma di una configurazione da sito a sito](media/hdinsight-extend-hadoop-virtual-network/site-to-site.png)

La configurazione da punto a sito permette di connettere una risorsa specifica alla rete virtuale di Azure usando una rete VPN software.

![Diagramma di una configurazione da punto a sito](media/hdinsight-extend-hadoop-virtual-network/point-to-site.png)

L'uso di Rete virtuale per collegare il cloud e il data center permette scenari simili alla configurazione solo cloud. Tuttavia, invece di limitarsi a usare risorse nel cloud, permette anche di usare risorse nel data center.

* **Trasferimento diretto di dati** tra HDInsight e il data center. Un esempio consiste nell'usare Sqoop per trasferire dati da o verso SQL Server o per leggere i dati generati da un'applicazione line-of-business.

* **Richiamo di servizi o processi di HDInsight** da un'applicazione line-of-business. Un esempio consiste nell'usare API Java HBase per archiviare e recuperare dati da un cluster HBase di HDInsight.

Per altre informazioni sulle funzionalità, i vantaggi e le capacità della rete virtuale, vedere la [panoramica della rete virtuale di Azure](../virtual-network/virtual-networks-overview.md).

> [!NOTE]
> Creare la rete virtuale di Azure prima del provisioning di un cluster HDInsight, quindi specificare la rete durante la creazione del cluster. Se si prevede di usare un server DNS personalizzato, è necessario aggiungerlo alla rete virtuale prima di HDInsight. Per altre informazioni, vedere [Attività di configurazione della rete virtuale](https://azure.microsoft.com/documentation/services/virtual-network/).

##<a id="hdinsight-ip"></a> Indirizzi IP richiesti

Il servizio HDInsight è un servizio gestito, e richiede l'accesso ai servizi di gestione di Azure durante il provisioning e l'esecuzione. La gestione di Azure esegue i servizi seguenti:

* Verificare l'integrità dell’entità cluster
* Avviare il failover delle risorse del cluster
* Modificare il numero di nodi del cluster tramite operazioni di ridimensionamento
* Altre attività di gestione

> [!NOTE]
> Queste operazioni non richiedono l'accesso completo a internet. Quando si limita l'accesso a internet, consentire l'accesso in entrata sulla porta 443 per i seguenti indirizzi IP. In tal modo Azure è in grado di gestire HDInsight.

Se si limita l'accesso alla rete virtuale è necessario consentire l'accesso agli indirizzi IP di gestione. Gli indirizzi IP che dovrebbero essere concessi sono specifici all'area in cui risiedono il cluster HDInsight e la rete virtuale. Usare i dati indicati nella tabella seguente per trovare gli indirizzi IP per l'area in uso.

| Paese | Region | Indirizzi IP consentiti | Porta consentita |
| ---- | ---- | ---- | ---- |
| Asia | Asia orientale | 23.102.235.122</br>52.175.38.134 | 443 |
| Brasile | Brasile meridionale | 191.235.84.104</br>191.235.87.113 | 443 |
| Canada | Canada orientale | 52.229.127.96</br>52.229.123.172 | 443 |
| &nbsp; | Canada centrale | 52.228.37.66</br>52.228.45.222 | 443 |
| Cina | Cina settentrionale | 42.159.96.170</br>139.217.2.219 | 443 |
| &nbsp; | Cina orientale | 42.159.198.178</br>42.159.234.157 | 443 |
| Germania | Germania centrale | 51.4.146.68</br>51.4.146.80 | 443 |
| &nbsp; | Germania nord-orientale | 51.5.150.132</br>51.5.144.101 | 443 |
| India | India centrale | 52.172.153.209</br>52.172.152.49 | 443 |
| Giappone | Giappone orientale | 13.78.125.90</br>13.78.89.60 | 443 |
| &nbsp; | Giappone occidentale | 40.74.125.69</br>138.91.29.150 | 443 |
| Regno Unito | Regno Unito occidentale | 51.141.13.110</br>51.141.7.20 | 443 |
| &nbsp; | Regno Unito meridionale | 51.140.47.39</br>51.140.52.16 | 443 |
| Stati Uniti | Stati Uniti centrali | 13.67.223.215</br>40.86.83.253 | 443 |
| &nbsp; | Stati Uniti centro-occidentali | 52.161.23.15</br>52.161.10.167 | 443 |
| &nbsp; | Stati Uniti occidentali 2 | 52.175.211.210</br>52.175.222.222 | 443 |

Per informazioni sugli indirizzi IP da usare per Azure per enti pubblici, vedere il documento [Azure Government Intelligence + Analytics](../azure-government/documentation-government-services-intelligenceandanalytics.md).

__Se l'area non è elencata nella tabella__, consentire il traffico alla porta __443__ negli indirizzi IP seguenti:

* 168.61.49.99
* 23.99.5.239
* 168.61.48.131
* 138.91.141.162

> [!IMPORTANT]
> HDInsight non supporta la limitazione del traffico in uscita, solo del traffico in ingresso. Quando si definiscono regole del gruppo di sicurezza di rete per la subnet che contiene HDInsight, __usare solo regole in ingresso__.

> [!NOTE]
> Se si usa un server DNS personalizzato con la rete virtuale, è anche necessario consentire l'accesso da __168.63.129.16__. Questo è l'indirizzo del sistema di risoluzione ricorsivo di Azure. Per altre informazioni, vedere il documento [Risoluzione dei nomi per macchine virtuali e istanze del ruolo](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="network-security-groups"></a>Gruppi di sicurezza di rete

Se si blocca l'accesso a Internet usando i gruppi di sicurezza di rete, non è possibile usare i servizi HDInsight che sono in genere esposti tramite il gateway pubblico per un cluster. Tra questi ci sono Ambari e SSH. Al contrario, è necessario accedere ai servizi tramite l'indirizzo IP interno dei nodi head del cluster.

Per trovare l'indirizzo IP interno dei nodi head, usare gli script nella sezione [Estendere le funzionalità di HDInsight usando Rete virtuale di Azure](#internal-ips-and-fqdns).

### <a name="example-secured-virtual-network"></a>Esempio: Rete virtuale protetta

Negli esempi seguenti viene illustrato come creare un gruppo di sicurezza di rete che consente il traffico in ingresso sulla porta 443 dagli indirizzi IP seguenti:

* 168.61.49.99
* 23.99.5.239
* 168.61.48.131
* 138.91.141.162

> [!IMPORTANT]
> Questi indirizzi sono per le aree che non dispongono di indirizzi IP specifici elencati. Per trovare gli indirizzi IP per la propria area, usare le informazioni nella sezione [Reti virtuali protette](#secured-virtual-networks).

Questa procedura presuppone la creazione di una rete virtuale e della subnet in cui si desidera installare HDInsight. Vedere [Creare una rete virtuale usando il portale di Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

> [!WARNING]
> Le regole vengono verificate con il traffico di rete, in ordine di __priorità__. Quando una regola corrisponde ai criteri di verifica, viene applicata e non vengono verificate altre regole per quella richiesta. Se si dispone di una regola che su vasta scala blocca il traffico in ingresso (ad esempio una regola **Nega tutto**), __deve__ venire dopo le regole che consentono il traffico.
>
> Per altre informazioni sulle regole dei gruppi di sicurezza di rete, vedere il documento [Che cos'è un gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md).

**Esempio: Uso del modello di gestione risorse di Azure**

Usare il modello di gestione risorse seguente disponibile nei [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/) per creare un cluster HDInsight in una rete virtuale con le configurazioni di rete protetta:

[Deploy a secured Azure VNet and an HDInsight Hadoop cluster within the VNet](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/) (Distribuire una rete virtuale protetta di Azure e un cluster HDInsight Hadoop nella rete virtuale)

**Esempio: Azure PowerShell**

```powershell
$vnetName = "Replace with your virtual network name"
$resourceGroupName = "Replace with the resource group the virtual network is in"
$subnetName = "Replace with the name of the subnet that HDInsight will be installed into"
# Get the Virtual Network object
$vnet = Get-AzureRmVirtualNetwork `
    -Name $vnetName `
    -ResourceGroupName $resourceGroupName
# Get the region the Virtual network is in.
$location = $vnet.Location
# Get the subnet object
$subnet = $vnet.Subnets | Where-Object Name -eq $subnetName
# Create a Network Security Group.
# And add exemptions for the HDInsight health and management services.
$nsg = New-AzureRmNetworkSecurityGroup `
    -Name "hdisecure" `
    -ResourceGroupName $resourceGroupName `
    -Location $location `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -name "hdirule1" `
        -Description "HDI health and management address 168.61.49.99" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.49.99" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 300 `
        -Direction Inbound `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -Name "hdirule2" `
        -Description "HDI health and management 23.99.5.239" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "23.99.5.239" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 301 `
        -Direction Inbound `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -Name "hdirule3" `
        -Description "HDI health and management 168.61.48.131" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.48.131" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 302 `
        -Direction Inbound `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -Name "hdirule4" `
        -Description "HDI health and management 138.91.141.162" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "138.91.141.162" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 303 `
        -Direction Inbound
# Set the changes to the security group
Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
# Apply the NSG to the subnet
Set-AzureRmVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name $subnetName `
    -AddressPrefix $subnet.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

**Esempio: Interfaccia della riga di comando di Azure**

1. Usare il comando seguente per creare un nuovo gruppo di sicurezza di rete denominato `hdisecure`. Sostituire **RESOURCEGROUPNAME** con il gruppo di risorse che contiene la rete virtuale di Azure. Sostituire **LOCATION** con la posizione (area geografica) in cui è stato creato il gruppo.

    ```azurecli
    az network nsg create -g RESOURCEGROUPNAME -n hdisecure -l LOCATION
    ```

    Dopo aver creato il gruppo si ricevono informazioni sul nuovo gruppo.

2. Usare le informazioni seguenti per aggiungere regole al nuovo gruppo di sicurezza di rete che ammettono la comunicazione in ingresso sulla porta 443 dal servizio integrità e gestione di Azure HDInsight. Sostituire **RESOURCEGROUPNAME** con il nome del gruppo di risorse che contiene la rete virtuale di Azure.

    ```azurecli
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule3 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"
    ```

3. Dopo aver creato le regole, usare le informazioni seguenti per recuperare l'identificatore univoco per questo gruppo di sicurezza di rete:

    ```azurecli
    az network nsg show -g RESOURCEGROUPNAME -n hdisecure --query 'id'
    ```

    Il comando restituisce un valore simile al testo seguente:

        "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"

    Se non si ottengono i risultati previsti, nel comando inserire l'ID tra virgolette doppie.

4. Usare il comando seguente per applicare il gruppo di sicurezza di rete a una subnet. Sostituire i valori __GUID__ e __RESOURCEGROUPNAME__ con quelli restituiti nel passaggio precedente. Sostituire __VNETNAME__ e __SUBNETNAME__ con il nome di rete virtuale e il nome di subnet da usare per la creazione di un cluster HDInsight.

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUPNAME --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    Dopo avere completato il comando, è possibile installare HDInsight nella rete virtuale protetta sulla subnet usata in questa procedura.

> [!IMPORTANT]
> La procedura sopra descritta consente di accedere solo al servizio integrità e gestione HDInsight nel cloud di Azure. Qualsiasi altro accesso al cluster HDInsight dall'esterno della rete virtuale è bloccato. Per abilitare l'accesso dall'esterno della rete virtuale è necessario aggiungere altre regole del gruppo di sicurezza di rete.
>
> L'esempio seguente dimostra come abilitare l'accesso SSH da Internet:
>
> ```powershell
> Add-AzureRmNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 304 -Direction Inbound
> ```
>
> ```azurecli
> az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"
> ```

Per altre informazioni sui gruppi di sicurezza di rete, vedere [Che cos'è un gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md). Per informazioni sul controllo del routing in una rete virtuale di Azure, vedere [Route definite dall'utente e inoltro IP](../virtual-network/virtual-networks-udr-overview.md).

## <a name="user-defined-routes"></a>Route definite dall'utente

Se si usano le route definite dall'utente per proteggere la rete virtuale, è necessario aggiungere le route per gli indirizzi IP di gestione HDInsight per la propria area. Per un elenco di indirizzi IP per area, vedere la sezione [Indirizzi IP richiesti](#hdinsight-ip).

Le route per gli indirizzi IP richiesti devono impostare il tipo __Hop successivo__ su __Internet__. L'immagine seguente è un esempio di come le route vengono visualizzate nel portale di Azure:

![route definite dall'utente per gli indirizzi IP richiesti da HDInsight](./media/hdinsight-extend-hadoop-virtual-network/user-defined-routes-portal.png)

Per altre informazioni sulle route definite dall'utente, vedere il documento [Route definite dall'utente e inoltro IP](../virtual-network/virtual-networks-udr-overview.md).

## <a name="virtual-appliance-firewall"></a>Firewall dell'appliance virtuale

Se si usa un firewall dell'appliance virtuale per proteggere la rete virtuale, è necessario consentire il traffico in uscita sulle porte seguenti:

* 53
* 443
* 1433
* 11000-11999
* 14000-14999

Per altre informazioni sulle regole del firewall per le appliance virtuali, vedere il documento [Scenario dell'appliance virtuale](../virtual-network/virtual-network-scenario-udr-gw-nva.md).

## <a name="forced-tunneling"></a>Tunneling forzato

Il tunneling forzato non è supportato con HDInsight.

## <a name="retrieve-internal-ips-and-fqdns"></a>Recuperare gli indirizzi IP interni e i nomi di dominio completi

Durante la connessione a HDInsight tramite una rete virtuale, è possibile connettersi direttamente ai nodi del cluster. Per determinare l'indirizzo IP interno e i nomi di dominio completi (FQDN) per i nodi nel cluster, usare gli script seguenti:

**Azure PowerShell**

```powershell
$resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"

$clusterNICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

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

__Interfaccia della riga di comando di Azure__

```azurecli
az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
```

> [!IMPORTANT]
> Nell'interfaccia della riga di comando di Azure 2.0, sostituire `<resourcegroupname>` con il nome del gruppo di risorse che contiene la rete virtuale.

Gli script funzionano eseguendo una query delle schede di interfaccia di rete virtuale (NIC) per il cluster. Le schede NIC sono presenti nel gruppo di risorse che contiene la rete virtuale usata da HDInsight.

## <a id="nextsteps"></a>Passaggi successivi

Gli esempi seguenti mostrano come usare HDInsight con Rete virtuale di Azure.

* [Cluster HBase nella rete virtuale di Azure](hdinsight-hbase-provision-vnet.md)

* [Analizzare i dati dei sensori con Storm e HBase in HDInsight](hdinsight-storm-sensor-data-analysis.md)

* [Effettuare il provisioning di cluster Hadoop in HDInsight](hdinsight-hadoop-provision-linux-clusters.md)

* [Usare Sqoop con Hadoop in HDInsight](hdinsight-use-sqoop-mac-linux.md)

Per altre informazioni sulle reti virtuali di Azure, vedere [Panoramica di Rete virtuale](../virtual-network/virtual-networks-overview.md).


