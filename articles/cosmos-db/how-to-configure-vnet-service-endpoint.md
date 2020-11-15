---
title: Configurare l'accesso in base alla rete virtuale per un account Azure Cosmos
description: Questo documento descrive i passaggi necessari per configurare un endpoint servizio di rete virtuale per Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1d63d21f4c49e3c7aef035208477ac9fc79f2e51
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2020
ms.locfileid: "94637184"
---
# <a name="configure-access-to-azure-cosmos-db-from-virtual-networks-vnet"></a>Configurare l'accesso alle Azure Cosmos DB da reti virtuali (VNet)
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

È possibile configurare l'account Azure Cosmos DB per consentire l'accesso solo da una subnet specifica di rete virtuale di Azure (VNet). Abilitando [Endpoint del servizio](../virtual-network/virtual-network-service-endpoints-overview.md) per accedere ad Azure Cosmos DB nella subnet all'interno di una rete virtuale, il traffico da tale subnet viene inviato ad Azure Cosmos DB con l'identità della subnet e della rete virtuale. Dopo aver abilitato l'endpoint del servizio Azure Cosmos DB, è possibile limitare l'accesso alla subnet aggiungendola all'account Azure Cosmos.

Per impostazione predefinita, un account di Azure Cosmos è accessibile da qualsiasi provider di origine purché la richiesta sia accompagnata da un token di autorizzazione valido. Quando si aggiunge una o più subnet all'interno delle reti virtuali, solo le richieste provenienti da tali subnet otterranno una risposta valida. Le richieste provenienti da qualsiasi altra origine riceveranno una risposta 403 (Non consentito). 

È possibile configurare gli account Azure Cosmos DB per consentire l'accesso da una sola subnet specifica di una rete virtuale di Azure. Per limitare l'accesso a un account Azure Cosmos DB con le connessioni da una subnet in una rete virtuale:

1. Abilitare la subnet per inviare l'identità della subnet e della rete virtuale ad Azure Cosmos DB. Per eseguire questa operazione, è necessario abilitare un endpoint di servizio per Azure Cosmos DB nella subnet specifica.

1. Aggiungere una regola nell'account Azure Cosmos DB per specificare la subnet come origine da cui è possibile accedere all'account.

> [!NOTE]
> Dopo aver abilitato l'endpoint di servizio per l'account Azure Cosmos DB in una subnet, l'origine del traffico che raggiunge Azure Cosmos DB passa da un indirizzo IP pubblico a una rete virtuale e una subnet. La commutazione del traffico si applica a tutti gli account di Azure Cosmos DB a cui si accede da questa subnet. Se gli account di Azure Cosmos usano un firewall basato su protocollo IP per consentire l'accesso a questa subnet, le richieste provenienti dalla subnet abilitata al servizio non corrispondono più alle regole del firewall IP e vengono rifiutate.
>
> Per altre informazioni, vedere la procedura descritta nella sezione [Migrazione da una regola del firewall IP a un elenco di controllo di accesso della rete virtuale](#migrate-from-firewall-to-vnet) di questo articolo.

Le sezioni seguenti descrivono come configurare un endpoint servizio di rete virtuale per un account Azure Cosmos DB.

## <a name="configure-a-service-endpoint-by-using-the-azure-portal"></a><a id="configure-using-portal"></a>Configurare un endpoint di servizio tramite il portale di Azure

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Configurare un endpoint di servizio per una rete virtuale e una subnet Azure esistenti

1. Nel pannello **Tutte le risorse** individuare l'account Azure Cosmos DB da proteggere.

1. Selezionare **Firewall e reti virtuali** dal menu delle impostazioni e scegliere di consentire l'accesso da **Reti selezionate**.

1. Per concedere l'accesso a una subnet della rete virtuale esistente, in **Reti virtuali** selezionare **Add existing Azure virtual network** (Aggiungi rete virtuale di Azure esistente).

1. Selezionare la **sottoscrizione** da cui si vuole aggiungere una rete virtuale di Azure. Selezionare le **reti virtuali** e le **subnet** di Azure a cui si vuole consentire l'accesso al proprio account Azure Cosmos DB. Selezionare quindi **Abilita** per abilitare le reti selezionate con gli endpoint di servizio per "Microsoft.AzureCosmosDB". Al termine, selezionare **Aggiungi**.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png" alt-text="Selezionare la rete virtuale e la subnet":::

1. Dopo aver abilitato l'account Azure Cosmos DB per l'accesso da una rete virtuale, il traffico sarà consentito solo dalla subnet scelta. La rete virtuale e la subnet aggiunte verranno visualizzate come illustrato nello screenshot seguente:

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png" alt-text="Rete virtuale e subnet configurate correttamente":::

> [!NOTE]
> Per abilitare gli endpoint servizio di rete virtuale sono necessarie le autorizzazioni seguenti per la sottoscrizione:
>   * Sottoscrizione con la rete virtuale: Collaboratore Rete
>   * Sottoscrizione con account Azure Cosmos DB: Collaboratore Account DocumentDB
>   * Se la rete virtuale e l'account Azure Cosmos DB si trovano in sottoscrizioni diverse, verificare che anche la sottoscrizione con rete virtuale disponga di un `Microsoft.DocumentDB` provider di risorse registrato. Per registrare un provider di risorse, vedere l'articolo sui [tipi e i provider di risorse di Azure](../azure-resource-manager/management/resource-providers-and-types.md) .

Di seguito sono riportate le istruzioni per la registrazione della sottoscrizione con il provider di risorse.

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Configurare un endpoint di servizio per una rete virtuale e una subnet Azure nuove

1. Nel pannello **Tutte le risorse** individuare l'account Azure Cosmos DB da proteggere.  

1. Selezionare **Firewall e reti virtuali** dal menu delle impostazioni e scegliere di consentire l'accesso da **Reti selezionate**.  

1. Per concedere l'accesso a una nuova rete virtuale di Azure, in **Reti virtuali** selezionare **Add new virtual network** (Aggiungi nuova rete virtuale).  

1. Fornire i dettagli necessari per creare una nuova rete virtuale e quindi selezionare **Crea**. La subnet verrà creata con un endpoint di servizio per "Microsoft.AzureCosmosDB" abilitato.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png" alt-text="Selezionare una rete virtuale e una subnet da una nuova rete virtuale":::

Se l'account Azure Cosmos DB viene usato da altri servizi di Azure come Azure ricerca cognitiva oppure è accessibile da analisi di flusso o Power BI, si consente l'accesso selezionando **accetta connessioni dai data center globali di Azure**.

Per assicurarsi di avere accesso alle metriche di Azure Cosmos DB dal portale, è necessario abilitare le opzioni **Consentire l'accesso dal portale di Azure**. Per altre informazioni su queste opzioni, vedere l'articolo [Configurare un firewall IP](how-to-configure-firewall.md). Dopo avere abilitato l'accesso, selezionare **Salva** per salvare le impostazioni.

## <a name="remove-a-virtual-network-or-subnet"></a><a id="remove-vnet-or-subnet"></a>Rimuovere una rete virtuale o una subnet

1. Dal pannello **Tutte le risorse** individuare l'account Azure Cosmos DB per cui sono stati assegnati gli endpoint di servizio.  

1. Selezionare **Firewall e reti virtuali** dal menu delle impostazioni.  

1. Per rimuovere una regola della rete virtuale o della subnet, selezionare **...** accanto alla rete virtuale o alla subnet, quindi selezionare **Rimuovi**.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png" alt-text="Rimuovere una rete virtuale":::

1. Selezionare **Salva** per applicare le modifiche.

## <a name="configure-a-service-endpoint-by-using-azure-powershell"></a><a id="configure-using-powershell"></a>Configurare un endpoint di servizio tramite Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Usare questa procedura per configurare un endpoint di servizio per un account Azure Cosmos DB usando Azure PowerShell:  

1. Installare [Azure PowerShell](/powershell/azure/install-Az-ps) e [accedere](/powershell/azure/authenticate-azureps).  

1. Abilitare l'endpoint di servizio per una subnet esistente di una rete virtuale.  

   ```powershell
   $resourceGroupName = "<Resource group name>"
   $vnetName = "<Virtual network name>"
   $subnetName = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"
   $serviceEndpoint = "Microsoft.AzureCosmosDB"

   Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName | Set-AzVirtualNetworkSubnetConfig `
      -Name $subnetName `
      -AddressPrefix $subnetPrefix `
      -ServiceEndpoint $serviceEndpoint | Set-AzVirtualNetwork
   ```

   > [!NOTE]
   > Quando si usa PowerShell o l'interfaccia della riga di comando di Azure, assicurarsi di specificare l'elenco completo dei filtri IP e degli ACL della rete virtuale nei parametri e non solo quelli che devono essere aggiunti.

1. Ottenere informazioni sulla rete virtuale.

   ```powershell
   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. Preparare una regola della rete virtuale Cosmos DB

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Aggiornare Azure Cosmos DB proprietà dell'account con la nuova configurazione dell'endpoint della rete virtuale: 

   ```powershell
   $accountName = "<Cosmos DB account name>"

   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. Eseguire il comando seguente per verificare che l'account Azure Cosmos DB venga aggiornato con l'endpoint servizio di rete virtuale configurato nel passaggio precedente:

   ```powershell
   $account = Get-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName

   $account.IsVirtualNetworkFilterEnabled
   $account.VirtualNetworkRules
   ```

## <a name="configure-a-service-endpoint-by-using-the-azure-cli"></a><a id="configure-using-cli"></a>Configurare un endpoint di servizio tramite l'interfaccia della riga di comando di Azure

Gli account Azure Cosmos possono essere configurati per gli endpoint di servizio quando vengono creati o aggiornati in un secondo momento se la subnet è già stata configurata. Gli endpoint di servizio possono anche essere abilitati nell'account Cosmos in cui la subnet non è ancora configurata e quindi inizierà a funzionare quando la subnet viene configurata in un secondo momento. Questa flessibilità consente agli amministratori che non hanno accesso all'account Cosmos e alle risorse della rete virtuale di rendere le proprie configurazioni indipendenti tra loro.

### <a name="create-a-new-cosmos-account-and-connect-it-to-a-back-end-subnet-for-a-new-virtual-network"></a>Creare un nuovo account Cosmos e connetterlo a una subnet back-end per una nuova rete virtuale

In questo esempio la rete virtuale e la subnet vengono create con gli endpoint di servizio abilitati per entrambi quando vengono creati.

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet with service endpoints enabled for Cosmos DB
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values and service endpoints
az cosmosdb create \
   -n $accountName \
   -g $resourceGroupName \
   --enable-virtual-network true \
   --virtual-network-rules $svcEndpoint
```

### <a name="connect-and-configure-a-cosmos-account-to-a-back-end-subnet-independently"></a>Connettere e configurare un account Cosmos in una subnet back-end in modo indipendente

Questo esempio illustra come connettere un account Azure Cosmos a una nuova rete virtuale esistente in cui la subnet non è ancora configurata per gli endpoint di servizio. Questa operazione viene eseguita tramite il `--ignore-missing-vnet-service-endpoint` parametro. Ciò consente di completare la configurazione dell'account Cosmos senza errori prima che la configurazione alla subnet della rete virtuale sia stata completata. Una volta completata la configurazione della subnet, l'account Cosmos diventerà accessibile tramite la subnet configurata.

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet
# that is not yet enabled for service endpoints.

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet but without configuring service endpoints (--service-endpoints Microsoft.AzureCosmosDB)
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values
az cosmosdb create -n $accountName -g $resourceGroupName

# Add the virtual network rule but ignore the missing service endpoint on the subnet
az cosmosdb network-rule add \
   -n $accountName \
   -g $resourceGroupName \
   --virtual-network $vnetName \
   --subnet svcEndpoint \
   --ignore-missing-vnet-service-endpoint true

read -p'Press any key to now configure the subnet for service endpoints'

az network vnet subnet update \
   -n $backEnd \
   -g $resourceGroupName \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB
```

## <a name="port-range-when-using-direct-mode"></a>Intervallo di porte quando si usa la modalità diretta

Quando si usano gli endpoint di servizio con un account Azure Cosmos tramite una connessione in modalità diretta, è necessario assicurarsi che l'intervallo di porte TCP da 10000 a 20000 sia aperto.

## <a name="migrating-from-an-ip-firewall-rule-to-a-virtual-network-acl"></a><a id="migrate-from-firewall-to-vnet"></a>Migrazione da una regola del firewall IP a un ACL di rete virtuale

Per eseguire la migrazione di un account Azure Cosmos DB usando le regole del firewall IP per usare gli endpoint del servizio rete virtuale, seguire questa procedura.

Dopo aver configurato un account di Azure Cosmos DB per un endpoint di servizio per una subnet, le richieste provenienti da tale subnet vengono inviate a Azure Cosmos DB con le informazioni sull'origine della rete virtuale e della subnet invece di un indirizzo IP pubblico di origine. Queste richieste non corrisponderanno più a un filtro IP configurato per l'account Azure Cosmos DB, motivo per cui è necessario eseguire i passaggi seguenti per evitare tempi di inattività.

Prima di procedere, abilitare l'endpoint del servizio Azure Cosmos DB nella rete virtuale e nella subnet usando il passaggio illustrato in "abilitare l'endpoint del servizio per una subnet esistente di una rete virtuale".

1. Ottenere informazioni su rete virtuale e subnet:

   ```powershell
   $resourceGroupName = "myResourceGroup"
   $accountName = "mycosmosaccount"
   $vnetName = "myVnet"
   $subnetName = "mySubnet"

   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. Preparare un nuovo oggetto regola della rete virtuale per l'account Azure Cosmos DB:

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Aggiornare l'account Azure Cosmos DB per abilitare l'accesso all'endpoint di servizio dalla subnet:

   ```powershell
   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. Ripetere i passaggi precedenti per tutti gli account di Azure Cosmos DB a cui si accede dalla subnet.

1. Rimuovere la regola del firewall IP per la subnet dalle regole del firewall dell'account Azure Cosmos DB.

## <a name="frequently-asked-questions"></a>Domande frequenti

Ecco alcune domande frequenti sulla configurazione dell'accesso da reti virtuali:

### <a name="are-notebooks-and-mongocassandra-shell-currently-compatible-with-virtual-network-enabled-accounts"></a>I notebook e la shell Mongo/Cassandra sono attualmente compatibili con gli account abilitati per la rete virtuale?

Al momento, le integrazioni di [Mongo Shell](https://devblogs.microsoft.com/cosmosdb/preview-native-mongo-shell/) e [Cassandra shell](https://devblogs.microsoft.com/cosmosdb/announcing-native-cassandra-shell-preview/) nel Cosmos DB Esplora dati e il [servizio Jupyter Notebooks](./cosmosdb-jupyter-notebooks.md)non sono supportate con l'accesso vnet. Questa implementazione è attualmente in fase di sviluppo attivo.

### <a name="can-i-specify-both-virtual-network-service-endpoint-and-ip-access-control-policy-on-an-azure-cosmos-account"></a>È possibile specificare l'endpoint servizio di rete virtuale e i criteri di controllo di accesso IP per un account Azure Cosmos? 

È possibile abilitare sia l'endpoint del servizio di rete virtuale sia un criterio di controllo di accesso IP (noto anche come firewall) nell'account Azure Cosmos. Queste due funzionalità sono complementari e garantiscono collettivamente isolamento e sicurezza dell'account di Azure Cosmos. L'utilizzo del firewall IP garantisce che gli indirizzi IP statici possano accedere all'account. 

### <a name="how-do-i-limit-access-to-subnet-within-a-virtual-network"></a>Come è possibile limitare l'accesso alla subnet all'interno di una rete virtuale? 

Esistono due passaggi necessari per limitare l'accesso all'account Azure Cosmos da una subnet. In primo luogo, si consente al traffico dalla subnet di portare le rispettive subnet e l'identità di rete virtuale ad Azure Cosmos DB. Questa operazione viene eseguita abilitando l'endpoint del servizio per Azure Cosmos DB nella subnet. Successivamente viene aggiunta una regola nell'account di Azure Cosmos che specifica questa subnet come un'origine da cui è possibile accedere all'account.

### <a name="will-virtual-network-acls-and-ip-firewall-reject-requests-or-connections"></a>Gli elenchi di accesso virtuale della rete virtuale e il Firewall IP rifiutano le richieste o le connessioni? 

Quando vengono aggiunte regole di accesso al firewall IP o alla rete virtuale, solo le richieste provenienti da origini autorizzate ricevono risposte valide. Le altre richieste vengono rifiutate con un errore 403 (Non consentito). È importante distinguere il firewall dell'account Azure Cosmos da un firewall a livello di connessione. L'origine può comunque connettersi al servizio e le connessioni stesse non vengono rifiutate.

### <a name="my-requests-started-getting-blocked-when-i-enabled-service-endpoint-to-azure-cosmos-db-on-the-subnet-what-happened"></a>Le richieste hanno iniziato a bloccarsi dopo aver abilitato l'endpoint del servizio ad Azure Cosmos DB nella subnet. Che cosa è successo?

Dopo aver abilitato l'endpoint di servizio per Azure Cosmos DB in una subnet, l'origine del traffico che raggiunge l'account passa dall'indirizzo IP pubblico alla rete virtuale e alla subnet. Se l'account Azure Cosmos dispone solo di un firewall basato su protocollo IP, il traffico dalla subnet del servizio abilitato non corrisponderebbe alle regole del firewall IP e pertanto sarebbe rifiutato. Esaminare i passaggi per eseguire facilmente la migrazione dal firewall basato su protocollo IP per il controllo di accesso basato sulla rete virtuale.

### <a name="are-additional-azure-rbac-permissions-needed-for-azure-cosmos-accounts-with-vnet-service-endpoints"></a>Sono necessarie altre autorizzazioni RBAC di Azure per gli account Azure Cosmos con gli endpoint del servizio VNET?

Dopo aver aggiunto gli endpoint del servizio di rete virtuale a un account Azure Cosmos, per apportare qualsiasi modifica alle impostazioni dell'account è necessario accedere all'azione `Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action` per tutte le reti virtuali configurate nell'account Azure Cosmos. Questa operazione è necessaria perché il processo di autorizzazione convalida l'accesso alle risorse (incluse le risorse di database e di rete virtuale) prima di valutare le proprietà.
 
Viene convalidata l'autorizzazione per l'azione della risorsa di rete virtuale anche se l'utente non specifica gli elenchi di controllo di accesso di rete virtuale usando l'interfaccia della riga di comando di Azure. Attualmente, il piano di controllo di un account Azure Cosmos supporta l'impostazione dello stato completo dell'account Azure Cosmos. Uno dei parametri per le chiamate del piano di controllo è `virtualNetworkRules`. Se questo parametro non viene specificato, l'interfaccia della riga di comando di Azure esegue una chiamata Get Database per recuperare il parametro `virtualNetworkRules` e usa questo valore nella chiamata di aggiornamento.

### <a name="do-the-peered-virtual-networks-also-have-access-to-azure-cosmos-account"></a>Anche le reti virtuali con peering hanno accesso all'account Azure Cosmos? 
Solo la rete virtuale e le subnet aggiunte all'account Azure Cosmos possono avere accesso. Le reti virtuali con peering non possono accedere all'account fino a quando le subnet all'interno delle reti virtuali con peering non vengono aggiunte all'account.

### <a name="what-is-the-maximum-number-of-subnets-allowed-to-access-a-single-cosmos-account"></a>Qual è il numero massimo di subnet consentite per accedere a un singolo account Cosmos? 
Attualmente, è consentito avere al massimo 256 subnet per un account Azure Cosmos.

### <a name="can-i-enable-access-from-vpn-and-express-route"></a>È possibile abilitare l'accesso da VPN ed Express Route? 
Per l'accesso all'account Azure Cosmos tramite ExpressRoute da locale, è necessario abilitare il peering Microsoft. Dopo avere inserito il firewall IP o le regole di accesso di rete virtuale, è possibile aggiungere gli indirizzi IP pubblici usati per il peering Microsoft sul firewall IP dell'account Azure Cosmos per consentire l'accesso dei servizi locali all'account Azure Cosmos. 

### <a name="do-i-need-to-update-the-network-security-groups-nsg-rules"></a>È necessario aggiornare le regole dei gruppi di sicurezza di rete (NSG)? 
Le regole NSG vengono utilizzate per limitare la connettività da e verso una subnet con la rete virtuale. Quando si aggiunge un endpoint del servizio per Azure Cosmos DB alla subnet, non è necessario aprire la connettività in uscita del gruppo di sicurezza di rete per l'account Azure Cosmos. 

### <a name="are-service-endpoints-available-for-all-vnets"></a>Gli endpoint di servizio sono disponibili per tutte le reti virtuali?
No, solo le reti virtuali di Azure Resource Manager hanno un endpoint di servizio abilitato. Le reti virtuali classiche non supportano gli endpoint di servizio.

### <a name="can-i-accept-connections-from-within-public-azure-datacenters-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>È possibile accettare le connessioni nei datacenter di Azure pubblici quando viene abilitato l'accesso all'endpoint del servizio per Azure Cosmos DB?  
Questa operazione è necessaria solo quando si vuole consentire l'accesso all'account Azure Cosmos DB da parte di altri servizi proprietari Azure come Azure Data Factory, Ricerca cognitiva di Azure o qualsiasi servizio distribuito in una specifica area di Azure.

## <a name="next-steps"></a>Passaggi successivi

* Per configurare un firewall per Azure Cosmos DB, vedere l'articolo [Supporto del firewall](how-to-configure-firewall.md).
