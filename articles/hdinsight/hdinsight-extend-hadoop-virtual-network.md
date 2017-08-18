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
ms.date: 08/04/2017
ms.author: larryfr
ms.translationtype: HT
ms.sourcegitcommit: 9633e79929329470c2def2b1d06d95994ab66e38
ms.openlocfilehash: 19095d65188ff935b99d1b89cefbc92ef06ebc6f
ms.contentlocale: it-it
ms.lasthandoff: 08/04/2017

---
# <a name="extend-azure-hdinsight-using-an-azure-virtual-network"></a>Estendere Azure HDInsight usando Rete virtuale di Azure

Informazioni su come usare HDInsight con [Rete virtuale di Azure](../virtual-network/virtual-networks-overview.md). Il servizio Rete virtuale di Azure consente di implementare gli scenari seguenti:

* Connessione a HDInsight direttamente da una rete locale.

* Connessione di HDInsight ad archivi dati in una rete virtuale di Azure.

* Accesso diretto ai servizi Hadoop che non sono disponibili pubblicamente su Internet, ad esempio le API Kafka o l'API Java HBase.

> [!WARNING]
> Le informazioni contenute in questo documento richiedono la conoscenza delle reti TCP/IP. Se non si ha familiarità con le reti TCP/IP, è consigliabile collaborare con qualcuno che ne abbia conoscenza se si intende apportare modifiche alle reti di produzione.

## <a name="planning"></a>Pianificazione

Di seguito sono elencate le domande che è necessario porsi quando si intende installare HDInsight in una rete virtuale:

* Occorre installare HDInsight in una rete virtuale esistente? Oppure si intende creare una rete nuova?

    Se si usa una rete virtuale esistente, potrebbe essere necessario modificare la configurazione di rete prima di poter installare HDInsight. Per altre informazioni, vedere la sezione [Aggiungere HDInsight a una rete virtuale esistente](#existingvnet).

* La rete virtuale che contiene HDInsight deve essere connessa a un'altra rete virtuale o alla rete locale?

    Per usare facilmente le risorse tra più reti, potrebbe essere necessario creare un DNS personalizzato e configurare l'inoltro DNS. Per altre informazioni, vedere la sezione [Connessione a più reti](#multinet).

* Si vuole limitare/reindirizzare il traffico in ingresso o in uscita a HDInsight?

    HDInsight deve disporre di una comunicazione senza restrizioni con indirizzi IP specifici nel data center di Azure. Sono presenti anche diverse porte che devono essere abilitate attraverso i firewall per la comunicazione client. Per altre informazioni, vedere la sezione [Controllo del traffico di rete](#networktraffic).

## <a id="existingvnet"></a>Aggiungere HDInsight a una rete virtuale esistente

Seguire la procedura in questa sezione per aggiungere un nuovo cluster HDInsight a una rete virtuale di Azure esistente.

> [!NOTE]
> È possibile aggiungere un cluster HDInsight esistente in una rete virtuale.

1. Si intende usare un modello di distribuzione classico o di Resource Manager per la rete virtuale?

    HDInsight 3.4 e versione successiva richiedono l'utilizzo di una rete virtuale di Resource Manager. Le versioni precedenti di HDInsight richiedono una rete virtuale classica, tuttavia tali versioni sono state ritirate o lo saranno presto.

    Se la rete virtuale esistente è un modello classico, è necessario creare una rete virtuale di Resource Manager e successivamente connettere le due reti. [Connessione di reti virtuali classiche a reti virtuali nuove](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

    Dopo che le due reti sono state unite, HDInsight installato nella rete di Resource Manager può interagire con le risorse della rete classica.

2. Si usa il tunneling forzato? Il tunneling forzato è un'impostazione di subnet che spinge il traffico Internet in uscita verso un dispositivo per l'ispezione e la registrazione. HDInsight non supporta il tunneling forzato. Occorre quindi rimuovere questa funzionalità prima di installare HDInsight in una subnet oppure si può creare una nuova subnet per HDInsight.

3. Si usano gruppi di sicurezza di rete, route definite dall'utente o appliance di rete virtuali per limitare il traffico all'interno o all'esterno della rete virtuale?

    In qualità di servizio gestito, HDInsight richiede l'accesso senza restrizioni a vari indirizzi IP nel data center di Azure. Per consentire la comunicazione con questi indirizzi IP, aggiornare tutti i gruppi di sicurezza di rete o le route definite dall'utente esistenti.

    HDInsight ospita più servizi, che usano porte diverse. Non bloccare il traffico indirizzato a queste porte. Per un elenco di porte da abilitare attraverso i firewall dell'appliance virtuale, vedere la sezione [Sicurezza](#security).

    Per trovare la configurazione di sicurezza esistente, usare i comandi di Azure PowerShell o dell'interfaccia della riga di comando di Azure che seguono:

    * Gruppi di sicurezza di rete

        ```powershell
        $resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"
        get-azurermnetworksecuritygroup -resourcegroupname $resourceGroupName
        ```

        ```azurecli-interactive
        read -p "Enter the name of the resource group that contains the virtual network: " RESOURCEGROUP
        az network nsg list --resource-group $RESOURCEGROUP
        ```

        Per altre informazioni, vedere il documento [Risolvere i problemi relativi ai gruppi di sicurezza di rete](../virtual-network/virtual-network-nsg-troubleshoot-portal.md).

        > [!IMPORTANT]
        > Le regole di gruppo di sicurezza di rete vengono applicate seguendo un ordine basato sulla priorità delle regole. Viene applicata la prima regola che corrisponde al modello di traffico, dopodiché non vengono applicate altre regole per quel traffico. Ordinare le regole dalla più permissiva alla più restrittiva. Per altre informazioni, vedere il documento [Filtrare il traffico di rete con gruppi di sicurezza di rete](../virtual-network/virtual-networks-nsg.md).

    * Route definite dall'utente

        ```powershell
        $resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"
        get-azurermroutetable -resourcegroupname $resourceGroupName
        ```

        ```azurecli-interactive
        read -p "Enter the name of the resource group that contains the virtual network: " RESOURCEGROUP
        az network route-table list --resource-group $RESOURCEGROUP
        ```

        Per altre informazioni, vedere il documento [Risolvere i problemi relativi alle route](../virtual-network/virtual-network-routes-troubleshoot-portal.md).

4. Creare un cluster HDInsight e selezionare la rete virtuale di Azure durante la configurazione. Per comprendere il processo di creazione del cluster, attenersi alla procedura nei documenti seguenti:

    * [Creare cluster HDInsight tramite il portale di Azure](hdinsight-hadoop-create-linux-clusters-portal.md)
    * [Creare cluster HDInsight tramite Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
    * [Creare cluster HDInsight tramite l'interfaccia della riga di comando di Azure 1.0](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
    * [Creare cluster HDInsight tramite un modello Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md)

  > [!IMPORTANT]
  > L'aggiunta di un cluster HDInsight a una rete virtuale è un passaggio di configurazione facoltativo. Assicurarsi di selezionare la rete virtuale quando si configura il cluster.

## <a id="multinet"></a>Connessione a più reti

Il problema più grande con una configurazione con più reti è la risoluzione dei nomi tra le reti.

Azure assicura la risoluzione dei nomi per i servizi di Azure che vengono installati in una rete virtuale. La risoluzione dei nomi integrata consente a HDInsight di connettersi alle risorse seguenti usando un nome di dominio completo (FQDN):

* Qualsiasi risorsa disponibile in Internet, ad esempio microsoft.com, google.com.

* Qualsiasi risorsa che si trovi nella stessa rete virtuale di Azure tramite l'utilizzo del __nome DNS interno__ della risorsa. Quando ad esempio si usa la risoluzione dei nomi predefinita, i seguenti sono nomi DNS interni di esempio assegnati a nodi del ruolo di lavoro di HDInsight:

    * wn0-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net
    * wn2-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net

    Entrambi questi nodi possono comunicare direttamente tra loro e con altri nodi in HDInsight, usando i nomi DNS interni.

La risoluzione dei nomi predefinita __non__ consente a HDInsight di risolvere i nomi di risorse in reti che sono aggiunte alla rete virtuale. È ad esempio pratica diffusa unire la rete locale alla rete virtuale. Con la semplice risoluzione dei nomi predefinita, HDInsight non può accedere alle risorse della rete locale in base al nome. È vero anche il contrario. Le risorse nella rete locale non possono accedere alle risorse della rete virtuale in base al nome.

> [!WARNING]
> È necessario creare il server DNS personalizzato e configurare la rete virtuale per il suo utilizzo prima di creare il cluster HDInsight.

Per abilitare la risoluzione dei nomi tra la rete virtuale e le risorse in reti aggiunte, è necessario eseguire le azioni seguenti:

1. Creare un server DNS personalizzato nella rete virtuale di Azure in cui si intende installare HDInsight.

2. Configurare la rete virtuale per l'utilizzo del server DNS personalizzato.

3. Trovare il suffisso DNS assegnato da Azure per la rete virtuale. Questo valore è simile a `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net`. Per informazioni sulla ricerca del suffisso DNS, vedere la sezione [Esempio: DNS personalizzato](#example-dns).

4. Configurare l'inoltro tra i server DNS. La configurazione dipende dal tipo di rete remota.

    * Se la rete remota è una rete locale, configurare il DNS come indicato di seguito:
        
        * __DNS personalizzato__ (nella rete virtuale):

            * Inoltrare le richieste per il suffisso DNS della rete virtuale al sistema di risoluzione ricorsiva di Azure (168.63.129.16). Azure gestisce le richieste per le risorse nella rete virtuale

            * Inoltrare tutte le altre richieste al server DNS locale. Il server DNS locale gestisce tutte le altre richieste di risoluzione dei nomi, persino le richieste per le risorse Internet quali Microsoft.com.

        * __DNS locale__: inoltrare le richieste per il suffisso DNS di rete virtuale al server DNS personalizzato. Il server DNS personalizzato le inoltra quindi al sistema di risoluzione ricorsiva di Azure.

        Questa configurazione indirizza le richieste di nomi di dominio completi che contengono il suffisso DNS della rete virtuale al server DNS personalizzato. Tutte le altre richieste (anche per gli indirizzi Internet pubblici) vengono gestite dal server DNS locale.

    * Se la rete remota è un'altra rete virtuale di Azure, configurare il DNS come indicato di seguito:

        * __DNS personalizzato__ (in ogni rete virtuale):

            * Le richieste per il suffisso DNS delle reti virtuali vengono inoltrate ai server DNS personalizzati. Il DNS in ogni rete virtuale è responsabile della risoluzione delle risorse all'interno della propria rete.

            * Inoltrare tutte le altre richieste al sistema di risoluzione ricorsiva di Azure. Il sistema di risoluzione ricorsiva è responsabile della risoluzione delle risorse Internet e locali.

        Il server DNS per ogni rete inoltra le richieste all'altro, in base al suffisso DNS. Le altre richieste vengono risolte usando il sistema di risoluzione ricorsiva di Azure.

    Per un esempio di ogni configurazione, vedere la sezione [Esempio: DNS personalizzato](#example-dns).

Per altre informazioni, vedere il documento [Risoluzione dei nomi per le macchine virtuali e le istanze del ruolo](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

## <a name="directly-connect-to-hadoop-services"></a>Connettersi direttamente ai servizi Hadoop

La maggior parte della documentazione in HDInsight presuppone che sia disponibile l'accesso al cluster tramite Internet. Verificare ad esempio che sia possibile connettersi al cluster all'indirizzo https://CLUSTERNAME.azurehdinsight.net. Questo indirizzo usa il gateway pubblico, che non è disponibile se sono stati usati gruppi di sicurezza di rete o route definite dall'utente per limitare l'accesso da Internet.

Per connettersi alle pagine Ambari e ad altre pagine Web tramite la rete virtuale, seguire questa procedura:

1. Per individuare i nomi di dominio completi (FQDN) interni dei nodi del cluster HDInsight, usare uno dei modi seguenti:

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

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

    ```azurecli
    az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

    Nell'elenco di nodi restituiti, individuare i nomi FQDN per i nodi head e usarli per connettersi al servizio Ambari e ad altri servizi Web. Usare ad esempio `http://<headnode-fqdn>:8080` per accedere ad Ambari.

    > [!IMPORTANT]
    > Alcuni servizi ospitati nei nodi head sono attivi solo in un nodo alla volta. Se nel tentativo di accedere a un servizio in un nodo head si riceve un messaggio di errore 404, passare al nodo head successivo.

2. Per determinare il nodo e la porta su cui un servizio è disponibile, vedere il documento [Porte usate dai servizi Hadoop su HDInsight](./hdinsight-hadoop-port-settings-for-services.md).

## <a id="networktraffic"></a> Controllo del traffico di rete

Il traffico di rete nelle reti virtuali di Azure può essere controllato usando i modi seguenti:

* i **gruppi di sicurezza di rete** (NSG) consentono di filtrare il traffico di rete in ingresso e in uscita. Per altre informazioni, vedere il documento [Filtrare il traffico di rete con gruppi di sicurezza di rete](../virtual-network/virtual-networks-nsg.md).

* Le **route definite dall'utente** definiscono il flusso del traffico tra le risorse nella rete. Per altre informazioni, vedere il documento [Route definite dall'utente e inoltro IP](../virtual-network/virtual-networks-udr-overview.md).

* Le **appliance virtuali di rete** replicano le funzionalità di dispositivi come i firewall e i router. Per altre informazioni, vedere il documento relativo alle [Appliance di rete](https://azure.microsoft.com/solutions/network-appliances).

In qualità di servizio gestito, HDInsight richiede l'accesso senza restrizioni a servizi di gestione e integrità di Azure nel cloud di Azure. Quando si usano i gruppi di sicurezza di rete e le route definite dall'utente, è necessario assicurarsi che questi servizi possano ancora comunicare con HDInsight.

HDInsight espone i servizi su porte diverse. Quando si usa un firewall di appliance virtuale, è necessario consentire il traffico sulle porte usate per questi servizi. Per altre informazioni, vedere la sezione [Porte richieste].

### <a id="hdinsight-ip"></a>HDInsight con gruppi di sicurezza di rete e route definite dall'utente

Se si intende usare **gruppi di sicurezza di rete** o **route definite dall'utente** per controllare il traffico di rete, eseguire le azioni seguenti prima di installare HDInsight:

1. Identificare l'area di Azure che si intende usare per HDInsight.

2. Identificare gli indirizzi IP richiesti da HDInsight. Gli indirizzi IP che dovrebbero essere concessi sono specifici all'area in cui risiedono il cluster HDInsight e la rete virtuale. Per un elenco di indirizzi IP per area, vedere la sezione [Indirizzi IP richiesti da HDInsight](#hdinsight-ip).

3. Creare o modificare i gruppi di sicurezza di rete o le route definite dall'utente per la subnet in cui si intende installare HDInsight.

    * __Gruppi di sicurezza di rete__: consentono il traffico __in ingresso__ sulla porta __443__ dagli indirizzi IP.
    * __Route definite dall'utente__: creare una route per ogni indirizzo IP e impostare __Tipo hop successivo__ su __Internet__.

Per altre informazioni sui gruppi di sicurezza di rete o sulle route definite dall'utente, vedere la documentazione seguente:

* [Gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md)

* [Route definite dall'utente](../virtual-network/virtual-networks-udr-overview.md)

#### <a name="forced-tunneling"></a>Tunneling forzato

Il tunneling forzato è una configurazione di routing definita dall'utente in cui tutto il traffico da una subnet viene spinto verso una rete o un percorso specifico, ad esempio la rete locale. HDInsight __non__ supporta il tunneling forzato.

## <a id="hdinsight-ip"></a> Indirizzi IP richiesti

L'elenco seguente contiene l'indirizzo IP dei servizi di monitoraggio e integrità di Azure che monitorano i cluster HDInsight. Questo elenco è importante solo se si usano i gruppi di sicurezza di rete o le route definite dall'utente. Per altre informazioni, vedere la sezione [Controllo del traffico di rete](#networktraffic).

Usare i dati indicati nella tabella seguente per trovare gli indirizzi IP per l'area in uso:

| Paese | Region | Indirizzi IP consentiti | Porta consentita | Direzione |
| ---- | ---- | ---- | ---- | ----- |
| Asia | Asia orientale | 23.102.235.122</br>52.175.38.134 | 443 | In ingresso |
| Australia | Australia orientale | 104.210.84.115</br>13.75.152.195 | 443 | In ingresso |
| Brasile | Brasile meridionale | 191.235.84.104</br>191.235.87.113 | 443 | In ingresso |
| Canada | Canada orientale | 52.229.127.96</br>52.229.123.172 | 443 | In ingresso |
| &nbsp; | Canada centrale | 52.228.37.66</br>52.228.45.222 | 443 | In ingresso |
| Cina | Cina settentrionale | 42.159.96.170</br>139.217.2.219 | 443 | In ingresso |
| &nbsp; | Cina orientale | 42.159.198.178</br>42.159.234.157 | 443 | In ingresso |
| Europa | Europa settentrionale | 52.164.210.96</br>13.74.153.132 | 443 | In ingresso |
| Germania | Germania centrale | 51.4.146.68</br>51.4.146.80 | 443 | In ingresso |
| &nbsp; | Germania nord-orientale | 51.5.150.132</br>51.5.144.101 | 443 | In ingresso |
| India | India centrale | 52.172.153.209</br>52.172.152.49 | 443 | In ingresso |
| Giappone | Giappone orientale | 13.78.125.90</br>13.78.89.60 | 443 | In ingresso |
| &nbsp; | Giappone occidentale | 40.74.125.69</br>138.91.29.150 | 443 | In ingresso |
| Corea | Corea centrale | 52.231.39.142</br>52.231.36.209 | 433 | In ingresso |
| &nbsp; | Corea meridionale | 52.231.203.16</br>52.231.205.214 | 443 | In ingresso
| Regno Unito | Regno Unito occidentale | 51.141.13.110</br>51.141.7.20 | 443 | In ingresso |
| &nbsp; | Regno Unito meridionale | 51.140.47.39</br>51.140.52.16 | 443 | In ingresso |
| Stati Uniti | Stati Uniti centrali | 13.67.223.215</br>40.86.83.253 | 443 | In ingresso |
| &nbsp; | Stati Uniti centro-occidentali | 52.161.23.15</br>52.161.10.167 | 443 | In ingresso |
| &nbsp; | Stati Uniti occidentali 2 | 52.175.211.210</br>52.175.222.222 | 443 | In ingresso |

Per informazioni sugli indirizzi IP da usare per Azure per enti pubblici, vedere il documento [Azure Government Intelligence + Analytics](https://docs.microsoft.com/azure/azure-government/documentation-government-services-intelligenceandanalytics) (Intelligence e Analisi di Azure per enti pubblici).

__Se l'area non è elencata nella tabella__, consentire il traffico alla porta __443__ negli indirizzi IP seguenti:

* 168.61.49.99
* 23.99.5.239
* 168.61.48.131
* 138.91.141.162

> [!IMPORTANT]
> HDInsight non supporta la limitazione del traffico in uscita, solo del traffico in ingresso.

> [!NOTE]
> Se si usa un server DNS personalizzato con la rete virtuale, è anche necessario consentire l'accesso da __168.63.129.16__. Questo è l'indirizzo del sistema di risoluzione ricorsiva di Azure. Per altre informazioni, vedere il documento [Risoluzione dei nomi per macchine virtuali e istanze del ruolo](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a id="hdinsight-ports"></a> Porte richieste

Se si intende usare un **firewall di appliance virtuale** di rete per proteggere la rete virtuale, è necessario abilitare il traffico in uscita sulle porte seguenti:

* 53
* 443
* 1433
* 11000-11999
* 14000-14999

Per un elenco di porte per servizi specifici, vedere il documento [Porte usate dai servizi Hadoop su HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Per altre informazioni sulle regole del firewall per le appliance virtuali, vedere il documento [Scenario dell'appliance virtuale](../virtual-network/virtual-network-scenario-udr-gw-nva.md).

## <a id="hdinsight-nsg"></a>Ad esempio: gruppi di sicurezza di rete con HDInsight

Gli esempi in questa sezione illustrano come creare regole per i gruppi di sicurezza di rete che consentano a HDInsight di comunicare con i servizi di gestione di Azure. Prima di usare gli esempi, modificare gli indirizzi IP in modo che corrispondano a quelli per l'area di Azure in uso. Queste informazioni sono disponibili nella sezione [HDInsight con gruppi di sicurezza di rete e route definite dall'utente](#hdinsight-ip).

### <a name="azure-resource-management-template"></a>Modello di Resource Manager di Azure

Il modello di Resource Manager seguente crea una rete virtuale che limita il traffico in ingresso, ma consente il traffico dagli indirizzi IP richiesti da HDInsight. Questo modello crea anche un cluster HDInsight nella rete virtuale.

* [Deploy a secured Azure Virtual Network and an HDInsight Hadoop cluster](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/) (Distribuire una rete virtuale di Azure protetta e un cluster Hadoop HDInsight)

> [!IMPORTANT]
> Modificare gli indirizzi IP usati in questo esempio in modo che corrispondano all'area di Azure in uso. Queste informazioni sono disponibili nella sezione [HDInsight con gruppi di sicurezza di rete e route definite dall'utente](#hdinsight-ip).

### <a name="azure-powershell"></a>Azure PowerShell

Usare lo script di PowerShell seguente per creare una rete virtuale che limita il traffico in ingresso, ma consente il traffico dagli indirizzi IP richiesti da HDInsight nell'area Europa settentrionale.

> [!IMPORTANT]
> Modificare gli indirizzi IP usati in questo esempio in modo che corrispondano all'area di Azure in uso. Queste informazioni sono disponibili nella sezione [HDInsight con gruppi di sicurezza di rete e route definite dall'utente](#hdinsight-ip).

```powershell
$vnetName = "Replace with your virtual network name"
$resourceGroupName = "Replace with the resource group the virtual network is in"
$subnetName = "Replace with the name of the subnet that you plan to use for HDInsight"
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
        -Description "HDI health and management address 52.164.210.96" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "52.164.210.96" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 300 `
        -Direction Inbound `
    | Add-AzureRmNetworkSecurityRuleConfig `
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
    | Add-AzureRmNetworkSecurityRuleConfig `
        -Name "blockeverything" `
        -Description "Block everything else" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "*" `
        -SourceAddressPrefix "Internet" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Deny `
        -Priority 500 `
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

> [!IMPORTANT]
> Questo esempio illustra come aggiungere le regole per consentire il traffico in ingresso sugli indirizzi IP richiesti. Non contiene regole per limitare l'accesso in ingresso da altre origini.
>
> L'esempio seguente dimostra come abilitare l'accesso SSH da Internet:
>
> ```powershell
> Add-AzureRmNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 304 -Direction Inbound
> ```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Seguire questa procedura per creare una rete virtuale che limita il traffico in ingresso, ma consente il traffico dagli indirizzi IP richiesti da HDInsight.

1. Usare il comando seguente per creare un nuovo gruppo di sicurezza di rete denominato `hdisecure`. Sostituire **RESOURCEGROUPNAME** con il gruppo di risorse che contiene la rete virtuale di Azure. Sostituire **LOCATION** con la posizione (area geografica) in cui è stato creato il gruppo.

    ```azurecli
    az network nsg create -g RESOURCEGROUPNAME -n hdisecure -l LOCATION
    ```

    Dopo aver creato il gruppo si ricevono informazioni sul nuovo gruppo.

2. Usare le informazioni seguenti per aggiungere regole al nuovo gruppo di sicurezza di rete che ammettono la comunicazione in ingresso sulla porta 443 dal servizio integrità e gestione di Azure HDInsight. Sostituire **RESOURCEGROUPNAME** con il nome del gruppo di risorse che contiene la rete virtuale di Azure.

    > [!IMPORTANT]
    > Modificare gli indirizzi IP usati in questo esempio in modo che corrispondano all'area di Azure in uso. Queste informazioni sono disponibili nella sezione [HDInsight con gruppi di sicurezza di rete e route definite dall'utente](#hdinsight-ip).

    ```azurecli
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "52.164.210.96" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "13.74.153.132" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n block --protocol "*" --source-port-range "*" --destination-port-range "*" --source-address-prefix "Internet" --destination-address-prefix "VirtualNetwork" --access "Deny" --priority 500 --direction "Inbound"
    ```

3. Per recuperare l'identificatore univoco per questo gruppo di sicurezza di rete, usare il comando seguente:

    ```azurecli
    az network nsg show -g RESOURCEGROUPNAME -n hdisecure --query 'id'
    ```

    Il comando restituisce un valore simile al testo seguente:

        "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"

    Se non si ottengono i risultati previsti, nel comando inserire l'ID tra virgolette doppie.

4. Usare il comando seguente per applicare il gruppo di sicurezza di rete a una subnet. Sostituire i valori __GUID__ e __RESOURCEGROUPNAME__ con quelli restituiti nel passaggio precedente. Sostituire __VNETNAME__ e __SUBNETNAME__ con il nome della rete virtuale e il nome della subnet che si intende creare.

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUPNAME --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    Dopo il completamento del comando, è possibile installare HDInsight nella rete virtuale.

> [!IMPORTANT]
> Questa procedura consente di accedere solo al servizio di gestione e integrità di HDInsight nel cloud di Azure. Qualsiasi altro accesso al cluster HDInsight dall'esterno della rete virtuale è bloccato. Per abilitare l'accesso dall'esterno della rete virtuale è necessario aggiungere altre regole del gruppo di sicurezza di rete.
>
> L'esempio seguente dimostra come abilitare l'accesso SSH da Internet:
>
> ```azurecli
> az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"
> ```

## <a id="example-dns"></a> Esempio: configurazione DNS

### <a name="name-resolution-between-a-virtual-network-and-a-connected-on-premises-network"></a>Risoluzione dei nomi tra una rete virtuale e una rete locale connessa

Questo esempio si basa sui presupposti seguenti:

* Si dispone di una rete virtuale di Azure che è connessa a una rete locale tramite un gateway VPN.

* Il server DNS personalizzato nella rete virtuale esegue il sistema operativo Linux o Unix.

* Nel server DNS personalizzato è installato [Bind](https://www.isc.org/downloads/bind/).

Nel server DNS personalizzato nella rete virtuale:

1. Usare Azure PowerShell o l'interfaccia della riga di comando di Azure per individuare il suffisso DNS della rete virtuale:

    ```powershell
    $resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"
    $NICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli-interactive
    read -p "Enter the name of the resource group that contains the virtual network: " RESOURCEGROUP
    az network nic list --resource-group $RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
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

3. Per usare la configurazione, riavviare Bind. Ad esempio, `sudo service bind9 restart`.

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

    ```powershell
    $resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"
    $NICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli-interactive
    read -p "Enter the name of the resource group that contains the virtual network: " RESOURCEGROUP
    az network nic list --resource-group $RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
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
    
    * Sostituire i valori `10.0.0.0/16` e `10.1.0.0/16` all'interno degli intervalli di indirizzi IP delle reti virtuali. Questa voce consente alle risorse in ogni rete di eseguire richieste dei server DNS.

    Tutte le richieste che non riguardano i suffissi DNS delle reti virtuali (ad esempio microsoft.com) vengono gestite dal sistema di risoluzione ricorsiva di Azure.

4. Per usare la configurazione, riavviare Bind. Ad esempio `sudo service bind9 restart` su entrambi i server DNS.

Dopo avere completato questi passaggi, è possibile connettersi alle risorse nella rete virtuale usando i nomi di dominio completi (FQDN). È ora possibile installare HDInsight nella rete virtuale.

## <a name="next-steps"></a>Passaggi successivi

* Per un esempio completo di configurazione di HDInsight per la connessione a una rete locale, vedere [Connettere HDInsight alla rete locale](./connect-on-premises-network.md).

* Per altre informazioni sulle reti virtuali di Azure, vedere la [panoramica sulle reti virtuali di Azure](../virtual-network/virtual-networks-overview.md).

* Per altre informazioni sui gruppi di sicurezza di rete, vedere [Gruppi di sicurezza di rete](../virtual-network/virtual-networks-nsg.md).

* Per altre informazioni sulle route definite dall'utente, vedere [Route definite dall'utente e inoltro IP](../virtual-network/virtual-networks-udr-overview.md).
